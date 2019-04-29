---
title: 在 Durable Functions 中管理執行個體 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 擴充中管理執行個體。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 12/07/2018
ms.date: 03/19/2019
ms.author: v-junlch
ms.openlocfilehash: ee96bc5e17051ab37be34eecbb8e4fe35599cd5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730764"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>在 Azure 中管理 Durable Functions 中的執行個體

如果你正在使用或想要开始使用 Azure Functions 的 [Durable Functions](durable-functions-overview.md) 扩展，请务必最充分地使用它。 可以通过详细了解 Durable Functions 业务流程实例的管理方式来优化这些实例。 本文討論每個執行個體管理作業的詳細資料。

例如，可以启动和终止实例，并可以查询实例，包括查询所有实例，或者使用筛选器查询实例。 此外，可将事件发送到实例、等待业务流程完成，以及检索 HTTP 管理 Webhook URL。 本文还会介绍其他管理操作，包括回退实例、清除实例历史记录和删除任务中心。

在 Durable Functions 中，可以使用相应的选项来执行其中的每项管理操作。 本文中的示例使用适用于 .NET (C#) 和 JavaScript 的 [Azure Functions Core Tools](../functions-run-local.md)。

## <a name="start-instances"></a>启动实例

必须能够启动业务流程的实例。 在另一个函数触发器中使用 Durable Functions 绑定时，通常会启动实例。

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) 中的 [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) 方法或 `DurableOrchestrationClient` (JavaScript) 中的 `startNew` 启动新的实例。 可以使用 `orchestrationClient` 绑定获取此类的实例。 在內部，此方法會將訊息加入控制佇列，然後就會利用 `orchestrationTrigger` 觸發程序繫結，觸發啟動具有指定名稱的函式。

協調流程程序已成功排定時，此非同步作業會完成。 協調程序應在 30 秒內啟動。 如果花费更长时间，将会出现 `TimeoutException`。

> [!WARNING]
> 在 JavaScript 中进行本地开发时，请将环境变量 `WEBSITE_HOSTNAME` 设置为 `localhost:<port>`（例如 `localhost:7071`）以使用 `DurableOrchestrationClient` 中的方法。 如需此需求的詳細資訊，請參閱 [GitHub 問題](https://github.com/Azure/azure-functions-durable-js/issues/28) \(英文\)。

### <a name="net"></a>.NET

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) 的參數如下：

* **Name**：要排程的協調器函式的名稱。
* **輸入**：應該當作輸入傳給協調器函式的任何 JSON 可序列化資料。
* **InstanceId**：(選擇性) 執行個體的唯一識別碼。 如果未指定此参数，该方法将使用随机 ID。

以下是簡單的 C# 範例：

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

`startNew` 的參數如下所示：

* **Name**：要排程的協調器函式的名稱。
* **InstanceId**：(選擇性) 執行個體的唯一識別碼。 如果未指定此参数，该方法将使用随机 ID。
* **輸入**：(選擇性) 應該當作輸入傳給協調器函式的任何 JSON 可序列化資料。

以下是簡單的 JavaScript 範例：

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!TIP]
> 使用隨機識別碼作為執行個體識別碼。 这有助于确保在多个 VM 上缩放业务流程协调程序函数时均衡分配负载。 使用非随机实例 ID 的适当时机是 ID 必须来自外部源，或实现[单一实例业务流程协调程序](durable-functions-singletons.md)模式时。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

还可以直接使用 [Azure Functions Core Tools](../functions-run-local.md) `durable start-new` 命令启动实例。 它需要以下參數：

* **`function-name` (必要)**：要启动的函数的名称。
* **`input` (選用)**：以内联方式或通过 JSON 文件提供的函数输入。 对于文件，请使用 `@` 将路径前缀添加到文件，例如 `@path/to/file.json`。
* **`id` (選用)**：協調流程執行個體的識別碼。 如果未指定此参数，该命令将使用随机 GUID。
* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 AzureWebJobsStorage。
* **`task-hub-name` (選用)**：要使用的 Durable Functions 任务中心的名称。 預設為 DurableFunctionsHub。 也可以使用 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

