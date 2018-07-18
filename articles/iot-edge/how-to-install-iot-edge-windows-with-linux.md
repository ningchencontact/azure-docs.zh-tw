---
title: 如何在具有 Linux 容器的 Windows 上安裝 Azure IoT Edge |Microsoft Docs
description: 在具有 Linux 容器的 Windows 上安裝 Azure IoT Edge 的指示
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: cd517d7e652b38c7ecf28a17657936698416413a
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034434"
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

Azure IoT Edge 需要 [OCI 相容][lnk-oci]容器執行階段 (例如 Docker)。 

您可以使用[適用於 Windows 的 Docker][lnk-docker-for-windows] 來進行對開發與測試。 請確定適用於 Windows 的 Docker 是[設定為使用 Linux 容器][lnk-docker-config]

## <a name="install-the-azure-iot-edge-security-daemon"></a>安裝 Azure IoT Edge 安全性精靈

>[!NOTE]
>使用 Azure IoT Edge 軟體套件時，必須遵守套件中的授權條款 (位於 [LICENSE] 目錄)。 使用套件之前，請先閱讀授權條款。 安裝及使用套件即表示您接受這些授權條款。 如果您不同意授權條款，請勿使用套件。

### <a name="download-the-edge-daemon-package-and-install"></a>下載 Edge 精靈套件並安裝

在系統管理員 PowerShell 視窗中，執行下列命令：

```powershell
Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
rmdir C:\ProgramData\iotedge\iotedged-windows
$env:Path += ";C:\ProgramData\iotedge"
SETX /M PATH "$env:Path"
```

使用以下命令來安裝 vcruntime：

```powershell
Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
.\vc_redist.exe /quiet /norestart
 ```

建立並啟動 *iotedge* 服務：

```powershell
New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
Start-Service iotedge
```

為服務所使用的連接埠新增防火牆例外：

```powershell
New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
```

建立具有下列內容的 **iotedge.reg** 檔案，然後按兩下此檔案或使用 `reg import iotedge.reg` 命令來將它匯入至 Windows 登錄：

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>設定 Azure IoT Edge 安全性精靈

精靈可以使用位於 `C:\ProgramData\iotedge\config.yaml` 的設定檔進行設定。Edge 裝置可以使用[裝置連接字串][lnk-dcs]手動設定<!--[automatically via Device Provisioning Service][lnk-dps] or-->。

針對手動設定，在 **config.yaml** 的 **provisioning:** 區段輸入裝置連接字串。

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

在 PowerShell 中使用 `hostname` 命令來取得 Edge 裝置的名稱，並在設定 yaml 中，將它設定為 **hostname:** 的值。 例如︰

```yaml
  ###############################################################################
  # Edge device hostname
  ###############################################################################
  #
  # Configures the environment variable 'IOTEDGE_GATEWAYHOSTNAME' injected into
  # modules.
  #
  ###############################################################################

  hostname: "edgedevice-1"
```

接下來，我們要在設定的 **connect:** 區段中，為 **workload_uri** 和 **management_uri** 提供 IP 位址和連接埠。

針對 IP 位址，請在 PowerShell 視窗中輸入 `ipconfig` 並選取 **vEthernet (DockerNAT)**` 介面的 IP 位址 (如下列範例所示，同時您系統上的 IP 位址可能會不同)：

![DockerNat][img-docker-nat]

```yaml
connect:
  management_uri: "http://10.0.75.1:15580"
  workload_uri: "http://10.0.75.1:15581"
```

在設定的 **listen:** 區段中輸入相同的位址。 例如︰

```yaml
listen:
  management_uri: "http://10.0.75.1:15580"
  workload_uri: "http://10.0.75.1:15581"
```

在 PowerShell 視窗中，建立一個具有 **management_uri** 位址的環境變數 **IOTEDGE_HOST**，範例：

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://10.0.75.1:15580")
```

最後，確定 **moby_runtime:** 底下的 **network:** 設定已取消註解並設定為 **azure-iot-edge**

```yaml
moby_runtime:
  docker_uri: "npipe://./pipe/docker_engine"
  network: "azure-iot-edge"
```

儲存設定檔並重新啟動服務：

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

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
  sort-object @{Expression="TimeCreated";Descending=$false}
```

然後，使用以下命令來列出執行中的模組：

```powershell
iotedge list
```

## <a name="next-steps"></a>後續步驟

如果您有 Edge 執行階段是否正確安裝的問題，請參閱[疑難排解][lnk-trouble]頁面。


<!-- Images -->
[img-docker-nat]: ./media/how-to-install-iot-edge-windows-with-linux/dockernat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows

