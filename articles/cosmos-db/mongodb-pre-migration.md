---
title: 將資料移轉至 Azure Cosmos DB 的 MongoDB 版 API 的預先遷移步驟
description: 本檔概要說明從 MongoDB 到 Cosmos DB 的資料移轉的必要條件。
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 4dc7038d0ff5180f15a43268fd3f3aa0cbb0c7a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445205"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>將資料從 MongoDB 遷移至 Azure Cosmos DB 的 MongoDB 版 API 的預先遷移步驟

在您將資料從 MongoDB （內部部署或雲端（IaaS））遷移至適用于 MongoDB Azure Cosmos DB 的 API 之前，您應該：

1. [建立 Azure Cosmos DB 帳戶](#create-account)
2. [估計您的工作負載所需的輸送量](#estimate-throughput)
3. [為您的資料選擇最佳的分割區索引鍵](#partitioning)
4. [瞭解您可以在資料上設定的編制索引原則](#indexing)

如果您已完成上述的遷移必要條件，請參閱將[mongodb 資料移轉至適用于 MongoDB 的 AZURE COSMOS DB API](../dms/tutorial-mongodb-cosmos-db.md) ，以取得實際的資料移轉指示。 如果沒有，本檔提供處理這些必要條件的指示。 

## <a id="create-account"></a>建立 Azure Cosmos DB 帳戶 

開始進行遷移之前，您必須[使用 Azure Cosmos DB 適用于 MongoDB 的 API 來建立 Azure Cosmos 帳戶](create-mongodb-dotnet.md)。 

建立帳戶時，您可以選擇 [設定] 以[全域散發](distribute-data-globally.md)您的資料。 您也可以選擇啟用多重區域寫入（或多宿主設定），讓每個區域都是寫入和讀取區域。

![帳戶-建立](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a>估計您的工作負載所需的輸送量

使用[資料庫移轉服務（DMS）](../dms/dms-overview.md)開始進行遷移之前，您應該先估計要為您的 Azure Cosmos 資料庫和集合布建的輸送量數量。

輸送量可以在下列其中一種方式布建：

- 集合

- 資料庫

> [!NOTE]
> 您也可以結合上述專案，其中，資料庫中的某些集合可能會有專用的布建輸送量，而其他則可能會共用輸送量。 如需詳細資訊，請參閱在[資料庫和容器上設定輸送量](set-throughput.md)頁面。
>

您應該先決定要布建資料庫或集合層級輸送量，或兩者的組合。 一般來說，建議您在集合層級設定專用的輸送量。 在資料庫層級布建輸送量可讓您資料庫中的集合共用布建的輸送量。 不過，使用共用的輸送量，並不保證每個個別集合的特定輸送量，而且您也不會獲得任何特定集合的可預測效能。

如果您不確定每個個別集合應專用多少輸送量，您可以選擇資料庫層級的輸送量。 您可以將 Azure Cosmos 資料庫上設定的布建輸送量視為邏輯上相當於 MongoDB VM 或實體伺服器的計算容量，但更符合成本效益的彈性擴充能力。 如需詳細資訊，請參閱[在 Azure Cosmos 容器和資料庫上](set-throughput.md)布建輸送量。

如果您在資料庫層級布建輸送量，則在該資料庫內建立的所有集合都必須使用 partition/分區索引鍵來建立。 如需資料分割的詳細資訊，請參閱[Azure Cosmos DB 中的資料分割和水準調整](partition-data.md)。 如果您未在遷移期間指定資料分割/分區金鑰，Azure 資料庫移轉服務會自動將每份檔所自動產生的 *_id*屬性填入 [分區金鑰] 欄位。

### <a name="optimal-number-of-request-units-rus-to-provision"></a>要布建的最佳要求單位（ru）數目

在 Azure Cosmos DB 中，會事先布建輸送量，並以每秒的要求單位（RU）來測量。 如果您的工作負載在 VM 或內部部署上執行 MongoDB，請將 RU 視為實體資源的簡單抽象概念，例如 VM 或內部部署伺服器的大小，以及所擁有的資源，例如記憶體、CPU、IOPs。 

不同于 Vm 或內部部署伺服器，ru 很容易就能隨時相應增加和減少。 您可以在數秒內變更已布建的 ru 數目，而且只需支付您在指定的一小時期間內所布建的 ru 數目上限。 如需詳細資訊，請參閱 [Azure Cosmos DB 中的要求單位](request-units.md)。

以下是影響必要 ru 數目的重要因素：
- **專案（例如檔）大小**：隨著專案/檔的大小增加，取用來讀取或寫入專案/檔的 ru 數目也會增加。
- **專案屬性計數**：假設所有屬性都有[預設的索引編制](index-overview.md)，則取用來寫入專案的 ru 數目會隨著專案屬性計數增加而增加。 您可以[限制已編制索引的屬性數目](index-policy.md)，以減少寫入作業的要求單位耗用量。
- **並行作業**：取用的要求單位也取決於執行不同 CRUD 作業（例如寫入、讀取、更新、刪除）和更複雜查詢的頻率。 您可以使用[mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/)來輸出目前 MongoDB 資料的並行需求。
- **查詢模式**：查詢的複雜性會影響查詢耗用多少要求單位。

如果您使用[mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/)匯出 JSON 檔案，並瞭解每秒發生的寫入、讀取、更新和刪除次數，您可以使用[Azure Cosmos DB 容量規劃](https://www.documentdb.com/capacityplanner)工具來估計要布建的初始 ru 數目。 容量規劃不會考慮更複雜的查詢成本。 因此，如果您對資料有複雜的查詢，將會耗用額外的 ru。 計算機也假設所有欄位都已編制索引，並使用會話一致性。 瞭解查詢成本的最佳方式是將您的資料（或範例資料）遷移至 Azure Cosmos DB、[連接至 Cosmos DB 的端點](connect-mongodb-account.md)，並使用 `getLastRequestStastistics` 命令從 MongoDB Shell 執行範例查詢，以取得要求費用，這會輸出取用的 ru 數目：

`db.runCommand({getLastRequestStatistics: 1})`

此命令會輸出類似下列的 JSON 檔：

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

瞭解查詢所耗用的 ru 數目和該查詢的並行需求之後，您可以調整已布建的 ru 數目。 優化 ru 不是一次性事件-根據您是否預期不會有大量的流量，而不是繁重的工作負載或匯入資料，您應該持續優化或相應增加布建的 ru。

## <a id="partitioning"></a>選擇分割區索引鍵
分割是在遷移至全域散發的資料庫（如 Azure Cosmos DB）之前的重要考慮點。 Azure Cosmos DB 使用資料分割來調整資料庫中的個別容器，以符合您應用程式的擴充性和效能需求。 在資料分割中，容器中的專案會分割成不同的子集，稱為邏輯分割區。 如需為您的資料選擇正確的分割區索引鍵的詳細資訊和建議，請參閱[選擇分割區索引鍵一節](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)。 

## <a id="indexing"></a>為數據編制索引
根據預設，Azure Cosmos DB 會在內嵌時編制所有資料欄位的索引。 您可以隨時修改 Azure Cosmos DB 中的[編制索引原則](index-policy.md)。 事實上，通常建議您在遷移資料時關閉編制索引，然後在資料已經 Cosmos DB 時，再重新開啟它。 如需索引編制的詳細資訊，您可以在 Azure Cosmos DB 一節的[編制索引中](index-overview.md)閱讀更多詳細資料。 

[Azure 資料庫移轉服務](../dms/tutorial-mongodb-cosmos-db.md)會自動遷移具有唯一索引的 MongoDB 集合。 不過，必須先建立唯一索引，然後再進行遷移。 當您的集合中已經有資料時，Azure Cosmos DB 不支援建立唯一索引。 如需詳細資訊，請參閱 [Azure Cosmos DB 中的唯一索引鍵](unique-keys.md)。

## <a name="next-steps"></a>後續步驟
* [使用資料庫移轉服務，將 MongoDB 資料移轉至 Cosmos DB。](../dms/tutorial-mongodb-cosmos-db.md) 
* [在 Azure Cosmos 容器和資料庫上布建輸送量](set-throughput.md)
* [Azure Cosmos DB 中的資料分割](partition-data.md)
* [Azure Cosmos DB 中的全域散發](distribute-data-globally.md)
* [Azure Cosmos DB 中的編製索引](index-overview.md)
* [Azure Cosmos DB 中的要求單位](request-units.md)
