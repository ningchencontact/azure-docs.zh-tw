---
title: 從命令列註冊新的裝置 - Azure IoT Edge | Microsoft Docs
description: 使用 Azure CLI 的 IoT 擴充功能來註冊新的 IoT Edge 裝置並擷取連接字串
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: de00c317483da9bcd93bb2e2505350d787385925
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66495382"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli"></a>使用 Azure CLI 來註冊新的 Azure IoT Edge 裝置

您必須先向 IoT 中樞註冊 IoT 裝置，才能將 IoT 裝置與 Azure IoT Edge 搭配使用。 一旦您註冊裝置時，您會收到可用來設定您的裝置，IoT Edge 的工作負載的連接字串。

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) 是一個開放原始碼跨平台命令列工具，用來管理 Azure 資源 (例如 IoT Edge)。 它可讓您管理 Azure IoT 中樞資源、裝置佈建服務執行個體，以及現成的連結中樞。 新的 IoT 擴充功能會以裝置管理和完整 IoT Edge 功能等功能來擴充 Azure CLI 的功能。

本文說明如何使用 Azure CLI 來註冊新的 IoT Edge 裝置。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-using-cli.md)。
* 您環境中的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 您的 Azure CLI 版本至少必須是 2.0.24 或更新版本。 使用 `az –-version` 進行驗證。 這個版本支援 az 擴充命令並引進 Knack 命令架構。
* [適用於 Azure CLI 的 IoT 擴充功能](https://github.com/Azure/azure-iot-cli-extension) \(英文\)。

## <a name="create-a-device"></a>建立裝置

使用[az iot 中樞裝置身分識別建立](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create)命令，以在 IoT 中樞建立新的裝置身分識別。 例如:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

這個命令包含三個參數：

* **device-id**：提供 IoT 中樞內唯一的描述性名稱。

* **hub-name**：提供 IoT 中樞的名稱。

* **edge-enabled**：此參數會宣告裝置是要 IoT Edge 搭配使用。

   ![az iot hub device-identity create 輸出](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>檢視所有裝置

使用[az iot 中樞裝置身分識別清單](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list)命令來檢視您的 IoT 中樞中所有的裝置。 例如:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

任何註冊為 IoT Edge 裝置的裝置，其 **capabilities.iotEdge** 屬性都會設定成 **true**。

## <a name="retrieve-the-connection-string"></a>擷取連接字串

當您準備好開始設定裝置時，需要連接字串才能利用實體裝置在 IoT 中樞中的身分識別來連結實體裝置。 使用[az iot 中樞裝置身分識別顯示連接字串](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string)命令，以傳回單一裝置的連接字串：

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

`device-id` 參數的值區分大小寫。 請勿複製連接字串兩旁的引號。

## <a name="next-steps"></a>後續步驟

了解如何[將模組部署到裝置，以使用 Azure CLI](how-to-deploy-modules-cli.md)。
