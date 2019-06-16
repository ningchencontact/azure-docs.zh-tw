---
title: Durable Functions 的子協調流程 - Azure
description: 如何在 Azure Functions 的 Durable Functions 擴充中，從協調流程呼叫協調流程。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 1ab9a5714a7ef24b51957bd48b1b67240cf13adb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60730237"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) 中的子協調流程

除了呼叫活動函式，協調器函式還可以呼叫其他協調器函式。 例如，您可以從協調器函式庫中編譯更大的協調流程。 或者，也可以平行執行協調器函式的多個執行個體。

協調器函式可以在 .NET 中呼叫 [CallSubOrchestratorAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorAsync_) 或 [CallSubOrchestratorWithRetryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallSubOrchestratorWithRetryAsync_) 方法，或是在 JavaScript 中呼叫 `callSubOrchestrator` 或 `callSubOrchestratorWithRetry` 方法，來呼叫另一個協調器函式。 [錯誤處理和補償](durable-functions-error-handling.md#automatic-retry-on-failure)一文提供自動重試的詳細資訊。

從呼叫端的觀點來看，子協調器函式的行為就像活動函式一樣。 子協調器函式可以傳回值、擲回例外狀況，還可以由父代協調器函式來等候。

## <a name="example"></a>範例

下列範例說明 IoT (物聯網) 情節，其中有多個需要佈建的裝置。 有一個特別的協調流程是每個裝置都需要進行，看起來可能如下所示：

### <a name="c"></a>C#

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

這個協調器函式可直接用於一次性裝置佈建，也可以當作更大協調流程的一部分。 在後者的情況下，父協調器函式可以使用 `CallSubOrchestratorAsync` (C#) 或 `callSubOrchestrator` (JavaScript) API 來排定 `DeviceProvisioningOrchestration` 的執行個體。

以下示範如何平行執行多個協調器函式。

### <a name="c"></a>C#

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    for (const deviceId of deviceIds) {
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.push(provisionTask);
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解工作中樞是什麼及如何設定](durable-functions-task-hubs.md)
