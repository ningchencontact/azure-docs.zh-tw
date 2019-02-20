---
title: 建立索引定義與概念 - Azure 搜尋服務
description: 介紹 Azure 搜尋服務中的索引字詞和概念，包括元件部分和實體結構。
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/01/2019
ms.custom: seodec2018
ms.openlocfilehash: 77f4b597ad4b87db7e720dd57191c6b192a4c93b
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000944"
---
# <a name="create-a-basic-index-in-azure-search"></a>在 Azure 搜尋服務中建立基本索引

在 Azure 搜尋服務中，*索引*是 Azure 搜尋服務上的*文件*及其他用於經篩選和全文搜尋的建構所做的持續性儲存。 就概念而言，文件是索引中可搜尋資料的單一單位。 例如，電子商務零售商可能會有儲存了每個銷售項目的文件、新聞組織可能會有儲存了每篇文章的文件，類似情況不一而足。 對應這些概念到更熟悉的資料庫同等項目：索引在概念上類似於資料表，而文件大致上相當於資料表中的資料列。

當您新增或上傳索引時，Azure 搜尋服務會根據您提供的結構描述建立實體結構。 例如，如果您索引中的某個欄位被標記為可搜尋，系統便會針對該欄位建立反向的索引。 當您稍後新增或上傳文件，或提交搜尋查詢到 Azure 搜尋服務時，您是將要求傳送到搜尋服務中的特定索引。 載入具有文件值的欄位，被稱為「編製索引」或資料擷取。

您可以在入口網站、[REST API](search-create-index-rest-api.md) 或 [.NET SDK](search-create-index-dotnet.md)中建立索引。

## <a name="components-of-an-index"></a>索引的元件

就結構描述而言，Azure 搜尋服務索引會包含下列元素。 

