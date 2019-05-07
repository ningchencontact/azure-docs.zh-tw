---
title: 使用 ILogger 探索 Azure Application Insights 中的 .NET 追蹤記錄
description: 使用 Azure Application Insights ILogger 提供者與 ASP.NET Core 和主控台應用程式的範例。
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: bd010193bf8f001d027ae80be9272ae30a0171c9
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149979"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>適用於.NET Core ILogger 記錄 ApplicationInsightsLoggerProvider

ASP.NET Core 支援的不同類型的內建和協力廠商記錄提供者的運作方式的記錄 API。 記錄是藉由呼叫**log （)** 或在不同*ILogger*執行個體。 這篇文章示範如何使用*ApplicationInsightsLoggerProvider*来擷取 ILogger 主控台和 ASP.NET Core 應用程式中的記錄檔。 本文也說明 ApplicationInsightsLoggerProvider 如何與其他 Application Insights 遙測整合。
若要深入了解，請參閱 [ASP.NET Core 中的記錄](https://docs.microsoft.com/aspnet/core/fundamentals/logging)。

## <a name="aspnet-core-applications"></a>ASP.NET Core 應用程式

在預設會啟用 ApplicationInsightsLoggerProvider [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)版本 2.7.0-beta3 （和更新版本） 當您開啟透過其中一種標準的一般 Application Insights 監視方法：
- 藉由呼叫**UseApplicationInsights** IWebHostBuilder 擴充方法 
- 藉由呼叫**AddApplicationInsightsTelemetry** IServiceCollection 擴充方法

ILogger ApplicationInsightsLoggerProvider 擷取的記錄檔會受到其他收集的遙測資料與相同的組態。 它們擁有相同的 TelemetryInitializers 和 TelemetryProcessors，使用相同的 TelemetryChannel，相互關聯和取樣的方式與其他遙測。 如果您使用版本 2.7.0-beta3 或更新版本中，不需要擷取 ILogger 記錄任何動作。

只有*警告*或更高層級的 ILogger 記錄 （從所有的類別目錄） 依預設，會傳送至 Application Insights。 不過您也可以[套用篩選來修改此行為](#control-logging-level)。 要擷取從 ILogger 記錄檔需要額外的步驟**Program.cs**或是**Startup.cs**。 (請參閱[ASP.NET Core 應用程式中擷取的 ILogger 記錄從 Startup.cs 和 Program.cs](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps)。)

如果您使用舊版 Microsoft.ApplicationInsights.AspNet SDK，或您想要只使用 ApplicationInsightsLoggerProvider，而不需要任何其他 Application Insights 監視，請使用下列程序：

1. 安裝 NuGet 套件：

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. 修改**Program.cs**如下所示：

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("ikey");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

步驟 2 中的程式碼會設定`ApplicationInsightsLoggerProvider`。 下列程式碼顯示範例控制器類別，它會使用`ILogger`傳送記錄檔。 Application Insights 會擷取記錄檔。

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>擷取從 Startup.cs 和 Program.cs ASP.NET Core 應用程式中的 ILogger 記錄檔

新的 ApplicationInsightsLoggerProvider 可以擷取從早期應用程式啟動管線中的記錄檔。 雖然 ApplicationInsightsLoggerProvider （起版本 2.7.0-beta3） 的 Application Insights 中，會自動啟用，它沒有管線中稍後才設定檢測金鑰。 因此，只會從記錄**控制器**/ 將擷取的其他類別。 若要擷取開頭為每個記錄檔**Program.cs**並**Startup.cs**本身，您必須明確啟用檢測金鑰 ApplicationInsightsLoggerProvider。 此外， *TelemetryConfiguration*未完全設定當您從登**Program.cs**或是**Startup.cs**本身。 因此這些記錄檔必須使用 InMemoryChannel、 沒有取樣，並沒有標準遙測初始設定式或處理器的最低組態。

下列範例示範這項功能**Program.cs**並**Startup.cs**。

#### <a name="example-programcs"></a>範例 Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Program.cs.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Program", LogLevel.Trace);
            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Startup.cs.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Startup", LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>範例 Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>從舊的 ApplicationInsightsLoggerProvider 移轉

Microsoft.ApplicationInsights.AspNet SDK 版本之前 2.7.0-beta2 支援的記錄提供者，現在已過時。 此提供者已透過啟用**AddApplicationInsights()** ILoggerFactory 的擴充方法。 我們建議您移轉至新的提供者，這牽涉到兩個步驟：

1. 移除*ILoggerFactory.AddApplicationInsights()* 從呼叫**Startup.Configure()** 方法來避免雙重的記錄。
2. 重新套用程式碼中的任何篩選規則，因為它們會忽略新的提供者。 多載*ILoggerFactory.AddApplicationInsights()* 採取最小 LogLevel 或篩選函式。 使用新的提供者，篩選是本身的記錄架構的一部分。 它不是由 Application Insights 提供者。 因此任何透過所提供的篩選條件*ILoggerFactory.AddApplicationInsights()* 應移除的多載。 篩選規則應該提供依照[控制記錄層級](#control-logging-level)指示。 如果您使用*appsettings.json*若要篩選記錄，它會繼續使用新的提供者，兩者都使用相同的提供者別名，因為*ApplicationInsights*。

您仍然可以使用舊的提供者。 （它將會移除只能在主要版本變更為 3。*xx*。)但我們建議您將移轉至新的提供者，原因如下：

- 先前的提供者不支援[記錄範圍](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes)。 在新的提供者，從範圍的屬性會自動為自訂屬性新增至所收集之遙測。
- 現在可以在應用程式啟動管線中的較早擷取記錄檔。 記錄來自**計劃**並**啟動**現在可以擷取類別。
- 使用新的提供者，在架構層級本身進行篩選。 您可以在其他提供者，包括主控台中，偵錯，等的內建提供者的相同方式篩選至 Application Insights 提供者的記錄檔等等。 您也可以套用相同的篩選多個提供者。
- 在 ASP.NET Core 中 （2.0 和更新版本），建議用來[啟用記錄提供者](https://github.com/aspnet/Announcements/issues/255)ILoggingBuilder 中上使用擴充方法**Program.cs**本身。

> [!Note]
> 新的提供者是適用於 NETSTANDARD2.0 為目標的應用程式或更新版本。 如果您的應用程式目標設為較舊的.NET Core 版本，例如.NET Core 1.1 中，或它以.NET Framework 為目標，繼續使用舊的提供者。

## <a name="console-application"></a>主控台應用程式

下列程式碼會顯示已傳送至 Application Insights 的 ILogger 追蹤的範例主控台應用程式。

安裝的封裝：

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

這個範例會使用獨立封裝`Microsoft.Extensions.Logging.ApplicationInsights`。 根據預設，此組態會使用 「 最低限度 」 將資料傳送至 Application Insights 的 TelemetryConfiguration。 最低限度表示 InMemoryChannel 用的通道。 沒有任何取樣並標準 TelemetryInitializers。 可以覆寫這個行為，是主控台應用程式，如下列範例所示。

安裝此額外封裝：

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

下一節示範如何使用覆寫預設 TelemetryConfiguration**服務。設定<TelemetryConfiguration>（)** 方法。 此範例會設定`ServerTelemetryChannel`和取樣。 它會加入自訂 ITelemetryInitializer TelemetryConfiguration。

```csharp
    // Create the DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>控制記錄層級

ASP.NET Core *ILogger*基礎結構已內建的機制，以套用[記錄檔篩選](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering)。 這可讓您控制傳送至每個已註冊的提供者，包括 Application Insights 提供者的記錄檔。 篩選可在組態中 (通常是藉由使用*appsettings.json*檔案) 或在程式碼。 這項功能是由架構本身提供的。 您並非專屬於 Application Insights 提供者。

下列範例會將篩選規則套用至 ApplicationInsightsLoggerProvider。

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>在 appsettings.json 組態中建立篩選器規則

提供者別名是 ApplicationInsightsLoggerProvider， `ApplicationInsights`。 下的一節*appsettings.json*設定記錄檔*警告*和更新版本所有類別的並*錯誤*和更新版本從開頭的類別 」Microsoft 在 「 傳送至`ApplicationInsightsLoggerProvider`。

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>在程式碼中建立篩選規則

下列程式碼片段會設定記錄檔*警告*和更新版本以及所有類別的*錯誤*和更新版本從類別開頭為"Microsoft"傳送到`ApplicationInsightsLoggerProvider`。 此設定是在上一節中相同*appsettings.json*。

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>ApplicationInsightsLoggerProvider 的舊和新版本有哪些？

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)包含內建 ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider)，透過已啟用**ILoggerFactory**擴充方法。 此提供者已標記為過時的版本 2.7.0-beta2 的。 它將會遭到完全移除 [下一步] 的主要版本變更中。 [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)封裝本身不是已經過時。 您需要啟用監視的要求、 相依性，等等。

建議的替代做法是新的獨立封裝[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)，其中包含改良的 ApplicationInsightsLoggerProvider （Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) 和 ILoggerBuilder 上的擴充方法，來啟用它。

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)版本 2.7.0-beta3 依存於新的封裝，並自動啟用 ILogger 擷取。

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>原因是某些 ILogger 顯示記錄檔兩次相同的 Application Insights？

如果您有舊版 （現在已過時） ApplicationInsightsLoggerProvider 藉由呼叫已啟用，就會發生重複`AddApplicationInsights`上`ILoggerFactory`。 如果核取您**設定**方法具有下列項目，並將其移除：

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

如果您遇到雙精度浮點的記錄，當您從 Visual Studio 偵錯時，設定`EnableDebugLogger`要*false*中啟用 Application Insights，如下所示的程式碼。 此重複資料刪除和修正相關時才在偵錯應用程式。

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>我已更新為[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)版本 2.7.0-beta3 和從 ILogger 的記錄檔會自動擷取。 如何關閉這項功能完全？

請參閱[控制記錄層級](../../azure-monitor/app/ilogger.md#control-logging-level)一般情況下一節，以了解如何篩選記錄檔。 若體會 ApplicationInsightsLoggerProvider，要使用`LogLevel.None`:

**在程式碼：**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**在 設定：**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>為什麼有些 ILogger 記錄檔沒有相同的屬性，與其他人？

Application Insights 會擷取並使用相同的 TelemetryConfiguration 用於每個其他的遙測傳送 ILogger 記錄檔。 但發生例外狀況。 根據預設，TelemetryConfiguration 未完全設定當您從登**Program.cs**或是**Startup.cs**。 從這些地方的記錄檔將不會有預設組態，讓它們不會執行所有 TelemetryInitializers 和 TelemetryProcessors。

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>我使用的獨立封裝 Microsoft.Extensions.Logging.ApplicationInsights，，而且我想要手動登錄一些額外的自訂遙測。 如何應該這麼做？

當您使用獨立套件中，`TelemetryClient`沒有插入至 DI 容器，因此您需要建立的新執行個體`TelemetryClient`和記錄器提供者會使用，下列程式碼所示為使用相同的組態。 這可確保相同的設定，用於所有自訂遙測，以及從 ILogger 的遙測。

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> 如果您使用的 Microsoft.ApplicationInsights.AspNetCore 封裝來啟用 Application Insights 時，修改此程式碼，以取得`TelemetryClient`直接在建構函式中。 如需範例，請參閱[這份常見問題集](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core-no-visualstudio#frequently-asked-questions)。


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Application Insights 遙測類型會產生從 ILogger 記錄？ 或者，哪裡可以查看 ILogger 登入 Application Insights？

ApplicationInsightsLoggerProvider 擷取 ILogger 記錄檔，並從其中建立 TraceTelemetry。 如果例外狀況物件傳遞給**log （)** ILogger，方法*ExceptionTelemetry*建立而不是 TraceTelemetry。 Application insights，包括入口網站、 分析或 Visual Studio 的本機偵錯工具，可以在相同的位置，為任何其他 TraceTelemetry 或 ExceptionTelemetry 找到這些遙測項目。

如果您想要一律傳送 「 TraceTelemetry，使用此程式碼片段： ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>我沒有安裝 SDK，並為我的 ASP.NET Core 應用程式啟用 Application Insights 的情況下，我在使用 Azure Web 應用程式擴充功能。 如何使用新的提供者？ 

在 Azure Web Apps 中的 Application Insights 延伸模組會使用舊的提供者。 您可以修改中的篩選規則*appsettings.json*應用程式的檔案。 若要充分利用新的提供者，使用 建置階段檢測的 NuGet 相依性 SDK。 擴充功能會切換成使用新的提供者時，將會更新本文。

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>我使用的獨立封裝 Microsoft.Extensions.Logging.ApplicationInsights 且啟用 Application Insights 提供者，藉由呼叫**產生器。AddApplicationInsights("ikey")**。 是否有選項可從組態取得檢測金鑰？


請修改 Program.cs 和 appsettings.json，如下所示：

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   相關的區段，從`appsettings.json`:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

只有當您使用獨立的記錄提供者時，才需要此程式碼。 一般的 Application Insights 監視，檢測金鑰會自動載入的組態路徑從*application Insights:Instrumentationkey*。 Appsettings.json 看起來應該像這樣：

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>後續步驟

深入了解：

* [ASP.NET Core 中的記錄](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [在 Application Insights 中的.NET 追蹤記錄檔](../../azure-monitor/app/asp-net-trace-logs.md)
