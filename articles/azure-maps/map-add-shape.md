---
title: 將多邊形圖層新增至 Azure 地圖服務 |Microsoft Docs
description: 如何將多邊形圖層新增至 Azure 地圖服務 Web SDK。
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ca6c0f5e6fde5a31655ed17f4a016bf44216643f
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976130"
---
# <a name="add-a-polygon-layer-to-the-map"></a>將多邊形圖層新增至地圖

本文說明如何使用多邊形圖層, 在地圖`Polygon`上`MultiPolygon`轉譯的區域和功能幾何。 Azure 地圖服務 Web SDK 也支援建立 Circle 幾何, 如[擴充 GeoJSON 架構](extend-geojson.md#circle)中所定義。 在地圖上轉譯時, 這些圓形會轉換成多邊形。 如果包裝了, 所有功能幾何也可以輕鬆地更新[。Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)類別。

## <a name="use-a-polygon-layer"></a>使用多邊形圖層 

當多邊形圖層連接至資料來源並載入到地圖上時, 它會轉譯`Polygon`和`MultiPolygon`功能的區域。 下列程式碼會示範如何建立多邊形、將它加入至資料來源, 並使用[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)類別來呈現多邊形圖層。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    opacaty: 0.5
}));
```

以下是上述功能的完整執行程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='在地圖中新增多邊形 ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>在地圖中新增多邊形</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>同時使用多邊形和線條圖層

線條圖層可以用來呈現多邊形的外框。 下列程式碼範例會轉譯如前一個範例的多邊形, 但現在會加入線條圖層做為連接至資料來源的第二個圖層。  

<iframe height='500' scrolling='no' title='用來新增多邊形的多邊形和線條圖層' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>用來新增多邊形的多邊形和線條圖層</a>。
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>使用模式填滿多邊形

除了以色彩填滿多邊形之外, 也可以使用影像模式。 將影像模式載入地圖影像 sprite 資源, 然後使用多邊形圖層的`fillPattern`屬性來參考此影像。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="多邊形填滿模式" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 的畫筆<a href='https://codepen.io/azuremaps/pen/JzQpYX/'>多邊形填滿模式</a>。
</iframe>


> [!TIP]
> Azure 地圖服務 web SDK 提供數個可自訂的影像範本, 供您用來做為填滿模式。 如需詳細資訊, 請參閱[如何使用影像範本](how-to-use-image-templates-web-sdk.md)檔。

## <a name="customize-a-polygon-layer"></a>自訂多邊形圖層

多邊形圖層只有少數樣式選項。 以下是可讓您試用這些選項的工具。

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>將圓形新增至地圖

Azure 地圖服務會使用 GeoJSON 架構的擴充版本, 如[這裡](extend-geojson.md#circle)所述, 提供圓形的定義。 您`Point`可以藉由建立`subType`具有值為之屬性的功能, 以及`radius`具有代表以計量之半徑`"Circle"`之數位的屬性, 在地圖上轉譯圓形。 例如:

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

Azure 地圖服務 Web SDK 會將這些`Point`功能轉換`Polygon`成幕後的功能, 而且可以使用多邊形和線條圖層在地圖上轉譯, 如下列程式碼範例所示。

<br/>

<iframe height='500' scrolling='no' title='在地圖中新增圓形' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>在地圖中新增圓形</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>讓 geometry 易於更新

類別會包裝[幾何](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest)或[功能](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest), 並可讓您輕鬆地進行更新和維護。`Shape` 您可以藉由傳入 geometry 和一組屬性來建立圖形, 或傳入功能, 如下列程式碼所示。

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

下列程式碼範例示範如何以 shape 類別包裝 circle GeoJSON 物件, 並使用滑杆輕鬆地更新它的半徑屬性。 當圖形中的半徑值變更時, 圓形的轉譯會在地圖上自動更新。

<br/>

<iframe height='500' scrolling='no' title='更新圖形屬性' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen<a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>更新圖形屬性</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

如需更多可新增至地圖的程式碼範例，請參閱下列文章：

> [!div class="nextstepaction"]
> [建立資料來源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [新增快顯](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [如何使用影像範本](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [新增線條圖層](map-add-line-layer.md)

其他資源:

> [!div class="nextstepaction"]
> [Azure 地圖服務 GeoJSON 規格延伸模組](extend-geojson.md#circle)