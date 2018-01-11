---
title: "若要存取 Azure 資源管理員使用 Linux VM 指派給使用者的 MSI"
description: "此教學課程引導您在 Linux VM 上使用 User-Assigned 管理服務身分識別 (MSI)，來存取 Azure 資源管理員的程序。"
services: active-directory
documentationcenter: 
author: bryanLa
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: arluca
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a51d0bdf092893288f2e1cc31a4dcc4117b041c2
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-resource-manager"></a>在 Linux VM 上使用使用者指派受管理服務身分識別 (MSI)，來存取 Azure 資源管理員

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

本教學課程說明如何建立使用者指派受管理服務身分識別 (MSI)，將它指派至 Linux 虛擬機器 (VM)，然後使用該識別來存取 Azure 資源管理員 API。 

由 Azure 自動管理受管理的服務身分識別。 它們可讓支援 Azure AD 驗證，而不需要您的程式碼中嵌入認證的服務驗證。

您會了解如何：

> [!div class="checklist"]
> * 建立指派給使用者的 MSI
> * 將 MSI 指派給 Linux VM 
> * MSI 存取授與資源群組中 「 Azure 資源管理員 
> * 取得存取權杖使用 MSI，以及使用它來呼叫 Azure 資源管理員 

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

若要執行本教學課程的 CLI 指令碼範例，您有兩個選項：

- 使用[Azure 雲端殼層](~/articles/cloud-shell/overview.md)從 Azure 入口網站，或是透過 「 試試看 」 按鈕位於右上角的每個程式碼區塊。
- [安裝最新版的 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 或更新版本) 如果您想要使用本機的 CLI 主控台。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站，位址是 [https://portal.azure.com](https://portal.azure.com)。

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>在新的資源群組中建立 Linux 虛擬機器

此教學課程中，您可以先建立新的 Linux VM。 您也可以選擇使用現有的 VM。

1. 按一下 Azure 入口網站左上角的 [新增] 按鈕。
2. 選取 [計算]，然後選取 [Ubuntu Server 16.04 LTS]。
3. 輸入虛擬機器資訊。 針對 [驗證類型] 選取 [SSH 公開金鑰] 或 [密碼]。 建立的認證可讓您登入 VM。

    ![建立 Linux VM](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. 在下拉式清單中選擇適用於虛擬機器的**訂用帳戶**。
5. 若要選取要在其中建立虛擬機器的新 [資源群組]，請選擇 [新建]。 完成時，按一下 [確定]。
6. 選取 VM 的大小。 若要查看更多大小，請選取 [檢視全部] 或變更支援的磁碟類型篩選條件。 在 [設定] 刀鋒視窗上，保留預設值並按一下 [確定]。

## <a name="create-a-user-assigned-msi"></a>建立指派給使用者的 MSI

1. 如果您使用 CLI 主控台 （而不是 Azure 雲端殼層工作階段中），啟動登入 Azure。 使用與您想要建立新的 MSI 所在的 Azure 訂用帳戶相關聯的帳戶：

    ```azurecli
    az login
    ```

2. 建立 指派使用者給 MSI 使用[az 身分識別建立](/cli/azure/identity#az_identity_create)。 `-g`參數指定 MSI 建立所在的資源群組和`-n`參數會指定其名稱。 請務必取代`<RESOURCE GROUP>`和`<MSI NAME>`參數值與您自己的值：

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    回應會包含詳細資料建立，類似於下列範例將使用者指派 msi。 請注意`id`程式 MSI 的值，因為它會使用下一個步驟中：

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "9012",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>將您指派給使用者的 MSI 指派給您的 Linux VM

系統指派的 MSI，與指派給使用者的 MSI 可供多個 Azure 資源上的用戶端。 此教學課程中，將它指派給單一 VM。 您也可以將它指派給多部 VM。

指派給使用 Linux VM 指派給使用者的 MSI [az vm 指派識別](/cli/azure/vm#az_vm_assign_identity)。 請務必取代`<RESOURCE GROUP>`和`<VM NAME>`參數值與您自己的值。 使用`id`前一個步驟中傳回屬性`--identities`參數值：

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> -–identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>您指派給使用者的 MSI 存取權授與資源群組中 「 Azure 資源管理員 

MSI 提供您的程式碼，以向資源 Api，可支援 Azure AD 驗證存取權杖。 在本教學課程中，您的程式碼存取 Azure 資源管理員 API。 

您的程式碼可以透過存取 API 之前，您需要授與 MSI 的身分識別存取 Azure 資源管理員中的資源。 在此情況下，其中包含 VM 資源群組。 別忘了以您自己的值取代 `<CLIENT ID>`、`<SUBSCRIPTION ID>` 和 `<RESOURCE GROUP>` 參數的值。 取代`<CLIENT ID>`與`clientId`屬性所傳回`az identity create`命令[建立指派給使用者的 MSI](#create-a-user-assigned-msi): 

```azurecli-interactive
az role assignment create --assignee <CLIENT ID> --role ‘Reader’ --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

回應包含建立，類似於下列範例中的角色指派詳細資料：

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>使用 VM 身分識別取得存取權杖，並使用它來呼叫 Resource Manager 

其餘課程要從稍早建立的 VM 繼續進行。

若要完成這些步驟，您需要 SSH 用戶端。 如果您使用 Windows，您可以在[適用於 Linux 的 Windows 子系統](https://msdn.microsoft.com/commandline/wsl/about)中使用 SSH 用戶端。 如果您需要設定 SSH 用戶端金鑰的協助，請參閱[如何在 Azure 上搭配 Windows 使用 SSH 金鑰](~/articles/virtual-machines/linux/ssh-from-windows.md)，或[如何在 Azure 中建立和使用 Linux VM 的 SSH 公開和私密金鑰組](~/articles/virtual-machines/linux/mac-create-ssh-keys.md)。

1. 在 Azure 入口網站中，瀏覽至 [虛擬機器]，移至您的 Linux 虛擬機器，然後在 [概觀] 頁面中，按一下頂端的 [連線]。 複製字串以連線到您的 VM。
2. 使用您所選擇的 SSH 用戶端來**連線**到 VM。  
3. 在終端機視窗中使用 CURL，向本機 MSI 端點提出要求來取得 Azure Resource Manager 的存取權杖。  

   CURL 要求取得存取權杖是以下列範例所示。 請務必取代`<CLIENT ID>`與`clientId`屬性所傳回`az identity create`命令[建立指派給使用者的 MSI](#create-a-user-assigned-msi): 
    
   ```bash
   curl -H Metadata:true "http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<CLIENT ID>"   
   ```
    
    > [!NOTE]
    > 值`resource`參數必須是完全符合預期的 Azure ad。 時使用的資源管理員資源識別碼，您必須包含結尾斜線的 uri。 
    
    此回應包含您存取 Azure Resource Manager 所需的存取權杖。 
    
    回應的範例：  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

4. 現在使用存取權杖來存取 Azure 資源管理員，並讀取要您先前授與您指派給使用者的 MSI 存取的資源群組的屬性。 請務必取代`<SUBSCRIPTION ID>`，`<RESOURCE GROUP>`您稍早指定的值和`<ACCESS TOKEN>`與上一個步驟中傳回的權杖。

    > [!NOTE]
    > URL 會區分大小寫，因此請務必使用相同大小寫完全相符時所使用稍早所命名的資源群組和大寫"G"中的`resourceGroups`。  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    回應包含特定的資源群組資訊，類似於下例： 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>後續步驟

- 如需 MSI 的概觀，請參閱[受控服務識別概觀](msi-overview.md)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。