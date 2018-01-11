---
title: "使用 Azure IoT Edge部署 Azure Machine Learning | Microsoft Docs"
description: "將 Azure Machine Learning 作為模組部署至邊緣裝置"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/13/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 2afdb257421b1333f451eb9d0dd4c2af5a12e946
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2017
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>將 Azure Machine Learning 部署為 IoT Edge 模組 - 預覽

您可以使用 IoT Edge 模組來部署程式碼，將您的商務邏輯直接實作到您的 IoT Edge 裝置。 本教學課程將引導您完成部署預測時，如果裝置沒有上模擬您在中建立的 IoT 邊緣裝置感應器資料為基礎的 Azure 機器學習模組[部署 Azure IoT 邊緣上模擬的裝置上 Windows] [ lnk-tutorial1-win]或[Linux] [ lnk-tutorial1-lin]教學課程。 

在本教學課程中，您了解如何： 

> [!div class="checklist"]
> * 建立 Azure 機器學習模組
> * 推入至 Azure 容器登錄的模組容器
> * 將 Azure Machine Learning 模組部署至 IoT Edge 裝置
> * 檢視產生的資料

您在本教學課程中建立的 Azure 機器學習模組會讀取您的裝置所產生的溫度資料，並只預測 （稱為異常狀況） 失敗時，將上游訊息傳送至 Azure IoT 中樞。 


## <a name="prerequisites"></a>必要條件

* 您在快速入門或第一份教學課程中所建立的 Azure IoT Edge 裝置。
* IoT 中樞 (連接您的 IoT Edge 裝置) 的 IoT 中樞連接字串。
* Azure 機器學習服務帳戶。 若要建立的帳戶，請依照下列中的指示[建立 Azure 機器學習服務帳戶，並安裝 Azure 機器學習 Workbench](../machine-learning/preview/quickstart-installation.md#create-azure-machine-learning-accounts)。 您不需要安裝 workbench 應用程式在此教學課程。 
* 在您的電腦上的 Azure ML 模組管理。 若要設定您的環境，並建立帳戶，請依照下列中的指示[模型管理安裝程式](https://docs.microsoft.com/en-us/azure/machine-learning/preview/deployment-setup-configuration)。

## <a name="create-the-azure-ml-container"></a>建立 Azure ML 容器
在本節中，您可以下載定型的模型檔案，並將它們轉換成 Azure ML 容器。  

在執行 Azure ML 模組管理的電腦上下載並儲存[iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py)和[model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl)從 GitHub 上 Azure ML IoT Toolkit。 這些檔案定義定型的機器學習模型，您將部署到您的 Iot 邊緣裝置。 

若要建立可部署到 IoT 邊緣裝置的容器使用定型的模型。

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```
服務名稱、 *machinelearningmodule*在此範例中，會變成 docker 容器映像的名稱。

### <a name="view-the-container-repository"></a>檢視容器儲存機制

請檢查您的容器映像已成功建立並儲存在您的機器學習環境相關聯的 Azure 容器儲存機制。

1. 在[Azure 入口網站](https://portal.azure.com)，請移至**所有服務**選取**容器登錄**。
2. 選取您的登錄。 名稱開頭應該**mlcr** ，以及屬於資源群組、 位置和您用來設定模組管理訂用帳戶。
3. 選取**存取金鑰**
4. 複製**登入伺服器**， **Username**，和**密碼**。  您需要這些從邊緣裝置存取登錄。
5. 選取**儲存機制**
6. 選取**machinelearningmodule**
7. 現在您有容器的完整的映像路徑。 記下下一節中的映像路徑。 看起來應該像這樣： **< registry_name >.azureacr.io/machinelearningmodule:1**

## <a name="add-registry-credentials-to-your-edge-device"></a>將登錄認證新增至 Edge 裝置

在執行 Edge 裝置的電腦上，將登錄的認證新增至 Edge 執行階段。 此命令會提供執行階段存取提取的容器。

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
1. 如果您先前已部署 tempSensor 模組至您的 IoT 邊緣裝置，可能會 autopopulate。 如果尚未在模組的清單，請將它加入。
    1. 選取 [新增 IoT Edge 模組]。
    2. 在 [名稱] 欄位中，輸入 `tempSensor`。
    3. 在 [映像 URI] 欄位中，輸入 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`。
    4. 選取 [ **儲存**]。
1. 新增機器學習您所建立的模組。
    1. 選取 [新增 IoT Edge 模組]。
    1. 在**名稱**欄位中，輸入`machinelearningmodule`
    1. 在 [映像] 欄位中，輸入您的映像地址，例如`<registry_name>.azurecr.io/machinelearningmodule:1`。
    1. 選取 [ **儲存**]。
1. 回到**加入模組**步驟中，選取**下一步**。
1. 在 [指定路由] 步驟中，將下列 JSON 複製到文字方塊。 第一個路由會透過所有 Azure Machine Learning 模組使用的「amlInput」端點，將訊息從溫度感應器傳輸到機器學習模組。 第二個路由會將訊息從機器學習模組傳輸到 IoT 中樞。 在此路由，' amlOutput ' 是使用輸出資料，所有 Azure 機器學習模組的端點，' 上游 $' 代表 IoT 中樞。 

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
1. 返回裝置的詳細資料頁面，選取 [重新整理]。  您應該會看到新**machinelearningmodule**執行連同**tempSensor**模組和 IoT 邊緣執行階段模組。

## <a name="view-generated-data"></a>檢視產生的資料

您可以檢視您的 IoT 邊緣裝置傳送所使用 Visual Studio 程式碼的 Azure IoT Toolkit 延伸模組的裝置到雲端訊息。 

1. 在 Visual Studio 程式碼中，選取**IoT Hub 裝置**。 
2. 選取**...**然後選取**設定 IoT 中樞連接字串**從功能表。 

   ![IoT 中樞裝置更多的功能表](./media/tutorial-deploy-machine-learning/set-connection.png)

3. 會在頁面頂端的文字方塊中輸入 IoT 中樞 iothubowner 連接字串。 您的 IoT 邊緣裝置應該會出現在 IoT Hub 裝置清單。
4. 選取**...**然後再次選取**開始監視 D2C 訊息**。
5. 觀察來自 tempSensor 哪些 machinelearningmodule 附加至其裝置的健全狀況評估每五秒的訊息。 

   ![Azure ML 回應訊息主體中](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="next-steps"></a>後續步驟

在此教學課程中，您可以部署受到 Azure Machine Learning 支援的 IoT Edge 模組。 您可以繼續閱讀其他任何教學課程，以了解 Azure IoT Edge 有什麼其他方法可協助您將此資料轉換成具有優勢的商業見解。

> [!div class="nextstepaction"]
> [將 Azure Functions 部署為模組](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
