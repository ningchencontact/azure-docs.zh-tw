---
title: 在 Durable Functions 中處理錯誤 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 擴充中處理錯誤。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 33d1b410119e631e0ccc9941beac1062d4ec30f9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087338"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>在 Durable Functions (Azure Functions) 中處理錯誤

Durable Function 協調流程在程式碼中實作，而且可以使用程式設計語言的錯誤處理功能。 記住這一點之後, 您不需要瞭解在協調流程中納入錯誤處理和補償的任何新概念。 不過，有一些行為值得注意。

## <a name="errors-in-activity-functions"></a>活動函式中的錯誤

活動函式中擲回的任何例外狀況會封送處理回到協調器函式，並以 `FunctionFailedException` 擲回。 您可以在協調器函式中撰寫符合需求的錯誤處理和補償程式碼。

例如，假設有下列協調器函式會從一個帳戶轉帳到另一個帳戶：

### <a name="c"></a>C#

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(DurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```

如果對目的地帳戶呼叫 **CreditAccount** 函式失敗，協調器函式會將款項匯回到來源帳戶，以彌補此情況。

## <a name="automatic-retry-on-failure"></a>失敗時自動重試

當您呼叫活動函式或子協調流程函式時，您可以指定自動重試原則。 下列範例會嘗試呼叫函式最多 3 次，並於每次重試之間等待 5 秒鐘：

### <a name="c"></a>C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

`CallActivityWithRetryAsync` (.NET) 或 `callActivityWithRetry` (JavaScript) API 會接受 `RetryOptions` 參數。 子協調流程呼叫如果使用 `CallSubOrchestratorWithRetryAsync` (.NET) 或 `callSubOrchestratorWithRetry` (JavaScript) API，便可使用這些相同的重試原則。

自訂自動重試原則有幾個選項。 其中包括：

* **重試次數上限**：重試嘗試的次數上限。
* **第一次重試間隔**：第一次重試嘗試之前等候的時間長度。
* **輪詢係數**：用來決定輪詢增加速率的係數。 預設值為 1。
* **重試間隔上限**：重試嘗試之間等候的時間長度上限。
* **重試逾時**：花費在重試的時間長度上限。 預設行為是無限期地重試。
* **控制代碼**：您可以指定使用者定義的回呼，以決定是否應該重試函式呼叫。

## <a name="function-timeouts"></a>函式逾時

如果協調器函式內的函式呼叫太久才會完成，您可以放棄此函式呼叫。 目前，適當的做法是使用 `context.CreateTimer` (.NET) 或 `context.df.createTimer` (JavaScript) 搭配 `Task.WhenAny` (.NET) 或 `context.df.Task.any` (JavaScript) 來建立[持久性計時器](durable-functions-timers.md)，如下列範例所示：

### <a name="c"></a>C#

```csharp
public static async Task<bool> Run(DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```

> [!NOTE]
> 這項機制並不會實際終止進行中的活動函式執行。 只是讓協調器函式略過結果並繼續執行。 如需詳細資訊，請參閱[計時器](durable-functions-timers.md#usage-for-timeout)文件。

## <a name="unhandled-exceptions"></a>未處理的例外狀況

如果協調器函式失敗並傳回未處理的例外狀況，例外狀況的詳細資料會記錄下來，而在執行個體會以 `Failed` 狀態結束。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何診斷問題](durable-functions-diagnostics.md)
