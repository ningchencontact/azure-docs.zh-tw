---
title: 將 Raspberry Pi 連線到 Azure IoT Central 應用程式 (Python) | Microsoft Docs
description: 身為裝置開發人員，如何將 Raspberry Pi 連線到您使用 Python 的 Azure IoT Central 應用程式。
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 6ac16651e2d49dd903ff994b18a8f571bd92fbf6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272354"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>將 Raspberry Pi 連線到 Azure IoT Central 應用程式 (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

本文說明如何以裝置開發人員身分，將 Raspberry Pi 連線到使用 Python 程式設計語言的 Microsoft Azure IoT Central 應用程式。

## <a name="before-you-begin"></a>開始之前

若要完成本文中的步驟，您需要下列元件︰

* Azure IoT Central 應用程式是從**範例 Devkits** 應用程式範本建立而來。 如需詳細資訊，請參閱[建立應用程式快速入門](quick-deploy-iot-central.md)。
* 執行 Raspbian 作業系統的 Raspberry Pi 裝置。 在 Raspberry Pi 必須能夠連線到網際網路。 如需詳細資訊，請參閱 <<c0> [ 設定您的 Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3)。

## <a name="sample-devkits-application"></a>**範例 Devkits** 應用程式

從**範例 Devkits** 應用程式範本建立的應用程式包含具有下列特性的 **Raspberry Pi**  裝置範本：

- 遙測，包括裝置將會收集的下列度量：
  - 溼度
  - 溫度
  - 壓力
  - 磁力計 (X, Y, Z)
  - 加速計 (X, Y, Z)
  - 迴轉儀 (X, Y, Z)
- 設定
  - 電壓
  - Current
  - 風扇速度
  - IR 切換。
- properties
  - 模具編號裝置屬性
  - 位置雲端屬性

設定裝置範本的完整詳細資訊，請參閱[Raspberry Pi 裝置範本詳細資料](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)。

## <a name="add-a-real-device"></a>新增真實裝置

Azure IoT Central 應用程式中加入實際的裝置，從**Raspberry Pi**裝置範本。 請記下的裝置的連線詳細資料 (**領域識別碼**，**裝置識別碼**，並**主索引鍵**)。 如需詳細資訊，請參閱[將真實裝置新增至 Azure IoT Central 應用程式](tutorial-add-device.md)。

### <a name="configure-the-raspberry-pi"></a>設定 Raspberry Pi

下列步驟說明如何從 GitHub 下載 Python 應用程式範例並進行設定。 此應用程式範例會：

* 將遙測和屬性值傳送至 Azure IoT Central。
* 回應在 Azure IoT Central 中所做的設定變更。

若要設定裝置[遵循的逐步指示在 GitHub 上](https://github.com/Azure/iot-central-firmware/blob/master/RaspberryPi/README.md)。

1. 設定裝置時，您的裝置就會開始將遙測度量傳送至 Azure IoT Central。
1. 在 Azure IoT Central 應用程式中，您會看到在 Raspberry Pi 上執行的程式碼如何與應用程式互動：

    * 在真實裝置的 [量測] 頁面上，您會看到從 Raspberry Pi 傳送過來的遙測。
    * 在 [**設定**] 頁面上，您可以變更例如電壓和風扇速度在 Raspberry Pi 上的設定。 當 Raspberry Pi 認可變更時，此設定會以**同步處理**。

## <a name="raspberry-pi-device-template-details"></a>在 raspberry Pi 裝置範本詳細資料

從**範例 Devkits** 應用程式範本建立的應用程式包含具有下列特性的 **Raspberry Pi**  裝置範本：

### <a name="telemetry-measurements"></a>遙測量測

| 欄位名稱     | Units  | 最小值 | 最大值 | 小數位數 |
| -------------- | ------ | ------- | ------- | -------------- |
| 溼度       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>設定

數值設定

| 顯示名稱 | 欄位名稱 | Units | 小數位數 | 最小值 | 最大值 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 電壓      | setVoltage | 伏特 | 0              | 0       | 240     | 0       |
| Current      | setCurrent | 安培  | 0              | 0       | 100     | 0       |
| 風扇速度    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

切換設定

| 顯示名稱 | 欄位名稱 | 開啟文字 | 關閉文字 | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | 開啟      | 關      | 關閉     |

### <a name="properties"></a>properties

| 類型            | 顯示名稱 | 欄位名稱 | 資料類型 |
| --------------- | ------------ | ---------- | --------- |
| 裝置屬性 | 模具編號   | dieNumber  | number    |
| 文字            | 位置     | location   | N/A       |

## <a name="next-steps"></a>後續步驟

既然您已了解如何將 Raspberry Pi 連線至 Azure IoT Central 應用程式，以下是建議的後續步驟：

* [泛型 Node.js 用戶端應用程式連接到 Azure IoT Central](howto-connect-nodejs.md)
