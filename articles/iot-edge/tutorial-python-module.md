---
title: Azure IoT Edge Python 模組 | Microsoft Docs
description: 使用 Python 程式碼建立 IoT Edge 模組並將它部署到邊緣裝置
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 3c46df85f95377f5740526542ac1baf5a8fd77c0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
ms.locfileid: "32177830"
---
# <a name="develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device---preview"></a>開發 Python IoT Edge 模組並部署到您的模擬裝置 - 預覽

您可以使用 IoT Edge 模組來部署程式碼，將您的商務邏輯直接實作到您的 IoT Edge 裝置。 本教學課程會逐步引導您建立並部署能篩選感應器資料的 IoT Edge 模組。 您將會使用模擬的 IoT Edge 裝置，其建立方法詳述於[在 Windows 中的模擬裝置上部署 Azure IoT Edge][lnk-tutorial1-win]，以及[在 Linux 中的模擬裝置上部署 Azure IoT Edge][lnk-tutorial1-lin] 這兩個教學課程中。 在本教學課程中，您了解如何：    

> [!div class="checklist"]
> * 使用 Visual Studio Code 建立 IoT Edge Python 模組
> * 使用 Visual Studio Code 和 Docker 建立 Docker 映像，並將其發行至您的登錄中 
> * 將模組部署到您的 IoT Edge 裝置
> * 檢視產生的資料


您於此教學課程中建立的 IoT Edge 模組，能夠篩選由您的裝置所產生的溫度資料。 它只有在溫度超過指定的閾值時，才會將訊息往上游傳送。 這類於邊緣所進行的分析，對於減少針對雲端所傳輸及儲存的資料量相當有幫助。 

> [!IMPORTANT]
> 目前 Python 模組僅能在 amd64 Linux 容器中執行；無法在 Windows 容器 ARM 架構的容器中執行。 

## <a name="prerequisites"></a>先決條件

