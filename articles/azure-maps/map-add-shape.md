---
title: 使用 Azure 地圖服務新增圖形 | Microsoft Docs
description: 如何在 JavaScript 地圖上新增圖形
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 1e550002948fc1320b8645bf1af635536d524fe6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282384"
---
# <a name="add-a-shape-to-a-map"></a>在地圖中新增圖形

本文說明如何呈現在地圖使用線條和多邊形圖層上的幾何。 Azure 地圖服務 Web SDK 也支援建立的圓形的幾何，如同[GeoJSON 擴充的結構描述](extend-geojson.md#circle)。 所有的功能幾何也可以輕鬆地更新如果包裝在一起[圖形](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)類別。

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>將行加入至對應

`LineString` 和`MultiLineString`功能用來代表路徑與在地圖上的外框。

## <a name="use-a-line"></a>使用一條線

<iframe height='500' scrolling='no' title='在地圖中新增線條' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/qomaKv/'>在地圖中新增線條</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

上述程式碼中的第一個程式碼區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

在第二個程式碼區塊中，則會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件。 [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) 物件會建立並新增至資料來源。

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 會轉譯 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中已包裝的線條物件。 最後一個程式碼區塊會建立線條圖層，並將其新增至地圖。 請在 [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) 查看線條圖層的屬性。 建立並加入至地圖中的資料來源及線條圖層[事件處理常式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)以確保對應完全載入後，會顯示線條。

### <a name="add-symbols-along-a-line"></a>將沿著一條線的符號

此範例示範如何在地圖上新增沿著一條線的箭號圖示。 當使用符號的圖層，設定 「 列 」 的 「 放置 」 選項時，這將會呈現沿著線符號並旋轉圖示 (0 度 = right)。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="顯示沿著線的箭號" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱畫筆<a href='https://codepen.io/azuremaps/pen/drBJwX/'>沿著線條顯示箭號</a>透過 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>

### <a name="line-stroke-gradient"></a> 加入一條線的筆劃漸層

除了能夠將單一筆劃色彩套用至的行中，您也可以使用漸層的色彩顯示從一個直線線段轉換到下一步，以填入一條線。 例如，列漸層可用來跨物件相連線條代表不同時間和距離或不同的溫度的變更。 若要將這項功能套用至一條線中，資料來源必須`lineMetrics`選項設為 true，，然後再色彩漸層停駐的運算式可以傳遞至`strokeColor`線條的選項。 筆劃的漸層停駐運算式必須參考`['line-progress']`公開 （expose） 至運算式的導出的列計量的資料運算式。

<br/>

<iframe height="265" style="width: 100%;" scrolling="no" title="使用筆劃漸層的線條" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=265&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱畫筆<a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>筆劃漸層的線條</a>透過 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>

### <a name="customize-a-line-layer"></a>自訂線條圖層

線條圖層有數個樣式選項。 以下是可讓您試用這些選項的工具。

<br/>

<iframe height='700' scrolling='no' title='線條圖層選項' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>線條圖層選項</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>將多邊形加入至對應

`Polygon` 和`MultiPolygon`功能通常用來代表地圖上的區域。 

### <a name="use-a-polygon-layer"></a>使用多邊形圖層 

多邊形圖層會呈現多邊形的區域。 

<iframe height='500' scrolling='no' title='在地圖中新增多邊形 ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>在地圖中新增多邊形</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

在第二個程式碼區塊中，則會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件。 [多邊形](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)會從座標陣列來建立，並且會新增至資料來源。 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) 會在地圖上轉譯 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包裝的資料。 最後一個程式碼區塊會建立多邊形圖層，並將其新增至地圖。 請在 [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) 查看多邊形圖層的屬性。 建立並加入至地圖中的資料來源及多邊形圖層[事件處理常式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)以確保對應完全載入後，會顯示多邊形。

### <a name="use-a-polygon-and-line-layer-together"></a>同時使用多邊形和線條圖層

線條圖層可以用來呈現多邊形的外框中。 

<iframe height='500' scrolling='no' title='用來新增多邊形的多邊形和線條圖層' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>用來新增多邊形的多邊形和線條圖層</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

