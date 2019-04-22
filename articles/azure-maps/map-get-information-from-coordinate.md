---
title: 使用 Azure 地圖服務顯示座標的相關資訊 | Microsoft Docs
description: 如何在使用者選取座標時於地圖上顯示地址的相關資訊
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 50f906a9d8a0dc19f5eb47bef4cb68f4703f020f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59256051"
---
# <a name="get-information-from-a-coordinate"></a>從座標取得資訊

本文會示範如何進行反向位址搜尋，顯示已按下的快顯位置本身的位址。

有兩種方式可以進行反向位址搜尋。 第一個方式是透過服務模組查詢 [Azure 地圖反向地址搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)。 其他的方式是利用[擷取 API](https://fetch.spec.whatwg.org/)若要提出的要求[Azure Maps 反向地址搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)找到位址。 以下簡要說明這兩種方式。

## <a name="make-a-reverse-search-request-via-service-module"></a>透過服務模組提出反向搜尋要求

<iframe height='500' scrolling='no' title='從座標取得資訊 (服務模組)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>從座標取得資訊 (服務模組)</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼，程式碼的第一個區塊會建構對應物件，並設定的驗證機制，利用訂用帳戶金鑰。 如需相關指示，您可以查看[建立對應](./map-create.md)。

程式碼的第二個區塊建立`SubscriptionKeyCredentialPolicy`來驗證對 Azure 地圖服務的 HTTP 要求，與訂用帳戶金鑰。 然後`atlas.service.MapsURL.newPipeline()`會採用`SubscriptionKeyCredential`原則，並建立[管線](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest)執行個體。 `searchURL` 代表 Azure 地圖服務[搜尋](https://docs.microsoft.com/rest/api/maps/search)作業的 URL。

程式碼的第三個區塊的指標會更新滑鼠資料指標的樣式，並建立[快顯](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)物件。 如需相關指示，您可以查看[在地圖上新增快顯](./map-add-popup.md)。

程式碼的第四個區塊將按下滑鼠[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)。 當觸發時，它會建立搜尋查詢的按下點的座標。 然後它會使用服務模組[getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)方法來查詢[反向 API 取得搜尋地址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)座標的位址。 使用再擷取回應中的 GeoJSON 功能集合`geojson.getFeatures()`方法。

程式碼的第五個區塊會設定要顯示的已按下 座標位置的回應位址的 HTML 快顯視窗內容。

游標變更、快顯物件和點選事件全都建立於地圖的[載入事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)，以確保在可擷取座標資訊前完全載入地圖。

## <a name="make-a-reverse-search-request-via-fetch-api"></a>請透過提取 API 的反向搜尋要求

按一下地圖，讓使用 fetch 該位置的反向地理代碼要求。

<iframe height='500' scrolling='no' title='從座標取得資訊' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>從座標取得資訊</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼，程式碼的第一個區塊會建構對應物件，並設定的驗證機制，利用訂用帳戶金鑰。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會將滑鼠游標的樣式更新為指標和[快顯](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)物件。 如需相關指示，您可以查看[在地圖上新增快顯](./map-add-popup.md)。

第三個程式碼區塊會新增滑鼠點選的事件接聽程式。 在按下滑鼠，它會利用[擷取 API](https://fetch.spec.whatwg.org/)來查詢[Azure Maps 反向地址搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)已按下 的座標位址。 對於成功的回應，它會收集所點選位置的地址，並透過快顯類別的 [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) 函式定義快顯內容和位置。

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
