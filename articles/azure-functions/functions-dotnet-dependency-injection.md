---
title: 在.NET Azure Functions 中使用相依性插入
description: 了解如何使用相依性插入註冊和使用服務，在.NET 函式
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure Functions, 函式, 無伺服器架構
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/28/2019
ms.author: jehollan, glenga, cshoe
ms.openlocfilehash: b1a6751f0d788c26af60b28eee994dc9b3877f00
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66693258"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>在.NET Azure Functions 中使用相依性插入

Azure Functions 支援相依性插入 (DI) 軟體設計模式，也就是一種技術來達成[控制反轉 (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)之間類別和其相依性。

Azure Functions 建置在 ASP.NET Core 相依性插入功能之上。 要了解服務、 存留期，與設計模式[ASP.NET Core 的相依性插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)之前先在 Azure Functions 中使用 DI 功能建議應用程式。

## <a name="prerequisites"></a>必要條件

您可以使用相依性插入之前，您必須安裝下列 NuGet 封裝：

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions 套件](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)1.0.28 版或更新版本

## <a name="register-services"></a>註冊伺服器

若要註冊的服務，您可以建立方法以設定，並將元件加入`IFunctionsHostBuilder`執行個體。  Azure Functions 主機建立的執行個體`IFunctionsHostBuilder`並將它直接傳入您的方法。

若要註冊的方法，新增`FunctionsStartup`在啟動期間，使用指定的型別名稱的組件屬性。

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
            builder.Services.AddSingleton((s) => {
                return new CosmosClient(Environment.GetEnvironmentVariable("COSMOSDB_CONNECTIONSTRING"));
            });
            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

## <a name="use-injected-dependencies"></a>使用插入的相依性

ASP.NET Core 會使用建構函式插入，將您的相依性提供給您的函式。 下列範例會示範如何`IMyService`和`HttpClient`HTTP 觸發的函式中插入相依性。

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();;
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

使用建構函式插入表示您應該使用靜態函式，如果您想要利用相依性插入。

## <a name="service-lifetimes"></a>服務生命週期

Azure Functions 應用程式提供做為相同的服務生命週期[ASP.NET 相依性插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)： 暫時性的範圍，與單一。

在函式應用程式中，已設定領域的服務的存留期會比對函式執行存留期。 每次執行一次建立已設定領域的服務。 之後的要求，該服務在執行期間重複使用現有的服務執行個體。 單一服務的存留期會比對的主應用程式存留期，並重複使用該執行個體上的函式執行。

單一存留期服務建議來連線和用戶端，例如`SqlConnection`， `CloudBlobClient`，或`HttpClient`執行個體。

檢視或下載[範例的不同服務存留期](https://aka.ms/functions/di-sample)GitHub 上。

## <a name="logging-services"></a>記錄服務

如果您需要自己的記錄提供者時，建議的方式是註冊`ILoggerProvider`執行個體。 Application Insights 會自動加入 Azure functions。

> [!WARNING]
> 不要新增`AddApplicationInsightsTelemetry()`服務集合，因為它暫存器服務衝突的環境所提供的服務。

## <a name="function-app-provided-services"></a>函式應用程式提供服務

函式主機註冊許多服務。 下列服務已安全地為您的應用程式中的相依性：

|服務類型|存留期|描述|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|單一|執行階段組態|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|單一|負責提供的主控件執行個體識別碼|

如果您想要依存於，其他服務[建立問題，並在 GitHub 上提出這些](https://github.com/azure/azure-functions-host)。

### <a name="overriding-host-services"></a>覆寫主機服務

目前不支援覆寫主機所提供的服務。  如果您想要覆寫時，服務[建立問題，並在 GitHub 上提出這些](https://github.com/azure/azure-functions-host)。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

- [如何監視您的函式應用程式](functions-monitoring.md)
- [Functions 的最佳作法](functions-best-practices.md)
