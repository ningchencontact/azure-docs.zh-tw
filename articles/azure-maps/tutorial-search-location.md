---
title: 使用 Azure 地圖服務進行搜尋 | Microsoft Docs
description: 使用 Azure 地圖服務來搜尋附近景點
author: walsehgal
ms.author: v-musehg
ms.date: 10/22/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e879e096fb990e4567b43b1938909449820edd42
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50412715"
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>使用 Azure 地圖服務來搜尋附近景點

本教學課程說明如何使用 Azure 地圖服務來設定帳戶，然後使用地圖服務 API 來搜尋景點。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立 Azure 地圖服務帳戶
> * 擷取地圖服務帳戶的主要金鑰
> * 使用地圖控制項 API 建立新的網頁
> * 使用地圖服務的搜尋服務來尋找附近景點

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>使用 Azure 地圖服務建立帳戶

使用下列步驟建立新的地圖服務帳戶：

1. 按一下 [Azure 入口網站](https://portal.azure.com)左上角的 [建立資源]。
2. 在 [搜尋 Marketplace] 方塊中，輸入 **Maps**。
3. 從 [結果] 中，選取 [地圖服務]。 按一下地圖下方顯示的 [建立] 按鈕。
4. 在 [建立地圖服務帳戶] 頁面上輸入下列值：
    * 新帳戶的 [名稱]。
    * 您想要使用於此帳戶的 [訂用帳戶]。
    * 此帳戶的 [資源群組] 名稱。 您可以選擇 [建立新的] 或 [使用現有的] 資源群組。
    * 選取 [資源群組位置]。
    * 閱讀 [授權] 和 [隱私權聲明]，然後選取核取方塊以接受條款。
    * 按一下 [ **建立** ] 按鈕。

    ![在入口網站中建立地圖服務帳戶](./media/tutorial-search-location/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>取得帳戶的主要金鑰

成功建立地圖服務帳戶後，擷取金鑰以便能查詢地圖服務 API。

1. 在入口網站中開啟地圖服務帳戶。
2. 在 [設定] 區段中，選取 [金鑰]。
3. 將 [主索引鍵] 複製到剪貼簿。 將其儲存在本機，以供本教學課程稍後使用。

    ![在入口網站中取得主索引鍵](./media/tutorial-search-location/get-key.png)

<a id="createmap"></a>

## <a name="create-a-new-map"></a>建立新的地圖

地圖控制項 API 是很方便的用戶端程式庫，可讓您輕鬆地將地圖服務整合到 Web 應用程式中。 它會隱藏裸機 REST 服務呼叫的複雜度，並使用可設定樣式和可自訂的元件提升生產力。 下列步驟顯示如何建立內嵌地圖控制項 API 的靜態 HTML 網頁。

1. 在您的本機機器上建立新檔案，並將其命名為 **MapSearch.html**。
2. 在檔案中新增下列 HTML 元件：

   ```HTML
   <!DOCTYPE html>
   <html>
   <head>
      <title>Map Search</title>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

      <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
      <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>

      <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
      <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>

      <script>      
         var map, datasource, client, popup;

         function GetMap(){
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

3. 在 HTML 檔案的 `GetMap` 函式中新增下列 JavaScript 程式碼。 將字串 **\<您的 Azure 地圖服務金鑰\>** 取代為您從地圖服務帳戶所複製的主要金鑰。

   ```JavaScript
   //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
   atlas.setSubscriptionKey('<Your Azure Maps Key>');

   //Initialize a map instance.
   map = new atlas.Map('myMap');
   ```

   此區段會為 Azure 地圖服務帳戶金鑰初始化地圖控制項 API。 **atlas** 是包含 API 和相關視覺效果元件的命名空間。 **atlas.Map** 可供控制視覺化互動式網路地圖。 

4. 將變更儲存至檔案，並在瀏覽器中開啟 HTML 網頁。 這是您可以藉由呼叫 **atlas.map** 和使用帳戶金鑰來建立的最基本地圖。

   ![檢視地圖](./media/tutorial-search-location/basic-map.png)

5. 在 `GetMap` 函式中，在地圖完成初始化之後新增下列 JavaScript 程式碼。 

   ```JavaScript
   //Wait until the map resources have fully loaded.
   map.events.add('load', function () {

      //Create a data source and add it to the map.
      datasource = new atlas.source.DataSource();
      map.sources.add(datasource);

      //Add a layer for rendering point data.
      var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
         iconOptions: {
            iconImage: 'pin-round-darkblue',
            anchor: 'center',
            allowOverlap: true
         }
      });
      map.layers.add(resultLayer);

      //Create a popup but leave it closed so we can update it and display it later.
      popup = new atlas.Popup();

      //Add a mouse over event to the result layer and display a popup when this event fires.
      map.events.add('mouseover', resultLayer, showPopup);
   });
   ```

   載入事件會新增至地圖，而地圖將在地圖資源已完全載入時引發。 在地圖載入事件處理常式中，會建立資料來源以儲存結果資料。 系統會建立符號圖層，並將其附加至資料來源。 此圖層會指定應如何呈現資料來源中的結果資料，在此案例中，會在結果座標中央加上深藍色圓釘圖示，而允許其他圖示重疊。 

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>新增搜尋功能

本節說明如何使用地圖服務的搜尋 API 在地圖上尋找景點。 這是針對開發人員所設計的 RESTful API，用於搜尋地址、景點及其他地理資訊。 搜尋服務會將經緯度資訊指派給指定的地址。 以下說明的**服務模組**可讓您使用地圖服務搜尋 API 來搜尋位置。

### <a name="service-module"></a>服務模組

1. 在地圖載入事件處理常式中，藉由新增下列 Javascript 程式碼將用戶端服務具現化。

    ```JavaScript
    //Create an instance of the services client.
     client = new atlas.service.Client(atlas.getSubscriptionKey());
    ```

2. 接著，新增下列指令碼區塊以建置搜尋查詢。 它會使用模糊搜尋服務，這是搜尋服務的基本搜尋 API。 模糊搜尋服務可處理大部分的模糊輸入，例如地址、地點和景點 (POI)。 此程式碼會在指定的半徑範圍內搜尋附近的加油站。 然後，回應會剖析為 GeoJSON 格式，並新增至資料來源，而使資料自動透過符號圖層呈現在地圖上。 指令碼的最後一個部分會透過地圖的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) 屬性使用結果的週框方塊來設定地圖相機檢視。 系統會加入邊框間距，以補償符號圖示的像素維度，因為週框方塊是根據座標而計算的。 
 
   ```JavaScript
   //Execute a POI search query then add the results to the map.
    client.search.getSearchPOI('gasoline station', {
        lat: 47.6292,
        lon: -122.2337,
        radius: 100000
    }).then(response => {
        //Parse the response into GeoJSON so that the map can understand.
        var geojsonResponse = new atlas.service.geojson.GeoJsonSearchResponse(response);
        var results = geojsonResponse.getGeoJsonResults();

        //Add the results to the data source so they can be rendered. 
        datasource.add(results);

        // Set the camera bounds
        map.setCamera({
            bounds: results.bbox,
            padding: 50
        });
    });
   ```
 
3. 儲存 **MapSearch.html** 檔案並重新整理瀏覽器。 您現在應該會看到地圖以西雅圖作為中心，並以藍色圖釘標示該區域內的汽油站位置。

   ![檢視地圖與搜尋結果](./media/tutorial-search-location/pins-map.png)

4. 您可以在瀏覽器中輸入下列 HTTPRequest，以查看地圖所呈現的原始資料。 將\<您的 Azure 地圖服務金鑰\>取代為主要金鑰。

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<Your Azure Maps Key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

至此，MapSearch 網頁可顯示模糊搜尋查詢所傳回的景點位置。 讓我們新增一些互動式功能和位置的詳細資訊。

## <a name="add-interactive-data"></a>新增互動式資料

我們到目前為止所建立的地圖只會查看搜尋結果的經緯度資料。 但如果您看一下地圖服務的搜尋服務所傳回的原始 JSON，就會看到它包含每個加油站的其他資訊，包括名稱和街道地址。 您可以使用互動式快顯方塊將該資料合併到地圖中。

1. 在查詢模糊搜尋服務的程式碼後面，在地圖載入事件處理常式中新增以下幾行程式碼。 這會建立快顯視窗的執行個體，並將滑鼠停駐事件新增至符號圖層。

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```
    
    API **atlas.Popup** 可提供錨定在地圖上所需位置的資訊視窗。 
      
2. 在 [指令碼] 標記中，在 `GetMap` 函式後面新增下列程式碼，以在快顯視窗中顯示滑鼠停駐的結果資訊。 

   ```JavaScript
   function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occured on.
        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = ['<div style="padding:5px"><div><b>', p.poi.name,
            '</b></div><div>', p.address.freeformAddress,
            '</div><div>', position[1], ', ', position[0], '</div></div>'];

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html.join(''),
            position: position
        });

        //Open the popup.
        popup.open(map);
   }
   ```

2. 儲存檔案並重新整理瀏覽器。 現在，當您將滑鼠停留在任何搜尋圖釘上時，瀏覽器中的地圖會顯示資訊快顯視窗。

    ![Azure 地圖控制項和搜尋服務](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 使用 Azure 地圖服務建立帳戶
> * 取得帳戶的主要金鑰
> * 使用地圖控制項 API 建立新網頁
> * 使用搜尋服務來尋找附近景點

您可以在這裡存取本教學課程的程式碼範例：

> [使用 Azure 地圖服務搜尋位置](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

[請參閱此處的實際範例](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

下一個教學課程會示範如何顯示兩個位置之間的路線。

> [!div class="nextstepaction"]
> [前往目的地的路線](./tutorial-route-location.md)
