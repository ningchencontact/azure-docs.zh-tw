---
title: 如何使用 WebJobs SDK - Azure
description: 深入了解如何針對 WebJobs SDK 撰寫程式碼。 创建事件驱动的后台处理作业，用于访问 Azure 服务和第三方服务中的数据。
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 0da4e1a0b20874c4452dd77bf77df0860dec455f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57848068"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>如何使用 Azure WebJobs SDK 進行事件驅動幕後處理

本文提供有关如何使用 Azure WebJobs SDK 的指导。 若要立即开始使用 WebJobs，请参阅[用于事件驱动式后台处理的 Azure WebJobs SDK 入门](webjobs-sdk-get-started.md)。 

## <a name="webjobs-sdk-versions"></a>WebJobs SDK 版本

下面是 WebJobs SDK 版本 3.*x* 与版本 2.*x* 之间的重要差别：

* 版本 3.*x* 中添加了对 .NET Core 的支持。
* 在版本 3.*x* 中，需要显式安装 WebJobs SDK 所需的存储绑定扩展。 在版本 2.*x* 中，存储绑定包含在 SDK 中。
* 用于 .NET Core 的 Visual Studio 工具 (3.*x*) 项目不同于 .NET Framework 工具 (2.*x*) 项目。 有关详细信息，请参阅[使用 Visual Studio 开发和部署 WebJob - Azure 应用服务](webjobs-dotnet-deploy-vs.md)。

本文会尽量提供同时适用于版本 3.*x* 和版本 2.*x* 的示例。

> [!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) 是基于 WebJobs SDK 构建的，本文提供了适用于某些主题的 Azure Functions 文档的链接。 注意 Functions 与 WebJobs SDK 之间的以下差异：
> * Azure Functions 版本 2.*x* 对应于 WebJobs SDK 版本 3.*x*，Azure Functions 1.*x* 对应于 WebJobs SDK 2.*x*。 源代码存储库使用 WebJobs SDK 编号。
> * Azure Functions C# 类库的示例代码类似于 WebJobs SDK 代码，不过，在 WebJobs SDK 项目中，无需指定 `FunctionName` 特性。
> * 某些绑定类型，例如 HTTP (Webhook) 以及基于 HTTP 的事件网格，只在 Functions 中受支持。
>
> 如需詳細資訊，請參閱[比較 WebJobs SDK 和 Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs) (英文)。

## <a name="webjobs-host"></a>WebJobs 主機

此主機是函式的執行階段容器。  它會接聽觸發程序並呼叫函式。 在版本 3.*x* 中，主机是 `IHost` 的实现。 在版本 2.*x* 中，使用的是 `JobHost` 对象。 您可以在程式碼中建立主機執行個體，並撰寫程式碼以自訂其行為。

这是直接使用 WebJobs SDK 与通过 Azure Functions 间接使用它的主要差别。 在 Azure Functions 中，由于由服务控制主机，因此无法通过编写代码来定义主机。 Azure Functions 允许通过 host.json 文件中的设置自定义主机行为。 这些设置是字符串而不是代码，限制可执行的自定义类型。

### <a name="host-connection-strings"></a>主機連接字串

在本地运行时，WebJobs SDK 在 local.settings.json 文件中查找 Azure 存储和 Azure 服务总线连接字符串；在 Azure 中运行时，它会在 WebJob 的环境中查找这些字符串。 默认情况下，需要名为 `AzureWebJobsStorage` 的存储连接字符串设置。  

使用版本 2.*x* 的 SDK，可以对这些连接字符串使用自己的名称，或将其存储于其他位置。 您可以在程式碼中使用設定名稱[ `JobHostConfiguration` ]，如下所示：

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

由于版本 3.*x* 使用默认的 .NET Core 配置 API，因此没有 API 可用于更改连接字符串名称。

### <a name="host-development-settings"></a>主機開發設定

您可以在開發模式中執行主機，讓本機開發更有效率。 下面介绍部分设置，这些设置在开发模式下运行时会发生更改：

