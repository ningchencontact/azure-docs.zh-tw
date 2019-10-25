---
title: OData search.in 函數參考
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋查詢中的 OData search.in 函數。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: f641e50554e720d273735fd20032e60444cb198a
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793288"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>Azure 認知搜尋中的 OData `search.in` 函式

[OData 篩選條件運算式](query-odata-filter-orderby-syntax.md)中的常見案例是檢查每個檔中的單一欄位是否等於許多可能值的其中一個。 例如，這是某些應用程式如何執行[安全性](search-security-trimming-for-azure-search.md)調整--藉由檢查包含一或多個主體識別碼的欄位，針對代表發出查詢之使用者的主體識別碼清單。 撰寫這類查詢的其中一種方式，就是使用[`eq`](search-query-odata-comparison-operators.md)和[`or`](search-query-odata-logical-operators.md)運算子：

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

不過，使用 `search.in` 函式，有較簡單的方式可以撰寫此功能：

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> 除了較短且更容易閱讀之外，使用 `search.in` 也會提供[效能優勢](#bkmk_performance)，並在篩選器中包含數百個或甚至數千個值時，避免[篩選準則的某些大小限制](search-query-odata-filter.md#bkmk_limits)。 基於這個理由，我們強烈建議使用 `search.in`，而不是較複雜的等號比較運算式。

> [!NOTE]
> 4\.01 版的 OData 標準最近引進了[`in` 運算子](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230)，其行為與 Azure 認知搜尋中的 `search.in` 函數類似。 不過，Azure 認知搜尋不支援此運算子，因此您必須改為使用 `search.in` 函數。

## <a name="syntax"></a>語法

下列 EBNF （[Extended 巴克斯-Backus-naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定義 `search.in` 函數的文法：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

也提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> 如需完整的 EBNF，請參閱[Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)。

`search.in` 函式會測試指定的字串欄位或範圍變數是否等於指定的值清單中的其中一個。 變數與清單中每個值之間的相等是以區分大小寫的方式來決定，這與 `eq` 運算子的方式相同。 因此，像 `search.in(myfield, 'a, b, c')` 這樣的運算式會等同於 `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`，差別在於 `search.in` 所產生的效果會好得多。

`search.in` 函式有兩個多載：

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

參數定義于下表中：

| 參數名稱 | Type | 描述 |
| --- | --- | --- |
| `variable` | `Edm.String` | 字串欄位參考（或字串集合欄位的範圍變數，在 `any` 或 `all` 運算式中使用 `search.in` 的情況下）。 |
| `valueList` | `Edm.String` | 字串，包含要比對 `variable` 參數之值的分隔清單。 如果未指定 `delimiters` 參數，則預設的分隔符號為空格和逗號。 |
| `delimiters` | `Edm.String` | 字串，在剖析 `valueList` 參數時，每個字元都會被視為分隔符號。 這個參數的預設值是 `' ,'`，這表示它們之間有空格和（或）逗號的任何值都將被分隔。 如果您需要使用空格和逗號以外的分隔符號，因為您的值包含這些字元，您可以在此參數中指定替代的分隔符號，例如 `'|'`。 |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>`search.in` 的效能

如果您使用 `search.in`，當第二個參數所含的清單有數百或數千個值時，預期的回應時間應該不到一秒。 雖然您仍然受到最大要求大小的限制，但您可以傳遞給 `search.in`的專案數沒有明確限制。 不過，當值的數目增加時，延遲也會隨之增長。

## <a name="examples"></a>範例

尋找名稱等於「海洋 View motel」或「預算飯店」的所有旅館。 片語包含空格，這是預設的分隔符號。 您可以使用單引號來指定替代分隔符號，做為第三個字串參數：  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

尋找名稱等於「海運視圖 motel」或「預算飯店」（以 ' | ' 分隔）的所有旅館：

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

尋找具有標記 ' wifi ' 或 ' 浴盆 ' 之會議室的所有旅館：

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

尋找集合中片語的相符項，例如標記中的「加熱式的紙巾機架」或「hairdryer 包含」。

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

尋找沒有標記 ' motel ' 或 ' cabin' 的所有旅館：

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔&#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
