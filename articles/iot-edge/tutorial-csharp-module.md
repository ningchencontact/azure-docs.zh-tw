---
title: Azure IoT Edge C# 教學課程 | Microsoft Docs
description: 本教學課程說明如何使用 C# 程式碼建立 IoT Edge 模組，並將其部署到邊緣裝置。
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: c94479ca523f0097c8fbf94729f3a255ffc0c2bf
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413216"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>教學課程：開發 C# IoT Edge 模組並部署至模擬裝置

您可以使用 Azure IoT Edge 模組來部署程式碼，直接在 IoT Edge 裝置上實作您的商務邏輯。 本教學課程會逐步引導您建立並部署能篩選感應器資料的 IoT Edge 模組。 您將會使用模擬的 IoT Edge 裝置，其建立方法詳述於[在 Windows 中的模擬裝置上部署 Azure IoT Edge][lnk-tutorial1-win]，以及[在 Linux 中的模擬裝置上部署 Azure IoT Edge][lnk-tutorial1-lin] 這兩個快速入門中。 在本教學課程中，您了解如何：    

> [!div class="checklist"]
> * 使用 Visual Studio Code 建立以 .NET Core 2.0 SDK 為基礎的 IoT Edge 模組。
> * 使用 Visual Studio Code 和 Docker 建立 Docker 映像，並將其發佈至您的登錄中。
> * 將模組部署到您的 IoT Edge 裝置。
> * 檢視產生的資料。


您於此教學課程中建立的 IoT Edge 模組，能夠篩選由您裝置所產生的溫度資料。 它只有在溫度超過指定的閾值時，才會將訊息往上游傳送。 這類於邊緣所進行的分析，對於減少針對雲端所傳輸及儲存的資料量相當有幫助。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>必要條件

Azure IoT Edge 裝置：

* 您可以遵循 [Linux](quickstart-linux.md) 或 [Windows 裝置](quickstart.md)快速入門中的步驟，使用您的開發電腦或虛擬機器作為邊緣裝置。

雲端資源：

* Azure 中的標準層 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。 

開發資源：

