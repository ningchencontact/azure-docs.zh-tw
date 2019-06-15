---
title: 取得 SQL 查詢的效能和執行計量
description: 了解如何擷取 SQL 查詢執行計量和設定檔的 Azure Cosmos DB 要求的 SQL 查詢效能。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: b4017666956d0e01ea19781fb4f1ce2dde15fff5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481560"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>取得 SQL 查詢執行計量，並分析查詢效能，使用.NET SDK

本文介紹如何分析 Azure Cosmos DB 上的 SQL 查詢效能。 此程式碼剖析可使用`QueryMetrics`擷取自.NET SDK，而這裡詳述。 [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx)是強類型的物件的後端的查詢執行的相關資訊。 這些度量會記載於更詳細地[微調查詢效能](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)文章。

## <a name="set-the-feedoptions-parameter"></a>設定 FeedOptions 參數

針對所有多載[DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx)中的選擇性[FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx)參數。 此選項可讓查詢執行來進行調整和參數化。 

若要收集的 Sql 查詢執行計量，您必須設定參數[PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics)中[FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx)到`true`。 設定`PopulateQueryMetrics`至 true 就變得如此`FeedResponse`會包含相關`QueryMetrics`。 

## <a name="get-query-metrics-with-asdocumentquery"></a>取得與 AsDocumentQuery() 查詢計量
下列程式碼範例示範如何使用時，不要擷取度量[AsDocumentQuery()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx)方法：

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>彙總 QueryMetrics

在上一節中，請注意已多次呼叫[ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx)方法。 每次呼叫傳回`FeedResponse`物件的字典， `QueryMetrics`，一個查詢的每個接續。 下列範例示範如何彙總這些`QueryMetrics`使用 LINQ:

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>依資料分割識別碼的群組查詢計量

您可以將群組`QueryMetrics`的分割區識別碼。 依資料分割識別碼的群組可讓您查看特定的分割區是否會造成效能問題，相較於其他項目。 下列範例示範如何分組`QueryMetrics`linq:

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>DocumentQuery 上的 LINQ

您也可以取得`FeedResponse`LINQ 查詢，使用`AsDocumentQuery()`方法：

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>且費時的查詢

您可以擷取的要求單位的每個查詢所耗用調查且費時的查詢或取用高輸送量的查詢。 您可以使用來取得要求費用[RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx)屬性中的`FeedResponse`。 若要深入了解如何使用 Azure 入口網站和不同的 Sdk 的要求費用，請參閱[尋找的要求單位費用](find-request-unit-charge.md)文章。

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>取得查詢執行時間

計算時執行的用戶端查詢所需的時間，請確定您只包含要呼叫的時間`ExecuteNextAsync`方法並不是程式碼基底的其他部分。 只有這些呼叫會協助您在計算花費多少時間執行查詢，在下列範例所示：

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>掃描 （通常緩慢且昂貴） 的查詢

掃描查詢是指不由索引，因為，許多查詢傳回的結果集之前載入的文件。

以下是掃描查詢的範例：

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

此查詢的篩選器會使用系統函數右上角，不由索引。 執行這項查詢的大型集合產生第一個接續的下列查詢度量：

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

請注意查詢計量的輸出中的以下值：

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

此查詢會載入 60,951 文件，而加 399,998,938 位元組。 正在載入這麼多的位元組會導致高成本或要求單位費用。 它也會需要長時間執行查詢，也就是清除所花費的總時間屬性：

```
Total Query Execution Time               :        4,500.34 milliseconds
```

這表示，查詢需要執行 4.5 秒 （這是只有一個接續）。

若要最佳化此查詢的範例，避免上方的篩選條件中使用。 相反地，當文件建立或更新，`c.description`必須以全部大寫的字元插入值。 查詢接著會變成： 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

此查詢現在就可以從索引提供的。

若要深入了解調整查詢效能，請參閱[微調查詢效能](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)文章。

## <a id="References"></a>參考

- [Azure Cosmos DB SQL 規格](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>後續步驟

- [微調查詢效能](sql-api-query-metrics.md)
- [索引編製概觀](index-overview.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)
