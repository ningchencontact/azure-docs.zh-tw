---
title: Azure Cosmos DB 中的高可用性
description: 這篇文章說明 Azure Cosmos DB 如何提供高可用性
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 74e2d7901d127c9dd7edd8509e5bba082c4ad220
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978978"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Azure Cosmos DB 的高可用性

Azure Cosmos DB 會以透明方式，在與 Cosmos 帳戶相關聯的所有 Azure 區域之間複寫您的資料。 Cosmos DB 針對您的資料採用多層備援，如下圖所示：

![實體資料分割](./media/high-availability/cosmosdb-data-redundancy.png)

- Cosmos 容器內的資料[水平分割](partitioning-overview.md)。

- 在每個區域內，每個分割區都會受到一個複本集所保護，而且所有寫入都會由大多數複本所複寫且永久認可。 複本會分散到多達 10-20 個容錯網域中。

- 所有區域中的每個分割區都會複寫。 每個區域都包含 Cosmos 容器的所有資料分割區，並且可以接受寫入與處理讀取。  

如果您的 Cosmos 帳戶分散*N* Azure 區域，會有至少*N* x 4 個複本的所有資料。 除了提供低延遲資料存取，並在您的 Cosmos 帳戶相關聯的區域調整寫入/讀取輸送量，需要更多區域 (較高*N*) 進一步提升可用性。  

## <a name="slas-for-availability"></a>用於提供可用性的 SLA

作為全域散發的資料庫，Cosmos DB 會提供全方位的 SLA，以包含輸送量、第 99 個百分位數的延遲、一致性和高可用性。 下表說明 Cosmos DB 針對單一和多重區域帳戶所提供的高可用性相關保證。 如需高可用性，一律先設定您的 Cosmos 帳戶有多個寫入區域。

|作業類型  | 單一區域 |多重區域 (單一區域寫入)|多重區域 (多重區域寫入) |
|---------|---------|---------|-------|
|寫入    | 99.99    |99.99   |99.999|
|讀取     | 99.99    |99.999  |99.999|

> [!NOTE]
> 在實務上，限定的過期、 工作階段、 一致前置詞和最終一致性模型的實際寫入可用性遠大於已發行的 Sla。 所有一致性層級的實際讀取可用性，明顯高於已發行的 SLA。

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>發生區域中斷時使用 Cosmos DB 的高可用性

區域性中斷時不是很常見，和 Azure Cosmos DB 可確保您的資料庫是 alwayson 高可用性。 下列詳細資料會擷取 Cosmos DB 行為在中斷期間，根據您的 Cosmos 帳戶設定：

- 使用 Cosmos DB，將寫入作業認可至用戶端之前，資料會由接受寫入作業之區域內的複本仲裁進行永久認可。

- 使用多重寫入區域設定的多重區域帳戶，將針對寫入和讀取維持高可用性。 區域性容錯移轉是即時的，不需要對應用程式進行任何變更。

