---
title: 開始使用 Azure IoT 中樞裝置管理 (Python) | Microsoft Docs
description: 如何使用 IoT 中樞裝置管理來起始遠端裝置重新開機。 您可以使用適用於 Python 的 Azure IoT SDK，實作模擬裝置應用程式 (包含直接方法) 和服務應用程式 (叫用直接方法)。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: eb5085db10c5763a4173f460eabde6afcccd5aff
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000456"
---
# <a name="get-started-with-device-management-python"></a>開始使用裝置管理 (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

本教學課程說明如何：

* 使用 Azure 入口網站來建立 IoT 中樞，並且在 IoT 中樞建立裝置識別。

* 建立模擬裝置應用程式，其包含可將該裝置重新開機的直接方法。 直接方法是從雲端叫用。

* 建立 Python 主控台應用程式，可透過您的 IoT 中樞在模擬的裝置應用程式中呼叫重新啟動直接方法。

在本教學課程結尾，您會有兩個 Python 主控台應用程式：

* **dmpatterns_getstarted_device.py**，它會以先前建立的裝置識別連線到您的 IoT 中樞，接收重新啟動直接方法，模擬實體重新啟動，並報告上一次重新啟動的時間。

* **dmpatterns_getstarted_service.py**，它會在模擬裝置應用程式上呼叫直接方法，顯示回應，並顯示更新的報告屬性。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>必要條件

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中樞註冊新的裝置

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式

在本節中，您可：

* 建立 Python 主控台應用程式，以回應雲端所呼叫的直接方法

* 模擬裝置重新啟動

* 使用報告屬性來啟用裝置對應項查詢，以識別裝置及其上次重新啟動時間

1. 在命令提示字元中，執行下列命令來安裝**azure iot 裝置**套件：

    ```cmd/sh
    pip install azure-iot-device
    ```

   > [!NOTE]
   > Iothub-服務用戶端的 pip 套件僅適用于 Windows 作業系統。 針對 Linux/Mac OS，請參閱[準備適用于 Python 的開發環境](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md)文章的 Linux 和 Mac os 特定章節。
   >

2. 使用文字編輯器，在工作目錄中建立名為**dmpatterns_getstarted_device .py**的檔案。

3. 在 **dmpatterns_getstarted_device.py** 檔案的開頭新增下列 `import` 陳述式。

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. 新增**CONNECTION_STRING**變數。 將`{deviceConnectionString}`預留位置值取代為您的裝置連接字串。 您先前已在[註冊 IoT 中樞的新裝置](#register-a-new-device-in-the-iot-hub)中複製此連接字串。  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. 新增下列函式回呼以在裝置上實作直接方法。

    ```python
    def reboot_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("rebootDevice")  # blocking call

            # Act on the method by rebooting the device...
            print( "Rebooting device" )
            time.sleep(20)
            print( "Device rebooted")

            # ...and patching the reported properties
            current_time = str(datetime.datetime.now())
            reported_props = {"rebootTime": current_time}
            client.patch_twin_reported_properties(reported_props)
            print( "Device twins updated with latest rebootTime")

            # Send a method response indicating the method request was resolved
            resp_status = 200
            resp_payload = {"Response": "This is the response from the device"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

6. 啟動直接方法接聽程式並等待。

    ```python
    def iothub_client_init():
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            # Start a thread listening for "rebootDevice" direct method invocations
            reboot_listener_thread = threading.Thread(target=reboot_listener, args=(client,))
            reboot_listener_thread.daemon = True
            reboot_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. 儲存並關閉 **dmpatterns_getstarted_device.py** 檔案。

> [!NOTE]
> 為了簡單起見，本教學課程不會實作任何重試原則。 在生產環境程式碼中，您應該如[暫時性錯誤處理](/azure/architecture/best-practices/transient-faults)一文中所建議，實作重試原則 (例如指數型輪詢)。

## <a name="get-the-iot-hub-connection-string"></a>取得 IoT 中樞連接字串

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>使用直接方法在裝置上觸發遠端重新啟動

在本節中，您會建立 Python 主控台應用程式，此應用程式會使用直接方法起始遠端重新開機。 應用程式使用裝置對應項查詢來探索該裝置的上次重新開機時間。

1. 在命令提示字元上執行下列命令，以安裝 **azure-iot-service-client** 套件：

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

   > [!NOTE]
   > Azure iothub-服務用戶端和 iothub 裝置用戶端的 pip 套件目前僅適用于 Windows 作業系統。 針對 Linux/Mac OS，請參閱[準備適用于 Python 的開發環境](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md)文章的 Linux 和 Mac os 特定章節。
   >

2. 使用文字編輯器，在工作目錄中建立名為**dmpatterns_getstarted_service .py**的檔案。

3. 在 **dmpatterns_getstarted_service.py** 檔案的開頭新增下列 `import` 陳述式。

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

4. 新增下列變數宣告。 將預留位置值取代為您先前在[取得 iot 中樞連接字串](#get-the-iot-hub-connection-string)中所複製的 iot 中樞連接字串。 `{IoTHubConnectionString}` 以您在[IoT 中樞註冊新裝置](#register-a-new-device-in-the-iot-hub)中註冊的裝置識別碼取代預留位置值。`{deviceId}`

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. 新增下列函式來叫用裝置方法，以重新啟動目標裝置，然後查詢裝置對應項並取得上次重新啟動時間。

    ```python
    def iothub_devicemethod_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                    if twin_info.find("rebootTime") != -1:
                        print ( "Last reboot time: " + twin_info[twin_info.find("rebootTime")+11:twin_info.find("rebootTime")+37])
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

6. 儲存並關閉 **dmpatterns_getstarted_service.py** 檔案。

## <a name="run-the-apps"></a>執行應用程式

您現在已經準備好執行應用程式。

1. 在命令提示字元中，執行下列命令來開始接聽重新啟動直接方法。

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. 在另一個命令提示字元中，執行下列命令以觸發裝置對應項的遠端重新啟動，以及查詢裝置對應項來尋找上次重新啟動時間。

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. 您會在主控台中看到直接方法的裝置回應。

   以下顯示對重新開機直接方法的裝置回應：

   ![模擬裝置應用程式輸出](./media/iot-hub-python-python-device-management-get-started/device.png)

   以下顯示的服務會呼叫重新開機直接方法，並輪詢裝置對應項的狀態：

   ![觸發程式重新開機服務輸出](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]