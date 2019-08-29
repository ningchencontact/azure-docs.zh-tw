---
title: Durable Functions 預覽功能-Azure Functions
description: 瞭解 Durable Functions 的預覽功能。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: article
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 94784ee9a72d11e168828f1543cbe62219ac4a74
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097946"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2.0 preview (Azure Functions)

*Durable Functions* 是 [Azure Functions](../functions-overview.md) 和 [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) 的擴充功能，可讓您在無伺服器環境中撰寫具狀態函式。 此擴充功能會為您管理狀態、設定檢查點和重新啟動。 如果您還不熟悉 Durable Functions, 請參閱[總覽檔](durable-functions-overview.md)。

Durable Functions 1.x 是 Azure Functions 的 GA (正式推出) 功能, 但也包含數個目前處於公開預覽狀態的子功能。 本文說明新發行的預覽功能, 並詳細說明其運作方式, 以及您可以如何開始使用它們。

> [!NOTE]
> 這些預覽功能屬於 Durable Functions 2.0 版本的一部分, 這是目前有數個重大變更的**預覽品質版本**。 您可以在 nuget.org 上找到 Azure Functions 的長期延伸模組封裝組建, 其版本的格式為**2.0.0-BetaX**。 這些組建並非適用于生產工作負載, 而後續版本可能包含額外的重大變更。

## <a name="breaking-changes"></a>中斷性變更

Durable Functions 2.0 中引進了幾項重大變更。 現有的應用程式不應與 Durable Functions 2.0 相容, 而不需要變更程式碼。 本節列出一些變更:

### <a name="hostjson-schema"></a>Host. json 架構

下列程式碼片段顯示適用于 host 的新架構。 要注意的主要變更是新的小節:

* `"storageProvider"`(和`"azureStorage"`子區段) 用於儲存特定設定
* `"tracking"`用於追蹤和記錄設定
* `"notifications"`事件方格通知`"eventGrid"`設定的 (和子區段)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

