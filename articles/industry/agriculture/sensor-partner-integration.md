---
title: 感應器合作夥伴整合
description: 描述感應器合作夥伴整合
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e7de815b7254fb071b3094f9ae636b712b38684b
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797682"
---
# <a name="sensor-partner-integration"></a>感應器合作夥伴整合
本文提供 Azure FarmBeats **Translator**元件的相關資訊。

藉由使用此元件，合作夥伴可以開發與 FarmBeats 整合的感應器，方法是利用我們的 API，並將客戶裝置資料和遙測傳送至 FarmBeats 資料中樞。 資料會使用 FarmBeats 加速器進行視覺化。 資料可以用於資料融合，以及用來建立機器語言/人工智慧模型。

## <a name="link-farmbeats-account"></a>連結 FarmBeats 帳戶

客戶購買並部署裝置/感應器之後，即可在裝置合作夥伴的 SaaS 入口網站（軟體即服務）上存取裝置資料和遙測。 裝置合作夥伴必須讓客戶將其帳戶連結至其在 Azure 上的 FarmBeats 實例。 下列認證必須以 customer/SI 填寫：

   - 顯示名稱（用於定義此整合名稱之使用者的選擇性欄位）
   - API 端點
   - 租用戶識別碼
   - 用戶端識別碼
   - 用戶端密碼
   - EventHub 連接字串
   - 開始日期

   > [!NOTE]
   > 開始日期：啟用歷程記錄資料摘要，亦即使用者所指定日期的資料。

## <a name="unlink-farmbeats"></a>取消連結 FarmBeats

客戶能夠取消連結現有的 FarmBeats 整合。 取消連結 FarmBeats 不應刪除在客戶資料中樞內建立的任何裝置/感應器中繼資料。 取消連結會執行下列動作：

   - 停止遙測流量。
   - 刪除和清除裝置夥伴上的整合認證。

## <a name="edit-farmbeats-integration"></a>編輯 FarmBeats 整合

客戶可以編輯 FarmBeats 整合。 編輯的主要案例是當用戶端密碼或連接字串因為到期而變更時，在此情況下，customer 只能編輯下欄欄位。

   - 顯示名稱（如果適用）
   - 用戶端密碼（應顯示為 "2x8 * * * * * * * * * * *" 格式或顯示/隱藏功能，而不是純文字）
   - 連接字串（應該顯示為 "2x8 * * * * * * * * * * *" 格式或顯示/隱藏功能，而不是純文字）

   > [!NOTE]
   > [編輯] 不應中斷中繼資料物件的建立。

## <a name="view-last-telemetry-sent"></a>查看上次傳送的遙測

您可以查看上次**傳送遙測**資料的時間戳記。 這是最新遙測成功傳送至 FarmBeats 的時間。

## <a name="translator-development"></a>Translator 開發

**以 Rest API 為基礎的整合**

FarmBeats 的感應器資料整合功能會透過 REST API 公開。 功能包括元資料定義、裝置/感應器布建、裝置和感應器管理。

**遙測內嵌**

遙測資料會對應至在 Azure 事件中樞上發佈以進行處理的標準訊息。 Azure EventHub 是一種服務，可讓您從連線的裝置和應用程式內嵌即時資料（遙測）。

**API 開發**

