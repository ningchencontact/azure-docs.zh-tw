---
title: 長期函式中的繫結 - Azure
description: 如何針對 Azure Functions 的 Durable Functions 擴充功能使用觸發程序和繫結。
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 40b5f0f17cbb6867a6ef293a485d728141a012ef
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233031"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>長期函式中的繫結 (Azure Functions)

[長期函式](durable-functions-overview.md)延伸模組引進了兩個新的觸發程序繫結，可控制協調器和活動函式的執行。 它也引進了輸出繫結，可作為長期函式執行階段的用戶端。

## <a name="orchestration-trigger"></a>Orchestration trigger

The orchestration trigger enables you to author [durable orchestrator functions](durable-functions-types-features-overview.md#orchestrator-functions). 這個觸發程序支援啟動新的協調器函式執行個體，以及繼續使用「等候」工作的現有協調器函式執行個體。

當您使用 Azure Functions 適用的 Visual Studio 工具時，協調流程觸發程序會使用 [OrchestrationTriggerAttribute](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.OrchestrationTriggerAttribute?view=azure-dotnet) .NET 屬性進行設定。

當您以指令碼語言 (例如，JavaScript 或 C# 指令碼處理) 撰寫協調器函式時，協調流程觸發程序會由 *function.json* 檔案之 `bindings` 陣列中的下列 JSON 物件所定義：

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` is the name of the orchestration that clients must use when they want to start new instances of this orchestrator function. 這是選用屬性。 如果未指定，會使用函式的名稱。

此觸發程序繫結會在內部輪詢函式應用程式之預設儲存體帳戶中的一系列佇列。 這些佇列是延伸模組的內部實作詳細資料，這就是為什麼它們未在繫結屬性中明確設定。

### <a name="trigger-behavior"></a>觸發程序行為

以下是協調流程觸發程序的一些附註：

* **單一執行緒** - 單一發送器執行緒用於單一主機執行個體上的所有協調器函式執行。 基於這個理由，請務必確保協調器函式程式碼有效率，且不會執行任何 I/O。 也務必要確保此執行緒不會執行除了等候長期函式特定工作類型以外的任何非同步工作。
* **有害訊息處理** - 協調流程觸發程序中並無有害訊息支援。
* **訊息可見度** - 協調流程觸發程序訊息會被清除佇列，並且在可設定持續期間保持不可見。 只要函式應用程式正在執行且狀況良好，就會自動更新這些訊息的可見度。
* **傳回值** - 傳回值會序列化為 JSON，保存到 Azure 資料表儲存體中的協調流程歷程記錄資料表。 協調流程用戶端繫結可以查詢這些傳回值，會在稍後說明。

> [!WARNING]
> 協調器函式應該永不使用協調流程繫結觸發程序繫結以外的任何輸入或輸出繫結。 這麼做有可能會造成長期工作延伸模組的問題，因為這些繫結可能不會遵照單一執行緒和 I/O 規則。 If you'd like to use other bindings, add them to an Activity function called from your Orchestrator function.

> [!WARNING]
> JavaScript 協調器函式永遠都不應宣告為 `async`。

### <a name="trigger-usage-net"></a>觸發程序使用方式 (.NET)

協調流程觸發程序繫結支援輸入及輸出。 協助了解輸入和輸出處理的一些事項如下：

* **inputs** - .NET orchestration functions support only `DurableOrchestrationContext` as a parameter type. 不支援直接在函式簽章中還原序列化輸入。 Code must use the `GetInput<T>` (.NET) or `getInput` (JavaScript) method to fetch orchestrator function inputs. 這些輸入必須是 JSON 可序列化類型。
* **輸出** - 協調流程觸發程序支援輸出值以及輸入。 函式的傳回值會用來指派輸出值，而且必須是 JSON 可序列化。 如果 .NET 函式傳回 `Task` 或 `void`，則會將 `null` 值儲存為輸出。

### <a name="trigger-sample"></a>觸發程序範例

The following example code shows what the simplest "Hello World" orchestrator function might look like:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

#### <a name="javascript-functions-20-only"></a>JavaScript (僅限 Functions 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> The `context` object in JavaScript does not represent the DurableOrchestrationContext, but the [function context as a whole](../functions-reference-node.md#context-object). 您可以透過 `context` 物件的 `df` 屬性來存取協調流程方法。

> [!NOTE]
> JavaScript 協調器應使用 `return`。 `durable-functions` 程式庫會負責呼叫 `context.done` 方法。

大部分協調器函式會呼叫活動函式；因此，這裡的 "Hello World" 範例示範如何呼叫活動函式：

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

#### <a name="javascript-functions-20-only"></a>JavaScript (僅限 Functions 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Activity trigger

The activity trigger enables you to author functions that are called by orchestrator functions, known as [activity functions](durable-functions-types-features-overview.md#activity-functions).

If you're using Visual Studio, the activity trigger is configured using the `ActivityTriggerAttribute` .NET attribute.

如果您使用 VS Code 或 Azure 入口網站來進行開發，則活動觸發程序會由 *function.json* 之 `bindings` 陣列中的下列 JSON 物件定義：

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` 是活動的名稱。 This value is the name that orchestrator functions use to invoke this activity function. 這是選用屬性。 如果未指定，會使用函式的名稱。

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

* **inputs** - .NET activity functions natively use `DurableActivityContext` as a parameter type. 或者，可以使用 JSON 可序列化的任何參數類型宣告活動函式。 When you use `DurableActivityContext`, you can call `GetInput<T>` to fetch and deserialize the activity function input.
* **輸出** - 活動函式支援輸出值和輸入。 函式的傳回值會用來指派輸出值，而且必須是 JSON 可序列化。 如果 .NET 函式傳回 `Task` 或 `void`，則會將 `null` 值儲存為輸出。
* **中繼資料**：.NET 活動函式可以繫結至 `string instanceId` 參數，以取得父代協調流程的執行個體識別碼。

### <a name="trigger-sample"></a>觸發程序範例

The following example code shows what a simple "Hello World" activity function might look like:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableActivityContext` instead of `IDurableActivityContext`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

.NET `ActivityTriggerAttribute` 繫結的預設參數類型是 `IDurableActivityContext`。 不過，.NET活動觸發程序也支援直接繫結至JSON 可序列化類型 (包括基本類型)，因此，同一個函式可以簡化如下：

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (僅限 Functions 2.0)

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


### <a name="using-input-and-output-bindings"></a>Using input and output bindings

You can use regular input and output bindings in addition to the activity trigger binding. For example, you can take the input to your activity binding, and send a message to an EventHub using the EventHub output binding:

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

The orchestration client binding enables you to write functions that interact with orchestrator functions. These functions are sometimes referred to as [client functions](durable-functions-types-features-overview.md#client-functions). 例如，您可以下列方式處理協調流程執行個體：

* 啟動它們。
* 查詢其狀態。
* 終止它們。
* 在它們執行時傳送事件給它們。
* 清除執行個體記錄。

If you're using Visual Studio, you can bind to the orchestration client by using the `OrchestrationClientAttribute` .NET attribute for Durable Functions 1.0. Starting in the Durable Functions 2.0, you can bind to the orchestration client by using the `DurableClientAttribute` .NET attribute.

If you're using scripting languages (for example, *.csx* or *.js* files) for development, the orchestration trigger is defined by the following JSON object in the `bindings` array of *function.json*:

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

In .NET functions, you typically bind to `IDurableOrchestrationClient`, which gives you full access to all orchestration client APIs supported by Durable Functions. In the older Durable Functions 2.x releases, you instead bind to the `DurableOrchestrationClient` class. In JavaScript, the same APIs are exposed by the object returned from `getClient`. 用戶端物件上的 API 包括：

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Alternatively, .NET functions can bind to `IAsyncCollector<T>` where `T` is `StartOrchestrationArgs` or `JObject`.

For more information on these operations, see the `IDurableOrchestrationClient` API documentation.

### <a name="client-sample-visual-studio-development"></a>用戶端範例 (Visual Studio 開發)

以下是範例佇列觸發函式，它會啟動 "HelloWorld" 協調流程。

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

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
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> The previous JSON is for Durable Functions 2.x. For Durable Functions 1.x, you must use `orchestrationClient` instead of the `durableClient` as the trigger type. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

以下是啟動新的協調器函式執行個體的特定語言範例。

#### <a name="c-script-sample"></a>C# Script Sample

The following sample shows how to use the durable orchestration client binding to start a new function instance from a queue-triggered C# function:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

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

## <a name="entity-trigger"></a>Entity trigger

Entity triggers allow you to author [entity functions](durable-functions-entities.md). This trigger supports processing events for a specific entity instance.

When you use the Visual Studio tools for Azure Functions, the entity trigger is configured using the `EntityTriggerAttribute` .NET attribute.

> [!NOTE]
> Entity triggers are available starting in Durable Functions 2.x.

此觸發程序繫結會在內部輪詢函式應用程式之預設儲存體帳戶中的一系列佇列。 這些佇列是延伸模組的內部實作詳細資料，這就是為什麼它們未在繫結屬性中明確設定。

### <a name="trigger-behavior"></a>觸發程序行為

Here are some notes about the entity trigger:

* **Single-threaded**: A single dispatcher thread is used to process operations for a particular entity. If multiple messages are sent to a single entity concurrently, the operations will be processed one-at-a-time.
* **Poison-message handling** - There is no poison message support in entity triggers.
* **Message visibility** - Entity trigger messages are dequeued and kept invisible for a configurable duration. 只要函式應用程式正在執行且狀況良好，就會自動更新這些訊息的可見度。
* **Return values** - Entity functions do not support return values. There are specific APIs that can be used to save state or pass values back to orchestrations.

Any state changes made to an entity during its execution will be automatically persisted after execution has completed.

### <a name="trigger-usage-net"></a>觸發程序使用方式 (.NET)

Every entity function has a parameter type of `IDurableEntityContext`, which has the following members:

* **EntityName**: the name of the currently executing entity.
* **EntityKey**: the key of the currently executing entity.
* **EntityId**: the ID of the currently executing entity.
* **OperationName**: the name of the current operation.
* **HasState**: whether the entity exists, that is, has some state. 
* **GetState\<TState>()** : gets the current state of the entity. If it does not already exist, it is created and initialized to `default<TState>`. The `TState` parameter must be a primitive or JSON-serializeable type. 
* **GetState\<TState>(initfunction)** : gets the current state of the entity. If it does not already exist, it is created by calling the provided `initfunction` parameter. The `TState` parameter must be a primitive or JSON-serializeable type. 
* **SetState(arg)** : creates or updates the state of the entity. The `arg` parameter must be a JSON-serializeable object or primitive.
* **DeleteState()** : deletes the state of the entity. 
* **GetInput\<TInput>()** : gets the input for the current operation. The `TInput` type parameter must be a primitive or JSON-serializeable type.
* **Return(arg)** : returns a value to the orchestration that called the operation. The `arg` parameter must be a primitive or JSON-serializeable object.
* **SignalEntity(EntityId, operation, input)** : sends a one-way message to an entity. The `operation` parameter must be a non-null string, and the `input` parameter must be a primitive or JSON-serializeable object.
* **CreateNewOrchestration(orchestratorFunctionName, input)** : starts a new orchestration. The `input` parameter must be a primitive or JSON-serializeable object.

The `IDurableEntityContext` object passed to the entity function can be accessed using the `Entity.Current` async-local property. This approach is convenient when using the class-based programming model.

### <a name="trigger-sample-c-function-based-syntax"></a>Trigger sample (C# function-based syntax)

下列程式碼會舉例說明實作為持久函式的簡單「計數器」實體。 此函式會定義三個作業 (`add`、`reset` 和 `get`)，每個作業都會以整數狀態運作。

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

如需以函式為基礎的語法及其使用方式的詳細資訊，請參閱[以函式為基礎的語法](durable-functions-dotnet-entities.md#function-based-syntax)。

### <a name="trigger-sample-c-class-based-syntax"></a>Trigger sample (C# class-based syntax)

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

此實體的狀態是 `Counter` 類型的物件，其中包含可儲存計數器目前值的欄位。 為了將此物件保存在儲存體中，該物件會由 [Json.NET](https://www.newtonsoft.com/json) 程式庫進行序列化和還原序列化。 

如需以類別為基礎的語法及其使用方式的詳細資訊，請參閱[定義實體類別](durable-functions-dotnet-entities.md#defining-entity-classes)。

> [!NOTE]
> 使用實體類別時，具有 `[FunctionName]` 屬性的函式進入點方法「必須」 宣告為 `static`。 非靜態的進入點方法可能會導致初始化多個物件，並可能導致其他未定義的行為。

Entity classes have special mechanisms for interacting with bindings and .NET dependency injection. For more information, see [Entity construction](durable-functions-dotnet-entities.md#entity-construction).

### <a name="trigger-sample-javascript"></a>Trigger sample (JavaScript)

The following code is an example of a simple *Counter* entity implemented as a durable function written in JavaScript. 此函式會定義三個作業 (`add`、`reset` 和 `get`)，每個作業都會以整數狀態運作。

**function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

> [!NOTE]
> 從 `durable-functions` npm 套件 **1.3.0** 版開始，JavaScript 提供了耐久性實體。

## <a name="entity-client"></a>Entity client

The entity client binding enables you to asynchronously trigger [entity functions](#entity-trigger). These functions are sometimes referred to as [client functions](durable-functions-types-features-overview.md#client-functions).

If you're using Visual Studio, you can bind to the entity client by using the `DurableClientAttribute` .NET attribute.

> [!NOTE]
> The `[DurableClientAttribute]` can also be used to bind to the [orchestration client](#orchestration-client).

If you're using scripting languages (for example, *.csx* or *.js* files) for development, the entity trigger is defined by the following JSON object in the `bindings` array of *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "out"
}
```

* `taskHub` - 在多個函式應用程式共用相同儲存體帳戶，但是必須相互隔離的案例中使用。 若未指定，就會使用 `host.json` 的預設值。 This value must match the value used by the target entity functions.
* `connectionName` - 包含儲存體帳戶連接字串的應用程式設定名稱。 The storage account represented by this connection string must be the same one used by the target entity functions. 如果未指定，則會使用函數應用程式的預設儲存體帳戶連接字串。

> [!NOTE]
> In most cases, we recommend that you omit the optional properties and rely on the default behavior.

### <a name="entity-client-usage"></a>Entity client usage

In .NET functions, you typically bind to `IDurableEntityClient`, which gives you full access to all client APIs supported by Durable Entities. You can also bind to the `IDurableOrchestrationClient` interface, which provides access to client APIs for both entities and orchestrations. 用戶端物件上的 API 包括：

* **ReadEntityStateAsync\<T>** : reads the state of an entity. It returns a response that indicates whether the target entity exists, and if so, what its state is.
* **SignalEntityAsync**: sends a one-way message to an entity, and waits for it to be enqueued.

There is no need to create the target entity before sending a signal - the entity state can be created from within the entity function that handles the signal.

> [!NOTE]
> It's important to understand that the "signals" sent from the client are simply enqueued, to be processed asynchronously at a later time. In particular, the `SignalEntityAsync` usually returns before the entity even starts the operation, and it is not possible to get back the return value or observe exceptions. If stronger guarantees are required (e.g. for workflows), *orchestrator functions* should be used, which can wait for entity operations to complete, and can process return values and observe exceptions.

### <a name="example-client-signals-entity-directly---c"></a>Example: client signals entity directly - C#

Here is an example queue-triggered function that invokes a "Counter" entity.

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

### <a name="example-client-signals-entity-via-interface---c"></a>Example: client signals entity via interface - C#

Where possible, we recommend [accessing entities through interfaces](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) because it provides more type checking. For example, suppose the `Counter` entity mentioned earlier implemented an `ICounter` interface, defined as follows:

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

Client code can then use `SignalEntityAsync<ICounter>` to generate a type-safe proxy:

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

The `proxy` parameter is a dynamically generated instance of `ICounter`, which internally translates the call to `Add` into the equivalent (untyped) call to `SignalEntityAsync`.

> [!NOTE]
> The `SignalEntityAsync` APIs represent one-way operations. If an entity interfaces returns `Task<T>`, the value of the `T` parameter will always be null or `default`.

In particular, it does not make sense to signal the `Get` operation, as no value is returned. Instead, clients can use either `ReadStateAsync` to access the counter state directly, or can start an orchestrator function that calls the `Get` operation.

### <a name="example-client-signals-entity---javascript"></a>Example: client signals entity - JavaScript

Here is an example queue-triggered function that signals a "Counter" entity in JavaScript.

**function.json**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
      },
      {
        "name": "starter",
        "type": "durableClient",
        "direction": "in"
      }
    ],
    "disabled": false
  }
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> 從 `durable-functions` npm 套件 **1.3.0** 版開始，JavaScript 提供了耐久性實體。

<a name="host-json"></a>
## <a name="hostjson-settings"></a>host.json 設定

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Built-in HTTP API reference for instance management](durable-functions-http-api.md)
