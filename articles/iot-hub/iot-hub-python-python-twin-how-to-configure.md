---
title: 使用 Azure IoT 中樞裝置對應項屬性 (Python) | Microsoft Docs
description: 如何使用 Azure IoT 中樞裝置對應項來設定裝置。 您可以使用適用於 Python 的 Azure IoT SDK，實作模擬裝置應用程式和服務應用程式，以修改使用裝置對應項的裝置組態。
services: iot-hub
documentationcenter: .net
author: kgremban
manager: timlt
editor: ''
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2018
ms.author: kgremban
ms.openlocfilehash: 0ba614ab26caad31a045849d85a1abcbb5a9f818
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="use-desired-properties-to-configure-devices-python"></a>使用所需屬性來設定裝置 (Python)
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

在本教學課程結尾，您將會有兩個 Python 主控台應用程式：

* **SimulateDeviceConfiguration.py**，這是模擬裝置應用程式，可等候所需的設定更新，並報告模擬設定更新程序的狀態。
* **SetDesiredConfigurationAndQuery.py**，這是 Python 後端應用程式，可在裝置上設定所需的設定，並查詢設定更新程序。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 一文提供可用來建置裝置和後端應用程式之 Azure IoT SDK 的相關資訊。
> 
> 

若要完成此教學課程，您需要下列項目：

* [Python 2.x 或 3.x][lnk-python-download]。 請務必使用安裝程式所需的 32 位元或 64 位元安裝。 在安裝期間出現系統提示時，務必將 Python 新增至平台特有的環境變數。 如果您使用 Python 2.x，則可能需要[安裝或升級 *pip*(Python 套件管理系統][lnk-install-pip])。
* 如果您使用 Windows 作業系統，則 [Visual c + + 可轉散發套件][lnk-visual-c-redist] 允許使用 Python 的原生 DLL。
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。)

如果您依照[開始使用裝置對應項][lnk-twin-tutorial]教學課程進行，您便已經有一個 IoT 中樞和一個稱為 **myDeviceId** 的裝置身分識別，您可以直接跳到[建立模擬裝置應用程式][lnk-how-to-configure-createapp]一節。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-simulated-device-app"></a>建立模擬裝置應用程式
在本節中，您將建立 Python 主控台應用程式，此應用程式會以 **myDeviceId** 身分連線到您的中樞、等候所需的設定更新，然後回報模擬設定更新程序上的更新。

1. 在您的命令提示字元中使用下列命令來安裝 **Azure IoT Python 裝置 SDK**：
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. 使用文字編輯器，建立新的 **SimulateDeviceConfiguration.py** 檔案。

1. 將下列程式碼新增至 **SimulateDeviceConfiguration.py** 檔案，並以您建立 **myDeviceId** 裝置身分識別時所複製的裝置連接字串，取代 **{device connection string}** 預留位置︰

    ```python   
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0

    TWIN_CALLBACKS = 0
    SEND_REPORTED_STATE_CALLBACKS = 0

    CONFIG_ID = "1"
    TWIN_PAYLOAD = "{\"configId\":" + CONFIG_ID + ",\"sendFrequency\":\"24h\"}"
    ```

    **CLIENT** 物件會公開從裝置與裝置對應項進行互動時所需的所有方法。 在進一步的程式碼中，您會附加所需屬性上的更新處理常式，然後新增額外的處理常式，以藉由比較 configIds 確認有實際的設定變更要求，從而叫用方法以啟動設定變更。 為了簡單起見，先前的程式碼是使用硬式編碼的預設值作為初始設定。 實際的應用程式可能會從本機儲存體載入該組態。
   
   > [!IMPORTANT]
   > 所需的屬性變更事件永遠會在裝置連線時發出一次，在執行任何動作之前，請務必檢查所需的屬性有實際的變更。
   > 

