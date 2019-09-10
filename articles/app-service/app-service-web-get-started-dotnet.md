---
title: 建立 C# ASP.NET Core Web 應用程式 - Azure App Service | Microsoft Docs
description: 了解如何藉由部署預設 C# ASP.NET Core Web 應用程式，在 Azure App Service 中執行 Web 應用程式。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e8c6c21efd1ea406c8293819a2889e198e8455bd
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242024"
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>在 Azure 中建立 ASP.NET Core Web 應用程式

> [!NOTE]
> 本文會將應用程式部署至 Windows 上的 App Service。 若要部署至 _Linux_ 上的 App Service，請參閱[在 Linux 上的 App Service 中建立 NET Core Web 應用程式](./containers/quickstart-dotnetcore.md)。
>

[Azure App Service](overview.md) 可提供可高度擴充、自我修復的 Web 主控服務。

本快速入門會顯示如何將第一個 ASP.NET Core Web 應用程式部署至 Azure App Service。 當您完成時，您會有已部署 Web 應用程式的資源群，其中包含 App Service 方案和 App Service 應用程式。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，請安裝 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> 和 **ASP.NET 以及 Web 開發**工作負載。

若您已安裝 Visual Studio 2019：

- 選取 [說明]   > [檢查更新]  以安裝最新的 Visual Studio 更新。
- 選取 [工具]   > [取得工具及功能]  。

## <a name="create-an-aspnet-core-web-app"></a>建立 ASP.NET Core Web 應用程式

請遵循下列步驟來建立 ASP.NET Core Web 應用程式：

1. 開啟 Visual Studio，然後選取 [建立新專案]  。

1. 在 [建立新專案]  中，針對 C# 尋找並選擇 [ASP.NET Core Web 應用程式]  ，然後選取 [下一步]  。

1. 在 [設定新專案]  中，將應用程式命名為 myFirstAzureWebApp  ，然後選取 [建立]  。

   ![設定 Web 應用程式專案](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. 在本快速入門中，選擇 [Web 應用程式]  範本。 請確定驗證已設定為 [不需要驗證]  ，且未選取其他選項。 選取 [建立]  。

   ![針對此教學課程選取 ASP.NET Core Razor 頁面](./media/app-service-web-get-started-dotnet/aspnet-razor-pages-app.png)

    您可以將任何類型的 ASP.NET Core Web 應用程式部署至 Azure。

1. 從 Visual Studio 功能表中，選取 [偵錯]   > [啟動但不偵錯]  以在本機執行 Web 應用程式。

   ![在本機執行應用程式](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="publish-your-web-app"></a>發佈 Web 應用程式

1. 在 [方案總管]  中，以滑鼠右鍵按一下 **myFirstAzureWebApp** 專案，然後選取 [發佈]  。

1. 選擇 [App Service]  ，然後選取 [發佈]  。

   ![從專案概觀頁面發佈](./media/app-service-web-get-started-dotnet/publish-app-vs2019.png)

1. 在 [App Service 建立新的]  中，您的選項取決於您是否已登入 Azure，以及是否有將 Visual Studio 帳戶連結至 Azure 帳戶。 選取 [新增帳戶]  或 [登入]  來登入 Azure 訂用帳戶。 若您已經登入，請選取要使用的帳戶。

   > [!NOTE]
   > 如果您已經登入，請勿選取 [建立]  。
   >

   ![登入 Azure](./media/app-service-web-get-started-dotnet/sign-in-azure-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. 在 [資源群組]  中選取 [新增]  。

1. 在 [新增資源群組名稱]  中，輸入 myResourceGroup  ，然後選取 [確定]  。

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. 在 [主控方案]  中選取 [新增]  。

1. 在 [設定主控方案]  對話方塊中，輸入下表中的值，然後選取 [確定]  。

   | 設定 | 建議的值 | 說明 |
   |-|-|-|
   |App Service 方案| myAppServicePlan | App Service 方案的名稱。 |
   | Location | 西歐 | 裝載 Web 應用程式的資料中心。 |
   | 大小 | 免費 | [定價層](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)可決定裝載功能。 |

   ![建立 App Service 方案](./media/app-service-web-get-started-dotnet/app-service-plan-vs2019.png)

1. 在 [名稱]  中，輸入唯一的應用程式名稱 (只包含 `a-z`、`A-Z`、`0-9` 和 `-` 等有效字元)。 您可以接受自動產生的唯一名稱。 Web 應用程式的 URL 是 `http://<app_name>.azurewebsites.net`，其中 `<app_name>` 是您的應用程式名稱。

   ![設定應用程式名稱](./media/app-service-web-get-started-dotnet/web-app-name-vs2019.png)

1. 選取 [建立]  開始建立 Azure 資源。

精靈完成後，它會將 ASP.NET Core Web 應用程式發佈至 Azure，然後在預設瀏覽器中啟動該應用程式。

![Azure 中已發佈的 ASP.NET Web 應用程式](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

在 [App Service 建立新的]  頁面中指定的應用程式名稱，會作為格式 `http://<app_name>.azurewebsites.net` 中的 URL 前置詞。

**恭喜！** ASP.NET Core Web 應用程式已在 Azure App Service 中即時執行。

## <a name="update-the-app-and-redeploy"></a>更新應用程式並重新部署

1. 在 [方案總管]  的專案底下，開啟 [頁面]   > [Index.cshtml]  。

1. 以下列程式碼取代這兩個 `<div>` 標記：

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. 若要重新部署至 Azure，請在 [方案總管]  中，以滑鼠右鍵按一下 **myFirstAzureWebApp** 專案，然後選取 [發佈]  。

1. 在 [發佈]  摘要頁面中，選取 [發佈]  。

   ![Visual Studio 發佈摘要頁面](./media/app-service-web-get-started-dotnet/publish-summary-page-vs2019.png)

發佈完成時，Visual Studio 會啟動瀏覽器以前往 Web 應用程式的 URL。

![Azure 中已更新的 ASP.NET Web 應用程式](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-app"></a>管理 Azure 應用程式

1. 請移至 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>，以管理 Web 應用程式。

1. 從左側功能表，選取 [應用程式服務]  ，然後選取 Azure 應用程式的名稱。

   ![入口網站瀏覽至 Azure 應用程式](./media/app-service-web-get-started-dotnet/access-portal-vs2019.png)

   您會看到 Web 應用程式的 [概觀] 頁面。 您可以在這裡進行基本管理，像是瀏覽、停止、啟動、重新啟動及刪除。

   ![Azure 入口網站中的 App Service](./media/app-service-web-get-started-dotnet/web-app-general-vs2019.png)

   左側功能表提供不同的頁面來設定您的應用程式。

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [ASP.NET Core 搭配 SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)
