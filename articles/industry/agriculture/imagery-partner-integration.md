---
title: 影像合作夥伴整合
description: 說明影像合作夥伴整合
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 2b15cc56cb4f804fac0b047819ccf166950e1cf3
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890981"
---
# <a name="imagery-partner-integration"></a>影像合作夥伴整合

本文說明如何使用 Azure FarmBeats Translator 元件，將影像資料傳送至 FarmBeats。 農業影像資料可以來自各種來源，包括 multispectral 攝影機、衛星和無人機。 農業影像合作夥伴可以與 FarmBeats 整合，為客戶提供其伺服器陣列的自訂產生對應。

資料一經提供，就可以透過 FarmBeats 加速器來視覺化，而且可能用於資料融合和（Machine Learning/人工智慧） ML/AI 模型（由農業企業或客戶系統整合者建立）。

FarmBeats 提供下列功能：

- 使用擴充類型 Api 定義自訂映射類型、來源、檔案格式
- 透過場景 & SceneFile Api 內嵌各種來源的影像資料。

下列資訊著重于在 FarmBeats 系統中取得任何形式的影像。

當您選取 [無人機影像] 區段時，快顯視窗會隨即開啟，以顯示無人機 orthomosaic 的高解析度影像。 您可以存取合作夥伴軟體，這有助於規劃無人機航班並取得原始資料。 您將繼續使用合作夥伴的軟體進行路徑規劃和 orthomosaic 影像裝訂。

無人機合作夥伴必須讓客戶能夠將其客戶帳戶與其在 Azure 上的 FarmBeats 實例連結在一起。

您必須在無人機合作夥伴軟體中使用下列認證來連結 FarmBeats：

- API 端點
- 租用戶識別碼
- 用戶端識別碼
- 用戶端密碼

## <a name="api-development"></a>API 開發