因為 Durable Functions 2.0 會持續穩定, 所以會在 host. json `durableTask`區段中引進更多變更。 如需這些變更的詳細資訊, 請參閱[此 GitHub 問題](https://github.com/Azure/azure-functions-durable-extension/issues/641)。

### <a name="public-interface-changes"></a>公用介面變更

Durable Functions 支援的各種「內容」物件具有抽象基類, 供單元測試使用。 在 Durable Functions 2.0 中, 這些抽象基類已取代為介面。 直接使用實體類型的函式程式碼不會受到影響。

下表代表主要變更:

| 舊類型 | 新增類型 |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

在抽象基類包含虛擬方法的情況下, 這些虛擬方法已由中`DurableContextExtensions`定義的擴充方法所取代。

## <a name="entity-functions"></a>實體函數

實體函式會定義讀取和更新部分狀態 (稱為「*持久實體*」) 的作業。 就像協調器函式一樣, 實體函式是具有特殊觸發程式類型 (*實體觸發*程式) 的函數。 與協調器函式不同的是, 實體函式沒有任何特定的程式碼條件約束。 實體函數也會明確管理狀態, 而不是透過控制流程隱含表示狀態。

### <a name="net-programing-models"></a>.NET 程式設計模型

有兩個選擇性的程式設計模型, 可用於撰寫持久性實體。 下列程式碼是實作為標準函數的簡單*計數器*實體範例。 此函式會定義三`add`個`reset`*作業*: `get`、和, 其中`currentValue`每一個都是以整數狀態值來運作。

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
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

此模型最適合用於簡單的實體執行, 或是具有一組動態作業的實作為。 不過, 也有一個以類別為基礎的程式設計模型, 適用于靜態但具有更複雜的實作為實體。 下列範例是使用 .net 類別和方法的`Counter`實體對等執行。

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

以類別為基礎的模型類似于[奧爾良](https://www.microsoft.com/research/project/orleans-virtual-actors/)普及化的程式設計模型。 在此模型中, 實體類型會定義為 .NET 類別。 類別的每個方法都是可供外部用戶端叫用的作業。 不過, 與奧爾良不同的是, .NET 介面是選擇性的。 先前的*計數器*範例未使用介面, 但仍可透過其他函式或透過 HTTP API 呼叫來叫用。

實體*實例*是透過唯一識別碼 (*實體 ID*) 來存取。 實體識別碼只是一對可唯一識別實體實例的字串。 其中包括：

* **機構名稱**: 識別實體類型的名稱 (例如, "Counter")。
* **實體索引鍵**: 可在相同名稱的所有其他實體之間唯一識別實體的字串 (例如, GUID)。

例如,*計數器*實體函式可用於在線上遊戲中保持分數。 遊戲的每個實例都有唯一的實體識別碼, 例如`@Counter@Game1`、 `@Counter@Game2`等等。

### <a name="comparison-with-virtual-actors"></a>與虛擬執行者的比較

持久性實體的設計會受到動作專案[模型](https://en.wikipedia.org/wiki/Actor_model)的嚴重影響。 如果您已經熟悉動作專案, 則應該熟悉持久性實體背後的概念。 特別的是, 持久性實體會以許多方式與[虛擬執行者](https://research.microsoft.com/projects/orleans/)類似:

* 持久實體可透過*實體識別碼*定址。
* 持久性實體作業一次會以序列方式執行, 以防止競爭條件。
* 當系統呼叫或發出信號時, 會自動建立持久性實體。
* 當不執行作業時, 會以無訊息方式從記憶體中卸載持久性實體。

不過, 有一些重要的差異值得注意:

* 持久性實體會優先處理*延遲*, 因此可能不適合具有嚴格延遲需求的應用程式。
* 實體之間傳送的訊息會可靠且依序傳遞。
* 持久性實體可以與長期協調流程搭配使用, 而且可以做為分散式鎖定, 本文稍後會加以說明。
* 實體中的要求/回應模式僅限於協調流程。 就實體到實體的通訊而言, 只允許單向訊息 (也稱為「信號」), 如同原始動作專案模型一樣。 此行為可防止分散式的鎖死。

### <a name="durable-entity-net-apis"></a>持久實體 .NET Api

實體支援牽涉到數個 Api。 其中一個是定義實體函式的新 API, 如上所示, 它會指定在實體上叫用作業時應該發生的情況。 此外, 用戶端和協調流程的現有 Api 已更新為與實體互動的新功能。

#### <a name="implementing-entity-operations"></a>執行實體作業

在實體上執行作業, 可以在內容物件 (`IDurableEntityContext` .net) 上呼叫這些成員:

* **OperationName**: 取得作業的名稱。
* **Getinputt>\<TInput >** : 取得作業的輸入。
* **GetState\<TState >** : 取得實體的目前狀態。
* **SetState**: 更新實體的狀態。
* **SignalEntity**: 將單向訊息傳送至實體。
* **Self**: 取得實體的識別碼。
* **Return**: 傳回值給呼叫作業的用戶端或協調流程。
* **IsNewlyConstructed**: `true`如果實體在作業之前不存在, 則傳回。
* **DestructOnExit**: 在完成作業之後刪除實體。

作業的限制低於協調流程:

* 作業可以使用同步或非同步 Api 呼叫外部 i/o (建議僅使用非同步)。
* 作業可以不具決定性。 例如, 您可以放心地呼叫`DateTime.UtcNow`、 `Guid.NewGuid()`或`new Random()`。

#### <a name="accessing-entities-from-clients"></a>從用戶端存取實體

您可以透過系結 ( `orchestrationClient` `IDurableOrchestrationClient`在 .net 中) 從一般函式叫用持久實體。 支援下列方法:

* **ReadEntityStateAsync\<T >** : 讀取實體的狀態。
* **SignalEntityAsync**: 將單向訊息傳送至實體, 並等候它排入佇列。
* **SignalEntityAsync\<T >** : 與相同`SignalEntityAsync` , 但使用類型`T`的產生的 proxy 物件。

先前`SignalEntityAsync`的呼叫需要將實體作業的名稱指定`string`為, 並將作業的裝載做為`object`。 下列範例程式碼是此模式的範例:

```csharp
EntityId id = // ...
object amount = 5;
context.SignalEntityAsync(id, "Add", amount);
```

也可以產生型別安全存取的 proxy 物件。 若要產生型別安全 proxy, 實體型別必須執行介面。 例如, 假設先前`Counter` `ICounter`所述的實體實作為介面, 定義如下:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    int Get();
}

public class Counter : ICounter
{
    // ...
}
```

接著, 用戶端程式`SignalEntityAsync<T>`代碼可以使用`ICounter` , 並將介面指定為型別參數, 以產生型別安全 proxy。 下列程式碼範例示範如何使用型別安全 proxy:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [OrchestrationClient] IDurableOrchestrationClient client)
{
    int amount = int.Parse(message);
    var target = new EntityId(nameof(Counter), "MyCounter");
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

在上述`proxy`範例中, 參數是動態產生的`ICounter`實例, 它會`Add`在內部將的呼叫轉譯為對等的 (不具類型`SignalEntityAsync`) 呼叫。

的型別參數`SignalEntityAsync<T>`具有下列限制:

* 型別參數必須是介面。
* 只有方法可以在介面上定義。 不支援屬性。
* 每個方法都必須定義一或多個參數。
* 每個方法都必須`void`傳回`Task`、或`Task<T>` , `T`其中是某些 JSON 可序列化類型。
* 介面必須剛好由介面元件內的一個型別來執行。

在大部分情況下, 不符合這些需求的介面會導致執行時間例外狀況。

> [!NOTE]
> 請務必注意, 的和`ReadEntityStateAsync` `SignalEntityAsync`方法`IDurableOrchestrationClient`會將效能優先于一致性。 `ReadEntityStateAsync`可以傳回過時的值, 而且`SignalEntityAsync`可以在作業完成之前傳回。

#### <a name="accessing-entities-from-orchestrations"></a>從協調流程存取實體

協調流程可以使用`IDurableOrchestrationContext`物件來存取實體。 他們可以選擇單向通訊 (火災和忘) 和雙向通訊 (要求和回應)。 各自的方法如下:

* **SignalEntity**: 將單向訊息傳送至實體。
* **CallEntityAsync**: 將訊息傳送至實體, 並等候回應, 表示作業已完成。
* **CallEntityAsync\<T >** : 將訊息傳送至實體, 並等候包含型別 T 結果的回應。

使用雙向通訊時, 在作業執行期間擲回的任何例外狀況也會傳回給呼叫的協調流程, 並重新擲回。 相反地, 在使用火災和忘時, 不會觀察到例外狀況。

針對型別安全存取, 協調流程函數可以根據介面產生 proxy。 `CreateEntityProxy`擴充方法可用於此用途:

```csharp
public interface IAsyncCounter
{
    Task AddAsync(int amount);
    Task ResetAsync();
    Task<int> GetAsync();
}

[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...
    IAsyncCounter proxy = context.CreateEntityProxy<IAsyncCounter>("MyCounter");
    await proxy.AddAsync(5);
    int newValue = await proxy.GetAsync();
    // ...
}
```

在上述範例中, 假設「計數器」實體已存在, 它`IAsyncCounter`會執行介面。 然後, 協調流程可以使用`IAsyncCounter`型別定義來產生 proxy 型別, 以便與實體同步互動。

### <a name="locking-entities-from-orchestrations"></a>從協調流程鎖定實體

協調流程可以鎖定實體。 這項功能提供簡單的方法, 讓您使用*重要區段*來防止不必要的爭用。

內容物件提供下列方法:

* **LockAsync**: 取得一或多個實體的鎖定。
* **IsLocked**: 如果目前在重要區段中, 則傳回 true, 否則傳回 false。

關鍵區段結束, 而且在協調流程結束時釋放所有鎖定。 在 .net 中`LockAsync` , `IDisposable`會傳回在處置時結束重要區段的, 這可以與`using`子句一起使用, 以取得重要區段的語法標記法。

例如, 假設有一個協調流程需要測試兩個玩家是否可用, 然後將它們指派給遊戲。 這項工作可以使用重要區段來執行, 如下所示:

```csharp
[FunctionName("Orchestrator")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext ctx)
{
    EntityId player1 = /* ... */;
    EntityId player2 = /* ... */;

    using (await ctx.LockAsync(player1, player2))
    {
        bool available1 = await ctx.CallEntityAsync<bool>(player1, "is-available");
        bool available2 = await ctx.CallEntityAsync<bool>(player2, "is-available");

        if (available1 && available2)
        {
            Guid gameId = ctx.NewGuid();

            await ctx.CallEntityAsync(player1, "assign-game", gameId);
            await ctx.CallEntityAsync(player2, "assign-game", gameId);
        }
    }
}
```

在重要區段中, 兩個玩家實體都會被鎖定, 這表示它們不會執行任何作業, 而不是從重要區段內呼叫。 此行為可防止發生衝突的作業, 例如指派給不同遊戲或登出的玩家。

我們會對如何使用重要區段有幾項限制。 這些限制是為了避免鎖死和重新進入。

* 無法嵌套重要區段。
* 重要區段無法建立 suborchestrations。
* 重要區段只能呼叫已鎖定的實體。
* 重要區段無法使用多個平行呼叫來呼叫相同的實體。
* 重要區段可以只通知未鎖定的實體。

## <a name="alternate-storage-providers"></a>替代儲存提供者

長期工作架構目前支援多個儲存提供者, 包括[Azure 儲存體](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage)、 [Azure 服務匯流排](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus)、[記憶體內部模擬器](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator), 以及實驗性[Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis)提供者。 不過, 到目前為止, Azure Functions 的持久工作延伸僅支援 Azure 儲存體提供者。 從 Durable Functions 2.0 開始, 正在新增替代儲存提供者的支援, 從 Redis 提供者開始。

> [!NOTE]
> Durable Functions 2.0 只支援 .NET Standard 2.0 相容的提供者。 在撰寫本文時, Azure 服務匯流排提供者不支援 .NET Standard 2.0, 因此無法以替代儲存提供者的形式提供。

### <a name="emulator"></a>模擬器

[DurableTask](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/)提供者是本機記憶體、非持久性儲存提供者, 適用于本機測試案例。 您可以使用下列最少的 host 來設定它 **。 json**架構:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (實驗性)

[DurableTask. Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/)提供者會將所有協調流程狀態保存到設定的 Redis 叢集。

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

`connectionStringName`必須參考應用程式設定或環境變數的名稱。 該應用程式設定或環境變數應包含 [*伺服器: 埠*] 格式的 Redis 連接字串值。 例如, `localhost:6379`用於連接到本機 Redis 叢集。

> [!NOTE]
> Redis 提供者目前為實驗性, 只支援在單一節點上執行的函數應用程式。 不保證 Redis 提供者會正式推出, 而且可能會在未來的版本中移除。
