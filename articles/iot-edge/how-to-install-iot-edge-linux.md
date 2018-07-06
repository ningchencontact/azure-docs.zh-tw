---
title: 如何在 Linux 上安裝 Azure IoT Edge | Microsoft Docs
description: Linux 上的 Azure IoT Edge 安裝指示
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 43f82341a3cc9d2163afd35e42864aaa7866b1b2
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034667"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>在 Linux (x64) 上安裝 Azure IoT Edge 執行階段

Azure IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 它有三個元件。 **IoT Edge 安全性精靈**提供及維護 Edge 裝置的安全性標準。 精靈會在每次開機時啟動，並且藉由啟動 IoT Edge 代理程式讓裝置進入啟動程序。 **IoT Edge 代理程式**有助於在 Edge 裝置 (包括 IoT Edge 中樞) 上部署及監視模組。 **IoT Edge 中樞**會管理 IoT Edge 裝置上的模組通訊，以及裝置與 IoT 中樞之間的通訊。

本文列出在 Linux x64 (Intel/AMD) Edge 裝置上安裝 Azure IoT Edge 執行階段的步驟。

>[!NOTE]
>Linux 軟體存放庫中的套件受限於每個套件中的授權條款 (/usr/share/doc/package-name)。 在使用套件之前，請先閱讀授權條款。 安裝及使用套件即表示接受這些授權條款。 如果您不同意授權條款，請勿使用套件。

## <a name="register-microsoft-key-and-software-repository-feed"></a>註冊 Microsoft 金鑰和軟體存放庫摘要

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="debian-9"></a>Debian 9

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/debian/9/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

## <a name="install-the-container-runtime"></a>安裝容器執行階段 

Azure IoT Edge 依賴 [OCI 相容][lnk-oci] 容器執行階段 (例如 Docker)。 如果您已在 Edge 裝置上安裝 Docker CE/EE，您可以繼續使用它，透過 Azure IoT Edge 進行開發與測試。 

針對生產案例，強烈建議您使用下方所提供的 [Moby 型][lnk-moby]引擎。 它是 Azure IoT Edge 正式支援的唯一容器引擎。 Docker CE/EE 容器映像與 Moby 執行階段完全相容。

*下列指示會安裝 moby 引擎及命令列介面 (CLI)。CLI 對於開發相當有用，但是對於生產部署則是選擇性的。*

```cmd/sh
sudo apt-get update
sudo apt-get install moby-engine
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>安裝 Azure IoT Edge 安全性精靈

如果 **iothsmlib** 尚未存在，以下命令也會安裝標準版本。

```cmd/sh
sudo apt-get update
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>設定 Azure IoT Edge 安全性精靈

精靈可以使用位於 `/etc/iotedge/config.yaml` 的組態檔進行設定。Edge 裝置可以使用[裝置連接字串][lnk-dcs]手動設定<!--[automatically via Device Provisioning Service][lnk-dps] or-->。

針對手動設定，在 **config.yaml** 的 [佈建] 區段輸入裝置連接字串

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

*檔案預設有寫入保護，您可能必須使用 `sudo` 來編輯。例如 `sudo nano /etc/iotedge/config.yaml`*

在組態中輸入佈建資訊之後，重新啟動精靈：

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

以及，使用以下項目列出執行中的模組：

```cmd/sh
iotedge list
```

## <a name="next-steps"></a>後續步驟

如果您有 Edge 執行階段是否正確安裝的問題，請參閱[疑難排解][lnk-trouble]頁面。

<!-- Links -->
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
