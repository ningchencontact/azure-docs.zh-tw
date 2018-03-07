---
title: "ASP.NET Core 的 Azure Application Insights | Microsoft Docs"
description: "監視 Web 應用程式的可用性、效能和使用方式。"
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: mbullwin
ms.openlocfilehash: e9fb3e68db66449d9ca3b43e6974910cb9477e62
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2018
---
# <a name="application-insights-for-aspnet-core"></a>ASP.NET Core 的 Application Insights

Azure Application Insights 可提供程式碼層級的深入 Web 應用程式監視。 您可輕鬆監視 Web 應用程式的可用性、效能和使用情形。 還可讓您快速識別並診斷應用程式的錯誤，不必等使用者回報。

本文逐步引導您在 Visual Studio 中建立 ASP.NET Core 的 [Razor 頁面](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio)應用程式範例，以及如何使用 Azure Application Insights 開始監視。

## <a name="prerequisites"></a>先決條件

- .NET Core 2.0.0 SDK 或更新版本。
- 包含 ASP.NET 和 Web 開發工作負載的 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 15.3 版或更新版本。

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>在 Visual Studio 中建立 ASP.NET Core 專案

1. 以滑鼠右鍵按一下 **Visual Studio 2017**，然後以系統管理員身分將其啟動。
2. 選取 [檔案] > [新增] > [專案] (Ctrl-Shift-N)。

   ![Visual Studio 檔案新增專案功能表的螢幕擷取畫面](./media/app-insights-asp-net-core/0001-file-new-project.png)

