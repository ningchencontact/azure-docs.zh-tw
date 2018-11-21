---
title: Azure Cosmos DB 中的一致性層級 | Microsoft Docs
description: Azure Cosmos DB 具有五個一致性層級，有助於在最終一致性、可用性和延遲的取捨之間取得平衡。
keywords: 最終一致性, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: andrl
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ace11cf3704ddbd503c0202d45874670476198e
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624822"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB 中的一致性層級

依賴複寫來達到高可用性或低延遲性 (或兩者皆是) 的分散式資料庫，可在讀取一致性與可用性、延遲性及輸送量之間進行基本取捨。 大部分的商用分散式資料庫都會要求開發人員在兩個極端的一致性模型之間進行選擇：強式一致性和最終一致性。 雖然 [線性化能力](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) \(英文\) 或強式一致性模型是資料可程式性的黃金標準，但它增添了付出更高延遲 (穩定狀態下) 和降低可用性 (失敗時) 的高昂代價。 相反地，最終一致性提供更高的可用性和更佳的效能，但很難為應用程式進行程式設計。

Cosmos DB 會提供資料一致性的選擇頻譜，而不是兩個極端的選擇。 強式一致性和最終一致性是此頻譜的兩端，而在此頻譜中有許多一致性選擇。 這些一致性選項可讓開發人員在高可用性或效能方面做出精確的選擇和更細微的權衡取捨。 Cosmos DB 可讓開發人員從一致性頻譜 (最強到最弱) 內五個定義完善的一致性模型之間進行選擇：**強式**、**限定過期**、**工作階段**、**一致前置詞**和**最終**。 這些一致性模型中的每一個都是完善定義且直覺化的模型，可用於特定的真實案例。 這五個一致性模型中的每一個都提供[可用性和效能權衡取捨](consistency-levels-tradeoffs.md)，並且由全方位 SLA 所支援。 下圖以頻譜顯示不同的一致性層級：

![以頻譜形式顯示的一致性](./media/consistency-levels/five-consistency-levels.png)

一致性層級與區域無關。 您 Cosmos DB 帳戶的一致性層級保證會套用至所有讀取作業，並且與下列項目皆無關：處理讀取或寫入項目的位置、與您 Cosmos 帳戶相關聯的區域數，或您的帳戶設定為單一或多個寫入區域。

## <a name="scope-of-the-read-consistency"></a>讀取一致性的範圍

讀取一致性會套用至分割區索引鍵範圍 (即邏輯分割區) 內的單一讀取作業。 讀取作業可由遠端用戶端或預存程序發出。

## <a name="configuring-the-default-consistency-level"></a>設定預設一致性層級