> [!NOTE]
> Core Tools 命令假设要从函数应用的根目录运行这些命令。 如果显式提供了 `connection-string-setting` 和 `task-hub-name` 参数，则可以从任何目录运行这些命令。 尽管无需运行函数应用主机即可运行这些命令，但除非运行主机，否则可能观察不到某些效果。 例如，`start-new` 命令会在目标任务中心内将某个启动消息排队，但除非某个正在运行的函数应用程序主机进程可以处理该消息，否则业务流程实际上不会运行。

以下命令启动名为 HelloWorld 的函数，并将文件 `counter-data.json` 的内容传递到该函数：

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>查询实例

在管理业务流程的过程中，你很可能需要收集有关业务流程实例状态的信息（例如，该实例是正常完成还是失败）。

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 類別 (.NET) 的 [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) 方法或 `DurableOrchestrationClient` 類別 (JavaScript) 的 `getStatus` 方法可查詢協調流程執行個體的狀態。

它會以 `instanceId` (必要)、`showHistory` (選用)、`showHistoryOutput` (選用) 和 `showInput` (選用，僅限 .NET) 作為參數。

* **`showHistory`**：如果设置为 `true`，则响应包含执行历史记录。
* **`showHistoryOutput`**：如果设置为 `true`，则执行历史记录包含活动输出。
* **`showInput`**：如果设置为 `false`，则响应不会包含函数的输入。 預設值為 `true`。 (僅限 .NET)

此方法會傳回具有下列屬性的 JSON 物件：

* **Name**：協調器函式的名稱。
* **InstanceId**：協調流程的執行個體識別碼 (應該與 `instanceId` 輸入相同)。
* **CreatedTime**：協調器函式開始執行的時間。
* **LastUpdatedTime**：協調流程前次執行檢查點檢查的時間。
* **輸入**：函式的 JSON 值輸入。 如果 `showInput` 为 false，则不会填充此字段。
* **CustomStatus**：JSON 格式的自訂協調流程狀態。
* **輸出**：函式的 JSON 值輸出 (如果函式已完成)。 如果业务流程协调程序函数失败，则此属性包含失败详细信息。 如果业务流程协调程序函数已终止，则此属性包含终止原因（如果有）。
* **RuntimeStatus**：下列其中一個值：
  * **Pending**：已排程的執行個體尚未開始執行。
  * **Running**：執行個體已開始執行。
  * **Completed**：執行個體已正常完成。
  * **ContinuedAsNew**：執行個體本身以新的記錄重新啟動。 這是暫時性的狀態。
  * **Failed**：函式失敗，發生錯誤。
  * **Terminated**：執行個體已突然停止。
* **History**：協調流程的執行記錄。 只有在 `showHistory` 設為 `true` 時，才會填入此欄位。

如果執行個體不存在或尚未開始執行，這個方法會傳回 `null`。

### <a name="c"></a>C#

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

