---
title: Durable Functions 概觀 - Azure
description: Azure Functions 的 Durable Functions 擴充簡介。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 08/07/2019
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: a917a823d47d6a072cf5a3ee5d636b432913df9a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299432"
---
# <a name="what-are-durable-functions"></a>Durable Functions 是什麼？

*Durable Functions* 是 [Azure Functions](../functions-overview.md) 的擴充功能，可讓您在無伺服器計算環境中撰寫具狀態函式。 此擴充功能可讓您使用 Azure Functions 程式設計模型，藉由撰寫[協調器函式  ](durable-functions-orchestrations.md)來定義具狀態的工作流程，以及撰寫[實體函式  ](durable-functions-entities.md)來定義具狀態的實體。 擴充功能其實會為您管理狀態、檢查點和重新啟動，讓您將焦點放在商務邏輯。

## <a name="language-support"></a>支援的語言

Durable Functions 目前支援下列語言：

* **C#** ：[預先編譯的類別庫](../functions-dotnet-class-library.md)和 [C# 指令碼](../functions-reference-csharp.md)。
* **F#** ：預先編譯的類別庫和 F# 指令碼。 只有 1.x 版的 Azure Functions 執行階段才支援 F# 指令碼。
* **JavaScript**：只有 2.x 版的 Azure Functions 執行階段才支援。 需要 1.7.0 版的 Durable Functions 擴充功能，或更新版本。 

