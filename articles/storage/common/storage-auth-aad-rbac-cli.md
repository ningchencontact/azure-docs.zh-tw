---
title: 使用 Azure CLI 來管理 Azure AD rbac-Azure 儲存體 blob 和佇列資料的存取權限
description: 使用 Azure CLI 來指派存取權給容器和佇列，使用角色型存取控制 (RBAC)。 Azure 儲存體支援透過 Azure AD 的驗證內建和自訂的 RBAC 角色。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: de8cb99ae5db93c2438a9ea982ad1c6c9324b47f
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449950"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-azure-cli"></a>授與存取權與使用 Azure CLI 的 RBAC 的 Azure blob 和佇列資料

Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../../role-based-access-control/overview.md)，來授與存取受保護資源的權限。 Azure 儲存體定義一組內建的 RBAC 角色，其中包含用來存取 blob 或佇列資料的權限的一組通用。 

RBAC 角色指派給 Azure AD 安全性主體時，Azure 授與存取這些資源的安全性主體。 存取權的範圍可以包括訂用帳戶、資源群組、儲存體帳戶或個別的容器或佇列層級。 Azure AD 安全性主體，可能是使用者、 群組、 應用程式的服務主體，或[受管理的 Azure 資源的識別](../../active-directory/managed-identities-azure-resources/overview.md)。

本文說明如何使用 Azure CLI 列出內建的 RBAC 角色，並將它們指派給使用者。 如需使用 Azure CLI 的詳細資訊，請參閱[Azure 命令列介面 (CLI)](https://docs.microsoft.com/cli/azure)。

## <a name="rbac-roles-for-blobs-and-queues"></a>適用於 Blob 和佇列的 RBAC 角色

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>判斷資源範圍 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>列出可用的 RBAC 角色

若要列出可用的內建 RBAC 角色，使用 Azure CLI，請使用[az 角色定義清單](/cli/azure/role/definition#az-role-definition-list)命令：

```azurecli-interactive
az role definition list --out table
```

您會看到列出，與其他內建的角色，適用於 Azure 的內建 Azure 儲存體資料角色：

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-user"></a>將 RBAC 角色指派給使用者

若要指派使用者 RBAC 角色，請使用[az 角色指派建立](/cli/azure/role/assignment#az-role-assignment-create)命令。 命令的格式可以根據指派的範圍而不同。 下列範例示範如何將角色指派給不同範圍的使用者。

### <a name="container-scope"></a>容器範圍

若要指派至容器的範圍設定的角色，指定字串，包含用於容器的範圍`--scope`參數。 容器的範圍的格式：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

下列範例會將指派**儲存體 Blob 資料參與者**角色給使用者，一個名為容器只限於*sample container>*。 請務必以您自己的值取代範例值和方括號中的預留位置值： 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>佇列範圍

若要指派角色，以佇列範圍，指定字串，包含的佇列領域`--scope`參數。 佇列範圍的格式：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

下列範例會將指派**儲存體佇列資料參與者**給使用者，以佇列名為範圍的角色*範例佇列*。 請務必以您自己的值取代範例值和方括號中的預留位置值： 

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>儲存體 」 帳戶範圍

若要指派範圍設定至儲存體帳戶的角色，以指定的儲存體帳戶資源的範圍`--scope`參數。 儲存體帳戶的範圍的格式：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

下列範例示範如何指派**儲存體 Blob 資料讀者**層級的儲存體帳戶的使用者角色。 請務必以您自己的值取代範例值： 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/storagesamples"
```

### <a name="resource-group-scope"></a>資源群組範圍

若要指派給資源群組範圍設定的角色，指定資源群組名稱或識別碼`--resource-group`參數。 下列範例會將指派**儲存體佇列資料讀者**給資源群組的層級的使用者角色。 請務必將範例值和括號括住的預留位置值取代為您自己的值： 

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group sample-resource-group
```

### <a name="subscription-scope"></a>訂用帳戶範圍

若要指派訂用帳戶範圍設定的角色，指定的訂用帳戶的範圍`--scope`參數。 訂用帳戶範圍的格式：

```
/subscriptions/<subscription>
```

下列範例示範如何指派**儲存體 Blob 資料讀者**層級的儲存體帳戶的使用者角色。 請務必以您自己的值取代範例值： 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription-id>"
```

## <a name="next-steps"></a>後續步驟

- [使用 RBAC 與 Azure PowerShell 管理 Azure 資源的存取權](../../role-based-access-control/role-assignments-powershell.md)
- [授與存取權與使用 Azure PowerShell 的 RBAC 的 Azure blob 和佇列資料](storage-auth-aad-rbac-powershell.md)
- [Azure blob 和佇列資料使用 RBAC 在 Azure 入口網站中授與存取權](storage-auth-aad-rbac-portal.md)
