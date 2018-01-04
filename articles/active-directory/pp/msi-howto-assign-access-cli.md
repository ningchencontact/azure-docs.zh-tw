---
title: "如何將使用者指派的 MSI 存取指派給 Azure 資源，使用 Azure CLI"
description: "逐步解說指示指派一個資源，在使用者指派 MSI 會存取另一個資源，使用 Azure CLI。"
services: active-directory
documentationcenter: 
author: bryanLa
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 732fc1981bdf95e7548ea0ebe0ca8ece00f483ce
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
# <a name="assign-a-user-assigned-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>將使用者指派的管理服務身分識別 (MSI) 存取權指派給資源，使用 Azure CLI

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

一旦您已建立指派給使用者的 MSI，您可以提供 MSI 存取另一個資源，就像任何安全性主體。 這個範例會示範如何讓使用者指定 MSI 存取 Azure 儲存體帳戶，使用 Azure CLI。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

若要執行本教學課程的 CLI 指令碼範例，您有兩個選項：

- 使用[Azure 雲端殼層](~/articles/cloud-shell/overview.md)從 Azure 入口網站，或是透過 「 試試看 」 按鈕位於右上角的每個程式碼區塊。
- [安裝最新版的 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 或更新版本) 如果您想要使用本機的 CLI 主控台。 然後 Azure 中，使用登入[az 登入](/cli/azure/#login)。 使用與您想要將使用者指派 MSI 和 VM 部署所在的 Azure 訂用帳戶相關聯的帳戶：

   ```azurecli
   az login
   ```

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>使用 RBAC 將 MSI 存取權指派給另一個資源

若要登入或資源的存取與主機資源 （例如 VM) 使用 MSI，MSI 必須先授與透過角色指派的資源存取。 才能將 MSI 關聯的主機，或之後，您可以執行這項操作。 如需建立並與 VM 相關聯的完整步驟，請參閱[針對 VM，使用 Azure CLI 設定指派給使用者的 MSI](msi-qs-configure-cli-windows-vm.md)。

在下列範例中，指派給使用者的 MSI 獲得存取權的儲存體帳戶。  

1. 為了提供 MSI 存取權，您需要 MSI 的服務主體的用戶端識別碼 （也稱為應用程式識別碼）。 用戶端識別碼由提供[az 身分識別建立](/cli/azure/identity#az_identity_create)，於 MSI 和其 （如下所示的參考） 的服務主體的佈建：

   ```azurecli-interactive
   az identity create -g rgPrivate -n msiServiceApp
   ```

   成功的回應包含指派給使用者的 MSI 的服務主體中的用戶端識別碼`clientId`屬性：

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

2. 已知的用戶端識別碼，一旦使用[az 角色指派建立](/cli/azure/role/assignment#az_role_assignment_create)將 MSI 存取指派給另一個資源。 務必使用您的用戶端 ID`--assignee`參數，且比對的訂用帳戶 ID 和資源群組名稱，當您執行時傳回`az identity create`。 這裡 MSI 指派 「 讀取 」 存取權給名為"myStorageAcct 」 的儲存體帳戶：

   ```azurecli-interactive
   az role assignment create --assignee 9391e5b1-dada-4a8z-834a-43ad44f296bl --role Reader --scope /subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

   成功的回應看起來類似下列輸出：

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
- 若要讓指派給使用者的 MSI Azure VM 上，請參閱[設定指派給使用者管理服務身分識別 (MSI) VM，使用 Azure CLI](msi-qs-configure-cli-windows-vm.md)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。

