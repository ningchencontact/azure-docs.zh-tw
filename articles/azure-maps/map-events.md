---
title: Handle events with Azure Maps | Microsoft Docs
description: How to make an interactive Web SDK map with map events
author: jingjing-z
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 99ef5aa0ee8fa542b0aa807cc536ebfbee369e10
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484281"
---
# <a name="interact-with-the-map"></a>與地圖互動

This article shows you how to use [map class events](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events) property to highlight events on the map and on different layers of the map. 此外，也會示範如何使用地圖類別事件屬性來醒目提示您與 HTML 標記互動時的事件。

## <a name="interact-with-the-map"></a>與地圖互動

Play with the map below, and see the corresponding mouse events highlighted on the right. 您可以按一下 [JS] 索引標籤來檢視和編輯 JavaScript 程式碼。 您也可以按一下 [在 CodePen 上編輯] 按鈕，並在 CodePen 上編輯程式碼。

<br/>

<iframe height='600' scrolling='no' title='與地圖互動 – 滑鼠事件' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>與地圖互動 – 滑鼠事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="interact-with-map-layers"></a>與地圖圖層互動

The following code highlights the name of the events that get fired up as you interact with the Symbol Layer. The symbol, bubble, line, and polygon layer all support the same set of events. The heat map and tile layers do not support any of these events.

<br/>

<iframe height='600' scrolling='no' title='與地圖互動 – 地圖事件' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>與地圖互動 – 圖層事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="interact-with-html-marker"></a>與 HTML 標記互動

The following code adds Javascript map events to an HTML marker. 它也會醒目提示當您與 HTML 標記互動時所引發的事件名稱。

<br/>

<iframe height='500' scrolling='no' title='與地圖互動 – HTML 標記事件' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>與地圖互動 – HTML 標記事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

The following table lists all of the supported map class events.

| 活動               | 描述 |
|---------------------|-------------|
| `boxzoomend`        | Fired when a "box zoom" interaction ends.|
| `boxzoomstart`      | Fired when a "box zoom" interaction starts.|
| `click`             | Fired when a pointing device is pressed and released at the same point on the map.|
| `close`             | Fired when the popup is closed manually or programatically.|
| `contextmenu`       | Fired when the right button of the mouse is clicked.|
| `data`              | Fired when any map data loads or changes. |
| `dataadded`         | Fired when shapes are added to the `DataSource`.|
| `dataremoved`       | Fired when shapes are removed from the `DataSource`.|
| `datasourceupdated` | Fired when the `DataSource` object is updated.|
| `dblclick`          | Fired when a pointing device is clicked twice at the same point on the map.|
| `drag`              | Fired repeatedly during a "drag to pan" interaction on the map, popup, or HTML marker.|
| `dragend`           | Fired when a "drag to pan" interaction ends on the map, popup, or HTML marker.|
| `dragstart`         | Fired when a "drag to pan" interaction starts on the map, popup, or HTML marker.|
| `error`             | Fired when an error occurs.|
| `idle`              | <p>Fired after the last frame rendered before the map enters an "idle" state:<ul><li>No camera transitions are in progress.</li><li>All currently requested tiles have loaded.</li><li>All fade/transition animations have completed.</li></ul></p>|
| `keydown`           | Fired when a key is pressed down.|
| `keypress`          | Fired when a key that produces a typable character (an ANSI key) is pressed.|
| `keyup`             | Fired when a key is released.|
| `layeradded`        | Fired when a layer is added to the map.|
| `layerremoved`      | Fired when a layer is removed from the map.|
| `load`              | Fired immediately after all necessary resources have been downloaded and the first visually complete rendering of the map has occurred.|
| `mousedown`         | Fired when a pointing device is pressed within the map or when on top of an element.|
| `mouseenter`        | Fired when a pointing device is initially moved over the map or an element. |
| `mouseleave`        | Fired when a pointing device is moved out the map or an element. |
| `mousemove`         | Fired when a pointing device is moved within the map or an element.|
| `mouseout`          | Fired when a point device leaves the map's canvas our leaves an element.|
| `mouseover`         | Fired when a pointing device is moved over the map or an element.|
| `mouseup`           | Fired when a pointing device is released within the map or when on top of an element.|
| `move`              | Fired repeatedly during an animated transition from one view to another, as the result of either user interaction or methods.|
| `moveend`           | Fired just after the map completes a transition from one view to another, as the result of either user interaction or methods.|
| `movestart`         | Fired just before the map begins a transition from one view to another, as the result of either user interaction or methods.|
| `open`              | Fired when the popup is opened manually or programatically.|
| `pitch`             | Fired whenever the map's pitch (tilt) changes as the result of either user interaction or methods.|
| `pitchend`          | Fired immediately after the map's pitch (tilt) finishes changing as the result of either user interaction or methods.|
| `pitchstart`        | Fired whenever the map's pitch (tilt) begins a change as the result of either user interaction or methods.|
| `ready`             | Fired when the minimum required map resources are loaded before the map is ready to be programmatically interacted with.|
| `render`            | <p>Fired whenever the map is drawn to the screen, as the result of:<ul><li>A change to the map's position, zoom, pitch, or bearing.</li><li>A change to the map's style.</li><li>A change to a `DataSource` source.</li><li>The loading of a vector tile, GeoJSON file, glyph, or sprite.</li></ul></p>|
| `resize`            | Fired immediately after the map has been resized.|
| `rotate`            | Fired repeatedly during a "drag to rotate" interaction.|
| `rotateend`         | Fired when a "drag to rotate" interaction ends.|
| `rotatestart`       | Fired when a "drag to rotate" interaction starts.|
| `shapechanged`      | Fired when a shape object property is changed.|
| `sourcedata`        | Fired when one of the map's sources loads or changes, including if a tile belonging to a source loads or changes. |
| `sourceadded`       | Fired when a `DataSource` or `VectorTileSource` is added to the map.|
| `sourceremoved`     | Fired when a `DataSource` or `VectorTileSource` is removed from the map.|
| `styledata`         | Fired when the map's style loads or changes.|
| `styleimagemissing` | Fired when a layer tries to load an image from the image sprite that doesn't exist |
| `tokenacquired`     | Fired when an AAD access token is obtained.|
| `touchcancel`       | Fired when a touchcancel event occurs within the map.|
| `touchend`          | Fired when a touchend event occurs within the map.|
| `touchmove`         | Fired when a touchmove event occurs within the map.|
| `touchstart`        | Fired when a touchstart event occurs within the map.|
| `wheel`             | Fired when a mouse wheel event occurs within the map.|
| `zoom`              | Fired repeatedly during an animated transition from one zoom level to another, as the result of either user interaction or methods.|
| `zoomend`           | Fired just after the map completes a transition from one zoom level to another, as the result of either user interaction or methods.|
| `zoomstart`         | Fired just before the map begins a transition from one zoom level to another, as the result of either user interaction or methods.|


## <a name="next-steps"></a>後續步驟

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [Using the Azure Maps Services module](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)
