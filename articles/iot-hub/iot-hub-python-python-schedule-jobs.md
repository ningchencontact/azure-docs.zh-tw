---
title: 使用 Azure IoT 中樞 (Python) 排程作業 | Microsoft Docs
description: 如何排定 Azure IoT 中樞作業在多個裝置上叫用直接方法。 您可以使用適用於 Python 的 Azure IoT SDK，實作模擬裝置應用程式和服務應用程式來執行作業。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: robinsh
ms.openlocfilehash: 63534260e042a1b47ca5e635c48123672d663a9b
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873274"
---
# <a name="schedule-and-broadcast-jobs-python"></a>排程及廣播作業 (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT 中樞是一項完全受控的服務，可讓後端應用程式建立作業來排定和更新數百萬個裝置，並追蹤作業。  作業可用於下列動作：

* 更新所需屬性
* 更新標籤
* 叫用直接方法

就概念而言，作業會包裝上述其中一個動作，然後針對由裝置對應項 (twin) 查詢所定義的一組裝置，追蹤執行進度。  例如，後端應用程式可以在 10,000 個裝置上使用作業叫用重新啟動方法，此方法由裝置對應項查詢指定並排定在未來執行。  接著，該應用程式可以追蹤每個這些裝置接收和執行重新啟動方法的進度。

從下列文章深入了解這當中的每一項功能：

* 裝置對應項和屬性：[開始使用裝置 twins](iot-hub-python-twin-getstarted.md)和[教學課程:如何使用裝置對應項屬性](tutorial-device-twins.md)

