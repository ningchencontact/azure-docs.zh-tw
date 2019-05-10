---
title: 管理 Azure Cosmos DB 中的索引編製原則
description: 了解如何管理 Azure Cosmos DB 中的索引編製原則
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: thweiss
ms.openlocfilehash: 48d67c765a8a76a6058592f59eb61770e2f23df5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068664"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>管理 Azure Cosmos DB 中的索引編製原則

Azure Cosmos DB 會遵循針對每個容器所定義的[索引編製原則](index-policy.md)來為資料編製索引。 新建立的容器所套用的預設索引編製原則，會對任何字串或數字強制執行範圍索引，而對 Point 類型的 GeoJSON 物件強制執行空間索引。 此原則可透過下列方式加以覆寫：

- 從 Azure 入口網站
- 使用 Azure CLI
- 使用其中一個 SDK

[更新索引編製原則](index-policy.md#modifying-the-indexing-policy)將會觸發索引的轉換。 您也可以從 SDK 追蹤此轉換的進度。

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

Azure Cosmos 容器會將其索引編製原則儲存為 JSON 文件，並可從 Azure 入口網站直接進行編輯。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 建立新的 Azure Cosmos 帳戶，或選取現有帳戶。

1. 開啟 [資料總管] 窗格，然後選取您要處理的容器。

1. 按一下 [規模與設定]。

1. 修改索引編製原則 JSON 文件 (請參閱[下面](#indexing-policy-examples)範例)

1. 當您完成時，按一下 [儲存]。

![使用 Azure 入口網站管理索引編製](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>使用 Azure CLI

從 Azure CLI 執行 [az cosmosdb collection update](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-update) 命令可讓您取代容器索引編製原則的 JSON 定義：

```azurecli-interactive
az cosmosdb collection update \
    --resource-group-name $resourceGroupName \
    --name $accountName \
    --db-name $databaseName \
    --collection-name $containerName \
    --indexing-policy "{\"indexingMode\": \"consistent\", \"includedPaths\": [{ \"path\": \"/*\", \"indexes\": [{ \"dataType\": \"String\", \"kind\": \"Range\" }] }], \"excludedPaths\": [{ \"path\": \"/headquarters/employees/?\" } ]}"
```

## <a name="use-the-net-sdk-v2"></a>使用 .NET SDK V2

[.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 中的 `DocumentCollection` 物件 (請參閱關於其使用方式的[這個快速入門](create-sql-api-dotnet.md)) 會公開 `IndexingPolicy` 屬性，以供您變更 `IndexingMode` 以及新增或移除 `IncludedPaths` 和 `ExcludedPaths`。

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// set the indexing mode to Consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/headquarters/employees/?" });
// update the container with our changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

若要追蹤索引的轉換進度，請傳遞 `RequestOptions` 物件並將其 `PopulateQuotaInfo` 屬性設定為 `true`。

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-java-sdk"></a>使用 Java SDK

[Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) 中的 `DocumentCollection` 物件 (請參閱關於其使用方式的[這個快速入門](create-sql-api-java.md)) 會公開 `getIndexingPolicy()` 和 `setIndexingPolicy()` 方法。 這些方法所管理的 `IndexingPolicy` 物件可讓您變更索引編製模式，以及新增或移除已納入和排除的路徑。

```java
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
    DocumentCollection container = result.getResource();
    IndexingPolicy indexingPolicy = container.getIndexingPolicy();
    // set the indexing mode to Consistent
    indexingPolicy.setIndexingMode(IndexingMode.Consistent);
    Collection<ExcludedPath> excludedPaths = new ArrayList<>();
    ExcludedPath excludedPath = new ExcludedPath();
    excludedPath.setPath("/*");
    // add an excluded path
    excludedPaths.add(excludedPath);
    indexingPolicy.setExcludedPaths(excludedPaths);
    // update the container with our changes
    client.replaceCollection(container, null);
});
```

若要在容器上追蹤索引轉換進度，請傳遞會要求填入配額資訊的 `RequestOptions` 物件，然後從 `x-ms-documentdb-collection-index-transformation-progress` 回應標頭中擷取值。

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>使用 Node.js SDK

[Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) 中的 `ContainerDefinition` 介面 (請參閱關於其使用方式的[這個快速入門](create-sql-api-nodejs.md)) 會公開 `indexingPolicy` 屬性，以供您變更 `indexingMode` 以及新增或移除 `includedPaths` 和 `excludedPaths`。

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read();
// set the indexing mode to Consistent
containerResponse.body.indexingPolicy.indexingMode = "consistent";
// add an excluded path
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/headquarters/employees/?' });
// update the container with our changes
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

若要在容器上追蹤索引轉換進度，請傳遞會將 `populateQuotaInfo` 屬性設定為 `true` 的 `RequestOptions` 物件，然後從 `x-ms-documentdb-collection-index-transformation-progress` 回應標頭中擷取值。

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>使用 Python SDK

使用 [Python SDK](https://pypi.org/project/azure-cosmos/) 時 (請參閱關於其使用方式的[這個快速入門](create-sql-api-python.md))，系統會以字典的形式來管理容器設定。 您可以從這個字典存取索引編製原則和其所有屬性。

```python
containerPath = 'dbs/database/colls/collection'
# retrieve the container's details
container = client.ReadContainer(containerPath)
# set the indexing mode to Consistent
container['indexingPolicy']['indexingMode'] = 'consistent'
# add an excluded path
container['indexingPolicy']['excludedPaths'] = [{"path" : "/headquarters/employees/?"}]
# update the container with our changes
response = client.ReplaceContainer(containerPath, container)
```

## <a name="indexing-policy-examples"></a>索引編製原則範例

下面有一些以其 JSON 格式顯示的索引編製原則範例，這就是其在 Azure 入口網站上公開的方式。 透過 Azure CLI 或任何 SDK 也可以設定相同的參數。

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>可選擇性地排除一些屬性路徑的退出原則
```
    {
        "indexingPolicy": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    },
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>可選擇性地納入一些屬性路徑的加入原則
```
    {
        "indexingPolicy": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

注意：一般會建議您使用**退出**索引編製原則來讓 Azure Cosmos DB 主動地對可能會新增至模型的新屬性編製索引。

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>僅對特定屬性路徑使用空間索引
```
    {
        "indexingPolicy": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/geojson/property/?",
                "indexes": [
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": []
    }
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>排除所有屬性路徑但讓索引編製保持作用狀態

此原則可用於[存留時間 (TTL) 功能](time-to-live.md)有作用但不需要 (即可使用 Azure Cosmos DB 作為純粹的索引鍵-值存放區) 次要索引的情況。
```
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>無索引編製
```
    {
        "indexingPolicy": "none"
    }
```

## <a name="composite-indexing-policy-examples"></a>複合式索引編製原則範例

除了包含或排除個別屬性的路徑以外，您也可以指定複合式索引。 如果您要為多個屬性執行具有 `ORDER BY` 子句的查詢，則必須要有這些屬性的[複合式索引](index-policy.md#composite-indexes)。

### <a name="composite-index-defined-for-name-asc-age-desc"></a>針對 (name asc, age desc) 定義的複合式索引：
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

此複合式索引能夠支援下列兩個查詢：

查詢 1：
```sql
    SELECT *
    FROM c
    ORDER BY name asc, age desc    
```

查詢 2：
```sql
    SELECT *
    FROM c
    ORDER BY name desc, age asc
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>針對 (name asc, age asc) 和 (name asc, age desc) 定義的複合式索引：

您可以在相同的索引編製原則內定義多個不同的複合式索引。 
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ]
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>針對 (name asc, age asc) 定義的複合式索引：

指定順序是選擇性動作。 若未指定，將採用遞增順序。
```
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

## <a name="next-steps"></a>後續步驟

在下列文章中深入了解編製索引：

- [索引編製概觀](index-overview.md)
- [編製索引原則](index-policy.md)
