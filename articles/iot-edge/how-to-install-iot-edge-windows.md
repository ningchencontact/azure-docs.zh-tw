---
title: 在 Windows 上安裝 Azure IoT Edge | Microsoft Docs
description: 在 Windows 10、Windows Server 和 Windows IoT 核心版上安裝 Azure IoT Edge 的指示
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: e48ab075264423479e792848af522a890736a403
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65152698"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>在 Windows 上安裝 Azure IoT Edge 執行階段

Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 此執行階段可以部署在像 Raspberry Pi 一樣小或像工業伺服器一樣大的裝置上。 利用 IoT Edge 執行階段設定裝置之後，您就可以開始從雲端將商務邏輯部署給它。 

若要深入了解 IoT Edge 執行階段，請參閱[了解 Azure IoT Edge 執行階段及其架構](iot-edge-runtime.md)。

本文列出您的 Windows x64 (AMD/Intel) 上安裝 Azure IoT Edge 執行階段的步驟使用 Windows 容器的系統。

> [!NOTE]
> 当 IoT Edge 模块（进程隔离的 Windows Nano Server 容器）正在运行时，一个已知的 Windows 操作系统问题会阻止转换到睡眠和休眠电源状态。 此问题会影响设备的电池寿命。
>
> 作为解决方法，在使用这些电源状态之前，请使用 `Stop-Service iotedge` 命令停止任何正在运行的 IoT Edge 模块。 

<!--
> [!NOTE]
> Using Linux containers on Windows systems is not a recommended or supported production configuration for Azure IoT Edge. However, it can be used for development and testing purposes.
-->

就 Azure IoT Edge 而言，在 Windows 系統上使用 Linux 容器不是建議或支援的生產環境設定。 不過，這很適合用於開發與測試用途。 若要進一步了解，請參閱[使用 IoT Edge 上執行 Linux 容器的 Windows](how-to-install-iot-edge-windows-with-linux.md)。

