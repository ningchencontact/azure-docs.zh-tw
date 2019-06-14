---
title: 使用 Azure IoT 中樞傳送雲端到裝置訊息 (Python) | Microsoft Docs
description: 如何使用適用於 Python 的 Azure IoT SDK，將雲端到裝置訊息從 Azure IoT 中樞傳送到裝置。 您可以修改模擬裝置應用程式，以接收雲端到裝置訊息，也可以修改後端應用程式，以傳送雲端到裝置訊息。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: kgremban
ms.openlocfilehash: 7ac668bdbc3698be3ed2aa50a428cef84e68369a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61441359"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>使用 IoT 中樞傳送雲端到裝置訊息 (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>簡介
Azure IoT 中樞是一項完全受控的服務，有助於讓數百萬個裝置和一個解決方案後端進行可靠且安全的雙向通訊。 [開始使用 IoT 中樞](quickstart-send-telemetry-python.md)快速入門示範如何建立 IoT 中樞、 佈建裝置識別，以及編寫模擬的裝置應用程式，以傳送裝置到雲端訊息。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教學課程是以 [開始使用 IoT 中樞](quickstart-send-telemetry-python.md)為基礎。 這會說明如何：

* 從您的解決方案後端，透過 IoT 中樞將雲端到裝置訊息傳送給單一裝置。

* 接收裝置上的雲端到裝置訊息。

* 從您的解決方案後端，要求確認收到從 IoT 中樞傳送到裝置的訊息 (「意見反應」  )。

您可以找到更多有關雲端到裝置訊息[IoT 中樞開發人員指南](iot-hub-devguide-messaging.md)。

在本教學課程結尾，您會執行兩個 Python 主控台應用程式：

* **SimulatedDevice.py**，這是在[開始使用 IoT 中樞](quickstart-send-telemetry-python.md)建立之應用程式的修改版本，可連線到您的 IoT 中樞，並接收雲端到裝置的訊息。

* **SendCloudToDeviceMessage.py**，會透過 IoT 中樞，將雲端到裝置訊息傳送到模擬裝置應用程式，然後接收其傳遞通知。

