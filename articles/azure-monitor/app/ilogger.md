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
ms.openlocfilehash: 9d5e25e0fd00f9c0635009f684e79336d58b7b4a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263752"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>適用於.NET Core ILogger 記錄 ApplicationInsightsLoggerProvider

ASP.NET Core 支援的不同類型的內建和協力廠商記錄提供者的運作方式的記錄 API。 記錄是藉由呼叫 log （） 或它的變體`ILogger`執行個體。 這篇文章示範如何使用`ApplicationInsightsLoggerProvider`擷取`ILogger`主控台和 ASP.NET Core 應用程式中的記錄。 本文也說明如何`ApplicationInsightsLoggerProvider`與其他 Application Insights 遙測整合。
若要深入了解 Asp.Net Core 中的多個記錄，請參閱[這篇文章](https://docs.microsoft.com/aspnet/core/fundamentals/logging)。

## <a name="aspnet-core-applications"></a>ASP.NET Core 應用程式

開頭[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)及更新版本，版本 2.7.0-beta3`ApplicationInsightsLoggerProvider`時啟用一般 Application Insights 監視使用可能是標準的方法-依預設會啟用呼叫`UseApplicationInsights`IWebHostBuilder 擴充方法或`AddApplicationInsightsTelemetry`IServiceCollection 擴充方法。 `ILogger` 所擷取的記錄檔`ApplicationInsightsLoggerProvider`受限於與其他遙測收集相同的組態。 亦即 它們有相同的一組`TelemetryInitializer`s `TelemetryProcessor`s，會使用相同`TelemetryChannel`，並將相互關聯和取樣中每個其他遙測一樣。  如果您是此版本的 SDK 或更新版本中，則不需要任何動作，以擷取`ILogger`記錄檔。

根據預設，只有`ILogger`記錄的`Warning`或以上 （所有類別） 來都傳送至 Application Insights。 可以變更此行為，藉由套用篩選，如所示[此處](#control-logging-level)。 額外的步驟也會需要如果`ILogger`記錄來自`Program.cs`或`Startup.cs`所示擷取[這裡](#capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications)。

如果您使用舊版 Microsoft.ApplicationInsights.AspNet SDK，或您想要只使用 ApplicationInsightsLoggerProvider，而不需要任何其他 Application Insights 監視，請遵循下列步驟。

1.安裝 nuget 套件。

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
    </ItemGroup>
```

2.修改`Program.cs`，如下所示

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
                // Providing an instrumentation key here is required if you are using
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

上述程式碼會設定`ApplicationInsightsLoggerProvider`。 下圖顯示範例控制器類別，它會使用`ILogger`若要將 application insights 所擷取的記錄傳送。

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

### <a name="capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications"></a>擷取從 Startup.cs，Asp.Net Core 應用程式中的 Program.cs 的 ILogger 記錄檔

使用新的 ApplicationInsightsLoggerProvider，就可以擷取從及早在應用程式啟動管線中的記錄檔。 甚至是 ApplicationInsightsLoggerProvider 自動啟用 Application Insights （從 2.7.0-beta3 及更新版本)，它並沒有檢測金鑰設定，直到稍後在管線中，所以只記錄從控制器/其他類別將會擷取。 要擷取開頭為每個記錄檔`Program.cs`和`Startup.cs`本身，需要以明確地啟用 ApplicationInsightsLoggerProvider 檢測金鑰。 它也是很重要的一點`TelemetryConfiguration`並未完整設定時記錄的某些資訊`Program.cs`或`Startup.cs`本身，因此這些記錄檔會使用裸機的最低設定，它會使用 InMemoryChannel、 沒有取樣，並沒有標準的遙測初始設定式或處理器。

下圖顯示的範例`Program.cs`和`Startup.cs`使用這項功能。

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
            // providing an instrumentation key here is required if you are using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Program.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Program", LogLevel.Trace);
            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Startup.cs
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

## <a name="migrating-from-old-applicationinsightsloggerprovider"></a>從舊的 ApplicationInsightsLoggerProvider 移轉

現已過時的記錄提供者支援 2.7.0-beta2 之前, 的 Microsoft.ApplicationInsights.AspNet SDK 版本。 此提供者已啟用`AddApplicationInsights()`擴充方法的`ILoggerFactory`。 此提供者現已淘汰，並建議使用者移轉至新的提供者。 移轉牽涉到兩個步驟。

1. 移除從 ILoggerFactory.AddApplicationInsights() 呼叫`Startup.Configure()`方法來避免雙重的記錄。
2. 重新套用任何程式碼中的篩選規則，因為它們會忽略新的提供者。 ILoggerFactory.AddApplicationInsights() 的多載所花費的最小 LogLevel 或篩選函式。 使用新的提供者，篩選是本身的記錄架構的一部分並不會由 Application Insights 提供者。 透過提供，因此任何篩選條件`ILoggerFactory.AddApplicationInsights()`應該移除多載，並篩選規則應提供下列[這些](#control-logging-level)指示。 如果您使用`appsettings.json`若要篩選記錄，它會繼續使用新的提供者，因為兩者都使用相同的提供者別名- **ApplicationInsights**。

雖然您還是可以使用舊的提供者 （它現在已過時，而將只移除 3.xx 主要版本變更），因為下列因素而強烈建議移轉至較新的提供者。

1. 先前的提供者缺乏的支援[範圍](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes)。 在新的提供者，從範圍的屬性會自動為自訂屬性新增至所收集之遙測。
2. 現在可以在應用程式啟動管線中的較早擷取記錄檔。 亦即 現在可以擷取從 程式和啟動類別的記錄檔。
3. 使用新的提供者，在架構層級本身進行篩選。 篩選的記錄檔至 Application Insights 提供者可以完成其他提供者，包括內建的提供者，例如 [主控台] 中，偵錯，完全相同方式等等。 它也可將相同的篩選套用至多個提供者。
4. [建議](https://github.com/aspnet/Announcements/issues/255)啟用記錄提供者在 ASP.NET Core (2.0 及更新版本) 中的方式是使用擴充方法中 ILoggingBuilder 上`Program.cs`本身。

> [!Note]
> 新的提供者是適用於目標的應用程式`NETSTANDARD2.0`或更高版本。 如果您的應用程式以較舊的.NET Core 版本，例如.NET Core 1.1 為目標，或以.NET Framework 為目標，繼續使用舊的提供者。

## <a name="console-application"></a>主控台應用程式

下列程式碼顯示範例主控台應用程式設定為傳送`ILogger`追蹤至 Application Insights。

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
        // Create DI container.
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

在上述範例中，獨立封裝`Microsoft.Extensions.Logging.ApplicationInsights`用。 此設定預設使用最低限度的 `TelemetryConfiguration`，將資料傳送至 Application Insights。 最低限度表示會使用 `InMemoryChannel` 通道，沒有取樣，也沒有標準 TelemetryInitializers。 可以覆寫掉主控台應用程式的這項行為，如下列範例所示。

安裝此額外封裝：

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

下一節示範如何覆寫預設值`TelemetryConfiguration`使用`services.Configure<TelemetryConfiguration>()`方法。 此範例會設定`ServerTelemetryChannel`管理員、 取樣，並將自訂`ITelemetryInitializer`至`TelemetryConfiguration`。

```csharp
    // Create DI container.
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

    // Explicitly call Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if application terminates, telemetry is sent to the back-end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>控制記錄層級

Asp.Net Core`ILogger`基礎結構已套用的內建機制[篩選](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering)的記錄檔，這樣可讓使用者控制傳送至每個已註冊的提供者，包括 Application Insights 提供者的記錄檔。 此篩選可在組態中 (通常會使用`appsettings.json`檔案) 或在程式碼。 這項功能由架構本身，提供，而且不 Application Insights 提供者專用的。

將篩選規則套用到 ApplicationInsightsLoggerProvider 的範例如下所示。

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>在組態中建立篩選規則，與 appsettings.json

提供者別名是 ApplicationInsightsLoggerProvider， `ApplicationInsights`。 如下所示一節`appsettings.json`設定記錄`Warning`和更新版本所有類別，從`Error`和更新版本從類別開頭為"Microsoft"傳送到`ApplicationInsightsLoggerProvider`。

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

下列程式碼片段會設定記錄檔`Warning`和更新版本所有類別，從`Error`和更新版本從類別開頭為 'Microsoft' 傳送至`ApplicationInsightsLoggerProvider`。 這個設定等同於在以上組態中完成`appsettings.json`。

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

*1.什麼是舊的和新 ApplicationInsightsLoggerProvider？*

* [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)隨附內建 ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider)，已啟用使用 ILoggerFactory擴充方法。 此提供者已標記為過時從 2.7.0-beta2 及更新版本，並在下一個主要版本變更將會完全移除。 [這](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)封裝本身未淘汰，然後才可啟用監視的要求、 相依性等等。

* 建議的替代做法是新的獨立封裝[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)，包含改良的 ApplicationInsightsLoggerProvider （Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) 和 ILoggerBuilder 上的延伸模組方法，可啟用它。

