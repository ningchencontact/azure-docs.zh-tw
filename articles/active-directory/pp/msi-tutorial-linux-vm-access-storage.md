---
title: 使用 Linux VM 上使用者指派的 MSI 存取 Azure 儲存體
description: 本教學課程引導您使用 Linux VM 上使用者所指派的受控服務識別 (MSI) 來存取 Azure 儲存體的程序。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: arluca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5ae0e4e8149772d79190ee196cdd1c1bef344681
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/16/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>使用 Linux VM 上使用者指派的受控服務識別 (MSI) 來存取 Azure 儲存體

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

本教學課程會示範如何以 Linux 虛擬機器建立及使用使用者指派的受控服務識別 (MSI)，然後使用該識別來存取 Azure 儲存體。 您會了解如何：

> [!div class="checklist"]
> * 建立使用者指派的受控服務識別 (MSI)
> * 將使用者指派的 MSI 指派至 Linux 虛擬機器
> * 將 MSI 存取權授與 Azure 儲存體執行個體
> * 使用使用者指派的 MSI 識別取得存取權杖，並使用它存取 Azure 儲存體

## <a name="prerequisites"></a>先決條件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

若要執行本教學課程中的 CLI 指令碼範例，您有兩個選項：

- 透過 Azure 入口網站或是每個程式碼區塊右上角的 [試試看] 按鈕，使用 [Azure Cloud Shell](~/articles/cloud-shell/overview.md)。
- 如果您需要使用本機 CLI 主控台，請[安裝最新版的 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 或更新版本)。

## <a name="sign-in-to-azure"></a>登入 Azure

