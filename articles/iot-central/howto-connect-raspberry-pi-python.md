---
title: 將 Raspberry Pi 連線到 Azure IoT Central 應用程式 (Python) | Microsoft Docs
description: 身為裝置開發人員, 如何使用 Python 將 Raspberry Pi 連線到您的 Azure IoT Central 應用程式。
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: c8fd5309f50cfc024083cb8a05d679d04bf112dc
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972272"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>將 Raspberry Pi 連線到 Azure IoT Central 應用程式 (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

本文說明如何以裝置開發人員身分，將 Raspberry Pi 連線到使用 Python 程式設計語言的 Microsoft Azure IoT Central 應用程式。

## <a name="before-you-begin"></a>開始之前

若要完成本文中的步驟，您需要下列元件︰

* Azure IoT Central 應用程式是從**範例 Devkits** 應用程式範本建立而來。 如需詳細資訊，請參閱[建立應用程式快速入門](quick-deploy-iot-central.md)。
* 執行 Raspbian 作業系統的 Raspberry Pi 裝置。 Raspberry Pi 必須能夠連接到網際網路。 如需詳細資訊, 請參閱[設定您的 Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3)。

> [!TIP]
> 若要瞭解如何設定並聯機到 Raspberry Pi 裝置, 請流覽[開始使用 Raspberry pi](https://projects.raspberrypi.org/en/pathways/getting-started-with-raspberry-pi)

## <a name="sample-devkits-application"></a>**範例 Devkits** 應用程式

從**範例 Devkits** 應用程式範本建立的應用程式包含具有下列特性的 **Raspberry Pi**  裝置範本：

- 遙測，包括裝置將會收集的下列度量：
  - 濕度
  - 溫度
  - Pressure
  - 磁力計 (X, Y, Z)
  - 加速計 (X, Y, Z)
  - 迴轉儀 (X, Y, Z)
- 設定
  - 電壓
  - 目前的密碼
  - 風扇速度
  - IR 切換。
- 屬性
  - 模具編號裝置屬性
  - 位置雲端屬性

如需裝置範本設定的完整詳細資訊, 請參閱[Raspberry Pi 裝置範本詳細資料](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)。

## <a name="add-a-real-device"></a>新增真實裝置

在您的 Azure IoT Central 應用程式中, 從**Raspberry Pi**裝置範本新增實際裝置。 記下裝置連線詳細資料 (**範圍識別碼**、**裝置識別碼**和**主要金鑰**)。 如需詳細資訊，請參閱[將真實裝置新增至 Azure IoT Central 應用程式](tutorial-add-device.md)。

### <a name="configure-the-raspberry-pi"></a>設定 Raspberry Pi

下列步驟說明如何從 GitHub 下載 Python 應用程式範例並進行設定。 此應用程式範例會：

* 將遙測和屬性值傳送至 Azure IoT Central。
* 回應在 Azure IoT Central 中所做的設定變更。

1. 從 Raspberry Pi 桌面或使用 SSH 從遠端連線到您 Raspberry Pi 上的 shell 環境。

1. 執行下列命令來安裝 IoT Central Python 用戶端:

    ```sh
    pip install iotc
    ```

1. 下載範例 Python 程式碼:

    ```sh
    curl -O https://raw.githubusercontent.com/Azure/iot-central-firmware/master/RaspberryPi/app.py
    ```

1. 編輯您`app.py`所下載的檔案, 並`DEVICE_ID`將`SCOPE_ID`、和`PRIMARY/SECONDARY device KEY`預留位置取代為您先前記下的連接值。 儲存您的變更。

    > [!TIP]
    > 在 Raspberry Pi 上的 shell 中, 您可以使用**nano**或**vi**文字編輯器。

1. 使用下列命令來執行範例:

    ```sh
    python app.py
    ```

    您的 Raspberry Pi 會開始將遙測度量傳送至 Azure IoT Central。

1. 在 Azure IoT Central 應用程式中，您會看到在 Raspberry Pi 上執行的程式碼如何與應用程式互動：

    * 在真實裝置的 [量測] 頁面上，您會看到從 Raspberry Pi 傳送過來的遙測。
    * 在 [**屬性**] 頁面上, 您可以看到 [**骰子號碼**] 裝置屬性。
    * 在 [**設定**] 頁面上, 您可以變更 Raspberry Pi 上的設定, 例如電壓和風扇速度。 當 Raspberry Pi 認可變更時, 設定會顯示為已**同步**處理。

## <a name="raspberry-pi-device-template-details"></a>Raspberry Pi 裝置範本詳細資料

從**範例 Devkits** 應用程式範本建立的應用程式包含具有下列特性的 **Raspberry Pi**  裝置範本：

### <a name="telemetry-measurements"></a>遙測量測

| 欄位名稱     | 單位  | 最小值 | 最大值 | 小數位數 |
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

| Display name | 欄位名稱 | 單位 | 小數位數 | 最小值 | 最大值 | 初始 |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 電壓      | setVoltage | 伏特 | 0              | 0       | 240     | 0       |
| 目前的密碼      | setCurrent | 安培  | 0              | 0       | 100     | 0       |
| 風扇速度    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

切換設定

| Display name | 欄位名稱 | 開啟文字 | 關閉文字 | 初始 |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | 開啟      | 關閉      | 關閉     |

### <a name="properties"></a>屬性

| Type            | Display name | 欄位名稱 | 資料類型 |
| --------------- | ------------ | ---------- | --------- |
| 裝置屬性 | 模具編號   | dieNumber  | 號    |
| 文字            | Location     | 位置   | N/A       |

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何將 Raspberry Pi 連線到您的 Azure IoT Central 應用程式, 建議的下一個步驟是瞭解如何為您自己的 IoT 裝置[設定自訂裝置範本](howto-set-up-template.md)。
