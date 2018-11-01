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
ms.date: 08/31/2018
ms.author: azfuncdf
ms.openlocfilehash: bcb87b3030eb673dd3c9a8b93a045c75fae964a9
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087027"
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>在 Durable Functions (Azure Functions) 中管理執行個體

您可以啟動、終止、查詢和傳送通知事件給 [Durable Functions](durable-functions-overview.md) 協調流程執行個體。 執行個體管理完全是透過[協調流程用戶端繫結](durable-functions-bindings.md)來進行。 本文討論每個執行個體管理作業的詳細資料。

## <a name="starting-instances"></a>啟動執行個體

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 的 [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) 方法可啟動協調器函式的新執行個體。 您可以使用 `orchestrationClient` 繫結來取得此類別的執行個體。 在內部，此方法會將訊息加入控制佇列，然後就會利用 `orchestrationTrigger` 觸發程序繫結，觸發啟動具有指定名稱的函式。 

工作會在協調程序啟動時完成。 協調程序應在 30 秒內啟動。 如果花費更久的時間，就會擲回 `TimeoutException`。 

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) 的參數如下：

* **Name**：要排程的協調器函式的名稱。
* **Input**：應該當作輸入傳給協調器函式的任何 JSON 可序列化資料。
* **InstanceId**：(選擇性) 執行個體的唯一識別碼。 如果未指定，將會產生隨機的執行個體識別碼。

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

使用非 .NET 語言時，函式輸出繫結也可以用來啟動新的執行個體。 在此情況下，有上述三個參數作為欄位的任何 JSON 可序列化物件都可使用。 例如，假設有下列 JavaScript 函式：

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```
上述程式碼假設您已在 function.json 檔案中定義名稱為 "starter" 和類型為"orchestrationClient" 的輸出繫結。 如果未定義此繫結，則不會建立耐久函式執行個體。

針對所要叫用的耐久函式，應修改 function.json 才能有協調流程用戶端的繫結 (如下所述)

```js
{
    "bindings": [{
        "name":"starter",
        "type":"orchestrationClient",
        "direction":"out"
    }]
}
```

> [!NOTE]
> 我們建議您使用隨機識別碼作為執行個體識別碼。 在將協調器函式擴展至多個虛擬機器時，這就有助於確保均等分散負載。 當識別碼必須來自外部來源，或實作[單次協調器](durable-functions-singletons.md)模式時，才是使用非隨機執行個體識別碼的適當時機。

## <a name="querying-instances"></a>查詢執行個體

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 類別的 [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) 方法可查詢協調流程執行個體的狀態。 它會以 `instanceId` (必要)、`showHistory` (選用) 和 `showHistoryOutput` (選用) 作為參數。 如果 `showHistory` 設為 `true`，回應將會包含執行記錄。 如果 `showHistoryOutput` 設為 `true`，則執行記錄將會包含活動輸出。 此方法會傳回具有下列屬性的物件：

* **Name**：協調器函式的名稱。
* **InstanceId**：協調流程的執行個體識別碼 (應該與 `instanceId` 輸入相同)。
* **CreatedTime**：協調器函式開始執行的時間。
* **LastUpdatedTime**：協調流程前次執行檢查點檢查的時間。
* **Input**：函式的 JSON 值輸入。
* **CustomStatus**：JSON 格式的自訂協調流程狀態。 
* **Output**：函式的 JSON 值輸出 (如果函式已完成)。 如果協調器函式失敗，此屬性會包含失敗詳細資料。 如果協調器函式終止，此屬性會包含提供的終止原因 (如果有的話)。
* **RuntimeStatus**：下列其中一個值：
    * **擱置**：已排程的執行個體尚未開始執行。
    * **Running**：執行個體已開始執行。
    * **Completed**：執行個體已正常完成。
    * **ContinuedAsNew**：執行個體本身以新的記錄重新啟動。 這是暫時性的狀態。
    * **Failed**：函式失敗，發生錯誤。
    * **Terminated**：執行個體突然終止。
* **歷程記錄**：協調流程的執行記錄。 只有在 `showHistory` 設為 `true` 時，才會填入此欄位。
    
如果執行個體不存在或尚未開始執行，這個方法會傳回 `null`。

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
## <a name="querying-all-instances"></a>查詢所有執行個體

您可以使用 `GetStatusAsync` 方法來查詢所有協調流程執行個體的狀態。 此方法不採用任何參數，或者，您可以在想要加以取消時傳入 `CancellationToken` 物件。 此方法會和使用參數的 `GetStatusAsync`方法一樣傳回具有相同屬性的物件，但不會傳回歷程記錄。 

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```
## <a name="querying-instances-with-filters"></a>利用篩選條件查詢執行個體

您也可以使用 `GetStatusAsync` 方法來取得符合一組預先定義的篩選條件的協調流程執行個體清單。 可能的篩選選項包括協調流程建立時間和協調流程執行階段狀態。

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

## <a name="terminating-instances"></a>終止執行個體

