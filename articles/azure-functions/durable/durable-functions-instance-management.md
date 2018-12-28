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
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 1ab2e35c916c6bd6f2d73a328f71710378fac890
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2018
ms.locfileid: "53343933"
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>在 Durable Functions (Azure Functions) 中管理執行個體

您可以啟動、終止、查詢和傳送通知事件給 [Durable Functions](durable-functions-overview.md) 協調流程執行個體。 執行個體管理完全是透過[協調流程用戶端繫結](durable-functions-bindings.md)來進行。 本文討論每個執行個體管理作業的詳細資料。

## <a name="starting-instances"></a>啟動執行個體

[StartNewAsync] ([DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) 上的 https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) 方法或 `DurableOrchestrationClient` (JavaScript) 上的 `startNew`) 可啟動協調器函式的新執行個體。 您可以使用 `orchestrationClient` 繫結來取得此類別的執行個體。 在內部，此方法會將訊息加入控制佇列，然後就會利用 `orchestrationTrigger` 觸發程序繫結，觸發啟動具有指定名稱的函式。

協調流程程序已成功排定時，此非同步作業會完成。 協調程序應在 30 秒內啟動。 如果花費更久的時間，就會擲回 `TimeoutException`。

> [!WARNING]
> 以 JavaScript 在本機開發時，您必須將環境變數 `WEBSITE_HOSTNAME` 設定為 `localhost:<port>`，例如 `localhost:7071`，以在 `DurableOrchestrationClient` 上使用方法。 如需此需求的詳細資訊，請參閱 [GitHub 問題](https://github.com/Azure/azure-functions-durable-js/issues/28) \(英文\)。

### <a name="net"></a>.NET

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) 的參數如下：

* **Name**：要排程的協調器函式的名稱。
* **輸入**：應該當作輸入傳給協調器函式的任何 JSON 可序列化資料。
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

### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

`startNew` 的參數如下所示：

* **Name**：要排程的協調器函式的名稱。
* **InstanceId**：(選擇性) 執行個體的唯一識別碼。 如果未指定，將會產生隨機的執行個體識別碼。
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

> [!NOTE]
> 使用隨機識別碼作為執行個體識別碼。 在將協調器函式擴展至多個虛擬機器時，這就有助於確保均等分散負載。 當識別碼必須來自外部來源，或實作[單次協調器](durable-functions-singletons.md)模式時，才是使用非隨機執行個體識別碼的適當時機。

### <a name="using-core-tools"></a>使用 Core Tools

您也可以直接透過 [Azure Functions Core Tools](../functions-run-local.md) `durable start-new` 命令啟動執行個體。 它需要以下參數：

