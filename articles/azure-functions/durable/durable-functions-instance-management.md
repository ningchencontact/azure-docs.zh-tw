---
title: 在 Durable Functions 中管理執行個體 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 擴充中管理執行個體。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 0df6f5f9728a8e48a3257e56ddf8ad23906dc92c
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933324"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>在 Azure 中管理 Durable Functions 中的執行個體

如果您使用 Azure Functions 的[Durable Functions](durable-functions-overview.md)延伸模組，或想要開始這麼做，請確定您已充分利用它。 您可以深入瞭解如何管理 Durable Functions 協調流程實例，藉此將其優化。 本文討論每個執行個體管理作業的詳細資料。

例如，您可以啟動和終止實例，而且可以查詢實例，包括使用篩選準則查詢所有實例和查詢實例的能力。 此外，您可以將事件傳送至實例、等候協調流程完成，並取出 HTTP 管理 webhook Url。 本文也涵蓋其他管理作業，包括倒帶實例、清除實例歷程記錄，以及刪除工作中樞。

在 Durable Functions 中，您可以選擇要如何執行每個管理作業。 本文提供的範例會使用 .NET（C#）和 JavaScript 的 [Azure Functions Core Tools](../functions-run-local.md)。

## <a name="start-instances"></a>啟動實例

務必要能夠啟動協調流程的實例。 當您在另一個函式的觸發程式中使用 Durable Functions 系結時，通常會進行這項工作。

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) （.net） `startNew` `DurableOrchestrationClient`或（JavaScript）上的[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)方法會啟動新的實例。 您可以使用`orchestrationClient`系結來取得這個類別的實例。 在內部，此方法會將訊息加入控制佇列，然後就會利用 `orchestrationTrigger` 觸發程序繫結，觸發啟動具有指定名稱的函式。

協調流程程序已成功排定時，此非同步作業會完成。 協調程序應在 30 秒內啟動。 如果需要較長的時間，您會`TimeoutException`看到。

### <a name="net"></a>.NET

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) 的參數如下：

* **Name**：要排程的協調器函式的名稱。
* **輸入**：應該當作輸入傳給協調器函式的任何 JSON 可序列化資料。
* **InstanceId**：(選擇性) 執行個體的唯一識別碼。 如果您未指定此參數，此方法會使用隨機識別碼。

以下是一些範例：

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

`startNew` 的參數如下所示：

* **Name**：要排程的協調器函式的名稱。
* **InstanceId**：(選擇性) 執行個體的唯一識別碼。 如果您未指定此參數，此方法會使用隨機識別碼。
* **輸入**：(選擇性) 應該當作輸入傳給協調器函式的任何 JSON 可序列化資料。

以下是簡單的 JavaScript 範例：

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!TIP]
> 使用隨機識別碼作為執行個體識別碼。 當您在多個 Vm 之間調整協調器功能時，這有助於確保相等的負載分配。 使用非隨機實例識別碼的適當時間是當識別碼必須來自外部來源，或當您要執行[單一協調](durable-functions-singletons.md)器模式時。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

您也可以使用[Azure Functions Core Tools](../functions-run-local.md) `durable start-new`命令，直接啟動實例。 它需要以下參數：

