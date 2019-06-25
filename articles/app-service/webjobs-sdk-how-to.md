---
title: 如何使用 WebJobs SDK - Azure
description: 深入了解如何針對 WebJobs SDK 撰寫程式碼。 建立事件驅動的背景處理作業，存取 Azure 服務和協力廠商服務中的資料。
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
ms.openlocfilehash: 38d8bdfcba48d2080b434ebec192b41f3663ae6a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60831787"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>如何使用 Azure WebJobs SDK 進行事件驅動幕後處理

本文章提供有關如何使用 Azure WebJobs SDK 的指引。 若要立即開始使用 WebJobs，請參閱[開始使用適用於事件驅動的背景處理的 Azure WebJobs SDK](webjobs-sdk-get-started.md)。 

## <a name="webjobs-sdk-versions"></a>WebJobs SDK 版本

這些是第 3 版之間的主要差異。*x*和第 2 版。*x* WebJobs SDK 的：

* 第 3 版。*x*新增適用於.NET Core 的支援。
* 在第 3 版。*x*，您需要明確安裝 WebJobs SDK 所需的儲存體繫結延伸模組。 在第 2 版。*x*，SDK 中所包含的儲存體繫結。
* Visual Studio 工具適用於.NET Core (3。*x*) 專案不同於適用於.NET Framework 的工具 (2。*x*) 專案。 若要進一步了解，請參閱[開發使用 Visual Studio-Azure App Service 和部署 Webjob](webjobs-dotnet-deploy-vs.md)。

可能的話，請將這兩個第 3 版的提供範例。*x*和第 2 版。*x*。

> [!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md)基礎 WebJobs SDK，和這篇文章提供 Azure Functions 文件的一些主題的連結。 請注意這些函式和 WebJobs SDK 之間的差異：
> * Azure Functions 第 2 版。*x*對應至 WebJobs SDK 第 3 版。*x*，和 Azure Functions 1。*x*對應到 WebJobs SDK 2。*x*。 原始程式碼儲存機制使用 WebJobs SDK 編號。
> * Azure Functions 的範例程式碼C#類別庫是 WebJobs SDK 程式碼，例如，但您不需要`FunctionName`WebJobs SDK 專案中的屬性。
> * 只能在函式，例如 HTTP (Webhook) 」 與 「 事件格線 （這以 HTTP 為基礎） 中支援某些繫結類型。
>
> 如需詳細資訊，請參閱[比較 WebJobs SDK 和 Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs) (英文)。

## <a name="webjobs-host"></a>WebJobs 主機

此主機是函式的執行階段容器。  它會接聽觸發程序並呼叫函式。 在第 3 版。*x*，主應用程式的實作`IHost`。 在第 2 版。*x*，您使用`JobHost`物件。 您可以在程式碼中建立主機執行個體，並撰寫程式碼以自訂其行為。

這是直接使用 WebJobs SDK 和 Azure Functions 透過間接使用的主要差異。 在 Azure Functions 中，服務控制主機，並無法撰寫程式碼來自訂主應用程式。 Azure Functions 可讓您自訂主應用程式行為，透過 host.json 檔案中的設定。 這些設定字串，不是程式碼，而且這會限制您可以執行的自訂項目類型。

### <a name="host-connection-strings"></a>主機連接字串

當您在 Azure 中執行時，WebJobs SDK 會尋找 Azure 儲存體和 Azure 服務匯流排連接字串在 local.settings.json 檔案，當您在本機執行時，或在 WebJob 環境中。 根據預設，儲存體連接字串設定名為`AzureWebJobsStorage`需要。  

第 2 版。*x*的 sdk 可讓您使用這些連接字串的名稱，或將它們儲存到其他位置。 您可以在程式碼中使用設定名稱[ `JobHostConfiguration` ]，如下所示：

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

因為第 3 版。*x*會使用預設的.NET Core 設定 Api，沒有任何 API，若要變更連接字串名稱。

### <a name="host-development-settings"></a>主機開發設定

您可以在開發模式中執行主機，讓本機開發更有效率。 以下是一些您在開發模式中執行時，會變更的設定：

