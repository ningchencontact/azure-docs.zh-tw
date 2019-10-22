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
ms.openlocfilehash: e3a83730e47686e9d4757f057d2e8da4629fdd7a
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312131"
---
# <a name="entity-functions-preview"></a>實體函式 (預覽)

實體函式會定義用於讀取和更新一小段狀態 (稱為「持久性實體」  ) 的作業。 和協調器函式一樣，實體函式也是具有特殊觸發程序類型 (「實體觸發程序」  ) 的函式。 不同於協調器函式，實體函式會明確管理實體的狀態，而不是透過控制流程來隱含表示狀態。
實體提供一個方法來擴充應用程式，方法是將工作分散到許多實體，而每個實體都有適度大小的狀態。

> [!NOTE]
> 只有 Durable Functions 2.0 和更新版本有提供實體函式和相關功能。 實體函式目前為公開預覽狀態。

## <a name="general-concepts"></a>一般概念

實體的行為有點像透過訊息進行通訊的小型服務。 每個實體都有唯一的身分識別和內部狀態 (如果有的話)。 就像服務或物件一樣，實體會在出現提示時執行作業。 當它執行時，作業可能會更新實體的內部狀態。 它也可以呼叫外部服務並等候回應。 實體會使用透過可靠佇列隱含傳送的訊息，與其他實體、協調流程和用戶端進行通訊。 

為了避免衝突，單一實體上的所有作業保證會以序列方式執行，也就是一個接一個執行。 

### <a name="entity-id"></a>實體識別碼
實體是透過唯一識別碼 (即「實體識別碼」  ) 來存取。 實體識別碼就只是一對可唯一識別實體執行個體的字串。 其中包括：

* **實體名稱**：可識別實體類型的名稱 (例如，「計數器」)。 此名稱必須符合實作為實體的實體函式名稱。 它不會區分大小寫。
* **實體索引鍵**：可在所有其他同名實體之間唯一識別實體的字串 (例如，GUID)。

例如，「計數器」  實體函式可用於在線上遊戲中保留分數。 遊戲的每個執行個體都會有唯一的實體識別碼，例如 `@Counter@Game1`、`@Counter@Game2` 等等。 以特定實體為目標的所有作業都必須將實體識別碼指定為參數。

### <a name="entity-operations"></a>實體作業 ###

若要在實體上叫用作業，使用者可指定：

* 目標實體的「實體識別碼」 
* 「作業名稱」  ，這是指定要執行之作業的字串。 例如，計數器實體可支援 "add"、"get" 或 "reset" 作業。
* 「作業輸入」  ，這是作業的選擇性輸入參數。 例如，"add" 作業可接受整數數量作為輸入。

作業可以傳回結果值或錯誤結果 (例如 JavaScript 錯誤或 .NET 例外狀況)。 呼叫作業的協調流程可以觀察到此結果或錯誤。

實體作業也可以建立、讀取、更新和刪除實體的狀態。 實體的狀態一律會永久保存在儲存體中。

## <a name="defining-entities"></a>定義實體

我們目前提供兩個不同的 API 以供定義實體。

**以函式為基礎的語法**，其中實體會表示為函式，而作業會由應用程式明確分派。 此語法很適合於具有簡單狀態、少數作業或一組動態作業 (例如在應用程式架構中) 的實體。 不過，其維護可能很單調乏味，因為它不會在編譯時期攔截類型錯誤。

**以類別為基礎的語法**，其中實體和作業是由類別和方法表示。 此語法會產生更易讀取的程式碼，並允許以型別安全的方式叫用作業。 以類別為基礎的語法只是以函式為基礎的語法之上的精簡層，因此這兩個變體可以在相同的應用程式中交替使用。

### <a name="example-function-based-syntax"></a>範例：以函式為基礎的語法

下列程式碼會舉例說明實作為持久函式的簡單「計數器」  實體。 此函式會定義三個作業 (`add`、`reset` 和 `get`)，每個作業都會以整數狀態運作。

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

### <a name="example-class-based-syntax"></a>範例：以類別為基礎的語法

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

## <a name="accessing-entities"></a>存取實體

您可使用單向或雙向通訊來存取實體。 我們使用下列術語來區別： 

