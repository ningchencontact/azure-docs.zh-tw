---
title: 從 Azure 監視器取得 Azure Cosmos DB 計量
description: 瞭解如何使用 Azure 入口網站從 Azure 監視器查看不同類別的 Azure Cosmos DB 計量。
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.openlocfilehash: 905eca99c137af2fd40a1243de8fabd15314477c
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973749"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>從 Azure 監視器監視和調試 Azure Cosmos DB 計量

您可以從 Azure 監視器 API 查看 Azure Cosmos DB 計量。 Azure 監視器提供數種方式來與計量互動，包括 Azure 入口網站、透過 REST API 加以存取，或使用 PowerShell 或 CLI 來查詢它們。 Azure Cosmos DB 計量是低延遲的數值，預設會以一分鐘的頻率收集，您也可以匯總這些計量。 這些計量能夠支援即時案例。  

本文說明您可以使用 Azure 入口網站從 Azure 監視器查看的不同 Azure Cosmos DB 計量。 如果您對常見使用案例感興趣，以及如何重新使用 Azure Cosmos DB 計量來分析和偵測這些問題，請參閱[在 Azure Cosmos DB 文章中使用計量進行監視和調試](use-metrics.md)。 您將使用其中一個現有的 Azure Cosmos 帳戶，並在資料庫、容器、區域、要求或作業層級上查看不同的計量。 因此，請確定您有包含範例資料的 Azure Cosmos 帳戶，並對該資料執行 CRUD 作業。