| 屬性 | 開發設定 |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` 將記錄輸出最大化。 |
| `Queues.MaxPollingInterval`  | 最低值可確保立即觸發佇列方法。  |
| `Singleton.ListenerLockPeriod` | 15 秒在快速反覆式開發法中有幫助。 |

啟用開發模式的程序取決於 SDK 版本。 

#### <a name="version-3x"></a>第 3 版。*x*

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

#### <a name="version-2x"></a>第 2 版。*x*

`JobHostConfiguration` 類別具有可啟用開發模式的 `UseDevelopmentSettings` 方法。  下列範例示範如何使用開發設定。 若要讓`config.IsDevelopment`傳回`true`當它在本機執行時，設定本機環境變數，名為`AzureWebJobsEnv`的值`Development`。

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

### <a name="jobhost-servicepointmanager-settings"></a>管理並行連線 (第 2 版。*x*)

在第 3 版。*x*，連接限制預設為無限的連線。 如果基於某些原因，您需要變更這項限制，您可以使用[ `MaxConnectionsPerServer` ](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver)屬性[ `WinHttpHandler` ](/dotnet/api/system.net.http.winhttphandler)類別。

在第 2 版。*x*，使用控制至主機的並行連線數目[ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API。 在 2。*x*，您應該開始您的 WebJobs 主機之前會增加此值從預設值是 2。

要從函式所使用的所有外寄 HTTP 要求`HttpClient`流經`ServicePointManager`。 連線到設定的值之後`DefaultConnectionLimit`，`ServicePointManager`再傳送啟動要求排入佇列。 假設您的 `DefaultConnectionLimit` 設為 2，且您的程式碼會發出 1,000 個 HTTP 要求。 一開始，只允許兩個要求傳至作業系統。 其他的 998 個要求會排入佇列，直到有足夠的空間給它們。 這表示您`HttpClient`可能會逾時時間，因為它似乎有提出要求，但此要求永遠不會由作業系統傳送到目的地伺服器。 因此，您可能會看到一個似乎不合理的行為：您的本機 `HttpClient` 花費 10 秒完成要求，但您的服務正以 200 毫秒的時間傳回每個要求。 

ASP.NET 應用程式的預設值是`Int32.MaxValue`，而且可能很適合在基本或更高的 App Service 方案中執行的 WebJobs。 WebJobs 通常需要的 Always On 設定值，而且，只有支援基本和更高版本 App Service 方案。

如果 WebJob 正在免費或共用的 App Service 方案中執行，則您的應用程式會受到 App Service 沙箱的限制，沙箱目前的[連線限制為 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)。 在未繫結的連接限制`ServicePointManager`，就越有可能會達到沙箱連線臨界值，以及站台將會關閉。 在此狀況下，將 `DefaultConnectionLimit` 設為更低的值，如 50 或 100，可防止這種狀況發生，同時仍允許有足夠的輸送量。

此設定必須先設定，才能發出任何 HTTP 要求。 基於這個理由，WebJobs 主應用程式不應該調整設定自動。 可能有主機啟動時，這可能導致非預期的行為之前發生的 HTTP 要求。 最好的方法是，若要設定值，立即在您`Main`方法之前初始化`JobHost`，如下所示：

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

函式必須是公用的方法，而且只能有一個觸發程序或[ `NoAutomaticTrigger` ](#manual-triggers)屬性。

### <a name="automatic-triggers"></a>自動觸發程序

自動觸發程序會呼叫函式以回應事件。 請考慮這個範例由 Azure 佇列儲存體中新增的訊息所觸發的函式。 它會回應由從 Azure Blob 儲存體讀取 blob:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

`QueueTrigger`屬性會告知執行階段呼叫的函式，每當佇列訊息會出現在`myqueue-items`佇列。 `Blob`屬性會告知執行階段讀取 blob，以在使用佇列訊息*範例工作項目*容器。 佇列訊息時，內容傳遞至函式中`myQueueItem`參數是 blob 的名稱。


### <a name="manual-triggers"></a>手動觸發程序

若要手動觸發的函式，請使用`NoAutomaticTrigger`屬性，如下所示：

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

手動觸發的函式的程序取決於 SDK 版本。

#### <a name="version-3x"></a>第 3 版。*x*

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

#### <a name="version-2x"></a>第 2 版。*x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>輸入與輸出繫結

輸入繫結會提供宣告式方法，讓 Azure 或協力廠商服務中的資料可供您的程式碼使用。 輸出繫結會提供更新資料的方法。 [開始](webjobs-sdk-get-started.md)文章將說明每個範例。

您可以使用輸出繫結的方法的傳回值，藉由將屬性套用至方法的傳回值。 請參閱中的範例[使用 Azure 函式傳回值](../azure-functions/functions-bindings-return-value.md)。

## <a name="binding-types"></a>繫結型別

安裝和管理繫結類型的程序取決於您使用第 3 版。*x*或 第 2 版。*x*的 sdk。 您可以找到要安裝特定的繫結類型 「 套件 」 一節中的該繫結類型的 Azure Functions 的套件[參考文章](#binding-reference-information)。 檔案觸發程序和繫結 （適用於本機檔案系統），這不 Azure Functions 所支援的例外狀況。

#### <a name="version-3x"></a>第 3 版。*x*

在第 3 版。*x*，儲存體繫結會包含在`Microsoft.Azure.WebJobs.Extensions.Storage`封裝。 呼叫`AddAzureStorage`擴充方法`ConfigureWebJobs`方法，如下所示：

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

若要使用其他觸發程序與繫結類型，請安裝包含這些項目的 NuGet 套件，並呼叫在擴充功能中實作的 `Add<binding>` 擴充方法。 例如，如果您想要使用 Azure Cosmos DB 繫結時，安裝`Microsoft.Azure.WebJobs.Extensions.CosmosDB`並呼叫`AddCosmosDB`，如下所示：

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

#### <a name="version-2x"></a>第 2 版。*x*

第 2 版中包含這些觸發程序和繫結的類型。*x*的`Microsoft.Azure.WebJobs`封裝：

* Blob 儲存體
* 佇列儲存體
* 表格儲存體

若要使用其他觸發程序與繫結型別，請安裝包含它們的 NuGet 封裝，並在 `JobHostConfiguration` 物件上呼叫 `Use<binding>` 方法。 例如，如果您想要使用計時器觸發程序，安裝`Microsoft.Azure.WebJobs.Extensions`並呼叫`UseTimers`在`Main`方法，如下所示：

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

若要繫結的程序[ `ExecutionContext` ]取決於您的 SDK 版本。

#### <a name="version-3x"></a>第 3 版。*x*

呼叫`AddExecutionContextBinding`擴充方法`ConfigureWebJobs`方法，如下所示：

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

#### <a name="version-2x"></a>第 2 版。*x*

先前所提到的 `Microsoft.Azure.WebJobs.Extensions` 封裝方法也提供特殊的繫結型別，您可以呼叫 `UseCore` 方法來註冊該繫結型別。 這個繫結可讓您定義[ `ExecutionContext` ]函式簽章，而這樣會啟用中的參數：

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

您可以設定某些觸發程序和繫結的行為。 設定它們的程序取決於 SDK 版本。

* **第 3 版。*x*:** 設定組態時`Add<Binding>`方法呼叫`ConfigureWebJobs`。
* **第 2 版。*x*:** 在您傳入的組態物件中設定屬性的設定組態`JobHost`。

這些繫結特有的設定相當於設定[host.json 專案檔](../azure-functions/functions-host-json.md)在 Azure Functions 中。

您可以設定下列繫結：

* [Azure CosmosDB 觸發程序](#azure-cosmosdb-trigger-configuration-version-3x)
* [事件中樞觸發程序](#event-hubs-trigger-configuration-version-3x)
* 佇列儲存體觸發程序
* [SendGrid 繫結](#sendgrid-binding-configuration-version-3x)
* [服務匯流排觸發程序](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Azure CosmosDB 觸發程序組態 (第 3 版。*x*)

此範例示範如何設定 Azure Cosmos DB 觸發程序：

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

如需詳細資訊，請參閱 < [Azure CosmosDB 繫結](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings)文章。

### <a name="event-hubs-trigger-configuration-version-3x"></a>事件中樞觸發程序組態 (第 3 版。*x*)

此範例示範如何設定事件中樞觸發程序：

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

如需詳細資訊，請參閱 <<c0> [ 事件中樞繫結](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings)文章。

### <a name="queue-storage-trigger-configuration"></a>佇列儲存體觸發程序組態

這些範例示範如何設定佇列儲存體觸發程序：

#### <a name="version-3x"></a>第 3 版。*x*

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

如需詳細資訊，請參閱 <<c0> [ 排入佇列儲存體繫結](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings)文章。

#### <a name="version-2x"></a>第 2 版。*x*

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

如需詳細資訊，請參閱 < [host.json v1.x 參考](../azure-functions/functions-host-json-v1.md#queues)。

### <a name="sendgrid-binding-configuration-version-3x"></a>SendGrid 繫結組態 (第 3 版。*x*)

此範例示範如何設定 SendGrid 輸出繫結：

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

如需詳細資訊，請參閱 < [SendGrid 繫結](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings)文章。

### <a name="service-bus-trigger-configuration-version-3x"></a>服務匯流排觸發程序組態 (第 3 版。*x*)

此範例示範如何設定服務匯流排觸發程序：

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

如需詳細資訊，請參閱 <<c0> [ 服務匯流排繫結](../azure-functions/functions-bindings-service-bus.md#hostjson-settings)文章。

### <a name="configuration-for-other-bindings"></a>其他繫結的設定

某些觸發程序和繫結的型別定義自己的自訂組態類型。 例如，檔案觸發程序可讓您指定根路徑，若要監視，如下列範例所示：

#### <a name="version-3x"></a>第 3 版。*x*

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

#### <a name="version-2x"></a>第 2 版。*x*

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

有時您想要指定在程式碼，而不是硬式編碼的佇列名稱、 blob 名稱或容器或資料表名稱。 例如，您可能想要在設定檔或環境變數中指定 `QueueTrigger` 屬性的佇列名稱。

您可以執行，藉由傳遞`NameResolver`物件中`JobHostConfiguration`物件。 在觸發程序或繫結屬性建構函式中包含預留位置，然後 `NameResolver` 程式碼會提供實際要用以取代這些預留位置的值。 您識別與百分比 （%） 住的預留位置符號，如下所示：

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

此程式碼可讓您在測試環境中使用名為 `logqueuetest` 的佇列，以及在生產環境中使用名為 `logqueueprod` 的佇列。 您不必使用硬式編碼的佇列名稱，而是在 `appSettings` 集合中指定項目的名稱。

沒有預設值`NameResolver`，才會生效，如果您未提供的自訂承載。 此預設值會從應用程式設定或環境變數取得值。

您`NameResolver`類別會取得佇列名稱，從`appSettings`，如下所示：

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>第 3 版。*x*

您可以使用相依性插入，以設定解析程式。 這些範例需要下列 `using` 陳述式：

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

#### <a name="version-2x"></a>第 2 版。*x*

傳遞您`NameResolver`類別中`JobHost`物件，如下所示：

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

如果您需要執行一些工作，在您的函式，才能使用的繫結屬性，例如`Queue`， `Blob`，或`Table`，您可以使用`IBinder`介面。

下列範例會使用輸入佇列訊息，並在輸出佇列中建立含有相同內容的新訊息。 输出队列名称由函数正文中的代码设置。

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

Azure Functions 文件提供有關每個繫結類型的參考資訊。 每個繫結參考文章中找到下列資訊。 （此範例根據儲存體佇列。）

* [封裝](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x)。 若要安裝 WebJobs SDK 專案中包含的繫結至所需的套件。
* [範例](../azure-functions/functions-bindings-storage-queue.md#trigger---example)。 程式碼範例。 C#類別的程式庫範例適用於 WebJobs SDK。 只要省略`FunctionName`屬性。
* [屬性](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes)。 若要使用的繫結類型屬性。
* [設定](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration)。 屬性的屬性和建構函式參數的說明。
* [使用量](../azure-functions/functions-bindings-storage-queue.md#trigger---usage)。 類型，您可以繫結和繫結的運作方式的詳細資訊。 例如：輪詢演算法、有害佇列處理。
  
繫結參考文章的清單，請參閱 「 支援繫結 」，在[觸發程序和繫結](../azure-functions/functions-triggers-bindings.md#supported-bindings)適用於 Azure Functions 的文章。 在該清單中，只能由 Azure Functions，WebJobs SDK 所不支援 HTTP、 Webhook 和 Event Grid 的繫結。

## <a name="disable-attribute"></a>Disable 屬性 

[ `Disable` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs)可以觸發屬性可讓您控制是否函式。 

在下列範例中，如果應用程式設定`Disable_TestJob`的值為`1`或`True`（不區分大小寫），此函式將不會執行。 在此情況下，執行階段會建立記錄訊息「*函式 'Functions.TestJob' 已停用*」。

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

當您變更應用程式設定值，在 Azure 入口網站中的時，挑選新的設定會重新啟動 WebJob。

此屬性可在參數、方法或類別等級宣告。 設定名稱也可以包含繫結運算式。

## <a name="timeout-attribute"></a>Timeout 屬性

[ `Timeout` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs)屬性使函式取消，如果它未在指定的時間內完成。 在下列範例中，函式會執行一天沒有逾時屬性。 逾時，會導致函式，15 秒之後被取消。

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

您可以套用在類別或方法層級的逾時屬性，而且您可以使用，以指定的全域逾時`JobHostConfiguration.FunctionTimeout`。 類別層級或是方法層級的逾時覆寫全域逾時。

## <a name="singleton-attribute"></a>Singleton 屬性

[ `Singleton` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs)屬性可確保該函式的只有一個執行個體執行時，即使有多個主 web 應用程式執行個體。 其做法是使用[分散式鎖定](#viewing-lease-blobs)。

在此範例中，單一執行個體的`ProcessImage`函式會執行一次：

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

您可以使用這些設定確保函式在單一執行個體上以單一項目執行。 若要確認函式中的單一執行個體正在執行 web 應用程式相應放大至多個執行個體時，將接聽程式層級的單一鎖定套用函式 (`[Singleton(Mode = SingletonMode.Listener)]`)。 JobHost 啟動時，會取得接聽程式鎖定。 如果三個擴充的執行個體全部同時啟動，則其中只有一個執行個體會取得鎖定，且只有一個接聽程式會啟動。

### <a name="scope-values"></a>範圍值

您可以指定*範圍運算式/值*上單一值。 運算式值可確保在特定範圍的函式的所有執行將會都序列化。 實作更細微的鎖定，這種方式可以允許進行某種程度的平行處理原則，您的函式時序列化其他引動過程，即會依照您的需求。 例如，下列程式碼中，scope 運算式繫結至`Region`內送訊息的值。 當佇列在東部、 東和西分別包含三個區域中的訊息時，必須以序列方式執行東部區域的訊息時，在西部執行以平行方式與在東部區域的訊息。

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

預設範圍的鎖定是`SingletonScope.Function`，這表示鎖定範圍 （blob 租用路徑） 會將它繫結，在完整的函式名稱。 若要鎖定函式，指定`SingletonScope.Host`，並使用您不想要同時執行的所有函式是相同的範圍識別碼名稱。 在下列範例中，一次只有 `AddItem` 或 `RemoveItem` 的一個執行個體會執行：

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

WebJobs SDK 使用 [Azure 二進位大型物件租用](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs)以實作分散式鎖定。 使用單一租用 blob 位於`azure-webjobs-host`容器中的`AzureWebJobsStorage`路徑 「 鎖定 」 下的儲存體帳戶。 例如，稍早所示第一個 `ProcessImage` 範例的租用二進位大型物件路徑可以是 `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`。 所有路徑皆包含 JobHost 識別碼，在此個案中為 061851c758f04938a4426aa9ab3869c0。

## <a name="async-functions"></a>异步函数

如需如何以程式碼非同步函式的資訊，請參閱[Azure Functions 文件](../azure-functions/functions-dotnet-class-library.md#async)。

## <a name="cancellation-tokens"></a>取消權杖

如需如何處理取消權杖的相關資訊，請參閱[取消權杖與順利關機](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens) (英文) 上的 Azure Functions 文件。

## <a name="multiple-instances"></a>多個執行個體

如果您的 Web 應用程式在多個執行個體上執行，則會有一個連續的 WebJob 在每個執行個體上執行，以接聽觸發程序和呼叫函式。 各種觸發程序繫結的設計是為了有效地共用在執行個體之間合作的工作，如此便能擴充至更多執行個體，讓您能處理更多負載。

佇列和 blob 觸發程序會自動防止函式處理佇列訊息或 blob 超過一次;函式不一定要具有等冪性。

計時器觸發程序會自動確保計時器只有一個執行個體會執行，因此在指定的排程時間不會有多個函式正在執行中。

如果您想要確保只有一個執行個體的函式會執行，即使有多個主 web 應用程式執行個體，您可以使用[ `Singleton` ](#singleton-attribute)屬性。

## <a name="filters"></a>篩選器

函式篩選條件 (預覽) 讓您能夠使用自己的邏輯自訂 WebJobs 執行管線。 篩選器如下[ASP.NET Core 篩選](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters)。 您可以將它們實作為會套用至您的函式或類別的宣告式屬性。 如需詳細資訊，請參閱[函式篩選條件](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters) (英文)。

## <a name="logging-and-monitoring"></a>記錄和監視

我們建議您開發適用於 ASP.NET 的記錄架構。 [開始](webjobs-sdk-get-started.md)文章說明如何使用它。 

### <a name="log-filtering"></a>記錄篩選

`ILogger` 執行個體建立的每個記錄皆有相關聯的 `Category` 和 `Level`。 [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) 列舉型別，且整數代碼表示相對的重要性：

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

#### <a name="version-3x"></a>第 3 版。*x*

第 3 版。*x* SDK 的依賴內建於.NET Core 的篩選。 `LogCategories` 類別可讓您為特定的函式、觸發程序或使用者定義類別。 它也會定義篩選特定的主機狀態，例如`Startup`和`Results`。 這可讓您微調記錄輸出。 如果在已定義的類別內找不到相符項目，則在決定是否篩選訊息時，篩選條件會回復為 `Default` 值。

`LogCategories` 需要下列 using 陳述式：

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

下列範例會建構篩選器，根據預設，篩選所有的記錄檔，位於`Warning`層級。 `Function`並`results`類別 (相當於`Host.Results`第 2 版。*x*) 會在篩選`Error`層級。 篩選條件會將目前的類別與 `LogCategories` 執行個體中所有已註冊的層級比較，並選擇最長的相符項目。 這表示`Debug`層級註冊`Host.Triggers`比對`Host.Triggers.Queue`或`Host.Triggers.Blob`。 這可讓您控制更廣泛的分類，但無需一一新增。

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

#### <a name="version-2x"></a>第 2 版。*x*

在第 2 版。*x*在您使用的 sdk，`LogCategoryFilter`類別來控制篩選。 `LogCategoryFilter`已經`Default`屬性的初始值`Information`，表示在任何訊息`Information`， `Warning`， `Error`，或`Critical`記錄層級，但任何訊息在`Debug`或`Trace`會立即篩選層級。

如同`LogCategories`中，第 3 版。*x*，則`CategoryLevels`屬性可讓您指定特定類別的記錄層級，因此您可以微調記錄輸出。 如果在 `CategoryLevels` 目錄內找不到相符項目，則當決定是否篩選訊息時，篩選條件會回復到 `Default` 值。

下列範例建構一個預設會篩選 `Warning` 等級所有記錄的篩選條件。 `Function`並`Host.Results`類別會在篩選`Error`層級。 The `LogCategoryFilter` 會將目前分類與所有已註冊的 `CategoryLevels` 比較，並選擇最長的相符項目。 因此`Debug`層級註冊`Host.Triggers`會比對`Host.Triggers.Queue`或`Host.Triggers.Blob`。 這可讓您控制更廣泛的分類，但無需一一新增。

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

#### <a name="version-3x"></a>第 3 版。*x*

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

#### <a name="version-2x"></a>第 2 版。*x*

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

`SamplingPercentageEstimatorSettings`物件會設定[調適型取樣](https://docs.microsoft.com/azure/application-insights/app-insights-sampling)。 這表示，在某些高容量的情況，Application Insights 會傳送遙測資料的子集選取伺服器。

建立遙測資料處理站之後，您會將它傳入至 Application Insights 記錄提供者：

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> 後續步驟

本文提供的程式碼片段示範如何處理使用 WebJobs SDK 的常見案例。 如需完整範例，請參閱 [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk-samples) (英文)。

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
