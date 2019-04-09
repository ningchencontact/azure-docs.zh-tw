---
title: 適用於 ASP.NET Core，不含 Visual Studio 的 azure Application Insights |Microsoft Docs
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
ms.date: 04/03/2019
ms.author: cithomas
ms.openlocfilehash: 8243523887ec9861459b2d196126237cf89bad97
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288362"
---
# <a name="application-insights-for-aspnet-core-applications"></a>ASP.NET Core 應用程式的 application Insights

本文說明啟用適用於 Application Insights 中的步驟[ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2) ，而不使用 Visual Studio IDE。 如果您有安裝，則 Visual Studio IDE[這](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)文件包含 Visual Studio 的特定指示。 藉由完成此文章中的指示，Application Insights 會收集從您的 ASP.NET Core 應用程式的要求、 相依性、 例外狀況、 效能計數器、 活動訊號，以及記錄檔來啟動。 使用範例應用程式是[MVC 應用程式](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/?view=aspnetcore-2.2)目標`netcoreapp2.2`，但是這裡的指示也適用於所有的 ASP.NET Core 應用程式。 任何例外狀況會在適用時會呼叫。

## <a name="supported-scenarios"></a>支援的案例

適用於 ASP.NET Core 的 application Insights SDK （軟體開發套件） 可以監視您的應用程式，無論何處或如何執行應用程式。 如果您的應用程式正在執行，且具備網路連線到 Application Insights 服務，預期遙測收集。 這項支援包括但不限於任何作業系統 (Windows、 Linux、 Mac)、 裝載方法 （同處理序與 out-處理序）、 部署方法 （framework 相依與獨立），網頁伺服器 (IIS，Kestrel，) 平台 （Azure Web Apps、 Azure VMDocker，AKS 等等。）或 IDE （Visual Studio 中，VS Code 中，命令列）。

## <a name="prerequisites"></a>必要條件

