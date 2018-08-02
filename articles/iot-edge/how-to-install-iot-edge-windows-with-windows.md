---
title: 如何在具有 Windows 容器的 Windows 上安裝 Azure IoT Edge |Microsoft Docs
description: 在具有 Windows 容器的 Windows 上安裝 Azure IoT Edge 的指示
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 5fc1163f590b2408fca913e35e57f014424b225c
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308393"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-windows-containers"></a>在 Windows 上安裝要與 Windows 容器搭配使用的 Azure IoT Edge 執行階段

Azure IoT Edge 執行階段會部署到所有 IoT Edge 裝置上。 它有三個元件。 **IoT Edge 安全性精靈**提供及維護 Edge 裝置的安全性標準。 精靈會在每次開機時啟動，並且透過啟動 IoT Edge 代理程式讓裝置進入啟動程序。 **IoT Edge 代理程式**有助於在 Edge 裝置 (包括 IoT Edge 中樞) 上部署及監視模組。 **IoT Edge 中樞**會管理 IoT Edge 裝置上的模組通訊，以及裝置與 IoT 中樞之間的通訊。

本文列出在 Windows x64 (AMD/Intel) 系統上安裝 Azure IoT Edge 執行階段的步驟。 

Windows 支援目前為預覽版。

## <a name="supported-windows-versions"></a>支援的 Windows 版本
具有 Windows 容器的 Azure IoT Edge 可搭配下列項目使用：
  * 搭載 2018 年 4 月更新 (版本 17134) 的 Windows 10/IoT 企業版/IoT 核心版。
  * Windows Server 1803

## <a name="install-the-container-runtime"></a>安裝容器執行階段 

>[!NOTE]
>如需將容器引擎安裝在 Windows IoT 核心版上，請遵循[佈建 IoT 核心版裝置文章][lnk-iot-core]中的步驟，然後繼續遵循下方的指示。

Azure IoT Edge 依賴 [OCI 相容][lnk-oci]的容器執行階段 (例如 Docker)。 您可以使用[適用於 Windows 的 Docker][lnk-docker-for-windows] 來進行開發與測試。 

**確定適用於 Windows 的 Docker [已設定為使用 Windows 容器][lnk-docker-config]**

## <a name="install-the-azure-iot-edge-security-daemon"></a>安裝 Azure IoT Edge 安全性精靈

>[!NOTE]
>使用 Azure IoT Edge 軟體套件時，必須遵守封裝中的授權條款 (位於 [LICENSE] 目錄)。 使用封裝前，請先閱讀授權條款。 安裝及使用封裝即表示您接受這些授權條款。 如果您不同意授權條款，請勿使用封裝。

### <a name="download-the-edge-daemon-package-and-install"></a>下載 Edge 精靈封裝並安裝

在系統管理員 PowerShell 視窗中，執行下列命令：

```powershell
Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
rmdir C:\ProgramData\iotedge\iotedged-windows
$sysenv = "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment"
$path = (Get-ItemProperty -Path $sysenv -Name Path).Path + ";C:\ProgramData\iotedge"
Set-ItemProperty -Path $sysenv -Name Path -Value $path
```

使用下列命令安裝 vcruntime (若您在 IoT 核心版 Edge 裝置上，則可略過此步驟)：

```powershell
Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
.\vc_redist.exe /quiet /norestart
 ```

建立並啟動 **iotedge** 服務：

```powershell
New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
Start-Service iotedge
```

為服務所使用的連接埠新增防火牆例外：

```powershell
New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
```

建立具有下列內容的 **iotedge.reg** 檔案，然後按兩下此檔案或使用 `reg import iotedge.reg` 命令將檔案匯入至 Windows 登錄：

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>設定 Azure IoT Edge 安全性精靈

您可以使用 `C:\ProgramData\iotedge\config.yaml` 上的組態檔來設定精靈。

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

接下來，請在組態的 **connect:** 和 **listen:** 區段中，為 **workload_uri** 和 **management_uri** 提供 IP 位址和連接埠。

若要擷取 IP 位址，請在 PowerShell 視窗中輸入 `ipconfig` 並複製 **vEthernet (nat)** 介面的 IP 位址，如下列範例所示 (您系統上的 IP 位址可能會不同)：  

![nat][img-nat]

在更新組態檔的 **connect:** 區段中，更新 **workload_uri** 和 **management_uri**。 請將 **\<GATEWAY_ADDRESS\>** 取代為您所複製的 vEthernet IP 位址。

```yaml
connect:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

在 **listen:** 區段中輸入相同的位址。

```yaml
listen:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

在 PowerShell 視窗中，建立一個具有 **management_uri** 位址的環境變數 **IOTEDGE_HOST**。

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<GATEWAY_ADDRESS>:15580")
```

在重新啟動時保存環境變數。

```powershell
SETX /M IOTEDGE_HOST "http://<GATEWAY_ADDRESS>:15580"
```

最後，確定 **moby_runtime:** 底下的 **network:** 設定已取消註解並設定為 **nat**

```yaml
moby_runtime:
  docker_uri: "npipe://./pipe/docker_engine"
  network: "nat"
```

儲存設定檔並重新啟動服務：

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>確認安裝成功

如果您在上一節中使用**手動設定**步驟，IoT Edge 執行階段應會在您的裝置上成功佈建並執行。 如果您使用**自動設定**步驟，則必須完成一些額外的步驟，讓執行階段可代表您向您的 IoT 中樞註冊裝置。 如需後續步驟，請參閱[在 Windows 上建立及佈建模擬 TPM Edge 裝置](how-to-auto-provision-simulated-device-windows.md#create-a-tpm-environment-variable)。

透過以下命令檢查 IoT Edge 服務的狀態： 

```powershell
Get-Service iotedge
```

使用以下命令檢查最後 5 分鐘的服務記錄檔：

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

如果您有 Edge 執行階段是否正確安裝的問題，請參閱[疑難排解][lnk-trouble]頁面。


<!-- Images -->
[img-nat]: ./media/how-to-install-iot-edge-windows-with-windows/nat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-windows.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows
[lnk-iot-core]: how-to-install-iot-core.md

