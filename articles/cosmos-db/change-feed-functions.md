---
title: 如何搭配 Azure Functions 使用 Azure Cosmos DB 變更摘要
description: 搭配 Azure Functions 使用 Azure Cosmos DB 變更摘要
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 41cbb657a4fc83b498c5cc9a6a16397a619aa075
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034041"
---
# <a name="how-to-use-azure-cosmos-db-change-feed-with-azure-functions"></a>如何搭配 Azure Functions 使用 Azure Cosmos DB 變更摘要

如果您使用 Azure Functions，則連線至變更摘要的最簡單方式是在您的 Azure Functions 應用程式中新增 [Azure Cosmos DB 觸發程序](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger)。 當您在 Azure Functions 應用程式中建立 Cosmos DB 觸發程序時，需選取要連線的 Cosmos 容器，並且每當您變更容器中的某個項目時，即會觸發該函式。

您可以在 Azure Functions 入口網站或 Azure Cosmos DB 入口網站中建立觸發程序，或以程式設計方式建立。 如需詳細資訊，請參閱[使用 Azure Cosmos DB 和 Azure Functions 的無伺服器資料庫計算](serverless-computing-database.md)。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region"></a>如何將 Azure Functions 設定為從特定區域讀取？

使用 Azure Cosmos DB 觸發程序來指定區域清單時，能夠定義 [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations) \(英文\)。 這與您自訂 ConnectionPolicy 的方式相同，可使觸發程序從您慣用的區域中讀取。 在理想的情況下，您想要從最接近您部署 Azure Functions 的區域讀取。

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Azure Functions 中預設的批次大小是多少？

針對 Azure Functions 的每個引動過程，預設大小為 100 個項目。 不過，此數量可在 function.json 檔案中設定。 以下是完整的[設定選項清單](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)。 如果是在本機開發，請更新 local.settings.json 檔案中的應用程式設定。

### <a name="i-am-monitoring-a-container-and-reading-its-change-feed-however-i-dont-get-all-the-inserted-documents-some-items-are-missing"></a>我正在監視一個容器並讀取其變更摘要，但無法取得所有插入的文件，是否遺漏了某些項目？

請確定沒有其他 Azure 函式正在使用相同的租用容器來讀取同一個容器。 遺漏的文件會由另一個也在使用相同租用的 Azure Functions 來處理。

因此，如果您要建立多個 Azure Functions 來讀取相同的變更摘要，它們必須使用不同的租用容器，或使用 "leasePrefix" 設定來共用相同的容器。 不過，在使用變更摘要處理器程式庫時，您可以啟動 Azure 函式的多個執行個體，而 SDK 會自動將文件分配到不同的執行個體上。

### <a name="azure-cosmos-item-is-updated-every-second-and-i-dont-get-all-the-changes-in-azure-functions-listening-to-change-feed"></a>Azure Cosmos 項目每秒都會更新一次，而我並未得到接聽變更摘要之 Azure Functions 中的所有變更？

Azure Functions 會以 5 秒的預設延遲時間上限，持續輪詢摘要變更以取得更多變更。 如果沒有任何要讀取的暫止變更，或在套用觸發程序之後有暫止的變更，此函式將會立即讀取它們。 不過，如果沒有任何暫止的變更，此函式將等候 5 秒，然後進行輪詢以取得更多變更。

如果您的文件會在相同的時間間隔接收多個變更 (觸發程序會以此時間間隔進行輪詢來取得新變更)，則您可能會收到最新版本的文件，而非中繼版本。

如果想要以少於 5 秒的時間來輪詢變更摘要 (例如，每秒)，您可以設定輪詢時間 "feedPollDelay"，請參閱[完整設定](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations) \(英文\)。 它以毫秒為單位定義，預設值為 5000。 小於 1 秒的輪詢是可行的，但不建議使用，因為您將開始使用更多 CPU 記憶體。

### <a name="can-multiple-azure-functions-read-one-containers-change-feed"></a>多個 Azure Functions 是否可讀取同一個容器的變更摘要？

是。 多個 Azure Functions 可讀取相同的容器變更摘要。 但是，Azure Functions 需要定義單獨的 “leaseCollectionPrefix”。

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-in-a-batch-of-10-documents-and-i-get-an-error-at-seventh-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-document-ie-seventh-document-in-my-next-feed"></a>假設我正在具 10 份文件的批次中使用 Azure Functions 來處理變更摘要，但在第七份文件中出現錯誤。 在該情況下，不會處理最後三份文件，我該如何在後續摘要中，從失敗的文件 (例如，第七份文件) 開始處理？

若要處理此錯誤，建議的模式是使用 try-catch 區塊來包裝您的程式碼，而且如果您正在逐一查看文件清單，請將每個反覆項目包裝於自己的 try-catch 區塊中。 攔截錯誤並將該文件放在佇列 (無效信件) 中，然後定義邏輯以處理產生錯誤的文件。 如果您有一個包含 200 個文件的批次，而且只有一個文件失敗，請使用此方法，不需要丟棄整個批次。

如果出現錯誤，請勿將檢查點倒轉回開始位置，您才可以繼續從變更摘要中取得那些文件。 請記住，變更摘要會保留文件的最後一個快照集，因此，您可能會遺失文件中的上一個快照集。 變更摘要僅保留最後一個版本的文件，而且可在其他處理程序之間顯示，及變更文件。

隨著不斷修正程式碼，不久之後，您在無效信件佇列中無法找到任何文件。 變更摘要系統會自動呼叫 Azure Functions，而且 Azure Functions 會在內部維護檢查點。 如果您想復原檢查點並控制其每個層面，則應考慮使用變更摘要處理器 SDK。

### <a name="are-there-any-extra-costs-for-using-the-azure-cosmos-db-trigger"></a>使用 Azure Cosmos DB 觸發程序是否有任何額外的成本？

Azure Cosmos DB 觸發程序會在內部運用變更摘要處理器程式庫。 因此，它需要額外的集合 (稱為「租用集合」) 來維護狀態和部份檢查點。 這個狀態管理需要能夠動態調整，才能在您想要停止 Azure Functions 並於稍後繼續處理時繼續。 若要深入了解，請參閱[如何使用變更摘要處理器程式庫](change-feed-processor.md)。

## <a name="next-steps"></a>後續步驟

您現在可以在下列文章中繼續深入了解變更摘要：

* [變更摘要的概觀](change-feed.md)
* [讀取變更摘要的方式](read-change-feed.md)
* [使用變更摘要處理器程式庫](change-feed-processor.md)
* [如何使用變更摘要處理器程式庫](change-feed-processor.md)
* [使用 Azure Cosmos DB 和 Azure Functions 的無伺服器資料庫計算](serverless-computing-database.md)
