---
title: 使用 Azure IoT 中樞對訊息進行路由傳送 (Python) | Microsoft Docs
description: 如何使用路由規則和自訂端點來處理 Azure IoT 中樞裝置到雲端的訊息，以將訊息分派至其他後端服務。
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: ''
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: 98216328e970af5f8acf04c7e7c3ca855f7065f1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="routing-messages-with-iot-hub-python"></a>使用 IoT 中樞對訊息進行路由傳送 (Python)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

本教學課程是以[開始使用 IoT 中樞]教學課程為基礎。  教學課程會：

* 說明如何使用路由規則，以簡單的設定方式來分派裝置到雲端訊息。
* 說明如何從解決方案後端隔離需要立即採取行動的互動式訊息，以便進一步處理。  例如，裝置可能會傳送一則警示訊息，以觸發將票證插入 CRM 系統的作業。  相較之下，資料點訊息 (例如溫度遙測) 只會饋送至分析引擎。

在本教學課程結尾，您會執行三個 Python 主控台應用程式：

* **SimulatedDevice.py** (修改自[開始使用 IoT 中樞]教學課程中所建立的應用程式) 每秒會傳送資料點的裝置到雲端訊息，並且以隨機間隔傳送互動式裝置到雲端的訊息。 此應用程式會使用 MQTT 通訊協定與「IoT 中樞」進行通訊。
* **ReadCriticalQueue.py** 可從連結到 IoT 中樞的服務匯流排佇列中清除重大訊息的佇列。

> [!NOTE]
> IoT 中樞對於許多裝置平台和語言 (包括 C、Java 和 JavaScript) 提供 SDK 支援。 如需了解如何以實體裝置取代本教學課程中的裝置，以及如何將裝置連接到「IoT 中樞」，請參閱 [Azure IoT 開發人員中心]。

若要完成此教學課程，您需要下列項目：

* [開始使用 IoT 中樞] 教學課程的完整運作版本。
* [Python 2.x 或 3.x][lnk-python-download]。 請務必使用安裝程式所需的 32 位元或 64 位元安裝。 在安裝期間出現系統提示時，務必將 Python 新增至平台特有的環境變數。 如果您使用 Python 2.x，則可能需要[安裝或升級 *pip*(Python 套件管理系統][lnk-install-pip])。
* 如果您使用 Windows 作業系統，則 [Visual c + + 可轉散發套件][lnk-visual-c-redist] 允許使用 Python 的原生 DLL。
* [Node.js 4.0 或更新版本][lnk-node-download]。 請務必使用安裝程式所需的 32 位元或 64 位元安裝。 這是安裝 [IoT 中樞總管工具][lnk-iot-hub-explorer]所需的項目。
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。)

我們也建議您閱讀 [Azure 儲存體]和 [Azure 服務匯流排]的相關文件。


## <a name="send-interactive-messages-from-a-device-app"></a>從裝置應用程式傳送互動式訊息
在本節中，您會修改您在[開始使用 IoT 中樞]教學課程中建立的裝置應用程式，偶爾傳送需要立即處理的訊息。

1. 使用文字編輯器，開啟 **SimulatedDevice.py** 檔案。 這個檔案包含您在[開始使用 IoT 中樞]教學課程中建立的 **SimulatedDevice**應用程式。

2. 使用下列程式碼取代 **iothub_client_telemetry_sample_run** 函式：

    ```python
    def iothub_client_telemetry_sample_run():

    try:
        client = iothub_client_init()
        print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
        message_counter = 0

        while True:
            random_seed = random.random()
            msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random_seed * 4 + 2))
            # messages can be encoded as string or bytearray
            if (message_counter & 1) == 1:
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
            else:
                message = IoTHubMessage(msg_txt_formatted)
            # optional: assign ids
            message.message_id = "message_%d" % message_counter
            message.correlation_id = "correlation_%d" % message_counter
            # optional: assign properties
            prop_map = message.properties()
            if random_seed > .5:
                if random_seed > .8:
                    prop_map.add("level", 'critical')
                else:
                    prop_map.add("level", 'storage')

            client.send_event_async(message, send_confirmation_callback, message_counter)
            print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

            status = client.get_send_status()
            print ( "Send status: %s" % status )
            time.sleep(30)

            status = client.get_send_status()
            print ( "Send status: %s" % status )

            message_counter += 1

    except IoTHubError as iothub_error:
        print ( "Unexpected error %s from IoTHub" % iothub_error )
        return
    except KeyboardInterrupt:
        print ( "IoTHubClient sample stopped" )
    ```
   
    這個方法會隨機將 `"level": "critical"` 和 `"level": "storage"` 屬性新增至裝置所傳送的訊息，這會模擬需要應用程式後端立即採取行動的訊息，或是需要永久儲存的訊息。 應用程式支援根據訊息本文，對訊息進行路由傳送。
   
   > [!NOTE]
   > 除了此處顯示的最忙碌路徑範例以外，您可以使用訊息屬性來路由傳送各種案例的訊息，包括冷門路徑處理。

