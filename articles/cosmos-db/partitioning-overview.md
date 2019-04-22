---
title: Azure Cosmos DB 中的資料分割
description: Azure Cosmos DB 中的分区概述。
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.openlocfilehash: e88be8e7b94566ff94dd94a8679f8ade9d54c0b6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784514"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB 中的資料分割

Azure Cosmos DB 使用分区缩放数据库中的单个容器，以满足应用程序的性能需求。 在分区中，可将容器中的项分割成不同的子集（称作“逻辑分区”）。 邏輯磁碟分割上的值形成基礎*分割區索引鍵*與容器中的每個項目相關聯。 逻辑分区中的所有项具有相同的分区键值。

例如，容器會保留項目。 每個項目具有唯一值`UserID`屬性。 如果 `UserID` 充当容器中的项的分区键，并且有 1,000 个唯一的 `UserID` 值，则会为容器创建 1,000 个逻辑分区。

決定項目的邏輯資料分割資料分割索引鍵，除了在容器中的每個項目具有*項目識別碼*（邏輯磁碟分割內唯一）。 将分区键与项 ID 相结合可以创建项的索引用于唯一标识该项。

[選擇資料分割索引鍵](partitioning-overview.md#choose-partitionkey)是一項重要的決策會影響您的應用程式效能。

## <a name="managing-logical-partitions"></a>管理逻辑分区

Azure Cosmos DB 會以透明的方式並自動管理上有效率地滿足容器的延展性和效能需求的實體分割區的邏輯磁碟分割的位置。 随着应用程序的吞吐量和存储要求的提高，Azure Cosmos DB 可移动逻辑分区，以自动在更多的服务器之间分散负载。 

Azure Cosmos DB 使用基于哈希的分区在物理分区之间分散逻辑分区。 Azure Cosmos DB 对项的分区键值进行哈希处理。 哈希处理结果确定了物理分区。 然后，Azure Cosmos DB 在物理分区之间均匀分配分区键哈希的键空间。

單一的邏輯分割區中存取資料的查詢會比查詢存取多個資料分割更符合成本效益。 只允许针对单个逻辑分区中的项执行事务（在存储过程或触发器中）。

若要详细了解 Azure Cosmos DB 如何管理分区，请参阅[逻辑分区](partition-data.md)。 （不需要了解內部的詳細資料，以建置或執行您的應用程式，但此處加入好奇的讀者。）

## <a id="choose-partitionkey"></a>選擇分割區索引鍵

以下是正確的指引，來選擇資料分割索引鍵：

* 单个逻辑分区的存储空间上限为 10 GB。  

* Azure Cosmos 容器有最小輸送量 （RU/秒） 秒 400 個要求單位。 对同一分区键的请求不能超过分配给某个分区的吞吐量。 如果请求超过分配的吞吐量，则请求将受到速率限制。 因此，請務必挑選不會在應用程式內產生「作用點」的分割區索引鍵。

* 選擇具有各種不同值的分割區索引鍵，以及存取平均分散到邏輯分割區的模式。 这有助于在逻辑分区集之间分散容器中的数据和活动，以便可以在逻辑分区之间分配数据存储和吞吐量的资源。

* 选择可以持续在所有分区之间均匀分散工作负荷的分区键。 所选的分区键应该可以根据在多个分区之间分配项的目标，平衡有效分区查询和事务的需求，以实现可伸缩性。

* 分区键的候选项可能包括经常在查询中显示为筛选器的属性。 可藉由在篩選述詞中包含分割區索引鍵，有效地路由傳送查詢。

## <a name="next-steps"></a>後續步驟

* 深入了解[資料分割與水平調整 Azure Cosmos DB 中](partition-data.md)。
* 了解 [Azure Cosmos DB 中佈建的輸送量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全域分散](distribute-data-globally.md)。
