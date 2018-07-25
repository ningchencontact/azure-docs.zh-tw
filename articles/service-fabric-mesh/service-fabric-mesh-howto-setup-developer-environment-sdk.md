---
title: 設定 Service Fabric Mesh 應用程式的 Windows 開發環境 | Microsoft Docs
description: 設定您的 Windows 開發環境，以便建立 Service Fabric Mesh 應用程式，並將其部署至 Azure Service Fabric Mesh。
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/11/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 96549696013a2dd94741090a0a017b57a3b1e19e
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125156"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-applications"></a>設定 Windows 開發環境以建置 Service Fabric 應用程式 | Microsoft Docs

若要在您的 Windows 開發機器上建置並執行 Azure Service Fabric 應用程式，請安裝 Service Fabric 執行階段、SDK 和工具。

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="supported-operating-system-versions"></a>支援的作業系統版本

下列為支援開發的作業系統版本：

* Windows 10 (企業版、專業版或教育版)
* Windows Server 2016

## <a name="enable-hyper-v"></a>啟用 Hyper-V

必須啟用 Hyper-V，您才可建立 Service Fabric 應用程式。 

### <a name="windows-10"></a>Windows 10

以系統管理員身分開啟 PowerShell，並執行下列命令：

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

重新啟動您的電腦。 如需如何啟用 Hyper-V 的詳細資訊，請參閱[在 Windows 10 上安裝 Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)。

### <a name="windows-server-2016"></a>Windows Server 2016

以系統管理員身分開啟 PowerShell，並執行下列命令：

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

重新啟動您的電腦。 如需如何啟用 Hyper-V 的詳細資訊，請參閱[在 Windows Server 2016 上安裝 Hyper-V 角色](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)。

## <a name="visual-studio"></a>Visual Studio

必須使用 Visual Studio 2017 才能部署 Service Fabric 應用程式。 [安裝 15.6.0 版][download-visual-studio]或更新版本，並啟用下列工作負載：

- ASP.NET 和 Web 開發
- Azure 開發

## <a name="docker"></a>Docker

安裝 Docker 以支援 Service Fabric Mesh 所使用的容器化 Service Fabric 應用程式。

### <a name="windows-10"></a>Windows 10

下載並安裝最新版的 [Docker Community Edition for Windows][download-docker]。 

在安裝期間，當系統詢問時，請選取 [使用 Windows 容器，而非 Linux 容器]。 接著您必須登出，然後再重新登入。 在重新登入後，如果您先前未啟用 Hyper-V，系統可能會提示您啟用 Hyper-V。 您必須啟用 Hyper-V，然後重新啟動電腦。

在電腦重新啟動後，Docker 會提示您啟用 [容器] 功能，請加以啟用，然後重新啟動電腦。

### <a name="windows-server-2016"></a>Windows Server 2016

使用下列 PowerShell 命令安裝 Docker。 如需詳細資訊，請參閱 [Docker Enterprise Edition for Windows Server][download-docker-server]。

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

重新啟動您的電腦。

## <a name="sdk-and-tools"></a>SDK 與工具

以相依順序安裝 Service Fabric Mesh 執行階段、SDK 和工具。

1. 使用 Web Platform Installer 安裝 [Service Fabric Mesh SDK][download-sdkmesh]。 這也將安裝 Microsoft Azure Service Fabric SDK 和執行階段。
2. 從 Visual Studio Marketplace 安裝 [Visual Studio Service Fabric 工具 (預覽) 擴充功能][download-tools]。

## <a name="build-a-cluster"></a>建置叢集

在建立和執行 Service Fabric 應用程式時若要有最佳偵錯效能，建議您建立單一節點的本機開發叢集。 每當您部署或偵錯 Service Fabric Mesh 專案時，都必須執行此叢集。

**必須**先執行 Docker，您才能建置叢集。 請開啟終端機視窗並執行 `docker ps`，然後查看是否有錯誤發生，以測試該 Docker。 如果回應未指出錯誤，表示 Docker 正在執行中，且您已可建置叢集。

在您安裝執行階段、SDK 和 Visual Studio 工具後，請建立開發叢集。

1. 關閉 PowerShell 視窗。
2. 以系統管理員身分開啟提升權限的新 PowerShell 視窗。 必須執行此步驟，才能載入您已安裝的 Service Fabric 模組。
3. 執行下列 PowerShell 命令，以建立開發叢集：

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster -UseMachineName
    ```

4. 若要啟動本機叢集管理員工具，請執行下列 PowerShell 命令：

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```

現在您已準備就緒，可建立 Service Fabric Mesh 應用程式！

## <a name="next-steps"></a>後續步驟

完整閱讀[建立 Azure Service Fabric 應用程式](service-fabric-mesh-tutorial-create-dotnetcore.md)教學課程。

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.ServiceFabricMesh
[download-visual-studio]: https://www.visualstudio.com/downloads/
