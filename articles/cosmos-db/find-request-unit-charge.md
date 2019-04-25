---
title: 在 Azure Cosmos DB 中尋找要求單位 (RU) 費用
description: 了解如何尋找對 Azure Cosmos 容器執行的任何作業所產生的要求單位費用
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: thweiss
ms.openlocfilehash: 833f815f0c84584f084e4d4637c0318f7c2daec0
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683829"
---
# <a name="find-the-request-unit-ru-charge-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中尋找要求單位 (RU) 費用

本文將介紹不同的方法，供您尋找對 Azure Cosmos 容器執行的任何作業所產生的[要求單位](request-units.md)耗用量。 此耗用量目前可使用 Azure 入口網站來測量，或藉由查看 Azure Cosmos DB 透過其中一個 SDK 傳回的回應來測量。

## <a name="sql-core-api"></a>SQL (Core) API

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

目前，Azure 入口網站只能讓您尋找 SQL 查詢的要求費用。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-sql-api-dotnet.md#create-account)並為其提供資料，或選取已包含資料的現有 Azure Cosmos 帳戶。

1. 開啟 [資料總管] 窗格，然後選取您要處理的容器。

1. 按一下 [新增 SQL 查詢]。

1. 輸入有效的查詢，然後按一下 [執行查詢]。

1. 按一下 [查詢統計資料]，以顯示您剛剛執行的要求實際的要求費用。

