---
title: 如何要求 Azure 地圖服務中的即時資料 |Microsoft Docs
description: 要求使用 Azure 地圖服務的行動服務的即時資料。
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: bb99a3c063f69aa5aeb00efdb51319a53d05b2d1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067613"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>要求使用 Azure 地圖服務的行動服務的即時資料

這篇文章會示範如何使用 Azure Maps[行動服務](https://aka.ms/AzureMapsMobilityService)要求即時傳輸資料。

這篇文章中您將了解如何：


 * 要求到達指定的停駐點的所有行的下一步 即時達到
 * 要求指定的自行車銜接站的即時資訊。


## <a name="prerequisites"></a>必要條件

若要讓 Azure 地圖服務公開金鑰傳輸的 Api 呼叫，您需要的地圖服務帳戶和金鑰。 如需建立帳戶和擷取金鑰的相關資訊，請參閱[如何管理 Azure 地圖服務帳戶和金鑰](how-to-manage-account-keys.md)。

本文使用 [Postman 應用程式](https://www.getpostman.com/apps)來建置 REST 呼叫。 您可以使用您偏好的任何 API 開發環境。


## <a name="request-real-time-arrivals-for-a-stop"></a>要求的即時抵達停駐點

若要要求針對特定的公用傳輸停止即時達到資料，您必須提出的要求[即時達到 API](https://aka.ms/AzureMapsMobilityRealTimeArrivals)的 Azure Maps[行動服務](https://aka.ms/AzureMapsMobilityService)。 您必須**metroID**並**stopID**來完成要求。 若要深入了解如何要求這些參數，請參閱我們的使用說明指南[要求公用的傳輸路由](https://aka.ms/AMapsHowToGuidePublicTransitRouting)。 

讓我們使用"522"作為我們 metro 識別碼，這是在停止識別碼 「 西雅圖 – Tacoma – Bellevue，WA 」 區域中，並使用停止識別碼"2060603 」，這是一個匯流排 metro"Ne 162nd Ave Ne，Bellevue WA 24 (& a) 」。 若要要求所有在此停止的下一步 即時達到接下來五個即時抵達的資料，請完成下列步驟：

1. 建立用來儲存要求的集合。 在 Postman 應用程式中，選取**新增**。 在 **新建**視窗中，選取**集合**。 命名集合，然後選取**建立** 按鈕。

2. 若要建立要求，請選取**新增**一次。 在 **新建**視窗中，選取**要求**。 請輸入**要求名稱**要求中，選取您在上一個步驟中建立做為要儲存要求，然後選取位置的集合**儲存**。

    ![在 Postman 中建立的要求](./media/how-to-request-transit-data/postman-new.png)

3. 選取產生器 索引標籤上的 GET HTTP 方法，並輸入下列 URL，以建立 GET 要求。

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=2060603&transitType=bus
    ```

4. 之後若要求成功，您會收到下列的回應。  請注意該參數 'scheduleType' 定義預估的抵達時間根據即時或靜態資料。

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 4,
                "scheduleType": "realTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 30,
                "scheduleType": "scheduledTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }


## Real-time data for bike docking station

The [Get Transit Dock Info API](https://aka.ms/AzureMapsMobilityTransitDock) of the Azure Maps Mobility Service, allows to request static and real-time information such as availability and vacancy information for a given bike or scooter docking station. We will make a request to get real-time data for a docking station for bikes.

In order to make a request to the Get Transit Dock Info API, you will need the **dockId** for that station. You can get the dock ID by making a search request to the [Get Nearby Transit API](https://aka.ms/AzureMapsMobilityNearbyTransit) and setting the **objectType** parameter to "bikeDock". Follow the steps below to get real-time data of a docking station for bikes.


### Get dock ID

To get **dockID**, follow the steps below to make a request to the Get Nearby Transit API:

1. In Postman, click **New Request** | **GET request** and name it **Get dock ID**.

2.  On the Builder tab, select the **GET** HTTP method, enter the following request URL, and click **Send**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. 之後若要求成功，您會收到下列的回應。 請注意，我們現在有**識別碼**在回應中，這可以用作稍後取得的傳輸停駐資訊 API 的要求中的查詢參數。

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 30,
                    "lastUpdated": "2019-05-21T20:06:59-04:00",
                    "operatorInfo": {
                        "id": "80",
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


### <a name="get-real-time-bike-dock-status"></a>取得即時的自行車停駐狀態

請遵循下列步驟來提出要求，以取得傳輸停駐資訊 API 來取得所選的停駐的即時資料。

1. 在 Postman 中，按一下**新要求** | **GET 要求**並將它命名**取得即時的停駐資料**。

2.  在產生器 索引標籤中，選取**取得**HTTP 方法，輸入下列的要求 URL，然後按一下**傳送**。
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. 之後若要求成功，您會收到下列結構的回應：

    ```JSON
    {
        "availableVehicles": 1,
        "vacantLocations": 29,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-05-21T20:26:47-04:00",
        "operatorInfo": {
            "id": "80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>後續步驟

了解如何要求使用行動服務的傳輸資料：

> [!div class="nextstepaction"]
> [如何要求傳輸資料](how-to-request-transit-data.md)

探索 Azure Maps 行動服務 API 文件：

> [!div class="nextstepaction"]
> [行動服務 API 文件](https://aka.ms/AzureMapsMobilityService)
