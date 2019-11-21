---
title: Durable Functions 中的永久性協調流程 - Azure
description: 了解如何使用 Azure Functions 的 Durable Functions 擴充來實作永久性協調流程。
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 8d28ae18c44c434dba053b23a60eb78728f8d8e0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232915"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 中的永久性協調流程

「永久協調流程」是指永不結束的協調流程函式。 想要在彙總工具和任何需要無限迴圈的情節下使用 [Durable Functions](durable-functions-overview.md) 時，永久性協調流程就很有用。

## <a name="orchestration-history"></a>協調流程記錄

As explained in the [orchestration history](durable-functions-orchestrations.md#orchestration-history) topic, the Durable Task Framework keeps track of the history of each function orchestration. 只要協調器函式繼續排程新的工作，此記錄就會持續成長。 如果協調器函式進入無限迴圈，並持續排程工作，此記錄可能會變得非常大，而造成明顯的效能問題。 「永久協調流程」概念是設計讓需要無限迴圈的應用程式減少發生這類問題。

## <a name="resetting-and-restarting"></a>重設和重新啟動

Instead of using infinite loops, orchestrator functions reset their state by calling the `ContinueAsNew` (.NET) or `continueAsNew` (JavaScript) method of the [orchestration trigger binding](durable-functions-bindings.md#orchestration-trigger). 此方法接受單一 JSON 序列化參數，此參數會變成新的輸入來產生下一個協調器函式。

呼叫 `ContinueAsNew` 時，執行個體在結束之前會將訊息加入其本身的佇列。 此訊息會以新的輸入值來重新啟動執行個體。 相同的執行個體識別碼會保留下來，但協調器函式的記錄實際上會截斷。

> [!NOTE]
> 「永久性工作架構」會維護相同的執行個體識別碼，但在內部會為 `ContinueAsNew` 所重設的協調器函式建立新的「執行識別碼」。 此執行識別碼通常不對外公開，但可能很助於了解偵錯協調流程執行。

## <a name="periodic-work-example"></a>定期工作範例

需要無限期地執行定期工作的程式碼，即為永久協調流程的一個使用案例。

### <a name="c"></a>C#

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

> [!NOTE]
> The previous C# example is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-20-only"></a>JavaScript (僅限 Functions 2.0)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    context.df.continueAsNew(undefined);
});
```

此範例與計時器觸發函式之間的差異在於，此處的清理觸發程序時間不是根據排程。 例如，每小時執行函式的 CRON 排程會在 1:00、2:00、3:00 等時間執行它，很可能會陷入重疊問題。 不過，在此範例中，如果清除需要 30 分鐘，則會排程在 1:00、2:30、4:00 等時間，不會有機會重疊。

## <a name="starting-an-eternal-orchestration"></a>Starting an eternal orchestration

Use the `StartNewAsync` (.NET) or the `startNew` (JavaScript) method to start an eternal orchestration, just like you would any other orchestration function.  

> [!NOTE]
> If you need to ensure a singleton eternal orchestration is running, it's important to maintain the same instance `id` when starting the orchestration. 如需詳細資訊，請參閱[執行個體管理](durable-functions-instance-management.md)。

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";
    // Null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId, null); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

## <a name="exit-from-an-eternal-orchestration"></a>從永久協調流程離開

如果協調器函式最終一定要完成，則您完全「不」需要呼叫 `ContinueAsNew`，讓函式自然結束即可。

If an orchestrator function is in an infinite loop and needs to be stopped, use the `TerminateAsync` (.NET) or `terminate` (JavaScript) method of the [orchestration client binding](durable-functions-bindings.md#orchestration-client) to stop it. 如需詳細資訊，請參閱[執行個體管理](durable-functions-instance-management.md)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何實作單次協調流程](durable-functions-singletons.md)
