---
title: Azure Cosmos DB 遍及各區的情形
description: 本文說明 Azure Cosmos DB 及不同雲端環境遍及各區的情形。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 851da72a308b164b9a01b7ccfa3d541fc06810ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926212"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Azure Cosmos DB 遍及各區的情形

Azure Cosmos DB 是 Azure 中的一项基础服务，默认情况下，在所有提供 Azure 的区域均可使用。 Azure 目前已在全球 [54 個區域](https://azure.microsoft.com/global-infrastructure/regions/)中提供使用。 

[![可使用 Azure Cosmos DB 的區域](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

在提供給客戶的五個不同 Azure 雲端環境中，全都有提供 Cosmos DB：

* **Azure 公用**雲端，全球皆有提供。

* **Azure 中國 21Vianet**可透過 Microsoft 與其中一個國家/地區的最大的網際網路提供者在中國的 21Vianet 之間的合作關係。

* **Azure Germany** 在資料信任者模型下提供服務，以確保 Deutsche Telecom 子公司 T-Systems International GmbH 以德國資料信任者的形式，掌管留在德國的客戶資料。

* **Azure Government** 在北美洲的四個區域中提供給美國政府機構和其合作夥伴使用。 

* **適用於美國國防部 (DoD) azure Government** US Department of Defense 至美國境內的兩個區域均可使用。

## <a name="regional-presence-with-global-distribution"></a>透過全球發佈遍及各區

根據預設，所有 Azure 區域中可用 （包括 SQL、 MongoDB、 Cassandra、 Gremlin 及資料表） 的 Azure Cosmos DB 所公開的所有 Api。 比方說，您可以讓 MongoDB 和 Cassandra Api 由 Azure Cosmos DB 中的所有全域 Azure 區域，不僅在主權雲端，例如中國、 德國、 政府和美國國防部 (DoD) 區域。

Azure Cosmos db[分散在世界各地](distribute-data-globally.md)資料庫服務。 您可以將任意數目的 Azure 區域與您的 Azure Cosmos 帳戶相關聯，而且您的資料會自動且透明地複寫。 您可以隨時在 Azure Cosmos 帳戶中新增或移除區域。 Azure Cosmos DB 具有周全的全球發佈功能和多重主要複寫通訊協定，可在第 99 個百分位數提供不到 10 毫秒的讀取和寫入延遲、具有 99.999 的讀取和寫入可用性，並可跨與您 Azure Cosmos 帳戶相關聯的所有區域，彈性調整針對其中的讀取和寫入所佈建的輸送量。 Azure Cosmos DB 也會提供五個具有完善定義的一致性模型，您可以選擇對資料套用特定的一致性模型。 最后，Azure Cosmos DB 是业内唯一一种提供综合[服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) 的数据库服务，包括预配的吞吐量、第 99 百分位的延迟、高可用性和一致性。 以上功能在所有 Azure 云中推出。

## <a name="next-steps"></a>後續步驟

现可通过以下文章了解 Azure Cosmos DB 的核心概念：

* [全球資料發佈](distribute-data-globally.md)
* [如何管理 Azure Cosmos DB 帳戶](manage-account.md)
* [佈建 Azure Cosmos 容器和資料庫的輸送量](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
