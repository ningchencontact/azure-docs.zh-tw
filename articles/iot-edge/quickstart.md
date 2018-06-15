---
title: 快速入門 Azure IoT Edge + Windows | Microsoft Docs
description: 在模擬的 Edge 裝置上執行分析以試用 Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 05/03/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 2fd16ab4ade61b1a08f93294051f4246e47839b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631729"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>快速入門：從 Azure 入口網站將您的第一個 IoT Edge 模組部署至 Windows 裝置 - 預覽

在本快速入門中，使用 Azure IoT Edge 雲端介面將預先建置的程式碼 IoT 從遠端部署到 Edge 裝置。 若要完成這項工作，請先使用您的 Windows 裝置來模擬 IoT Edge 裝置，然後就可以將模組部署到該裝置。

如果您沒有使用中的 Azure 訂用帳戶，請在開始前建立[免費帳戶][lnk-account]。

## <a name="prerequisites"></a>先決條件

本教學課程假設您使用執行 Windows 的電腦或虛擬機器，來模擬物聯網裝置。 如果您在虛擬機器中執行 Windows，請啟用[巢狀虛擬化][lnk-nested]並配置至少 2GB 的記憶體。 

1. 請確定您使用的是受支援的 Windows 版本：
   * Windows 10 
   * Windows Server
2. 安裝[適用於 Windows 的 Docker][lnk-docker] 並確定它正在執行。
3. 在 [Windows 上安裝 Python][lnk-python]，並確定您可以使用 pip 命令。 本快速入門已進行過 >= 2.7.9 和 >= 3.5.4 的 Python 版本測試。  
4. 執行下列命令以下載 IoT Edge 控制指令碼。

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT Edge 可以執行 Windows 容器或 Linux 容器。 若要使用 Windows 容器，您必須執行：
>    * Windows 10 Fall Creators Update，或
>    * Windows Server 1709 (組建 16299)，或
>    * x64 型裝置上的 Windows IoT 核心版 (組建 16299)
>
> 針對 Windows IoT 核心版，請遵循[在 Windows IoT 核心版上安裝 IoT Edge 執行階段][lnk-install-iotcore]中的指示。 或是，直接[設定可使用 Windows 容器的 Docker][lnk-docker-containers]，並選擇性地使用下列 powershell 命令來驗證您的必要條件：
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```

## <a name="create-an-iot-hub-with-azure-cli"></a>使用 Azure CLI 建立 IoT 中樞

在您的 Azure 訂用帳戶中建立 IoT 中樞。 此快速入門適用於 IoT 中樞的免費層級。 如果您在過去已使用過 IoT 中樞，並已建立可用的中樞，就可以跳過本節並移至[註冊 IoT Edge 裝置][anchor-register]。 每個訂用帳戶只能有一個免費的 IoT 中樞。 

1. 登入 [Azure 入口網站][lnk-portal]。 
1. 選取 [Cloud Shell] 按鈕。 

   ![[Cloud Shell] 按鈕][1]

1. 建立資源群組。 下列程式碼在**美國西部**區域中會建立一個名為 **IoTEdge** 的資源群組：

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. 在您的新資源群組中建立 IoT 中樞。 下列程式碼會在資源群組 **IoTEdge** 中建立一套名為 **MyIotHub** 的免費 **F1** 中樞：

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>註冊 IoT Edge 裝置

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>設定 IoT Edge 執行階段

IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 其包含兩個模組。 首先，IoT Edge 代理程式可協助進行部署及監視 IoT Edge 裝置上的模組。 第二，IoT Edge 中樞會管理 IoT Edge 裝置上的模組通訊，以及裝置與 IoT 中樞之間的通訊。 

使用上一節中的 IoT Edge 裝置連接字串來設定執行階段。

```cmd
iotedgectl setup --connection-string "{device connection string}" --nopass
```

啟動執行階段。

```cmd
iotedgectl start
```

檢查 Docker 以確認 IoT Edge 代理程式是否正作為模組執行中。

```cmd
docker ps
```

![請參閱 Docker 中的 edgeAgent](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>部署模組

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>檢視產生的資料

在此快速入門中，您可以建立新的 IoT Edge 裝置，並在其中安裝 IoT Edge 執行階段。 然後，您會使用 Azure 入口網站來推送 IoT Edge 模組，讓其無須變更裝置本身就能在裝置上執行。 在此案例中，您推送的模組會建立可在教學課程中使用的環境資料。 

在執行模擬裝置的電腦上再次開啟命令提示字元。 確認從雲端部署的模組是在 IoT Edge 裝置上執行。 

```cmd
docker ps
```

![在您的裝置上檢視三個模組](./media/tutorial-simulate-device-windows/docker-ps2.png)

檢視從 tempSensor 模組傳送至雲端的訊息。 

```cmd
docker logs -f tempSensor
```

![從您的模組中檢視資料](./media/tutorial-simulate-device-windows/docker-logs.png)

您也可以使用 [IoT 中樞總管工具][lnk-iothub-explorer]，檢視裝置正在傳送的遙測資料。 
## <a name="clean-up-resources"></a>清除資源

如果您想要移除您建立的模擬裝置，以及針對每個模組啟動的 Docker 容器，請使用下列命令： 

```cmd
iotedgectl uninstall
```

當您不再需要您所建立的 IoT 中樞時，可以使用 [az iot hub delete][lnk-delete] 命令來移除資源及任何與其相關聯的裝置：

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>後續步驟

您已了解如何將 IoT Edge 模組部署到 IoT Edge 裝置。 現在，請嘗試部署不同類型的 Azure 服務作為模組，以便您可以在 Edge 分析資料。 

* [將 Azure Functions 部署為模組](tutorial-deploy-function.md)
* [將 Azure 串流分析部署為模組](tutorial-deploy-stream-analytics.md)
* [將您自己的程式碼部署為模組](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-python]: https://www.python.org/downloads/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete
[lnk-install-iotcore]: how-to-install-iot-core.md

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
