---
title: 如何要求 Azure 地圖服務中的即時資料 |Microsoft Docs
description: 使用 Azure 地圖服務行動服務來要求即時資料。
author: walsehgal
ms.author: v-musehg
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4c53d1c1ffbc80e694a9a7b423b2aaf9c6d38b48
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914388"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>使用 Azure 地圖服務行動服務來要求即時資料

本文說明如何使用 Azure 地圖服務[行動服務](https://aka.ms/AzureMapsMobilityService)來要求即時傳輸資料。

在本文中，您將瞭解如何：


 * 針對抵達給定停止的所有行要求下一次即時抵達
 * 要求指定自行車銜接站的即時資訊。


## <a name="prerequisites"></a>必要條件

若要對 Azure 地圖服務公用傳輸 Api 進行任何呼叫，您需要地圖服務帳戶和金鑰。 如需建立帳戶的相關資訊，請遵循[管理帳戶](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account)中的指示，然後依照[取得主要金鑰](./tutorial-search-location.md#getkey)中的步驟來抓取您帳戶的主要訂用帳戶金鑰。

本文使用 [Postman 應用程式](https://www.getpostman.com/apps)來建置 REST 呼叫。 您可以使用您偏好的任何 API 開發環境。


## <a name="request-real-time-arrivals-for-a-stop"></a>要求停止的即時抵達

若要在特定的公用傳輸停止時要求即時抵達資料，您必須向 Azure 地圖服務[行動服務](https://aka.ms/AzureMapsMobilityService)的[即時抵達 API](https://aka.ms/AzureMapsMobilityRealTimeArrivals)提出要求。 您將需要**metroID**和**stopID**才能完成要求。 若要深入瞭解如何要求這些參數，請參閱我們的作法指南以[要求公用傳輸路由](https://aka.ms/AMapsHowToGuidePublicTransitRouting)。 

讓我們使用 "522" 作為我們的 metro 識別碼，也就是「西雅圖– Tacoma – Bellevue，WA」區域的 metro 識別碼，並使用停止識別碼「522---2060603」，也就是在 "Ne 24 日 St & 162nd 等中 Ne、Bellevue WA" 的匯流排停止。 若要在此停止時要求下一次即時抵達的後續五個即時抵達資料，請完成下列步驟：

1. 建立要在其中儲存要求的集合。 在 Postman 應用程式中，選取 [**新增**]。 在 [**建立新**視窗] 中，選取 [**集合**]。 將集合命名為，然後選取 [**建立**] 按鈕。

2. 若要建立要求，請再次選取 [**新增**]。 在 [**建立新**視窗] 中，選取 [**要求**]。 輸入要求的 [**要求名稱**]，選取您在上一個步驟中建立的集合做為儲存要求的位置，然後選取 [**儲存**]。

    ![在 Postman 中建立要求](./media/how-to-request-transit-data/postman-new.png)

3. 選取 [建立器] 索引標籤上的 [取得 HTTP] 方法，然後輸入下列 URL 來建立 GET 要求。

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. 成功要求之後，您會收到下列回應。  請注意，參數 ' scheduleType ' 會定義預估抵達時間是否以即時或靜態資料為基礎。

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>自行車銜接站的即時資料

Azure 地圖服務行動服務的[取得傳輸 Dock 資訊 API](https://aka.ms/AzureMapsMobilityTransitDock) ，可讓要求靜態和即時資訊，例如指定自行車或機車銜接站的可用性和空缺資訊。 我們會提出要求，以取得適用于自行車的銜接站的即時資料。

為了向「取得傳輸」 Dock 資訊 API 提出要求，您需要該工作站的**dockId** 。 您可以對「[取得附近的傳輸 API](https://aka.ms/AzureMapsMobilityNearbyTransit) 」進行搜尋要求，並將**objectType**參數設定為「bikeDock」，以取得 dock 識別碼。 請遵循下列步驟，取得適用于自行車的銜接站的即時資料。


### <a name="get-dock-id"></a>取得停駐識別碼

若要取得**dockID**，請遵循下列步驟向「取得附近傳輸」 API 提出要求：

1. 在 Postman 中，按一下 **新增要求** |  **取得要求**，並將其命名為 **取得 dock 識別碼**

2.  在 [建立器] 索引標籤上，選取 [**取得**HTTP 方法]，輸入下列要求 URL，然後按一下 [**傳送**]。
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. 成功要求之後，您會收到下列回應。 請注意，在回應中現在會有**識別碼**，稍後可在取得傳輸 DOCK 資訊 API 的要求中，將其用作查詢參數。

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 31,
                    "lastUpdated": "2019-09-07T00:55:19Z",
                    "operatorInfo": {
                        "id": "121---80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>取得即時自行車 dock 狀態

請遵循下列步驟向「取得傳輸 Dock」資訊 API 提出要求，以取得所選 Dock 的即時資料。

1. 在 Postman 中，按一下 [**新增要求** | ] [**取得要求**]，然後將 [名稱]**取得即時停駐資料**。

2.  在 [建立器] 索引標籤上，選取 [**取得**HTTP 方法]，輸入下列要求 URL，然後按一下 [**傳送**]。
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. 成功要求之後，您會收到下列結構的回應：

    ```JSON
    {
        "availableVehicles": 0,
        "vacantLocations": 31,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-09-07T00:55:19Z",
        "operatorInfo": {
            "id": "121---80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>後續步驟

瞭解如何使用行動服務要求傳輸資料：

> [!div class="nextstepaction"]
> [如何要求傳輸資料](how-to-request-transit-data.md)

探索 Azure 地圖服務行動服務 API 檔：

> [!div class="nextstepaction"]
> [行動服務 API 檔](https://aka.ms/AzureMapsMobilityService)
