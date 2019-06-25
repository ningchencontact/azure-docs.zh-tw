---
title: 從 Azure 監視器取得 Azure Cosmos DB 計量
description: ''
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 2eb61a6b9afa3cabf1733be120dfbdacb7de4534
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276791"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>監視和偵錯 Azure 監視器中的 Azure Cosmos DB 計量

您可以檢視來自 Azure 監視器 API 的 Azure Cosmos DB 計量。 Azure 監視器提供數種方式可互動的計量，包括 Azure 入口網站、 透過 REST API 存取它們，或查詢它們使用 PowerShell 或 CLI。 Azure Cosmos DB 計量是低延遲的數值，收集一分鐘的頻率，根據預設，您也可以彙總這些計量。 這些計量是能夠支援即時的案例。  

這篇文章說明不同 Azure Cosmos DB 計量可以檢視從使用 Azure 入口網站的 Azure 監視器。 如果您想要在一般使用案例，以及如何重新的 Azure Cosmos DB 計量來分析和偵錯這些問題，請參閱[監視器和 Azure Cosmos DB 中的計量偵錯](use-metrics.md)文章。 您將使用其中一個現有的 Azure Cosmos 帳戶，並檢視不同的計量，在資料庫、 容器、 區域、 要求或作業層級。 因此，請確定您有 Azure Cosmos 帳戶使用範例資料，而且該資料上執行 CRUD 作業。

