---
title: 在 Linux 上安裝 Azure IoT Edge | Microsoft Docs
description: 執行 Ubuntu 的 Linux AMD64 裝置的 Azure IoT Edge 安裝指示
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 86ca3080229f2a286e8aa4725fe13c40e2a38549
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67054286"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>在 Linux (x64) 上安裝 Azure IoT Edge 執行階段

Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 此執行階段可以部署在像 Raspberry Pi 一樣小或像工業伺服器一樣大的裝置上。 利用 IoT Edge 執行階段設定裝置之後，您就可以開始從雲端將商務邏輯部署給它。

若要進一步了解，請參閱[了解 Azure IoT Edge 執行階段及其架構](iot-edge-runtime.md)。

本文列出您在 Ubuntu Linux x64 (Intel/AMD) 上安裝 Azure IoT Edge 執行階段的步驟 IoT Edge 裝置。 請參閱[Azure IoT Edge 支援系統](support.md#operating-systems)取得一份支援 AMD64 的作業系統。

> [!NOTE]
> Linux 軟體存放庫中的套件受限於每個套件中的授權條款 (/usr/share/doc/package-name  )。 在使用套件之前，請先閱讀授權條款。 安裝及使用套件即表示接受這些授權條款。 如果您不同意授權條款，請勿使用套件。

## <a name="register-microsoft-key-and-software-repository-feed"></a>註冊 Microsoft 金鑰和軟體存放庫摘要

準備您的裝置之 IoT edge 執行階段安裝。


安裝的存放庫設定。 選擇  **16.04**或是**18.04**視您的 Ubuntu 版本的程式碼片段：

> [!NOTE]
> 請確定您的程式碼片段從方塊中選擇正確的程式碼針對您的 Ubuntu 版本。

* 針對**Ubuntu 16.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   ```

* 針對**Ubuntu 18.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
   ```
   
複製產生的清單。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

安裝 Microsoft GPG 公開金鑰

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

## <a name="install-the-container-runtime"></a>安裝容器執行階段

Azure IoT Edge 會依賴 [OCI 相容](https://www.opencontainers.org/)的容器執行階段。 生產案例，我們建議您使用[白鯨型](https://mobyproject.org/)下面提供的引擎。 它是 Azure IoT Edge 正式支援的唯一容器引擎。 Docker CE/EE 容器映像與 Moby 執行階段相容。

執行 apt 更新。

   ```bash
   sudo apt-get update
   ```

安裝 Moby 引擎。

   ```bash
   sudo apt-get install moby-engine
   ```

安裝 Moby 命令列介面 (CLI)。 CLI 對於開發相當有用，但是對於生產部署則是選擇性的。

   ```bash
   sudo apt-get install moby-cli
   ```

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>請確認您的 Linux 核心的白鯨相容性

許多內嵌的裝置製造商提供裝置映像，其中包含自訂的 Linux 核心可能會遺失所需的容器執行階段相容性功能。 如果您遇到問題時安裝的建議[白鯨](https://github.com/moby/moby)容器的執行階段，您可以疑難排解您 Linux 核心設定，使用[核取設定](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh)中提供的指令碼官方[白鯨 Github 存放庫](https://github.com/moby/moby)藉由在裝置上執行下列命令。

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

這會提供詳細的輸出，其中包含白鯨執行階段所使用的核心功能的狀態。 您會想要確保所有項目底下`Generally Necessary`和`Network Drivers`可確保您的核心是與白鯨執行階段完全相容。  如果您已識別任何遺漏的功能，您可能會重建您的核心，從來源，然後選取包含相關聯的模組，在適當的核心.config 檔案中啟用它們。同樣地，如果您使用的核心組態產生器，例如 defconfig 或 menuconfig，您必須尋找及啟用個別功能，並據以重建您的核心。  一旦您已部署了新修改的核心，執行一次以確認您已識別的功能已成功啟用的核取設定指令碼。

## <a name="install-the-azure-iot-edge-security-daemon"></a>安裝 Azure IoT Edge 安全性精靈

**IoT Edge 安全性精靈**提供及維護 IoT Edge 裝置上的安全性標準。 此精靈會在每次開機時啟動，並藉由啟動 IoT Edge 執行階段讓裝置進入啟動程序。

如果 **iothsmlib** 尚不存在，安裝命令也會安裝其標準版本。

執行 apt 更新。

   ```bash
   sudo apt-get update
   ```

安裝安全性精靈。 套件會安裝在 `/etc/iotedge/`。

   ```bash
   sudo apt-get install iotedge
   ```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>設定 Azure IoT Edge 安全性精靈

設定 IoT Edge 執行階段，以連結您的實體裝置與 Azure IoT 中樞中存在的裝置身分識別。

您可以使用 `/etc/iotedge/config.yaml` 上的組態檔來設定精靈。 此檔案預設有防寫保護，您可能需要提高的權限才能加以編輯。

單一 IoT Edge 裝置可以使用 IoT 中樞所提供的裝置連接字串以手動方式佈建。 或者，您可以使用裝置佈建服務來自動佈建裝置，這在您有許多裝置要佈建時將很有幫助。 請根據您選擇的佈建方式，選擇適當的安裝指令碼。

### <a name="option-1-manual-provisioning"></a>選項 1：手動佈建

若要手動佈建裝置，您需要提供[裝置連接字串](how-to-register-device-portal.md)，在您的 IoT 中樞註冊新裝置即可建立該字串。

開啟組態檔。

```bash
sudo nano /etc/iotedge/config.yaml
```

尋找佈建設定檔的區段。 取消註解**手動**佈建模式，並確定佈建模式 dps 標記為註解。以來自 IoT Edge 裝置的連接字串更新 **device_connection_string** 的值。

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

儲存並關閉檔案。

   `CTRL + X`, `Y`, `Enter`

在組態檔中輸入佈建資訊之後，請重新啟動精靈：

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>選項 2：自動佈建

若要自動佈建裝置，[請設定裝置佈建服務並擷取您的裝置註冊識別碼](how-to-auto-provision-simulated-device-linux.md)。 自動佈建只適用於具有信賴平台模組 (TPM) 晶片的裝置。 例如，Raspberry Pi 裝置預設未隨附 TPM。

開啟組態檔。

```bash
sudo nano /etc/iotedge/config.yaml
```

尋找佈建設定檔的區段。 取消註解**dps**佈建模式 」 和 「 手動 」 一節的註解。 請將 **scope_id** 和 **registration_id** 的值更新為 IoT 中樞裝置佈建服務和具有 TPM 的 IoT Edge 裝置中的值。

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

儲存並關閉檔案。

   `CTRL + X`, `Y`, `Enter`

在組態檔中輸入佈建資訊之後，請重新啟動精靈：

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>確認安裝成功

如果您在上一節中使用**手動設定**步驟，IoT Edge 執行階段應會在您的裝置上成功佈建並執行。 如果您使用**自動設定**步驟，則必須完成一些額外的步驟，讓執行階段可代表您向您的 IoT 中樞註冊裝置。 下一個步驟，請參閱[建立及佈建模擬 TPM IoT Edge 裝置上的 Linux 虛擬機器](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)。

您可以使用以下項目，檢查 IoT Edge 精靈的狀態：

```bash
systemctl status iotedge
```

使用以下項目檢查精靈記錄：

```bash
journalctl -u iotedge --no-pager --no-full
```

並使用以下項目列出執行中的模組：

```bash
sudo iotedge list
```

## <a name="tips-and-suggestions"></a>祕訣與建議

您必須要有提高的權限才能執行 `iotedge` 命令。 安裝執行階段之後，請登出您的電腦並重新登入，以自動更新您的權限。 在那之前，請在任何 `iotedge` 命令前面使用 **sudo**。

在資源受限的裝置上，強烈建議將 OptimizeForPerformance  環境變數設定為 false  ，如[疑難排解指南](troubleshoot.md)中的指示所述。

如果您的網路具有 Proxy 伺服器，請遵循[設定您的 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)中的步驟。

## <a name="uninstall-iot-edge"></a>解除安裝 IoT Edge

如果您想要從 Linux 裝置中移除 IoT Edge 安裝，請在命令列中使用下列命令。

移除 IoT Edge 執行階段。

```bash
sudo apt-get remove --purge iotedge
```

IoT Edge 執行階段移除後，它所建立的容器隨即停止，但仍會存在於您的裝置上。 檢視所有容器以查看哪些容器保留下來。

```bash
sudo docker ps -a
```

刪除您裝置中的容器，包括兩個執行階段容器。

```bash
sudo docker rm -f <container name>
```

最後，移除裝置中的容器執行階段。

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>後續步驟

現在您已佈建 IoT Edge 裝置並安裝執行階段，接下來您可以[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)。

如果您有正確安裝 IoT Edge 執行階段問題，請參閱[疑難排解](troubleshoot.md)頁面。

若要將現有安裝更新為最新版的 IoT Edge，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。
