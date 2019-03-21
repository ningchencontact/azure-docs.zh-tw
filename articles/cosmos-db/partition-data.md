---
title: Azure Cosmos DB 中的資料分割和水平調整
description: 了解分区在 Azure Cosmos DB 中的工作原理，分区和分区键的配置方式以及应用程序分区键的选择方法。
ms.author: mjbrown
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 4c6847d8f870c02228aa14ab9e11c85b091ec48b
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959947"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB 中的資料分割和水平調整

本文介绍 Azure Cosmos DB 中的物理分区和逻辑分区。 此外，介绍有关缩放和分区的最佳做法。 

## <a name="logical-partitions"></a>邏輯分割區

逻辑分区由一组具有相同分区键的项构成。 例如，在所有项都包含一个 `City` 属性的容器中，可以使用 `City` 作为该容器的分区键。 具有特定 `City` 值（例如 `London`、`Paris` 和 `NYC`）的项组构成了独立的逻辑分区。 删除基础数据时，无需担心是否会删除分区。

在 Azure Cosmos DB 中，容器是延展性的基本單位。 添加到容器的数据以及针对容器预配的吞吐量将自动在一组逻辑分区之间（水平）分区。 数据和吞吐量是根据为 Azure Cosmos DB 容器指定的分区键分区的。 有关详细信息，请参阅[创建 Azure Cosmos DB 容器](how-to-create-container.md)。

邏輯分割區會定義資料庫交易的範圍。 您可以使用含有快照集隔離的交易來更新邏輯分割區內的項目。 將新項目新增至容器時，系統會自動在背景建立新的邏輯分割區。

## <a name="physical-partitions"></a>實體分割區

通过将数据和吞吐量分配到大量逻辑分区上来缩放 Azure Cosmos DB 容器。 在内部，一个或多个逻辑分区将映射到由一组副本（也称为副本集）构成的物理分区。 每个副本集托管 Azure Cosmos DB 数据库引擎的一个实例。 副本集使物理分区中存储的数据具有持久性、高可用性和一致性。 物理分区支持最大数量的存储和请求单位 (RU)。 构成物理分区的每个副本均继承该分区的存储配额。 物理分区的所有副本共同支持分配给物理分区的吞吐量。 

下圖顯示如何將邏輯分割區對應至全域散發的實體分割區：

![演示 Azure Cosmos DB 分区的插图](./media/partition-data/logical-partitions.png)

为容器预配的吞吐量在物理分区之间均匀划分。 不会均匀分配吞吐量请求的分区键设计可能会产生“热”分区。 热分区可能导致速率限制以及预配吞吐量的低效使用。

與邏輯分割區不同，實體分割區是系統的內部實作。 无法控制物理分区的大小、位置或计数，也无法控制逻辑分区与物理分区之间的映射。 不過，您可以藉由選擇正確的分割區索引鍵，來控制邏輯分割區的數目以及資料和輸送量的散發。

## <a name="next-steps"></a>後續步驟

* 了解如何[选择分区键](partitioning-overview.md#choose-partitionkey)。
* 了解 [Azure Cosmos DB 中佈建的輸送量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全域分散](distribute-data-globally.md)。
* 了解如何[在 Azure Cosmos DB 容器中预配吞吐量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos DB 数据库中预配吞吐量](how-to-provision-database-throughput.md)。
