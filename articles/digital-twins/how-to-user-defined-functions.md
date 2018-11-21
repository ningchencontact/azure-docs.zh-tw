---
title: 如何在 Azure Digital Twins 中使用使用者定義函數 | Microsoft Docs
description: 有關如何使用 Azure Digital Twins 建立使用者定義函數、比對器和角色指派的指導方針。
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: alinast
ms.openlocfilehash: 6a757dca48dc3ff41adfe6f8802fad40e7a4ca81
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636827"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>如何在 Azure Digital Twins 中使用使用者定義函數

[使用者定義函式](./concepts-user-defined-functions.md) (UDF) 可讓使用者對傳入遙測訊息和空間圖表中繼資料執行自訂邏輯。 然後使用者可以將事件傳送至預先定義的端點。 本指南逐步引導您進行有關溫度事件的範例，以偵測超過特定溫度的任何讀數並發出警示。

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>用戶端程式庫參考

使用者定義函式執行階段中可作為協助程式方法的函式，會列在[用戶端參考](#Client-Reference)一節中。

## <a name="create-a-matcher"></a>建立比對器

比對器是針對指定的遙測訊息，判斷要使用哪個使用者定義函式的圖表物件。

- 有效比對器條件的比較：

  - `Equals`
  - `NotEquals`
  - `Contains`

- 有效比對器條件的目標：

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

下列比對器範例在資料類型值為 `"Temperature"` 的任何感應器遙測事件上會評估為 True。 您可以在使用者定義函式上建立多個比對器：

```plaintext
POST YOUR_MANAGEMENT_API_URL/matchers
{
  "Name": "Temperature Matcher",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ],
  "SpaceId": "YOUR_SPACE_IDENTIFIER"
}
```

| 值 | 更換為 |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | 裝載您執行個體的伺服器區域 |

## <a name="create-a-user-defined-function-udf"></a>建立使用者定義函式 (UDF)

建立比對器之後，使用下列 **POST** 呼叫來上傳函式程式碼片段：

> [!IMPORTANT]
> - 在標頭中，設定下列內容：`Content-Type: multipart/form-data; boundary="userDefinedBoundary"`。
> - 主體是由多個部分組成的：
>   - 第一個部分是 UDF 所需的相關中繼資料。
>   - 第二個部分是 JavaScript 計算邏輯。
> - 在 **USER_DEFINED_BOUNDARY** 區段中，取代 **SpaceId** 和 **Machers** 值。

```plaintext
POST YOUR_MANAGEMENT_API_URL/userdefinedfunctions with Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"
```

| 參數值 | 更換為 |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | 多部分內容界限名稱 |

### <a name="body"></a>body

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "YOUR_SPACE_IDENTIFIER",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| 值 | 更換為 |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | 空間識別碼  |
| YOUR_MATCHER_IDENTIFIER | 您想要使用之比對器的識別碼 |

### <a name="example-functions"></a>函式範例

直接針對資料類型為 **Temperature** (也就是 `sensor.DataType`) 的感應器設定感應器遙測讀取：

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

**telemetry** 參數會公開 **SensorId** 和 **Message** 屬性 (對應到感應器所傳送的訊息)。 ExecutionContext 參數會公開下列屬性：

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

在下一個範例中，如果感應器遙測讀取超出預先定義的閾值，我們就會記錄訊息。 如果 Azure Digital Twins 執行個體上已啟用診斷設定，則也會轉送來自使用者定義函式的記錄：

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

如果溫度高於在預先定義的常數，下列程式碼會觸發通知：

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

如需更複雜的 UDF 程式碼範例，[請使用新鮮空氣 UDF 檢查可用空間](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)。

## <a name="create-a-role-assignment"></a>建立角色指派

我們需要建立角色指派，讓使用者定義函式可在其下方執行。 如果不這樣做，使用者定義函式就沒有能與管理 API 互動的適當權限，也無法在圖表物件上執行動作。 使用者定義函式所執行的動作仍須遵循 Azure Digital Twins 管理 API 內的角色型存取控制。 您可以藉由指定特定角色或特定存取控制路徑來限制這些動作的範圍。 如需詳細資訊，請參閱[角色型存取控制](./security-role-based-access-control.md)文件。

1. 查詢角色，並針對您想要指派至 UDF 的角色取得其識別碼。 將它傳遞給 **RoleId**：

    ```plaintext
    GET YOUR_MANAGEMENT_API_URL/system/roles
    ```

1. **ObjectId** 就是稍早建立的 UDF 識別碼。
1. 藉由使用 `fullpath` 查詢您的空間來找到 **Path** 的值。
1. 複製傳回的 `spacePaths` 值。 您會在下列程式碼中使用它：

    ```plaintext
    GET YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | 參數值 | 更換為 |
    | --- | --- |
    | *YOUR_SPACE_NAME* | 想要使用的空間名稱 |

1. 將傳回的 `spacePaths` 值貼上至 **Path**，以建立 UDF 角色指派：

    ```plaintext
    POST YOUR_MANAGEMENT_API_URL/roleassignments
    {
      "RoleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "ObjectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "ObjectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "Path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | 您的值 | 更換為 |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | 所需角色的識別碼 |
    | YOUR_USER_DEFINED_FUNCTION_ID | 要使用的 UDF 識別碼 |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | 指定 UDF 類型的識別碼 |
    | YOUR_ACCESS_CONTROL_PATH | 存取控制路徑 |

## <a name="send-telemetry-to-be-processed"></a>傳送要處理的遙測

圖表中描述的感應器所產生的遙測資料，觸發已上傳之使用者定義函式的執行。 資料處理器取用遙測資料。 然後針對使用者定義函式的引動，建立執行計劃。

1. 針對產生讀數的感應器擷取比對器。
1. 根據評估成功的比對器，擷取相關聯的使用者定義函式。
1. 執行每個使用者定義函式。

## <a name="client-reference"></a>用戶端參考

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

此函式根據指定的空間識別碼，從圖表擷取空間。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | 空間識別碼 |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

此函式根據指定的感應器識別碼，從圖表擷取感應器。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | 感應器識別碼 |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

此函式根據指定的裝置識別碼，從圖表擷取裝置。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | 裝置識別碼 |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

此函式根據指定的感應器識別碼和其資料類型，擷取該感應器的目前值。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | 感應器識別碼 |
| dataType  | `string` | 感應器資料類型 |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

此函式根據指定的空間識別碼和值的名稱，擷取該空間屬性的目前值。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | 空間識別碼 |
| valueName | `string` | 空間屬性名稱 |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

此函式根據指定的感應器識別碼和其資料類型，擷取該感應器的歷史值。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| sensorId | `guid` | 感應器識別碼 |
| dataType | `string` | 感應器資料類型 |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

此函式根據指定的空間識別碼和值的名稱，擷取空間上該屬性的歷史值。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| spaceId | `guid` | 空間識別碼 |
| valueName | `string` | 空間屬性名稱 |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

此函式根據指定的空間識別碼，擷取該父空間的子空間。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| spaceId | `guid` | 空間識別碼 |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

此函式根據指定的空間識別碼，擷取該父空間的子感應器。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| spaceId | `guid` | 空間識別碼 |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

此函式根據指定的空間識別碼，擷取該父空間的子裝置。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| spaceId | `guid` | 空間識別碼 |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

此函式根據指定的裝置識別碼，擷取該父裝置的子感應器。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| deviceId | `guid` | 裝置識別碼 |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

此函式根據指定的空間識別碼，擷取其父空間。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| childSpaceId | `guid` | 空間識別碼 |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

此函式根據指定的感應器識別碼，擷取其父空間。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| childSensorId | `guid` | 感應器識別碼 |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

此函式根據指定的裝置識別碼，擷取其父空間。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| childDeviceId | `guid` | 裝置識別碼 |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

此函式根據指定的感應器識別碼，擷取其父裝置。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| childSensorId | `guid` | 感應器識別碼 |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

此函式根據指定的空間識別碼，從該空間擷取屬性和其值。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| spaceId | `guid` | 空間識別碼 |
| propertyName | `string` | 空間屬性名稱 |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

此函式根據指定的感應器識別碼，從該感應器擷取屬性和其值。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| sensorId | `guid` | 感應器識別碼 |
| propertyName | `string` | 感應器屬性名稱 |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

此函式根據指定的裝置識別碼，從該裝置擷取屬性和其值。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| deviceId | `guid` | 裝置識別碼 |
| propertyName | `string` | 裝置屬性名稱 |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

此函式在具有指定資料類型的感應器物件上設定值。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| sensorId | `guid` | 感應器識別碼 |
| dataType  | `string` | 感應器資料類型 |
| *value*  | `string` | 值 |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

此函式在具有指定資料類型的空間物件上設定值。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| spaceId | `guid` | 空間識別碼 |
| dataType | `string` | 資料類型 |
| *value* | `string` | 值 |

### <a name="logmessage"></a>log(message)

此函式記錄使用者定義函式中的下列訊息。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| *message* | `string` | 要記錄的訊息 |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

此函式傳送出要分派的自訂通知。

**種類**：全域函式

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| topologyObjectId  | `guid` | 圖表物件識別碼。 範例如空間、感應器和裝置識別碼。|
| topologyObjectType  | `string` | 範例如感應器和裝置。|
| payload  | `string` | 與通知一起傳送的 JSON 承載。 |

## <a name="return-types"></a>傳回類型

下列模型描述來自上述用戶端參考的傳回物件。

### <a name="space"></a>空白字元

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000000",
  "Name": "Space",
  "FriendlyName": "Conference Room",
  "TypeId": 0,
  "ParentSpaceId": "00000000-0000-0000-0000-000000000001",
  "SubtypeId": 0
}
```

### <a name="space-methods"></a>空間方法

#### <a name="parent--space"></a>Parent() ⇒ `space`

此函式傳回目前空間的父空間。

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

此函式傳回目前空間的子感應器。

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

此函式傳回目前空間的子裝置。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

此函式傳回目前空間的擴充屬性和其值。

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| propertyName | `string` | 擴充屬性的名稱 |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

此函式傳回目前空間的值。

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| valueName | `string` | 值的名稱 |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

此函式傳回目前空間的歷史值。

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| valueName | `string` | 值的名稱 |

#### <a name="notifypayload"></a>Notify(payload)

此函式傳送具有指定承載的通知。

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| payload | `string` | 要包含在通知中的 JSON 承載 |

### <a name="device"></a>裝置

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000002",
  "Name": "Device",
  "FriendlyName": "Temperature Sensing Device",
  "Description": "This device contains a sensor that captures temperature readings.",
  "Type": "None",
  "Subtype": "None",
  "TypeId": 0,
  "SubtypeId": 0,
  "HardwareId": "ABC123",
  "GatewayId": "ABC",
  "SpaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>裝置方法

#### <a name="parent--space"></a>Parent() ⇒ `space`

此函式傳回目前裝置的父空間。

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

此函式傳回目前裝置的子感應器。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

此函式傳回目前裝置的擴充屬性和其值。

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| propertyName | `string` | 擴充屬性的名稱 |

#### <a name="notifypayload"></a>Notify(payload)

此函式傳送具有指定承載的通知。

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| payload | `string` | 要包含在通知中的 JSON 承載 |

### <a name="sensor"></a>感應器

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000003",
  "Port": "30",
  "PollRate": 3600,
  "DataType": "Temperature",
  "DataSubtype": "None",
  "Type": "Classic",
  "PortType": "None",
  "DataUnitType": "FahrenheitTemperature",
  "SpaceId": "00000000-0000-0000-0000-000000000000",
  "DeviceId": "00000000-0000-0000-0000-000000000001",
  "PortTypeId": 0,
  "DataUnitTypeId": 0,
  "DataTypeId": 0,
  "DataSubtypeId": 0,
  "TypeId": 0  
}
```

### <a name="sensor-methods"></a>感應器方法

#### <a name="space--space"></a>Space() ⇒ `space`

此函式傳回目前感應器的父空間。

#### <a name="device--device"></a>Device() ⇒ `device`

此函式傳回目前感應器的父裝置。

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

此函式傳回目前感應器的擴充屬性和其值。

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| propertyName | `string` | 擴充屬性的名稱 |

#### <a name="value--value"></a>Value() ⇒ `value`

此函式傳回目前感應器的值。

#### <a name="history--value"></a>History() ⇒ `value[]`

此函式傳回目前感應器的歷史值。

#### <a name="notifypayload"></a>Notify(payload)

此函式傳送具有指定承載的通知。

| 參數  | 類型                | 說明  |
| ------ | ------------------- | ------------ |
| payload | `string` | 要包含在通知中的 JSON 承載 |

### <a name="value"></a>值

```JSON
{
  "DataType": "Temperature",
  "Value": "70",
  "CreatedTime": ""
}
```

### <a name="extended-property"></a>擴充的屬性

```JSON
{
  "Name": "OccupancyStatus",
  "Value": "Occupied"
}
```

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Azure Digital Twins 端點](how-to-egress-endpoints.md)以傳送事件。

- 如需 Azure Digital Twins 端點的詳細資訊，請[深入了解端點](concepts-events-routing.md)。
