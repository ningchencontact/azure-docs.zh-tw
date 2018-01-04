---
title: "您可以使用 Visual Studio 程式碼來開發 Azure IoT 邊緣的 C# 模組 |Microsoft 文件"
description: "開發和部署 Azure IoT 邊緣 VS 程式碼中的 C# 模組沒有內容切換"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 269f77e5015175e45e0078926ef06699811889a4
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2017
---
# <a name="use-visual-studio-code-to-develop-c-module-with-azure-iot-edge"></a>使用 Visual Studio 程式碼來開發 Azure IoT 邊緣的 C# 模組
本文章提供詳細的指示使用[Visual Studio Code](https://code.visualstudio.com/)與主要開發工具來開發和部署您的 IoT 邊緣模組。 

## <a name="prerequisites"></a>必要條件
本教學課程假設您使用的電腦或您的開發電腦以執行 Windows 或 Linux 的虛擬機器。 您的 IoT 邊緣裝置可能是另一個實體裝置，或您可以在開發電腦上模擬您的 IoT 邊緣裝置。

請確定您有已完成下列教學課程，才能啟動這項指引。
- 在模擬的裝置上部署 Azure IoT 邊緣[Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows)或[Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [開發，並將 C# IoT 邊緣模組部署到您模擬的裝置](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)

以下是顯示的項目，您應該在您完成前述的教學課程後的檢查清單。

- [Visual Studio Code](https://code.visualstudio.com/)。 
- [Visual Studio Code 的 Azure IoT Edge 擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
- [C# for Visual Studio Code (採用 OmniSharp 技術) 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 
- [Python 2.7](https://www.python.org/downloads/)
- [IoT 邊緣控制指令碼](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- AzureIoTEdgeModule 範本 (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- 至少在 IoT 邊緣裝置與 active IoT 中樞。

它也會建議安裝[VS Code 的 Docker 支援](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)若要加強管理容器和模組映像。

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>部署 VS 程式碼中的 Azure IoT 邊緣模組

### <a name="list-your-iot-hub-devices"></a>列出您的 IoT 中樞裝置
有兩種方式，來列出您的 IoT 中樞裝置 VS 程式碼中。 您可以選擇繼續其中一個方法。

#### <a name="sign-in-your-azure-account-in-vscode-and-choose-your-iot-hub"></a>登入您的 Azure 帳戶中 VSCode 並選擇 IoT 中樞
1. 在命令選擇區 （f1 鍵或 Ctrl + Shift + P），輸入，然後選取**Azure： 登入**。 然後按一下  **複製*& 開啟** 快顯視窗中。 貼入 （Ctrl + V） 程式碼在您的瀏覽器中，按一下 [繼續] 按鈕。 然後登入您的 Azure 帳戶。 您可以看到您在 VS Code 狀態列中的帳戶資訊。
2. 在命令選擇區 （f1 鍵或 Ctrl + Shift + P），輸入，然後選取**IoT： 選取 IoT 中樞**。 您第一次選取上一個教學課程中建立 IoT 中樞所在的訂用帳戶。 然後選擇 IoT 中樞包含 IoT 邊緣裝置。


#### <a name="set-iot-hub-connection-string"></a>設定 IoT 中樞連接字串
1. 在命令選擇區 （f1 鍵或 Ctrl + Shift + P），輸入，然後選取**IoT： 設定 IoT 中樞連接字串**。 請確定您貼上連接字串在原則下的**iothubowner** （您可以找到它的 IoT 中樞共用存取原則在 Azure 入口網站中）。
 

您可以看到在左提要欄位中的 IoT 中樞裝置總管 中的裝置清單。

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>啟動您的 IoT 邊緣執行階段和部署模組
安裝並啟動您裝置上的 Azure IoT Edge 執行階段。 並部署會將遙測資料傳送到 IoT 中樞模擬感應器模組。
1. 命令調色盤中選取**邊緣： 安裝邊緣**，然後選擇 IoT 邊緣裝置識別碼。 或邊緣裝置識別碼，在裝置清單中的以滑鼠右鍵按一下並選取**安裝邊緣**。
2. 命令調色盤中選取**邊緣： 開始邊緣**開始邊緣 runtime。 您可以看到對應的輸出，在整合式終端機。
3. 檢查 [Docker 總管] 中的 Edge 執行階段狀態。 綠色表示它正在執行。 已成功啟動您的 IoT 邊緣執行階段。
4. 現在您邊緣執行階段正在執行，這表示您的電腦現在會模擬在邊緣裝置。 下一個步驟是要模擬會持續將訊息傳送至邊緣裝置 sensorthing。 命令調色盤上，輸入，然後選取**邊緣： 產生邊緣組態檔**。 然後選取要建立此檔案的資料夾。 在產生的 deployment.json 檔案中，將這一行"<registry>/<image>:<tag>"與`microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`。
5. 選取**邊緣： 建立部署邊緣裝置**選擇邊緣裝置識別碼，以建立新的部署。 您可以以滑鼠右鍵按一下 [裝置] 清單中的邊緣裝置識別碼，並選取或者**建立邊緣裝置部署**。 
6. 您應該會看到您的 IoT 邊緣以開始執行 Docker 總管與模擬的感應器。 以滑鼠右鍵按一下 Docker 總管 中的容器。 您可以觀賞 docker 記錄檔中的每個模組。
7. 以滑鼠右鍵按一下您的邊緣裝置識別碼，您可以監視 D2C VS 程式碼中的訊息。
8. 若要停止您的 IoT 邊緣執行階段和感應器模組，您可以輸入，並選取**邊緣： 停止邊緣**命令調色盤中。

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>開發和部署 VS 程式碼中的 C# 模組
在教學課程[開發 C# 模組](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)、 更新、 建置和發行您的模組映像 VS 程式碼中，然後瀏覽 Azure 入口網站中部署您的 C# 模組。 本節將介紹如何部署及監視您的 C# 模組使用 VS 程式碼。

### <a name="start-a-local-docker-registry"></a>啟動本機 docker 登錄
您可以此教學課程中使用任何與 Docker 相容的登錄。 於雲端中提供使用的兩個熱門 Docker 登錄服務為 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 和 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) \(英文\)。 本節使用[本機 Docker 登錄](https://docs.docker.com/registry/deploying/)，也就是更容易在您的早期開發時測試用途。
在 VS Code**整合的終端機**(Ctrl + ') 中，執行下列命令來啟動本機登錄。  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> 上述範例示範只適用於測試的登錄設定。 備妥登錄必須受 TLS 保護，而且應該在理想情況下使用存取控制機制。 我們建議您改用[Azure 容器登錄中](https://docs.microsoft.com/azure/container-registry/)或[Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)部署備妥 IoT 邊緣模組。

### <a name="create-an-iot-edge-module-project"></a>建立 IoT Edge 模組專案
下列步驟會示範如何 使用 Visual Studio Code 和 Azure IoT Edge 擴充功能，來建立以 .NET core 2.0 為基礎的 IoT Edge 模組。 如果您已完成本節先前的教學課程中，您可以放心地略過本節。
1. 在 Visual Studio Code 中，選取 [檢視] > [整合式終端機] 以開啟 VS Code 整合式終端機。
3. 在整合式終端機中，輸入下列命令來安裝 (或更新) dotnet 中的 **AzureIoTEdgeModule** 範本：

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. 針對新模組建立專案。 下列命令會在目前的工作資料夾中建立專案資料夾 **FilterModule**：

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```
 
3. 選取 [檔案] > [開啟資料夾]。
4. 瀏覽至 **FilterModule** 資料夾，然後按一下 [選取資料夾] 以在 VS Code 中開啟專案。
5. 在 VS Code 檔案總管中，按一下 **Program.cs** 來將其開啟。
6. 將 `temperatureThreshold` 變數新增至 **Program** 類別。 此變數會設定在將資料傳送至 IoT 中樞之前，測量的溫度必須超過的值。 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

7. 將 `MessageBody`、`Machine` 及 `Ambient` 類別新增至 **Program** 類別。 這些類別會定義內送郵件本文的預期結構描述。

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

8. 在 **Init** 方法中，該程式碼會建立並設定 **DeviceClient** 物件。 此物件可允許模組連線至本機的 Azure IoT Edge 執行階段，以便傳送和接收訊息。 用於 **Init** 方法的連接字串，將會由 IoT Edge 執行階段提供給模組。 建立 **DeviceClient** 之後，程式碼會註冊回呼以透過 **input1** 端點接收來自 IoT Edge 中樞的訊息。 將 `SetInputMessageHandlerAsync` 方法取代為新的版本，然後針對所需的屬性更新新增 `SetDesiredPropertyUpdateCallbackAsync` 方法。 若要進行這項變更，請使用下列程式碼取代 **Init** 方法的最後一行：

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. 將 `onDesiredPropertiesUpdate` 方法新增至 **Program** 類別。 此方法會從模組對應項接收所需的屬性，並會更新 **temperatureThreshold** 變數以符合該屬性。 所有模組都具有自己的模組對應項，這可讓您直接從雲端設定於模組內執行的程式碼。

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
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

10. 使用 `FilterMessages` 方法來取代 `PipeMessage` 方法。 每當模組從 IoT Edge 中樞接收到訊息時，就會呼叫此方法。 它會篩選所報告溫度低於 (透過模組對應項所設定) 之溫度閾值的訊息。 針對具有設定為 [警示] 之值的訊息，此方法也會將 **MessageType** 屬性新增至該訊息。 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        int counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            byte[] messageBytes = message.GetBytes();
            string messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get message body
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
                await deviceClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

11. 若要建置專案，請在檔案總管中以滑鼠右鍵按一下 **FilterModule.csproj** 檔案，然後按一下 [建置 IoT Edge 模組]。 此程序會編譯模組，並將二進位檔及其相依性匯出至用於建立 Docker 映像的資料夾中。


### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>建立 Docker 映像並發行到您的登錄中

1. 在 VS Code 檔案總管中，展開 [Docker] 資料夾。 然後展開適用於您容器平台的資料夾：**linux-x64** 或 **windows-nano**。
2. 以滑鼠右鍵按一下 [Dockerfile] 檔案，然後按一下 [建置 IoT Edge 模組的 Docker 映像]。 
3. 在 [選取資料夾] 視窗中，瀏覽至或是輸入 `./bin/Debug/netcoreapp2.0/publish`。 按一下 [選取資料夾為 EXE_DIR]。
4. 在 VS Code 視窗頂端的快顯文字方塊中，輸入映像名稱。 例如：`<your container registry address>/filtermodule:latest`。 如果您要部署到本機登錄中，它應該是`localhost:5000/filtermodule:latest`。
5. 將映像推送到您的 Docker 存放庫。 使用**邊緣： Push IoT 邊緣模組 Docker 映像**命令，並在 VS 程式碼視窗頂端快顯的文字方塊中輸入影像 URL。 使用相同的影像 URL 中使用上述步驟。

### <a name="deploy-your-iot-edge-modules"></a>部署您的 IoT 邊緣模組

1. 開啟`deployment.json`檔案中，取代**模組**區段中具有下列內容：
    ```json
    "tempSensor": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
            "createOptions": ""
        }
    },
    "filtermodule": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "localhost:5000/filtermodule:latest",
            "createOptions": ""
        }
    }
    ```

2. 取代**路由**區段中具有下列內容：
    ```json
    {
        "routes": {
            "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        }
    }
    ```
   > [!NOTE]
   > 執行階段中的宣告式規則會定義這些訊息的流向。 在本教學課程中，您需要兩個路由。 第一個路由傳輸溫度感應器透過 「 input1 」 端點，篩選模組的訊息，這是您設定與 FilterMessages 處理常式的端點。 第二個路由會將訊息從篩選模組傳輸到 IoT 中樞。 在此路由中上游是特殊的目的地，告知邊緣集線器，以將訊息傳送到 IoT 中樞。

3. 儲存這個檔案。
4. 命令調色盤中選取**邊緣： 建立部署邊緣裝置**。 然後，選取您的 IoT 邊緣裝置識別碼，以建立部署。 或以滑鼠右鍵按一下 [裝置] 清單中的裝置識別碼，並選取**建立邊緣裝置部署**。
5. 選取`deployment.json`您更新。 在 [輸出] 視窗中，您可以看到對應的輸出，為您的部署。
6. 開始邊緣 runtime 命令調色盤中。 **邊緣： 開始邊緣**
7. 您可以查看您執行階段以開始執行 Docker 總管使用模擬的感應器和篩選模組的 IoT 邊緣。
8. 以滑鼠右鍵按一下您的邊緣裝置識別碼，您可以監視 D2C VS 程式碼中的訊息。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您建立 IoT 邊緣模組並將它部署到 IoT 邊緣裝置 VS 程式碼中。 您可以繼續閱讀下列教學課程來了解其他案例時開發 VS 程式碼中的 Azure IoT 邊緣之一。

> [!div class="nextstepaction"]
> [偵錯 VS 程式碼中的 C# 模組](how-to-vscode-debug-csharp-module.md)
