---
title: 使用 Azure 地圖服務顯示搜尋結果 | Microsoft Docs
description: 如何使用 Azure 地圖服務執行搜尋要求，然後在 Javascript 地圖上顯示結果
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c68b4bdffde5f987fe07d50d76fa83e7bdfa5235
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755033"
---
# <a name="show-search-results-on-the-map"></a>在地圖上顯示搜尋結果

本文說明如何搜尋景點，並在地圖上顯示搜尋結果。

有兩種方式可搜尋景點。 其中一個方式是使用服務模組發出搜尋要求。 第二個方式是透過 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 向 [Azure 地圖服務模糊搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 發出搜尋要求。 下文將討論這兩種方式。

## <a name="make-a-search-request-via-service-module"></a>透過服務模組提出搜尋要求

<iframe height='500' scrolling='no' title='在地圖上顯示搜尋結果 (服務模組)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/zLdYEB/'> (英文) 在地圖上顯示搜尋結果 (服務模組)</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a> (英文)。
</iframe>

在以上程式碼中，第一個程式碼區塊會建構地圖物件，並且將用戶端服務初始化。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會使用[服務模組](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1)中的 [getSearchFuzzy](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchfuzzy-string--searchgetsearchfuzzyoptionalparams-) 方法。 它可讓您透過[模糊搜尋 Rest API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 執行自由格式文字搜尋，以搜尋景點。 模糊搜尋 API 可以處理任何模糊輸入組合。 然後，模糊搜尋服務的回應會透過 [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest) 方法剖析成 GeoJSON 格式。 

第三個程式碼區塊會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件，以及搜尋其結果。 [符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的點式資料轉譯為地圖上的符號。  接著會建立符號圖層，而資料來源會新增至符號層，然後新增至地圖。

程式碼的最後一個區塊會使用地圖的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 屬性，為地圖調整觀景窗界限。

搜尋要求、資料來源、符號圖層及觀景窗界限會建立並設定於地圖的[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)內，以確保在地圖完全載入後顯示結果。


## <a name="make-a-search-request-via-xmlhttprequest"></a>透過 XMLHttpRequest 提出搜尋要求

<iframe height='500' scrolling='no' title='在地圖上顯示搜尋結果' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>在地圖上顯示搜尋結果</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建構地圖物件。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會將 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 傳送至 [Azure 地圖服務模糊搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 搜尋景點。 模糊搜尋 API 可以處理任何模糊輸入組合。 

第三個程式碼區塊會剖析搜尋回應，並將結果儲存在陣列中以計算界限。 然後，它會傳回搜尋結果。

第四個程式碼區塊會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件，以及搜尋其結果。 [符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的點式資料轉譯為地圖上的符號。 接著會建立符號圖層，而資料來源會新增至符號層，然後新增至地圖。

最後一個程式碼區塊會使用結果陣列建立 [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) 物件，然後使用地圖的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 來調整地圖的觀景窗界限。 然後，它會呈現結果圖釘。

路線查詢、資料來源、符號和線條圖層以及觀景窗界限會設定於地圖的[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)內，以確保在地圖完全載入後顯示結果。

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
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [顯示從甲地到乙地的指示](./map-route.md)