在第二個程式碼區塊中，則會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件。 [多邊形](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)會從座標陣列來建立，並且會新增至資料來源。 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) 會在地圖上轉譯 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包裝的資料。 請在 [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) 查看多邊形圖層的屬性。 [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)是線條的陣列。 請在 [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) 查看線條圖層的屬性。 第三個程式碼區塊會建立多邊形和線條圖層。

最後一個程式碼區塊會將多邊形和線條圖層新增至地圖中。 建立並加入至地圖中的資料來源和階層[事件處理常式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)以確保對應完全載入後，會顯示多邊形。

> [!TIP]
> 線條圖層預設會呈現多邊形，以及資料來源中的線條的座標。 若要限制圖層，使它只會呈現 LineString 功能組`filter`的圖層屬性`['==', ['geometry-type'], 'LineString']`或`['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]`如果您想要包含也 MultiLineString 功能。

### <a name="fill-a-polygon-with-a-pattern"></a>多邊形填入模式

除了以色彩填滿的多邊形也可以使用映像模式。 地圖影像 sprite 資源載入映像模式，並接著參考此映像`fillPattern`的多邊形圖層的屬性。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="多邊形的填滿模式" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱畫筆<a href='https://codepen.io/azuremaps/pen/JzQpYX/'>多邊形填滿圖樣</a>透過 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>

### <a name="customize-a-polygon-layer"></a>自訂多邊形圖層

多邊形圖層只有少數樣式選項。 以下是可讓您試用這些選項的工具。

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>將圓形加入至對應

Azure 地圖服務會使用提供的定義，如所述的圓形 GeoJSON 結構描述的擴充的版本[此處](extend-geojson.md#circle)。 圓形可以呈現在地圖上，藉由建立`Point`的功能，具有`subType`屬性值是`"Circle"`和`radius`具有數字，代表以公尺為單位的半徑屬性。 例如︰

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

Azure 地圖服務 Web SDK 轉換這些`Pooint`功能到`Polygon`功能在幕後，而且可以使用多邊形和線條圖層，如下所示在地圖上轉譯。

<iframe height='500' scrolling='no' title='在地圖中新增圓形' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>在地圖中新增圓形</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

上述程式碼中的第一個程式碼區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

在第二個程式碼區塊中，則會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件。 圓形[功能](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)的[點](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)且`subType`屬性設為`"Circle"`和`radius`以公尺為單位的屬性值。 當點功能`subType`的`"Circle"`會新增至資料來源時，它轉換成循環的多邊形內的對應。

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) 會在地圖上轉譯 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包裝的資料。 最後一個程式碼區塊會建立多邊形圖層，並將其新增至地圖。 請在 [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) 查看多邊形圖層的屬性。 建立並加入至地圖中的資料來源及多邊形圖層[事件處理常式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)以確保對應完全載入後，會顯示圓形。

## <a name="make-a-geometry-easy-to-update"></a>輕鬆更新幾何

A`Shape`類別會包裝[幾何](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest)或是[功能](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)，並使其更容易更新和維護它們。
`new Shape(data: Feature<data.Geometry, any>)` 建構圖形物件，並使用指定的功能將它初始化。

<br/>

<iframe height='500' scrolling='no' title='更新圖形屬性' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen<a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>更新圖形屬性</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

上述第一個程式碼區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

點是 [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 類別的[功能](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)。 程式碼的第二個區塊會初始化 HTML 滑桿元素的半徑值，然後在 [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 類別物件中建構和包裝點物件。

第三個程式碼區塊會建立函式，以取用 HTML 範圍滑桿元素中的值，並使用 shape 類別的 [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 方法變更半徑值。

在第四個程式碼區塊中，則會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件。 然後，點就會新增至資料來源。

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) 會在地圖上轉譯 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包裝的資料。 第三個程式碼區塊會建立多邊形圖層。 請在 [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) 查看多邊形圖層的屬性。 建立並加入至地圖中的資料來源，按一下事件 hanlder 中，多邊形圖層[事件處理常式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)以確保對應完全載入後，會顯示的點。

## <a name="next-steps"></a>後續步驟

如需更多可新增至地圖的程式碼範例，請參閱下列文章：

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)
