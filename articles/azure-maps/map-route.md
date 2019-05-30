---
title: 使用 Azure 地圖服務顯示指示 | Microsoft Docs
description: 如何在 JavaScript 地圖上顯示兩個位置之間的指示
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 31403a11e8989cee6a1c166879a0b2f594892704
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357621"
---
# <a name="show-directions-from-a-to-b"></a>顯示從甲地到乙地的指示

本文說明如何提出路線要求，並在地圖上顯示路線。

有兩種方式可以這麼做。 第一個方式是透過服務模組查詢 [Azure 地圖服務路線 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。 第二個方法是使用[擷取 API](https://fetch.spec.whatwg.org/)提出搜尋要求以[Azure 地圖服務路線 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。 下文將討論這兩種方式。

## <a name="query-the-route-via-service-module"></a>透過服務模組查詢路線

<iframe height='500' scrolling='no' title='在地圖上顯示從甲地到乙地的指示 (服務模組)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/RBZbep/'>在地圖上顯示從甲地到乙地的指示 (服務模組)</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼，程式碼的第一個區塊建構對應物件，並設定為使用的訂用帳戶金鑰的驗證機制。 如需相關指示，您可以查看[建立對應](./map-create.md)。

程式碼的第二個區塊建立`SubscriptionKeyCredentialPolicy`來驗證對 Azure 地圖服務的 HTTP 要求，與訂用帳戶金鑰。 `atlas.service.MapsURL.newPipeline()` 會採用 `SubscriptionKeyCredential` 原則，並建立[管線](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)執行個體。 `routeURL` 代表 Azure 地圖服務[路線規劃](https://docs.microsoft.com/rest/api/maps/route)作業的 URL。

建立程式碼的第三個區塊，並將[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)到對應的物件。

程式碼的第四個區塊建立開始和結束[點](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)物件，並將它們加入至資料來源物件。

線條是 LineString 的[功能](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)。 [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 會將 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包裝的線條物件轉譯為地圖上的線條。 第四個程式碼區塊會建立線條圖層，並將其新增至地圖。 請在 [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) 查看線條圖層的屬性。

[符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的點式資料轉譯為地圖上的符號。 第五個區塊的程式碼會建立，並將符號圖層加入至地圖。

第六個區塊的程式碼查詢 Azure 地圖服務路由服務，也就是組件的[服務模組](https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js)。 [CalculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) RouteURL 的方法用來取得的開始和結束點之間的路由。 使用再擷取回應中的 GeoJSON 功能集合`geojson.getFeatures()`方法並加入至資料來源。 然後，它會將回應呈現為地圖上的路線。 如需有關將線條新增至地圖的詳細資訊，請參閱[在地圖上新增線條](./map-add-shape.md#addALine)。

程式碼的最後一個區塊設定的範圍使用地圖的地圖[setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)屬性。

路線查詢、資料來源、符號和線條圖層以及觀景窗界限會建立並設定於地圖的[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)內，以確保在地圖完全載入後顯示結果。

## <a name="query-the-route-via-fetch-api"></a>查詢透過擷取 API 路由

<iframe height='500' scrolling='no' title='在地圖上顯示從甲地到乙地的指示' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>在地圖上顯示從甲地到乙地的指示</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼，程式碼的第一個區塊建構對應物件，並設定為使用的訂用帳戶金鑰的驗證機制。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會建立 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 物件並將其新增至地圖。

第三個程式碼區塊會建立路線的起點和目的地點，並將它們新增至資料來源。 如需使用 [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 的指示，您可以查看[在地圖上新增圖釘](map-add-pin.md)。

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 會將 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包裝的線條物件轉譯為地圖上的線條。 第四個程式碼區塊會建立線條圖層，並將其新增至地圖。 請在 [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) 查看線條圖層的屬性。

[符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的點式資料轉譯為地圖上的符號。 第五個區塊的程式碼會建立，並將符號圖層加入至地圖。 請在 [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 上查看符號圖層的屬性。

下一個程式碼區塊會從起點與目的地點建立 `SouthWest` 和 `NorthEast` 點，並使用地圖的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 屬性來設定地圖的範圍。

使用程式碼的最後一個區塊[擷取 API](https://fetch.spec.whatwg.org/)提出搜尋要求以[Azure 地圖服務路線 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。 然後剖析回應。 如果回應成功，緯度和經度資訊用來建立陣列的設定，一條線連接這些點。 行資料再加入資料來源，以呈現在地圖上的路由。 如需相關指示，您可以查看[在地圖上新增線條](./map-add-shape.md#addALine)。

路線查詢、資料來源、符號和線條圖層以及觀景窗界限會建立並設定於地圖的[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)內，以確保在地圖完全載入後顯示結果。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [在地圖上顯示路況](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [與地圖互動 – 滑鼠事件](./map-events.md)