> [!NOTE]
> 「IoT 中樞」透過 Azure IoT 裝置 SDK 為許多裝置平台和語言 (包括 C、Java 及 Javascript) 提供 SDK 支援。 如需有關如何將您的裝置與本教學課程中的程式碼連接 (通常是連接到「Azure IoT 中樞」) 的逐步指示，請參閱 [Azure IoT 開發人員中樞](https://www.azure.com/develop/iot)。
>

若要完成此教學課程，您需要下列項目：

* [Python 2.x 或 3.x](https://www.python.org/downloads/)。 請務必使用安裝程式所需的 32 位元或 64 位元安裝。 在安裝期間出現系統提示時，務必將 Python 新增至平台特有的環境變數。 如果您是使用 Python 2.x，可能需要[安裝或升級 pip  (Python 套件管理系統](https://pip.pypa.io/en/stable/installing/))。

* 如果您是使用 Windows 作業系統，則 [Visual C++ 可轉散發套件](https://www.microsoft.com/download/confirmation.aspx?id=48145)允許使用 Python 的原生 DLL。

* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。)

> [!NOTE]
> `azure-iothub-service-client` 和 `azure-iothub-device-client` 的 pip  套件目前僅適用於 Windows OS。 若為 Linux/Mac OS，請參閱以 Linux 和 Mac OS 特定章節[準備開發環境適用於 Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md)張貼。
>

## <a name="receive-messages-in-the-simulated-device-app"></a>在模擬的裝置應用程式中接收訊息

在本節中，您會建立一個 Python 主控台應用程式，來模擬裝置並接收來自 IoT 中樞的雲端到裝置訊息。

1. 使用文字編輯器，建立 **SimulatedDevice.py** 檔案。

2. 在 **SimulatedDevice.py** 檔案開頭新增下列 `import` 陳述式和變數：

   ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError

    RECEIVE_CONTEXT = 0
    WAIT_COUNT = 10
    RECEIVED_COUNT = 0
    RECEIVE_CALLBACKS = 0
    ```

3. 將下列程式碼新增至 **SimulatedDevice.py** 檔案。 "{DeviceConnectionString}"預留位置值取代為您在中建立裝置的裝置連接字串[開始使用 IoT 中樞](quickstart-send-telemetry-python.md)快速入門：

    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. 新增下列函式，將接收的訊息列印至主控台：

    ```python
    def receive_message_callback(message, counter):
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "Received Message [%d]:" % counter )
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        counter += 1
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        return IoTHubMessageDispositionResult.ACCEPTED

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def print_last_message_time(client):
        try:
            last_message = client.get_last_message_receive_time()
            print ( "Last Message: %s" % time.asctime(time.localtime(last_message)) )
            print ( "Actual time : %s" % time.asctime() )
        except IoTHubClientError as iothub_client_error:
            if iothub_client_error.args[0].result == IoTHubClientResult.INDEFINITE_TIME:
                print ( "No message received" )
            else:
                print ( iothub_client_error )
    ```

5. 新增下列程式碼，將用戶端初始化，並等候以接收雲端到裝置訊息：

    ```python
    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    status = client.get_send_status()
                    print ( "Send status: %s" % status )
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

        print_last_message_time(client)
    ```

6. 新增下列 main 函式：

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

7. 儲存並關閉 **SimulatedDevice.py** 檔案。

## <a name="send-a-cloud-to-device-message"></a>傳送雲端到裝置訊息

在本節中，您會建立一個 Python 主控台應用程式，將雲端到裝置訊息傳送到模擬裝置應用程式。 您需要您在新增裝置的裝置識別碼[開始使用 IoT 中樞](quickstart-send-telemetry-python.md)快速入門。 您也需要中樞的 IoT 中樞連接字串 (可在 [Azure 入口網站](https://portal.azure.com)中找到)。

1. 使用文字編輯器，建立 **SendCloudToDeviceMessage.py** 檔案。

2. 在 **SendCloudToDeviceMessage.py** 檔案開頭新增下列 `import` 陳述式和變數：

    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

3. 將下列程式碼新增至 **SendCloudToDeviceMessage.py** 檔案。 "{IoTHubConnectionString}"預留位置值取代為您在建立中樞的 IoT 中樞連接字串[開始使用 IoT 中樞](quickstart-send-telemetry-python.md)快速入門。 "{DeviceId}"預留位置取代為您在新增裝置的裝置識別碼[開始使用 IoT 中樞](quickstart-send-telemetry-python.md)快速入門：

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. 新增下列函式，將意見反應訊息列印至主控台：

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. 加入下列程式碼，當裝置收到雲端到裝置訊息時，會將訊息傳送至您的裝置，並處理意見反應訊息︰

    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

6. 新增下列 main 函式：

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. 儲存並關閉 **SendCloudToDeviceMessage.py** 檔案。

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行應用程式。

1. 開啟命令提示字元並安裝**適用於 Python 的 Azure IoT 中樞裝置 SDK**。

    ```
    pip install azure-iothub-device-client
    ```

2. 在命令提示字元中，執行下列命令，以接聽雲端到裝置訊息：

    ```shell
    python SimulatedDevice.py
    ```

    ![執行模擬裝置應用程式](./media/iot-hub-python-python-c2d/simulated-device.png)

3. 開啟新的命令提示字元並安裝**適用於 Python 的 Azure IoT 裝中樞服務 SDK**。

    ```
    pip install azure-iothub-service-client
    ```

4. 在命令提示字元中，執行下列命令，以傳送雲端到裝置訊息並等候訊息意見反應：

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![執行應用程式以傳送雲端到裝置命令](./media/iot-hub-python-python-c2d/send-command.png)

5. 請注意裝置所接收的訊息。

    ![已接收的訊息](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何傳送和接收雲端到裝置的訊息。

若要查看使用 IoT 中樞的完整端對端解決方案範例，請參閱 [Azure IoT 遠端監視解決方案加速器](https://azure.microsoft.com/documentation/suites/iot-suite/)。

若要深入了解如何使用 IoT 中樞開發解決方案，請參閱 [IoT 中樞開發人員指南](iot-hub-devguide.md)。