Durable Functions 的目標是支援所有的 [Azure Functions 語言](../supported-languages.md)。 請參閱 [Durable Functions 問題清單](https://github.com/Azure/azure-functions-durable-extension/issues)，以取得最新工作狀態來支援其他語言。

如同 Azure Functions，有一些範本可協助您使用 [Visual Studio 2019](durable-functions-create-first-csharp.md)、[Visual Studio Code](quickstart-js-vscode.md) 和 [Azure 入口網站](durable-functions-create-portal.md)開發 Durable Functions。

## <a name="application-patterns"></a>應用程式模式

Durable Functions 主要用來簡化無伺服器應用程式中複雜的具狀態協調需求。 下列各節會說明可受惠於 Durable Functions 的典型應用程式模式：

* [函式鏈結](#chaining)
* [展開傳送/收合傳送](#fan-in-out)
* [非同步的 HTTP API](#async-http)
* [監視](#monitoring)
* [人為互動](#human)
* [匯總工具](#aggregator)

### <a name="chaining"></a>模式 #1：函式鏈結

在函式鏈結模式中，有一連串的函式會依特定順序執行。 在此模式中，一個函式的輸出會套用至另一個函式的輸入。

![模式鏈結模式的圖表](./media/durable-functions-concepts/function-chaining.png)

您可以使用 Durable Functions 簡潔地實作如下列範例所示的用函式鏈結模式：

#### <a name="c"></a>C#

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1", null);
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return    yield context.df.callActivity("F4", z);
});
```

在此範例中，`F1`、`F2`、`F3` 和 `F4`值是函式應用程式中其他函式的名稱。 您可以使用一般命令式編碼建構來實作控制流程。 程式碼會由上而下地執行。 程式碼可包含現有語言的控制流程語意，例如條件和迴圈。 您可以在 `try`/ `catch`/`finally` 區塊中包含錯誤處理邏輯。

您可以使用 `context` 參數 [DurableOrchestrationContext] \(.NET\) 和 `context.df` 物件 (JavaScript)，依名稱叫用其他函式、傳遞參數，以及傳回函式輸出。 每當程式碼呼叫 `await` (C#) 或 `yield` (JavaScript) 時，Durable Functions 架構便會對目前函式執行個體的進度設定檢查點。 如果處理序或 VM 在執行途中回收，函式執行個體便會從先前的 `await` 或 `yield` 呼叫繼續執行。 如需詳細資訊，請參閱下一節，模式 #2：展開傳送/收合傳送。

> [!NOTE]
> JavaScript 中的 `context` 物件代表整個[函式內容](../functions-reference-node.md#context-object)，而不只是 [DurableOrchestrationContext] 參數。

### <a name="fan-in-out"></a>模式 #2：展開傳送/收合傳送。

在展開傳送/收合傳送模式中，您會以平行方式執行多個函式，然後等候所有函式完成。 某些彙總工作通常會在函式傳回結果時執行。

![展開傳送/收合傳送模式的圖表](./media/durable-functions-concepts/fan-out-fan-in.png)

在標準函式中，您可藉由讓函式傳送多則訊息給佇列來進行展開傳送。 反過來的收合傳送則困難得多。 若要收合傳送，您可撰寫程式碼來追蹤佇列所觸發的函式何時結束，然後儲存函式輸出。

Durable Functions 擴充功能會以較簡單的程式碼處理此模式：

#### <a name="c"></a>C#

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1", null);
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

展開傳送工作會散發至 `F2` 函式的多個執行個體。 此工作是使用動態的工作清單進行追蹤。 系統會呼叫 .NET `Task.WhenAll` API 或 JavaScript `context.df.Task.all` API 來等候所有呼叫的函式完成。 然後，`F2` 函式的輸出會從動態工作清單彙總起來並傳遞給 `F3` 函式。

在 `Task.WhenAll` 或 `context.df.Task.all` 的 `await` 或 `yield` 呼叫發生的自動設定檢查點作業，可確保可能的中途當機或重新開機不需要重新啟動已經完成的工作。

> [!NOTE]
> 在少數的情況下，在活動函式完成後，但將其完成儲存到協調流程歷程記錄前的空窗期中可能會發生損毀。 如果發生這種情況，則會在程序復原之後從頭重新執行活動函式。

### <a name="async-http"></a>模式 #3：非同步的 HTTP API

非同步 HTTP API 模式會處理與外部用戶端協調長時間執行作業狀態的問題。 實作為此模式的常見方式是讓 HTTP 端點觸發長時間執行的動作。 然後，將用戶端重新導向至用戶端輪詢的狀態端點，以了解作業完成的時間。

![HTTP API 模式的圖表](./media/durable-functions-concepts/async-http-api.png)

Durable Functions 會提供此模式的**內建**支援，進而簡化或甚至移除您需要撰寫的程式碼，以與長時間執行的函式執行作業互動。 例如，Durable Functions 快速入門範例 ([C#](durable-functions-create-first-csharp.md) 和 [JavaScript](quickstart-js-vscode.md)) 會顯示一個簡單的 REST 命令，您可將此命令用來啟動新的協調器函式執行個體。 在執行個體啟動後，擴充功能就會公開 Webhook HTTP API 來查詢協調器函式的狀態。 

下列範例顯示 REST 命令，這些命令可啟動協調器及查詢其狀態。 為了清楚起見，此範例省略了一些通訊協定詳細資料。

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Durable Functions 執行階段會為您管理狀態，因此您不必實作自己的狀態追蹤機制。

Durable Functions 擴充功能會公開內建 HTTP API，該 API 可管理長時間執行的協調流程。 您也可以使用自己的函式觸發程式 (例如 HTTP、佇列或 Azure 事件中樞) 和[協調流程用戶端繫結](durable-functions-bindings.md#orchestration-client)，自行執行此模式。 例如，您可以使用佇列訊息來觸發終止。 或者，您可以使用由 Azure Active Directory 驗證原則保護的 HTTP 觸發程序，而不是使用內建 HTTP API (其使用產生的金鑰進行驗證)。

如需詳細資訊，請參閱 [HTTP 功能](durable-functions-http-features.md)一文，其中說明如何使用 Durable Functions 擴充功能，透過 HTTP 公開非同步、長時間執行的程序。

### <a name="monitoring"></a>模式 #4：監視

監視模式是指工作流程中有彈性的週期性程序。 範例會輪詢直到符合特定條件為止。 您可以一般[計時器觸發程序](../functions-bindings-timer.md)來解決基本案例，例如定期清除作業，但其間隔是靜態的，且管理執行個體存留期會變得很複雜。 您可以使用 Durable Functions 來建立有彈性的週期間隔、管理工作存留期，以及從單一協調流程建立多個監視程序。

監視模式的範例是反轉先前的非同步 HTTP API 案例。 長期執行的監視器會取用外部端點，而後等待狀態變更，而不會公開端點，以供外部用戶端監視長時間執行的作業。

![監視模式的圖表](./media/durable-functions-concepts/monitor.png)

在幾行程式碼中，您可以使用 Durable Functions 建立可觀察任意端點的多個監視器。 監視器可以在符合條件時結束執行，或 [DurableOrchestrationClient](durable-functions-instance-management.md) 可以終止監視器。 您可以根據特定條件 (例如，指數輪詢) 來變更監視器的 `wait` 間隔時間。 

下列程式碼會實作基本的監視器：

#### <a name="c"></a>C#

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

收到要求時，系統會針對該作業識別碼建立新的協調流程執行個體。 執行個體會輪詢狀態，直到符合條件且迴圈結束為止。 長期計時器可控制輪詢間隔。 接著可執行更多工作，否則協調流程可能會結束。 當 `context.CurrentUtcDateTime` (.NET) 或 `context.df.currentUtcDateTime` (JavaScript) 超過 `expiryTime` 值時，監視器就會結束。

### <a name="human"></a>模式 #5：與使用者互動

許多自動化程序都會牽涉到某種類型的人為互動。 自動化程序卻需人為涉入很弔詭，因為人們無法像雲端服務一樣隨時有空能夠回應。 自動化程序可使用逾時和補償邏輯來允許此互動。

核准程序是需要人為互動的商務程序範例。 超過特定金額的費用報告就可能需要管理員核准。 如果經理未在 72 小時內核准費用報告 (可能是經理休假了)，系統就會啟動向上呈報程序，以尋求其他人的核准 (或許是經理的經理)。

![人類互動模式的圖表](./media/durable-functions-concepts/approval.png)

您可以使用協調器函式，在此範例中執行模式。 協調器會使用[長期計時器](durable-functions-timers.md)來要求核准。 如果發生逾時，協調器就會向上呈報。 協調器會等待[外部事件](durable-functions-external-events.md)，例如人為互動所產生的通知。

這些範例會建立核准程序，以示範人類互動模式：

#### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval", null);
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate", null);
        }
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

若要建立長期計時器，請呼叫 `context.CreateTimer` (.NET) 或 `context.df.createTimer` (JavaScript)。 通知則由 `context.WaitForExternalEvent` (.NET) 或 `context.df.waitForExternalEvent` (JavaScript) 接收。 接著，還會呼叫 `Task.WhenAny` (.NET) 或 `context.df.Task.any` (JavaScript) 以決定是要向上呈報 (先發生逾時) 還是處理核准 (逾時前收到核准)。

外部用戶端可以從另一個函式使用[內建 HTTP API](durable-functions-http-api.md#raise-event) 或使用 [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) API，將事件通知傳遞至等候的協調器函式：

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

### <a name="aggregator"></a>模式 #6：彙總工具 (預覽)

第六個模式是關於將一段時間的事件資料彙總成單一可定址的「實體」  。 在此模式中，所彙總的資料可能來自多個來源、可分批傳遞，或可散佈於長期的時間。 彙總工具可能需要在事件資料送達時對其採取動作，而外部用戶端可能需要查詢所彙總的資料。

![彙總工具圖表](./media/durable-functions-concepts/aggregator.png)

嘗試使用一般無狀態函式來實作此模式的弔詭之處在於，並行控制會變成一大挑戰。 您不只需要擔心多個執行緒同時修改相同的資料，也需要擔心如何確保彙總工具一次只在單一 VM 上執行。

使用[長期實體函式](durable-functions-preview.md#entity-functions)，可以輕鬆地將此模式當作單一函式實作。

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += amount;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

長期實體也可以模型化為 .NET 類別。 如果作業清單固定且變得很大，此模型會很實用。 下列範例等同於使用 .NET 類別和方法來實作 `Counter` 實體。

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

用戶端可以使用[實體用戶端繫結](durable-functions-bindings.md#entity-client)，將實體函式的「作業」  排入佇列 (也稱為「訊號處理」)。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [OrchestrationClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

動態產生的 Proxy 也適用於以型別安全方式進行實體訊號處理。 除了訊號處理外，用戶端也可以在協調流程用戶端繫結上使用[型別安全方法](durable-functions-bindings.md#entity-client-usage)來查詢實體函式的狀態。

> [!NOTE]
> 實體函式目前僅作為 [Durable Functions 2.0 預覽](durable-functions-preview.md)的一部分在 .NET 中提供。

## <a name="the-technology"></a>技術

Durable Functions 擴充功能其實是以[長期工作架構](https://github.com/Azure/durabletask)為基礎所建置的，長期工作架構是 GitHub 上以程式碼用來建置工作流程的開放原始碼程式庫。 如同 Azure Functions 是 Azure WebJobs 的無伺服器演化，Durable Functions 也是長期工作架構的無伺服器演化。 Microsoft 和其他組織會廣泛地使用長期工作架構，將任務關鍵程序自動化。 它天生就適合無伺服器的 Azure Functions 環境。

## <a name="code-constraints"></a>程式碼條件約束

為了提供可靠且長時間執行的執行保證，協調器函式具有一組必須遵循的編碼規則。 如需詳細資訊，請參閱[協調器函式程式碼條件約束](durable-functions-code-constraints.md)一文。

## <a name="billing"></a>計費

Durable Functions 的收費方式與 Azure Functions 相同。 如需詳細資訊，請參閱 [Azure Functions 價格](https://azure.microsoft.com/pricing/details/functions/)。 在 Azure Functions [取用方案](../functions-scale.md#consumption-plan)中執行協調器函式時，有一些要注意的計費行為。 如需這些行為的詳細資訊，請參閱 [Durable Functions 計費](durable-functions-billing.md)一文。

## <a name="jump-right-in"></a>立即參與

完成下列其中一個特定語言快速入門教學課程，即可在 10 分鐘內開始使用 Durable Functions：

* [使用 Visual Studio 2019 的 C#](durable-functions-create-first-csharp.md)
* [使用 Visual Studio Code 的 JavaScript](quickstart-js-vscode.md)

在這兩個快速入門中，您會在本機建立及測試 "hello world" 耐久函式。 接著會將函式程式碼發佈至 Azure。 您建立的函式會協調對其他函式的呼叫並鏈結在一起。

## <a name="learn-more"></a>深入了解

下列影片特別說明 Durable Functions 的優點：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

如需 Durable Functions 和基礎技術的更深入討論，請參閱下列影片 (著重於 .NET，但這些概念也適用於其他支援的語言)：

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

因為 Durable Functions 是 [Azure Functions](../functions-overview.md) 的進階擴充功能，因此並非所有應用程式都適用。 如需與其他 Azure 協調流程技術的比較，請參閱[比較 Azure Functions 和 Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Durable Functions 函式類型和功能](durable-functions-types-features-overview.md)
