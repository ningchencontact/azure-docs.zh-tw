---
title: Azure Cosmos DB 服務配額
description: Azure Cosmos DB 服務配額，以及在不同的資源類型上的預設限制。
author: arramac
ms.author: arramac
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 74df0038676e8459028084890da569ed3b75a682
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797294"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure Cosmos DB 服務配額

這篇文章概述 Azure Cosmos DB 中的不同資源所提供的預設配額。

## <a name="storage-and-throughput"></a>儲存體和輸送量

您的訂用帳戶下建立 Azure Cosmos 帳戶之後，您可以管理資料在您的帳戶所[建立資料庫、 容器和項目](databases-containers-items.md)。 您可以佈建在容器層級或資料庫層級的輸送量[的要求單位 （RU/秒或 Ru）](request-units.md)。 下表列出儲存體和輸送量，每個容器/資料庫的限制。

| Resource | 預設限制 |
| --- | --- |
| 每個容器的最大 Ru ([專用的輸送量佈建的模式](databases-containers-items.md#azure-cosmos-containers)) | 根據預設 1,000,000。 您可以增加所[提出 Azure 支援票證](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)或連絡我們透過[詢問 Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| 每個資料庫的最大 Ru ([共用的輸送量佈建的模式](databases-containers-items.md#azure-cosmos-containers)) | 根據預設 1,000,000。 您可以增加所[提出 Azure 支援票證](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)或連絡我們透過[詢問 Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| 每個 （邏輯） 的資料分割索引鍵的最大 Ru | 10,000 |
| 每個 （邏輯） 的資料分割索引鍵的所有項目之間的最大儲存體| 10 GB |
| 相異 （邏輯） 的資料分割索引鍵的最大數目 | 無限 |
| 每個容器的最大儲存體 | 無限 |
| 每個資料庫的最大儲存體 | 無限 |

> [!NOTE]
> 如需管理有需要較高限制的儲存體或輸送量的資料分割索引鍵的工作負載的最佳作法，請參閱[熱資料分割索引鍵設計](synthetic-partition-keys.md)
>

Cosmos 容器 （或共用的輸送量資料庫） 必須具有最少 400 Ru 的輸送量。 隨著容器時，最小支援的輸送量也會取決於下列因素：

* 以往在容器上佈建的輸送量上限。 此服務支援容器以佈建最多 10%的降低的輸送量。 例如，如果您的輸送量已增加到 10000 Ru，則最低可能佈建的輸送量會是 1000 Ru
* 在每個容器的 100 個 Ru，測量您曾經在共用的輸送量資料庫中，建立容器的總數。 比方說，如果您已經建立共用的輸送量資料庫內的五個容器，則輸送量必須至少 500 Ru

從 Azure 入口網站或 Sdk，可以擷取容器或資料庫的目前和最小輸送量。 如需詳細資訊，請參閱 <<c0> [ 容器和資料庫上的佈建輸送量](set-throughput.md)。 總而言之，以下是最小已佈建的 RU 限制。 

| Resource | 預設限制 |
| --- | --- |
| 每個容器的最小 Ru ([專用的輸送量佈建的模式](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| 每個資料庫最小的 Ru ([共用的輸送量佈建的模式](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| 最小的 Ru，每個共用的輸送量資料庫內的容器 | 100 |

Cosmos DB 支援彈性調整輸送量 (Ru) 的每個容器或透過 Sdk 或入口網站的資料庫。 每個容器可以調整以同步和立即 10 到 100 倍，最小和最大值之間的縮放範圍內。 如果要求的輸送量值超出範圍時，縮放比例會以非同步方式執行。 非同步的調整可能需要分鐘小時才能完成取決於容器中的資料儲存體大小與要求的輸送量。  

## <a name="control-plane-operations"></a>透過 azure resource manager

您可以[佈建和管理您的 Azure Cosmos 帳戶](how-to-manage-database-account.md)使用 Azure 入口網站、 Azure PowerShell、 Azure CLI 和 Azure Resource Manager 範本。 下表列出每個訂用帳戶、 帳戶和作業數目的限制。

| Resource | 預設限制 |
| --- | --- |
| 每個訂閱的最大的資料庫帳戶 | 預設值 50。 您可以增加所[提出 Azure 支援票證](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)或連絡我們透過[詢問 Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| 區域性容錯移轉的最大數目 | 預設的 1/小時。 您可以增加所[提出 Azure 支援票證](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)或連絡我們透過[詢問 Cosmos DB](mailto:askcosmosdb@microsoft.com)|

> [!NOTE]
> 區域性容錯移轉只適用於單一區域寫入帳戶。 多重區域寫入帳戶不需要或有任何變更寫入區域的限制。

Cosmos DB 會自動備份您的資料一定的間隔。 如需備份的保留間隔和 windows 上的詳細資訊，請參閱 < [Azure Cosmos DB 中的線上備份和隨資料還原](online-backup-and-restore.md)。

## <a name="per-container-limits"></a>每個容器的限制

根據您所使用的 API，可以代表其中一個集合，資料表中，Azure Cosmos 容器，或將其圖形中。 容器支援的組態[唯一的索引鍵條件約束](unique-keys.md)，[預存程序、 觸發程序和 Udf](stored-procedures-triggers-udfs.md)，並[編製索引原則](how-to-manage-indexing-policy.md)。 下表列出特定設定的容器內的限制。 

| Resource | 預設限制 |
| --- | --- |
| 資料庫或容器名稱的長度上限 | 255 |
| 每個容器的最大預存程序 | 100 <sup>*</sup>|
| 每個容器的最大 Udf | 25 <sup>*</sup>|
| 路徑編製索引原則的最大數目| 100 <sup>*</sup>|
| 每個容器的唯一索引鍵的最大數目|10 <sup>*</sup>|
| 每個唯一的索引鍵條件約束的路徑數目上限|16 <sup>*</sup>|

<sup>*</sup> 您可以透過連絡 Azure 支援服務，或連絡我們透過增加任何這些每個容器的限制[詢問 Cosmos DB](mailto:askcosmosdb@microsoft.com)。

## <a name="per-item-limits"></a>每個項目限制

根據您所使用的 API，Azure Cosmos 項目可以代表集合中的資料表，或節點的資料列中的文件，或是圖形中的邊緣。 下表顯示在 Cosmos DB 中的每個項目限制。 

| Resource | 預設限制 |
| --- | --- |
| 項目的大小上限 | 2 MB （utf-8 長度的 JSON 表示法） |
| 資料分割索引鍵值的最大長度 | 2048 個位元組 |
| 識別碼值的最大長度 | 1024 個位元組 |
| 每個項目屬性的最大數目 | 沒有實際限制 |
| 最大巢狀結構深度 | 沒有實際限制 |
| 屬性名稱的長度上限 | 沒有實際限制 |
| 屬性值的最大長度 | 沒有實際限制 |
| 字串屬性值的最大長度 | 沒有實際限制 |
| 數值的屬性值的最大長度 | IEEE754 雙精確度 64 位元 |

在項目承載，例如屬性和巢狀結構深度，除了資料分割索引鍵和識別碼值，以及整體的長度限制的數目沒有限制大小的 2 MB 的限制。 您可能要設定適用於容器的索引編製原則，以降低 RU 耗用量的大型或複雜的項目結構。 請參閱[模型 Cosmos DB 中的項目](how-to-model-partition-example.md)真實世界範例中，與模式，來管理大型的項目。

## <a name="per-request-limits"></a>每個要求限制

Cosmos DB 支援[CRUD 和查詢作業](https://docs.microsoft.com/rest/api/cosmos-db/)針對容器、 項目及資料庫等資源。  

| Resource | 預設限制 |
| --- | --- |
| 針對單一作業 （例如預存程序執行或單一查詢 頁面擷取） 的最大執行時間| 5 秒 |
| 最大要求大小 （預存程序，CRUD）| 2 MB |
| 回應大小上限 （例如，分頁查詢） | 4 MB |

一次作業，例如查詢達到執行逾時或回應的大小限制，它會傳回一頁結果和接續 token 給用戶端，繼續執行。 沒有任何單一查詢可跨頁面/接續執行持續時間的實際限制。

Cosmos DB 會使用 HMAC 來進行授權。 您可以使用其中一個主要金鑰，或[資源權杖](secure-access-to-data.md)容器等資源的更細緻的存取控制，資料分割索引鍵或項目。 下表列出在 Cosmos DB 中的授權權杖的限制。

| Resource | 預設限制 |
| --- | --- |
| 主要權杖的到期時間上限 | 15 分鐘  |
| 最小資源權杖的到期時間 | 10 分鐘  |
| 最大的資源權杖的到期時間 | 預設的 24 小時。 您可以增加所[提出 Azure 支援票證](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)或連絡我們透過[詢問 Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| 最大時鐘誤差的權杖授權| 15 分鐘 |

Cosmos DB 支援的觸發程序在寫入期間的執行。 此服務支援最多一個預先觸發程序和一個後置觸發程序每個寫入作業。 

## <a name="sql-query-limits"></a>SQL 查詢限制

Cosmos DB 支援查詢使用的項目[SQL](how-to-sql-query.md)。 下表描述在查詢陳述式，例如數量子句或查詢長度的限制。

| Resource | 預設限制 |
| --- | --- |
| SQL 查詢的最大長度| 256 KB <sup>*</sup>|
| 每個查詢的最大聯結| 5 <sup>*</sup>|
| 每個查詢的最大 ANDs| 2000 <sup>*</sup>|
| 每個查詢的最大 Or| 2000 <sup>*</sup>|
| 每個查詢的最大 Udf| 10 <sup>*</sup>|
| 在運算式中每個最大引數| 6000 <sup>*</sup>|
| 每個多邊形的最大點| 4096 <sup>*</sup>|

<sup>*</sup> 您可以透過連絡 Azure 支援服務，或連絡我們透過增加這些 SQL 查詢限制的任何[詢問 Cosmos DB](mailto:askcosmosdb@microsoft.com)。

## <a name="mongodb-api-specific-limits"></a>MongoDB API 特有的限制

Cosmos DB 支援 MongoDB 有線通訊協定，針對 MongoDB 所撰寫的應用程式。 您可以找到支援的命令，並在通訊協定版本[支援 MongoDB 功能和語法](mongodb-feature-support.md)。

下表列出特定 MongoDB 功能支援的限制。 Sql （核心） API 所述的其他服務限制也適用於 MongoDB 的 API。

| Resource | 預設限制 |
| --- | --- |
| MongoDB 查詢的記憶體大小上限 | 40 MB |
| MongoDB 作業執行時間上限| 30 秒 |

## <a name="try-cosmos-db-free-limits"></a>請嘗試免費 Cosmos DB 的限制

下表列出的限制[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)試用版。

| Resource | 預設限制 |
| --- | --- |
| 在試用期間 | 30 天 (可以是更新次數) |
| 最大的容器，每個訂用帳戶 (SQL、 Gremlin，資料表 API) | 1 |
| 最大的容器，每個訂用帳戶 (MongoDB API) | 3 |
| 每個容器的最大輸送量 | 5000 |
| 每個共用輸送量資料庫的最大輸送量 | 20000 |
| 每個帳戶的最大總儲存體 | 10 GB |

試用 Cosmos DB 支援全域散發中只有美國中部、 北歐和東南亞區域。 試用 Azure Cosmos DB 帳戶，無法建立 azure 支援票證。 不過，為現有的支援方案的訂閱者提供支援。

## <a name="next-steps"></a>後續步驟

深入的了解 Cosmos DB 的核心概念[全域散發](distribute-data-globally.md)並[分割](partitioning-overview.md)並[佈建的輸送量](request-units.md)。

透過下列其中一個快速入門開始使用 Azure Cosmos DB：

* [開始使用 Azure Cosmos DB SQL API](create-sql-api-dotnet.md)
* [開始使用適用於 MongoDB 的 Azure Cosmos DB API](create-mongodb-nodejs.md)
* [開始使用 Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md)
* [開始使用 Azure Cosmos DB Gremlin API](create-graph-dotnet.md)
* [開始使用 Azure Cosmos DB 資料表 API](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