在 [https://portal.azure.com](https://portal.azure.com) 登入 Azure 入口網站。

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>在新的資源群組中建立 Linux 虛擬機器

首先，請建立新的 Linux VM。 也可以在現有的 VM 中啟用 MSI。

1. 按一下 Azure 入口網站左上角的 [+/建立新服務] 按鈕。
2. 選取 [計算]，然後選取 [Ubuntu Server 16.04 LTS]。
3. 輸入虛擬機器資訊。 針對 [驗證類型] 選取 [SSH 公開金鑰] 或 [密碼]。 建立的認證可讓您登入 VM。

    ![替代映像文字](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. 在下拉式清單中選擇適用於虛擬機器的**訂用帳戶**。
5. 若要選取要在其中建立虛擬機器的新 [資源群組]，請選擇 [新建]。 完成時，按一下 [確定]。
6. 選取 VM 的大小。 若要查看更多大小，請選取 [檢視全部] 或變更支援的磁碟類型篩選條件。 在 [設定] 刀鋒視窗上，保留預設值並按一下 [確定]。

## <a name="create-a-user-assigned-msi"></a>建立使用者指派的 MSI

1. 如果使用的是 CLI 主控台 (而不是 Azure Cloud Shell 工作階段)，則請從登入 Azure 開始。 請使用您想用來建立新 MSI，且已與 Azure 訂用帳戶建立關聯的帳戶：

    ```azurecli
    az login
    ```

2. 使用 [az identity create](/cli/azure/identity#az_identity_create)，建立使用者指派的 MSI。 `-g` 參數會指定要建立 MSI 的資源群組，而 `-n` 參數則指定其名稱。 請務必以您自己的值取代 `<RESOURCE GROUP>` 和 `<MSI NAME>` 參數的值：

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    回應會包含所建立之使用者指派 MSI 的詳細資料，與下列範例類似。 請記下 MSI 的 `id` 值，因為後續步驟中會使用該值：

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
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

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>將使用者指派的 MSI 指派給 Linux VM

與系統指派的 MSI 不同，使用者指派的 MSI 可以由多個 Azure 資源上的用戶端使用。 在本教學課程中，您可以將它指派給單一 VM。 也可以將它指派給多個 VM。

使用 [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity)，將使用者指派的 MSI 指派給 Linux VM。 請務必以您自己的值取代 `<RESOURCE GROUP>` 和 `<VM NAME>` 參數的值。 至於 `--identities` 參數值，請使用前一個步驟中所傳回的 `id` 屬性：

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-storage-account"></a>建立儲存體帳戶 

如果您還沒有帳戶，請立即建立一個儲存體帳戶。 也可以跳過此步驟，直接使用現有的儲存體帳戶。 

1. 按一下 Azure 入口網站左上角的 [+/建立新服務] 按鈕。
2. 按一下 [儲存體]，然後按一下 [儲存體帳戶]，就會顯示新的 [建立儲存體帳戶] 面板。
3. 輸入儲存體帳戶的**名稱**，您稍後將會使用它。  
4. [部署模型] 和 [帳戶類型] 應該分別設定為「資源管理員」和「一般用途」。 
5. 確定 [訂用帳戶] 和 [資源群組] 符合您在上一個步驟中建立 VM 時指定的值。
6. 按一下頁面底部的 [新增] 。

    ![建立新的儲存體帳戶](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>在儲存體帳戶中建立 Blob 容器

由於檔案需要 Blob 儲存體，您需要建立 Blob 容器，用來儲存檔案。 然後，可以在新的儲存體帳戶中，上傳和下載檔案到 Blob 容器。

1. 巡覽回到您新建立的儲存體帳戶。
2. 按一下左側 [Blob 服務] 下的 [容器] 連結。
3. 按一下頁面上方的 [+ 容器]，[新的容器] 面板隨即會滑出。
4. 指定容器的名稱，選取存取層級，然後按一下 [確定]。 稍後在教學課程中也會用到您指定的名稱。 

    ![建立儲存體容器](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. 按一下容器名稱，按一下 [上傳]，然後選取檔案，再按一下 [上傳]，即可將檔案上傳至新建立的容器。

    ![上傳文字檔](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>將使用者指派的 MSI 存取權授與 Azure 儲存體容器

您的程式碼可以使用 MSI 來取得存取權杖，來向支援 Azure AD 驗證的資源進行驗證。 在本教學課程中，會使用 Azure 儲存體。

首先，將 MSI 識別存取權授與 Azure 儲存體容器。 在此情況下，會使用稍早建立的容器。 將 `<SUBSCRIPTION ID>`、`<RESOURCE GROUP>`、`<STORAGE ACCOUNT NAME>` 和 `<CONTAINER NAME>` 的值更新為適用於環境的值。 此外，將 `<MSI PRINCIPALID>` 更改為[建立使用者指派的 MSI](#create-a-user-assigned-msi) 時，`az identity create` 命令所傳回的 `principalId` 屬性：

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/containers/<CONTAINER NAME>"
```

回應會包含已建立的角色指派之詳細資料：

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.storage/storageAccounts/<STORAGE ACCOUNT NAME>/blogServices/default/<CONTAINER NAME>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>使用使用者指派的 MSI 識別取得存取權杖，並使用它來呼叫 Azure 儲存體

其餘教學課程要從稍早建立的 VM 繼續進行。

若要完成這些步驟，您需要 SSH 用戶端。 如果您使用 Windows，您可以在[適用於 Linux 的 Windows 子系統](https://msdn.microsoft.com/commandline/wsl/about)中使用 SSH 用戶端。 如果您需要設定 SSH 用戶端金鑰的協助，請參閱[如何在 Azure 上搭配 Windows 使用 SSH 金鑰](~/articles/virtual-machines/linux/ssh-from-windows.md)，或[如何在 Azure 中建立和使用 Linux VM 的 SSH 公開和私密金鑰組](~/articles/virtual-machines/linux/mac-create-ssh-keys.md)。

1. 在 Azure 入口網站中，瀏覽至 [虛擬機器]，移至您的 Linux 虛擬機器，然後在 [概觀] 頁面中，按一下頂端的 [連線]。 複製字串以連線到您的 VM。
2. 使用您所選擇的 SSH 用戶端來**連線**到 VM。 
3. 在終端機視窗中，使用 CURL 向本機 MSI 端點提出要求，來取得 Azure 儲存體的存取權杖。

   用來取得存取權杖的 CURL 要求，則如以下範例所示。 請務必將 `<CLIENT ID>` 更改為[建立使用者指派的 MSI](#create-a-user-assigned-msi)時，`az identity create` 命令所傳回的 `clientId` 屬性：
   
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com/&client_id=<MSI CLIENT ID>" 
   ```

   > [!NOTE]
   > 前面的要求中，`resource` 參數的值必須完全符合 Azure AD 的預期。 當使用 Azure 儲存體資源 ID 時，必須在 URI 中包含結尾的斜線。
   > 在下列回應中，為求簡單明瞭，已縮短 access_token 元素。

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
   "refresh_token":"",
   "expires_in":"3599",
   "expires_on":"1504130527",
   "not_before":"1504126627",
   "resource":"https://storage.azure.com",
   "token_type":"Bearer"}
   ```

4. 現在請使用存取權杖來存取 Azure 儲存體，例如可以讀取先前上傳至容器的範例檔案內容。 將 `<STORAGE ACCOUNT>`、`<CONTAINER NAME>`、`<FILE NAME>` 更改為您先前指定的值，並將 `<ACCESS TOKEN>` 更改為上一個步驟中所傳回的權杖。

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME>?api-version=2017-11-09 -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   回應會包含檔案的內容：

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>後續步驟

- 如需 MSI 的概觀，請參閱[受控服務識別概觀](msi-overview.md)。
- 若要了解如何使用儲存體 SAS 認證進行此相同教學課程，請參閱[使用 Linux VM 受控服務識別，透過 SAS 認證存取 Azure 儲存體](msi-tutorial-linux-vm-access-storage-sas.md)
- 如需有關 Azure 儲存體帳戶 SAS 功能的詳細資訊，請參閱：
  - [使用共用存取簽章 (SAS)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [建構服務 SAS](/rest/api/storageservices/Constructing-a-Service-SAS.md)

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。





