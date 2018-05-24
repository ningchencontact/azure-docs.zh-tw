---
title: 建立索引子 (Azure 搜尋服務 REST api-version=2017-11-11-Preview)
description: 在預覽 API 中，索引子已擴充為包含 outputFieldMapping 參數，此參數可用來將擴充輸出對應至 Azure 搜尋服務索引中的欄位。
services: search
manager: pablocas
author: luiscabrer
ms.author: luisca
ms.date: 05/01/2018
ms.prod: azure
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.openlocfilehash: d47b14342caf0312e052584d20f1a9c86ca29cad
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786917"
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>建立索引子 (Azure 搜尋服務 REST api-version=2017-11-11-Preview)

此 API 參考是預覽特定的文件版本，涵蓋編製索引的認知搜尋增強功能。

在[正式推出](https://docs.microsoft.com/rest/api/searchservice/create-indexer)版本中，您就可以使用 HTTP POST 要求，在 Azure 搜尋服務內建立新的索引子。 

```http
POST https://[service name].search.windows.net/indexers?api-version=[api-version]  
    Content-Type: application/json  
    api-key: [admin key]  
```  
**api-key** 必須為管理員金鑰 (相對於查詢金鑰)。 如需金鑰的詳細資訊，請參閱 [Azure 搜尋服務中的安全性](search-security-overview.md)的驗證一節。 [在入口網站中建立 Azure 搜尋服務](search-create-service-portal.md)說明如何取得要求所使用的服務 URL 與金鑰屬性。

或者，您可以使用 PUT，在 URI 中指定資料來源的名稱。 如果索引不存在，系統就會加以建立。  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
需要 **api-version**。 目前版本為 `2016-09-01`。 如需詳細資料，請參閱 [Azure 搜尋服務中的 API 版本](search-api-versions.md)。

如需建立索引子的資料平台特定指引，請從[索引子概觀](search-indexer-overview.md)開始，其中包含[相關文章](search-indexer-overview.md#next-steps)的完整清單。

> [!NOTE]  
>  允許的索引子數目上限依定價層而有所不同。 免費服務允許最多 3 個索引子。 標準服務允許 50 個索引子。 如需詳細資訊，請參閱 [服務限制](search-limits-quotas-capacity.md) 。    

## <a name="request"></a>要求  
 要求的本文包含索引子定義，以指定要編製索引的資料來源與目標索引，以及選用的索引排程和參數。  

 下列為建構要求承載的語法。 本主題稍後會提供範例要求。  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "schedule" : { Optional. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```

### <a name="indexer-schedule"></a>索引子排程  
索引子可以選擇性地指定排程。 如有排程，索引子會依照排程定期執行。 排程器是內建功能，您無法使用外部排程器。 **排程**具有下列屬性： 

-   **間隔**：必要。 可用以指定索引子執行間隔或期間的持續時間值。 允許的最小間隔為 5 分鐘；最長間隔為一天。 其必須格式化為 XSD "dayTimeDuration" 值 ( [ISO 8601 持續時間](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 值的受限子集)。 模式是：`"P[nD][T[nH][nM]]".` 範例：`PT15M` 代表每隔 15 分鐘，`PT2H` 代表每隔 2 個小時。  

-   **startTime** - 選用。 索引子應該開始執行的 UTC 日期時間。  

### <a name="indexer-parameters"></a>索引子參數  
 您可選擇性地為索引子指定數個參數來影響其行為。 下面所列的所有參數都是選擇性的。  

-   **maxFailedItems**：在索引子執行發生錯誤前，可能無法編製索引的項目數。 預設值為 0。 [取得索引子狀態 &#40;Azure 搜尋服務 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) 作業可傳回失敗項目的相關資訊。  

-   **maxFailedItemsPerBatch**：在認定索引子執行失敗之前，每個批次中無法編製索引的項目數。 預設值為 0。  

-   **batchSize:**：指定從資料來源讀取並以單一批次編製索引來提升效能的項目數。 預設值取決於資料來源類型：Azure SQL 和 Azure Cosmos DB 的預設值為 1000，而 Azure Blob 儲存體的預設值為 10。

### <a name="field-mapping-parameters"></a>欄位對應參數

索引子定義包含了欄位關聯，可將 Azure 搜尋服務索引中的來源欄位對應至目的地欄位。 有兩種類型的關聯，視內容傳輸遵循直接或擴充路徑而定：

+ **fieldMappings** 是選擇性的，當來源目的地欄位名稱不相符時，或當您想要指定函式時適用。
+ 如果您要建置[擴充管線](cognitive-search-concept-intro.md)，就需要 **outputFieldMappings**。 在擴充管線中，輸出欄位是在擴充程序期間所定義的建構。 例如，輸出欄位可能是在擴充期間，從來源文件中的兩個個別欄位所建置的複合結構。 

#### <a name="fieldmappings"></a>fieldMappings

在下列範例中，假設來源資料表包含 `_id` 欄位。 Azure 搜尋服務不允許欄位名稱的開頭是底線，因此必須重新命名欄位。 您可使用索引子的 `fieldMappings` 屬性來進行這項作業，如下所示：

```json
"fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
```

您可以指定多個欄位對應：

```json
"fieldMappings" : [
    { "sourceFieldName" : "_id", "targetFieldName" : "id" },
    { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" }
]
```

來源和目標欄位名稱皆有區分大小寫。

若要瞭解欄位對應的實用案例，請參閱[搜尋索引子欄位對應](https://docs.microsoft.com/azure/search/search-indexer-field-mappings)。

#### <a name="outputfieldmappings"></a>outputFieldMappings

在技能集繫結至索引子的認知搜尋案例中，您必須新增 `outputFieldMappings`，以便將提供內容的擴充步驟輸出，關聯至索引中的可搜尋欄位。

```json
  "outputFieldMappings" : [
        {
          "sourceFieldName" : "/document/organizations", 
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*", 
          "targetFieldName" : "keyphrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "language",
            "mappingFunction": null
        }      
   ],
```

<a name="FieldMappingFunctions"></a>

#### <a name="field-mapping-functions"></a>欄位對應函式

您也可以使用「欄位對應函式」，以欄位對應用來轉換來源欄位值。 例如，以 base64 編碼的任意字串值可以用來填入文件索引鍵欄位。

若要深入了解何時及如何使用欄位對應函式，請參閱[欄位對應函式](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions)。

### <a name="request-body-examples"></a>要求本文範例  
 下列範例會建立索引子，以將資料從 `ordersds` 資料來源所參考的資料表，依照排程 (從 2015 年 1 月 1 日 UTC 開始，每小時執行一次) 複製到 `orders` 索引。 如果每個批次中無法編製索引的項目不超過 5 個，且無法編製索引的項目總數不超過 10 個的話，每個索引子的引動都將成功。  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

## <a name="response"></a>Response  
 「201 已建立」表示要求成功。  

## <a name="see-also"></a>另請參閱

+ [認知搜尋概觀](cognitive-search-concept-intro.md)
+ [快速入門：試用認知搜尋](cognitive-search-quickstart-blob.md)
+ [如何對應欄位](cognitive-search-output-field-mapping.md)
