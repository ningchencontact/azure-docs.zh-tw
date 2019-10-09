---
title: 將「延伸多邊形圖層」新增至 Azure 地圖服務 |Microsoft Docs
description: 如何將「區域」多邊形圖層新增至 Azure 地圖服務 Web SDK。
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: fdb5e2b78d9e5817c5a5d139cdf0b34744ed011f
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170707"
---
# <a name="add-an-extrusion-polygon-layer-to-the-map"></a>將「延伸多邊形圖層」新增至地圖

本文說明如何使用多邊形的 [延伸圖層]，將 `Polygon` 的區域和 @no__t 1 功能幾何轉譯為地圖上的拉伸圖形。 Azure 地圖服務 Web SDK 也支援建立 Circle 幾何，如[擴充 GeoJSON 架構](extend-geojson.md#circle)中所定義。 在地圖上轉譯時，這些圓形會轉換成多邊形。 如果包裝了，所有功能幾何也可以輕鬆地更新[。Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)類別。


## <a name="use-a-polygon-extrusion-layer"></a>使用多邊形延伸圖層

當[多邊形延伸圖層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)連接到資料來源並載入地圖上時，它會將 `Polygon` 和 @no__t 2 功能的區域轉譯為拉伸的圖形。 多邊形拉伸圖層的 `height` 和 @no__t 1 屬性，會定義從拉伸圖形的地面和高度到量表的基底**距離。** 下列程式碼示範如何建立多邊形、將它加入至資料來源，並使用多邊形的 [延伸層] 類別來呈現它。

> [!Note]
> 多邊形延伸層中所定義的 `base` 值應該小於或等於 `height` 的值。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="已拉伸多邊形" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）畫筆已<a href='https://codepen.io/azuremaps/pen/wvvBpvE'>拉伸多邊形</a>。</iframe>


## <a name="add-data-driven-multipolygons"></a>新增資料驅動 multipolygons

分級著色圖地圖可以使用多邊形延伸圖層來轉譯，方法是設定其 `height` 和 @no__t 1 屬性，與 `Polygon` 和 @no__t 3 功能幾何中統計變數的測量比例成正比。 下列程式碼範例會根據州的人口密度測量，顯示分級著色圖的拉伸對應。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="已拉伸的分級著色圖對應" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）畫筆已<a href='https://codepen.io/azuremaps/pen/eYYYNox'>拉伸的分級著色圖對應</a>。
</iframe>

## <a name="add-a-circle-to-the-map"></a>將圓形新增至地圖

Azure 地圖服務會使用 GeoJSON 架構的擴充版本，如[這裡](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle)所述，提供圓形的定義。 藉由建立具有 `Circle` `subType` 屬性的 @no__t 0 功能，以及代表以量表表示半徑的編號 `Radius` 屬性，即可在地圖上轉譯圓角**圓形。** 例如:

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

Azure 地圖服務 Web SDK 會將這些 `Point` 功能轉換成幕後的 @no__t 1 功能，而且可以使用多邊形的延伸層在地圖上呈現，如下列程式碼範例所示。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="無人機空域多邊形" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的 Pen<a href='https://codepen.io/azuremaps/pen/zYYYrxo'>無人機空域多邊形</a>。
</iframe>


## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [多邊形延伸圖層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

其他資源:

> [!div class="nextstepaction"]
> [Azure 地圖服務 GeoJSON 規格延伸模組](extend-geojson.md#circle)