1. 將下列程式碼新增至 **SimulateDeviceConfiguration.py** 檔案結尾：

    ```python
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global CONFIG_ID

        desired_configId = payload[payload.find("configId")+11:payload.find("configId")+12]
        if CONFIG_ID != desired_configId:
            print ( "" )
            print ( "Reported pending config change: %s" % payload)
        
            desired_sendFrequency = payload[payload.find("sendFrequency")+17:payload.find("sendFrequency")+19]
            print ( "...desired configId: " + desired_configId)
            print ( "...desired sendFrequency: " + desired_sendFrequency)
            new_payload = "{\"configId\":" + desired_configId + ",\"sendFrequency\":\"" + desired_sendFrequency + "\"}"
            CLIENT.send_reported_state(new_payload, len(new_payload), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        
        CONFIG_ID = desired_configId
    
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS
    
        print ( "" )
        print ( "Device twins updated." )
    ```
   
    **device_twin_callback** 方法會依設定更新要求及設定 _configId_，在本機裝置對應項物件上更新所報告的屬性。 成功更新裝置對應項之後，會列印更新訊息。 為了節省頻寬，更新所報告的屬性時，只會指定要修改的屬性 (在上述程式碼中名為 **TWIN_PAYLOAD**)，而不是取代整份文件。
   
   > [!NOTE]
   > 本教學課程不會模擬並行組態更新的任何行為。 某些組態更新程序可能在更新執行時能夠接受目標組態的變更，其他程序可能必須將變更排入佇列，還有其他程序可能會因錯誤條件而拒絕變更。 請務必考慮您的特定程序所需的行為，並在初始化組態變更之前新增適當的邏輯。
   > 

1. 將下列程式碼新增至 **SimulateDeviceConfiguration.py** 檔案結尾： 

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()
        
            CLIENT.send_reported_state(TWIN_PAYLOAD, len(TWIN_PAYLOAD), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. 執行裝置應用程式︰
   
    ```cmd/sh
    node SimulateDeviceConfiguration.py
    ```
   
    您應該會看見訊息 `Device twins updated.`。 保持應用程式執行。


## <a name="create-the-service-app"></a>建立服務應用程式
在本節中，您將建立 Python 主控台應用程式，在與 **myDeviceId** 相關聯的裝置對應項上，以新的遙測組態物件來更新「所需屬性」。 然後，它會查詢儲存在 IoT 中樞的裝置對應項，並顯示裝置的所需和所報告組態之間的差異。

1. 在您的命令提示字元中使用下列命令來安裝 **Azure IoT Python 服務 SDK**：
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. 使用文字編輯器，建立新的 **SetDesiredAndQuery.py** 檔案。

1. 將下列程式碼新增至 **SetDesiredAndQuery.py** 檔案，並以您建立中樞時所複製的 IoT 中樞連接字串取代 **{IoTHubConnectionString}** 預留位置，以及 **{deviceId}** 預留位置取代您裝置的名稱︰

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubError, IoTHubDeviceTwin

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"configId\":3, \"sendFrequency\":\"" + sys.argv[1:][0] + "\"}}}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

1. 在 **SetDesiredAndQuery.py** 檔案結尾處新增下列程式碼：

    ```python
    def iothub_devicetwin_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
        
            twin_info = iothub_twin_method.get_twin(DEVICE_ID)
            print ( "" )
            print ( "Device Twin before update    :" )
            print ( "{0}".format(twin_info) )

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)
            print ( "" )
            print ( "Device Twin after update     :" )
            print ( "{0}".format(twin_info) )
        
            while True:
                time.sleep(10)

                twin_info = iothub_twin_method.get_twin(DEVICE_ID)
                print ( "" )
                print ( "Device Twin after client change initiated    :" )
                print ( "{0}".format(twin_info) )
        
                print ( "" )
                print ( "IoT Hub service sample complete, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(30)
                
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceTwins Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicetwin_sample_run()
    ```

1. 在 **SimulateDeviceConfiguration.py** 執行時，利用下列項目在新的命令提示字元中執行應用程式：

    ```cmd/sh
    python SetDesiredAndQuery.py 5m
    ```
   
    您應該會看到報告組態識別碼從 **1** 變更成 **2**，作用中的新傳送頻率是五分鐘，而不是 24 小時。


## <a name="next-steps"></a>後續步驟
在本教學課程中，您將會從後端應用程式將所需的組態設為「所需屬性」，還會撰寫模擬裝置應用程式來偵測該變更，並模擬更新程序，以「所報告屬性」將其狀態回報給裝置對應項。

使用下列資源來了解如何：

* 利用[開始使用 IoT 中樞][lnk-iothub-getstarted]教學課程，傳送裝置的遙測資料，
* 在大量裝置上排程或執行作業，請參閱[排程及廣播作業][lnk-schedule-jobs]教學課程。
* 以互動方式控制裝置 (例如，從使用者控制的應用程式開啟風扇)，請參閱[使用直接方法][lnk-methods-tutorial]教學課程。

<!-- links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-python-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-iothub-getstarted]: iot-hub-python-getstarted.md
[lnk-methods-tutorial]: iot-hub-python-python-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
