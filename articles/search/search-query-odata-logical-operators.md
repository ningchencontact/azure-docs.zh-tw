---
title: OData 邏輯運算子參考-Azure 搜尋服務
description: Azure 搜尋服務查詢中的 OData 邏輯運算子、and、or 和 not。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: bf4939a40a2fdf1c8fc6cf97beca0184b1604c98
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648003"
---
# <a name="odata-logical-operators-in-azure-search---and-or-not"></a>Azure 搜尋服務中的 OData 邏輯運算子`and`- `or`、、`not`

Azure 搜尋服務中的[OData 篩選條件運算式](query-odata-filter-orderby-syntax.md)是評估為`true`或`false`的布林運算式。 您可以撰寫一系列[較簡單](search-query-odata-comparison-operators.md)的篩選器, 並使用來自[布林代數](https://en.wikipedia.org/wiki/Boolean_algebra)的邏輯運算子來撰寫複雜的篩選

- `and`:二元運算子, 如果其左`true`和右子運算式評估為`true`, 則會評估為。
- `or`:二元運算子, 如果其中一個`true`左或右子運算式評估為`true`, 則評估為。
- `not`:一元運算子, 如果其子`true`運算式評估為`false`, 則會評估為, 反之亦然。

這些與[ `any`集合運算子和`all`](search-query-odata-collection-operators.md)結合, 可讓您建立可以表達非常複雜之搜尋準則的篩選器。

## <a name="syntax"></a>語法

下列 EBNF ([Extended 巴克斯-Backus-naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義使用邏輯運算子之 OData 運算式的文法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

也提供互動式語法圖:

> [!div class="nextstepaction"]
> [Azure 搜尋服務的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> 如需完整 EBNF 的 Azure 搜尋服務, 請參閱[OData 運算式語法參考](search-query-odata-syntax-reference.md)。

邏輯運算式有兩種`and`形式: binary (/ `or`), 其中有兩個子運算式和一元 (`not`), 其中只有一個。 子運算式可以是任何種類的布林運算式:

- 類型的欄位或範圍變數`Edm.Boolean`
- 傳回類型`Edm.Boolean`值的函式, `geo.intersects`例如或`search.ismatch`
- [比較運算式](search-query-odata-comparison-operators.md), 例如`rating gt 4`
- [集合運算式](search-query-odata-collection-operators.md), 例如`Rooms/any(room: room/Type eq 'Deluxe Room')`
- 布林常`true`值或`false`。
- 使用`and`、 `or`和所建造的其他邏輯運算式。`not`

> [!IMPORTANT]
> 在某些情況下`and` `or`, 並非所有類型的子運算式都可以搭配使用/, 特別是在 lambda 運算式中。 如需詳細資訊, 請參閱[Azure 搜尋服務中的 OData 集合運算子](search-query-odata-collection-operators.md#limitations)。

### <a name="logical-operators-and-null"></a>邏輯運算子和`null`

大部分的布林運算式 (例如函式和比較`null` ) 都無法產生值, 而且邏輯運算子無法直接`null`套用到常值 (例如`x and null` , 不允許)。 不過, 布林值欄位可以`null`是, 因此您必須留意`and`、 `or`和`not`運算子在存在 null 時的行為。 下表摘要說明這一點, 其中`b`是類型`Edm.Boolean`的欄位:

| 運算式 | 當為`b`時的結果`null` |
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

當布林值欄位`b`本身在篩選運算式中出現時, 其行為會如同已撰寫`b eq true`, 因此如果`b`為`null`, 則運算式會評估為`false`。 同樣地`not b` , 的`not (b eq true)`行為類似, 因此它`true`會評估為。 如此一來, `null`欄位的行為會與`false`相同。 這與使用`and`和`or`的其他運算式結合時的行為一致, 如上表所示。 儘管如此, 對`false` (`b eq false`) 的直接比較還是會評估為`false`。 換句話說, `null`不`false`等於, 即使它在布林運算式中的行為類似也一樣。

## <a name="examples"></a>範例

符合欄位介於 3 `rating`和5之間的檔 (包含:

    rating ge 3 and rating le 5

符合`ratings`欄位的所有元素小於3或大於5的檔:

    ratings/all(r: r lt 3 or r gt 5)

比對`location`欄位位於給定多邊形中的檔, 且檔不包含「公用」一詞。

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

將檔用於符合 deluxe 房間且低於160的飯店 (加拿大):

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>後續步驟  

- [Azure 搜尋服務中的篩選條件](search-filters.md)
- [Azure 搜尋服務的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋文件 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
