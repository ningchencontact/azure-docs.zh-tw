---
title: 在 Azure Cosmos 容器和資料庫上佈建輸送量
description: 了解如何設定 Azure Cosmos 容器和資料庫的佈建輸送量。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: ed3a171e60c078975de7003cdf599f0bac62c402
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61330289"
---
# <a name="provision-throughput-on-containers-and-databases"></a>在容器和資料庫中佈建輸送量

Azure Cosmos 資料庫是一組容器的管理單位。 資料庫是由一組無從驗證結構描述的容器所組成的。 Azure Cosmos 容器是適用於輸送量和儲存體的延展性單位。 容器會跨 Azure 區域內的一組機器進行水平分割，並散發到與您 Azure Cosmos 帳戶相關聯的所有 Azure 區域。

使用 Azure Cosmos DB 时，可以在两个粒度级别预配吞吐量：
 
- Azure Cosmos 容器
- Azure Cosmos 資料庫

## <a name="set-throughput-on-a-container"></a>在容器上設定輸送量  

在 Azure Cosmos 容器上预配的吞吐量专门保留给该容器使用。 該容器隨時都可接收佈建輸送量。 容器上的佈建輸送量在財務方面會由 SLA 所支援。 若要了解如何在容器上配置吞吐量，请参阅[在 Azure Cosmos 容器上预配吞吐量](how-to-provision-container-throughput.md)。

在容器上设置预配吞吐量是最频繁使用的选项。 可以通过使用[请求单位 (RU)](request-units.md) 预配任意数量的吞吐量来弹性缩放容器的吞吐量。 

Azure Cosmos 容器上佈建的輸送量會統一散發到容器的所有邏輯分割區。 无法选择性地指定逻辑分区的吞吐量。 因為實體分割區會裝載容器的一或多個邏輯分割區；因此，實體分割區專屬於該容器並支援該容器上佈建的輸送量。 

如果逻辑分区上运行的工作负荷消耗的吞吐量超过了分配给该逻辑分区的吞吐量，操作将受到速率限制。 出现速率限制时，可以增大整个容器的预配吞吐量，或重试操作。 如需有關分割的詳細資訊，請參閱[邏輯分割區](partition-data.md)。

當您想要保證容器的效能時，我們建議您在容器資料粒度上設定輸送量。

下圖示範實體分割區如何裝載容器的一或多個邏輯分割區：