1. 儲存並關閉 **SimulatedDevice.py** 檔案。

    > [!NOTE]
    > 為了簡單起見，本教學課程不會實作任何重試原則。 在實際程式碼中，您應該如 MSDN 文章 [Transient Fault Handling (暫時性錯誤處理)]所建議來實作重試原則 (例如指數型輪詢)。


## <a name="add-queues-to-your-iot-hub-and-route-messages-to-them"></a>將佇列新增至 IoT 中樞並將訊息路由傳送到這些佇列

在本節中，您會建立服務匯流排佇列和儲存體帳戶、將它們連線到您的 IoT 中樞，並設定您的 IoT 中樞，進而根據訊息上屬性的目前狀態，將訊息傳送至佇列，並將所有訊息傳送至儲存體帳戶。 如需如何處理來自服務匯流排佇列之訊息的詳細資訊，請參閱[開始使用佇列][lnk-sb-queues-node]，如需如何管理儲存體的詳細資訊，請參閱[開始使用 Azure 儲存體][Azure 儲存體]。

1. 如[開始使用佇列][lnk-sb-queues-node]所述，建立服務匯流排佇列。 記下命名空間和佇列名稱。

    > [!NOTE]
    > 作為 IoT 中樞端點的服務匯流排佇列和主題不能啟用 [工作階段] 或 [重複偵測]。 如果已啟用其中一個選項，端點會在 Azure 入口網站中顯示為 [無法連線]。

1. 在 Azure 入口網站中，開啟 IoT 中樞然後按一下 [端點]。

    ![IoT 中樞端點][30]

1. 在 [端點] 刀鋒視窗中，按一下頂端的 [新增] 將您的佇列新增至 IoT 中樞。 將端點命名為 **CriticalQueue**，並使用下拉式清單來選取 [服務匯流排佇列]、您的佇列所在的服務匯流排命名空間，以及您的佇列名稱。 完成時，請按一下底部的 [確定]。  

    ![新增端點][31]