| 屬性 | 開發設定 |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` 將記錄輸出最大化。 |
| `Queues.MaxPollingInterval`  | 最低值可確保立即觸發佇列方法。  |
| `Singleton.ListenerLockPeriod` | 15 秒在快速反覆式開發法中有幫助。 |

启用开发模式的过程取决于 SDK 版本。 

#### <a name="version-3x"></a>版本 3.*x*

第 3 版。*x*使用標準的 ASP.NET Core Api。 呼叫[ `UseEnvironment` ](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment)方法[ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder)執行個體。 將名為字串傳遞`development`，如這個範例所示：

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

#### <a name="version-2x"></a>版本 2.*x*

`JobHostConfiguration` 類別具有可啟用開發模式的 `UseDevelopmentSettings` 方法。  下列範例示範如何使用開發設定。 若要使 `config.IsDevelopment` 在本地运行时返回 `true`，请设置名为 `AzureWebJobsEnv`、值为 `Development` 的本地环境变量。

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-servicepointmanager-settings"></a>管理并发连接数（版本 2.*x*）

在版本 3.*x* 中，连接限制默认为无限次连接。 如果基於某些原因，您需要變更這項限制，您可以使用[ `MaxConnectionsPerServer` ](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver)屬性[ `WinHttpHandler` ](/dotnet/api/system.net.http.winhttphandler)類別。

在版本 2.*x* 中，使用 [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) API 控制主机的并发连接数。 在 2.*x* 中，应在启动 WebJobs 主机之前，在默认值 2 的基础上增大此值。

使用 `HttpClient` 从某个函数发出的所有传出 HTTP 请求都会流经 `ServicePointManager`。 达到 `DefaultConnectionLimit` 中设置的值后，`ServicePointManager` 会开始将请求排队，然后再发送请求。 假設您的 `DefaultConnectionLimit` 設為 2，且您的程式碼會發出 1,000 個 HTTP 要求。 一開始，只允許兩個要求傳至作業系統。 其他的 998 個要求會排入佇列，直到有足夠的空間給它們。 这意味着 `HttpClient` 可能会超时，因为它似乎已发出请求，但是，OS 从未将此请求发送到目标服务器。 因此，您可能會看到一個似乎不合理的行為：您的本機 `HttpClient` 花費 10 秒完成要求，但您的服務正以 200 毫秒的時間傳回每個要求。 

ASP.NET 应用程序的默认值是 `Int32.MaxValue`，这可能非常适合在“基本”或更高级别应用服务计划中运行的 WebJob。 WebJob 通常需要 Always On 设置，该设置仅受“基本”和更高级别应用服务计划的支持。

如果 WebJob 正在免費或共用的 App Service 方案中執行，則您的應用程式會受到 App Service 沙箱的限制，沙箱目前的[連線限制為 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)。 如果在 `ServicePointManager` 中指定无限制的连接数，则很有可能会达到沙盒连接阈值，并且站点将会关闭。 在此狀況下，將 `DefaultConnectionLimit` 設為更低的值，如 50 或 100，可防止這種狀況發生，同時仍允許有足夠的輸送量。

此設定必須先設定，才能發出任何 HTTP 要求。 出于此原因，WebJobs 主机不应自动尝试调整该设置。 在主机启动之前可能已发生 HTTP 请求，因而可能导致意外的行为。 最佳的做法是先在 `Main` 方法中设置值，紧接着初始化 `JobHost`，如下所示：

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>觸發程序

函数必须是公共方法，并且必须包含一个触发器特性或 [`NoAutomaticTrigger`](#manual-trigger) 特性。

### <a name="automatic-triggers"></a>自动触发器

自動觸發程序會呼叫函式以回應事件。 以下示例函数由添加到 Azure 队列存储的消息触发。 该函数的响应方式是从 Azure Blob 存储中读取 Blob：

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

`QueueTrigger` 特性告知运行时，每当某个队列消息显示在 `myqueue-items` 队列中，就要调用该函数。 `Blob` 特性告知运行时要使用队列消息读取 *sample-workitems* 容器中的 Blob。 在 `myQueueItem` 参数中传递给函数的队列消息的内容是 Blob 的名称。


### <a name="manual-triggers"></a>手动触发器

若要手动触发函数，请使用 `NoAutomaticTrigger` 特性，如下所示：

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

手动触发函数的过程取决于 SDK 版本。

#### <a name="version-3x"></a>版本 3.*x*

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>版本 2.*x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>輸入與輸出繫結

輸入繫結會提供宣告式方法，讓 Azure 或協力廠商服務中的資料可供您的程式碼使用。 輸出繫結會提供更新資料的方法。 [入门](webjobs-sdk-get-started.md)文章中演示了输入和输出绑定的示例。

您可以使用輸出繫結的方法的傳回值，藉由將屬性套用至方法的傳回值。 請參閱中的範例[使用 Azure 函式傳回值](../azure-functions/functions-bindings-return-value.md)。

## <a name="binding-types"></a>繫結型別

安装和管理绑定类型的过程取决于使用的是 SDK 版本 3.*x* 还是版本 2.*x*。 可以在特定绑定类型的 Azure Functions [参考文章](#binding-reference-information)的“包”部分找到要为该绑定类型安装的包。 异常是 Files 触发器和绑定（适用于本地文件系统），不受 Azure Functions 的支持。

#### <a name="version-3x"></a>版本 3.*x*

在版本 3.*x* 中，存储绑定包含在 `Microsoft.Azure.WebJobs.Extensions.Storage` 包中。 在 `ConfigureWebJobs` 方法中调用 `AddAzureStorage` 扩展方法，如下所示：

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

若要使用其他觸發程序與繫結類型，請安裝包含這些項目的 NuGet 套件，並呼叫在擴充功能中實作的 `Add<binding>` 擴充方法。 例如，若要使用 Azure Cosmos DB 绑定，请安装 `Microsoft.Azure.WebJobs.Extensions.CosmosDB` 并调用 `AddCosmosDB`，如下所示：

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

若要使用核心服務中包含的計時器觸發程序或檔案繫結，請分別呼叫 `AddTimers` 或 `AddFiles` 擴充方法。

#### <a name="version-2x"></a>版本 2.*x*

以下触发器和绑定类型包含在版本 2.*x* 的 `Microsoft.Azure.WebJobs` 包中：

* Blob 儲存體
* 佇列儲存體
* 表格儲存體

若要使用其他觸發程序與繫結型別，請安裝包含它們的 NuGet 封裝，並在 `JobHostConfiguration` 物件上呼叫 `Use<binding>` 方法。 例如，若要使用 Timer 触发器，请安装 `Microsoft.Azure.WebJobs.Extensions` 并在 `Main` 方法中调用 `UseTimers`，如下所示：

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

若要使用檔案繫結，請安裝 `Microsoft.Azure.WebJobs.Extensions` 並呼叫 `UseFiles`。

### <a name="executioncontext"></a>ExecutionContext

WebJobs 可讓您繫結至 [`ExecutionContext`]。 透過此繫結，您將可存取函式簽章中的參數 [`ExecutionContext`]。 例如，下列程式碼會使用內容物件存取引動過程識別碼，而您可以使用此識別碼讓給定的函式引動過程所產生的所有記錄相互關聯。  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

绑定到 [`ExecutionContext`] 的过程取决于所用的 SDK 版本。

#### <a name="version-3x"></a>版本 3.*x*

在 `ConfigureWebJobs` 方法中调用 `AddExecutionContextBinding` 扩展方法，如下所示：

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

#### <a name="version-2x"></a>版本 2.*x*

先前所提到的 `Microsoft.Azure.WebJobs.Extensions` 封裝方法也提供特殊的繫結型別，您可以呼叫 `UseCore` 方法來註冊該繫結型別。 使用此绑定可以在函数签名中定义 [`ExecutionContext`] 参数，函数签名的启用方式如下：

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>繫結設定

可以配置某些触发器和绑定的行为。 配置过程取决于 SDK 版本。

* **版本 3.*x*：** 在 `ConfigureWebJobs` 中调用 `Add<Binding>` 方法时设置配置。
* **版本 2.*x*：** 通过在传入 `JobHost` 的配置对象中设置属性来设置配置。

这些特定于绑定的设置相当于 Azure Functions 的 [host.json 项目文件](../azure-functions/functions-host-json.md)中的设置。

可配置以下绑定：

* [Azure CosmosDB 触发器](#azure-cosmosdb-trigger-configuration-version-3x)
* [事件中心触发器](#event-hubs-trigger-configuration-version-3x)
* 佇列儲存體觸發程序
* [SendGrid 绑定](#sendgrid-binding-configuration-version-3x)
* [服务总线触发器](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Azure CosmosDB 触发器配置（版本 3.*x*）

此示例演示如何配置 Azure Cosmos DB 触发器：

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

有关更多详细信息，请参阅 [Azure CosmosDB 绑定](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings)一文。

### <a name="event-hubs-trigger-configuration-version-3x"></a>事件中心触发器配置（版本 3.*x*）

此示例演示如何配置事件中心触发器：

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

有关更多详细信息，请参阅[事件中心绑定](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings)一文。

### <a name="queue-storage-trigger-configuration"></a>队列存储触发器配置

这些示例演示如何配置队列存储触发器：

#### <a name="version-3x"></a>版本 3.*x*

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

有关更多详细信息，请参阅[队列存储绑定](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings)一文。

#### <a name="version-2x"></a>版本 2.*x*

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

有关更多详细信息，请参阅 [host.json v1.x 参考](../azure-functions/functions-host-json-v1.md#queues)。

### <a name="sendgrid-binding-configuration-version-3x"></a>SendGrid 绑定配置（版本 3.*x*）

此示例演示如何配置 SendGrid 输出绑定：

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

有关更多详细信息，请参阅 [SendGrid 绑定](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings)一文。

### <a name="service-bus-trigger-configuration-version-3x"></a>服务总线触发器配置（版本 3.*x*）

此示例演示如何配置服务总线触发器：

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

有关更多详细信息，请参阅[服务总线绑定](../azure-functions/functions-bindings-service-bus.md#hostjson-settings)一文。

### <a name="configuration-for-other-bindings"></a>其他繫結的設定

某些触发器和绑定类型定义其自身的自定义配置类型。 例如，File 触发器允许指定要监视的根路径，如以下示例中所示：

#### <a name="version-3x"></a>版本 3.*x*

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

#### <a name="version-2x"></a>版本 2.*x*

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>繫結運算式

在屬性建構函式參數中，您可以使用運算式來解析各種來源的值。 例如，在下列程式碼中，`BlobTrigger` 屬性的路徑會建立名為 `filename`。 當用於輸出繫結時，`filename` 會解析觸發中二進位大型物件的名稱。

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

如需繫結運算式的詳細資訊，請參閱 Azure Functions 文件中的[繫結運算式和模式](../azure-functions/functions-bindings-expressions-patterns.md) (英文)。

### <a name="custom-binding-expressions"></a>自訂繫結運算式

有时，你想要在代码中指定队列名称、Blob 名称、容器或表名称，而不是进行硬编码。 例如，您可能想要在設定檔或環境變數中指定 `QueueTrigger` 屬性的佇列名稱。

为此，可以向 `JobHostConfiguration` 对象传入 `NameResolver` 对象。 在觸發程序或繫結屬性建構函式中包含預留位置，然後 `NameResolver` 程式碼會提供實際要用以取代這些預留位置的值。 占位符的标识方式是以百分号 (%) 将其括住，如下所示：

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

此程式碼可讓您在測試環境中使用名為 `logqueuetest` 的佇列，以及在生產環境中使用名為 `logqueueprod` 的佇列。 您不必使用硬式編碼的佇列名稱，而是在 `appSettings` 集合中指定項目的名稱。

如果未提供自定义值，则默认值 `NameResolver` 将会生效。 此預設值會從應用程式設定或環境變數取得值。

`NameResolver` 类从 `appSettings` 获取队列名称，如下所示：

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>版本 3.*x*

使用依赖关系注入配置解析程序。 這些範例需要下列 `using` 陳述式：

```cs
using Microsoft.Extensions.DependencyInjection;
```

您新增的解析程式藉由呼叫[ `ConfigureServices` ]擴充方法[ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder)，如這個範例所示：

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>版本 2.*x*

将 `NameResolver` 类传入 `JobHost` 对象，如下所示：

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions 會實作 `INameResolver`，以從應用程式設定取得值，如下面範例所示。 當您直接使用 WebJobs SDK 時，您可以撰寫自訂實作，以從任何您偏好的來源取得預留位置取代值。

## <a name="binding-at-runtime"></a>執行階段的繫結

如果需要在使用 `Queue`、`Blob` 或 `Table` 等绑定特性之前在函数中执行某项操作，可以使用 `IBinder` 接口。

下列範例會使用輸入佇列訊息，並在輸出佇列中建立含有相同內容的新訊息。 輸出佇列名稱會由函數主體中的程式碼設定。

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

如需詳細資訊，請參閱 Azure Functions 文件中的[執行階段的繫結](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) (英文)。

## <a name="binding-reference-information"></a>繫結參考資訊

Azure Functions 文档中提供了有关每个绑定类型的参考信息。 每篇绑定参考文章中介绍了以下信息。 （此示例基于存储队列。）

* [包](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x)。 需要安装哪个包才能在 WebJobs SDK 项目中支持绑定。
* [示例](../azure-functions/functions-bindings-storage-queue.md#trigger---example)。 代码示例。 C# 类库示例适用于 WebJobs SDK。 只需省略 `FunctionName` 特性。
* [特性](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes)。 用于绑定类型的特性。
* [配置](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration)。 特性属性和构造函数参数的解释。
* [使用量](../azure-functions/functions-bindings-storage-queue.md#trigger---usage)。 可绑定到哪些类型，以及有关绑定工作原理的信息。 例如：輪詢演算法、有害佇列處理。
  
有关绑定参考文章的列表，请参阅 Azure Functions [触发器和绑定](../azure-functions/functions-triggers-bindings.md#supported-bindings)一文中的“支持的绑定”。 在该列表中，HTTP、Webhook 和事件网格绑定仅受 Azure Functions 的支持，而不受 WebJobs SDK 的支持。

## <a name="disable-attribute"></a>Disable 屬性 

[`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) 特性用于控制是否可以触发某个函数。 

