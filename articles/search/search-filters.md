---
title: Filter on search results
titleSuffix: Azure Cognitive Search
description: Filter by user security identity, language, geo-location, or numeric values to reduce search results on queries in Azure Cognitive Search, a hosted cloud search service on Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f4ce3cd0db20f76aa6169f15254cf36ee64151a5
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406753"
---
# <a name="filters-in-azure-cognitive-search"></a>Filters in Azure Cognitive Search 

A *filter* provides criteria for selecting documents used in an Azure Cognitive Search query. 未篩選的搜尋會包含索引中的所有文件。 篩選條件會將搜尋查詢的範圍設定為文件子集。 例如，篩選條件可以限制只對具有特定品牌或色彩且超過特定閾值之價格點的產品進行全文檢索搜尋。

某些搜尋體驗會強制實行篩選條件需求以作為實作的一部分，但是，當您想要使用「以值為基礎」的準則 (針對 "Simon & Schuster" 所發行的類別 "non-fiction"，將搜尋範圍設定為產品類型 "books") 來限制搜尋時，隨時都可使用篩選條件。

反之，如果您的目標是在特定資料「結構」上進行搜尋 (將搜尋範圍設定為客戶評論欄位)，則有替代方法，如下所述。

## <a name="when-to-use-a-filter"></a>使用篩選條件的時機

篩選條件是數個搜尋體驗的基礎，包括 "find near me"、多面向導覽和安全性篩選，以便只顯示允許使用者查看的文件。 如果您實作這其中任一個體驗，則篩選條件是必要的。 它是搜尋查詢附加的篩選條件，這類查詢可提供地理位置座標、使用者所選取的 Facet 類別或要求者的安全性識別碼。

範例案例包含下列項目：

1. 使用篩選條件，根據索引中的資料值來為索引配量。 假設有一個具有城市、房屋類型及便利設施的結構描述，您可能會建立一個篩選條件，明確選取滿足您準則 (位於西雅圖、公寓、濱水區) 的文件。 

   具備相同輸入的全文檢索搜尋通常會產生類似的結果，但篩選條件更為精確，因為它需要針對索引中的內容取得與篩選條件完全相符的項目。 

2. 如果搜尋體驗伴隨著篩選條件需求，請使用篩選條件：

   * [多面向導覽](search-faceted-navigation.md)會使用篩選條件，傳回使用者所選取的 Facet 類別。
   * 地理搜尋會使用篩選條件，在 "find near me" 應用程式中傳遞目前位置的座標。 
   * 安全性篩選條件會傳遞安全性識別碼作為篩選條件準則，索引中的相符項目則會作為用以存取文件權限的 Proxy。

3. 如果您想要數值欄位上的搜尋準則，請使用篩選條件。 

   數值欄位在文件中是可擷取且可出現在搜尋結果中，但不可單獨搜尋它們 (受限於全文檢索搜尋)。 如果您需要以數值資料為基礎的選取準則，請使用篩選條件。

### <a name="alternative-methods-for-reducing-scope"></a>縮減範圍的替代方法

如果您希望在搜尋結果中產生縮減效果，則篩選條件並非唯一的選擇。 視您的目標而定，這些替代選項可能更適合：

 + `searchFields` 查詢參數會將搜尋限制為特定欄位。 例如，如果您的索引分別提供適用於英文和西班牙文描述的欄位，您可以使用 searchFields，將目標設定為要用來進行全文檢索搜尋的欄位。 

+ `$select` 參數可用來指定要包含在結果集中的欄位，先有效地調整回應，然後將它傳送至呼叫應用程式。 This parameter does not refine the query or reduce the document collection, but if a smaller response is your goal, this parameter is an option to consider. 

