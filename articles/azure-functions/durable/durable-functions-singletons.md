---
title: Durable Functions 的單次個體 - Azure
description: 如何在 Azure Functions 的 Durable Functions 擴充中使用單一資料庫。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: c032ba046668310ff71d067d22a805fc6446667c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683810"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 中的單次協調器

對於背景作業，您通常必須確定特定的協調流程一次只執行一個執行個體。 作法就是在 [Durable Functions](durable-functions-overview.md) 中，只要在建立協調器時指派特定的執行個體識別碼即可。

## <a name="singleton-example"></a>單次個體範例

下列 C# 與 JavaScript 範例示範 HTTP 觸發程序函式如何建立單次背景作業協調流程。 程式碼可確保一個指定的執行個體識別碼只存在一個執行個體。

### <a name="c"></a>C#

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

以下是 function.json 檔案：
```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

以下是 JavaScript 程式碼：
```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

根據預設，執行個體識別碼是隨機產生的 GUID。 但在此案例中，執行個體識別碼是從 URL 傳入路由資料中。 程式碼會呼叫 [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) (C#) 或 `getStatus` (JavaScript)，來檢查具有指定識別碼的執行個體是否已在執行中。 如果沒有，則會以該識別碼建立執行個體。

> [!WARNING]
> 以 JavaScript 在本機開發時，您必須將環境變數 `WEBSITE_HOSTNAME` 設定為 `localhost:<port>`，例如 `localhost:7071`，以在 `DurableOrchestrationClient` 上使用方法。 如需此需求的詳細資訊，請參閱 [GitHub 問題](https://github.com/Azure/azure-functions-durable-js/issues/28) \(英文\)。

> [!NOTE]
> 在此範例中有潛在的競爭條件。 如果 **HttpStartSingle** 的兩個實例同時執行，則兩個函式呼叫都將會報告成功，但現在只會實際啟動一個協調流程執行個體。 視您的需求而定，這可能會有非預期的副作用。 基於這個理由，請務必確定不會有兩個要求同時執行此觸發程序函式。

協調器函式的實作細節實際上不重要。 它可能是會啟動並完成的一般協調器函式，也可能是永遠執行的函式 (也就是[永久性協調流程](durable-functions-eternal-orchestrations.md))。 重點是一次只有一個執行個體在執行。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何呼叫子協調流程](durable-functions-sub-orchestrations.md)
