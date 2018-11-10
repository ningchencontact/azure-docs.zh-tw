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
ms.openlocfilehash: 801cbd6367e0843e43121b3582971a437984e863
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2018
ms.locfileid: "50247703"
---
# <a name="add-a-shape-to-a-map"></a>在地圖中新增圖形

本文會說明如何將[圖形](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)新增至地圖，以及如何更新地圖上現有圖形的屬性。

<a id="addALine"></a>

## <a name="add-a-line"></a>新增線條

<iframe height='500' scrolling='no' title='在地圖中新增線條' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/qomaKv/'>在地圖中新增線條</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

上述程式碼中的第一個程式碼區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

在第二個程式碼區塊中，則會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件。 線條是 LineString 的[功能](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)。 此功能接著會使用 `new atlas.Shape(new atlas.data.Feature((new atlas.data.LineString()))` 在 [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 類別物件中建立和包裝線條，並將此線條新增至資料來源。

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 會轉譯 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中已包裝的線條物件。 最後一個程式碼區塊會建立線條圖層，並將其新增至地圖。 請在 [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.linestringlayeroptions?view=azure-iot-typescript-latest) 查看線條圖層的屬性。 資料來源及線條圖層會在[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函式內建立並新增至地圖，以確保線條會在地圖完全載入後顯示。

<a id="addACircle"></a>

## <a name="add-a-circle"></a>新增圓形

<iframe height='500' scrolling='no' title='在地圖中新增圓形' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>在地圖中新增圓形</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

上述程式碼中的第一個程式碼區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

在第二個程式碼區塊中，則會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件。 圓形是 [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 的[功能](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)。 此功能接著會使用 `new atlas.Shape(new atlas.data.Feature(new atlas.data.Point()))` 在 [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 類別物件中建立和包裝圓形，並將此圓形新增至資料來源。

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) 會在地圖上轉譯 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包裝的資料。 最後一個程式碼區塊會建立多邊形圖層，並將其新增至地圖。 請在 [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.polygonlayeroptions?view=azure-iot-typescript-latest) 查看多邊形圖層的屬性。 資料來源及多邊形圖層會在[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函式內建立並新增至地圖，以確保圓形會在地圖完全載入後顯示。

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>新增多邊形

將多邊形新增至地圖的方式有兩種。 下列範例會說明這兩種方式。

### <a name="use-polygon-layer"></a>使用多邊形圖層 

<iframe height='500' scrolling='no' title='在地圖中新增多邊形 ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>在地圖中新增多邊形</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

在第二個程式碼區塊中，則會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件。 [多邊形](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) 是 [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) 類別的[功能](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)。 `new atlas.Shape(new atlas.data.Feature(new atlas.data.Polygon()))` 會用來建立有已排序座標的多邊形，並包裝在 [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 類別物件中。 然後 Shape 物件會新增至資料來源。

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) 會在地圖上轉譯 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包裝的資料。 最後一個程式碼區塊會建立多邊形圖層，並將其新增至地圖。 請在 [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.polygonlayeroptions?view=azure-iot-typescript-latest) 查看多邊形圖層的屬性。 資料來源及多邊形圖層會在[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函式內建立並新增至地圖，以確保多邊形會在地圖完全載入後顯示。

### <a name="use-polygon-and-line-layer"></a>使用多邊形和線條圖層

<iframe height='500' scrolling='no' title='用來新增多邊形的多邊形和線條圖層' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>用來新增多邊形的多邊形和線條圖層</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

在第二個程式碼區塊中，則會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件。 [多邊形](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) 是 [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) 的[功能](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)。 `new atlas.Shape(new atlas.data.Feature(new atlas.data.Polygon()))` 會用來建立有已排序座標的多邊形，並包裝在 [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 類別物件中。 然後 Shape 物件會新增至資料來源。

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) 會在地圖上轉譯 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包裝的資料。 請在 [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.polygonlayeroptions?view=azure-iot-typescript-latest) 查看多邊形圖層的屬性。 [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)是線條的陣列。 請在 [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.linestringlayeroptions?view=azure-iot-typescript-latest) 查看線條圖層的屬性。 第三個程式碼區塊會建立多邊形和線條圖層。

最後一個程式碼區塊會將多邊形和線條圖層新增至地圖中。 資料來源及圖層會在[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函式內建立並新增至地圖，以確保多邊形會在地圖完全載入後顯示。

## <a name="update-a-shape"></a>更新圖形

Shape 類別會包裝[幾何](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest)或[功能](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)，使圖形更容易更新和維護。
`new Shape(data: Feature<data.Geometry, any>)` 會建構圖形物件，並使用指定的功能來初始化圖形物件。

<br>

<iframe height='500' scrolling='no' title='更新圖形屬性' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen<a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>更新圖形屬性</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

上述第一個程式碼區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

點是 [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 類別的[功能](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)。 程式碼的第二個區塊會初始化 HTML 滑桿元素的半徑值，然後在 [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 類別物件中建構和包裝點物件。

第三個程式碼區塊會建立函式，以取用 HTML 範圍滑桿元素中的值，並使用 shape 類別的 [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest#addproperty) 方法變更半徑值。

在第四個程式碼區塊中，則會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件。 然後，點就會新增至資料來源。

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) 會在地圖上轉譯 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包裝的資料。 第三個程式碼區塊會建立多邊形圖層。 請在 [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.polygonlayeroptions?view=azure-iot-typescript-latest) 查看多邊形圖層的屬性。 資料來源 (點選事件接聽程式) 及多邊形圖層會在[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函式內建立並新增至地圖，以確保點會在地圖完全載入後顯示。

## <a name="next-steps"></a>後續步驟

如需更多可新增至地圖的程式碼範例，請參閱下列文章：

> [!div class="nextstepaction"]
> [新增自訂 HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [顯示搜尋結果](./map-search-location.md)
