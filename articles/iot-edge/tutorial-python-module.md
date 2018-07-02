---
title: Azure IoT Edge Python 教學課程 | Microsoft Docs
description: 本教學課程說明如何使用 Python 程式碼建立 IoT Edge 模組並將它部署到邊緣裝置
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 884237a851461fe3d7a48708d221909804760ceb
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063117"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device"></a>教學課程：開發 Python IoT Edge 模組並部署到您的模擬裝置

您可以使用 IoT Edge 模組來部署程式碼，將您的商務邏輯直接實作到您的 IoT Edge 裝置。 本教學課程會逐步引導您建立並部署能篩選感應器資料的 IoT Edge 模組。 您將會使用模擬的 IoT Edge 裝置，其建立方法詳述於[在 Windows 中的模擬裝置上部署 Azure IoT Edge][lnk-quickstart-win]，以及[在 Linux 中的模擬裝置上部署 Azure IoT Edge][lnk-quickstart-lin] 這兩個快速入門中。 在本教學課程中，您了解如何：    

> [!div class="checklist"]
> * 使用 Visual Studio Code 建立 IoT Edge Python 模組
> * 使用 Visual Studio Code 和 Docker 建立 Docker 映像，並將其發行至您的登錄中 
> * 將模組部署到您的 IoT Edge 裝置
> * 檢視產生的資料


您於此教學課程中建立的 IoT Edge 模組，能夠篩選由您的裝置所產生的溫度資料。 它只有在溫度超過指定的閾值時，才會將訊息往上游傳送。 這類於邊緣所進行的分析，對於減少針對雲端所傳輸及儲存的資料量相當有幫助。 

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free) 。


## <a name="prerequisites"></a>先決條件

