---
title: 使用 Azure IoT Edge 部署 Azure Function | Microsoft Docs
description: 將 Azure Function 作為模組部署至邊緣裝置。
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3f3ba0ccb1cb8961344b605e7ec386b6d6692262
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006872"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules-preview"></a>教學課程：將 Azure Functions 部署為 IoT Edge 模組 (預覽)

您可以使用 Azure Functions 來部署程式碼，直接在 Azure IoT Edge 裝置上實作您的商務邏輯。 本教學課程會逐步引導您建立並部署 Azure 函式，能在模擬的 IoT Edge 裝置上篩選感應器資料。 您將會使用模擬的 IoT Edge 裝置，其建立方法詳述於[在 Windows 中的模擬裝置上部署 Azure IoT Edge][lnk-tutorial1-win]，以及[在 Linux 中的模擬裝置上部署 Azure IoT Edge][lnk-tutorial1-lin] 這兩個快速入門中。 在本教學課程中，您了解如何：     

> [!div class="checklist"]
> * 使用 Visual Studio Code 來建立 Azure 函式。
> * 使用 VS Code 和 Docker 建立 Docker 映像，並將其發佈至您的容器登錄。
> * 從容器登錄將模組部署到您的 IoT Edge 裝置。
> * 檢視已篩選的資料。

