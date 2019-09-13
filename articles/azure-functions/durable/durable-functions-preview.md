---
title: Durable Functions 預覽功能-Azure Functions
description: 瞭解 Durable Functions 的預覽功能。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: article
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 8f2560141eac4e7d9fed267d347990e65bfe9c26
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933251"
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
| OrchestrationClientAttribute | DurableClientAttribute |

在抽象基類包含虛擬方法的情況下, 這些虛擬方法已由中`DurableContextExtensions`定義的擴充方法所取代。

## <a name="entity-functions"></a>實體函數

從 Durable Functions v 2.0.0-Alpha 開始，我們引進了新的[實體函數](durable-functions-entities.md)概念。

實體函式會定義讀取和更新部分狀態 (稱為「*持久實體*」) 的作業。 就像協調器函式一樣, 實體函式是具有特殊觸發程式類型 (*實體觸發*程式) 的函數。 與協調器函式不同的是, 實體函式沒有任何特定的程式碼條件約束。 實體函數也會明確管理狀態, 而不是透過控制流程隱含表示狀態。

根據最初的使用者意見反應，我們稍後為 Durable Functions v 2.0.0-Beta1 中的實體新增了以類別為基礎之程式設計模型的支援。

若要深入瞭解，請參閱[實體](durable-functions-entities.md)函式一文。

## <a name="durable-http"></a>持久 HTTP

從 Durable Functions v 2.0.0-Beta2 開始，我們引進了新的[持久性 HTTP](durable-functions-http-features.md)功能，可讓您：

* 直接從協調流程函式呼叫 HTTP Api （有一些記載的限制）
* 執行自動用戶端 HTTP 202 狀態輪詢
* [Azure 受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別的內建支援

若要深入瞭解，請參閱[HTTP 功能](durable-functions-http-features.md#consuming-http-apis)一文。

## <a name="alternate-storage-providers"></a>替代儲存提供者

長期工作架構目前支援多個儲存提供者, 包括[Azure 儲存體](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage)、 [Azure 服務匯流排](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus)、[記憶體內部模擬器](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator), 以及實驗性[Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis)提供者。 不過, 到目前為止, Azure Functions 的持久工作延伸僅支援 Azure 儲存體提供者。 從 Durable Functions 2.0 開始, 正在新增替代儲存提供者的支援, 從 Redis 提供者開始。

> [!NOTE]
> Durable Functions 2.0 只支援 .NET Standard 2.0 相容的提供者。

### <a name="emulator"></a>模擬器

[DurableTask](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/)提供者是本機記憶體、非持久性儲存提供者, 適用于本機測試案例。 您可以使用下列最少的 host 來設定它 **。 json**架構:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { "enabled": true }
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