也可以直接使用 [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` 命令获取业务流程实例的状态。 它需要以下參數：

* **`id` (必要)**：協調流程執行個體的識別碼。
* **`show-input` (選用)**：如果设置为 `true`，则响应包含函数的输入。 預設值為 `false`。
* **`show-output` (選用)**：如果设置为 `true`，则响应包含函数的输出。 預設值為 `false`。
* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設值為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)**：要使用的 Durable Functions 任务中心的名称。 預設值為 `DurableFunctionsHub`。 也可以使用 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

以下命令检索业务流程实例 ID 为 0ab8c55a66644d68a3a8b220b12d209c 的实例的状态（包括输入和输出）。 假设从函数应用的根目录运行 `func` 命令：

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

可以使用 `durable get-history` 命令检索业务流程实例的历史记录。 它需要以下參數：

* **`id` (必要)**：協調流程執行個體的識別碼。
* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設值為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)**：要使用的 Durable Functions 任务中心的名称。 預設值為 `DurableFunctionsHub`。 也可以使用 durableTask:HubName 在 host.json 中设置此参数。

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>查询所有实例

你可能会发现，一次性查询业务流程中的所有实例比逐个查询更加有效。

您可以使用 `GetStatusAsync` (.NET) 或 `getStatusAll` (JavaScript) 方法來查詢所有協調流程執行個體的狀態。 在 .NET 中，如果要取消该查询，可以传递 `CancellationToken` 对象。 此方法會和使用參數的 `GetStatusAsync`方法一樣傳回具有相同屬性的物件。

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

也可以直接使用 [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` 命令查询实例。 它需要以下參數：

* **`top` (選用)**：這個命令支援分頁。 此參數會對應至每個要求擷取的執行個體數目。 預設值為 10。
* **`continuation-token` (選用)**：用于指示要检索的实例页或节的标记。 每個 `get-instances` 執行都會將權杖傳回給下一組執行個體。
* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設值為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)**：要使用的 Durable Functions 任务中心的名称。 預設值為 `DurableFunctionsHub`。 也可以使用 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>使用筛选器查询实例

如果你实际上并不需要标准实例查询可以提供的所有信息，应该怎样做？ 例如，你只需要查找业务流程的创建时间或业务流程的运行时状态。 为此，可以通过应用筛选器来缩小查询范围。

使用 `GetStatusAsync` (.NET) 或 `getStatusBy` (JavaScript) 方法获取匹配一组预定义筛选器的业务流程实例的列表。

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IEnumerable<OrchestrationRuntimeStatus> runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

在 Azure Functions Core Tools 中，还可以结合筛选器使用 `durable get-instances` 命令。 除了前面提到的 `top`、`continuation-token`、`connection-string-setting` 和 `task-hub-name` 参数以外，还可以使用三个筛选器参数（`created-after`、`created-before` 和 `runtime-status`）。

* **`created-after` (選用)**：擷取這個日期/時間 (UTC) 之後建立的執行個體。 可接受 ISO 8601 格式的日期時間。
* **`created-before` (選用)**：擷取這個日期/時間 (UTC) 之後建立的執行個體。 可接受 ISO 8601 格式的日期時間。
* **`runtime-status` (選用)**：检索具有特定状态（例如 running 或 completed）的实例。 可以提供多個狀態 (以空格分隔)。
* **`top` (選用)**：每個要求擷取的執行個體數目。 預設值為 10。
* **`continuation-token` (選用)**：用于指示要检索的实例页或节的标记。 每個 `get-instances` 執行都會將權杖傳回給下一組執行個體。
* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設值為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)**：要使用的 Durable Functions 任务中心的名称。 預設值為 `DurableFunctionsHub`。 也可以使用 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

如果未提供任何筛选器（`created-after`、`created-before` 或 `runtime-status`），则无论运行时状态或创建时间是什么，该命令都会检索 `top` 实例。

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>终止实例

如果需要花费太长的时间来运行某个业务流程实例，或者出于某种原因需要提前将其停止，可以选择将其终止。

可以使用 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 类 (.NET) 的 [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) 方法，或 `DurableOrchestrationClient` 类 (JavaScript) 的 `terminate` 方法。 两个参数为 `instanceId` 和 `reason` 字符串，将写入日志和实例状态。 终止的实例在达到下一个 `await` (.NET) 或 `yield` (JavaScript) 点时会立即停止运行，或者在已进入 `await` 或 `yield` 点时会立即终止。

### <a name="c"></a>C#

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> 当前不会传播实例终止。 无论调用活动函数和子业务流程的业务流程实例是否已终止，活动函数和子业务流程都将运行至完成状态。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

也可以直接使用 [Azure Functions Core Tools](../functions-run-local.md) `durable terminate` 命令终止业务流程实例。 它需要以下參數：

