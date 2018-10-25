---
title: 使用 Azure 地圖服務顯示搜尋結果 | Microsoft Docs
description: 如何使用 Azure 地圖服務執行搜尋要求，然後在 Javascript 地圖上顯示結果
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 78ffa42bcf57b7163afc13b2550abdbae240ef00
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729225"
---
# <a name="show-search-results-on-the-map"></a>在地圖上顯示搜尋結果

本文說明如何搜尋景點，並在地圖上顯示搜尋結果。

有兩種方式可搜尋景點。 其中一個方式是使用服務模組發出搜尋要求。 第二個方式是透過 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 向 [Azure 地圖服務模糊搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 發出搜尋要求。 下文將討論這兩種方式。

## <a name="make-a-search-request-via-service-module"></a>透過服務模組提出搜尋要求

<iframe height='500' scrolling='no' title='在地圖上顯示搜尋結果 (服務模組)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/zLdYEB/'> (英文) 在地圖上顯示搜尋結果 (服務模組)</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a> (英文)。
</iframe>

程式碼的第一個區塊會建構地圖物件，並且將用戶端服務初始化。 如需相關指示，您可以查看[建立對應](./map-create.md)。

程式碼的第二個區塊會使用 [Azure 地圖服務模糊搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) (英文) 來搜尋景點。 模糊搜尋 API 可以處理任何模糊輸入組合。 然後，模糊搜尋服務的回應會透過 [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest#geojsonsearchresponse) 方法剖析成 GeoJSON 格式。 這些釘選接著會新增至地圖，以在地圖上顯示景點。

程式碼的最後一個區塊會使用地圖的 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) (英文) 屬性，為地圖調整觀景窗界限。

## <a name="make-a-search-request-via-xmlhttprequest"></a>透過 XMLHttpRequest 提出搜尋要求

<iframe height='500' scrolling='no' title='在地圖上顯示搜尋結果' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>在地圖上顯示搜尋結果</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

程式碼的第一個區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會將搜尋結果圖層新增至地圖。 搜尋結果圖層會將搜尋結果顯示為地圖上的圖釘。 使用 [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) 新增圖釘。

程式碼的第三個區塊會將 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 傳送至 [Azure 地圖服務模糊搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 搜尋景點。 模糊搜尋 API 可以處理任何模糊輸入組合。

最後一個區塊的程式碼會剖析回應，並使用地圖的 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) 轉譯轉換圖釘來調整地圖的觀景窗界限。

## <a name="next-steps"></a>後續步驟

深入了解**模糊搜尋**：

> [!div class="nextstepaction"]
> [Azure 地圖服務模糊搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [從座標取得資訊](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [顯示從甲地到乙地的指示](./map-route.md)
