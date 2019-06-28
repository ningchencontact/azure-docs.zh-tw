---
title: Azure Cosmos DB 中的 SQL 查詢執行
description: 深入了解 Azure Cosmos DB 中的 SQL 查詢執行
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: e4e26b658bd29e4589be40e4d29935059836c909
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342754"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Azure Cosmos DB SQL 查詢執行

任何能發出 HTTP/HTTPS 要求的語言都可以呼叫 Cosmos DB REST API。 Cosmos DB 也會提供.NET、 Node.js、 JavaScript 和 Python 程式設計語言的程式設計程式庫。 所有 REST API 和程式庫都支援透過 SQL、 查詢和.NET SDK 也支援[LINQ 查詢](sql-query-linq-to-sql.md)。

下列範例顯示如何建立查詢，並針對 Cosmos DB 資料庫帳戶提交它。

## <a id="REST-API"></a>REST API

Cosmos DB 提供透過 HTTP 的開放 RESTful 程式設計模型。 資源模型包含一組資源，以資料庫帳戶的 Azure 訂用帳戶佈建。 資料庫帳戶包含一組*資料庫*，其中每一個可以包含多個*容器*，其中又包含*項目*，Udf 和其他資源類型。 每個 Cosmos DB 資源是可利用邏輯和穩定 URI 定址。 一組資源稱為*摘要*。 

與這些資源的基本互動模型是透過 HTTP 指令動詞`GET`， `PUT`， `POST`，和`DELETE`，具有其標準解譯。 使用`POST`若要建立新的資源，請執行預存程序，或發出 Cosmos DB 查詢。 查詢一律是唯讀作業，而且沒有任何副作用。

下列範例會顯示`POST`SQL API 查詢的範例項目。 查詢會對 JSON 中的簡單篩選`name`屬性。 `x-ms-documentdb-isquery`和 內容類型：`application/query+json`標頭表示作業是查詢。 取代`mysqlapicosmosdb.documents.azure.com:443`與 Cosmos DB 帳戶的 URI。

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

結果為：

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

接下來，更複雜的查詢會從聯結傳回多個結果：

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": []
    }
```

結果為： 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

如果查詢的結果無法放入單一頁面中，REST API 會傳回接續 token 透過`x-ms-continuation-token`回應標頭。 用戶端可以藉由在後續的結果中包括標頭分頁結果。 您也可以控制每個透過頁面的結果數目`x-ms-max-item-count`標頭。

如果查詢的計數等彙總函式，[查詢] 頁面可能透過只有一頁結果會傳回部分彙總的值。 用戶端必須對這些結果來產生最終的結果執行第二層彙總。 例如，對個別頁面，以傳回總計數中傳回的計數加總。

若要管理查詢的資料一致性原則，請使用`x-ms-consistency-level`如同所有的 REST API 要求的標頭。 工作階段一致性，也需要回應的最新`x-ms-session-token`在查詢要求的 cookie 標頭。 所查詢容器的編製索引原則也會影響查詢結果的一致性。 與編製索引原則設定適用於容器的預設值，索引一律具有最新項目內容中，而且查詢結果會符合針對資料所選擇的一致性。 如需詳細資訊，請參閱 [Azure Cosmos DB 一致性層級] [一致性層級]。

如果在容器上設定的索引原則無法支援指定的查詢，Azure Cosmos DB 伺服器就會傳回 400 「 錯誤要求 」。 此錯誤訊息傳回查詢的明確排除編製索引的路徑。 您可以指定`x-ms-documentdb-query-enable-scan`標頭以允許索引無法使用時，執行掃描的查詢。

您也可以設定查詢執行取得詳細的計量`x-ms-documentdb-populatequerymetrics`標頭`true`。 如需詳細資訊，請參閱[適用於 Azure Cosmos DB 的 SQL 查詢計量](sql-api-query-metrics.md)。

## <a name="c-net-sdk"></a>C# (.NET SDK)

.NET SDK 支援 LINQ 和 SQL 查詢。 下列範例示範如何執行上述的篩選條件查詢，使用.NET:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

下列範例會比較每個項目，內相等的兩個屬性，並使用匿名投射。

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

下一個範例顯示聯結，透過 LINQ 表示`SelectMany`。

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

.NET 用戶端自動逐一查看查詢結果中的所有頁面`foreach`區塊中使用，如上述範例所示。 中導入的查詢選項[REST API](#REST-API)一節中也會有.NET SDK 中，使用`FeedOptions`並`FeedResponse`中的類別`CreateDocumentQuery`方法。 您可以使用來控制的頁面數目`MaxItemCount`設定。

您可以建立，以明確地控制分頁`IDocumentQueryable`使用`IQueryable`物件，然後藉由讀取`ResponseContinuationToken`做為傳回的值，並將它們`RequestContinuationToken`在`FeedOptions`。 您可以設定`EnableScanInQuery`啟用掃描時所設定的索引編製原則不支援的查詢。 您可以使用資料分割的容器，`PartitionKey`來針對單一資料分割，執行查詢，雖然 Azure Cosmos DB 可以從自動擷取查詢文字。 您可以使用`EnableCrossPartitionQuery`針對多個分割區執行查詢。

如需使用查詢的多個.NET 範例，請參閱[Azure Cosmos DB.NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)在 GitHub 中。

## <a id="JavaScript-server-side-API"></a>JavaScript 伺服器端 API

Azure Cosmos DB 提供的程式設計模型[執行 JavaScript 型應用程式](stored-procedures-triggers-udfs.md)邏輯直接在容器中，使用預存程序和觸發程序。 在容器層級註冊的 JavaScript 邏輯接著可以發出指定的容器，包裝在環境 ACID 交易中的項目上的資料庫作業。

下列範例示範如何使用`queryDocuments`在 JavaScript 伺服器 API，可進行查詢，從預存程序和觸發程序內：

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a name="next-steps"></a>後續步驟

- [Azure Cosmos DB 簡介](introduction.md)
- [Azure Cosmos DB .NET 範例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Azure Cosmos DB 一致性層級](consistency-levels.md)