1. 現在按一下 IoT 中樞中的 [路由]。 按一下刀鋒視窗頂端的 [新增] 來建立路由規則，以便將訊息路由傳送至您剛才新增的佇列。 

    ![新增路由][34]

    輸入名稱，然後選取 [裝置訊息] 作為資料來源。 選擇要作為自訂端點的 [CriticalQueue] 作為路由規則端點，然後輸入 `level="critical"` 作為查詢字串。 按一下底部的 [儲存]。

    ![路由詳細資料][32]

    確定後援路由設定為 [開啟]。 此設定為 IoT 中樞的預設設定。

    ![後援路由][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(選用) 從佇列端點讀取

在本節中，您會建立 Python 主控台應用程式，以讀取來自 IoT 服務匯流排的重大訊息。 如需更深入的資訊，請參閱[開始使用佇列][lnk-sb-queues-node]。 

1. 開啟新的命令提示字元並安裝 Azure IoT Hub Device SDK for Python，如下所示。 安裝之後，請關閉命令提示字元。

    ```cmd/sh
    pip install azure-servicebus
    ```

    > [!NOTE]
    > 對於安裝 **azure-servicebus** 封裝的問題，或者要取得進一步安裝選項，請參閱 [Python azure-servicebus 封裝][lnk-python-service-bus]。

1. 建立名為 **ReadCriticalQueue.py** 的檔案。 在您所選的 Python 編輯器/IDE (例如 IDLE) 中開啟此檔案。

1. 新增下列程式碼，從裝置 SDK 匯入必要的模組：

    ```python
    from azure.servicebus import ServiceBusService, Message, Queue
    ```

1. 新增下列程式碼，並使用您服務匯流排的連線資料取代預留位置：

    ```python
    SERVICE_BUS_NAME = {serviceBusName}
    SHARED_ACCESS_POLICY_NAME = {sharedAccessPolicyName}
    SHARED_ACCESS_POLICY_KEY_VALUE = {sharedAccessPolicyKeyValue}
    QUEUE_NAME = {queueName}    
    ```

5. 新增下列程式碼，以連線並讀取服務匯流排： 

    ```python
    def setup_client():
        bus_service = ServiceBusService(
        service_namespace=SERVICE_BUS_NAME,
        shared_access_key_name=SHARED_ACCESS_POLICY_NAME,
        shared_access_key_value=SHARED_ACCESS_POLICY_KEY_VALUE)

        while True:
            msg = bus_service.receive_queue_message(QUEUE_NAME, peek_lock=False)
            print(msg.body)
    ```

1. 最後，新增 main 函式。 

    ```python
    if __name__ == '__main__':
        setup_client()
    ```

1. 儲存並關閉 **ReadCriticalQueue.py** 檔案。 現在您已經準備好執行應用程式。


## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行應用程式。

1. 開啟命令提示字元並安裝 IoT 中樞總管。 

    ```cmd/sh
    npm install -g iothub-explorer
    ```

1. 在命令提示字元執行下列命令，開始監視來自裝置的裝置到雲端資訊。 在 `--login` 之後的預留位置中使用 IoT 中樞的連接字串。

    ```cmd/sh
    iothub-explorer monitor-events [deviceId] --login "[IoTHub connection string]"
    ```

1. 開啟新的命令提示字元並瀏覽至含有 **SimulatedDevice.py** 檔案的目錄。

1. 執行 **SimulatedDevice.py** 檔案，定期將遙測資料傳送至您的 IoT 中樞。 
   
    ```cmd/sh
    python SimulatedDevice.py
    ```

1. 若要執行 **ReadCriticalQueue** 應用程式，請在命令提示字元或殼層中，瀏覽至 **ReadCriticalQueue.py** 檔案，然後執行下列命令：

   ```cmd/sh
   python ReadCriticalQueue.py
   ```

1. 在從上一節執行 IoT 中樞總管的命令提示字元上觀察裝置訊息。 觀察 **ReadCriticalQueue** 應用程式中的 `critical` 訊息。

    ![Python 裝置到雲端訊息][2]


## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(選擇性) 將儲存體容器新增至 IoT 中樞並將訊息路由傳送給它

在本節中，您會建立儲存體帳戶，將它連線到您的 IoT 中樞，然後設定 IoT 中樞以根據訊息上的屬性將訊息傳送至該帳戶。 如需如何管理儲存體的詳細資訊，請參閱[開始使用 Azure 儲存體][Azure 儲存體]。

 > [!NOTE]
   > 建立於「F1 可用」層下方的 IoT 中樞帳戶會限制為單一**端點**。 如果您不受限於單一**端點**，除了 **CriticalQueue** 之外，您還可以設定 **StorageContainer**，並同時執行它們。

1. 按照 [Azure 儲存體文件][lnk-storage]中所述的方法來建立儲存體帳戶。 記下帳戶名稱。

2. 在 Azure 入口網站中，開啟 IoT 中樞然後按一下 [端點]。

3. 在 [端點] 刀鋒視窗中，選取 [CriticalQueue] 端點，然後按一下 [刪除]。 按一下 [是]，然後按一下 [新增]。 將端點命名為 **StorageContainer**，並使用下拉式清單選取 [Azure 儲存體容器]，然後建立**儲存體帳戶**和**儲存體容器**。  記下它們的名稱。  完成時，請按一下底部的 [確定]。 

 > [!NOTE]
   > 建立於「F1 可用」層下方的 IoT 中樞帳戶會限制為單一**端點**。 如果您不受限於單一**端點**，則不需要刪除 **CriticalQueue**。

4. 按一下 IoT 中樞中的 [路由]。 按一下刀鋒視窗頂端的 [新增] 來建立路由規則，以便將訊息路由傳送至您剛才新增的佇列。 選取**裝置訊息** 作為資料來源。 輸入 `level="storage"` 作為條件，然後選擇 [StorageContainer] 作為路由規則端點。 按一下底部的 [儲存]。  

    確定後援路由設定為 [開啟]。 此設定為 IoT 中樞的預設設定。

1. 請確定先前的應用程式 **SimulatedDevice.py** 仍在執行。 

1. 在 Azure 入口網站中移至儲存體帳戶，並按一下 [Blob 服務] 下方的 [瀏覽 Blob]。選取您的容器，瀏覽至 JSON 檔案並按一下它，然後按一下 [下載] 來檢視資料。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已學到如何使用 IoT 中樞的訊息路由功能來可靠地分派裝置到雲端訊息。

若要查看使用 IoT 中樞的完整端對端解決方案範例，請參閱 [Azure IoT 遠端監視解決方案加速器][lnk-suite]。

若要深入了解如何使用 IoT 中樞開發解決方案，請參閱 [IoT 中樞開發人員指南]。

若要深入了解 IoT 中樞的訊息路由，請參閱[使用 IoT 中樞傳送和接收訊息][lnk-devguide-messaging]。

<!-- Images. -->
[2]: ./media/iot-hub-python-python-process-d2c/output.png

[30]: ./media/iot-hub-python-python-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-python-python-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-python-python-process-d2c/route-creation.png
[33]: ./media/iot-hub-python-python-process-d2c/fallback-route.png
[34]: ./media/iot-hub-python-python-process-d2c/click-routes.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md

[Azure 儲存體]: https://azure.microsoft.com/documentation/services/storage/
[Azure 服務匯流排]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT 中樞開發人員指南]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[開始使用 IoT 中樞]: iot-hub-python-getstarted.md
[Azure IoT 開發人員中心]: https://azure.microsoft.com/develop/iot

[Transient Fault Handling (暫時性錯誤處理)]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/azure/storage/
[lnk-python-service-bus]: https://pypi.python.org/pypi/azure-servicebus