這些 Api 包含 Swagger 技術檔。 如需 Api 及其對應的要求/回應的詳細資訊，請參閱[Swagger](https://aka.ms/FarmBeatsDatahubSwagger)

**驗證**

FarmBeats 會利用 Microsoft Azure 的 Active Directory 驗證。 Azure App Service 提供內建的驗證和授權支援。

如需詳細資訊，請參閱 [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)。

FarmBeats 資料中樞會使用持有人驗證，其需要下列認證：
   - 用戶端識別碼
   - 用戶端密碼
   - 租用戶識別碼

呼叫者可以使用上述認證來要求存取權杖，這必須在標頭區段的後續 API 要求中傳送，如下所示：

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

以下是提供存取權杖的範例 Python 程式碼，可用於後續對 FarmBeats 的 API 呼叫： 

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```


**HTTP 要求標頭**

以下是對 FarmBeats 資料中樞進行 API 呼叫時，需要指定的最常見要求標頭：


**標頭** | **描述和範例**
--- | ---
Content-Type | 適用于 FarmBeats 資料中樞 Api 格式的要求格式（內容類型： application/<format>）為 json。 內容類型： application/json
授權 | 指定進行 API 呼叫授權所需的存取權杖：持有人 < 存取權杖 >
Accept | 回應格式。 針對 FarmBeats 資料中樞 Api，格式為 json Accept： application/json

**API 要求**

若要進行 REST （具像狀態傳輸） API 要求，請將 HTTP （GET、POST 或 PUT）方法、API 服務的 URL、URI （統一資源識別項），結合到要查詢、提交資料至、更新或刪除的資源，以及一個或多個 HTTP 要求標題. API 服務的 URL 是客戶提供的 API 端點。 以下是範例： HTTPs://\<yourdatahub-網站名稱 >. azurewebsites. net

（選擇性）您可以在 GET 呼叫上包含查詢參數來篩選、限制的大小，以及排序回應中的資料。

以下範例要求是取得裝置的清單：

```
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```
大部分的 GET、POST 和 PUT 呼叫都需要 JSON 要求主體。

以下範例要求是建立裝置（此範例具有具有要求本文的輸入 json）。

```
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>資料格式

JSON （JavaScript 物件標記法）是與語言無關的通用資料格式，可提供任意資料結構的簡單文字標記法。 如需詳細資訊，請參閱[json.org](http://json.org)。

## <a name="metadata-specifications"></a>中繼資料規格

FarmBeats 資料中樞具有下列 Api，可讓裝置合作夥伴建立和管理裝置/感應器中繼資料。  

- /**devicemodel 傳遞**-裝置型號會對應至裝置的中繼資料，例如裝置的 [製造商]、[閘道] 或 [節點] 類型。  
- /**裝置**裝置會對應至存在於伺服器陣列中的實體裝置。
- /**SensorModel** -感應器型號對應于感應器的中繼資料，例如製造商、感應器類型、類比或數位、感應器量值（例如，環境溫度、壓力等等）。
- /**感應器**-感應器會對應到記錄值的實體感應器。 感應器通常會連接到裝置識別碼為的裝置。

  裝置型號| Devicemodel 傳遞會對應至裝置的中繼資料，例如 [製造商]、[閘道] 或 [節點] 的裝置類型。
  --- | ---
  類型（節點、閘道）  | 1 顆星 |
  製造商  | 2 顆星 |
  ProductCode  | 裝置產品代碼或型號名稱/號碼。 例如，EnviroMonitor # 6800 |
  連接埠  | 埠名稱和類型（數位/類比）  |
  名稱  | 用來識別資源的名稱。 例如，模型名稱/產品名稱 |
  說明  | 提供模型的有意義描述 |
  屬性  | 製造商提供的其他屬性 |
  **裝置** | **裝置對應至存在於伺服器陣列中的實體裝置。每個裝置都有唯一的裝置識別碼** |
DeviceModelId  |相關聯裝置型號的識別碼。 |
hardwareId   |裝置的唯一識別碼，例如 MAC 位址等等。  |
reportingInterval |報告間隔（以秒為單位） |
位置    |裝置緯度（-90 到 + 90）/longitude （-180 到180）/elevation （以計量計） |
ParentDeviceId | 此裝置所連接之父裝置的識別碼。 例如： 連接到閘道的節點;節點會將 parentDeviceID 作為閘道 |
  名稱  | 用來識別資源的名稱。  裝置合作夥伴必須傳送與裝置合作夥伴端上的裝置名稱一致的名稱。 如果裝置名稱是裝置合作夥伴端上的使用者定義，則相同的使用者定義名稱應傳播至 FarmBeats  |
  說明  | 提供有意義的描述  |
  屬性  |製造商提供的其他屬性  |
  **感應器型號** | SensorModel 對應于感應器的中繼資料，例如製造商、感應器類型、類比或數位、感應器量值（例如，環境溫度、壓力等等）。 |
  類型（類比、數位）  |提及類比或數位感應器|
  manufacturer  | 製造商名稱 |
  ProductCode  | 產品代碼或型號名稱/號碼。 例如，RS-CO2-N01  |
  SensorMeasures > 名稱  | 感應器量值的名稱。 僅支援小寫。 若要從不同的深度進行測量，請指定深度。 例如，soil_moisture_15cm 此名稱必須與遙測資料一致。 |
  SensorMeasures > DataType  | 遙測資料類型。 目前支援 double  |
  sensorMeasures > 類型  | 感應器遙測資料的測量類型。 以下是系統定義的類型： AmbientTemperature、CO2、Depth、ElectricalConductivity、LeafWetness、Length、LiquidLevel、Nitrate、O2、PH、Phosphate、PointInTime、Potassium、壓力、RainGauge、RelativeHumidity、Salinity、SoilMoisture、SoilTemperature、SolarRadiation、State、TimeDuration、UVRadiation、UVIndex、Volume、WindDirection、WindRun、WindSpeed、Evapotranspiration、PAR。 若要新增更多，請參閱/ExtendedType API
  SensorMeasures > 單位 | 感應器遙測資料的單位。 以下是系統定義的單位： NoUnit、攝氏、華氏、開氏、Rankine、Pascal、水星、PSI、毫米、釐米、計量、英寸、墊腳、英里、公里、MilesPerHour、MilesPerSecond、KMPerHour、KMPerSecond、MetersPerHour、MetersPerSecond、學位、WattsPerSquareMeter、KiloWattsPerSquareMeter、MilliWattsPerSquareCentiMeter、MilliJoulesPerSquareCentiMeter、VolumetricWaterContent、百分比、PartsPerMillion、MicroMol、MicroMolesPerLiter、SiemensPerSquareMeterPerMole、MilliSiemensPerCentiMeter、Centibar、DeciSiemensPerMeter、KiloPascal、VolumetricIonContent、升、MilliLiter、Seconds、UnixTimestamp、MicroMolPerMeterSquaredPerSecond、InchesPerHour 以新增更多，請參閱/ExtendedType API
  SensorMeasures > aggregationType  | 任何一個、平均、最大值、最小值、StandardDeviation
  SensorMeasures > 深度  | 感應器的深度，以釐米為單位（例如，地面的濕度 10 cm 測量）
  sensorMeasures > 描述  | 提供量值有意義的描述
  名稱  | 用來識別資源的名稱。 例如，模型名稱/產品名稱
  說明  | 提供模型的有意義描述
  properties  | 製造商提供的其他屬性
  **感應器**  |
  hardwareId  | 製造商所設定之感應器的唯一識別碼
  sensorModelId  | 相關聯感應器型號的識別碼。
  location  | 感應器緯度（-90 到 + 90）/longitude （-180 到180）/elevation （以計量計）
  埠 > 名稱  |裝置上感應器連線的埠名稱和類型。 這必須與裝置模型中所定義的名稱相同
  deviceId  | 感應器所連接之裝置的識別碼
  名稱  | 用來識別資源的名稱。 例如，感應器名稱/產品名稱和模型編號/產品代碼。
  說明  | 提供有意義的描述
  properties  | 製造商提供的其他屬性

 如需每個物件及其屬性的詳細資訊，請參閱[swagger](httpa://aka.ms/FarmBeatsDatahubSwagger)。

 > [!NOTE]
 > Api 會針對每個建立的實例傳回唯一的識別碼。 此識別碼必須由翻譯工具保留，以進行裝置管理和元資料同步。


**元資料同步**

翻譯工具應傳送中繼資料的更新。 例如，更新案例為–變更裝置/感應器名稱、裝置/感應器位置的變更。

翻譯工具應能夠新增已由使用者張貼連結 FarmBeats 的裝置和/或感應器。 同樣地，如果使用者已更新裝置/感應器，則應該在對應的裝置/感應器的 FarmBeats 中更新相同的。 更新裝置/感應器的一般案例可能是：變更裝置位置、在節點中新增感應器等等。


> [!NOTE]
> 裝置/感應器中繼資料不支援刪除。
>
> 若要更新中繼資料，必須在裝置/感應器上呼叫/Get/{id}、更新已變更的屬性，然後執行/Put/{id}，讓使用者所設定的任何屬性不會遺失。

### <a name="adding-new-typesunit"></a>加入新的類型/單位

FarmBeats 支援新增感應器量數值型別和單位。 如需/ExtendedType API、 [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)的詳細資訊。

## <a name="telemetry-specifications"></a>遙測規格

遙測資料會對應至在 Azure 事件中樞上發佈以進行處理的標準訊息。 Azure EventHub 是一種服務，可讓您從連線的裝置和應用程式內嵌即時資料（遙測）。

## <a name="send-telemetry-data-to-farmbeats"></a>將遙測資料傳送至 FarmBeats

若要將遙測資料傳送至 FarmBeats，您必須建立用戶端，將訊息傳送至 FarmBeats 中的事件中樞。 如需遙測資料的詳細資訊，請參閱[將遙測傳送至事件中樞](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)。

以下是範例 Python 程式碼，可將遙測當做用戶端傳送至指定的事件中樞：

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

標準訊息格式如下所示：

```
{
“deviceid”: “<id of the Device created>”,
 "timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>”
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": value
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": value
        }
      ]
    }
}

```

遙測 json 中的所有索引鍵名稱都應該是小寫，例如 deviceid、sensordata 等等。

例如，遙測訊息：


```
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}

```

## <a name="troubleshooterror-management"></a>疑難排解/錯誤管理

**疑難排解選項/支援**

如果客戶無法在指定的 FarmBeats 實例中接收裝置資料和/或遙測，裝置合作夥伴應提供支援，以及疑難排解相同的機制。

**遙測資料保留**

遙測資料也應該保留預先定義的時間週期，如此一來，在發生錯誤或資料遺失的情況時，相同的就可以用來進行偵錯工具或重新傳送遙測。

**錯誤管理/錯誤通知**

在裝置夥伴系統中影響裝置/感應器中繼資料/資料整合或遙測資料流程的錯誤事件中，系統應該會向客戶通知相同的情況。 同時也應該設計並實行解決錯誤的機制。

**連接檢查清單**

裝置製造商/合作夥伴可以具有下列健全測試/檢查清單，以確保客戶提供的認證正確無誤。

   - 使用提供的認證檢查是否已收到存取權杖
   - 檢查 API 呼叫是否成功並收到存取權杖
   - 檢查是否已建立 EventHub 用戶端連線

## <a name="next-steps"></a>後續步驟

如需 REST API 的詳細資訊，請參閱[REST API](references-for-farmbeats.md#rest-api)。
