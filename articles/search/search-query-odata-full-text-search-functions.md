---
title: OData 全文檢索搜尋函式參考-Azure 搜尋服務
description: OData 全文檢索搜尋函式、 search.ismatch 和 search.ismatchscoring，在 Azure 搜尋服務查詢中。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
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
ms.openlocfilehash: 158312a7afe88e7b9885376c5d28b01958acbbfb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079804"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>OData 全文檢索搜尋函式，在 Azure 搜尋服務-`search.ismatch`和 `search.ismatchscoring`

Azure 搜尋服務支援的內容中的全文檢索搜尋[OData 篩選條件運算式](query-odata-filter-orderby-syntax.md)透過`search.ismatch`和`search.ismatchscoring`函式。 這些函數可讓您只要使用最上層不可行的方式嚴格布林篩選時，結合全文檢索搜尋`search`的參數[搜尋 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)。

> [!NOTE]
> `search.ismatch`並`search.ismatchscoring`中的篩選器只支援函數[搜尋 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)。 中不支援[建議](https://docs.microsoft.com/rest/api/searchservice/suggestions)或是[自動完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete)Api。

## <a name="syntax"></a>語法

下列 EBNF ([擴充式 Backus-naur 形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義的文法`search.ismatch`和`search.ismatchscoring`函式：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

互動式的語法圖表也會提供：

> [!div class="nextstepaction"]
> [Azure 搜尋服務的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> 請參閱[Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)的完整 EBNF。

### <a name="searchismatch"></a>search.ismatch

`search.ismatch`函式評估的全文檢索搜尋查詢篩選條件運算式的一部分。 符合搜尋查詢的文件將會在結果集中傳回。 此函式有下列多載可供使用：

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

下表中定義的參數：

| 參數名稱 | 類型 | 描述 |
| --- | --- | --- |
| `search` | `Edm.String` | 搜尋查詢 (在[簡單](query-simple-syntax.md)或是[完整](query-lucene-syntax.md)Lucene 查詢語法)。 |
| `searchFields` | `Edm.String` | 以逗號分隔清單中的可搜尋欄位中，搜尋若要在索引中的所有可搜尋欄位的預設值。 使用時[潮流搜尋](query-lucene-syntax.md#bkmk_fields)在`search`參數，Lucene 查詢中的欄位指定名稱的覆寫此參數中指定任何欄位。 |
| `queryType` | `Edm.String` | `'simple'` 或是`'full'`; 預設為`'simple'`。 指定 `search` 參數中使用的查詢語言。 |
| `searchMode` | `Edm.String` | `'any'` 或是`'all'`，預設為`'any'`。 指出是否任何或所有搜尋的搜尋都文字中`search`參數必須符合才能將文件視為相符項目。 使用時[Lucene 布林運算子](query-lucene-syntax.md#bkmk_boolean)在`search`參數，它們將會優先於此參數。 |

所有上述參數相當於對應[Search API 中搜尋要求參數](https://docs.microsoft.com/rest/api/searchservice/search-documents)。

`search.ismatch`函式傳回值的型別`Edm.Boolean`，可讓您撰寫與使用布林的其他篩選子運算式[邏輯運算子](search-query-odata-logical-operators.md)。

> [!NOTE]
> Azure 搜尋服務不支援使用`search.ismatch`或`search.ismatchscoring`lambda 運算式內。 這表示不可能透過可以相互關聯的嚴格的篩選相符項目相同的物件上的全文檢索搜尋相符的物件集合的寫入篩選器。 如需這項限制，以及範例的詳細資訊，請參閱 <<c0> [ 疑難排解 Azure 搜尋服務中的集合篩選](search-query-troubleshoot-collection-filters.md)。 如需深入了解為何有這項限制，請參閱 <<c0> [ 了解 Azure 搜尋服務中的集合篩選](search-query-understand-collection-filters.md)。


### <a name="searchismatchscoring"></a>search.ismatchscoring

`search.ismatchscoring`函數跟`search.ismatch`函式會傳回`true`符合全文檢索搜尋查詢的文件，做為參數傳遞。 兩者的差別在於，符合 `search.ismatchscoring` 查詢的文件所得到的相關性分數將對整體的文件分數產生貢獻，而 `search.ismatch` 則不會變更文件分數。 此函式的下列多載可與等同於 `search.ismatch` 參數的參數搭配使用：

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

同時`search.ismatch`和`search.ismatchscoring`函式可以用於相同的篩選條件運算式。

## <a name="examples"></a>範例

尋找含有「海濱」一詞的文件。 此篩選查詢等同於使用 `search=waterfront` 的[搜尋要求](https://docs.microsoft.com/rest/api/searchservice/search-documents)。

    search.ismatchscoring('waterfront')

尋找含有「青年旅館」一詞、且評分為 4 或以上的文件，或含有「汽車旅館」一詞、且評分為 5 的文件。 請注意，此要求必須使用 `search.ismatchscoring` 函式來表示。

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

尋找不含「豪華」一詞的文件。

    not search.ismatch('luxury')

尋找含有「海景」一詞或評分為 5 的文件。 `search.ismatchscoring` 查詢只會對欄位 `HotelName` 和 `Rooms/Description` 執行。

比對分離的第二個子句的文件將會傳回 – 旅館`Rating`等於 5。 若要讓它清除，這些文件不符合任何運算式的評分的組件，它們就會傳回等於 0 的分數。

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

尋找文件的條款"hotel"和 「 機場 」 彼此的旅館，描述中的 5 個字內的位置，以及其中吸煙中不允許至少部分聊天室。 此查詢會使用[完整 Lucene 查詢語言](query-lucene-syntax.md)。

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>後續步驟  

- [Azure 搜尋服務中的篩選條件](search-filters.md)
- [Azure 搜尋服務的 OData 運算式語言概觀](query-odata-filter-orderby-syntax.md)
- [Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋文件 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
