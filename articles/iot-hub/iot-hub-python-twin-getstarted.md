---
title: 開始使用 Azure IoT 中樞裝置對應項 (Python) | Microsoft Docs
description: 如何使用 Azure IoT 中樞裝置對應項來新增標籤，然後使用 IoT 中樞查詢。 您可以使用適用於 Python 的 Azure IoT SDK，實作模擬裝置應用程式和服務應用程式，以新增標籤和執行 IoT 中樞查詢。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: kgremban
ms.openlocfilehash: 3e703c999d57cf62064291cf91059a17a959a2c3
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608396"
---
# <a name="get-started-with-device-twins-python"></a>開始使用裝置對應項 (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

在本教學課程結尾，您將會有兩個 Python 主控台應用程式：

* **AddTagsAndQuery.py**，這是 Python 後端應用程式，可新增標籤和查詢裝置對應項。

* **ReportConnectivity.py**，這是會模擬裝置的 Python 應用程式，此裝置會以稍早建立的裝置身分識別連線到您的 IoT 中樞，並報告其連線狀況。

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) 一文提供可用來建置裝置和後端應用程式之 Azure IoT SDK 的相關資訊。

若要完成此教學課程，您需要下列項目：

* [Python 2.x 或 3.x](https://www.python.org/downloads/)。 請務必使用安裝程式所需的 32 位元或 64 位元安裝。 在安裝期間出現系統提示時，務必將 Python 新增至平台特有的環境變數。 如果您是使用 Python 2.x，可能需要[安裝或升級 pip (Python 套件管理系統](https://pip.pypa.io/en/stable/installing/))。

* 如果您是使用 Windows 作業系統，則 [Visual C++ 可轉散發套件](https://www.microsoft.com/download/confirmation.aspx?id=48145)允許使用 Python 的原生 DLL。

* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。)

> [!NOTE]
> `azure-iothub-service-client` 和 `azure-iothub-device-client` 的 pip 套件目前僅適用於 Windows OS。 若為 Linux/Mac OS，請參閱以 Linux 和 Mac OS 特定章節[準備開發環境適用於 Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md)張貼。
>

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>擷取 IoT 中樞的連接字串

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中樞註冊新的裝置

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-the-service-app"></a>建立服務應用程式

在本節中，您會建立 Python 主控台應用程式，將位置中繼資料加入至相關聯的裝置對應項您 **{裝置識別碼}**。 接著，它會選取位於 Redmond 的裝置來查詢儲存在 IoT 中樞的裝置對應項，再查詢會報告行動電話連線的對應項。

1. 開啟命令提示字元並安裝 **Azure IoT Hub Service SDK for Python**。 安裝 SDK 之後，請關閉命令提示字元。

   ```
   pip install azure-iothub-service-client
   ```

2. 使用文字編輯器，建立新的 **AddTagsAndQuery.py** 檔案。

3. 新增下列程式碼，從服務 SDK 匯入必要的模組：

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. 新增下列程式碼，取代預留位置`[IoTHub Connection String]`和`[Device Id]`與 IoT 中樞 」 與 「 前幾節所建立的裝置識別碼的連接字串。
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. 將下列程式碼新增至 **AddTagsAndQuery.py** 檔案：

     ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)

            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)

            print ( "Devices in Redmond: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )

            print ( "" )

            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```

    **Registry** 物件會公開從服務來與裝置對應項進行互動時所需的所有方法。 程式碼會先將 **Registry** 物件初始化，然後針對 **deviceId** 更新裝置對應項，最後執行兩個查詢。 第一個只選取位於 **Redmond43** 工廠之裝置的裝置對應項，第二個會重新調整查詢，只選取也透過行動電話網路連線的裝置。

6. 在 **AddTagsAndQuery.py** 結尾處新增下列程式碼來實作 **iothub_service_sample_run** 函式：

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. 使用下列命令執行應用程式：

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    如果是查詢所有位於 **Redmond43** 中的裝置，您在結果中會看到一個裝置，而如果查詢將結果限於使用行動電話網路的裝置，則您不會看到任何裝置。

    ![第一個查詢在里德蒙，顯示所有裝置](./media/iot-hub-python-twin-getstarted/1-device-twins-python-service-sample.png)

在下一節，您將建立一個裝置應用程式，以報告連線資訊並變更上一節的查詢結果。

## <a name="create-the-device-app"></a>建立裝置應用程式

在本節中，您會建立 Python 主控台應用程式連接到您的中樞，作為您 **{裝置識別碼}**，然後更新其裝置對應項所報告屬性，以包含它連接使用行動電話通訊網路的資訊。

1. 開啟命令提示字元並安裝 **Azure IoT Hub Service SDK for Python**。 安裝 SDK 之後，請關閉命令提示字元。

    ```
    pip install azure-iothub-device-client
    ```

2. 使用文字編輯器，建立新的 **ReportConnectivity.py** 檔案。

3. 新增下列程式碼，從服務 SDK 匯入必要的模組：

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

4. 新增下列程式碼，使用您在先前小節中為 IoT 中樞裝置建立的連接字串來取代 `[IoTHub Device Connection String]` 的預留位置。

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

5. 將下列程式碼新增至 **ReportConnectivity.py** 檔案來實作裝置對應項功能：

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "" )
        print ( "Twin callback called with:" )
        print ( "    updateStatus: %s" % update_state )
        print ( "    payload: %s" % payload )

    def send_reported_state_callback(status_code, user_context):
        print ( "" )
        print ( "Confirmation for reported state called with:" )
        print ( "    status_code: %d" % status_code )

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_twin_callback(
                device_twin_callback, TWIN_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            if client.protocol == IoTHubTransportProvider.MQTT:
                print ( "Sending data as reported property..." )

                reported_state = "{\"connectivity\":\"cellular\"}"

                client.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "Press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= TIMER_COUNT:
                    status = client.get_send_status()
                    time.sleep(10)
                    status_counter += 1 
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
     ```

    **Client** 物件會公開從裝置來與裝置對應項進行互動時所需的所有方法。 先前的程式碼在初始化 **Client** 物件之後，會擷取您裝置的裝置對應項，並以連線資訊來更新其報告屬性。

6. 在 **ReportConnectivity.py** 結尾處新增下列程式碼來實作 **iothub_client_sample_run** 函式：

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

7. 執行裝置應用程式︰

    ```cmd/sh
    python ReportConnectivity.py
    ```

    您應該會看到裝置對應項已更新的確認。

    ![更新對應項](./media/iot-hub-python-twin-getstarted/2-python-client-sample.png)

8. 現在，裝置已回報其連線資訊，它應該會出現在這兩個查詢中。 請返回並再次執行查詢：

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    此時您 **{裝置識別碼}** 應該會出現在這兩個查詢結果。

    ![第二個查詢](./media/iot-hub-python-twin-getstarted/3-device-twins-python-service-sample.png)

## <a name="next-steps"></a>後續步驟

在此教學課程中，您在 Azure 入口網站中設定了新的 IoT 中樞，然後在 IoT 中樞的身分識別登錄中建立了裝置身分識別。 您已從後端應用程式將裝置中繼資料新增為標籤，並撰寫模擬裝置應用程式來報告裝置對應項中的裝置連線資訊。 您也已經了解如何使用登錄來查詢此資訊。

使用下列資源來了解如何：

* 利用[開始使用 IoT 中樞](quickstart-send-telemetry-python.md)教學課程，傳送裝置的遙測資料。

* 設定裝置使用裝置對應項的所需的屬性[使用所需屬性來設定裝置](tutorial-device-twins.md)教學課程。

* 用來控制裝置以互動方式 （例如開啟風扇從使用者控制的應用程式），[使用直接方法](quickstart-control-device-python.md)教學課程。