---
title: 使用 Azure IoT Edge部署 Azure Machine Learning | Microsoft Docs
description: 將 Azure Machine Learning 作為模組部署至邊緣裝置
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/12/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4201395085dd72eb92b774eaed5980737b2e5de0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>將 Azure Machine Learning 部署為 IoT Edge 模組 - 預覽

您可以使用 IoT Edge 模組來部署程式碼，將您的商務邏輯直接實作到您的 IoT Edge 裝置。 本教學課程會逐步引導您部署 Azure Machine Learning 模組，以根據模擬 IoT Edge 裝置上的感應器資料預測裝置失效的時間 (模擬裝置是在＜在 [Windows][lnk-tutorial1-win] 或 [Linux][lnk-tutorial1-lin] 上的模擬裝置中部署 Azure IoT Edge＞教學課程中所建立的)。 

在本教學課程中，您了解如何： 

> [!div class="checklist"]
> * 建立 Azure Machine Learning 模組
> * 將模組容器推送至 Azure Container Registry
> * 將 Azure Machine Learning 模組部署至 IoT Edge 裝置
> * 檢視產生的資料

您在本教學課程中建立的 Azure Machine Learning 模組會讀取裝置所產生的環境資料，並將訊息標示為異常或非異常。 

## <a name="prerequisites"></a>先決條件

* 您在快速入門或第一份教學課程中所建立的 Azure IoT Edge 裝置。
* IoT 中樞 (連接您的 IoT Edge 裝置) 的 IoT 中樞連接字串。
* Azure Machine Learning 帳戶。 若要建立帳戶，請遵循[建立 Azure Machine Learning 帳戶，並安裝 Azure Machine Learning Workbench](../machine-learning/preview/quickstart-installation.md#create-azure-machine-learning-services-accounts) 中的指示。 您不需要針對此教學課程安裝 Workbench 應用程式。 
* 在您的電腦上的 Azure ML 模組管理。 若要設定您的環境並建立帳戶，請遵循[模型管理設定](https://docs.microsoft.com/azure/machine-learning/preview/deployment-setup-configuration)中的指示。

## <a name="create-the-azure-ml-container"></a>建立 Azure ML 容器
在本節中，您會下載定型模型檔案，並將它們轉換成 Azure ML 容器。  

在執行 Azure ML 模組管理的電腦上，從 GitHub 上的 Azure ML IoT Toolkit 下載並儲存 [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) 和 [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl)。 這些檔案會定義您將會部署到 IoT Edge 裝置的定型機器學習模型。 

使用定型模型以建立可以部署到 IoT Edge 裝置的容器。 使用下列命令，您可以：

   * 註冊您的模組。
   * 建立資訊清單。
   * 建立名為 machinelearningmodule 的 Docker 容器映像。
   * 將映像部署至您的 Azure Container Service (AKS) 叢集。

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```

### <a name="view-the-container-repository"></a>檢視容器存放庫

檢查您的容器映像是否已成功建立，並且儲存在與您的機器學習環境相關聯的 Azure 容器存放庫。

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至 [所有服務]，然後選取 [容器登錄]。
2. 選取您的登錄。 名稱應該以 **mlcr** 開頭，並且屬於您用來設定模組管理的資源群組、位置及訂用帳戶。
3. 選取 [存取金鑰]
4. 複製 [登入伺服器]、[使用者名稱] 及 [密碼]。  您需要這些值以便從 Edge 裝置存取登錄。
5. 選取 [存放庫]
6. 選取 [machinelearningmodule]
7. 現在您有容器的完整映像路徑。 記下此映像路徑以在下一節中使用。 看起來如下所示：**<registry_name>.azureacr.io/machinelearningmodule:1**

## <a name="add-registry-credentials-to-your-edge-device"></a>將登錄認證新增至 Edge 裝置

在執行 Edge 裝置的電腦上，將登錄的認證新增至 Edge 執行階段。 此命令會提供存取權給執行階段，以提取容器。

Linux：
   ```cmd
   sudo iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

Windows:
   ```cmd
   iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

## <a name="run-the-solution"></a>執行解決方案

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 IoT 中樞。
1. 移至 [IoT Edge (預覽)] 並選取您的 IoT Edge 裝置。
1. 選取 [設定模組]。
1. 如果您先前在 IoT Edge 裝置上部署過 tempSensor 模組，可能會自動填入。 如果尚未在模組清單中，請新增它。
    1. 選取 [新增 IoT Edge 模組]。
    2. 在 [名稱] 欄位中，輸入 `tempSensor`。
    3. 在 [映像 URI] 欄位中，輸入 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`。
    4. 選取 [ **儲存**]。
1. 新增您建立的機器學習模組。
    1. 選取 [新增 IoT Edge 模組]。
    1. 在 [名稱] 欄位中，輸入 `machinelearningmodule`
    1. 在 [映像] 欄位中，輸入您的映像地址，例如`<registry_name>.azurecr.io/machinelearningmodule:1`。
    1. 選取 [ **儲存**]。
1. 回到 [新增模組] 步驟中，選取 [下一步]。
1. 在 [指定路由] 步驟中，將下列 JSON 複製到文字方塊。 第一個路由會透過所有 Azure Machine Learning 模組使用的「amlInput」端點，將訊息從溫度感應器傳輸到機器學習模組。 第二個路由會將訊息從機器學習模組傳輸到 IoT 中樞。 在此路由中，「amlInput」是所有 Azure Machine Learning 模組用來輸出資料的端點，而「$upstream」代表 IoT 中樞。 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. 選取 [下一步] 。 
1. 在 [檢閱範本] 步驟中，選取 [提交]。 
1. 返回裝置的詳細資料頁面，選取 [重新整理]。  您應該會看到新的 **machinelearningmodule** 正在與 **tempSensor** 模組和 IoT Edge 執行階段模組一起執行。

## <a name="view-generated-data"></a>檢視產生的資料

您可以使用 [IoT 中樞總管](https://github.com/azure/iothub-explorer) \(英文\) 或適用於 Visual Studio Code 的 Azure IoT Toolkit 擴充功能，來檢視 IoT Edge 裝置傳送的裝置到雲端訊息。 

1. 在 Visual Studio Code 中，選取 [IoT 中樞裝置]。 
2. 從功能表選取 [...]，然後選取 [設定 IoT 中樞連接字串]。 

   ![IoT 中樞裝置更多功能表](./media/tutorial-deploy-machine-learning/set-connection.png)

3. 在頁面頂端開啟的文字方塊中，輸入 IoT 中樞的 iothubowner 連接字串。 您的 IoT Edge 裝置應該會出現在 IoT 中樞裝置清單中。
4. 再次選取 [...]，然後選取 [開始監視 D2C 訊息]。
5. 每五秒就會觀察來自 tempSensor 的訊息。 訊息本文包含稱為 **anomaly** 的屬性，machinelearningmodule 會搭配 True 或 False 值來提供此屬性。 如果模型已順利執行，**AzureMLResponse** 屬性即會包含 "OK" 值。 

   ![訊息主體中的 Azure ML 回應](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="next-steps"></a>後續步驟

在此教學課程中，您可以部署受到 Azure Machine Learning 支援的 IoT Edge 模組。 您可以繼續閱讀其他任何教學課程，以了解 Azure IoT Edge 有什麼其他方法可協助您將此資料轉換成具有優勢的商業見解。

> [!div class="nextstepaction"]
> [將 Azure Functions 部署為模組](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