* **`id` (必要)**：要终止的业务流程实例的 ID。
* **`reason` (選用)**：终止的原因。
* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設值為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)**：要使用的 Durable Functions 任务中心的名称。 預設值為 `DurableFunctionsHub`。 也可以使用 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

以下命令终止 ID 为 0ab8c55a66644d68a3a8b220b12d209c 的业务流程实例：

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>将事件发送到实例

在某些情况下，业务流程协调程序函数必须能够等待和侦听外部事件。 这包括[监视函数](durable-functions-concepts.md#monitoring)，以及等待[人机交互](durable-functions-concepts.md#human)的函数。

使用 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 类 (.NET) 的 [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) 方法或 `DurableOrchestrationClient` 类 (JavaScript) 的 `raiseEvent` 方法将事件通知发送到正在运行的实例。 正在等候呼叫 [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) 或 `waitForExternalEvent` (JavaScript) 的執行個體可以處理這些事件。

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) 和 `raiseEvent` (JavaScript) 的參數如下：

* **InstanceId**：執行個體的唯一識別碼。
* **EventName**：要傳送的事件名稱。
* **EventData**：要傳送至執行個體的 JSON 可序列化裝載。

### <a name="c"></a>C#

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!IMPORTANT]
> 如果不存在具有指定实例 ID 的业务流程实例，或者该实例不在等待指定的事件名称，则会丢弃事件消息。 如需這個行為的詳細資訊，請參閱 [GitHub 問題](https://github.com/Azure/azure-functions-durable-extension/issues/29)。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

也可以直接使用 [Azure Functions Core Tools](../functions-run-local.md) `durable raise-event` 命令向业务流程实例引发事件。 它需要以下參數：

* **`id` (必要)**：協調流程執行個體的識別碼。
* **`event-name` (選用)**：所要引發事件的名稱。 預設值為 `$"Event_{RandomGUID}"`。
* **`event-data` (選用)**：要傳送至協調流程執行個體的資料。 这可以是某个 JSON 文件的路径，或者，你可以直接在命令行中提供数据。
* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設值為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)**：要使用的 Durable Functions 任务中心的名称。 預設值為 `DurableFunctionsHub`。 也可以使用 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>等候協調流程完成

