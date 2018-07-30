---
title: 使用 Linux VM 使用者指派的受控服務識別來存取 Azure Resource Manager
description: 此教學課程會引導您使用 Linux VM 上使用者指派的受控服務識別來存取 Azure Resource Manager 的程序。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c2735d385b0a3c2201ec2dad83c0c32fe44d458c
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258238"
---
# <a name="tutorial-use-a-user-assigned-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>教學課程：使用 Linux 虛擬機器上使用者指派的身分識別，來存取 Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

本教學課程說明如何建立使用者指派的身分識別，並將其指派給 Linux 虛擬機器 (VM)，然後使用該身分識別存取 Azure Resource Manager API。 由 Azure 自動管理受控服務身分識別。 它們會啟用對支援 Azure AD 驗證之服務的驗證，而不需要在程式碼中內嵌認證。 

由 Azure 自動管理受控服務身分識別。 它們會啟用對支援 Azure AD 驗證之服務的驗證，而不需要在程式碼中內嵌認證。

您會了解如何：

> [!div class="checklist"]
> * 建立使用者指派的身分識別
> * 將使用者指派的身分識別指派至 Linux 虛擬機器 
> * 在 Azure Resource Manager 中將使用者指派身分識別的存取權授與資源群組 
> * 使用使用者指派的身分識別來取得存取權杖，並用其呼叫 Azure Resource Manager 

## <a name="prerequisites"></a>必要條件