## <a name="view-metrics-from-azure-portal"></a>從 Azure 入口網站查看計量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 從左側導覽列選取 [**監視**]，然後選取 [**計量**]。

   ![Azure 監視器中的 [計量] 窗格](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. 從 [**計量**] 窗格 >**選取資源**> 選擇所需的**訂**用帳戶和**資源群組**。 針對 [**資源類型**]，選取 [ **Azure Cosmos DB 帳戶**] **，選擇其中**一個現有的 Azure Cosmos 帳戶，然後選取 [套用]。 

   ![選擇 Cosmos DB 帳戶來查看計量](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. 接下來，您可以從可用的計量清單中選取度量。 您可以選取 [要求單位]、[儲存體]、[延遲]、[可用性]、[Cassandra] 和其他的特定計量。 若要深入瞭解此清單中所有可用的計量，請參閱本文的[依據類別的計量](#metrics-by-category)一節。 在此範例中，讓我們選取 [**要求單位**] 和 [**平均**] 作為匯總值。 

   除了這些詳細資料之外，您也可以選取度量的**時間範圍**和**時間細微性**。 在 [最大值] 中，您可以查看過去30天的計量。  套用篩選之後，就會根據您的篩選器來顯示圖表。 您可以看到所選期間內每分鐘耗用的平均要求單位數。  

   ![從 Azure 入口網站選擇度量](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>將篩選新增至計量

您也可以篩選特定**CollectionName**、 **DatabaseName**、 **OperationType**、 **Region**和**StatusCode**所顯示的計量和圖表。 若要篩選計量，請選取 [**新增篩選**] 並選擇必要的屬性（例如**OperationType** ），然後選取 [**查詢**] 之類的值。 圖形接著會顯示所選期間內，查詢作業所耗用的要求單位。 不會記錄透過預存程式執行的作業，因此它們無法在 OperationType 度量下提供。

![新增篩選以選取度量資料細微性](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

您可以使用 [套用**分割**] 選項將計量分組。 例如，您可以將每個作業類型的 [要求單位] 分組，並一次查看所有作業的圖形，如下圖所示： 

![新增套用分割篩選](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>依類別分類的計量

### <a name="request-metrics"></a>要求計量
            
|度量（度量顯示名稱）|單位（匯總類型） |描述|維度| 時間資料粒度| 舊版計量對應 | 使用量 |
|---|---|---|---| ---| ---| ---|
| TotalRequests （要求總數） | 計數（計數） | 進行的要求數目| DatabaseName、CollectionName、Region、StatusCode| 全部 | TotalRequests、Http 2xx、Http 3xx、Http 400、Http 401、內部伺服器錯誤、可用的服務、已節流的要求、平均每秒鐘要求數 | 用來監視每個狀態碼的要求，每分鐘的資料細微性。 若要取得平均每秒要求數，請使用每分鐘 Count 彙總並除以 60。 |
| MetadataRequests （中繼資料要求） |計數（計數） | 中繼資料要求計數。 Azure Cosmos DB 會維護每個帳戶的系統中繼資料容器，讓您可以免費列舉集合、資料庫等和其設定。 | DatabaseName、CollectionName、Region、StatusCode| 全部| |用來依中繼資料要求而監視節流。|
| MongoRequests （Mongo 要求） | 計數（計數） | 已提出的 Mongo 要求數目 | DatabaseName、CollectionName、Region、CommandName、ErrorCode| 全部 |Mongo 查詢要求率、Mongo 更新要求率、Mongo 刪除要求率、Mongo 插入要求率，Mongo 計數要求速率| 用於監視 Mongo 要求錯誤、各命令類型的使用量。 |

### <a name="request-unit-metrics"></a>要求單位計量

|度量（度量顯示名稱）|單位（匯總類型）|描述|維度| 時間資料粒度| 舊版計量對應 | 使用量 |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge （Mongo 要求費用） | 計數（總計） |已使用的 Mongo 要求單位| DatabaseName、CollectionName、Region、CommandName、ErrorCode| 全部 |Mongo 查詢要求費用、Mongo 更新要求費用、Mongo 刪除要求費用、Mongo 插入要求費用，Mongo 計數要求費用| 用來監視在一分鐘內的 Mongo 資源 RU。|
| TotalRequestUnits （要求單位總數）| 計數（總計） | 已使用的要求單位| DatabaseName、CollectionName、Region、StatusCode |全部| TotalRequestUnits| 用來監視每分鐘資料粒度的總 RU 使用量。 若要取得平均每秒耗用的 RU，請使用每分鐘 Total 彙總並除以 60。|
| ProvisionedThroughput （布建的輸送量）| 計數（最大值） |在容器資料細微性布建輸送量| DatabaseName、容器名稱| 5M| | 用來監視每個容器的布建輸送量。|

### <a name="storage-metrics"></a>儲存體度量

|度量（度量顯示名稱）|單位（匯總類型）|描述|維度| 時間資料粒度| 舊版計量對應 | 使用量 |
|---|---|---|---| ---| ---| ---|
| AvailableStorage （可用的存放裝置） |位元組（總計） | 每個區域每5分鐘資料細微性報告的可用儲存體總計| DatabaseName、CollectionName、Region| 5M| 可用的儲存體| 用於監視可用的儲存體容量 (僅適用於固定的儲存體集合) 最小資料粒度應為 5 分鐘。| 
| DataUsage （資料使用方式） |位元組（總計） |每個區域每5分鐘資料細微性報告的資料使用量總計| DatabaseName、CollectionName、Region| 5M |資料大小 | 用來監視容器和區域的總數據使用量，最小細微性應為5分鐘。|
| IndexUsage （索引使用方式） | 位元組（總計） |每個區域每5分鐘資料細微性報告的索引使用量總計| DatabaseName、CollectionName、Region| 5M| 索引大小| 用來監視容器和區域的總數據使用量，最小細微性應為5分鐘。 |
| DocumentQuota （檔配額） | 位元組（總計） | 每個區域每5分鐘資料細微性報告的總儲存體配額。| DatabaseName、CollectionName、Region| 5M |儲存體容量| 用來監視容器和區域的總配額，最小資料細微性應為5分鐘。|
| DocumentCount （檔計數） | 計數（總計） |每個區域每5分鐘資料細微性報告的總檔計數| DatabaseName、CollectionName、Region| 5M |文件計數|用來監視容器和區域的檔計數，最小資料細微性應為5分鐘。|

### <a name="latency-metrics"></a>延遲計量

|度量（度量顯示名稱）|單位（匯總類型）|描述|維度| 時間資料粒度| 使用量 |
|---|---|---|---| ---| ---|
| ReplicationLatency （複寫延遲）| 毫秒（最小值、最大值、平均） | 異地複寫啟用的帳戶其跨來源和目標區域的 P99 複寫延遲| SourceRegion、TargetRegion| 全部 | 用來監視異地複寫的帳戶其任兩個區域之間的 P99 複寫延遲。 |


### <a name="availability-metrics"></a>可用性度量

|度量（度量顯示名稱） |單位（匯總類型）|描述| 時間資料粒度| 舊版計量對應 | 使用量 |
|---|---|---|---| ---| ---|
| ServiceAvailability （服務可用性）| 百分比（最小值，最大值） | 一小時資料粒度的帳戶要求可用性| 1H | 服務可用性 | 代表通過要求總數的百分比。 如果狀態碼為 410、500 或 503，則要求會因為系統錯誤而視為失敗。用於以小時資料粒度監視帳戶的可用性。 |


### <a name="cassandra-api-metrics"></a>Cassandra API 計量

|度量（度量顯示名稱）|單位（匯總類型）|描述|維度| 時間資料粒度| 使用量 |
|---|---|---|---| ---| ---|
| CassandraRequests （Cassandra 要求） | 計數（計數） | 已提出的 Cassandra API 要求數目| DatabaseName、CollectionName、ErrorCode、Region、OperationType、ResourceType| 全部| 用於監視每分鐘資料粒度的 Cassandra 要求數。 若要取得平均每秒要求數，請使用每分鐘 Count 彙總並除以 60。|
| CassandraRequestCharges （Cassandra 要求費用） | Count （Sum、Min、Max、Avg） | Cassandra API 要求所耗用的要求單位| DatabaseName、CollectionName、Region、OperationType、ResourceType| 全部| 用來監視 Cassandra API 帳戶每分鐘所使用的 RU 數。|
| CassandraConnectionClosures （Cassandra 連接閉） |計數（計數） |已終止的 Cassandra 連線數目| ClosureReason、Region| 全部 | 用於監視用戶端與 Azure Cosmos DB Cassandra API 之間的連線。|

## <a name="next-steps"></a>後續步驟

* [從 Azure Cosmos DB 帳戶計量窗格中查看和監視計量](use-metrics.md)

* [Azure Cosmos DB 中的診斷記錄](logging.md)