您隨時可以在 Cosmos DB 帳戶上設定**預設一致性層級**。 您帳戶上設定的預設一致性層級會套用到該帳戶下所有 Cosmos 資料庫 (和容器)。 對容器或資料庫發出的所有讀取和查詢，預設都將使用指定的一致性層級。 若要深入了解，請參閱如何[設定預設一致性層級](how-to-manage-consistency.md#configure-the-default-consistency-level)一文。

## <a name="guarantees-associated-with-consistency-levels"></a>與一致性層級相關的保證

Azure Cosmos DB 會提供全方位 SLA，保證 100% 的讀取要求都將符合任何所選一致性層級的一致性保證。 如果滿足與一致性層級相關聯的所有一致性保證，就會將讀取要求視為符合一致性 SLA。 您可以在 [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) \(英文\) GitHub 存放庫中，了解 Cosmos DB 中使用 [TLA + 規格語言](http://lamport.azurewebsites.net/tla/tla.html) \(英文\) 精確定義的五個一致性層級。 以下將敘述五個一致性層級的語意：

- **一致性層級 =「強式」**：強式一致性利用保證傳回項目最新認可版本的讀取來提供[線性化能力](https://aphyr.com/posts/313-strong-consistency-models) \(英文\) 保證。 用戶端將永遠不會看到未認可或部分的寫入。 保證使用者一律會讀取最新認可的寫入。

- **一致性層級 =「限定過期」**：保證讀取會實踐一致前置詞的保證。 讀取可能會落後寫入最多 K 個項目版本 (這就是「更新」) 或 ‘t’ 個時間間隔。 選擇限定過期時，可以兩種方式來設定「過期」： 

  * 項目的版本數 (K) 或
  * 讀取可能落後寫入的時間間隔 (t)。 

  限定過期提供全域訂單總數，但「過期期間」內的除外。 在區域內，「過期期間」內外都有單純的讀取保證存在。 強式一致性的語意與「限定過期」所提供的相同，而且「過期期間」等於零。 限定過期也稱為**時間延遲的線性化能力**。 當用戶端在接受寫入的區域內執行讀取作業時，限定過期一致性所提供的保證會與強式一致性所提供的保證一樣。

- **一致性層級 =「工作階段」**：保證讀取會實踐一致前置詞、單純讀取、單純寫入、讀取您的寫入、讀取後接寫入的保證。 工作階段一致性的範圍會限制在用戶端工作階段。

- **一致性層級 =「一致前置詞」**：傳回的更新包含所有更新的部分前置詞 (沒有間隔)。 一致前置詞保證讀取永遠不會看到沒有順序的寫入。

- **一致性層級 =「最終」**：讀取沒有排序保證。 如果不再有任何寫入，複本最終將會聚合。

## <a name="consistency-levels-explained-through-baseball"></a>透過棒球說明一致性層級

讓我們以棒球賽案例為例，假設寫入序列表示棒球賽每局列出的分數，如同[透過棒球說明複寫資料一致性](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) \(英文\) 一文所述。 這個假設的棒球賽目前正打完第七局的上半場 (也就是眾所皆知的第 7 局休息時間)，而主隊以 2-5 暫時領先。

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **得分** |
| - | - | - | - | - | - | - | - | - | - | - |
| **客隊** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **主隊** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Cosmos DB 容器會保留客隊和主隊的得分總計。 比賽進行時，不同讀取保證可能會導致用戶端讀取不同的分數。 下表完整列出使用五個一致性保證來讀取客隊和主隊分數所傳回的一組分數。 客隊分數會先列出，而其他可能的傳回值會以逗號分隔。

| **一致性層級** | **分數** |
| - | - |
| **強式** | 2-5 |
| **限定過期** | 最多有一局過期的分數" 2-3, 2-4, 2-5 |
| **工作階段** | <ul><li>針對寫入者" 2-5</li><li> 針對寫入者以外的任何其他人：0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5</li><li>讀取 1-3 後：1-3, 1-4, 1-5, 2-3, 2-4, 2-5</li> |
| **一致前置詞** | 0-0, 0-1, 1-1, 1-2, 1-3, 2-3, 2-4, 2-5 |
| **最終** | 0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5 |

## <a name="additional-reading"></a>其他閱讀資料

若要深入了解一致性的概念，請閱讀下列文章：

- [Azure Cosmos DB 中五個一致性層級的高階 TLA + 規格](https://github.com/Azure/azure-cosmos-tla)
- [Doug Terry 透過棒球來解說複寫的資料一致性 (影片)](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Doug Terry 透過棒球來解說複寫的資料一致性 (白皮書)](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [弱式一致複寫資料的工作階段保證](https://dl.acm.org/citation.cfm?id=383631)
- [現代分散式資料庫系統設計的一致性取捨：CAP 是整個過程中的唯一解決方案](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
- [實際部分仲裁的隨機限定過期 (PBS) (英文)](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [再論最終一致](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>後續步驟

若要深入了解 Cosmos DB 中的一致性，請閱讀下列文章：

* [為應用程式選擇正確的一致性層級](consistency-levels-choosing.md)
* [Cosmos DB API 上的一致性層級](consistency-levels-across-apis.md)
* [各種一致性層級的可用性和效能取捨](consistency-levels-tradeoffs.md)
* [如何設定預設一致性層級](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [如何覆寫預設一致性層級](how-to-manage-consistency.md#override-the-default-consistency-level)

