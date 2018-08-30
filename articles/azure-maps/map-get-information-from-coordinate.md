---
title: 使用 Azure 地圖服務顯示座標的相關資訊 | Microsoft Docs
description: 如何在使用者選取座標時於地圖上顯示地址的相關資訊
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e1cbed8995c0efbfb6010daaca5cd97ebec92dc6
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746337"
---
# <a name="get-information-from-a-coordinate"></a>從座標取得資訊

本文說明如何進行反向地址搜尋，並在點按滑鼠時於快顯中顯示所點選位置的地址。 

## <a name="understand-the-code"></a>了解程式碼

<iframe height='500' scrolling='no' title='從座標取得資訊' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>從座標取得資訊</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構對應物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

程式碼的第二個區塊會將滑鼠游標的樣式更新為指標。

程式碼的第三個區塊會建立快顯。 如需相關指示，您可以查看[在地圖上新增快顯](./map-add-popup.md)。

最後一個程式碼區塊會新增滑鼠點選的事件接聽程式。 點按滑鼠後，它會將 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 傳送至 [Azure 地圖服務反向地址搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)。 對於成功的回應，它會收集所點選位置的地址，並透過快顯類別的 [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) 函式定義快顯內容和位置

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法： 
* [反向地址搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener)
* [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#close)

如需更多可新增至地圖的程式碼範例，請參閱下列文章： 
* [顯示從甲地到乙地的指示](./map-route.md)
* [顯示流量](./map-show-traffic.md)