>[!NOTE]
>Azure IoT Edge 上的 Azure Function 模組目前為[公開預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

您於此教學課程中建立的 Azure 函式，能夠篩選由您裝置所產生的溫度資料。 函式只有在溫度超過指定的閾值時，才會將訊息往上游傳送至 Azure IoT 中樞。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

若要測試您在本教學課程中建立的函式模組，您必須要有 IoT Edge 裝置。 您可以使用您在 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 快速入門中設定的裝置。

您的開發電腦上必須具備下列先決條件： 
* [Visual Studio Code](https://code.visualstudio.com/)。 
* [C# for Visual Studio Code (採用 OmniSharp 技術) 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。
* 適用於 Visual Studio Code 的 [Azure IoT Edge 擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。
* 開發電腦上的 [Docker CE](https://docs.docker.com/install/)。 

## <a name="create-a-container-registry"></a>建立容器登錄庫
在本教學課程中，您會使用適用於 VS Code 的 Azure IoT Edge 擴充功能來建置模組，並從檔案建立**容器映像**。 接著，您會將此映像推送至儲存並管理映像的**登錄**。 最後，您會從登錄部署該映像，以在 IoT Edge 裝置上執行。  

您可以此教學課程中使用任何與 Docker 相容的登錄。 雲端中提供使用的兩個熱門 Docker 登錄服務為 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 和 [Docker 中樞](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) (英文)。 本教學課程使用的是 Azure Container Registry。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源] > [容器] > [容器登錄]。

    ![建立容器登錄庫](./media/tutorial-deploy-function/create-container-registry.png)

2. 輸入您登錄的名稱，然後選擇訂用帳戶。
3. 針對資源群組，建議您使用包含 IoT 中樞的相同資源群組名稱。 您可以將所有資源放在相同群組中，而一併加以管理。 例如，當您將用於測試的資源群組刪除時，會將群組中所有的測試資源刪除。 
4. 將 SKU 設定為 [基本]，並將 [管理使用者] 切換為 [啟用]。 
5. 選取 [建立] 。
6. 建立容器登錄之後，請加以瀏覽，然後選取 [存取金鑰]。 
7. 複製 [登入伺服器]、[使用者名稱] 及 [密碼] 的值。 您在本教學課程後續的內容中，會用到這些值。 

## <a name="create-a-function-project"></a>建立函式專案
下列步驟會使用 Visual Studio Code 和 Azure IoT Edge 擴充功能來建立 IoT Edge 函式。

1. 開啟 Visual Studio Code。
2. 選取 [檢視] > [整合式終端機]，以開啟 VS Code 整合式終端機。 
2. 選取 [檢視] > [命令選擇區]，以開啟 VS Code 命令選擇區。
3. 在 [命令選擇區] 中，輸入並執行命令 **Azure: Sign in**。 依照指示登入 Azure 帳戶。 如果您已登入，則可以略過此步驟。
3. 在 [命令選擇區] 中，輸入並執行命令 **Azure IoT Edge: New IoT Edge solution**。 在命令選擇區中提供下列資訊，以建立解決方案： 

   1. 選取要用來建立解決方案的資料夾。 
   2. 為解決方案提供名稱，或是接受預設值 **EdgeSolution**。
   3. 選擇 [Azure Functions - C#] 作為模組範本。 
   4. 將模組命名為 **CSharpFunction**。 
   5. 將您在上一節所建立的 Azure 容器登錄，指定為第一個模組的映像存放庫。 將 **localhost:5000** 取代為您所複製的登入伺服器值。 最終字串看起來會類似於：\<登錄名稱\>.azurecr.io/csharpfunction。

4. VS Code 視窗會載入您的 IoT Edge 解決方案工作區：\.vscode 資料夾、模組資料夾、部署資訊清單範本檔案。 和 \.env 檔案。 在 VS Code 總管中，開啟 [模組] > [CSharpFunction] > [EdgeHubTrigger Csharp] > [run.csx]。

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
            // Get the body of the message and deserialize it.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold.
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object.
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);                }
                // Add a new property to the message to indicate it is an alert.
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message.       
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages.
    class MessageBody
    {
        public Machine machine {get; set;}
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

6. 儲存檔案。

## <a name="build-your-iot-edge-solution"></a>建置 IoT Edge 解決方案

在上一節中，您已建立 IoT Edge 解決方案，並將程式碼新增至 **CSharpFunction**，來篩選掉回報的機器溫度低於可接受閾值的訊息。 現在，您需要建置容器映像形式的解決方案，並將它推送到容器登錄。

1. 透過在 Visual Studio Code 整合式終端機中輸入下列命令，來登入 Docker。 接著，您可以將模組映像推送到您的 Azure 容器登錄： 
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    請使用您先前從 Azure 容器登錄複製而來的使用者名稱和登入伺服器。 系統會提示您輸入密碼。 將您的密碼貼到提示中，然後按 **Enter** 鍵。

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. 在 VS Code 總管中，於 IoT Edge 解決方案工作區中開啟 deployment.template.json 檔案。 此檔案會指示 IoT Edge 執行階段應將哪些模組部署至裝置。 若要深入了解部署資訊清單，請參閱[了解如何使用、設定以及重複使用 IoT Edge 模組](module-composition.md)。

3. 尋找部署資訊清單中的 **registryCredentials** 區段。 以容器登錄中的認證更新**使用者名稱**、**密碼**和**位址**。 本節將為您裝置上的 IoT Edge 執行階段提供相關權限，使其能夠提取您儲存在私人登錄中的容器映像。 實際的使用者名稱和密碼組，會儲存在 git 所忽略的 .env 檔案中。

5. 儲存這個檔案。

6. 在 VS Code 總管中，以滑鼠右鍵按一下 deployment.template.json 檔案，然後選取 [建置 IoT Edge 解決方案]。 

當您指示 Visual Studio Code 建置解決方案時，它會先擷取部署範本中的資訊，再於名為 **config** 的新資料夾中產生 deployment.json 檔案。然後，它會在整合式終端機中執行兩個命令：`docker build` 和 `docker push`。 這兩個命令會建置程式碼、將函式容器化，再將程式碼推送至您在初始化解決方案時所指定的容器登錄。 

## <a name="view-your-container-image"></a>檢視容器映像

當容器映像推送至您的容器登錄時，Visual Studio Code 會輸出成功訊息。 如果您想要確認本身作業成功，您可以檢視登錄中的映像。 

1. 在 Azure 入口網站中，瀏覽到您的 Azure 容器登錄。 
2. 選取 [存放庫]。
3. 您應該會在清單中看到 **csharpfunction** 存放庫。 選取此存放庫可查看詳細資料。
4. 在 [標記] 區段中，您應該會看到 **0.0.1-amd64** 標記。 此標記表示您所建置的映像版本和平台。 這些值設定於 CSharpFunction 資料夾內的 module.json 檔案中。 

## <a name="deploy-and-run-the-solution"></a>部署並執行解決方案

您可以使用 Azure 入口網站將函式模組部署到 IoT Edge 裝置，如同您在快速入門中所做的一樣。 您也可以從 Visual Studio Code 內部署和監視模組。 下列幾節會使用先決條件中所列的「適用於 VS Code 的 Azure IoT Edge 擴充功能」。 請立即安裝擴充功能 (如果您尚未安裝)。 

1. 選取 [檢視] > [命令選擇區]，以開啟 VS Code 命令選擇區。

2. 搜尋並執行命令 **Azure: Sign in**。 依照指示登入 Azure 帳戶。 

3. 在命令選擇區中，搜尋並執行命令 **Azure IoT Hub: Select IoT Hub**。 

4. 選取 IoT 中樞所在的訂用帳戶，然後選取您要存取的 IoT 中樞。

5. 在 VS Code 總管中，展開 [Azure IoT 中樞裝置] 區段。 

6. 以滑鼠右鍵按一下 IoT Edge 裝置的名稱，然後選取 [建立 IoT Edge 裝置的部署]。 

7. 瀏覽至 **CSharpFunction** 所在的解決方案資料夾。 開啟 config 資料夾、選取 deployment.json 檔案，然後選擇 [選取 Edge 部署資訊清單]。

8. 重新整理 [Azure IoT 中樞裝置] 區段。 您應該會看到新的 **CSharpFunction** 正在與 **TempSensor** 模組以及 **$edgeAgent** 和 **$edgeHub** 一起執行。 

   ![在 VS Code 中檢視已部署的模組](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>檢視產生的資料

您可以在命令選擇區中執行 **Azure IoT Hub: Start Monitoring D2C Message**，以檢視所有送達 IoT 中樞的訊息。

您也可以篩選檢視來查看所有從特定裝置送達 IoT 中樞的訊息。 請以滑鼠右鍵按一下 [Azure IoT 中樞裝置] 區段中的裝置，然後選取 [開始監視 D2C 訊息]。

若要停止監視訊息，請在命令選擇區中執行命令 **Azure IoT Hub: Stop monitoring D2C message**。 


## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)]

根據您的 IoT 裝置平台 (Linux 或 Windows) 移除 IoT Edge 服務執行階段。

#### <a name="windows"></a>Windows

移除 IoT Edge 執行階段。

```Powershell
stop-service iotedge -NoWait
sleep 5
sc.exe delete iotedge
```

刪除已在您的裝置上建立的容器。 

```Powershell
docker rm -f $(docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

#### <a name="linux"></a>Linux

移除 IoT Edge 執行階段。

```bash
sudo apt-get remove --purge iotedge
```

刪除已在您的裝置上建立的容器。 

```bash
sudo docker rm -f $(sudo docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

移除容器執行階段。

```bash
sudo apt-get remove --purge moby
```



## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立包含程式碼的 Azure 函式模組，可篩選您 IoT Edge 裝置所產生的原始資料。 當您準備好要建置自己的模組時，您可以進一步了解如何[使用適用於 Visual Studio Code 的 Azure IoT Edge 開發 Azure Functions](how-to-develop-csharp-function.md)。 

請繼續進行後續教學課程，以了解 Azure IoT Edge 有什麼其他方法，可協助您將此資料轉換成具有優勢的商業見解。

> [!div class="nextstepaction"]
> [使用 Azure 串流分析中的浮動視窗尋找平均值](tutorial-deploy-stream-analytics.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
