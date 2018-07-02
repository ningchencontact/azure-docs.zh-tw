---
title: 快速入門 Azure IoT Edge + Linux | Microsoft Docs
description: 在本快速入門中，了解如何將預先建置的程式碼從遠端部署到 IoT Edge 裝置。
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 86bf28249321a705e8855de35121611b05009854
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063488"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-linux-x64-device"></a>快速入門：將您的第一個 IoT Edge 模組部署至 Linux x64 裝置

Azure IoT Edge 會將雲端的強大功能移至您的物聯網裝置。 在本快速入門中，了解如何使用雲端介面將預先建置的程式碼從遠端部署到 IoT Edge 裝置。

在此快速入門中，您將了解如何：

1. 建立 IoT 中樞。
2. 向 IoT 中樞註冊 IoT Edge 裝置。
3. 在裝置上安裝並啟動 IoT Edge 執行階段。
4. 將模組從遠端部署到 IoT Edge 裝置。

![快速入門架構][2]

本快速入門會讓 Linux 電腦或虛擬機器變成 IoT Edge 裝置。 然後，您可以從 Azure 入口網站將模組部署到裝置。 您在本快速入門中部署的模組是一個模擬感應器，會產生溫度、溼度和壓力資料。 其他 Azure IoT Edge 教學課程會以您在此所做的工作為基礎，部署模組來分析模擬資料以產生商業見解。 

如果您沒有使用中的 Azure 訂用帳戶，請在開始前建立[免費帳戶][lnk-account]。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

您可以使用 Azure CLI 來完成本快速入門中的許多步驟，而且 Azure IoT 有擴充功能可供啟用其他功能。 

在 Cloud Shell 執行個體中新增 Azure IoT 擴充功能。

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

在 Azure 入口網站中建立 IoT 中樞以開始此快速入門。
![建立 IoT 中樞][3]

此快速入門適用於 IoT 中樞的免費層級。 如果您在過去已使用過 IoT 中樞，並已建立可用的中樞，就可以使用該 IoT 中樞。 每個訂用帳戶只能有一個免費的 IoT 中樞。 

1. 在 Azure Cloud Shell 中建立資源群組。 下列程式碼會在**美國西部**區域建立一個名為 **TestResources** 的資源群組。 將快速入門和教學課程的所有資源放在群組中，即可同時管理這些資源。 

   ```azurecli-interactive
   az group create --name TestResources --location westus
   ```

1. 在您的新資源群組中建立 IoT 中樞。 下列程式碼會在資源群組 **TestResources** 中建立一個免費的 **F1** 中樞。 以 IoT 中樞的唯一名稱取代 {hub_name}。

   ```azurecli-interactive
   az iot hub create --resource-group TestResources --name {hub_name} --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>註冊 IoT Edge 裝置

向新建立的 IoT 中樞註冊 IoT Edge 裝置。
![註冊裝置][4]

建立模擬裝置的裝置身分識別，以便與 IoT 中樞通訊。 由於 IoT Edge 裝置的行為和管理方式不同於典型 IoT 裝置，您從開頭就要將此宣告為 IoT Edge 裝置。 

1. 在 Azure Cloud Shell 中輸入下列命令，以在中樞內建立名為 **myEdgeDevice** 的裝置。

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
   ```

1. 擷取裝置的連接字串，此字串會讓實體裝置與裝置在 IoT 中樞內的身分識別相連結。 

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

1. 複製連接字串，並加以儲存。 您將在下一節中使用此值設定 IoT Edge 執行階段。 


## <a name="install-and-start-the-iot-edge-runtime"></a>安裝並啟動 IoT Edge 執行階段

安裝並啟動您裝置上的 Azure IoT Edge 執行階段。 
![註冊裝置][5]

IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 它有三個元件。 **IoT Edge 安全性精靈**會在每次 Edge 裝置開機時啟動，並藉由啟動 IoT Edge 代理程式來啟動該裝置。 **IoT Edge 代理程式**有助於在 IoT Edge 裝置 (包括 IoT Edge 中樞) 上部署及監視模組。 **IoT Edge 中樞**會管理 IoT Edge 裝置上的模組通訊，以及裝置與 IoT 中樞之間的通訊。 

### <a name="register-your-device-to-use-the-software-repository"></a>註冊裝置以使用軟體存放庫

要執行 IoT Edge 執行階段所需的套件可於軟體存放庫中進行管理。 請設定 IoT Edge 裝置來存取這個存放庫。 

本節中的步驟適用於執行 **Ubuntu 16.04** 的裝置。 若要存取其他 Linux 版本上的軟體存放庫，請參閱[在 Linux (x64) 上安裝 Azure IoT Edge 執行階段](how-to-install-iot-edge-linux.md)或[在 Linux (ARM32v7/armhf) 上安裝 Azure IoT Edge 執行階段](how-to-install-iot-edge-linux-arm.md)。

1. 在要作為 IoT Edge 裝置的機器上，安裝存放庫設定。

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

2. 安裝公開金鑰來存取存放庫。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>安裝容器執行階段

IoT Edge 執行階段是一組容器，而部署到 IoT Edge 裝置的邏輯則會封裝為容器。 安裝容器執行階段，讓裝置準備好使用這些元件。

