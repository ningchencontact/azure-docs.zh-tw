---
title: 調整 Azure Cosmos DB 的輸送量
description: 此文章說明 Azure Cosmos DB 如何彈性調整輸送量
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: b645fe210e7eeb073380dcadefead3e1b4d7ccc0
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407431"
---
# <a name="globally-scale-provisioned-throughput"></a>全域調整佈建的輸送量 

在 Azure Cosmos DB 中，佈建的輸送量以要求單位/秒 （RU/秒或複數形式 Ru）。 RU 會根據您的 Cosmos 容器來衡量讀取與寫入作業的成本，如下圖所示：

![要求單位](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

您可以在 Cosmos 容器或 Cosmos 資料庫上佈建 RU。 在容器上佈建的 Ru 是僅適用於該容器上執行的作業。 佈建於資料庫上的 RU 會在該資料庫內的所有容器之間共用 (但任何具有以獨佔方式指派之 RU 的容器除外)。

彈性地調整佈建的輸送量，您可以增加或減少在任何時間的佈建的 RU/秒。 如需詳細資訊，請參閱 <<c0> [ 如何佈建輸送量](set-throughput.md)以及彈性地調整 Cosmos 容器和資料庫。 全域調整輸送量，您可以新增或移除您的 Cosmos 帳戶中的區域，在任何時間。 如需詳細資訊，請參閱[在資料庫帳戶中新增/移除區域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)。 將多個區域與 Cosmos 帳戶產生關聯，請務必在許多情況下-來達成低延遲及[高可用性](high-availability.md)世界各地。

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>如何在區域中散發佈建輸送量

如果您佈建 *'R'* Ru Cosmos 容器 （或資料庫），在 Cosmos DB 可確保 *'R'* Ru 位於*每個*Cosmos 帳戶相關聯的區域。 每當您將新的區域新增至您的帳戶，Cosmos DB 會自動會佈建 *'R'* 新加入的區域中的 Ru。 針對您的 Cosmos 容器執行的作業保證會取得 *'R'* 每個區域中的 Ru。 您無法選擇性地將 RU 指派給特定區域。 您的 Cosmos 帳戶相關聯的所有區域中佈建 Cosmos 容器 （或資料庫） 上佈建的 Ru。

假設 Cosmos 容器設有 *'R'* Ru 還有 *'n'* 區域關聯至 Cosmos 帳戶，然後：

- 如果 Cosmos 帳戶設定為使用單一的寫入區域，在容器上的全域可用的總 Ru = *R* x *N*。

- 如果 Cosmos 帳戶設有多個的寫入區域，在容器上的全域可用的總 Ru = *R* x (*N*+ 1)。 額外*R* Ru 會自動佈建程序更新衝突和反 entropy 流量跨區域。

您選擇的[一致性模型](consistency-levels.md)也會影響輸送量。 您可以取得大約 2 倍的讀取輸送量更寬鬆的一致性層級 (例如*工作階段*，*一致前置詞*並*最終*一致性) 相較於更強的一致性層級 (例如*限定過期*或是*強式*一致性)。

## <a name="next-steps"></a>後續步驟

接下來您可以了解如何在容器或資料庫上設定的輸送量：

* [取得並設定適用於容器和資料庫的輸送量](set-throughput.md) 