- **多重區域帳戶的單一寫入區域 （寫入區域中斷）：** 在寫入區域中斷期間，這些帳戶將針對讀取維持高可用性。 不過，寫入您必須 **「 啟用自動容錯移轉 」** 在您的 Cosmos 帳戶進行容錯移轉到另一個區域受影響的區域。 容錯移轉將依照您所指定的區域優先順序進行。 受影響的區域再度上線時，出現在受影響的寫入區域中斷期間未複寫的資料可透過[衝突摘要](how-to-manage-conflicts.md#read-from-conflict-feed)。 應用程式可以讀取衝突摘要、 解決衝突應用程式特定邏輯為基礎，並將更新的資料寫回 Cosmos 容器。 一旦先前受影響的寫入區域復原，它將自動作為讀取區域使用。 您可以叫用手動容錯移轉，並將受影響的區域設定為寫入區域。 再次執行手動容錯移轉，以及在使用[Azure CLI 或 Azure 入口網站](how-to-manage-database-account.md#manual-failover)。 在手動容錯移轉之前、期間或之後，沒有**遺失任何資料或可用性**。 應用程式可繼續保有高可用性。 

- **多重區域帳戶的單一寫入區域 （讀取的區域中斷）：** 在讀取區域中斷期間，這些帳戶將針對讀取和寫入維持高可用性。 受影響的區域將自動與寫入區域中斷連線，並將標示為離線。 [Cosmos DB Sdk](sql-api-sdk-dotnet.md)會將讀取重新導向至慣用的區域清單中的下一個可用區域的呼叫。 如果慣用區域清單中的區域都無法使用，呼叫會自動切換回目前的寫入區域。 不需要對應用程式的程式碼做任何變更來處理讀取區域中斷。 最後，當受影響的區域再度上線時，先前受影響的讀取區域將自動與目前寫入區域同步處理，並可再次用於處理讀取要求。 後續的讀取會被重新導向至復原的區域，不需要對應用程式的程式碼做任何變更。 期間同時容錯移轉，並重新加入之前失敗的區域的讀取的一致性保證絲毫無損 Cosmos DB。

- 單一區域帳戶可能會在區域性中斷之後失去可用性。 建議一律設為**至少兩個區域**（最好是至少兩個寫入區域） 與您的 Cosmos 帳戶，以隨時確保高可用性。

- 即使在極少且不幸的事件會永久無法復原，資料庫的 Azure 區域時不會遺失任何資料如果您的多重區域 Cosmos 帳戶已使用的預設一致性層級*強式*。 萬一使用限定過期一致性設定的多區域 Cosmos 帳戶永久無法復原的寫入區域，潛在資料遺失期間僅限於過期期間 (*K* 或*T*);工作階段、 一致前置詞和最終一致性層級，潛在資料遺失時間是限制為五秒的最大值。

## <a name="building-highly-available-applications"></a>高度可用的應用程式

- 若要確保寫入和讀取高可用性，請將 Cosmos 帳戶設定為跨越至少兩個具有多重寫入區域的區域。 這項設定會提供最低的延遲，最高的可用性，最佳的延展性，同時讀取和寫入備份 Sla 的規定。 若要深入了解，請參閱如何[使用多重寫入區域設定您的 Cosmos 帳戶](tutorial-global-distribution-sql-api.md)。

- 針對使用單一寫入區域設定的多重區域 Cosmos 帳戶，[使用 Azure CLI 或 Azure 入口網站來啟用自動容錯移轉](how-to-manage-database-account.md#automatic-failover)。 啟用自動容錯移轉之後，只要發生區域性災難，Cosmos DB 就會自動容錯移轉您的帳戶。  

- 即使您的 Cosmos 帳戶具有高可用性，您的應用程式也可能無法正確設計以維持高可用性。 若要測試您的應用程式的端對端高可用性，定期叫用[使用 Azure CLI 或 Azure 入口網站的手動容錯移轉](how-to-manage-database-account.md#manual-failover)，隨您應用程式測試或災害復原 (DR) 演練。

- 在全域分散式資料庫環境內，當發生全區域中斷情況時，一致性層級與資料持久性之間具有直接關聯性。 當您開發商務持續性計劃時，您必須了解應用程式在干擾性事件之後完全復原所需的最大可接受時間。 完全復原應用程式所需的時間，也稱為復原時間目標 (RTO)。 您也必須了解在干擾性事件之後復原時，應用程式可忍受遺失的最近資料更新最大期間。 您可能經得起遺失的更新時間週期，也稱為復原點目標 (RPO)。 若要查看 Azure Cosmos DB 的 RPO 與 RTO請參閱[一致性層級與資料持久性](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>後續步驟

接下來，您可以閱讀下列文章：

* [各種一致性層級的可用性和效能權衡取捨](consistency-levels-tradeoffs.md)
* [全域調整佈建的輸送量](scaling-throughput.md)
* [全域散發 - 運作原理](global-dist-under-the-hood.md)
* [Azure Cosmos DB 中的一致性層級](consistency-levels.md)
* [如何設定您的 Cosmos 帳戶使用多個寫入區域](how-to-multi-master.md)
