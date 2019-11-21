---
title: Azure Functions 的最佳作法
description: 了解 Azure Functions 的最佳作法與模式。
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 10/16/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa85f636233a067713d127938d674b359bd03696
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227375"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>將 Azure Functions 效能和可靠性最佳化

本文提供指引來改善[無伺服器](https://azure.microsoft.com/solutions/serverless/)函式應用程式的效能和可靠性。  

## <a name="general-best-practices"></a>一般最佳作法

以下是如何使用 Azure Functions 建置及打造無伺服器解決方案的最佳做法。

### <a name="avoid-long-running-functions"></a>避免長時間執行的函式

大型長時間執行的函式可能會造成非預期的逾時問題。 To learn more about the timeouts for a given hosting plan, see [function app timeout duration](functions-scale.md#timeout). 

A function can become large because of many Node.js dependencies. 匯入相依性也可能會造成載入時間增加，而導致未預期的逾時。 系統會以明確和隱含方式載入相依性。 您的程式碼載入的單一模組可能會載入其本身的其他模組。 

在可能時，將大型函式重構為較小的函式集，共用運作並快速傳回回應。 For example, a webhook or HTTP trigger function might require an acknowledgment response within a certain time limit; it's common for webhooks to require an immediate response. 您可以將 HTTP 觸發程序承載傳遞到要由佇列觸發程序函式處理的佇列中。 This approach lets you defer the actual work and return an immediate response.


### <a name="cross-function-communication"></a>跨函式通訊

[Durable Functions](durable/durable-functions-overview.md) 和 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 均建置用來管理多個函式之間的狀態轉換和通訊。

If not using Durable Functions or Logic Apps to integrate with multiple functions, it's best to use storage queues for cross-function communication. The main reason is that storage queues are cheaper and much easier to provision than other storage options. 

儲存體佇列中個別訊息大小限制在 64 KB。 如果您需要在函式之間傳遞更大型的訊息，Azure 服務匯流排佇列可用來支援標準層中大小上限為 256 KB 的訊息，以及進階層中上限為 1 MB 的訊息。

如果您需要在處理之前篩選訊息，服務匯流排主題會很實用。

事件中樞對於支援大量通訊很有用。


### <a name="write-functions-to-be-stateless"></a>撰寫無狀態函式 

若可能，Functions 應該是無狀態和具有等冪性。 將任何必要的狀態資訊與您的資料產生關聯。 例如，正在處理訂單就可能具有相關聯的 `state` 成員。 函式本身保持無狀態時，函式可以依據該狀態處理訂單。 

特別建議計時器觸發程序使用等冪函式。 For example, if you have something that absolutely must run once a day, write it so it can run anytime during the day with the same results. The function can exit when there's no work for a particular day. 如果先前的執行無法完成，下一次執行應該會定停止的位置開始。


### <a name="write-defensive-functions"></a>編寫防禦性函式

假設您的函式可能隨時會遇到例外狀況。 設計您的函式，使得它能夠在下一次執行期間從先前的失敗點繼續執行。 假設需要執行下列動作的案例︰

1. Query for 10,000 rows in a database.
2. 對每個資料列建立佇列訊息，來進一步處理向下一行。
 
Depending on how complex your system is, you may have: involved downstream services behaving badly, networking outages, or quota limits reached, etc. All of these can affect your function at any time. 您必須設計您的函式，以對其做好準備。

如果在插入這些項目中的 5,000 個至佇列以進行處理之後發生失敗，您的程式碼如何因應？ 追蹤集合中您已完成的項目。 否則，您可能下一次又將它們插入。 This double-insertion can have a serious impact on your work flow, so [make your functions idempotent](functions-idempotent.md). 

如果佇列項目已經過處理，請讓您的函式成為無作業。

利用已針對您在 Azure Functions 平台中所使用元件提供的防禦性措施。 例如，請參閱文件中**處理有害的佇列訊息**，以了解 [Azure 儲存體佇列觸發程序和繫結](functions-bindings-storage-queue.md#trigger---poison-messages)。 

## <a name="scalability-best-practices"></a>延展性最佳做法

There are a number of factors that impact how instances of your function app scale. [函式調整](functions-scale.md)文件中會提供詳細資料。  以下是一些最佳做法，可確保函式應用程式的最佳延展性。

### <a name="share-and-manage-connections"></a>共用及管理連線

Reuse connections to external resources whenever possible.  請參閱[如何管理 Azure Functions 中的連線](./manage-connections.md)。

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>不要在相同函式應用程式中混用測試和實際執行程式碼

函式應用程式內的 Functions 會共用資源。 例如，共用記憶體。 如果您在生產環境中使用函式應用程式，請勿對它新增與測試相關的函式和資源。 在實際執行程式碼執行期間可能導致發生未預期的額外負荷。

對於在實際執行函式應用程式中載入的項目，請務必小心。 記憶體會在應用程式中的每個函式間平均分配。

If you have a shared assembly referenced in multiple .NET functions, put it in a common shared folder. Otherwise, you could accidentally deploy multiple versions of the same binary that behave differently between functions.

Don't use verbose logging in production code, which has a negative performance impact.

### <a name="use-async-code-but-avoid-blocking-calls"></a>使用非同步程式碼但避免封鎖呼叫

非同步程式設計是建議的最佳作法。 不過，請務必避免在 `Task` 執行個體上參考 `Result` 屬性或呼叫 `Wait` 方法。 這個方法可能會導致執行緒耗盡。

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>儘可能分批接收訊息

某些觸發程序 (如事件中樞) 能夠在單一引動過程中接收一批訊息。  分批處理訊息的效能比較好。  如 [host.json 參考文件](functions-host-json.md)所述，您可以在 `host.json` 檔案中設定批次大小上限。

For C# functions, you can change the type to a strongly-typed array.  例如，方法簽章可能是 `EventData[] sensorEvent`，而不是 `EventData sensorEvent`。  For other languages, you'll need to explicitly set the cardinality property in your `function.json` to `many` in order to enable batching [as shown here](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>設定主機的行為，更妥善處理並行作業

函式應用程式中的 `host.json` 檔案能夠設定主機執行階段和觸發程序行為。  除了批次處理行為，您可以管理數個觸發程序的並行作業。 經常調整這些選項中的值，可協助每個執行個體針對所叫用函式的需求進行適當調整。

Settings in the host.json file apply across all functions within the app, within a *single instance* of the function. For example, if you had a function app with two HTTP functions and [`maxConcurrentRequests`](functions-bindings-http-webhook.md#hostjson-settings) requests set to 25, a request to either HTTP trigger would count towards the shared 25 concurrent requests.  When that function app is scaled to 10 instances, the two functions effectively allow 250 concurrent requests (10 instances * 25 concurrent requests per instance). 

Other host configuration options are found in the [host.json configuration article](functions-host-json.md).

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

* [如何管理 Azure Functions 中的連線](manage-connections.md)
* [Azure App Service 最佳做法](../app-service/app-service-best-practices.md)
