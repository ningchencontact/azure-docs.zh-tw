---
title: 長期函式模式和 Azure Functions 中的技術概念
description: 了解如何在 Azure Functions 的 Durable Functions 擴充可讓您在雲端中的具狀態的程式碼執行的優點。
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: e5be81efcd655f1f0361d8c00d978a81c3e6caa5
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443414"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>長期函式模式和技術的概念 (Azure Functions)

Durable Functions 也是的延伸模組[Azure Functions](../functions-overview.md)並[Azure WebJobs](../../app-service/web-sites-create-web-jobs.md)。 您可以使用 Durable Functions 的無伺服器環境中撰寫具狀態的函式。 此擴充功能會為您管理狀態、設定檢查點和重新啟動。 

此文章提供的詳細資訊之行為的 Durable Functions 擴充功能用於 Azure Functions 和常見的實作模式。 資訊可協助您判斷如何使用 Durable Functions 協助解決您的開發挑戰。

> [!NOTE]
> Durable Functions 也是不是適用於所有應用程式的 Azure Functions 的進階擴充功能。 本文假設您已非常熟悉的概念[Azure Functions](../functions-overview.md)所面臨的挑戰與無伺服器應用程式開發。

## <a name="patterns"></a>模式

本章節描述一些常見的應用程式模式，Durable Functions 中可以有幫助。

### <a name="chaining"></a>模式 #1：函式鏈結

在函式鏈結模式時，在一連串的函式會執行以特定順序。 在此模式中，一個函式的輸出會套用至另一個函式的輸入。

![函式鏈結模式的圖表](./media/durable-functions-concepts/function-chaining.png)

您可以使用長期函式來實作函式鏈結模式，只有在下列範例所示，簡潔：

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
> 寫入先行編譯的永久性函式的細微差異C#中撰寫先行編譯的永久性函式和C#範例所示的指令碼。 在C#先行編譯函式，必須使用個別的屬性裝飾持久的參數。 例如，`[OrchestrationTrigger]`屬性`DurableOrchestrationContext`參數。 在C#先行編譯永久性的函式，如果未適當地裝飾參數，執行階段無法插入函式的變數，而且發生錯誤。 如需其他範例，請參閱 < [GitHub 上的 azure functions-持久擴充範例](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples)。

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

在此範例中，值`F1`， `F2`， `F3`，和`F4`函式應用程式中的其他函式的名稱。 您可以使用一般的命令式編碼建構來實作控制流程。 程式碼執行由上往下。 程式碼可包含現有語言的控制流程語意，例如條件和迴圈。 您可以包含錯誤處理邏輯`try` / `catch` / `finally`區塊。