![Azure 入口網站上的 SQL 查詢要求費用的螢幕擷取畫面](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk-v2"></a>使用 .NET SDK V2

從 [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 傳回的物件 (請參閱[本快速入門](create-sql-api-dotnet.md)中有關於其使用方式的資訊) 會公開 `RequestCharge` 屬性。

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

### <a name="use-the-java-sdk"></a>使用 Java SDK

從 [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) 傳回的物件 (請參閱[本快速入門](create-sql-api-java.md)中有關於其使用方式的資訊) 會公開 `getRequestCharge()` 方法。

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

### <a name="use-the-nodejs-sdk"></a>使用 Node.js SDK

從 [Java SDK](https://www.npmjs.com/package/@azure/cosmos) 傳回的物件 (請參閱[本快速入門](create-sql-api-nodejs.md)中有關於其使用方式的資訊) 會公開 `headers` 子物件 (用以對應基礎 HTTP API 所傳回的所有標頭)。 要求費用會提供在 `x-ms-request-charge` 索引鍵下方。

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

### <a name="use-the-python-sdk"></a>使用 Python SDK

[Python SDK](https://pypi.org/project/azure-cosmos/) 傳回的 `CosmosClient` 物件 (請參閱[本快速入門](create-sql-api-python.md)中有關於其使用方式的資訊) 會公開 `last_response_headers` 字典 (用以對應基礎 HTTP API 針對最後執行的作業傳回的所有標頭)。 要求費用會提供在 `x-ms-request-charge` 索引鍵下方。

```python
response = client.ReadItem('dbs/database/colls/container/docs/itemId', { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure('dbs/database/colls/container/sprocs/storedProcedureId', None, { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']
```

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>適用於 MongoDB 的 Azure Cosmos DB API

要求單位費用是由名為 `getLastRequestStatistics.` 的自訂[資料庫命令](https://docs.mongodb.com/manual/reference/command/)所公開。此命令會傳回一份文件，其中包含最後執行的作業名稱，及其要求費用和持續時間。

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

目前，Azure 入口網站只能讓您尋找查詢的要求費用。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-mongodb-dotnet.md#create-a-database-account)並為其提供資料，或選取已包含資料的現有帳戶。

1. 開啟 [資料總管] 窗格，然後選取您要處理的集合。

1. 按一下 [新增查詢]。

1. 輸入有效的查詢，然後按一下 [執行查詢]。

1. 按一下 [查詢統計資料]，以顯示您剛剛執行的要求實際的要求費用。

![Azure 入口網站上的 MongoDB 查詢要求費用的螢幕擷取畫面](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>使用 MongoDB .NET 驅動程式

使用[官方 MongoDB .NET 驅動程式](https://docs.mongodb.com/ecosystem/drivers/csharp/)時 (請參閱[本快速入門](create-mongodb-dotnet.md)中有關於其使用方式的資訊)，可藉由對 `IMongoDatabase` 物件呼叫 `RunCommand` 方法來執行命令。 使用此方法時，必須實作 `Command<>` 抽象類別。

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

### <a name="use-the-mongodb-java-driver"></a>使用 MongoDB Java 驅動程式

使用[官方 MongoDB Java 驅動程式](http://mongodb.github.io/mongo-java-driver/)時 (請參閱[本快速入門](create-mongodb-java.md)中有關於其使用方式的資訊)，可藉由對 `MongoDatabase` 物件呼叫 `runCommand` 方法來執行命令。

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

### <a name="use-the-mongodb-nodejs-driver"></a>使用 MongoDB Node.js 驅動程式

使用[官方 MongoDB Node.js 驅動程式](https://mongodb.github.io/node-mongodb-native/)時 (請參閱[本快速入門](create-mongodb-nodejs.md)中有關於其使用方式的資訊)，可藉由對 `db` 物件呼叫 `command` 方法來執行命令。

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

## <a name="cassandra-api"></a>Cassandra API

對 Azure Cosmos DB 的 Cassandra API 執行作業時，要求單位費用會在傳入的裝載中以名為 `RequestCharge` 的欄位傳回。

### <a name="use-the-net-sdk"></a>使用 .NET SDK

使用 [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/) 時 (請參閱[本快速入門](create-cassandra-dotnet.md)中有關於其使用方式的資訊)，可在 `RowSet` 物件的 `Info` 屬性下方擷取傳入的裝載。

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"], 0);
```

### <a name="use-the-java-sdk"></a>使用 Java SDK

使用 [Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core) 時 (請參閱[本快速入門](create-cassandra-java.md)中有關於其使用方式的資訊)，可藉由對 `ResultSet` 物件呼叫 `getExecutionInfo()` 方法來擷取傳入的裝載。

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

## <a name="gremlin-api"></a>Gremlin API

### <a name="use-drivers-and-sdk"></a>使用驅動程式和 SDK

Gremlin API 所傳回的標頭會對應至目前透過 Gremlin.NET 和 Java SDK 來呈現的自訂狀態屬性。 要求費用會提供在 `x-ms-request-charge` 索引鍵下方。

### <a name="use-the-net-sdk"></a>使用 .NET SDK

使用 [Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/) 時 (請參閱[本快速入門](create-graph-dotnet.md)中有關於其使用方式的資訊)，狀態屬性會提供在 `ResultSet<>` 物件的 `StatusAttributes` 屬性下方。

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

### <a name="use-the-java-sdk"></a>使用 Java SDK

使用 [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) 時 (請參閱[本快速入門](create-graph-java.md)中有關於其使用方式的資訊)，可藉由對 `ResultSet` 物件呼叫 `statusAttributes()` 方法來擷取狀態屬性。

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

## <a name="table-api"></a>資料表 API

目前唯一會針對資料表作業傳回要求單位費用的 SDK，是 [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) (請參閱[本快速入門](create-table-dotnet.md)中有關於其使用方式的資訊)。 此 SDK 用於 Azure Cosmos DB 的資料表 API 時，`TableResult` 物件會公開由此 SDK 填入的 `RequestCharge` 屬性。

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以深入了解如何最佳化您的要求單位耗用量：

* [Azure Cosmos DB 中的要求單位和輸送量](request-units.md)
* [在 Azure Cosmos DB 中最佳化已佈建的輸送量成本](optimize-cost-throughput.md)
* [在 Azure Cosmos DB 中最佳化查詢成本](optimize-cost-queries.md)
* [全域調整佈建的輸送量](scaling-throughput.md)
* [在容器和資料庫中佈建輸送量](set-throughput.md)
* [如何佈建容器的輸送量](how-to-provision-container-throughput.md)