* **呼叫**實體表示我們使用雙向 (來回) 通訊：我們會將作業訊息傳送至實體，然後等候回應訊息，再繼續進行。 回應訊息可提供結果值或錯誤結果 (例如 JavaScript 錯誤或 .NET 例外狀況)。 接著呼叫端會觀察到此結果或錯誤。
* **傳送訊號**給實體表示我們使用單向 (射後不理) 通訊：我們會傳送作業訊息，但不等待回應。 雖然訊息保證最終會傳遞，但傳送者不知道何時，且無法觀察到任何結果值或錯誤。

您可以從用戶端函式、協調器函式或實體函式內存取實體。 並非所有形式的通訊都受到所有內容支援：

* 從用戶端，您可以「傳送訊號」  給實體，而且可以「讀取」  實體狀態。
* 從協調流程，您可以「傳送訊號」  給實體，而且可以「呼叫」  實體。
* 從實體，您可以「傳送訊號」  給實體。

以下顯示的一些範例會說明用於存取實體的各種方式。

> [!NOTE]
> 為了簡單起見，下列範例會顯示用於存取實體的鬆散型別語法。 一般來說，我們建議[透過介面存取實體](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces)，因為它提供更多型別檢查。

### <a name="example-client-signals-an-entity"></a>範例：用戶端傳送訊號給實體