* 您在快速入門或第一份教學課程中所建立的 Azure IoT Edge 裝置。
* IoT Edge 裝置的主索引鍵連接字串。  
* [Visual Studio Code](https://code.visualstudio.com/)。 
* [Visual Studio Code 的 Azure IoT Edge 擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
* [適用於 Visual Studio Code 的 Python 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-python.python) \(英文\)。 
* 在具有 Visual Studio Code 的電腦上也已安裝 [Docker](https://docs.docker.com/engine/installation/) \(英文\)。 針對本教學課程，使用 Community Edition (CE) 便已足夠。 
* [Python](https://www.python.org/downloads/)。
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) 用於安裝 Python 套件 (通常包含在您的 Python 安裝中)。

## <a name="create-a-container-registry"></a>建立容器登錄庫
在本教學課程中，您會使用適用於 VS Code 的 Azure IoT Edge 擴充功能來建置模組，並從檔案建立**容器映像**。 接著，您會將此映像推送至儲存並管理映像的**登錄**。 最後，您會從登錄部署該映像，以在 IoT Edge 裝置上執行。  

您可以此教學課程中使用任何與 Docker 相容的登錄。 於雲端中提供使用的兩個熱門 Docker 登錄服務為 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 和 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) \(英文\)。 本教學課程使用的是 Azure Container Registry。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源] > [容器] > [Azure Container Registry]。
2. 為登錄提供名稱，選擇訂用帳戶，選擇資源群組，然後將 SKU 設定為 [基本]。 
3. 選取 [建立] 。
4. 建立容器登錄之後，請瀏覽至它並選取 [存取金鑰]。 
5. 將 [管理使用者] 切換為 [啟用]。
6. 複製 [登入伺服器]、[使用者名稱] 及 [密碼] 的值。 您在本教學課程後續的內容中將會用到這些值。 

## <a name="create-an-iot-edge-module-project"></a>建立 IoT Edge 模組專案
下列步驟會示範如何使用 Visual Studio Code 與 Azure IoT Edge 擴充功能來建立 IoT Edge Python 模組。
1. 在 Visual Studio Code 中，選取 [檢視] > [整合式終端機] 以開啟 VS Code 整合式終端機。
2. 在整合式終端機中，輸入下列命令以安裝 (或更新) **cookiecutter** (建議您在虛擬環境下執行此動作，或作為使用者安裝，如下所示)：

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. 針對新模組建立專案。 下列命令會使用您的容器存放庫來建立專案資料夾 **FilterModule**。 如果您使用 Azure 容器登錄，`image_repository` 的參數格式應該是 `<your container registry name>.azurecr.io/filtermodule`。 在目前的工作資料夾中輸入下列命令：

    ```cmd/sh
    cookiecutter --no-input https://github.com/Azure/cookiecutter-azure-iot-edge-module module_name=FilterModule image_repository=<your container registry address>/filtermodule
    ```
 
4. 選取 [檔案] > [開啟資料夾]。
5. 瀏覽至 [FilterModule] 資料夾，然後按一下 [選取資料夾] 以在 VS Code 中開啟專案。
6. 在 VS Code 總管中，按一下 **main.py** 以將它開啟。
7. 在 **FilterModule** 命名空間頂端，匯入 `json` 程式庫：

    ```python
    import json
    ```

8. 在全域計數器下加入 `TEMPERATURE_THRESHOLD`、`RECEIVE_CALLBACKS` 與 `TWIN_CALLBACKS`。 溫度閾值會設定在將資料傳送至 IoT 中樞之前，測量的溫度必須超過的值。

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = RECEIVE_CALLBACKS = 0
    ```

9. 使用下列內容來更新函式 `receive_message_callback`。

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we will forward this message onto the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print("    Data: <<<{}>>> & Size={:d}".format(message_text, size))
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print("    Properties: {}".format(key_value_pair))
        RECEIVE_CALLBACKS += 1
        print("    Total calls received: {:d}".format(RECEIVE_CALLBACKS))
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature {} exceeds threshold {}".format(data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

10. 新增函式 `device_twin_callback`。 當更新所需的屬性時，會叫用此函式。

    ```python
    # device_twin_callback is invoked when twin's desired properties are updated.
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print("\nTwin callback called with:\nupdateStatus = {}\npayload = {}\ncontext = {}".format(update_state, payload, user_context))
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print("Total calls confirmed: {:d}\n".format(TWIN_CALLBACKS))
    ```

11. 在類別 `HubManager` 中，將新行加入 `__init__` 方法以初始化您剛新增的 `device_twin_callback` 函式。

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_device_twin_callback(device_twin_callback, self)
    ```


12. 儲存這個檔案。

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>建立 Docker 映像並發行到您的登錄中

1. 透過在 VS Code 整合式終端機中輸入下列命令來登入 Docker： 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   使用您在建立 Azure 容器登錄時從中複製的使用者名稱、密碼及登入伺服器。

2. 在 VS Code 總管中，以滑鼠右鍵按一下 **module.json** 檔案並按一下 [建置及推送 IoT Edge 模組 Docker 映像]。 在於 VS Code 視窗頂端彈出的下拉式清單方塊中，選取您的容器平台，例如適用於 Linux 容器的 **amd64**。 VS Code 會將 `main.py` 與必要相依性容器化，然後將它推送到您指定的容器登錄。 第一次建置映像可能需要數分鐘的時間。

3. 您可以在 VS Code 整合式終端機中取得完整容器映像位址。 如需有關建置及推送定義的詳細資訊，請參閱 `module.json` 檔案。

## <a name="add-registry-credentials-to-edge-runtime"></a>將登錄認證新增至 Edge 執行階段
在執行 Edge 裝置的電腦上，將登錄的認證新增至 Edge 執行階段。 這些認證會將提取容器的存取權提供給執行階段。 

- 針對 Windows，請執行下列命令：
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- 針對 Linux，請執行下列命令：
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>執行解決方案

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 IoT 中樞。
2. 移至 [IoT Edge (預覽)] 並選取您的 IoT Edge 裝置。
3. 選取 [設定模組]。 
2. 檢查 **tempSensor** 模組是否已自動填入。 如果沒有，請使用下列步驟來新增它：
    1. 選取 [新增 IoT Edge 模組]。
    2. 在 [名稱] 欄位中，輸入 `tempSensor`。
    3. 在 [映像 URI] 欄位中，輸入 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`。
    4. 其他設定保留不變，然後按一下 [儲存]。
9. 新增您在先前小節中建立的 **filterModule** 模組。 
    1. 選取 [新增 IoT Edge 模組]。
    2. 在 [名稱] 欄位中，輸入 `filterModule`。
    3. 在 [映像 URI] 欄位中，輸入您的映像位址，例如 `<your container registry address>/filtermodule:0.0.1-amd64`。 您可以在上一節找到完整的映像位址。
    4. 選取 [啟用] 方塊以編輯模組對應項。 
    5. 使用下列 JSON 取代模組對應項文字方塊中的 JSON： 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. 按一下 [檔案] 。
10. 按 [下一步] 。
11. 在 [指定路由] 步驟中，將下列 JSON 複製到文字方塊。 模組會將所有訊息發佈到 Edge 執行階段。 執行階段中的宣告式規則會定義訊息的流向。 在本教學課程中，您需要兩個路由。 第一個路由會透過使用 **FilterMessages** 處理常式設定的 "input1" 端點，將訊息從溫度感應器傳輸至篩選模組。 第二個路由會將訊息從篩選模組傳輸到 IoT 中樞。 在此路由中，`upstream` 是告知 Edge 中樞將訊息傳送至 IoT 中樞的特殊目的地。 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. 按 [下一步] 。
5. 在 [檢閱範本] 步驟中，按一下 [提交]。 
6. 返回 IoT Edge 裝置的詳細資料頁面，按一下 [重新整理]。 您應該會看到新的 **filtermodule** 正在與 **tempSensor** 模組和 **IoT Edge 執行階段**一起執行。 

## <a name="view-generated-data"></a>檢視產生的資料

監視從 IoT Edge 裝置傳送到 IoT 中樞的「裝置到雲端」訊息：
1. 使用 IoT 中樞連接字串來設定 Azure IoT Toolkit 擴充功能： 
    1. 選取 [檢視] > [檔案總管] 來開啟 VS Code 檔案總管。 
    3. 在檔案總管中，按一下 [IOT HUB DEVICES]，然後按一下 [...]。按一下 [設定 IoT 中樞連接字串]，然後在快顯視窗中，輸入與您 IoT Edge 裝置連線的 IoT 中樞連接字串。 

        若要尋找連接字串，在 Azure 入口網站中按一下 IoT 中樞圖格，然後按一下 [共用存取原則]。 在 [共用存取原則] 中，按一下 [iothubowner] 原則，然後複製 [iothubowner] 視窗中的 IoT 中樞連接字串。   

1. 若要監視抵達 IoT 中樞的資料，請選取 [檢視] > [命令選擇區]，然後搜尋 [IoT: 開始監視 D2C 訊息] 功能表命令。 
2. 若要停止監視資料，請使用 [IoT:停止監視 D2C 訊息] 功能表命令。 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立包含程式碼的 IoT Edge 模組，可篩選您 IoT Edge 裝置所產生的原始資料。 您可以繼續閱讀下列任何教學課程，以了解 Azure IoT Edge 有什麼其他方法可協助您將此資料轉換成具有優勢的商業見解。

> [!div class="nextstepaction"]
> [將 Azure Functions 部署為模組](tutorial-deploy-function.md)
> [將 Azure 串流分析部署為模組](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
