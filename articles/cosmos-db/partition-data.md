---
title: Azure Cosmos DB 中的資料分割和水平調整
description: 了解資料分割的運作方式，在 Azure Cosmos DB、 如何設定資料分割和資料分割索引鍵，以及如何選擇正確的資料分割索引鍵，為您的應用程式。
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.openlocfilehash: 30290652044499ff8e537adc90689f562e1489d2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65953765"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB 中的資料分割和水平調整

這篇文章說明 Azure Cosmos DB 中的實體和邏輯分割區。 它也會討論調整和資料分割最佳的作法。 

## <a name="logical-partitions"></a>邏輯分割區

邏輯分割區是由一組具有相同的資料分割索引鍵的項目所組成。 例如，在容器中的所有項目包含`City`屬性，您可以使用`City`為容器的資料分割索引鍵。 具有特定值的項目群組`City`，這類`London`， `Paris`，和`NYC`，形成邏輯的不同資料分割。 您不必擔心基礎的資料刪除時，刪除資料分割。

在 Azure Cosmos DB 中，容器是延展性的基本單位。 加入至容器和容器佈建輸送量的資料會自動跨一組邏輯資料分割 （水平） 分割。 根據 Azure Cosmos 容器您指定資料分割索引鍵會分割資料和輸送量。 如需詳細資訊，請參閱 <<c0> [ 建立 Azure Cosmos 容器](how-to-create-container.md)。

邏輯分割區也會定義資料庫交易的範圍。 您可以使用更新的邏輯磁碟分割內的項目[快照集隔離交易](database-transactions-optimistic-concurrency.md)。 當新項目加入至容器時，系統會以透明的方式建立新的邏輯磁碟分割。

## <a name="physical-partitions"></a>實體分割區

Azure Cosmos 容器被調整資料和輸送量分配給大量的邏輯磁碟分割。 就內部而言，一或多個邏輯分割區會對應至實體分割區所組成的一組複本，也稱為[*複本集*](global-dist-under-the-hood.md)。 每個複本設定主機的 Azure Cosmos DB 資料庫引擎執行個體。 複本集可讓儲存在實體分割區內持久、 高度可用且一致的資料。 實體分割區支援的最大儲存體 」 和 「 要求單位 (Ru) 數量。 每個構成實體的資料分割的複本會繼承資料分割的儲存體配額。 所有實體分割區複本共同支援實體的分割區配置的輸送量。 

下圖顯示如何將邏輯分割區對應至全域散發的實體分割區：

![示範 Azure Cosmos DB 分割映像](./media/partition-data/logical-partitions.png)

為容器佈建的輸送量會平均分配實體分割區。 不將輸送量要求平均分散資料分割索引鍵設計可能會建立 「 熱 」 資料分割。 在 速率限制和佈建的輸送量和更高成本的效率不佳的使用中，可能會產生熱點資料分割。

與邏輯分割區不同，實體分割區是系統的內部實作。 您無法控制大小、 位置或實體的分割區計數，而且您無法控制邏輯分割區和實體分割區之間的對應。 不過，您可以控制的邏輯磁碟分割和資料、 工作負載和輸送量的發佈數目[選擇正確邏輯資料分割索引鍵](partitioning-overview.md#choose-partitionkey)。

## <a name="next-steps"></a>後續步驟

* 深入了解[選擇資料分割索引鍵](partitioning-overview.md#choose-partitionkey)。
* 了解 [Azure Cosmos DB 中佈建的輸送量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全域分散](distribute-data-globally.md)。
* 了解如何[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。