* [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 版本及更新版本會依存於上述的套件，並可讓`ILogger`自動擷取。

*2.我看到一些`ILogger`記錄檔會顯示兩次相同的 Application Insights？*

* 這種重複狀況是如果您有較舊 （現在已過時） 版本可能`ApplicationInsightsLoggerProvider`啟用藉由呼叫`AddApplicationInsights`上`ILoggerFactory`。 如果核取您`Configure`方法具有下列項目，並將它移除。

   ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
        // ..other code.
    }
   ```

* 如果您遇到雙記錄從 Visual Studio 偵錯時，然後修改設定，如下所示，啟用 Application Insights 使用的程式碼`EnableDebugLogger`設為 false。 此重複資料刪除問題和修正程式才相關偵錯應用程式時。

   ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
        options.EnableDebugLogger = false;
        services.AddApplicationInsightsTelemetry(options);
        // ..other code.
    }
   ```

*3.我已更新為[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)版本 2.7.0-beta3，我現在看到，可將記錄從`ILogger`自動擷取。 如何可以關閉這項功能完全？*

* 請參閱[這](../../azure-monitor/app/ilogger.md#control-logging-level)一般情況下一節，以了解如何篩選記錄檔。 若體會 ApplicationInsightsLoggerProvider 来使用`LogLevel.None`它。

  在程式碼

    ```csharp
        builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                          ("", LogLevel.None);
    ```

  在組態中

    ```json
    {
      "Logging": {
        "ApplicationInsights": {
          "LogLevel": {
            "Default": "None"
          }
    }
    ```

*4.我看到一些`ILogger`記錄不會有相同的屬性，與其他人？*

* 應用程式的深入解析會擷取並送`ILogger`記錄檔使用相同`TelemetryConfiguration`用於每個其他遙測。 沒有此規則的例外狀況。 預設值`TelemetryConfiguration`並未完整設定時記錄的某些資訊`Program.cs`或是`Startup.cs`本身，因此不會預設組態中，從這些地方的記錄檔，並因此就不會執行所有`TelemetryInitializer`s 和`TelemetryProcessor`s。

*5.Application Insights 遙測類型會產生從`ILogger`記錄檔？ 或，可以看到`ILogger`登入 Application Insights？*

* 擷取 ApplicationInsightsLoggerProvider`ILogger`記錄檔，並建立`TraceTelemetry`從它。 如果例外狀況物件傳遞至 log （） 方法上 ILogger，然後而非`TraceTelemetry`、`ExceptionTelemetry`建立。 這些遙測項目可在同一個地方與任何其他`TraceTelemetry`或`ExceptionTelemetry`Application insights，包括入口網站、 分析或 Visual Studio 的本機偵錯工具。
如果您想要一律傳送`TraceTelemetry`，然後使用 程式碼片段```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```。

*5.我沒有安裝 SDK，並為我的 Asp.Net Core 應用程式啟用 Application Insights 的情況下，我在使用 Azure Web 應用程式擴充功能。 如何使用新的提供者？*

* 在 Azure Web 應用程式的 application Insights 擴充功能會使用舊的提供者。 篩選規則可修改`appsettings.json`應用程式。 如果您想要利用新的提供者，可供建置階段檢測採用 SDK 的 nuget 相依性。 延伸模組會切換成使用新的提供者時，將會更新此文件。

*6.我要使用獨立封裝 Microsoft.Extensions.Logging.ApplicationInsights，並啟用 Application Insights 提供者所呼叫的產生器。AddApplicationInsights("ikey")。 是否有選項可從組態取得檢測金鑰？*


* 修改`Program.cs`和`appsettings.json`，如下所示。

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

從相關的區段 `appsettings.json`

```json
{
  "myikeyfromconfig": "putrealikeyhere"
}
```

使用獨立的記錄提供者時，才需要上述程式碼。 一般的 Application Insights 監視，檢測金鑰會自動從載入的組態路徑`ApplicationInsights:Instrumentationkey`和`appsettings.json`看起來應該像下面。

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

* [Asp.Net Core 中的記錄](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [在 Application Insights 中的.NET 追蹤記錄檔](../../azure-monitor/app/asp-net-trace-logs.md)
