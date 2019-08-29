---
title: Azure Functions 中的 Durable Functions 模式和技術概念
description: 瞭解 Azure Functions 中的 Durable Functions 延伸模組如何提供雲端中具狀態程式碼執行的優點。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: 828bcaa8c93454ba845c30c03c76144310891123
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098261"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Durable Functions 模式和技術概念 (Azure Functions)

Durable Functions 是[Azure Functions](../functions-overview.md)和[Azure WebJobs](../../app-service/web-sites-create-web-jobs.md)的延伸模組。 您可以使用 Durable Functions, 在無伺服器環境中撰寫具狀態函式。 此擴充功能會為您管理狀態、設定檢查點和重新啟動。 

本文提供 Azure Functions 和一般執行模式之 Durable Functions 延伸模組行為的詳細資訊。 這些資訊可協助您判斷如何使用 Durable Functions 來協助解決您的開發挑戰。

> [!NOTE]
> Durable Functions 是適用于 Azure Functions 的先進延伸模組, 不適用於所有應用程式。 本文假設您已熟悉[Azure Functions](../functions-overview.md)中的概念, 以及與無伺服器應用程式開發相關的挑戰。

## <a name="patterns"></a>模式

本節說明一些常見的應用程式模式, 其中 Durable Functions 會很有用。

### <a name="chaining"></a>模式 #1：函式鏈結

在函式鏈模式中, 一連串的函式會依特定循序執行。 在此模式中, 會將一個函數的輸出套用至另一個函式的輸入。

![函數鏈模式的圖表](./media/durable-functions-concepts/function-chaining.png)

您可以使用 Durable Functions 來簡明地實作用函式連結模式, 如下列範例所示:

#### <a name="c-script"></a>C# 指令碼

