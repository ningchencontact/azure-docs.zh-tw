---
title: 在.NET Azure Functions 中使用相依性插入
description: 了解如何使用相依性插入註冊和使用服務，在.NET 函式
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure Functions, 函式, 無伺服器架構
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2019
ms.author: jehollan
ms.openlocfilehash: 2044718d2ec7a7acc58e1e7ba9ba04ec5caf16b3
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408455"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>在.NET Azure Functions 中使用相依性插入

Azure Functions 支援相依性插入 (DI) 軟體設計模式，也就是達到目標的技巧[控制反轉 (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)之間類別和其相依性。

Azure Functions 建置在 ASP.NET Core 相依性插入功能之上。  您應該了解服務、 存留期及設計模式[ASP.NET Core 的相依性插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)之前在函式中使用它們。

## <a name="installing-dependency-injection-packages"></a>封裝安裝相依性插入

若要使用的相依性插入功能，您必須包含會公開這些 Api 的 NuGet 套件。

```powershell
Install-Package Microsoft.Azure.Functions.Extensions
```

## <a name="registering-services"></a>註冊服務

若要註冊的服務，您可以建立的設定方法，並將元件加入`IFunctionsHostBuilder`執行個體。  Azure Functions 主機建立`IFunctionsHostBuilder`並將它直接傳入您設定的方法。

若要註冊您設定方法，您必須新增組件屬性指定的類型，您設定方法使用`FunctionsStartup`屬性。

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

## <a name="service-lifetimes"></a>服務生命週期

Azure Functions 應用程式提供做為相同的服務生命週期[ASP.NET 相依性插入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)、 暫時性、 已設定領域，和單一。

在函式應用程式中，已設定領域的服務的存留期會比對函式執行存留期。 每次執行一次建立已設定領域的服務。  之後的要求，該服務在執行期間重複使用該執行個體。  單一服務的存留期會比對的主應用程式存留期，並重複使用該執行個體上的函式執行。

單一存留期服務建議來連線和用戶端，例如`SqlConnection`， `CloudBlobClient`，或`HttpClient`。

檢視或下載[範例的不同服務存留期](https://aka.ms/functions/di-sample)。

## <a name="logging-services"></a>記錄服務

如果您需要自己的記錄提供者時，建議的方式是註冊`ILoggerProvider`。  Application Insights 的函式 Application Insights 會自動為您新增。  

> [!WARNING]
> 不要新增`AddApplicationInsightsTelemetry()`服務集合，它將會發生衝突的服務中註冊該做什麼是環境所提供。 
 
## <a name="function-app-provided-services"></a>函式應用程式提供服務

函式的主應用程式會向許多服務。  以下是可安全依存於服務。  若要註冊或相依於不支援其他主機服務。  如果有其他服務，您想要採取相依性，請[建立 GitHub 上的問題，並討論](https://github.com/azure/azure-functions-host)。

|服務類型|存留期|說明|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|單一|執行階段組態|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|單一|負責提供的主控件執行個體識別碼|

### <a name="overriding-host-services"></a>覆寫主機服務

目前不支援覆寫主機所提供的服務。  如果您想要覆寫的服務，請[建立 GitHub 上的問題，並討論](https://github.com/azure/azure-functions-host)。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

* [如何監視您的函式應用程式](functions-monitoring.md)
* [Functions 的最佳作法](functions-best-practices.md)