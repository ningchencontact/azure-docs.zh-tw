---
title: Azure 搜尋服務中的查詢基礎 | Microsoft Docs
description: 在 Azure 搜尋服務中建置搜尋查詢，並使用參數來篩選、選取及排序結果的基本概念。
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/27/2018
ms.openlocfilehash: 4650ad89850f32ae5e83a7ac1cd5eac096b180ed
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366441"
---
# <a name="query-fundamentals-in-azure-search"></a>Azure 搜尋服務中的查詢基礎

Azure 搜尋服務中的查詢定義是包含下列組件的完整要求規格：服務 URL 端點、目標索引、用來驗證要求的 API 金鑰、API 版本和查詢字串。 

查詢字串組合由[搜尋文件 API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 中定義的參數所組成。 最重要的是 **search=** 參數，此參數可能未定義 (`search=*`)，但通常會包含字詞、片語和運算子，如下列範例所示：

```
"search": "seattle townhouse +\"lake\""
```

其他參數可用來指引查詢處理或強化回應。 參數使用方式的範例，包括將搜尋範圍限定於特定欄位、設定搜尋模式以調整精確度-召回率偏差，以及新增計數以便追蹤結果。 

```
{  
    "search": "seattle townhouse* +\"lake\"",  
    "searchFields": "description, city",  
    "searchMode": "all",
    "count": "true", 
    "queryType": "simple" 
 } 
```

雖然查詢定義是基本要件，但索引結構描述在逐欄位指定允許的作業方面同樣具有重要性。 在索引開發期間，欄位的屬性將會決定允許的作業。 例如，欄位必須同時標示為*可搜尋*和*可擷取*，才會符合全文檢索搜尋的條件，並包含在搜尋結果中。

查詢一律會針對單一索引執行，並使用要求中提供 API 金鑰進行驗證。 您無法加入索引，或建立自訂或暫存資料結構作為查詢目標。  

即使您使用 .NET API 而內建序列化，查詢結果仍會在 REST API 中以 JSON 文件的形式串流處理。 您可以設定查詢的參數，並為結果選取特定欄位，以修改結果

總結來說，查詢要求的本質會指定範圍和作業：要在搜尋中包含哪些欄位，要在結果集中包含哪些欄位，是否排序或篩選等等。 未指定時，將會以全文檢索搜尋作業的形式對所有可搜尋的欄位執行查詢，並以任意順序傳回未計分的結果集。

<a name="types-of-queries"></a>

## <a name="types-of-queries-search-and-filter"></a>查詢的類型：搜尋和篩選

Azure 搜尋服務提供許多選項可建立功能極為強大的查詢。 您將會使用的兩個主要查詢類型是 `search` 和 `filter`。 

+ `search` 查詢會在索引的所有可搜尋欄位中掃描一或多個字詞，且其運作方式會如同 Google 或 Bing 等搜尋引擎。 簡介中的範例使用 `search` 參數。

+ `filter` 查詢會對索引中的所有可篩選欄位評估布林運算式。 不同於 `search`，`filter` 查詢會比對欄位的確切內容，包括字串欄位的大小寫。

您可以同時或個別使用搜尋和篩選。 獨立的篩選條件 (不含查詢字串) 在篩選條件運算式能夠完全限定相關文件時，將有其效用。 沒有查詢字串，就沒有語彙或語言分析、沒有計分且沒有排名。 請注意，搜尋字串是空的。

```
POST /indexes/nycjobs/docs/search?api-version=2017-11-11  
    {  
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "count": "true"
    }
```

搭配使用時，會先將篩選套用到整個索引，再對篩選結果執行搜尋。 由於篩選能夠減少搜尋查詢需要處理的資料集合，因此對於提升查詢效能方面是很實用的技術。

篩選運算式的語法是 [OData 篩選語言](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search)的子集。 對於搜尋查詢，您可以使用[簡化語法](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)或 [Lucene 查詢語法](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search)，下面會有這兩種語法的討論。


## <a name="choose-a-syntax-simple-or-full"></a>選擇語法：簡單或完整

Azure 搜尋服務位於 Apache Lucene 之上，並可讓您從兩種查詢剖析器中選擇其一來處理一般和特製化查詢。 一般搜尋要求會使用預設的[簡單查詢語法](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)來編寫。 此語法支援許多常見的搜尋運算子，包括 AND、OR、NOT、片語、尾碼和優先順序運算子。

[Lucene 查詢語法](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax)會在您將 **queryType=full** 新增置要求時啟用，可公開廣為採用、且開發作為 [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 一部分的表達式查詢語言。 使用此查詢語法可建立特製化查詢：

+ [欄位範圍查詢](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields)
+ [模糊搜尋](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy)
+ [鄰近搜尋](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity)
+ [詞彙提升](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost)
+ [規則運算式搜尋](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex)
+ [萬用字元搜尋](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard)

布林運算子在這兩種語法中大致相同，只是在完整 Lucene 中會有其他格式：

+ [簡單語法中的布林運算子](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search#operators-in-simple-search)
+ [完整 Lucene 語法中的布林運算子](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean)

## <a name="required-and-optional-elements"></a>必要和選用元素

在提交搜尋要求給 Azure 搜尋服務時，除了在應用程式搜尋方塊中實際輸入的單字以外，還可以指定幾個參數。 這些查詢參數可讓您更深入地控制[全文檢索搜尋體驗](search-lucene-query-architecture.md)。

查詢要求的必要元素包含下列元件：

+ 服務端點和索引文件集合，在此以 URL `https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs` 表示。
+ API 版本 (僅限 REST)，以 **api-version=** 表示
+ 查詢或系統管理 API 金鑰，以 **api-key=** 表示
+ 以 **search=** 表示的查詢字串，如果您要執行空的搜尋，可以未指定。 您也可以只傳送篩選條件運算式作為 **$filter=**。
+ **queryType=** (簡單或完整)，如果您要使用預設的簡單語法，則可以省略。

其他所有搜尋參數都是選用的。

## <a name="apis-and-tools-for-testing"></a>測試用的 API 和工具

下表列出使用 API 和工具來提交查詢的方法。

| 方法 | 說明 |
|-------------|-------------|
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | 可用來查詢 Azure 搜尋服務索引的用戶端。  <br/>[深入了解。](search-howto-dotnet-sdk.md#core-scenarios)  |
| [搜尋文件 (REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | 索引的 GET 或 POST 方法，使用查詢參數作為額外輸入。  |
| [Fiddler、Postman 或其他 HTTP 測試工具](search-fiddler.md) | 說明如何設定將查詢傳送至 Azure 搜尋服務的要求標頭和本文。  |
| [Azure 入口網站中的搜尋總管](search-explorer.md) | 提供搜尋列以及選取索引和 API 版本的選項。 結果會以 JSON 文件的形式傳回。 <br/>[深入了解。](search-get-started-portal.md#query-index) | 

## <a name="manage-search-results"></a>管理搜尋結果

查詢的參數可透過下列方式用來建構結果集：

+ 限制或批次處理結果中的文件數目 (預設為 50 個)
+ 選取要包含在結果中的欄位
+ 設定排序次序
+ 新增搜尋結果醒目提示，以凸顯搜尋結果本文中的相符字詞

### <a name="tips-for-unexpected-results"></a>未預期結果的提示

有時候，結果的本質和結構會不符預期的。 當查詢結果不符合您想看到的結果時，您可以嘗試進行下列查詢修改，看看結果是否有所改善：

+ 將 `searchMode=any` (預設值) 變更為 `searchMode=all`，以要求須符合所有準則，而非任何準則。 此做法在查詢中包含布林運算子時特別有用。

+ 如果文字或語彙分析是必要的，但查詢類型無法進行語言處理，請變更查詢技術。 在全文檢索搜尋中，文字或語彙分析會自動更正拼字錯誤、單數複數文字形式，甚至不規則的動詞或名詞。 在像是模糊或萬用字元搜尋等某些查詢中，文字分析並不是查詢剖析管線的一部分。 在某些情況下，規則運算式是既有的因應措施。 

### <a name="paging-results"></a>分頁結果
Azure 搜尋服務可讓您輕鬆地對搜尋結果分頁。 透過使用 `top` 和 `skip` 參數，您就可以順利地發出搜尋要求，將所收到的整組搜尋結果分拆成方便管理且經過排序的子集，輕鬆實現良好的搜尋 UI 做法。 在收到這些分拆成較小子集的結果時，您也會收到整組搜尋結果中所含文件的計數。

您可以在 [如何在 Azure 搜尋服務中對搜尋結果分頁](search-pagination-page-layout.md)一文中深入了解如何對搜尋結果分頁。

### <a name="ordering-results"></a>排序結果
收到搜尋查詢的結果時，您可以要求 Azure 搜尋服務提供依特定欄位值排序的結果。 根據預設，Azure 搜尋服務會以每份文件的搜尋分數排名 (衍生自 [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) 來排序搜尋結果。

如果您想要讓 Azure 搜尋服務在傳回結果時是以搜尋分數以外的值來排序，您可以使用 `orderby` 搜尋參數。 您可以指定 `orderby` 參數的值，納入欄位名稱和 [`geo.distance()` 函式](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search)的呼叫以獲得地理空間值。 每個運算式後面都可以加上 `asc` 來表示要以遞增順序要求結果，而加上 `desc` 則表示要以遞減順序要求結果。 預設排名為遞增順序。


### <a name="hit-highlighting"></a>搜尋結果醒目提示
在 Azure 搜尋服務中，只要使用 `highlight`、`highlightPreTag` 和 `highlightPostTag` 參數就可強調提示搜尋結果中符合搜尋查詢的確切部分。 您可以指定哪些可搜尋欄位應該強調其相符的文字，以及指定要在 Azure 搜尋服務傳回的相符文字開頭和結尾附加的確切字串標記。

## <a name="see-also"></a>另請參閱

+ [全文檢索搜尋如何在 Azure 搜尋服務中運作 (查詢剖析架構)](search-lucene-query-architecture.md)
+ [搜尋總管](search-explorer.md)
+ [如何在 .NET 中進行查詢](search-query-dotnet.md)
+ [如何在 REST 中進行查詢](search-query-rest-api.md)
