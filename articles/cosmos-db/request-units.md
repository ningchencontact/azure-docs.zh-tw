---
title: Azure Cosmos DB 中的要求單位和輸送量
description: 了解如何在 Azure Cosmos DB 中指定和預估要求單位需求
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: 72ec8332a3363d5336fb84b3390d5e44ced0b2e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60925970"
---
# <a name="request-units-in-azure-cosmos-db"></a>Azure Cosmos DB 中的要求單位

使用 Azure Cosmos DB，您需要按小時支付所佈建的輸送量及所取用的儲存體。 必须预配吞吐量才能确保始终为 Azure Cosmos 数据库提供足够的系统资源。 需要有足够的资源才能达到或超过 [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) 的要求。

Azure Cosmos DB 支援許多 API，例如 SQL、MongoDB、Cassandra、Gremlin 和資料表。 每個 API 都有它自己的一組資料庫作業。 這些作業的範圍可從簡單的點讀取和寫入到複雜查詢。 每個資料庫作業都會根據作業的複雜度來取用系統資源。 

所有数据库操作的成本将由 Azure Cosmos DB 规范化，并以“请求单位”（缩写为 RU）表示。 您可以將每秒 RU 想像為輸送量的貨幣。 每秒 RU 是以速率為基礎的貨幣。 它會擷取執行 Azure Cosmos DB 所支援之資料庫作業所需的系統資源 (例如 CPU、IOPS 和記憶體)。 

读取 1 KB 项的成本为 1 个请求单位（1 个 RU）。 以类似方式为其他所有数据库操作分配 RU 成本。 無論您使用哪種 API 與 Azure Cosmos 容器互動，成本始終由 RU 測量。 無論資料庫作業是寫入、讀取或查詢，成本一律以 RU 為單位進行測量。

下图展示了 RU 的概要情况。

![資料庫作業會取用要求單位](./media/request-units/request-units.png)

為了管理和規劃容量，Azure Cosmos DB 會確保指定資料集上指定資料庫作業的 RU 數目具有決定性。 可以检查响应标头来跟踪任一数据库操作消耗的 RU 数。 了解[影响 RU 费用的因素](request-units.md#request-unit-considerations)以及应用程序吞吐量要求后，可以经济高效地运行应用程序。

您還是會以每秒為單位，以遞增 100 個 RU/秒的方式來佈建應用程式的 RU 數目。 若要調整應用程式的佈建輸送量，您可以隨時增加或減少 RU 數目。 可以以 100 RU 为增量或减量进行缩放。 您可以程式設計方式或使用 Azure 入口網站進行變更。 你需要按小时付费。

您可以在兩個不同的資料粒度上佈建輸送量： 

* **容器**：如需詳細資訊，請參閱[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* **数据库**：如需詳細資訊，請參閱[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。

## <a name="request-unit-considerations"></a>要求單位的考量

雖然您預估每秒佈建的的 RU 數目，但請考慮下列因素：

* **項目大小**：當項目的大小增加時，取用來讀取或寫入該項目的 RU 數目也會增加。

* **編製索引**：預設會自動編製每個項目的索引。 如果您選擇不要在容器中編製某些項目的索引，即會取用較少的 RU。

* **項目屬性計數**：假设所有属性采用默认索引，写入某个项所要消耗的 RU 数会随着项属性计数的增加而增加。

* **已編製索引的屬性**：每個容器上的索引原則會判定預設要編製哪些索引的屬性。 若要減少寫入作業的要求單位取用量，請限制已編製索引的屬性數目。

* **資料一致性**：相較於其他寬鬆一致性層級的讀取作業，強式和限定過期一致性層級在執行讀取作業時所取用 RU 大約是 2 倍以上。

* **查詢模式**：查詢的複雜性會影響針對作業所耗用的要求單位數目。 影響查詢作業成本的因素包括： 
    
    - 查询结果数
    - 谓词数
    - 谓词性质
    - 用户定义的函数数目
    - 源数据的大小
    - 结果集的大小
    - 投影数

  Azure Cosmos DB 保證相同資料上相同查詢的成本，一律會與重複執行上的 RU 數目相同。

* **指令碼使用方式**：和查詢一樣，預存程序和觸發程序都會根據所執行作業的複雜度來取用 RU。 开发应用程序时，请检查[请求费用标头](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query)，以更好地了解每个操作消耗的 RU 容量。

## <a name="next-steps"></a>後續步驟

* 深入了解如何[在 Azure Cosmos 容器和資料庫上佈建輸送量](set-throughput.md)。
* 深入了解[邏輯分割區](partition-data.md)。
* 深入了解如何[全域調整佈建的輸送量](scaling-throughput.md)。
* 了解如何[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。
* 了解如何[查找操作所产生的请求单位费用](find-request-unit-charge.md)。
* 了解如何[在 Azure Cosmos DB 中优化预配的吞吐量成本](optimize-cost-throughput.md)。
* 了解如何[优化 Azure Cosmos DB 中的读取和写入成本](optimize-cost-reads-writes.md)。
* 了解如何[优化 Azure Cosmos DB 中的查询成本](optimize-cost-queries.md)。
