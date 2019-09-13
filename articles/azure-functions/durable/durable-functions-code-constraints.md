---
title: 持久協調器程式碼條件約束-Azure Functions
description: Azure Durable Functions 的協調流程函式重新執行和程式碼條件約束。
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/18/2019
ms.author: azfuncdf
ms.openlocfilehash: 87851a4879760c76950f765d05de4662ecb04bea
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935764"
---
# <a name="orchestrator-function-code-constraints"></a>協調器函式程式碼條件約束

Durable Functions 是[Azure Functions](../functions-overview.md)的延伸模組，可讓您建立具狀態的應用程式。 您可以[使用協調器函式來](durable-functions-orchestrations.md)協調函式應用程式中其他持久性函式的執行。 協調器函式是具狀態、可靠且可能長時間執行的功能。

## <a name="orchestrator-code-constraints"></a>協調器程式碼條件約束

協調器函式會使用[事件來源](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)來確保可靠的執行，並維護本機變數狀態。 Orchestrator 程式碼的重新執行[行為](durable-functions-orchestrations.md#reliability)會針對您可以在協調器函式中撰寫的程式碼類型建立條件約束。 例如，協調器程式碼必須具*決定性*。  協調器函式會多次重新執行，而且每次都必須產生相同的結果。

下列各節提供一些簡單的指導方針，以確保您的程式碼具決定性。

### <a name="using-deterministic-apis"></a>使用決定性 Api

協調器函式可自由地呼叫其目的語言所需的任何 API。 不過，協調器功能必須只呼叫具*決定性*的 api。 *決定性的 api*是一種 api，不論呼叫的時間或頻率為何，一律會傳回完全相同的值給相同的輸入。

以下是一些應避免的 Api 範例，因為它們*不*具決定性。 這些限制僅適用于協調器函式。 其他函數類型沒有這類限制。

| API 類別 | `Reason` | 因應措施 |
| ------------ | ------ | ---------- |
| 日期/時間  | 傳回_目前_日期或時間的 api 不具決定性，因為每次重新執行時，其傳回值將會不同。 | 使用[CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) （.net）或`currentUtcDateTime` （JavaScript） API，這對於重新執行是安全的。 |
| Guid/Uuid  | 傳回_隨機_GUID/UUID 的 api 不具決定性，因為每次重新執行時產生的值會不同。 | 使用[NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) （.net）或`newGuid` （JavaScript）來安全地產生隨機的 guid。 |
| 亂數字 | 傳回亂數字的 Api 不具決定性，因為每次重新執行時產生的值會不同。 | 使用活動函式，將亂數字傳回至協調流程。 活動函式的傳回值一律可安全地重新執行。 |
| 繫結 | 輸入和輸出系結通常會執行 i/o 且不具決定性。 即使[協調流程用戶端](durable-functions-bindings.md#orchestration-client)和[實體客戶](durable-functions-bindings.md#entity-client)端系結也不能直接由協調器函式使用。 | 在用戶端或活動函式內使用輸入和輸出系結。 |
| 網路 | 網路呼叫牽涉到外部系統，而且不具決定性。 | 使用活動功能進行網路呼叫。 如果您需要從協調器函式進行 HTTP 呼叫，您也可以選擇使用[持久性 HTTP api](durable-functions-http-features.md#consuming-http-apis)。 |
| 封鎖 Api | 封鎖 api `Thread.Sleep` （例如（.net）或其他類似的 api）可能會導致協調器函式的效能和規模問題，因此應該予以避免。 在 Azure Functions 耗用量方案中，甚至可能導致不必要的執行時間費用。 | 使用替代專案來封鎖 api （如果可用） `CreateTimer` ，例如在協調流程執行中引進延遲。 [持久性計時器](durable-functions-timers.md)延遲不會計入協調器函式的執行時間。 |
| 非同步 Api | 協調器程式碼必須**永不起始任何非同步作業**，除非使用 [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) API 或 `context.df` 物件的 API。 例如，沒有 `Task.Run`、`Task.Delay` 或 `HttpClient.SendAsync` 在 .NET 中，或 `setTimeout()` 和 `setInterval()` 在 JavaScript 中。 長期工作架構會在單一執行緒上執行協調器程式碼，並且無法與可以由其他非同步 API 排程的任何其他執行緒進行互動。 | 協調器函式應該只進行*持久*的非同步呼叫。 任何其他非同步 API 呼叫都應該從活動函式來完成。 |
| 非同步 JavaScript 函數 | JavaScript 協調器函式`async`不能是，因為 node.js 執行時間不保證非同步函式具有決定性。 | JavaScript 協調器函式必須宣告為同步產生器函式。 |
| 執行緒 Api | 長期工作架構會在單一執行緒上執行協調器程式碼，而且無法與任何其他執行緒互動。 將新的執行緒引進協調流程的執行，可能會導致不具決定性的執行或鎖死。 | 執行緒 Api 幾乎不能用於協調器函式中。 如有必要，它們應該僅限於活動功能。 |
| 靜態變數 | 在協調器函式中應該避免非常數靜態變數，因為它們的值可能會隨著時間而改變，因而導致不具決定性的執行行為。 | 使用常數，或將靜態變數的使用限制為活動函式。 |
| 環境變數 | 請勿在協調器函式中使用環境變數。 其值可能會隨著時間而改變，因而導致不具決定性的執行行為。 | 環境變數必須只在用戶端函式或活動函式內參考。 |
| 無限迴圈 | 在協調器函式中應該避免無限迴圈。 長期工作架構會在協調流程函式進行時儲存執行歷程記錄，因此無限迴圈可能會導致 orchestrator 實例耗盡記憶體。 | 在無限迴圈的案例中，使用例如 [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) (.NET) 或 `continueAsNew` (JavaScript) 之類的 API 重新啟動函式執行，並捨棄先前的執行歷程記錄。 |

雖然這些條件約束乍看之下令人卻步，但是實際上遵循它們並不困難。 長期工作架構嘗試偵測上述規則的違規，並且擲回 `NonDeterministicOrchestrationException`。 不過，這個偵測行為是最佳方式，您不應該仰賴它。

## <a name="versioning"></a>版本控制

長期協調流程可能會持續執行數天、數月、數年，甚至[eternally](durable-functions-eternal-orchestrations.md)。 對影響尚未完成之協調流程 Durable Functions 應用程式所做的任何程式碼更新，可能會中斷其重新執行行為。 因此，在更新程式碼時請務必小心規劃。 如需如何版本化程式碼的詳細說明，請參閱[版本](durable-functions-versioning.md)設定一文。

## <a name="durable-tasks"></a>長期工作

> [!NOTE]
> 本章節描述長期工作架構的內部實作詳細資料。 您不需要知道這項資訊就可以使用長期函式。 它只是用來協助您了解重新執行行為。

可以在協調器函式中安全地等候的工作偶爾會稱為「長期工作」。 這些工作是由長期任務架構所建立和管理。 範例包括 .net 協調器函`CallActivityAsync`式`WaitForExternalEvent`中、 `CreateTimer`和所傳回的工作。

這些*長期*工作都是在內部使用 .net 中的`TaskCompletionSource`物件清單來管理。 在重新執行期間，這些工作會建立為協調器程式碼執行的一部分，並且在發送器列舉對應歷程記錄事件時完成。 執行作業會使用單一執行緒以同步方式完成，直到所有歷程記錄都已重新執行為止。 不是由歷程記錄重新執行結束而完成的任何持久工作，都有適當的動作。例如，訊息可以會被加入佇列以呼叫活動函式。

此處所述的執行行為應該可以協助您瞭解協調器函式`await`程式`yield`代碼絕對不能或非長期工作：發送器執行緒無法等候它完成，而且該工作的任何回呼都可能已損毀協調器函式的追蹤狀態。 某些執行時間檢查已準備好嘗試偵測這些違規。

如何您想要關於長期工作架構如何執行協調器函式的詳細資訊，最好的方式是參閱 [GitHub 上的長期工作原始程式碼](https://github.com/Azure/durabletask)。 請特別參閱 [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) 和 [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解如何叫用子協調流程](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [了解如何處理版本控制](durable-functions-versioning.md)
