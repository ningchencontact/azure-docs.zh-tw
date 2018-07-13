---
title: 使用 Azure IoT Edge部署 Azure Machine Learning | Microsoft Docs
description: 將 Azure Machine Learning 作為模組部署至邊緣裝置
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/25/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 47db87bf734674bd424fecd0f0f22bff9e2df5d5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38299249"
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>將 Azure Machine Learning 部署為 IoT Edge 模組 - 預覽

您可以使用 IoT Edge 模組來部署程式碼，將您的商務邏輯直接實作到您的 IoT Edge 裝置。 本教學課程會逐步引導您部署 Azure Machine Learning 模組，以根據模擬機器的溫度資料來預測裝置何時會故障。 

您在本教學課程中建立的 Azure Machine Learning 模組會讀取裝置所產生的環境資料，並將訊息標示為異常或非異常。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立 Azure Machine Learning 模組
> * 將模組容器推送至 Azure Container Registry
> * 將 Azure Machine Learning 模組部署至 IoT Edge 裝置
> * 檢視產生的資料

>[!NOTE]
>Azure IoT Edge 上的 Azure Machine Learning 模組目前為公開預覽版。 

## <a name="prerequisites"></a>先決條件

若要測試您在本教學課程中建置的 Machine Learning 模組，您必須要有 IoT Edge 裝置。 您可以使用您在 [Linux](quickstart-linux.md) 或 [Windows 裝置](quickstart.md)快速入門中所設定的裝置。 

Azure Machine Learning 模組不支援 ARM 處理器。

