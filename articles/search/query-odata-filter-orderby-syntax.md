---
title: OData 語言概觀-Azure 搜尋服務
description: OData 語言概觀篩選中，選取和排序依據的 Azure 搜尋服務查詢。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 166c23088fe0388199ca51efde05153bb5697e38
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063692"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-search"></a>適用於 OData 語言概觀`$filter`， `$orderby`，和`$select`在 Azure 搜尋服務

Azure 搜尋服務支援的 OData 運算式語法的子集 **$filter**， **$orderby**，並 **$select**運算式。 在進行查詢剖析、將搜尋限定於特定欄位，或在索引掃描期間新增比對準則時，會評估篩選運算式。 Order by 運算式會透過結果集，來排序所傳回的文件套用後置處理步驟。 Select 運算式決定哪些文件欄位會包含在結果集。 這些運算式的語法會區別[簡單](query-simple-syntax.md)或[完整](query-lucene-syntax.md)查詢中使用的語法**搜尋**參數，雖然有部分重疊的語法參考的欄位。

這篇文章提供篩選、 order by 和 select 運算式中使用的 OData 運算式語言的概觀。 「 由下往上 「、 開始的最基本的項目，並在其上建置，則會顯示語言。 每個參數的最上層的語法是由個別的文章所述：

- [$filter 語法](search-query-odata-filter.md)
- [$orderby syntax](search-query-odata-orderby.md)
- [$select syntax](search-query-odata-select.md)

OData 運算式範圍從簡單到非常複雜，但他們全部共用通用項目。 Azure 搜尋服務中的 OData 運算式的最基本的組件如下：

- **欄位路徑**，這是指特定欄位的索引。
- **常數**，這是特定資料類型的常值。

