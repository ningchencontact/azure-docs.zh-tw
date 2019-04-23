---
title: Azure Cosmos DB 中的資料分割和水平調整
description: 了解分区在 Azure Cosmos DB 中的工作原理，分区和分区键的配置方式以及应用程序分区键的选择方法。
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.openlocfilehash: cf454d6504f0433d7ac7ca883982ae317b14f814
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797818"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB 中的資料分割和水平調整

本文介绍 Azure Cosmos DB 中的物理分区和逻辑分区。 此外，介绍有关缩放和分区的最佳做法。 

## <a name="logical-partitions"></a>邏輯分割區

逻辑分区由一组具有相同分区键的项构成。 例如，在所有项都包含一个 `City` 属性的容器中，可以使用 `City` 作为该容器的分区键。 具有特定值的項目群組`City`，這類`London`， `Paris`，和`NYC`，形成邏輯的不同資料分割。 删除基础数据时，无需担心是否会删除分区。

在 Azure Cosmos DB 中，容器是延展性的基本單位。 添加到容器的数据以及针对容器预配的吞吐量将自动在一组逻辑分区之间（水平）分区。 根據 Azure Cosmos 容器您指定資料分割索引鍵會分割資料和輸送量。 如需詳細資訊，請參閱 <<c0> [ 建立 Azure Cosmos 容器](how-to-create-container.md)。

邏輯分割區也會定義資料庫交易的範圍。 您可以使用更新的邏輯磁碟分割內的項目[快照集隔離交易](database-transactions-optimistic-concurrency.md)。 當新項目加入至容器時，系統會以透明的方式建立新的邏輯磁碟分割。

## <a name="physical-partitions"></a>實體分割區

Azure Cosmos 容器被調整資料和輸送量分配給大量的邏輯磁碟分割。 就內部而言，一或多個邏輯分割區會對應至實體分割區所組成的一組複本，也稱為[*複本集*](global-dist-under-the-hood.md)。 每个副本集托管 Azure Cosmos DB 数据库引擎的一个实例。 副本集使物理分区中存储的数据具有持久性、高可用性和一致性。 物理分区支持最大数量的存储和请求单位 (RU)。 构成物理分区的每个副本均继承该分区的存储配额。 物理分区的所有副本共同支持分配给物理分区的吞吐量。 

下圖顯示如何將邏輯分割區對應至全域散發的實體分割區：

![演示 Azure Cosmos DB 分区的插图](./media/partition-data/logical-partitions.png)

为容器预配的吞吐量在物理分区之间均匀划分。 不会均匀分配吞吐量请求的分区键设计可能会产生“热”分区。 在 速率限制和佈建的輸送量和更高成本的效率不佳的使用中，可能會產生熱點資料分割。

與邏輯分割區不同，實體分割區是系統的內部實作。 无法控制物理分区的大小、位置或计数，也无法控制逻辑分区与物理分区之间的映射。 不過，您可以控制的邏輯磁碟分割和資料、 工作負載和輸送量的發佈數目[選擇正確邏輯資料分割索引鍵](partitioning-overview.md#choose-partitionkey)。

## <a name="next-steps"></a>後續步驟

* 了解如何[选择分区键](partitioning-overview.md#choose-partitionkey)。
* 了解 [Azure Cosmos DB 中佈建的輸送量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全域分散](distribute-data-globally.md)。
* 了解如何[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。
