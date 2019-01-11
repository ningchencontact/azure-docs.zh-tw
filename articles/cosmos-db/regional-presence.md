---
title: Azure Cosmos DB 遍及各區的情形
description: 本文說明 Azure Cosmos DB 及不同雲端環境遍及各區的情形。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 2062862670f5b373d3153c473e45455e10d5e996
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548675"
---
# <a name="regional-presence-of-azure-cosmos-db"></a>Azure Cosmos DB 遍及各區

Azure 目前已在全球 [54 個區域](https://azure.microsoft.com/global-infrastructure/regions/)中提供使用。 Azure Cosmos DB 是 Azure 中的基本服務，只要是有提供 Azure 的區域，就會提供 Azure Cosmos DB。

[![可使用 Azure Cosmos DB 的區域](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

在提供給客戶的五個不同 Azure 雲端環境中，全都有提供 Cosmos DB：

* **Azure 公用**雲端，全球皆有提供。

* Microsoft 與中國最大的網際網路供應商 21Vianet 締結了特殊的合作關係，從而提供 **Azure China**。

* **Azure Germany** 在資料信任者模型下提供服務，以確保 Deutsche Telecom 子公司 T-Systems International GmbH 以德國資料信任者的形式，掌管留在德國的客戶資料。

* **Azure Government** 在北美洲的四個區域中提供給美國政府機構和其合作夥伴使用。 

* **Azure Government for Department of Defense (DoD)** 在北美洲的兩個區域中提供給美國國防部使用。

## <a name="regional-presence-with-global-distribution"></a>透過全球發佈遍及各區

Azure Cosmos DB 所公開的不同 API (包括 SQL、MongoDB、Cassandra、Gremlin 和 Azure 資料表儲存體) 可在所有 Azure 區域中使用。 例如，您不僅可以在全球所有 Azure 區域中擁有 Azure Cosmos DB 所公開的 MongoDB 和 Cassandra API，還可在 Azure 主權區域 (例如，Azure China、Azure Germany、Azure Government 和 Azure Department of Defense (DoD) 區域) 中擁有。

Azure Cosmos DB 是[分散在世界各地](distribute-data-globally.md)的資料庫。 您可以將任意數目的 Azure 區域與您的 Azure Cosmos 帳戶相關聯，而且您的資料會自動且透明地複寫。 您可以隨時在 Azure Cosmos 帳戶中新增或移除區域。 Azure Cosmos DB 具有周全的全球發佈功能和多重主要複寫通訊協定，可在第 99 個百分位數提供不到 10 毫秒的讀取和寫入延遲、具有 99.999 的讀取和寫入可用性，並可跨與您 Azure Cosmos 帳戶相關聯的所有區域，彈性調整針對其中的讀取和寫入所佈建的輸送量。 Azure Cosmos DB 也會提供五個具有完善定義的一致性模型，您可以選擇對資料套用特定的一致性模型。 最後，Azure Cosmos DB 是業界唯一會提供全方位服務等級協定 (SLA) 的資料庫服務，協定範圍包含所佈建的輸送量、在第 99 個百分位數的延遲、高可用性和一致性。

## <a name="next-steps"></a>後續步驟

現在，您可以透過下列文章來了解 Azure Cosmos DB 的不同概念：

* [全球資料發佈](distribute-data-globally.md)
* [如何管理 Azure Cosmos DB 帳戶](manage-account.md)
* [佈建 Azure Cosmos 容器和資料庫的輸送量](set-throughput.md)
* [Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
