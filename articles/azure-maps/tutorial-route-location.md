---
title: 使用 Azure 地圖服務尋找路線 | Microsoft Docs
description: 使用 Azure 地圖服務的景點路線
author: walsehgal
ms.author: v-musehg
ms.date: 11/14/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 7b5b82e80ab4998f7cd106f469bf7ac8e271285d
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588359"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>使用 Azure 地圖服務的景點路線

本教學課程說明如何使用 Azure 地圖服務帳戶和路線規劃服務 SDK，來尋找景點路線。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 使用地圖控制項 API 建立新的網頁
> * 設定地址座標
> * 查詢路線規劃服務，以了解如何前往景點

## <a name="prerequisites"></a>必要條件

在繼續之前，請遵循上一個教學課程中的步驟來[建立 Azure 地圖服務帳戶](./tutorial-search-location.md#createaccount)，並[取得帳戶的訂用帳戶金鑰](./tutorial-search-location.md#getkey)。

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>建立新的地圖

下列步驟顯示如何建立內嵌地圖控制項 API 的靜態 HTML 網頁。

1. 在您的本機機器上建立新檔案，並將其命名為 **MapRoute.html**。
2. 在檔案中新增下列 HTML 元件：

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
        
        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>
        
        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
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
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```
    
    請注意，HTML 標頭包含 Azure 地圖控制項程式庫所裝載的 CSS 和 JavaScript 資源檔案。 請注意頁面本文上的 `onload` 事件，此事件在頁面本文載入時將會呼叫 `GetMap` 函式。 此函式會包含內嵌的 JavaScript 程式碼，以便存取 Azure 地圖服務 API。 

3. 在 `GetMap` 函式中新增下列 JavaScript 程式碼。 將字串 **\<您的 Azure 地圖服務金鑰\>** 取代為您從地圖服務帳戶所複製的主要金鑰。

    ```JavaScript
    //Add your Azure Maps subscription key to the map SDK. 
    atlas.setSubscriptionKey('<Your Azure Maps Key>');

    //Initialize a map instance.
    map = new atlas.Map('myMap');
    ```

    `atlas.Map` 是 Azure 地圖控制項 API 的元件，可供您控制視覺化互動式網路地圖。

4. 儲存檔案並在瀏覽器中將其開啟。 至此，您已擁有可以進一步發展的基本地圖了。

   ![檢視基本地圖](./media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>定義呈現路線的方式

在本教學課程中，將使用路線起點和終點的符號圖示以及路線路徑的線條來呈現簡易路線。

1. 在 GetMap 函式中，在地圖完成初始化之後新增下列 JavaScript 程式碼。

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
            lineJoin: 'round',
            lineCap: 'round',
            filter: ['==', '$type', 'LineString']
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
           },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['==', '$type', 'Point']
        }));
    });
    ```
    
    載入事件會新增至地圖，而地圖將在地圖資源已完全載入時引發。 在地圖載入事件處理常式中，會建立資料來源以儲存路線以及起點和終點。 系統會建立線條圖層並將其附加至資料來源，以定義呈現路線的方式。 路線將呈現為寬度為 5 像素的鮮明藍色，並採用圓角的線條聯合和線蓋。 此時會新增一個篩選條件，以確定此圖層僅呈現 GeoJSON LineString 資料。 將圖層新增至地圖時，會傳入值為 `'labels'` 的第二個參數，而指定要將此圖層呈現在地圖標籤下方。 這樣可以確保路線不會遮住道路標籤。 系統會建立符號圖層，並將其附加至資料來源。 此圖層會指定起點和終點的呈現方式，而此案例中已新增運算式，用以從每個點物件的屬性中擷取圖示影像和文字標籤資訊。 
    
2. 在此教學課程中，將起點設在 Microsoft，並將終點設在西雅圖的加油站。 在地圖載入事件處理常式中，新增下列程式碼。

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: 'Microsoft',
        icon: 'pin-round-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: 'Contoso Oil & Gas',
        icon: 'pin-blue'
    });    
    ```

    此程式碼會建立兩個 [GeoJSON 點物件](https://en.wikipedia.org/wiki/GeoJSON)，代表路線的起點和終點。 `title` 和 `icon` 屬性會新增至每個點。
    
3. 接著，請新增下列 JavaScript 程式碼，以在地圖中新增起點和終點的圖釘：

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);
    
    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```
    
    起點和終點會新增至資料來源。 系統會使用 `atlas.data.BoundingBox.fromData` 函式計算起點和終點的週框方塊。 此週框方塊會用來透過 **map.setCamera** 函式設定起點和終點的地圖相機檢視。 系統會加入邊框間距，以補償符號圖示的像素維度。

3. 儲存 **MapRoute.html** 檔案並重新整理瀏覽器。 現在地圖會以西雅圖作為中心，而且您會看到以圓形藍色圖釘標示的起點，和以藍色圖釘標示的終點。

   ![檢視標有起點和終點的地圖](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>取得指示

本節說明如何使用地圖服務的路線規劃服務 API，尋找從給定起點到終點的路線。 路線規劃服務會提供 API 來規劃兩個位置之間「最快速」、「最短」、「最環保」或「驚心動魄」的路線。 它也可讓使用者使用 Azure 廣泛的歷史路況資料庫，並預測任何日期和時間的路線時間，以規劃日後的路線。 如需詳細資訊，請參閱[取得路線指示](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。 以下所有功能都應該新增至**地圖載入 eventListener 內**，以確保會在地圖完整載入之後載入。

1. 藉由在地圖載入事件處理常式中新增下列 Javascript 程式碼，將服務用戶端具現化。

    ```JavaScript
    //If the service client hasn't already been created, create an instance.
    if (!client) {
        client = new atlas.service.Client(atlas.getSubscriptionKey());
    }
    ```

2. 新增下列程式碼區塊，以建構路由查詢字串。
    ```JavaScript
    //Create the route request with the query being the start and end point in the format 'startLongitude,startLatitude:endLongitude,endLatitude'.
    var routeQuery = startPoint.geometry.coordinates[1] +
        ',' +
        startPoint.geometry.coordinates[0] +
        ':' +
        endPoint.geometry.coordinates[1] +
        ',' +
        endPoint.geometry.coordinates[0];
    ```

3. 若要取得路線，請將下列程式碼區塊新增至指令碼。 它會透過 [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest) 方法來查詢 Azure 地圖服務路線服務，然後使用 [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routegeojson?view=azure-iot-typescript-latest) 將回應剖析為 GeoJSON 格式。 接著，它會新增路線以回應資料來源，繼而自動呈現在地圖上。

    ```JavaScript
    //Execute the car route query then add the route to the map once a response is received.
    client.route.getRouteDirections(routeQuery).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Add the route line to the data source.
        datasource.add(geoJsonResponse.getGeoJsonRoutes().features[0]);
    });
    ```

5. 儲存 **MapRoute.html** 檔案並重新整理網頁瀏覽器。 如果您成功連線到地圖服務的 API，您應該會看到類似下面的地圖。

    ![Azure 地圖控制項和路線規劃服務](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 使用地圖控制項 API 建立新的網頁
> * 設定地址座標
> * 查詢路線規劃服務以了解如何前往景點

您可以在這裡存取本教學課程的程式碼範例：

> [使用 Azure 地圖服務尋找路線](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

[請參閱此處的實際範例](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

下一個教學課程會示範如何建立具有限制 (例如，行進模式或貨物類型) 的路線查詢 ，然後在同一個地圖上顯示多條路線。

> [!div class="nextstepaction"]
> [尋找不同行進模式的路線](./tutorial-prioritized-routes.md)
