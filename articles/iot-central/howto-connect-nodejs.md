---
title: 將一般 Node.js 用戶端應用程式連線至 Azure IoT Central | Microsoft Docs
description: 身為裝置開發人員，如何將一般的 Node.js 裝置連線到 Azure IoT Central 應用程式。
author: dominicbetts
ms.author: dobett
ms.date: 02/04/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 4c04d9dbaf0065f2e68182c9ad84181845dee3e9
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905319"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>將一般用戶端應用程式連線到 Azure IoT 中心應用程式 (Node.js)

本文說明如何以裝置開發人員身分，將代表真實裝置的一般 Node.js 應用程式連線到 Microsoft Azure IoT 中心應用程式。

## <a name="before-you-begin"></a>開始之前

若要完成這篇文章中的步驟，您需要下列項目︰

1. Azure IoT Central 應用程式。 如需詳細資訊，請參閱[建立應用程式快速入門](quick-deploy-iot-central.md)。
1. 已安裝 [Node.js](https://nodejs.org/) 4.0.0 版或更新版本的開發電腦。 您可以在命令列執行 `node --version` 來檢查版本。 Node.js 適用於多種作業系統。

## <a name="create-a-device-template"></a>建立裝置範本

Azure IoT Central 應用程式中，您需要下列度量、 裝置內容、 設定和命令的裝置範本：

### <a name="telemetry-measurements"></a>遙測量測

將下列遙測資料上**測量**頁面：

| 顯示名稱 | 欄位名稱  | Units | Min | max | 小數位數 |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| 溫度  | 溫度 | F     | 60  | 110 | 0              |
| 溼度     | 溼度    | %     | 0   | 100 | 0              |
| 壓力     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
> 遙測量測的資料類型為浮點數。

在裝置範本中輸入完全如上表所示的欄位名稱。 如果欄位名稱不符合對應的裝置程式碼中的屬性名稱，無法顯示遙測資料，應用程式中。

### <a name="state-measurements"></a>狀態量值

上加入下列的狀態**測量**頁面：

| 顯示名稱 | 欄位名稱  | 值 1 | 顯示名稱 | 值 2 | 顯示名稱 |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| 風扇模式     | fanmode     | 1       | 執行中      | 0       | 已停止      |

> [!NOTE]
> 狀態量測的資料類型為字串。

在裝置範本中輸入完全如上表所示的欄位名稱。 如果欄位名稱不符合對應的裝置程式碼中的屬性名稱，就無法顯示狀態的應用程式中。

### <a name="event-measurements"></a>事件量值

加入下列事件上**測量**頁面：

| 顯示名稱 | 欄位名稱  | 嚴重性 |
| ------------ | ----------- | -------- |
| 過熱  | overheat    | Error    |

> [!NOTE]
> 事件量測的資料類型為字串。

### <a name="device-properties"></a>裝置屬性

新增下列裝置屬性上**屬性**頁面：

| 顯示名稱        | 欄位名稱        | 資料類型 |
| ------------------- | ----------------- | --------- |
| 序號       | serialNumber      | text      |
| 裝置製造商 | manufacturer      | text      |

在裝置範本中輸入完全如上表所示的欄位名稱。 如果欄位名稱不符合對應的裝置程式碼中的屬性名稱，則無法顯示內容，應用程式中。

### <a name="settings"></a>設定

新增下列**數字**上的設定**設定**頁面：

| 顯示名稱    | 欄位名稱     | Units | 小數位數 | Min | max  | Initial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| 風扇速度       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| 設定溫度 | setTemperature | F     | 0        | 20  | 200  | 80      |

在裝置範本中輸入完全如上表所示的欄位名稱。 如果欄位名稱不符合對應的裝置程式碼中的屬性名稱，則裝置無法接收的設定值。

### <a name="commands"></a>命令

新增下列命令，在**命令**頁面：

| 顯示名稱    | 欄位名稱     | 預設逾時 | 資料類型 |
| --------------- | -------------- | --------------- | --------- |
| 倒數計時       | 倒數計時      | 30              | number    |

倒數計時命令中加入下列的輸入的欄位：

| 顯示名稱    | 欄位名稱     | 資料類型 | 值 |
| --------------- | -------------- | --------- | ----- |
| 從計算      | countFrom      | number    | 10    |

完全依照顯示資料表中插入裝置範本輸入欄位名稱。 如果欄位名稱不符合對應的裝置程式碼中的屬性名稱，則裝置無法處理命令。

## <a name="add-a-real-device"></a>新增真實裝置

Azure IoT Central 應用程式中加入您在上一節中建立的裝置範本中的實際的裝置。

然後在 [新增裝置] 教學課程，以遵循指示[產生實際的裝置的連接字串](tutorial-add-device.md#generate-connection-string)。 您在下一節中使用此連接字串：

### <a name="create-a-nodejs-application"></a>建立 Node.js 應用程式

下列步驟示範如何建立用戶端應用程式，以實作您新增至應用程式的真實裝置。 在這裡，Node.js 應用程式代表真實裝置。 

1. 在電腦上建立名為 `connected-air-conditioner-adv` 的資料夾。 在命令列環境中瀏覽至該資料夾。

1. 若要初始化 Node.js 專案，請執行下列命令：

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. 在 `connected-air-conditioner-adv` 資料夾中建立一個名為 **connectedAirConditionerAdv.js** 的檔案。

1. 在 **connectedAirConditionerAdv.js** 檔案的開頭，新增下列 `require` 陳述式：

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. 將下列變數宣告新增至該檔案：

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    更新預留位置`{your device connection string}`具有[裝置連接字串](tutorial-add-device.md#generate-connection-string)。 在此範例中，您會初始化`targetTemperature`為零，您可以使用目前正在讀取的從裝置或裝置對應項的值。

1. 若要傳送的遙測、 狀態和事件的度量，Azure IoT Central 應用程式時，將下列函式加入檔案：

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
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

1. 新增下列程式碼來處理從 IoT Central 應用程式傳送的倒數計時命令：

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');

      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;

      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          client.getTwin((err, twin) => {
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
        client.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
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

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>執行 Node.js 應用程式

在命令列環境中執行下列命令：

```cmd/sh
node connectedAirConditionerAdv.js
```

身為 Azure IoT 中心應用程式的操作者，您可以針對真實裝置：

* 在 [量值] 頁面上檢視遙測：

    ![檢視遙測](media/howto-connect-nodejs/viewtelemetry.png)

* 在 [屬性] 頁面上檢視從裝置傳送的裝置屬性值。 當裝置連線的裝置屬性的磚更新：

    ![檢視裝置屬性](media/howto-connect-nodejs/viewproperties.png)

* 設定從風扇速度和目標溫度**設定**頁面：

    ![設定風扇速度](media/howto-connect-nodejs/setfanspeed.png)

* 呼叫的倒數計時命令**命令**頁面：

    ![呼叫倒數計時命令](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>後續步驟

既然您已了解如何連接至 Azure IoT Central 應用程式的泛型 Node.js 用戶端，建議的下一個步驟是了解如何[準備，並將 Raspberry Pi 連線](howto-connect-raspberry-pi-python.md)。
