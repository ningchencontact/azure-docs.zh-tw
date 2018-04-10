---
title: 使用 Azure IoT Edge部署 Azure Function | Microsoft Docs
description: 將 Azure Function 作為模組部署至 Edge 裝置
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3d7dd0986878c747f92afc712301453bc8772ef2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>將 Azure Function 部署為 IoT Edge 模組 - 預覽
您可以使用 Azure Functions 來部署程式碼，將您的商務邏輯直接實作到您的 IoT Edge 裝置。 本教學課程會逐步引導您建立及部署 Azure Function，以篩選模擬 IoT Edge 裝置上的感應器資料 (模擬裝置是在＜在 [Windows][lnk-tutorial1-win] 或 [Linux][lnk-tutorial1-lin] 上的模擬裝置中部署 Azure IoT Edge＞教學課程中所建立)。 在本教學課程中，您了解如何：     

> [!div class="checklist"]
> * 使用 Visual Studio Code 來建立 Azure Function
> * 使用 VS Code 和 Docker 建立 Docker 映像，並將其發行至您的登錄中 
> * 將模組部署到您的 IoT Edge 裝置
> * 檢視產生的資料


您在本教學課程中建立的 Azure Function 會篩選由您裝置產生的溫度資料，而且只會在溫度高於指定臨界值時，將訊息上游傳送到 Azure IoT 中樞。 

## <a name="prerequisites"></a>先決條件

