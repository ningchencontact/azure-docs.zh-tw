---
title: Azure Cosmos DB 中的一致性層級
description: Azure Cosmos DB 具有五個一致性層級，有助於在最終一致性、可用性和延遲的取捨之間取得平衡。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 395b7bc31377fd771549a399032bad9d951ec804
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384915"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB 中的一致性層級

依賴複寫來取到高可用性或低延遲性或兩者的分散式資料庫，可在讀取一致性與可用性、延遲性及輸送量之間進行基本權衡取捨。 大部分的商用分散式資料庫會要求開發人員在兩個極端的一致性模型之間進行選擇:*強*式一致性和*最終*一致性。 線性化能力或強式一致性模型是資料可程式性的黃金標準。 但它增加了較高延遲 (穩定狀態) 和降低可用性 (失敗時) 的價格。 相反地, 最終一致性提供更高的可用性和更佳的效能, 但讓應用程式變得很困難。 

Azure Cosmos DB 可提供資料一致性的選擇頻譜，而不是兩個極端的選擇。 強式一致性和最終一致性是在頻譜的結尾, 但在頻譜中有許多一致性選擇。 開發人員可以使用這些選項, 針對高可用性和效能做出精確的選擇和細微的取捨。 

透過 Azure Cosmos DB，一致性頻譜有五個定義完善的一致性模型可供開發人員選擇。 從最強到更寬鬆的模型包括*強*式、*限定過期*、*會話*、*一致前置*詞和*最終*一致性。 這些模型是妥善定義且直覺化的, 可用於特定的真實案例。 每個模型都提供[可用性和效能取捨](consistency-levels-tradeoffs.md), 並受到 sla 的支援。 下圖顯示不同的一致性層級做為頻譜。

![以頻譜形式顯示的一致性](./media/consistency-levels/five-consistency-levels.png)

一致性層級與區域無關, 不論是從何種區域提供讀取和寫入、與您的 Azure Cosmos 帳戶相關聯的區域數, 或您的帳戶是否設定為單一, 都能保證所有作業或多個寫入區域。

## <a name="scope-of-the-read-consistency"></a>讀取一致性的範圍

讀取一致性會套用至分割區索引鍵範圍 (或邏輯分割區) 內的單一讀取作業。 讀取作業可由遠端用戶端或預存程序發出。

## <a name="configure-the-default-consistency-level"></a>設定預設一致性層級

您隨時可以在 Azure Cosmos 帳戶上設定預設一致性層級。 您帳戶上設定的預設一致性層級會套用到該帳戶下的所有 Azure Cosmos 資料庫和容器。 對容器或資料庫發出的所有讀取和查詢，預設都將使用指定的一致性層級。 若要深入了解，請參閱如何[設定預設一致性層級](how-to-manage-consistency.md#configure-the-default-consistency-level)。

## <a name="guarantees-associated-with-consistency-levels"></a>與一致性層級相關的保證

Azure Cosmos DB 會提供全方位 SLA，保證 100% 的讀取要求都將符合任何所選一致性層級的一致性保證。 如果滿足與一致性層級相關聯的所有一致性保證，即表示讀取要求符合一致性 SLA。 [Cosmos-TLA](https://github.com/Azure/azure-cosmos-tla) GitHub 存放庫中提供了使用 TLA + 規格語言 Azure Cosmos DB 中五個一致性層級的精確定義。

五個一致性層級的語意說明如下：

- **重要事項**：強式一致性提供了線性化能力保證。 線性化能力是指同時提供要求服務。 保證讀取會傳回最新版本的認可項目。 用戶端將絕不會看到未認可或部分的寫入。 保證使用者一律會讀取最新認可的寫入。

- **限定過期**：保證讀取會實踐一致前置詞的保證。 讀取可能會落後寫入最多「 *K* 」個專案版本 (亦即「更新」) 或「 *T* 」時間間隔。 換句話說, 當您選擇限定過期時, 可以透過兩種方式來設定「過期」: 

  * 專案的版本數 (*K*)
  * 讀取可能落後寫入的時間間隔 (*T*) 

  限定過期提供全域訂單總數，但「過期期間」內的除外。 在區域內，過期期間內外都有單純的讀取保證存在。 強式一致性的語義與限定過期所提供的相同。 過期期間等於零。 限定過期也稱為時間延遲的線性化能力。 當用戶端在接受寫入的區域內執行讀取作業時, 限定過期一致性所提供的保證會與強式一致性的保證相同。

- **工作階段**：在單一用戶端會話中, 讀取保證會接受一致前置詞 (假設單一「寫入器」會話)、單純讀取、單純寫入、讀取您的寫入, 以及寫入後接讀取的保證。 執行寫入的會話外的用戶端會看到最終一致性。

- **一致前置詞**：傳回的更新會包含所有更新的部分前置詞 (沒有間隔)。 一致前置詞一致性層級可確保讀取永遠不會看到不按照順序的寫入。

- **最終**：讀取沒有排序保證。 如果不再有任何寫入，複本最終將會聚合。

## <a name="consistency-levels-explained-through-baseball"></a>透過棒球說明一致性層級

接下來我們將以棒球賽為例。 假設有一系列的寫入代表棒球賽的分數。 [透過棒球說明複寫資料一致性](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf)一文說明了棒球賽每局列出的分數。 這場假設的棒球賽目前正打完七局上。 這是第 7 局休息時間。 訪客落後的分數為2到 5, 如下所示:

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **得分** |
| - | - | - | - | - | - | - | - | - | - | - |
| **客隊** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **主隊** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Azure Cosmos 容器會保存訪客和家庭團隊的執行總計。 比賽進行時，不同讀取保證可能會導致用戶端讀取不同的分數。 下表完整列出使用五個一致性保證來讀取客隊和主隊分數所傳回的一組分數。 客隊的分數會先列出。 其他可能的傳回值會以逗號分隔。

| **一致性層級** | **分數 (訪客, 首頁)** |
| - | - |
| **強式** | 2-5 |
| **限定過期** | 最多有一局過期的分數：2-3, 2-4, 2-5 |
| **工作階段** | <ul><li>針對寫入者：2-5</li><li> 針對寫入者以外的任何其他人：0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5</li><li>讀取 1-3 後：1-3, 1-4, 1-5, 2-3, 2-4, 2-5</li> |
| **一致前置詞** | 0-0, 0-1, 1-1, 1-2, 1-3, 2-3, 2-4, 2-5 |
| **最終** | 0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5 |

## <a name="additional-reading"></a>其他閱讀資料

若要深入了解一致性的概念，請閱讀下列文章：

- [Azure Cosmos DB 中五個一致性層級的高階 TLA + 規格](https://github.com/Azure/azure-cosmos-tla)
- [Doug Terry 透過棒球來解說複寫的資料一致性 (影片)](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Doug Terry 透過棒球來解說複寫的資料一致性 (白皮書)](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [弱式一致複寫資料的工作階段保證](https://dl.acm.org/citation.cfm?id=383631)
- [新式分散式資料庫系統設計的一致性權衡取捨：CAP 是整個過程中的唯一解決方案](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [實際部分仲裁的隨機限定過期 (PBS) (英文)](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [再論最終一致](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Cosmos DB 中的一致性，請閱讀下列文章：

* [為應用程式選擇正確的一致性層級](consistency-levels-choosing.md)
* [Azure Cosmos DB API 間的一致性層級](consistency-levels-across-apis.md)
* [各種一致性層級的可用性和效能權衡取捨](consistency-levels-tradeoffs.md)
* [設定預設一致性層級](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [覆寫預設一致性層級](how-to-manage-consistency.md#override-the-default-consistency-level)

