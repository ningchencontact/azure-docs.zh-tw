---
title: 使用 Azure CLI 管理 Azure Cosmos DB 資源
description: 使用 Azure CLI 來管理您的 Azure Cosmos DB 帳戶、資料庫和容器。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 144515fef9da714ab80f15bb39757ed2283c6dd0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243368"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>使用 Azure CLI 管理 Azure Cosmos 資源

下列指南說明常用的命令，以自動化管理您的 Azure Cosmos DB 帳戶、 資料庫及使用 Azure CLI 的容器。 您可以在 [Azure CLI 參考](https://docs.microsoft.com/cli/azure/cosmosdb)中取得所有 Azure Cosmos DB CLI 命令的參考頁面。 您也可以在[適用於 Azure Cosmos DB 的 Azure CLI 範例](cli-samples.md)中找到更多範例，包括如何針對 MongoDB、Gremlin、Cassandra 及資料表 API建立和管理 Cosmos DB 帳戶、資料庫和容器。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-an-azure-cosmos-db-account"></a>建立 Azure Cosmos DB 帳戶

若要使用 SQL API 建立 Azure Cosmos DB 帳戶，在美國東部和美國西部區域中的工作階段一致性，請執行下列命令：

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations EastUS=0 WestUS=1 \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> Azure Cosmos 帳戶名稱必須是小寫。

## <a name="create-a-database"></a>建立資料庫

若要建立 Cosmos DB 資料庫，執行下列命令：

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>建立容器

若要建立的 Cosmos DB 容器以 RU/秒 400 和資料分割索引鍵，執行下列命令：

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

若要變更 1000 RU/秒的 Cosmos DB 容器輸送量，請執行下列命令：

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

若要取得您的 Cosmos 帳戶的金鑰，請執行下列命令：

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>列出連接字串

若要取得您的 Cosmos 帳戶連接字串，請執行下列命令：

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>重新產生帳戶金鑰

若要重新產生新的主要金鑰，為您的 Cosmos 帳戶，請執行下列命令：

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
