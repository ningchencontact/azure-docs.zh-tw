---
title: 使用 Azure IoT 中樞傳送雲端到裝置訊息 (Node) | Microsoft Docs
description: 如何使用適用於 Node.js 的 Azure IoT SDK，將雲端到裝置訊息從 Azure IoT 中樞傳送至裝置。 您可以修改模擬裝置應用程式，以接收雲端到裝置訊息，也可以修改後端應用程式，以傳送雲端到裝置訊息。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: baefd05b562d688b662bf988c7b36a0e9cd154b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61441815"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>使用 IoT 中樞 (Node) 傳送雲端到裝置訊息
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>簡介
Azure IoT 中心是一项完全托管的服务，有助于在数百万台设备和单个解决方案后端之间实现安全可靠的双向通信。 [開始使用 IoT 中樞](quickstart-send-telemetry-node.md) 教學課程會示範如何建立 IoT 中樞、在其中佈建裝置識別，以及編寫模擬的裝置應用程式，以傳送裝置到雲端的訊息。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教程是在 [IoT 中心入门](quickstart-send-telemetry-node.md)的基础上编写的。 其中了说明了如何：

* 從您的解決方案後端，透過 IoT 中樞將雲端到裝置訊息傳送給單一裝置。
* 接收裝置上的雲端到裝置訊息。
* 從您的解決方案後端，要求確認收到從 IoT 中樞傳送到裝置的訊息 (「意見反應」)。

您可以找到更多有關雲端到裝置訊息[IoT 中樞開發人員指南](iot-hub-devguide-messaging.md)。

在本教學課程結尾處，您會執行兩個 Node.js 主控台應用程式：

* **SimulatedDevice**，这是在 [IoT 中心入门](quickstart-send-telemetry-node.md)中创建的应用的修改版本，可连接到 IoT 中心并接收云到设备的消息。

* **SendCloudToDeviceMessage**：會透過 IoT 中樞，將雲端到裝置訊息傳送到模擬裝置應用程式，然後接收其傳遞通知。

> [!NOTE]
> IoT 中心通过 Azure IoT 设备 SDK 对许多设备平台和语言（包括 C、Java 和 Javascript）提供 SDK 支持。 有关如何将设备连接到本教程的代码以及通常如何连接到 Azure IoT 中心的分步说明，请参阅 [Azure IoT 开发人员中心](https://azure.microsoft.com/develop/iot)。
>

若要完成此教學課程，您需要下列項目：

* Node.js 4.0.x 版或更新版本。
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial)。)

## <a name="receive-messages-in-the-simulated-device-app"></a>在模擬的裝置應用程式中接收訊息

在本節中，您會修改在[開始使用 IoT 中樞](quickstart-send-telemetry-node.md)中建立的模擬裝置應用程式，以接收來自 IoT 中樞的雲端對裝置訊息。

1. 使用文字編輯器開啟 SimulatedDevice.js 檔案。

2. 修改 **connectCallback** 函式來處理從 IoT 中樞傳送的訊息。 在本示例中，设备始终调用 **complete** 函数，以通知 IoT 中心它已处理消息。 新版的 **connectCallback** 函式看起來會像下列程式碼片段︰
   
    ```javascript
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var temperature = 20 + (Math.random() * 15);
            var humidity = 60 + (Math.random() * 20);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
            var message = new Message(data);
            message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
  
   > [!NOTE]
   > 如果您使用 HTTPS 而不是使用 MQTT 或 AMQP 作為傳輸，**DeviceClient** 執行個體將不會經常 (頻率低於每隔 25 分鐘) 檢查「IoT 中樞」是否有訊息。 如需有關 MQTT、 AMQP 及 HTTPS 支援，以及 IoT 中樞節流的差異的詳細資訊，請參閱 < [IoT 中樞開發人員指南](iot-hub-devguide-messaging.md)。
   >

## <a name="send-a-cloud-to-device-message"></a>傳送雲端到裝置訊息

在本節中，您會建立 Node.js 主控台應用程式，將雲端到裝置訊息傳送至模擬裝置應用程式。 需要使用 [IoT 中心入门](quickstart-send-telemetry-node.md)教程中添加的设备的设备 ID。 您也需要中樞的 IoT 中樞連接字串 (可在 [Azure 入口網站](https://portal.azure.com)中找到)。

1. 创建名为 **sendcloudtodevicemessage**的空文件夹。 在 **sendcloudtodevicemessage** 資料夾中，於命令提示字元使用下列命令建立 package.json 檔案。 接受所有預設值：

    ```shell
    npm init
    ```

2. 在命令提示字元中，於 **sendcloudtodevicemessage** 資料夾中執行下列命令以安裝 **azure-iothub** 套件：
   
    ```shell
    npm install azure-iothub --save
    ```

3. 使用文字編輯器，在 **sendcloudtodevicemessage** 資料夾中建立 **SendCloudToDeviceMessage.js** 檔案。

4. 在 **SendCloudToDeviceMessage.js** 文件的开头添加以下 `require` 语句：
   
    ```javascript
    'use strict';
   
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. 在 **SendCloudToDeviceMessage.js** 檔案中新增下列程式碼。 將 "{IoT 中樞連接字串}" 預留位置的值，替換為您在[開始使用 IoT 中樞](quickstart-send-telemetry-node.md)教學課程中所建立中樞的 IoT 中樞連接字串。 將 "{裝置識別碼}" 預留位置替換為您在[開始使用 IoT 中樞](quickstart-send-telemetry-node.md)教學課程中所新增裝置的裝置識別碼：
   
    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. 加入下列函式來將作業結果列印至主控台︰
   
    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. 加入下列函式來將傳遞意見反應訊息列印至主控台︰
   
    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. 加入下列程式碼，當裝置收到雲端到裝置訊息時，會將訊息傳送至您的裝置，並處理意見反應訊息︰
   
    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. 儲存並關閉 **SendCloudToDeviceMessage.js** 檔案。

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行應用程式。

1. 在命令提示字元中，於 **simulateddevice** 資料夾中執行下列命令，將遙測傳送至 IoT 中樞並接聽雲端到裝置訊息：
   
    ```shell
    node SimulatedDevice.js 
    ```
   
    ![執行模擬裝置應用程式](./media/iot-hub-node-node-c2d/receivec2d.png)

2. 在命令提示字元中，於 **sendcloudtodevicemessage** 資料夾中執行下列命令，以傳送雲端到裝置訊息並等候通知的意見反應︰
   
    ```shell
    node SendCloudToDeviceMessage.js 
    ```
   
    ![執行應用程式以傳送雲端到裝置命令](./media/iot-hub-node-node-c2d/sendc2d.png)
   
   > [!NOTE]
   > 為了簡單起見，本教學課程不會實作任何重試原則。 在生產環境程式碼中，您應該如[暫時性錯誤處理](/azure/architecture/best-practices/transient-faults)文章所建議，實作重試原則 (例如指數型輪詢)。
   >

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何傳送和接收雲端到裝置的訊息。 

若要查看使用 IoT 中樞的完整端對端解決方案範例，請參閱 [Azure IoT 遠端監視解決方案加速器](https://azure.microsoft.com/documentation/suites/iot-suite/)。

若要深入了解如何使用 IoT 中樞開發解決方案，請參閱 [IoT 中樞開發人員指南](iot-hub-devguide.md)。