---
title: 使用 Azure Cosmos DB 資料庫、容器和項目
description: 本文說明如何建立及使用 Azure Cosmos DB 資料庫、容器和項目
author: dharmas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: d834b7f43d961400e2d5080a46cf921d719f3393
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684846"
---
# <a name="working-with-azure-cosmos-databases-containers-and-items"></a>使用 Azure Cosmos 資料庫、容器和項目

建立 [Azure Cosmos DB 帳戶](account-overview.md) 之後，您就可以在 Azure 訂用帳戶底下，透過建立資料庫、容器和項目來管理帳戶中的資料。 本文會逐一描述以下項目：資料庫、容器和項目。 下圖顯示 Azure Cosmos 帳戶中不同實體的階層：

![Azure Cosmos 帳戶項目](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Azure Cosmos 資料庫

您可以在自己的帳戶下建立一或多個 Azure Cosmos 資料庫。 資料庫相當於命名空間，它是一組 Azure Cosmos 容器的管理單位。 下表說明 Azure Cosmos 資料庫如何對應至各種 API 特定實體：

| **Azure Cosmos 實體** | **SQL API** | **Cassandra API** | **MongoDB API** | **Gremlin API** | **資料表 API** |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 資料庫 | 資料庫 | keyspace | 資料庫 | NA | NA |

> [!NOTE]
> 使用 Gremlin 和資料表 API 帳戶時，在您建立第一個圖形或資料表時，會在您的 Azure Cosmos 帳戶內自動建立一個預設資料庫。

### <a name="operations-on-an-azure-cosmos-database"></a>Azure Cosmos 資料庫中的作業

您可以使用以下 Azure Cosmos API 與 Azure Cosmos 資料庫互動：

| **作業** | **Azure CLI**|**SQL API** | **Cassandra API** | **MongoDB API** | **Gremlin API** | **資料表 API** |
| --- | --- | --- | --- | --- | --- | --- |
|列舉所有資料庫| 是 | 是 | 是 (資料庫會對應至 keyspace) | 是 | NA | NA |
|讀取資料庫| 是 | 是 | 是 (資料庫會對應至 keyspace) | 是 | NA | NA |
|Create new database| 是 | 是 | 是 (資料庫會對應至 keyspace) | 是 | NA | NA |
|更新資料庫| 是 | 是 | 是 (資料庫會對應至 keyspace) | 是 | NA | NA |


## <a name="azure-cosmos-containers"></a>Azure Cosmos 容器

Azure Cosmos 容器是適用於項目的已佈建輸送量和儲存體的延展性單位。 容器會以水平方式分割，然後跨多個區域複寫。 您新增至容器的項目和您在容器上佈建的輸送量，都會自動依據分割區索引鍵，跨一組邏輯分割區散佈。 若要深入了解分割和分割區索引鍵，請參閱[邏輯分割區](partition-data.md)文章。 

建立 Azure Cosmos 容器時，您可以透過以下其中一種方法設定輸送量：

* **專用佈建輸送量**模式︰容器上佈建的輸送量會專門保留給該容器使用，且會以 SLA 作為後盾。 如需詳細資訊，請參閱[如何在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。

* **共用佈建輸送量**模式︰這些容器會與同一資料庫中其他容器 (不包括已經設定專用佈建輸送量的容器) 共用佈建的輸送量。 換句話說，所有共用的容器會共用資料庫中佈建的輸送量。 如需詳細資訊，請參閱[如何在 Azure Cosmos 資料庫上設定佈建輸送量](how-to-provision-database-throughput.md)。

無論使用「共用」或「專用」佈建輸送量模式建立容器，都可以彈性調整 Azure Cosmos 容器，也就是它們可以具有無限制的儲存體和佈建的輸送量。  

Azure Cosmos 容器是與結構描述無關的項目容器。 容器內的項目可以有任意的結構描述。 例如，表示人員的項目和表示汽車的項目，都可以放在相同的容器中。 根據預設，您新增至容器的所有項目都會自動編製索引，不需要執行任何明確的索引或結構描述管理作業。 您可以藉由在容器上設定索引編製原則，即可自訂編製索引行為。 

您可以在於 Azure Cosmos 容器內選取的項目上，或在整個容器上設定存留時間 (TTL)，即可優雅地將這些項目從系統中清除。 當項目過期時，Azure Cosmos DB 將會自動將它們刪除。 它也可以保證在容器中執行的查詢不會傳回固定繫結內過期的項目。 若要進一步了解，請參閱[如何在容器上設定 TTL](how-to-time-to-live.md)。

您可以藉由使用變更摘要，訂閱針對容器的每個邏輯分割區管理的作業記錄檔。 變更摘要會提供在容器上執行之所有更新的記錄檔，以及項目的之前和之後映像。 請參閱[如何使用變更摘要建置回應式應用程式](change-feed.md)。 您也可以使用容器上的變更摘要原則，設定變更摘要的保留期持續期間。 

您可以利用 Azure Cosmos 容器註冊預存程序、觸發程序、使用者定義函數 (UDF) 和合併程序。 

您可以在您的 Azure Cosmos 容器中指定唯一索引鍵。 您可以建立唯一索引鍵原則，以確保每個邏輯分割區索引鍵一或多個值的唯一性。 一旦使用唯一索引鍵原則建立容器，即可防止使用與唯一索引鍵條件約束所指定值重複的值，建立任何新的或已更新的項目。 若要進一步了解，請參閱[唯一索引鍵條件約束](unique-keys.md)。

Azure Cosmos 容器會特製化成 API 特定實體，如下所示：

| **Azure Cosmos 實體** | **SQL API** | **Cassandra API** | **MongoDB API** | **Gremlin API** | **資料表 API** |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 容器 | 容器 | 資料表 | 集合 | 圖形 | 資料表 |

### <a name="properties-of-an-azure-cosmos-container"></a>Azure Cosmos 容器的屬性

Azure Cosmos 容器都有一組系統定義的屬性。 根據選擇的 API 不同，其中部分屬性可能不會直接公開。 下表描述所支援系統定義屬性的清單：

| **系統定義屬性** | **系統產生或可由使用者設定** | **用途** | **SQL API** | **Cassandra API** | **MongoDB API** | **Gremlin API** | **資料表 API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|__rid | 由系統產生 | 容器的唯一識別碼 | 是 | 否 | 否 | 否 | 否 |
|__etag | 由系統產生 | 適用於開放式並行存取控制的實體標記 | 是 | 否 | 否 | 否 | 否 |
|__ts | 由系統產生 | 容器的上次更新日期時間戳記 | 是 | 否 | 否 | 否 | 否 |
|__self | 由系統產生 | 容器的可定址 URI | 是 | 否 | 否 | 否 | 否 |
|id | 可由使用者設定 | 使用者定義的容器唯一名稱 | 是 | 是 | 是 | 是 | 是 |
|indexingPolicy | 可由使用者設定 | 提供變更的索引路徑、其精確度，以及一致性模型的能力。 | 是 | 否 | 否 | 否 | 是 |
|TimeToLive | 可由使用者設定 | 提供在一段時間後自動從容器中刪除項目的能力。 如需詳細資訊，請參閱[存留時間](time-to-live.md)文章。 | 是 | 否 | 否 | 否 | 是 |
|changeFeedPolicy | 可由使用者設定 | 用來讀取容器中對項目所做的變更。 如需詳細資訊，請參閱[變更摘要](change-feed.md)文章。 | 是 | 否 | 否 | 否 | 是 |
|uniqueKeyPolicy | 可由使用者設定 | 利用唯一索引鍵，您就能確保邏輯分割區內一或多個值的唯一性。 如需詳細資訊，請參閱[唯一索引鍵](unique-keys.md)一文。 | 是 | 否 | 否 | 否 | 是 |

### <a name="operations-on-an-azure-cosmos-container"></a>Azure Cosmos 容器中的作業

Azure Cosmos 容器支援下列使用任一 Azure Cosmos API 的作業。

| **作業** | **Azure CLI** | **SQL API** | **Cassandra API** | **MongoDB API** | **Gremlin API** | **資料表 API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 列舉資料庫中的容器 | 是* | 是 | 是 | 是 | NA | NA |
| 讀取容器 | 是 | 是 | 是 | 是 | NA | NA |
| 建立新容器 | 是 | 是 | 是 | 是 | NA | NA |
| 更新容器 | 是 | 是 | 是 | 是 | NA | NA |
| 刪除容器 | 是 | 是 | 是 | 是 | NA | NA |

## <a name="azure-cosmos-items"></a>Azure Cosmos 項目

Azure Cosmos 項目可以根據選擇的 API，分別代表集合中的文件、資料表中的資料列，或圖形中的節點/邊緣。 下表顯示 API 特定實體與 Azure Cosmos 項目之間的對應：

| **Cosmos 實體** | **SQL API** | **Cassandra API** | **MongoDB API** | **Gremlin API** | **資料表 API** |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 項目 | Item | 資料列 | 文件 | 節點或邊緣 | Item |

### <a name="properties-of-an-item"></a>項目的屬性

每個 Azure Cosmos 項目都有以下系統定義屬性。 根據選擇的 API 不同，其中部分屬性可能不會直接公開。

|**系統定義屬性** | **系統產生或可由使用者設定**| **用途** | **SQL API** | **Cassandra API** | **MongoDB API** | **Gremlin API** | **資料表 API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|__id | 由系統產生 | 項目的唯一識別碼 | 是 | 否 | 否 | 否 | 否 |
|__etag | 由系統產生 | 適用於開放式並行存取控制的實體標記 | 是 | 否 | 否 | 否 | 否 |
|__ts | 由系統產生 | 項目的上次更新日期時間戳記 | 是 | 否 | 否 | 否 | 否 |
|__self | 由系統產生 | 項目的可定址 URI | 是 | 否 | 否 | 否 | 否 |
|id | 無論是 | 邏輯分割區內使用者定義的唯一名稱。 如果使用者未指定識別碼，系統會自動產生一個。 | 是 | 是 | 是 | 是 | 是 |
|任意使用者定義的屬性 | 使用者定義 | 以 API 原生表示法 (JSON、BSON，CQL 等等) 表示的使用者定義屬性 | 是 | 是 | 是 | 是 | 是 |

### <a name="operations-on-items"></a>項目上的作業

Azure Cosmos 項目支援下列可使用任一 Azure Cosmos API 執行的作業。

| **作業** | **Azure CLI** | **SQL API** | **Cassandra API** | **MongoDB API** | **Gremlin API** | **資料表 API** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 插入、取代、刪除、更新插入、讀取 | 否 | yes | 是 | 是 | 是 | 是 |

## <a name="next-steps"></a>後續步驟

您現在可以繼續了解如何在 Azure Cosmos 帳戶上佈建輸送量，或查看其他概念：

* [如何在 Azure Cosmos 資料庫上設定佈建輸送量](how-to-provision-database-throughput.md)
* [如何在 Azure Cosmos 容器上設定佈建輸送量](how-to-provision-container-throughput.md)
* [邏輯分割區](partition-data.md)
* [如何在 Azure Cosmos 容器上設定 TTL](how-to-time-to-live.md)
* [如何使用變更摘要建置回應式應用程式](change-feed.md)
* [如何在 Azure Cosmos 容器上設定唯一索引鍵條件約束](unique-keys.md)
