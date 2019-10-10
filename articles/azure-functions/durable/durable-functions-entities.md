---
title: 持久性實體 - Azure Functions
description: 了解什麼是持久性實體，以及如何在 Azure Functions 的 Durable Functions 擴充功能中使用這些實體。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 864a641968268c439c65996998cbb822746b96f9
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838993"
---
# <a name="entity-functions-preview"></a>實體函式 (預覽)

實體函式會定義用於讀取和更新一小段狀態 (稱為「持久性實體」  ) 的作業。 和協調器函式一樣，實體函式也是具有特殊觸發程序類型 (「實體觸發程序」  ) 的函式。 和協調器函式不同的是，實體函式沒有任何特定的程式碼條件約束。 實體函式也會明確管理狀態，而不是透過控制流程來隱含表示狀態。

> [!NOTE]
> 只有 Durable Functions 2.0 和更新版本有提供實體函式和相關功能。 實體函式目前為公開預覽狀態。

## <a name="entity-identity"></a>實體身分識別

實體 (有時稱為實體「執行個體」  ) 可透過唯一識別碼 (「實體識別碼」  ) 來加以存取。 實體識別碼就只是一對可唯一識別實體執行個體的字串。 其中包括：

* **實體名稱**：可識別實體類型的名稱 (例如，「計數器」)。
* **實體索引鍵**：可在所有其他同名實體之間唯一識別實體的字串 (例如，GUID)。

例如，「計數器」  實體函式可用於在線上遊戲中保留分數。 遊戲的每個執行個體都會有唯一的實體識別碼，例如 `@Counter@Game1`、`@Counter@Game2` 等等。 以特定實體為目標的所有作業都必須將實體識別碼指定為參數。

## <a name="programming-models"></a>程式設計模型

持久性實體支援兩種不同的程式設計模型。 第一種模型是動態「函式」模型，其實體是由單一函式進行定義。 第二種模型是物件導向模型，其實體是由類別和方法進行定義。 這些模型以及用來與實體互動的程式設計模型會在後續幾節說明。

### <a name="defining-entities"></a>定義實體

有兩個選擇性的程式設計模型可用來製作持久性實體。 下列程式碼會舉例說明實作為標準函式的簡單「計數器」  實體。 此函式會定義三個「作業」  (`add`、`reset` 和 `get`)，這些作業各會以整數狀態值 `currentValue` 來運作。

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += amount;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

此模型最適合用來實作簡單的實體，或實作一組動態的作業。 不過，您也可以使用以類別為基礎的程式設計模型，這種模型適用於靜態、但實作方式更複雜的實體。 下列範例等同於使用類別和方法來實作 `Counter` 實體。

```csharp
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

> [!NOTE]
> 使用實體類別時，具有 `[FunctionName]` 屬性的函式進入點方法「必須」  宣告為 `static`。 非靜態的進入點方法可能會導致初始化多個物件，並可能導致其他未定義的行為。

在以類別為基礎的程式設計模型中，`IDurableEntityContext` 物件可在 `Entity.Current` 靜態屬性中使用。

以類別為基礎的模型類似於 [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/) 所推廣的程式設計模型。 在此模型中，實體類型會定義為 .NET 類別。 此類別的每個方法都是可供外部用戶端叫用的作業。 不過，與 Orleans 不同的是，.NET 介面是選擇性的。 先前的「計數器」  範例未使用介面，但仍可透過其他函式或透過 HTTP API 呼叫來加以叫用。

> [!NOTE]
> 實體觸發程序函式可在 Durable Functions 2.0 和更新版本中取得。 目前，實體觸發程序函式僅適用於 .NET 函式應用程式。

### <a name="accessing-entities-from-clients"></a>從用戶端存取實體

您可以使用[實體用戶端輸出繫結](durable-functions-bindings.md#entity-client)，從一般函式 (也稱為「用戶端函式」  ) 叫用或查詢持久性實體。 下列範例會說明由佇列觸發的函式，該函式會使用此繫結來進行實體的「訊號發送」  。

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

> [!NOTE]
> .NET 函式同時支援使用弱型別和型別安全方法來發送實體訊號。 如需詳細資訊，請參閱[實體用戶端繫結](durable-functions-bindings.md#entity-client-usage)參考文件。

「訊號」  一詞表示實體 API 的叫用是單向且非同步的。 「用戶端函式」  無法得知實體在何時處理作業，實體函式也無法將值傳回給用戶端函式。 單向的佇列型傳訊是針對持久性實體而刻意設計的，目的是為了將持久性放在效能前面。 相較於其他類似技術，持久性實體選擇這項設計是權衡之後的結果。 目前只有協調流程能夠處理來自實體的傳回值，下一節會有相關說明。

用戶端函式也可以查詢實體的狀態，如下列範例所示：

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    JObject state = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, state);
}
```

