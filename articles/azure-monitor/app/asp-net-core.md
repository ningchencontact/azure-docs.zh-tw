---
title: ASP.NET Core 應用程式的 Azure 應用程式見解 |Microsoft Docs
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
ms.openlocfilehash: 9e14a9f3f2f27112a591f14e9a93580f66aadef7
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169561"
---
# <a name="application-insights-for-aspnet-core-applications"></a>ASP.NET Core 應用程式的 Application Insights

本文說明如何啟用[ASP.NET Core](https://docs.microsoft.com/aspnet/core)應用程式的 Application Insights。 當您完成本文中的指示時, Application Insights 將會從您的 ASP.NET Core 應用程式收集要求、相依性、例外狀況、效能計數器、心跳和記錄。

我們將在此使用的範例是以為目標`netcoreapp2.2`的[MVC 應用程式](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app)。 您可以將這些指示套用至所有 ASP.NET Core 應用程式。

## <a name="supported-scenarios"></a>支援的案例

[適用于 ASP.NET Core 的 APPLICATION INSIGHTS SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)可以監視您的應用程式, 不論其執行位置或方式為何。 如果您的應用程式正在執行, 且具有與 Azure 的網路連線, 則可以收集遙測。 支援 .NET Core 的任何位置都支援 Application Insights 監視。 支援涵蓋:
* **作業系統**：Windows、Linux 或 Mac。
* **裝載方法**:進程內或進程外。
* **部署方法**:架構相依或獨立的。
* **網頁伺服器**:IIS (Internet Information Server) 或 Kestrel。
* **裝載平臺**:Azure App Service、Azure VM、Docker、Azure Kubernetes Service (AKS) 等的 Web Apps 功能。
* **IDE**:Visual Studio、VS Code 或命令列。

> [!NOTE]
> 如果您搭配 Application Insights 使用 ASP.NET Core 3.0-preview，請使用[2.8.0-Beta3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0-beta3)版本或更高版本。 這是已知可與 ASP.NET Core 3.0 搭配運作的唯一版本。 此外，ASP.NET Core 3.0 應用程式尚不支援以 Visual Studio 為基礎的上架。

## <a name="prerequisites"></a>必要條件

- 正常運作的 ASP.NET Core 應用程式。 如果您需要建立 ASP.NET Core 應用程式, 請遵循此[ASP.NET Core 教學](https://docs.microsoft.com/aspnet/core/getting-started/)課程。
- 有效的 Application Insights 檢測金鑰。 必須要有此金鑰, 才能將任何遙測資料傳送至 Application Insights。 如果您需要建立新的 Application Insights 資源來取得檢測金鑰, 請參閱[建立 Application Insights 資源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)。

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>啟用 Application Insights 伺服器端遙測 (Visual Studio)

1. 在 Visual Studio 中，開啟您的專案。

    > [!TIP]
    > 如果您想要的話, 可以設定專案的原始檔控制, 以便追蹤 Application Insights 進行的所有變更。 若要啟用原始檔控制  > , 請選取 [檔案] [**新增至原始檔控制**]。

2. 選取 [專案] > [新增 Application Insights 遙測]。

3. 選取 **[馬上開始]** 。 根據您的 Visual Studio 版本而定, 此選取的文字可能會有所不同。 某些較舊版本則改為使用 [**開始免費**] 按鈕。

4. 選取您的訂用帳戶。 然後選取 [**資源** > **註冊**]。

5. 將 Application Insights 新增至您的專案之後, 請檢查以確認您使用的是最新穩定版本的 SDK。 移至 [**專案** > ] [**管理 NuGet 套件** > ] [**ApplicationInsights. AspNetCore**]。 如有需要, 請選擇 [**更新**]。

     ![顯示要在哪裡選取 Application Insights 套件以進行更新的螢幕擷取畫面](./media/asp-net-core/update-nuget-package.png)

6. 如果您已遵循選用提示並將專案加入至原始檔控制, 請移至 [ **View**  > ]**Team Explorer**  > [**變更**]。 然後選取每個檔案, 以查看 Application Insights 遙測所做變更的差異觀點。

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>啟用 Application Insights 伺服器端遙測 (無 Visual Studio)

1. 安裝[適用于 ASP.NET Core 的 APPLICATION INSIGHTS SDK NuGet 套件](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)。 我們建議您一律使用最新的穩定版本。 在[開放原始碼 GitHub](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)存放庫中尋找 SDK 的完整版本資訊。

    下列程式碼範例顯示要新增至專案`.csproj`檔案的變更。

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. 將`services.AddApplicationInsightsTelemetry();`新增至`ConfigureServices()` `Startup`類別中的方法, 如下列範例所示:

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. 設定檢測金鑰。

    雖然您可以提供檢測金鑰做為的引數`AddApplicationInsightsTelemetry`, 但我們建議您在設定中指定檢測金鑰。 下列程式碼範例顯示如何在中`appsettings.json`指定檢測金鑰。 請確定`appsettings.json`在發行期間, 會將複製到應用程式根資料夾。

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

    或者, 在下列其中一個環境變數中指定檢測金鑰:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    例如:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    一般來說, `APPINSIGHTS_INSTRUMENTATIONKEY`會為部署到 Web Apps 的應用程式指定檢測金鑰。

    > [!NOTE]
    > 在程式碼中指定的檢測金鑰是透過環境`APPINSIGHTS_INSTRUMENTATIONKEY`變數來進行, 這會優先于其他選項。

## <a name="run-your-application"></a>執行您的應用程式

執行您的應用程式, 並對其提出要求。 遙測現在應流向 Application Insights。 Application Insights SDK 會自動收集下列遙測。

|要求/相依性 |詳細資料|
|---------------|-------|
|要求 | 傳入的 web 要求傳送至您的應用程式。 |
|HTTP 或 HTTPS | 以進行的`HttpClient`呼叫。 |
|SQL | 以進行的`SqlClient`呼叫。 |
|[Azure 儲存體](https://www.nuget.org/packages/WindowsAzure.Storage/) | Azure 儲存體用戶端所提出的呼叫。 |
|[EventHubs 用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Version 1.1.0 和更新版本。 |
|[匯流排用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Version 3.0.0 和更新版本。 |
|Azure Cosmos DB | 只有在使用 HTTP/HTTPS 時, 才會自動追蹤。 Application Insights 不會捕捉 TCP 模式。 |

### <a name="performance-counters"></a>效能計數器

ASP.NET Core 中的[效能計數器](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)支援有限:

* 如果應用程式是在 Azure Web Apps （Windows）中執行，SDK 版本2.4.1 和更新版本會收集效能計數器。
* 如果應用程式是在 Windows 和目標`NETSTANDARD2.0`或更新版本中執行，SDK 版本2.7.1 和更新版本會收集效能計數器。
* 針對以 .NET Framework 為目標的應用程式, 所有版本的 SDK 都支援效能計數器。
* SDK 版本 2.8.0-Beta3 和更新版本支援 Linux 中的 cpu/記憶體計數器。 Linux 不支援其他計數器。 在 Linux （和其他非 Windows 環境）中取得系統計數器的建議方式是使用[EventCounters](#eventcounter)

### <a name="eventcounter"></a>EventCounter

[EventCounter](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md)是一種跨平臺方法，可在 .NET/.net Core 中發佈和使用計數器。 雖然這項功能已存在，但沒有任何內建的提供者會發行這些計數器。 從 .NET Core 3.0 開始，會發行幾個計數器，例如 CLR 計數器、ASP.NET Core 計數器等等。

SDK 版本 2.8.0-Beta3 和更新版本支援 EventCounters 的集合。 根據預設，SDK 會收集下列計數器，而且可以在計量瀏覽器中或使用 PerformanceCounter 資料表下的分析查詢來查詢這些計數器。 計數器的名稱的格式為 "Category |Counter "。

|Category | 計數器|
|---------------|-------|
|System.web | cpu 使用量 |
|System.web | 工作集 |
|System.web | gc-堆積大小 |
|System.web | gen-0-gc-計數 |
|System.web | gen-1-gc-計數 |
|System.web | gen-2-gc-計數 |
|System.web | gc 時間 |
|System.web | gen-0-大小 |
|System.web | gen-1-大小 |
|System.web | gen-2-大小 |
|System.web | loh-大小 |
|System.web | 配置-速率 |
|System.web | 元件計數 |
|System.web | 例外狀況-計數 |
|System.web | threadpool-執行緒計數 |
|System.web | 監視-鎖定-爭用計數 |
|System.web | threadpool-佇列長度 |
|System.web | threadpool-已完成-個專案-計數 |
|System.web | 主動-計時器計數 |
|AspNetCore。裝載 | 每秒要求數 |
|AspNetCore。裝載 | 總計-要求 |
|AspNetCore。裝載 | 目前-要求 |
|AspNetCore。裝載 | 失敗-要求 |

### <a name="ilogger-logs"></a>ILogger 記錄

[ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)嚴重性`Warning`或更高版本的記錄檔會自動在 SDK 版本 2.7.0-Beta3 和更新版本中捕捉。

### <a name="live-metrics"></a>即時計量

可能需要幾分鐘的時間, 才會開始在入口網站中顯示遙測。 若要快速確保一切運作正常, 最好在對執行中的應用程式提出要求時, 使用[即時計量](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)。

## <a name="enable-client-side-telemetry-for-web-applications"></a>啟用 web 應用程式的用戶端遙測

上述步驟足以協助您開始收集伺服器端遙測資料。 如果您的應用程式有用戶端元件, 請遵循後續步驟來開始收集[使用量遙測](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)。

1. 在`_ViewImports.cshtml`中, 新增插入:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. 在`_Layout.cshtml`中, `HtmlHelper`于`<head>`區段結尾, 但在任何其他腳本之前插入。 如果您想要從頁面報告任何自訂的 JavaScript 遙測, 請將它插入此程式碼片段後面:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

先前`.cshtml`參考的檔案名是來自預設的 MVC 應用程式範本。 最後, 如果您想要適當地為應用程式啟用用戶端監視, 則 JavaScript 程式碼片段必須出現在`<head>`您應用程式的每個頁面的區段中, 您想要監視。 您可以藉由將 JavaScript 程式碼片段新增至`_Layout.cshtml`, 來為此應用程式範本達成此目標。 

如果您的專案不`_Layout.cshtml`包含, 您仍然可以加入[用戶端監視](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring)。 若要這麼做, 您可以將 JavaScript 程式碼片段新增至對等的`<head>`檔案, 以控制應用程式內所有頁面的。 或者, 您也可以將程式碼片段新增至多個頁面, 但此解決方案很難以維護, 而且通常不建議這麼做。

## <a name="configure-the-application-insights-sdk"></a>設定 Application Insights SDK

您可以自訂 ASP.NET Core 的 Application Insights SDK, 以變更預設設定。 Application Insights ASP.NET SDK 的使用者可能會熟悉使用`ApplicationInsights.config`或修改`TelemetryConfiguration.Active`來變更設定。 您會以不同的方式變更 ASP.NET Core 的設定。 將 ASP.NET Core SDK 新增至應用程式, 並使用 ASP.NET Core 內建相依性[插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)來設定它。 除非另有指示, 否則請`ConfigureServices()`在`Startup.cs`類別的方法中進行幾乎所有的設定變更。 下列各節提供詳細資訊。

> [!NOTE]
> 在 ASP.NET Core 應用程式中, 不支援`TelemetryConfiguration.Active`透過修改來變更設定。

### <a name="using-applicationinsightsserviceoptions"></a>使用 ApplicationInsightsServiceOptions

您可以藉由傳遞`ApplicationInsightsServiceOptions`至來`AddApplicationInsightsTelemetry`修改一些一般設定, 如下列範例所示:

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

中的完整設定清單`ApplicationInsightsServiceOptions`

|設定 | 描述 | 預設
|---------------|-------|-------
|EnableQuickPulseMetricStream | 啟用/停用 LiveMetrics 功能 | true
|EnableAdaptiveSampling | 啟用/停用調適型取樣 | true
|EnableHeartbeat | [啟用/停用心跳] 功能會定期（15分鐘預設）傳送名為 ' HeartBeatState ' 的自訂計量，其中包含執行時間的相關資訊，例如 .NET 版本、Azure 環境資訊（如果適用）等等。 | true
|AddAutoCollectedMetricExtractor | 啟用/停用 AutoCollectedMetrics 解壓縮程式，這是一種 TelemetryProcessor，會在進行取樣之前，先傳送要求/相依性的預先匯總計量。 | true
|RequestCollectionOptions.TrackExceptions | 啟用/停用要求收集模組未處理之例外狀況追蹤的報告。 | 在 NETSTANDARD 2.0 中為 false （因為例外狀況是使用 ApplicationInsightsLoggerProvider 來追蹤），否則為 true。

如需最新清單，請參閱[中`ApplicationInsightsServiceOptions`的可設定設定](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs)。

### <a name="sampling"></a>取樣

適用于 ASP.NET Core 的 Application Insights SDK 支援固定速率和調適型取樣。 預設會啟用調適型取樣。 

如需詳細資訊, 請參閱[設定 ASP.NET Core 應用程式的](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)調適型取樣。

### <a name="adding-telemetryinitializers"></a>加入 TelemetryInitializers

當您想要定義與所有遙測一起傳送的全域屬性時, 請使用[遙測初始化運算式](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer)。

將任何新`TelemetryInitializer`的`DependencyInjection`新增至容器, 如下列程式碼所示。 SDK 會自動挑選已新增`TelemetryInitializer` `DependencyInjection`至容器的任何。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>移除 TelemetryInitializers

預設會顯示遙測初始化運算式。 若要移除所有或特定的遙測初始化運算式, 請在呼叫`AddApplicationInsightsTelemetry()`之後使用下列範例程式碼。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                         (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
    }
```

### <a name="adding-telemetry-processors"></a>新增遙測處理器

您可以使用上`TelemetryConfiguration` `IServiceCollection`的擴充方法`AddApplicationInsightsTelemetryProcessor` , 將自訂遙測處理器加入至。 您可以在「[高級篩選」案例](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor)中使用遙測處理器, 以更直接控制您傳送至 Application Insights 服務的遙測資料所包含或排除的內容。 請使用下列範例。

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

Application Insights 使用遙測模組自動收集有關特定工作負載的實用遙測，而不需要使用者手動追蹤。

預設會啟用下列自動收集模組。 這些模組負責自動收集遙測。 您可以停用或設定它們來改變其預設行為。

* `RequestTrackingTelemetryModule`-從傳入的 web 要求收集 RequestTelemetry。
* `DependencyTrackingTelemetryModule`-從傳出 HTTP 呼叫和 sql 呼叫收集 DependencyTelemetry。
* `PerformanceCollectorModule`-收集 Windows PerformanceCounters。
* `QuickPulseTelemetryModule`-收集遙測以顯示在即時計量入口網站中。
* `AppServicesHeartbeatTelemetryModule`-收集有關裝載應用程式之 Azure App Service 環境的核心節拍（以自訂計量傳送）。
* `AzureInstanceMetadataTelemetryModule`-收集關於裝載應用程式之 Azure VM 環境的核心節拍（以自訂計量傳送）。
* `EventCounterCollectionModule`-收集[EventCounters。](#eventcounter) 此模組是新功能，可在 SDK Version 2.8.0-Beta3 和更高版本中取得。

若要設定任何`TelemetryModule`預設值, 請在`ConfigureTelemetryModule<T>`上`IServiceCollection`使用擴充方法, 如下列範例所示。

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

        // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
                            (module, o) =>
                            {
                                module.Counters.Clear();
                                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
                            }
                        );

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-a-telemetry-channel"></a>設定遙測通道

預設的通道是`ServerTelemetryChannel`。 您可以覆寫它, 如下列範例所示。

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>動態停用遙測

如果您想要有條件且動態地停用遙測, `TelemetryConfiguration`您可以在程式碼中的任何位置使用 ASP.NET Core 相依`DisableTelemetry`性插入容器來解析實例, 並在其上設定旗標。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

請注意，上述動作並不會防止任何自動收集模組收集遙測資料。 只有將遙測傳送至 Application Insights 會使用上述方法來停用。 如果不需要特定的自動收集模組，最好是[移除遙測模組](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>如何追蹤不會自動收集的遙測？

使用函式插入`TelemetryClient`來取得的實例, 並在其上`TrackXXX()`呼叫所需的方法。 我們不建議在 ASP.NET Core `TelemetryClient`應用程式中建立新的實例。 的單一實例`TelemetryClient`已經`DependencyInjection`在容器中註冊, 而這會與`TelemetryConfiguration`其餘的遙測資料共用。 只有當您`TelemetryClient`需要的設定與其余的遙測資料不同時, 才建議建立新的實例。 

下列範例顯示如何追蹤來自控制器的其他遙測。

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

如需 Application Insights 中自訂資料包表的詳細資訊, 請參閱[Application Insights 自訂計量 API 參考](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/)。

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>某些 Visual Studio 範本會在 IWebHostBuilder 上使用 .Useapplicationinsights () 擴充方法來啟用 Application Insights。 這種用法仍然有效嗎？

是, 使用此方法啟用 Application Insights 是有效的。 這項技術用於 Visual Studio 上架和 Web Apps 延伸模組中。 不過, 我們建議使用`services.AddApplicationInsightsTelemetry()` , 因為它會提供多載以控制某些設定。 這兩種方法會在內部執行相同的動作, 因此, 如果您不需要套用自訂設定, 則可以呼叫任一種方法。

`IWebHostBuilder``IHostBuilder`在 ASP.NET Core 3.0 中取代為，為了避免混淆，Application Insights 版本 2.8.0-Beta3 開頭會將 .useapplicationinsights （）方法標示為已淘汰，並將在下一個主要版本中移除。

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>我要將 ASP.NET Core 應用程式部署到 Web Apps。 我仍然應該從 Web Apps 啟用 Application Insights 擴充功能嗎？

如果 SDK 是在組建期間安裝, 如本文所示, 您就不需要從 App Service 入口網站啟用[Application Insights 擴充](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps)功能。 即使已安裝延伸模組, 當它偵測到 SDK 已新增至應用程式時, 它也會重新開機。 如果您從擴充功能啟用 Application Insights, 就不需要安裝及更新 SDK。 但是, 如果您依照本文中的指示來啟用 Application Insights, 您會有更大的彈性, 原因如下:

   * Application Insights 遙測會繼續在中使用:
       * 所有的作業系統, 包括 Windows、Linux 和 Mac。
       * 所有發行模式, 包括獨立或架構相依。
       * 所有目標 framework, 包括完整的 .NET Framework。
       * 所有裝載選項, 包括 Web Apps、Vm、Linux、容器、Azure Kubernetes Service 和非 Azure 裝載。
       * 所有 .NET Core 版本，包括預覽版本。
   * 當您從 Visual Studio 進行調試時, 您可以在本機看到遙測。
   * 您可以使用`TrackXXX()` API 來追蹤其他自訂遙測。
   * 您可以完整控制設定。

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>我可以使用狀態監視器之類的工具來啟用 Application Insights 監視嗎？

資料分割 [狀態監視器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)和[狀態監視器 v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview)目前僅支援 ASP.NET 4.x。

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Application Insights 自動啟用我的 ASP.NET Core 2.0 應用程式嗎？

`Microsoft.AspNetCore.All` 2.0 中繼套件包含 Application Insights SDK (版本 2.1.0)。 如果您在 Visual Studio 偵錯工具下執行應用程式, Visual Studio 會啟用 Application Insights, 並在 IDE 本身的本機顯示遙測。 除非指定了檢測金鑰, 否則遙測不會傳送至 Application Insights 服務。 我們建議遵循本文中的指示來啟用 Application Insights, 即使是2.0 應用程式也是如此。

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>如果我在 Linux 中執行應用程式, 是否支援所有功能？

是的。 SDK 的功能支援在所有平臺上都相同, 但有下列例外狀況:

* 只有在 Windows 中才支援效能計數器。
* 雖然預設`ServerTelemetryChannel`會啟用, 但如果應用程式是在 Linux 或 MacOS 中執行, 則通道不會自動建立本機儲存體資料夾, 以在發生網路問題時暫時保存遙測。 由於這項限制, 當發生暫時性網路或伺服器問題時, 就會遺失遙測。 若要解決此問題, 請設定通道的本機資料夾:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="open-source-sdk"></a>開放原始碼 SDK

[讀取和貢獻程式碼](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)。

## <a name="video"></a>視訊

- 請參閱此外部逐步解說, 以[使用 .Net Core 設定 Application Insights, 並從頭 Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) 。
- 請參閱此外部逐步解說, 以[使用 .Net Core 設定 Application Insights, 並從頭 Visual Studio Code](https://youtu.be/ygGt84GDync) 。

## <a name="next-steps"></a>後續步驟

* [探索使用者流程](../../azure-monitor/app/usage-flows.md), 以瞭解使用者如何流覽您的應用程式。
* [設定快照集集合](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger), 以查看擲回例外狀況時原始程式碼和變數的狀態。
* [使用 API](../../azure-monitor/app/api-custom-events-metrics.md)來傳送您自己的事件和計量, 以深入瞭解應用程式的效能和使用方式。
* 使用[可用性測試](../../azure-monitor/app/monitor-web-app-availability.md)持續從世界各地檢查您的應用程式。
* [ASP.NET Core 中的相依性插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
