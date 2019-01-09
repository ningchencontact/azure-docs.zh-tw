---
title: 快速入門：在 Windows 上建立 Azure IoT Edge 裝置 | Microsoft Docs
description: 在本快速入門中，了解如何建立 IoT Edge 裝置，然後從 Azure 入口網站遠端部署預先建置的程式碼。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/31/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 2295ed6d3d1b22d70f95d0c9ac4542b59c7ddc09
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2019
ms.locfileid: "53972085"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>快速入門：從 Azure 入口網站將您的第一個 IoT Edge 模組部署至 Windows 裝置 - 預覽

在本快速入門中，使用 Azure IoT Edge 雲端介面將預先建置的程式碼 IoT 從遠端部署到 Edge 裝置。 若要完成這項工作，請先使用您的 Windows 裝置來模擬 IoT Edge 裝置，然後就可以將模組部署到該裝置。

在此快速入門中，您將了解如何：

1. 建立 IoT 中樞。
2. 向 IoT 中樞註冊 IoT Edge 裝置。
3. 在裝置上安裝並啟動 IoT Edge 執行階段。
4. 將模組從遠端部署至 IoT Edge 裝置，並將遙測資料傳送至 IoT 中樞。

![圖表 - 裝置和雲端的快速入門架構](./media/quickstart/install-edge-full.png)

您在本快速入門中部署的模組是一個模擬感應器，會產生溫度、溼度和壓力資料。 其他 Azure IoT Edge 教學課程會以您在此所做的工作為基礎，部署模組來分析模擬資料以產生商業見解。