- 正常運作的 ASP.NET Core 應用程式。 請遵循[這](https://docs.microsoft.com/aspnet/core/getting-started/)建立 ASP.NET Core 應用程式，如有需要的指南。
- 有效的 Application Insights 檢測金鑰，才能將任何遙測傳送至 Application Insights 服務。 請遵循[這些](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)視需要建立新的 Application Insights 資源，並取得檢測金鑰的指示。

## <a name="enabling-application-insights-server-side-telemetry"></a>啟用 Application Insights 伺服器端遙測

1. 適用於 ASP.NET Core，也就是一般的 NuGet 封裝安裝 Application Insights SDK。 建議您一律使用最新穩定版本。 這篇文章中所述的功能的一些可能無法在較舊版本。 您可以找到 sdk 的完整版本資訊[此處](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)。 

下圖顯示加入至專案的.csproj 檔案的變更。

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

   雖然您可以做為引數提供檢測金鑰`services.AddApplicationInsightsTelemetry("putinstrumentationkeyhere");`，建議您在組態中指定的檢測金鑰。 下圖顯示如何指定中的檢測金鑰`appsettings.json`。 請確定`appsettings.json`複製到發行的應用程式根資料夾。

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

    Alternately, the instrumentation key can also be specified in either of the following environment variables.
    APPINSIGHTS_INSTRUMENTATIONKEY
    ApplicationInsights:InstrumentationKey

    Example:
    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

`APPINSIGHTS_INSTRUMENTATIONKEY` 通常用來指定部署至 Azure Web Apps 的應用程式的檢測金鑰。

> [!NOTE]
> 透過環境變數指定在程式碼 wins 中的檢測金鑰`APPINSIGHTS_INSTRUMENTATIONKEY`，這會優先於其他選項。

4. 執行您的應用程式，並對其發出要求。 遙測應該立即開始送至 Application Insights。 Application Insights SDK 會自動收集下列遙測資料。

    1. 要求數-傳入 web 要求您的應用程式。
    1. 相依性
        1. 使用 http/Https 呼叫 `HttpClient`
        1. 使用 SQL 呼叫 `SqlClient`
        1. 使用 azure 儲存體呼叫[Azure 儲存體用戶端](https://www.nuget.org/packages/WindowsAzure.Storage/)*
        1. [事件中樞用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) 1.1.0 版和更新版本
        1. [服務匯流排用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) 3.0.0 版和更新版本

             * 只有在使用 HTTP/HTTPS，則會自動追蹤 azure Cosmos DB。 Application Insights 不會擷取 TCP 模式。


    1. [效能計數器](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)
        1. ASP.NET Core 中的效能計數器的支援僅限於下列
            1. SDK 2.4.1 版和上方會收集效能計數器如果應用程式正在執行 Azure Web 應用程式 (Windows)
            1. SDK 版本 2.7.0-beta3 上方會收集效能計數器如果正在執行 Windows，在應用程式，並將其目標和`NETSTANDARD2.0`或更高版本。
            1. 對於以完整的.NET Framework 為目標的應用程式，在所有版本的 SDK 支援效能計數器。

            在 Linux 中的效能計數器支援新增時，將會更新此文件。
    1. [即時計量](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)
    1. `ILogger` 記錄檔的嚴重性`Warning`或更新版本會自動從 SDK 版本 2.7.0-beta3 擷取或更新版本。 請在[這裡](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)閱讀更多資訊。

可能需要幾分鐘的時間才會開始出現在入口網站中的遙測。 若要快速檢查一切是否運作，最好是使用[即時計量](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)，而讓要求執行的應用程式。

## <a name="send-ilogger-logs-to-application-insights"></a>將 ILogger 記錄傳送至 Application Insights

Application Insights 支援擷取透過 ILogger 傳送的記錄檔。 閱讀完整的文件[此處](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)。

## <a name="enable-client-side-telemetry-for-web-applications"></a>為 Web 應用程式啟用用戶端遙測

上述步驟，就能開始收集伺服器端遙測。 如果您的應用程式的用戶端元件，然後遵循下列步驟來開始收集[使用量遙測](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)從該處。

1. 在 _ViewImports.cshtml，加入資料隱碼攻擊：

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. 在 _Layout.cshtml，插入 HtmlHelper 結尾`<head>`區段但任何其他指令碼之前。 此程式碼片段之後，應該插入任何您想要報告從頁面的自訂 JavaScript 遙測：

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

修改檔案名稱，根據您實際的應用程式。 上述範例中使用的名稱是從預設的 MVC 應用程式範本。

## <a name="configuring-application-insights-sdk"></a>設定 Application Insights SDK

可以自訂 ASP.NET Core 的 application Insights SDK，以變更預設設定。 Application Insights ASP.NET SDK 的使用者可能很熟悉組態中使用`ApplicationInsights.config`，或藉由修改`TelemetryConfiguration.Active`。 針對 ASP.NET Core，是以不同的方式進行設定。 ASP.NET Core SDK 新增至使用 ASP.NET Core 內建的應用程式[DependencyInjection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)機制，並設定相同也用 DependencyInjection。 幾乎所有的組態變更會在中完成`ConfigureServices()`方法的程式`Startup.cs`類別，除非另外註明否則。 請遵循下列各節來了解更多。

> [!NOTE]
> 請務必請注意，修改的組態，藉由修改`TelemetryConfiguration.Active`不建議使用 ASP.NET Core 應用程式中。

### <a name="configuring-using-applicationinsightsserviceoptions"></a>設定使用 ApplicationInsightsServiceOptions

可修改的一些常見的設定，藉由傳遞`ApplicationInsightsServiceOptions`至`services.AddApplicationInsightsTelemetry();`。 範例如下所示。

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

可設定的設定中的確切清單`ApplicationInsightsServiceOptions`可以找到[這裡](https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/f0b8631e482d25982eb52092103b34e3ff6e5fef/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs)。

### <a name="sampling"></a>取樣

ASP.NET Core 的 application Insights SDK 支援 FixedRate 和調適型取樣。 預設會啟用調適型取樣。 請遵循[這](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)文件，以了解如何設定 ASP.NET Core 應用程式的取樣。

### <a name="adding-telemetryinitializers"></a>新增 TelemetryInitializers

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

可以加入自訂的遙測處理器`TelemetryConfiguration`藉由使用擴充方法`AddApplicationInsightsTelemetryProcessor`上`IServiceCollection`。 使用下列的範例。

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

下列自動收集模組預設會啟用，並負責自動收集的遙測。 它們可以停用及設定以變更預設行為。

1. `RequestTrackingTelemetryModule`
1. `DependencyTrackingTelemetryModule`
1. `PerformanceCollectorModule`
1. `QuickPulseTelemetryModule`
1. `AppServicesHeartbeatTelemetryModule`
1. `AzureInstanceMetadataTelemetryModule`

若要設定任何預設`TelemetryModule`，使用擴充方法`ConfigureTelemetryModule<T>`上`IServiceCollection`如下列範例所示。

```csharp
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
    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>常見問題集

*1.我想要追蹤一些額外的遙測，除了自動收集遙測。 我該怎麼做？*

* 取得的執行個體`TelemetryClient`藉由使用建構函式插入，以及呼叫所需`TrackXXX()`在其上的方法。 不建議建立新`TelemetryClient`在 ASP.NET Core 應用程式，作為單一執行個體的執行個體`TelemetryClient`已註冊在 DI 容器中，共用`TelemetryConfiguration`搭配 rest 的遙測。 建立新`TelemetryClient`必須具有不同的設定，遙測的其餘部分時，才建議使用執行個體。 下列範例示範如何追蹤來自控制站的其他遙測。

```csharp
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

 請參閱[Application Insights 自訂計量 API 參考](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/)如 Application Insights 中的報告功能的自訂資料的描述。

*2.某些 Visual Studio 範本會用上 IWebHostBuilder UseApplicationInsights() 擴充方法，來啟用 Application Insights。 這種用法是否仍然有效的？*

* 使用這個方法來啟用 Application Insights 有效，而且可在 Visual Studio 上架，和 Azure Web 應用程式擴充功能。 不過，建議使用`services.AddApplicationInsightsTelemery()`因為它提供多載以控制一些設定。 同時方法在內部執行相同的動作，因此，如果不沒有套用任何自訂組態，則呼叫其中一個是沒問題。

*3.我要部署我的 ASP.NET Core 應用程式至 Azure Web Apps。 仍應啟用 Application Insights 延伸模組，從 Web 應用程式嗎？*

* 如果這篇文章中所示，已會在建置階段中安裝 SDK，，則不需要啟用 Application Insights 延伸模組，從 Web 應用程式入口網站。 即使已安裝擴充功能，它會退避法，當它偵測到 SDK 已經加入應用程式。 啟用 Application Insights 從延伸模組，可讓您從安裝和更新您的應用程式的 SDK。 不過，這篇文章根據啟用 Application Insights 是基於下列理由更有彈性。
    1. Application Insights 遙測會繼續運作
        1. 所有作業系統-Windows、 Linux、 mac。
        1. 所有發行模式-自封式 」 或 「 相依於架構。
        1. 所有目標架構，包括完整的.NET Framework。
        1. 所有的裝載選項-Azure Web 應用程式、 Vm、 Linux、 容器、 AKS，非 Azure。
    1. 從 Visual Studio 偵錯時，可以在本機上，看到遙測。
    1. 可讓追蹤其他自訂遙測使用`TrackXXX()`API。
    1. 已設定的完整控制權。

*4.我可以啟用 Application Insights 監視使用狀態監視器等工具嗎？*

* 沒有。 [狀態監視器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)及其即將推出的取代項目[IISConfigurator](https://github.com/Microsoft/ApplicationInsights-Announcements/issues/21)目前僅支援 ASP.NET。 將更新文件時支援 ASP.NET Core 應用程式。

*5.我有 ASP.NET Core 2.0 應用程式嗎？ 不是 Application Insights 自動啟用，而不做任何動作嗎？*

* `Microsoft.AspNetCore.All` 2.0 中繼套件包含 Application Insights SDK （版本 2.1.0），，如果您執行 Visual Studio 偵錯工具中的應用程式時，Visual Studio 可讓 application insights，並在 IDE 本身會在本機顯示遙測。 不是傳送遙測至 Application Insights 服務，除非明確指定檢測金鑰。 我們建議您遵循這篇文章中的指示啟用 Application Insights 中，即使是針對 2.0 應用程式。

*6.在 Linux 中執行我的應用程式。 所有功能在 Linux 中，也支援嗎？*

* 是。 SDK 的功能支援是在所有平台，但有下列例外狀況相同：
    1. 不在非 Windows 尚未支援效能計數器。 新增 Linux 支援後，將會更新此文件。
    1. 即使`ServerTelemetryChannel`啟用根據預設，如果應用程式正在執行非 windows，通道不會自動建立暫時保留遙測，如果網路有問題的本機儲存體資料夾。 這項限制會導致遙測，如果有暫時性的網路或伺服器問題會遺失。 此問題的因應措施為使用者設定的通道的本機資料夾，如下所示。

```csharp
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
