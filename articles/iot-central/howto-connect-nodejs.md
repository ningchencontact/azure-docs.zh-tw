---
title: 將一般 Node.js 用戶端應用程式連線至 Azure IoT Central | Microsoft Docs
description: 身為裝置開發人員，如何將一般 Node.js 裝置連線到您的 Azure IoT Central 應用程式。
author: dominicbetts
ms.author: dobett
ms.date: 09/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: ccded68cfaa00e6e13e2bb32e114b81108742829
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686676"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>將一般用戶端應用程式連線到 Azure IoT 中心應用程式 (Node.js)

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

本文說明如何以裝置開發人員身分，將代表真實裝置的一般 Node.js 應用程式連線到 Microsoft Azure IoT 中心應用程式。

## <a name="before-you-begin"></a>開始之前

若要完成這篇文章中的步驟，您需要下列項目︰

- Azure IoT Central 應用程式。 如需詳細資訊，請參閱[建立應用程式快速入門](quick-deploy-iot-central.md)。
- 已安裝 [Node.js](https://nodejs.org/) 4.0.0 版或更新版本的開發電腦。 您可以在命令列執行 `node --version` 來檢查版本。 Node.js 適用於多種作業系統。

## <a name="create-a-device-template"></a>建立裝置範本

在您的 Azure IoT Central 應用程式中，您需要具有下列測量、裝置屬性、設定和命令的裝置範本：

### <a name="telemetry-measurements"></a>遙測量測

在 [量**值**] 頁面上新增下列遙測：

| 顯示名稱 | 欄位名稱  | 單位 | Min | max | 小數位數 |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| 溫度  | 溫度 | F     | 60  | 110 | 0              |
| 濕度     | 溼度    | %     | 0   | 100 | 0              |
| Pressure     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
> 遙測量測的資料類型為浮點數。

在裝置範本中輸入完全如上表所示的欄位名稱。 如果功能變數名稱不符合對應裝置程式碼中的屬性名稱，則無法在應用程式中顯示遙測。

### <a name="state-measurements"></a>狀態量值

在 [量**值**] 頁面上新增下列狀態：

| 顯示名稱 | 欄位名稱  | 值 1 | 顯示名稱 | 值 2 | 顯示名稱 |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| 風扇模式     | fanmode     | 1       | Running      | 0       | 已停止      |

> [!NOTE]
> 狀態量測的資料類型為字串。

在裝置範本中輸入完全如上表所示的欄位名稱。 如果功能變數名稱不符合對應裝置程式碼中的屬性名稱，則無法在應用程式中顯示狀態。

### <a name="event-measurements"></a>事件量值

在 [量**值**] 頁面上新增下列事件：

| 顯示名稱 | 欄位名稱  | Severity |
| ------------ | ----------- | -------- |
| 過熱  | overheat    | Error    |

> [!NOTE]
> 事件量測的資料類型為字串。

### <a name="location-measurements"></a>位置測量

在 [量**值**] 頁面上新增下列位置測量：

| 顯示名稱 | 欄位名稱  |
| ------------ | ----------- |
| Location     | 位置    |

位置量測資料類型是由經度和緯度的兩個浮點數所組成，而非高度的選擇性浮點數。

在裝置範本中輸入完全如上表所示的欄位名稱。 如果功能變數名稱不符合對應裝置程式碼中的屬性名稱，則無法在應用程式中顯示該位置。

### <a name="device-properties"></a>裝置屬性

在 [**屬性**] 頁面上新增下列裝置屬性：

| 顯示名稱        | 欄位名稱        | 資料類型 |
| ------------------- | ----------------- | --------- |
| 序號       | serialNumber      | 文字      |
| 裝置製造商 | 製造商      | 文字      |

在裝置範本中輸入完全如上表所示的欄位名稱。 如果功能變數名稱不符合對應裝置程式碼中的屬性名稱，則無法在應用程式中顯示內容。

### <a name="settings"></a>設定

在 [**設定**] 頁面上新增下列**數目**設定：

| 顯示名稱    | 欄位名稱     | 單位 | 小數位數 | Min | max  | 初始 |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| 風扇速度       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| 設定溫度 | setTemperature | F     | 0        | 20  | 200  | 80      |

在裝置範本中輸入完全如上表所示的欄位名稱。 如果功能變數名稱不符合對應裝置程式碼中的屬性名稱，裝置就無法接收設定值。

### <a name="commands"></a>命令

在 [**命令**] 頁面上新增下列命令：

| 顯示名稱    | 欄位名稱     | 預設逾時 | 資料類型 |
| --------------- | -------------- | --------------- | --------- |
| 倒數       | 倒數      | 30              | number    |

將下列輸入欄位新增至倒數命令：

| 顯示名稱    | 欄位名稱     | 資料類型 | 值 |
| --------------- | -------------- | --------- | ----- |
| 計數來源      | countFrom      | number    | 10    |

在裝置範本中輸入完全如上表所示的功能變數名稱。 如果功能變數名稱不符合對應裝置程式碼中的屬性名稱，則裝置無法處理此命令。

## <a name="add-a-real-device"></a>新增真實裝置

在您的 Azure IoT Central 應用程式中，將實際裝置新增至您在上一節中建立的裝置範本。

記下 [**裝置**連線] 頁面上的裝置連接資訊：**範圍識別碼**、**裝置識別碼**和**主要金鑰**。 您稍後會在本操作指南中，將這些值新增至您的裝置程式碼：

![裝置連接資訊](./media/howto-connect-nodejs/device-connection.png)

### <a name="create-a-nodejs-application"></a>建立 Node.js 應用程式

下列步驟示範如何建立用戶端應用程式，以實作您新增至應用程式的真實裝置。 在這裡，Node.js 應用程式代表真實裝置。

1. 在電腦上建立名為 `connected-air-conditioner-adv` 的資料夾。 在命令列環境中瀏覽至該資料夾。

1. 若要初始化 Node.js 專案，請執行下列命令：

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. 在 `connected-air-conditioner-adv` 資料夾中建立一個名為 **connectedAirConditionerAdv.js** 的檔案。

1. 在 **connectedAirConditionerAdv.js** 檔案的開頭，新增下列 `require` 陳述式：

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. 將下列變數宣告新增至該檔案：

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;

    var targetTemperature = 0;
    var locLong = -122.1215;
    var locLat = 47.6740;
    ```

    使用您先前`{your Scope ID}`所`{your Device ID}`記下`{your Primary Key}`的值來更新預留位置、和。 在此範例中，您`targetTemperature`會將初始化為零，您可以使用裝置的目前讀取或裝置對應項的值。

1. 若要將遙測、狀態、事件和位置度量傳送至您的 Azure IoT Central 應用程式，請將下列函式新增至檔案：

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var locationLong = locLong - (Math.random() / 100);
      var locationLat = locLat - (Math.random() / 100);
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined,
        location: {
            lon: locationLong,
            lat: locationLat }
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

1. 若要將裝置屬性傳送至 Azure IoT 中心應用程式，請將下列函式新增至檔案：

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. 若要定義您的裝置所回應的設定，請新增下列定義：

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. 若要從 Azure IoT 中心應用程式處理更新後的設定，請新增下列檔案：

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. 新增下列程式碼，以處理從 IoT Central 應用程式傳送的倒數計時命令：

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');
    
      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;
    
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          hubClient.getTwin((err, twin) => {
            function doCountdown(){
              if ( countFrom >= 0 ) {
                var patch = {
                  countdown:{
                    value: countFrom
                  }
                };
                sendDeviceProperties(twin, patch);
                countFrom--;
                setTimeout(doCountdown, 2000 );
              }
            }
    
            doCountdown();
          });
        }
      });
    }
    ```

1. 新增下列程式碼以完成對 Azure IoT Central 的連線，並連結用戶端程式碼中的函式：

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Create handler for countdown command
        hubClient.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              serialNumber: '123-ABC',
              manufacturer: 'Contoso'
            };
            sendDeviceProperties(twin, properties);

            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>執行 Node.js 應用程式

在命令列環境中執行下列命令：

```cmd/sh
node connectedAirConditionerAdv.js
```

身為 Azure IoT 中心應用程式的操作者，您可以針對真實裝置：

* 在 [量值] 頁面上檢視遙測：

    ![檢視遙測](media/howto-connect-nodejs/viewtelemetry.png)

* 在 [量**值**] 頁面上，查看位置：

    ![視圖位置測量](media/howto-connect-nodejs/viewlocation.png)

* 在 [屬性] 頁面上檢視從裝置傳送的裝置屬性值。 裝置屬性磚會在裝置連線時更新：

    ![檢視裝置屬性](media/howto-connect-nodejs/viewproperties.png)

* 從 [**設定**] 頁面設定 [風扇速度] 和 [目標溫度]：

    ![設定風扇速度](media/howto-connect-nodejs/setfanspeed.png)

* 從 [**命令**] 頁面呼叫倒數命令：

    ![呼叫倒數命令](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何將一般 Node.js 用戶端連線至您的 Azure IoT Central 應用程式，建議的下一個步驟是瞭解如何為您自己的 IoT 裝置[設定自訂裝置範本](howto-set-up-template.md)。
