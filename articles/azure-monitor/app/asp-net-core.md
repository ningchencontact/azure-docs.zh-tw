---
title: ASP.NET Core 應用程式的 azure Application Insights |Microsoft Docs
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
ms.openlocfilehash: 7fe5a4f5a5d1d254918f1b4f997acfb9cf67a75b
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272452"
---
# <a name="application-insights-for-aspnet-core-applications"></a>ASP.NET Core 應用程式的 application Insights

本文說明如何啟用適用於 Application Insights [ASP.NET Core](https://docs.microsoft.com/aspnet/core)應用程式。 當您完成這篇文章中的指示時，Application Insights 會從您的 ASP.NET Core 應用程式收集的要求、 相依性、 例外狀況、 效能計數器、 活動訊號，以及記錄檔。 

我們將在此處使用的範例[MVC 應用程式](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app)目標`netcoreapp2.2`。 您可以將這些指示套用至所有的 ASP.NET Core 應用程式。

## <a name="supported-scenarios"></a>支援的案例

[Application Insights SDK for ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)可以監視您的應用程式，無論在何處或如何執行。 如果您的應用程式正在執行，且具備網路連線到 Azure，就可以收集遙測資料。 Application Insights 監視支援.NET Core 支援的每個地方。 支援涵蓋：
* **作業系統**：Windows、 Linux 或 mac。
* **裝載方法**:在 處理程序或跨處理序。 
* **部署方法**:相依或獨立的架構。
* **Web 伺服器**:IIS (Internet Information Server) 或 Kestrel。 
* **裝載平台**:Azure App Service、 Azure VM、 Docker、 Azure Kubernetes Service (AKS) 等等的 Web Apps 功能。
* **IDE**:Visual Studio、 VS Code 中或命令列。

## <a name="prerequisites"></a>必要條件

- 正常運作的 ASP.NET Core 應用程式。 如果您要建立 ASP.NET Core 應用程式，請遵循這[ASP.NET Core 教學課程](https://docs.microsoft.com/aspnet/core/getting-started/)。
- 有效的 Application Insights 檢測金鑰。 此金鑰，才能將任何遙測傳送至 Application Insights。 如果您要建立新的 Application Insights 資源，以取得檢測金鑰，請參閱[建立 Application Insights 資源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)。

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>啟用 Application Insights 伺服器端遙測資料 (Visual Studio)

1. 在 Visual Studio 中，開啟您的專案。

    > [!TIP]
    > 如果您想要您可以設定原始檔控制專案讓您能夠追蹤 Application Insights 所產生的所有變更。 若要啟用原始檔控制，請選取**檔案** > **加入原始檔控制**。

2. 選取 [專案]   > [新增 Application Insights 遙測]  。

3. 選取 **[馬上開始]** 。 此選取項目文字可能會有所不同，視您的 Visual Studio 版本而定。 使用某些舊版**免費開始**按鈕。

4. 選取您的訂用帳戶。 然後選取**Resource** > **註冊**。

5. 之後您可以將 Application Insights 加入您的專案，請檢查以確認您使用最新穩定版本的 sdk。 移至**專案** > **管理 NuGet 封裝** > **Microsoft.ApplicationInsights.AspNetCore**。 如果您需要選擇**更新**。

     ![顯示如何選取 Application Insights 套件更新的螢幕擷取畫面](./media/asp-net-core/update-nuget-package.png)

6. 如果您遵循選擇性的提示，並加入原始檔控制您的專案，請移至**檢視** > **Team Explorer** > **變更**。 然後選取每個檔案，以查看差異檢視的 Application Insights 遙測資料所做的變更。

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>啟用 Application Insights 伺服器端遙測資料 (沒有 Visual Studio)

1. 安裝[ASP.NET Core 的 Application Insights SDK NuGet 封裝](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)。 我們建議您一律使用最新穩定版本。 上找到完整的版本資訊適用於 SDK[開放原始碼 GitHub 存放庫](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)。

    下列程式碼範例顯示要加入至您的專案的變更`.csproj`檔案。

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. 新增`services.AddApplicationInsightsTelemetry();`要`ConfigureServices()`方法，在您`Startup`類別，如此範例所示：

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

    雖然您可以做為引數提供的檢測金鑰`AddApplicationInsightsTelemetry`，我們建議您在組態中指定的檢測金鑰。 下列程式碼範例示範如何指定中的檢測金鑰`appsettings.json`。 請確定`appsettings.json`在發佈期間複製到應用程式根資料夾。

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

    或者，在下列環境變數中指定的檢測金鑰：

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    例如:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    一般而言，`APPINSIGHTS_INSTRUMENTATIONKEY`指定應用程式部署到 Web 應用程式的檢測金鑰。

    > [!NOTE]
    > 透過環境變數指定在程式碼 wins 中的檢測金鑰`APPINSIGHTS_INSTRUMENTATIONKEY`，這會優先於其他選項。

## <a name="run-your-application"></a>執行您的應用程式

執行您的應用程式，並對其發出要求。 遙測現在應該傳送至 Application Insights。 Application Insights SDK 會自動收集下列遙測資料。

|要求/相依項目 |詳細資料|
|---------------|-------|
|Requests | 您的應用程式傳入 web 要求。 |
|HTTP 或 HTTPS | 使用呼叫`HttpClient`。 |
|SQL | 使用呼叫`SqlClient`。 |
|[Azure 儲存體](https://www.nuget.org/packages/WindowsAzure.Storage/) | 使用 Azure 儲存體用戶端所進行的呼叫。 |
|[EventHubs 用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1\.1.0 版和更新版本。 |
|[服務匯流排用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 3\.0.0 版和更新版本。 |
|Azure Cosmos DB | 使用 HTTP/HTTPS 時，才會自動追蹤。 Application Insights 不會擷取 TCP 模式。 |

### <a name="performance-counters"></a>效能計數器

支援[效能計數器](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)在 ASP.NET Core 會限制：

   * SDK 版本 2.4.1，如果應用程式正在執行 Web 應用程式 (Windows) 中的稍後收集效能計數器。
   * SDK 版本 2.7.0-beta3 和更新版本的收集效能計數器，如果應用程式正在執行 Windows 和目標`NETSTANDARD2.0`或更新版本。
   * 對於以.NET Framework 為目標的應用程式，所有版本的 SDK 都支援效能計數器。
 
在 Linux 中的效能計數器支援新增時，將會更新本文。

### <a name="ilogger-logs"></a>ILogger 記錄檔

[ILogger 記錄](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)嚴重性的`Warning`或更新版本會自動擷取 SDK 版本 2.7.0-beta3 及更新版本。

### <a name="live-metrics"></a>即時計量

可能需要幾分鐘的時間之前遙測會開始出現在入口網站中。 若要快速地確定一切運作正常，最好是使用[即時計量](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)當您對要求執行的應用程式。

## <a name="enable-client-side-telemetry-for-web-applications"></a>啟用 web 應用程式的用戶端遙測

上述的步驟便足以幫助您開始收集伺服器端遙測資料。 如果您的應用程式的用戶端元件，請依照下列步驟，以開始收集[使用量遙測](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)。

1. 在  `_ViewImports.cshtml`，新增插入式攻擊：

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. 在  `_Layout.cshtml`，插入`HtmlHelper`結尾的`<head>`區段但任何其他指令碼之前。 如果您想要報告任何自訂的 JavaScript 遙測從頁面，請將其插入此程式碼片段之後：

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

`.cshtml`稍早參考的檔案名稱是來自預設 MVC 應用程式範本。 最後，如果您想要正確地啟用 用戶端監視應用程式時，JavaScript 程式碼片段必須出現在`<head>`您想要監視的應用程式的每個頁面的區段。 您可以加入至 JavaScript 程式碼片段來達成此目標，才能使用此應用程式範本`_Layout.cshtml`。 

如果您的專案不包含`_Layout.cshtml`，您還是可以加入[用戶端監視](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring)。 您可以藉由將 JavaScript 程式碼片段新增至對等的檔案，以控制`<head>`的應用程式內的所有頁面。 或您可以將程式碼片段新增至多個頁面，但這個解決方案相當難以維護而且通常不建議。

## <a name="configure-the-application-insights-sdk"></a>設定 Application Insights SDK

您可以自訂 Application Insights SDK for ASP.NET Core 以變更預設組態。 Application Insights ASP.NET SDK 的應用程式的使用者可能很熟悉變更所使用的組態`ApplicationInsights.config`或藉由修改`TelemetryConfiguration.Active`。 您變更不同的 ASP.NET Core 的組態。 新增應用程式的 ASP.NET Core SDK 和設定使用內建的 ASP.NET Core[相依性插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)。 進行中幾乎所有的組態變更`ConfigureServices()`方法的程式`Startup.cs`類別，除非有向。 下列各節提供詳細的資訊。

> [!NOTE]
> 在 ASP.NET Core 應用程式變更組態，藉由修改`TelemetryConfiguration.Active`不建議。

### <a name="using-applicationinsightsserviceoptions"></a>使用 ApplicationInsightsServiceOptions

您可以修改一些共用設定，藉由傳遞`ApplicationInsightsServiceOptions`至`AddApplicationInsightsTelemetry`，如這個範例所示：

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

如需詳細資訊，請參閱 <<c0> [ 可設定的設定，在`ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs)。

### <a name="sampling"></a>取樣

Application Insights SDK for ASP.NET Core 支援固定取樣率和調適型取樣。 預設會啟用調適型取樣。 

如需詳細資訊，請參閱 <<c0> [ 設定的 ASP.NET Core 應用程式的調適型取樣](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)。

### <a name="adding-telemetryinitializers"></a>新增 TelemetryInitializers

使用[遙測初始設定式](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer)當您想要定義與所有遙測一起傳送的全域屬性。

加入任何新`TelemetryInitializer`至`DependencyInjection`容器，如下列程式碼所示。 SDK 會自動挑選任何`TelemetryInitializer`加入至`DependencyInjection`容器。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>移除 TelemetryInitializers

遙測初始設定式是預設存在的項目。 若要移除所有或特定遙測初始設定式中，使用下列程式碼範例*之後*您呼叫`AddApplicationInsightsTelemetry()`。

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

您可以新增自訂遙測處理器`TelemetryConfiguration`藉由使用擴充方法`AddApplicationInsightsTelemetryProcessor`上`IServiceCollection`。 使用中的遙測處理器[進階篩選案例](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor)以便進行更直接地控制功能已包含或排除的遙測資料傳送至 Application Insights 服務。 使用下列的範例。

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

Application Insights 會使用到的遙測模組[自動收集有用的資訊](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies)有關特定工作負載，而不需要額外的設定。

預設會啟用下列自動集合模組。 這些模組負責自動收集的遙測。 您可以停用，或將其設定為變更其預設行為。

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

若要設定任何預設`TelemetryModule`，使用擴充方法`ConfigureTelemetryModule<T>`上`IServiceCollection`，如下列範例所示。

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

### <a name="configuring-a-telemetry-channel"></a>設定遙測通道

預設通道是`ServerTelemetryChannel`。 您可以如下列範例所示來覆寫它設定。

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

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>如何追蹤不會自動收集的遙測？

取得的執行個體`TelemetryClient`藉由使用建構函式插入，以及呼叫所需`TrackXXX()`在其上的方法。 我們不建議建立新`TelemetryClient`ASP.NET Core 應用程式中的執行個體。 單一執行個體`TelemetryClient`已在中註冊`DependencyInjection`容器，共用`TelemetryConfiguration`搭配 rest 的遙測。 建立新`TelemetryClient`只建議的執行個體，需要不同於其他的遙測組態。 

下列範例示範如何追蹤來自控制站的其他遙測。

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

如需 Application Insights 中的報告功能的自訂資料的詳細資訊，請參閱[Application Insights 自訂計量 API 參考](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/)。

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>某些 Visual Studio 範本會用上 IWebHostBuilder UseApplicationInsights() 擴充方法，來啟用 Application Insights。 這種用法是否仍然有效的？

是，使用這個方法來啟用 Application Insights 是有效的。 Visual Studio 入門訓練和 Web 應用程式擴充功能中，會使用這項技術。 不過，我們建議使用`services.AddApplicationInsightsTelemetry()`因為它提供多載以控制一些設定。 就內部而言，因此如果您不需要套用自訂組態，您可以呼叫這兩種方法時，這兩種方法將執行相同的動作。

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>我要部署我的 ASP.NET Core 應用程式至 Web 應用程式。 仍應啟用 Application Insights 延伸模組，從 Web 應用程式嗎？

如果在建置階段安裝 SDK，這篇文章中所示，您不需要啟用 Application Insights 延伸模組，從 App Service 入口網站。 即使已安裝擴充功能，它將會退出當 ata 偵測到 SDK 已經加入應用程式。 如果您啟用 Application Insights 從延伸模組時，您不需要安裝及更新的 SDK。 但如果您依照這篇文章中的指示啟用 Application Insights，因為有更大的彈性：

   * Application Insights 遙測會繼續運作：
       * 所有的作業系統，包括 Windows、 Linux 和 mac。
       * 所有發行模式中，包括獨立或架構相依。
       * 所有目標架構，包括完整的.NET Framework。
       * 所有的裝載選項，包括 Web 應用程式、 Vm、 Linux、 容器、 Azure Kubernetes 服務，和非 Azure 裝載。
   * 當您從 Visual Studio 偵錯，您可以在本機上看到遙測。
   * 您可以使用，以追蹤其他自訂遙測`TrackXXX()`API。
   * 您可以透過組態來完整控制。

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>啟用 Application Insights 監視使用狀態監視器等工具？

沒有。 [狀態監視器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)並[狀態監視器 v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview)目前支援 ASP.NET 4.x 只。

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Application Insights 會自動啟用我的 ASP.NET Core 2.0 應用程式嗎？

`Microsoft.AspNetCore.All` 2.0 中繼套件包含 Application Insights SDK （版本 2.1.0）。 如果您執行 Visual Studio 偵錯工具中的應用程式時，Visual Studio 可讓 Application Insights，並在 IDE 本身會在本機顯示遙測。 遙測資料不是傳送至 Application Insights 服務中，除非指定檢測金鑰。 我們建議您遵循這篇文章中的指示，以啟用 Application Insights 中，即使是針對 2.0 應用程式。

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>如果我在 Linux 中執行我的應用程式，都支援所有功能？

是。 SDK 的功能支援是相同的所有平台，但有下列例外狀況：

* 只有 Windows 才支援效能計數器。
* 即使`ServerTelemetryChannel`已啟用預設的情況下，如果應用程式正在執行 Linux 或 MacOS，通道不會自動建立暫時保留遙測，如果網路有問題的本機儲存體資料夾。 由於此限制，當有暫時性的網路或伺服器問題時，會遺失遙測。 若要解決此問題，請設定通道的本機資料夾：

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

## <a name="video"></a>影片

- 簽出到這個外部逐步視訊[使用.NET Core 和 Visual Studio 中設定 Application Insights](https://www.youtube.com/watch?v=NoS9UhcR4gA&t)從零開始。
- 簽出到這個外部逐步視訊[使用.NET Core 和 Visual Studio Code 中設定 Application Insights](https://youtu.be/ygGt84GDync)從零開始。

## <a name="next-steps"></a>後續步驟

* [瀏覽使用者流程](../../azure-monitor/app/usage-flows.md)若要了解使用者如何巡覽您的應用程式。
* [設定快照集集合](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger)以查看擲回例外狀況時原始程式碼和變數的狀態。
* [使用 API](../../azure-monitor/app/api-custom-events-metrics.md)來傳送您自己的事件和度量資訊。 您的應用程式效能和使用方式的詳細檢視。
* 使用[可用性測試](../../azure-monitor/app/monitor-web-app-availability.md)持續從世界各地檢查您的應用程式。