## <a name="view-metrics-from-azure-portal"></a>從 Azure 入口網站檢視計量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 選取 **監視器**從左側導覽列中，然後選取**計量**。

   ![Azure 監視器中的 [計量] 窗格](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. 從**度量**窗格 >**選取資源**> 選擇所需**訂用帳戶**，以及**資源群組**。 針對**資源類型**，選取**Azure Cosmos DB 帳戶**，選擇其中一個現有的 Azure Cosmos 帳戶，然後選取**套用**。 

   ![選擇 檢視度量的 Cosmos DB 帳戶](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. 接下來，您可以從可用的度量清單中選取計量。 您可以選取特定的要求單位、 儲存體、 延遲、 可用性、 Cassandra 和其他度量。 若要了解在所有可用的度量，這份清單中的相關詳細資料，請參閱[依類別目錄的度量](#metrics-by-category)一節。 在此範例中，請選取**的要求單位**並**Avg**做為彙總的值。 

   除了這些詳細資料，您也可以選取**時間範圍**並**時間資料粒度**的度量。 在最大值，您可以檢視計量在過去 30 天內。  套用篩選之後，圖表會顯示根據您的篩選條件。 您可以看到每分鐘的所選時段所耗用的要求單位的平均數目。  

   ![從 Azure 入口網站中選擇的計量](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>將篩選新增至計量

您也可以篩選計量和顯示由特定的圖表**CollectionName**， **DatabaseName**， **OperationType**，**區域**，並**StatusCode**。 若要篩選的計量，請選取**新增篩選器**並選擇所需的內容，例如**OperationType**選取值，例如**查詢**。 然後，此圖形會顯示所選時段的查詢作業所耗用的要求單位。 透過預存程序執行的作業不會記錄，因此不提供在 OperationType 計量。

![加入篩選以選取計量資料粒度](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

您可以使用群組計量**適用於分割**選項。 比方說，您可以群組的要求單位，每種作業類型，並檢視一次所示在下圖中的所有作業的圖形： 

![新增適用於分割的篩選器](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>依類別目錄的度量

### <a name="request-metrics"></a>要求計量
            
|計量 （度量的顯示名稱）|單位 （彙總類型） |描述|維度| 時間資料粒度| 舊版計量對應 | 使用量 |
|---|---|---|---| ---| ---| ---|
| TotalRequests （要求總數） | 計數 （計數） | 進行的要求數目| DatabaseName、CollectionName、Region、StatusCode| 全部 | TotalRequests、Http 2xx、Http 3xx、Http 400、Http 401、內部伺服器錯誤、可用的服務、已節流的要求、平均每秒鐘要求數 | 用於監視各狀態碼的要求數、分鐘資料粒度的集合。 若要取得平均每秒要求數，請使用每分鐘 Count 彙總並除以 60。 |
| MetadataRequests （中繼資料要求） |計數 （計數） | 中繼資料要求計數。 Azure Cosmos DB 會維護系統中繼資料集合，針對每個帳戶，可讓您列舉集合、 資料庫等，及其組態，免費。 | DatabaseName、CollectionName、Region、StatusCode| 全部| |用來依中繼資料要求而監視節流。|
| MongoRequests （Mongo 要求） | 計數 （計數） | 已提出的 Mongo 要求數目 | DatabaseName、CollectionName、Region、CommandName、ErrorCode| 全部 |Mongo 查詢要求率、Mongo 更新要求率、Mongo 刪除要求率、Mongo 插入要求率，Mongo 計數要求速率| 用於監視 Mongo 要求錯誤、各命令類型的使用量。 |

### <a name="request-unit-metrics"></a>要求單位計量

|計量 （度量的顯示名稱）|單位 （彙總類型）|描述|維度| 時間資料粒度| 舊版計量對應 | 使用量 |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge （Mongo 要求收費） | 計數 （總計） |已使用的 Mongo 要求單位| DatabaseName、CollectionName、Region、CommandName、ErrorCode| 全部 |Mongo 查詢要求費用、Mongo 更新要求費用、Mongo 刪除要求費用、Mongo 插入要求費用，Mongo 計數要求費用| 用來監視在一分鐘內的 Mongo 資源 RU。|
| TotalRequestUnits （總的要求單位）| 計數 （總計） | 已使用的要求單位| DatabaseName、CollectionName、Region、StatusCode |全部| TotalRequestUnits| 用來監視每分鐘資料粒度的總 RU 使用量。 若要取得平均每秒耗用的 RU，請使用每分鐘 Total 彙總並除以 60。|
| ProvisionedThroughput （佈建的輸送量）| 計數 （最多） |於集合資料粒度所佈建的輸送量| DatabaseName、CollectionName| 5M| | 用於監視各集合所佈建的輸送量。|

### <a name="storage-metrics"></a>儲存體度量

|計量 （度量的顯示名稱）|單位 （彙總類型）|描述|維度| 時間資料粒度| 舊版計量對應 | 使用量 |
|---|---|---|---| ---| ---| ---|
| AvailableStorage （可用的存放裝置） |位元組 （總計） | 可用總儲存體，每個區域的 5 分鐘資料粒度報告| DatabaseName、CollectionName、Region| 5M| 可用的儲存體| 用於監視可用的儲存體容量 (僅適用於固定的儲存體集合) 最小資料粒度應為 5 分鐘。| 
| DataUsage （資料使用方式） |位元組 （總計） |報告每個區域的 5 分鐘資料粒度的總資料使用量| DatabaseName、CollectionName、Region| 5M |資料大小 | 用來監視在集合和區域的總資料使用方式，最小資料粒度應為 5 分鐘。|
| IndexUsage （索引使用方式） | 位元組 （總計） |報告每個區域的 5 分鐘資料粒度的索引使用量總計| DatabaseName、CollectionName、Region| 5M| 索引大小| 用來監視在集合和區域的總資料使用方式，最小資料粒度應為 5 分鐘。 |
| DocumentQuota （文件配額） | 位元組 （總計） | 每個區域的 5 分鐘資料粒度所報告的總儲存體配額。| DatabaseName、CollectionName、Region| 5M |儲存體容量| 用來監視在集合和區域的總配額，最小資料粒度應為 5 分鐘。|
| DocumentCount （文件計數） | 計數 （總計） |總計的文件計數報告每個區域的 5 分鐘資料粒度| DatabaseName、CollectionName、Region| 5M |文件計數|用來監視在集合和區域的文件計數，最小資料粒度應為 5 分鐘。|

### <a name="latency-metrics"></a>延遲計量

|計量 （度量的顯示名稱）|單位 （彙總類型）|描述|維度| 時間資料粒度| 使用量 |
|---|---|---|---| ---| ---|
| ReplicationLatency （複寫延遲）| 毫秒 （最小值、 最大值、 平均值） | 異地複寫啟用的帳戶其跨來源和目標區域的 P99 複寫延遲| SourceRegion、TargetRegion| 全部 | 用來監視異地複寫的帳戶其任兩個區域之間的 P99 複寫延遲。 |


### <a name="availability-metrics"></a>可用性度量

|計量 （度量的顯示名稱） |單位 （彙總類型）|描述| 時間資料粒度| 舊版計量對應 | 使用量 |
|---|---|---|---| ---| ---|
| ServiceAvailability （服務可用性）| %（最小值、 最大值） | 一小時資料粒度的帳戶要求可用性| 1H | 服務可用性 | 表示傳遞要求的總計百分比。 如果狀態碼為 410、500 或 503，則要求會因為系統錯誤而視為失敗。用於以小時資料粒度監視帳戶的可用性。 |


### <a name="cassandra-api-metrics"></a>Cassandra API 計量

|計量 （度量的顯示名稱）|單位 （彙總類型）|描述|維度| 時間資料粒度| 使用量 |
|---|---|---|---| ---| ---|
| CassandraRequests （Cassandra 要求） | 計數 （計數） | 已提出的 Cassandra API 要求數目| DatabaseName、CollectionName、ErrorCode、Region、OperationType、ResourceType| 全部| 用於監視每分鐘資料粒度的 Cassandra 要求數。 若要取得平均每秒要求數，請使用每分鐘 Count 彙總並除以 60。|
| CassandraRequestCharges （Cassandra 要求費用） | 計數 （總和、 Min、 Max、 Avg） | Cassandra API 要求所耗用的要求單位| DatabaseName、CollectionName、Region、OperationType、ResourceType| 全部| 用來監視 Cassandra API 帳戶每分鐘所使用的 RU 數。|
| CassandraConnectionClosures (Cassandra 連接 Closure) |計數 （計數） |已終止的 Cassandra 連線數目| ClosureReason、Region| 全部 | 用於監視用戶端與 Azure Cosmos DB Cassandra API 之間的連線。|

## <a name="next-steps"></a>後續步驟

* [從 Azure Cosmos DB 帳戶的計量 窗格的檢視和監視計量](use-metrics.md)

* [Azure Cosmos DB 中的診斷記錄](logging.md)