在长时间运行的业务流程中，你可能想要等待并获取业务流程的结果。 对于这种情况，在业务流程中定义超时期限也可能会很有帮助。 如果超过了超时期限，则应返回业务流程的状态而不是结果。

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 类在 .NET 中公开 [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API。 可以使用此 API 同步获取业务流程实例的实际输出。 在 JavaScript 中，`DurableOrchestrationClient` 類別會公開 `waitForCompletionOrCreateCheckStatusResponse`API 達成相同目的。 如果未设置这些参数，这些方法会使用默认值 10 秒作为 `timeout`，使用 1 秒作为 `retryInterval`。  

以下是範例 HTTP 觸發函式，它會示範如何使用這個 API：

```C#
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace VSSample
{
    public static class HttpSyncStart
    {
        private const string Timeout = "timeout";
        private const string RetryInterval = "retryInterval";

        [FunctionName("HttpSyncStart")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/wait")]
            HttpRequestMessage req,
            [OrchestrationClient] DurableOrchestrationClientBase starter,
            string functionName,
            ILogger log)
        {
            // Function input comes from the request content.
            dynamic eventData = await req.Content.ReadAsAsync<object>();
            string instanceId = await starter.StartNewAsync(functionName, eventData);

            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

            TimeSpan timeout = GetTimeSpan(req, Timeout) ?? TimeSpan.FromSeconds(30);
            TimeSpan retryInterval = GetTimeSpan(req, RetryInterval) ?? TimeSpan.FromSeconds(1);
            
            return await starter.WaitForCompletionOrCreateCheckStatusResponseAsync(
                req,
                instanceId,
                timeout,
                retryInterval);
        }

        private static TimeSpan? GetTimeSpan(HttpRequestMessage request, string queryParameterName)
        {
            string queryParameterStringValue = request.RequestUri.ParseQueryString()[queryParameterName];
            if (string.IsNullOrEmpty(queryParameterStringValue))
            {
                return null;
            }

            return TimeSpan.FromSeconds(double.Parse(queryParameterStringValue));
        }
    }
}
```

```Javascript
const df = require("durable-functions");

const timeout = "timeout";
const retryInterval = "retryInterval";

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = await client.startNew(req.params.functionName, undefined, req.body);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    const timeoutInMilliseconds = getTimeInSeconds(req, timeout) || 30000;
    const retryIntervalInMilliseconds = getTimeInSeconds(req, retryInterval) || 1000;

    return client.waitForCompletionOrCreateCheckStatusResponse(
        context.bindingData.req,
        instanceId,
        timeoutInMilliseconds,
        retryIntervalInMilliseconds);
};

function getTimeInSeconds (req, queryParameterName) {
    const queryValue = req.query[queryParameterName];
    return queryValue
        ? queryValue // expected to be in seconds
        * 1000 : undefined;
}
```

使用以下行调用该函数。 使用 2 秒作为超时，使用 0.5 秒作为重试间隔：

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

取決於從協調流程執行個體取得回應所需的時間，會有兩種情況：

* 业务流程实例在定义的超时（在本例中为 2 秒）内完成，响应是同步传送的实际业务流程实例输出：

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* 业务流程实例无法在定义的超时内完成，响应是 [HTTP API URL 发现](durable-functions-http-api.md)中所述的默认值：

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}",
            "rewindPostUri": "https://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/rewind?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Webhook URL 的格式可能会有所不同，具体取决于所运行 Azure Functions 主机的版本。 上述範例適用於 Azure Functions 2.x 主機。

## <a name="retrieve-http-management-webhook-urls"></a>检索 HTTP 管理 Webhook URL

可以使用外部系统来监视事件或者向业务流程引发事件。 外部系统可以通过作为 [HTTP API URL 发现](durable-functions-http-api.md)中所述的默认响应一部分的 Webhook URL 来与 Durable Functions 通信。 但是，也可以在业务流程客户端中或者在活动函数中以编程方式访问 Webhook URL。 为此，请使用 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 类 (.NET) 的 [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) 方法，或 `DurableOrchestrationClient` 类 (JavaScript) 的 `createHttpManagementPayload` 方法。

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) 和 `createHttpManagementPayload` 有一個參數：

* **instanceId**：執行個體的唯一識別碼。

该方法返回 [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) 或对象 (JavaScript) 的实例，该实例具有以下字符串属性：

* **Id**：協調流程的執行個體識別碼 (應該與 `InstanceId` 輸入相同)。
* **StatusQueryGetUri**：協調流程執行個體的狀態 URL。
* **SendEventPostUri**：協調流程執行個體的「引發事件」URL。
* **TerminatePostUri**：協調流程執行個體的「終止」URL。
* **RewindPostUri**：協調流程執行個體的「倒轉」URL。

活動函式可以將這些物件的執行個體傳送至外部系統，以監視或引發協調流程的事件：

### <a name="c"></a>C#

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] DurableActivityContext ctx,
    [OrchestrationClient] DurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

## <a name="rewind-instances-preview"></a>回退实例（预览）

如果意外的原因导致业务流程失败，可以使用相应的 API 将实例回退到以前的正常状态。

> [!NOTE]
> 此 API 並非要取代適當的錯誤處理和重試原則。 相反地，它只是要用於協調流程執行個體因非預期原因而失敗的情況。 有关错误处理和重试策略的详细信息，请参阅[错误处理](durable-functions-error-handling.md)主题。

使用 [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) 或 `rewindAsync` (JavaScript) API 将业务流程恢复到 *Running* 状态。 重新运行导致业务流程失败的活动或子业务流程执行失败。