- 如果您不熟悉受控服務識別，請參閱[概觀](overview.md)一節。 **請務必檢閱[系統和使用者指派之身分識別其間的差異](overview.md#how-does-it-work)**。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。
- 若要執行本教學課程中所需的資源建立和角色管理步驟，您的帳戶必須在適當的範圍 (您的訂用帳戶或資源群組) 上具備「擁有者」權限。 如果您需要角色指派的協助，請參閱[使用角色型存取控制來管理 Azure 訂用帳戶資源的存取權](/azure/role-based-access-control/role-assignments-portal)。

如果您選擇在本機安裝和使用 CLI，本快速入門會要求您執行 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="sign-in-to-azure"></a>登入 Azure

在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>在新的資源群組中建立 Linux 虛擬機器

在本教學課程中，您會先建立新的 Linux VM。 您也可以選擇使用現有 VM。

1. 按一下 Azure 入口網站左上角的 [建立資源]。
2. 選取 [計算]，然後選取 [Ubuntu Server 16.04 LTS]。
3. 輸入虛擬機器資訊。 針對 [驗證類型] 選取 [SSH 公開金鑰] 或 [密碼]。 建立的認證可讓您登入 VM。

    ![建立 Linux VM](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. 在下拉式清單中選擇適用於虛擬機器的**訂用帳戶**。
5. 若要選取要在其中建立虛擬機器的新 [資源群組]，請選擇 [新建]。 完成時，按一下 [確定]。
6. 選取 VM 的大小。 若要查看更多大小，請選取 [檢視全部] 或變更支援的磁碟類型篩選條件。 在 [設定] 刀鋒視窗上，保留預設值並按一下 [確定]。

## <a name="create-a-user-assigned-identity"></a>建立使用者指派的身分識別

1. 如果使用的是 CLI 主控台 (而不是 Azure Cloud Shell 工作階段)，則請從登入 Azure 開始。 請使用您想用來建立新的使用者指派身分識別，且已與 Azure 訂用帳戶建立關聯的帳戶：

    ```azurecli
    az login
    ```

2. 使用 [az identity create](/cli/azure/identity#az_identity_create)，建立使用者指派的身分識別。 `-g` 參數會指定要在其中建立受控服務識別的資源群組，而 `-n` 參數會指定其名稱。 請務必以您自己的值取代 `<RESOURCE GROUP>` 和 `<MSI NAME>` 參數的值：
    
[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <MSI NAME>
```

回應會包含所建立使用者指派身分識別的詳細資料，與下列範例類似。 請記下使用者指派身分識別的 `id` 值，因為後續步驟中會使用該值：

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

## <a name="assign-a-user-assigned-identity-to-your-linux-vm"></a>將使用者指派的身分識別指派給 Linux 虛擬機器

使用者指派的身分識別可以由多個 Azure 資源上的用戶端使用。 使用下列命令以將使用者指派的身分識別指派至單一虛擬機器。 針對 `-IdentityID` 參數，請使用前一個步驟中所傳回的 `Id` 屬性。

請使用 [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity)，將使用者指派的受控服務識別指派給 Linux VM。 請務必以您自己的值取代 `<RESOURCE GROUP>` 和 `<VM NAME>` 參數的值。 至於 `--identities` 參數值，請使用前一個步驟中所傳回的 `id` 屬性。

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="grant-your-user-assigned-identity-access-to-a-resource-group-in-azure-resource-manager"></a>在 Azure Resource Manager 中將使用者指派身分識別的存取權授與資源群組 

受控服務識別 (MSI) 提供的身分識別可讓您的程式碼用於要求存取權杖，以針對支援 Azure AD 驗證的資源 API 進行驗證。 在本教學課程中，您的程式碼將存取 Azure Resource Manager API。  

您必須先將身分識別存取權授與 Azure Resource Manager 中的資源，您的程式碼才能存取 API。 在此情況下，是包含 VM 的資源群組。 將 `<SUBSCRIPTION ID>` 和 `<RESOURCE GROUP>` 的值更新為適用於環境的值。 此外，將 `<MSI PRINCIPALID>` 更改為[建立使用者指派的 MSI](#create-a-user-assigned-msi) 時，`az identity create` 命令所傳回的 `principalId` 屬性：

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

回應會包含已建立的角色指派詳細資料，與下列範例類似：

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

若要完成這些步驟，您需要 SSH 用戶端。 如果您使用 Windows，您可以在[適用於 Linux 的 Windows 子系統](https://msdn.microsoft.com/commandline/wsl/about)中使用 SSH 用戶端。 

1. 登入 Azure [入口網站](https://portal.azure.com)。
2. 在入口網站中，瀏覽至 [虛擬機器]，並移至您的 Linux 虛擬機器，在 [概觀] 中按一下 [連線]。 複製字串以連線到您的 VM。
3. 使用您所選擇的 SSH 用戶端來連線到虛擬機器。 如果您使用 Windows，您可以在[適用於 Linux 的 Windows 子系統](https://msdn.microsoft.com/commandline/wsl/about)中使用 SSH 用戶端。 如果您需要設定 SSH 用戶端金鑰的協助，請參閱[如何在 Azure 上搭配 Windows 使用 SSH 金鑰](~/articles/virtual-machines/linux/ssh-from-windows.md)，或[如何在 Azure 中建立和使用 Linux VM 的 SSH 公開和私密金鑰組](~/articles/virtual-machines/linux/mac-create-ssh-keys.md)。
4. 在終端機視窗中使用 CURL，向 Azure Instance Metadata Service (IMDS) 身分識別端點提出要求來取得 Azure Resource Manager 的存取權杖。  

   用來取得存取權杖的 CURL 要求，則如以下範例所示。 請務必將 `<CLIENT ID>` 取代為[建立使用者指派的身分識別](#create-a-user-assigned-msi)時，`az identity create` 命令所傳回的 `clientId` 屬性： 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > `resource` 參數的值必須完全符合 Azure AD 的預期。 使用 Resource Manager 資源識別碼時，必須在 URI 上包含結尾斜線。 
    
    此回應包含您存取 Azure Resource Manager 所需的存取權杖。 
    
    回應範例：  

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

5. 使用存取權杖來存取 Azure Resource Manager，以及讀取您先前授與使用者指派身分識別存取權的資源群組屬性。 請務必將 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>` 取代為您先前指定的值，並將 `<ACCESS TOKEN>` 取代為上一個步驟中所傳回的權杖。

    > [!NOTE]
    > URL 區分大小寫，因此請務必使用稍早在命名資源群組時所使用的相同大小寫，而且 `resourceGroups` 中的 "G" 為大寫。  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    回應包含特定資源群組資訊，與下列範例類似： 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>後續步驟

在本教學課程中，您已學習如何建立使用者指派的身分識別，並將其連結至 Linux 虛擬機器以存取 Azure Resource Manager API。  若要深入了解 Azure Resource Manager，請參閱：

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)