* 直接方法：[IoT 中樞開發人員指南-直接方法](iot-hub-devguide-direct-methods.md)和[教學課程: 直接方法](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教學課程說明如何：

* 建立具有直接方法的 Python 模擬裝置應用程式，此直接方法會啟用 **lockDoor** 供解決方案後端呼叫。

* 建立 Python 主控台應用程式，此應用程式會使用作業在模擬裝置應用程式中呼叫 **lockDoor** 直接方法，並使用裝置作業來更新所需屬性。

在本教學課程結尾，您將會有兩個 Python 應用程式：

**simDevice.py** 會使用裝置身分識別連線到您的 IoT 中樞，並接收 **lockDoor** 直接方法。

**scheduleJobService.py** 會使用作業在模擬裝置應用程式中呼叫直接方法，並更新裝置對應項的所需屬性。

> [!NOTE]
> **適用於 Python 的 Azure IoT SDK** 不直接支援 [作業] 功能。 反之，本教學課程使用非同步執行緒與計時器提供替代方案。 如需進一步的更新，請參閱 [適用於 Python 的 Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-python) 頁面上的 [服務用戶端 SDK] 功能清單。
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要：

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中樞註冊新的裝置

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式

在本節中，您建立 Python 主控台應用程式，回應雲端所呼叫的直接方法，可觸發模擬的 **lockDoor** 方法。

1. 在命令提示字元上執行下列命令，以安裝 **azure-iot-device-client** 套件：

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

   > [!NOTE]
   > Azure iothub-服務用戶端和 iothub 裝置用戶端的 pip 套件目前僅適用于 Windows 作業系統。 針對 Linux/Mac OS, 請參閱[準備適用于 Python 的開發環境](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md)文章的 Linux 和 Mac os 特定章節。
   >

2. 使用文字編輯器，在工作目錄中建立新的 **simDevice.py** 檔案。

3. 在 **simDevice.py** 檔案開頭新增下列 `import` 陳述式和變數。 將 `deviceConnectionString` 取代為您上方所建立裝置的連接字串：

    ```python
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    METHOD_CONTEXT = 0
    TWIN_CONTEXT = 0
    WAIT_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. 新增下列函式回呼以處理 **lockDoor** 方法：

    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

5. 新增另一個函式回呼來處理裝置對應項更新：

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

6. 新增下列程式碼以註冊 **lockDoor** 方法的處理常式。 另包含 `main` 常式：

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)
            client.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

            print ( "Direct method initialized." )
            print ( "Device twin callback initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

            while True:
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
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_jobs_sample_run()
    ```

7. 儲存並關閉 **simDevice.py** 檔案。

> [!NOTE]
> 為了簡單起見，本教學課程不會實作任何重試原則。 在生產環境程式碼中，您應該如[暫時性錯誤處理](/azure/architecture/best-practices/transient-faults)一文中所建議，實作重試原則 (例如指數型輪詢)。
>

## <a name="get-the-iot-hub-connection-string"></a>取得 IoT 中樞連接字串

在本文中, 您會建立後端服務, 以在裝置上叫用直接方法, 並更新裝置對應項。 服務需要**服務 connect**許可權, 才能在裝置上呼叫直接方法。 服務也需要登錄**讀取**和登錄**寫入**許可權, 才能讀取和寫入身分識別登錄。 沒有僅包含這些許可權的預設共用存取原則, 因此您需要建立一個。

若要建立共用存取原則, 以授與**服務**連線、登錄**讀取**和登錄**寫入**許可權, 以及取得此原則的連接字串, 請遵循下列步驟:

1. 在[Azure 入口網站](https://portal.azure.com)中開啟您的 IoT 中樞。 若要進入 IoT 中樞, 最簡單的方法是選取 [**資源群組**], 選取您的 iot 中樞所在的資源群組, 然後從資源清單中選取您的 iot 中樞。

2. 在 IoT 中樞的左側窗格中, 選取 [**共用存取原則**]。

3. 從原則清單上方的頂端功能表中, 選取 [**新增**]。

4. 在 [**新增共用存取原則**] 窗格中, 輸入原則的描述性名稱;例如: *serviceAndRegistryReadWrite*。 在 **[許可權** **]** 底下, 選取 [服務連線及登錄**寫入**] (當您選取 [登錄**寫入**] 時, 會自動選取 [登錄**讀取**]) 然後選取 [建立]。

    ![示範如何新增共用存取原則](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. 回到 [**共用存取原則**] 窗格, 從原則清單中選取您的新原則。

6. 在 [**共用存取金鑰**] 底下, 選取 [**連接字串-主要金鑰**] 的複製圖示, 然後儲存值。

    ![顯示如何擷取連接字串](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

如需 IoT 中樞共用存取原則和許可權的詳細資訊, 請參閱[存取控制和許可權](./iot-hub-devguide-security.md#access-control-and-permissions)。

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>排定用於呼叫直接方法及更新裝置對應項 (twin) 屬性的作業

在本節中，您會建立一個 Python 主控台應用程式，會使用直接方法在裝置上初始化遠端 **lockDoor**，並更新裝置對應項的屬性。

1. 在命令提示字元上執行下列命令，以安裝 **azure-iot-service-client** 套件：

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

   > [!NOTE]
   > Azure iothub-服務用戶端和 iothub 裝置用戶端的 pip 套件目前僅適用于 Windows 作業系統。 針對 Linux/Mac OS, 請參閱[準備適用于 Python 的開發環境](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md)文章的 Linux 和 Mac os 特定章節。
   >

2. 使用文字編輯器，在工作目錄中建立新的 **scheduleJobService.py** 檔案。

3. 在 scheduleJobService.py 檔案`import`的開頭新增下列語句和變數。 將預留位置取代為您先前在[取得 iot 中樞連接字串](#get-the-iot-hub-connection-string)中所複製的 iot 中樞連接字串。 `{IoTHubConnectionString}` 以您在在[IoT 中樞註冊新裝置](#register-a-new-device-in-the-iot-hub)中註冊的裝置識別碼取代預留位置:`{deviceId}`

    ```python
    import sys
    import time
    import threading
    import uuid

    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_JSON = "{\"properties\":{\"desired\":{\"building\":43,\"floor\":3}}}"
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. 新增下列函式，用來查詢裝置：

    ```python
    def query_condition(device_id):
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        number_of_devices = 10
        dev_list = iothub_registry_manager.get_device_list(number_of_devices)

        for device in range(0, number_of_devices):
            if dev_list[device].deviceId == device_id:
                return 1

        print ( "Device not found" )
        return 0
    ```

5. 新增下列方法來執行呼叫直接方法和裝置對應項的兩個作業：

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        if query_condition(device_id):
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            response = iothub_device_method.invoke(device_id, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        if query_condition(device_id):
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. 新增下列程式碼來排程作業及更新作業狀態。 另包含 `main` 常式：

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
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
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. 儲存並關閉 **scheduleJobService.py** 檔案。

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行應用程式。

1. 在工作目錄的命令提示字元中，執行下列命令以開始接聽重新啟動直接方法：

    ```cmd/sh
    python simDevice.py
    ```

2. 在工作目錄的另一個命令提示字元中，執行下列命令以觸發鎖門作業並更新對應項：
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. 您會在主控台中看到直接方法和裝置對應項更新的裝置回應。

    ![IoT 中樞作業範例 1--裝置輸出](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![IoT 中樞作業範例 2--裝置輸出](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用作業來排定裝置的直接方法，以及更新裝置對應項 (twin) 的屬性。

若要繼續開始使用 IoT 中樞和裝置管理模式 (例如遠端透過空中固件更新), 請參閱[如何執行固件更新](tutorial-firmware-update.md)。