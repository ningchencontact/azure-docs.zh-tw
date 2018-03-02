---
title: "使用 Azure IoT 中樞 (Python) 進行裝置韌體更新 | Microsoft Docs"
description: "如何使用 Azure IoT 中樞上的裝置管理來起始裝置韌體更新。 您可以使用適用於 Python 的 Azure IoT SDK，實作模擬裝置應用程式和服務應用程式，以觸發韌體更新。"
services: iot-hub
documentationcenter: .net
author: msebolt
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2018
ms.author: v-masebo
ms.openlocfilehash: e7ca0d95da3fd36e6dad081bad0aa5a178436159
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-pythonpython"></a>使用裝置管理來起始裝置韌體更新 (Python/Python)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

在[開始使用裝置管理][lnk-dm-getstarted]教學課程中，您已了解如何使用[裝置對應項][lnk-devtwin]和[直接方案][lnk-c2dmethod]基礎，從遠端重新啟動裝置。 本教學課程會使用相同的 IoT 中樞基礎，並且提供指引和示範如何進行端對端模擬韌體更新。  此模式用於 Intel Edison 裝置範例的韌體更新實作。

本教學課程說明如何：

* 建立 Python 主控台應用程式，以透過您的 IoT 中樞在模擬裝置應用程式中呼叫 firmwareUpdate 直接方法。
* 建立會實作 **firmwareUpdate** 直接方法的模擬裝置應用程式。 此方法會起始多階段程序，此程序會等候下載韌映像、下載韌體映像，最後再套用韌體映像。 在更新的每個階段，裝置都會使用回報的屬性來回報進度。

在本教學課程結尾，您會有兩個 Python 主控台應用程式：

**dmpatterns_fwupdate_service.py**，在模擬裝置應用程式中呼叫直接方法、顯示回應，並定期 (每 500 毫秒) 顯示更新的報告屬性。

**dmpatterns_fwupdate_device.py**，這會將您的 IoT 中樞連線至稍早建立的裝置身分識別，接收 firmwareUpdate 直接方法，透過多重狀態處理執行以模擬韌體更新，包括等待映像下載、下載新的映像，最後套用映像。

若要完成此教學課程，您需要下列項目：

* [Python 2.x 或 3.x][lnk-python-download]。 請務必使用安裝程式所需的 32 位元或 64 位元安裝。 在安裝期間出現系統提示時，務必將 Python 新增至平台特有的環境變數。 如果您使用 Python 2.x，則可能需要[安裝或升級 *pip*(Python 套件管理系統][lnk-install-pip])。
* 如果您使用 Windows 作業系統，則 [Visual c + + 可轉散發套件][lnk-visual-c-redist] 允許使用 Python 的原生 DLL。
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>使用直接方法在裝置上觸發遠端韌體更新
在本節中，您會建立 Python 主控台應用程式，此應用程式會在裝置上起始遠端韌體更新。 此應用程式使用直接方法來起始更新，並使用裝置對應項查詢來定期取得作用中韌體更新的狀態。

1. 在命令提示字元上執行下列命令，以安裝 **azure-iothub-service-client** 套件：
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. 使用文字編輯器，在您的工作目錄中建立 **dmpatterns_getstarted_service.py** 檔案。

1. 在 **dmpatterns_getstarted_service.py** 檔案的開頭新增下列 'import' 陳述式。 將 `IoTHubConnectionString` 和 `deviceId` 取代為您先前所述的值：
   
    ```python
    import sys
    import time

    import iothub_service_client
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "firmwareUpdate"
    METHOD_PAYLOAD = "{\"fwPackageUri\":\"test.com\"}"
    TIMEOUT = 60
    MESSAGE_COUNT = 5
    ```

1. 新增下列函式來呼叫直接方法，並顯示 firmwareUpdate 報告屬性的值。 另新增 `main` 常式：
   
    ```python
    def iothub_firmware_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            print ( "" )
            print ( "Direct Method called." )
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
        
            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)
            print ( response.payload )
        
            print ( "" )
            print ( "Device Twin queried, press Ctrl-C to exit" )
            while True:
                twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                if "\"firmwareStatus\":\"standBy\"" in twin_info:
                    print ( "Waiting on device..." )
                elif "\"firmwareStatus\":\"downloading\"" in twin_info:
                    print ( "Downloading firmware..." )
                elif "\"firmwareStatus\":\"applying\"" in twin_info:
                    print ( "Download complete, applying firmware..." )
                elif "\"firmwareStatus\":\"completed\"" in twin_info:
                    print ( "Firmware applied" )
                    print ( "" )
                    print ( "Get reported properties from device twin:" )
                    print ( twin_info )
                    break
                else:
                    print ( "Unknown status" )

                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub firmware update Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_firmware_sample_run()
    ```

