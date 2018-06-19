---
title: 在雲端建立 Kubernetes 開發人員空間 | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 06/06/2018
ms.topic: quickstart
description: 在 Azure 上使用容器和微服務快速進行 Kubernetes 開發
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器
manager: douge
ms.openlocfilehash: 16ec493708f85e9b3819943e131b9f9c3649f27e
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824633"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-net-core-and-visual-studio"></a>快速入門：使用 Azure Dev Spaces 建立 Kubernetes 開發人員空間 (.NET Core 和 Visual Studio)

在本指南中，您將了解如何：

- 使用 Azure 中受管理的 Kubernetes 叢集，設定 Azure Dev Spaces。
- 使用 Visual Studio 在容器中反覆開發程式碼。
- 對您的叢集中執行的程式碼進行偵錯。

> [!Note]
> **如果作業出現停滯的情況**，請參閱[疑難排解](troubleshooting.md)一節，或在此頁面上張貼留言。 您也可以嘗試進行更詳細的[教學課程](get-started-netcore-visualstudio.md)。

## <a name="prerequisites"></a>先決條件

- 在 EastUS、WestEurope 或 CanadaEast 區域中執行 Kubernetes 1.9.6、並且已啟用 HTTP 應用程式路由的 Kubernetes 叢集。

  ![請務必啟用 HTTP 應用程式路由。](media/common/Kubernetes-Create-Cluster-3.PNG)

- 已安裝網頁程式開發工作負載的 Visual Studio 2017。 如果您尚未安裝，請在[這裡](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)下載。

## <a name="set-up-azure-dev-spaces"></a>設定 Azure Dev Spaces

安裝[適用於 Azure Dev Spaces 的 Visual Studio 擴充功能](https://aka.ms/get-azds-visualstudio)。

## <a name="connect-to-a-cluster"></a>連接到叢集

接下來，您將建立和設定 Azure Dev Spaces 的專案。

### <a name="create-an-aspnet-web-app"></a>建立 ASP.NET Web 應用程式

在 Visual Studio 2017 中，建立新的專案。 目前，此專案必須是 **ASP.NET Core Web 應用程式**。 將專案命名為 **webfrontend**。

選取 [Web 應用程式 (Model-View-Controller)] 範本，並確定您的目標是 **.NET Core** 和 **ASP.NET Core 2.0**。

### <a name="create-a-dev-space-in-azure"></a>在 Azure 中建立開發人員空間

開啟您剛建立的專案，然後從啟動設定下拉式清單中選取 [Azure 開發人員空間]，如下所示。

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

在接下來顯示的對話方塊中，請確定您以適當的帳戶登入，然後選取現有的叢集。

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.png)

暫時讓 [空間] 下拉式清單設定為 `default`。 請核取 [可公開存取] 核取方塊，以便透過公用端點存取 Web 應用程式。

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

按一下 [確定] 以選取或建立叢集。

如果您選擇尚未設定要搭配 Azure Dev Spaces 使用的叢集，您會看到訊息詢問您是否要設定它。

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

選擇 [確定]。 

### <a name="look-at-the-files-added-to-project"></a>查看已新增至專案的檔案
等待建立開發人員空間時，查看當您在選擇使用 Azure Dev Spaces 時新增至專案的檔案。

- 已新增名為 `charts` 的資料夾，而此資料夾內已建構適用於您的應用程式的 [Helm 圖表](https://docs.helm.sh)。 這些檔案可用來將您的應用程式部署到開發人員空間中。
- `Dockerfile` 包含以標準 Docker 格式封裝應用程式所需的資訊。
- `azds.yaml` 包含開發人員空間所需的組態資訊，例如應用程式是否可透過公用端點存取。

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>在 Kubernetes 中進行容器偵錯
在成功建立開發人員空間後，您即可進行應用程式偵錯。 在程式碼中設定中斷點，例如在 `Message` 變數設定所在 `HomeController.cs` 檔案中的第 20 行上。 按一下 **F5** 開始偵錯。 

Visual Studio 會與開發人員空間通訊，以建置和部署應用程式，然後以執行中的 Web 應用程式開啟瀏覽器。 容器可能看起來像在本機執行，但實際是在 Azure 的開發人員空間中執行。 localhost 位址的原因是因為 Azure 開發人員空間會對在 Azure 中執行的容器建立暫存 SSH 通道。

按一下頁面頂端的 [關於] 連結，以觸發中斷點。 就如同已在本機執行程式碼一樣，您擁有偵錯資訊的完整存取權，例如呼叫堆疊、區域變數、例外狀況資訊等等。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用多個容器和小組開發](get-started-netcore-visualstudio.md#call-another-container)