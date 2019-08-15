---
title: 使用 Azure 地圖服務顯示搜尋結果 | Microsoft Docs
description: 如何使用 Azure 地圖服務執行搜尋要求, 然後在 Azure 地圖服務 Web SDK 上顯示結果。
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7c4c78e1d21754d42391a3762e9f7ed199a7376b
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68975971"
---
# <a name="show-search-results-on-the-map"></a>在地圖上顯示搜尋結果

本文說明如何搜尋景點，並在地圖上顯示搜尋結果。

有兩種方式可搜尋景點。 其中一個方式是使用服務模組發出搜尋要求。 另一種方式是透過[FETCH api](https://fetch.spec.whatwg.org/)提出搜尋要求, 以[AZURE 地圖服務模糊搜尋 api](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 。 下文將討論這兩種方式。

## <a name="make-a-search-request-via-service-module"></a>透過服務模組提出搜尋要求

<iframe height='500' scrolling='no' title='在地圖上顯示搜尋結果 (服務模組)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/zLdYEB/'> (英文) 在地圖上顯示搜尋結果 (服務模組)</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a> (英文)。
</iframe>

在上述程式碼中, 程式碼的第一個區塊會建立地圖物件, 並將驗證機制設定為使用存取權杖。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會`TokenCredential`建立, 以使用存取權杖來驗證 Azure 地圖服務的 HTTP 要求。 然後, 它會`TokenCredential`將`atlas.service.MapsURL.newPipeline()`傳遞至, 並建立[管線](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)實例。 `searchURL` 代表 Azure 地圖服務[搜尋](https://docs.microsoft.com/rest/api/maps/search)作業的 URL。

第三個程式碼區塊會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件，以及搜尋其結果。 [符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的點式資料轉譯為地圖上的符號。  接著會建立符號圖層，而資料來源會新增至符號層，然後新增至地圖。

第四個程式碼區塊會使用[服務模組](how-to-use-services-module.md)中的[SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams)方法。 它可讓您透過[取得搜尋模糊 REST API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)執行自由格式的文字搜尋, 以搜尋重點。 取得搜尋模糊 API 可以處理任何模糊輸入組合。 接著會使用 `geojson.getFeatures()` 方法，從回應中擷取 GeoJSON 功能集合並新增至資料來源，而自動使資料透過符號圖層呈現在地圖上。

程式碼的最後一個區塊會使用地圖的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 屬性，為地圖調整觀景窗界限。

系統會在地圖的就緒[事件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)接聽程式中建立並設定搜尋要求、資料來源、符號層和相機界限, 以確保在地圖完全載入後顯示結果。


## <a name="make-a-search-request-via-fetch-api"></a>透過 Fetch API 提出搜尋要求

<iframe height='500' scrolling='no' title='在地圖上顯示搜尋結果' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>在地圖上顯示搜尋結果</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中, 程式碼的第一個區塊會建立地圖物件, 並將驗證機制設定為使用存取權杖。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會建立一個 URL, 以向提出搜尋要求。 它也會建立兩個數組, 以儲存搜尋結果的界限和 pin。

第三個程式碼區塊會使用[FETCH API](https://fetch.spec.whatwg.org/)提出要求, 以[AZURE 地圖服務模糊搜尋 api](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)來搜尋感的重點。 模糊搜尋 API 可以處理任何模糊輸入組合。 然後, 它會處理並剖析搜尋回應, 並將結果釘選到 searchPins 陣列。

第四個程式碼區塊會使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 類別來建立資料來源物件，以及搜尋其結果。 [符號層](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)會使用文字或圖示，將包裝在 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的點式資料轉譯為地圖上的符號。 接著會建立符號圖層，而資料來源會新增至符號層，然後新增至地圖。

最後一個程式碼區塊會使用結果陣列建立 [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) 物件，然後使用地圖的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 來調整地圖的觀景窗界限。 然後, 它會轉譯結果針腳。

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
