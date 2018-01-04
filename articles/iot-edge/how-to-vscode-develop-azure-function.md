---
title: "使用 Visual Studio 程式碼來開發和部署 Azure 函式 Azure IoT 邊緣 |Microsoft 文件"
description: "開發和部署 Azure IoT 邊緣 VS 程式碼中使用 C# Azure 函數沒有內容切換"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 9637986d10a0e89568b2f79ede3d7b7468bb99a7
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2017
---
# <a name="use-visual-studio-code-to-develop-and-deploy-azure-functions-to-azure-iot-edge"></a>使用 Visual Studio 程式碼來開發並部署至 Azure IoT 邊緣 Azure 函式

本文章提供詳細的指示使用[Visual Studio Code](https://code.visualstudio.com/)與主要開發工具來開發和部署 Azure IoT 邊緣上的函式。 

## <a name="prerequisites"></a>必要條件
本教學課程假設您使用的電腦或您的開發電腦以執行 Windows 或 Linux 的虛擬機器。 您的 IoT 邊緣裝置可能是另一個實體裝置，或您可以在開發電腦上模擬您的 IoT 邊緣裝置。

請確定您有已完成下列教學課程，才能啟動這項指引。
- 在模擬的裝置上部署 Azure IoT 邊緣[Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows)或[Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [部署 Azure Functions](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function)

以下是顯示的項目，您應該在您完成前述的教學課程後的檢查清單。

- [Visual Studio Code](https://code.visualstudio.com/)。 
- [Visual Studio Code 的 Azure IoT Edge 擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
- [C# for Visual Studio Code (採用 OmniSharp 技術) 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 
- [Python 2.7](https://www.python.org/downloads/)
- [IoT 邊緣控制指令碼](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- AzureIoTEdgeFunction 範本 (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
- 至少在 IoT 邊緣裝置與 active IoT 中樞。

它也會建議安裝[VS Code 的 Docker 支援](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)若要加強管理容器和模組映像。

> [!NOTE]
> 目前，Azure IoT 邊緣上的函式只支援 C#。

## <a name="deploy-azure-iot-functions-in-vs-code"></a>部署 Azure IoT VS 程式碼中的函式
在教學課程[部署 Azure 函式](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function)、 更新、 建置和發佈 VS 程式碼中的函式的模組映像，然後瀏覽 Azure 入口網站中部署 Azure 函式。 本節將介紹如何使用 VS 程式碼來部署及監視您的 Azure 函式。

### <a name="start-a-local-docker-registry"></a>啟動本機 docker 登錄
您可以此教學課程中使用任何與 Docker 相容的登錄。 於雲端中提供使用的兩個熱門 Docker 登錄服務為 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 和 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) \(英文\)。 本節使用[本機 Docker 登錄](https://docs.docker.com/registry/deploying/)，也就是更容易在您的早期開發時測試用途。
在 VS Code**整合的終端機**(Ctrl + ') 中，執行下列命令來啟動本機登錄。  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> 上述範例示範只適用於測試的登錄設定。 備妥登錄必須受 TLS 保護，而且應該在理想情況下使用存取控制機制。 我們建議您改用[Azure 容器登錄中](https://docs.microsoft.com/azure/container-registry/)或[Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)部署備妥 IoT 邊緣模組。

### <a name="create-a-function-project"></a>建立函式專案
下列步驟會示範如何 使用 Visual Studio Code 和 Azure IoT Edge 擴充功能，來建立以 .NET core 2.0 為基礎的 IoT Edge 模組。 如果您已完成本節先前的教學課程中，您可以放心地略過本節。

1. 在 Visual Studio Code 中，選取 [檢視] > [整合式終端機] 以開啟 VS Code 整合式終端機。
2. 若要安裝 （或更新） **AzureIoTEdgeFunction** dotnet，執行下列命令，在整合式終端機中的範本：

   ```cmd/sh
   dotnet new -i Microsoft.Azure.IoT.Edge.Function
   ```
3. 針對新模組建立專案。 下列命令會建立專案資料夾中， **FilterFunction**，目前的工作資料夾中：

   ```cmd/sh
   dotnet new aziotedgefunction -n FilterFunction
   ```
 
4. 選取**檔案 > 開啟資料夾**，然後瀏覽至**FilterFunction**資料夾，然後開啟 VS 程式碼中的專案。
5. 瀏覽至**FilterFunction**資料夾，然後按一下**選取資料夾**VS 程式碼中開啟專案。
6. 在 VS 程式碼總管 中，依序展開**EdgeHubTrigger Csharp**資料夾，然後開啟**run.csx**檔案。
7. 取代下列程式碼檔案的內容：

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
3. 瀏覽至**FilterFunction**專案資料夾，然後按一下**選取資料夾 EXE_DIR**。 
4. 在 VS Code 視窗頂端的快顯文字方塊中，輸入映像名稱。 例如：`<your container registry address>/filterfunction:latest`。 如果您要部署到本機登錄中，它應該是`localhost:5000/filterfunction:latest`。
5. 將映像推送到您的 Docker 存放庫。 使用**邊緣： Push IoT 邊緣模組 Docker 映像**命令，並在 VS 程式碼視窗頂端快顯的文字方塊中輸入影像 URL。 使用相同的影像 URL 中使用上述步驟。

### <a name="deploy-your-function-to-iot-edge"></a>IoT 邊緣部署您的函式

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

2. 取代**路由**區段中具有下列內容：
   ```json
   {
       "routes":{
           "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
           "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
   }
   ```
   > [!NOTE]
   > 執行階段中的宣告式規則會定義這些訊息的流向。 在本教學課程中，您需要兩個路由。 第一個路由傳輸溫度感應器透過 「 input1 」 端點，filter 函數的訊息，這是您設定與 FilterMessages 處理常式的端點。 第二個路由傳送訊息到 IoT 中樞篩選函數。 在此路由中上游是特殊的目的地，告知邊緣集線器，以將訊息傳送到 IoT 中樞。

3. 儲存這個檔案。
4. 命令調色盤中選取**邊緣： 建立部署邊緣裝置**。 然後，選取您的 IoT 邊緣裝置識別碼，以建立部署。 或以滑鼠右鍵按一下 [裝置] 清單中的裝置識別碼，並選取**建立邊緣裝置部署**。
5. 選取`deployment.json`您更新。 在 [輸出] 視窗中，您可以看到對應的輸出，為您的部署。
6. 開始邊緣 runtime 命令調色盤中。 **邊緣： 開始邊緣**
7. 您可以看到您的 IoT 邊緣執行階段以 Docker 總管 中，以模擬的感應器和篩選函數開始執行。
8. 以滑鼠右鍵按一下您的邊緣裝置識別碼，您可以監視 D2C VS 程式碼中的訊息。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您建立 IoT 邊緣 Azure 函式並將它部署到 IoT 邊緣裝置 VS 程式碼中。 您可以繼續閱讀下列教學課程來了解其他案例時開發 VS 程式碼中的 Azure IoT 邊緣之一。

> [!div class="nextstepaction"]
> [偵錯 VS 程式碼中的 Azure 函式](how-to-vscode-debug-azure-function.md)
