---
title: 使用 Azure 地圖服務顯示指示 | Microsoft Docs
description: 如何在 JavaScript 地圖上顯示兩個位置之間的指示
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 8c337ba1770b9b618567e7a3ee5fdbadba63181b
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2019
ms.locfileid: "54231847"
---
# <a name="show-directions-from-a-to-b"></a>顯示從甲地到乙地的指示

本文說明如何提出路線要求，並在地圖上顯示路線。

有兩種方式可以這麼做。 第一個方式是透過服務模組查詢 [Azure 地圖服務路線 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。 第二個方式是對於 API 發出 [XMLHttpRequest](https://xhr.spec.whatwg.org/)。 下文將討論這兩種方式。

## <a name="query-the-route-via-service-module"></a>透過服務模組查詢路線

<iframe height='500' scrolling='no' title='在地圖上顯示從甲地到乙地的指示 (服務模組)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/RBZbep/'>在地圖上顯示從甲地到乙地的指示 (服務模組)</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊中的程式碼行會具現化用戶端服務。

第三個會建立 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 物件並將其新增至地圖。

 線條是 LineString 的[功能](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)。 [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 會將 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包裝的線條物件轉譯為地圖上的線條。 第四個程式碼區塊會建立線條圖層，並將其新增至地圖。 請在 [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.deprecated.linestringlayeroptions?view=azure-iot-typescript-latest) 查看線條圖層的屬性。

[符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的點式資料轉譯為地圖上的符號。 第五個程式碼區塊會建立符號圖層，並將其新增至地圖。

第六個程式碼區塊會建立開始和結束[點](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)物件，並將它們新增至 dataSource 物件。

第七個程式碼區塊會使用地圖的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 屬性來設定地圖的界限。

最後一個程式碼區塊會查詢 Azure 地圖服務路線規劃服務，這是[服務模組](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1)的組件。 [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) 方法用來取得開始與結束點之間的路線。 然後，回應會使用 [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) 方法剖析為 GeoJSON 格式。 然後，它會將回應呈現為地圖上的路線。 如需有關將線條新增至地圖的詳細資訊，請參閱[在地圖上新增線條](./map-add-shape.md#addALine)。

路線查詢、資料來源、符號和線條圖層以及觀景窗界限會建立並設定於地圖的[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)內，以確保在地圖完全載入後顯示結果。

## <a name="query-the-route-via-xmlhttprequest"></a>透過 XMLHttpRequest 查詢路線

<iframe height='500' scrolling='no' title='在地圖上顯示從甲地到乙地的指示' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>在地圖上顯示從甲地到乙地的指示</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

程式碼的第一個區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會建立 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 物件並將其新增至地圖。

第三個程式碼區塊會建立路線的起點和目的地點，並將它們新增至資料來源。 如需使用 [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) 的指示，您可以查看[在地圖上新增圖釘](map-add-pin.md)。

 [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 會將 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包裝的線條物件轉譯為地圖上的線條。 第四個程式碼區塊會建立線條圖層，並將其新增至地圖。 請在 [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) 查看線條圖層的屬性。

[符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的點式資料轉譯為地圖上的符號。 第五個程式碼區塊會建立符號圖層，並將其新增至地圖。 請在 [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 上查看符號圖層的屬性。

下一個程式碼區塊會從起點與目的地點建立 `SouthWest` 和 `NorthEast` 點，並使用地圖的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 屬性來設定地圖的範圍。

最後一個程式碼區塊會將 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 傳送至 [Azure 地圖服務路線規劃 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。 它會接著剖析傳入的回應。 而對於成功的回應，它會收集每個路線點的經緯度資訊，並藉由連接這些點來建立線條陣列。 然後它會將所有線條全都新增到 dataSource，以在地圖上呈現路線。 如需相關指示，您可以查看[在地圖上新增線條](./map-add-shape.md#addALine)。

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
