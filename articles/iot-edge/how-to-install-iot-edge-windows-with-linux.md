---
title: 如何在具有 Linux 容器的 Windows 上安裝 Azure IoT Edge |Microsoft Docs
description: 在具有 Linux 容器的 Windows 上安裝 Azure IoT Edge 的指示
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/06/2018
ms.author: kgremban
ms.openlocfilehash: ea576c0d434d4db7077fc41bc1f5bbbc89e7779e
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576642"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>在 Windows 上安裝要與 Linux 容器搭配使用的 Azure IoT Edge 執行階段

Azure IoT Edge 執行階段是部署到所有 IoT Edge 裝置上。 它有三個元件。 **IoT Edge 安全性精靈**提供及維護 Edge 裝置的安全性標準。 精靈會在每次開機時啟動，並且透過啟動 IoT Edge 代理程式讓裝置進入啟動程序。 **IoT Edge 代理程式**有助於在 Edge 裝置 (包括 IoT Edge 中樞) 上部署及監視模組。 **IoT Edge 中樞**會管理 IoT Edge 裝置上的模組通訊，以及裝置與 IoT 中樞之間的通訊。

本文列出在 Windows x64 (AMD/Intel) 系統上安裝 Azure IoT Edge 執行階段的步驟。 Windows 支援目前為預覽版。

>[!NOTE]
針對 Azure IoT Edge 而言，在 Windows 系統上使用 Linux 容器不是建議或支援的生產設定。 不過，這很適合用於開發與測試用途。

## <a name="supported-windows-versions"></a>支援的 Windows 版本
使用 Linux 容器時，可以在下列 Windows 版本上進行 Azure IoT Edge 的開發與測試：
  * Windows 10 或更新版本的電腦作業系統。
  * Windows Server 2016 或新的伺服器作業系統。

## <a name="install-the-container-runtime"></a>安裝容器執行階段 

Azure IoT Edge 須依賴 [OCI 相容][lnk-oci]的容器執行階段 (例如 Docker)。 

您可以使用[適用於 Windows 的 Docker][lnk-docker-for-windows] 來進行開發與測試。 將適用於 Windows 的 Docker 設定為[使用 Linux 容器][lnk-docker-config]

## <a name="install-the-azure-iot-edge-security-daemon"></a>安裝 Azure IoT Edge 安全性精靈

>[!NOTE]
>使用 Azure IoT Edge 軟體套件時，必須遵守封裝中的授權條款 (位於 [LICENSE] 目錄)。 使用封裝前，請先閱讀授權條款。 安裝及使用封裝即表示您接受這些授權條款。 如果您不同意授權條款，請勿使用封裝。

單一 IoT Edge 裝置可以使用 IoT 中樞所提供的裝置連接字串以手動方式佈建。 或者，您可以使用裝置佈建服務來自動佈建裝置，這在您有許多裝置要佈建時將很有幫助。 請根據您選擇的佈建方式，選擇適當的安裝指令碼。 

### <a name="install-and-manually-provision"></a>安裝並手動佈建

1. 依照[註冊新的 Azure IoT Edge 裝置][lnk-dcs]中的步驟註冊您的裝置，並擷取裝置連接字串。 

2. 在您的 IoT Edge 裝置上，以系統管理員身分執行 PowerShell。 

3. 下載並安裝 IoT Edge 執行階段。 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Linux
   ```

4. 在出現 **DeviceConnectionString** 的提示時，請提供您從 IoT 中樞擷取的連接字串。 請勿在連接字串兩側加上引號。 

### <a name="install-and-automatically-provision"></a>安裝並自動佈建

1. 依照[在 Windows 上建立及佈建模擬 TPM Edge 裝置][lnk-dps]中的步驟設定裝置佈建服務並擷取其 [範圍識別碼]、模擬 TPM 裝置並擷取其 [註冊識別碼]，然後建立個別註冊。 在您的 IoT 中樞註冊裝置之後，請繼續進行安裝。  

   >[!TIP]
   >在安裝和測試期間，請將執行 TPM 模擬器的視窗保持開啟。 

2. 在您的 IoT Edge 裝置上，以系統管理員身分執行 PowerShell。 

3. 下載並安裝 IoT Edge 執行階段。 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Dps -ContainerOs Linux
   ```

4. 出現提示時，請提供佈建服務和裝置的 [範圍識別碼] 和 [註冊識別碼]。

## <a name="verify-successful-installation"></a>確認安裝成功

透過以下命令，檢查 IoT Edge 服務的狀態： 

```powershell
Get-Service iotedge
```

使用以下命令，檢查最後 5 分鐘的服務記錄檔：

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

並使用以下項目列出執行中的模組：

```powershell
iotedge list
```

## <a name="next-steps"></a>後續步驟

現在您已佈建 IoT Edge 裝置並安裝執行階段，接下來您可以[部署 IoT Edge 模組][lnk-modules]。

如果您有 Edge 執行階段是否正確安裝的問題，請參閱[疑難排解][lnk-trouble]頁面。


<!-- Images -->
[img-docker-nat]: ./media/how-to-install-iot-edge-windows-with-linux/dockernat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-windows.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows
[lnk-modules]: how-to-deploy-modules-portal.md
