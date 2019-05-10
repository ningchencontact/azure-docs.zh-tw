---
title: 在 Azure Cosmos DB 中建立容器
description: 了解如何在 Azure Cosmos DB 中建立容器
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: 3952ad98cf734de322741ace80c17029e282b94c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068705"
---
# <a name="create-an-azure-cosmos-container"></a>建立 Azure Cosmos 容器

本文說明建立 Azure Cosmos 容器 (集合、資料表或圖形) 的不同方式。 您可使用 Azure 入口網站、Azure CLI 或支援的 SKD。 本文將示範如何建立容器、指定分割區索引鍵，以及佈建輸送量。

## <a name="create-a-container-using-azure-portal"></a>使用 Azure 入口網站建立容器

### <a id="portal-sql"></a>SQL API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-sql-api-dotnet.md#create-account)，或選取現有帳戶。

1. 開啟 [資料總管] 窗格，然後選取 [新增集合]。 接下來，提供下列詳細資料：

   * 指出您正在建立新的資料庫，還是使用現有的帳戶。
   * 輸入集合識別碼。
   * 輸入分割區索引鍵。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定] 。

![醒目提示 [新增集合] 的資料總管窗格螢幕擷取畫面](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>適用於 MongoDB 的 Azure Cosmos DB API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-mongodb-dotnet.md#create-a-database-account)，或選取現有帳戶。

1. 開啟 [資料總管] 窗格，然後選取 [新增集合]。 接下來，提供下列詳細資料：

   * 指出您正在建立新的資料庫，還是使用現有的帳戶。
   * 輸入集合識別碼。
   * 輸入分區索引鍵。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定] 。

![適用於 MongoDB 的 Azure Cosmos DB API 中 [新增集合] 對話方塊的螢幕擷取畫面](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>Cassandra API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-cassandra-dotnet.md#create-a-database-account)，或選取現有帳戶。

1. 開啟 [資料總管] 窗格，然後選取 [新增資料表]。 接下來，提供下列詳細資料：

   * 指出您正在建立新的 Keyspace，還是使用現有的 Keyspace。
   * 輸入資料表名稱。
   * 輸入屬性並指定主索引鍵。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定] 。

![Cassandra API 中 [新增資料表] 對話方塊的螢幕擷取畫面](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Cassandra API 的主索引鍵會作為分割區索引鍵。

### <a id="portal-gremlin"></a>Gremlin API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-graph-dotnet.md#create-a-database-account)，或選取現有帳戶。

1. 開啟 [資料總管] 窗格，然後選取 [新增圖形]。 接下來，提供下列詳細資料：

   * 指出您正在建立新的資料庫，還是使用現有的帳戶。
   * 輸入圖形識別碼。
   * 選取 [不受限] 的儲存體容量。
   * 輸入頂點的分割區索引鍵。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定] 。

![Gremlin API 中 [新增圖形] 對話方塊的螢幕擷取畫面](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>資料表 API

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-table-dotnet.md#create-a-database-account)，或選取現有帳戶。

1. 開啟 [資料總管] 窗格，然後選取 [新增資料表]。 接下來，提供下列詳細資料：

   * 輸入資料表識別碼。
   * 輸入要佈建的輸送量 (例如 1000 RU)。
   * 選取 [確定] 。

![資料表 API 中 [新增資料表] 對話方塊的螢幕擷取畫面](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> 對於資料表 API，在您每次新增資料列時都會指定分割區索引鍵。

## <a name="create-a-container-using-azure-cli"></a>使用 Azure CLI 建立容器

### <a id="cli-sql"></a>SQL API

```azurecli-interactive
# Create a container with a partition key and provision 400 RU/s throughput.

az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 400
```

### <a id="cli-mongodb"></a>適用於 MongoDB 的 Azure Cosmos DB API

```azurecli-interactive
# Create a collection with a shard key and provision 400 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $collectionName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myShardKey \
    --throughput 400
```

### <a id="cli-cassandra"></a>Cassandra API

```azurecli-interactive
# Create a table with a partition/primary key and provision 400 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $keyspaceName \
    --partition-key-path /myPrimaryKey \
    --throughput 400
```

### <a id="cli-gremlin"></a>Gremlin API

```azurecli-interactive
# Create a graph with a partition key and provision 400 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $graphName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 400
```

### <a id="cli-table"></a>資料表 API

```azurecli-interactive
# Create a table with 400 RU/s
# Note: you don't need to specify partition key in the following command because the partition key is set on each row.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $databaseName \
    --throughput 400
```

## <a name="create-a-container-using-powershell"></a>使用 PowerShell 建立容器

下列範例說明如何建立在 Azure Cosmos DB 中佈建容器層級資源所需的所有支援資源

### <a id="ps-sql"></a>SQL API

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.
$databaseName = "database1"
$databaseResourceName = $accountName + "/sql/" + $databaseName
$containerName = "container1"
$containerResourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

# Create account
$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$accountProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Kind "GlobalDocumentDB" -Name $accountName -PropertyObject $accountProperties


# Create database with shared throughput
$databaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseResourceName -PropertyObject $databaseProperties

# Create a container with default policies
$containerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }
    }; 
    "options"=@{}
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerResourceName -PropertyObject $containerProperties 
```

### <a id="ps-cassandra"></a>Cassandra API

```azurepowershell-interactive
# Create an Azure Cosmos Account for Cassandra API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.
$keyspaceName = "keyspace1"
$keyspaceResourceName = $accountName + "/cassandra/" + $keyspaceName
$tableName = "table1"
$tableResourceName = $accountName + "/cassandra/" + $keyspaceName + "/" + $tableName