![實體分割區](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>在資料庫上設定輸送量

當您在 Azure Cosmos 資料庫上佈建輸送量時，輸送量會在資料庫中的所有容器上共用。 一种例外是在数据库中的特定容器上指定了预配的吞吐量。 在容器之间共享数据库级预配吞吐量相当于在计算机群集上托管数据库。 因為資料庫共用內的所有容器都會共用機器上可用的資源，因此，您自然不會取得任何特定容器上的可預測效能。 若要了解如何在数据库上配置预配吞吐量，请参阅[在 Azure Cosmos 数据库上配置预配吞吐量](how-to-provision-database-throughput.md)。

在 Azure Cosmos 数据库上设置吞吐量可保证随时能够获得该数据库的预配吞吐量。 因為資料庫共用內的所有容器都會共用佈建輸送量，因此，Azure Cosmos DB 不會針對該資料庫中的特定容器提供任何可預測的輸送量保證。 特定容器可接收的輸送量部分取決於：

* 容器數目。
* 對於各種容器所選擇的分割區索引鍵。
* 散發於容器中各種不同邏輯分割區的工作負載。 

當您想要在多個容器之間共用輸送量，但不想讓輸送量專屬於任何特定容器時，我們建議您在資料庫上設定輸送量。 

以下是最好在資料庫層級佈建輸送量的範例：

* 在一組容器上共用資料庫的佈建輸送量非常適用於多租用戶應用程式。 每位使用者都可透過不同的 Azure Cosmos 容器來表示。

* 當您將從 VM 叢集或從內部部署實體伺服器裝載的 NoSQL 資料庫 (例如 MongoDB 或 Cassandra) 移轉到 Azure Cosmos DB 時，在一組容器之間共用資料庫的佈建輸送量就很實用。 將 Azure Cosmos 資料庫上設定的佈建輸送量想像為 MongoDB 或 Cassandra 叢集計算容量之佈建輸送量的邏輯對等項目 (但更符合成本效益且更具彈性)。  

必须使用[分区键](partition-data.md)创建在具有预配吞吐量的数据库内创建的所有容器。 在任何指定的時間點，配置給資料庫內容器的輸送量都會散發到該容器的所有邏輯分割區。 如果有容器共享在数据库上配置的预配吞吐量，则无法选择性地将吞吐量应用到特定的容器或逻辑分区。 

如果邏輯分割區上的工作負載所取用的輸送量超過配置給特定邏輯分割區的輸送量時，您的作業將會受到速率限制。 出现速率限制时，可以增大整个数据库的吞吐量，或重试操作。 如需有關分割的詳細資訊，請參閱[邏輯分割區](partition-data.md)。

可在单个物理分区中托管多个属于不同容器的逻辑分区，这些容器共享预配给数据库的吞吐量。 尽管容器的单个逻辑分区始终划归到物理分区，但可以在“R”物理分区中映射和托管“C”容器中共享数据库预配吞吐量的“L”逻辑分区。 

下圖顯示實體分割區如何裝載一或多個屬於資料庫內不同容器的邏輯分割區：

![實體分割區](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>在資料庫和容器上設定輸送量

您可以結合這兩個模型， 就能同時在資料庫和容器上佈建輸送量。 下列範例說明如何在 Azure Cosmos 資料庫和容器上佈建輸送量：

* 可以创建预配吞吐量为“K”RU 的名为“Z”的 Azure Cosmos 数据库。 
* 接下来，在该数据库中创建名为 A、B、C、D、E 的五个容器。
* 可在名为 B 的容器上显式配置“P”RU 的预配吞吐量。
* “K”RU 吞吐量在 A、C、D、E 这四个容器之间共享。提供给 A、C、D 或 E 的确切吞吐量各不相同。 而且沒有適用於每個個別容器輸送量的 SLA。
* 保证名为 B 的容器始终可以获得“P”RU 吞吐量。 並受到 SLA 支援。

## <a name="update-throughput-on-a-database-or-a-container"></a>在数据库或容器上更新吞吐量

创建 Azure Cosmos 容器或数据库后，可以更新预配的吞吐量。 对于可以在数据库或容器上配置的最大预配吞吐量，没有任何限制。 最小预配吞吐量取决于以下因素： 

* 曾经存储在容器中的最大数据大小
* 曾经在容器上预配的最大吞吐量
* 曾经在数据库中创建的具有共享吞吐量的 Azure Cosmos 容器的最大数目。 

可以通过 SDK 以编程方式检索容器或数据库的最小吞吐量，也可以在 Azure 门户中查看值。 使用 .NET SDK 时，可以通过 [DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) 方法缩放预配的吞吐量值。 使用 Java SDK 时，可以通过 [RequestOptions.setOfferThroughput](sql-api-java-samples.md#offer-examples) 方法缩放预配的吞吐量值。 

使用 .NET SDK 时，可以通过 [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) 方法检索容器或数据库的最小吞吐量。 

可以随时缩放容器或数据库的预配吞吐量。 

## <a name="comparison-of-models"></a>模型的比較

|**參數**  |**資料庫上佈建的輸送量**  |**容器上佈建的輸送量**|
|---------|---------|---------|
|RU 數目下限 |400 (在前四個容器之後，每個額外的容器至少需要每秒 100 RU)。 |400|
|每個容器的 RU 數目下限|100|400|
|取用 1 GB 儲存體所需的 RU 數目下限|40|40|
|RU 數目上限|在資料庫上無限制。|在容器上無限制。|
|指派給或適用於特定容器的 RU|不提供保證。 指派給指定容器的 RU 視屬性而定。 屬性可以選擇是共用輸送量之容器的資料分割索引鍵、工作負載散發，以及容器的數目。 |設定於容器上的所有 RU 都是專為該容器保留的。|
|容器的儲存體上限|無限制。|無限制。|
|容器中每個邏輯分割區的輸送量上限|10K RU|10K RU|
|容器中每個邏輯分割區的儲存體上限 (資料 + 索引)|10 GB|10 GB|

## <a name="next-steps"></a>後續步驟

* 深入了解[邏輯分割區](partition-data.md)。
* 了解如何[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。