* 您在 [Linux](quickstart-linux.md) 或 [Windows 裝置](quickstart.md)的快速入門中所建立的 Azure IoT Edge 裝置。
* [Visual Studio Code](https://code.visualstudio.com/)。 
* [適用於 Visual Studio Code 的 Azure IoT Edge 延伸模組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) \(英文\) 
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

### <a name="create-a-new-solution"></a>建立新解決方案

使用 Python 套件 **cookiecutter** 來建立 Python 解決方案範本，以供作為您的建置基礎。 

1. 在 Visual Studio Code 中，選取 [檢視] > [整合式終端機] 以開啟 VS Code 整合式終端機。

2. 在整合式終端機中，輸入下列命令來安裝 (或更新) **cookiecutter**，以供用來在 VS Code 中建立 Edge 解決方案範本：

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. 選取 [檢視] > [命令選擇區] 來開啟 VS Code 命令選擇區。 

4. 在命令選擇區中，輸入並執行命令 **Azure: Sign in**，然後遵循指示來登入 Azure 帳戶。 如果您已經登入，則可以略過此步驟。

5. 在命令選擇區中，輸入並執行命令 **Azure IoT Edge: New IoT Edge solution**。 在命令選擇區中，提供下列資訊來建立解決方案： 

   1. 選取要將解決方案建立在哪個資料夾。 
   2. 為解決方案提供名稱，或是接受預設值 **EdgeSolution**。
   3. 選擇 [Python 模組] 作為模組範本。 
   4. 將模組命名為 **PythonModule**。 
   5. 將您在上一節所簡歷的 Azure Container Registry 指定作為第一個模組的映像存放庫。 將 **localhost:5000** 取代為您所複製的登入伺服器值。 最終字串的樣貌如下：**\<登錄名稱\>.azurecr.io/pythonmodule**。
 
VS Code 視窗會載入您的 IoT Edge 方案工作區。 裡面會有 **modules** 資料夾、部署資訊清單範本檔案和 **.env** 檔案。 

### <a name="add-your-registry-credentials"></a>新增登錄認證

環境檔案會儲存容器存放庫的認證，並與 IoT Edge 執行階段共用這些認證。 執行階段需要有這些認證才能將私人映像提取到 IoT Edge 裝置。 

1. 在 VS Code 總管中，開啟 **.env** 檔案。 
2. 使用從 Azure Container Registry 複製過來的 [使用者名稱] 和 [密碼] 值來更新欄位。 
3. 儲存這個檔案。 

### <a name="update-the-module-with-custom-code"></a>使用自訂程式碼來更新模組

每個範本均隨附程式碼範例，會從 **tempSensor** 模組擷取模擬感應器資料，再將資料路由傳送到 IoT 中樞。 在本節中，請新增程式碼來擴充 pythonModule，以便先分析訊息再傳送。 

1. 在 VS Code 總管中，開啟 [modules] > [PythonModule] > [main.py]。

2. 在 **main.py** 頂端，匯入 `json` 程式庫。

    ```python
    import json
    ```

3. 在全域計數器下新增 `TEMPERATURE_THRESHOLD` 和 `TWIN_CALLBACKS` 變數。 溫度閾值會設定在將資料傳送至 IoT 中樞之前，測量的機器溫度必須超過的值。

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. 使用下列程式碼來取代 `receive_message_callback` 函式：

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

5. 新增稱為 `module_twin_callback` 的函式。 當更新所需的屬性時，會叫用此函式。

    ```python
    # module_twin_callback is invoked when twin's desired properties are updated.
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

6. 在類別 `HubManager` 中，將新行加入 `__init__` 方法以初始化您剛新增的 `module_twin_callback` 函式。

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. 儲存這個檔案。

## <a name="build-your-iot-edge-solution"></a>建置 IoT Edge 解決方案

在上一節中，您已建立 IoT Edge 解決方案，並在 PythonModule 中新增了程式碼，來篩選出所回報機器溫度低於可接受閾值的訊息。 現在，您需要建置容器映像形式的解決方案，並將它推送到容器登錄。 

1. 透過在 Visual Studio Code 整合式終端機中輸入下列命令來登入 Docker，以便將模組映像推送到 ACR： 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   使用您在第一節中從 Azure Container Registry 複製而來的使用者名稱、密碼及登入伺服器。 或者，在 Azure 入口網站中從登錄的 [存取金鑰] 區段再擷取一次這些資料。

2. 在 VS Code 總管中，於 IoT Edge 解決方案工作區開啟 **deployment.template.json** 檔案。 

   此檔案會指示 `$edgeAgent` 部署兩個模組：**tempSensor** (會模擬裝置資料) 和 **PythonModule**。 `PythonModule.image` 值會設定為映像的 Linux amd64 版本。 若要深入了解部署資訊清單，請參閱[了解如何使用、設定以及重複使用 IoT Edge 模組](module-composition.md)。

   此檔案也包含登錄的認證。 範本檔案會在使用者名稱和密碼中填入預留位置。 當您產生部署資訊清單時，系統就會使用您新增至 **.env** 的值來更新這兩個欄位。 

3. 在部署資訊清單中新增 PythonModule 模組對應項。 在 `moduleContent` 區段底部，於 `$edgeHub` 模組對應項之後插入下列 JSON 內容： 
    ```json
        "PythonModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. 儲存這個檔案。

5. 在 VS Code 總管中，以滑鼠右鍵按一下 **deployment.template.json** 檔案，然後選取 [建置 IoT Edge 解決方案]。 

當您指示 Visual Studio Code 建置解決方案時，它會先擷取部署範本中的資訊，再於新的 **config** 資料夾中產生 `deployment.json` 檔案。 然後，它會在整合式終端機中執行兩個命令：`docker build` 和 `docker push`。 這兩個命令會建置程式碼、將 Python 程式碼容器化，再將程式碼推送至您在初始化解決方案時所指定的容器登錄中。 

您可以在執行於 VS Code 整合式終端機的 `docker build` 命令中看到完整的容器映像位址。 系統會根據 `module.json` 檔案中的資訊建置映像位址，其格式如下：**\<存放庫\>:\<版本\>-\<平台\>**。 在此教學課程中，其樣貌如下：**registryname.azurecr.io/pythonmodule:0.0.1-amd64**。

## <a name="deploy-and-run-the-solution"></a>部署並執行解決方案

您可以利用和快速入門相同的方式，使用 Azure 入口網站將 Python 模組部署到 IoT Edge 裝置，但您也可以從 Visual Studio Code 部署和監視模組。 下列幾節會使用先決條件中所列的「適用於 VS Code 的 Azure IoT Edge 擴充功能」。 如果還未安裝該擴充功能，請立即安裝。 

1. 選取 [檢視] > [命令選擇區] 來開啟 VS Code 命令選擇區。

2. 搜尋和執行命令 **Azure: Sign in**。 遵循指示來登入 Azure 帳戶。 

3. 在命令選擇區中，搜尋並執行命令 **Azure IoT Hub: Select IoT Hub**。 

4. 選取 IoT 中樞所在的訂用帳戶，然後選取您想要存取的 IoT 中樞。

5. 在 VS Code 總管中，展開 [Azure IoT 中樞裝置] 區段。 

6. 以滑鼠右鍵按一下 IoT Edge 裝置的名稱，然後選取 [建立 IoT Edge 裝置的部署]。 

7. 瀏覽至 PythonModule 所在的解決方案資料夾。 開啟 **config** 資料夾，然後選取 **deployment.json** 檔案。 按一下 [選取 Edge 部署資訊清單]。

8. 重新整理 [Azure IoT 中樞裝置] 區段。 您應該會看到新的 **PythonModule** 正在與 **TempSensor** 模組以及 **$edgeAgent** 和 **$edgeHub** 一起執行。 

## <a name="view-generated-data"></a>檢視產生的資料

1. 若要監視抵達 IoT 中樞的資料，請按一下 [...]，然後選取 [開始監視 D2C 訊息]。
2. 若要監視特定裝置的 D2C 訊息，請以滑鼠右鍵按一下清單中的裝置，然後選取 [開始監視 D2C 訊息]。
3. 若要停止監視資料，請在命令選擇區中執行命令 **Azure IoT Hub: Stop monitoring D2C message**。 
4. 若要檢視或更新模組對應項，請以滑鼠右鍵按一下清單中的模組，然後選取 [編輯模組對應項]。 若要更新模組對應項，請儲存對應項 JSON 檔案，並以滑鼠右鍵按一下編輯器區域，然後選取 [更新模組對應項]。
5. 若要檢視 Docker 記錄，您可以安裝適用於 VS Code 的 [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)，並在 Docker 總管中於本機尋找執行中的模組。 在快顯功能表中，按一下 [顯示記錄] 以在整合式終端機中進行檢視。 

## <a name="clean-up-resources"></a>清除資源 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

如果您將繼續閱讀下一篇建議的文章，則可以保留您已建立的資源和設定，並加以重複使用。

否則，您可以刪除在本文中建立的本機設定和 Azure 資源，以避免產生費用。 

> [!IMPORTANT]
> 刪除 Azure 資源和資源群組是無法回復的動作。 一旦刪除，資源群組和其中包含的所有資源都將永久刪除。 請確定您不會不小心刪除錯誤的資源群組或資源。 如果您在現有的資源群組內建立了 IoT 中樞，而該群組中包含您想要保留的資源，則您只需刪除 IoT 中樞本身即可，而不要刪除資源群組。
>

若只要刪除 IoT 中樞，請使用中樞名稱和資源群組名稱執行下列命令：

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


若要依名稱刪除整個資源群組：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [資源群組]。

2. 在 [依名稱篩選...] 文字方塊中，輸入包含 IoT 中樞的資源群組名稱。 

3. 在結果清單中的資源群組右側，按一下 **...**，然後按一下 [刪除資源群組]。

4. 系統將會要求您確認是否刪除資源。 再次輸入您的資源群組名稱進行確認，然後按一下 [刪除]。 片刻過後，系統便會刪除該資源群組及其所有內含的資源。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立包含程式碼的 IoT Edge 模組，可篩選您 IoT Edge 裝置所產生的原始資料。 您可以繼續閱讀下列任何教學課程，以了解 Azure IoT Edge 有什麼其他方法可協助您將此資料轉換成具有優勢的商業見解。

> [!div class="nextstepaction"]
> [將 Azure Functions 部署為模組](tutorial-deploy-function.md)
> [將 Azure 串流分析部署為模組](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