# Create account
$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$accountProperties = @{
    "capabilities"= @( @{ "name"="EnableCassandra" } );
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Kind "GlobalDocumentDB" -Name $accountName -PropertyObject $accountProperties

# Create keyspace with shared throughput
$keyspaceProperties = @{
    "resource"=@{ "id"=$keyspaceName };
    "options"=@{ "Throughput"="400" }
}
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/keyspaces" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $keyspaceResourceName -PropertyObject $keyspaceProperties

# Create a table
$tableProperties = @{
    "resource"=@{
        "id"=$tableName; 
        "schema"= @{
            "columns"= @(
                @{ "name"= "loadid"; "type"= "uuid" };
                @{ "name"= "machine"; "type"= "uuid" };
                @{ "name"= "cpu"; "type"= "int" };
                @{ "name"= "mtime"; "type"= "int" };
                @{ "name"= "load"; "type"= "float" };
            );
            "partitionKeys"= @(
                @{ "name"= "machine" };
                @{ "name"= "cpu" };
                @{ "name"= "mtime" }; 
            );
            "clusterKeys"= @( 
                @{ "name"= "loadid"; "orderBy"= "asc" }
            )
        }
    }; 
    "options"=@{}
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/keyspaces/tables" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $tableResourceName -PropertyObject $tableProperties 

```

### <a id="ps-mongodb"></a>MongoDB API

```azurepowershell-interactive
# Create a collection for an Azure Cosmos Account for MongoDB API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.
$databaseName = "database1"
$databaseResourceName = $accountName + "/mongodb/" + $databaseName
$collectionName = "collection1"
$collectionResourceName = $accountName + "/mongodb/" + $databaseName + "/" + $collectionName

# Create account
$locations = @(
    @{ "locationName"="East US 2"; "failoverPriority"=0 },
    @{ "locationName"="North Central US"; "failoverPriority"=1 }
)

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$accountProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Kind "MongoDB" -Name $accountName -PropertyObject $accountProperties


# Create database with shared throughput
$databaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseResourceName -PropertyObject $databaseProperties


# Create Collection
$collectionProperties = @{
    "resource"=@{
        "id"=$collectionName; 
        "shardKey"= @{ "user_id"="Hash" };
        "indexes"= @(
            @{
                "key"= @{ "keys"=@("user_id", "user_address") };
                "options"= @{ "unique"= "true" }
            };
            @{
                "key"= @{ "keys"=@("_ts") };
                "options"= @{ "expireAfterSeconds"= "1000" }
            }
        )
    }; 
    "options"=@{}
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/collections" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $collectionResourceName -PropertyObject $collectionProperties 
```

### <a id="ps-gremlin"></a>Gremlin API

```azurepowershell-interactive
# Create an Azure Cosmos Account for Gremlin API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.
$databaseName = "database1"
$databaseResourceName = $accountName + "/gremlin/" + $databaseName
$graphName = "graph1"
$graphResourceName = $accountName + "/gremlin/" + $databaseName + "/" + $graphName

# Create account
$locations = @(
    @{ "locationName"="East US 2"; "failoverPriority"=0 },
    @{ "locationName"="North Central US"; "failoverPriority"=1 }
)

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$accountProperties = @{
    "capabilities"= @( @{ "name"="EnableGremlin" } );
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Kind "GlobalDocumentDB" -Name $accountName -PropertyObject $accountProperties


# Create database with shared throughput
$databaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseResourceName -PropertyObject $databaseProperties


# Create a graph with defaults
$graphProperties = @{
    "resource"=@{
        "id"=$graphName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }
    }; 
    "options"=@{}
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/graphs" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $graphResourceName -PropertyObject $graphProperties 
```

### <a id="ps-table"></a>資料表 API

```azurepowershell-interactive
# Create an Azure Cosmos account for Table API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.
$tableName = "table1"
$tableResourceName = $accountName + "/table/" + $tableName

# Create account
$locations = @(
    @{ "locationName"="East US 2"; "failoverPriority"=0 },
    @{ "locationName"="North Central US"; "failoverPriority"=1 }
)

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$accountProperties = @{
    "capabilities"= @( @{ "name"="EnableTable" } );
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Kind "GlobalDocumentDB" -Name $accountName -PropertyObject $accountProperties


# Create table
$tableProperties = @{
    "resource"=@{ "id"=$tableName };
    "options"=@{ "Throughput"="400" }
}
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/tables" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $tableResourceName -PropertyObject $tableProperties
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

### <a id="dotnet-mongodb"></a>適用於 MongoDB 的 Azure Cosmos DB API

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB 網路通訊協定不了解[要求單位](request-units.md)的概念。 若要建立已佈建輸送量的新集合，請使用 Azure 入口網站或適用於 SQL API 的 Cosmos DB SDK。

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 中的資料分割](partitioning-overview.md)
- [Azure Cosmos DB 中的要求單位](request-units.md)
- [在容器和資料庫中佈建輸送量](set-throughput.md)
- [使用 Azure Cosmos 帳戶](account-overview.md)