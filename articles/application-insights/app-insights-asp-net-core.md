---
title: ASP.NET Core 的 Azure Application Insights | Microsoft Docs
description: 監視 Web 應用程式的可用性、效能和使用方式。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: 261bc78bfe427173ba81eef731e33eddd2ec379b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294270"
---
# <a name="application-insights-for-aspnet-core"></a>ASP.NET Core 的 Application Insights

Azure Application Insights 可提供程式碼層級的深入 Web 應用程式監視。 您可輕鬆監視 Web 應用程式的可用性、效能和使用情形。 還可讓您快速識別並診斷應用程式的錯誤，不必等使用者回報。

本文逐步引導您在 Visual Studio 中建立 ASP.NET Core 的 [Razor 頁面](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio)應用程式範例，以及如何使用 Azure Application Insights 開始監視。

## <a name="prerequisites"></a>先決條件

- .NET Core 2.0.0 SDK 或更新版本。
- 包含 ASP.NET 和 Web 開發工作負載的 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 15.7.3 版或更新版本。 

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>在 Visual Studio 中建立 ASP.NET Core 專案

1. 以滑鼠右鍵按一下 **Visual Studio 2017**，然後以系統管理員身分將其啟動。
2. 選取 [檔案] > [新增] > [專案] (Ctrl-Shift-N)。

   ![Visual Studio 檔案新增專案功能表的螢幕擷取畫面](./media/app-insights-asp-net-core/001-new-project.png)

