---
title: 內嵌歷程記錄遙測資料
description: 說明如何取得內嵌歷程記錄遙測資料
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 6fc70b55b3e672ecc67eb1145bb751de33d998a1
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847427"
---
# <a name="ingest-historical-telemetry-data"></a>內嵌歷程記錄遙測資料

本文說明如何將歷史感應器資料內嵌到 Azure FarmBeats。

從物聯網（IoT）內嵌資源（例如裝置和感應器）的歷程記錄資料是 FarmBeats 的常見案例。 您會建立裝置和感應器的中繼資料，然後以標準格式將歷程記錄資料內嵌至 FarmBeats。

## <a name="before-you-begin"></a>開始之前

繼續進行本文之前，請確定您已安裝 FarmBeats，並從 IoT 收集歷程記錄資料。

## <a name="enable-partner-access"></a>啟用合作夥伴存取

您必須啟用 Azure FarmBeats 實例的合作夥伴整合。 此步驟會建立可存取 Azure FarmBeats 作為裝置合作夥伴的用戶端，並提供後續步驟中所需的下列值。

- API 端點–這是資料中樞 URL，例如，HTTPs://<datahub>. azurewebsites.net
- 租用戶識別碼
- 用戶端識別碼
- 用戶端密碼
- EventHub 連接字串

請遵循下列步驟來產生這些：

>[!NOTE]
> 您必須是系統管理員，才能執行下列步驟。