* **`function-name` (必要)**：要啟動的函式名稱
* **`input` (選用)**：內嵌或透過 JSON 檔案輸入至函式。 若透過檔案，請使用 `@` 作為檔案路徑的前置詞，例如 `@path/to/file.json`。
* **`id` (選用)**：協調流程執行個體的識別碼。 如果未提供，則會產生隨機 GUID。
* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 AzureWebJobsStorage。
* **`task-hub-name` (選用)**：要使用的長期工作中樞名稱。 預設為 DurableFunctionsHub。 也可以在 [host.json](durable-functions-bindings.md#host-json) 中透過 durableTask:HubName 來進行設定。

> [!NOTE]
> Core Tools 命令假設其本身會從函式應用程式的根目錄中執行。 如果明確提供 `connection-string-setting` 和 `task-hub-name`，則可以從任何目錄執行命令。 雖然不需要執行函式應用程式主機就可以執行這些命令，但除非執行主機，否則可能不會看到某些效果。 例如，`start-new` 命令會將啟動訊息佇列加入目標工作中樞內，但協調流程並不會實際執行，除非有可以處理訊息的函式應用程式主機處理序在執行。

下列命令會啟動名為 HelloWorld 的函式，並在該函式中傳入檔案 'counter-data.json' 的內容：

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="querying-instances"></a>查詢執行個體

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 類別 (.NET) 的 [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) 方法或 `DurableOrchestrationClient` 類別 (JavaScript) 的 `getStatus` 方法可查詢協調流程執行個體的狀態。

它會以 `instanceId` (必要)、`showHistory` (選用)、`showHistoryOutput` (選用) 和 `showInput` (選用，僅限 .NET) 作為參數。

* **`showHistory`**：如果設為 `true`，回應將會包含執行記錄。
* **`showHistoryOutput`**：如果設為 `true`，則執行記錄將會包含活動輸出。
* **`showInput`**：如果設為 `false`，回應將不會包含函式的輸入。 預設值為 `true`。 (僅限 .NET)

此方法會傳回具有下列屬性的 JSON 物件：

* **Name**：協調器函式的名稱。
* **InstanceId**：協調流程的執行個體識別碼 (應該與 `instanceId` 輸入相同)。
* **CreatedTime**：協調器函式開始執行的時間。
* **LastUpdatedTime**：協調流程前次執行檢查點檢查的時間。
* **輸入**：函式的 JSON 值輸入。 如果 `showInput` 為 false，將不會填入這個欄位。
* **CustomStatus**：JSON 格式的自訂協調流程狀態。
* **輸出**：函式的 JSON 值輸出 (如果函式已完成)。 如果協調器函式失敗，此屬性會包含失敗詳細資料。 如果協調器函式終止，此屬性會包含提供的終止原因 (如果有的話)。
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

### <a name="using-core-tools"></a>使用 Core Tools

您也可以直接透過 [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` 命令取得協調流程執行個體的狀態。 它需要以下參數：

* **`id` (必要)**：協調流程執行個體的識別碼
* **`show-input` (選用)**：如果設為 `true`，回應將不會包含函式的輸入。 預設值為 `false`。
* **`show-output` (選用)**：如果設為 `true`，回應將不會包含函式的輸出。 預設值為 `false`。
* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 AzureWebJobsStorage。
* **`task-hub-name` (選用)**：要使用的長期工作中樞名稱。 預設為 DurableFunctionsHub。 也可以在 [host.json](durable-functions-bindings.md#host-json) 中透過 durableTask:HubName 來進行設定。

下列命令會針對協調流程執行個體識別碼為 0ab8c55a66644d68a3a8b220b12d209c 的執行個體，擷取其狀態 (包括輸入和輸出)。 它假設 `func` 命令會從函式應用程式的根目錄中執行：

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

`durable get-history` 命令可用來擷取協調流程執行個體的記錄。 它需要以下參數：

* **`id` (必要)**：協調流程執行個體的識別碼
* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 AzureWebJobsStorage。
* **`task-hub-name` (選用)**：要使用的長期工作中樞名稱。 預設為 DurableFunctionsHub。 也可以在 host.json 中透過 durableTask:HubName 來進行設定。

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="querying-all-instances"></a>查詢所有執行個體

您可以使用 `GetStatusAsync` (.NET) 或 `getStatusAll` (JavaScript) 方法來查詢所有協調流程執行個體的狀態。 在 .NET 中，您可以在想要加以取消時傳入 `CancellationToken` 物件。 此方法會和使用參數的 `GetStatusAsync`方法一樣傳回具有相同屬性的物件。

### <a name="c"></a>C#

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

### <a name="using-core-tools"></a>使用 Core Tools

您也可以直接透過 [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` 命令查詢執行個體。 它需要以下參數：

* **`top` (選用)**：這個命令支援分頁。 此參數會對應至每個要求擷取的執行個體數目。 預設值為 10。
* **`continuation-token` (選用)**：用來指出所要擷取執行個體頁面/區段的權杖。 每個 `get-instances` 執行都會將權杖傳回給下一組執行個體。
* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 AzureWebJobsStorage。
* **`task-hub-name` (選用)**：要使用的長期工作中樞名稱。 預設為 DurableFunctionsHub。 也可以在 [host.json](durable-functions-bindings.md#host-json) 中透過 durableTask:HubName 來進行設定。

```bash
func durable get-instances
```

## <a name="querying-instances-with-filters"></a>利用篩選條件查詢執行個體

您也可以使用 `GetStatusAsync` (.NET) 或 `getStatusBy` (JavaScript) 方法來取得符合一組預先定義的篩選條件的協調流程執行個體清單。 可能的篩選選項包括協調流程建立時間和協調流程執行階段狀態。

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

### <a name="using-the-functions-core-tools"></a>使用 Functions Core Tools

`durable get-instances` 命令也可以搭配使用篩選條件。 除了前述的 `top`、`continuation-token`、`connection-string-setting` 和 `task-hub-name` 參數之外，還可以使用另外三個篩選條件參數 (`created-after`、`created-before` 和 `runtime-status`)。

* **`created-after` (選用)**：擷取這個日期/時間 (UTC) 之後建立的執行個體。 可接受 ISO 8601 格式的日期時間。
* **`created-before` (選用)**：擷取這個日期/時間 (UTC) 之後建立的執行個體。 可接受 ISO 8601 格式的日期時間。
* **`runtime-status` (選用)**：擷取狀態符合這些值 ('running'、'completed' 等) 的執行個體。 可以提供多個狀態 (以空格分隔)。
* **`top` (選用)**：每個要求擷取的執行個體數目。 預設值為 10。
* **`continuation-token` (選用)**：用來指出所要擷取執行個體頁面/區段的權杖。 每個 `get-instances` 執行都會將權杖傳回給下一組執行個體。
* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 AzureWebJobsStorage。
* **`task-hub-name` (選用)**：要使用的長期工作中樞名稱。 預設為 DurableFunctionsHub。 也可以在 [host.json](durable-functions-bindings.md#host-json) 中透過 durableTask:HubName 來進行設定。

如果沒有提供任何篩選條件 (`created-after`、`created-before` 或 `runtime-status`)，則會擷取 `top` 執行個體，而不考慮執行階段狀態或建立時間。

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminating-instances"></a>終止執行個體

您可以使用 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 類別 (.NET) 的 [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) 方法或 `DurableOrchestrationClient` 類別 (JavaScript) 的 `terminate` 方法來終止執行中的協調流程執行個體。 兩個參數是 `instanceId` 和 `reason` 字串，將會寫入記錄和執行個體狀態中。 終止的執行個體會在到達下一個 `await` (.NET) 或 `yield` (JavaScript) 點時就停止執行，但如果已處於 `await` (.NET) 或 `yield` (JavaScript)，則會立即終止。

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
> 執行個體終止目前未傳播。 無論呼叫活動函式和子協調流程的協調流程執行個體是否已終止，這些活動函式和子協調流程皆會執行到完成為止。

### <a name="using-core-tools"></a>使用 Core Tools

您也可以直接透過 [Core Tools](../functions-run-local.md) `durable terminate` 命令終止協調流程執行個體。 它需要以下參數：

* **`id` (必要)**：要終止的協調流程執行個體識別碼
* **`reason` (選用)**：終止原因
* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 AzureWebJobsStorage。
* **`task-hub-name` (選用)**：要使用的長期工作中樞名稱。 預設為 DurableFunctionsHub。 也可以在 [host.json](durable-functions-bindings.md#host-json) 中透過 durableTask:HubName 來進行設定。

下列命令會終止識別碼為 0ab8c55a66644d68a3a8b220b12d209c 的協調流程執行個體：

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="sending-events-to-instances"></a>將事件傳送至執行個體

您可以使用 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 類別 (.NET) 的 [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) 方法或 `DurableOrchestrationClient` 類別 (JavaScript) 的 `raiseEvent` 方法將事件通知傳送至執行中的執行個體。 正在等候呼叫 [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) 或 `waitForExternalEvent` (JavaScript) 的執行個體可以處理這些事件。

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

> [!WARNING]
> 如果沒有協調流程執行個體具有指定的「執行個體識別碼」，或執行個體並未等候指定的「事件名稱」，則會捨棄事件訊息。 如需這個行為的詳細資訊，請參閱 [GitHub 問題](https://github.com/Azure/azure-functions-durable-extension/issues/29)。

### <a name="using-core-tools"></a>使用 Core Tools

您也可以直接透過 [Core Tools](../functions-run-local.md) `durable raise-event` 命令引發協調流程執行個體的事件。 它需要以下參數：

* **`id` (必要)**：協調流程執行個體的識別碼
* **`event-name` (選用)**：所要引發事件的名稱。 預設值為 `$"Event_{RandomGUID}"`
* **`event-data` (選用)**：要傳送至協調流程執行個體的資料。 這可以是 JSON 檔案的路徑，也可以直接在命令列上提供資料
* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 AzureWebJobsStorage。
* **`task-hub-name` (選用)**：要使用的長期工作中樞名稱。 預設為 DurableFunctionsHub。 也可以在 [host.json](durable-functions-bindings.md#host-json) 中透過 durableTask:HubName 來進行設定。

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>等候協調流程完成

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 類別會公開 .NET 中的 [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API，可用來從協調流程執行個體以同步方式取得實際的輸出。 在 JavaScript 中，`DurableOrchestrationClient` 類別會公開 `waitForCompletionOrCreateCheckStatusResponse`API 達成相同目的。 此方法在 `timeout` 和 `retryInterval` 皆未設定時，會分別使用其預設值 10 秒和 1 秒。  

以下是範例 HTTP 觸發函式，它會示範如何使用這個 API：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

您可以在下列命令列中使用 2 秒的逾時和 0.5 秒的重試間隔呼叫此函式：

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

取決於從協調流程執行個體取得回應所需的時間，會有兩種情況：

* 協調流程執行個體在定義的逾時 (在此案例中為 2 秒) 內完成，回應是以同步方式傳遞的實際協調流程執行個體輸出：

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

* 協調流程執行個體無法在定義的逾時 (在此案例中為 2 秒) 內完成，回應是在 **HTTP API URL 探索**中說明的預設值一：

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
> Webhook URL 的格式依據您執行的 Azure Functions 主機版本，可能會有所不同。 上述範例適用於 Azure Functions 2.x 主機。

## <a name="retrieving-http-management-webhook-urls"></a>擷取 HTTP 管理 Webhook URL

外部系統可透過 [HTTP API URL 探索](durable-functions-http-api.md)中說明的預設回應所包含的 Webhook URL 與長期函式通訊。 不過，您也可透過 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 類別 (.NET) 的 [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) 方法或 `DurableOrchestrationClient` 類別 (JavaScript) 的 `createHttpManagementPayload` 方法，以程式設計方式在協調流程用戶端或活動函式中存取 Webhook URL。

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) 和 `createHttpManagementPayload` 有一個參數：

* **instanceId**：執行個體的唯一識別碼。

方法會傳回 [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) 的執行個體或具有下列字串屬性的物件 (JavaScript)：

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

## <a name="rewinding-instances-preview"></a>倒轉執行個體 (預覽)

使用 [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) 或 `rewindAsync` (JavaScript) API，可以將失敗的協調流程執行個體「倒轉」至先前狀況良好的狀態。 其運作方式是讓協調流程回到「執行中」狀態，然後重新執行此活動和/或導致協調流程失敗的子協調流程執行失敗。

> [!NOTE]
> 此 API 並非要取代適當的錯誤處理和重試原則。 相反地，它只是要用於協調流程執行個體因非預期原因而失敗的情況。 如需錯誤處理和重試原則的詳細資訊，請參閱[錯誤處理](durable-functions-error-handling.md)主題。

「倒轉」的其中一個範例使用案例為涉及一系列[人為核准](durable-functions-overview.md#pattern-5-human-interaction)的工作流程。 假設有一系列的活動函式，其可通知某人需要其核准並等待即時回應。 在所有核准活動已收到回應或逾時之後，另一個活動會因為應用程式設定錯誤 (例如資料庫連接字串無效) 而失敗。 結果就是深入工作流程的協調流程失敗。 透過 `RewindAsync` (.NET) 或 `rewindAsync` (JavaScript) API，應用程式系統管理員可以修正設定錯誤，以及讓失敗的協調流程「倒轉」回到失敗之前的狀態。 不需要重新核准任何人為互動步驟，協調流程現在可以順利完成。

> [!NOTE]
> 「倒轉」功能不支援使用耐久計時器的倒轉協調流程執行個體。

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

### <a name="using-core-tools"></a>使用 Core Tools

您也可以直接透過 [Core Tools](../functions-run-local.md) `durable rewind` 命令倒轉協調流程執行個體。 它需要以下參數：

* **`id` (必要)**：協調流程執行個體的識別碼
* **`reason` (選用)**：倒轉協調流程執行個體的原因
* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 AzureWebJobsStorage。
* **`task-hub-name` (選用)**：要使用的長期工作中樞名稱。 預設為 DurableFunctionsHub。 也可以在 [host.json](durable-functions-bindings.md#host-json) 中透過 durableTask:HubName 來進行設定。

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>清除執行個體記錄

> [!NOTE]
> `PurgeInstanceHistoryAsync` API 目前僅適用於 C#。 這會新增至未來版本的 JavaScript。

您可以使用 [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) 來清除協調流程記錄。 此功能會將與協調流程相關聯的所有資料移除 - Azure 資料表的資料列和大型訊息 Blob (如果存在)。 此方法有兩個多載。 第一個會依據協調流程執行個體的識別碼來清除記錄：

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

第二個範例會顯示計時器觸發的函式，它會清除在指定時間間隔之後完成的所有協調流程執行個體記錄。 在此案例中，它會將已完成 30 天以上的所有執行個體資料移除。 其排程為每天上午 12:00 執行一次：

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
> 接受時間週期作為參數的 PurgeInstanceHistory 多載，只會處理執行階段狀態為「已完成」、「已終止」或「失敗」之一的協調流程執行個體。

### <a name="using-core-tools"></a>使用 Core Tools

您可以使用 [Core Tools](../functions-run-local.md) `durable purge-history` 命令清除協調流程執行個體的記錄。 與上面的第二個 C# 範例類似，此命令會清除在指定的時間間隔期間所建立所有協調流程執行個體的記錄。 您可以進一步依執行階段狀態來篩選已清除的執行個體。 此命令有數個參數：

* **`created-after` (選用)**：清除這個日期/時間 (UTC) 之後所建立執行個體的記錄。 可接受 ISO 8601 格式的日期時間。
* **`created-before` (選用)**：清除這個日期/時間 (UTC) 之前所建立執行個體的記錄。 可接受 ISO 8601 格式的日期時間。
* **`runtime-status` (選用)**：清除狀態符合這些值 ('running'、'completed' 等) 的執行個體記錄。 可以提供多個狀態 (以空格分隔)。
* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 AzureWebJobsStorage。
* **`task-hub-name` (選用)**：要使用的長期工作中樞名稱。 預設為 DurableFunctionsHub。 也可以在 [host.json](durable-functions-bindings.md#host-json) 中透過 durableTask:HubName 來進行設定。

下列命令會刪除 2018 年 11 月 14 日下午 7:35 (UTC) 以前所建立且失敗的所有執行個體記錄。

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="deleting-a-task-hub"></a>刪除工作中樞

使用 [Core Tools](../functions-run-local.md) `durable delete-task-hub` 命令，就可以刪除與特定工作中樞相關聯的所有儲存體成品。 這包括 Azure 儲存體資料表、佇列和 Blob。 此命令有兩個參數：

* **`connection-string-setting` (選用)**：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 AzureWebJobsStorage。
* **`task-hub-name` (選用)**：要使用的長期工作中樞名稱。 預設為 DurableFunctionsHub。 也可以在 [host.json](durable-functions-bindings.md#host-json) 中透過 durableTask:HubName 來進行設定。

下列命令會將與 'UserTest' 工作中樞相關聯的所有 Azure 儲存體資料刪除。

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何使用 HTTP API 來管理執行個體](durable-functions-http-api.md)