* **`function-name` (必要)** ：要啟動的函式名稱。
* **`input` (選用)** ：以內嵌方式或透過 JSON 檔案輸入至函式。 針對檔案，請將前置詞新增至檔案的路徑`@`， `@path/to/file.json`例如。
* **`id` (選用)** ：協調流程執行個體的識別碼。 如果您未指定此參數，此命令會使用隨機的 GUID。
* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 AzureWebJobsStorage。
* **`task-hub-name` (選用)** ：要使用 Durable Functions 任務中樞的名稱。 預設為 DurableFunctionsHub。 您也可以使用 durableTask： HubName 在[host](durable-functions-bindings.md#host-json)中設定此項。

> [!NOTE]
> 核心工具命令會假設您是從函式應用程式的根目錄執行它們。 如果您明確提供`connection-string-setting`和`task-hub-name`參數，您可以從任何目錄執行命令。 雖然您可以在不執行函式應用程式主機的情況下執行這些命令，但您可能會發現除非主機正在執行，否則您無法觀察到某些效果。 例如， `start-new`命令會將開始訊息將到目標工作中樞，但除非有執行的函式應用程式主機進程可以處理訊息，否則不會實際執行協調流程。

下列命令會啟動名為 HelloWorld 的函式，並將檔案的內容`counter-data.json`傳遞給它：

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>查詢實例

在您管理協調流程的過程中，您很可能需要收集有關協調流程實例狀態的資訊（例如，它是否已正常完成或失敗）。

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 類別 (.NET) 的 [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) 方法或 `DurableOrchestrationClient` 類別 (JavaScript) 的 `getStatus` 方法可查詢協調流程執行個體的狀態。

它會以 `instanceId` (必要)、`showHistory` (選用)、`showHistoryOutput` (選用) 和 `showInput` (選用，僅限 .NET) 作為參數。

* **`showHistory`** ：如果設定為`true`，回應就會包含執行歷程記錄。
* **`showHistoryOutput`** ：如果設定為`true`，則執行歷程記錄會包含活動輸出。
* **`showInput`** ：如果設定為`false`，回應將不會包含函數的輸入。 預設值為 `true`。 (僅限 .NET)

此方法會傳回具有下列屬性的 JSON 物件：

* **Name**：協調器函式的名稱。
* **InstanceId**：協調流程的執行個體識別碼 (應該與 `instanceId` 輸入相同)。
* **CreatedTime**：協調器函式開始執行的時間。
* **LastUpdatedTime**：協調流程前次執行檢查點檢查的時間。
* **輸入**：函式的 JSON 值輸入。 如果`showInput`為 false，則不會填入此欄位。
* **CustomStatus**：JSON 格式的自訂協調流程狀態。
* **輸出**：函式的 JSON 值輸出 (如果函式已完成)。 如果協調器函式失敗，此屬性會包含失敗詳細資料。 如果協調器函式已終止，此屬性會包含終止的原因（如果有的話）。
* **RuntimeStatus**：下列其中一個值：
  * **Pending**：已排程的執行個體尚未開始執行。
  * **Running**：執行個體已開始執行。
  * **Completed**：執行個體已正常完成。
  * **ContinuedAsNew**：執行個體本身以新的記錄重新啟動。 這是暫時性的狀態。
  * **Failed**：函式失敗，發生錯誤。
  * **Terminated**：執行個體已突然停止。
* **History**：協調流程的執行記錄。 只有在 `showHistory` 設為 `true` 時，才會填入此欄位。

如果執行個體不存在或尚未開始執行，這個方法會傳回 `null`。

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

您也可以使用[Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status`命令，直接取得協調流程實例的狀態。 它需要以下參數：

* **`id` (必要)** ：協調流程執行個體的識別碼。
* **`show-input` (選用)** ：如果設定為`true`，回應就會包含函式的輸入。 預設值為 `false`。
* **`show-output` (選用)** ：如果設定為`true`，回應會包含函式的輸出。 預設值為 `false`。
* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)** ：要使用 Durable Functions 任務中樞的名稱。 預設為 `DurableFunctionsHub`。 您也可以使用 durableTask： HubName，在[host. json](durable-functions-bindings.md#host-json)中設定它。

下列命令會抓取協調流程實例識別碼為0ab8c55a66644d68a3a8b220b12d209c 之實例的狀態（包括輸入和輸出）。 它假設您是`func`從函式應用程式的根目錄執行命令：

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

您可以使用`durable get-history`命令來取出協調流程實例的歷程記錄。 它需要以下參數：

* **`id` (必要)** ：協調流程執行個體的識別碼。
* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)** ：要使用 Durable Functions 任務中樞的名稱。 預設為 `DurableFunctionsHub`。 您也可以使用 durableTask： HubName，在 host. json 中設定它。

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>查詢所有實例

不是一次查詢協調流程中的一個實例，您可能會發現一次查詢所有專案會更有效率。

您可以使用 `GetStatusAsync` (.NET) 或 `getStatusAll` (JavaScript) 方法來查詢所有協調流程執行個體的狀態。 在 .net 中，您可以在`CancellationToken`想要取消物件時傳遞物件。 此方法會和使用參數的 `GetStatusAsync`方法一樣傳回具有相同屬性的物件。

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

您也可以使用[Azure Functions Core Tools](../functions-run-local.md) `durable get-instances`命令直接查詢實例。 它需要以下參數：

* **`top` (選用)** ：這個命令支援分頁。 此參數會對應至每個要求擷取的執行個體數目。 預設值為 10。
* **`continuation-token` (選用)** ：Token，指出要取得的實例頁面或區段。 每個 `get-instances` 執行都會將權杖傳回給下一組執行個體。
* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)** ：要使用 Durable Functions 任務中樞的名稱。 預設為 `DurableFunctionsHub`。 您也可以使用 durableTask： HubName，在[host. json](durable-functions-bindings.md#host-json)中設定它。

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>具有篩選的查詢實例

如果您不需要標準實例查詢可以提供的所有資訊，該怎麼辦？ 例如，如果您只是要尋找協調流程建立時間或協調流程執行時間狀態呢？ 您可以藉由套用篩選來縮小查詢範圍。

使用（.net）或`getStatusBy` （JavaScript）方法來取得符合一組預先定義之篩選準則的協調流程實例清單。 `GetStatusAsync`

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

在 Azure Functions Core Tools 中，您也可以搭配使用`durable get-instances`命令與篩選準則。 除了`top`上述`runtime-status`的`continuation-token` `created-after` `created-before`、 `connection-string-setting` 、和參數之外，您還可以使用三個篩選參數（`task-hub-name` 、和）。

* **`created-after` (選用)** ：擷取這個日期/時間 (UTC) 之後建立的執行個體。 可接受 ISO 8601 格式的日期時間。
* **`created-before` (選用)** ：擷取這個日期/時間 (UTC) 之後建立的執行個體。 可接受 ISO 8601 格式的日期時間。
* **`runtime-status` (選用)** ：使用特定狀態（例如，執行中或已完成）來抓取實例。 可以提供多個狀態 (以空格分隔)。
* **`top` (選用)** ：每個要求擷取的執行個體數目。 預設值為 10。
* **`continuation-token` (選用)** ：Token，指出要取得的實例頁面或區段。 每個 `get-instances` 執行都會將權杖傳回給下一組執行個體。
* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)** ：要使用 Durable Functions 任務中樞的名稱。 預設為 `DurableFunctionsHub`。 您也可以使用 durableTask： HubName，在[host. json](durable-functions-bindings.md#host-json)中設定它。

如果您未提供任何篩選（`created-after`、 `created-before`或`runtime-status`），此命令只`top`會抓取實例，而不考慮執行時間狀態或建立時間。

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>終止實例

如果您的協調流程實例執行時間太長，或只是因為任何原因而需要停止它，您可以選擇將它終止。

您可以使用[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)類別（.net）的`terminate` [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)方法或`DurableOrchestrationClient`類別（JavaScript）的方法。 這兩個參數是`instanceId` `reason`和字串，會寫入記錄和實例狀態。 終止的實例會在到達下`await`一個（.net）或`yield` （JavaScript）點時立即停止執行，如果已在`await`或`yield`上，則會立即終止。

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> 實例終止目前不會傳播。 活動函式和子協調流程會執行到完成，不論您是否已結束通話它們的協調流程實例。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

您也可以使用[Azure Functions Core Tools](../functions-run-local.md) `durable terminate`命令，直接終止協調流程實例。 它需要以下參數：

* **`id` (必要)** ：要終止之協調流程實例的識別碼。
* **`reason` (選用)** ：終止的原因。
* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)** ：要使用 Durable Functions 任務中樞的名稱。 預設為 `DurableFunctionsHub`。 您也可以使用 durableTask： HubName，在[host. json](durable-functions-bindings.md#host-json)中設定它。

下列命令會終止識別碼為0ab8c55a66644d68a3a8b220b12d209c 的協調流程實例：

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>將事件傳送至實例

在某些情況下，您的協調器函式必須能夠等候並接聽外來事件，這是很重要的。 這包括等候[人為互動](durable-functions-overview.md#human)的[監視](durable-functions-overview.md#monitoring)函式和函式。

使用[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)類別（.net `raiseEvent` ）的[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)方法或`DurableOrchestrationClient`類別（JavaScript）的方法，將事件通知傳送至執行中的實例。 正在等候呼叫 [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) 或 `waitForExternalEvent` (JavaScript) 的執行個體可以處理這些事件。

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) 和 `raiseEvent` (JavaScript) 的參數如下：

* **InstanceId**：執行個體的唯一識別碼。
* **EventName**：要傳送的事件名稱。
* **EventData**：要傳送至執行個體的 JSON 可序列化裝載。

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!IMPORTANT]
> 如果沒有具有指定之實例識別碼的協調流程實例，或如果實例不是在等候指定的事件名稱，則會捨棄事件訊息。 如需這個行為的詳細資訊，請參閱 [GitHub 問題](https://github.com/Azure/azure-functions-durable-extension/issues/29)。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

您也可以使用[Azure Functions Core Tools](../functions-run-local.md) `durable raise-event`命令，直接將事件引發至協調流程實例。 它需要以下參數：

* **`id` (必要)** ：協調流程執行個體的識別碼。
* **`event-name` (選用)** ：所要引發事件的名稱。 預設為 `$"Event_{RandomGUID}"`。
* **`event-data` (選用)** ：要傳送至協調流程執行個體的資料。 這可以是 JSON 檔案的路徑，或者您可以直接在命令列上提供資料。
* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)** ：要使用 Durable Functions 任務中樞的名稱。 預設為 `DurableFunctionsHub`。 您也可以使用 durableTask： HubName，在[host. json](durable-functions-bindings.md#host-json)中設定它。

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>等候協調流程完成

在長時間執行的協調流程中，您可能會想要等候並取得協調流程的結果。 在這些情況下，可以在協調流程上定義超時時間也很有用。 如果超過超時時間，則應該傳回協調流程的狀態，而不是結果。

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)類別會在 .net 中公開[WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API。 您可以使用這個 API，以同步方式取得協調流程實例的實際輸出。 在 JavaScript 中，`DurableOrchestrationClient` 類別會公開 `waitForCompletionOrCreateCheckStatusResponse`API 達成相同目的。 若未設定，則方法會針對`timeout`使用預設值10秒，並為`retryInterval`1 秒。  

以下是範例 HTTP 觸發函式，它會示範如何使用這個 API：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

使用下列程式程式碼呼叫函數。 在 [重試間隔] 中，針對 [超時] 和 [0.5 秒] 使用2秒：

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

取決於從協調流程執行個體取得回應所需的時間，會有兩種情況：

* 協調流程實例會在定義的時間內完成（在此案例中為2秒），而回應會是以同步方式傳遞的實際協調流程實例輸出：

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

* 協調流程實例無法在定義的時間內完成，而且回應是[HTTP API URL 探索](durable-functions-http-api.md)中所述的預設值：

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
> Webhook Url 的格式可能會不同，視您執行的 Azure Functions 主機版本而定。 上述範例適用於 Azure Functions 2.x 主機。

## <a name="retrieve-http-management-webhook-urls"></a>取出 HTTP 管理 webhook Url

您可以使用外部系統來監視或引發事件至協調流程。 外部系統可以透過 webhook Url 與 Durable Functions 通訊，這是[HTTP API URL 探索](durable-functions-http-api.md)中所述之預設回應的一部分。 不過，您也可以在協調流程用戶端或活動函式中以程式設計方式存取 webhook Url。 若要這麼做`createHttpManagementPayload` ，請使用[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)類別（.net）的[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_)方法或`DurableOrchestrationClient`類別（JavaScript）的方法。

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) 和 `createHttpManagementPayload` 有一個參數：

* **instanceId**：執行個體的唯一識別碼。

方法會傳回[HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) （.net）或物件（JavaScript）的實例，並具有下列字串屬性：

* **Id**：協調流程的執行個體識別碼 (應該與 `InstanceId` 輸入相同)。
* **StatusQueryGetUri**：協調流程執行個體的狀態 URL。
* **SendEventPostUri**：協調流程執行個體的「引發事件」URL。
* **TerminatePostUri**：協調流程執行個體的「終止」URL。
* **RewindPostUri**：協調流程執行個體的「倒轉」URL。

活動函式可以將這些物件的執行個體傳送至外部系統，以監視或引發協調流程的事件：

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

## <a name="rewind-instances-preview"></a>倒轉實例（預覽）

如果您因為非預期的原因而發生協調流程失敗，*您可以使用*針對該目的所建立的 API，將實例倒轉成先前狀況良好的狀態。

> [!NOTE]
> 此 API 並非要取代適當的錯誤處理和重試原則。 相反地，它只是要用於協調流程執行個體因非預期原因而失敗的情況。 如需有關錯誤處理和重試原則的詳細資訊，請參閱[錯誤處理](durable-functions-error-handling.md)主題。

使用[RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) （.net）或`rewindAsync` （JavaScript） API，讓協調流程回到執行中狀態 *。* 重新執行導致協調流程失敗的活動或子協調流程執行失敗。

例如，假設您有一個工作流程牽涉到一系列[人工核准](durable-functions-overview.md#human)。 假設有一系列的活動功能會通知某人需要核准，並等候即時回應。 當所有核准活動都收到回應或超時之後，假設另一個活動因為應用程式設定錯誤而失敗，例如不正確資料庫連接字串。 結果就是深入工作流程的協調流程失敗。 使用（.net）或`rewindAsync` （JavaScript） API，應用程式系統管理員可以修正設定錯誤，並將失敗的協調流程回溯回到失敗前的狀態。 `RewindAsync` 不需要 reapproved 任何人為互動步驟，協調流程現在可以順利完成。

> [!NOTE]
> 倒轉*功能不*支援使用持久計時器的倒帶協調流程實例。

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

您也可以使用[Azure Functions Core Tools](../functions-run-local.md) `durable rewind`命令，直接倒轉協調流程實例。 它需要以下參數：

* **`id` (必要)** ：協調流程執行個體的識別碼。
* **`reason` (選用)** ：倒帶協調流程實例的原因。
* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)** ：要使用 Durable Functions 任務中樞的名稱。 預設為 `DurableFunctionsHub`。 您也可以使用 durableTask： HubName，在[host. json](durable-functions-bindings.md#host-json)中設定它。

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>清除實例歷程記錄

若要移除與協調流程相關聯的所有資料，您可以清除實例歷程記錄。 例如，您可能想要清除 Azure 資料表資料列和大型訊息 blob （如果有的話）。 若要這麼做，請使用[PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) API。

> [!NOTE]
> `PurgeInstanceHistoryAsync` API 目前僅適用於 C#。

 此方法有兩個多載。 第一個會依據協調流程實例的識別碼來清除歷程記錄：

### <a name="c"></a>C#

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

第二個範例會顯示計時器觸發的函式，它會清除在指定時間間隔之後完成的所有協調流程執行個體記錄。 在這種情況下，它會移除所有實例在30天之前完成的資料。 其排程為每天上午12點執行一次：

### <a name="c"></a>C#

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
                    DateTime.MinValue,
                    DateTime.UtcNow.AddDays(-30),  
                    new List<OrchestrationStatus>
                    {
                        OrchestrationStatus.Completed
                    });
}
```

> [!NOTE]
> 若要讓時間觸發函式進程成功，執行時間狀態必須是 [**已完成**]、[已**終止**] 或 [**失敗**]。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

您可以使用[Azure Functions Core Tools](../functions-run-local.md) `durable purge-history`命令來清除協調流程實例的歷程記錄。 與上一節C#中的第二個範例類似，它會清除在指定的時間間隔期間所建立之所有協調流程實例的歷程記錄。 您可以依執行時間狀態進一步篩選已清除的實例。 此命令有數個參數：

* **`created-after` (選用)** ：清除這個日期/時間 (UTC) 之後所建立執行個體的記錄。 可接受 ISO 8601 格式的日期時間。
* **`created-before` (選用)** ：清除這個日期/時間 (UTC) 之前所建立執行個體的記錄。 可接受 ISO 8601 格式的日期時間。
* **`runtime-status` (選用)** ：清除具有特定狀態（例如，執行中或已完成）之實例的歷程記錄。 可以提供多個狀態 (以空格分隔)。
* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)** ：要使用 Durable Functions 任務中樞的名稱。 預設為 `DurableFunctionsHub`。 您也可以使用 durableTask： HubName，在[host. json](durable-functions-bindings.md#host-json)中設定它。

下列命令會刪除2018年11月14日下午 7:35 PM （UTC）之前建立之所有失敗實例的歷程記錄。

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>刪除工作中樞

您可以使用[Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub`命令，刪除與特定工作中樞相關聯的所有儲存成品。 這包括 Azure 儲存體資料表、佇列和 Blob。 此命令有兩個參數：

* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)** ：要使用 Durable Functions 任務中樞的名稱。 預設為 `DurableFunctionsHub`。 您也可以使用 durableTask： HubName，在[host. json](durable-functions-bindings.md#host-json)中設定它。

下列命令會刪除與工作`UserTest`中樞相關聯的所有 Azure 儲存體資料。

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何處理版本控制](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [實例管理的內建 HTTP API 參考](durable-functions-http-api.md)