您可以使用`context`參數[DurableOrchestrationContext] \(.NET\)而`context.df`名稱來叫用其他函式、 傳遞參數，並傳回函式的物件 (JavaScript)輸出。 每次程式碼會呼叫`await`(C#) 或`yield`(JavaScript)，Durable Functions 架構檢查點目前的函式執行個體的進度。 如果 VM 的處理序回收在執行途中，函式執行個體就會繼續從上述`await`或`yield`呼叫。 如需詳細資訊，請參閱下一步 區段中，模式 #2:風扇 out/風扇中。

> [!NOTE]
> `context`在 JavaScript 中的物件都代表整個[函式內容](../functions-reference-node.md#context-object)，不只[DurableOrchestrationContext]參數。

### <a name="fan-in-out"></a>模式 #2：風扇 out/中風扇

在 風扇 out/風扇模式中，您平行執行多個函式，然後等候完成的所有函式。 通常，在函式會傳回結果時執行一些彙總的工作。

![風扇的圖表外/風扇模式](./media/durable-functions-concepts/fan-out-fan-in.png)

標準函式中，您可以有多個訊息傳送至佇列的函式展開傳送。 反過來則困難得多。 若要正常的函式中，展開您要撰寫程式碼來追蹤佇列所觸發的函式結束時，並加以儲存函式的輸出。 

Durable Functions 擴充功能會處理此模式相當簡單的程式碼：

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

展開傳送工作分散到多個執行個體`F2`函式。 使用動態工作清單來追蹤工作。 .NET `Task.WhenAll` API 或 JavaScript`context.df.Task.all`呼叫 API 時，等候完成呼叫的函式。 然後，`F2`函式的輸出是從動態工作清單彙總，並且傳遞給`F3`函式。

在發生自動檢查點`await`或是`yield`上呼叫`Task.WhenAll`或`context.df.Task.all`可確保，可能會遇到當機或重新開機不需要重新啟動已完成的工作。

### <a name="async-http"></a>模式 #3：非同步的 HTTP API

非同步 HTTP Api 模式可解決問題的協調長時間執行的作業，與外部用戶端的狀態。 若要實作此模式的常見方式是擁有 HTTP 呼叫觸發程序長時間執行的動作。 然後，將用戶端重新導向至用戶端會輪詢以了解作業完成時的狀態端點。

![HTTP API 模式的圖表](./media/durable-functions-concepts/async-http-api.png)

Durable Functions 會提供內建的 Api 來簡化您撰寫來與長時間執行的函式執行互動的程式碼。 Durable Functions 快速入門範例 ([ C# ](durable-functions-create-first-csharp.md)並[JavaScript](quickstart-js-vscode.md)) 顯示一個簡單的 REST 命令，可用來啟動新的協調器函式執行個體。 執行個體啟動之後，擴充功能會公開 webhook HTTP Api，查詢協調器函式的狀態。 

下列範例會顯示啟動協調器，並查詢其狀態的 REST 命令。 為了清楚起見，此範例中省略了部分詳細資料。

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

Durable Functions 執行階段會管理狀態，因為您不需要實作您自己的狀態追蹤機制。

Durable Functions 擴充功能已內建管理長時間執行的協調流程的 webhook。 您可以自行實作此模式使用您自己的函式觸發程序 （例如 HTTP、 佇列或 Azure 事件中樞） 和`orchestrationClient`繫結。 例如，您可能會使用佇列訊息來觸發終止。 或者，您可以使用受保護的 Azure Active Directory 驗證原則，而不是使用產生的金鑰進行驗證的內建 webhook 的 HTTP 觸發程序。

以下是一些範例，示範如何使用 HTTP API 模式：

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

> [!WARNING]
> 當您在本機開發在 JavaScript 中，若要使用上的方法`DurableOrchestrationClient`，您必須設定環境變數`WEBSITE_HOSTNAME`要`localhost:<port>`(比方說， `localhost:7071`)。 如需有關這項需求的詳細資訊，請參閱 < [GitHub 問題 28](https://github.com/Azure/azure-functions-durable-js/issues/28)。

在 .NET 中，[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` 參數是來自 `orchestrationClient` 輸出繫結 (此繫結是 Durable Functions 擴充功能的一部分) 的值。 在 JavaScript 中，呼叫 `df.getClient(context)` 會傳回這個物件。 這些物件會提供您可以使用來啟動、 傳送事件、 終止，及查詢新的或現有的協調器函式執行個體的方法。

在上述範例中，HTTP 觸發的函式會採用`functionName`從傳入 URL 的值，並傳遞至[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)。 [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_)再繫結 API 傳回的回應包含`Location`標頭和執行個體的其他資訊。 稍後若要查閱已啟動的執行個體的狀態，或終止執行個體，您可以使用資訊。

### <a name="monitoring"></a>模式 #4：監視

監視模式是指工作流程中的彈性、 重複執行程序。 範例輪詢直到符合特定條件。 您可以使用一般[計時器觸發程序](../functions-bindings-timer.md)解決基本案例中的，例如定期清除作業，但其間隔可以是靜態的管理執行個體存留期會變得複雜。 您可以使用 Durable Functions 來建立彈性的循環間隔、 管理工作的存留期，以及從單一的協調流程建立多個監視處理序。

若要反轉先前的非同步 HTTP API 案例，是監視模式的範例。 而不是公開端點的外部用戶端監視長時間執行作業，長時間執行的監視器會取用外部端點，，然後等候狀態變更。

![監視模式的圖表](./media/durable-functions-concepts/monitor.png)

幾行程式碼，您可以使用 Durable Functions 來建立觀察任意端點的多個監視器。 監視器可以結束執行，當符合條件時，或[DurableOrchestrationClient](durable-functions-instance-management.md)可以終止監視。 您可以變更監視器的`wait`間隔根據特定條件 （例如指數型輪詢）。 

下列程式碼會實作基本的監視：

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

收到要求時，系統會針對該作業識別碼建立新的協調流程執行個體。 執行個體會輪詢狀態，直到符合條件且迴圈結束為止。 永久性計時器控制輪詢間隔。 接著，可以執行更多工作，或協調流程可以結束。 當`context.CurrentUtcDateTime`(.NET) 或`context.df.currentUtcDateTime`(JavaScript) 超過`expiryTime`值，監視器會結束。

### <a name="human"></a>模式 #5：與使用者互動

許多自動化的程序牽涉到某種類型的人為互動。 自動化程序卻相當困難，因為人不為高可用性和回應以雲端服務。 自動化程序可能會允許此使用逾時和補償邏輯。

核准程序是需要人為互動的商務程序的範例。 管理員核准可能需要超過特定金額的費用報表。 如果管理員未核准的經費支出報表 （或許 manager 去渡假） 的 72 小時內，向上呈報程序就會開始從其他人 （或許是管理員的管理員） 取得核准。

![圖表的人為互動模式](./media/durable-functions-concepts/approval.png)

您可以使用協調器函式，在此範例中實作的模式。 使用 orchestrator[永久性計時器](durable-functions-timers.md)以要求核准。 Orchestrator 擴大，如果發生逾時。 Orchestrator 會等到[外部事件](durable-functions-external-events.md)，例如人為互動所產生的通知。

這些範例會建立核准程序來示範的人為互動模式：

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

若要建立長期計時器，請呼叫`context.CreateTimer`(.NET) 或`context.df.createTimer`(JavaScript)。 通知則由 `context.WaitForExternalEvent` (.NET) 或 `context.df.waitForExternalEvent` (JavaScript) 接收。 然後， `Task.WhenAny` (.NET) 或`context.df.Task.any`(JavaScript) 會呼叫以決定是否要向上呈報 （先發生逾時） 還是處理核准 （逾時前收到核准）。

外部用戶端可以藉由使用為等候協調器函式中提供事件通知[內建的 HTTP Api](durable-functions-http-api.md#raise-event)或使用[DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) API另一個函式：

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

## <a name="the-technology"></a>技術

Durable Functions 擴充功能之上的幕後[長期工作架構](https://github.com/Azure/durabletask)，用來建置長期工作協調流程的 GitHub 上的開放原始碼程式庫。 Azure Functions 是 Azure WebJobs 的無伺服器演化，Durable Functions 也是長期工作架構的無伺服器演化。 Microsoft 和其他組織長期工作架構廣泛使用來自動化關鍵程序。 它天生就適合無伺服器的 Azure Functions 環境。

### <a name="event-sourcing-checkpointing-and-replay"></a>事件來源、檢查點和重新執行

協調器函式使用可靠地維持其執行狀態[事件溯源](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)設計模式。 而不是直接儲存協調流程的目前狀態，Durable Functions 擴充功能會使用附加專用存放區記錄完整系列的函式協調流程所採取的動作。 附加專用存放區有相較於 「 傾印 」 完整的執行階段狀態的許多優點。 優點包括更高的效能、 延展性及回應性。 您也可以取得最終一致性的交易資料和完整稽核記錄和歷程記錄。 稽核記錄支援可靠的補償動作。

長期函式會使用事件來源以透明的方式。 在幕後`await`(C#) 或`yield`回給長期工作架構發送器在協調器函式 (JavaScript) 運算子會產生的控制協調器執行緒。 發送器接著會將協調器函式所排程的任何新的動作 (例如，呼叫一或多個子函式或排程長期計時器) 認可至儲存體。 透明認可動作會附加至協調流程執行個體的執行歷程記錄。 歷程記錄會儲存於儲存體資料表。 認可動作接著會將訊息新增至佇列以排程實際的工作。 此時，協調器函式即可從記憶體卸載。 

如果您使用 Azure Functions 取用方案，就會停止計費協調器函式。 當有更多工作來執行，函式會重新啟動，且其狀態會重新建構。

當協調流程函式有更多工作來執行 （例如，收到回應訊息或長期計時器過期），協調器會甦醒，並重新執行整個函式，從開始到重建本機狀態。 

如果呼叫的函式的程式碼會嘗試重新執行期間 (或進行任何其他非同步工作)，「 永久性工作架構會參考目前的協調流程的執行歷程記錄。 如果它發現[活動函式](durable-functions-types-features-overview.md#activity-functions)已經執行並產生結果，便會重新執行該函式的結果，而協調器程式碼會繼續執行。 重新執行會繼續直到完成函式程式碼，或直到它已排定新的非同步工作。

### <a name="orchestrator-code-constraints"></a>協調器程式碼條件約束

協調器程式碼的重新執行行為會建立的協調器函式中，您可以撰寫程式碼的類型條件約束。 例如，協調器程式碼必須具決定性因為它會重新執行多次，但它必須產生相同的結果每次。 條件約束的完整清單，請參閱[協調器程式碼條件約束](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints)。

## <a name="monitoring-and-diagnostics"></a>監視和診斷

Durable Functions 擴充功能自動結構化的追蹤資料發出到[Application Insights](../functions-monitoring.md)如果您使用 Azure Application Insights 檢測金鑰設定您的函式應用程式。 若要監視的動作和您的協調流程的進度，您可以使用追蹤資料。

以下是 Durable Functions 追蹤事件如下所示在 Application Insights 入口網站中使用時的範例[Application Insights Analytics](../../application-insights/app-insights-analytics.md):

![Application Insights 查詢結果](./media/durable-functions-concepts/app-insights-1.png)

您可以找到有用的結構化的資料，在`customDimensions`欄位中每個記錄項目。 以下是完全展開項目的範例：

![在 Application Insights 查詢中的 customDimensions 欄位](./media/durable-functions-concepts/app-insights-2.png)

由於長期工作架構發送器的重新執行行為，您應該會看到重新執行的動作有重複的記錄項目。 重複的記錄項目可協助您了解核心引擎的重新執行行為。 [診斷](durable-functions-diagnostics.md)文章說明可篩選掉重新執行記錄，以便您可以看到 「 即時 」 的記錄查詢範例。

## <a name="storage-and-scalability"></a>儲存體和延展性

Durable Functions 擴充功能使用佇列、 資料表和 blob 在 Azure 儲存體來保存執行歷程記錄的狀態和觸發程序函式執行。 您可以將函數應用程式中，使用預設儲存體帳戶，或您可以設定個別的儲存體帳戶。 您可能需要不同的帳戶在儲存體輸送量限制。 您所撰寫的協調器程式碼不會與這些儲存體帳戶中的實體互動。 長期工作架構會管理實體直接做為實作細節。

協調器函式會排程活動函式，並透過內部佇列訊息收到其回應。 在 Azure Functions 取用方案中，執行的函式應用程式時[Azure Functions 縮放控制器](../functions-scale.md#how-the-consumption-plan-works)監視這些佇列。 視需要加入新的計算執行個體。 調升規模到多個 Vm，可能會在一部 VM，而協調器函式呼叫可能在數個不同的 Vm 執行的活動函式上執行協調器函式。 Durable functions 的縮放行為的相關資訊，請參閱[效能和規模](durable-functions-perf-and-scale.md)。

協調器帳戶的執行歷程記錄會儲存在資料表儲存體。 每當特定 VM 上，解除凍結執行個體，協調器會擷取其執行歷程記錄從資料表儲存體讓它可以重建其本機狀態。 資料表儲存體中提供歷程記錄的便利層面是，您可以使用工具，像是[Azure 儲存體總管](../../vs-azure-tools-storage-manage-with-storage-explorer.md)以查看您的協調流程的歷程記錄。

儲存體 blob 主要用於為租用機制來協調跨多個 Vm 的向外延展的協調流程執行個體。 儲存體 blob 包含無法直接在資料表或佇列中儲存的大型訊息的資料。

![Azure 儲存體總管的螢幕擷取畫面](./media/durable-functions-concepts/storage-explorer.png)

> [!WARNING]
> 雖然很簡單且方便地查看資料表儲存體中的執行歷程記錄，但不要讓此資料表上的任何相依性。 隨著發展而 Durable Functions 擴充功能，可能會變更的資料表。

## <a name="known-issues"></a>已知問題

[GitHub 問題](https://github.com/Azure/azure-functions-durable-extension/issues)清單應可追蹤所有的已知問題。 如果您遇到問題，並在 GitHub 中找不到的問題，請開啟新的問題。 包含問題的詳細的描述。

## <a name="next-steps"></a>後續步驟

若要深入了解 Durable Functions，請參閱[Durable Functions 函式的類型和功能](durable-functions-types-features-overview.md)。 

開始進行之前：

> [!div class="nextstepaction"]
> [建立您的第一個長期函式](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html
