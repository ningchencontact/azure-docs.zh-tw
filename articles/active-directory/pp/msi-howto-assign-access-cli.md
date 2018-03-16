---
title: "如何使用 Azure CLI 將使用者指派的 MSI 存取權指派給 Azure 資源"
description: "使用 Azure CLI 在一個資源上指派使用者指派的 MSI，並存取另一個資源的逐步解說指示。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 8cdfd80f436a9ef39426da9d3f2f76ae59677847
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="assign-a-user-assigned-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>使用 Azure CLI 將使用者指派的受控服務識別 (MSI) 存取權指派給資源

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

一旦您已建立使用者指派的 MSI 後，就可以將 MSI 存取權提供給另一個資源，就如同任何安全性主體。 此範例將示範如何使用 Azure CLI 將使用者指派的 MSI 存取權提供給 Azure 儲存體帳戶。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

若要執行本教學課程中的 CLI 指令碼範例，您有兩個選項：

- 透過 Azure 入口網站或是每個程式碼區塊右上角的 [試試看] 按鈕，使用 [Azure Cloud Shell](~/articles/cloud-shell/overview.md)。
- 如果您需要使用本機的 CLI 主控台，請[安裝最新版的 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 或更新版本)。 然後使用 [az login](/cli/azure/reference-index#az_login) 登入 Azure。 使用與 Azure 訂用帳戶相關聯的帳戶，而您要以此帳戶部署使用者指派的 MSI 和虛擬機器：

   ```azurecli
   az login
   ```

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>使用 RBAC 將 MSI 存取權指派給另一個資源

若要使用 MSI 搭配主機資源 (例如 VM) 來登入或存取資源，MSI 必須先透過角色指派授與資源存取權。 您可以在 MSI 與主機建立關聯之前或之後，執行這項操作。 如需建立並與 VM 建立關聯的完整步驟，請參閱[使用 Azure CLI 為 VM 設定使用者指派的 MSI](msi-qs-configure-cli-windows-vm.md)。

在下列範例中，系統會向使用者指派的 MSI 提供儲存體帳戶的存取權。  

1. 為了提供存取權給 MSI，您需要 MSI 服務主體的用戶端識別碼 (也稱為應用程式識別碼)。 用戶端識別碼是在佈建 MSI 和其服務主體 (如下所示以供參考) 時，由 [az identity create](/cli/azure/identity#az_identity_create) 提供：

   ```azurecli-interactive
   az identity create -g rgPrivate -n msiServiceApp
   ```

   成功回應會在 `clientId` 屬性中包含使用者指派的 MSI 之服務主體的用戶端識別碼：

   ```json
   {
        "clientId": "9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp/credentials?tid=733a8f0p-ec41-4e69-8adz-971fc4b533bl&oid=z4baa4fc-fce4-4202-8250-5fb359abblll&aid=9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp",
        "location": "westcentralus",
        "name": "msiServiceApp",
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "resourceGroup": "rgPrivate",
        "tags": {},
        "tenantId": "733a8f0p-ec41-4e69-8adz-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
   }
   ```

2. 得知用戶端識別碼後，請使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 將 MSI 存取權指派給另一個資源。 務必使用您的用戶端識別碼作為 `--assignee` 參數，以及當您執行 `az identity create` 時傳回的相符訂用帳戶識別碼和資源群組名稱。 系統在這裡會將名為 "myStorageAcct" 的儲存體帳戶「讀取」存取權指派給 MSI：

   ```azurecli-interactive
   az role assignment create --assignee 9391e5b1-dada-4a8z-834a-43ad44f296bl --role Reader --scope /subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

   成功回應大致如下列輸出所示：

   ```json
   {
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct/providers/Microsoft.Authorization/roleAssignments/f4766864-9493-43c6-91d7-abd131c3c017",
        "name": "f4766864-9493-43c6-91d7-abd131c3c017",
        "properties": {
            "additionalProperties": {
            "createdBy": null,
            "createdOn": "2017-12-21T20:49:37.5590544Z",
            "updatedBy": "pd78b21f-17a4-41az-b7db-9aadec3376bl",
            "updatedOn": "2017-12-21T20:49:37.5590544Z"
            },
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "roleDefinitionId": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "scope": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct"
        },
        "resourceGroup": "rgPrivate",
        "type": "Microsoft.Authorization/roleAssignments"
   }
   ```

## <a name="next-steps"></a>後續步驟

- 如需 MSI 的概觀，請參閱[受控服務識別概觀](msi-overview.md)。
- 若要在 Azure VM 上啟用使用者指派的 MSI，請參閱[使用 Azure CLI 為 VM 設定使用者指派的受控服務識別 (MSI)](msi-qs-configure-cli-windows-vm.md)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。

