---
title: Durable Functions 預覽功能-Azure Functions
description: Durable functions，以了解預覽功能。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.date: 04/23/2019
ms.author: azfuncdf
ms.openlocfilehash: 8ceb84ab9e9c41ff6a9cbde62571fb12ae67d790
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65596071"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2.0 預覽 (Azure Functions)

*Durable Functions* 是 [Azure Functions](../functions-overview.md) 和 [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) 的擴充功能，可讓您在無伺服器環境中撰寫具狀態函式。 此擴充功能會為您管理狀態、設定檢查點和重新啟動。 如果您已經熟悉如何使用 Durable Functions，請參閱[概觀文件](durable-functions-overview.md)。

Durable Functions 是 Azure Functions 的 GA （正式推出） 功能，但也包含數個目前處於公開預覽狀態的子功能。 本文章說明新發行的預覽功能，並進入詳細資料，在它們的運作方式，以及如何開始使用它們。

> [!NOTE]
> 這些預覽功能是 Durable Functions 2.0 版本中，也就是目前的一部分**alpha 品質版本**具有幾項重大變更。 與版本的形式在 nuget.org 上可以找到 Azure Functions 長期延伸模組套件建置**2.0.0-alpha**。 這些組建並不適合任何生產工作負載，及後續版本可能包含其他的重大變更。

## <a name="breaking-changes"></a>重大變更

在 Durable Functions 2.0 導入幾項重大變更。 現有的應用程式不應該為無須變更程式碼與 Durable Functions 2.0 相容。 此區段會列出的一些變更：

### <a name="dropping-net-framework-support"></a>卸除之.NET Framework 支援

已卸除 Durable Functions 2.0 的.NET Framework，因此 Functions 1.0） 的支援。 主要的原因是要啟用非 Windows 參與者，輕鬆地建置及測試它們從 macOS 和 Linux 平台進行 Durable Functions 的變更。 次要的原因是為了協助鼓勵開發人員將移至最新版本的 Azure Functions 執行階段。

### <a name="hostjson-schema"></a>Host.json 結構描述

