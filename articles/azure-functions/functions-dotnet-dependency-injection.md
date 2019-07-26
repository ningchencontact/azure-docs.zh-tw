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
ms.date: 05/28/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 1dbbb6e7bd88e08520225515c422529dc260e1b2
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377365"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>在 .NET Azure Functions 中使用相依性插入

Azure Functions 支援相依性插入 (DI) 軟體設計模式, 這項技術可在類別及其相依性之間達成[控制反轉 (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 。

Azure Functions 建置於 ASP.NET Core 相依性插入功能之上。 建議在使用 Azure Functions 應用程式中的 DI 功能之前, 留意 ASP.NET Core 相依性[插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)的服務、存留期和設計模式。

相依性插入的支援是以 Azure Functions 2.x 開始。

## <a name="prerequisites"></a>必要條件

您必須先安裝下列 NuGet 套件, 才可以使用相依性插入:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- 1\.0.28 或更新版本的[函數套件](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

- 選用：僅在啟動時註冊 HttpClient 時才需要[Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/)

## <a name="register-services"></a>註冊伺服器

若要註冊服務, 您可以建立方法來設定和新增元件至`IFunctionsHostBuilder`實例。  Azure Functions 主機會建立的實例, `IFunctionsHostBuilder`並將它直接傳遞至您的方法。

若要註冊方法, 請加入`FunctionsStartup`元件屬性, 以指定啟動期間所使用的型別名稱。 此外, 程式碼在 Nuget 上會參考[Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)的發行前版本。

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos;

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

ASP.NET Core 會使用函式插入功能, 讓您的相依性可供您的函式使用。 下列範例示範如何將`IMyService`和`HttpClient`相依性插入 HTTP 觸發的函式中。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

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

如果您想要利用相依性插入, 就不應該使用函式插入功能。

## <a name="service-lifetimes"></a>服務存留期

Azure Functions 應用程式提供與 ASP.NET 相依性[插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)相同的服務存留期: 暫時性、限定範圍和單一。

在函式應用程式中, 限定範圍的服務存留期會符合函式執行存留期。 限域服務會在每次執行時建立一次。 稍後在執行期間對該服務提出的要求會重複使用現有的服務實例。 單一服務存留期會符合主機存留期, 並會在該實例上跨函數執行重複使用。

針對連接和用戶端 (例如`SqlConnection` `CloudBlobClient`、或`HttpClient`實例), 建議使用單一存留期服務。

在 GitHub 上查看或下載[不同服務存留期的範例](https://aka.ms/functions/di-sample)。

## <a name="logging-services"></a>記錄服務

如果您需要自己的記錄提供者, 建議的方式是註冊`ILoggerProvider`實例。 Application Insights 由 Azure Functions 自動新增。

> [!WARNING]
> 請不要加入`AddApplicationInsightsTelemetry()`服務集合, 因為它會註冊與環境所提供之服務衝突的服務。

## <a name="function-app-provided-services"></a>函數應用程式提供的服務

函數主機會註冊許多服務。 下列服務可安全地做為應用程式中的相依性:

|服務類型|存留期|描述|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|實體|執行時間設定|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|實體|負責提供主控制項實例的識別碼|

如果您想要取得相依性的其他服務, 請[在 GitHub 上建立問題並加以提議](https://github.com/azure/azure-functions-host)。

### <a name="overriding-host-services"></a>覆寫主機服務

目前不支援覆寫主機所提供的服務。  如果您想要覆寫服務, 請[在 GitHub 上建立問題並加以提議](https://github.com/azure/azure-functions-host)。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

- [如何監視您的函數應用程式](functions-monitoring.md)
- [函數的最佳做法](functions-best-practices.md)