在以下示例中，如果应用设置 `Disable_TestJob` 使用值 `1` 或 `True`（不区分大小写），则函数不会运行。 在此情況下，執行階段會建立記錄訊息「*函式 'Functions.TestJob' 已停用*」。

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

在 Azure 门户中更改应用设置值时，WebJob 会重启并选取新的设置。

此屬性可在參數、方法或類別等級宣告。 設定名稱也可以包含繫結運算式。

## <a name="timeout-attribute"></a>Timeout 屬性

如果某个函数在指定的时间段内未完成，则 [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) 特性会导致该函数被取消。 以下示例中的函数不带 Timeout 特性，将会运行一天。 如果指定了 Timeout，该函数将在 15 秒后被取消。

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

可以在类或方法级别应用 Timeout 特征，并可以使用 `JobHostConfiguration.FunctionTimeout` 指定全局超时。 类级别或方法级别的超时替代全局超时。

## <a name="singleton-attribute"></a>Singleton 屬性

[`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) 特性可确保即使有多个主机 Web 应用的实例，也只有一个函数实例运行。 使用[分布式锁定](#viewing-lease-blobs)可实现此目的。

在此示例中，在任意给定时间只会运行 `ProcessImage` 函数的单个实例：

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

某些觸發程序內建並行管理的支援：

* **QueueTrigger**。 將 `JobHostConfiguration.Queues.BatchSize` 設定為 `1`。
* **ServiceBusTrigger**。 將 `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` 設定為 `1`。
* **FileTrigger**。 將 `FileProcessor.MaxDegreeOfParallelism` 設定為 `1`。

您可以使用這些設定確保函式在單一執行個體上以單一項目執行。 若要确保在 Web 应用横向扩展到多个实例时只运行函数的单个实例，请对该函数应用侦听器级别的单一实例锁 (`[Singleton(Mode = SingletonMode.Listener)]`)。 启动 JobHost 时获取侦听器锁。 如果三個擴充的執行個體全部同時啟動，則其中只有一個執行個體會取得鎖定，且只有一個接聽程式會啟動。

### <a name="scope-values"></a>范围值

可以在单一实例中指定一个范围表达式/值。 表达式/值可确保特定范围内的所有函数执行都将序列化。 以这种方式实现更细化的锁定可以为函数提供一定程度的并行度，同时根据你的需求串行化其他调用。 例如，在以下代码中，范围表达式将绑定到传入消息的 `Region` 值。 如果队列分别在区域 East、East 和 West 中包含 3 条消息，则区域为“East”的消息将串行运行，而区域为 West 的消息将与 East 中的这些消息并行运行。

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

锁的默认范围为 `SingletonScope.Function`，这意味着，锁范围（Blob 租约路径）已绑定到完全限定的函数名称。 若要跨函数锁定，请指定 `SingletonScope.Host`，并使用在不想要同时运行的所有函数中相同的范围 ID 名称。 在下列範例中，一次只有 `AddItem` 或 `RemoveItem` 的一個執行個體會執行：

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>檢視租用二進位大型物件

WebJobs SDK 使用 [Azure 二進位大型物件租用](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs)以實作分散式鎖定。 可以在 `AzureWebJobsStorage` 存储帐户的 `azure-webjobs-host` 容器中的路径“locks”下面找到单一实例使用的租约 Blob。 例如，稍早所示第一個 `ProcessImage` 範例的租用二進位大型物件路徑可以是 `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`。 所有路徑皆包含 JobHost 識別碼，在此個案中為 061851c758f04938a4426aa9ab3869c0。

## <a name="async-functions"></a>Async 函數

有关如何编写异步函数代码的信息，请参阅 [Azure Functions 文档](../azure-functions/functions-dotnet-class-library.md#async)。

## <a name="cancellation-tokens"></a>取消權杖

如需如何處理取消權杖的相關資訊，請參閱[取消權杖與順利關機](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens) (英文) 上的 Azure Functions 文件。

## <a name="multiple-instances"></a>多個執行個體

如果您的 Web 應用程式在多個執行個體上執行，則會有一個連續的 WebJob 在每個執行個體上執行，以接聽觸發程序和呼叫函式。 各種觸發程序繫結的設計是為了有效地共用在執行個體之間合作的工作，如此便能擴充至更多執行個體，讓您能處理更多負載。

队列和 Blob 触发器自动阻止函数多次处理队列消息或 Blob；函数不需要是幂等的。

計時器觸發程序會自動確保計時器只有一個執行個體會執行，因此在指定的排程時間不會有多個函式正在執行中。

如果要确保即使有多个主机 Web 应用的实例，也只有一个函数实例运行，可以使用 [`Singleton`](#singleton-attribute) 特性。

## <a name="filters"></a>篩選器

函式篩選條件 (預覽) 讓您能夠使用自己的邏輯自訂 WebJobs 執行管線。 筛选器类似于 [ASP.NET Core 筛选器](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters)。 可将其实现为应用到函数或类的声明性特性。 如需詳細資訊，請參閱[函式篩選條件](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters) (英文)。

## <a name="logging-and-monitoring"></a>記錄和監視

我们建议使用针对 ASP.NET 开发的日志记录框架。 [入门](webjobs-sdk-get-started.md)文章中介绍了其用法。 

### <a name="log-filtering"></a>記錄篩選

`ILogger` 執行個體建立的每個記錄皆有相關聯的 `Category` 和 `Level`。 [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) 是一个枚举，整数代码指示相对重要性：

|LogLevel    |代碼|
|------------|---|
|追蹤       | 0 |
|偵錯       | 1 |
|資訊 | 2 |
|警告     | 3 |
|Error       | 4 |
|重要    | 5 |
|None        | 6 |

您可以獨立地篩選至特定的每個類別目錄[ `LogLevel` ](/dotnet/api/microsoft.extensions.logging.loglevel)。 例如，您可能想要看見二進位大型物件觸發程序處理的所有記錄，但只看見所有其他項目的 `Error` 以上等級記錄。

#### <a name="version-3x"></a>版本 3.*x*

版本 3.*x* 的 SDK 依赖于 .NET Core 内置的筛选。 `LogCategories` 類別可讓您為特定的函式、觸發程序或使用者定義類別。 它还能为特定主机状态（例如，`Startup` 和 `Results`）定义筛选器。 这样就可以微调日志记录输出。 如果在已定義的類別內找不到相符項目，則在決定是否篩選訊息時，篩選條件會回復為 `Default` 值。

`LogCategories` 需要下列 using 陳述式：

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

以下示例构造的筛选器默认会筛选 `Warning` 级别的所有日志。 `Function` 和 `results`类别（等效于版本 2.*x* 中的 `Host.Results`）在 `Error` 级别进行筛选。 篩選條件會將目前的類別與 `LogCategories` 執行個體中所有已註冊的層級比較，並選擇最長的相符項目。 这意味着，为 `Host.Triggers` 注册的 `Debug` 级别将匹配 `Host.Triggers.Queue` 或 `Host.Triggers.Blob`。 這可讓您控制更廣泛的分類，但無需一一新增。

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>版本 2.*x*

在版本 2.*x* 的 SDK 中，`LogCategoryFilter` 类用于控制筛选。 `LogCategoryFilter` 包含初始值为 `Information` 的 `Default` 属性，这意味着，将会记录级别为 `Information`、`Warning`、`Error` 或 `Critical` 的所有消息，但会筛选掉级别为 `Debug` 或 `Trace` 的所有消息。

与版本 3.*x* 中的 `LogCategories` 一样，使用 `CategoryLevels` 属性可以指定特定类别的日志级别，以便能够微调日志记录输出。 如果在 `CategoryLevels` 目錄內找不到相符項目，則當決定是否篩選訊息時，篩選條件會回復到 `Default` 值。

下列範例建構一個預設會篩選 `Warning` 等級所有記錄的篩選條件。 `Function` 和 `Host.Results` 类别在 `Error` 级别进行筛选。 The `LogCategoryFilter` 會將目前分類與所有已註冊的 `CategoryLevels` 比較，並選擇最長的相符項目。 因此，为 `Host.Triggers` 注册的 `Debug` 级别将匹配 `Host.Triggers.Queue` 或 `Host.Triggers.Blob`。 這可讓您控制更廣泛的分類，但無需一一新增。

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Application Insights 的自訂遙測

實作自訂遙測的程序[Application Insights](../azure-monitor/app/app-insights-overview.md)的 SDK 版本而定。 若要了解如何設定 Application Insights，請參閱[新增 Application Insights 記錄](webjobs-sdk-get-started.md#add-application-insights-logging)。

#### <a name="version-3x"></a>版本 3.*x*

因為第 3 版。*x* WebJobs SDK 的依賴.NET Core，不會再提供泛型主機，自訂的遙測資料處理站。 但是，您也可以使用相依性插入到管線新增自訂遙測。 本節中的範例需要下列 `using` 陳述式：

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

[`ITelemetryInitializer`] 的下列自訂實作可讓您將自己的 [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) 新增至預設 [`TelemetryConfiguration`]。

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

在建立器中呼叫 [`ConfigureServices`]，以將您的自訂 [`ITelemetryInitializer`] 新增至管線。

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

[`TelemetryConfiguration`] 建構後，將會納入所有已註冊的 [`ITelemetryInitializer`] 類型。 若要進一步了解，請參閱[自訂事件和度量的 Application Insights API](../azure-monitor/app/api-custom-events-metrics.md)。

在第 3 版。*x*，您不再需要排清[ `TelemetryClient` ]主應用程式就會停止。 .NET Core 相依性插入系統會自動處置已註冊的 `ApplicationInsightsLoggerProvider`，而排清 [`TelemetryClient`]。

#### <a name="version-2x"></a>版本 2.*x*

在第 2 版。*x*，則[ `TelemetryClient` ] WebJobs SDK 會使用在內部建立 Application Insights 提供者[ `ServerTelemetryChannel` ](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)。 當 Application Insights 端點無法使用或正在節流連入要求時，此通道[會將要求儲存在 Web 應用程式的檔案系統中，並於稍後重新提交](https://apmtips.com/blog/2015/09/03/more-telemetry-channels)。

[`TelemetryClient`] 由實作 `ITelemetryClientFactory` 的類別所建立。 根據預設，這會是 [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs)。

如果您想要修改 Application Insights 管線的任何一部分，您可以提供自己的 `ITelemetryClientFactory`，主機將使用您的類別來建構 [`TelemetryClient`]。 比方說，這段程式碼會覆寫`DefaultTelemetryClientFactory`若要修改的屬性`ServerTelemetryChannel`:

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

`SamplingPercentageEstimatorSettings`物件會設定[調適型取樣](https://docs.microsoft.com/azure/application-insights/app-insights-sampling#adaptive-sampling-at-your-web-server)。 這表示，在某些高容量的情況，Application Insights 會傳送遙測資料的子集選取伺服器。

建立遙測資料處理站之後，您會將它傳入至 Application Insights 記錄提供者：

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> 後續步驟

本文提供的代码片段演示了如何处理 WebJobs SDK 的常用方案。 如需完整範例，請參閱 [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk-samples) (英文)。

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