您的開發電腦必須符合下列先決條件： 
* Azure Machine Learning 帳戶。 請遵循[建立 Azure Machine Learning 帳戶，並安裝 Azure Machine Learning Workbench](../machine-learning/service/quickstart-installation.md#create-azure-machine-learning-services-accounts) 中的指示。 您不需要針對此教學課程安裝 Workbench 應用程式。 
* 在您的電腦上的 Azure ML 模組管理。 若要設定您的環境並建立帳戶，請遵循[模型管理設定](../machine-learning/desktop-workbench/deployment-setup-configuration.md)中的指示。

### <a name="disable-process-identification"></a>停用程序識別

>[!NOTE]
>
> 預覽版的 Azure Machine Learning 不支援 IoT Edge 所預設啟用的程序識別安全性功能。 
> 以下是此功能的停用步驟。 不過，這不適合生產環境使用。

若要停用程序識別，您必須在 IoT Edge 精靈設定的 [連線] 區段中，對 **workload_uri** 和 **management_uri** 提供 IP 位址和連接埠。

先取得 IP 位址。 在命令列中輸入 `ifconfig`，然後複製 **docker0** 介面的 IP 位址。

編輯 IoT Edge 精靈設定檔：

```cmd/sh
sudo nano /etc/iotedge/config.yaml
```

以您的 IP 位址更新設定的 [連線] 區段。 例如︰
```yaml
connect:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

在設定的 [接聽] 區段中輸入相同的位址。 例如︰

```yaml
listen:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

使用 management_uri 位址建立環境變數 IOTEDGE_HOST (若要永久設定此變數，請將變數新增至 `/etc/environment`)。例如：

```cmd/sh
export IOTEDGE_HOST="http://172.17.0.1:15580"
```


## <a name="create-the-azure-ml-container"></a>建立 Azure ML 容器
在本節中，您會下載定型模型檔案，並將它們轉換成 Azure ML 容器。

在執行 Azure ML 模組管理的電腦上，從 GitHub 上的 Azure ML IoT Toolkit 下載並儲存 [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) 和 [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl)。 這些檔案會定義您將會部署到 IoT Edge 裝置的定型機器學習模型。

使用定型模型以建立可以部署到 IoT Edge 裝置的容器。 使用下列命令，您可以：

   * 註冊您的模組。
   * 建立資訊清單。
   * 建立名為 machinelearningmodule 的 Docker 容器映像。
   * 將映像部署至您的 Azure Kubernetes Service (AKS) 叢集。

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```

### <a name="view-the-container-repository"></a>檢視容器存放庫

檢查容器映像是否已成功建立，並且儲存在與機器學習環境相關聯的 Azure Container Registry 中。

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至 [所有服務]，然後選取 [容器登錄]。
2. 選取您的登錄。 名稱應該以 **mlcr** 開頭，並且屬於您用來設定模組管理的資源群組、位置及訂用帳戶。
3. 選取 [存取金鑰]
4. 複製 [登入伺服器]、[使用者名稱] 及 [密碼]。  您需要這些值以便從 Edge 裝置存取登錄。
5. 選取 [存放庫]
6. 選取 [machinelearningmodule]
7. 現在您有容器的完整映像路徑。 記下此映像路徑以在下一節中使用。 看起來如下所示：**<registry_name>.azureacr.io/machinelearningmodule:1**

## <a name="deploy-to-your-device"></a>部署至裝置

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的 IoT 中樞。

1. 移至 [IoT Edge] 並選取您的 IoT Edge 裝置。

1. 選取 [設定模組]。

1. 在 [登錄設定] 區段中，新增您從 Azure Container Registry 複製得到的認證。 

   ![新增登錄認證](./media/tutorial-deploy-machine-learning/registry-settings.png)

1. 如果您先前在 IoT Edge 裝置上部署過 tempSensor 模組，可能會自動填入。 如果尚未在模組清單中，請新增它。

    1. 按一下 [新增]，然後選取 [IoT Edge 模組]。
    2. 在 [名稱] 欄位中，輸入 `tempSensor`。
    3. 在 [映像 URI] 欄位中，輸入 `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`。
    4. 選取 [ **儲存**]。

1. 新增您建立的機器學習模組。

    1. 按一下 [新增]，然後選取 [Azure Machine Learning 模組]。
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

1. 在 [檢閱部署] 步驟中，選取 [提交]。

1. 返回裝置的詳細資料頁面，選取 [重新整理]。  您應該會看到新的 **machinelearningmodule** 正在與 **tempSensor** 模組和 IoT Edge 執行階段模組一起執行。

## <a name="view-generated-data"></a>檢視產生的資料

您可以檢視每個 IoT Edge 模組所會產生的訊息，也可檢視傳遞至 IoT 中樞的訊息。

### <a name="view-data-on-your-iot-edge-device"></a>檢視 IoT Edge 裝置上的資料

在 IoT Edge 裝置上，您可以檢視會從每個模組傳來的訊息。 

如果您在 Linux 裝置上執行這些命令，則可能需要使用 `sudo` 以獲得較高的權限。

1. 在 IoT Edge 裝置上檢視所有模組。

   ```cmd/sh
   iotedge list
   ```

2. 檢視會從特定裝置傳來的訊息。 使用前一個命令輸出中的模組名稱。

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>檢視抵達 IoT 中樞的資料

您可以使用 [IoT 中樞總管](https://github.com/azure/iothub-explorer)或[適用於 Visual Studio Code 的 Azure IoT 工具組擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)，來檢視 IoT 中樞所收到的裝置到雲端訊息。

下列步驟說明如何設定 Visual Studio Code 來監視抵達 IoT 中樞的裝置到雲端訊息。 

1. 在 Visual Studio Code 中，選取 [IoT 中樞裝置]。

2. 從功能表選取 [...]，然後選取 [設定 IoT 中樞連接字串]。

   ![IoT 中樞裝置更多功能表](./media/tutorial-deploy-machine-learning/set-connection.png)

3. 在頁面頂端開啟的文字方塊中，輸入 IoT 中樞的 iothubowner 連接字串。 您的 IoT Edge 裝置應該會出現在 IoT 中樞裝置清單中。

4. 再次選取 [...]，然後選取 [開始監視 D2C 訊息]。

5. 每五秒就會觀察來自 tempSensor 的訊息。 訊息本文包含稱為 **anomaly** 的屬性，machinelearningmodule 會搭配 True 或 False 值來提供此屬性。 如果模型已順利執行，**AzureMLResponse** 屬性即會包含 "OK" 值。

   ![訊息主體中的 Azure ML 回應](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>清除資源 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

如果您將繼續閱讀下一篇建議的文章，則可以保留您已建立的資源和設定，並加以重複使用。

否則，您可以刪除在本文中建立的本機組態和 Azure 資源，以避免產生費用。 

> [!IMPORTANT]
> 刪除 Azure 資源和資源群組是無法回復的動作。 一旦刪除，資源群組和其中包含的所有資源都將永久刪除。 請確定您不會不小心刪除錯誤的資源群組或資源。 如果您在現有的資源群組內建立了 IoT 中樞，而該群組中包含您想要保留的資源，則您只需刪除 IoT 中樞本身即可，而不要刪除資源群組。
>

若只要刪除 IoT 中樞，請使用中樞名稱和資源群組名稱執行下列命令：

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


若要依名稱刪除整個資源群組：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [資源群組]。

2. 在 [依名稱篩選...] 文字方塊中，輸入包含 IoT 中樞的資源群組名稱。 

3. 在結果清單中的資源群組右側，按一下 **...**，然後按一下 [刪除資源群組]。

<!--
   ![Delete](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)
-->
4. 系統將會要求您確認是否刪除資源。 再次輸入您的資源群組名稱進行確認，然後按一下 [刪除]。 片刻過後，系統便會刪除該資源群組及其所有內含的資源。

## <a name="next-steps"></a>後續步驟

在此教學課程中，您可以部署受到 Azure Machine Learning 支援的 IoT Edge 模組。 您可以繼續閱讀其他任何教學課程，以了解 Azure IoT Edge 有什麼其他方法可協助您將此資料轉換成具有優勢的商業見解。

> [!div class="nextstepaction"]
> [使用 C# 程式碼篩選感應器資料](tutorial-csharp-module.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