更新 **apt-get**。

   ```bash
   sudo apt-get update
   ```

安裝 Moby、容器執行階段和其 CLI 命令。 

   ```bash
   sudo apt-get install moby-engine
   sudo apt-get install moby-cli   
   ```

### <a name="install-and-configure-the-iot-edge-security-daemon"></a>安裝和設定 IoT Edge 安全性精靈

安全性精靈會安裝為系統服務，以便在每次裝置開機時啟動 IoT Edge 執行階段。 這個安裝也包括某個版本的 **hsmlib**，其可讓安全性精靈與裝置的硬體安全性互動。 

1. 下載和安裝 IoT Edge 安全性精靈。 

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

2. 開啟 IoT Edge 設定檔。 此檔案受到保護，因此您可能必須使用較高的權限才能存取。
   
   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

3. 新增您在註冊裝置時所複製的 IoT Edge 裝置連接字串。 取代您稍早在本快速入門中複製的 **device_connection_string** 變數值。

4. 重新啟動 Edge 安全性精靈：

   ```bash
   sudo systemctl restart iotedge
   ```

5. 確認 Edge 安全性精靈正以系統服務的形式執行：

   ```bash
   sudo systemctl status iotedge
   ```

   ![看到 Edge 精靈正以系統服務的形式執行](./media/quickstart-linux/iotedged-running.png)

   您也可以執行下列命令，以查看 Edge 安全性精靈中的記錄：

   ```bash
   journalctl -u iotedge
   ```

6. 檢視在裝置上執行的模組： 

   ```bash
   iotedge list
   ```

   ![在裝置上看到一個模組](./media/quickstart-linux/iotedge-list-1.png)

## <a name="deploy-a-module"></a>部署模組

在雲端管理 Azure IoT Edge 裝置，以部署會將遙測資料傳送到 IoT 中樞的模組。
![註冊裝置][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>檢視產生的資料

在此快速入門中，您可以建立新的 IoT Edge 裝置，並在其中安裝 IoT Edge 執行階段。 然後，您會使用 Azure 入口網站來推送 IoT Edge 模組，讓其無須變更裝置本身就能在裝置上執行。 在此案例中，您推送的模組會建立可在教學課程中使用的環境資料。 

在執行模擬裝置的電腦上再次開啟命令提示字元。 確認從雲端部署的模組正在 IoT Edge 裝置上執行：

   ```bash
   iotedge list
   ```

   ![在您的裝置上檢視三個模組](./media/quickstart-linux/iotedge-list-2.png)

檢視從 tempSensor 模組送出的訊息：

   ```bash
   sudo iotedge logs tempSensor -f 
   ```
登出再登入後，上述命令就不需要 *sudo*。

![從您的模組中檢視資料](./media/quickstart-linux/iotedge-logs.png)

如果您在記錄中看到的最後一行是 `Using transport Mqtt_Tcp_Only`，則表示溫度感應器模組可能正在等候連線到 Edge 中樞。 請嘗試終止此模組，並讓 Edge 代理程式重新啟動模組。 您可以使用 `sudo docker stop tempSensor` 命令終止模組。

您也可以使用 [IoT 中樞總管工具][lnk-iothub-explorer]或[適用於 Visual Studio Code 的 Azure IoT 工具組擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)，來檢視裝置所傳送遙測資料。 


## <a name="clean-up-resources"></a>清除資源

如果您想要繼續進行 IoT Edge 教學課程，您可以使用在本快速入門中註冊和設定的裝置。 如果您想要從裝置移除這些安裝，請使用下列命令。  

移除 IoT Edge 執行階段。

   ```bash
   sudo apt-get remove --purge iotedge
   ```

刪除裝置上所建立的容器。 

   ```bash
   sudo docker rm -f $(sudo docker ps -aq)
   ```

移除容器執行階段。

   ```bash
   sudo apt-get remove --purge moby
   ```

當您不再需要您所建立的 Azure 資源時，可以使用下列命令來刪除所建立的資源群組以及任何與其相關聯的資源：

   ```azurecli-interactive
   az group delete --name TestResources
   ```

## <a name="next-steps"></a>後續步驟

本快速入門是所有 IoT Edge 教學課程的先決條件。 您可以繼續閱讀其他任何教學課程，以了解 Azure IoT Edge 可如何協助您將此資料轉換成具有優勢的商業洞見。

> [!div class="nextstepaction"]
> [使用 Azure 函式篩選感應器資料](tutorial-deploy-function.md)



<!-- Images -->
[0]: ./media/quickstart-linux/cloud-shell.png
[1]: ./media/quickstart-linux/view-module.png
[2]: ./media/quickstart-linux/install-edge-full.png
[3]: ./media/quickstart-linux/create-iot-hub.png
[4]: ./media/quickstart-linux/register-device.png
[5]: ./media/quickstart-linux/start-runtime.png
[6]: ./media/quickstart-linux/deploy-module.png
[7]: ./media/quickstart-linux/iotedged-running.png
[8]: ./media/tutorial-simulate-device-linux/running-modules.png
[9]: ./media/tutorial-simulate-device-linux/sensor-data.png


<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
