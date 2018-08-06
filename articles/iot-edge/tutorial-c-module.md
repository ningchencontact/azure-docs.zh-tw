---
title: Azure IoT Edge C 教學課程 | Microsoft Docs
description: 本教學課程說明如何使用 C 程式碼建立 IoT Edge 模組，並將其部署到邊緣裝置
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 07/30/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: c9d1931f1b78bb19f5e321a19baca45265ea7ab4
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413156"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>教學課程：開發 C IoT Edge 模組並部署至模擬裝置

您可以使用 IoT Edge 模組來部署程式碼，將您的商務邏輯直接實作到您的 IoT Edge 裝置。 本教學課程會逐步引導您建立並部署能篩選感應器資料的 IoT Edge 模組。 在本教學課程中，您了解如何：    

> [!div class="checklist"]
> * 使用 Visual Studio Code 以 C 語言建立 IoT Edge 模組
> * 使用 Visual Studio Code 和 Docker 建立 Docker 映像，並將其發佈至容器登錄 
> * 將模組部署到您的 IoT Edge 裝置
> * 檢視產生的資料


您於此教學課程中建立的 IoT Edge 模組，能夠篩選由您的裝置所產生的溫度資料。 它只有在溫度超過指定的閾值時，才會將訊息往上游傳送。 這類於邊緣所進行的分析，對於減少針對雲端所傳輸及儲存的資料量相當有幫助。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>必要條件

Azure IoT Edge 裝置：

* 您可以遵循 [Linux](quickstart-linux.md) 或 [Windows 裝置](quickstart.md)快速入門中的步驟，使用您的開發電腦或虛擬機器作為邊緣裝置。

雲端資源：

* Azure 中的標準層 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。 

開發資源：

