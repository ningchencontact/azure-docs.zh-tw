---
title: 使用 ILogger 探索 Azure Application Insights 中的 .NET 追蹤記錄
description: 使用 Azure Application Insights ILogger 實作搭配 ASP.NET Core 和主控台應用程式的範例。
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: 14c9a453913a9a8080c840df3f23751487b86c34
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58006165"
---
# <a name="ilogger"></a>ILogger

ASP.NET Core 支援適用於各種內建和協力廠商記錄提供者的記錄 API。 本文會說明如何在主控台和 ASP.NET Core 應用程式中使用 Application Insights ILogger 實作處理記錄作業。 若要深入了解 ILogger 記錄，請參閱[此文章](https://docs.microsoft.com/aspnet/core/fundamentals/logging)。

## <a name="console-application"></a>主控台應用程式

以下顯示的是設定好的主控台應用程式範例，會將 ILogger 追蹤傳送至 Application Insights。

安裝的封裝：

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging" Version="2.1.0" />
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
            
        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(loggingBuilder =>
        {
        // Optional: Apply filters to configure LogLevel Trace or above is sent to ApplicationInsights for all
        // categories.
        loggingBuilder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
            loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");                
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional. Epecially in case of AspNetCore request info is already
        // present in scope.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }
    }
}
```

## <a name="aspnet-core-application"></a>ASP.NET Core 應用程式

以下顯示的是設定好的 ASP.NET Core 應用程式範例，會將 ILogger 追蹤傳送至 Application Insights。 可按照此範例的做法，從 Program.cs、Startup.cs 或其他任何控制器/應用程式邏輯傳送 ILogger 追蹤。

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = BuildWebHost(args);
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        logger.LogInformation("From Program. Running the host now.."); // This will be picked up up by AI
        host.Run();
    }

    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()                
        .ConfigureLogging(logging =>
        {                
        logging.AddApplicationInsights("ikeyhere");
                
        // Optional: Apply filters to configure LogLevel Trace or above is sent to
        // ApplicationInsights for all categories.
            logging.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
                
            // Additional filtering For category starting in "Microsoft",
        // only Warning or above will be sent to Application Insights.
        logging.AddFilter<ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Warning);
        })
        .Build();
}
```

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    
    // The following be picked up up by Application Insights.
        _logger.LogInformation("From ConfigureServices. Services.AddMVC invoked"); 
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
        // The following be picked up up by Application Insights.   
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following be picked up up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked upby Application Insights.
    // and all have ("MyKey", "MyValue") in Properties.
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
        {           
        _logger.LogInformation("An example of a Information trace..");
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogTrace("An example of a Trace level message");
        }

        return new string[] { "value1", "value2" };
    }
}
```

以上兩個範例使用的是 Microsoft.Extensions.Logging.ApplicationInsights 獨立封裝。 此設定預設使用最低限度的 `TelemetryConfiguration`，將資料傳送至 Application Insights。 最低限度表示會使用 `InMemoryChannel` 通道，沒有取樣，也沒有標準 TelemetryInitializers。 可以覆寫掉主控台應用程式的這項行為，如下列範例所示。

安裝此額外封裝：

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

下一節會示範如何覆寫預設的 `TelemetryConfiguration`。 此範例設定了 `ServerTelemetryChannel`、取樣和自訂的 `ITelemetryInitializer`。

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

    // Add the logging pipelines to use. We are adding ApplicationInsights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });
```

您可以在 ASP.NET Core 應用程式中使用上述方法，不過更常見的方式是搭配使用一般的應用程式監視 (要求、相依性等) 與 ILogger 擷取，如下所示。

安裝此額外封裝：

```xml
<PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
```

將下列內容新增至 `ConfigureServices` 方法。 此程式碼能以預設設定啟用一般的應用程式監視 (ServerTelemetryChannel LiveMetrics、要求/相依性、相互關聯等)。

```csharp
services.AddApplicationInsightsTelemetry("ikeyhere");
```

在此範例中，`ApplicationInsightsLoggerProvider` 使用的設定與一般應用程式監視所用的相同。 因此，`ILogger` 追蹤和其他遙測 (要求、相依性等) 會執行同一組 `TelemetryInitializers`、`TelemetryProcessors` 和 `TelemetryChannel`， 會以相同的方式相互關聯並取樣/不取樣。

不過，這種行為有個例外狀況。 記錄 `Program.cs` 或 `Startup.cs` 本身的內容時，並未完整設定預設的 `TelemetryConfiguration`，因此那些記錄不具有預設設定。 不過，每個其他每個記錄 (例如，控制器、模型等記錄) 會共用設定。

## <a name="control-logging-level"></a>控制記錄層級

除了篩選記錄檔，如上述範例所示的程式碼，它也可的控制擷取的 Application Insights 的記錄層級，藉由修改`appsettings.json`。 [記錄基本概念文件的 ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering)示範如何達成此目的。 針對 Application Insights 提供者別名的名稱是`ApplicationInsights`中所示，下列範例中也就是設定`ApplicationInsights`要擷取的記錄檔`Warning`和更新版本所有類別的。

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

## <a name="next-steps"></a>後續步驟

深入了解：

- [ILogger 記錄](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
- [.NET 追蹤記錄](../../azure-monitor/app/asp-net-trace-logs.md)
