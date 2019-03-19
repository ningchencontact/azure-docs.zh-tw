---
title: 使用閘道來轉譯 Modbus 通訊協定 - Azure IoT Edge | Microsoft Docs
description: 允許使用 Modbus TCP 的裝置藉由建立 IoT Edge 閘道裝置來與 Azure IoT 中樞通訊
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 1c9855f982b888e8e1d68bfe5233983db8c826ad
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873758"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>透過 IoT Edge 裝置閘道連線 Modbus TCP 裝置

如果您想要將使用 Modbus TCP 或 RTU 通訊協定的 IoT 裝置連線到 Azure IoT 中樞，請使用 IoT Edge 裝置作為閘道。 閘道裝置會從您的 Modbus 裝置讀取資料，然後使用支援的通訊協定將該資料傳達至雲端。

![Modbus 裝置透過 IoT Edge 閘道連線到 IoT 中樞](./media/deploy-modbus-gateway/diagram.png)

本文說明如何針對 Modbus 模組建立自己的容器映像 (您也可以使用預先建置的範例)，然後將它部署到將作為閘道的 IoT Edge 裝置。

本文假設您使用 Modbus TCP 通訊協定。 如需有關如何設定此模組以支援 Modbus RTU 的詳細資訊，請參閱 GitHub 上的 [Azure IoT Edge Modbus 模組](https://github.com/Azure/iot-edge-modbus)專案。

## <a name="prerequisites"></a>必要條件
* Azure IoT Edge 裝置。 如需如何設定一個逐步解說，請參閱 <<c0> [ 在 Windows 上部署 Azure IoT Edge](quickstart.md)或是[Linux](quickstart-linux.md)。
* IoT Edge 裝置的主索引鍵連接字串。
* 支援 Modbus TCP 的實體或模擬 Modbus 裝置。

## <a name="prepare-a-modbus-container"></a>準備 Modbus 容器

如果您想要測試 Modbus 閘道功能，Microsoft 有可供您使用的範例模組。 您可以存取的模組從 Azure Marketplace [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)，或使用映像 URI **mcr.microsoft.com/azureiotedge/modbus:1.0**。

如果您想要建立自己的模組並針對您的環境進行自訂，GitHub 上有提供開放原始碼 [Azure IoT Edge Modbus 模組](https://github.com/Azure/iot-edge-modbus)專案。 請遵循該專案中的方針，建立您自己的容器映像。 如果您建立自己的容器映像時，請參閱[開發C#Visual Studio 中的模組](how-to-visual-studio-develop-csharp-module.md)或是[開發在 Visual Studio Code 中的模組](how-to-vs-code-develop-module.md)。 這些文章提供的指示建立新的模組，並將容器映像發行至登錄。

## <a name="try-the-solution"></a>試用解決方案

這一節逐步說明部署 Microsoft 的範例 Modbus 模組到您的 IoT Edge 裝置。

1. 在 [Azure 入口網站](https://portal.azure.com/)上，移至您的 IoT 中樞。

2. 移至 [IoT Edge]，然後按一下您的 IoT Edge 裝置。

3. 選取 [設定模組]。

4. 新增 Modbus 模組：

   1. 按一下 [新增]，然後選取 [IoT Edge 模組]。

   2. 在 [名稱] 欄位中，輸入 "modbus"。

   3. 在 [映像] 欄位中，輸入範例容器的映像 URI：`mcr.microsoft.com/azureiotedge/modbus:1.0`。

   4. 核取 [啟用] 方塊以更新模組對應項所需的屬性。

   5. 將下列 JSON 複製到文字方塊中。 將 [SlaveConnection] 的值變更為 Modbus 裝置的 IPv4 位址。

      ```JSON
      {
        "properties.desired":{
          "PublishInterval":"2000",
          "SlaveConfigs":{
            "Slave01":{
              "SlaveConnection":"<IPV4 address>","HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{
                "Op01":{
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"400001",
                  "Count":"2",
                  "DisplayName":"Voltage"
                }
              }
            }
          }
        }
      }
      ```

   6. 選取 [ **儲存**]。

5. 回到 [新增模組] 步驟中，選取 [下一步]。

7. 在 [指定路由] 步驟中，將下列 JSON 複製到文字方塊中。 此路由會將 Modbus 模組收集的所有訊息傳送到 IoT 中樞。 在此路由中，' modbusOutput '是 Modbus 模組用來輸出資料的端點，而' upstream ' 是告知 IoT Edge 中樞將訊息傳送至 IoT 中樞的特殊目的地。
   ```JSON
   {
    "routes": {
      "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
    }
   }
   ```

8. 選取 [下一步] 。

9. 在 [檢閱部署] 步驟中，選取 [提交]。

10. 返回裝置的詳細資料頁面，選取 [重新整理]。 您應該會看到新的 **modbus** 模組隨著 IoT Edge 執行階段而執行。

## <a name="view-data"></a>檢視資料
檢視透過 modbus 模組提供的資料：
```cmd/sh
iotedge logs modbus
```

您也可以使用[適用於 Visual Studio Code 的 Azure IoT 中樞工具組延伸模組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (先前稱為「Azure IoT 工具組」延伸模組)，來檢視裝置所傳送的遙測資料。

## <a name="next-steps"></a>後續步驟

- 若要深入了解 IoT Edge 裝置可以做為閘道的方式，請參閱[建立的 IoT Edge 裝置作為透明閘道](./how-to-create-transparent-gateway.md)。
- 如需有關 IoT Edge 模組的運作方式的詳細資訊，請參閱 <<c0> [ 了解 Azure IoT Edge 模組](iot-edge-modules.md)。
