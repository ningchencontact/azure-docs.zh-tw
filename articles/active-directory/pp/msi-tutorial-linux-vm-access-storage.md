---
title: "用於指派 MSI Linux VM 上的使用者存取 Azure 儲存體"
description: "此教學課程引導您使用 Linux VM 上的使用者指派受管理服務身分識別 (MSI) 來存取 Azure 儲存體的程序。"
services: active-directory
documentationcenter: 
author: bryanLa
manager: mtillman
editor: arluca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5c02eb32fd12e28d79dcc0340811f4ced48ed4be
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>使用 Linux VM 上的使用者指派受管理服務身分識別 (MSI) 來存取 Azure 儲存體

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

本教學課程會示範如何建立及使用使用者指派受管理服務身分識別 (MSI) 從 Linux 虛擬機器，然後使用它來存取 Azure 儲存體。 您會了解如何：

> [!div class="checklist"]
> * 建立使用者指派受管理的服務身分識別 (MSI)
> * 將使用者指派 MSI 指派至 Linux 虛擬機器
> * 授與 Azure 儲存體執行個體的 MSI 存取
> * 取得存取權杖使用使用者指派 MSI 身分識別，並使用它來存取 Azure 儲存體

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

若要執行本教學課程的 CLI 指令碼範例，您有兩個選項：

- 使用[Azure 雲端殼層](~/articles/cloud-shell/overview.md)從 Azure 入口網站，或是透過 「 試試看 」 按鈕位於右上角的每個程式碼區塊。
- [安裝最新版的 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 或更新版本) 如果您想要使用本機的 CLI 主控台。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站，位址是 [https://portal.azure.com](https://portal.azure.com)。

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>在新的資源群組中建立 Linux 虛擬機器

首先，您會建立新的 Linux VM。 如果您想要的話，您也可以啟用現有的 VM 上的 MSI。

1. 按一下 Azure 入口網站左上角的 [+/建立新服務] 按鈕。
2. 選取 [計算]，然後選取 [Ubuntu Server 16.04 LTS]。
3. 輸入虛擬機器資訊。 針對 [驗證類型] 選取 [SSH 公開金鑰] 或 [密碼]。 建立的認證可讓您登入 VM。

    ![替代映像文字](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

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

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>將您指派給使用者的 MSI 指派給您的 Linux VM

系統指派的 MSI，與指派給使用者的 MSI 可供多個 Azure 資源上的用戶端。 此教學課程中，將它指派給單一 VM。 您也可以將它指派給多部 VM。

指派給使用 Linux VM 指派給使用者的 MSI [az vm 指派識別](/cli/azure/vm#az_vm_assign_identity)。 請務必取代`<RESOURCE GROUP>`和`<VM NAME>`參數值與您自己的值。 使用`id`前一個步驟中傳回屬性`--identities`參數值：

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> -–identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-storage-account"></a>建立儲存體帳戶 

如果您還沒有其中一個，立即建立儲存體帳戶。 您也可以略過此步驟，並使用現有的儲存體帳戶，如果您偏好。 

1. 按一下 Azure 入口網站左上角的 [+/建立新服務] 按鈕。
2. 按一下**儲存體**，然後**儲存體帳戶**，並顯示新的 [建立儲存體帳戶] 面板。
3. 輸入**名稱**儲存體帳戶，稍後使用。  
4. [部署模型] 和 [帳戶類型] 應該分別設定為「資源管理員」和「一般用途」。 
5. 確定 [訂用帳戶] 和 [資源群組] 符合您在上一個步驟中建立 VM 時指定的值。
6. 按一下頁面底部的 [新增] 。

    ![建立新的儲存體帳戶](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>在儲存體帳戶中建立 Blob 容器

因為檔案需要 blob 儲存體，您需要建立 blob 容器，用來儲存檔案。 然後您上傳，並將檔案下載至 blob 容器，新的儲存體帳戶中。

1. 巡覽回到您新建立的儲存體帳戶。
2. 按一下左側 [Blob 服務] 下的 [容器] 連結。
3. 按一下頁面上方的 [+ 容器]，[新的容器] 面板隨即會滑出。
4. 指定容器的名稱，選取存取層級，然後按一下 [確定]。 稍後在本教學課程中，也會使用您指定的名稱。 

    ![建立儲存體容器](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. 將檔案上傳新建立的容器上的 容器名稱，然後**上傳**，然後選取檔案，然後按一下**上傳**。

    ![上傳文字檔](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>授與您的使用者指派 MSI 存取 Azure 儲存體容器

藉由使用 MSI，您的程式碼可以取得存取權杖來向支援 Azure AD 驗證的資源。 在本教學課程中，您可以使用 Azure 儲存體。

第一次您授與 Azure 儲存體容器的 MSI 身分識別存取。 在此情況下，您會使用稍早建立的容器。 更新的值`<MSI CLIENTID>`， `<SUBSCRIPTION ID>`， `<RESOURCE GROUP>`， `<STORAGE ACCOUNT NAME>`，和`<CONTAINER NAME>`為適合您的環境。 取代`<CLIENT ID>`與`clientId`屬性所傳回`az identity create`命令[建立指派給使用者的 MSI](#create-a-user-assigned-msi):

```azurecli-interactive
az role assignment create --assignee <MSI CLIENTID> --role ‘Reader’ --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/<CONTAINER NAME>"
```

回應包含建立角色指派的詳細資料：

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

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>取得存取權杖使用指派給使用者的 MSI 身分識別，以及使用它來呼叫 Azure 儲存體

教學課程的其餘部分，您需要從您稍早建立的 VM。

若要完成這些步驟，您需要 SSH 用戶端。 如果您使用 Windows，您可以在[適用於 Linux 的 Windows 子系統](https://msdn.microsoft.com/commandline/wsl/about)中使用 SSH 用戶端。 如果您需要設定 SSH 用戶端金鑰的協助，請參閱[如何在 Azure 上搭配 Windows 使用 SSH 金鑰](~/articles/virtual-machines/linux/ssh-from-windows.md)，或[如何在 Azure 中建立和使用 Linux VM 的 SSH 公開和私密金鑰組](~/articles/virtual-machines/linux/mac-create-ssh-keys.md)。

1. 在 Azure 入口網站中，瀏覽至 [虛擬機器]，移至您的 Linux 虛擬機器，然後在 [概觀] 頁面中，按一下頂端的 [連線]。 複製字串以連線到您的 VM。
2. 使用您所選擇的 SSH 用戶端來**連線**到 VM。 
3. 在終端機視窗中，使用 CURL，提出要求至本機的 MSI 端點，以便取得存取權杖的 Azure 儲存體。

   CURL 要求取得存取權杖是以下列範例所示。 請務必取代`<CLIENT ID>`與`clientId`屬性所傳回`az identity create`命令[建立指派給使用者的 MSI](#create-a-user-assigned-msi):

   ```bash
   curl -H Metadata:true "http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fstorage.azure.com/&client_id=<CLIENT ID>"
   ```

   > [!NOTE]
   > 一個要求的值`resource`參數必須是完全符合預期的 Azure ad。 當使用 Azure 儲存體資源識別碼，必須包含結尾斜線的 uri。
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

4. 現在使用存取權杖來存取 Azure 儲存體，例如若要讀取先前上傳至容器的範例檔案的內容。 值取代`<STORAGE ACCOUNT>`， `<CONTAINER NAME>`，和`<FILE NAME>`您稍早指定的值和`<ACCESS TOKEN>`與上一個步驟中傳回的權杖。

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   回應包含檔案的內容：

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





