---
title: 如何呈現在 Azure Maps 點陣地圖上的自訂資料 |Microsoft Docs
description: 將 Azure maps 點陣地圖上的自訂資料呈現。
author: walsehgal
ms.author: v-musehg
ms.date: 04/03/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a9fed8464bd19c4b8a32e37c8c97698f0a2d9503
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734316"
---
# <a name="render-custom-data-on-a-raster-map"></a>轉譯自訂點陣地圖上的資料

這篇文章說明如何使用[靜態映像服務](https://docs.microsoft.com/rest/api/maps/render/getmapimage)與映像組合功能，可讓在點陣地圖上的重疊。 映像組合包含能夠取得額外資料，例如自訂的圖釘、 標籤和幾何覆疊點陣 圖格上一步。

若要轉譯自訂圖釘、 標籤和幾何覆疊，您可以使用 Postman 應用程式。 您可以使用 Azure Maps[資料服務 Api](https://docs.microsoft.com/rest/api/maps/data)來儲存及呈現重疊。


## <a name="prerequisites"></a>必要條件

### <a name="create-an-azure-maps-account"></a>建立 Azure 地圖服務帳戶

若要完成這篇文章中的程序，您必須先[建立 Azure 地圖服務帳戶](how-to-manage-account-keys.md)S1 定價層中。

## <a name="render-pushpins-with-labels-and-a-custom-image"></a>呈現使用標籤與自訂映像的圖釘

> [!Note]
> 在本節中的程序需要 S0 或 S1 定價層中的 Azure 地圖服務帳戶。

Azure 地圖服務帳戶的 S0 層所支援單一執行個體的`pins`參數。 它可讓您呈現最多五個圖釘，指定在要求 URL 中，使用自訂映像。

若要轉譯使用標籤與自訂映像的圖釘，完成下列步驟：

1. 建立用來儲存要求的集合。 在 Postman 應用程式中，選取**新增**。 在 **新建**視窗中，選取**集合**。 命名集合，然後選取**建立** 按鈕。 

2. 若要建立要求，請選取**新增**一次。 在 **新建**視窗中，選取**要求**。 請輸入**要求名稱**圖釘，選取您在上一個步驟中建立做為要儲存要求，然後選取位置的集合**儲存**。
    
    ![在 Postman 中建立的要求](./media/how-to-render-custom-data/postman-new.png)

3. 選取產生器 索引標籤上的 GET HTTP 方法，並輸入下列 URL，以建立 GET 要求。

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    以下是產生的映像：

    ![自訂標籤圖釘](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>從 Azure 地圖服務資料儲存體取得資料

> [!Note]
> 在本節中的程序需要定價層 S1 中的 Azure 地圖服務帳戶。

您可以使用取得的路徑和 pin 碼的位置資訊[資料上傳 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)。 遵循下列步驟來上傳路徑和圖釘資料。

1. 在 Postman 應用程式中，開啟您在上一節中建立的集合中的新索引標籤。 選取產生器 索引標籤上的 POST HTTP 方法，並輸入下列 URL 來發出 POST 要求：

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. 在  **Params**索引標籤上，輸入下列索引鍵/值組，用於 POST 要求 URL。 取代`subscription-key`使用您 Azure 地圖服務的訂用帳戶金鑰的值。
    
    ![在 Postman 中的索引鍵/值參數](./media/how-to-render-custom-data/postman-key-vals.png)

3. 在 **主體**索引標籤上選取原始的輸入的格式，從下拉式清單中選擇 JSON 做為輸入的格式。 提供此 JSON 做為要上傳資料：
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. 選取 **傳送**並檢閱回應標頭。 位置標頭包含用來存取或下載資料以供未來使用的 URI。 其中也包含已上傳資料的唯一 `udId`。  

   ```HTTP
   https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

5. 使用`udId`收到來自資料上傳 API 來呈現在地圖上的功能值。 若要這樣做，開啟您在上一節中建立的集合中的新索引標籤。 選取產生器 索引標籤上的 GET HTTP 方法，並輸入此 URL 來提出 GET 要求：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    以下是回應映像：

    ![從 Azure 地圖服務資料儲存體取得資料](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>呈現多邊形色彩和不透明度

> [!Note]
> 在本節中的程序需要定價層 S1 中的 Azure 地圖服務帳戶。


您可以使用樣式修飾詞搭配[路徑參數](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)來修改多邊形的外觀。

1. 在 Postman 應用程式中，開啟您稍早建立的集合中的新索引標籤。 選取產生器 索引標籤上的 GET HTTP 方法，並輸入下列 URL，以設定要呈現的多邊形色彩和不透明度的 GET 要求：
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

    以下是回應映像：

    ![呈現不透明的多邊形](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>呈現的圓形和使用自訂標籤的圖釘

> [!Note]
> 在本節中的程序需要定價層 S1 中的 Azure 地圖服務帳戶。


您可以讓圖釘和其標籤放大或縮小使用`sc`刻度樣式修飾詞。 此修飾詞會小於或等於零的值。 1 的值是標準比例。 大於 1 的值會使圖釘變大，小於 1 的值會使它們變小。 如需有關樣式的修飾詞的詳細資訊，請參閱 <<c0> [ 靜態映像服務路徑參數](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)。


遵循下列步驟來呈現的圓形和使用自訂標籤的圖釘：

1. 在 Postman 應用程式中，開啟您稍早建立的集合中的新索引標籤。 選取產生器 索引標籤上的 GET HTTP 方法，並輸入此 URL 來提出 GET 要求：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    以下是回應映像：

    ![轉譯自訂圖釘的圓形](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>後續步驟


* 探索 [Azure 地圖服務 Get Map Image API](https://docs.microsoft.com/rest/api/maps/render/getmapimage) \(英文\) 文件。
* 若要深入了解 Azure 地圖服務的資料服務，請參閱[服務文件](https://docs.microsoft.com/rest/api/maps/data)。

