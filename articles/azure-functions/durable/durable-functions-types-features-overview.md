---
title: Azure Functions 的 Durable Functions 延伸模組中的函式類型和功能
description: 深入瞭解在 Azure Functions 的 Durable Functions 協調流程中, 支援函式對函式通訊的函數和角色類型。
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 0d3087c768a02bb5c647fc0d10db3aa4274804f4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097747"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Durable Functions 類型和功能 (Azure Functions)

Durable Functions 是[Azure Functions](../functions-overview.md)的延伸模組。 您可以使用 Durable Functions 來執行函式的具狀態協調流程。 長期函式是由不同 Azure 函式所組成的解決方案。 函數可以在長期函式協調流程中扮演不同的角色。 

本文提供您可以在 Durable Functions 協調流程中使用之函數類型的總覽。 本文包含一些可供您用來連接函式的常見模式。 瞭解 Durable Functions 如何協助您解決應用程式開發的挑戰。

![顯示持久性函數類型的影像][1]  

## <a name="types-of-durable-functions"></a>長期函式類型

您可以在 Azure Functions 中使用四個長期函式類型: 活動、orchestrator、實體和用戶端。

### <a name="activity-functions"></a>活動函式

活動函數是長期函式協調流程中的基本工作單位。 活動函式是在進程中協調的函式和工作。 例如, 您可以建立長期函數來處理訂單。 這些工作牽涉到檢查清查、向客戶收費, 以及建立出貨。 每個工作都是活動函式。 