「[欄位集合](#fields-collection)」通常是索引中最大的一部分，其中每個欄位都會具有名稱、類型和屬性，以及可決定其使用方式的可允許行為。 其他元素包括[建議工具](#suggesters)、[評分設定檔](#scoring-profiles)、具有元件組件以支援自訂的[分析器](#analyzers)，以及 [CORS](#cors) 選項。

```json
{  
  "name": (optional on PUT; required on POST) "name_of_index",  
  "fields": [  
    {  
      "name": "name_of_field",  
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",  
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),  
      "filterable": true (default) | false,  
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),  
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),  
      "key": true | false (default, only Edm.String fields can be keys),  
      "retrievable": true (default) | false,  
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }  
  ],  
  "suggesters": [  
    {  
      "name": "name of suggester",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": ["field1", "field2", ...]  
    }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "name of scoring profile",  
      "text": (optional, only applies to searchable fields) {  
        "weights": {  
          "searchable_field_name": relative_weight_value (positive #'s),  
          ...  
        }  
      },  
      "functions": (optional) [  
        {  
          "type": "magnitude | freshness | distance | tag",  
          "boost": # (positive number used as multiplier for raw score != 1),  
          "fieldName": "...",  
          "interpolation": "constant | linear (default) | quadratic | logarithmic",  
          "magnitude": {  
            "boostingRangeStart": #,  
            "boostingRangeEnd": #,  
            "constantBoostBeyondRange": true | false (default)  
          },  
          "freshness": {  
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)  
          },  
          "distance": {  
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)  
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)  
          },  
          "tag": {  
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)  
          }  
        }  
      ],  
      "functionAggregation": (optional, applies only when functions are specified)   
        "sum (default) | average | minimum | maximum | firstMatching"  
    }  
  ],  
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",  
  "corsOptions": (optional) {  
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],  
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)  
  }  
}
```

## <a name="fields-collection-and-attribution"></a>欄位集合與屬性
您在定義結構描述時必須指定索引中每個欄位的名稱、類型和屬性。 欄位類型可分類該欄位中儲存的資料。 個別欄位上設定的屬性可指定使用欄位的方式。 下列幾個資料表列舉您可以指定的類型和屬性。

### <a name="data-types"></a>資料類型
| 類型 | 說明 |
| --- | --- |
| *Edm.String* |可選擇性予以 Token 化以供進行全文檢索搜尋 (斷字、詞幹分析等) 的文字。 |
| *Collection(Edm.String)* |可選擇性予以 Token 化以供進行全文檢索搜尋的字串清單。 理論上，集合中的項目數沒有上限，但集合的承載大小有 16 MB 的上限。 |
| *Edm.Boolean* |包含 true/false 值。 |
| *Edm.Int32* |32 位元整數值。 |
| *Edm.Int64* |64 位元整數值。 |
| *Edm.Double* |雙精度數值資料。 |
| *Edm.DateTimeOffset* |以 OData V4 格式 (例如 `yyyy-MM-ddTHH:mm:ss.fffZ` 或 `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`) 表示的日期時間值。 |
| *Edm.GeographyPoint* |代表地球上地理位置的一點。 |

您可以在這裡找到有關 Azure 搜尋服務 [支援的資料類型](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)的詳細資訊。

### <a name="index-attributes"></a>索引屬性
| 屬性 | 說明 |
| --- | --- |
| *Key* |字串，提供每一份文件的唯一識別碼，用於查閱文件。 每個索引必須有一個索引鍵。 只有一個欄位可以做為索引鍵，而且其類型必須設定為 Edm.String。 |
| *Retrievable* |指定搜尋結果中是否可傳回某欄位。 |
| *Filterable* |允許欄位用於篩選查詢。 |
| *Sortable* |允許查詢使用此欄位排序搜尋結果。 |
| *Facetable* |允許欄位用於使用者自我引導篩選的 [多面向導覽](search-faceted-navigation.md) 結構中。 通常，欄位若包含您可以用來將多份文件群組在一起的重複值 (例如，落在單一品牌或服務類別目錄下的多份文件)，最適合做為 Facet。 |
| *Searchable* |將欄位標記為可供全文檢索。 |

您可以在這裡找到有關 Azure 搜尋服務 [索引屬性](https://docs.microsoft.com/rest/api/searchservice/Create-Index)的詳細資訊。

## <a name="suggesters"></a>建議工具
建議工具是結構描述的區段，其定義索引中有哪些欄位會被用來支援搜尋中的自動完成或預先輸入查詢。 系統通常會在使用者輸入搜尋查詢期間，將部分搜尋字串傳送到「建議」(Azure 搜尋服務 REST API)，而該 API 會傳回一組建議的片語。 您在索引中定義的建議工具會判斷要使用哪個欄位來建立自動完成的搜尋詞彙。 如需詳細資訊，請參閱[新增建議工具](index-add-suggesters.md)以取得設定詳細資料。

## <a name="scoring-profiles"></a>評分設定檔

評分設定檔是結構描述的區段，能定義自訂評分行為，讓您能夠影響搜尋結果中哪些項目的出現機率會比較高。 評分設定檔是由欄位權數和函式所組成。 若要使用它們，您可以在查詢字串上以名稱指定設定檔。

預設的評分設定檔會在背景運行，以針對結果集中的每個項目計算搜尋分數。 您可以使用內部未命名的評分設定檔。 或者，設定 defaultScoringProfile 以使用自訂設定檔做為預設值，以在查詢字串中沒有指定自訂設定檔時叫用。

如需詳細資訊，請參閱[新增評分設定檔](index-add-scoring-profiles.md)。

## <a name="analyzers"></a>分析器

分析器元素會設定要用於欄位之語言分析器的名稱。 如需允許的值組，請參閱 [Azure 搜尋服務中的語言分析器](index-add-language-analyzers.md)。 此選項只可以搭配可搜尋的欄位使用，而無法與 **searchAnalyzer** 或 **indexAnalyzer** 一起設定。 選擇分析器之後，就無法針對此欄位進行變更。

## <a name="cors"></a>CORS

用戶端的 JavaScript 預設無法呼叫任何 API，因為瀏覽器會阻止所有跨原始來源的要求。 若要允許對索引進行跨原始來源查詢，請設定 **corsOptions** 屬性來啟用 CORS (跨原始來源資源共用)。 基於安全緣故，僅查詢 API 才能支援 CORS。 

您可以為 CORS 設定下列選項：

+ **allowedOrigins** (必要)：這是將授與您索引存取權限的原始來源清單。 這表示將允許來自那些原始來源的所有 JavaScript 程式碼查詢您的索引 (假設它能提供正確的 API 金鑰)。 每個原始來源的形式通常是 `protocol://<fully-qualified-domain-name>:<port>` (儘管經常會忽略 `<port>`)。 請參閱[跨原始來源資源共用](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)以取得詳細資料。

  如果您想要允許所有原始來源進行存取，請在 **allowedOrigins** 陣列中包含 `*` 作為單一項目。 「此做法並不建議用於生產環境搜尋服務」，但對於開發和偵錯通常很有幫助。

+ **maxAgeInSeconds** (選擇性)：瀏覽器會使用此值來判斷快取 CORS 預檢回應的持續期間 (以秒為單位)。 這必須是非負數的整數。 這個值越大，效能就越好，但是讓 CORS 原則變更生效的時間也就越長。 若未設定，即會使用預設持續期間 5 分鐘。

## <a name="next-steps"></a>後續步驟

在了解索引的結構之後，您接著便可以在入口網站中建立第一個索引。

> [!div class="nextstepaction"]
> [新增索引 (入口網站)](search-create-index-portal.md)