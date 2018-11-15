---
title: Azure Cosmos DB 中的資料分割和水平調整
description: 了解資料分割在 Azure Cosmos DB 中的運作方式、如何設定資料分割和資料分割索引鍵，以及如何為您的應用程式挑選合適的資料分割索引鍵。
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: 5dd1926496351f5bbfe8e5b3e4d1e0b68e82d272
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283388"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB 中的資料分割和水平調整

本文將說明 Azure Cosmos DB 中的實體和邏輯分割區，以及用來進行調整與資料分割的最佳做法。 

## <a name="logical-partitions"></a>邏輯分割區

邏輯分割區會由一組具有相同分割區索引鍵的項目所組成。 例如，假設容器中的所有項目均包含 `City` 屬性，則您可以使用 `City` 作為容器的分割區索引鍵。 具備 `City` 特定值 (例如 "London"、"Paris"、"NYC" 等) 的項目群組將形成不同的邏輯分割區。

在 Azure Cosmos DB 中，容器是延展性的基本單位。 新增至容器的資料和您在容器上佈建的輸送量都會自動 (水平) 分割到一組邏輯分割區。 它們會根據您針對 Cosmos 容器指定的分割區索引鍵進行分割。 若要深入了解，請參閱[如何為您的 Cosmos 容器指定分割區索引鍵](how-to-create-container.md)一文。

邏輯分割區會定義資料庫交易的範圍。 您可以使用含有快照集隔離的交易來更新邏輯分割區內的項目。

將新項目新增至容器時，或者當容器上佈建的輸送量增加時，系統就會以透明的方式建立新的邏輯分割區。

## <a name="physical-partitions"></a>實體分割區

您可以藉由將資料和輸送量散發到許多邏輯分割區來調整 Cosmos 容器。 就內部而言，會將一或多個邏輯分割區對應至由一組複本 (也稱為「複本集」) 所組成的**資源分割區**。 每個複本集都會裝載 Cosmos 資料庫引擎的執行個體。 複本集讓資源分割區內儲存的資料都具耐久性、高度可用且一致。 資源分割區支援固定且最大數量的儲存體和 RU。 組成資源分割區的每個複本都會繼承儲存體配額。 此外，資源分割區的所有複本會共同支援配置給資源分割區的輸送量。 下圖顯示如何將邏輯分割區對應至全域散發的實體分割區：

![Azure Cosmos DB 資料分割](./media/partition-data/logical-partitions.png)

為容器佈建的輸送量會平均分割到實體分割區。 因此，不會平均散發輸送量要求的分割區索引鍵設計可以建立「熱」分割區。 熱分割區可能導致佈建輸送量受到速率限制且以無效率的方式使用。

與邏輯分割區不同，實體分割區是系統的內部實作。 您無法控制它們的大小、位置、計數，或邏輯分割區與實體分割區之間的對應。 不過，您可以藉由選擇正確的分割區索引鍵，來控制邏輯分割區的數目以及資料和輸送量的散發。

## <a name="next-steps"></a>後續步驟

本文提供了資料分割的概觀，以及在 Azure Cosmos DB 中進行調整和資料分割的最佳做法。 

* 了解 [Azure Cosmos DB 中佈建的輸送量](request-units.md)
* 了解 [Azure Cosmos DB 中的全域分散](distribute-data-globally.md)
* 了解如何[選擇分割區索引鍵](partitioning-overview.md#choose-partitionkey)
* 了解[如何在 Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)
* 了解[如何在 Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)
