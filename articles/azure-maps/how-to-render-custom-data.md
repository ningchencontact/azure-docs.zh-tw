---
title: 如何在 Azure 地圖服務中的點陣地圖上轉譯自訂資料 | Microsoft Docs
description: 在 Azure 地圖服務中的點陣地圖上轉譯自訂資料。
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9b3c0f7b1ff56cb269f6852be8fd2affeca8b8f1
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56119037"
---
# <a name="render-custom-data-on-raster-map"></a>在點陣地圖上轉譯自訂資料

本文說明如何搭配影像組合功能使用[靜態映像服務](https://docs.microsoft.com/rest/api/maps/render/getmapimage) \(英文\) 來在點陣地圖上允許重疊。 影像組合包括取回點陣圖格，以及自訂圖釘、標籤及幾何重疊等額外資料的能力。 為了轉譯自訂圖釘、標籤及幾何，我們將會使用 postman 應用程式。 開啟 Postman 應用程式，按一下 [New] (新增) | [Create New] (新建)，選取集合或資料夾來儲存它，然後按一下 [Save] (儲存)。

我們將會使用 Azure 地圖服務 [Data Service API](https://docs.microsoft.com/rest/api/maps/data) \(英文\) 來儲存並轉譯重疊。 


## <a name="prerequisites"></a>先決條件

### <a name="create-an-azure-maps-account"></a>建立 Azure 地圖服務帳戶 

若要遵循本指南中的步驟，您必須先查看[管理帳戶和金鑰](how-to-manage-account-keys.md)來建立及管理具有 S1 定價層的訂用帳戶。

## <a name="render-pushpins-with-labels-and-custom-image"></a>搭配標籤和自訂影像轉譯圖釘

> [!Note]
> 此範例需要具有定價層 S0 或 S1 的 Azure 地圖服務帳戶。 

Azure 地圖服務帳戶 S0 SKU 僅支援單一執行個體的 `pins` 參數，可讓使用者轉譯最多 5 個搭配自訂影像在 URL 要求中指定的圖釘。

若要轉譯搭配標籤和自訂影像的圖釘，請遵循下列步驟：

1. 開啟 Postman 應用程式，然後按一下 [新增] (New) | [新建] (Create new)，然後選取 [Request] (要求)。 為轉譯圖釘輸入要求名稱，選取集合或資料夾來儲存它，然後按一下 [Save] (儲存)。
    
    ![使用 Postman 上傳地理柵欄](./media/tutorial-geofence/postman-new.png)

2. 選取建立器索引標籤上的 GET HTTP 方法，然後輸入下列 URL 來提出 GET 要求。

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    下面是您會取得的回應影像。

    ![搭配標籤轉譯自訂圖釘](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>從 Azure 地圖服務資料儲存體取得資料

> [!Note]
> 此範例需要具有定價層 S1 的 Azure 地圖服務帳戶。

路徑和圖釘位置資訊也可以透過 [Data Upload API](https://docs.microsoft.com/rest/api/maps/mapdata/upload) 取得。 遵循下列步驟來上傳路徑和圖釘資料。

1. 在 Postman 應用程式中，在您於上方所建立的相同集合中開啟新的索引標籤。 選取建立器索引標籤上的 POST HTTP 方法，然後輸入下列 URL 來提出 POST 要求：

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. 按一下 [Params] (參數)，然後輸入下列金鑰/值組來用於 POST 要求 URL。 以您的 Azure 地圖服務訂用帳戶金鑰取代 subscription-key 值。
    
    ![金鑰-值參數 Postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. 按一下 [Body] \(主體\)，然後選取 [raw input format] (原始輸入格式)，並從下拉式清單中選擇 [JSON] 作為輸入格式。 提供下列 JSON 作為要上傳的資料：
    
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

4. 按一下 [Send] (傳送)，然後檢視回應標頭。 位置標頭會包含可用來存取或下載資料以供未來使用的 URI。 它也包含已上傳資料的唯一 `udId`。   

  ```HTTP
  https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
  ```



5. 使用從 Data Upload API 接收到的 `udid` 值來在地圖上轉譯特徵。 若要這麼做，請在您於上方所建立的相同集合中開啟新的索引標籤。 選取建立器索引標籤上的 GET HTTP 方法，然後輸入下列 URL 來提出 GET 要求：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. 回應影像看起來應該如下所示：

    ![轉譯已上傳的資料](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-polygon-with-color-and-opacity"></a>搭配色彩和不透明度轉譯多邊形

> [!Note]
> 此範例需要具有定價層 S1 的 Azure 地圖服務帳戶。

您可以使用樣式修飾詞搭配[路徑參數](https://docs.microsoft.com/rest-staging/api/maps/render/getmapimage#uri-parameters)來修改多邊形的外觀。

1. 在 Postman 應用程式中，在您於上方所建立的相同集合中開啟新的索引標籤。 選取建立器索引標籤上的 GET HTTP 方法，然後輸入下列 URL 來提出 GET 要求，以轉譯具有色彩和不透明度的多邊形：
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

回應影像看起來應該如下所示：

![轉譯不透明多邊形](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-polygon-with-circle-and-push-pins-with-custom-labels"></a>搭配圓形和圖釘及自訂標籤轉譯多邊形

> [!Note]
> 此範例需要具有定價層 S1 的 Azure 地圖服務帳戶。

您可以使用 'sc’ 比例樣式修飾詞來讓圖釘和其標籤變得更大或更小。 此值必須大於零。 1 的值是標準比例。 大於 1 的值會使圖釘變大，小於 1 的值會使它們變小。 如需樣式修飾詞的詳細資訊，請參閱[靜態影像服務路徑參數](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters) \(英文\)。

請遵循下列步驟來搭配圓形和圖釘及自訂標籤轉譯多邊形：

1. 在 Postman 應用程式中，在您於上方所建立的相同集合中開啟新的索引標籤。 選取建立器索引標籤上的 GET HTTP 方法，然後輸入下列 URL 來提出 GET 要求：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

回應影像看起來應該如下所示：

![轉譯具自訂圖釘的圓形](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>後續步驟

* 探索 [Azure 地圖服務 Get Map Image API](https://docs.microsoft.com/rest/api/maps/search) \(英文\) 文件。
* 若要深入了解 Azure 地圖服務資料服務功能，請參閱[服務文件](https://docs.microsoft.com/rest/api/maps/data) \(英文\)。