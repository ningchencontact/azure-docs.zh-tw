---
title: 使用 Visual Studio Code 開發 Azure Functions 並部署至 Azure IoT Edge | Microsoft Docs
description: 在不需切換內容的情況下，於 VS Code 中使用 Azure IoT Edge 開發和部署 C# Azure Functions
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 47d420b4b283b390f67719233c4bea59495a589a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2018
---
# <a name="use-visual-studio-code-to-develop-and-deploy-azure-functions-to-azure-iot-edge"></a>使用 Visual Studio Code 開發 Azure Functions 並部署至 Azure IoT Edge

本文提供使用 [Visual Studio Code](https://code.visualstudio.com/) \(英文\) 作為主要開發工具，在 IoT Edge 上開發和部署 Azure Functions 的詳細指示。 

## <a name="prerequisites"></a>先決條件
本文假設您使用執行 Windows 或 Linux 的電腦或虛擬機器作為開發電腦。 IoT Edge 裝置可以是其他實體裝置，您也可以在開發機器上模擬 IoT Edge 裝置。

在開始使用本指引之前，請確定您已完成下列教學課程。
- 在 [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) 或 [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux) 中的模擬裝置上部署 Azure IoT Edge
- [部署 Azure Functions](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function)

下列檢查清單能顯示您在完成前述教學課程之後應具備的項目。

- [Visual Studio Code](https://code.visualstudio.com/)。 
- [Visual Studio Code 的 Azure IoT Edge 擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
- [C# for Visual Studio Code (採用 OmniSharp 技術) 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 
- [Python 2.7](https://www.python.org/downloads/)
- [IoT Edge 控制指令碼](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl) \(英文\)
- AzureIoTEdgeFunction 範本 (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
- 至少具有一個 IoT Edge 裝置的作用中 IoT 中樞。

此外，也建議安裝[適用於 VS Code 的 Docker 支援](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) \(英文\)，以加強管理模組映像和容器。

> [!NOTE]
> IoT Edge 上的 Azure Functions 目前只支援 C#。

## <a name="deploy-azure-iot-functions-in-vs-code"></a>在 VS Code 中部署 Azure IoT Functions
在本[部署 Azure Functions](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function) 教學課程中，您要在 VS Code 中更新、建置和發佈函式模組映像，然後瀏覽 Azure 入口網站以部署 Azure Functions。 本節將介紹如何使用 VS Code 來部署及監視您的 Azure Functions。

### <a name="start-a-local-docker-registry"></a>啟動本機 Docker 登錄
您可以使用本文中任何與 Docker 相容的登錄。 於雲端中提供使用的兩個熱門 Docker 登錄服務為 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 和 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) \(英文\)。 本節使用[本機 Docker 登錄](https://docs.docker.com/registry/deploying/)，它可讓您更輕鬆地在早期開發期間進行測試。
在 VS Code **整合式終端機** (Ctrl + `) 中，執行下列命令來啟動本機登錄。  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> 上述範例顯示的登錄組態只適合用於測試。 已準備好用於生產環境的登錄必須受到 TLS 保護，且最好應使用存取控制機制。 我們建議您使用 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 或 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) (英文)，來部署已準備好用於生產環境的 IoT Edge 模組。

### <a name="create-a-function-project"></a>建立函式專案
下列步驟會示範如何 使用 Visual Studio Code 和 Azure IoT Edge 擴充功能，來建立以 .NET core 2.0 為基礎的 IoT Edge 模組。 如果您已在先前的教學課程中完成本節，則可以放心地跳過本節。

1. 在 Visual Studio Code 中，選取 [檢視] > [整合式終端機] 以開啟 VS Code 整合式終端機。
2. 若要在 dotnet 中安裝 (或更新) **AzureIoTEdgeFunction** 範本，請在整合式終端機中執行下列命令：

   ```cmd/sh
   dotnet new -i Microsoft.Azure.IoT.Edge.Function
   ```
3. 針對新模組建立專案。 下列命令會在目前的工作資料夾中建立專案資料夾 **FilterFunction**：

   ```cmd/sh
   dotnet new aziotedgefunction -n FilterFunction
   ```
 
4. 選取 [檔案] > [開啟資料夾]，然後瀏覽至 **FilterFunction** 資料夾，並在 VS Code 中開啟專案。
5. 瀏覽至 **FilterFunction** 資料夾，然後按一下 [選取資料夾] 以在 VS Code 中開啟專案。
6. 在 VS Code 總管中，展開 **EdgeHubTrigger-Csharp** 資料夾，然後開啟 **run.csx** 檔案。
7. 使用下列程式碼取代檔案的內容：

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
        const int temperatureThreshold = 25;
        byte[] messageBytes = messageReceived.GetBytes();
        var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

        if (!string.IsNullOrEmpty(messageString))
        {
            // Get the body of the message and deserialize it
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }
                // Add a new property to the message to indicate it is an alert
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message        
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages
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

8. 儲存檔案。

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>建立 Docker 映像並發行到您的登錄中

1. 在 VS Code 檔案總管中，展開 [Docker] 資料夾。 然後展開適用於您容器平台的資料夾：**linux-x64** 或 **windows-nano**。
2. 以滑鼠右鍵按一下 [Dockerfile] 檔案，然後按一下 [建置 IoT Edge 模組的 Docker 映像]。 

    ![建置 Docker 映像](./media/how-to-vscode-develop-csharp-function/build-docker-image.png)

3. 瀏覽至 **FilterFunction** 專案資料夾，然後按一下 [選取資料夾作為 EXE_DIR]。 
4. 在 VS Code 視窗頂端的快顯文字方塊中，輸入映像名稱。 例如：`<your container registry address>/filterfunction:latest`。 如果您要部署到本機登錄，它應該是 `localhost:5000/filterfunction:latest`。
5. 將映像推送到您的 Docker 存放庫。 使用 [Edge: 推送 IoT Edge 模組 Docker 映像] 命令，然後在 VS Code 視窗頂端的快顯文字方塊中輸入映像 URL。 使用您在上述步驟中使用的相同映像 URL。
    ![推送 Docker 映像](./media/how-to-vscode-develop-csharp-function/push-image.png)

### <a name="deploy-your-function-to-iot-edge"></a>將您的函式部署到 IoT Edge

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
   "filterfunction": {
      "version": "1.0",
      "type": "docker",
      "status": "running",
      "restartPolicy": "always",
      "settings": {
         "image": "localhost:5000/filterfunction:latest",
         "createOptions": ""
      }
   }
   ```

2. 使用下列內容取代 **routes** 區段：
   ```json
       "routes":{
           "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
           "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
   ```
   > [!NOTE]
   > 執行階段中的宣告式規則會定義這些訊息的流向。 在本文中，您需要兩個路由。 第一個路由會透過使用 FilterMessages 處理常式設定的 "input1" 端點，將訊息從溫度感應器傳輸到篩選函式。 第二個路由會將訊息從篩選函式傳輸到 IoT 中樞。 在此路由中，upstream 是告知 Edge 中樞將訊息傳送至 IoT 中樞的特殊目的地。

3. 儲存這個檔案。
4. 在命令選擇區中，選取 [Edge：建立 Edge 裝置的部署]。 然後，選取 IoT Edge 裝置識別碼以建立部署。 或者，以滑鼠右鍵按一下裝置清單中的裝置識別碼，然後選取 [建立 Edge 裝置的部署]。

    ![建立部署](./media/how-to-vscode-develop-csharp-function/create-deployment.png)

5. 選取您更新的 `deployment.json`。 在輸出視窗中，您可以看到部署的對應輸出。
6. 在命令選擇區中啟動您的 Edge 執行階段。 [Edge：啟動 Edge]
7. 您可以在 Docker 總管中看到您的 IoT Edge 執行階段開始執行，其中具有模擬感應器和篩選函式。

    ![執行中的解決方案](./media/how-to-vscode-develop-csharp-function/solution-running.png)

8. 以滑鼠右鍵按一下您的 Edge 裝置識別碼，就能在 VS Code 中監視 D2C 訊息。

    ![監視訊息](./media/how-to-vscode-develop-csharp-function/monitor-d2c-messages.png)


## <a name="next-steps"></a>後續步驟

[在 VS Code 中對 Azure Functions 進行偵錯](how-to-vscode-debug-azure-function.md)
