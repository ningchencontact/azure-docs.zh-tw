---
title: 使用 Azure 地圖服務顯示指示 | Microsoft Docs
description: 如何在 JavaScript 地圖上顯示兩個位置之間的指示
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: ed522779f5a86e38ee12a246cea9ac85d0379f9e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729032"
---
# <a name="show-directions-from-a-to-b"></a>顯示從甲地到乙地的指示

本文說明如何提出路線要求，並在地圖上顯示路線。

有兩種方式可以這麼做。 第一個方式是透過服務模組查詢 [Azure 地圖服務路線 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。 第二個方式是對於 API 發出 [XMLHttpRequest](https://xhr.spec.whatwg.org/)。 下文將討論這兩種方式。

## <a name="query-the-route-via-service-module"></a>透過服務模組查詢路線

<iframe height='500' scrolling='no' title='在地圖上顯示從甲地到乙地的指示 (服務模組)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/RBZbep/'>在地圖上顯示從甲地到乙地的指示 (服務模組)</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

程式碼的第一個區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊中的行會具現化服務用戶端。

第三個程式碼區塊會初始化地圖上的[串字圖層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)。

程式碼的第四個區塊會在地圖上建立並新增圖釘，以代表路線的起點和終點。 如需使用 [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) 的指示，您可以查看[在地圖上新增圖釘](map-add-pin.md)。

程式碼的下一個區塊會使用地圖類別的 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) 函式，根據路線的起點和終點設定地圖的週框方塊。

第六個程式碼區塊會建構路線查詢。

程式碼的最後一個區塊會透過 [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) 方法查詢 Azure 地圖服務路線規劃服務，以取得起點與終點之間的路線。 然後，回應會使用 [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) 方法剖析為 GeoJSON 格式。 它會將這些線條全都新增到地圖上以呈現路線。 如需詳細資訊，您可以查看[在地圖上新增線條](./map-add-shape.md#addALine)。

## <a name="query-the-route-via-xmlhttprequest"></a>透過 XMLHttpRequest 查詢路線

<iframe height='500' scrolling='no' title='在地圖上顯示從甲地到乙地的指示' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>在地圖上顯示從甲地到乙地的指示</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

程式碼的第一個區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

程式碼的第二個區塊會在地圖上建立並新增圖釘，以代表路線的起點和終點。 如需使用 [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) 的指示，您可以查看[在地圖上新增圖釘](map-add-pin.md)。

程式碼的第三個區塊會使用地圖類別的 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) 函式，來根據路線的起點和終點設定地圖的週框方塊。

程式碼的第四個區塊會將 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 傳送至 [Azure 地圖服務路線規劃 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。

程式碼的最後一個區塊則會剖析傳入的回應。 對於成功的回應，它會收集每個導航點的經緯度資訊。 它會藉由將每個導航點連接到後續的導航點來建立線條陣列。 它會將這些線條全都新增到地圖上以呈現路線。 如需相關指示，您可以查看[在地圖上新增線條](./map-add-shape.md#addALine)。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [在地圖上顯示路況](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [與地圖互動 – 滑鼠事件](./map-events.md)