例如，假设某个工作流涉及到一系列[人工审批](durable-functions-concepts.md#human)。 假设有一系列活动函数会通知某人做出审批并等待其实时响应。 在所有审批活动收到响应或超时后，假设另一活动因应用程序配置错误（例如数据库连接字符串无效）而失败。 結果就是深入工作流程的協調流程失敗。 使用 `RewindAsync` (.NET) 或 `rewindAsync` (JavaScript) API，应用程序管理员可以修复配置错误并将失败的业务流程回退到失败前的状态。 无需再次审批任何人工交互步骤，业务流程现可成功完成。

> [!NOTE]
> 回退功能不支持回退使用持久计时器的业务流程实例。

### <a name="c"></a>C#

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

也可以直接使用 [Azure Functions Core Tools](../functions-run-local.md) `durable rewind` 命令回退业务流程实例。 它需要以下參數：

* **`id` (必要)**：協調流程執行個體的識別碼。
* **`reason` (選用)**：回退业务流程实例的原因。
* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設值為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)**：要使用的 Durable Functions 任务中心的名称。 預設值為 `DurableFunctionsHub`。 也可以使用 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>清除实例历史记录

若要删除与业务流程关联的所有数据，可以清除实例历史记录。 例如，你可能想要删除现有的 Azure 表行和大消息 Blob。 为此，请使用 [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) API。

> [!NOTE]
> `PurgeInstanceHistoryAsync` API 目前僅適用於 C#。

 此方法有兩個多載。 第一个示例按业务流程实例的 ID 清除历史记录：

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

第二個範例會顯示計時器觸發的函式，它會清除在指定時間間隔之後完成的所有協調流程執行個體記錄。 在本例中，该函数将删除 30 天或更长时间以前完成的所有实例的数据。 该函数计划为在每天的凌晨 12 点运行一次：

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
                    DateTime.MinValue,
                    DateTime.UtcNow.AddDays(-30),  
                    new List<OrchestrationStatus>
                    {
                        OrchestrationStatus.Completed
                    });
}
```

> [!NOTE]
> 若要成功完成时间触发的函数进程，运行时状态必须是 **Completed**、**Terminated** 或 **Failed**。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

可以使用 [Azure Functions Core Tools](../functions-run-local.md) `durable purge-history` 命令清除业务流程实例的历史记录。 类似于前一部分中的第二个 C# 示例，该命令将清除在指定时间间隔内创建的所有业务流程实例的历史记录。 可按运行时状态进一步筛选已清除的实例。 此命令有數個參數：

* **`created-after` (選用)**：清除這個日期/時間 (UTC) 之後所建立執行個體的記錄。 可接受 ISO 8601 格式的日期時間。
* **`created-before` (選用)**：清除這個日期/時間 (UTC) 之前所建立執行個體的記錄。 可接受 ISO 8601 格式的日期時間。
* **`runtime-status` (選用)**：清除具有特定状态（例如 running 或 completed）的实例的历史记录。 可以提供多個狀態 (以空格分隔)。
* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設值為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)**：要使用的 Durable Functions 任务中心的名称。 預設值為 `DurableFunctionsHub`。 也可以使用 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

以下命令删除在 2018 年 11 月 14 日下午 7:35 (UTC) 之前创建的所有失败实例的历史记录。

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>删除任务中心

使用 [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` 命令可以删除与特定任务中心关联的所有存储项目。 這包括 Azure 儲存體資料表、佇列和 Blob。 此命令有兩個參數：

* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設值為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)**：要使用的 Durable Functions 任务中心的名称。 預設值為 `DurableFunctionsHub`。 也可以使用 durableTask:HubName 在 [host.json](durable-functions-bindings.md#host-json) 中设置此参数。

以下命令删除与 `UserTest` 任务中心关联的所有 Azure 存储数据。

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何使用 HTTP API 來管理執行個體](durable-functions-http-api.md)

<!-- Update_Description: wording update -->