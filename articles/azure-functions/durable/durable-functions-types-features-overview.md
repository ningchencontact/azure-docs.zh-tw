---
title: 函式型別和 Azure Functions 的 Durable Functions 擴充中的功能
description: 了解函式和支援在 Azure Functions 的 Durable Functions 協調流程中的函式至函式通訊的角色類型。
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: azfuncdf
ms.openlocfilehash: de5019e0f91c92829082aed962bb9633da52b4a9
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812836"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>長期函式型別和功能 (Azure Functions)

Durable Functions 也是的延伸模組[Azure Functions](../functions-overview.md)。 您可以使用長期函式的函式執行具狀態協調流程。 長期函式會是個不同的 Azure 函式的解決方案。 函式可以長期函式協調流程中扮演不同的角色。 

這篇文章會提供您可以使用 Durable Functions 協調流程中的函式類型的概觀。 本文會包含一些常見的模式，您可以使用 functions 連接。 了解 Durable Functions 可協助您解決您的應用程式的開發挑戰。

![此圖顯示長期函式的類型][1]  

## <a name="types-of-durable-functions"></a>長期函式類型

您可以在 Azure Functions 中使用四個永久性函式類型： 活動、 orchestrator、 實體和用戶端。

### <a name="activity-functions"></a>活動函式

活動函式是工作的一個永久性函式的協調流程中的基本單位。 活動函式是函式和協調程序中的工作。 例如，您可以建立一個永久性函式來處理訂單。 工作都涉及檢查庫存、 付費客戶，以及建立出貨。 每個工作是活動函式。 