這些 Api 包含 Swagger 技術檔。 如需 Api 和對應要求/回應的相關資訊，請參閱[Swagger](https://aka.ms/FarmBeatsDatahubSwagger) 。

## <a name="authentication"></a>驗證

FarmBeats 會利用 Microsoft Azure 的[Active Directory]((https://docs.microsoft.com/azure/app-service/overview-authentication-authorization))。 驗證 Azure App Service 提供內建的驗證和授權支援。 

如需的詳細資訊，請參閱[Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)。   

FarmBeats 資料中樞會使用持有人驗證，其需要下列認證：

- 用戶端識別碼
- 用戶端密碼
- 租用戶識別碼

使用上述認證時，呼叫端可以要求存取權杖，這需要在後續的 API 要求中傳送，如下所示：

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

以下是可抓取存取權杖的 Python 程式碼範例。 接著，您可以使用權杖來進行後續的 API 呼叫 FarmBeat：   
匯入 azure 

```python
from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net"   
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

## <a name="http-request-headers"></a>HTTP 要求標頭

以下是對 FarmBeats 資料中樞進行 API 呼叫時，需要指定的最常見要求標頭：

**標頭** | **描述和範例**
--- | ---
Content-Type  | FarmBeats 資料中樞 API 格式的要求格式（內容類型： application/<format>）為 json。 內容類型： application/json
授權 | 指定進行 API 呼叫所需的存取權杖。 授權：持有人 < 存取權杖 >
Accept  | 回應格式。 針對 FarmBeats 資料中樞 Api，格式為 json Accept： application/json


## <a name="api-requests"></a>API 要求

若要提出 REST API 要求，您可以將 HTTP 方法（GET/POST/PUT）、API 服務的 URL、資源 URI （用來查詢、提交資料、更新或刪除）和一或多個 HTTP 要求標頭結合。

（選擇性）您可以在 GET 呼叫上包含查詢參數來篩選、限制的大小，以及排序回應中的資料。

以下範例要求是取得裝置的清單：

```azurepowershell-interactive
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

大部分的 GET、POST 和 PUT 呼叫都需要 JSON 要求主體。

以下範例要求是建立裝置（這具有具有要求主體的輸入 JSON）。


```azurepowershell-interactive
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>資料格式

JSON （JavaScript 物件標記法）是與語言無關的通用資料格式，可提供任意資料結構的簡單文字標記法。 如需詳細資訊，請參閱[JSON 組織](https://JSON.org)。

## <a name="ingesting-imagery-into-farmbeats"></a>將影像內嵌到 FarmBeats

當合作夥伴具有可連線至 FarmBeats 資料中樞的認證後，合作夥伴會在 Translator 元件中執行下列動作：

1.  根據將要上傳的影像類型，為下欄欄位建立新的擴充類型：

    - 場景來源：例如，< drone_partner_name >
    - 場景類型：例如，<drone>
    - 場景檔案類型：例如，<chlorophyll index>
    - 場景檔案內容類型：例如，< 影像/tiff >

2.  呼叫伺服器陣列 API，以從 Azure FarmBeats 系統內取得伺服器陣列清單。
3.  提供客戶從伺服器陣列清單中選擇單一伺服器陣列的能力。

    夥伴系統必須顯示合作夥伴軟體內的伺服器陣列，才能執行路徑規劃和無人機航班和影像收集。

4.  呼叫場景 API 並提供必要的詳細資料，以建立具有唯一 SceneID 的新場景。
5.  接收 Blob SAS URL，以將所需的影像上傳至 FarmBeats 資料中樞（在所選伺服器陣列的內容中）至 FarmBeats 系統。

以下是 API 呼叫的詳細流程：

### <a name="step-1-extendedtype"></a>步驟1： ExtendedType

如果 FarmBeats 上有類型和檔案來源，請簽入 ExtendedType API。 若要這麼做，您可以呼叫/ExtendedType API 上的 GET。

以下是系統定義的值：

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```
這會是一次性設定，而此新 scenetype 的範圍僅限於部署 FarmBeats 專案所在的訂用帳戶。

範例：若要新增 SceneSource： "SlantRange"，您可以使用索引鍵： "SceneSource" 輸入裝載，將/ExtendedType 放在其識別碼上：

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

綠色欄位是系統定義場景來源值的新新增。

### <a name="step-2-get-farmdetails"></a>步驟2：取得 FarmDetails

幕後（tiff 或 .csv 檔案）將會在伺服器陣列的內容中。 您需要在/Farm API 上執行 get，以取得伺服器陣列的詳細資料。 此 API 會傳回 FarmBeats 中可用的伺服器陣列清單，您可以選取要內嵌資料的伺服器陣列。

取得/Farm 回應：

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-ascene-id-post-call"></a>步驟3：建立/場景識別碼（post 呼叫）

使用指定的資訊來建立新的場景（tiff 或 .csv 檔案），並提供與場景相關聯的日期、順序和伺服器陣列識別碼。 與場景相關聯的中繼資料可以在屬性下定義，包括量值的持續時間和類型。

這會建立新的 SceneID，這會與伺服器陣列產生關聯。 建立 SceneID 之後，使用者可以使用相同的來建立新的檔案（tiff 或 .csv），& 儲存檔案的內容。

Post 呼叫 on/場景 API 的範例輸入承載

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

API 回應：

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

**建立/SceneFile**

步驟3中傳回的場景識別碼是 SceneFile 的輸入。 SceneFile 會傳回 SAS URL 權杖，其有效期為24小時。

如果使用者需要以程式設計方式上傳影像串流，則可以使用 blob 儲存體 SDK 來定義使用 Scenefile ID、location & URL 的方法。

/Scenefile API 上 Post 呼叫的範例輸入承載：

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
API 回應：

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

/SceneFile API 的 Post 呼叫會傳回 SAS 上傳 URL，其可用來上傳使用 Azure Blob 儲存體用戶端/程式庫的 csv 或 tiff 檔案。


## <a name="next-steps"></a>後續步驟

如需以 Rest API 為基礎的整合詳細資料的詳細資訊，請參閱[REST API](references-for-farmbeats.md#rest-api)。
