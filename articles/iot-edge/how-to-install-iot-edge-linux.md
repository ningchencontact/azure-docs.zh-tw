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
ms.openlocfilehash: 477fa7e10fab0afadb28d7749009f87e82d9b6ba
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035389"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>在 Linux (x64) 上安裝 Azure IoT Edge 執行階段

Azure IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 它有三個元件。 **IoT Edge 安全性精靈**提供及維護 Edge 裝置的安全性標準。 精靈會在每次開機時啟動，並且透過啟動 IoT Edge 代理程式讓裝置進入啟動程序。 **IoT Edge 代理程式**有助於在 Edge 裝置 (包括 IoT Edge 中樞) 上部署及監視模組。 **IoT Edge 中樞**會管理 IoT Edge 裝置上的模組通訊，以及裝置與 IoT 中樞之間的通訊。

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

# Perform apt upgrade
sudo apt-get upgrade
```

## <a name="install-the-container-runtime"></a>安裝容器執行階段 

Azure IoT Edge 會依賴 [OCI 相容][lnk-oci]的容器執行階段。 針對生產案例，強烈建議您使用下方所提供的 [Moby 型][lnk-moby]引擎。 它是 Azure IoT Edge 正式支援的唯一容器引擎。 Docker CE/EE 容器映像與 Moby 執行階段相容。

更新 apt-get。

```bash
sudo apt-get update
```

安裝 Moby 引擎及命令列介面 (CLI)。 CLI 對於開發相當有用，但是對於生產部署則是選擇性的。*

```bash
sudo apt-get install moby-engine
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>安裝 Azure IoT Edge 安全性精靈

如果 **iothsmlib** 尚未存在，以下命令也會安裝標準版本。

```bash
sudo apt-get update
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>設定 Azure IoT Edge 安全性精靈

您可以使用 `/etc/iotedge/config.yaml` 上的組態檔來設定精靈。 此檔案預設有防寫保護，您可能需要提高的權限才能加以編輯。

```bash
sudo nano /etc/iotedge/config.yaml
```

Edge 裝置可以使用[裝置連接字串][lnk-dcs]手動設定，或[透過裝置佈建服務自動設定][lnk-dps]。

* 若要手動設定，請取消註解**手動**佈建模式。 以來自 IoT Edge 裝置的連接字串更新 **device_connection_string** 的值。

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning: 
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

* 若要自動設定，請取消註解 **dps** 佈建模式。 請將 **scope_id** 和 **registration_id** 的值更新為 IoT Hub DPS 執行個體和具有 TPM 的 IoT Edge 裝置中的值。 

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning: 
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

在組態中輸入佈建資訊之後，請重新啟動精靈：

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>確認安裝成功

如果您在上一節中使用**手動設定**步驟，IoT Edge 執行階段應會在您的裝置上成功佈建並執行。 如果您使用**自動設定**步驟，則必須完成一些額外的步驟，讓執行階段可代表您向您的 IoT 中樞註冊裝置。 如需後續步驟，請參閱[在 Linux 虛擬機器上建立及佈建模擬 TPM Edge 裝置](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)。

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
sudo iotedge list
```

## <a name="next-steps"></a>後續步驟

如果您有 Edge 執行階段是否正確安裝的問題，請參閱[疑難排解][lnk-trouble]頁面。

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
