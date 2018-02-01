---
title: "使用 Visual Studio Code 以 Azure IoT Edge 開發 C# 模組 | Microsoft Docs"
description: "在 VS Code 中使用 Azure IoT Edge 開發和部署 C# 模組，而不必進行環境切換"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: cad28b4e6d4e46058641da19795cd71efdbd0c92
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2018
---
# <a name="use-visual-studio-code-to-develop-c-module-with-azure-iot-edge"></a>使用 Visual Studio Code 以 Azure IoT Edge 開發 C# 模組
本文會提供詳細指示，說明如何使用 [Visual Studio Code](https://code.visualstudio.com/) 作為開發和部署 IoT Edge 模組的主要開發工具。 

## <a name="prerequisites"></a>先決條件
本教學課程假設您使用執行 Windows 或 Linux 的電腦或虛擬機器作為開發電腦。 IoT Edge 裝置可以是其他實體裝置，您也可以在開發機器上模擬 IoT Edge 裝置。

開始使用本指引之前，請確定您已完成下列教學課程。
- 在 [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) 或 [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux) 中的模擬裝置上部署 Azure IoT Edge
- [開發 C# IoT Edge 模組並部署到模擬裝置](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)

下列檢查清單顯示您在完成前述教學課程之後應具備的項目。

- [Visual Studio Code](https://code.visualstudio.com/)。 
- [Visual Studio Code 的 Azure IoT Edge 擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
- [C# for Visual Studio Code (採用 OmniSharp 技術) 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 
- [Python 2.7](https://www.python.org/downloads/)
- [IoT Edge 控制指令碼](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- AzureIoTEdgeModule 範本 (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- 至少具有一個 IoT Edge 裝置的作用中 IoT 中樞。

此外，也建議您安裝[適用於 VS Code 的 Docker 支援](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) (英文)，以加強管理模組映像和容器。

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>在 VS Code 中部署 Azure IoT Edge 模組

### <a name="list-your-iot-hub-devices"></a>列出 IoT 中樞裝置
有兩種方法可列出 VS Code 中的 IoT 中樞裝置。 您可以選擇任一個方法來繼續。

#### <a name="sign-in-your-azure-account-in-vscode-and-choose-your-iot-hub"></a>在 VSCode 中登入 Azure 帳戶並選擇 IoT 中樞
1. 在命令選擇區 (F1 或 Ctrl + Shift + P) 中，輸入並選取 [Azure: 登入]。 然後按一下快顯視窗中的 [複製並開啟]。 在瀏覽器中貼上 (Ctrl + V) 程式碼，並按一下 [繼續] 按鈕。 然後使用 Azure 帳戶登入。 您可以在 VS Code 狀態列中看到帳戶資訊。
2. 在命令選擇區 (F1 或 Ctrl + Shift + P) 中，輸入並選取 [IoT: 選取 IoT 中樞]。 請先選取您在上一個教學課程中用來建立 IoT 中樞的訂用帳戶。 然後選擇包含 IoT Edge 裝置的 IoT 中樞。

    ![裝置清單](./media/how-to-vscode-develop-csharp-module/device-list.png)

#### <a name="set-iot-hub-connection-string"></a>設定 IoT 中樞連接字串
在命令選擇區 (F1 或 Ctrl + Shift + P) 中，輸入並選取 [IoT: 設定 IoT 中樞連接字串]。 請確實地將連接字串貼在 **iothubowner** 原則下 (您可以在 Azure 入口網站中 IoT 中樞的共用存取原則中找到此原則)。
 
您可以在左側邊欄的 IoT 中樞裝置總管中看到裝置清單。

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>啟動 IoT Edge 執行階段並部署模組
安裝並啟動您裝置上的 Azure IoT Edge 執行階段。 然後部署模擬感應器模組，以將遙測資料傳送到 IoT 中樞。
1. 在命令選擇區中，選取 [Edge: 設定 Edge]，然後選擇 IoT Edge 裝置識別碼。 或者，以滑鼠右鍵按一下裝置清單中的 Edge 裝置識別碼，然後選取 [設定 Edge]。

    ![設定 Edge 執行階段](./media/how-to-vscode-develop-csharp-module/setup-edge.png)

2. 在命令選擇區中，選取 [Edge: 啟動 Edge] 以啟動 Edge 執行階段。 您可以在整合式終端機中看到對應的輸出。

    ![啟動 Edge 執行階段](./media/how-to-vscode-develop-csharp-module/start-edge.png)

3. 在 Docker 總管中檢查 Edge 執行階段的狀態。 綠色表示正在執行。 IoT Edge 執行階段已成功啟動。

    ![Edge 執行階段正在執行](./media/how-to-vscode-develop-csharp-module/edge-runtime.png)

4. 現在，Edge 執行階段正在執行，這表示電腦正在模擬 Edge 裝置。 下一個步驟是模擬感應器，使其持續將訊息傳送至 Edge 裝置。 在命令選擇區中，輸入並選取 [Edge: 產生 Edge 組態檔]。 然後選取資料夾來建立此檔案。 在產生的 deployment.json 檔案中，將 `<registry>/<image>:<tag>` 內容替換為 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` 並儲存檔案。

    ![感應器模組](./media/how-to-vscode-develop-csharp-module/sensor-module.png)

5. 選取 [Edge: 建立 Edge 裝置的部署]，然後選擇 Edge 裝置識別碼以建立新的部署。 您也可以用滑鼠右鍵按一下裝置清單中的 Edge 裝置識別碼，然後選取 [建立 Edge 裝置的部署]。 

6. 您應該會在 Docker 總管中看到 IoT Edge 開始執行，其中具有模擬感應器。 以滑鼠右鍵按一下 Docker 總管中的容器。 您可以檢視每個模組的 Docker 記錄。 此外，您也可以在裝置清單中檢視模組清單。

    ![模組清單](./media/how-to-vscode-develop-csharp-module/module-list.png)

7. 以滑鼠右鍵按一下您的 Edge 裝置識別碼，就能在 VS Code 中監視 D2C 訊息。
8. 若要停止 IoT Edge 執行階段和感應器模組，您可以在命令選擇區中輸入並選取 [Edge: 停止 Edge]。

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>在 VS Code 中開發和部署 C# 模組
在[開發 C# 模組](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)教學課程中，您會在 VS Code 中更新、建置和發佈模組映像，然後瀏覽 Azure 入口網站以部署 C# 模組。 本節將介紹如何使用 VS Code 來部署及監視 C# 模組。

### <a name="start-a-local-docker-registry"></a>啟動本機 Docker 登錄
您可以此教學課程中使用任何與 Docker 相容的登錄。 於雲端中提供使用的兩個熱門 Docker 登錄服務為 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 和 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) \(英文\)。 本節使用[本機 Docker 登錄](https://docs.docker.com/registry/deploying/)，它可讓您更輕鬆地在早期開發期間進行測試。
在 VS Code **整合式終端機** (Ctrl + `) 中，執行下列命令來啟動本機登錄。  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> 上述範例顯示的登錄組態只適合用於測試。 已準備好用於生產環境的登錄必須受到 TLS 保護，且最好應使用存取控制機制。 我們建議您使用 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 或 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) (英文)，來部署已準備好用於生產環境的 IoT Edge 模組。

### <a name="create-an-iot-edge-module-project"></a>建立 IoT Edge 模組專案
下列步驟會示範如何 使用 Visual Studio Code 和 Azure IoT Edge 擴充功能，來建立以 .NET core 2.0 為基礎的 IoT Edge 模組。 如果您已在先前教學課程中完成這一節，則可放心地略過本節。
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
5. 在 VS Code 檔案總管中，按一下 **Program.cs** 來將其開啟。 在 **program.cs** 頂端納入下列命名空間。
   ```csharp
   using Microsoft.Azure.Devices.Shared;
   using System.Collections.Generic;  
   using Newtonsoft.Json;
   ```

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

    ![建置模組](./media/how-to-vscode-develop-csharp-module/build-module.png)

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>建立 Docker 映像並發行到您的登錄中

1. 在 VS Code 檔案總管中，展開 [Docker] 資料夾。 然後展開適用於您容器平台的資料夾：**linux-x64** 或 **windows-nano**。
2. 以滑鼠右鍵按一下 [Dockerfile] 檔案，然後按一下 [建置 IoT Edge 模組的 Docker 映像]。 

    ![建置 Docker 映像](./media/how-to-vscode-develop-csharp-module/build-docker-image.png)

3. 在 [選取資料夾] 視窗中，瀏覽至或是輸入 `./bin/Debug/netcoreapp2.0/publish`。 按一下 [選取資料夾為 EXE_DIR]。
4. 在 VS Code 視窗頂端的快顯文字方塊中，輸入映像名稱。 例如：`<your container registry address>/filtermodule:latest`。 如果您要部署到本機登錄，它應該是 `localhost:5000/filtermodule:latest`。
5. 將映像推送到您的 Docker 存放庫。 使用 [Edge: 推送 IoT Edge 模組 Docker 映像] 命令，然後在 VS Code 視窗頂端的快顯文字方塊中輸入映像 URL。 使用您在上述步驟中使用的相同映像 URL。 檢查主控台記錄，並確定您已成功推送映像。

    ![推送 Docker 映像](./media/how-to-vscode-develop-csharp-module/push-image.png) ![已推送的 Docker 映像](./media/how-to-vscode-develop-csharp-module/pushed-image.png)

### <a name="deploy-your-iot-edge-modules"></a>部署 IoT Edge 模組

1. 開啟 `deployment.json` 檔案，使用下列內容取代 **modules** 區段：
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

2. 使用下列內容取代 **routes** 區段：
    ```json
    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
    ```
   > [!NOTE]
   > 執行階段中的宣告式規則會定義這些訊息的流向。 在本教學課程中，您需要兩個路由。 第一個路由會透過使用 FilterMessages 處理常式設定的 "input1" 端點，將訊息從溫度感應器傳輸至篩選模組。 第二個路由會將訊息從篩選模組傳輸到 IoT 中樞。 在此路由中，upstream 是告知 Edge 中樞，將訊息傳送至 IoT 中樞的特殊目的地。

3. 儲存這個檔案。
4. 在命令選擇區中，選取 [Edge: 建立 Edge 裝置的部署]。 然後，選取 IoT Edge 裝置識別碼以建立部署。 或者，以滑鼠右鍵按一下裝置清單中的裝置識別碼，然後選取 [建立 Edge 裝置的部署]。

    ![建立部署](./media/how-to-vscode-develop-csharp-module/create-deployment.png)

5. 選取您更新的 `deployment.json`。 在輸出視窗中，您可以看到部署的對應輸出。

    ![部署成功](./media/how-to-vscode-develop-csharp-module/deployment-succeeded.png)

6. 在命令選擇區中啟動 Edge 執行階段。 **Edge: 啟動 Edge**
7. 您可以在 Docker 總管中看到 IoT Edge 執行階段開始執行，其中具有模擬感應器和篩選模組。

    ![IoT Edge 解決方案正在執行](./media/how-to-vscode-develop-csharp-module/solution-running.png)

8. 以滑鼠右鍵按一下您的 Edge 裝置識別碼，就能在 VS Code 中監視 D2C 訊息。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您建立了 IoT Edge 模組，並將它部署到 VS Code 中的 IoT Edge 裝置。 您可以繼續閱讀下列任一個教學課程，以了解在 VS Code 中開發 Azure IoT Edge 時的其他案例。

> [!div class="nextstepaction"]
> [在 VS Code 中進行 C# 模組偵錯](how-to-vscode-debug-csharp-module.md)
