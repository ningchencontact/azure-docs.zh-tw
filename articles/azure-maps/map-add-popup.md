---
title: 使用 Azure 地圖服務新增快顯 | Microsoft Docs
description: 如何將快捷方式新增至 Azure 地圖服務 Web SDK。
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cde6c745034d0963bd372e36e6e5a046113c202b
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976558"
---
# <a name="add-a-popup-to-the-map"></a>在地圖上新增快顯

本文說明如何在地圖上的某個點新增快顯。

## <a name="understand-the-code"></a>了解程式碼

下列程式碼會使用符號圖層, 將`name`具有`description`和屬性的點特徵新增至地圖。 隨即會建立[快顯視窗類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)的實例, 但不會顯示。 滑鼠事件會加入至符號圖層, 以在滑鼠停留在符號標記上時, 觸發開啟和關閉快顯視窗。 當標記符號暫留時, 快顯的`position`屬性會更新為標記的位置, `content`而選項會使用`name`一些 HTML 來更新, 以包裝要`description`暫留的 point 功能的和屬性。 然後會使用其`open`函式, 在地圖上顯示快顯。

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

以下是上述功能的完整執行程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='使用 Azure 地圖服務新增快顯' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>使用 Azure 地圖服務新增快顯</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>重複使用具有多個點的快顯

當您有大量的點, 而且只想要一次顯示一個快顯視窗時, 最好的方法是建立一個快顯並重複使用它, 而不是針對每個點功能建立快捷方式。 藉由重複使用快顯, 應用程式所建立的 DOM 元素數目會大幅減少, 而能夠提供更好的效能。 下列範例會建立3點特徵。 如果您對任何一個特徵按一下，便會顯示快顯，內有該點特徵的內容。

<br/>

<iframe height='500' scrolling='no' title='重複使用具有多個釘選的快顯' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>重複使用具有多個釘選的快顯</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="customizing-a-popup"></a>自訂快顯視窗

根據預設, 快顯視窗會有白色背景、底部的指標箭號, 以及右上角的 [關閉] 按鈕。 下列範例會使用快顯視窗的`fillColor`選項, 將背景色彩變更為黑色。 將此`shoCloseButton`選項設定為 false, 即可移除 [關閉] 按鈕。 快顯的 HTML 內容會使用快顯視窗邊緣的10圖元填補, 而文字則會設為白色, 使其在黑色背景上顯示良好。  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="自訂快顯視窗" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 的畫筆<a href='https://codepen.io/azuremaps/pen/ymKgdg/'>自訂快顯視窗</a>。
</iframe>

## <a name="popup-events"></a>快顯視窗事件

您可以開啟、關閉和拖曳快顯視窗。 Popup 類別提供事件, 協助開發人員對這些動作做出反應。 下列範例會反白顯示當您開啟、關閉或拖曳快顯視窗時, 所引發的事件。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="快顯視窗事件" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>), 以查看畫筆<a href='https://codepen.io/azuremaps/pen/BXrpvB/'>快顯視窗事件</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

請參閱下列推薦文章，了解完整程式碼範例：

> [!div class="nextstepaction"]
> [新增符號圖層](./map-add-pin.md)

> [!div class="nextstepaction"]
> [新增 HTML 標記](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [新增線條圖層](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](map-add-shape.md)