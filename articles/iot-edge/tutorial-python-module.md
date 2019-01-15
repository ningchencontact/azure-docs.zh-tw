---
title: 建立自訂 Python 模組的教學課程 - Azure IoT Edge | Microsoft Docs
description: 本教學課程說明如何使用 Python 程式碼建立 IoT Edge 模組，並將其部署到邊緣裝置。
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 89c19adc571d500fff54d493072bb9976ce51aa9
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052882"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device"></a>教學課程：開發 Python IoT Edge 模組並部署到您的模擬裝置

您可以使用 Azure IoT Edge 模組來部署程式碼，直接在 IoT Edge 裝置上實作您的商務邏輯。 本教學課程會逐步引導您建立並部署能篩選感應器資料的 IoT Edge 模組。 您將使用您在快速入門中建立的模擬 IoT Edge 裝置。 在本教學課程中，您了解如何：    

> [!div class="checklist"]
> * 使用 Visual Studio Code 建立 IoT Edge Python 模組。
> * 使用 Visual Studio Code 和 Docker 建立 Docker 映像，並將其發佈至您的登錄中。
> * 將模組部署到您的 IoT Edge 裝置。
> * 檢視產生的資料。


您於此教學課程中建立的 IoT Edge 模組，能夠篩選由您裝置所產生的溫度資料。 它只有在溫度超過指定的閾值時，才會將訊息往上游傳送。 這類於邊緣所進行的分析，對於減少針對雲端所傳輸及儲存的資料量相當有幫助。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>必要條件

Azure IoT Edge 裝置：

* 您可以遵循 [Linux](quickstart-linux.md) 快速入門中的步驟，使用開發電腦或虛擬機器作為邊緣裝置。
* 適用於 IoT Edge 的 Python 模組不支援 Windows 裝置。

雲端資源：

* Azure 中的免費或標準層 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。 

開發資源：

