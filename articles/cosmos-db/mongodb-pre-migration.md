---
title: 移轉前步驟對於資料移轉從 MongoDB 適用於 MongoDB 的 Azure Cosmos DB 的 API
description: 此文件會提供從 MongoDB 資料移轉的必要條件概觀，Cosmos db。
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 476a143555323bbb5058541000a5b1a26d23b71a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013427"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>移轉前步驟對於資料移轉從 MongoDB 適用於 MongoDB 的 Azure Cosmos DB 的 API

將資料從 MongoDB 移轉之前 (在內部部署或雲端 (IaaS)) 至 Azure Cosmos DB 的 mongodb 的 API，您應該：

1. [建立 Azure Cosmos DB 帳戶](#create-account)
2. [預估您的工作負載所需的輸送量](#estimate-throughput)
3. [挑選最佳的資料分割索引鍵，為您的資料](#partitioning)
4. [了解您可以設定您的資料編製索引原則](#indexing)

如果您已完成的移轉上述必要條件，請參閱[移轉 MongoDB 資料到 Azure Cosmos DB 的 MongoDB API](../dms/tutorial-mongodb-cosmos-db.md)實際資料的移轉說明。 如果沒有，本文件提供指示來處理這些必要條件。 

## <a id="create-account"></a> 建立 Azure Cosmos DB 帳戶 

開始之前移轉，您需要[建立 Azure Cosmos 帳戶使用 Azure Cosmos DB 的 API for MongoDB](create-mongodb-dotnet.md)。 

建立帳戶之後，您可以選擇設定[全域散發](distribute-data-globally.md)您的資料。 您也可以啟用多區域寫入 （或多重主機組態），可讓您區這兩個寫入和讀取區域的每一個。

![Account-Creation](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a> 預估您的工作負載的輸送量需求

使用開始移轉之前[資料庫移轉服務 (DMS)](../dms/dms-overview.md)，您應該估計您 Azure Cosmos 資料庫和集合的佈建的輸送量的量。

可在佈建輸送量：

- 集合

- 資料庫

> [!NOTE]
> 您也可以讓上述組合、 在資料庫中的一些集合可能會有專用的佈建輸送量和其他人可能共用的輸送量。 如需詳細資訊，請參閱[資料庫和容器上的設定輸送量](set-throughput.md)頁面。
>

您應該先決定您是否想要佈建資料庫或集合層級的輸送量或兩者的組合的動作。 一般情況下，建議您設定專用的輸送量層級的集合。 佈建的輸送量，在資料庫層級可讓您共用的佈建的輸送量的資料庫中的集合。 共用的輸送量，不過，不保證特定的輸送量，在每個個別的集合，而且沒有任何特定的集合上得到可預測的效能。

如果您不確定多少輸送量只能專用於每個個別的集合，您可以選擇資料庫層級的輸送量。 您可以將佈建的輸送量設定為邏輯的對等的計算容量，MongoDB VM 或實體伺服器，Azure Cosmos 資料庫，但是更符合成本效益的彈性調整能力。 如需詳細資訊，請參閱 < [Azure Cosmos 容器和資料庫上的佈建輸送量](set-throughput.md)。

如果您佈建資料庫層級的輸送量，就必須建立該資料庫中建立的所有集合資料分割/分區索引鍵。 如需有關資料分割的詳細資訊，請參閱 <<c0> [ 的資料分割與水平調整 Azure Cosmos DB 中](partition-data.md)。 如果您未指定資料分割/分區化索引鍵在移轉期間，Azure 資料庫移轉服務會自動填入的分區索引鍵欄位，具有 *_id*每份文件時，會自動產生的屬性。

### <a name="optimal-number-of-request-units-rus-to-provision"></a>要佈建要求單位 (Ru) 的最佳數目

在 Azure Cosmos DB 中，已預先佈建輸送量，並以每秒要求單位 (RU)。 如果您有 VM 或內部部署執行 MongoDB 的工作負載，將 RU 的視為簡單的抽象的實體資源，例如大小的 VM 或內部部署上的伺服器和資源他們擁有，例如記憶體、 CPU、 IOPs。 

不同於 Vm 或內部部署伺服器，Ru 皆可輕鬆地相應增加和減少在任何時間。 您可以變更的已佈建的 Ru 數秒內，並向您收費只會針對給定的一小時期間內您佈建的 Ru 數目上限。 如需詳細資訊，請參閱 [Azure Cosmos DB 中的要求單位](request-units.md)。

以下是會影響需要的 Ru 數目的關鍵因素：
- **項目 （亦即，文件） 大小**:當項目/文件大小增加時的 Ru 數會使用讀取或寫入項目/文件也會增加。
- **項目屬性計數**：假設[預設索引編製](index-overview.md)在所有的屬性的 Ru 數耗用來寫入項目會隨著項目屬性計數增加而增加。 您可以減少寫入作業的要求單位耗用量[限制數目的索引屬性](index-policy.md)。
- **並行作業**:要求所耗用的單位也取決於哪些不同的 CRUD 作業 （例如寫入、 讀取、 更新、 刪除） 的頻率和執行更複雜的查詢。 您可以使用[mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/)輸出目前的 MongoDB 資料的並行需求。
- **查詢模式**：查詢的複雜性會影響查詢所耗用多少要求單位。

如果您將使用的 JSON 檔案的匯出[mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/)了解多少寫入、 讀取、 更新和刪除每秒所發生，您可以使用[Azure Cosmos DB 容量規劃工具](https://www.documentdb.com/capacityplanner)來估計初始佈建的 Ru 數目。 容量規劃工具不會不納入考量的更複雜的查詢成本。 因此，如果您有複雜的查詢資料時，將會耗用額外的 Ru。 計算機也會假設所有欄位編製都索引，且會使用工作階段一致性。 若要了解之查詢的成本是將您的資料 （或範例資料） 移轉至 Azure Cosmos DB 的最佳方式[連線到 Cosmos DB 的端點](connect-mongodb-account.md)並執行範例查詢從 MongoDB 殼層使用`getLastRequestStastistics`命令，以取得要求費用，會將輸出的取用的 Ru 數目：

`db.runCommand({getLastRequestStatistics: 1})`

此命令會輸出類似下列的 JSON 文件：

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

您了解查詢所取用的 Ru 數目，以及該查詢的並行需求之後，您可以調整佈建的 Ru 數目。 最佳化 Ru 不是一次性事件-您應該持續最佳化，或相應增加 Ru 佈建，取決於您不是需要大量的流量，而不是工作負載過重或匯入資料。

## <a id="partitioning"></a>選擇您的資料分割索引鍵
資料分割是移轉至像是 Azure Cosmos DB 全域散發的資料庫之前需要考量的重點。 Azure Cosmos DB 使用資料分割中的資料庫，以符合您的應用程式的延展性和效能需求調整個別的容器。 在分割中，在容器中的項目分成稱為 「 邏輯磁碟分割的不同子集。 如需詳細資訊和建議選擇正確的資料分割索引鍵，為您的資料，請參閱[選擇 資料分割索引鍵區段](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)。 

## <a id="indexing"></a>您的資料編製索引
根據預設，Azure Cosmos DB 編製索引時擷取您所有的資料欄位。 您可以修改[編製索引原則](index-policy.md)在任何時間的 Azure Cosmos DB 中。 事實上，通常建議以關閉編製索引的資料，在移轉時，並再重新開啟它的資料時在 Cosmos DB 中。 如需編製索引的詳細資訊，您可以深入了解在[Azure Cosmos DB 中編製索引](index-overview.md)一節。 

[Azure 資料庫移轉服務](../dms/tutorial-mongodb-cosmos-db.md)自動移轉具有唯一索引的 MongoDB 集合。 不過，在移轉之前，必須建立唯一的索引。 Azure Cosmos DB 不支援建立唯一的索引，當您的集合中已經有資料。 如需詳細資訊，請參閱 [Azure Cosmos DB 中的唯一索引鍵](unique-keys.md)。

## <a name="next-steps"></a>後續步驟
* [將 MongoDB 資料移轉到使用資料庫移轉服務的 Cosmos DB。](../dms/tutorial-mongodb-cosmos-db.md) 
* [Azure Cosmos 容器和資料庫上的佈建輸送量](set-throughput.md)
* [Azure Cosmos DB 中的資料分割](partition-data.md)
* [Azure Cosmos DB 中的全域散發](distribute-data-globally.md)
* [Azure Cosmos DB 中的編製索引](index-overview.md)
* [Azure Cosmos DB 中的要求單位](request-units.md)
