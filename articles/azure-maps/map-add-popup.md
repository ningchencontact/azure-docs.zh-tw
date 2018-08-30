---
title: 使用 Azure 地圖服務新增快顯 | Microsoft Docs
description: 如何在 JavaScript 地圖上新增快顯
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0f86578e33e5c6a2d6528e2deb1c8068a0c94d01
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2018
ms.locfileid: "42747100"
---
# <a name="add-a-popup-to-the-map"></a>在地圖上新增快顯

本文說明如何在地圖上新增快顯。  

## <a name="understand-the-code"></a>了解程式碼

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='在地圖上新增快顯' src='//codepen.io/azuremaps/embed/zRyKxj/?height=545&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/zRyKxj/'>在地圖上新增快顯</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構對應物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

程式碼的第二個區塊會在地圖上建立並新增圖釘。 如需相關指示，您可以查看[在地圖上新增圖釘](./map-add-pin.md)。

程式碼的第三個區塊會建立要在快顯中顯示的內容。 快顯內容是 HTML 元素。 

程式碼的第四個區塊會透過 `new atlas.Popup()` 建立[快顯物件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)。 快顯屬性 (例如內容和位置) 是 [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.popupoptions?view=azure-iot-typescript-latest) 的一部分。 PopupOptions 可定義在快顯建構函式中或透過快顯類別的 [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) 函式來定義。

程式碼的最後一個區塊會使用地圖類別的 [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener) 函式來接聽圖釘上的滑鼠停放事件，並使用 快顯類別的 [open](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) 函式在事件發生時開啟快顯。


## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法： 

* [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener)
* [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#close)
    
如需更多可新增至地圖的程式碼範例，請參閱下列文章： 
* [新增圖形](./map-add-shape.md)
* [新增自訂 HTML](./map-add-custom-html.md)