如需其中任一個參數的詳細資訊，請參閱[搜尋文件 > 要求 > 查詢參數](https://docs.microsoft.com/rest/api/searchservice/search-documents#request) \(英文\)。


## <a name="how-filters-are-executed"></a>How filters are executed

At query time, a filter parser accepts criteria as input, converts the expression into atomic Boolean expressions represented as a tree, and then evaluates the filter tree over filterable fields in an index.

Filtering occurs in tandem with search, qualifying which documents to include in downstream processing for document retrieval and relevance scoring. When paired with a search string, the filter effectively reduces the recall set of the subsequent search operation. 單獨使用時 (例如，當查詢字串空白時，其中 `search=*`)，篩選條件準則會是唯一的輸入。 

## <a name="defining-filters"></a>定義篩選器
Filters are OData expressions, articulated using a [subset of OData V4 syntax supported in Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

You can specify one filter for each **search** operation, but the filter itself can include multiple fields, multiple criteria, and if you use an **ismatch** function, multiple full-text search expressions. In a multi-part filter expression, you can specify predicates in any order (subject to the rules of operator precedence). 如果您嘗試以特定順序重新整理述詞，則不會有任何顯著的效能改善。

One of the limits on a filter expression is the maximum size limit of the request. 整個要求 (包含篩選條件) 針對 POST 最多可有 16 MB，針對 GET 則是 8 KB。 There is also a limit on the number of clauses in your filter expression. 最佳經驗法則是，如果您有數百個子句，則必須承擔達到限制的風險。 我們建議以這類不會產生無大小限制之篩選條件的方式來設計您的應用程式。

下列範例會呈現數個 API 中的典型篩選條件定義。

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2019-05-06&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Filter usage patterns

The following examples illustrate several usage patterns for filter scenarios. 如需更多概念，請參閱 [OData 運算式語法 > 範例](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples) \(英文\) 。

+ 獨立的 **$filter** (不含查詢字串)，在篩選條件運算式能夠完全限定為感興趣的文件時很有用。 沒有查詢字串，就沒有語彙或語言分析、沒有計分且沒有排名。 Notice the search string is just an asterisk, which means "match all documents".

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ 查詢字串和 **$filter** 的組合，其中的篩選條件會建立子集，而查詢字串會提供字詞輸入，以便在篩選的子集上進行全文檢索搜尋。 The addition of terms (walking distance theaters) introduces search scores in the results, where documents that best match the terms are ranked higher. Using a filter with a query string is the most common usage pattern.

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ 複合式查詢 (以 "or" 分隔)，每個均有自己的篩選條件準則 (例如，'dog' 中的 'beagles' 或 'cat' 中的 'siamese')。 Expressions combined with `or` are evaluated individually, with the union of documents matching each expression sent back in the response. This usage pattern is achieved through the `search.ismatchscoring` function. You can also use the non-scoring version, `search.ismatch`.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  It is also possible to combine full-text search via `search.ismatchscoring` with filters using `and` instead of `or`, but this is functionally equivalent to using the `search` and `$filter` parameters in a search request. For example, the following two queries produce the same result:

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

繼續閱讀下列文章，以取得特定使用案例的完整指導方針：

+ [Facet 篩選條件](search-filters-facets.md)
+ [語言篩選條件](search-filters-language.md)
+ [安全性調整](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>進行篩選的欄位需求

In the REST API, filterable is *on* by default for simple fields. 可篩選的欄位會增加索引大小；請務必針對您不打算在篩選條件中實際使用的欄位設定 `"filterable": false`。 如需適用於欄位定義之設定的詳細資訊，請參閱[建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index) \(英文\)。

在 .NET SDK 中，可篩選預設為 *off*。 You can make a field filterable by setting the [IsFilterable property](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) of the corresponding [Field](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) object to `true`. You can also do this declaratively by using the [IsFilterable attribute](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). In the example below, the attribute is set on the `BaseRate` property of a model class that maps to the index definition.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Making an existing field filterable

You can't modify existing fields to make them filterable. Instead, you need to add a new field, or rebuild the index. For more information about rebuilding an index or repopulating fields, see [How to rebuild an Azure Cognitive Search index](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>文字篩選條件基本概念

Text filters match string fields against literal strings that you provide in the filter. Unlike full-text search, there is no lexical analysis or word-breaking for text filters, so comparisons are for exact matches only. For example, assume a field *f* contains "sunny day", `$filter=f eq 'Sunny'` does not match, but `$filter=f eq 'sunny day'` will. 

文字字串會區分大小寫。 There is no lower-casing of upper-cased words: `$filter=f eq 'Sunny day'` will not find "sunny day".

### <a name="approaches-for-filtering-on-text"></a>Approaches for filtering on text

| 方法 | 描述 | When to use |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | A function that matches a field against a delimited list of strings. | Recommended for [security filters](search-security-trimming-for-azure-search.md) and for any filters where many raw text values need to be matched with a string field. The **search.in** function is designed for speed and is much faster than explicitly comparing the field against each string using `eq` and `or`. | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | 此函式可讓您在相同的篩選條件運算式中，混用全文檢索搜尋作業以及純布林值篩選作業。 | Use **search.ismatch** (or its scoring equivalent, **search.ismatchscoring**) when you want multiple search-filter combinations in one request. 您也可以將它用於 *contains* 篩選條件，以篩選較大字串內的部分字串。 |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | 使用者定義的運算式，由欄位、運算子和值所組成。 | Use this when you want to find exact matches between a string field and a string value. |

## <a name="numeric-filter-fundamentals"></a>數值篩選條件基本概念

數值欄位在全文檢索搜尋的內容中不是 `searchable`。 只有字串才能進行全文檢索搜尋。 例如，如果您輸入 99.99 作為搜尋字詞，則將不會得到價格為 $ 99.99 美元的項目。 相反地，您會看到在文件的字串欄位中有數字 99 的項目。 因此，如果您有數值資料，則很可能會用於篩選條件，包括範圍、Facet、群組等。 

包含數值欄位 (價格、大小、SKU、識別碼) 的文件會在搜尋結果中提供那些值，但前提是已將該欄位標記為 `retrievable`。 此處的重點是全文檢索搜尋本身不適用於數值欄位類型。

## <a name="next-steps"></a>後續步驟

首先，嘗試使用入口網站中的 [搜尋總管]，提交含有 **$filter** 參數的查詢。 [不動產範例索引](search-get-started-portal.md)會在您將下列篩選查詢貼至搜尋列時提供它們的有趣結果：

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

若要使用更多範例，請參閱 [OData 篩選條件運算式語法 > 範例](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples) \(英文\)。

## <a name="see-also"></a>請參閱

+ [How full text search works in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [搜尋文件 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [簡單查詢語法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene 查詢語法](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [支援的資料類型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
