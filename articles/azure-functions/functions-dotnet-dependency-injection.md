---
title: 在 .NET Azure Functions 中使用相依性插入
description: 瞭解如何在 .NET 函式中使用相依性插入來註冊和使用服務
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure Functions, 函式, 無伺服器架構
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 09bcce6daf519c7d5e99c7c120064f5c8bb92475
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996882"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>在 .NET Azure Functions 中使用相依性插入

Azure Functions 支援相依性插入 (DI) 軟體設計模式, 這項技術可在類別及其相依性之間達成[控制反轉 (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 。

- Azure Functions 中的相依性插入是以 .NET Core 相依性插入功能為基礎。 建議您熟悉[.Net Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)相依性插入。 不過，在覆寫相依性方面，以及如何使用取用方案 Azure Functions 讀取設定值的方式有一些差異。

- 相依性插入的支援是以 Azure Functions 2.x 開始。

## <a name="prerequisites"></a>必要條件

您必須先安裝下列 NuGet 套件, 才可以使用相依性插入:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- 1\.0.28 或更新版本的[函數套件](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

## <a name="register-services"></a>註冊伺服器

若要註冊服務，請建立方法來設定並將元件加入`IFunctionsHostBuilder`至實例。  Azure Functions 主機會建立的實例, `IFunctionsHostBuilder`並將它直接傳遞至您的方法。

若要註冊方法, 請加入`FunctionsStartup`元件屬性, 以指定啟動期間所使用的型別名稱。

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>需要注意的事項

在執行時間處理啟動類別之前和之後執行的一系列註冊步驟。 因此，請記住下列專案：

- *Startup 類別僅適用于設定和註冊。* 避免在啟動過程中使用在啟動時註冊的服務。 例如，請勿嘗試在啟動期間註冊的記錄器中記錄訊息。 註冊程式的這個點太早無法使用您的服務。 `Configure`執行方法之後，函式執行時間會繼續註冊其他相依性，這可能會影響服務的運作方式。

- 相依性*插入容器只會保存明確註冊的類型*。 唯一可作為得以插入類型的服務是在`Configure`方法中設定的。 因此，在安裝期間或作為得以插入類型`BindingContext`時`ExecutionContext` ，不會提供函式特定類型（例如和）。

## <a name="use-injected-dependencies"></a>使用插入的相依性

函式插入是用來讓您的相依性可在函式中使用。 使用「處理常式」插入時，您不需要使用靜態類別。

下列範例示範如何將`IMyService`和`HttpClient`相依性插入 HTTP 觸發的函式中。 這個範例會使用在啟動`HttpClient`時註冊所需的[Microsoft Extensions. Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/)套件。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## <a name="service-lifetimes"></a>服務存留期

Azure Functions 應用程式提供與 ASP.NET 相依性[插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)相同的服務存留期。 針對函式應用程式，不同的服務存留期的行為如下所示：

- **暫時性**：系統會在每個服務要求時建立暫時性服務。
- 限**域**：限定範圍的服務存留期會符合函式執行存留期。 限域服務會在每次執行時建立一次。 稍後在執行期間對該服務提出的要求會重複使用現有的服務實例。
- **Singleton**：單一服務存留期會符合主機存留期，並會在該實例上跨函數執行重複使用。 針對連接和用戶端（例如`SqlConnection`或`HttpClient`實例），建議使用單一存留期服務。

在 GitHub 上查看或下載[不同服務存留期的範例](https://aka.ms/functions/di-sample)。

## <a name="logging-services"></a>記錄服務

如果您需要自己的記錄提供者，請將自訂類型`ILoggerProvider`註冊為實例。 Application Insights 由 Azure Functions 自動新增。

> [!WARNING]
> - 請不要加入`AddApplicationInsightsTelemetry()`服務集合, 因為它會註冊與環境所提供之服務衝突的服務。
> - 如果您使用內建`TelemetryConfiguration`的`TelemetryClient` Application Insights 功能，請勿註冊您自己的或。

## <a name="function-app-provided-services"></a>函數應用程式提供的服務

函數主機會註冊許多服務。 下列服務可安全地做為應用程式中的相依性:

|服務類型|存留期|描述|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|實體|執行時間設定|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|實體|負責提供主控制項實例的識別碼|

如果您想要取得相依性的其他服務, 請[在 GitHub 上建立問題並加以提議](https://github.com/azure/azure-functions-host)。

### <a name="overriding-host-services"></a>覆寫主機服務

目前不支援覆寫主機所提供的服務。  如果您想要覆寫服務, 請[在 GitHub 上建立問題並加以提議](https://github.com/azure/azure-functions-host)。

## <a name="working-with-options-and-settings"></a>使用選項和設定

在 [[應用程式設定](./functions-how-to-use-azure-function-app-settings.md#settings)] 中定義的`IConfiguration`值可在實例中使用，讓您可以讀取啟動類別中的應用程式設定值。

您可以將實例中的`IConfiguration`值解壓縮至自訂類型。 將應用程式設定值複製到自訂類型，可以讓這些值得以插入，輕鬆地測試您的服務。 請考慮下列類別，其中包含名稱與應用程式設定一致的屬性。

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

從`Startup.Configure`方法內部，您可以使用下列程式碼， `IConfiguration`將實例中的值解壓縮至您的自訂類型：

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.Bind(settings);
                                           });
```

呼叫`Bind`會將具有相符屬性名稱的值從設定複製到自訂實例。 Options 實例現在可用於 IoC 容器中，以插入函式中。

Options 物件會插入至函式中，做為泛型`IOptions`介面的實例。 `Value`使用屬性來存取您的設定中找到的值。

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _service = service;
        _settings = options.Value;
    }
}
```

如需有關使用選項的詳細資訊，請參閱[ASP.NET Core 中的選項模式](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

- [如何監視您的函數應用程式](functions-monitoring.md)
- [函數的最佳做法](functions-best-practices.md)
