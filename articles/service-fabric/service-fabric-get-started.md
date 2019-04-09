---
title: 設定 Azure 微服務的 Windows 開發環境 | Microsoft Docs
description: 安裝執行階段、SDK 和工具，並建立本機開發叢集。 完成此設定之後，您就可以開始在 Windows 上建置應用程式。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/02/2019
ms.author: aljo
ms.openlocfilehash: 45d5d1b4e050d01b003b6321e1af44c0da762e19
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59275473"
---
# <a name="prepare-your-development-environment-on-windows"></a>在 Windows 上準備您的開發環境
> [!div class="op_single_selector"]
> * [ Windows](service-fabric-get-started.md) 
> * [ Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

若要在您的 Windows 開發機器上建置並執行 [Azure Service Fabric 應用程式][1]，請安裝 Service Fabric 執行階段、SDK 和工具。 您也必須[執行 SDK 中包含的 Windows PowerShell 指令碼](#enable-powershell-script-execution)。

## <a name="prerequisites"></a>必要條件
### <a name="supported-operating-system-versions"></a>支援的作業系統版本
下列為支援開發的作業系統版本：

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Windows 7 支援：
> - 根據預設，Windows 7 只包含 Windows PowerShell 2.0。 Service Fabric PowerShell cmdlet 需要 PowerShell 3.0 或更高版本。 您可以從 Microsoft 下載中心[下載 Windows PowerShell 5.0][powershell5-download]。
> - 無法在 Windows 7 上使用 Service Fabric 反向 Proxy。
>

## <a name="install-the-sdk-and-tools"></a>安裝 SDK 和工具
Web Platform Installer (WebPI) 是安裝 SDK 和工具的建議方式。 如果您在使用 WebPI 時接收到執行階段錯誤，您也可以在特定 Service Fabric 版本的版本資訊中找到安裝程式的直接連結。 版本資訊可在 [Service Fabric 小組部落格](https://blogs.msdn.microsoft.com/azureservicefabric/) \(英文\) 上的各個版本公告中找到。

> [!NOTE]
> 不支援本機 Service Fabric 開發叢集升級。

### <a name="to-use-visual-studio-2017"></a>若要使用 Visual Studio 2017
Service Fabric 工具屬於 Visual Studio 2017 中的 Azure 開發工作負載。 啟用此工作負載作為 Visual Studio 安裝的一部分。
此外，您必須使用 Web Platform Installer 來安裝 Microsoft Azure Service Fabric SDK 和執行階段。

* [安裝 Microsoft Azure Service Fabric SDK][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>若要使用 Visual Studio 2015 (需要 Visual Studio 2015 Update 2 或更新版本)
在 Visual Studio 2015 中，使用 Web Platform Installer，Service Fabric 工具會與 SDK 和執行階段一起安裝︰

* [安裝 Microsoft Azure Service Fabric SDK 和工具][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>僅限 SDK 安裝
如果您只需要 SDK，您可以安裝此套件︰
* [安裝 Microsoft Azure Service Fabric SDK][core-sdk]

目前的版本如下︰
* Service Fabric SDK 和工具 3.3.654
* Service Fabric 執行階段 6.4.654
* Service Fabric Tools for Visual Studio 2015 2.4.11116.1
* Visual Studio 2017 15.9 包含 Service Fabric Tools for Visual Studio 2.4.11024.1 

如需支援版本的清單，請參閱[Service Fabric 版本](service-fabric-versions.md)

> [!NOTE]
> 單一機器叢集 (OneBox) 並不支援進行應用程式或叢集升級；如果您需要執行叢集升級，或是在執行應用程式升級時遇到任何問題，請刪除 OneBox 叢集並重新建立它。 

## <a name="enable-powershell-script-execution"></a>啟用 PowerShell 指令碼執行
Service Fabric 會使用 Windows PowerShell 指令碼，以便建立本機開發叢集，以及從 Visual Studio 部署應用程式。 根據預設，Windows 會封鎖這些指令碼的執行。 若要啟用其，您必須修改 PowerShell 執行原則。 以系統管理員身分開啟 PowerShell 並輸入下列命令：

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```
## <a name="install-docker-optional"></a>安裝 Docker (選擇性)
[Service Fabric 是容器協調者](service-fabric-containers-overview.md)，可用於將微服務部署至整個機器叢集。 若要在本機開發叢集上執行 Windows 容器應用程式，您必須先安裝 Docker for Windows。 取得 [Docker CE for Windows (穩定)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)。 安裝並啟動 Docker 之後，以滑鼠右鍵按一下系統匣圖示，然後選取 [切換至 Windows 容器]。 這是執行以 Windows 為基礎的 Docker 映像時的必要步驟。

## <a name="next-steps"></a>後續步驟
現在您的開發環境已完成設定，您可以開始建置和執行應用程式。

* [了解如何建立、 部署及管理應用程式](service-fabric-tutorial-create-dotnet-app.md)
* [深入了解程式設計模型：Reliable Services 和 Reliable Actors](service-fabric-choose-framework.md)
* [請參閱 GitHub 上的 Service Fabric 程式碼範例](https://aka.ms/servicefabricsamples)
* [使用 Service Fabric Explorer 視覺化叢集](service-fabric-visualizing-your-cluster.md)
* 了解 [Service Fabric 支援選項](service-fabric-support.md)

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric 活動頁面"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI 連結"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI 連結"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI 連結"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
