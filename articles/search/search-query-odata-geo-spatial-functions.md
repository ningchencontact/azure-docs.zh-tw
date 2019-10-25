---
title: OData 地理空間函數參考
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋查詢中的 OData 地理空間函式、地理距離和地理交集。
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
ms.openlocfilehash: 09034423e16c652cf6994b38f8d92574abc0ce55
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793330"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Azure 認知搜尋中的 OData 地理空間函數-`geo.distance` 和 `geo.intersects`

Azure 認知搜尋可透過 `geo.distance` 和 `geo.intersects` 函數，在[OData 篩選運算式](query-odata-filter-orderby-syntax.md)中支援地理空間查詢。 `geo.distance` 函式會傳回兩個點之間的距離（以公里為單位），一個是欄位或範圍變數，另一個是做為篩選準則一部分傳遞的常數。 如果給定的點位於指定的多邊形內，而此點是欄位或範圍變數，且多邊形已指定為做為篩選準則一部分傳遞的常數，則 `geo.intersects` 函數會傳回 `true`。

`geo.distance` 函式也可以用在[ **$orderby**參數](search-query-odata-orderby.md)中，以從指定點的距離來排序搜尋結果。 `geo.distance` 在 **$orderby** 中的語法與其在 **$filter** 中的語法相同。 在 **$orderby**中使用 `geo.distance` 時，它所套用的欄位必須是 `Edm.GeographyPoint` 類型，而且也必須是可**排序**的。

> [!NOTE]
> 在 **$orderby**參數中使用 `geo.distance` 時，您傳遞給函式的欄位必須只包含單一的地理點。 換句話說，它的類型必須是 `Edm.GeographyPoint`，而不是 `Collection(Edm.GeographyPoint)`。 您不能在 Azure 認知搜尋中排序集合欄位。

## <a name="syntax"></a>語法

下列 EBNF （[延伸巴克斯-Backus-naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定義 `geo.distance` 和 `geo.intersects` 函式的文法，以及其運作所在的地理空間值：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

也提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> 如需完整的 EBNF，請參閱[Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)。

### <a name="geodistance"></a>地理距離

`geo.distance` 函式會接受 `Edm.GeographyPoint` 類型的兩個參數，並傳回 `Edm.Double` 值，也就是兩者之間的距離（以公里為間隔）。 這與支援 OData 地理空間作業的其他服務不同，這通常會傳回量表中的距離。

`geo.distance` 的其中一個參數必須是地理位置點常數，另一個則必須是欄位路徑（或者，篩選準則的範圍變數會反復查看類型 `Collection(Edm.GeographyPoint)`的欄位）。 這些參數的順序並不重要。

地理位置點常數的格式為 `geography'POINT(<longitude> <latitude>)'`，其中經度和緯度是數值常數。

> [!NOTE]
> 在篩選中使用 `geo.distance` 時，您必須使用 `lt`、`le`、`gt`或 `ge`，比較函式所傳回的距離與常數。 比較距離時不支援使用運算子 `eq` 和 `ne`。 例如，這是 `geo.distance`： `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`的正確使用方式。

### <a name="geointersects"></a>地理交集

`geo.intersects` 函式會採用類型 `Edm.GeographyPoint` 和常數 `Edm.GeographyPolygon` 的變數，並傳回 `Edm.Boolean` -- `true` 如果該點位於多邊形的界限內，則為，否則為 `false`。

多邊形是儲存為一連串點的二維介面，可定義周框環形（請參閱下列[範例](#examples)）。 多邊形必須是封閉的，也就是第一個和最後一個點集合必須相同。 [多邊形內的點必須採用逆時針順序](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>跨180度經線經線的地理空間查詢和多邊形

許多地理空間查詢程式庫所組成的查詢，包含180度經線經線（接近 dateline 附近）是關閉限制或需要因應措施，例如將多邊形分割成兩個，一個在經線的兩邊。

在「Azure 認知搜尋」中，如果查詢圖形是矩形，而且您的座標與經度和緯度的格線版面配置（例如 `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`）一致，則包含180度經度的地理空間查詢就會如預期般運作。 而如果是非矩形或未對齊的形狀，請考慮採用分割多邊形的方法。  

### <a name="geo-spatial-functions-and-null"></a>地理空間函數和 `null`

就像 Azure 認知搜尋中的所有其他非集合欄位一樣，`Edm.GeographyPoint` 類型的欄位可以包含 `null` 值。 當 Azure 認知搜尋評估 `null`的欄位 `geo.intersects` 時，將一律會 `false`結果。 在此情況下，`geo.distance` 的行為取決於內容：

- 在 [篩選] 中，`null` 欄位的 `geo.distance` 會導致 `null`。 這表示檔將不會相符，因為與任何非 null 值相較之下，`null` 會評估為 `false`。
- 使用 **$orderby**排序結果時，`null` 欄位 `geo.distance` 會導致最大可能距離。 當使用排序方向 `asc` （預設值）時，具有這類欄位的檔會比其他專案排序，而當方向為 `desc`時，則會比其他人更高。

## <a name="examples"></a>範例

### <a name="filter-examples"></a>篩選範例

尋找在指定參考點的10公里內的所有旅館（其中 location 是 `Edm.GeographyPoint`類型的欄位）：

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

尋找指定的範圍內的所有旅館，以多邊形的形式描述（其中 location 是 `Edm.GeographyPoint`類型的欄位）。 請注意，多邊形必須是封閉的 (第一個和最後一個點集合必須相同)，且[點必須以逆時針順序列出](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Order-by 範例

依 `rating`，然後依指定座標的距離昇冪來排序旅館：

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

以遞減的順序依 `search.score` 和 `rating`排序飯店，然後依照指定座標的遞增順序，讓兩個旅館具有相同的評等，最接近的飯店會最先列出：

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>後續步驟  

- [Azure 認知搜尋中的篩選](search-filters.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔&#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
