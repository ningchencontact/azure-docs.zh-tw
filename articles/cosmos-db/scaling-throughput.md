---
title: 調整 Azure Cosmos DB 的輸送量
description: 此文章說明 Azure Cosmos DB 如何彈性調整輸送量
services: cosmos-db
author: dharmas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 7e3f6d053e9466f07e15b0c2c1092fece76c98a4
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52160659"
---
# <a name="scaling-throughput-in-azure-cosmos-db"></a>調整 Azure Cosmos DB 的輸送量

在 Azure Cosmos DB 中，佈建的輸送量會以要求單位/秒 (RU/秒) 來表示。 RU 會根據您的 Cosmos 容器來衡量讀取與寫入作業的成本，如下圖所示：

![要求單位](./media/scale-throughput/figure1.png)

您可以在 Cosmos 容器或 Cosmos 資料庫上佈建 RU。 佈建於容器上的 RU 僅適用於在該容器上執行的作業。 佈建於資料庫上的 RU 會在該資料庫內的所有容器之間共用 (但任何具有以獨佔方式指派之 RU 的容器除外)。

對於要彈性調整的輸送量，您隨時都能增加或減少佈建的 RU/秒。 如需詳細資訊，請參閱[如何佈建輸送量](set-throughput.md)並彈性調整 Cosmos 容器和資料庫。 對於全域調整的輸送量，您隨時都能新增或移除 Cosmos 帳戶上的區域。 如需詳細資訊，請參閱[在資料庫帳戶中新增/移除區域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)。 在許多案例中，將多個區域與 Cosmos 帳戶產生關聯，對於在世界各地達成低延遲及[高可用性](high-availability.md)而言非常重要。

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>如何在區域中散發佈建輸送量

如果您在 Cosmos 容器 (或資料庫) 上佈建 'R' 個 RU，Cosmos DB 可確保在與您 Cosmos 帳戶相關聯的「每個」區域中都有 'R' 個 RU 可供使用。 每當您將新區域新增至帳戶時，Cosmos DB 就會在新增的區域中自動佈建 'R' 個 RU。 針對您 Cosmos 容器執行的作業保證會在每個區域中取得 'R' 個 RU。 您無法選擇性地將 RU 指派給特定區域。 基於 Cosmos 容器 (或資料庫) 佈建的 RU 會針對與您 Cosmos 帳戶相關聯的所有區域進行佈建。

假設 Cosmos 容器是使用 'R' 個 RU 設定的，而且有 'N' 個與 Cosmos 帳戶相關聯的區域，則：

- 如果 Cosmos 帳戶是使用單一寫入區域設定的，容器上可供全域使用的 RU 總計 = R x N。

- 如果 Cosmos 帳戶是使用多個寫入區域設定的，容器上可供全域使用的 RU 總計 = R x (N+1)。 額外的 R 個 RU 會自動佈建來跨區域處理更新衝突和反熵流量。

您選擇的[一致性模型](consistency-levels.md)也會影響輸送量。 相較於強式和限定過期，您可以針對工作階段、一致前置詞和最終一致性取得大約 2X 的讀取輸送量。

## <a name="next-steps"></a>後續步驟

接下來，您將了解如何使用下列文章的說明來設定輸送量：

* [取得並設定適用於容器和資料庫的輸送量](set-throughput.md) 

