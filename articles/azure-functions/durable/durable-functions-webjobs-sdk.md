---
title: 如何以 WebJobs 的形式运行 Durable Functions - Azure
description: 了解如何使用 WebJobs SDK，對 Durable Functions 進行編碼並設定為在 WebJobs 中執行。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: df12639aaafaf3df7ae2b755d635d4fba83d846e
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905082"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>如何以 WebJobs 的形式运行 Durable Functions

默认情况下，Durable Functions 使用 Azure Functions 运行时来托管业务流程。 但是，在某些情况下，可能需要对侦听事件的代码进行更多的控制。 本文介绍如何使用 WebJobs SDK 来实现业务流程。 若要查看更詳細的比較 Functions 和 WebJobs 之間，請參閱[比較 Functions 和 WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)。

[Azure Functions](../functions-overview.md) 和 [Durable Functions](durable-functions-overview.md) 擴充功能是以 [WebJobs SDK](../../app-service/webjobs-sdk-how-to.md) 為基礎所建置的。 WebJobs SDK 中的作业主机是 Azure Functions 中的运行时。 如果需要以 Azure Functions 中做不到的方式来控制行为，可以使用 WebJobs SDK 自行开发并运行 Durable Functions。

在 WebJobs SDK 的版本 3.x 中，主机是 `IHost` 的实现，而在版本 2.x 中，你使用 `JobHost` 对象。

WebJobs SDK 2.x 版中提供了有关链接 Durable Functions 的示例：下载或克隆 [Durable Functions 存储库](https://github.com/azure/azure-functions-durable-extension/)，然后转到 *samples\\webjobssdk\\chaining* 文件夹。

## <a name="prerequisites"></a>必要條件

本文假設您已熟悉 WebJobs SDK、Azure Functions 的C# 類別庫開發和 Durable Functions 等項目的基本概念。 如果您需要這些主題的簡介，請參閱下列資源：

* [開始使用 WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [使用 Visual Studio 建立第一個函式](../functions-create-your-first-function-visual-studio.md)
* [長期函式](durable-functions-sequence.md)

若要完成本文中的步驟：

* [安裝 Visual Studio 2017 15.6 版或更新版本](https://docs.microsoft.com/visualstudio/install/)與 **Azure 開發**工作負載。

  如果已安装 Visual Studio，但未配置该工作负荷，请选择“工具” > “获取工具和功能”以添加该工作负荷。

  (您可以改為使用 [Visual Studio Code](https://code.visualstudio.com/)，但某些指示是 Visual Studio 特有的。)

* 安裝並執行 [Azure 儲存體模擬器](../../storage/common/storage-use-emulator.md) 5.2 版或更新版本。 替代方法是使用 Azure 儲存體連接字串來更新 App.config 檔案。

## <a name="webjobs-sdk-versions"></a>WebJobs SDK 版本

本文說明如何開發 WebJobs SDK 2.x 專案 (相當於 Azure Functions 1.x 版)。 如需 3.x 版的資訊，請參閱本文稍後的 [WebJobs SDK 3.x](#webjobs-sdk-3x)。

## <a name="create-a-console-app"></a>建立主控台應用程式

若要将 Durable Functions 作为 WebJobs 运行，必须先创建控制台应用。 WebJobs SDK 專案只是安裝了適當 NuGet 套件的主控台應用程式專案。

在 Visual Studio 的“新建项目”对话框中，选择“Windows 经典桌面” > “控制台应用(.NET Framework)”。 在專案檔中，`TargetFrameworkVersion` 應該是 `v4.6.1`。

Visual Studio 还有一个 WebJob 项目模板，选择“云” > “Azure WebJob (.NET Framework)”即可使用此模板。 此範本會安裝許多套件，但您可能不需要其中的某些套件。

## <a name="install-nuget-packages"></a>安裝 NuGet 套件

您需要 NuGet 套件中的 WebJobs SDK、核心繫結、記錄架構和長期工作擴充功能。 下面是这些包的“包管理器控制台”命令，并提供了截至编写本文时的最新稳定版本号：

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

您也需要記錄提供者。 下列命令安裝 Azure Application Insights 提供者和`ConfigurationManager`。 `ConfigurationManager` 可讓您從應用程式設定中取得 Application Insights 檢測金鑰。

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

下列命令會安裝主控台提供者：

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost 程式碼

创建控制台应用并安装所需的 NuGet 包以后，即可使用 Durable Functions。 可以使用 JobHost 代码来这样做。

若要使用 Durable Functions 擴充功能，請在 `Main` 方法中的 `JobHostConfiguration` 物件上呼叫 `UseDurableTask`：

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

如需可在 `DurableTaskExtension` 物件中設定的屬性清單，請參閱 [host.json](../functions-host-json.md#durabletask)。

`Main` 方法也是用來設定記錄提供者的位置。 下列範例會設定主控台和 Application Insights 提供者。

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

WebJobs 上下文中的 Durable Functions 有点不同于 Azure Functions 上下文中的 Durable Functions。 在编写代码时，必须了解相关差异。

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

在 WebJobs SDK 项目中，可对业务流程客户端对象调用方法，而无需发送 HTTP 请求。 下列方法對應至三個可使用 HTTP 管理 API 來進行的工作：

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

示例项目中的业务流程客户端函数启动业务流程协调程序函数，然后进入每隔 2 秒调用 `GetStatusAsync` 的循环：

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

你已经将 Durable Functions 设置为以 WebJob 方式运行，并且已了解其与以独立 Azure Functions 形式运行 Durable Functions 时的区别。 此时可以在示例中查看其运行情况。

本節概述如何執行[範例專案](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining)。 如需會說明如何在本機執行 WebJobs SDK 專案並將其部署至 Azure WebJob 的詳細指示，請參閱[開始使用 WebJobs SDK](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob)。

### <a name="run-locally"></a>在本機執行

1. 確定儲存體模擬器正在執行 (請參閱[先決條件](#prerequisites))。

1. 如果您想要在本機執行專案時，請參閱 Application Insights 中的記錄檔：

    a. 建立 Application Insights 資源，並用**一般**為它的應用程式類型。

    b. 將檢測金鑰儲存在 App.config 檔案。

1. 執行專案。

### <a name="run-in-azure"></a>在 Azure 中執行

1. 建立 Web 應用程式和儲存體帳戶。

1. 在 web 應用程式中，將儲存在應用程式中設定具名的儲存體連接字串`AzureWebJobsStorage`。

1. 建立 Application Insights 資源，並用**一般**為它的應用程式類型。

1. 將檢測金鑰儲存在應用程式中設定具名`APPINSIGHTS_INSTRUMENTATIONKEY`。

1. 部署為 WebJob。

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

這篇文章說明如何開發 WebJobs SDK 2.x 專案。 如果您正在開發[WebJobs SDK 3.x](../../app-service/webjobs-sdk-get-started.md)專案中，本節將協助您了解這些差異。

導入的主要變更是使用.NET Core，而不是.NET Framework。 若要建立 WebJobs SDK 3.x 版專案，指示都相同，但有下列例外：

1. 建立 .NET Core 主控台應用程式。 在 Visual Studio**新的專案**對話方塊中，選取 **.NET Core** > **主控台應用程式 (.NET Core)**。 專案檔會將 `TargetFramework` 指定為 `netcoreapp2.x`。

1. 選擇的 WebJobs SDK 的發行版本 3.x 的下列套件：

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. 設定儲存體連接字串和中的 Application Insights 檢測金鑰*appsettings.json*檔案中的，使用.NET Core 組態架構。 以下是範例：

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. 變更`Main`方法程式碼，若要這樣做。 以下是範例：

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
