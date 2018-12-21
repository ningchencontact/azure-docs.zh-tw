---
title: Azure CLI 指令碼：建立 Azure Cosmos DB 資料表 API 帳戶、資料庫和資料表
description: Azure CLI 指令碼範例：建立 Azure Cosmos DB 資料表 API 帳戶、資料庫和資料表
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.topic: sample
ms.date: 10/26/2018
ms.openlocfilehash: 5e9cd406f02ea17c4d91e316cbd3f0cc937f6afc
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957567"
---
# <a name="azure-cosmos-db-create-a-table-api-account-using-azure-cli"></a>Azure Cosmos DB：使用 Azure CLI 來建立資料表 API 帳戶

此範例 CLI 指令碼會建立 Azure Cosmos DB 資料表 API 帳戶、資料庫和資料表。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/create-cosmosdb-table-account/create-cosmosdb-table-account.sh "Create an Azure Cosmos DB Table API account, database, and table.")]

## <a name="clean-up-deployment"></a>清除部署

在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | 建立 Azure Cosmos DB 帳戶。 |
| [az cosmosdb database create](/cli/azure/cosmosdb/database#az-cosmosdb-database-create) | 建立 Azure Cosmos DB 資料庫。 |
| [az cosmosdb collection create](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-create) | 建立 Azure Cosmos DB 資料表。 |
| [az group delete](/cli/azure/resource#az-resource-delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure Cosmos DB CLI 文件](../cli-samples.md)中找到其他 Azure Cosmos DB CLI 指令碼範例。
