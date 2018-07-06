---
title: 如何在 Linux 上安裝 Azure IoT Edge | Microsoft Docs
description: ARM32 上 Linux 的 Azure IoT Edge 安裝指示
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: ad70fcc6b9779cb33772a3fce2fb11b4cec804ee
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062593"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>在 Linux (ARM32v7/armhf) 上安裝 Azure IoT Edge 執行階段

在所有 IoT Edge 裝置上都有部署 Azure IoT Edge 執行階段。 它有三個元件。 **IoT Edge 安全性精靈**提供及維護 Edge 裝置的安全性標準。 精靈在每次開機時都會啟動，並藉由啟動 IoT Edge 代理程式，讓裝置進入啟動程序。 **IoT Edge 代理程式**有助於在 Edge 裝置 (包括 IoT Edge 中樞) 上部署及監視模組。 **IoT Edge 中樞**會管理 IoT Edge 裝置上的模組通訊，以及裝置與 IoT 中樞之間的通訊。

本文列出在 Linux ARM32v7/armhf Edge 裝置 (例如 Raspberry Pi) 上安裝 Azure IoT Edge 執行階段的步驟。

>[!NOTE]
>Linux 軟體存放庫中的套件受限於每個套件中的授權條款 (/usr/share/doc/package-name)。 在使用套件之前，請先閱讀授權條款。 安裝及使用套件即表示接受這些授權條款。 如果您不同意授權條款，請勿使用套件。

## <a name="install-the-container-runtime"></a>安裝容器執行階段

Azure IoT Edge 依賴 [OCI 相容][lnk-oci] 容器執行階段 (例如 Docker)。 如果您已在 Edge 裝置上安裝 Docker CE/EE，則可以繼續使用它，透過 Azure IoT Edge 進行開發與測試。 

針對生產案例，強烈建議您使用下方所提供的 [Moby 型][lnk-moby]引擎。 它是 Azure IoT Edge 正式支援的唯一容器引擎。 Docker CE/EE 容器映像與 Moby 執行階段完全相容。

下列命令會安裝 Moby 引擎及命令列介面 (CLI)。 CLI 對於開發相當有用，但是對於生產部署則是選擇性的。

```cmd/sh

# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f

```

## <a name="install-the-iot-edge-security-daemon"></a>安裝 IoT Edge 安全性精靈

```cmd/sh
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>設定 Azure IoT Edge 安全性精靈

精靈可以使用位於 `/etc/iotedge/config.yaml` 的組態檔進行設定。Edge 裝置可以使用[裝置連接字串][lnk-dcs]手動設定<!--[automatically via Device Provisioning Service][lnk-dps] or-->。

針對手動設定，請在 **config.yaml** 的 [佈建] 區段中，輸入裝置連接字串

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

*檔案預設有寫入保護，您可能必須使用 `sudo` 來編輯。例如：`sudo nano /etc/iotedge/config.yaml`*

在組態中輸入佈建資訊之後，請重新啟動精靈：

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>確認安裝成功

您可以使用以下項目，檢查 IoT Edge 精靈的狀態：

```cmd/sh
systemctl status iotedge
```

使用以下項目檢查精靈記錄：

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

並使用以下項目列出執行中的模組：

```cmd/sh
iotedge list
```

## <a name="next-steps"></a>後續步驟

如果您有 Edge 執行階段是否安裝正確的問題，請參閱[疑難排解][lnk-trouble]頁面。

<!-- Links -->
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md