---
title: 使用 Azure Notebooks (Python) 的電動車路線 | Microsoft Docs
description: 使用 Azure 地圖服務路線規劃 API 和 Azure Notebooks 的 EV 路線。
author: walsehgal
ms.author: v-musehg
ms.date: 10/01/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: c4b46bc952782fc7c9b56d6f0c049fe17b63d0f2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2019
ms.locfileid: "71836410"
---
# <a name="electric-vehicle-routing-using-azure-notebooks-python"></a>使用 Azure Notebooks (Python) 的電動車路線

Azure 地圖服務是原本就整合在 Azure 中的地理空間服務 API 產品組合，可讓開發人員、企業及 ISV 建立位置感知應用程式以及 IoT、行動、物流和資產追蹤解決方案。 您可以從 Python 和 R 等語言呼叫 Azure 地圖服務 REST API，以啟用地理空間資料分析和機器學習案例。 Azure 地圖服務提供一組健全的[路線規劃 API](https://docs.microsoft.com/rest/api/maps/route)，可讓使用者根據不同的條件 (例如車輛類型或可達區域) 計算數個資料點之間的路線。 在本教學課程中，我們將逐步解說一個案例，以協助車輛電量偏低的電動車駕駛者依據行車所需時間找出離他最近的充電站。

在本教學課程中，您將：

> [!div class="checklist"]
> * 在雲端的 [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) 上建立及執行 Jupyter Notebook
> * 在 Python 中呼叫 Azure 地圖服務 REST API
> * 根據電動車的耗電量模型搜尋可達範圍。
> * 搜尋位於可達範圍 (或等時線) 內的電動車充電站。
> * 在地圖上轉譯可達範圍界限和充電站。
> * 根據時間尋找最近的電動車充電站，並將路線視覺化。


## <a name="prerequisites"></a>必要條件 

若要完成本教學課程中的步驟，您必須先建立 Azure 地圖服務帳戶，並取得主要金鑰 (訂用帳戶金鑰)。 請遵循[管理帳戶](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) \(部分機器翻譯\) 中的指示，建立使用 S1 定價層的 Azure 地圖服務帳戶訂用帳戶，並遵循[取得主要金鑰](./tutorial-search-location.md#getkey)中的步驟來取得適用於您帳戶的主要訂用帳戶金鑰。

## <a name="create-an-azure-notebook"></a>建立 Azure Notebook

若要依照本教學課程操作，您必須建立 Azure Notebook 專案，並下載及執行 Jupyter Notebook 檔案。 此 Notebook 檔案包含 Python 程式碼，可實作本教學課程中的案例。 請依照下列步驟建立 Azure Notebook 專案，並將 Jupyter Notebook 文件上傳到其中。

1. 前往 [Azure Notebooks](https://notebooks.azure.com) 並登入。 如需詳細資訊，請參閱[快速入門](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks)。
2. 從您的公用設定檔頁面中，選取頁面頂端的 [我的專案]  。

    ![我的專案](./media/tutorial-ev-routing/myproject.png)

3. 在 [我的專案]  頁面上，選取 [新增專案]  。
 
   ![新增專案](./media/tutorial-ev-routing/create-project.png)

4. 在顯示的 [建立新專案]  快顯視窗中輸入下列資訊，然後按一下 [建立]  ：
    * 專案名稱
    * 專案識別碼
 
    ![建立專案](./media/tutorial-ev-routing/create-project-window.png)

5. 專案建立後，請從 [Azure 地圖服務 Jupyter Notebook 存放庫](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)下載 [Jupyter Notebook 文件檔案](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb)。 

6. 從 [我的專案]  頁面上的專案清單中選取您的專案，然後按一下 [上傳]  以上傳 Jupyter Notebook 文件檔案。 從您的電腦上傳檔案，然後按一下 [完成]  。

    ![上傳 Notebook](./media/tutorial-ev-routing/upload-notebook.png)

7. 成功上傳時，您會在專案頁面中看到您的檔案。 按一下 Notebook 檔案，將其開啟為 Jupyter Notebook。

為了進一步了解在 Notebook 檔案中實作的功能，建議您在 Notebook 中就個別資料格逐一執行程式碼。 您可以按一下 Notebook 應用程式頂端的 [執行]  按鈕，以執行每個資料格中的程式碼。

  ![執行](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>安裝專案層級套件

若要在 Notebook 中執行程式碼，您必須在專案層級安裝套件。 請依照下列步驟安裝必要的套件：

1. 從 [Azure 地圖服務 Jupyter Notebook 存放庫](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)下載 ["requirements.txt"](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) 檔案，並將其上傳至您的專案。
2. 在專案儀表板上，選取 [專案設定]  。 
3. 在出現的快顯視窗中選取 [環境]  索引標籤，然後選取 [新增]  。
4. 在 [環境設定步驟]  下方， 
    * 在第一個下拉式控制項中選擇 **Requirements.txt**。
    * 在第二個下拉式控制項中，選擇您的 "requirements.txt" 檔案。
    * 在第三個下拉式控制項中選擇 [Python 3.6 版]，作為 Python 版本。
7. 選取 [儲存]  。

    ![安裝套件](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-required-modules-and-frameworks"></a>載入必要的模組和架構

執行下列指令碼，以載入所有必要的模組和架構。

```python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-for-reachable-range-boundary"></a>要求可達範圍界限

在我們的案例中，某家快遞公司的車隊編制了一些電動車。 在一天之中，電動車必須能夠在外充電而不需返回倉庫。 每當電動車目前的剩餘電量低於一小時的續航力時 (電動車的電量偏低)，我們就需要搜尋位於可達範圍內的一組充電站，並取得該範圍的界限資訊。 由於公司偏好使用兼顧經濟效益和速度的路線，因此要求的 routeType 為「環保」。 下列指令碼會使用車輛耗電量模型的參數來呼叫 Azure 地圖服務路線規劃服務的[取得路線範圍 API](https://docs.microsoft.com/rest/api/maps/route/getrouterange)，並剖析其回應，以建立 geojson 格式的多邊形物件，代表車輛的最大可達範圍。

執行下列資料格中的指令碼，以取得電動車可達範圍的界限。

```python
subscriptionKey = "Your Azure Maps primary subscription key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get bounds for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-electric-vehicle-charging-stations-within-reachable-range"></a>搜尋位於可達範圍內的電動車充電站

取得電動車的可達範圍 (等時線) 後，我們就可以搜尋位於該範圍內的充電站。 下列指令碼會呼叫 Azure 地圖服務 [Post 幾何內搜尋 API](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)，以搜尋位於車輛最大可達範圍界限內的電動車充電站，然後將回應剖析為可達位置的陣列。

執行下列指令碼，以搜尋位於可達範圍內的電動車充電站。

```python
# Search for EV stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-reachable-range-and-charging-points-to-azure-maps-data-service"></a>將可達範圍和充電地點上傳至 Azure 地圖服務資料服務

為了在地圖上將電動車最大可達範圍的充電站和界限視覺化，我們必須使用[資料上傳 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)，將界限資料和充電站資料以 geojson 物件的形式上傳至 Azure 地圖服務資料服務。 

執行下列兩個資料格，將界限和充電地點資料上傳至 Azure 地圖服務資料服務。

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload range data to Azure Maps data service.
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload EV charging stations data to Azure Maps data service.
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-charging-stations-and-reachable-range-on-map"></a>在地圖上轉譯充電站和可達範圍

將資料上傳至資料服務後，我們現在將執行下列指令碼以呼叫 Azure 地圖服務[取得地圖影像服務](https://docs.microsoft.com/rest/api/maps/render/getmapimage)，在靜態地圖影像上轉譯充電地點和最大可達界限。

```python
# Get bounds for bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render range and EV charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![位置範圍](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station-to-stop"></a>尋找最適合前往的充電站

當我們取得可達範圍內的所有潛在充電站之後，我們想要知道哪個充電站可在最短的時間內抵達。 下列指令碼會呼叫 Azure 地圖服務[矩陣路線規劃 API](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)，而傳回指定車輛位置前往每個給定充電站位置的行車時間和距離。 下一個資料格中的指令碼會剖析回應，以依時間取得最近的可達充電站所在的位置。

執行下列資料格，以找出可在最短時間內抵達的最近可達充電站。

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to every given charging station location.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-route-to-the-closest-charging-station"></a>計算最近充電站的路線

現在我們已找到最近的充電站，接下來我們將呼叫[取得路線指示 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)，以要求從電動車目前所在位置前往充電站的詳細路線。

執行下列資料格中的指令碼以取得路線，並剖析回應，以建立代表路線的 geojson 物件。

```python
# Get route from current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>將路線視覺化

為了將路線視覺化，我們會先使用 Azure 地圖服務[資料上傳 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)，將路線資料以 geojson 物件的形式上傳至 Azure 地圖服務資料服務中。 然後，呼叫轉譯服務[取得地圖影像 API](https://docs.microsoft.com/rest/api/maps/render/getmapimage) 以在地圖上轉譯路線，並將其視覺化。

執行下列指令碼，以取得地圖上轉譯之路線的影像。

```python
# Upload route data to Azure data service.
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get bounds for bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

#Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![路由](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何直接呼叫 Azure 地圖服務 REST API，並使用 Python 將 Azure 地圖服務資料視覺化。

若要探索本教學課程中使用的 Azure 地圖服務 API，請參閱：

* [取得路線範圍](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Post 幾何內搜尋](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [資料上傳](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [轉譯 - 取得地圖影像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Post 路線矩陣](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)
* [取得路線指示](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

如需 Azure 地圖服務 REST API 的完整清單，請參閱：

* [Azure 地圖服務 REST API](https://docs.microsoft.com/azure/azure-maps/#reference)

若要深入了解 Azure Notebooks，請參閱：

* [Azure Notebooks](https://docs.microsoft.com/azure/notebooks)