活動函式不受限於您可以在其中執行的工作類型。 您可以使用[Durable Functions 支援的任何語言](durable-functions-overview.md#language-support)來撰寫活動函數。 長期任務架構可確保每個呼叫的活動函式在協調流程期間至少執行一次。

使用[活動觸發](durable-functions-bindings.md#activity-triggers)程式來觸發活動函數。 .NET 函數會接收[DurableActivityCoNtext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html)做為參數。 您也可以將觸發程式繫結至任何其他物件，以將輸入的資訊傳遞至函式。 在 JavaScript 中, 您可以透過[ `context.bindings`物件](../functions-reference-node.md#bindings)上的`<activity trigger binding name>`屬性來存取輸入。

您的活動函數也可以將值傳回協調器。 如果您從活動函數傳送或傳回大量的值, 您可以使用[元組或陣列](durable-functions-bindings.md#passing-multiple-parameters)。 您只能從協調流程實例觸發活動函數。 雖然活動函式和另一個函式 (例如 HTTP 觸發的函數) 可能會共用一些程式碼, 但每個函數只能有一個觸發程式。

如需詳細資訊和範例, 請參閱[活動](durable-functions-bindings.md#activity-triggers)函式。

### <a name="orchestrator-functions"></a>協調器函式

協調器函式會描述動作的執行方式, 以及執行動作的順序。 協調器函式描述程式碼 (C#或 JavaScript) 中的協調流程, 如[Durable Functions 模式和技術概念](durable-functions-concepts.md)中所示。 協調流程可以有許多不同類型的動作, 包括[活動](#activity-functions)函[式、子協調流程](#sub-orchestrations)、[等待外來事件](#external-events)和[計時器](#durable-timers)。 協調器函式也可以與[實體](#entity-functions)函式互動。

協調器函式必須由[協調流程觸發程序](durable-functions-bindings.md#orchestration-triggers)觸發。

Orchestrator[用戶端](#client-functions)會啟動協調器。 您可以從任何來源 (HTTP、佇列、事件串流) 觸發協調器。 協調流程的每個實例都有實例識別碼。 實例識別碼可以自動產生 (建議), 或由使用者產生。 您可以使用實例識別碼來管理協調流程的[實例](durable-functions-instance-management.md)。

如需詳細資訊和範例, 請參閱[協調流程觸發](durable-functions-bindings.md#orchestration-triggers)程式。

###  <a name="entity-functions"></a>實體函式 (預覽)

實體函式會定義讀取和更新部分狀態 (稱為「*持久實體*」) 的作業。 就像協調器函式一樣, 實體函式是具有特殊觸發程式類型 (*實體觸發*程式) 的函數。 與協調器函式不同的是, 實體函式沒有任何特定的程式碼條件約束。 實體函數也會明確管理狀態, 而不是透過控制流程隱含表示狀態。

> [!NOTE]
> 只有 Durable Functions 2.0 和更新版本才提供實體函數和相關功能。

如需實體函式的詳細資訊, 請參閱[實體](durable-functions-preview.md#entity-functions)函式預覽功能檔。

### <a name="client-functions"></a>用戶端函式

用戶端函式是觸發的函式, 可建立和管理協調流程和實體的實例。 它們實際上是與 Durable Functions 互動的進入點。 您可以從任何來源 (HTTP、佇列、事件資料流程等等) 觸發用戶端函式。 用戶端函式會使用[協調流程用戶端](durable-functions-bindings.md#orchestration-client)系結來建立和管理持久的協調流程和實體。

用戶端函式的最基本範例是 HTTP 觸發的函式, 它會啟動協調器函式, 然後傳回檢查狀態回應。 如需範例, 請參閱[HTTP API URL 探索](durable-functions-http-api.md#http-api-url-discovery)。

如需詳細資訊和範例, 請參閱[協調流程用戶端](durable-functions-bindings.md#orchestration-client)。

## <a name="features-and-patterns"></a>功能與模式

下一節將說明 Durable Functions 類型的功能與模式。

### <a name="sub-orchestrations"></a>子協調流程

協調器函式可以呼叫活動函式, 但它們也可以呼叫其他協調器函式。 例如，您可以從協調器函式庫中編譯更大的協調流程。 或者, 您可以平行執行協調器函式的多個實例。

如需詳細資訊和範例, 請參閱[子協調流程](durable-functions-sub-orchestrations.md)。

### <a name="durable-timers"></a>永久性計時器

[Durable Functions](durable-functions-overview.md)提供可在協調器函式中用來執行延遲的*持久計時器*, 或設定非同步動作的超時時間。 使用協調器函式中的`Thread.Sleep`持久性計時器, 而不是和`Task.Delay` (C#) 或`setTimeout()`和`setInterval()` (JavaScript)。

如需詳細資訊和範例, 請參閱[持久計時器](durable-functions-timers.md)。

### <a name="external-events"></a>外部事件

協調器函式可以等待外部事件以更新協調流程執行個體。 這項 Durable Functions 功能通常適用于處理人為互動或其他外部回呼。

如需詳細資訊和範例, 請參閱[外來事件](durable-functions-external-events.md)。

### <a name="error-handling"></a>錯誤處理

使用程式碼來執行 Durable Functions 協調流程。 您可以使用程式設計語言的錯誤處理功能。 在協調`try`流程中工作之類/ `catch`的模式。 

Durable Functions 也隨附內建的重試原則。 動作會在例外狀況發生時, 自動延遲和重試活動。 您可以使用重試來處理暫時性例外狀況, 而不會放棄協調流程。

如需詳細資訊和範例, 請參閱[錯誤處理](durable-functions-error-handling.md)。

### <a name="cross-function-app-communication"></a>跨函式應用程式通訊

雖然長期協調流程會在單一函式應用程式的內容中執行, 但是您可以使用模式來協調多個函式應用程式之間的協調流程。 跨應用程式通訊可能會透過 HTTP 進行, 但針對每個活動使用持久性架構, 表示您仍然可以在兩個應用程式之間維護長期的進程。

下列範例示範和 JavaScript 中C#的跨函式應用程式協調流程。 在每個範例中, 一個活動會啟動外部協調流程。 另一個活動會抓取並傳回狀態。 協調器會`Complete`等候狀態為 [繼續]。

以下是跨函數應用程式協調流程的一些範例:

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration.
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now, go do more work...
}

[FunctionName("StartRemoteOrchestration")]
public static async Task<string> StartRemoteOrchestration([ActivityTrigger] string orchestratorName)
{
    using (var response = await HttpClient.PostAsync(
        $"https://appB.azurewebsites.net/orchestrations/{orchestratorName}",
        new StringContent("")))
    {
        string statusUrl = await response.Content.ReadAsAsync<string>();
        return statusUrl;
    }
}

[FunctionName("CheckIsComplete")]
public static async Task<bool> CheckIsComplete([ActivityTrigger] string statusUrl)
{
    using (var response = await HttpClient.GetAsync(statusUrl))
    {
        // 200 = Complete, 202 = Running
        return response.StatusCode == HttpStatusCode.OK;
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    // Do some work...

    // Call a remote orchestration.
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now, go do more work...
});
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, orchestratorName) {
    const options = {
        method: "POST",
        uri: `https://appB.azurewebsites.net/orchestrations/${orchestratorName}`,
        body: ""
    };

    const statusUrl = await request(options);
    return statusUrl;
};
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, statusUrl) {
    const options = {
        method: "GET",
        uri: statusUrl,
        resolveWithFullResponse: true,
    };

    const response = await request(options);
    // 200 = Complete, 202 = Running
    return response.statusCode === 200;
};
```

## <a name="next-steps"></a>後續步驟

若要開始使用, 請在或[JavaScript](quickstart-js-vscode.md)中[C#](durable-functions-create-first-csharp.md)建立您的第一個持久性函式。

> [!div class="nextstepaction"]
> [閱讀更多有關 Durable Functions 的資訊](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
