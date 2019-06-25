---
title: OData 地理空間函式參考-Azure 搜尋服務
description: OData 地理空間函式，geo.distance 和 geo.intersects，在 Azure 搜尋服務查詢中。
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
ms.openlocfilehash: 0ce63ab1143c784eb3e10f47c20ef2b5034d63a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079791"
---
# <a name="odata-geo-spatial-functions-in-azure-search---geodistance-and-geointersects"></a>OData 地理空間函式，在 Azure 搜尋服務-`geo.distance`和 `geo.intersects`

Azure 搜尋服務支援中的地理空間查詢[OData 篩選條件運算式](query-odata-filter-orderby-syntax.md)透過`geo.distance`和`geo.intersects`函式。 `geo.distance`函式傳回以公里為單位的兩個點之間的距離，其中有一個是欄位或範圍變數和常數的其中一個參數傳遞做為篩選條件的一部分。 `geo.intersects`函式會傳回`true`如果給的定點位於給定的多邊形，其中的點是欄位或範圍的變數，而多邊形會指定為常數，當做篩選條件一部分傳遞。

`geo.distance`函式也可用在[ **$orderby**參數](search-query-odata-orderby.md)來排序搜尋結果，以從指定的點的距離。 `geo.distance` 在 **$orderby** 中的語法與其在 **$filter** 中的語法相同。 使用時`geo.distance`中 **$orderby**，要套用此欄位必須是型別的`Edm.GeographyPoint`也必須是**可排序**。

## <a name="syntax"></a>語法

下列 EBNF ([擴充式 Backus-naur 形式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義的文法`geo.distance`和`geo.intersects`函式，以及其運作所在的地理空間值：

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

互動式的語法圖表也會提供：

> [!div class="nextstepaction"]
> [Azure 搜尋服務的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> 請參閱[Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)的完整 EBNF。

### <a name="geodistance"></a>geo.distance

`geo.distance`函數會採用兩個參數類型`Edm.GeographyPoint`，並傳回`Edm.Double`是它們以公里為單位之間的距離的值。 這不同於其他服務支援 OData 地理空間作業，通常會以公尺為單位傳回距離。

其中一個參數`geo.distance`必須是地理位置點常數，與其他必須是欄位的路徑 (或在篩選條件的情況下的範圍變數逐一查看型別的欄位`Collection(Edm.GeographyPoint)`)。 這些參數的順序並不重要。

地理位置點常數的形式是`geography'POINT(<longitude> <latitude>)'`、 經度和緯度是數值常數。

> [!NOTE]
> 使用時`geo.distance`在篩選中，您必須比較常使用的函式所傳回的距離`lt`， `le`， `gt`，或`ge`。 比較距離時不支援使用運算子 `eq` 和 `ne`。 比方說，這是正確使用方式`geo.distance`: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`。

### <a name="geointersects"></a>geo.intersects

`geo.intersects`函式接受類型的變數`Edm.GeographyPoint`和常數`Edm.GeographyPolygon`，並傳回`Edm.Boolean`  --  `true`多邊形、 範圍內的點是否`false`否則。

多邊形是儲存為一連串點定義週框環形的二維介面 (請參閱[範例](#examples)下方)。 多邊形必須是封閉的，也就是第一個和最後一個點集合必須相同。 [多邊形內的點必須採用逆時針順序](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>地理空間查詢和跨越 180th 經線的多邊形

許多的地理空間查詢編寫查詢，其中包含 180th 的經線 （靠近國際換日線） 的程式庫是辦到，或是需要因應措施，例如分割成兩個，其中的經線任一邊的多邊形。

在 Azure 搜尋服務，包括 180 度經度的地理空間查詢運作如預期般運作，如果查詢形狀是矩形，且您的座標對齊格線版面配置，沿著經度和緯度 (比方說， `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`)。 而如果是非矩形或未對齊的形狀，請考慮採用分割多邊形的方法。  

### <a name="geo-spatial-functions-and-null"></a>地理空間函式和 `null`

讓 Azure 搜尋服務，類型的欄位中的所有其他非集合欄位`Edm.GeographyPoint`可以包含`null`值。 當 Azure 搜尋服務會評估`geo.intersects`欄位`null`，結果一律為`false`。 行為`geo.distance`在此情況下取決於內容：

- 在篩選條件，`geo.distance`的`null`欄位中的結果`null`。 這表示文件將不會因為符合`null`相較於任何非 null 的值評估為`false`。
- 排序結果使用時 **$orderby**，`geo.distance`的`null`欄位中的最大可能距離的結果。 具有這類欄位的文件將排序較低，比所有其他時的排序方向`asc`會使用 （預設值），並高於所有其他項目時的方向是`desc`。

## <a name="examples"></a>範例

### <a name="filter-examples"></a>篩選範例

尋找指定的參考點 10 公里內的所有旅館 (其中位置是型別的欄位`Edm.GeographyPoint`):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

尋找在指定的檢視區所謂的多邊形內的所有旅館 (其中位置是型別的欄位`Edm.GeographyPoint`)。 請注意，多邊形必須是封閉的 (第一個和最後一個點集合必須相同)，且[點必須以逆時針順序列出](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Order-by 範例

排序依遞減的旅館`rating`，然後從指定的座標，以遞增的距離：

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

排序以遞減順序的旅館`search.score`和`rating`，然後在指定座標的遞增順序距離，以便之間具有相同的評等的兩間旅館，一個最接近會先列出：

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>後續步驟  

- [Azure 搜尋服務中的篩選條件](search-filters.md)
- [Azure 搜尋服務的 OData 運算式語言概觀](query-odata-filter-orderby-syntax.md)
- [Azure 搜尋服務的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋文件 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
