---
title: 在 Azure Cosmos DB 中建立容器
description: 了解如何在 Azure Cosmos DB 中建立容器
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: d22103a049d62f48e8b9b9d3f71138842fbfaa89
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262483"
---
# <a name="create-a-container-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中建立容器

本文說明建立容器 (集合、資料表、圖形) 的不同方式。 您可以使用 Azure 入口網站、Azure CLI 或支援的 SDK 來建立容器。 本文將示範如何建立容器、指定分割區索引鍵和佈建輸送量。

## <a name="create-a-container-using-azure-portal"></a>使用 Azure 入口網站建立容器

### <a id="portal-sql"></a>SQL (Core) API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Cosmos DB 帳戶](create-sql-api-dotnet.md#create-a-database-account)，或選取現有的帳戶。

1. 開啟 [資料總管] 窗格，然後選取 [新增集合]。 接著，在表單中填入下列詳細資料：

   * 建立新的資料庫或使用現有的資料庫。
   * 輸入集合識別碼。
   * 選取 [不受限] 的儲存體容量。
   * 輸入分割區索引鍵。
   * 輸入輸送量，例如 1000 RU。
   * 選取 [確定] 。

![SQL API 建立集合](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>MongoDB API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Cosmos DB 帳戶](create-mongodb-dotnet.md#create-a-database-account)，或選取現有的帳戶。

1. 開啟 [資料總管] 窗格，然後選取 [新增集合]。 接著，在表單中填入下列詳細資料：

   * 建立新的資料庫或使用現有的資料庫。
   * 輸入集合識別碼。
   * 選取 [不受限] 的儲存體容量。
   * 輸入分區索引鍵。
   * 輸入輸送量，例如 1000 RU。
   * 選取 [確定] 。

![MongoDB API 建立集合](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>Cassandra API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Cosmos DB 帳戶](create-cassandra-dotnet.md#create-a-database-account)，或選取現有的帳戶。

1. 開啟 [資料總管] 窗格，然後選取 [新增資料表]。 接著，在表單中填入下列詳細資料：

   * 建立新的 Keyspace 或使用現有的 Keyspace。
   * 輸入資料表名稱。
   * 輸入屬性並指定主索引鍵。
   * 輸入輸送量，例如 1000 RU。
   * 選取 [確定] 。

![Cassandra API 建立集合](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Cassandra API 的主索引鍵會作為分割區索引鍵。

### <a id="portal-gremlin"></a>Gremlin API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Cosmos DB 帳戶](create-graph-dotnet.md#create-a-database-account)，或選取現有的帳戶。

1. 開啟 [資料總管] 窗格，然後選取 [新增圖形]。 接著，在表單中填入下列詳細資料：

   * 建立新的資料庫或使用現有的資料庫。
   * 輸入圖形識別碼。
   * 選取 [不受限] 的儲存體容量。
   * 輸入頂點的分割區索引鍵。
   * 輸入輸送量，例如 1000 RU。
   * 選取 [確定] 。

![Gremlin API 建立集合](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>資料表 API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Cosmos DB 帳戶](create-table-dotnet.md#create-a-database-account)，或選取現有的帳戶。

1. 開啟 [資料總管] 窗格，然後選取 [新增資料表]。 接著，在表單中填入下列詳細資料：

   * 輸入資料表識別碼。
   * 選取 [不受限] 的儲存體容量。
   * 輸入輸送量，例如 1000 RU。
   * 選取 [確定] 。

![資料表 API 建立集合](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> 對於資料表 API，在您每次新增資料列時都會指定分割區索引鍵。

## <a name="create-a-container-using-azure-cli"></a>使用 Azure CLI 建立容器

### <a id="cli-sql"></a>SQL (Core) API

```azurecli-interactive
# Create a container with a partition key and provision 1000 RU/s throughput.

az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-mongodb"></a>MongoDB API

```azurecli-interactive
# Create a collection with a shard key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $collectionName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myShardKey \
    --throughput 1000
```

### <a id="cli-cassandra"></a>Cassandra API

```azurecli-interactive
# Create a table with a partition/primary key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $keyspaceName \
    --partition-key-path /myPrimaryKey \
    --throughput 1000
```

### <a id="cli-gremlin"></a>Gremlin API

```azurecli-interactive
# Create a graph with a partition key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $graphName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-table"></a>資料表 API

```azurecli-interactive
# Create a table with 1000 RU/s
# Note: you don't need to specify partition key in the following command because the partition key is set on each row.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $databaseName \
    --throughput 1000
```

## <a name="create-a-container-using-net-sdk"></a>使用 .NET SDK 建立容器

### <a id="dotnet-sql-graph"></a>SQL API 和 Gremlin API

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-mongodb"></a>MongoDB API

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB 並沒有要求單位的概念。 若要建立具有輸送量的新集合，請使用 Azure 入口網站或 SQL API，如前述範例所說明。

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以了解 Cosmos DB 中的資料分割：

- [Azure Cosmos DB 中的資料分割](partitioning-overview.md)