* 您在快速入門或上一個教學課程中所建立的 Azure IoT Edge 裝置。
* [Visual Studio Code](https://code.visualstudio.com/)。 
* [C# for Visual Studio Code (採用 OmniSharp 技術) 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。 
* [Visual Studio Code 的 Azure IoT Edge 擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
* [Docker](https://docs.docker.com/engine/installation/)。 在本教學課程中，使用 Community Edition (CE) 作為您的平台是可行的。 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 

## <a name="create-a-container-registry"></a>建立容器登錄庫
在本教學課程中，您會使用適用於 VS Code 的 Azure IoT Edge 擴充功能來建置模組，並從檔案建立**容器映像**。 接著，您會將此映像推送至儲存並管理映像的**登錄**。 最後，您會從登錄部署該映像，以在 IoT Edge 裝置上執行。  

您可以此教學課程中使用任何與 Docker 相容的登錄。 於雲端中提供使用的兩個熱門 Docker 登錄服務為 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 和 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) \(英文\)。 本教學課程使用的是 Azure Container Registry。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源] > [容器] > [Azure Container Registry]。
2. 為登錄提供名稱，選擇訂用帳戶，選擇資源群組，然後將 SKU 設定為 [基本]。 
3. 選取 [建立] 。
4. 建立容器登錄之後，請瀏覽至它並選取 [存取金鑰]。 
5. 將 [管理使用者] 切換為 [啟用]。
6. 複製 [登入伺服器]、[使用者名稱] 及 [密碼] 的值。 您在本教學課程後續的內容中將會用到這些值。 

## <a name="create-a-function-project"></a>建立函式專案
下列步驟會示範如何使用 Visual Studio Code 和 Azure IoT Edge 擴充功能來建立 IoT Edge 函式。
1. 開啟 Visual Studio Code。
2. 開啟 VS Code 整合式終端機，選取 [檢視] > [整合式終端機]。
3. 若要在 dotnet 中安裝 (或更新) **AzureIoTEdgeFunction** 範本，請在整合式終端機中執行下列命令：

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. 針對新模組建立專案。 下列命令會使用您的容器存放庫來建立專案資料夾 **FilterFunction**。 如果您使用 Azure 容器登錄，第二個參數格式應該是 `<your container registry name>.azurecr.io`。 在目前的工作資料夾中輸入下列命令：

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction -r <your container registry address>/filterfunction
    ```

3. 選取 [檔案] > [開啟資料夾]，然後瀏覽至 **FilterFunction** 資料夾，並在 VS Code 中開啟專案。
4. 在 VS Code 總管中，展開 **EdgeHubTrigger-Csharp** 資料夾，然後開啟 **run.csx** 檔案。
5. 使用下列程式碼取代檔案的內容：

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

11. 儲存檔案。

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>建立 Docker 映像並發行到您的登錄中

1. 透過在 VS Code 整合式終端機中輸入下列命令來登入 Docker： 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   若要尋找使用者名稱、密碼及登入伺服器，以在此命令中使用，請移至 [Azure 入口網站] (https://portal.azure.com)。 從 [所有資源] 中，按一下 Azure 容器登錄圖格以開啟其內容，然後按一下 [存取金鑰]。 複製 [使用者名稱]、[密碼] 和 [登入伺服器] 欄位中的值。 

2. 在 VS Code 總管中，以滑鼠右鍵按一下 **module.json** 檔案並按一下 [建置及推送 IoT Edge 模組 Docker 映像] \(Build and Push IoT Edge module Docker image\)。 在於 VS Code 視窗頂端彈出的下拉式清單方塊中，選取您的容器平台，即適用於 Linux 容器的 **amd64**，或適用於 Windows 容器的 **windows-amd64**。 VS Code 會接著將您的函式程式碼容器化，再將它推送到您指定的容器登錄。


3. 您可以在 VS Code 整合式終端機中取得完整容器映像位址。 如需有關建置及推送定義的詳細資訊，請參閱 `module.json` 檔案。

## <a name="add-registry-credentials-to-your-edge-device"></a>將登錄認證新增至 Edge 裝置
在執行 Edge 裝置的電腦上，將登錄的認證新增至 Edge 執行階段。 這會提供存取權給執行階段，以提取容器。 

- 針對 Windows，請執行下列命令：
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- 針對 Linux，請執行下列命令：
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>執行解決方案

1. 在 **Azure 入口網站**中，瀏覽至您的 IoT 中樞。
2. 移至 [IoT Edge (預覽)] 並選取您的 IoT Edge 裝置。
1. 選取 [設定模組]。 
2. 如果您已將 **tempSensor** 模組部署到此裝置，則系統可能會自動將其填入。 如果不是這樣，請遵循下列步驟來加以新增：
    1. 選取 [新增 IoT Edge 模組]。
    2. 在 [名稱] 欄位中，輸入 `tempSensor`。
    3. 在 [映像 URI] 欄位中，輸入 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`。
    4. 其他設定保留不變，然後按一下 [儲存]。
1. 新增 **filterFunction** 模組。
    1. 再次選取 [新增 IoT Edge 模組]。
    2. 在 [名稱] 欄位中，輸入 `filterFunction`。
    3. 在 [映像 URI] 欄位中，輸入您的映像位址，例如 `<your container registry address>/filterfunction:0.0.1-amd64`。 您可以在上一節找到完整的映像位址。
    74. 按一下 [檔案] 。
2. 按 [下一步] 。
3. 在 [指定路由] 步驟中，將下列 JSON 複製到文字方塊。 第一個路由會透過 "input1" 端點，將訊息從溫度感應器傳輸到篩選模組。 第二個路由會將訊息從篩選模組傳輸到 IoT 中樞。 在此路由中，`$upstream` 是告知 Edge 中樞將訊息傳送至 IoT 中樞的特殊目的地。 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterFunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterFunction/outputs/* INTO $upstream"
       }
    }
    ```

4. 按 [下一步] 。
5. 在 [檢閱範本] 步驟中，按一下 [提交]。 
6. 返回 IoT Edge 裝置的詳細資料頁面，按一下 [重新整理]。 您應該會看到新的 **filterfunction** 正在與 **tempSensor** 模組和 **IoT Edge 執行階段**一起執行。 

## <a name="view-generated-data"></a>檢視產生的資料

監視從 IoT Edge 裝置傳送到 IoT 中樞的「裝置到雲端」訊息：
1. 使用 IoT 中樞連接字串來設定 Azure IoT Toolkit 擴充功能： 
    1. 在 Azure 入口網站中，瀏覽至您的 IoT 中樞，並選取 [共用存取原則]。 
    2. 選取 [iothubowner]，然後複製 [連接字串-主索引鍵] 的值。
    3. 在 VS Code 檔案總管中，按一下 [IOT HUB DEVICES]，然後按一下 [...]。 
    4. 選取 [設定 IoT 中樞連接字串]，並在快顯視窗中輸入 IoT 中樞連接字串。 

2. 若要監視抵達 IoT 中樞的資料，請選取 [檢視] > [命令選擇區...]，然後搜尋 [IoT: 開始監視 D2C 訊息]。 
3. 若要停止監視資料，請在命令選擇區中使用 **IoT: Stop monitoring D2C message** 命令。 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立包含程式碼的 Azure Function，可篩選您 IoT Edge 裝置所產生的原始資料。 若要保持瀏覽 Azure IoT Edge，請了解如何使用 IoT Edge 裝置作為閘道。 

> [!div class="nextstepaction"]
> [建立 IoT Edge 閘道裝置](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