下列程式碼片段說明 host.json 的新結構描述。 要注意的主要變更是新`"storageProvider"`區段中，而`"azureStorage"`區段底下。 這項變更為了支援[替代儲存體提供者](durable-functions-preview.md#alternate-storage-providers)。

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
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "traceInputAndOutputs": <bool?>,
      "eventGridTopicEndpoint": <string?>,
      "eventGridKeySettingName": <string?>,
      "eventGridPublishRetryCount": <string?>,
      "eventGridPublishRetryInterval": <hh:mm:ss?>,
      "eventGridPublishRetryHttpStatus": <int[]?>,
      "eventgridPublishEventTypes": <string[]?>,
      "customLifeCycleNotificationHelperType"
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "logReplayEvents": <bool?>
  }
}
```

隨著 Durable Functions 2.0 持續穩定，更多的變更將會為您介紹`durableTask`區段 host.json。 如需有關這些變更的詳細資訊，請參閱 <<c0> [ 此 GitHub 問題](https://github.com/Azure/azure-functions-durable-extension/issues/641)。

### <a name="public-interface-changes"></a>公用介面變更

Durable Functions 所支援的各種 「 內容 」 物件具有適合在單元測試中使用的抽象基底類別。 Durable Functions 2.0 的一部分，已取代這些抽象的基底類別與介面。 不會影響函式程式碼直接使用具象型別。

下表代表主要變更：

| 舊的類型 | 新的類型 |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

在抽象基底類別，其中包含虛擬方法的情況下，這些虛擬方法已取代中所定義的擴充方法`DurableContextExtensions`。

## <a name="entity-functions"></a>實體函式

實體函式定義作業讀取及更新小片段狀態，稱為*長期實體*。 協調器函式，例如實體函式函式是特殊的觸發程序型別*實體的觸發程序*。 不同協調器函式中，於實體函式沒有任何特定的程式碼條件約束。 實體函式也會管理狀態明確而不是以隱含方式代表透過控制流程的狀態。

下列程式碼是簡單實體函式定義的範例*計數器*實體。 此函式定義三項作業， `add`， `subtract`，並`reset`，每個的整數值，它更新`currentValue`。

```csharp
[FunctionName("Counter")]
public static async Task Counter(
    [EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    int operand = ctx.GetInput<int>();

    switch (ctx.OperationName)
    {
        case "add":
            currentValue += operand;
            break;
        case "subtract":
            currentValue -= operand;
            break;
        case "reset":
            await SendResetNotificationAsync();
            currentValue = 0;
            break;
    }

    ctx.SetState(currentValue);
}
```

實體*執行個體*唯一的識別碼，透過存取*實體識別碼*。 實體識別碼是只需一組字串可唯一識別實體執行個體。 其中包括：

1. **實體名稱**： 識別之實體類型 （例如，「 計數器 」） 的名稱
2. **實體索引鍵**： 字串可唯一識別相同的名稱 (例如，GUID) 的所有其他實體之間的實體

例如，*計數器*實體函式可能會用於將保留在製作線上遊戲的分數。 遊戲的每個執行個體必須是唯一的實體識別碼、 這類`@Counter@Game1`， `@Counter@Game2`，依此類推。

### <a name="comparison-with-virtual-actors"></a>比較虛擬動作項目

持久的實體設計經常會受到[動作項目模型](https://en.wikipedia.org/wiki/Actor_model)。 如果您已熟悉使用動作項目，持久的實體的基本概念應該很熟悉。 特別是，持久的實體是類似[虛擬動作項目](https://research.microsoft.com/en-us/projects/orleans/)在許多方面：

* 持久的實體會透過定址*實體識別碼*。
* 持久的實體作業依序執行，一次一個，為了避免競爭情形。
* 呼叫或收到信號時，會自動建立持久的實體。
* 不執行作業，持久的實體時以無訊息方式從記憶體中卸載。

有一些重要的差異，不過，值得注意的是：

* 持久的實體會模型化為純虛擬函式。 這項設計是從代表動作項目使用類別、 屬性和方法的特定語言的支援大部分物件導向的架構不同。
* 持久的實體排列優先順序*持久性*透過*延遲*，因此可能不適合有嚴格的延遲需求的應用程式。
* 實體之間傳送的訊息會傳遞可靠且順序。
* 持久的實體可以永久性協調流程搭配使用，而且可做為分散式鎖定，本文稍後所述。
* 在實體中的要求/回應模式是限制為協調流程。 實體對實體的通訊，被允許只有單向訊息 （也稱為 「 發出訊號 」），原始的動作項目模型。 此行為可避免分散式的死結。

### <a name="durable-entity-apis"></a>持久的實體 Api

實體支援牽涉到多個 Api。 第一，沒有新的 API 來定義實體的函式，如上所示，它會指定在實體上叫用作業時應該發生的事情。 此外，用戶端和協調流程的現有 Api 已更新使用與實體互動的新功能。

### <a name="implementing-entity-operations"></a>實作實體作業

實體上的作業執行可以在內容物件上呼叫這些成員 (`IDurableEntityContext`在.NET 中):

* **OperationName**： 取得作業的名稱。
* **GetInput\<T >** ： 取得作業的輸入。
* **GetState\<T >** ： 取得實體的目前狀態。
* **SetState**： 更新實體的狀態。
* **SignalEntity**： 傳送單向訊息至實體。
* **自助**： 取得實體的識別碼。
* **傳回**： 傳回至用戶端或呼叫作業的協調流程的值。
* **IsNewlyConstructed**： 傳回`true`如果實體不存在的作業之前。
* **DestructOnExit**： 完成作業之後會刪除實體。

作業是較不受限制比協調流程：

* 作業可以呼叫外部的 I/O，使用同步或非同步 Api （我們建議使用只有非同步的）。
* 作業可能不具決定性。 比方說，它會安全地呼叫`DateTime.UtcNow`，`Guid.NewGuid()`或`new Random()`。

### <a name="accessing-entities-from-clients"></a>從用戶端存取實體

持久的實體，可以從一般的函式，透過叫用`orchestrationClient`繫結 (`IDurableOrchestrationClient`在.NET 中)。 支援下列方法：

* **ReadEntityStateAsync\<T >** ： 讀取實體的狀態。
* **SignalEntityAsync**： 將單向訊息傳送至實體，並等候它要加入佇列。

這些方法會設定效能優先順序高於一致性：`ReadEntityStateAsync`可以過時的值，傳回和`SignalEntityAsync`可以傳回之前完成此作業。 相反地，（如下所述），從協調流程呼叫的實體是強式一致的。

### <a name="accessing-entities-from-orchestrations"></a>從協調流程中存取實體

協調流程可以存取使用的內容物件的實體。 他們可以選擇之間單向通訊 （射後不理） 和雙向通訊 （要求和回應）。 個別的方法

* **SignalEntity**： 傳送單向訊息至實體。
* **CallEntityAsync**： 將訊息傳送至實體，並等候回應，指出作業已完成。
* **CallEntityAsync\<T >** ： 將訊息傳送至實體，並等候回應，包含結果的型別 t。

使用時的雙向通訊，作業執行期間擲回任何例外狀況也會傳輸回到呼叫的協調流程，並重新擲回。 相反地，當使用單一事件-fire-and-forget，例外狀況是未觀察到。

### <a name="locking-entities-from-orchestrations"></a>鎖定的實體，從協調流程

協調流程可以鎖定的實體。 這項功能提供簡單的方式來使用，以避免不必要的競爭情況*關鍵區段*。

內容物件提供下列方法：

* **LockAsync**： 取得一個或多個實體鎖定。
* **IsLocked**： 傳回目前在重要區段中，如果為 true，則為 false 否則。

重要區段結束且所有的鎖定時，會釋放，協調流程結束。 在.NET 中，`LockAsync`會傳回`IDisposable`結束重要區段處置時，可用來搭配`using`子句，以取得重要區段的語法表示。

比方說，請考慮需要測試兩個播放程式是否可使用，協調流程，然後將它們指派至遊戲。 可以使用重要區段，如下所示來實作這項工作：

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

在重要區段中，這兩個播放程式實體被鎖住，這表示不會執行任何作業會從內重要區段呼叫以外）。 此行為可避免競爭情況與衝突作業，例如指派給不同的玩家遊戲，或簽署關閉。

加諸幾項限制可使用重要區段。 若要避免死結和重新進入，提供這些限制。

* 關鍵區段不可為巢狀。
* 關鍵區段無法建立 suborchestrations。
* 關鍵區段可以呼叫這些鎖定的實體。
* 使用多個平行呼叫相同的實體時，無法呼叫關鍵區段。
* 關鍵區段可以發出信號他們未擁有鎖定的實體。

## <a name="alternate-storage-providers"></a>替代儲存體提供者

長期工作架構支援多個儲存體提供者，包括[Azure 儲存體](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage)， [Azure 服務匯流排](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus)，則[記憶體模擬器](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)，和實驗性[Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis)提供者。 但到目前為止，Azure Functions 的長期工作延伸模組僅支援 Azure 儲存體提供者。 Durable Functions 2.0，支援替代的儲存體提供者從開始加入，開頭為 Redis 提供者。

> [!NOTE]
> Durable Functions 2.0 只支援.NET Standard 2.0 相容提供者。 在撰寫本文時，Azure 服務匯流排提供者不支援.NET Standard 2.0，並因此不是可用的替代儲存體提供者。

### <a name="emulator"></a>模擬器

[DurableTask.Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/)提供者是本機記憶體，適用於本機測試案例的非持久性儲存體提供者。 可以設定使用下列最低**host.json**結構描述：

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

### <a name="redis-experimental"></a>Redis （實驗性）

[DurableTask.Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/)提供者會保存到設定的 Redis 叢集的所有協調流程狀態。

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

`connectionStringName`必須參考應用程式設定或環境變數的名稱。 該應用程式設定或環境變數應該包含 Redis 連接字串值的形式*伺服器： 連接埠*。 比方說，`localhost:6379`用於連接到本機的 Redis 叢集。

> [!NOTE]
> Redis 提供者目前為實驗性，而且僅支援單一節點上執行的函式應用程式。
