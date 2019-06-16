---
title: OData 比較運算子參考-Azure 搜尋服務
description: OData 比較運算子、 eq、 ne、 gt、 lt、 ge 及 le、 在 Azure 搜尋服務查詢中。
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
ms.openlocfilehash: b51bf3d77283ae828f47fdb0355d2deb43f071a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079921"
---
# <a name="odata-comparison-operators-in-azure-search---eq-ne-gt-lt-ge-and-le"></a>Azure 搜尋服務層中的 OData 比較運算子`eq`， `ne`， `gt`， `lt`， `ge`，及 `le`

中的最基本的作業[OData 篩選運算式](query-odata-filter-orderby-syntax.md)在 Azure 搜尋服務是比較指定的數值欄位。 可能的會有兩種類型的比較相等比較，以及範圍比較。 要比較的常數值的欄位，您可以使用下列運算子：

等號比較運算子：

- `eq`:測試欄位是否**等於**常數值
- `ne`:測試欄位是否**不等於**常數值

範圍運算子：

- `gt`:測試欄位是否**大於**常數值
- `lt`:測試欄位是否**小於**常數值
- `ge`:測試欄位是否**大於或等於**常數值
- `le`:測試欄位是否**小於或等於**常數值

您可以使用範圍運算子結合[邏輯運算子](search-query-odata-logical-operators.md)來測試是否為特定值範圍內的欄位。 請參閱[範例](#examples)本文稍後。

> [!NOTE]
> 如果想要的話，您可以將常數值放運算子和右邊的欄位名稱的左邊。 範圍運算子，會反轉比較的意義。 例如，如果常數的值是在左側`gt`會測試是否大於該欄位的常數值。 您也可以使用比較運算子來比較函式的結果，例如`geo.distance`，值。 布林值的函式，如`search.ismatch`，比較結果`true`或`false`是選擇性的。

## <a name="syntax"></a>語法

下列 EBNF ([擴充式 Backus-naur 形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義的使用比較運算子的 OData 運算式文法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

互動式的語法圖表也會提供：

> [!div class="nextstepaction"]
> [Azure 搜尋服務的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> 請參閱[Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)的完整 EBNF。

有兩種形式的比較運算式。 兩者之間唯一的差別是運算子的常數是否出現在左邊-或右邊右手邊。 必須是運算子的運算式的另一端**變數**或函式呼叫。 變數可以是欄位名稱或範圍變數的情況[lambda 運算式](search-query-odata-collection-operators.md)。

## <a name="data-types-for-comparisons"></a>比較的資料類型

比較運算子的兩邊的資料類型必須相容。 例如，如果左邊的欄位型別的`Edm.DateTimeOffset`，則右側必須是日期時間常數。 數值資料類型會更有彈性。 下表中所述，您可以比較變數和常數的任何其他數值類型，但有一些限制，任何數值類型的函數。

| 變數或函式的類型 | 常數的實值型別 | 限制 |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | 比較受限於[特殊規則 `NaN`](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | 常數會轉換成`Edm.Double`，因而導致失去精確度的大範圍值 |
| `Edm.Double` | `Edm.Int32` | n/a |
| `Edm.Int64` | `Edm.Double` | 若要比較`NaN`， `-INF`，或`INF`不允許 |
| `Edm.Int64` | `Edm.Int64` | n/a |
| `Edm.Int64` | `Edm.Int32` | 常數會轉換成`Edm.Int64`比較之前 |
| `Edm.Int32` | `Edm.Double` | 若要比較`NaN`， `-INF`，或`INF`不允許 |
| `Edm.Int32` | `Edm.Int64` | n/a |
| `Edm.Int32` | `Edm.Int32` | n/a |

不允許，例如比較類型的欄位的比較`Edm.Int64`至`NaN`，Azure 搜尋服務 REST API 會傳回 「 HTTP 400:錯誤的要求 」 錯誤。

> [!IMPORTANT]
> 即使數值類型的比較有彈性，但我們強烈建議在篩選條件中撰寫比較，以便屬於相同的資料類型的變數或函式，它所比較的常數值。 這點特別重要的時機混合浮點和整數值，可能會遺失有效位數的隱含轉換所在位置。

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>特殊情況下，針對`null`和 `NaN`

使用比較運算子，它時，務必記得，可能會在 Azure 搜尋服務中的所有非集合欄位`null`。 下表顯示所有可能的結果比較運算式中會有任一端`null`:

| 運算子 | 只有欄位或變數時的結果 `null` | 只有常數時的結果 `null` | 當欄位或變數和常數的結果 `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400:不正確的要求時發生錯誤 | HTTP 400:不正確的要求時發生錯誤 |
| `lt` | `false` | HTTP 400:不正確的要求時發生錯誤 | HTTP 400:不正確的要求時發生錯誤 |
| `ge` | `false` | HTTP 400:不正確的要求時發生錯誤 | HTTP 400:不正確的要求時發生錯誤 |
| `le` | `false` | HTTP 400:不正確的要求時發生錯誤 | HTTP 400:不正確的要求時發生錯誤 |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

在 [摘要]`null`等於只有本身，而且不能小於或大於任何其他值。

如果您的索引具有類型的欄位`Edm.Double`和您上傳`NaN`給這些欄位的值，您必須撰寫篩選器時，該帳戶。 Azure 搜尋服務會實作處理的 IEEE 754 標準`NaN`值，以及這類值的比較產生不明顯的結果下, 表所示。

| 運算子 | 當至少一個運算元的結果 `NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

在 [摘要]`NaN`不等於任何值，包括本身。

### <a name="comparing-geo-spatial-data"></a>比較的地理空間資料

您無法直接比較類型的欄位`Edm.GeographyPoint`常數的值，但您可以使用`geo.distance`函式。 此函式傳回值的型別`Edm.Double`，因此您可以比較它與數值常數，以篩選根據常數的地理空間座標之間的距離。 請參閱[範例](#examples)如下。

### <a name="comparing-string-data"></a>比較字串資料

可以在篩選中比較字串，使用完全相符`eq`和`ne`運算子。 這些比較會區分大小寫。

## <a name="examples"></a>範例

比對文件的位置`Rating`是介於 3 到 5 （含) 之間的欄位：

    Rating ge 3 and Rating le 5

比對文件的位置`Location`是小於 2 公里從指定的緯度和經度欄位：

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

比對文件的位置`LastRenovationDate`欄位是大於或等於 2015 年 1 月 1 日 UTC 午夜：

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

比對文件何處`Details/Sku`欄位不是`null`:

    Details/Sku ne null

比對其中至少一個房間具有類型 「 豪華空間 」，其中的旅館的文件的字串`Rooms/Type`欄位完全符合篩選條件：

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>後續步驟  

- [Azure 搜尋服務中的篩選條件](search-filters.md)
- [Azure 搜尋服務的 OData 運算式語言概觀](query-odata-filter-orderby-syntax.md)
- [Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋文件 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