> [!NOTE]
> Azure 搜尋服務中的術語有別[OData 標準](https://www.odata.org/documentation/)以數種方式。 我們所謂**欄位**在 Azure 搜尋服務中呼叫**屬性**在 OData 中，以此類推**欄位路徑**與**屬性路徑**。 **Index**包含**文件**Azure 搜尋服務中稱為更廣泛做為 OData**實體集**包含**實體**。 Azure 搜尋服務的術語整份參考手冊。

## <a name="field-paths"></a>欄位路徑

下列 EBNF ([擴充式 Backus-naur 形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義的欄位路徑的文法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

互動式的語法圖表也會提供：

> [!div class="nextstepaction"]
> [Azure 搜尋服務的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> 請參閱[Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)的完整 EBNF。

欄位路徑組成一或多個**識別碼**以斜線分隔。 每個識別項是必須以 ASCII 字母或底線開頭，並只能包含 ASCII 字母、 數字或底線的字元序列。 上方或小寫，可以是字母。

識別項可以參考到名稱 欄位中，或以**範圍變數**的內容中[集合運算式](search-query-odata-collection-operators.md)(`any`或`all`) 篩選器中。 範圍變數就像代表目前的項目集合的迴圈變數。 對於複雜的集合，該變數代表物件，這就是為什麼您可以使用 欄位路徑來參考變數的子欄位。 這是類似於許多程式設計語言中的點標記法。

下表顯示欄位路徑範例：

| 欄位路徑 | 描述 |
| --- | --- |
| `HotelName` | 索引的最上層欄位參考 |
| `Address/City` | 是指`City`子欄位的索引; 中的複雜欄位`Address`別的`Edm.ComplexType`在此範例 |
| `Rooms/Type` | 是指`Type`子欄位的複雜集合索引中的欄位;`Rooms`別的`Collection(Edm.ComplexType)`在此範例 |
| `Stores/Address/Country` | 是指`Country`的子欄位`Address`子欄位的複雜集合索引中的欄位;`Stores`屬於型別`Collection(Edm.ComplexType)`並`Address`屬於類型`Edm.ComplexType`在此範例 |
| `room/Type` | 是指`Type`的子欄位`room`範圍變數，例如在篩選條件運算式 `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | 是指`Country`的子欄位`Address`子欄位`store`範圍變數，例如在篩選條件運算式 `Stores/any(store: store/Address/Country eq 'Canada')` |

欄位路徑的意義會因內容而有所不同。 值，在篩選器，參考欄位路徑*單一執行個體*目前文件中的欄位。 在其他內容中，這類 **$orderby**， **$select**，或在[完整的 Lucene 語法中加入欄位的搜尋](query-lucene-syntax.md#bkmk_fields)，欄位路徑是指欄位本身。 這項差異會有一些如您在篩選中使用欄位路徑的後果。

欄位路徑，請考慮`Address/City`。 在篩選中，這是指目前的文件，例如"San Francisco"單一縣 （市）。 相反地，`Rooms/Type`是指`Type`子欄位 （例如 「 標準 」 的第一個房間、"豪華 」 的第二個房間，依此類推） 的許多聊天室。 由於`Rooms/Type`未參考*單一執行個體*的子欄位`Type`，它不能直接在篩選中。 相反地，若要篩選的空間型別，您會使用[lambda 運算式](search-query-odata-collection-operators.md)與範圍變數，就像這樣：

    Rooms/any(room: room/Type eq 'deluxe')

在此範例中，範圍變數`room`會出現在`room/Type`欄位路徑。 如此一來，`room/Type`參考目前文件中目前的聊天室的型別。 這是單一執行個體`Type`子欄位，所以可以直接在篩選中使用。

### <a name="using-field-paths"></a>使用欄位路徑

欄位路徑用於許多參數方面[Azure 搜尋服務 API](https://docs.microsoft.com/rest/api/searchservice/)。 下表列出所有位置中可以使用這些，再加上任何限制，其使用方式：

| API | 參數名稱 | 限制 |
| --- | --- | --- |
| [建立](https://docs.microsoft.com/rest/api/searchservice/create-index)或是[更新](https://docs.microsoft.com/rest/api/searchservice/update-index)索引 | `suggesters/sourceFields` | None |
| [建立](https://docs.microsoft.com/rest/api/searchservice/create-index)或是[更新](https://docs.microsoft.com/rest/api/searchservice/update-index)索引 | `scoringProfiles/text/weights` | 只能參考**可搜尋**欄位 |
| [建立](https://docs.microsoft.com/rest/api/searchservice/create-index)或是[更新](https://docs.microsoft.com/rest/api/searchservice/update-index)索引 | `scoringProfiles/functions/fieldName` | 只能參考**filterable**欄位 |
| [搜尋](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search` 當`queryType`是 `full` | 只能參考**可搜尋**欄位 |
| [搜尋](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | 只能參考**facetable**欄位 |
| [搜尋](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | 只能參考**可搜尋**欄位 |
| [搜尋](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | 只能參考**可搜尋**欄位 |
| [建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)和[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | 只能參考到欄位屬於[建議工具](index-add-suggesters.md) |
| [搜尋](https://docs.microsoft.com/rest/api/searchservice/search-documents)，[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)，和[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | 只能參考**filterable**欄位 |
| [搜尋](https://docs.microsoft.com/rest/api/searchservice/search-documents)和[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | 只能參考**排序**欄位 |
| [搜尋](https://docs.microsoft.com/rest/api/searchservice/search-documents)，[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)，和[查閱](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | 只能參考**可擷取**欄位 |

## <a name="constants"></a>常數

在 OData 中的常數是常值的指定[Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM) 型別。 請參閱[支援的資料類型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)取得一份 Azure 搜尋服務中支援的類型。 不支援集合類型的常數。

下表顯示每個 Azure 搜尋服務所支援的資料類型常數的範例：

| 数据类型 | 範例常數 |
| --- | --- |
| `Edm.Boolean` | `true`、 `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`、 `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

下列 EBNF ([擴充式 Backus-naur 形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義大部分的上述表格所示的常數的文法。 可以位於地理空間類型的文法[Azure 搜尋服務中的 OData 地理空間函式](search-query-odata-geo-spatial-functions.md)。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

互動式的語法圖表也會提供：

> [!div class="nextstepaction"]
> [Azure 搜尋服務的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> 請參閱[Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)的完整 EBNF。

## <a name="building-expressions-from-field-paths-and-constants"></a>建置運算式，從 欄位路徑和常數

欄位路徑和常數的最基本的部分的 OData 運算式，但它們已完整運算式本身。 事實上， **$select** Azure 搜尋服務中的參數是以逗號分隔的欄位路徑清單，以及 **$orderby**不是較為複雜，比 **$select**。 如果您剛好有型別的欄位`Edm.Boolean`在索引中，您甚至可以撰寫該欄位的路徑只是篩選。 常數`true`和`false`同樣是有效的篩選。

不過，大部分時間您將需要更複雜的運算式，將多個欄位和常數。 這些運算式是以不同的方式，根據參數來建置。

下列 EBNF ([擴充式 Backus-naur 形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義的文法 **$filter**， **$orderby**，以及 **$select**參數。 這些是建立從更簡單的運算式參考欄位的路徑和常數：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

互動式的語法圖表也會提供：

> [!div class="nextstepaction"]
> [Azure 搜尋服務的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> 請參閱[Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)的完整 EBNF。

**$Orderby**並 **$select**參數是這兩個以逗號分隔的清單較簡單的運算式。 **$Filter**參數是一個布林值運算式所組成的較簡單的子運算式。 這些子運算式會結合使用邏輯運算子，例如[ `and`， `or`，以及`not` ](search-query-odata-logical-operators.md)，比較運算子，例如[ `eq`， `lt`， `gt`等等](search-query-odata-comparison-operators.md)，和集合運算子，例如[`any`並`all` ](search-query-odata-collection-operators.md)。

**$Filter**， **$orderby**，並 **$select**參數會探索下列文章中詳述：

- [Azure 搜尋服務中的 OData $filter 語法](search-query-odata-filter.md)
- [Azure 搜尋服務中的 OData $orderby 語法](search-query-odata-orderby.md)
- [Azure 搜尋服務中的 OData $select 語法](search-query-odata-select.md)

## <a name="see-also"></a>請參閱  

- [Azure 搜尋服務中的多面向導覽](search-faceted-navigation.md)
- [Azure 搜尋服務中的篩選條件](search-filters.md)
- [搜尋文件 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Lucene 查詢語法](query-lucene-syntax.md)
- [Azure 搜尋服務中的簡單查詢語法](query-simple-syntax.md)