```csharp
public static async Task<object> Run(DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
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

> [!NOTE]
> 在中撰寫先行編譯的長期函式與在C#範例中所示的C#腳本中寫入先行編譯的長期函式之間有些許差異。 在先行C#編譯函式中, 持久性參數必須以個別的屬性裝飾。 例如`[OrchestrationTrigger]` , `DurableOrchestrationContext`參數的屬性。 在先行C#編譯的持久性函式中, 如果未正確裝飾參數, 執行時間就無法將變數插入函式中, 而且會發生錯誤。 如需更多範例, 請參閱[GitHub 上的 azure 函式-持久性擴充功能範例](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples)。

#### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

在此範例中,、 `F1`、 `F2`和`F3` `F4`值是函數應用程式中其他函式的名稱。 您可以使用一般命令式編碼結構來執行控制流程。 程式碼會從上到下執行。 程式碼可能牽涉到現有的語言控制流程語義, 例如條件式和迴圈。 您可以在`try`區塊中/ `catch` 包含錯誤處理`finally`邏輯。 /

您`context`可以使用[DurableOrchestrationCoNtext] \(.net\)和物件(JavaScript)參數,依名稱、傳遞參數和傳回函式輸出叫用其他函式。`context.df` 每次程式碼呼叫`await` (C#) 或`yield` (JavaScript) 時, Durable Functions framework 都會檢查目前函式實例的進度。 如果進程或 VM 在執行過程中回收, 函式實例會從上述`await`的繼續進行, 或`yield`呼叫。 如需詳細資訊, 請參閱下一節, 模式 #2:中的扇出/風扇。

> [!NOTE]
> JavaScript `context`中的物件代表整個[函數內容](../functions-reference-node.md#context-object), 而不只是[DurableOrchestrationCoNtext]參數。

### <a name="fan-in-out"></a>模式 #2：扇出/風扇傳入

在模式的扇出/風扇中, 您會平行執行多個函式, 然後等候所有函式完成。 通常, 某些匯總工作會在從函式傳回的結果上執行。

![扇出/風扇圖樣的圖表](./media/durable-functions-concepts/fan-out-fan-in.png)

使用標準函式時, 您可以讓函式將多個訊息傳送至佇列, 藉以進行扇出。 反過來回去會更具挑戰性。 若要在中使用, 您可以在一般函式中撰寫程式碼, 以追蹤佇列觸發的函式何時結束, 然後儲存函數輸出。 

Durable Functions 延伸模組會使用相當簡單的程式碼來處理此模式:

#### <a name="c-script"></a>C# 指令碼

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
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

展開傳送工作會散發至函式的多個實例`F2` 。 工作是使用動態的工作清單進行追蹤。 呼叫 .net `Task.WhenAll` API 或 JavaScript `context.df.Task.all` API, 以等候所有已呼叫的函式完成。 然後, `F2`函式輸出會從動態工作清單匯總並傳遞`F3`至函數。

發生于`await`或`yield`呼叫`Task.WhenAll`或的自動檢查點,可確保可能中途損毀或重新開機不需要重新開機已完成的工作。`context.df.Task.all`

### <a name="async-http"></a>模式 #3：非同步的 HTTP API

非同步 HTTP Api 模式會解決協調與外部用戶端之長時間執行作業狀態的問題。 實作為此模式的常見方式是讓 HTTP 呼叫觸發長時間執行的動作。 然後, 將用戶端重新導向至用戶端輪詢的狀態端點, 以瞭解作業完成的時間。

![HTTP API 模式的圖表](./media/durable-functions-concepts/async-http-api.png)

Durable Functions 提供內建 Api, 可簡化您撰寫的程式碼, 以與長時間執行的函式執行互動。 Durable Functions 快速入門範例 ([C#](durable-functions-create-first-csharp.md)和[JavaScript](quickstart-js-vscode.md)) 顯示一個簡單的 REST 命令, 可讓您用來啟動新的協調器函式實例。 實例啟動後, 擴充功能會公開可查詢協調器函式狀態的 webhook HTTP Api。 

下列範例會顯示啟動協調器並查詢其狀態的 REST 命令。 為了清楚起見，此範例中省略了部分詳細資料。

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

因為 Durable Functions 執行時間會管理狀態, 所以您不需要執行自己的狀態追蹤機制。

Durable Functions 延伸模組有內建的 webhook, 可管理長時間執行的協調流程。 您可以使用自己的函式觸發程式 (例如 HTTP、佇列或 Azure 事件中樞) 和系結, `orchestrationClient`自行執行此模式。 例如, 您可以使用佇列訊息來觸發終止。 或者, 您可以使用受 Azure Active Directory 驗證原則保護的 HTTP 觸發程式, 而不是使用產生的金鑰進行驗證的內建 webhook。

以下是如何使用 HTTP API 模式的一些範例:

#### <a name="c"></a>C#

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
// An HTTP-triggered function starts a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // The function name comes from the request URL.
    // The function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

在 .NET 中，[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` 參數是來自 `orchestrationClient` 輸出繫結 (此繫結是 Durable Functions 擴充功能的一部分) 的值。 在 JavaScript 中，呼叫 `df.getClient(context)` 會傳回這個物件。 這些物件會提供方法, 讓您用來啟動、傳送事件至、終止和查詢新的或現有的協調器函式實例。

在上述範例中, HTTP 觸發的函式會接受`functionName`來自傳入 URL 的值, 並將值傳遞給[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)。 [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_)系結 API 接著會傳迴響應, 其中包含`Location`標頭和實例的其他相關資訊。 您稍後可以使用此資訊來查詢已啟動之實例的狀態或終止實例。

### <a name="monitoring"></a>模式 #4：監視

監視模式指的是工作流程中有彈性、週期性的程式。 範例會輪詢直到符合特定條件為止。 您可以使用一般[計時器觸發](../functions-bindings-timer.md)程式來處理基本案例, 例如定期清除作業, 但其間隔是靜態的, 且管理實例存留期會變得複雜。 您可以使用 Durable Functions 來建立彈性的迴圈間隔、管理工作存留期, 以及從單一協調流程建立多個監視進程。

