---
title: 長期函式中的繫結 - Azure
description: 如何針對 Azure Functions 的 Durable Functions 擴充功能使用觸發程序和繫結。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 1b056ce8afe86fcd6629aff23ac95acae02ed9ba
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299873"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>長期函式中的繫結 (Azure Functions)

[長期函式](durable-functions-overview.md)延伸模組引進了兩個新的觸發程序繫結，可控制協調器和活動函式的執行。 它也引進了輸出繫結，可作為長期函式執行階段的用戶端。

## <a name="orchestration-trigger"></a>協調流程觸發程式

協調流程觸發程式可讓您撰寫[持久協調](durable-functions-types-features-overview.md#orchestrator-functions)器函式。 這個觸發程序支援啟動新的協調器函式執行個體，以及繼續使用「等候」工作的現有協調器函式執行個體。

當您使用 Azure Functions 適用的 Visual Studio 工具時，協調流程觸發程序會使用 [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) .NET 屬性進行設定。

當您以指令碼語言 (例如，JavaScript 或 C# 指令碼處理) 撰寫協調器函式時，協調流程觸發程序會由 *function.json* 檔案之 `bindings` 陣列中的下列 JSON 物件所定義：

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` 是協調流程的名稱。 這是用戶端在想要啟動這個協調器函式的新執行個體時必須使用的值。 這是選用屬性。 如果未指定，會使用函式的名稱。

此觸發程序繫結會在內部輪詢函式應用程式之預設儲存體帳戶中的一系列佇列。 這些佇列是延伸模組的內部實作詳細資料，這就是為什麼它們未在繫結屬性中明確設定。

### <a name="trigger-behavior"></a>觸發程序行為

以下是協調流程觸發程序的一些附註：

* **單一執行緒** - 單一發送器執行緒用於單一主機執行個體上的所有協調器函式執行。 基於這個理由，請務必確保協調器函式程式碼有效率，且不會執行任何 I/O。 也務必要確保此執行緒不會執行除了等候長期函式特定工作類型以外的任何非同步工作。
* **有害訊息處理** - 協調流程觸發程序中並無有害訊息支援。
* **訊息可見度** - 協調流程觸發程序訊息會被清除佇列，並且在可設定持續期間保持不可見。 只要函式應用程式正在執行且狀況良好，就會自動更新這些訊息的可見度。
* **傳回值** - 傳回值會序列化為 JSON，保存到 Azure 資料表儲存體中的協調流程歷程記錄資料表。 協調流程用戶端繫結可以查詢這些傳回值，會在稍後說明。

> [!WARNING]
> 協調器函式應該永不使用協調流程繫結觸發程序繫結以外的任何輸入或輸出繫結。 這麼做有可能會造成長期工作延伸模組的問題，因為這些繫結可能不會遵照單一執行緒和 I/O 規則。 如果您想要使用其他系結，請將它們新增至從協調器函式呼叫的活動函式。

> [!WARNING]
> JavaScript 協調器函式永遠都不應宣告為 `async`。

### <a name="trigger-usage-net"></a>觸發程序使用方式 (.NET)

協調流程觸發程序繫結支援輸入及輸出。 協助了解輸入和輸出處理的一些事項如下：

* **輸入**：.NET 協調流程函式僅支援 [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) \(英文\) 作為參數類型。 不支援直接在函式簽章中還原序列化輸入。 程式碼必須使用 [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.NET) \(英文\) 或 `getInput` (JavaScript) 方法來擷取協調器函式輸入。 這些輸入必須是 JSON 可序列化類型。
* **輸出** - 協調流程觸發程序支援輸出值以及輸入。 函式的傳回值會用來指派輸出值，而且必須是 JSON 可序列化。 如果 .NET 函式傳回 `Task` 或 `void`，則會將 `null` 值儲存為輸出。

### <a name="trigger-sample"></a>觸發程序範例

下列範例程式碼示範最簡單的 "Hello World" 協調器函式看起來可能像這樣：

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> JavaScript 中的 `context` 物件不代表 DurableOrchestrationCoNtext，而是整個函式[內容](../functions-reference-node.md#context-object)。 您可以透過 `context` 物件的 `df` 屬性來存取協調流程方法。

> [!NOTE]
> JavaScript 協調器應使用 `return`。 `durable-functions` 程式庫會負責呼叫 `context.done` 方法。

大部分協調器函式會呼叫活動函式；因此，這裡的 "Hello World" 範例示範如何呼叫活動函式：

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>活動觸發程式

活動觸發程式可讓您撰寫協調器函式所呼叫的函式，稱為「活動函式」（ [activity 函數](durable-functions-types-features-overview.md#activity-functions)）。

如果您使用 Visual Studio，便會使用 [ActivityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) .NET 屬性來設定活動觸發程序。

如果您使用 VS Code 或 Azure 入口網站來進行開發，則活動觸發程序會由 *function.json* 之 `bindings` 陣列中的下列 JSON 物件定義：

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` 是活動的名稱。 此值是協調器函式用來叫用此活動函式的名稱。 這是選用屬性。 如果未指定，會使用函式的名稱。

此觸發程序繫結會在內部輪詢函式應用程式之預設儲存體帳戶中的佇列。 這個佇列是延伸模組的內部實作詳細資料，這就是為什麼它未在繫結屬性中明確設定。

### <a name="trigger-behavior"></a>觸發程序行為

以下是活動觸發程序的一些附註：

* **執行緒** - 與協調流程觸發程序不同，活動觸發程序在執行緒或 I/O 方面沒有任何限制。 它們可以被視為一般函式。
* **有害訊息處理** - 活動觸發程序中沒有任何有害訊息支援。
* **訊息可見度** - 活動觸發程序訊息會被清除佇列，並且在可設定持續期間保持不可見。 只要函式應用程式正在執行且狀況良好，就會自動更新這些訊息的可見度。
* **傳回值** - 傳回值會序列化為 JSON，保存到 Azure 資料表儲存體中的協調流程歷程記錄資料表。

> [!WARNING]
> 活動函式的儲存體後端是實作詳細資料，使用者程式碼不應直接與這些儲存體實體互動。

### <a name="trigger-usage-net"></a>觸發程序使用方式 (.NET)

活動觸發程序繫結支援輸入及輸出，就像協調流程觸發程序一樣。 協助了解輸入和輸出處理的一些事項如下：

* **輸入**：.NET 活動函式原生使用 [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) \(英文\) 作為參數類型。 或者，可以使用 JSON 可序列化的任何參數類型宣告活動函式。 當您使用 `DurableActivityContext` 時，可以呼叫 [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) 以擷取和還原序列化活動函式輸入。
* **輸出** - 活動函式支援輸出值和輸入。 函式的傳回值會用來指派輸出值，而且必須是 JSON 可序列化。 如果 .NET 函式傳回 `Task` 或 `void`，則會將 `null` 值儲存為輸出。
* **中繼資料**：.NET 活動函式可以繫結至 `string instanceId` 參數，以取得父代協調流程的執行個體識別碼。

### <a name="trigger-sample"></a>觸發程序範例

下列範例程式碼示範簡單的 "Hello World" 活動函式看起來可能像這樣：

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

.NET `ActivityTriggerAttribute` 繫結的預設參數類型是 `DurableActivityContext`。 不過，.NET活動觸發程序也支援直接繫結至JSON 可序列化類型 (包括基本類型)，因此，同一個函式可以簡化如下：

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (僅限 Functions 2.x)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

JavaScript 繫結也能以額外參數形式傳入，因此，同一個函式可以簡化如下：

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>使用輸入和輸出系結

除了活動觸發程式系結之外，您還可以使用一般輸入和輸出系結。 例如，您可以接受活動系結的輸入，並使用 EventHub 輸出系結將訊息傳送至 EventHub：

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>協調流程用戶端

協調流程用戶端系結可讓您撰寫與協調器函式互動的函式。 這些函數有時稱為[用戶端](durable-functions-types-features-overview.md#client-functions)函式。 例如，您可以下列方式處理協調流程執行個體：

* 啟動它們。
* 查詢其狀態。
* 終止它們。
* 在它們執行時傳送事件給它們。
* 清除執行個體記錄。

如果您使用 Visual Studio，您可以使用 Durable Functions 1.0 的[OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .net 屬性來系結至協調流程用戶端。 從 Durable Functions 2.0 preview 開始，您可以使用 `DurableClientAttribute` .NET 屬性來系結至協調流程用戶端。

如果您使用指令碼語言（例如， *.csx*或 *.js*檔案）進行開發，協調流程觸發程式是由函式的 @no__t 2 陣列中的下列 JSON 物件所*定義：*

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` - 在多個函式應用程式共用相同儲存體帳戶，但是必須相互隔離的案例中使用。 若未指定，就會使用 `host.json` 的預設值。 此值必須符合目標協調器函式所使用的值。
* `connectionName` - 包含儲存體帳戶連接字串的應用程式設定名稱。 這個連接字串代表的儲存體帳戶，必須是目標協調器函式使用的相同帳戶。 如果未指定，則會使用函數應用程式的預設儲存體帳戶連接字串。

> [!NOTE]
> 在大部分情況下，建議您省略這些屬性，並依賴預設行為。

### <a name="client-usage"></a>用戶端使用方式

在 .NET 函式中，您通常會繫結至 `DurableOrchestrationClient`，這可讓您具有 Durable Functions 所支援之所有用戶端 API 的完整存取權。 從 Durable Functions 2.0 開始，您會改為系結至 @no__t 0 介面。 在 JavaScript 中，相同的 Api 是由 `getClient` 傳回的物件所公開。 用戶端物件上的 API 包括：

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_)
* [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_)
* [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_)

或者，.NET 函式可以繫結至 `IAsyncCollector<T>`，其中 `T` 是 [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) \(英文\) 或 `JObject`。

如需這些作業的詳細資訊，請參閱[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) API 檔。

### <a name="client-sample-visual-studio-development"></a>用戶端範例 (Visual Studio 開發)

以下是範例佇列觸發函式，它會啟動 "HelloWorld" 協調流程。

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>用戶端範例 (不是 Visual Studio)

如果您不是使用 Visual Studio 進行開發，您可以建立下列 function.json 檔案。 此範例示範如何設定佇列觸發函式，它使用長期協調流程用戶端繫結：

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

以下是啟動新的協調器函式執行個體的特定語言範例。

#### <a name="c-sample"></a>C# 範例

下列範例會示範如何使用長期協調流程用戶端繫結以從 C# 指令碼函式啟動新的函式執行個體：

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>JavaScript 範例

下列範例會示範如何使用長期協調流程用戶端繫結以從 JavaScript 函式啟動新的函式執行個體：

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

啟動執行個體的更多詳細資料可以在[執行個體管理](durable-functions-instance-management.md)中找到。

## <a name="entity-trigger"></a>實體觸發程式

實體觸發程式可讓您撰寫[實體](durable-functions-entities.md)函式。 此觸發程式支援處理特定實體實例的事件。

當您使用適用于 Azure Functions 的 Visual Studio 工具時，會使用 `EntityTriggerAttribute` .NET 屬性來設定實體觸發程式。

> [!NOTE]
> 實體觸發程式可在 Durable Functions 2.0 和更新版本中取得。 JavaScript 尚無法使用實體觸發程式。

此觸發程序繫結會在內部輪詢函式應用程式之預設儲存體帳戶中的一系列佇列。 這些佇列是延伸模組的內部實作詳細資料，這就是為什麼它們未在繫結屬性中明確設定。

### <a name="trigger-behavior"></a>觸發程序行為

以下是有關實體觸發程式的一些注意事項：

* **單一執行緒**：單一發送器執行緒是用來處理特定實體的作業。 如果多個訊息同時傳送至單一實體，則會一次性處理作業。
* **有害訊息處理**-實體觸發程式中沒有有害訊息支援。
* **訊息可見度**-實體觸發程式訊息會被清除佇列，並且在可設定的持續時間內保持不可見。 只要函式應用程式正在執行且狀況良好，就會自動更新這些訊息的可見度。
* 傳回**值**-Entity 函數不支援傳回值。 有一些特定的 Api 可用來儲存狀態，或將值傳回到協調流程。

在執行期間對實體所做的任何狀態變更都會在執行完成之後自動儲存。

### <a name="trigger-usage-net"></a>觸發程序使用方式 (.NET)

每個實體函式的參數類型為 `IDurableEntityContext`，其具有下列成員：

* **EntityName**：目前正在執行之實體的名稱。
* **EntityKey**：目前正在執行之實體的索引鍵。
* **EntityId**：目前正在執行之實體的識別碼。
* **OperationName**：目前作業的名稱。
* **HasState**：實體是否存在，也就是具有某種狀態。 
* **GetState @ no__t-1TState > （）** ：取得實體的目前狀態。 如果它不存在，則會建立並初始化為 `default<TState>`。 @No__t-0 參數必須是基本型別或 JSON 型別（可序列化 type）。 
* **GetState @ no__t-1TState > （initfunction）** ：取得實體的目前狀態。 如果它不存在，則會藉由呼叫提供的 `initfunction` 參數來建立。 @No__t-0 參數必須是基本型別或 JSON 型別（可序列化 type）。 
* **SetState （arg）** ：建立或更新實體的狀態。 @No__t-0 參數必須是可序列化物件或基本類型。
* **DeleteState （）** ：刪除實體的狀態。 
* **Getinput t> @ no__t-1TInput > （）** ：取得目前作業的輸入。 @No__t-0 型別參數必須是基本型別或 JSON 型別（可序列化 type）。
* **Return （arg）** ：將值傳回至呼叫作業的協調流程。 @No__t-0 參數必須是基本或 JSON 可序列化物件。
* **SignalEntity （EntityId，operation，input）** ：傳送單向訊息給實體。 @No__t-0 參數必須是非 null 字串，且 @no__t 1 參數必須是基本或可序列化的物件。
* **CreateNewOrchestration （orchestratorFunctionName，input）** ：啟動新的協調流程。 @No__t-0 參數必須是基本或 JSON 可序列化物件。

傳遞至實體函式的 @no__t 0 物件可以使用 `Entity.Current` 個非同步區域屬性來存取。 當使用以類別為基礎的程式設計模型時，這個方法會很方便。

### <a name="trigger-sample-function-based-syntax"></a>觸發程式範例（以函式為基礎的語法）

下列程式碼是實作為長期函數的簡單*計數器*實體範例。 此函式會定義三個作業，`add`，`reset`，而 `get`，其中每一個都是以整數狀態運作。

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

如需以函式為基礎的語法和其使用方式的詳細資訊，請參閱以函[式為基礎的語法](durable-functions-dotnet-entities.md#function-based-syntax)。

### <a name="trigger-sample-class-based-syntax"></a>觸發程式範例（以類別為基礎的語法）

下列範例等同於使用類別和方法來實作 `Counter` 實體。

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

這個實體的狀態是 `Counter` 類型的物件，其中包含的欄位會儲存計數器的目前值。 為了將此物件保存在儲存體中，它會由[Json.NET](https://www.newtonsoft.com/json)程式庫進行序列化和還原序列化。 

如需以類別為基礎的語法和其使用方式的詳細資訊，請參閱[定義實體類別](durable-functions-dotnet-entities.md#defining-entity-classes)。

> [!NOTE]
> 使用實體類別時，具有 `[FunctionName]` 屬性的函式進入點方法「必須」 宣告為 `static`。 非靜態的進入點方法可能會導致初始化多個物件，並可能導致其他未定義的行為。

實體類別具有與系結和 .NET 相依性插入互動的特殊機制。 如需詳細資訊，請參閱[實體結構](durable-functions-dotnet-entities.md#entity-construction)。

## <a name="entity-client"></a>實體用戶端

實體用戶端系結可讓您以非同步方式觸發[實體](#entity-trigger)函式。 這些函數有時稱為[用戶端](durable-functions-types-features-overview.md#client-functions)函式。

如果您使用 Visual Studio，您可以使用 `DurableClientAttribute` .NET 屬性來系結至 entity client。

> [!NOTE]
> @No__t-0 也可以用來系結至[協調流程用戶端](#orchestration-client)。

如果您使用指令碼語言（例如， *.csx*或 *.js*檔案）進行開發，則實體觸發程式是由函式的 @no__t 2 陣列中的下列 JSON 物件所*定義：*

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "out"
}
```

* `taskHub` - 在多個函式應用程式共用相同儲存體帳戶，但是必須相互隔離的案例中使用。 若未指定，就會使用 `host.json` 的預設值。 這個值必須符合目標實體函數所使用的值。
* `connectionName` - 包含儲存體帳戶連接字串的應用程式設定名稱。 此連接字串所代表的儲存體帳戶必須與目標實體函式所使用的相同。 如果未指定，則會使用函數應用程式的預設儲存體帳戶連接字串。

> [!NOTE]
> 在大部分情況下，建議您省略選擇性屬性，並依賴預設行為。

### <a name="entity-client-usage"></a>實體用戶端使用方式

在 .NET 函式中，您通常會系結至 `IDurableEntityClient`，這可讓您完整存取持久實體所支援的所有用戶端 Api。 您也可以系結至 `IDurableClient` 介面，這可讓您存取實體和協調流程的用戶端 Api。 用戶端物件上的 API 包括：

* **ReadEntityStateAsync @ no__t-1T >** ：讀取實體的狀態。 它會傳迴響應，指出目標實體是否存在，如果有的話，它的狀態為何。
* **SignalEntityAsync**：將單向訊息傳送至實體，並等候它排入佇列。

在傳送信號之前，不需要建立目標實體-可以從處理信號的實體函式內建立實體狀態。

> [!NOTE]
> 請務必瞭解，從用戶端傳送的「信號」只會排入佇列，以便在稍後以非同步方式處理。 特別是，`SignalEntityAsync` 通常會在實體甚至啟動作業之前傳回，而且不可能取得傳回值或觀察例外狀況。 如果需要更強的保證（例如，針對工作流程），應該使用*協調*器函式，這可以等候實體作業完成，而且可以處理傳回值並觀察例外狀況。

### <a name="example-client-signals-entity-directly"></a>範例：用戶端直接通知實體

以下是叫用「計數器」實體的範例佇列觸發函式。

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="example-client-signals-entity-via-interface"></a>範例：透過介面的用戶端信號實體

可能的話，我們建議您[透過介面存取實體](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces)，因為它提供更多的型別檢查。 例如，假設先前所述的 @no__t 0 實體實作為 @no__t 1 介面，定義如下：

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

接著，用戶端程式代碼可以使用 `SignalEntityAsync<ICounter>` 來產生型別安全的 proxy：

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

@No__t-0 參數是動態產生的 `ICounter` 實例，它會在內部將對 `Add` 的呼叫轉譯為 `SignalEntityAsync` 的對等（不具類型）呼叫。

> [!NOTE]
> @No__t-0 Api 代表單向作業。 如果實體介面傳回 `Task<T>`，則 @no__t 1 參數的值一律會是 null 或 `default`。

特別的是，對 `Get` 作業發出信號是沒有意義的，因為不會傳回任何值。 相反地，用戶端可以使用 `ReadStateAsync` 直接存取計數器狀態，也可以啟動呼叫 `Get` 作業的協調器函式。 

<a name="host-json"></a>
## <a name="hostjson-settings"></a>host.json 設定

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [實例管理的內建 HTTP API 參考](durable-functions-http-api.md)