您可以使用 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 類別的 [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) 方法來終止執行中的協調流程執行個體。 兩個參數是 `instanceId` 和 `reason` 字串，將會寫入記錄和執行個體狀態中。 終止的執行個體會在到達下一個 `await` 點時就停止執行，但如果已處於 `await`，則會立即終止。 

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

> [!NOTE]
> 執行個體終止目前未傳播。 無論呼叫活動函式和子協調流程的協調流程執行個體是否已終止，這些活動函式和子協調流程皆會執行到完成為止。

## <a name="sending-events-to-instances"></a>將事件傳送至執行個體

您可以使用 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 類別的 [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) 方法將事件通知傳送至執行中的執行個體。 正在等候呼叫 [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) 的執行個體可以處理這些事件。 

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) 的參數如下：

* **InstanceId**：執行個體的唯一識別碼。
* **EventName**：要傳送的事件名稱。
* **EventData**：要傳送至執行個體的 JSON 可序列化裝載。

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

> [!WARNING]
> 如果沒有協調流程執行個體具有指定的「執行個體識別碼」，或執行個體並未等候指定的「事件名稱」，則會捨棄事件訊息。 如需這個行為的詳細資訊，請參閱 [GitHub 問題](https://github.com/Azure/azure-functions-durable-extension/issues/29)。

## <a name="wait-for-orchestration-completion"></a>等候協調流程完成

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 類別會公開 [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API，可用來從協調流程執行個體以同步方式取得實際的輸出。 此方法在 `timeout` 和 `retryInterval` 皆未設定時，會分別使用其預設值 10 秒和 1 秒。  

以下是範例 HTTP 觸發函式，它會示範如何使用這個 API：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

您可以在下列命令列中使用 2 秒的逾時和 0.5 秒的重試間隔呼叫此函式：

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

取決於從協調流程執行個體取得回應所需的時間，會有兩種情況：

1. 協調流程執行個體在定義的逾時 (在此案例中為 2 秒) 內完成，回應是以同步方式傳遞的實際協調流程執行個體輸出：

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

2. 協調流程執行個體無法在定義的逾時 (在此案例中為 2 秒) 內完成，回應是在 **HTTP API URL 探索**中說明的預設值一：

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
> Webhook URL 的格式依據您執行的 Azure Functions 主機版本，可能會有所不同。 上述範例適用於 Azure Functions 2.0 主機。

## <a name="retrieving-http-management-webhook-urls"></a>擷取 HTTP 管理 Webhook URL

外部系統可透過 [HTTP API URL 探索](durable-functions-http-api.md)中說明的預設回應所包含的 Webhook URL 與長期函式通訊。 不過，您也可透過 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 類別的 [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) 方法，以程式設計方式在協調流程用戶端或活動函式中存取 Webhook URL。 

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) 有一個參數：

* **instanceId**：執行個體的唯一識別碼。

此方法會傳回具有下列字串屬性的 [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) 執行個體：

* **Id**：協調流程的執行個體識別碼 (應該與 `InstanceId` 輸入相同)。
* **StatusQueryGetUri**：協調流程執行個體的狀態 URL。
* **SendEventPostUri**：協調流程執行個體的「引發事件」URL。
* **TerminatePostUri**：協調流程執行個體的「終止」URL。
* **RewindPostUri**：協調流程執行個體的「倒轉」URL。

活動函式可以將 [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) 的執行個體傳送至外部系統，以監視或引發協調流程的事件：

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

## <a name="rewinding-instances-preview"></a>倒轉執行個體 (預覽)

使用 [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) API，可以將失敗的協調流程執行個體「倒轉」至先前狀況良好的狀態。 其運作方式是讓協調流程回到「執行中」狀態，然後重新執行此活動和/或導致協調流程失敗的子協調流程執行失敗。

> [!NOTE]
> 此 API 並非要取代適當的錯誤處理和重試原則。 相反地，它只是要用於協調流程執行個體因非預期原因而失敗的情況。 如需錯誤處理和重試原則的詳細資訊，請參閱[錯誤處理](durable-functions-error-handling.md)主題。

「倒轉」的其中一個範例使用案例為涉及一系列[人為核准](durable-functions-overview.md#pattern-5-human-interaction)的工作流程。 假設有一系列的活動函式，其可通知某人需要其核准並等待即時回應。 在所有核准活動已收到回應或逾時之後，另一個活動會因為應用程式設定錯誤 (例如資料庫連接字串無效) 而失敗。 結果就是深入工作流程的協調流程失敗。 透過 `RewindAsync` API，應用程式系統管理員可以修正設定錯誤，以及讓失敗的協調流程「倒轉」回到失敗之前的狀態。 不需要重新核准任何人為互動步驟，協調流程現在可以順利完成。

> [!NOTE]
> 「倒轉」功能不支援使用耐久計時器的倒轉協調流程執行個體。

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

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何使用 HTTP API 來管理執行個體](durable-functions-http-api.md)
