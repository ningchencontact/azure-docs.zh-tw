---
title: ASP.NET Core 的 Azure Application Insights | Microsoft Docs
description: 監視 ASP.NET Core Web 應用程式的可用性、效能和使用方式。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mbullwin
ms.openlocfilehash: cb7ace20fd0a59dafff3d7f8240f54c3c8e12492
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66226405"
---
# <a name="application-insights-for-aspnet-core-applications"></a>ASP.NET Core 應用程式的 application Insights

本文說明如何啟用適用於 Application Insights [ASP.NET Core](https://docs.microsoft.com/aspnet/core)應用程式。 當您完成這篇文章中的指示時，Application Insights 會開始收集從您的 ASP.NET Core 應用程式的要求、 相依性、 例外狀況、 效能計數器、 活動訊號，以及記錄檔。 範例應用程式是[MVC 應用程式](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app)目標`netcoreapp2.2`，但是這些指示也適用於所有的 ASP.NET Core 應用程式。

## <a name="supported-scenarios"></a>支援的案例

[ASP.NET Core 的 Application Insights SDK （軟體開發套件）](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)可以監視您的應用程式，無論何處或如何執行應用程式。 如果您的應用程式正在執行，且具備網路連線到 Azure，就可以收集遙測資料。 這表示，Application Insights 監視都支援.NET Core 支援的每個地方。 這項支援包括任何作業系統 (Windows、 Linux、 Mac)、 裝載方法 （同處理序與 out-處理序）、 部署方法 （framework 相依與獨立），網頁伺服器 (IIS，Kestrel，) 裝載的平台 （Azure Web Apps、 Azure VM、 Docker、Azure Kubernetes Service (AKS)，並依此類推。）或 IDE （Visual Studio 中，VS Code 中，命令列）。

## <a name="prerequisites"></a>必要條件

- 正常運作的 ASP.NET Core 應用程式。 請遵循[ASP.NET Core，getting 入門指南](https://docs.microsoft.com/aspnet/core/getting-started/)如有需要建立 ASP.NET Core 應用程式。
- 有效的 Application Insights 檢測金鑰，才能將任何遙測傳送至 Application Insights 服務。 請遵循[建立資源的指示](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)視需要建立新的 Application Insights 資源，並取得檢測金鑰。

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>啟用 Application Insights 伺服器端遙測資料 (Visual Studio)

1. 在 Visual Studio 中，開啟您的專案。

    > [!TIP]
    > 而不是必要的步驟，可以設定為您的專案的原始檔控制，讓您可以追蹤所有 Application Insights 所做的變更，很有幫助。 若要啟用原始檔控制選取**檔案** > **加入原始檔控制**。

2. 選取 [專案]   > [新增 Application Insights 遙測]  。

3. 選取 **[馬上開始]** 。 (視您的 Visual Studio 版本而定，內容可能會稍微不同。 一些較舊版本會有「免費開始」  按鈕。)

4. 選取您的訂用帳戶，然後選取 [資源]   > [註冊]  。

5. 之後將 Application Insights 新增至您的專案，請檢查以確認您使用 SDK 的最新穩定版本。 移至**專案** > **管理 NuGet 套件** > **Microsoft.ApplicationInsights.AspNetCore** > 如有需要選擇**更新**。

     ![螢幕擷取畫面以選取要更新的 Application Insights 套件管理 NuGet 套件畫面](./media/asp-net-core/update-nuget-package.png)

6. 如果後面的選擇性提示，並將您的專案加入原始檔控制您可以前往**檢視** > **Team Explorer** > **變更**和選取 加入 Application Insights 遙測資料所做的變更差異檢視每個個別檔案。

## <a name="enable-application-insights-server-side-telemetry-without-visual-studio"></a>啟用 Application Insights 伺服器端遙測資料 （不含 Visual Studio)

1. 安裝[ASP.NET Core 的 Application Insights SDK NuGet 封裝](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)。 我們建議一律使用最新穩定版本。 Sdk 的完整版本資訊可於[開啟來源 GitHub 存放庫](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)。

    下列程式碼片段會顯示要加入至您的專案變更`.csproj`檔案。

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
        </ItemGroup>
    ```

2. 新增`services.AddApplicationInsightsTelemetry();`要`ConfigureServices()`方法，在您`Startup`類別。 完整的範例如下。

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // code adding other services for your application
            services.AddMvc();
        }
    ```

3. 設定檢測金鑰。

    雖然您可以做為引數提供的檢測金鑰`AddApplicationInsightsTelemetry`，我們建議您在組態中指定的檢測金鑰。 下圖顯示如何指定中的檢測金鑰`appsettings.json`。 請確定`appsettings.json`複製到發行的應用程式根資料夾。

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    或者，檢測金鑰也可以指定在下列環境變數。

    APPINSIGHTS_INSTRUMENTATIONKEY

    ApplicationInsights:InstrumentationKey

    範例：

    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    `APPINSIGHTS_INSTRUMENTATIONKEY` 通常用來指定部署至 Azure Web Apps 的應用程式的檢測金鑰。

    > [!NOTE]
    > 透過環境變數指定在程式碼 wins 中的檢測金鑰`APPINSIGHTS_INSTRUMENTATIONKEY`，這會優先於其他選項。

## <a name="run-your-application"></a>執行您的應用程式

 執行您的應用程式，並對其發出要求。 遙測應該立即開始送至 Application Insights。 Application Insights SDK 會自動收集下列遙測資料。

|要求/相依項目 |詳細資料|
|---------------|-------|
|Requests | 您的應用程式傳入 web 要求。 |
|Http/Https | 使用呼叫`HttpClient`。 |
|SQL | 使用呼叫`SqlClient`。 |
|[Azure 儲存體](https://www.nuget.org/packages/WindowsAzure.Storage/) | 使用 Azure 儲存體用戶端所進行的呼叫。 |
|[事件中樞用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1\.1.0 版和更新版本。 |
|[ServiceBus 用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 3\.0.0 版和更新版本。 |
|Azure Cosmos DB | 只會自動追蹤如果使用 HTTP/HTTPS。 Application Insights 不會擷取 TCP 模式。 |

### <a name="performance-counters"></a>效能計數器

支援[效能計數器](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)ASP.NET Core 中僅限於下列

   * SDK 2.4.1 版和上方會收集效能計數器如果應用程式正在執行 Azure Web 應用程式 (Windows)
   * SDK 版本 2.7.0-beta3 上方會收集效能計數器如果正在執行 Windows，在應用程式，並將其目標和`NETSTANDARD2.0`或更高版本。
   * 對於以.NET Framework 為目標的應用程式，在所有版本的 SDK 支援效能計數器。
   * 在 Linux 中的效能計數器支援新增時，將會更新本文。

### <a name="ilogger-logs"></a>ILogger 記錄檔

[ILogger 記錄](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)嚴重性的`Warning`或更新版本會自動從 SDK 版本 2.7.0-beta3 擷取或更新版本。

### <a name="live-metrics"></a>即時計量

可能需要幾分鐘的時間才會開始出現在入口網站中的遙測。 若要快速檢查一切是否運作，最好是使用[即時計量](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)，而讓要求執行的應用程式。

## <a name="enable-client-side-telemetry-for-web-applications"></a>為 Web 應用程式啟用用戶端遙測

上述步驟，就能開始收集伺服器端遙測資料。 如果您的應用程式的用戶端元件，然後遵循下列步驟來開始收集[使用量遙測](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)從該處。

1. 在  `_ViewImports.cshtml`，新增插入式攻擊：

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. 在  `_Layout.cshtml`，結尾插入 HtmlHelper`<head>`區段但任何其他指令碼之前。 此程式碼片段之後，應該插入任何您想要報告從頁面的自訂 JavaScript 遙測：

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

`.cshtml`上面提及的檔案名稱是來自預設 MVC 應用程式範本。 最後，若要正確地啟用 用戶端監視應用程式您需要的 JavaScript 程式碼片段會出現在`<head>`您想要監視的應用程式的每個頁面的區段。 此應用程式 範本加入至 Javascript 程式碼片段`_Layout.cshtml`會有效地達成此目標。 如果您的專案沒有這個特定的檔案您還是可以加入[用戶端監視](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring)。 您會需要為加入 JavaScript 同等的檔案，以控制`<head>`的所有頁面在您的應用程式，或者您可以新增多個個別的程式碼片段頁面但這會很難維護，並且通常不是建議使用。

## <a name="configuring-application-insights-sdk"></a>設定 Application Insights SDK

可以自訂 ASP.NET Core 的 application Insights SDK，以變更預設設定。 Application Insights ASP.NET SDK 的使用者可能很熟悉組態中使用`ApplicationInsights.config`，或藉由修改`TelemetryConfiguration.Active`。 針對 ASP.NET Core，是以不同的方式進行設定。 加入至應用程式並設定使用 ASP.NET Core 內建 ASP.NET Core SDK[相依性插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)。 幾乎所有的組態變更會在中完成`ConfigureServices()`方法的程式`Startup.cs`類別，除非另外註明否則。 請遵循下列各節來了解更多。

> [!NOTE]
>  變更組態，藉由修改`TelemetryConfiguration.Active`不建議使用 ASP.NET Core 應用程式中。

### <a name="configuring-using-applicationinsightsserviceoptions"></a>設定使用 ApplicationInsightsServiceOptions

可修改的一些常見的設定，藉由傳遞`ApplicationInsightsServiceOptions`至`AddApplicationInsightsTelemetry`。 範例如下所示。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    }
```

可設定的設定中的確切清單`ApplicationInsightsServiceOptions`可以找到[這裡](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs)。

### <a name="sampling"></a>取樣

ASP.NET Core 的 application Insights SDK 支援 FixedRate 和調適型取樣。 預設會啟用調適型取樣。 請依照我們[調適型取樣指引](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)，以了解如何設定 ASP.NET Core 應用程式的取樣。

### <a name="adding-telemetryinitializers"></a>新增 TelemetryInitializers

[遙測初始設定式](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer)時您想要定義與所有遙測一起傳送的全域屬性，會使用。

若要加入新`TelemetryInitializer`，將它加入至 DependencyInjection 容器，如下所示。 `TelemetryInitializer`加入至 DependencyInjection 容器將會由 SDK 自動挑選。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>移除 TelemetryInitializers

若要移除所有或特定 TelemetryInitializers，有預設情況下，使用下列程式碼範例**之後**呼叫`AddApplicationInsightsTelemetry()`。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // Remove a specific built-in TelemetryInitializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                         (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
    }
```

### <a name="adding-telemetryprocessors"></a>新增 TelemetryProcessors

可以加入自訂的遙測處理器`TelemetryConfiguration`藉由使用擴充方法`AddApplicationInsightsTelemetryProcessor`上`IServiceCollection`。 中所使用的遙測處理器[進階篩選案例](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor)以便進行更直接地控制要包含或排除的遙測資料傳送至 Application Insights 服務。 使用下列的範例。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>設定或移除預設 TelemetryModules

Application Insights 將遙測模組使用這種[自動收集有用的資訊](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies)有關特定工作負載，而不需要額外的組態。

下列自動收集模組預設會啟用，並負責自動收集的遙測。 它們可以停用及設定以變更預設行為。

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

若要設定任何預設`TelemetryModule`，使用擴充方法`ConfigureTelemetryModule<T>`上`IServiceCollection`如下列範例所示。

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures DependencyTrackingTelemetryModule.
        // Similarly, any other default modules can be configured.
        services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
                        {
                            module.EnableW3CHeadersInjection = true;
                        });

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-telemetry-channel"></a>設定遙測通道

使用預設通道是`ServerTelemetryChannel`。 它可以覆寫下列範例。

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="i-want-to-track-additional-telemetry-other-than-the-auto-collected-telemetry-how-do-i-do-it"></a>我想要追蹤的自動收集遙測資料以外的其他遙測。 我該怎麼做？

取得的執行個體`TelemetryClient`藉由使用建構函式插入，以及呼叫所需`TrackXXX()`在其上的方法。 不建議建立新`TelemetryClient`在 ASP.NET Core 應用程式，作為單一執行個體的執行個體`TelemetryClient`已註冊在 DI 容器中，共用`TelemetryConfiguration`搭配 rest 的遙測。 建立新`TelemetryClient`必須具有不同的設定，遙測的其餘部分時，才建議使用執行個體。 下列範例示範如何追蹤來自控制站的其他遙測。

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // use constructor injection to get TelemetryClient instance
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // call required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

 請參閱[Application Insights 自訂計量 API 參考](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/)的 Application Insights 中的報告功能的自訂資料的說明。

### <a name="some-visual-studio-templates-used-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>某些 Visual Studio 範本會用上 IWebHostBuilder UseApplicationInsights() 擴充方法，來啟用 Application Insights。 這種用法是否仍然有效的？

使用這個方法來啟用 Application Insights 有效，而且可在 Visual Studio 上架，和 Azure Web 應用程式擴充功能。 不過，建議使用`services.AddApplicationInsightsTelemetry()`因為它提供多載以控制一些設定。 同時方法在內部執行相同的動作，因此，如果不沒有套用任何自訂組態，則呼叫其中一個是沒問題。

### <a name="i-am-deploying-my-aspnet-core-application-to-azure-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>我要部署我的 ASP.NET Core 應用程式至 Azure Web Apps。 仍應啟用 Application Insights 延伸模組，從 Web 應用程式嗎？

如果這篇文章中所示，將 SDK 安裝在建置階段，，則不需要啟用 Application Insights 延伸模組，從 App Service 入口網站。 即使已安裝擴充功能，它將會退出當 ata 偵測到 SDK 已經加入應用程式。 啟用 Application Insights 從延伸模組，可讓您從安裝和更新的 SDK。 不過，這篇文章根據啟用 Application Insights 是基於下列理由更有彈性。
   * Application Insights 遙測會繼續運作：
       * 所有作業系統-Windows、 Linux、 mac。
       * 所有發行模式-自封式 」 或 「 相依於架構。
       * 所有目標架構，包括完整的.NET Framework。
       * 所有的裝載選項-Azure Web 應用程式、 Vm、 Linux、 容器、 AKS，非 Azure。
   * 從 Visual Studio 偵錯時，可以在本機上，看到遙測。
   * 可讓追蹤其他自訂遙測使用`TrackXXX()`API。
   * 您可以透過組態來完整控制。

### <a name="can-i-enable-application-insights-monitoring-using-tools-like-status-monitor"></a>我可以啟用 Application Insights 監視使用狀態監視器等工具嗎？

沒有。 [狀態監視器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)及其即將推出的取代項目[狀態監視器 v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview)目前支援 ASP.NET 4.x 只。

### <a name="i-have-an-aspnet-core-20-application-isnt-application-insights-automatically-enabled-without-me-doing-anything"></a>我有 ASP.NET Core 2.0 應用程式。 未做任何事我不會自動啟用 Application Insights？

`Microsoft.AspNetCore.All` 2.0 中繼套件包含 Application Insights SDK （版本 2.1.0），，如果您執行 Visual Studio 偵錯工具中的應用程式時，Visual Studio 可讓 Application Insights，並在 IDE 本身會在本機顯示遙測。 不是傳送遙測至 Application Insights 服務，除非明確指定檢測金鑰。 我們建議您遵循這篇文章中的指示，以啟用 Application Insights 中，即使是針對 2.0 應用程式。

### <a name="i-run-my-application-in-linux-are-all-features-supported-in-linux-as-well"></a>在 Linux 中執行我的應用程式。 所有功能在 Linux 中，也支援嗎？

* 是。 SDK 的功能支援是在所有平台，但有下列例外狀況相同：

    * 不在非 Windows 尚未支援效能計數器。
    * 即使`ServerTelemetryChannel`已啟用預設的情況下，如果應用程式正在執行 Linux 或 MacOS，通道不會自動建立暫時保留遙測，如果網路有問題的本機儲存體資料夾。 這項限制會導致遙測，如果有暫時性的網路或伺服器問題會遺失。 此問題的因應措施為使用者設定的通道的本機資料夾，如下所示。

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure channel to use the given folder to temporarily
        // store telemetry items during network or application insights server issues.
        // User should ensure that the given folder already exists,
        // and that application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="open-source-sdk"></a>開放原始碼 SDK
[讀取和貢獻程式碼](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)。

## <a name="video"></a>影片

- 關於如何從頭開始[使用 .NET Core 和 Visual Studio 設定 Application Insights](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) 的外部逐步說明影片。
- 關於如何從頭開始[使用 .NET Core 和 Visual Studio Code 設定 Application Insights](https://youtu.be/ygGt84GDync) 的外部逐步說明影片。

## <a name="next-steps"></a>後續步驟
* [瀏覽使用者流程](../../azure-monitor/app/usage-flows.md)來了解使用者如何瀏覽您的應用程式。
* [設定快照集集合](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger)，以查看擲回例外狀況時原始程式碼和變數的狀態。
* [使用 API](../../azure-monitor/app/api-custom-events-metrics.md) 可傳送您自己的事件和計量，以取得您的應用程式效能和使用方式的更詳細檢視。
* 使用[可用性測試](../../azure-monitor/app/monitor-web-app-availability.md)持續從世界各地檢查您的應用程式。