若要從一般 Azure 函式 (也稱為「用戶端函式」  ) 存取實體 - 請使用[實體用戶端輸出繫結](durable-functions-bindings.md#entity-client)。 下列範例會說明由佇列觸發的函式，該函式會使用此繫結來進行實體的「訊號發送」  。

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

「訊號」  一詞表示實體 API 的叫用是單向且非同步的。 「用戶端函式」  不可能知道實體處理作業的時間。 此外，用戶端函式無法觀察到任何結果值或例外狀況。 

### <a name="example-client-reads-an-entity-state"></a>範例：用戶端讀取實體狀態

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

實體狀態查詢會傳送至持久性追蹤存放區，並傳回實體最近的「持久性」  狀態。 此狀態一律是「已認可」狀態，也就是在執行作業的過程中，永遠不會有暫時的中繼狀態。 不過，相較於實體的記憶體內部狀態，此狀態可能會過時。 只有協調流程可以讀取實體的記憶體內部狀態，下一節會有相關說明。

### <a name="example-orchestration-signals-and-calls-an-entity"></a>範例：協調流程傳送訊號並呼叫實體

協調器函式可以使用[協調流程觸發程序繫結](durable-functions-bindings.md#orchestration-trigger)上的 API 來存取實體。 下列程式碼範例會說明進行「計數器」  實體「呼叫」  和「訊號發送」  的協調器函式。

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

   // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

只有協調流程能夠呼叫實體並取得回應，而此回應可能是傳回值或例外狀況。 使用[用戶端繫結](durable-functions-bindings.md#entity-client)的用戶端函式只能進行實體的「訊號發送」  。

> [!NOTE]
> 從協調器函式呼叫實體類似於從協調器函式呼叫[活動函式](durable-functions-types-features-overview.md#activity-functions)。 主要差異在於實體函式是具有位址 (「實體識別碼」  ) 的持久性物件，並支援指定作業名稱。 另一方面，活動函式則是無狀態的，而且沒有操作概念文件。

### <a name="example-entity-signals-an-entity"></a>範例：實體傳送訊號給實體

實體函式可以在執行作業時，傳送訊號給其他實體 (或甚至傳送給本身！)。
例如，我們可以修改上述的計數器實體範例，以便在計數器達到值 100 時，傳送「達到里程碑」訊號給某個監視器實體：

```csharp
   case "add":
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }
        currentValue += amount;
        break;
```

下列程式碼片段會示範如何將插入的服務併入到實體類別。

```csharp
public class HttpEntity
{
    private readonly HttpClient client;

    public HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public async Task<int> GetAsync(string url)
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

### <a name="example-transfer-funds"></a>範例：轉移資金

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

> [!NOTE] 
> 當協調流程終止 (按慣例或因為錯誤) 時，任何進行中的關鍵區段都會隱含地結束，而且會釋放所有的鎖定。

### <a name="critical-section-behavior"></a>重要區段行為

`LockAsync` 方法會在協調流程中建立「重要區段」  。 這些「重要區段」  會防止其他協調流程對一組指定的實體進行重疊變更。 從內部來看，`LockAsync` API 會將「鎖定」作業傳送至實體，並在收到來自上述每個相同實體的「已取得鎖定」回應訊息時傳回。 「鎖定」  和「解除鎖定」  都是所有實體皆會支援的內建作業。

當實體處於鎖定狀態時，就不會允許來自其他用戶端的任何作業。 此行為可確保一次只有一個協調流程執行個體可以鎖定實體。 如果呼叫端嘗試在協調流程鎖定的實體上叫用作業，該作業將會放入「擱置作業佇列」  。 在握有鎖定實體的協調流程釋放該鎖定之前，系統不會處理任何擱置作業。

> [!NOTE] 
> 這與大多數程式設計語言 (例如，C# 中的 `lock` 陳述式) 中使用的同步處理基元有些微不同。 例如在 C# 中，所有執行緒都必須使用 `lock` 陳述式，以確保多個執行緒會正確同步。 不過，實體則不會要求所有呼叫端明確「鎖定」  實體。 如果有任何呼叫端鎖定實體，該實體上的所有其他作業將會遭到封鎖，並排入到佇列中的該鎖定之後。

實體的鎖定具有持久性，因此即使執行中的程序已回收，鎖定仍會保留下來。 在內部，鎖定會保留下來而成為實體持久性狀態的一部分。

與交易不同的是，關鍵區段不會在發生錯誤時自動復原變更。 然而，任何錯誤處理 (復原、重試或其他) 都必須明確地編碼；例如，藉由攔截錯誤或例外狀況。 這是刻意設計的選擇。 自動復原協調流程的所有效果，通常很難或根本不可能，因為協調流程可以執行活動並呼叫無法復原的外部服務。 此外，復原嘗試可能會失敗，而且需要進一步的錯誤處理。

### <a name="critical-section-rules"></a>關鍵區段規則

不同於大部分程式設計語言中的低階鎖定基元，關鍵區段**保證不會鎖死**。 為了避免鎖死，我們會強制執行下列限制： 

* 重要區段無法形成巢狀結構。
* 重要區段無法建立子協調流程。
* 重要區段只能呼叫其已鎖定的實體。
* 重要區段無法使用多個平行呼叫來呼叫相同實體。
* 重要區段只能對其尚未鎖定的實體進行訊號發送。

違反這些規則會造成執行階段錯誤 (例如 .NET 中的 `LockingRulesViolationException`)，其中包含說明違反哪項規則的訊息。

## <a name="comparison-with-virtual-actors"></a>與虛擬執行者的比較

許多持久性實體的功能都受到[執行者模型](https://en.wikipedia.org/wiki/Actor_model)的啟發。 如果您已熟悉執行者，或許就能認出本文所述的許多概念。 持久性實體特別類似於 [Orleans 專案 ](http://dotnet.github.io/orleans/)所推廣的[虛擬動作項目](https://research.microsoft.com/projects/orleans/)或「粒紋」  。 例如︰

* 持久性實體可透過「實體識別碼」  定址。
* 持久性實體的作業會以序列方式一次執行一個，以免產生競爭條件。
* 系統會在呼叫或發出訊號時隱含地建立持久性實體。
* 沒有執行中的作業時，會以無訊息方式從記憶體中卸載持久性實體。

不過，有一些值得注意的重要差異：

* 持久性實體更看重「持久性」  而非「延遲」  ，因此可能不適合具有嚴格延遲需求的應用程式。
* 持久性實體沒有內建的訊息逾時。 在 Orleans 中，所有訊息都會在可設定的時間 (預設為30秒) 過後逾時。
* 實體之間傳送的訊息會以可靠的方式依序傳遞。 在 Orleans 中，透過資料流傳送的內容可支援可靠或排序的傳遞，但不保證會傳遞粒紋之間的所有訊息。
* 實體中的要求/回應模式僅限於協調流程。 如同原始動作項目模型 (而不像 Orleans 中的粒紋)，從實體，只允許單向傳訊 (也稱為「傳送訊號」)。 
* 持久性實體不會鎖死。 在 Orleans 中，可能會發生鎖死 (且直到訊息逾期才會解決)。
* 持久性實體可與持久性協調流程搭配使用，並支援分散式鎖定機制。 


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [閱讀 .NET 中持久性實體的開發人員指南](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [了解工作中樞](durable-functions-task-hubs.md)
