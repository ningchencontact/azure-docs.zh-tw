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
ms.openlocfilehash: ee96bc5e17051ab37be34eecbb8e4fe35599cd5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60730764"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>在 Azure 中管理 Durable Functions 中的執行個體

如果您使用[Durable Functions](durable-functions-overview.md)延伸模組的 Azure Functions 或您想来啟動這項操作，請確定您遇到問題的最佳用法。 您可以深入了解如何管理它們，以最佳化您的 Durable Functions 協調流程執行個體。 本文討論每個執行個體管理作業的詳細資料。

您可以啟動和終止執行個體，比方說，而且您可以查詢執行個體，包括能夠查詢與篩選條件的所有執行個體和查詢執行個體。 此外，您可以將事件傳送至執行個體、 等候協調流程完成並擷取 HTTP 管理 webhook Url。 本文章涵蓋其他管理作業，也包括倒帶執行個體、 清除執行個體歷程記錄，以及刪除工作中樞。

在 Durable Functions 中，您會有針對您想要實作每個管理作業的方式的選項。 本文提供使用的範例[Azure Functions Core Tools](../functions-run-local.md)適用於這兩個.NET (C#) 和 JavaScript。

## <a name="start-instances"></a>啟動執行個體

請務必要能夠啟動協調流程執行個體。 這通常是另一個函式的觸發程序中使用長期函式繫結時。

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)方法[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) 或`startNew`上`DurableOrchestrationClient`(JavaScript) 啟動的新執行個體。 您可以取得此類別的執行個體使用`orchestrationClient`繫結。 在內部，此方法會將訊息加入控制佇列，然後就會利用 `orchestrationTrigger` 觸發程序繫結，觸發啟動具有指定名稱的函式。

協調流程程序已成功排定時，此非同步作業會完成。 協調程序應在 30 秒內啟動。 如果要花較長，您會看到`TimeoutException`。

> [!WARNING]
> 當在本機開發，在 JavaScript 中，設定環境變數`WEBSITE_HOSTNAME`來`localhost:<port>`(例如`localhost:7071`) 上使用方法`DurableOrchestrationClient`。 如需此需求的詳細資訊，請參閱 [GitHub 問題](https://github.com/Azure/azure-functions-durable-js/issues/28) \(英文\)。

### <a name="net"></a>.NET

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) 的參數如下：

* **Name**：要排程的協調器函式的名稱。
* **輸入**：應該當作輸入傳給協調器函式的任何 JSON 可序列化資料。
* **InstanceId**：(選擇性) 執行個體的唯一識別碼。 如果您未指定此參數，此方法會使用隨機的識別碼。

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
* **InstanceId**：(選擇性) 執行個體的唯一識別碼。 如果您未指定此參數，此方法會使用隨機的識別碼。
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
> 使用隨機識別碼作為執行個體識別碼。 這有助於確保均等分散負載，您要跨多個 Vm 調整協調器函式時。 使用非隨機的執行個體識別碼的適當時機是當識別碼必須來自外部來源，或當您實作[單次協調器](durable-functions-singletons.md)模式。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

您也可以使用來直接啟動執行個體[Azure Functions Core Tools](../functions-run-local.md) `durable start-new`命令。 它需要以下參數：

