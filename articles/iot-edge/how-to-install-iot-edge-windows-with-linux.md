---
title: Windows 上安裝適用於 Linux 的 Azure IoT Edge |Microsoft Docs
description: 適用於 Windows 10，Windows Server 和 Windows IoT 核心版上的 Linux 容器的 azure IoT Edge 安裝指示
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: b7386cbbe18d7e05c2fbffb96f6214b468956192
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66151694"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>在 Windows 上使用 IoT Edge，來執行 Linux 容器

測試使用 Windows 電腦的 Linux 裝置的 IoT Edge 模組。 

在生產案例中，Windows 裝置應該只執行 Windows 容器。 不過，常見的開發案例是建置 IoT Edge 模組，適用於 Linux 裝置使用的 Windows 電腦。 Windows 的 IoT Edge 執行階段可讓您執行的 Linux 容器**測試和開發**用途。 

這篇文章列出的步驟安裝在您的 Windows x64 (AMD/Intel) 上使用 Linux 容器的 Azure IoT Edge 執行階段系統。 若要深入了解 IoT Edge 執行階段安裝程式，包括詳細的安裝參數，請參閱[在 Windows 上安裝 Azure IoT Edge 執行階段](how-to-install-iot-edge-windows.md)。

## <a name="prerequisites"></a>必要條件

使用此區段可以檢閱 Windows 裝置是否可支援 IoT Edge，以及在安裝之前為容器引擎做好準備。 

### <a name="supported-windows-versions"></a>支援的 Windows 版本

下列版本的 Windows 與 Linux 容器的 azure IoT Edge 可以執行： 
* Windows 10 年度更新版 (組建 14393) 或較新版本
* Windows Server 2016 或更新版本

如需最新版本 IoT Edge 中包含哪些內容的詳細資訊，請參閱 [Azure IoT Edge 版次](https://github.com/Azure/azure-iotedge/releases)。

如果您想要在虛擬機器上安裝 IoT Edge，啟用巢狀虛擬化，並配置至少 2 GB 的記憶體。 啟用巢狀虛擬化的方式不一樣在 hypervisor 上，視您使用就行了。 Hyper-v，第 2 代虛擬機器具有巢狀虛擬化預設啟用。 對於 VMWare，沒有可啟用此功能在您的虛擬機器上的切換符號。 

### <a name="prepare-the-container-engine"></a>準備容器引擎 

Azure IoT Edge 會依賴 [OCI 相容](https://www.opencontainers.org/)的容器引擎。 在 Windows 機器是 IoT Edge 安裝包含 Windows 容器的執行階段，但您必須提供自己的執行階段適用於 Linux 容器，然後再安裝 IoT Edge 上執行 Windows 和 Linux 容器之間最大的組態差異。 

若要設定開發和測試容器的 Linux 裝置的 Windows 機器，您可以使用[Docker 桌面](https://www.docker.com/docker-windows)做為容器引擎。 您需要安裝 Docker，並將它設定為[使用 Linux 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)之前安裝 IoT Edge。  

如果您的 IoT Edge 裝置是 Windows 電腦，請檢查其是否符合 Hyper-V 的[系統需求](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements)。

## <a name="install-iot-edge-on-a-new-device"></a>在新的裝置上安裝 IoT Edge

>[!NOTE]
>使用 Azure IoT Edge 軟體套件時，必須遵守封裝中的授權條款 (位於 [LICENSE] 目錄)。 使用封裝前，請先閱讀授權條款。 安裝及使用封裝即表示您接受這些授權條款。 如果您不同意授權條款，請勿使用封裝。

PowerShell 指令碼會下載並安裝 Azure IoT Edge 安全性精靈。 接著，安全性精靈會開始第一個執行階段模組 (總共兩個)，也就是可遠端部署其他模組的 IoT Edge 代理程式。 

當您第一次在裝置上安裝 IoT Edge 執行階段時，需要使用來自 IoT 中樞的身分識別以佈建裝置。 單一的 IoT Edge 裝置可以使用您的 IoT 中樞所提供的裝置連接字串，以手動方式佈建。 或者，您可以使用裝置佈建服務來自動佈建裝置，這在您有許多裝置要設定時將很有幫助。 

您可以深入了解不同的安裝選項和文件中的參數[在 Windows 上安裝 Azure IoT Edge 執行階段](how-to-install-iot-edge-windows.md)。 安裝和設定適用於 Linux 容器的 Docker 桌面之後，主要安裝差異宣告與 Linux **-ContainerOs**參數。 例如： 

1. 如果您還沒有這麼做，註冊新的 IoT Edge 裝置，並擷取的裝置連接字串。 複製連接字串以供日後使用，這一節。 您可以完成此步驟中使用下列工具：

   * [Azure 入口網站](how-to-register-device-portal.md)
   * [Azure CLI](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. 以系統管理員身分執行 PowerShell。

   >[!NOTE]
   >您可以使用 PowerShell AMD64 工作階段來安裝 IoT Edge，PowerShell (x86)。 如果您不確定您使用哪一個工作階段類型，請執行下列命令：
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. **部署 IoTEdge**命令會檢查您的 Windows 電腦上支援的版本、 開啟 「 容器 」 功能，以及然後下載 白鯨執行階段 （這不適用於 Linux 容器） 和 IoT Edge 執行階段。 則命令會預設為 Windows 容器，所以會將 Linux 宣告做為作業系統的 瀏覽的容器。 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. 此時，IoT 核心版的裝置可能會自動重新啟動。 其他 Windows 10 或 Windows Server 的裝置可能會提示您重新啟動。 如果是的話，請立即重新啟動您的裝置。 您的裝置準備就緒後，再以系統管理員身分執行 PowerShell。

5. **Initialize-IoTEdge** 命令會設定機器的 IoT Edge 執行階段。 此命令會預設為手動佈建的裝置連接字串。 一次將 Linux 宣告做為作業系統的 瀏覽的容器。 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. 出現提示時，提供您在步驟 1 中擷取的裝置連接字串。 裝置連接字串會將實體裝置與 IoT 中樞的裝置識別碼產生關聯。 

   裝置連接字串會採用下列格式，而且不應該包含引號： `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>確認安裝成功

檢查 IoT Edge 服務的狀態。 它應該會列示為執行中。  

```powershell
Get-Service iotedge
```

檢查最後 5 分鐘的服務記錄。 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

列出執行中的模組。 完成新的安装后，应会看到唯一运行的模块是 **edgeAgent**。 [部署 IoT Edge 模块](how-to-deploy-modules-portal.md)后，将会看到其他模块。 

```powershell
iotedge list
```

## <a name="next-steps"></a>後續步驟

現在您已佈建 IoT Edge 裝置並安裝執行階段，接下來您可以[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)。

如果您對於 IoT Edge 是否正確安裝有任何問題，請參閱[疑難排解](troubleshoot.md)頁面。

若要將現有安裝更新為最新版的 IoT Edge，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。