1. 下載此[腳本](https://aka.ms/farmbeatspartnerscript)，並在本機磁片磁碟機上將它解壓縮。 您會在 ZIP 檔案中找到兩個檔案。
2. 登入[Azure 入口網站](https://portal.azure.com/)並開啟 Cloud Shell （此選項可在入口網站的右上方列中取得）  

    ![專案伺服器陣列的節拍](./media/for-tutorials/navigation-bar-1.png)

3. 請確定環境已設定為**PowerShell**。

    ![專案伺服器陣列的節拍](./media/for-tutorials/power-shell-new-1.png)

4. 上傳您在 Cloud Shell 中所下載的兩個檔案（從上面的步驟1）。  

    ![專案伺服器陣列的節拍](./media/for-tutorials/power-shell-two-1.png)

5. 移至上傳檔案的目錄（根據預設，它會上傳至 [主目錄]/home/username/。
6. 使用命令執行腳本：  

    ```azurepowershell-interactive
    PS> ./generateCredentials.ps1
    ```

7. 請遵循畫面上的指示來完成程式。

## <a name="create-devicesensor-metadata"></a>建立裝置/感應器中繼資料

 現在您已有必要的認證，您可以使用 FarmBeats Api 建立中繼資料來定義裝置和感應器。

 FarmBeats 資料中樞具有下列 Api，可讓您建立及管理裝置/感應器中繼資料。   

- /**devicemodel 傳遞**-裝置型號會對應至裝置的中繼資料，例如裝置的 [製造商]、[閘道] 或 [節點] 類型。  
- /**裝置**裝置會對應至存在於伺服器陣列中的實體裝置。  
- /**SensorModel** -感應器型號對應于感應器的中繼資料，例如製造商、感應器類型、類比或數位、感應器量值（例如，環境溫度或壓力）。
- /**感應器**-感應器會對應到記錄值的實體感應器。 感應器通常會連接到裝置識別碼為的裝置。  


|        裝置模式   |  建議   |
| ------- | -------             |
|     類型（節點、閘道）        |          1 顆星      |
|          製造商            |         2 顆星     |
|  ProductCode                    |  裝置產品代碼或型號名稱/號碼。 例如，EnviroMonitor # 6800。  |
|            連接埠          |     埠名稱和類型（數位/類比）
|     名稱                 |  用來識別資源的名稱。 例如，模型名稱/產品名稱。
      說明     | 提供模型的有意義描述
|    屬性          |    製造商提供的其他屬性   |
|    **裝置**             |                      |
|   DeviceModelId     |     相關聯裝置型號的識別碼  |
|  hardwareId          | 裝置的唯一識別碼，例如 MAC 位址等等。
|  reportingInterval        |   報告間隔（以秒為單位）
|  位置            |  裝置緯度（-90 到 + 90）/Longitude （-180 到180）/Elevation （以計量計）   
|ParentDeviceId       |    此裝置所連接之父裝置的識別碼。 例如，連接到閘道的節點。 節點會以閘道的形式 parentDeviceId。  |
|    名稱            | 用來識別資源的名稱。 裝置合作夥伴必須傳送與合作夥伴端上的裝置名稱一致的名稱。 如果合作夥伴裝置名稱是使用者定義的，則相同的使用者定義名稱應傳播至 FarmBeats。|
|     說明       |      提供有意義的描述  |
|     屬性    |  製造商提供的其他屬性
|     **感應器型號**        |          |
|       類型（類比、數位）          |      感應器類型，不論是類比或數位       |
|          製造商            |       感應器的製造商     |
|     ProductCode| 產品代碼或型號名稱/號碼。 例如，RS-CO2-N01。 |
|       SensorMeasures > 名稱       | 感應器量值的名稱。 僅支援小寫。 若要從不同的深度進行測量，請指定深度。 例如，soil_moisture_15cm。 此名稱必須與遙測資料一致  |
|          SensorMeasures > DataType       |遙測資料類型。 目前支援 double|
|    sensorMeasures > 類型    |感應器遙測資料的測量類型。 以下是系統定義的類型： AmbientTemperature、CO2、Depth、ElectricalConductivity、LeafWetness、Length、LiquidLevel、Nitrate、O2、PH、Phosphate、PointInTime、Potassium、壓力、RainGauge、RelativeHumidity、Salinity、SoilMoisture、SoilTemperature、SolarRadiation、State、TimeDuration、UVRadiation、UVIndex、Volume、WindDirection、WindRun、WindSpeed、Evapotranspiration、PAR。 若要新增更多，請參閱/ExtendedType API。|
|        SensorMeasures > 單位              | 感應器遙測資料的單位。 以下是系統定義的單位： NoUnit、攝氏、華氏、開氏、Rankine、Pascal、水星、PSI、毫米、釐米、計量、英寸、墊腳、英里、公里、MilesPerHour、MilesPerSecond、KMPerHour、KMPerSecond、MetersPerHour、MetersPerSecond、學位、WattsPerSquareMeter、KiloWattsPerSquareMeter、MilliWattsPerSquareCentiMeter、MilliJoulesPerSquareCentiMeter、VolumetricWaterContent、百分比、PartsPerMillion、MicroMol、MicroMolesPerLiter、SiemensPerSquareMeterPerMole、MilliSiemensPerCentiMeter、Centibar、DeciSiemensPerMeter、KiloPascal、VolumetricIonContent、升、MilliLiter、Seconds、UnixTimestamp、MicroMolPerMeterSquaredPerSecond、InchesPerHour 以新增更多，請參閱/ExtendedType API。|
|    SensorMeasures > aggregationType    |  值可以是 none、average、maximum、下限或 StandardDeviation  |
|          名稱            | 用來識別資源的名稱。 例如，模型名稱/產品名稱。  |
|    說明        | 提供模型的有意義描述  |
|   屬性       |  製造商提供的其他屬性  |
|    **感應器**      |          |
| hardwareId          |   製造商所設定之感應器的唯一識別碼 |
|  sensorModelId     |    相關聯感應器型號的識別碼   |
| location          |  感應器緯度（-90 到 + 90）/Longitude （-180 到180）/Elevation （以計量計）|
|   埠 > 名稱        |  裝置上感應器連線的埠名稱和類型。 這必須與裝置模型中所定義的名稱相同。 |
|    DeviceID  |    感應器所連接之裝置的識別碼     |
| 名稱            |   用來識別資源的名稱。 例如，感應器名稱/產品名稱和模型編號/產品代碼。|
|    說明      | 提供有意義的描述 |
|    屬性        |製造商提供的其他屬性 |

如需物件的詳細資訊，請參閱[Swagger](https://aka.ms/FarmBeatsDatahubSwagger)。

**建立中繼資料的 API 要求**

若要提出 API 要求，您可以將 HTTP （POST）方法、API 服務的 URL、要查詢的資源 URI、提交資料來建立或刪除要求，並新增一或多個 HTTP 要求標頭。 API 服務的 URL 是 API 端點，亦即資料中樞 URL （HTTPs://<yourdatahub>. azurewebsites.net）  

**驗證**：

FarmBeats 資料中樞會使用持有人驗證，這需要我們在上一節中產生的下列認證。

- 用戶端識別碼
- 用戶端密碼
- 租用戶識別碼  

呼叫者可以使用上述認證來要求存取權杖，這必須在標頭區段的後續 API 要求中傳送，如下所示：

標頭 = *{"Authorization"： "持有者" + access_token，...}*

**HTTP 要求標頭**：

以下是對 FarmBeats 資料中樞進行 API 呼叫時，需要指定的最常見要求標頭：

- 內容類型： application/json
- 授權：持有人 < 存取權杖 >
- Accept： application/json

用**來建立中繼資料的輸入**承載：

**Devicemodel 傳遞**


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

Device
```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
感應器

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
以下範例要求是建立裝置（這具有輸入 json 做為具有要求主體的承載）。  

```
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "
{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\",}"*
```

> [!NOTE]
> Api 會針對每個建立的實例傳回唯一的識別碼。 您必須保留識別碼，才能傳送對應的遙測訊息。

**傳送遙測**

現在您已在 FarmBeats 中建立裝置和感應器，可以傳送相關聯的遙測訊息。  

**建立遙測用戶端**

您必須將遙測傳送至 Azure 事件中樞以進行處理。 Azure EventHub 是一種服務，可讓您從連線的裝置和應用程式內嵌即時資料（遙測）。 若要將遙測資料傳送至 FarmBeats，您需要建立用戶端，將訊息傳送至 FarmBeats 中的事件中樞。 如需有關傳送遙測的詳細資訊，請參閱[Azure 事件中樞](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)。

**以用戶端的身分傳送遙測訊息**

一旦您將連線建立為 EventHub 用戶端，您就可以將訊息傳送至 EventHub 做為 json。  
將歷程感應器資料格式轉換為 Azure FarmBeats 瞭解的標準格式。 標準訊息格式如下所示：  


 ```
  {   
      “deviceid”: “<id of the Device created>”,   
      "timestamp": "<timestamp in ISO 8601 format>",     
      "version" : "1",   
      "sensors":
      [     
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


新增對應的裝置和感應器之後，請取得遙測訊息中的 deviceid 和 sensorid，如上一節中所述

範例遙測訊息：


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
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


## <a name="next-steps"></a>後續步驟

如需以 Rest API 為基礎之整合詳細資料的詳細資訊，請參閱[REST API](references-for-farmbeats.md#rest-api)。