1. 儲存並關閉 **dmpatterns_fwupdate_service.py** 檔案。


## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式
在本節中，您可：

* 建立 Python 主控台應用程式，以回應雲端所呼叫的直接方法
* 觸發模擬韌體更新
* 使用報告屬性來啟用裝置對應項查詢，以識別裝置及其上次完成韌體更新的時間

1. 在命令提示字元上執行下列命令，以安裝 **azure-iothub-device-client** 套件：
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. 使用文字編輯器，建立 **dmpatterns_fwupdate_device.py** 檔案。

1. 在 **dmpatterns_fwupdate_device.py** 檔案開頭新增下列 'import' 陳述式。 將 `deviceConnectionString` 取代為您 IoT 中樞的裝置連接字串：
   
    ```python
    import time, datetime
    import sys
    import threading

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    MESSAGE_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)
    ```

1. 新增下列用來提供報告屬性更新的函式，並實作直接方法：
   
    ```python
    def send_reported_state_callback(status_code, user_context):
        print ( "Reported state updated." )

    def device_method_callback(method_name, payload, user_context):
        if method_name == "firmwareUpdate":
            print ( "Starting firmware update." )
            image_url = payload
            thr = threading.Thread(target=simulate_download_image, args=([payload]), kwargs={})
            thr.start()
    
            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"Firmware update started\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. 新增下列函式，以模擬韌體映像的下載和套用：
   
    ```python
    def simulate_download_image(image_url):
        time.sleep(15)
        print ( "Downloading image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"downloading\", \"downloadComplete\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_apply_image(image_url)
    
    def simulate_apply_image(image_url):
        print ( "Applying image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"applying\", \"startedApplyingImage\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_complete_image()

    def simulate_complete_image():
        print ( "Image applied." )

        reported_state = "{\"firmwareStatus\":\"completed\", \"lastFirmwareUpdate\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
    ```

8. 新增下列初始化裝置對應項報告屬性的函式，並等候呼叫直接方法。 另新增 `main` 常式：
   
    ```python
    def iothub_firmware_sample_run():
        try:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

            reported_state = "{\"firmwareStatus\":\"standBy\", \"logTime\":\"" + str(datetime.datetime.now()) + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
            print ( "Device twins initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )
        
            while True:
                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
                    time.sleep(10)
                    status_counter += 1
            
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python firmware update sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_firmware_sample_run()
    ```

> [!NOTE]
> 為了簡單起見，本教學課程不會實作任何重試原則。 在實際程式碼中，您應該如 MSDN 文章[暫時性錯誤處理](https://msdn.microsoft.com/library/hh675232.aspx)所建議，實作重試原則 (例如指數型輪詢)。
> 


## <a name="run-the-apps"></a>執行應用程式
您現在可以開始執行應用程式。

1. 在命令提示字元中，執行下列命令來開始接聽重新啟動直接方法。
   
    ```cmd/sh
    python dmpatterns_fwupdate_device.py
    ```

1. 在另一個命令提示字元中，執行下列命令以觸發裝置對應項的遠端重新啟動，以及查詢裝置對應項來尋找上次重新啟動時間。
   
    ```cmd/sh
    python dmpatterns_fwupdate_service.py
    ```

1. 您會在主控台中看到直接方法的裝置回應。 然後記下整個韌體更新的報告屬性之變更。

    ![程式輸出][1]


## <a name="next-steps"></a>後續步驟
在此教學課程中，您使用直接方法來觸發裝置上的遠端韌體更新，並且使用回報的屬性追蹤韌體更新的進度。

若要了解如何擴充您的 IoT 解決方案以及在多個裝置上排程方法呼叫，請參閱[排程及廣播作業][lnk-tutorial-jobs]教學課程。

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[1]: ./media/iot-hub-python-python-firmware-update/1.png