3. 展開 [Visual C#] > 選取 [.NET Core] > [ASP.NET Core Web 應用程式]。 輸入 [名稱] > [解決方案名稱]> 勾選 [建立新的 Git 存放庫]。

   ![Visual Studio 檔案新增專案精靈的螢幕擷取畫面](./media/app-insights-asp-net-core/0002-new-project-web-application.png)

4. 選取 [.Net Core] > [ASP.NET Core 2.0] [Web 應用程式] > [確定]。

    ![Visual Studio 檔案新增專案選取項目功能表的螢幕擷取畫面](./media/app-insights-asp-net-core/0003-dot-net-core.png)

## <a name="add-application-insights-telemetry"></a>新增 Application Insights 遙測

1. 選取 [專案] > [新增 Application Insights 遙測...](或者您可以滑鼠右鍵按一下 [已連線的服務]> [新增已連線的服務]。)

    ![Visual Studio 檔案新增專案選取項目功能表的螢幕擷取畫面](./media/app-insights-asp-net-core/0004-add-application-insights-telemetry.png)

2. 選取 [開始免費使用]。

    ![Visual Studio 檔案新增專案選取項目功能表的螢幕擷取畫面](./media/app-insights-asp-net-core/0005-start-free.png)

3. 選取適當的 [訂用帳戶] > [資源]> 以及是否允許每個月超過 1 GB 的集合 > [註冊]。

    ![Visual Studio 檔案新增專案選取項目功能表的螢幕擷取畫面](./media/app-insights-asp-net-core/0006-register.png)

## <a name="changes-made-to-your-project"></a>對您專案進行的變更

Application Insights 的負荷極低。 若要檢視新增 Application Insights 遙測後對專案造成的修改：

選取 [檢視] > [Team Explorer] (Ctrl+\, Ctrl+M) > [專案] > [變更]

- 總計四個變更：

  ![新增 Application Insights 後的檔案變更螢幕擷取畫面](./media/app-insights-asp-net-core/0007-changes.png)

- 已建立一個新檔案：

   **ConnectedService.json**

  ![新增 Application Insights 後的檔案變更螢幕擷取畫面](./media/app-insights-asp-net-core/0008-connectedservice-json.png)

- 已修改三個檔案：

  **appsettings.json**

   ![新增 Application Insights 後的檔案變更螢幕擷取畫面](./media/app-insights-asp-net-core/0009-appsettings-json.png)

  **ContosoDotNetCore.csproj**

   ![新增 Application Insights 後的檔案變更螢幕擷取畫面](./media/app-insights-asp-net-core/0010-contoso-netcore-csproj.png)

   **Program.cs**

   ![新增 Application Insights 後的檔案變更螢幕擷取畫面](./media/app-insights-asp-net-core/0011-program-cs.png)

## <a name="synthetic-transactions-with-powershell"></a>使用 PowerShell 的綜合交易

啟動您的應用程式，然後手動按一下幾個連結可用來產生測試流量。 而這通常有助於在 PowerShell 中建立簡單的綜合交易。

1. 按一下 IIS Express 以執行應用程式 ![Visual Studio IIS Express 圖示的螢幕擷取畫面](./media/app-insights-asp-net-core/0012-iis-express.png)

2. 從瀏覽器的網址列複製 URL。 格式為 http://localhost:{隨機連接埠號碼}

   ![瀏覽器 URL 網址列的螢幕擷取畫面](./media/app-insights-asp-net-core/0013-copy-url.png)

3. 執行下列 PowerShell 迴圈，以針對您的測試應用程式建立 100 個綜合交易。 修改 **localhost:** 後的連接埠號碼，以符合您在上一個步驟中複製的 URL。

   ```PS
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>開啟 Application Insights 入口網站

在執行上一節中的 PowerShell 之後，啟動 Application Insights 以檢視交易，並確認已收集資料。 

從 Visual Studio 功能表中，選取 [專案] > [Application Insights] > [開啟 Application Insights 入口網站]

   ![Application Insights 概觀的螢幕擷取畫面](./media/app-insights-asp-net-core/0014-portal-01.png)

> [!NOTE]
> 在上述範例螢幕擷取畫面中，目前未收集 [即時資料流]、[網頁檢視載入時間] 和 [失敗的要求]。 下一節將逐步解說如何新增這每一個項目。 如果您已經在收集 [即時資料流] 和 [網頁檢視載入時間]，則請僅遵循 [失敗的要求] 的步驟。

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>收集失敗的要求、即時資料流，以及網頁檢視載入時間

### <a name="failed-requests"></a>失敗的要求

技術上而言，系統會收集 [失敗的要求]，但尚未發生任何失敗的要求。 若要加快程序，可將自訂的例外狀況新增至現有專案，以模擬真實的例外狀況。 如果您的應用程式仍在 Visual Studio 中執行，在繼續之前，請 [停止偵錯] (Shift + F5)

1. 在 [方案總管] **中** > 展開 [頁面] > [About.cshtml] > 開啟 [About.cshtml.cs]。

   ![Visual Studio 方案總管的螢幕擷取畫面](./media/app-insights-asp-net-core/0015-solution-explorer-about.png)

2. 在 ``Message=`` 下新增例外狀況 > 將變更儲存至檔案。

   ```C#
   throw new Exception("Test Exception");
   ```

   ![例外狀況程式碼的螢幕擷取畫面](./media/app-insights-asp-net-core/000016-exception.png)

### <a name="live-stream"></a>即時資料流

若要存取 Application Insights 的即時資料流功能，請將 ASP.NET Core 更新為 **Microsoft.ApplicationInsights.AspNetCore 2.2.0** NuGet 套件。

從 Visual Studio 中，選取 [專案] > [管理 NuGet 套件] > [Microsoft.ApplicationInsights.AspNetCore] > [版本 2.2.0] > [更新]。

  ![NuGet 套件管理員的螢幕擷取畫面](./media/app-insights-asp-net-core/0017-update-nuget.png)

將會發生多個確認提示，請閱讀並接受 (如果您同意變更)。

### <a name="page-view-load-time"></a>網頁檢視載入時間

1. 在 Visual Studio 中，瀏覽至 [方案總管] > [頁面] > 兩個檔案將需要修改：**_Layout.cshtml** 和 **_ViewImports.cshtml**

2. 在 **_ViewImports.cshtml** 中，新增：

   ```C#
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```
     ![_ViewImports.cshtml 的程式碼變更螢幕擷取畫面](./media/app-insights-asp-net-core/00018-view-imports.png)

3. 在 **Layout.cshtml** 中的 ``</head>`` 標籤之前新增下列行 (但要在任何其他指令碼之前)。

    ```C#
    @Html.Raw(snippet.FullScript)
    ```
    ![layout.cshtml 的程式碼變更螢幕擷取畫面](./media/app-insights-asp-net-core/0018-layout-cshtml.png)

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>測試失敗的要求、網頁檢視載入時間、即時資料流

現在您已完成先前步驟，您可以測試並確認一切正常。

1. 按一下 IIS Express 以執行應用程式 ![Visual Studio IIS Express 圖示的螢幕擷取畫面](./media/app-insights-asp-net-core/0012-iis-express.png)

2. 瀏覽至 [關於] 頁面以觸發測試例外狀況。 (如果您正在偵錯模式中執行，在 Application Insights 收取例外狀況之前，您必須按一下 Visual Studio 中的 [繼續]。)

3. 重新執行前面的模擬 PowerShell 交易指令碼 (您可能需要調整指令碼中的連接埠號碼)。

4. 如果 Application Insights 概觀仍未開啟，請從 Visual Studio 功能表中選取 [專案] > [Application Insights] > [開啟 Application Insights 入口網站]。 

   > [!TIP]
   > 如果您還沒有看到新的流量，請檢查 [時間範圍]，然後按一下 [重新整理]。

   ![概觀視窗的螢幕擷取畫面](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. 選取即時資料流

   ![即時計量資料流的螢幕擷取畫面](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (如果您的 PowerShell 指令碼仍在執行，您應該會看到即時計量，如果已停止，請再次執行指令碼，並讓即時資料流維持開啟狀態。)

## <a name="app-insights-sdk-comparison"></a>App Insights SDK 的比較

Application Insights 產品群組已致力於盡可能達到[完整 .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)與 .Net Core SDK 之間最接近的功能同位。 Application Insights 的 2.2.0 版 [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) 已大幅度縮小功能差距。

深入了解 [.NET 與 .NET Core](https://docs.microsoft.com/en-us/dotnet/standard/choosing-core-framework-server)之間的差異和權衡。

   | SDK 比較 | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **即時計量**      | **+** |**-** | **+** |
   | **伺服器遙測通道** | **+** |**-** | **+**|
   |**調適型取樣**| **+** | **-** | **+**|
   | **SQL 相依性呼叫數**     | **+** |**-** | **+**|
   | **效能計數器*** | **+** | **-**| **-**|

此內容中的_效能計數器_是指[伺服器端效能計數器](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-performance-counters)，例如處理器、記憶體和磁碟使用狀況。

## <a name="open-source-sdk"></a>開放原始碼 SDK
[讀取和貢獻程式碼](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="video"></a>影片

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>後續步驟
* [瀏覽使用者流程](app-insights-usage-flows.md)來了解使用者如何瀏覽您的應用程式。
* [使用 API](app-insights-api-custom-events-metrics.md) 可傳送您自己的事件和計量，以取得您的應用程式效能和使用方式的更詳細檢視。
* [可用性測試](app-insights-monitor-web-app-availability.md) 可持續從世界各地檢查您的應用程式。