實體狀態查詢會傳送至持久性追蹤存放區，並傳回實體最近的「持久性」  狀態。 相較於實體的記憶體內部狀態，傳回的狀態可能會過時。 只有協調流程可以讀取實體的記憶體內部狀態，下一節會有相關說明。

### <a name="accessing-entities-from-orchestrations"></a>從協調流程存取實體

協調器函式可以使用[協調流程觸發程序繫結](durable-functions-bindings.md#orchestration-trigger)上的 API 來存取實體。 協調器函式可以選擇單向通訊 (射後不理，也稱為「訊號發送」  )，也可以選擇雙向通訊 (要求和回應，也稱為「呼叫」  )。 下列程式碼範例會說明進行「計數器」  實體「呼叫」  和「訊號發送」  的協調器函式。

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Synchronous call to the entity which returns a value - will await a response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // Asynchronous call which updates the value - will not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

只有協調流程能夠呼叫實體並取得回應，而此回應可能是傳回值或例外狀況。 使用[用戶端繫結](durable-functions-bindings.md#entity-client)的用戶端函式只能進行實體的「訊號發送」  。

> [!NOTE]
> 從協調器函式呼叫實體類似於從協調器函式呼叫[活動函式](durable-functions-types-features-overview.md#activity-functions)。 主要差異在於實體函式是具有位址 (「實體識別碼」  ) 的持久性物件，並支援指定作業名稱。 另一方面，活動函式則是無狀態的，而且沒有操作概念文件。

### <a name="dependency-injection-in-entity-classes-net"></a>在實體類別中插入相依性 (.NET)

實體類別支援 [Azure Functions 相依性插入](../functions-dotnet-dependency-injection.md)。 下列範例會示範如何將 `IHttpClientFactory` 服務註冊到以類別為基礎的實體。

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

下列程式碼片段會示範如何將插入的服務併入到實體類別。

```csharp
public class HttpEntity
{
    private readonly HttpClient client;

    public class HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    // The function entry point must be declared static
    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> 不同於在一般的 .NET Azure Functions 中使用建構函式插入時，以類別為基礎的實體「必須」  將函式進入點方法宣告為 `static`。 宣告非靜態函式進入點可能會導致一般 Azure Functions 物件初始設定式與持久性實體物件初始設定式之間發生衝突。

### <a name="bindings-in-entity-classes-net"></a>實體類別中的繫結 (.NET)

不同於一般函式，實體類別方法無法直接存取輸入和輸出繫結。 相反地，其必須在進入點函式宣告中擷取繫結資料，然後再傳遞至 `DispatchAsync<T>` 方法。 任何傳遞至 `DispatchAsync<T>` 的物件都會自動以引數的形式傳遞至實體類別建構函式。

下列範例會說明如何讓以類別為基礎的實體可以使用來自 [Blob 輸入繫結](../functions-bindings-storage-blob.md#input)的 `CloudBlobContainer` 參考。

```csharp
public class BlobBackedEntity
{
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

如需 Azure Functions 中繫結的詳細資訊，請參閱 [Azure Functions 觸發程序和繫結](../functions-triggers-bindings.md)文件。

## <a name="entity-coordination"></a>實體協調

有時候您可能需要協調多個實體的作業。 例如，在銀行應用程式中，您可能有代表個別銀行帳戶的實體。 將資金從某個帳戶轉移到另一個帳戶時，您必須確定「來源」  帳戶具有足夠的資金，而且會以交易一致的方式同時完成「來源」  和「目的地」  帳戶的更新。

### <a name="transfer-funds-example-in-c"></a>C# 的傳輸資金範例

下列程式碼範例會使用協調器函式，在兩個「帳戶」  實體之間傳輸資金。 若要協調實體更新，就必須使用 `LockAsync` 方法在協調流程中建立「重要區段」  ：

> [!NOTE]
> 為了簡單起見，此範例會重複使用先前定義的 `Counter` 實體。 不過，在實際的應用程式中，最好改為定義更詳細的 `BankAccount` 實體。

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

在 .NET 中，`LockAsync` 會傳回 `IDisposable` 以在完成處置時結束重要區段。 這個 `IDisposable` 結果可以與 `using` 區塊一起使用，以獲得用語法表示的重要區段。

在上述範例中，協調器函式會將資金從「來源」  實體轉移到「目的地」  實體。 `LockAsync` 方法已鎖定「來源」  和「目的地」  帳戶實體。 此鎖定確保了在協調流程邏輯於 `using` 陳述式結束而離開「重要區段」  之前，其他所有用戶端皆無法查詢或修改任一帳戶的狀態。 這可以有效防止「來源」  帳戶產生透支的可能性。

### <a name="critical-section-behavior"></a>重要區段行為

`LockAsync` 方法會在協調流程中建立「重要區段」  。 這些「重要區段」  會防止其他協調流程對一組指定的實體進行重疊變更。 從內部來看，`LockAsync` API 會將「鎖定」作業傳送至實體，並在收到來自上述每個相同實體的「已取得鎖定」回應訊息時傳回。 「鎖定」  和「解除鎖定」  都是所有實體皆會支援的內建作業。

當實體處於鎖定狀態時，就不會允許來自其他用戶端的任何作業。 此行為可確保一次只有一個協調流程執行個體可以鎖定實體。 如果呼叫端嘗試在協調流程鎖定的實體上叫用作業，該作業將會放入「擱置作業佇列」  。 在握有鎖定實體的協調流程釋放該鎖定之前，系統不會處理任何擱置作業。

> [!NOTE] 
> 這與大多數程式設計語言 (例如，C# 中的 `lock` 陳述式) 中使用的同步處理基元有些微不同。 例如在 C# 中，所有執行緒都必須使用 `lock` 陳述式，以確保多個執行緒會正確同步。 不過，實體則不會要求所有呼叫端明確「鎖定」  實體。 如果有任何呼叫端鎖定實體，該實體上的所有其他作業將會遭到封鎖，並排入到佇列中的該鎖定之後。

實體的鎖定具有持久性，因此即使執行中的程序已回收，鎖定仍會保留下來。 在內部，鎖定會保留下來而成為實體持久性狀態的一部分。

### <a name="critical-section-restrictions"></a>重要區段限制

我們對重要區段的使用方式施加了幾項限制。 這些限制是為了避免鎖死和重新進入。

* 重要區段無法形成巢狀結構。
* 重要區段無法建立子協調流程。
* 重要區段只能呼叫其已鎖定的實體。
* 重要區段無法使用多個平行呼叫來呼叫相同實體。
* 重要區段只能對其尚未鎖定的實體進行訊號發送。

## <a name="comparison-with-virtual-actors"></a>與虛擬執行者的比較

許多持久性實體的功能都受到[執行者模型](https://en.wikipedia.org/wiki/Actor_model)的啟發。 如果您已熟悉執行者，或許就能認出本文所述的許多概念。 具體來說，持久性實體在許多方面與[虛擬執行者](https://research.microsoft.com/projects/orleans/)類似：

* 持久性實體可透過「實體識別碼」  定址。
* 持久性實體的作業會以序列方式一次執行一個，以免產生競爭條件。
* 系統會在呼叫或發出訊號時自動建立持久性實體。
* 沒有執行中的作業時，會以無訊息方式從記憶體中卸載持久性實體。

不過，有一些值得注意的重要差異：

* 持久性實體更看重「持久性」  而非「延遲」  ，因此可能不適合具有嚴格延遲需求的應用程式。
* 實體之間傳送的訊息會以可靠的方式依序傳遞。
* 持久性實體可與持久性協調流程搭配使用，並支援分散式鎖定機制。
* 實體中的要求/回應模式僅限於協調流程。 如同原始執行者模型，針對「用戶端對實體」  和「實體對實體」  的通訊，只允許單向傳訊 (也稱為「訊號發送」)。 此行為可防止分散式的鎖死。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解工作中樞](durable-functions-task-hubs.md)
