---
title: 在 Azure Cosmos DB 中各種一致性層級的可用性和效能權衡取捨 | Microsoft Docs
description: 在 Azure Cosmos DB 中各種一致性層級的可用性和效能權衡取捨。
keywords: 一致性, 效能, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.openlocfilehash: 0e4105d6f56a8eb45a83e970c85319cf25041781
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2018
ms.locfileid: "51514769"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中各種一致性層級的可用性和效能權衡取捨

依賴複寫來達到高可用性或低延遲性 (或兩者皆是) 的分散式資料庫，可在讀取一致性與可用性、延遲性及輸送量之間進行基本取捨。 Azure Cosmos DB 將會以各種不同選項來達到資料一致性，而不是強式和最終一致性這兩個極端選項。 Cosmos DB 可讓開發人員從一致性頻譜 (最強到最弱) 內五個定義完善的一致性模型之間進行選擇：**強式**、**限定過期**、**工作階段**、**一致前置詞**和**最終**。 這五個一致性模型中的每一個都提供可用性和效能權衡取捨，並且由全方位 SLA 所支援。

## <a name="consistency-levels-and-latency"></a>一致性層級和延遲

- 一律保證適用於所有一致性層級的**讀取延遲**都會在第 99 個百分位數小於 10 毫秒且由 SLA 所支援。 平均 (在第 50 個百分位數) 讀取延遲通常是 2 毫秒或更少。

- 除了跨越數個區域且使用強式一致性設定的 Cosmos 帳戶，一律保證適用於剩餘一致性層級的**寫入延遲**都會在第 99 個百分位數時小於 10 毫秒。 這個寫入延遲由 SLA 所支援。 平均 (在第 50 個百分位數) 寫入延遲通常是 5 毫秒或更少。

- 針對具有使用強式一致性 (目前處於預覽狀態) 設定之數個區域的 Cosmos 帳戶，保證**寫入延遲**會在第 99 個百分位數小於 < (2 * 來回時間/RTT) + 10 毫秒。 這兩個最遠區域中任一個區域間的 RTT 會與您的 Cosmos 帳戶相關聯。 確切的 RTT 延遲是一個光速距離的函式和確切的 Azure 網路拓樸。 Azure 網路不會針對任兩個 Azure 區域之間的 RTT 提供任何延遲 SLA。 對於您 Cosmos 帳戶的 Cosmos DB 複寫延遲會顯示於 Azure 入口網站中，可讓您監視與 Cosmos 帳戶相關聯之各種不同區域間的複寫延遲。

## <a name="consistency-levels-and-throughput"></a>一致性層級和輸送量

- 相較於強式和限定過期，工作階段、一致前置詞和最終一致性層級可針對相同數量的要求單位提供大約 2X 的讀取輸送量。

- 針對指定類型的寫入作業 (例如，插入、取代、更新插入、刪除等等)，對於要求單位的寫入輸送量會與所有一致性層級完全相同。

## <a name="next-steps"></a>後續步驟

接下來，您將使用下列文章，深入了解分散式系統中的全域散發和一般一致性權衡取捨：

* [新式分散式資料庫系統設計的一致性權衡取捨](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html) \(英文\)
* [高可用性](high-availability.md)
* [Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
