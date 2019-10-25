---
title: OData 邏輯運算子參考
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋查詢中的 OData 邏輯運算子、and、or 和 not。
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
ms.openlocfilehash: 4e016047d66e49f17c08d4b92a1c865f4b63e39b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793316"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Azure 認知搜尋中的 OData 邏輯運算子-`and`、`or`、`not`

Azure 認知搜尋中的[OData 篩選條件運算式](query-odata-filter-orderby-syntax.md)是評估為 `true` 或 `false`的布林運算式。 您可以撰寫一系列[較簡單](search-query-odata-comparison-operators.md)的篩選器，並使用來自[布林代數](https://en.wikipedia.org/wiki/Boolean_algebra)的邏輯運算子來撰寫複雜的篩選

- `and`：如果左和右子運算式評估為 `true`，則評估為 `true` 的二元運算子。
- `or`：如果其中一個左或右子運算式評估為 `true`，則會評估為 `true` 的二元運算子。
- `not`：評估為 `true` 的一元運算子（如果其子運算式評估為 `false`），反之亦然。

這些與[集合運算子一起 `any` 和 `all`](search-query-odata-collection-operators.md)，可讓您建立可以表達非常複雜搜尋準則的篩選器。

## <a name="syntax"></a>語法

下列 EBNF （[Extended 巴克斯-Backus-naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定義使用邏輯運算子之 OData 運算式的文法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

也提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> 如需完整的 EBNF，請參閱[Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)。

邏輯運算式有兩種形式： binary （`and`/`or`），其中有兩個子運算式和一元（`not`），其中只有一個。 子運算式可以是任何種類的布林運算式：

- `Edm.Boolean` 類型的欄位或範圍變數
- 傳回 `Edm.Boolean`類型值的函數，例如 `geo.intersects` 或 `search.ismatch`
- [比較運算式](search-query-odata-comparison-operators.md)，例如 `rating gt 4`
- [集合運算式](search-query-odata-collection-operators.md)，例如 `Rooms/any(room: room/Type eq 'Deluxe Room')`
- `true` 或 `false`的布林常值。
- 使用 `and`、`or`和 `not`所建造的其他邏輯運算式。

> [!IMPORTANT]
> 在某些情況下，並非所有子運算式都可以與 `and`/`or`搭配使用，特別是在 lambda 運算式中。 如需詳細資訊，請參閱[Azure 認知搜尋中的 OData 集合運算子](search-query-odata-collection-operators.md#limitations)。

### <a name="logical-operators-and-null"></a>邏輯運算子和 `null`

大部分的布林運算式（例如函式和比較）都無法產生 `null` 值，而邏輯運算子也不能直接套用至 `null` 常值（例如，不允許 `x and null`）。 不過，布林值欄位可以 `null`，因此您必須留意 `and`、`or`和 `not` 運算子在出現 null 時的行為方式。 下表摘要說明這一點，其中 `b` 是 `Edm.Boolean`類型的欄位：

| 運算是 | `null` `b` 時的結果 |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

當布林欄位 `b` 本身在篩選運算式中出現時，它的行為就如同已經 `b eq true`撰寫過，因此如果 `null``b`，運算式就會評估為 `false`。 同樣地，`not b` 的行為類似 `not (b eq true)`，因此會評估為 `true`。 如此一來，`null` 欄位與 `false`的行為相同。 這與使用 `and` 和 `or`的其他運算式結合時的行為一致，如上表所示。 這種情況下，與 `false` 的直接比較（`b eq false`）仍會評估為 `false`。 換句話說，`null` 不等於 `false`，即使它在布林運算式中的行為類似也一樣。

## <a name="examples"></a>範例

比對檔，其中 `rating` 欄位介於3到5（含）之間：

    rating ge 3 and rating le 5

比對檔，其中 `ratings` 欄位的所有元素都小於3或大於5：

    ratings/all(r: r lt 3 or r gt 5)

比對檔，其中 `location` 欄位位於指定的多邊形內，而檔未包含「公用」一詞。

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

將檔用於符合 deluxe 房間且低於160的飯店（加拿大）：

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔&#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