* [Visual Studio Code](https://code.visualstudio.com/)。 
* 適用於 Visual Studio Code 的 [C/C++ 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)。
* 適用於 Visual Studio Code 的 [Azure IoT Edge 擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。
* [Docker CE](https://docs.docker.com/install/)。 


## <a name="create-a-container-registry"></a>建立容器登錄庫
在本教學課程中，您會使用適用於 VS Code 的 Azure IoT Edge 擴充功能來建置模組，並從檔案建立**容器映像**。 接著，您會將此映像推送至儲存並管理映像的**登錄**。 最後，您會從登錄部署該映像，以在 IoT Edge 裝置上執行。  

您可以此教學課程中使用任何與 Docker 相容的登錄。 於雲端中提供使用的兩個熱門 Docker 登錄服務為 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 和 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) \(英文\)。 本教學課程使用的是 Azure Container Registry。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源] > [容器] > [Azure Container Registry]。
2. 為登錄提供名稱，選擇訂用帳戶，選擇資源群組，然後將 SKU 設定為 [基本]。 
3. 選取 [建立] 。
4. 建立容器登錄之後，請瀏覽至它並選取 [存取金鑰]。 
5. 將 [管理使用者] 切換為 [啟用]。
6. 複製 [登入伺服器]、[使用者名稱] 及 [密碼] 的值。 稍後您在本教學課程中，將 Docker 映像發佈到您的登錄，或將登錄認證新增至 Edge 執行階段時，將會用到這些值。 

## <a name="create-an-iot-edge-module-project"></a>建立 IoT Edge 模組專案
下列步驟說明如何使用 Visual Studio Code 和 Azure IoT Edge 擴充功能，建立以 .NET Core 2.0 為基礎的 IoT Edge 模組專案。
1. 在 Visual Studio Code 中，選取 [檢視] > [整合式終端機] 以開啟 VS Code 整合式終端機。
2. 選取 [檢視] > [命令選擇區]，以開啟 VS Code 命令選擇區。 
3. 在命令選擇區中，輸入並執行命令 **Azure: Sign in**，然後依照指示登入您的 Azure 帳戶。 如果您已登入，則可以略過此步驟。
4. 在命令選擇區中，輸入並執行命令 **Azure IoT Edge: New IoT Edge solution**。 在命令選擇區中提供下列資訊，以建立解決方案： 
   1. 選取要用來建立解決方案的資料夾。 
   2. 為解決方案提供名稱，或是接受預設值 **EdgeSolution**。
   3. 選擇 [C 模組] 作為模組範本。 
   4. 將模組命名為 **CModule**。 
   5. 將您在上一節所建立的 Azure Container Registry 指定為第一個模組的映像存放庫。 將 **localhost:5000** 取代為您所複製的登入伺服器值。 最終字串的樣貌如下：**\<登錄名稱\>.azurecr.io/cmodule**。
 
4. VS Code 視窗會載入您的 IoT Edge 方案工作區。 裡面會有 **modules** 資料夾、**.vscode** 資料夾、部署資訊清單範本檔案和 **.env** 檔案。 預設模組程式碼會以管道模組形式實作。 

5. 若要以 JSON 格式篩選訊息，必須匯入適用於 C 的 JSON 程式庫。 您可以選擇任何 JSON 程式庫，或在您的 C 模組中撰寫自己的程式庫來剖析 JSON。 下列步驟使用 [Parson](https://github.com/kgabis/parson) 作為範例。
   1. 從 [Parson Github 存放庫](https://github.com/kgabis/parson)下載 **parson.c** 和 **parson.h**。 然後將這兩個檔案複製並貼到 **CModule** 資料夾中。
   2. 開啟 [模組] > [CModule] > [CMakeLists.txt]。 新增下列幾行，匯入 parson 程式庫作為 my_parson。

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. 在 **CMakeLists.txt** 的 `target_link_libraries` 中，新增 `my_parson`。
   4. 開啟 [模組] > [CModule] > [main.c]。 在 include 區段的底部，新增以下程式碼以包含 `parson.h` 進行 JSON 支援：

      ```c
      #include "parson.h"
      ```

6. 在 include 區段之後新增全域變數 `temperatureThreshold`。 此變數會設定在將資料傳送至 IoT 中樞之前，測量的溫度必須超過的值。 

    ```c
    static double temperatureThreshold = 25;
    ```

7. 使用以下程式碼來取代整個 `CreateMessageInstance` 函式。 此函式會配置回呼的內容。 

    ```c
    static MESSAGE_INSTANCE* CreateMessageInstance(IOTHUB_MESSAGE_HANDLE message)
    {
        MESSAGE_INSTANCE* messageInstance = (MESSAGE_INSTANCE*)malloc(sizeof(MESSAGE_INSTANCE));
        if (NULL == messageInstance)
        {
            printf("Failed allocating 'MESSAGE_INSTANCE' for pipelined message\r\n");
        }
        else
        {
            memset(messageInstance, 0, sizeof(*messageInstance));

            if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
            {
                free(messageInstance);
                messageInstance = NULL;
            }
            else
            {
                messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
                MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
                if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
                {
                    printf("ERROR: Map_AddOrUpdate Failed!\r\n");
                }
            }
        }

        return messageInstance;
    }
    ```

8. 使用以下程式碼來取代整個 `InputQueue1Callback` 函式。 此函式會實作實際的傳訊篩選條件。 

    ```c
    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) != IOTHUB_MESSAGE_OK)
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n", 
                messagesReceivedByInput1Queue, messageBody);

        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

9. 新增 `moduleTwinCallback` 函式。 此方法會從模組對應項接收所需的屬性，並會更新 **temperatureThreshold** 變數以符合該屬性。 所有模組都具有自己的模組對應項，這可讓您直接從雲端設定於模組內執行的程式碼。

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n", 
            ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

10. 在 `SetupCallbacksForModule` 函式中新增 `moduleTwinCallback` 的回呼。 使用以下程式碼來取代整個 `SetupCallbacksForModule` 函式。

    ```c
    static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
    {
        int ret;

        if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else
        {
            ret = 0;
        }

        return ret;
    }
    ```

11. 儲存這個檔案。

## <a name="build-your-iot-edge-solution"></a>建置 IoT Edge 解決方案

在上一節中，您已建立 IoT Edge 解決方案，並在 CModule 中新增了程式碼，來篩選出所回報機器溫度低於可接受閾值的訊息。 現在，您需要建置容器映像形式的解決方案，並將它推送到容器登錄。 

1. 透過在 Visual Studio Code 整合式終端機中輸入下列命令來登入 Docker，以便將模組映像推送到 ACR： 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   使用您在第一節中從 Azure Container Registry 複製而來的使用者名稱、密碼及登入伺服器。 或者，在 Azure 入口網站中從登錄的 [存取金鑰] 區段再擷取一次這些資料。

2. 在 VS Code 總管中，於 IoT Edge 解決方案工作區中開啟 **deployment.template.json** 檔案。 此檔案會指示 `$edgeAgent` 部署兩個模組：**tempSensor** 和 **CModule**。 `CModule.image` 值會設定為映像的 Linux amd64 版本。 若要深入了解部署資訊清單，請參閱[了解如何使用、設定以及重複使用 IoT Edge 模組](module-composition.md)。

3. 在 **deployment.template.json** 檔案中會有一個 **registryCredentials** 區段，其中儲存您的 Docker 登錄認證。 實際的使用者名稱和密碼組會儲存在 git 忽略的 .env 檔案中。

4. 在部署資訊清單中新增 CModule 模組對應項。 在 `moduleContent` 區段底部，於 `$edgeHub` 模組對應項後面插入下列 JSON 內容： 
    ```json
        "CModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. 儲存這個檔案。
5. 在 VS Code 總管中，以滑鼠右鍵按一下 **deployment.template.json** 檔案，然後選取 [建置 IoT Edge 解決方案]。 

當您指示 Visual Studio Code 建置解決方案時，它會先擷取部署範本中的資訊，再於新的 **config** 資料夾中產生 `deployment.json` 檔案。 然後，它會在整合式終端機中執行兩個命令：`docker build` 和 `docker push`。 這兩個命令會建置程式碼、將 `CModule.dll` 容器化，再將程式碼推送至您在初始化解決方案時所指定的容器登錄。 

您可以在 VS Code 整合式終端機中檢視完整容器映像位址。 系統會根據 `module.json` 檔案中的資訊建置映像位址，其格式如下：**\<存放庫\>:\<版本\>-\<平台\>**。 在此教學課程中，其樣貌如下：**registryname.azurecr.io/cmodule:0.0.1-amd64**。

## <a name="deploy-and-run-the-solution"></a>部署並執行解決方案

1. 使用 IoT 中樞連接字串來設定 Azure IoT Toolkit 擴充功能： 
    1. 選取 [檢視] > [檔案總管] 來開啟 VS Code 檔案總管。 
    2. 在檔案總管中，按一下 [AZURE IOT HUB DEVICES]，然後按一下 [...]。按一下 [選取 IoT 中樞]。 請依照指示登入您的 Azure 帳戶，然後選擇 IoT 中樞。 
       請注意，您也可以按一下 [設定 IoT 中樞連接字串] 以進行設定。 在快顯視窗中，輸入與您的 IoT Edge 裝置連線的 IoT 中樞所使用的連接字串。

2. 在 Azure IoT 中樞裝置總管中，以滑鼠右鍵按一下 IoT Edge 裝置，然後按一下 [建立 IoT Edge 裝置的部署]。 選取 **config** 資料夾中的 **deployment.json** 檔案，然後按一下 [選取 Edge 部署資訊清單]。

3. 按一下 [重新整理] 按鈕。 您應該會看到新的 **CModule** 正在與 **TempSensor** 模組以及 **$edgeAgent** 和 **$edgeHub** 一起執行。 

## <a name="view-generated-data"></a>檢視產生的資料

1. 若要監視送達 IoT 中樞的資料，請按一下 [...]，然後選取 [開始監視 D2C 訊息]。
2. 若要監視特定裝置的 D2C 訊息，請以滑鼠右鍵按一下清單中的裝置，然後選取 [開始監視 D2C 訊息]。
3. 若要停止監視資料，請在命令選擇區中執行命令 **Azure IoT Hub: Stop monitoring D2C message**。 
4. 若要檢視或更新模組對應項，請以滑鼠右鍵按一下清單中的模組，然後選取 [編輯模組對應項]。 若要更新模組對應項，請儲存對應項 JSON 檔案，並以滑鼠右鍵按一下編輯器區域，然後選取 [更新模組對應項]。
5. 若要檢視 Docker 記錄，您可以安裝適用於 VS Code 的 [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)，並在 Docker 總管中於本機尋找執行中的模組。 在快顯功能表中，按一下 [顯示記錄] 以在整合式終端機中進行檢視。
 
## <a name="clean-up-resources"></a>清除資源 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

如果您將繼續閱讀下一篇建議的文章，則可以保留您已建立的資源和設定，並加以重複使用。

否則，您可以刪除在本文中建立的本機組態和 Azure 資源，以避免產生費用。 

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

在本教學課程中，您已建立包含程式碼的 IoT Edge 模組，可篩選您 IoT Edge 裝置所產生的原始資料。 當您準備好要建置自己的模組時，可以進一步了解如何[使用適用於 Visual Studio Code 的 Azure IoT Edge 開發 C 模組](how-to-develop-c-module.md)。 您可以繼續進行後續教學課程，以了解 Azure IoT Edge 有什麼其他方法可協助您將此資料轉換成 Edge 上的商業見解。

> [!div class="nextstepaction"]
> [使用 SQL Server 資料庫在Edge 上儲存資料](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