>[!NOTE]
>Windows 上的 IoT Edge 執行階段為[公開預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果您沒有使用中的 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

您可以使用 Azure CLI 來完成本快速入門中的許多步驟，而且 Azure IoT 有擴充功能可供啟用其他功能。

將 Azure IoT 擴充功能新增至 Cloud Shell 執行個體。

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prerequisites"></a>必要條件

雲端資源：

* 一個資源群組，用以管理本快速入門中使用的所有資源。

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus
   ```

IoT Edge 裝置：

* 可當作您 IoT Edge 裝置的 Windows 電腦或虛擬機器。 使用支援的 Windows 版本：
  * 具有 2018 年 10 月更新的 Windows 10 或 IoT 核心版 (組建 17763)
  * Windows Server 2019
* 啟用虛擬化，使您的裝置可以裝載容器
   * 如果是 Windows 電腦，請啟用容器功能。 在開始列中，瀏覽至 [開啟或關閉 Windows 功能]，並勾選 [容器] 旁的核取方塊。
   * 如果使用虛擬機器，請啟用[巢狀虛擬化](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)並配置至少 2GB 的記憶體。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

使用 Azure CLI 建立 IoT 中樞，開始進行此快速入門。

![圖表 - 在雲端建立 IoT 中樞](./media/quickstart/create-iot-hub.png)

此快速入門適用於 IoT 中樞的免費層級。 如果您在過去已使用過 IoT 中樞，並已建立可用的中樞，您可以使用該 IoT 中樞。 每個訂用帳戶只能有一個免費的 IoT 中樞。

下列程式碼會在資源群組 **IoTEdgeResources** 中建立免費的 **F1** 中樞。 請以 IoT 中樞的唯一名稱取代 {hub_name}。

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1
   ```

   如果因您的訂用帳戶中已有免費中樞而發生錯誤，請將 SKU 變更為 **S1**。 如果您收到無法使用 IoT 中樞名稱的錯誤，則表示其他人已經有該名稱的中樞。 請嘗試新的名稱。 

## <a name="register-an-iot-edge-device"></a>註冊 IoT Edge 裝置

向新建立的 IoT 中樞註冊 IoT Edge 裝置。
![圖表 - 使用 IoT 中樞身分識別註冊裝置](./media/quickstart/register-device.png)

建立模擬裝置的裝置身分識別，以便與 IoT 中樞通訊。 裝置身分識別存在於雲端，您可以使用唯一的裝置連接字串，讓實體裝置與裝置身分識別建立關聯。

由於 IoT Edge 裝置的行為和管理方式不同於典型 IoT 裝置，請使用 `--edge-enabled` 旗標將此身分識別宣告為 IoT Edge 裝置。 

1. 在 Azure Cloud Shell 中輸入下列命令，以在中樞內建立名為 **myEdgeDevice** 的裝置。

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
   ```

   如果您收到有關 iothubowner 原則金鑰的錯誤，請確定 Cloud Shell 正在執行最新版的 azure-cli-iot-ext 擴充功能。 

2. 擷取裝置的連接字串，此字串將作為連結實體裝置與其在 IoT 中樞內的身分識別。

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. 從 JSON 輸出中複製連接字串，並加以儲存。 您將在下一節中使用此值設定 IoT Edge 執行階段。

   ![從 CLI 輸出中擷取連接字串](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>安裝並啟動 IoT Edge 執行階段

在 IoT Edge 裝置上安裝 Azure IoT Edge 執行階段，並使用裝置連接字串進行設定。
![圖表 - 在裝置上啟動執行階段](./media/quickstart/start-runtime.png)

IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 它有三個元件。 **IoT Edge 安全性精靈**會在每次 Edge 裝置開機時啟動，並藉由啟動 IoT Edge 代理程式來啟動該裝置。 **IoT Edge 代理程式**有助於在 IoT Edge 裝置 (包括 IoT Edge 中樞) 上部署及監視模組。 **IoT Edge 中樞**會管理 IoT Edge 裝置上的模組通訊，以及裝置與 IoT 中樞之間的通訊。

安裝指令碼也包含名為 Moby 的容器引擎，可用來管理 IoT Edge 裝置上的容器映像。 

在執行階段安裝期間，系統會要求您提供裝置連接字串。 請使用您從 Azure CLI 擷取到的字串。 這個字串會讓實體裝置與 Azure 中的 IoT Edge 裝置身分識別建立關聯。

本節中的指示會設定 Windows 容器的 IoT Edge 執行階段。 如果您想要使用 Linux 容器，請參閱[在 Windows 上安裝 Azure IoT Edge 執行階段](how-to-install-iot-edge-windows-with-linux.md)，以取得必要條件和安裝步驟。

### <a name="connect-to-your-iot-edge-device"></a>連線到 IoT Edge 裝置

本節中的步驟全都在 IoT Edge 裝置上進行。 如果您使用虛擬機器或次要硬體，您可以透過 SSH 或遠端桌面立即連線到該機器。 如果您使用自己的機器作為 IoT Edge 裝置，則可以繼續進行下一節。 

### <a name="download-and-install-the-iot-edge-service"></a>下載並安裝 IoT Edge 服務

使用 PowerShell 下載並安裝 IoT Edge 執行階段。 使用從 IoT 中樞擷取到的裝置連接字串來設定裝置。

1. 在您的 IoT Edge 裝置上，以系統管理員身分執行 PowerShell。

2. 對裝置下載並安裝 IoT Edge 服務。

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Windows
   ```

3. 當系統提示您提供 **DeviceConnectionString** 時，請提供上一節所複製的字串。 請勿在連接字串兩側加上引號。

### <a name="view-the-iot-edge-runtime-status"></a>檢視 IoT Edge 執行階段狀態

確認已成功安裝並設定執行階段。

1. 檢查 IoT Edge 服務的狀態。

   ```powershell
   Get-Service iotedge
   ```

2. 如果您需要對服務進行疑難排解，請擷取服務記錄。

   ```powershell
   # Displays logs from today, newest at the bottom.

   Get-WinEvent -ea SilentlyContinue `
    -FilterHashtable @{ProviderName= "iotedged";
      LogName = "application"; StartTime = [datetime]::Today} |
    select TimeCreated, Message |
    sort-object @{Expression="TimeCreated";Descending=$false} |
    format-table -autosize -wrap
   ```

3. 檢視在 IoT Edge 裝置上執行的所有模組。 由於服務只有第一次會啟動，您應該只會看到 **edgeAgent** 模組正在執行。 EdgeAgent 模組預設會執行，且有助於安裝及啟動您部署至裝置的任何其他模組。

   ```powershell
   iotedge list
   ```

   ![檢視裝置上的一個模組](./media/quickstart/iotedge-list-1.png)

IoT Edge 裝置現已設定完成。 並已準備好執行雲端部署的模組。

## <a name="deploy-a-module"></a>部署模組

從雲端管理您的 Azure IoT Edge 裝置，以部署會將遙測資料傳送到 IoT 中樞的模組。
![圖表 - 將模組從雲端部署到裝置](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>檢視產生的資料

在此快速入門中，您可以建立新的 IoT Edge 裝置，並在其中安裝 IoT Edge 執行階段。 然後，您會使用 Azure 入口網站來推送 IoT Edge 模組，讓其無須變更裝置本身就能在裝置上執行。 在此案例中，您推送的模組會建立可在教學課程中使用的環境資料。

確認從雲端部署的模組是在 IoT Edge 裝置上執行。

```powershell
iotedge list
```

   ![在您的裝置上檢視三個模組](./media/quickstart/iotedge-list-2.png)

檢視從溫度感應器模組傳送至雲端的訊息。

```powershell
iotedge logs SimulatedTemperatureSensor -f
```

   >[!TIP]
   >IoT Edge 命令在在參考模組名稱時會區分大小寫。

   ![從您的模組中檢視資料](./media/quickstart/iotedge-logs.png)

您也可以使用[適用於 Visual Studio Code 的 Azure IoT 中樞工具組擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (先前稱為 Azure IoT 工具組擴充功能)，查看送達 IoT 中樞的訊息。 

## <a name="clean-up-resources"></a>清除資源

如果您想要繼續進行 IoT Edge 教學課程，您可以使用在本快速入門中註冊和設定的裝置。 否則，您可以刪除您所建立的 Azure 資源，並從裝置中移除 IoT Edge 執行階段。

### <a name="delete-azure-resources"></a>刪除 Azure 資源

如果您是在新的資源群組中建立虛擬機器和 IoT 中樞，您可以刪除該群組和所有相關聯的資源。 再次檢查資源群組的內容，確定沒有您想要保留的內容。 如果您不想刪除整個群組，可改為刪除個別資源。

移除 **IoTEdgeResources** 群組。

   ```azurecli-interactive
   az group delete --name IoTEdgeResources
   ```

### <a name="remove-the-iot-edge-runtime"></a>移除 IoT Edge 執行階段

如果您想要從裝置移除這些安裝，請使用下列命令。  

移除 IoT Edge 執行階段。 如果您打算重新安裝 IoT Edge，請省略 `-DeleteConfig` 和 `-DeleteMobyDataRoot` 參數，以便使用您剛設定的相同組態重新安裝。

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Uninstall-SecurityDaemon -DeleteConfig -DeleteMobyDataRoot
   ```

IoT Edge 執行階段移除後，它所建立的容器隨即停止，但仍會存在於您的裝置上。 檢視所有容器。

   ```powershell
   docker -H npipe:////./pipe/iotedge_moby_engine ps -a
   ```

   >[!TIP]
   >Docker 命令中的 **-H** (主機) 旗標會指向隨著 IoT Edge 執行階段安裝的 Moby 引擎。 如果您在相同的機器上使用 Docker 和 Moby，主機旗標將可讓您指定特定命令所使用的引擎。 如果您只想要使用 Moby，您可以將 **DOCKER_HOST** 環境變數設為指向 npipe:////./pipe/iotedge_moby_engine。

刪除 IoT Edge 執行階段在您的裝置上建立的容器。 

   ```powershell
   docker -H npipe:////./pipe/iotedge_moby_engine rm -f SimulatedTemperatureSensor
   docker -H npipe:////./pipe/iotedge_moby_engine rm -f edgeHub
   docker -H npipe:////./pipe/iotedge_moby_engine rm -f edgeAgent
   ```
   
## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立新的 IoT Edge 裝置，並使用 Azure IoT Edge 雲端介面將程式碼部署至裝置上。 現在，您已有測試裝置，可產生其環境的相關原始資料。

您可以繼續進行任何其他教學課程，以了解 Azure IoT Edge 可如何協助您將此資料轉換成 Edge 上的商業見解。

> [!div class="nextstepaction"]
> [使用 Azure 函式篩選感應器資料](tutorial-deploy-function.md)