* [Visual Studio Code](https://code.visualstudio.com/)。 
* [C# for Visual Studio Code (採用 OmniSharp 技術) 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。
* 適用於 Visual Studio Code 的 [Azure IoT Edge 擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。
* [Docker CE](https://docs.docker.com/install/)


## <a name="create-a-container-registry"></a>建立容器登錄庫
在本教學課程中，您會使用適用於 VS Code 的 Azure IoT Edge 擴充功能來建置模組，並從檔案建立**容器映像**。 接著，您會將此映像推送至儲存並管理映像的**登錄**。 最後，您會從登錄部署該映像，以在 IoT Edge 裝置上執行。  

您可以此教學課程中使用任何與 Docker 相容的登錄。 雲端中提供使用的兩個熱門 Docker 登錄服務為 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 和 [Docker 中樞](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) (英文)。 本教學課程使用的是 Azure Container Registry。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源] > [容器] > [Azure Container Registry]。
2. 為登錄提供名稱，選擇訂用帳戶，選擇資源群組，然後將 SKU 設定為 [基本]。 
3. 選取 [建立] 。
4. 建立容器登錄之後，請加以瀏覽，並選取 [存取金鑰]。 
5. 將 [管理使用者] 切換為 [啟用]。
6. 複製 [登入伺服器]、[使用者名稱] 及 [密碼] 的值。 稍後您在本教學課程中，將 Docker 映像發佈到您的登錄，或將登錄認證新增至 Azure IoT Edge 執行階段時，將會用到這些值。 

## <a name="create-an-iot-edge-module-project"></a>建立 IoT Edge 模組專案
下列步驟會使用 Visual Studio Code 和 Azure IoT Edge 擴充功能，建立以 .NET Core 2.0 SDK 為基礎的 IoT Edge 模組專案。

1. 在 Visual Studio Code 中，選取 [檢視] > [命令選擇區]，以開啟 VS Code 命令選擇區。 
2. 請在命令選擇區中，輸入並執行命令 **Azure: Sign in**，然後依照指示登入您的 Azure 帳戶。 如果您已登入，則可以略過此步驟。
3. 在 [命令選擇區] 中，輸入並執行命令 **Azure IoT Edge: New IoT Edge solution**。 在命令選擇區中提供下列資訊，以建立解決方案： 

   1. 選取要用來建立解決方案的資料夾。 
   2. 為解決方案提供名稱，或是接受預設值 **EdgeSolution**。
   3. 選擇 [C# 模組] 作為模組範本。 
   4. 將預設模組名稱取代為 **CSharpModule**。 
   5. 將您在上一節所建立的 Azure 容器登錄，指定為第一個模組的映像存放庫。 將 **localhost:5000** 取代為您所複製的登入伺服器值。 最終字串看起來會類似於：\<登錄名稱\>.azurecr.io/csharpmodule。

4.  VS Code 視窗會載入您的 IoT Edge 解決方案工作區：模組資料夾、\.vscode 資料夾、部署資訊清單範本檔案，以及 \.env 檔案。 請在 VS Code 總管中，開啟 [modules] > [CSharpModule] > [Program.cs]。

5. 在 **CSharpModule** 命名空間頂端，為稍後會用到的類型新增三個 **using** 陳述式：

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

6. 將 **temperatureThreshold** 變數新增至 [程式] 類別。 此變數會設定在將資料傳送至 IoT 中樞之前，測量的溫度必須超過的值。 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

7. 將 [MessageBody]、[Machine] 和 [Ambient] 類別新增至 [程式] 類別。 這些類別會定義內送郵件本文的預期結構描述。

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
       public double temperature {get; set;}
       public double pressure {get; set;}         
    }
    class Ambient
    {
       public double temperature {get; set;}
       public int humidity {get; set;}         
    }
    ```

8. 在 **Init** 方法中，該程式碼會建立並設定 **ModuleClient** 物件。 此物件可允許模組連線至本機的 Azure IoT Edge 執行階段，以便傳送和接收訊息。 **Init** 方法中使用的連接字串，將會由 IoT Edge 執行階段提供給模組。 在建立 **ModuleClient** 之後，程式碼會從模組對應項所需的屬性中讀取 **temperatureThreshold** 值。 程式碼會註冊回撥，以透過 **input1** 端點接收來自 IoT Edge 中樞的訊息。 將 **SetInputMessageHandlerAsync** 方法取代為新的方法，並將更新的 **SetDesiredPropertyUpdateCallbackAsync** 方法新增至所需的屬性。 若要進行這項變更，請使用下列程式碼取代 **Init** 方法的最後一行：

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    try {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    } catch(ArgumentOutOfRangeException e) {
        Console.WriteLine($"Property TemperatureThreshold not exist: {e.Message}"); 
    }

    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. 將 **onDesiredPropertiesUpdate** 方法新增至 [程式] 類別。 此方法會從模組對應項接收所需的屬性，並會更新 **temperatureThreshold** 變數以符合該屬性。 所有模組都具有自己的模組對應項，這可讓您直接從雲端設定於模組內執行的程式碼。

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

10. 使用 **FilterMessages** 方法取代 **PipeMessage** 方法。 每當模組從 IoT Edge 中樞接收到訊息時，就會呼叫此方法。 它會篩選所報告溫度低於 (透過模組對應項所設定) 之溫度閾值的訊息。 針對具有設定為 [警示] 之值的訊息，此方法也會將 **MessageType** 屬性新增至該訊息。 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await moduleClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

11. 儲存這個檔案。

## <a name="build-your-iot-edge-solution"></a>建置 IoT Edge 解決方案

在上一節中，您已建立 IoT Edge 解決方案，並將程式碼新增至 **CSharpModule**，以篩選掉報告的機器溫度低於可接受閾值的訊息。 現在，您需要建置容器映像形式的解決方案，並將它推送到容器登錄。 

1. 透過在 Visual Studio Code 整合式終端機中輸入下列命令，來登入 Docker。 接著，您可以將模組映像推送到您的 Azure 容器登錄。
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   使用您在第一節中從 Azure 容器登錄複製而來的使用者名稱、密碼及登入伺服器。 您也可以在 Azure 入口網站中，從登錄的 [存取金鑰] 區段擷取這些資料。

2. 在 VS Code 總管中，於 IoT Edge 解決方案工作區中開啟 deployment.template.json 檔案。 這個檔案會指示 **$edgeAgent** 部署兩個模組：**tempSensor** 和 **CSharpModule**。 **CSharpModule.image** 值會設定為映像的 Linux amd64 版本。 

   確認範本具有正確的模組名稱，而不是您在建立 IoT Edge 解決方案時變更的預設 **SampleModule** 名稱。

   若要深入了解部署資訊清單，請參閱[了解如何使用、設定以及重複使用 IoT Edge 模組](module-composition.md)。

3. 在 deployment.template.json 檔案中，**registryCredentials** 區段會儲存您的 Docker 登錄認證。 實際的使用者名稱和密碼組，會儲存在 git 所忽略的 .env 檔案中。  

4. 在部署資訊清單中新增 **CSharpModule** 模組對應項。 在 **moduleContent** 區段底部，於 **$edgeHub** 模組對應項後面插入下列 JSON 內容： 
    ```json
        "CSharpModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. 儲存這個檔案。

5. 在 VS Code 總管中，以滑鼠右鍵按一下 deployment.template.json 檔案，然後選取 [建置 IoT Edge 解決方案]。 

當您指示 Visual Studio Code 建置解決方案時，它會先擷取部署範本中的資訊，再於名為 **config** 的新資料夾中產生 deployment.json 檔案。然後，它會在整合式終端機中執行兩個命令：`docker build` 和 `docker push`。 這兩個命令會建置程式碼、將 CSharpModule.dll 容器化，再將程式碼推送至您在初始化解決方案時所指定的容器登錄中。 

您可以在 VS Code 整合式終端機中檢視完整容器映像位址。 系統會根據 module.json 檔案中的資訊建置映像位址，其格式如下：\<存放庫\>：\<版本\>-\<平台\>。 在本教學課程中，它會顯示為：registryname.azurecr.io/csharpmodule:0.0.1-amd64。

## <a name="deploy-and-run-the-solution"></a>部署並執行解決方案

1. 使用 IoT 中樞連接字串來設定 Azure IoT Toolkit 擴充功能： 

    1. 選取 [檢視] > [檔案總管] 來開啟 VS Code 檔案總管。

    1. 在 [總管] 中，選取 [Azure IoT 中樞裝置]、選取省略符號 (**...**)，然後選擇 [選取 IoT 中樞]。 請依照指示登入您的 Azure 帳戶，然後選擇 IoT 中樞。 

       > [!Note]
       > 您也可以選擇 [設定 IoT 中樞連接字串] 來完成設定。 在快顯視窗中，輸入與您的 IoT Edge 裝置連線的 IoT 中樞所使用的連接字串。

2. 在 Azure IoT 中樞裝置總管中，以滑鼠右鍵按一下 IoT Edge 裝置，然後選取 [針對 IoT Edge 裝置建立部署]。 選取 config 資料夾中的 deployment.json 檔案，然後選擇 [選取 Edge 部署資訊清單]。

3. 重新整理 [Azure IoT 中樞裝置] 區段。 您應該會看到新的 **CSharpModule** 正在與 **TempSensor** 模組以及 **$edgeAgent** 和 **$edgeHub** 一起執行。 

## <a name="view-generated-data"></a>檢視產生的資料

1. 若要監視抵達 IoT 中樞的資料，請選取省略符號 (**...**)，然後選取 [開始監視 D2C 訊息]。
2. 若要監視特定裝置的 D2C 訊息，請以滑鼠右鍵按一下清單中的裝置，然後選取 [開始監視 D2C 訊息]。
3. 若要停止監視資料，請在命令選擇區中執行命令 **Azure IoT Hub: Stop monitoring D2C message**。 
4. 若要檢視或更新模組對應項，請以滑鼠右鍵按一下清單中的模組，然後選取 [編輯模組對應項]。 若要更新模組對應項，請儲存對應項 JSON 檔案，並以滑鼠右鍵按一下編輯器區域，然後選取 [更新模組對應項]。
5. 若要檢視 Docker 記錄，請安裝適用於 VS Code 的 [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)。 您可以在 Docker 總管中，本機找到您執行中的模組。 在快顯功能表中，選取 [顯示記錄]，以在整合式終端機中進行檢視。
 
## <a name="clean-up-resources"></a>清除資源 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

如果您打算繼續閱讀下一篇建議的文章，則可以保留您所建立的資源和組態，並加以重複使用。

否則，可以刪除您在本文中建立的本機組態和 Azure 資源，以避免產生費用。 

> [!IMPORTANT]
> 刪除 Azure 資源和資源群組是無法回復的動作。 當您刪除這些項目時，資源群組和其中包含的所有資源都將永久刪除。 請確定您不會誤刪錯誤的資源群組或資源。 如果您在現有的資源群組內建立了 IoT 中樞，而該群組中包含您想要保留的資源，則您只需刪除 IoT 中樞資源本身即可，而不要刪除資源群組。
>

若只要刪除 IoT 中樞，請使用中樞名稱和資源群組名稱執行下列命令：

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


若要依名稱刪除整個資源群組：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取 [資源群組]。

2. 在 [依名稱篩選] 文字方塊中，輸入包含 IoT 中樞的資源群組名稱。 

3. 在結果清單中資源群組的右側，選取省略符號 (**...**)，然後選取 [刪除資源群組]。

4. 系統將會要求您確認是否刪除資源群組。 重新輸入您資源群組的名稱以進行確認，然後選取 [刪除]。 片刻過後，系統便會刪除該資源群組及其所有內含的資源。



## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立包含程式碼的 IoT Edge 模組，可篩選您 IoT Edge 裝置所產生的原始資料。 當您準備好要建置自己的模組時，可以進一步了解如何[使用適用於 Visual Studio Code 的 Azure IoT Edge 開發 C# 模組](how-to-develop-csharp-module.md)。 您可以繼續進行後續教學課程，以了解 Azure IoT Edge 有什麼其他方法，可協助您將此資料轉換成 Edge 上的商業見解。

> [!div class="nextstepaction"]
> [使用 SQL Server 資料庫在Edge 上儲存資料](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