如需 IoT Edge 的最新版本包含哪些內容資訊，請參閱[Azure IoT Edge 釋放](https://github.com/Azure/azure-iotedge/releases)。

## <a name="prerequisites"></a>必要條件

使用此區段可以檢閱 Windows 裝置是否可支援 IoT Edge，以及在安裝之前為容器引擎做好準備。 

### <a name="supported-windows-versions"></a>支援的 Windows 版本

開發和測試案例中，使用 Windows 容器的 Azure IoT Edge 可以安裝在任何版本的 Windows 10 或 Windows Server 2019 （組建 17763） 可支援容器功能。 如需作業系統支援哪些的目前針對實際執行案例的資訊，請參閱[Azure IoT Edge 支援系統](support.md#operating-systems)。 

### <a name="prepare-for-a-container-engine"></a>準備容器引擎 

Azure IoT Edge 會依賴 [OCI 相容](https://www.opencontainers.org/)的容器引擎。 對於生產案例，請使用安裝指令碼中包含的 Moby 引擎，在 Windows 裝置上執行 Windows 容器。 

## <a name="install-iot-edge-on-a-new-device"></a>在新的裝置上安裝 IoT Edge

>[!NOTE]
>使用 Azure IoT Edge 軟體套件時，必須遵守封裝中的授權條款 (位於 [LICENSE] 目錄)。 使用封裝前，請先閱讀授權條款。 安裝及使用封裝即表示您接受這些授權條款。 如果您不同意授權條款，請勿使用封裝。

PowerShell 指令碼會下載並安裝 Azure IoT Edge 安全性精靈。 接著，安全性精靈會開始第一個執行階段模組 (總共兩個)，也就是可遠端部署其他模組的 IoT Edge 代理程式。 

當您第一次在裝置上安裝 IoT Edge 執行階段時，需要使用來自 IoT 中樞的身分識別以佈建裝置。 單一的 IoT Edge 裝置可以使用 IoT 中樞所提供的裝置連接字串，以手動方式佈建。 或者，您可以使用裝置佈建服務來自動佈建裝置，這在您有許多裝置要設定時將很有幫助。 請根據您選擇的佈建方式，選擇適當的安裝指令碼。 

下列各節說明新裝置上 IoT Edge 安裝指令碼的常見使用案例和參數。 

### <a name="option-1-install-and-manually-provision"></a>選項 1：安裝並手動佈建

在此第一個選項時，您可以提供**裝置連接字串**佈建裝置的 IoT 中樞所產生。 

此範例示範使用 Windows 容器的手動安裝：

1. 如果您還沒有這麼做，註冊新的 IoT Edge 裝置，並擷取**裝置連接字串**。 複製連接字串以供日後使用，這一節。 您可以完成此步驟中使用下列工具：

   * [Azure 入口網站](how-to-register-device-portal.md)
   * [Azure CLI](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. 以系統管理員身分執行 PowerShell。

3. **部署 IoTEdge**命令會檢查您的 Windows 電腦上支援的版本、 開啟 「 容器 」 功能，以及然後下載 白鯨執行階段和 IoT Edge 執行階段。 此命令預設會使用 Windows 容器。 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. 此時，IoT 核心版的裝置可能會自動重新啟動。 其他 Windows 10 或 Windows Server 的裝置可能會提示您重新啟動。 如果是的話，請立即重新啟動您的裝置。 您的裝置準備就緒後，再以系統管理員身分執行 PowerShell。

5. **Initialize IoTEdge**命令會設定您的電腦上的 IoT Edge 執行階段。 此命令會預設為使用 Windows 容器手動佈建。 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. 出現提示時，提供您在步驟 1 中擷取的裝置連接字串。 裝置連接字串會將實體裝置與 IoT 中樞的裝置識別碼產生關聯。 

   裝置連接字串會採用下列格式，而且不應該包含引號： `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. 使用中的步驟[確認安裝是否成功](#verify-successful-installation)檢查 IoT Edge 裝置上的狀態。 

當您手動安裝及佈建裝置時，可以使用其他參數修改安裝，包括：
* 將流量導向經過 Proxy 伺服器
* 將安裝程式指向離線目錄
* 宣告特定的代理程式容器映像，並在在私人登錄時提供認證

如需這些安裝選項的詳細資訊，請直接跳到了解[所有的安裝參數](#all-installation-parameters)。

### <a name="option-2-install-and-automatically-provision"></a>選項 2：安裝並自動佈建

在第二個選項中，使用 IoT 中樞裝置佈建服務來佈建裝置。 提供來自「裝置佈建服務」執行個體的**範圍識別碼**，以及來自您裝置的**註冊識別碼**。

下列範例示範使用 Windows 容器的自動安裝：

1. 依照[在 Windows 上建立及佈建模擬 TPM Edge 裝置](how-to-auto-provision-simulated-device-windows.md)中的步驟設定裝置佈建服務，並擷取其 [範圍識別碼]、模擬 TPM 裝置並擷取其 [註冊識別碼]，然後建立個別註冊。 一旦您的 IoT 中樞中註冊您的裝置，繼續進行下列安裝步驟。  

   >[!TIP]
   >在安裝和測試期間，請將執行 TPM 模擬器的視窗保持開啟。 

2. 以系統管理員身分執行 PowerShell。

3. **部署 IoTEdge**命令會檢查您的 Windows 電腦上支援的版本、 開啟 「 容器 」 功能，以及然後下載 白鯨執行階段和 IoT Edge 執行階段。 此命令預設會使用 Windows 容器。 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. 此時，IoT 核心版的裝置可能會自動重新啟動。 其他 Windows 10 或 Windows Server 的裝置可能會提示您重新啟動。 如果是的話，請立即重新啟動您的裝置。 您的裝置準備就緒後，再以系統管理員身分執行 PowerShell。

6. **Initialize IoTEdge**命令會設定您的電腦上的 IoT Edge 執行階段。 此命令會預設為使用 Windows 容器手動佈建。 使用`-Dps`旗標，以使用裝置佈建服務，而不是手動佈建。

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps
   ```

7. 出現提示時，提供從您的裝置佈建服務的範圍 ID 和您的裝置，這兩者都應該擷取在步驟 1 中的註冊識別碼。

8. 使用中的步驟[確認安裝是否成功](#verify-successful-installation)檢查 IoT Edge 裝置上的狀態。 

當您手動安裝及佈建裝置時，可以使用其他參數修改安裝，包括：
* 將流量導向經過 Proxy 伺服器
* 將安裝程式指向離線目錄
* 宣告特定的代理程式容器映像，並在在私人登錄時提供認證

如需這些安裝選項的詳細資訊，請繼續閱讀本文，或跳過以了解[所有安裝參數](#all-installation-parameters)。

## <a name="offline-installation"></a>離線安裝

在安裝期間不會下載兩個檔案： 
* Microsoft Azure IoT Edge 封包，其中包含 IoT Edge 安全性精靈 (iotedged) 」、 「 白鯨容器引擎，以及 「 白鯨 CLI。
* Visual C++ 可轉散發套件 (VC 執行階段) msi

您可以下載一或兩個事先到裝置，然後指向包含檔案的目錄中的安裝指令碼。 安裝程式會先檢查目錄，然後僅下載找不到的元件。 如果離線可用的所有檔案，您可以安裝沒有網際網路連線。 您也可以使用這項功能來安裝特定版本的元件。  

如先前的版本以及最新 IoT Edge 安裝檔案，請參閱 < [Azure IoT Edge 釋放](https://github.com/Azure/azure-iotedge/releases)。

若要安裝與離線的元件，請使用`-OfflineInstallationPath`參數部署 IoTEdge 命令，並提供檔案目錄的絕對路徑。 例如，

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

您也可以使用離線安裝路徑參數更新 IoTEdge 介紹的命令，在本文稍後使用。 

## <a name="verify-successful-installation"></a>確認安裝成功

檢查 IoT Edge 服務的狀態。 它應該會列示為執行中。  

```powershell
Get-Service iotedge
```

檢查最後 5 分鐘的服務記錄。 如果您剛完成安裝 IoT Edge 執行階段，您可能會看到一份執行之間的時間中的錯誤**部署 IoTEdge**並**初始化 IoTEdge**。 必須是這些錯誤，因為服務正在嘗試啟動之前設定。 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

列出執行中的模組。 完成新的安装后，应会看到唯一运行的模块是 **edgeAgent**。 [部署 IoT Edge 模块](how-to-deploy-modules-portal.md)后，将会看到其他模块。 

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>管理模块容器

IoT Edge 服务要求在设备上运行容器引擎。 将模块部署到设备时，IoT Edge 运行时将使用容器引擎从云中的注册表提取容器映像。 IoT Edge 服务允许与模块交互和检索日志，但有时，你可能想要使用容器引擎来与容器本身交互。 

有关模块概念的详细信息，请参阅[了解 Azure IoT Edge 模块](iot-edge-modules.md)。 

如果在 Windows IoT Edge 设备上运行 Windows 容器，则 IoT Edge 安装中已包含 Moby 容器引擎。 Moby 引擎所基于的标准与 Docker 相同，可在 Docker Desktop 所在的同一台计算机上同时运行。 因此，若要以 Moby 引擎管理的容器为目标，则必须专门将该引擎指定为目标，而不要以 Docker 为目标。 

例如，若要列出所有 Docker 映像，可使用以下命令：

```powershell
docker images
```

若要列出所有 Moby 映像，可以使用指向 Moby 引擎的指针修改上述命令： 

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

引擎 URI 将在安装脚本的输出中列出，也可以在 config.yaml 文件的容器运行时设置节中找到它。 

![config.yaml 中的 moby_runtime uri](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

若要详细了解可以使用哪些命令来与设备上运行的容器和映像交互，请参阅 [Docker 命令行接口](https://docs.docker.com/engine/reference/commandline/docker/)。

## <a name="update-an-existing-installation"></a>更新現有的安裝

如果您已先在裝置上安裝 IoT Edge 執行階段，並從 IoT 中樞的身分識別將它佈建，然後您就可以更新執行階段而不需要重新輸入您的裝置資訊。 

如需更多資訊，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。

此範例顯示指向現有組態檔的安裝，並使用 Windows 容器： 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

當您更新 IoT Edge 時，您可以使用其他參數來修改該更新，包括：
* 直接流量通過 proxy 伺服器，或
* 將安裝程式指向離線目錄 
* 如有必要，而不會提示重新啟動

您無法宣告指令碼參數的 IoT Edge 代理程式容器映像，因為該資訊已在組態檔中先前安裝的設定。 如果您想要修改代理程式容器映像，請在 config.yaml 檔案中執行此操作。 

如需有關這些更新選項，請使用命令`Get-Help Update-IoTEdge -full`，或請參閱[所有的安裝參數](#all-installation-parameters)。

## <a name="uninstall-iot-edge"></a>解除安裝 IoT Edge

如果您想要從 Windows 裝置中移除 IoT Edge 安裝，請從管理 PowerShell 視窗中使用下列命令。 此命令會移除 IoT Edge 執行階段，以及您現有的設定和 Moby 引擎資料。 

```powershell
Uninstall-IoTEdge
```

解除安裝 IoTEdge 命令在 Windows IoT 核心版上無法運作。 若要移除 Windows IoT 核心版裝置的 IoT Edge，您需要重新部署您的 Windows IoT 核心版映像。 

如需有關解除安裝選項的詳細資訊，請使用命令`Get-Help Uninstall-IoTEdge -full`。 

## <a name="all-installation-parameters"></a>所有安裝參數

前幾節介紹常見的安裝案例，示範如何使用參數來修改安裝指令碼。 本節提供這些常見的參數，用來安裝、 更新或解除安裝 IoT Edge 的參考資料表。 

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

部署 IoTEdge 命令會下載和部署 IoT Edge 安全性精靈和其相依性。 [部署] 命令會接受這些常見的參數，其他項目。 如需完整的清單中，使用命令`Get-Help Deploy-IoTEdge -full`。  

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** 或 **Linux** | 如果未不指定任何容器的作業系統，Windows 就會是預設值。<br><br>適用於 Windows 容器，IoT Edge 會使用包含在安裝白鯨容器引擎。 對於 Linux 容器，您需要先安裝容器引擎，再開始安裝。 |
| **Proxy** | Proxy URL | 如果您的裝置需要通過 Proxy 伺服器連線網際網路，請包含此參數。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **OfflineInstallationPath** | 目錄路徑 | 如果包含此參數時，安裝程式會檢查 IoT Edge 的封包和安裝所需的 VC 執行階段的 MSI 檔案列出的目錄。 在目錄中找不到任何檔案會下載。 如果這兩個檔案的目錄中，您可以安裝沒有網際網路連線的 IoT Edge。 您也可以使用這個參數來使用特定版本。 |
| **InvokeWebRequestParameters** | 參數和值的雜湊表 | 在安裝期間，會提出數個 Web 要求。 您可以使用此欄位，為這些 Web 要求設定參數。 此參數對於設定 Proxy 伺服器的認證非常有用。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **RestartIfNeeded** | None | 如有必要，這個旗標會讓部署指令碼，而不提示，重新啟動電腦。 |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

初始化 IoTEdge 命令會設定 IoT Edge 裝置連接字串與詳細操作資訊。 大部分此命令所產生的資訊接著會儲存在 iotedge\config.yaml 檔案中。 初始化命令接受這些常見的參數，其他項目。 如需完整的清單中，使用命令`Get-Help Initialize-IoTEdge -full`。 

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **手動** | None | **切換參數**。 如果未不指定任何佈建的類型，手動是預設值。<br><br>宣告您將提供裝置連接字串以手動佈建裝置 |
| **Dps** | None | **切換參數**。 如果未不指定任何佈建的類型，手動是預設值。<br><br>宣告您將提供裝置佈建服務 (DPS) 的範圍識別碼，以及透過 DPS 佈建的裝置註冊識別碼。  |
| **DeviceConnectionString** | 此連接字串來自 IoT 中樞註冊的 IoT Edge 裝置，以單引號括住 | 此為手動安裝的**必要項目**。 如果您未提供指令碼參數中的連接字串，在安裝期間會提示您輸入一個連接字串。 |
| **ScopeId** | 此範圍識別碼來自與您 IoT 中樞相關聯之裝置佈建服務的執行個體。 | 此為 DPS 安裝的**必要項目**。 如果您未提供指令碼參數中的範圍識別碼，在安裝期間會提示您輸入範圍識別碼。 |
| **RegistrationId** | 由您裝置產生的註冊識別碼 | 此為 DPS 安裝的**必要項目**。 如果您未提供指令碼參數中的註冊識別碼，在安裝期間會提示您輸入註冊識別碼。 |
| **ContainerOs** | **Windows** 或 **Linux** | 如果未不指定任何容器的作業系統，Windows 就會是預設值。<br><br>適用於 Windows 容器，IoT Edge 會使用包含在安裝白鯨容器引擎。 對於 Linux 容器，您需要先安裝容器引擎，再開始安裝。 |
| **InvokeWebRequestParameters** | 參數和值的雜湊表 | 在安裝期間，會提出數個 Web 要求。 您可以使用此欄位，為這些 Web 要求設定參數。 此參數對於設定 Proxy 伺服器的認證非常有用。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **AgentImage** | IoT Edge 代理程式映像 URI | 根據預設，新的 IoT Edge 安裝會為 IoT Edge 代理程式映像使用最新的輪替標籤。 使用此參數，為映像版本設定特定標籤，或提供您自己的代理程式映像。 如需詳細資訊，請參閱[了解 IoT Edge 標籤](how-to-update-iot-edge.md#understand-iot-edge-tags)。 |
| **使用者名稱** | 容器登錄使用者名稱 | 只有在將 -AgentImage 參數設定為私人登錄中的容器時，才使用此參數。 提供可以存取登錄的使用者名稱。 |
| **密碼** | 安全密碼字串 | 只有在將 -AgentImage 參數設定為私人登錄中的容器時，才使用此參數。 提供可以存取登錄的密碼。 | 

### <a name="update-iotedge"></a>Update-IoTEdge

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** 或 **Linux** | 如果作業系統指定沒有容器，Windows 就會是預設值。 對於 Windows 容器，安裝中將包含容器引擎。 對於 Linux 容器，您需要先安裝容器引擎，再開始安裝。 |
| **Proxy** | Proxy URL | 如果您的裝置需要通過 Proxy 伺服器連線網際網路，請包含此參數。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **InvokeWebRequestParameters** | 參數和值的雜湊表 | 在安裝期間，會提出數個 Web 要求。 您可以使用此欄位，為這些 Web 要求設定參數。 此參數對於設定 Proxy 伺服器的認證非常有用。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。 |
| **OfflineInstallationPath** | 目錄路徑 | 如果包含此參數時，安裝程式會檢查 IoT Edge 的封包和安裝所需的 VC 執行階段的 MSI 檔案列出的目錄。 在目錄中找不到任何檔案會下載。 如果這兩個檔案的目錄中，您可以安裝沒有網際網路連線的 IoT Edge。 您也可以使用這個參數來使用特定版本。 |
| **RestartIfNeeded** | None | 如有必要，這個旗標會讓部署指令碼，而不提示，重新啟動電腦。 |


### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| 參數 | 接受的值 | 註解 |
| --------- | --------------- | -------- |
| **Force** | None | 如果先前嘗試解除安裝不成功，這個旗標會強制解除安裝。 
| **RestartIfNeeded** | None | 如有必要，這個旗標可讓重新啟動電腦，而不提示，解除安裝指令碼。 |


## <a name="next-steps"></a>後續步驟

現在您已佈建 IoT Edge 裝置並安裝執行階段，接下來您可以[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)。

如果您對於 IoT Edge 是否正確安裝有任何問題，請參閱[疑難排解](troubleshoot.md)頁面。

若要將現有安裝更新為最新版的 IoT Edge，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。