* **`function-name` (必要)** ：若要啟動的函式名稱。
* **`input` (選用)** ：輸入函式，內嵌方式或透過 JSON 檔案。 對於檔案，加入含有檔案路徑前置詞`@`，例如`@path/to/file.json`。
* **`id` (選用)** ：協調流程執行個體的識別碼。 如果您未指定此參數，此命令會使用隨機的 GUID。
* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 AzureWebJobsStorage。
* **`task-hub-name` (選用)** ：若要使用 Durable Functions 工作中樞的名稱。 預設為 DurableFunctionsHub。 您也可以設定這[host.json](durable-functions-bindings.md#host-json)使用 durableTask:HubName。

> [!NOTE]
> Core Tools 命令假設您從函式應用程式的根目錄中執行它們。 如果您明確提供`connection-string-setting`和`task-hub-name`參數，您可以從任何目錄執行命令。 雖然您可以執行下列命令，而不需要執行的函式應用程式主機，您可能會發現，您就無法觀察一些效果，除非主機正在執行。 比方說，`start-new`命令將啟動訊息為目標的工作中樞，但協調流程並不實際執行，函式應用程式主機處理序執行，除非可以處理訊息。

下列命令會啟動名為 HelloWorld，函式，並將傳遞檔案的內容`counter-data.json`給它：

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>查詢執行個體

隨著您的工作來管理您的協調流程的詳細資訊，您很可能要收集的協調流程執行個體 （比方說，不論它已正常完成或失敗） 狀態的相關資訊。

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 類別 (.NET) 的 [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) 方法或 `DurableOrchestrationClient` 類別 (JavaScript) 的 `getStatus` 方法可查詢協調流程執行個體的狀態。

它會以 `instanceId` (必要)、`showHistory` (選用)、`showHistoryOutput` (選用) 和 `showInput` (選用，僅限 .NET) 作為參數。

* **`showHistory`** ：如果設定為`true`，回應會包含執行記錄。
* **`showHistoryOutput`** ：如果設定為`true`，執行記錄包含活動輸出。
* **`showInput`** ：如果設定為`false`，回應將不會包含此函式的輸入。 預設值為 `true`。 (僅限 .NET)

此方法會傳回具有下列屬性的 JSON 物件：

* **Name**：協調器函式的名稱。
* **InstanceId**：協調流程的執行個體識別碼 (應該與 `instanceId` 輸入相同)。
* **CreatedTime**：協調器函式開始執行的時間。
* **LastUpdatedTime**：協調流程前次執行檢查點檢查的時間。
* **輸入**：函式的 JSON 值輸入。 如果未填入這個欄位`showInput`為 false。
* **CustomStatus**：JSON 格式的自訂協調流程狀態。
* **輸出**：函式的 JSON 值輸出 (如果函式已完成)。 如果協調器函式失敗，則這個屬性會包含失敗詳細資料。 如果終止協調器函式，這個屬性會包含終止的原因 （如果有的話）。
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

您也可使用直接取得協調流程執行個體的狀態[Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status`命令。 它需要以下參數：

* **`id` (必要)** ：協調流程執行個體的識別碼。
* **`show-input` (選用)** ：如果設定為`true`，回應會包含此函式的輸入。 預設值為 `false`。
* **`show-output` (選用)** ：如果設定為`true`，回應會包含函式的輸出。 預設值為 `false`。
* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)** ：若要使用 Durable Functions 工作中樞的名稱。 預設為 `DurableFunctionsHub`。 它也可以設定[host.json](durable-functions-bindings.md#host-json)，使用 durableTask:HubName。

下列命令會擷取 0ab8c55a66644d68a3a8b220b12d209c 的 協調流程執行個體識別碼的執行個體的狀態 （包括輸入和輸出）。 它會假設您正在`func`命令從函式應用程式的根目錄：

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

您可以使用`durable get-history`命令來擷取協調流程執行個體的歷程記錄。 它需要以下參數：

* **`id` (必要)** ：協調流程執行個體的識別碼。
* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)** ：若要使用 Durable Functions 工作中樞的名稱。 預設為 `DurableFunctionsHub`。 它也可以設定在 host.json 中，使用 durableTask:HubName。

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>查詢所有的執行個體

而不是在您的協調流程一次查詢一個執行個體，您可能會發現它一次查詢所有人都更有效率。

您可以使用 `GetStatusAsync` (.NET) 或 `getStatusAll` (JavaScript) 方法來查詢所有協調流程執行個體的狀態。 在.NET 中，您可以傳遞`CancellationToken`物件，如果您想要取消它。 此方法會和使用參數的 `GetStatusAsync`方法一樣傳回具有相同屬性的物件。

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

它也可直接查詢執行個體使用[Azure Functions Core Tools](../functions-run-local.md) `durable get-instances`命令。 它需要以下參數：

* **`top` (選用)** ：這個命令支援分頁。 此參數會對應至每個要求擷取的執行個體數目。 預設值為 10。
* **`continuation-token` (選用)** ：指出頁面或區段的執行個體來擷取權杖。 每個 `get-instances` 執行都會將權杖傳回給下一組執行個體。
* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)** ：若要使用 Durable Functions 工作中樞的名稱。 預設為 `DurableFunctionsHub`。 它也可以設定[host.json](durable-functions-bindings.md#host-json)，使用 durableTask:HubName。

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>含篩選的查詢執行個體

如果您真的不需要標準執行個體的查詢可以提供的所有資訊？ 例如，如果您只要尋找協調流程的建立時間或協調流程執行階段狀態？ 您可以藉由套用篩選來縮小您的查詢。

使用`GetStatusAsync`(.NET) 或`getStatusBy`(JavaScript) 方法，以取得一份協調流程執行個體符合一組預先定義的篩選條件。

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

在 Azure Functions Core Tools，您也可以使用`durable get-instances`命令並搭配篩選條件。 除了前述`top`， `continuation-token`， `connection-string-setting`，以及`task-hub-name`參數，您可以使用三個篩選條件的參數 (`created-after`， `created-before`，和`runtime-status`)。

* **`created-after` (選用)** ：擷取這個日期/時間 (UTC) 之後建立的執行個體。 可接受 ISO 8601 格式的日期時間。
* **`created-before` (選用)** ：擷取這個日期/時間 (UTC) 之後建立的執行個體。 可接受 ISO 8601 格式的日期時間。
* **`runtime-status` (選用)** ：擷取特定狀態 （例如，執行或完成） 的執行個體。 可以提供多個狀態 (以空格分隔)。
* **`top` (選用)** ：每個要求擷取的執行個體數目。 預設值為 10。
* **`continuation-token` (選用)** ：指出頁面或區段的執行個體來擷取權杖。 每個 `get-instances` 執行都會將權杖傳回給下一組執行個體。
* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)** ：若要使用 Durable Functions 工作中樞的名稱。 預設為 `DurableFunctionsHub`。 它也可以設定[host.json](durable-functions-bindings.md#host-json)，使用 durableTask:HubName。

如果您未提供任何篩選器 (`created-after`， `created-before`，或`runtime-status`)，此命令只會擷取`top`執行個體，而不考慮到執行階段狀態] 或 [建立時間。

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>終止執行個體

如果您有時間太長，無法執行時，協調流程執行個體，或您只需要因為任何原因完成之前，請停止它，您可以選擇將它終止。

您可以使用[Durableorchestrationclient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)方法[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)類別 (.NET)，或有`terminate`方法`DurableOrchestrationClient`類別 (JavaScript)。 兩個參數`instanceId`和`reason`寫入記錄檔及執行個體狀態的字串。 終止執行個體停止執行，只要它到達下一個`await`(.NET) 或`yield`(JavaScript) 點，就會立即終止如果它已經存在於`await`或`yield`。

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
> 終止執行個體目前不會傳播。 活動函式和子協調流程執行到完成為止，不論是否已在此終止呼叫的協調流程執行個體。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

您也可能會終止協調流程執行個體直接使用[Azure Functions Core Tools](../functions-run-local.md) `durable terminate`命令。 它需要以下參數：

* **`id` (必要)** ：要終止的協調流程執行個體的識別碼。
* **`reason` (選用)** ：終止的原因。
* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)** ：若要使用 Durable Functions 工作中樞的名稱。 預設為 `DurableFunctionsHub`。 它也可以設定[host.json](durable-functions-bindings.md#host-json)，使用 durableTask:HubName。

下列命令會終止協調流程的執行個體識別碼 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>將事件傳送至執行個體

在某些情況下，務必針對您的協調器函式能夠等候和接聽外部事件。 這包括[監視函式](durable-functions-concepts.md#monitoring)和函式，會等候[人為互動](durable-functions-concepts.md#human)。

將事件通知傳送給執行所使用的執行個體[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)方法[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)類別 (.NET) 或`raiseEvent`方法`DurableOrchestrationClient`類別 （JavaScript)。 正在等候呼叫 [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) 或 `waitForExternalEvent` (JavaScript) 的執行個體可以處理這些事件。

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
> 如果沒有協調流程執行個體具有指定的執行個體識別碼，或執行個體並未等候指定的事件名稱，則會捨棄事件訊息。 如需這個行為的詳細資訊，請參閱 [GitHub 問題](https://github.com/Azure/azure-functions-durable-extension/issues/29)。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

您可以也會引發事件，以協調流程執行個體直接使用[Azure Functions Core Tools](../functions-run-local.md) `durable raise-event`命令。 它需要以下參數：

* **`id` (必要)** ：協調流程執行個體的識別碼。
* **`event-name` (選用)** ：所要引發事件的名稱。 預設為 `$"Event_{RandomGUID}"`。
* **`event-data` (選用)** ：要傳送至協調流程執行個體的資料。 這可以是 JSON 檔案的路徑，或您可以直接在命令列上提供的資料。
* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)** ：若要使用 Durable Functions 工作中樞的名稱。 預設為 `DurableFunctionsHub`。 它也可以設定[host.json](durable-functions-bindings.md#host-json)，使用 durableTask:HubName。

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>等候協調流程完成

在長時間執行的協調流程，您可能要等候，並取得協調流程的結果。 在這些情況下，最好也要能夠在協調流程上定義的逾時期限。 如果超過逾時，應該會傳回協調流程的狀態，而不是結果。

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)類別會公開[WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_)在.NET 中的 API。 您可以使用此 API 來以同步方式從協調流程執行個體取得實際的輸出。 在 JavaScript 中，`DurableOrchestrationClient` 類別會公開 `waitForCompletionOrCreateCheckStatusResponse`API 達成相同目的。 當未設定時，這些方法會使用預設值是 10 秒讓`timeout`，和 1 秒`retryInterval`。  

以下是範例 HTTP 觸發函式，它會示範如何使用這個 API：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

呼叫函式搭配下列這一行。 使用 2 秒的逾時和 0.5 秒重試間隔：

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

取決於從協調流程執行個體取得回應所需的時間，會有兩種情況：

* （在此案例 2 秒） 中定義的逾時內完成的協調流程執行個體，並回應是以同步方式傳遞的實際協調流程執行個體輸出：

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

* 協調流程執行個體無法在定義的逾時內完成，回應是其中一個所述的預設值[HTTP API URL 探索](durable-functions-http-api.md):

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
> Webhook Url 的格式可能會有所不同，您會執行 Azure Functions 主機的版本。 上述範例適用於 Azure Functions 2.x 主機。

## <a name="retrieve-http-management-webhook-urls"></a>擷取 HTTP 管理 webhook Url

您可以使用外部系統監視，或引發事件，以協調流程。 外部系統可以透過 webhook Url 中所述的預設回應一部分的 Durable Functions 與通訊[HTTP API URL 探索](durable-functions-http-api.md)。 不過，webhook Url 也可以存取以程式設計方式在協調流程用戶端或活動函式。 做法是使用[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_)方法[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)類別 (.NET)，或有`createHttpManagementPayload`方法`DurableOrchestrationClient`類別 (JavaScript)。

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) 和 `createHttpManagementPayload` 有一個參數：

* **instanceId**：執行個體的唯一識別碼。

方法會傳回的執行個體[HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) 或物件 (JavaScript)，使用下列字串屬性：

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

## <a name="rewind-instances-preview"></a>倒轉執行個體 （預覽）

如果您將協調流程失敗的未預期的原因時，您可以*倒轉*来使用 API 之前狀況良好狀態的執行個體建基於這個目的。

> [!NOTE]
> 此 API 並非要取代適當的錯誤處理和重試原則。 相反地，它只是要用於協調流程執行個體因非預期原因而失敗的情況。 如需錯誤處理和重試原則的詳細資訊，請參閱 <<c0> [ 錯誤處理](durable-functions-error-handling.md)主題。

使用[RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) 或`rewindAsync`(JavaScript) API，以將協調流程將會回到*執行*狀態。 重新執行活動或 suborchestration 執行失敗導致協調流程失敗。

例如，假設您有涉及一系列的工作流程[人力核准](durable-functions-concepts.md#human)。 假設有一系列的某人，他們核准所需的及等待出即時回應通知的活動函式。 畢竟，核准活動的已收到回應或逾時，假設另一個活動失敗時，由於應用程式的組態不正確，例如無效的資料庫連接字串。 結果就是深入工作流程的協調流程失敗。 具有`RewindAsync`(.NET) 或`rewindAsync`(JavaScript) API，應用程式系統管理員可以修正設定錯誤，並倒轉回之前失敗的狀態失敗的協調流程。 人類看得互動步驟都需要相關，而且協調流程現在可以順利完成。

> [!NOTE]
> *倒轉*功能不支援倒帶協調流程執行個體使用永久性計時器。

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

您也可以直接透過使用 rewind 協調流程執行個體[Azure Functions Core Tools](../functions-run-local.md) `durable rewind`命令。 它需要以下參數：

* **`id` (必要)** ：協調流程執行個體的識別碼。
* **`reason` (選用)** ：倒轉協調流程執行個體的原因。
* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)** ：若要使用 Durable Functions 工作中樞的名稱。 預設為 `DurableFunctionsHub`。 它也可以設定[host.json](durable-functions-bindings.md#host-json)，使用 durableTask:HubName。

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>清除執行個體記錄

若要移除協調流程相關聯的所有資料，您可以清除執行個體歷程記錄。 比方說，您可能想要去除 Azure 資料表的資料列和大型訊息 blob，如果有的話。 若要這樣做，請使用[PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) API。

> [!NOTE]
> `PurgeInstanceHistoryAsync` API 目前僅適用於 C#。

 此方法有兩個多載。 第一個會清除歷程記錄的協調流程執行個體識別碼：

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

第二個範例會顯示計時器觸發的函式，它會清除在指定時間間隔之後完成的所有協調流程執行個體記錄。 在此情況下，它會移除所有的執行個體完成前 30 個或更多天的資料。 它被排定為每天執行一次，在上午 12 點：

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
> 成功的時間觸發的函式處理序的執行階段狀態必須是**已完成**， **Terminated**，或**失敗**。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

您可以藉由清除協調流程執行個體的歷程記錄[Azure Functions Core Tools](../functions-run-local.md) `durable purge-history`命令。 類似於第二個C#上一節的範例，它會清除所有指定的時間間隔期間所建立的協調流程執行個體的歷程記錄。 您可以進一步篩選已清除的執行個體，由執行階段狀態。 此命令有數個參數：

* **`created-after` (選用)** ：清除這個日期/時間 (UTC) 之後所建立執行個體的記錄。 可接受 ISO 8601 格式的日期時間。
* **`created-before` (選用)** ：清除這個日期/時間 (UTC) 之前所建立執行個體的記錄。 可接受 ISO 8601 格式的日期時間。
* **`runtime-status` (選用)** ：清除時 （例如，執行或完成） 的特定狀態的執行個體的記錄。 可以提供多個狀態 (以空格分隔)。
* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)** ：若要使用 Durable Functions 工作中樞的名稱。 預設為 `DurableFunctionsHub`。 它也可以設定[host.json](durable-functions-bindings.md#host-json)，使用 durableTask:HubName。

下列命令會刪除所有失敗的執行個體建立之前 2018 年 11 月 14 日下午 7:35 (UTC) 的歷程記錄。

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>刪除工作中樞

使用[Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub`命令時，您可以刪除所有與特定的工作中樞相關聯的儲存體成品。 這包括 Azure 儲存體資料表、佇列和 Blob。 此命令有兩個參數：

* **`connection-string-setting` (選用)** ：應用程式設定的名稱，包含要使用的儲存體連接字串。 預設為 `AzureWebJobsStorage`。
* **`task-hub-name` (選用)** ：若要使用 Durable Functions 工作中樞的名稱。 預設為 `DurableFunctionsHub`。 它也可以設定[host.json](durable-functions-bindings.md#host-json)，使用 durableTask:HubName。

下列命令會刪除所有相關聯的 Azure 儲存體資料`UserTest`工作中樞。

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解如何使用 HTTP API 來管理執行個體](durable-functions-http-api.md)
