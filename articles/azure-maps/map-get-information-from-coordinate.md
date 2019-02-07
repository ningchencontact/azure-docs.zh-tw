---
title: 使用 Azure 地圖服務顯示座標的相關資訊 | Microsoft Docs
description: 如何在使用者選取座標時於地圖上顯示地址的相關資訊
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 20616adf649924a13e80411aa5135889a175f442
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750183"
---
# <a name="get-information-from-a-coordinate"></a>從座標取得資訊

本文會示範如何進行反向位址搜尋，顯示已按下的快顯位置本身的位址。

有兩種方式可以進行反向位址搜尋。 第一個方式是透過服務模組查詢 [Azure 地圖反向地址搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)。 其他方法是對於 API 進行 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 來尋找地址。 以下簡要說明這兩種方式。

## <a name="make-a-reverse-search-request-via-service-module"></a>透過服務模組提出反向搜尋要求

<iframe height='500' scrolling='no' title='從座標取得資訊 (服務模組)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>從座標取得資訊 (服務模組)</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

程式碼的第一個區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊中的程式碼行會具現化用戶端服務。

第三個程式碼區塊會將滑鼠游標的樣式更新為指標和[快顯](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)物件。 如需相關指示，您可以查看[在地圖上新增快顯](./map-add-popup.md)。

第四個程式碼區塊會新增滑鼠點選的[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)。 按下滑鼠時，會使用所按下點的座標建立搜尋查詢。 然後，它會使用地圖的 [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest) 端點查詢座標的位址。

對於成功的回應，它會收集所點選位置的地址，並透過快顯類別的 [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) 函式定義快顯內容和位置。

游標變更、快顯物件和點選事件全都建立於地圖的[載入事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)，以確保在可擷取座標資訊前完全載入地圖。

## <a name="make-a-reverse-search-request-via-xmlhttprequest"></a>透過 XMLHttpRequest 提出反向搜尋要求

<iframe height='500' scrolling='no' title='從座標取得資訊' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>從座標取得資訊</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

上述第一個程式碼區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會將滑鼠游標的樣式更新為指標和[快顯](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)物件。 如需相關指示，您可以查看[在地圖上新增快顯](./map-add-popup.md)。

第三個程式碼區塊會新增滑鼠點選的事件接聽程式。 點按滑鼠後，它會將 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 傳送至 [Azure 地圖服務反向地址搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)，以查詢所點按的座標位址。 對於成功的回應，它會收集所點選位置的地址，並透過快顯類別的 [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) 函式定義快顯內容和位置。

游標變更、快顯物件和點選事件全都建立於地圖的[載入事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)，以確保在可擷取座標資訊前完全載入地圖。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [顯示從甲地到乙地的指示](./map-route.md)

> [!div class="nextstepaction"]
> [顯示流量](./map-show-traffic.md)