3. 展開 [Visual C#] > 選取 [.NET Core] > [ASP.NET Core Web 應用程式]。 輸入 [名稱] > [解決方案名稱]> 勾選 [建立新的 Git 存放庫]。

   ![Visual Studio 檔案新增專案精靈的螢幕擷取畫面](./media/app-insights-asp-net-core/002-asp-net-core-web-application.png)

4. 選取 [.Net Core] > [ASP.NET Core 2.0] [Web 應用程式] > [確定]。

    ![Visual Studio 檔案新增專案選取項目功能表的螢幕擷取畫面](./media/app-insights-asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Application Insights 搜尋

根據 Visual Studio 2015 Update 2 或更新版本與 ASP.NET Core 2 + 為基礎的專案的預設，您甚至可以在您明確地將 Application Insights 加入您的專案前善用 [Application Insights 搜尋](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-visual-studio)。

若要測試這項功能：

1. 按一下 IIS Express 以執行應用程式 ![Visual Studio IIS Express 圖示的螢幕擷取畫面](./media/app-insights-asp-net-core/004-iis-express.png)

2. 選取**檢視** > **其他視窗** > **Application Insights 搜尋**。

   ![Visual Studio 診斷工具的螢幕擷取畫面](./media/app-insights-asp-net-core/005-view-other-windows-search.png)

3. 偵錯工作階段遙測目前只提供做為本機分析。 若要完全啟用 Application Insights，請選取右上角的**遙測整備**，或遵循下一節中的步驟。

   ![Visual Studio Application Insights 搜尋的螢幕擷取畫面](./media/app-insights-asp-net-core/006-search.png)

> [!NOTE]
> 若要深入了解 Visual Studio 如何強化本機的 [Application Insights 搜尋](app-insights-visual-studio.md)和 [CodeLens](app-insights-visual-studio-codelens.md) 功能，請在您將 Application Insights 加入 ASP.NET Core 專案前 參閱[本文結尾的說明。](#Application-Insights-search-continued)

## <a name="add-application-insights-telemetry"></a>新增 Application Insights 遙測

1. 選取 **[專案]**  > **[新增 Application Insights 遙測...]**。(或者您可以滑鼠右鍵按一下 [已連線的服務]然後選取 [新增已連線的服務]。)

    ![Visual Studio 檔案新增專案選取項目功能表的螢幕擷取畫面](./media/app-insights-asp-net-core/007-project-add-telemetry.png)

2. 選取 **[馬上開始]** 。 （視您的 Visual Studio 版本而定，內容可能會稍微不同。 一些較舊版本會有「免費開始」按鈕。)

    ![Visual Studio 檔案新增專案選取項目功能表的螢幕擷取畫面](./media/app-insights-asp-net-core/008-get-started.png)

3. 選取適當的 **[訂閱]** > **[資源]** > **[註冊]**。

## <a name="changes-made-to-your-project"></a>對您專案進行的變更

Application Insights 為低負荷。 若要檢視新增 Application Insights 遙測後對專案造成的修改：

選取 [檢視] > [Team Explorer] (Ctrl+\, Ctrl+M) > [專案] > [變更]

- 總計四個變更：

  ![新增 Application Insights 後的檔案變更螢幕擷取畫面](./media/app-insights-asp-net-core/009-changes.png)

- 已建立一個新檔案：

   _ConnectedService.json_

```json
{
  "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
  "Version": "8.12.10405.1",
  "GettingStartedDocument": {
    "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
  }
}
```

- 三個檔案已修改：(新增其他註解以醒目顯示變更)

  _appsettings.json_

```json
{
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Warning"
    }
  },
// Changes to file post adding Application Insights Telemetry:
  "ApplicationInsights": {
    "InstrumentationKey": "10101010-1010-1010-1010-101010101010"
  }
}
//
```

  _ContosoDotNetCore.csproj_

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp2.0</TargetFramework>
 <!--Changes to file post adding Application Insights Telemetry:-->
    <ApplicationInsightsResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsResourceId>
    <ApplicationInsightsAnnotationResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsAnnotationResourceId>
<!---->
  </PropertyGroup>
  <ItemGroup>
 <!--Changes to file post adding Application Insights Telemetry:-->
    <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.1.1" />
<!---->
    <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.8" />
  </ItemGroup>
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
  </ItemGroup>
<!--Changes to file post adding Application Insights Telemetry:-->
  <ItemGroup>
    <WCFMetadata Include="Connected Services" />
  </ItemGroup>
<!---->
</Project>
```

   _Program.cs_

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace DotNetCore
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
// Change to file post adding Application Insights Telemetry:
                .UseApplicationInsights()
//
                .UseStartup<Startup>()
                .Build();
    }
}
```

## <a name="synthetic-transactions-with-powershell"></a>使用 PowerShell 的綜合交易

若要對應用程式自動要求綜合交易。

1. 按一下 IIS Express 以執行應用程式 ![Visual Studio IIS Express 圖示的螢幕擷取畫面](./media/app-insights-asp-net-core/004-iis-express.png)

2. 從瀏覽器的網址列複製 URL。 它的格式是 http://localhost:{random port number}

   ![瀏覽器 URL 網址列的螢幕擷取畫面](./media/app-insights-asp-net-core/0013-copy-url.png)

3. 執行下列 PowerShell 迴圈，以針對您的測試應用程式建立 100 個綜合交易。 修改 **localhost:** 後的連接埠號碼，以符合您在上一個步驟中複製的 URL。

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>開啟 Application Insights 入口網站

在執行上一節中的 PowerShell 之後，啟動 Application Insights 以檢視交易，並確認已收集資料。 

從 Visual Studio 功能表中，選取 [專案] > [Application Insights] > [開啟 Application Insights 入口網站]

   ![Application Insights 概觀的螢幕擷取畫面](./media/app-insights-asp-net-core/010-portal.png)

> [!NOTE]
> 在上述範例螢幕擷取畫面中，目前未收集 [即時資料流]、[網頁檢視載入時間] 和 [失敗的要求]。 下一節將逐步解說如何新增這每一個項目。 如果您已經在收集 [即時資料流] 和 [網頁檢視載入時間]，則請僅遵循 [失敗的要求] 的步驟。

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>收集失敗的要求、即時資料流，以及網頁檢視載入時間

### <a name="failed-requests"></a>失敗的要求

技術上而言，系統會收集 [失敗的要求]，但尚未發生任何失敗的要求。 若要加快程序，可將自訂的例外狀況新增至現有專案，以模擬真實的例外狀況。 如果您的應用程式仍在 Visual Studio 中執行，在繼續之前，請 [停止偵錯] (Shift + F5)

1. 在 [方案總管] **中** > 展開 [頁面] > [About.cshtml] > 開啟 [About.cshtml.cs]。

   ![Visual Studio 方案總管的螢幕擷取畫面](./media/app-insights-asp-net-core/011-about.png)

2. 在 ``Message=`` 下新增例外狀況並將變更儲存至檔案。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc.RazorPages;

    namespace DotNetCore.Pages
    {
        public class AboutModel : PageModel
        {
            public string Message { get; set; }

            public void OnGet()
            {
                Message = "Your application description page.";
                throw new Exception("Test Exception");
            }
        }
    }
    ```

### <a name="live-stream"></a>即時資料流

若要存取 Application Insights 的即時資料流功能，請將 ASP.NET Core 更新為 **Microsoft.ApplicationInsights.AspNetCore 2.2.0** NuGet 套件。

從 Visual Studio 中，選取 [專案] > [管理 NuGet 套件] > [Microsoft.ApplicationInsights.AspNetCore] > [版本 2.2.0] > [更新]。

  ![NuGet 套件管理員的螢幕擷取畫面](./media/app-insights-asp-net-core/012-nuget-update.png)

會發生多個確認提示。 詳閱之後，如果您同意所做的變更，請接受。

### <a name="page-view-load-time"></a>網頁檢視載入時間

1. 在 Visual Studio 中，瀏覽至 **[方案總管]** > **[頁面]** > 兩個檔案將需要修改：_Layout.cshtml_ 和 _ViewImports.cshtml_

2. 在 __ViewImports.cshtml_ 中，新增：

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. 在 **_Layout.cshtml** 中的 ``</head>`` 標籤之前新增下列行 (但同樣要在任何其他指令碼之前)。

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>測試失敗的要求、網頁檢視載入時間、即時資料流

若要測試並確認一切運作正常：

1. 按一下 IIS Express 以執行應用程式 ![Visual Studio IIS Express 圖示的螢幕擷取畫面](./media/app-insights-asp-net-core/0012-iis-express.png)

2. 瀏覽至 [關於] 頁面以觸發測試例外狀況。 (如果您正在偵錯模式中執行，在 Application Insights 出現例外狀況時，您必須按一下 Visual Studio 中的 **[繼續]**。)

3. 重新執行前面的模擬 PowerShell 交易指令碼 (您可能需要調整指令碼中的連接埠號碼)。

4. 如果 Application Insights 概觀仍未開啟，請從 Visual Studio 功能表中選取 [專案] > [Application Insights] > [開啟 Application Insights 入口網站]。 

   > [!TIP]
   > 如果您還沒有看到新的流量，請檢查 [時間範圍]，然後按一下 [重新整理]。

   ![概觀視窗的螢幕擷取畫面](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. 選取即時資料流

   ![即時計量資料流的螢幕擷取畫面](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (如果您的 PowerShell 指令碼仍在執行，您應該會看到即時計量，如果它已停止，請再次執行指令碼，並讓即時資料流維持開啟狀態。)

## <a name="app-insights-sdk-comparison"></a>App Insights SDK 的比較

Application Insights 產品群組已致力於達到[完整 .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)與 .Net Core SDK 之間的功能同位。 Application Insights 的 2.2.0 版 [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) 已大幅度縮小功能差距。

深入了解 [.NET 與 .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server)之間的差異和權衡。

   | SDK 比較 | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **即時計量**      | **+** |**-** | **+** |
   | **伺服器遙測通道** | **+** |**-** | **+**|
   |**調適型取樣**| **+** | **-** | **+**|
   | **SQL 相依性呼叫數**     | **+** |**-** | **+**|
   | **效能計數器*** | **+** | **-**| **-**|

此內容中的_效能計數器_是指[伺服器端效能計數器](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters)，例如處理器、記憶體和磁碟使用狀況。

## <a name="open-source-sdk"></a>開放原始碼 SDK
[讀取和貢獻程式碼](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="application-insights-search-continued"></a>繼續 Application Insights 搜尋

如要進一步瞭解 Application Insights 搜尋如何在 Visual Studio 中執行 ASP.NET Core 2 專案 (即使您尚未明確安裝 Application Insights NuGet 封裝)。 建議您可以檢視偵錯輸出。

如要搜尋 _insight_ 這個字的輸出，會反白顯示為類似下列的結果：

```DebugOuput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

CoreCLR 正在載入兩個組件： 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_。

Application Insights 遙測每個執行個體的_未設定_指出此應用程式未 與金鑰建立關聯，因此當應用程式執行時產生的資料不會傳送至 Azure，而只能在本機中搜尋和分析。

部分原因是 NuGet 封裝 _Microsoft.AspNetCore.All_ 採用相依項 [ _Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1)

![Microsoft.AspNETCore.all 的 NuGet 相依性關係圖之螢幕擷取畫面](./media/app-insights-asp-net-core/013-dependency.png)

Visual Studio 之外，如果您在 VSCode 或某些其他編輯器中編輯 ASP.NET Core 專案，如果您尚未將 Application Insights 明確加入您的專案，則這些組件不會在偵錯期間自動載入。

不過，在 Visual Studio 從外部組件強化本機 Application Insights 功能時，會透過使用 [IHostingStartup 介面](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1)在偵錯期間以動態方式加入 Application Insights。

若要深入了解如何[透過 IHostingStartup 從 ASP.NET Core 的外部組件](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1)強化應用程式。 

### <a name="how-to-disable-application-insights-in-visual-studio-net-core-projects"></a>如何停用 Visual Studio.NET Core 專案中的 Application Insights

儘管 Application Insights 搜尋功能的自動強化可能在部分情況下很實用，但在未預期的情況下看到產生偵錯遙測則可能會相當令人困惑。

如果只停用遙測產生就已足夠，您可以將此程式碼區塊加入您 _Startup.cs_ 檔案的設定方法中：

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

CoreCLR 仍然會載入 _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_和_Microsoft.ApplicationInsights.AspNetCore.dll_，但是不會執行任何動作。

如果您想要完全停用您的 Visual Studio.NET Core 專案中的 Application Insights，建議您可以選取 [工具] > [選項] > [專案和解決方案] > [Web 專案]> 然後勾選方塊，以停用本機中 ASP.NET Core web 專案的 Application Insights。 Visual Studio 15.6 已加入這項功能。

![Visual Studio 選項視窗 Web 專案畫面的螢幕擷取畫面](./media/app-insights-asp-net-core/014-disable.png)

如果您正在執行舊版的 Visual Studio ，而且您想要完全移除所有您透過 IHostingStartup 載入的組件，則您可以加入：

`.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")`

_Program.cs_：

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace DotNetCore
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
                .UseStartup<Startup>()
                .Build();
    }
}
```

或者，您可以新增 ``"ASPNETCORE_preventHostingStartup": "True"``至_launchSettings.json_ 環境變數。

使用其中一種方法的問題是它不會只停用 Application Insights，而是會停用 Visual Studio 中任何利用 IHostingStartup 強化功能的項目。

## <a name="video"></a>影片

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>後續步驟
* [瀏覽使用者流程](app-insights-usage-flows.md)來了解使用者如何瀏覽您的應用程式。
* [設定快照集集合](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications)，以查看擲回例外狀況時原始程式碼和變數的狀態。
* [使用 API](app-insights-api-custom-events-metrics.md) 可傳送您自己的事件和計量，以取得您的應用程式效能和使用方式的更詳細檢視。
* [可用性測試](app-insights-monitor-web-app-availability.md) 可持續從世界各地檢查您的應用程式。
