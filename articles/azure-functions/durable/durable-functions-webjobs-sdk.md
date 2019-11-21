---
title: How to run Durable Functions as WebJobs - Azure
description: 了解如何使用 WebJobs SDK，對 Durable Functions 進行編碼並設定為在 WebJobs 中執行。
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: d8dd0c86fbc520d0bd3ef6034891bd9871774b4a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232731"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>How to run Durable Functions as WebJobs

By default, Durable Functions uses the Azure Functions runtime to host orchestrations. However, there may be certain scenarios where you need more control over the code that listens for events. This article shows you how to implement your orchestration using the WebJobs SDK. To see a more detailed comparison between Functions and WebJobs, see [Compare Functions and WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Azure Functions](../functions-overview.md) 和 [Durable Functions](durable-functions-overview.md) 擴充功能是以 [WebJobs SDK](../../app-service/webjobs-sdk-how-to.md) 為基礎所建置的。 The job host in the WebJobs SDK is the runtime in Azure Functions. If you need to control behavior in ways not possible in Azure Functions, you can develop and run Durable Functions by using the WebJobs SDK yourself.

In version 3.x of the WebJobs SDK, the host is an implementation of `IHost`, and in version 2.x you use the `JobHost` object.

The chaining Durable Functions sample is available in a WebJobs SDK 2.x version: download or clone the [Durable Functions repository](https://github.com/azure/azure-functions-durable-extension/), and go to the *samples\\webjobssdk\\chaining* folder.

## <a name="prerequisites"></a>必要條件

本文假設您已熟悉 WebJobs SDK、Azure Functions 的C# 類別庫開發和 Durable Functions 等項目的基本概念。 如果您需要這些主題的簡介，請參閱下列資源：

* [開始使用 WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [使用 Visual Studio 建立第一個函數](../functions-create-your-first-function-visual-studio.md)
* [Durable Functions](durable-functions-sequence.md)

若要完成本文中的步驟：

* [Install Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) with the **Azure development** workload.

  If you already have Visual Studio, but don't have that workload, add the workload by selecting **Tools** > **Get Tools and Features**.

  (您可以改為使用 [Visual Studio Code](https://code.visualstudio.com/)，但某些指示是 Visual Studio 特有的。)

* 安裝並執行 [Azure 儲存體模擬器](../../storage/common/storage-use-emulator.md) 5.2 版或更新版本。 替代方法是使用 Azure 儲存體連接字串來更新 App.config 檔案。

## <a name="webjobs-sdk-versions"></a>WebJobs SDK 版本

本文說明如何開發 WebJobs SDK 2.x 專案 (相當於 Azure Functions 1.x 版)。 如需 3.x 版的資訊，請參閱本文稍後的 [WebJobs SDK 3.x](#webjobs-sdk-3x)。

## <a name="create-a-console-app"></a>建立主控台應用程式

To run Durable Functions as WebJobs, you must first create a console app. WebJobs SDK 專案只是安裝了適當 NuGet 套件的主控台應用程式專案。

In the Visual Studio **New Project** dialog box, select **Windows Classic Desktop** > **Console App (.NET Framework)** . 在專案檔中，`TargetFrameworkVersion` 應該是 `v4.6.1`。

Visual Studio also has a WebJob project template, which you can use by selecting **Cloud** > **Azure WebJob (.NET Framework)** . 此範本會安裝許多套件，但您可能不需要其中的某些套件。

## <a name="install-nuget-packages"></a>安裝 NuGet 套件

您需要 NuGet 套件中的 WebJobs SDK、核心繫結、記錄架構和長期工作擴充功能。 Here are **Package Manager Console** commands for those packages, with the latest stable version numbers as of the date this article was written:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

您也需要記錄提供者。 The following commands install the Azure Application Insights provider and the `ConfigurationManager`. `ConfigurationManager` 可讓您從應用程式設定中取得 Application Insights 檢測金鑰。

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

下列命令會安裝主控台提供者：

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost 程式碼

Having created the console app and installed the NuGet packages you need, you're ready to use Durable Functions. You do so by using JobHost code.

若要使用 Durable Functions 擴充功能，請在 `Main` 方法中的 `JobHostConfiguration` 物件上呼叫 `UseDurableTask`：

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

如需可在 `DurableTaskExtension` 物件中設定的屬性清單，請參閱 [host.json](../functions-host-json.md#durabletask)。

`Main` 方法也是用來設定記錄提供者的位置。 The following example configures the console and Application Insights providers.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Functions

Durable Functions in the context of WebJobs differs somewhat from Durable Functions in the context of Azure Functions. It's important to be aware of the differences as you write your code.

WebJobs SDK 不支援下列 Azure Functions 功能：

* [FunctionName 屬性](#functionname-attribute)
* [HTTP 觸發程序](#http-trigger)
* [Durable Functions HTTP 管理 API](#http-management-api)

### <a name="functionname-attribute"></a>FunctionName 屬性

在 WebJobs SDK 專案中，函式的方法名稱即為函式名稱。 `FunctionName` 屬性只會在 Azure Functions 中使用。

### <a name="http-trigger"></a>HTTP 觸發程序

WebJobs SDK 沒有 HTTP 觸發程序。 範例專案的協調流程用戶端會使用計時器觸發程序：

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>HTTP 管理 API

由於沒有 HTTP 觸發程序，WebJobs SDK 沒有 [HTTP 管理 API](durable-functions-http-api.md)。

In a WebJobs SDK project, you can call methods on the orchestration client object, instead of by sending HTTP requests. 下列方法對應至三個可使用 HTTP 管理 API 來進行的工作：

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

The orchestration client function in the sample project starts the orchestrator function, and then goes into a loop that calls `GetStatusAsync` every 2 seconds:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>執行範例

You've got Durable Functions set up to run as a WebJob, and you now have an understanding of how this will differ from running Durable Functions as standalone Azure Functions. At this point, seeing it work in a sample might be helpful.

本節概述如何執行[範例專案](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining)。 如需會說明如何在本機執行 WebJobs SDK 專案並將其部署至 Azure WebJob 的詳細指示，請參閱[開始使用 WebJobs SDK](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob)。

### <a name="run-locally"></a>在本機執行

1. 確定儲存體模擬器正在執行 (請參閱[先決條件](#prerequisites))。

1. If you want to see logs in Application Insights when you run the project locally:

    a. Create an Application Insights resource, and use the **General** app type for it.

    b.這是另一個 C# 主控台應用程式。 將檢測金鑰儲存在 App.config 檔案。

1. 執行專案。

### <a name="run-in-azure"></a>在 Azure 中執行

1. 建立 Web 應用程式和儲存體帳戶。

1. In the web app, save the storage connection string in an app setting named `AzureWebJobsStorage`.

1. Create an Application Insights resource, and use the **General** app type for it.

1. Save the instrumentation key in an app setting named `APPINSIGHTS_INSTRUMENTATIONKEY`.

1. 部署為 WebJob。

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

This article explains how to develop a WebJobs SDK 2.x project. If you're developing a [WebJobs SDK 3.x](../../app-service/webjobs-sdk-get-started.md) project, this section helps you understand the differences.

The main change introduced is the use of .NET Core instead of .NET Framework. To create a WebJobs SDK 3.x project, the instructions are the same, with these exceptions:

1. 建立 .NET Core 主控台應用程式。 In the Visual Studio **New Project** dialog box, select  **.NET Core** > **Console App (.NET Core)** . 專案檔會將 `TargetFramework` 指定為 `netcoreapp2.x`。

1. Choose the release version WebJobs SDK 3.x of the following packages:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Set the storage connection string and the Application Insights instrumentation key in an *appsettings.json* file, by using the .NET Core configuration framework. 以下是範例：

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Change the `Main` method code to do this. 以下是範例：

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>後續步驟

若要深入了解 WebJobs SDK，請參閱[如何使用 WebJobs SDK](../../app-service/webjobs-sdk-how-to.md)。
