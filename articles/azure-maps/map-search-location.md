---
title: 使用 Azure 地圖服務顯示搜尋結果 | Microsoft Docs
description: 如何使用 Azure 地圖服務執行搜尋要求，然後在 Javascript 地圖上顯示結果
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 85e15b756d2ea241296e11ebfcb5bf3029143de7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66357774"
---
# <a name="show-search-results-on-the-map"></a>在地圖上顯示搜尋結果

本文說明如何搜尋景點，並在地圖上顯示搜尋結果。

有兩種方式可搜尋景點。 其中一個方式是使用服務模組發出搜尋要求。 其他的方法是搜尋要求，來[Azure Maps 模糊搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)透過[擷取 API](https://fetch.spec.whatwg.org/)。 下文將討論這兩種方式。

## <a name="make-a-search-request-via-service-module"></a>透過服務模組提出搜尋要求

<iframe height='500' scrolling='no' title='在地圖上顯示搜尋結果 (服務模組)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/zLdYEB/'> (英文) 在地圖上顯示搜尋結果 (服務模組)</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a> (英文)。
</iframe>

在上述程式碼，程式碼的第一個區塊會建構對應物件，並設定的驗證機制，利用訂用帳戶金鑰。 如需相關指示，您可以查看[建立對應](./map-create.md)。

程式碼的第二個區塊建立`SubscriptionKeyCredentialPolicy`來驗證對 Azure 地圖服務的 HTTP 要求，與訂用帳戶金鑰。 然後`atlas.service.MapsURL.newPipeline()`會採用`SubscriptionKeyCredential`原則，並建立[管線](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)執行個體。 `searchURL` 代表 Azure 地圖服務[搜尋](https://docs.microsoft.com/rest/api/maps/search)作業的 URL。

第三個程式碼區塊會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件，以及搜尋其結果。 [符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的點式資料轉譯為地圖上的符號。  接著會建立符號圖層，而資料來源會新增至符號層，然後新增至地圖。

第四個程式碼區塊會使用[SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams)方法中的[服務模組](https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js)。 它可讓您執行自由格式文字搜尋，則透過[取得搜尋模糊的 rest API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)來搜尋景點。 取得搜尋模糊 API 可以處理任何模糊的輸入組合。 接著會使用 `geojson.getFeatures()` 方法，從回應中擷取 GeoJSON 功能集合並新增至資料來源，而自動使資料透過符號圖層呈現在地圖上。

程式碼的最後一個區塊會使用地圖的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 屬性，為地圖調整觀景窗界限。

搜尋要求，會建立資料來源和符號層和相機界限並引導模式中的設定準備好[事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)以確保對應完全載入後，就會顯示結果。


## <a name="make-a-search-request-via-fetch-api"></a>請透過提取 API 的搜尋要求

<iframe height='500' scrolling='no' title='在地圖上顯示搜尋結果' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>在地圖上顯示搜尋結果</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼，程式碼的第一個區塊會建構對應物件，並設定的驗證機制，利用訂用帳戶金鑰。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個區塊的程式碼會建立搜尋要求的 URL。 它也會建立兩個陣列來儲存繫結和搜尋結果的 pin。

使用程式碼的第三個區塊[擷取 API](https://fetch.spec.whatwg.org/)提出要求以[Azure Maps 模糊搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)觀光點搜尋。 模糊搜尋 API 可以處理任何模糊輸入組合。 接著會處理和剖析搜尋回應，並將結果接點加入至 searchPins 陣列。

第四個程式碼區塊會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件，以及搜尋其結果。 [符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的點式資料轉譯為地圖上的符號。 接著會建立符號圖層，而資料來源會新增至符號層，然後新增至地圖。

最後一個程式碼區塊會使用結果陣列建立 [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) 物件，然後使用地圖的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 來調整地圖的觀景窗界限。 然後，它會呈現結果 pin。

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
