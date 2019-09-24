---
title: 使用 Azure 地圖服務建立地理柵欄 | Microsoft Docs
description: 使用 Azure 地圖服務設定地理柵欄。
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a020ef91e52a5d801557399df827d3641bfb974e
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934194"
---
# <a name="set-up-a-geofence-by-using-azure-maps"></a>使用 Azure 地圖服務設定地理柵欄

本教學課程會引導您完成使用 Azure 地圖服務來設定地理柵欄的基本步驟。 我們在本教學課程中所講解的，是協助建築工地主任監視是否有潛在的危險設備移到指定建築區域外的案例。 建築工地會有昂貴的設備，且有嚴格的規範。 一般都會要求設備必須留置於建築工地內，未經許可不得移出。

我們將使用 Azure 地圖服務的 Data Upload API 儲存地理柵欄，並使用 Azure 地圖服務的地理柵欄 API 來檢查設備與地理柵欄的相對位置。 我們將使用 Azure 事件方格來串流地理柵欄結果，並設定以地理柵欄結果為基礎的通知。
若要深入了解事件方格，請參閱 [Azure 事件方格](https://docs.microsoft.com/azure/event-grid/overview)。
在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 使用 Data Upload API 在 Azure 地圖服務的資料服務中上傳地理柵欄區域。
> *   設定用來處理地理柵欄的事件方格。
> *   設定地理柵欄事件處理常式。
> *   使用 Logic Apps 設定回應地理柵欄事件的警示。
> *   使用 Azure 地圖服務的地理柵欄服務 API 來追蹤某項建構資產是否位於建築工地內。


## <a name="prerequisites"></a>必要條件

### <a name="create-an-azure-maps-account"></a>建立 Azure 地圖服務帳戶 

若要完成此教學課程中的步驟，請遵循[管理帳戶](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) \(部分機器翻譯\) 中的指示，建立使用 S1 定價層的 Azure 地圖服務帳戶訂用帳戶，並遵循[取得主要金鑰](./tutorial-search-location.md#getkey)中的步驟來取得適用於您帳戶的主要訂用帳戶金鑰。

## <a name="upload-geofences"></a>上傳地理柵欄

為了使用 Data Upload API 來上傳建築工地的地理柵欄，我們將使用 Postman 應用程式。 為了方便進行本教學課程，我們假設有一個整體的建築工地區域，而這是一個建築設備不應違反的硬參數。 超出此柵欄視同嚴重違規，將回報給營運經理知悉。 此外也可另行使用一組最佳化的柵欄，依據預定進度追蹤整體建築區域內的不同建築區域。 我們可以假設主要地理柵欄包含 subsite1，此工地已設定到期時間，而將在該時間之後到期。 您可以依據本身的需求建立更多巢狀地理柵欄。 例如，subsite1 可以是將在預定進度的第 1 到 4 週進行施工的工地，而 subsite2 則是在第 5 到 7 週施工的工地。 這類柵欄全都可以在專案開始之初載入作為單一資料集，用以根據時間和空間追蹤規則。 如需關於地理柵欄資料格式的詳細資訊，請參閱[地理柵欄 GeoJSON 資料](https://docs.microsoft.com/azure/azure-maps/geofence-geojson)。 如需關於將資料上傳至 Azure 地圖服務的詳細資訊，請參閱 [Data Upload API 文件](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)。

開啟 Postman 應用程式，並依照下列步驟使用 Azure 地圖服務的 Data Upload API 上傳建築工地地理柵欄。

1. 開啟 Postman 應用程式，然後按一下 [New] (新增) | [Create new] (新建)，然後選取 [Request] (要求)。 輸入上傳地理柵欄資料的要求名稱，選取集合或資料夾加以儲存，然後按一下 [Save] (儲存)。

    ![使用 Postman 上傳地理柵欄](./media/tutorial-geofence/postman-new.png)

2. 選取建立器索引標籤上的 POST HTTP 方法，然後輸入下列 URL 以提出 POST 要求。

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    URL 路徑中的 GEOJSON 參數代表上傳資料的資料格式。

3. 按一下 [Params]  (參數)，然後輸入下列金鑰/值組用於 POST 要求 URL。 將 subscription-key 值取代為您的 Azure 地圖服務主要訂用帳戶金鑰。
   
    ![金鑰-值參數 Postman](./media/tutorial-geofence/postman-key-vals.png)

4. 按一下 [Body]  (主體)，然後選取 [raw input format] (原始輸入格式)，並從下拉式清單中選擇 [JSON] 作為輸入格式。 提供下列 JSON 作為要上傳的資料：

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
              "expiredTime": "2019-01-15T00:00:00",
              "validityPeriod": [
                {
                  "startTime": "2019-01-08T01:00:00",
                  "endTime": "2019-01-08T17:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. 按一下 [Send] (傳送)，然後檢視回應標頭。 位置標頭會包含可用來存取或下載資料以供未來使用的 URI。 其中也包含已上傳資料的唯一 `udId`。

   ```HTTP
   https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

## <a name="set-up-an-event-handler"></a>設定事件處理常式

若要向營運經理發出關於進入和退出事件的通知，我們應建立用來接收通知的事件處理常式。

我們將建立兩個 [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) 服務來處理進入和退出事件。 我們也會在 Logic Apps 內建立由這些事件觸發的事件觸發程序。 其概念是要傳送警示；就此案例而言，也就是在每次有設備進入或退出建築工地時，傳送電子郵件給營運經理。 下圖說明如何建立適用於地理柵欄進入事件的邏輯應用程式。 您可以比照相同方式，為退出事件建立另一個應用程式。
您可以查看所有[支援的事件處理常式](https://docs.microsoft.com/azure/event-grid/event-handlers)以取得詳細資訊。

1. 在 Azure 入口網站中建立邏輯應用程式

   ![建立 Logic Apps](./media/tutorial-geofence/logic-app.png)

2. 選取 HTTP 要求觸發程序，然後選取 [傳送電子郵件] 作為 Outlook 連接器中的動作
  
   ![Logic Apps 結構描述](./media/tutorial-geofence/logic-app-schema.png)

3. 儲存邏輯應用程式以產生 HTTP URL 端點，並複製 HTTP URL。

   ![Logic Apps 端點](./media/tutorial-geofence/logic-app-endpoint.png)


## <a name="create-an-azure-maps-events-subscription"></a>建立 Azure 地圖服務事件訂用帳戶

Azure 地圖服務支援三種事件類型。 您可以在[這裡](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps)查看 Azure 地圖服務支援的事件類型。 我們將建立兩個不同的訂用帳戶，一個用於進入事件，一個用於退出事件。

請依照下列步驟，建立地理柵欄進入事件的事件訂用帳戶。 您可以用類似的方式訂閱地理柵欄退出事件。

1. 透過[此入口網站連結](https://ms.portal.azure.com/#@microsoft.onmicrosoft.com/dashboard/)瀏覽至您的 Azure 地圖服務帳戶，然後選取事件索引標籤。

   ![Azure 地圖服務事件](./media/tutorial-geofence/events-tab.png)

2. 若要建立事件訂用帳戶，請從事件頁面中選取事件訂用帳戶。

   ![Azure 地圖服務事件訂用帳戶](./media/tutorial-geofence/create-event-subscription.png)

3. 為事件訂用帳戶命名，並訂閱進入事件類型。 現在，選取為 Webhook 作為 [Endpoint Type] (端點類型)，並將您邏輯應用程式的 HTTP URL 端點複製到 [Endpoint] (端點) 中

   ![事件訂用帳戶](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>使用地理柵欄 API

您可以使用地理柵欄 API 來檢查某項**裝置** (設備是狀態的一部分) 是否位於地理柵欄內。 為了進一步了解地理柵欄 GET API， 我們對特定設備隨著時間而轉換工地的不同位置，來查詢此 API。 下圖說明特定建築設備的五個所在位置，每項設備各有唯一的**裝置識別碼**，且依時間先後順序觀察。 這五個位置會分別用來根據柵欄評估地理柵欄的進入或退出狀態變更。 發生狀態變更時，地理柵欄服務就會觸發事件，並由事件方格傳送至邏輯應用程式。 因此，營運經理將透過電子郵件收到對應的進入或退出通知。

> [!Note]
> 上述案例和行為以相同的**裝置識別碼**為基礎，因此反映出五個不同的位置，如下圖所示。

![地理柵欄地圖](./media/tutorial-geofence/geofence.png)

在 Postman 應用程式中，在您於上方建立的相同集合中開啟新的索引標籤。 選取建立器索引標籤上的 GET HTTP 方法：

以下是五個 HTTP GET 地理柵欄 API 要求，設備各有不同的對應位置座標，依時間先後順序觀察。 每個要求後面都有回應主體。
 
1. 位置 1：
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![地理柵欄查詢 1](./media/tutorial-geofence/geofence-query1.png)

   查看上述回應時，與主要地理柵欄間的距離若是負數，表示設備位於地理柵欄內；與子工地地理柵欄間的距離若是正數，表示設備位於子工地地理柵欄以外。 

2. 位置 2： 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![地理柵欄查詢 2](./media/tutorial-geofence/geofence-query2.png)

   仔細查看上述 JSON 回應時，您會發現設備位於子工地以外，但位於主要柵欄內。 這並不會觸發事件，也不會傳送電子郵件。

3. 位置 3： 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![地理柵欄查詢 3](./media/tutorial-geofence/geofence-query3.png)

   發生了狀態變更，此時設備位於主要和子工地地理柵欄內。 這時會發佈事件，並傳送通知電子郵件給營運經理。

4. 位置 4： 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![地理柵欄查詢 4](./media/tutorial-geofence/geofence-query4.png)

   仔細觀察對應的回應時您會發現，即使設備已退出子工地地理柵欄，仍未發佈任何事件。 如果您查看使用者在 GET 要求中指定的時間，您會發現子工地地理柵欄已超過此時間，而設備仍位於主要地理柵欄內。 您也會在回應主體中的 `expiredGeofenceGeometryId` 下方看到子工地地理柵欄的幾何識別碼。


5. 位置 5：
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![地理柵欄查詢 5](./media/tutorial-geofence/geofence-query5.png)

   您可以看到設備已退出主要建築工地地理柵欄。 這是嚴重違規，因此會發佈事件，並傳送重大警示電子郵件給營運經理。

## <a name="next-steps"></a>後續步驟

在本教學課程中您了解到，如何使用 Data Upload API 在 Azure 地圖服務的資料服務中上傳地理柵欄，藉以設定地理柵欄。 您也已了解如何使用 Azure 地圖服務的事件方格來訂閱及處理地理柵欄事件。 

* 請參閱[處理 Azure Logic Apps 中的內容類型](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)，了解如何使用 Logic Apps 剖析 JSON 以建置更複雜的邏輯。
* 若要深入了解事件方格中的事件處理常式，請參閱[事件方格中支援的事件處理常式](https://docs.microsoft.com/azure/event-grid/event-handlers)。
