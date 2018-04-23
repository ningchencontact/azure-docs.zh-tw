---
title: 使用 Visual Studio Code 以 Azure IoT Edge 開發 C# 模組 | Microsoft Docs
description: 在 Visual Studio Code 中使用 Azure IoT Edge 來開發及部署 C# 模組，而不必進行環境切換。
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 48c6cacebdeb7505c8dc2bcaed099c33862589ac
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2018
---
# <a name="use-visual-studio-code-to-develop-a-c-module-with-azure-iot-edge"></a>使用 Visual Studio Code 以 Azure IoT Edge 開發 C# 模組
本文提供詳細指示，說明如何使用 [Visual Studio Code](https://code.visualstudio.com/) 作為主要的開發工具來開發和部署 Azure IoT Edge 模組。 

## <a name="prerequisites"></a>先決條件
本文假設您使用執行 Windows 或 Linux 的電腦或虛擬機器作為開發電腦。 您的 IoT Edge 裝置可以是其他實體裝置，或是您也可以在開發機器上模擬 IoT Edge 裝置。

開始使用此指導方針之前，請先完成下列教學課程：
- 在 [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) 或 [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux) 中的模擬裝置上部署 Azure IoT Edge
- [開發 C# IoT Edge 模組並部署到模擬裝置](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)

以下檢查清單顯示您在完成上述教學課程之後應具備的項目：

- [Visual Studio Code](https://code.visualstudio.com/) 
- [適用於 Visual Studio Code 的 Azure IoT Edge 延伸模組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) \(英文\) 
- [C# for Visual Studio Code (由 OmniSharp 提供技術支援) 延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) \(英文\) 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) \(英文\) 
- [Python 2.7](https://www.python.org/downloads/)
- [IoT Edge 控制指令碼](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- AzureIoTEdgeModule 範本 (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- 至少具有一部 IoT Edge 裝置的作用中 IoT 中樞

此外，安裝[適用於 VS Code 的 Docker 支援](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) \(英文\) 也有幫助，可更有效管理模組映像和容器。

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>在 VS Code 中部署 Azure IoT Edge 模組

### <a name="list-your-iot-hub-devices"></a>列出 IoT 中樞裝置
有兩種方法可以在 VS Code 中列出 IoT 中樞裝置。 您可以選擇任一個方法來繼續。

#### <a name="sign-in-to-your-azure-account-in-vs-code-and-choose-your-iot-hub"></a>在 VS Code 中登入您的 Azure 帳戶，然後選擇您的 IoT 中樞
1. 在命令選擇區 (F1 或 Ctrl + Shift + P) 中，輸入並選取 [Azure: 登入]。 然後選取 [複製並開啟]。 在瀏覽器中貼上 (Ctrl + V) 程式碼，然後選取 [繼續]。 然後使用您的 Azure 帳戶登入。 您可以在 VS Code 狀態列中看到帳戶資訊。
2. 在命令選擇區中，輸入並選取 [IoT: 選取 IoT 中樞]。 首先，選取您在上一個教學課程中用來建立 IoT 中樞的訂用帳戶。 接著，選擇包含 IoT Edge 裝置的 IoT 中樞。

    ![裝置清單的螢幕擷取畫面](./media/how-to-vscode-develop-csharp-module/device-list.png)

#### <a name="set-the-iot-hub-connection-string"></a>設定 IoT 中樞連接字串
在命令選擇區中，輸入並選取 [IoT: 設定 IoT 中樞連接字串]。 請務必將連接字串貼在 [iothubowner] 原則底下 (您可以在 Azure 入口網站中 IoT 中樞的共用存取原則中找到該原則)。
 
您可以在左側邊欄的「IoT 中樞 Device Explorer」中看到裝置清單。

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>啟動 IoT Edge 執行階段並部署模組
安裝並啟動您裝置上的 Azure IoT Edge 執行階段。 部署會將遙測資料傳送給「Azure IoT 中樞」的模擬感應器模組。
1. 在命令選擇區中，選取 [Edge: 設定 Edge]，然後選擇 IoT Edge 裝置識別碼。 或者，您也可以在**裝置清單**中的 IoT Edge 裝置識別碼上按一下滑鼠右鍵，然後選取 [設定 Edge]。

    ![設定 Edge 執行階段的螢幕擷取畫面](./media/how-to-vscode-develop-csharp-module/setup-edge.png)

2. 在命令選擇區中，選取 [Edge: 啟動 Edge] 以啟動 IoT Edge 執行階段。 您可以在整合式終端機中看到對應的輸出。

    ![啟動 Edge 執行階段的螢幕擷取畫面](./media/how-to-vscode-develop-csharp-module/start-edge.png)

3. 在「Docker 總管」中檢查 IoT Edge 執行階段狀態。 綠色表示正在執行，且您的 IoT Edge 執行階段已成功啟動。 您的電腦現在會模擬 IoT Edge 裝置。

    ![Edge 執行階段狀態的螢幕擷取畫面](./media/how-to-vscode-develop-csharp-module/edge-runtime.png)

4. 模擬會持續將訊息傳送給 IoT Edge 裝置的感應器。 在命令選擇區中，輸入並選取 [Edge: 產生 Edge 組態檔]。 選取一個資料夾以建立此檔案。 在產生的 deployment.json 檔案中，將 `<registry>/<image>:<tag>` 內容取代為 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`，然後儲存檔案。

    ![感應器模組的螢幕擷取畫面](./media/how-to-vscode-develop-csharp-module/sensor-module.png)

5. 選取 [Edge: 建立 Edge 裝置的部署]，然後選擇 IoT Edge 裝置識別碼以建立新的部署。 或者，您也可以在裝置清單中的 IoT Edge 裝置識別碼上按一下滑鼠右鍵，然後選取 [建立 Edge 裝置的部署]。 

6. 您應該會在「Docker 總管」中看到 IoT Edge 開始執行，其中具有模擬感應器。 在「Docker 總管」中的容器上按一下滑鼠右鍵。 您可以檢視每個模組的 Docker 記錄。 此外，您也可以在裝置清單中檢視模組清單。

    ![模組清單的螢幕擷取畫面](./media/how-to-vscode-develop-csharp-module/module-list.png)

7. 在您 IoT Edge 裝置識別碼上按一下滑鼠右鍵，即可在 VS Code 中監視 D2C 訊息。
8. 若要停止 IoT Edge 執行階段和感應器模組，請在命令選擇區中輸入並選取 [Edge: 停止 Edge]。

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>在 VS Code 中開發和部署 C# 模組
在[開發 C# 模組](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)教學課程中，您會在 VS Code 中更新、建置及發佈您的模組映像。 接著，您會移至 Azure 入口網站來部署您的 C# 模組。 本節將介紹如何使用 VS Code 來部署及監視 C# 模組。

### <a name="start-a-local-docker-registry"></a>啟動本機 Docker 登錄
您可以使用本文中任何與 Docker 相容的登錄。 於雲端中提供使用的兩個熱門 Docker 登錄服務為 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 和 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) \(英文\)。 本節使用[本機 Docker 登錄](https://docs.docker.com/registry/deploying/)，它可讓您更輕鬆地在早期開發期間進行測試。
在 VS Code **整合式終端機** (Ctrl + `) 中，執行下列命令來啟動本機登錄：  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> 此範例顯示的登錄設定只適用於測試。 已準備好用於生產環境的登錄必須受到 TLS 保護，且最好應使用存取控制機制。 我們建議您使用 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 或 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) (英文)，來部署已準備好用於生產環境的 IoT Edge 模組。

### <a name="create-an-iot-edge-module-project"></a>建立 IoT Edge 模組專案
下列步驟會示範如何使用 Visual Studio Code 和 Azure IoT Edge 延伸模組，根據 .NET Core 2.0 來建立 IoT Edge 模組。 如果您已在先前教學課程中完成這一節，則可放心地略過本節。
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
4. 瀏覽至 [FilterModule] 資料夾，然後按一下 [選取資料夾] 以在 VS Code 中開啟專案。
5. 在「VS Code 總管」中，選取 [Program.cs] 以開啟它。 在 **program.cs** 頂端，納入下列命名空間：
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

8. 在 **Init** 方法中，該程式碼會建立並設定 **DeviceClient** 物件。 此物件可讓模組連線至本機 IoT Edge 執行階段來傳送和接收訊息。 IoT Edge 執行階段會將 **Init** 方法中使用的連接字串提供給模組。 在建立 **DeviceClient** 物件之後，程式碼會註冊回呼以透過 **input1** 端點接收來自 IoT Edge 中樞的訊息。 將 `SetInputMessageHandlerAsync` 方法取代為新的版本，然後針對所需的屬性更新新增 `SetDesiredPropertyUpdateCallbackAsync` 方法。 若要進行這項變更，請使用下列程式碼取代 **Init** 方法的最後一行：

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

10. 使用 `FilterMessages` 方法來取代 `PipeMessage` 方法。 每當模組從「IoT Edge 中樞」收到訊息時，都會呼叫此方法。 它會篩選所報告溫度低於 (透過模組對應項所設定) 之溫度閾值的訊息。 針對具有設定為 [警示] 之值的訊息，此方法也會將 **MessageType** 屬性新增至該訊息。 

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

11. 若要建置專案，請在「總管」中的 [FilterModule.csproj] 檔案上按一下滑鼠右鍵，然後選取 [建置 IoT Edge 模組]。 此程序會編譯模組，然後將二進位檔及其相依性匯出至用來建立 Docker 映像的資料夾中。 

    ![VS Code 總管的螢幕擷取畫面](./media/how-to-vscode-develop-csharp-module/build-module.png)

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>建立 Docker 映像並發行到您的登錄中

1. 在「VS Code 總管」中，展開 [Docker] 資料夾。 然後展開適用於您容器平台的資料夾：**linux-x64** 或 **windows-nano**。
2. 在 [Dockerfile] 檔案上按一下滑鼠右鍵，然後選取 [建置 IoT Edge 模組 Docker 映像]。 

    ![VS Code 總管的螢幕擷取畫面](./media/how-to-vscode-develop-csharp-module/build-docker-image.png)

3. 在 [選取資料夾] 視窗中，瀏覽至或是輸入 `./bin/Debug/netcoreapp2.0/publish`。 選取 [選取資料夾為 EXE_DIR]。
4. 在 VS Code 視窗頂端的快顯文字方塊中，輸入映像名稱。 例如：`<your container registry address>/filtermodule:latest`。 如果您要部署到本機登錄，它應該是 `localhost:5000/filtermodule:latest`。
5. 將映像推送到您的 Docker 存放庫。 使用 [Edge: 推送 IoT Edge 模組 Docker 映像] 命令，然後在 VS Code 視窗頂端的快顯文字方塊中輸入映像 URL。 使用您在上述步驟中使用的相同映像 URL。 查看主控台記錄，以確定已成功推送映像。

    ![推送 Docker 映像的螢幕擷取畫面](./media/how-to-vscode-develop-csharp-module/push-image.png) ![主控台記錄的螢幕擷取畫面](./media/how-to-vscode-develop-csharp-module/pushed-image.png)

### <a name="deploy-your-iot-edge-modules"></a>部署 IoT Edge 模組

1. 開啟 `deployment.json` 檔案，並以下列程式碼取代 **modules** 區段：
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

2. 以下列程式碼取代 **routes** 區段：
    ```json
    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
    ```
   > [!NOTE]
   > 執行階段中的宣告式規則會定義這些訊息的流向。 在本文中，您需要兩個路由。 第一個路由會透過 "input1" 端點，將訊息從溫度感應器傳輸到篩選模組。 這是您搭配 FilterMessages 處理常式設定的端點。 第二個路由會將訊息從篩選模組傳輸到 IoT 中樞。 在此路由中，upstream 是一個特殊目的地，用來告知「IoT Edge 中樞」將訊息傳送給「IoT 中樞」。

3. 儲存這個檔案。
4. 在命令選擇區中，選取 [Edge: 建立 Edge 裝置的部署]。 然後，選取 IoT Edge 裝置識別碼以建立部署。 或者，在裝置清單中的裝置識別碼上按一下滑鼠右鍵，然後選取 [建立 Edge 裝置的部署]。

    ![建立部署選項的螢幕擷取畫面](./media/how-to-vscode-develop-csharp-module/create-deployment.png)

5. 選取您更新的 `deployment.json`。 在輸出視窗中，您可以看到部署的對應輸出。

    ![輸出視窗的螢幕擷取畫面](./media/how-to-vscode-develop-csharp-module/deployment-succeeded.png)

6. 在命令選擇區中啟動 IoT Edge 執行階段 (選取 [Edge: 啟動 Edge])。
7. 您可以在「Docker 總管」中看到 IoT Edge 執行階段開始執行，其中具有模擬感應器和篩選模組。

    ![Docker 總管的螢幕擷取畫面](./media/how-to-vscode-develop-csharp-module/solution-running.png)

8. 在您 IoT Edge 裝置識別碼上按一下滑鼠右鍵，即可在 VS Code 中監視 D2C 訊息。


## <a name="next-steps"></a>後續步驟

[在 VS Code 中進行 C# 模組偵錯](how-to-vscode-debug-csharp-module.md)