監視模式的範例是反轉先前的非同步 HTTP API 案例。 長時間執行的監視器會取用外部端點, 然後等候狀態變更, 而不是公開外部用戶端的端點來監視長時間執行的作業。

![監視模式的圖表](./media/durable-functions-concepts/monitor.png)

在幾行程式碼中, 您可以使用 Durable Functions 來建立多個監視, 以觀察任意端點。 監視可以在符合條件時結束執行, 或[DurableOrchestrationClient](durable-functions-instance-management.md)可以終止監視器。 您可以根據特定條件 ( `wait`例如, 指數輪詢) 來變更監視的間隔時間。 

下列程式碼會實行基本監視器:

#### <a name="c-script"></a>C# 指令碼

```csharp
public static async Task Run(DurableOrchestrationContext context)
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

收到要求時，系統會針對該作業識別碼建立新的協調流程執行個體。 執行個體會輪詢狀態，直到符合條件且迴圈結束為止。 持久性計時器控制輪詢間隔。 然後, 可以執行更多工具, 或協調流程可以結束。 當 (.net) 或`context.df.currentUtcDateTime` `expiryTime` (JavaScript) 超過值時, 監視器會結束。 `context.CurrentUtcDateTime`

### <a name="human"></a>模式 #5：與使用者互動

許多自動化程式都牽涉到某種人為互動。 在自動化程式中牽涉到人類很棘手, 因為使用者不是高度可用, 而且回應雲端服務。 自動化程式可能會使用「超時」和「補償邏輯」來允許此作業。

核准程式是牽涉到人為互動的商務程式範例。 對於超出特定金額的支出報表, 可能需要管理員核准。 如果管理員未在72小時內核准費用報告 (可能是經理休假), 則呈報程式會開始進行, 以取得其他人的核准 (可能是經理的經理)。

![人類互動模式的圖表](./media/durable-functions-concepts/approval.png)

您可以使用協調器函式, 在此範例中執行模式。 Orchestrator 會使用[持久性計時器](durable-functions-timers.md)來要求核准。 協調器會在發生超時時擴大。 協調器會等待[外來事件](durable-functions-external-events.md), 例如人為互動所產生的通知。

這些範例會建立核准程式, 以示範人類互動模式:

#### <a name="c-script"></a>C# 指令碼

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
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
            await context.CallActivityAsync("Escalate");
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

若要建立持久計時器, 請`context.CreateTimer`呼叫 (.net) `context.df.createTimer`或 (JavaScript)。 通知則由 `context.WaitForExternalEvent` (.NET) 或 `context.df.waitForExternalEvent` (JavaScript) 接收。 然後, `Task.WhenAny`呼叫 (.net) `context.df.Task.any`或 (JavaScript) 來決定是否要向上呈報 (先發生超時) 或處理核准 (在超時前收到核准)。

外部用戶端可以使用[內建的 HTTP api](durable-functions-http-api.md#raise-event) , 或使用來自另一個函式的[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) API, 將事件通知傳遞至等候的協調器函式:

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
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

### <a name="aggregator"></a>模式 #6:匯總工具 (預覽)

第六個模式是關於將一段時間內的事件資料匯總成單一可定址的*實體*。 在此模式中, 所匯總的資料可能來自多個來源、可分批傳遞, 或散佈在長時間內。 匯總工具可能需要在事件資料到達時對其採取動作, 而且外部用戶端可能需要查詢匯總資料。

![匯總工具圖表](./media/durable-functions-concepts/aggregator.png)

嘗試使用一般的無狀態函式來實作為此模式的困難之處在于, 並行控制會變得很大的挑戰。 您不僅需要擔心多個執行緒同時修改相同的資料, 您也需要擔心如何確保匯總工具一次只能在單一 VM 上執行。

使用[持久性實體](durable-functions-preview.md#entity-functions)函式, 可以輕鬆地將此模式實作為單一函數。

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
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

持久性實體也可以模型化為 .NET 類別。 如果作業清單變得很大, 而且大部分都是靜態的, 這就很有用。 下列範例是使用 .net 類別和方法的`Counter`實體對等執行。

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

用戶端可以使用`orchestrationClient`系結加入實體函式 (也稱為「信號」) 的佇列作業。

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

動態產生的 proxy 也適用于以型別安全方式發出的信號實體。 除了信號外, 用戶端也可以使用系結上`orchestrationClient`的方法來查詢實體函式的狀態。

> [!NOTE]
> 實體函數目前僅適用于[Durable Functions 2.0 preview](durable-functions-preview.md)。

## <a name="the-technology"></a>技術

在幕後, Durable Functions 延伸模組建置於長期工作[架構](https://github.com/Azure/durabletask)之上, 此為 GitHub 上的開放原始碼程式庫, 可用來建立長期工作協調流程。 就像 Azure Functions 是 Azure WebJobs 的無伺服器演進, Durable Functions 是長期工作架構的無伺服器進化。 Microsoft 和其他組織會廣泛地使用長期工作架構, 將任務關鍵性程式自動化。 它天生就適合無伺服器的 Azure Functions 環境。

### <a name="event-sourcing-checkpointing-and-replay"></a>事件來源、檢查點和重新執行

協調器函式會使用[事件來源](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)設計模式, 可靠地維護其執行狀態。 Durable Functions 擴充功能不會直接儲存協調流程的目前狀態, 而是使用附加專用存放區來記錄函式協調流程所採取的一系列完整動作。 相較于「傾印」完整執行時間狀態, 附加專用存放區有許多好處。 優點包括提升效能、擴充性和回應能力。 您也可以取得交易資料的最終一致性, 以及完整的審核線索和歷程記錄。 「審核線索」支援可靠的補償動作。

Durable Functions 會以透明的方式使用事件來源。 在幕後, 協調器`await`函C#式中`yield`的 () 或 (JavaScript) 運算子會將協調器執行緒的控制權, 回傳給長期的工作架構發送器。 發送器接著會將協調器函式所排程的任何新的動作 (例如，呼叫一或多個子函式或排程長期計時器) 認可至儲存體。 「透明認可」動作會附加至協調流程實例的執行歷程記錄。 歷程記錄會儲存於儲存體資料表。 認可動作接著會將訊息新增至佇列以排程實際的工作。 此時，協調器函式即可從記憶體卸載。 

如果您使用 Azure Functions 取用方案, 協調器函式的計費就會停止。 當有更多工作要執行時, 函式會重新開機, 並重建其狀態。

當協調流程函式有更多工作要執行時 (例如, 收到回應訊息或持久計時器過期), orchestrator 會喚醒並重新執行整個函式, 以重建本機狀態。 

在重新執行期間, 如果程式碼嘗試呼叫函式 (或執行任何其他非同步工作), 則長期工作架構會查閱目前協調流程的執行歷程記錄。 如果它發現[活動](durable-functions-types-features-overview.md#activity-functions)函式已執行並產生結果, 它會重新執行該函式的結果, 並繼續執行協調器程式碼。 重新執行會繼續執行, 直到函式程式碼完成或已排程新的非同步工作為止。

### <a name="orchestrator-code-constraints"></a>協調器程式碼條件約束

Orchestrator 程式碼的重新執行行為會針對您可以在協調器函式中撰寫的程式碼類型建立條件約束。 例如, 協調器程式碼必須具決定性, 因為它會多次重新執行, 而且每次都必須產生相同的結果。 如需條件約束的完整清單, 請參閱協調器程式[代碼條件約束](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints)。

## <a name="monitoring-and-diagnostics"></a>監視和診斷

如果您使用 Azure 應用程式 Insights 檢測金鑰來設定函式應用程式, Durable Functions 擴充功能會自動發出結構化追蹤資料給[Application Insights](../functions-monitoring.md) 。 您可以使用追蹤資料來監視協調流程的動作和進度。

以下範例說明當您使用[Application Insights 分析](../../application-insights/app-insights-analytics.md)時, Durable Functions 追蹤事件在 Application Insights 入口網站中的樣子:

![Application Insights 查詢結果](./media/durable-functions-concepts/app-insights-1.png)

您可以在每個記錄專案的`customDimensions`欄位中找到有用的結構化資料。 以下是完全展開的專案範例:

![Application Insights 查詢中的 customDimensions 欄位](./media/durable-functions-concepts/app-insights-2.png)

由於長期工作架構發送器的重新執行行為，您應該會看到重新執行的動作有重複的記錄項目。 重複的記錄專案可協助您瞭解核心引擎的重新執行行為。 [診斷](durable-functions-diagnostics.md)文章會顯示篩選出重新執行記錄的範例查詢, 讓您可以只看到「即時」記錄檔。

## <a name="storage-and-scalability"></a>儲存體和延展性

Durable Functions 延伸模組會使用 Azure 儲存體中的佇列、資料表和 blob 來保存執行歷程記錄狀態和觸發函式執行。 您可以使用函數應用程式的預設儲存體帳戶, 也可以設定個別的儲存體帳戶。 您可能會想要以儲存體輸送量限制為依據的個別帳戶。 您所撰寫的協調器程式碼不會與這些儲存體帳戶中的實體互動。 長期工作架構會直接管理實體, 做為執行詳細資料。

協調器函式會排程活動函式，並透過內部佇列訊息收到其回應。 當函式應用程式在 Azure Functions 耗用量方案中執行時, [Azure Functions 縮放控制器](../functions-scale.md#how-the-consumption-and-premium-plans-work)會監視這些佇列。 新的計算實例會視需要新增。 相應放大至多個 Vm 時, 協調器函式可能會在一個 VM 上執行, 而協調器函式所呼叫的活動功能可能會在數個不同的 Vm 上執行。 如需 Durable Functions 的調整行為詳細資訊, 請參閱[效能和規模](durable-functions-perf-and-scale.md)。

Orchestrator 帳戶的執行歷程記錄會儲存在資料表儲存體中。 每當實例在特定 VM 上解除凍結時, orchestrator 就會從資料表儲存體提取其執行歷程記錄, 讓它可以重建其本機狀態。 在資料表儲存體中提供歷程記錄的方便層面在於, 您可以使用[Azure 儲存體總管](../../vs-azure-tools-storage-manage-with-storage-explorer.md)之類的工具來查看協調流程的歷程記錄。

儲存體 blob 主要是用來做為租用機制, 以協調跨多個 Vm 的協調流程實例相應放大。 儲存體 blob 會保存無法直接儲存在資料表或佇列中的大型訊息資料。

![Azure 儲存體總管的螢幕擷取畫面](./media/durable-functions-concepts/storage-explorer.png)

> [!NOTE]
> 雖然查看資料表儲存體中的執行歷程記錄很簡單且方便, 但請勿對此資料表進行任何相依性。 當 Durable Functions 延伸模組演進時, 資料表可能會變更。

## <a name="known-issues"></a>已知問題

[GitHub 問題](https://github.com/Azure/azure-functions-durable-extension/issues)清單應可追蹤所有的已知問題。 如果您遇到問題, 而且在 GitHub 中找不到問題, 請開啟新的問題。 包含問題的詳細描述。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Durable Functions, 請參閱[Durable Functions 函數類型和功能](durable-functions-types-features-overview.md)。 

開始進行之前：

> [!div class="nextstepaction"]
> [建立您的第一個長期函式](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html