活動函式不可以在其上執行的工作類型的限制。 您可以撰寫活動函式的任何[Durable Functions 支援的語言](durable-functions-overview.md#language-support)。 長期任務架構可確保每個呼叫的活動函式在協調流程期間至少執行一次。

使用[活動觸發程序](durable-functions-bindings.md#activity-triggers)觸發活動函式。 .NET 函式會收到[DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html)做為參數。 您也可以將觸發程式繫結至任何其他物件，以將輸入的資訊傳遞至函式。 在 JavaScript 中，您可以透過輸入`<activity trigger binding name>`上的屬性[`context.bindings`物件](../functions-reference-node.md#bindings)。

您的活動函式也可以將值傳回給協調器。 如果您傳送，或從活動函式會傳回大量的值，您可以使用[tuple 或陣列](durable-functions-bindings.md#passing-multiple-parameters)。 您可以觸發活動函式只能從協調流程執行個體。 雖然活動函式和另一個函式 （例如 HTTP 觸發的函式） 可能會共用一些程式碼，每個函式可以有只有一個觸發程序。

如需詳細資訊和範例，請參閱[活動函式](durable-functions-bindings.md#activity-triggers)。

### <a name="orchestrator-functions"></a>協調器函式

協調器函式會說明如何執行動作，以及執行動作的順序。 協調器函式說明中的程式碼的協調流程 (C#或 JavaScript) 中所示[Durable Functions 模式和技術概念](durable-functions-concepts.md)。 協調流程可以有許多不同類型的動作，包括[活動函式](#activity-functions)，[子協調流程](#sub-orchestrations)，[等待外部事件](#external-events)，以及[計時器](#durable-timers)。 協調器函式也可以與互動[實體函式](#entity-functions)。

協調器函式必須由[協調流程觸發程序](durable-functions-bindings.md#orchestration-triggers)觸發。

啟動協調器[orchestrator 用戶端](#client-functions)。 您可以觸發任何來源 （HTTP、 佇列、 事件資料流） orchestrator。 每個協調流程執行個體具有執行個體識別碼。 執行個體識別碼可以是自動產生 （建議選項），或使用者所產生。 您可以使用的執行個體識別碼[管理執行個體](durable-functions-instance-management.md)協調流程。

如需詳細資訊和範例，請參閱[協調流程觸發程序](durable-functions-bindings.md#orchestration-triggers)。

###  <a name="entity-functions"></a>實體函式 （預覽）

實體函式定義作業讀取及更新小片段狀態，稱為*長期實體*。 協調器函式，例如實體函式函式是特殊的觸發程序型別*實體的觸發程序*。 不同協調器函式中，於實體函式沒有任何特定的程式碼條件約束。 實體函式也會管理狀態明確而不是以隱含方式代表透過控制流程的狀態。

> [!NOTE]
> 實體函式和相關的功能才可用在 Durable Functions 2.0 及更新版本。

如需有關實體函式的詳細資訊，請參閱[實體函式](durable-functions-preview.md#entity-functions)預覽功能文件。

### <a name="client-functions"></a>用戶端函式

用戶端函式會觸發的函式，建立及管理協調流程和實體的執行個體。 它們實際上是 Durable Functions 與互動的進入點。 您可以觸發用戶端函式，從任何來源 (HTTP、 佇列、 事件資料流等。)。 用戶端函式會使用[繫結的協調流程用戶端](durable-functions-bindings.md#orchestration-client)來建立和管理永久性協調流程和實體。

用戶端函式的最基本範例是 HTTP 觸發的函式會啟動協調器函式，然後傳回 核取狀態回應。 如需範例，請參閱[HTTP API URL 探索](durable-functions-http-api.md#http-api-url-discovery)。

如需詳細資訊和範例，請參閱[協調流程用戶端](durable-functions-bindings.md#orchestration-client)。

## <a name="features-and-patterns"></a>功能與模式

下一節中說明的 Durable Functions 類型模式的功能。

### <a name="sub-orchestrations"></a>子協調流程

協調器函式可以呼叫活動函式，但它們也可以呼叫其他協調器函式。 例如，您可以從協調器函式庫中編譯更大的協調流程。 或者，您可以平行執行的協調器函式的多個執行個體。

如需詳細資訊和範例，請參閱[子協調流程](durable-functions-sub-orchestrations.md)。

### <a name="durable-timers"></a>永久性計時器

[Durable Functions](durable-functions-overview.md)提供*永久性計時器*，您可以使用協調器函式中實作延遲，或設定在非同步動作上的逾時。 在協調器函式，而不是使用永久性計時器`Thread.Sleep`並`Task.Delay`(C#) 或`setTimeout()`並`setInterval()`(JavaScript)。

如需詳細資訊和範例，請參閱[永久性計時器](durable-functions-timers.md)。

### <a name="external-events"></a>外部事件

協調器函式可以等待外部事件以更新協調流程執行個體。 此 Durable Functions 功能通常適合用來處理人為互動或其他外部的回呼。

如需詳細資訊和範例，請參閱[外部事件](durable-functions-external-events.md)。

### <a name="error-handling"></a>錯誤處理

您可以使用程式碼來實作 Durable Functions 協調流程。 您可以使用程式設計語言的錯誤處理功能。 模式，例如`try` / `catch`協調流程中的工作。 

Durable Functions 也會隨附內建的重試原則。 動作可以延遲，並自動重試活動時發生例外狀況。 您可以使用重試來處理暫時性例外狀況，而不需要放棄協調流程。

如需詳細資訊和範例，請參閱[錯誤處理](durable-functions-error-handling.md)。

### <a name="cross-function-app-communication"></a>跨函式應用程式通訊

雖然單一函式應用程式的內容中執行長期的協調流程，您可以使用模式來協調跨許多函式應用程式的協調流程。 跨應用程式的通訊可能會發生在 HTTP 上，但每個活動中使用長期架構表示您仍可以跨兩個應用程式維護長期的程序。

下列範例示範如何跨函式應用程式中的協調流程C#和 JavaScript。 在每個範例中，一個活動啟動外部協調流程。 另一個活動會擷取並傳回的狀態。 Orchestrator 會等到狀態變成`Complete`繼續執行之前。

以下是一些跨函式應用程式協調流程的範例：

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

若要開始，建立您的第一個永久性函式，在[ C# ](durable-functions-create-first-csharp.md)或是[JavaScript](quickstart-js-vscode.md)。

> [!div class="nextstepaction"]
> [深入的了解 Durable Functions](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
