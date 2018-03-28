---
title: 使用 Application Insights Profiler 來分析 ASP.NET Core Azure Linux Web 應用程式 | Microsoft Docs
description: 啟用方式的概念概觀以及逐步教學課程
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: mbullwin
ms.openlocfilehash: 2d7405baee84b53311f01e748ca7975147c107d8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/16/2018
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>使用 Application Insights Profiler 來分析 ASP.NET Core Azure Linux Web 應用程式

此功能目前為預覽狀態

了解在使用 [Application Insights ](app-insights-overview.md) 時，即時 Web 應用程式的每個方法各使用了多少時間。 Profiler 現在可在應用程式服務上供裝載於 Linux 中的 ASP.NET Core Web 應用程式使用。 本指南提供逐步指示，說明如何針對 ASP.NET Core Linux Web 應用程式收集分析工具追蹤。

完成本逐步解說之後，您的應用程式將會收集類似以下螢幕擷取畫面的分析工具追蹤。 在此範例中，分析工具追蹤會指出特定的 Web 要求變慢是因為將大部分的時間花費在等候上。 程式碼中使應用程式變慢的最忙碌路徑之前會加上火焰圖示。 這個範例會示範 `HomeController` 中的 `About` 方法使 Web 應用程式變慢是因為它在呼叫 `Thread.Sleep`。

![分析工具追蹤](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="pre-requisites"></a>先決條件
下列指示適用於所有 Windows、Linux 和 Mac 開發環境：

* 安裝 [.NET Core SDK 2.1.2 或更新版本](https://www.microsoft.com/net/download/windows/build)
* 遵循[使用者入門 - 安裝 Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) 中的指示來安裝 Git

## <a name="setup-project-locally"></a>在本機設定專案

1. 在您的機器上開啟命令提示字元。 下列指示適用於所有 Windows、Linux 和 Mac 開發環境。

2. 建立 ASP.NET Core MVC Web 應用程式
    ```
    dotnet new mvc -n LinuxProfilerTest
    ```
3. 將命令提示字元中的目錄變更為專案根資料夾

4. 新增 Nuget 套件來收集分析工具追蹤。
    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```
5. 新增程式碼以在 HomeController.cs 中隨機延遲幾秒鐘的時間

    ```csharp
        using System.Threading;
        ...

        public IActionResult About()
            {
                Random r = new Random();
                int delay = r.Next(5000, 10000);
                Thread.Sleep(delay);
                return View();
            }
    ```
6. 儲存並認可您對本機存放庫的變更

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-azure-app-service-for-hosting-your-project"></a>建立 Azure App Service 來裝載您的專案
1. 建立應用程式服務 Linux 環境

    ![建立 Linux 應用程式服務](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. 建立部署認證。 記下您的密碼，因為稍後當您部署應用程式時需要這個密碼。

    ![建立部署認證](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. 選擇部署選項。 請遵循 Azure 入口網站上的指示，在 Web 應用程式中設定本機 Git 存放庫。 系統將會自動建立 Git 存放庫。

    ![設定 Git 存放庫](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

[這裡](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type)提供更多部署選項

## <a name="deploy-your-project"></a>部署專案

1. 在命令提示字元中，巡覽至您的專案根資料夾。 新增 Git 遠端存放庫以指向應用程式服務上的根資料夾：

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```
    * 使用「建立部署認證」步驟中的「使用者名稱」。
    * 使用「建立 App Service」步驟中的「應用程式名稱」。

2. 將變更推送至 Azure 來部署專案

    ```
    git push azure master
    ```
您將看到類似以下的輸出：

    ```
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>新增 Application Insights 以監視您的 Web 應用程式
1. [建立 Application Insights 資源](./app-insights-create-new-resource.md)
2. 複製 Application Insights 資源的 iKey，並在您的 App Service 中設定下列設定

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![設定應用程式設定](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    變更應用程式設定會自動將網站重新啟動。 一旦套用新的設定後，分析工具會立即開始執行 2 分鐘。 然後它每小時會執行 2 分鐘。

3. 產生一些流向網站的流量。 您可以將網站 ```About``` 頁面重新整理幾次。

4. 等待 2-5 分鐘，讓系統可將事件彙總至 Application Insights。

5. 在 Azure 入口網站中巡覽至 Application Insights 效能窗格。 您在右下角會看到可用的分析工具追蹤。

    ![檢視追蹤](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="next-steps"></a>後續步驟
如果您要使用應用程式服務所裝載的自訂容器，請依照[為容器化的 ASP.NET Core 應用程式啟用服務分析工具](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp)中的指示啟用 App Insights Profiler

如果您有任何問題或建議，請向我們的 Github 存放庫報告：[ApplicationInsights-Profiler-AspNetCore：問題](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues)