* [Visual Studio Code](https://code.visualstudio.com/)。 
* 適用於 Visual Studio Code 的 [Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。
* [適用於 Visual Studio Code 的 Python 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。 
* [Docker CE](https://docs.docker.com/engine/installation/)。 
* [Python](https://www.python.org/downloads/)。
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) 用於安裝 Python 套件 (通常包含在您的 Python 安裝中)。

>[!Note]
>請確認您的 `bin` 資料夾位於您平台的路徑上。 這通常是 `~/.local/` (在 UNIX 和 macOS 上) 或 `%APPDATA%\Python` (在 Windows 上)。

## <a name="create-a-container-registry"></a>建立容器登錄庫

在本教學課程中，您會使用適用於 Visual Studio Code 的 Azure IoT 工具來建置模組，並從檔案建立**容器映像**。 接著，您會將此映像推送至儲存並管理映像的**登錄**。 最後，您會從登錄部署該映像，以在 IoT Edge 裝置上執行。  

您可以使用任何與 Docker 相容的登錄來保存容器映像。 兩個熱門 Docker 登錄服務為 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 和 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。 本教學課程使用的是 Azure Container Registry。 

如果您還沒有容器登錄，請依照下列步驟在 Azure 中建立新的容器登錄：

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源] > [容器] > [容器登錄]。

2. 提供下列值以建立您的容器登錄：

   | 欄位 | 值 | 
   | ----- | ----- |
   | 登錄名稱 | 提供唯一名稱。 |
   | 訂用帳戶 | 從下拉式清單中選取訂用帳戶。 |
   | 資源群組 | 建議您對於在 IoT Edge 快速入門和教學課程中建立的所有測試資源，使用相同的資源群組。 例如 **IoTEdgeResources**。 |
   | 位置 | 選擇接近您的位置。 |
   | 管理員使用者 | 設定為 [已啟用]。 |
   | SKU | 選取 [基本]。 | 

5. 選取 [建立] 。

6. 建立容器登錄之後，請加以瀏覽，然後選取 [存取金鑰]。 

7. 複製 [登入伺服器]、[使用者名稱] 及 [密碼] 的值。 稍後在本教學課程中，您會使用這些值來提供容器登錄的存取權。 

## <a name="create-an-iot-edge-module-project"></a>建立 IoT Edge 模組專案
下列步驟會使用 Visual Studio Code 和 Azure IoT 工具來建立 IoT Edge Python 模組。

### <a name="create-a-new-solution"></a>建立新解決方案

使用 Python 套件 **cookiecutter** 來建立 Python 解決方案範本，以供作為您的建置基礎。 

1. 在 Visual Studio Code 中，選取 [檢視] > [終端機] 以開啟 VS Code 整合式終端機。

2. 在整合式終端機中，輸入下列命令來安裝 (或更新) **cookiecutter**，以便在 VS Code 中建立 IoT Edge 解決方案範本：

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```
   >[!Note]
   >請確定將用來安裝 cookiecutter 的目錄位於環境的 PATH 中，如此才能從命令提示字元加以叫用。 目錄是安裝指令碼輸出的一部分，例如 `C:\Users\{user}\AppData\Roaming\Python\Python{version}\Scripts`。
   >
   >重新啟動 Visual Studio Code 以挑選 PATH 的變更。 

3. 選取 [檢視] > [命令選擇區]，以開啟 VS Code 命令選擇區。 

4. 在 [命令選擇區] 中，輸入並執行命令 **Azure:Sign in**，然後遵循指示來登入您的 Azure 帳戶。 如果您已登入，則可以略過此步驟。

5. 在命令選擇區中，輸入並執行命令 **Azure IoT Edge:**[新增 IoT Edge 解決方案]。 依照命令選擇區中的提示建立解決方案。

   | 欄位 | 值 |
   | ----- | ----- |
   | 選取資料夾 | 選擇開發機器上可供 VS Code 建立解決方案檔案的位置。 |
   | 提供解決方案名稱 | 輸入解決方案的描述性名稱或接受預設值 **EdgeSolution**。 |
   | 選取模組範本 | 選擇 [Python 模組]。 |
   | 提供模組名稱 | 將模組命名為 **PythonModule**。 |
   | 提供模組的 Docker 映像存放庫 | 映像存放庫包含容器登錄名稱和容器映像名稱。 系統會預先填入上一個步驟的容器映像。 將 **localhost:5000** 取代為 Azure Container Registry 的登入伺服器值。 您可以在 Azure 入口網站中，從容器登錄的 [概觀] 頁面擷取登入伺服器。 最終字串看起來如下：\<登錄名稱\>.azurecr.io/pythonmodule. |
 
   ![提供 Docker 映像存放庫](./media/tutorial-python-module/repository.png)

VS Code 視窗會載入您的 IoT Edge 方案工作區。 解決方案工作區包含五個最上層元件。 **modules** 資料夾包含您的模組所需的 Python 程式碼，以及用來將模組建置為容器映像的 Dockerfile。 **\.env** 檔案會儲存您的容器登錄認證。 **Deployment.template.json** 檔案包含 IoT Edge 執行階段用來在裝置上部署模組的資訊。 而 **deployment.debug.template.json** 檔案包含模組的偵錯版本。 您在本教學課程中將不會編輯 **\.vscode** 資料夾或 **\.gitignore** 檔案。  

如果您在建立解決方案時未指定容器登錄，但接受了預設的 localhost:5000 值，則不會有 \.env 檔案。 

<!--
   ![Python solution workspace](./media/tutorial-python-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>新增登錄認證

環境檔案會儲存容器存放庫的認證，並與 IoT Edge 執行階段共用這些認證。 執行階段需要有這些認證才能將私人映像提取到 IoT Edge 裝置。 

1. 在 VS Code 總管中，開啟 .env 檔案。 
2. 使用從 Azure Container Registry 複製過來的 [使用者名稱] 和 [密碼] 值來更新欄位。 
3. 儲存這個檔案。 

### <a name="update-the-module-with-custom-code"></a>使用自訂程式碼來更新模組

每個範本均包含範例程式碼，會從 **tempSensor** 模組擷取模擬的感應器資料，再將資料路由傳送到 IoT 中樞。 在本節中，請新增程式碼來擴充 **PythonModule**，以便先分析訊息再傳送。 

1. 在 VS Code 總管中，開啟 [modules] > [PythonModule] > [main.py]。

2. 在 **main.py** 頂端，匯入 **json** 程式庫：

    ```python
    import json
    ```

3. 在全域計數器下新增 **TEMPERATURE_THRESHOLD** 和 **TWIN_CALLBACKS** 變數。 溫度閾值會設定在將資料傳送至 IoT 中樞之前，測量的機器溫度必須超過的值。

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. 使用下列程式碼取代 **receive_message_callback** 函式：

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we forward this message to the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

5. 新增名為 **module_twin_callback** 的新函式。 當所需屬性更新時，便會叫用此函式。

    ```python
    # module_twin_callback is invoked when the module twin's desired properties are updated.
    def module_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print ( "\nTwin callback called with:\nupdateStatus = %s\npayload = %s\ncontext = %s" % (update_state, payload, user_context) )
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
    ```

6. 在 **HubManager** 類別中，將新的行新增至 **__init__** 方法，將您剛才新增的 **module_twin_callback** 函式初始化：

    ```python
    # Sets the callback when a module twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. 儲存 main.py 檔案。

8. 在 VS Code 總管中，於 IoT Edge 解決方案工作區中開啟 **deployment.template.json** 檔案。 此檔案會告訴 IoT Edge 代理程式要部署哪些模組 (在此情況下為 **tempSensor** 和 **PythonModule**)，並告知 IoT Edge 中樞如何在其間路由傳送訊息。 Visual Studio Code 擴充功能會在部署範本中自動填入您需要的大部分資訊，但不會為您的解決方案確認一切正確無誤： 

   1. IoT Edge 的預設平台會設定為 VS Code 狀態列中的 **amd64**，這表示您的 **PythonModule** 會設定為映像的 Linux amd64 版。 將狀態列中的預設平台從 **amd64** 變更為 **arm32v7** 或 **windows-amd64** (如果這是您 IoT Edge 裝置的架構)。 

      ![更新模組映像平台](./media/tutorial-python-module/image-platform.png)

   2. 確認範本具有正確的模組名稱，而不是您在建立 IoT Edge 解決方案時變更的預設 **SampleModule** 名稱。

   3. **registryCredentials** 區段會儲存您的 Docker 登錄認證，以便 IoT Edge 代理程式提取您的模組映像。 實際的使用者名稱和密碼組，會儲存在 git 所忽略的 .env 檔案中。 如果您還沒將您的認證新增至 .env 檔案，請這麼做。  

   4. 如果您想要深入了解部署資訊清單，請參閱[了解如何在 IoT Edge 中部署模組及建立路由](module-composition.md)。

9. 在部署資訊清單中新增 **PythonModule** 模組對應項。 在 **moduleContent** 區段底部，於 **$edgeHub** 模組對應項後面插入下列 JSON 內容： 

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![將模組對應項新增至部署範本](./media/tutorial-python-module/module-twin.png)

10. 儲存 deployment.template.json 檔案。

## <a name="build-and-push-your-solution"></a>建置並推送解決方案

在上一節中，您已建立 IoT Edge 解決方案，並將程式碼新增至 **PythonModule**，來篩選掉所回報機器溫度低於可接受閾值的訊息。 現在，您需要建置容器映像形式的解決方案，並將它推送到容器登錄。 

1. 透過在 Visual Studio Code 整合式終端機中輸入下列命令，來登入 Docker。 接著，您可以將模組映像推送到您的 Azure 容器登錄： 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   使用您在第一節中從 Azure 容器登錄複製而來的使用者名稱、密碼及登入伺服器。 您也可以在 Azure 入口網站中，從登錄的 [存取金鑰] 區段擷取這些資料。

2. 在 VS Code 總管中，以滑鼠右鍵按一下 deployment.template.json 檔案，然後選取 [建置並推送 IoT Edge 解決方案]。 

當您指示 Visual Studio Code 建置解決方案時，它會先擷取部署範本中的資訊，再於名為 **config** 的新資料夾中，產生 deployment.json 檔案。然後，它會在整合式終端機中執行兩個命令：`docker build` 和 `docker push`。 這兩個命令會建置程式碼、將 Python 程式碼容器化，再將程式碼推送至您在初始化解決方案時所指定的容器登錄。 

您可以在執行於 VS Code 整合式終端機的 `docker build` 命令中看到完整的容器映像位址。 系統會根據 module.json 檔案中的資訊建置映像位址，其格式如下：\<存放庫\>：\<版本\>-\<平台\>。 本教學課程中，它看起來應該像：registryname.azurecr.io/pythonmodule:0.0.1-amd64。

## <a name="deploy-and-run-the-solution"></a>部署並執行解決方案

在您用來設定 IoT Edge 裝置的快速入門文章中，您使用 Azure 入口網站部署了模組。 您也可以使用適用於 Visual Studio Code 的 Azure IoT 中樞工具組擴充功能 (先前稱為 Azure IoT 工作組擴充功能) 來部署模組。 您已備妥您的案例所需的部署資訊清單，即 **deployment.json** 檔案。 現在您只需選取要接收部署的裝置即可。

1. 在 VS Code 命令選擇區中，執行 **Azure IoT Hub:Select IoT Hub**。 

2. 選擇您要設定的 IoT Edge 裝置所屬的訂用帳戶和 IoT 中樞。 

3. 在 VS Code 總管中，展開 [Azure IoT 中樞裝置] 區段。 

4. 以滑鼠右鍵按一下 IoT Edge 裝置的名稱，然後選取 [建立單一裝置的部署]。 

   ![建立單一裝置的部署](./media/tutorial-python-module/create-deployment.png)

5. 選取 **config** 資料夾中的 **deployment.json** 檔案，然後按一下 [選取 Edge 部署資訊清單]。 請勿使用 deployment.template.json 檔案。 

6. 按一下 [重新整理] 按鈕。 您應該會看到新的 **PythonModule** 正在與 **TempSensor** 模組以及 **$edgeAgent** 和 **$edgeHub** 一起執行。 

## <a name="view-generated-data"></a>檢視產生的資料

在您將部署資訊清單套用至 IoT Edge 裝置後，裝置的 IoT Edge 執行階段即會收集新的部署資訊，並開始在裝置上執行。 裝置上任何執行中、但未包含在部署資訊清單中的模組都會停止。 裝置中遺漏的任何模組都會啟動。 

您可以使用 Visual Studio Code 總管的 [Azure IoT 中樞裝置] 區段，檢視 IoT Edge 裝置的狀態。 請展開裝置的詳細資料，以查看已部署且執行中的模組清單。 

在 IoT Edge 裝置本身之中，您可以使用 `iotedge list` 命令查看部署模組的狀態。 您應該會看到四個模組：兩個 IoT Edge 執行階段模組、tempSensor 和您在本教學課程中建立的自訂模組。 啟動所有模組可能需要幾分鐘的時間，因此若您一開始未看到所有模組，請重新執行命令。 

若要檢視任何模組所產生的訊息，請使用 `iotedge logs <module name>` 命令。 

您可以在訊息送達 IoT 中樞時使用 Visual Studio Code 加以檢視。 

1. 若要監視抵達 IoT 中樞的資料，請選取省略符號 (**...**)，然後選取 [開始監視 D2C 訊息]。
2. 若要監視特定裝置的 D2C 訊息，請以滑鼠右鍵按一下清單中的裝置，然後選取 [開始監視 D2C 訊息]。
3. 若要停止監視資料，請在命令選擇區中執行命令 **Azure IoT Hub:Stop monitoring D2C message**。 
4. 若要檢視或更新模組對應項，請以滑鼠右鍵按一下清單中的模組，然後選取 [編輯模組對應項]。 若要更新模組對應項，請儲存對應項 JSON 檔案，並以滑鼠右鍵按一下編輯器區域，然後選取 [更新模組對應項]。
5. 若要檢視 Docker 記錄，請安裝適用於 VS Code 的 [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)。 您可以在本機的 Docker 總管中，找到您執行中的模組。 在操作功能表中，選取 [顯示記錄]，以在整合式終端機中進行檢視。 

## <a name="clean-up-resources"></a>清除資源 

如果您打算繼續閱讀下一篇建議的文章，則可以保留您所建立的資源和組態，並加以重複使用。 您可以也繼續使用相同的 IoT Edge 裝置作為測試裝置。 

否則，可以刪除您在本文中建立的本機組態和 Azure 資源，以避免產生費用。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

### <a name="delete-local-resources"></a>刪除本機資源

如果您想要從裝置中移除 IoT Edge 執行階段和相關資源，請使用下列命令。 

移除 IoT Edge 執行階段。

   ```bash
   sudo apt-get remove --purge iotedge
   ```

IoT Edge 執行階段移除後，它所建立的容器隨即停止，但仍會存在於您的裝置上。 檢視所有容器。

   ```bash
   sudo docker ps -a
   ```

刪除已在您的裝置上建立的執行階段容器。

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

藉由參照容器名稱，刪除在 `docker ps` 輸出中列出的任何其他容器。 

移除容器執行階段。

   ```bash
   sudo apt-get remove --purge moby
   ```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立包含程式碼的 IoT Edge 模組，可篩選您 IoT Edge 裝置所產生的原始資料。 您可以繼續進行後續教學課程，以了解 Azure IoT Edge 有什麼其他方法，可協助您將此資料轉換成 Edge 上的商業見解。

> [!div class="nextstepaction"]
> [將 Azure Functions 部署為模組](tutorial-deploy-function.md)
> [將 Azure 串流分析部署為模組](tutorial-deploy-stream-analytics.md)
