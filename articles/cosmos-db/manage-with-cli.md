---
title: 使用 Azure CLI 管理 Azure Cosmos DB 資源
description: 使用 Azure CLI 來管理您的 Azure Cosmos DB 帳戶、資料庫和容器。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: f9d8bf9161343e4b36a3c16209873962b69d8af5
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615217"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>使用 Azure CLI 管理 Azure Cosmos 資源

下列指南說明使用 Azure CLI 自動化管理 Azure Cosmos DB 帳戶、資料庫和容器的常見命令。 您可以在 [Azure CLI 參考](https://docs.microsoft.com/cli/azure/cosmosdb)中取得所有 Azure Cosmos DB CLI 命令的參考頁面。 您也可以在[適用於 Azure Cosmos DB 的 Azure CLI 範例](cli-samples.md)中找到更多範例，包括如何針對 MongoDB、Gremlin、Cassandra 及資料表 API建立和管理 Cosmos DB 帳戶、資料庫和容器。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-an-azure-cosmos-db-account"></a>建立 Azure Cosmos DB 帳戶

若要建立具有 SQL API 的 Azure Cosmos DB 帳戶、美國東部和美國西部區域中的會話一致性, 請執行下列命令:

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount # must be lowercase and < 31 characters \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=EastUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=WestUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> Azure Cosmos 帳戶名稱必須是小寫。

## <a name="create-a-database"></a>建立資料庫

若要建立 Cosmos 資料庫, 請執行下列命令:

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>建立容器

若要建立具有 RU/秒400和分割區索引鍵的 Cosmos 容器, 請執行下列命令:

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --partition-key-path /myPartitionKey \
   --throughput 400
```

## <a name="change-the-throughput-of-a-container"></a>變更容器的輸送量

若要將 Cosmos 容器的輸送量變更為 1000 RU/秒, 請執行下列命令:

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --throughput 1000
```

## <a name="list-account-keys"></a>列出帳戶金鑰

若要取得 Cosmos 帳戶的金鑰, 請執行下列命令:

```azurecli-interactive
# List account keys
az cosmosdb keys list \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>列出連接字串

若要取得 Cosmos 帳戶的連接字串, 請執行下列命令:

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>重新產生帳戶金鑰

若要為您的 Cosmos 帳戶重新產生新的主要金鑰, 請執行下列命令:

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --key-kind primary
```

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱：

- [安裝 Azure CLI](/cli/azure/install-azure-cli)
- [Azure CLI 參考](https://docs.microsoft.com/cli/azure/cosmosdb)
- [適用於 Azure Cosmos DB 的其他 Azure CLI 範例](cli-samples.md)
