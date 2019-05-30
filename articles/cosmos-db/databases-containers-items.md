---
title: 使用資料庫、 容器和 Azure Cosmos DB 中的項目
description: 本文說明如何建立和使用 Azure Cosmos DB 中的資料庫、 容器和項目。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 574dd9fd6189b6d0f1e5d455146d6d083ad7ff77
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389474"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>使用資料庫、 容器和 Azure Cosmos DB 中的項目

建立 [Azure Cosmos DB 帳戶](account-overview.md) 之後，您就可以在 Azure 訂用帳戶底下，透過建立資料庫、容器和項目來管理帳戶中的資料。 本文說明每個實體。 

下圖顯示 Azure Cosmos DB 帳戶中的不同實體的階層：

![Azure Cosmos 帳戶項目](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Azure Cosmos 資料庫

您可以在您的帳戶下建立一或多個 Azure Cosmos 資料庫。 資料庫類似於命名空間， 資料庫是管理的一組的 Azure Cosmos 容器單位。 下表說明 Azure Cosmos 資料庫如何對應至各種 API 特定實體：

| Azure Cosmos 實體 | SQL API | Cassandra API | 適用於 MongoDB 的 Azure Cosmos DB API | Gremlin API | 資料表 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 資料庫 | 資料庫 | keyspace | 資料庫 | 資料庫 | NA |

> [!NOTE]
> 使用資料表 API 帳戶，當您建立第一個資料表時，預設的資料庫會自動建立 Azure Cosmos 帳戶中。

### <a name="operations-on-an-azure-cosmos-database"></a>Azure Cosmos 資料庫中的作業

下表中所述，您可以使用 Azure Cosmos 資料庫與 Azure Cosmos Api 進行互動：

| 作業 | Azure CLI | SQL API | Cassandra API | 適用於 MongoDB 的 Azure Cosmos DB API | Gremlin API | 資料表 API |
| --- | --- | --- | --- | --- | --- | --- |
|列舉所有資料庫| 是 | 是 | 是 (資料庫會對應至 keyspace) | 是 | NA | NA |
|讀取資料庫| 是 | 是 | 是 (資料庫會對應至 keyspace) | 是 | NA | NA |
|Create new database| 是 | 是 | 是 (資料庫會對應至 keyspace) | 是 | NA | NA |
|更新資料庫| 是 | 是 | 是 (資料庫會對應至 keyspace) | 是 | NA | NA |


## <a name="azure-cosmos-containers"></a>Azure Cosmos 容器

Azure Cosmos 容器是延展性的針對佈建的輸送量和儲存體單位。 容器會以水平方式分割，然後跨多個區域複寫。 您新增至容器的項目與您在容器上佈建的輸送量，會根據分割區索引鍵自動分配到一組邏輯分割區。 若要深入了解資料分割和資料分割索引鍵，請參閱[分割資料](partition-data.md)。 

當您建立 Azure Cosmos 容器時，您會設定輸送量，其中一種模式如下：

* **專用佈建輸送量模式**:在容器上佈建的輸送量是特別針對該容器保留的，而且由 SLA 支持。 若要進一步了解，請參閱[如何在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。

* **共用的佈建的輸送量模式**:這些容器會分享相同的資料庫 （不包括已設定專用的佈建輸送量的容器） 中的其他容器中的佈建的輸送量。 換句話說，資料庫上已佈建的輸送量會由所有「共用輸送量」容器共用。 若要進一步了解，請參閱[如何將 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。

> [!NOTE]
> 只有在建立資料庫和容器時，您可以設定共用和專用的輸送量。 若要建立容器之後，請切換從專用的輸送量模式為共用的輸送量模式 （反之亦然），您必須建立新的容器，並將資料移轉到新的容器。 您可以使用 Azure Cosmos DB 變更摘要 」 功能來移轉資料。

不論您是使用專用或共用的佈建的輸送量模式建立容器時，可以彈性調整，調整 Azure Cosmos 容器。

Azure Cosmos 容器是與結構描述無關的項目容器。 在容器中的項目可以有任意結構描述。 例如，在放置代表個人的項目和項目，表示汽車*相同的容器*。 根據預設，系統會自動為您新增至容器的所有項目編製索引，您不需要進行任何明確的索引或結構描述管理。 您可以透過設定容器上的[索引編製原則](index-overview.md)以自定義索引編製行為。 

您可以設定[時間 (TTL)](time-to-live.md)上選取的項目，在 Azure Cosmos 容器，或依正常程序從系統清除這些項目之整個容器。 過期時，azure Cosmos DB 會自動刪除的項目。 它也會保證在容器上執行的查詢不會傳回固定的繫結中過期的項目。 若要深入了解，請參閱[在您的容器上設定 TTL](how-to-time-to-live.md)。

您可以使用[變更摘要](change-feed.md)訂閱會替您容器的每個邏輯分割區的作業記錄檔。 變更摘要會提供與容器上, 執行的所有更新的記錄檔之前和之後的項目。 如需詳細資訊，請參閱 <<c0> [ 建立回應式應用程式，使用變更摘要](serverless-computing-database.md)。 您也可以設定的保留持續時間，變更摘要使用變更摘要在容器上的原則。 

您可以註冊[預存程序、 觸發程序，使用者定義函數 (Udf)](stored-procedures-triggers-udfs.md)，並[合併程序](how-to-manage-conflicts.md)為您的 Azure Cosmos 容器。 

您可以在您的 Azure Cosmos 容器上指定[唯一索引鍵值條件約束](unique-keys.md)。 透過建立唯一索引鍵原則，您可以確保每個邏輯分割區索引鍵中一或多個值的唯一性。 如果使用唯一索引鍵原則建立容器，且新增或更新之項目的值與唯一索引鍵條件約束所指定的值重複，則無法建立該新增或已更新的項目。 若要深入了解，請參閱[唯一索引鍵條件約束](unique-keys.md)。

下表所示，Azure Cosmos 容器特製化成特定 API 的實體：

| Azure Cosmos 實體 | SQL API | Cassandra API | 適用於 MongoDB 的 Azure Cosmos DB API | Gremlin API | 資料表 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 容器 | 集合 | 資料表 | 集合 | 圖形 | 資料表 |

### <a name="properties-of-an-azure-cosmos-container"></a>Azure Cosmos 容器的屬性

Azure Cosmos 容器具備一組系統定義的屬性。 視您使用的 API 而定，某些屬性可能不會直接公開。 下表說明系統定義屬性清單：

| 系統定義的屬性 | 系統產生或使用者可設定 | 目的 | SQL API | Cassandra API | 適用於 MongoDB 的 Azure Cosmos DB API | Gremlin API | 資料表 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | System-generated | 容器的唯一識別碼 | 是 | 否 | 否 | 否 | 否 |
|\_etag | System-generated | 適用於開放式並行存取控制的實體標記 | 是 | 否 | 否 | 否 | 否 |
|\_ts | System-generated | 容器的上次更新日期時間戳記 | 是 | 否 | 否 | 否 | 否 |
|\_self | System-generated | 容器的可定址 URI | 是 | 否 | 否 | 否 | 否 |
|id | 使用者可設定 | 使用者定義的容器唯一名稱 | 是 | 是 | 是 | 是 | 是 |
|indexingPolicy | 使用者可設定 | 讓您能夠變更的索引路徑、 索引類型和索引模式 | 是 | 否 | 否 | 否 | 是 |
|TimeToLive | 使用者可設定 | 讓您能夠在一段時間過後刪除自動從容器項目。 如需詳細資訊，請參閱 <<c0> [ 存留時間](time-to-live.md)。 | 是 | 否 | 否 | 否 | 是 |
|changeFeedPolicy | 使用者可設定 | 用來讀取對容器中之項目所做的變更。 如需詳細資訊，請參閱 <<c0> [ 變更摘要](change-feed.md)。 | 是 | 否 | 否 | 否 | 是 |
|uniqueKeyPolicy | 使用者可設定 | 用來確保邏輯資料分割中的一或多個值的唯一性。 如需詳細資訊，請參閱 <<c0> [ 唯一的索引鍵條件約束](unique-keys.md)。 | 是 | 否 | 否 | 否 | 是 |

### <a name="operations-on-an-azure-cosmos-container"></a>Azure Cosmos 容器中的作業

當您使用任一 Azure Cosmos Api 時，Azure Cosmos 容器支援下列作業：

| 作業 | Azure CLI | SQL API | Cassandra API | 適用於 MongoDB 的 Azure Cosmos DB API | Gremlin API | 資料表 API |
| --- | --- | --- | --- | --- | --- | --- |
| 列舉資料庫中的容器 | 是 | 是 | 是 | 是 | NA | NA |
| 讀取容器 | 是 | 是 | 是 | 是 | NA | NA |
| 建立新的容器 | 是 | 是 | 是 | 是 | NA | NA |
| 更新容器 | 是 | 是 | 是 | 是 | NA | NA |
| 刪除容器 | 是 | 是 | 是 | 是 | NA | NA |

## <a name="azure-cosmos-items"></a>Azure Cosmos 項目

根據您所使用的 API，Azure Cosmos 項目可以代表集合中的資料表，或節點的資料列中的文件，或是圖形中的邊緣。 下表顯示對應的 API 特定實體到 Azure Cosmos 項目：

| Cosmos 實體 | SQL API | Cassandra API | 適用於 MongoDB 的 Azure Cosmos DB API | Gremlin API | 資料表 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 項目 | 文件 | 資料列 | 文件 | 節點或邊緣 | Item |

### <a name="properties-of-an-item"></a>項目的屬性

每個 Azure Cosmos 項目具有下列系統定義屬性。 根據您所使用的 API，其中部分可能不會直接公開。

| 系統定義的屬性 | 系統產生或使用者可設定| 目的 | SQL API | Cassandra API | 適用於 MongoDB 的 Azure Cosmos DB API | Gremlin API | 資料表 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | System-generated | 項目的唯一識別碼 | 是 | 否 | 否 | 否 | 否 |
|\_etag | System-generated | 適用於開放式並行存取控制的實體標記 | 是 | 否 | 否 | 否 | 否 |
|\_ts | System-generated | 項目上次更新時間戳記 | 是 | 否 | 否 | 否 | 否 |
|\_self | System-generated | 項目的可定址 URI | 是 | 否 | 否 | 否 | 否 |
|id | 無論是 | 使用者定義唯一的名稱中的邏輯磁碟分割。 如果使用者未指定識別碼，系統會自動產生一個。 | 是 | 是 | 是 | 是 | 是 |
|任意使用者定義的屬性 | 使用者定義 | 使用者定義的屬性 （包括 JSON、 BSON 和 CQL） 的 API-原生表示法中表示 | 是 | 是 | 是 | 是 | 是 |

### <a name="operations-on-items"></a>項目上的作業

Azure Cosmos 項目支援下列作業。 您可以使用任一 Azure Cosmos Api 來執行作業。

| 作業 | Azure CLI | SQL API | Cassandra API | 適用於 MongoDB 的 Azure Cosmos DB API | Gremlin API | 資料表 API |
| --- | --- | --- | --- | --- | --- | --- |
| 插入、取代、刪除、更新插入、讀取 | 否 | yes | 是 | 是 | 是 | 是 |

## <a name="next-steps"></a>後續步驟

深入了解這些工作與概念：

* [Azure Cosmos 資料庫上的佈建輸送量](how-to-provision-database-throughput.md)
* [在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)
* [使用邏輯磁碟分割](partition-data.md)
* [在 Azure Cosmos 容器上設定 TTL](how-to-time-to-live.md)
* [使用變更摘要建置回應式應用程式](change-feed.md)
* [設定您的 Azure Cosmos 容器的唯一索引鍵條件約束](unique-keys.md)
