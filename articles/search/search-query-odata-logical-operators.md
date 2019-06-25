---
title: OData 的邏輯運算子參考-Azure 搜尋服務
description: OData 邏輯運算子，或者，不是，在 Azure 搜尋服務查詢。
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
ms.openlocfilehash: de93765117b4cafe70e5ad277e32ca0a1fa8d90a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079765"
---
# <a name="odata-logical-operators-in-azure-search---and-or-not"></a>在 Azure 搜尋服務-OData 的邏輯運算子`and`， `or`， `not`

[OData 篩選條件運算式](query-odata-filter-orderby-syntax.md)在 Azure 搜尋服務會評估為布林運算式`true`或`false`。 您可以透過撰寫一系列的撰寫複雜的篩選條件[更簡單的篩選條件](search-query-odata-comparison-operators.md)及使用從的邏輯運算子加以組合[布林值代數](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`:二元運算子會評估結果`true`如果兩個其左側和右側子運算式評估為`true`。
- `or`:二元運算子會評估結果`true`如果其左邊或右邊的子運算式的其中一個評估為`true`。
- `not`:一元運算子來評估`true`如果其子運算式評估為`false`，反之亦然。

這些項目，連同[集合運算子`any`並`all` ](search-query-odata-collection-operators.md)，讓您建構可以用來表示非常複雜的搜尋條件的篩選條件。

## <a name="syntax"></a>語法

下列 EBNF ([擴充式 Backus-naur 形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義的使用邏輯運算子的 OData 運算式文法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

互動式的語法圖表也會提供：

> [!div class="nextstepaction"]
> [Azure 搜尋服務的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> 請參閱[Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)的完整 EBNF。

有兩種形式的邏輯運算式： 二進位 (`and`/`or`)，其中有兩個的子運算式和一元 (`not`)，其中只有一個。 子運算式可以是任何種類的布林運算式：

- 類型的欄位或範圍變數 `Edm.Boolean`
- 傳回類型的值的函式`Edm.Boolean`，例如`geo.intersects`或 `search.ismatch`
- [比較運算式](search-query-odata-comparison-operators.md)，例如 `rating gt 4`
- [集合運算式](search-query-odata-collection-operators.md)，例如 `Rooms/any(room: room/Type eq 'Deluxe Room')`
- 布林常值`true`或`false`。
- 使用建構其他邏輯運算式`and`， `or`，和`not`。

> [!IMPORTANT]
> 有一些情況下，並非所有的子運算式的類型可以搭配`and` / `or`，特別是內部 lambda 運算式。 請參閱[Azure 搜尋服務中的 OData 集合運算子](search-query-odata-collection-operators.md#limitations)如需詳細資訊。

### <a name="logical-operators-and-null"></a>邏輯運算子和 `null`

無法產生大部分的布林運算式，例如函式和比較`null`值和邏輯運算子無法套用至`null`直接常值 (例如`x and null`不允許)。 不過，布林值欄位可`null`，因此您必須要了解`and`， `or`，和`not`運算子的行為有 null。 這摘要在下列資料表中，其中`b`是型別的欄位`Edm.Boolean`:

| 運算是 | 結果時`b`是 `null` |
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

當 布林值欄位`b`將其單獨出現在篩選運算式中中, 其行為就如同它已寫入`b eq true`，因此，如果`b`是`null`，運算式評估為`false`。 同樣地，`not b`行為類似`not (b eq true)`，因此其評估結果為`true`。 如此一來，`null`欄位的行為與相同`false`。 這是與使用其他運算式合併使用時的行為一致`and`和`or`，如上表所示。 儘管如此，直接比較來`false`(`b eq false`) 仍會評估為`false`。 亦即`null`不等於`false`，即使它的行為類似它布林運算式中。

## <a name="examples"></a>範例

比對文件的位置`rating`是介於 3 到 5 （含) 之間的欄位：

    rating ge 3 and rating le 5

比對文件位置的所有項目`ratings`欄位會少於 3 或大於 5:

    ratings/all(r: r lt 3 or r gt 5)

比對文件的位置`location`欄位是在給定的多邊形內，而且文件不包含詞彙 「 公用 」。

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

比對文件中加拿大溫哥華的旅館，具有基底的豪華挪出空間速率小於 160 時：

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>後續步驟  

- [Azure 搜尋服務中的篩選條件](search-filters.md)
- [Azure 搜尋服務的 OData 運算式語言概觀](query-odata-filter-orderby-syntax.md)
- [Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋文件 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
