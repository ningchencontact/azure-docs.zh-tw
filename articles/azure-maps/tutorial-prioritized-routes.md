---
title: Azure 地圖服務的多個路線 | Microsoft Docs
description: 使用 Azure 地圖服務尋找不同行進模式的路線
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: b45394cedcb7bbcb46b9730b7e445b04c8cf4f18
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>使用 Azure 地圖服務尋找不同行進模式的路線

本教學課程說明如何使用 Azure 地圖服務帳戶和路線規劃服務來尋找景點路線 (依行進模式設定優先順序)。 您會在地圖上顯示兩條不同的路線，一條是汽車專用，一條則是可能會因為載送貨物的高度、重量或危險性而導致路線受限的卡車專用。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 使用地圖控制項 API 建立新的網頁
> * 在地圖上以視覺方式呈現流量
> * 建立會宣告行進模式的路線查詢
> * 在地圖上顯示多條路線

## <a name="prerequisites"></a>先決條件

在繼續之前，請遵循第一個教學課程中的步驟來[建立 Azure 地圖服務帳戶](./tutorial-search-location.md#createaccount)，並[取得帳戶的訂用帳戶金鑰](./tutorial-search-location.md#getkey)。 


## <a name="create-a-new-map"></a>建立新的地圖 
下列步驟顯示如何建立內嵌地圖控制項 API 的靜態 HTML 網頁。 

1. 在您的本機機器上建立新檔案，並將其命名為 **MapTruckRoute.html**。 
2. 在檔案中新增下列 HTML 元件：

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    
    <body>
        <div id="map"></div>
        <script>
            // Embed Map Control JavaScript code here
        </script>
    </body>

    </html>
    ```
    HTML 標頭會內嵌 CSS 的資源位置和 Azure 地圖服務程式庫的 JavaScript 檔案。 HTML 主體中的 script 區段將會包含地圖的內嵌 JavaScript 程式碼。
3. 在 HTML 檔案的 script 區塊中新增下列 JavaScript 程式碼。 將字串 **\<您的帳戶金鑰\>** 取代為您從地圖服務帳戶所複製的主要金鑰。

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    **atlas.Map** 是 Azure 地圖控制項 API 的元件，可供控制視覺化互動式網路地圖。

4. 儲存檔案並在瀏覽器中將其開啟。 至此，您已擁有可以進一步發展的基本地圖了。 

   ![檢視基本地圖](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>以視覺方式呈現流量

1. 如果您未告知地圖要聚焦在何處，您會看到全世界的視圖。 若要能夠檢視流量資料，請在地圖上設定中心點和縮放層級。 將宣告 `new atlas.Map` 的程式碼取代為下列 JavaScript 程式碼： 
    
    ```JavaScript
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey,
        center: [-118.2437,34.0522],
        zoom: 12
    });
    ```

    這段程式碼會設定地圖的中心點並宣告縮放層級，讓您可以預設聚焦在特定區域。 

1. 在地圖上新增流量顯示：

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    此程式碼將交通流量設定為 `relative`，這是相對於自由流量的道路速度。 您也可以將其設定為道路的 `absolute` 速度，或設定為 `relative-delay`，以顯示不同於自由流量的相對速度。 

2. 儲存 **MapTruckRoute.html** 檔案，並重新整理瀏覽器頁面。 您應該會看到洛杉磯的街道及其目前的流量資料。

   ![檢視流量地圖](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="set-start-and-end-points"></a>設定起點和終點

在此教學課程中，將起點設在西雅圖的一家虛構公司 Fabrikam，並將終點設在 Microsoft 辦公室。 

1. 新增下列 JavaScript 程式碼，以建立路線的起點和終點圖釘：

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.356099, 47.580045]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Fabrikam, Inc.",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Microsoft",
        icon: "pin-blue"
    });
    ```
    此程式碼會建立兩個 [GeoJSON 物件](https://en.wikipedia.org/wiki/GeoJSON)，代表路線的起點和終點。 

2. 新增下列 JavaScript 程式碼，以在地圖中新增起點和終點：

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 100
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    **map.setCameraBounds** 呼叫會根據起點和終點座標來調整地圖視窗。 **map.addPins** API 會在地圖控制項中以視覺化元件的形式新增景點。

3. 儲存檔案並重新整理瀏覽器，以查看地圖上顯示的圖釘。 即使您已宣告地圖並將中心點設在洛杉磯，**map.setCameraBounds** 仍會移動視圖以顯示起點和終點。 

   ![檢視有起點和終點的地圖](./media/tutorial-prioritized-routes/pins-map.png)


<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>轉譯依行進模式設定優先順序的路線

本節說明如何使用地圖服務的路線規劃服務 API，根據運輸模式，尋找從給定起點到目的地的多條路線。 路線規劃服務會提供 API 來規劃兩個位置之間「最快速」、「最短」、「最環保」或「驚心動魄」的路線，並將即時路況考慮進去。 它也可讓使用者使用 Azure 廣泛的歷史路況資料庫，並預測任何日期和時間的路線時間，以規劃日後的路線。 如需詳細資訊，請參閱[取得路線指示](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。


1. 首先，在地圖上新增一個圖層來顯示路線路徑 (亦即 linestring)。 此教學課程有兩條不同的路線，分別是 **car-route** 和 **truck-route**，且各有自己的樣式。 在 script 區塊中新增下列 JavaScript 程式碼：

    ```JavaScript
    // Place route layers on the map
    var carRouteLayerName = "car-route";
    map.addLinestrings([], {
        name: carRouteLayerName,
        color: "#B76DAB",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });

    var truckRouteLayerName = "truck-route";
    map.addLinestrings([], {
        name: truckRouteLayerName,
        color: "#2272B9",
        width: 9,
        cap: "round",
        join: "round",
        before: carRouteLayerName
    });
    ```

2. 在 script 區塊中新增下列 JavaScript 程式碼，以要求卡車路線並將結果顯示在地圖上：

    ```JavaScript
    // Perform a request to the route service and draw the resulting truck route on the map
    var xhttpTruck = new XMLHttpRequest();
    xhttpTruck.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: truckRouteLayerName
            });
        }
    };

    var truckRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    truckRouteUrl += "&api-version=1.0";
    truckRouteUrl += "&subscription-key=" + MapsAccountKey;
    truckRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
    truckRouteUrl += "&travelMode=truck";
    truckRouteUrl += "&vehicleWidth=2";
    truckRouteUrl += "&vehicleHeight=2";
    truckRouteUrl += "&vehicleLength=5";
    truckRouteUrl += "&vehicleLoadType=USHazmatClass2";

    xhttpTruck.open("GET", truckRouteUrl, true);
    xhttpTruck.send();
    ```
    此程式碼片段會建立 [XMLHttpRequest](https://xhr.spec.whatwg.org/)，並新增事件處理常式以剖析傳入的回應。 若是成功的回應，它會建立所傳回路線的座標陣列，並將它新增至地圖的 `truckRouteLayerName` 圖層。 
    
    此程式碼片段也會使用帳戶金鑰建立地圖服務路線規劃服務的查詢。 查詢中會包含起點座標、終點座標和選擇性參數，以指出該路線是重型卡車專用的。

2. 新增下列 JavaScript 程式碼以要求卡車路線並顯示結果：

    ```JavaScript
    // Perform a request to the route service and draw the resulting car route on the map
    var xhttpCar = new XMLHttpRequest();
    xhttpCar.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: carRouteLayerName
            });
        }
    };

    var carRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    carRouteUrl += "&api-version=1.0";
    carRouteUrl += "&subscription-key=" + MapsAccountKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    此程式碼片段會建立另一個 [XMLHttpRequest](https://xhr.spec.whatwg.org/)，並新增事件處理常式以剖析傳入的回應。 若是成功的回應，它會建立所傳回路線的座標陣列，並將它新增至地圖的 `carRouteLayerName` 圖層。 
    
    此程式碼片段也會使用帳戶金鑰建立地圖服務路線規劃服務的查詢。 查詢中包含起點座標和終點座標。 因為未提供任何額外的參數，路線規劃服務會預設使用「汽車」行進模式。 

3. 儲存 **MapTruckRoute.html** 檔案並重新整理瀏覽器，以觀察結果。 如果您成功連線到地圖服務的 API，您應該會看到類似下面的地圖。 

    ![使用 Azure 路線規劃服務設定優先順序的路線](./media/tutorial-prioritized-routes/prioritized-routes.png)

    卡車路線是較粗的藍色線，汽車路線則為較細的紫色線。 汽車路線會透過 I-90 穿越華盛頓湖，期間會經由隧道穿過住宅區底下，因此不得載運危險廢棄物貨物。 卡車路線 (指定了 USHazmatClass2 貨物類型) 則會正確引導使用不同的高速公路。 

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 使用地圖控制項 API 建立新的網頁
> * 在地圖上以視覺方式呈現流量
> * 建立會宣告行進模式的路線查詢
> * 在地圖上顯示多條路線

若要深入了解 Azure 地圖服務的涵蓋範圍和功能，請參閱[縮放層級和圖格格線](zoom-levels-and-tile-grid.md)以及其他概念文章。 

如需更多程式碼範例和互動式編碼體驗，請參閱[如何使用地圖控制項](how-to-use-map-control.md)以及其他使用說明指南。 
