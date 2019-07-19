---
title: 在 Linux ARM32 上安裝 Azure IoT Edge | Microsoft Docs
description: ARM32 裝置 (如 Raspberry PI) 上 Linux 的 Azure IoT Edge 安裝指示
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: kgremban
ms.openlocfilehash: 72349597a2831a64cb11ff66b30e6948f34cc921
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/15/2019
ms.locfileid: "68224700"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>在 Linux (ARM32v7/armhf) 上安裝 Azure IoT Edge 執行階段

Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 此執行階段可以部署在像 Raspberry Pi 一樣小或像工業伺服器一樣大的裝置上。 利用 IoT Edge 執行階段設定裝置之後，您就可以開始從雲端將商務邏輯部署給它。 

若要深入了解 IoT Edge 執行階段的運作方式，以及會包含哪些元件，請參閱[了解 Azure IoT Edge 執行階段及其架構](iot-edge-runtime.md)。

本文列出在 Linux ARM32v7/armhf IoT Edge 裝置上安裝 Azure IoT Edge 執行時間的步驟。 例如，這些步驟適用於 Raspberry Pi 裝置。 如需支援的 ARM32 作業系統清單, 請參閱[Azure IoT Edge 支援的系統](support.md#operating-systems)。 

>[!NOTE]
>Linux 軟體存放庫中的套件受限於每個套件中的授權條款 (/usr/share/doc/package-name  )。 在使用套件之前，請先閱讀授權條款。 安裝及使用套件即表示接受這些授權條款。 如果您不同意授權條款，請勿使用封裝。

## <a name="install-the-latest-version"></a>安裝最新版本

請使用下列各節, 將最新版本的 Azure IoT Edge 服務安裝到 Linux ARM 裝置上。 

### <a name="install-the-container-runtime"></a>安裝容器執行階段

Azure IoT Edge 會依賴 [OCI 相容](https://www.opencontainers.org/)的容器執行階段。 針對生產案例，強烈建議您使用下方所提供的 [Moby 型](https://mobyproject.org/)引擎。 它是 Azure IoT Edge 正式支援的唯一容器引擎。 Docker CE/EE 容器映像與 Moby 架構的執行階段相容。

下列命令會安裝以 Moby 為基礎的引擎和命令列介面 (CLI)。 CLI 對於開發相當有用，但是對於生產部署則是選擇性的。

```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f
```

### <a name="install-the-iot-edge-security-daemon"></a>安裝 IoT Edge 安全性精靈

**IoT Edge 安全性守護**程式會在 IoT Edge 裝置上提供並維護安全性標準。 此精靈會在每次開機時啟動，並藉由啟動 IoT Edge 執行階段讓裝置進入啟動程序。 


```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

成功安裝 IoT Edge 之後, 輸出將會提示您更新設定檔。 請遵循[設定 Azure IoT Edge 安全性守護](#configure-the-azure-iot-edge-security-daemon)程式一節中的步驟, 完成布建您的裝置。 

## <a name="install-a-specific-version"></a>安裝特定版本

如果您想要安裝特定版本的 Azure IoT Edge, 可以直接從 IoT Edge GitHub 存放庫將元件檔案設為目標。 使用前幾`curl`節所列的相同命令, 將所有 IoT Edge 元件都放在您的裝置上: Moby 引擎和 CLI、libiothsm, 最後是 IoT Edge 的安全性守護程式。 唯一的差別在於, 您會以直接指向您要使用之每個元件版本的連結來取代**Aka.ms** url。

流覽至[Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases), 並尋找您要設為目標的發行版本。 展開版本的 [**資產**] 區段, 然後選擇符合您 IoT Edge 裝置架構的檔案。 每個 IoT Edge 版本都包含**iotedge**和**libiothsm**檔案。 並非所有版本都包含**moby 引擎**或**moby cli**。 如果您還沒有安裝 Moby 容器引擎, 請查看較舊的版本, 直到找到其中一個包含 Moby 元件的發行。 

成功安裝 IoT Edge 之後, 輸出將會提示您更新設定檔。 請依照下一節中的步驟完成布建您的裝置。 

## <a name="configure-the-azure-iot-edge-security-daemon"></a>設定 Azure IoT Edge 安全性守護程式

設定 IoT Edge 執行階段，以連結您的實體裝置與 Azure IoT 中樞中存在的裝置身分識別。 

您可以使用 `/etc/iotedge/config.yaml` 上的組態檔來設定精靈。 檔案預設為寫入保護, 因此您可能需要較高的許可權才能進行編輯。

單一 IoT Edge 裝置可以使用 IoT 中樞所提供的裝置連接字串以手動方式佈建。 或者，您可以使用裝置佈建服務來自動佈建裝置，這在您有許多裝置要佈建時將很有幫助。 請根據您選擇的佈建方式，選擇適當的安裝指令碼。 

### <a name="option-1-manual-provisioning"></a>選項 1：手動佈建

若要手動布建裝置, 您需要提供[裝置連接字串](how-to-register-device-portal.md), 您可以在 IoT 中樞註冊新的 IoT Edge 裝置來建立。

開啟組態檔。 

```bash
sudo nano /etc/iotedge/config.yaml
```

尋找檔案的布建設定, 並取消批註**手動**布建設定一節。 以來自 IoT Edge 裝置的連接字串更新 **device_connection_string** 的值。 請確定已將任何其他布建區段標記為批註。

```yaml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
# DPS TPM provisioning configuration
# provisioning:
#   source: "dps"
#   global_endpoint: "https://global.azure-devices-provisioning.net"
#   scope_id: "{scope_id}"
#   attestation:
#     method: "tpm"
#     registration_id: "{registration_id}"
```

儲存並關閉檔案。 

`CTRL + X`, `Y`, `Enter`

在組態檔中輸入佈建資訊之後，請重新啟動精靈：

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>選項 2：自動佈建

若要自動佈建裝置，[請設定裝置佈建服務並擷取您的裝置註冊識別碼](how-to-auto-provision-simulated-device-linux.md)。 使用自動布建時, IoT Edge 支援數種證明機制, 但您的硬體需求也會影響您的選擇。 例如, Raspberry Pi 裝置預設不會隨附信賴平臺模組 (TPM) 晶片。

開啟組態檔。 

```bash
sudo nano /etc/iotedge/config.yaml
```

尋找檔案的布建設定, 並取消批註適用于您證明機制的區段。 例如, 使用 TPM 證明時, 請將**scope_id**和**registration_id**的值更新為 IoT 中樞裝置布建服務中的值, 以及使用具有 TPM 的 IoT Edge 裝置。

```yaml
   # Manual provisioning configuration
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     attestation:
       method: "tpm"
       registration_id: "{registration_id}"
```

儲存並關閉檔案。 

`CTRL + X`, `Y`, `Enter`

在組態檔中輸入佈建資訊之後，請重新啟動精靈：

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>確認安裝成功

如果您在上一節中使用**手動設定**步驟，IoT Edge 執行階段應會在您的裝置上成功佈建並執行。 或者, 如果您使用**自動**設定步驟, 則必須完成一些額外的步驟, 讓執行時間可以代表您向 IoT 中樞註冊您的裝置。 如需後續步驟, 請參閱[在 Linux 虛擬機器上建立及布建模擬 TPM IoT Edge 裝置](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)。

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

在資源受限的裝置上，強烈建議將 OptimizeForPerformance  環境變數設定為 false  ，如[疑難排解指南](troubleshoot.md#stability-issues-on-resource-constrained-devices)中的指示所述。

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

如果您有 IoT Edge 執行時間正確安裝的問題, 請參閱[疑難排解](troubleshoot.md#stability-issues-on-resource-constrained-devices)頁面。

若要將現有安裝更新為最新版的 IoT Edge，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。
