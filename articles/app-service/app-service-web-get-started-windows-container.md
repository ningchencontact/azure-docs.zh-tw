---
title: 在 Azure (預覽) 中執行自訂 Windows 容器 | Microsoft Docs
description: 了解如何將自訂 Windows 容器部署至 Azure App Service。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/07/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: e8f357347e39c2e8ff071e8f4af8e69dcce3940e
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2018
ms.locfileid: "39640172"
---
# <a name="run-a-custom-windows-container-in-azure-preview"></a>在 Azure (預覽) 中執行自訂 Windows 容器

[Azure App Service](app-service-web-overview.md) 會在 Windows 上提供預先定義的應用程式堆疊 (例如 ASP.NET 或 Node.js)，執行於 IIS 上。 預先設定的 Windows 環境會鎖定作業系統的系統管理存取、軟體安裝、對全域組件快取的變更等作業 (請參閱 [Azure App Service 上的作業系統功能](web-sites-available-operating-system-functionality.md))。 如果您的應用程式需要的存取超出預先設定的環境所允許的，您可以改為部署自訂 Windows 容器。 本快速入門說明如何從 [Docker Hub](https://hub.docker.com/) 將自訂 IIS 映像部署至 Azure App Service。

![](media/app-service-web-get-started-windows-container/app-running.png)

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-windows-container-app"></a>建立 Windows 容器應用程式

1. 選擇 Azure 入口網站左上角的 [建立資源]。

2. 在 Azure Marketplace 資源清單上方的搜尋方塊中，搜尋並選取 [適用於容器的 Web 應用程式]。

3. 提供應用程式名稱 (例如 *mywebapp*)，接受預設值以建立新的資源群組，然後按一下 [作業系統] 方塊中的 [Windows (預覽)]。

    ![](media/app-service-web-get-started-windows-container/portal-create-page.png)

4. 按一下 [App Service 方案/位置] > [新建]，以建立 App Service 方案。 為新方案指定名稱，並接受預設值，然後按一下 [確定]。

    ![](media/app-service-web-get-started-windows-container/portal-create-plan.png)

5. 按一下 [設定容器]，在 [映像和選擇性標記] 中輸入 _microsoft/iis:latest_，然後按一下 [確定]。

    ![](media/app-service-web-get-started-windows-container/portal-configure-container.png)

    在本文中，您會使用公用的 [microsoft/iis:latest](https://hub.docker.com/r/microsoft/iis/) Docker Hub 映像。 如果您在他處有用於 Web 應用程式的自訂映像 (例如，在 [Azure Container Registry](/azure/container-registry/) 中或任何其他私人存放庫中)，您可以在此處加以設定。

6. 按一下 [建立]，並等候 Azure 建立所需的資源。

## <a name="browse-to-the-container-app"></a>瀏覽至容器應用程式

Azure 作業完成時，會顯示通知方塊。

![](media/app-service-web-get-started-windows-container/portal-create-finished.png)

1. 按一下 [前往資源]。

2. 在應用程式頁面中，按一下 [URL] 下方的連結。

新的瀏覽器頁面隨即開啟，並顯示下列頁面：

![](media/app-service-web-get-started-windows-container/app-starting.png)

在幾分鐘後再試一次，直到您看到 IIS 歡迎頁面：

![](media/app-service-web-get-started-windows-container/app-running.png)

**恭喜！** 您正在 Azure App Service 中執行您的第一個自訂 Windows 容器。

## <a name="see-container-start-up-logs"></a>檢視容器啟動記錄

Windows 容器載入可能需要一些時間。 若要查看進度，請將 *\<app_name>* 取代為您的應用程式名稱，以瀏覽至下列 URL。
```
https://<app_name>.scm.azurewebsites.net/api/logstream
```

串流處理的記錄會如下所示：

```
2018-07-27T12:03:11  Welcome, you are now connected to log-streaming service.
27/07/2018 12:04:10.978 INFO - Site: win-container-demo - Start container succeeded. Container: facbf6cb214de86e58557a6d073396f640bbe2fdec88f8368695c8d1331fc94b
27/07/2018 12:04:16.767 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.017 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.020 INFO - Site: win-container-demo - Container started successfully
```

## <a name="use-a-different-docker-image"></a>使用不同的 Docker 映像

您可以自由使用不同的自訂 Docker 映像來執行應用程式。 不過，您必須為您要的架構選擇正確的[父映像](https://docs.docker.com/develop/develop-images/baseimages/)： 

- 若要部署 .NET Framework 應用程式，請根據 Windows Server Core 2016 [長期維護通道 (LTSC)](https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview#long-term-servicing-channel-ltsc) 版本使用正確的父映像。 
- 若要部署 .NET Core 應用程式，請根據 Windows Server Nano 2016 [長期維護通道 (LTSC)](https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview#long-term-servicing-channel-ltsc) 版本使用正確的父映像。 

在應用程式啟動期間，下載父映像需要一些時間。 不過，您可以使用下列其中一個已在 Azure App Service 中快取的父映像，以縮短啟動時間：

- [microsoft/iis](https://hub.docker.com/r/microsoft/iis/):windowsservercore-ltsc2016 (最新)
- [microsoft/iis](https://hub.docker.com/r/microsoft/iis/):nanoserver-sac2016
- [microsoft/aspnet](https://hub.docker.com/r/microsoft/aspnet/):4.7.2-windowsservercore-ltsc2016, 4.7.2 (最新)
- [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/):2.1-aspnetcore-runtime
- [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/):2.1-sdk
