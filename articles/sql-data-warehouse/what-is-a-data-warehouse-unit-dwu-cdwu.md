---
title: Azure SQL 資料倉儲中的資料倉儲單位 (DWU、cDWU) | Microsoft Docs
description: 選擇理想的資料倉儲單位 (DWU、cDWU) 數目以獲得最佳價格與效能，以及如何變更單位數目的建議。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 05/30/2019
ms.author: martinle
ms.reviewer: igorstan
mscustom: sqlfreshmay19
ms.openlocfilehash: d20a600951a0fe586e981adf12127072df1b744c
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428004"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>資料倉儲單位 (DWU) 和計算資料倉儲單位 (cDWU)

選擇理想的資料倉儲單位 (DWU、cDWU) 數目以獲得最佳價格與效能，以及如何變更單位數目的建議。

## <a name="what-are-data-warehouse-units"></a>什麼是資料倉儲單位

Azure SQL 資料倉儲，將 CPU、 記憶體和 IO 組合到稱為 「 資料倉儲單位 (Dwu) 的計算縮放比例。 DWU 能以抽象而標準化的量值來呈現計算資源與效能。 您的服務層級的變更會改變是提供給系統，進而調整的效能與成本，您的系統的 Dwu 數目。

針對較高的效能，您可以增加資料倉儲單位數目。 較低的效能，降低資料倉儲單位。 儲存體和計算成本會分別計費，因此，變更資料倉儲單位不會影響儲存體成本。

資料倉儲單位的效能是以這些資料倉儲工作負載計量為根據：

- 標準資料倉儲查詢可以掃描大量資料列，並接著執行複雜的彙總速度。 這個作業是 I/O 和 CPU 密集型作業。
- 如何快速資料倉儲可以內嵌資料從 Azure 儲存體 Blob 或 Azure Data Lake。 這個作業是網路和 CPU 密集型作業。
- 如何快速[ `CREATE TABLE AS SELECT` ](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL 命令可以將資料表複製。 這個作業牽涉到從儲存體讀取資料、跨應用裝置的節點散發資料，以及重新寫入至儲存體。 這個作業是 CPU、IO 和網路密集型作業。

增加 DWU：

- 以線性方式變更掃描、彙總和 CTAS 陳述式的系統效能
- 針對 PolyBase 載入作業增加讀取器和寫入器的數目
- 增加並行查詢和並行位置的數目上限。

## <a name="service-level-objective"></a>服務等級目標

服務等級目標 (SLO) 是決定您資料倉儲之成本和效能層級的延展性設定。 Gen2 的服務等級會以計算資料倉儲單位 (cDWU) 來測量，例如 DW2000c。 Gen1 服務等級則會以 DWU 來測量，例如 DW2000。
  > [!NOTE]
  > Azure SQL 資料倉儲 Gen2 最近新增了其他調整規模功能，以支援最低 100 計算資料倉儲單位的計算層。 目前在 Gen1 上需要較低計算層的現有資料倉儲，現在可以升級到目前可用的區域中的 Gen2，不需要額外成本。  如果尚不支援您的區域，您仍然可以升級到支援的地區。 如需詳細資訊，請參閱[升級至 Gen2](upgrade-to-latest-generation.md)。

在 T-SQL 的 SERVICE_OBJECTIVE 設定會決定服務層級和資料倉儲的效能層級。

```sql
--Gen1
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Gen2
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>效能層級和資料倉儲單位

每一個效能層級都會使用與其資料倉儲單位稍有不同的測量單位。 這項差異會在縮放單位直接轉換為帳單時反映於發票上。

- Gen1 資料倉儲會以資料倉儲單位 (DWU) 來測量。
- Gen2 資料倉儲則會以計算的資料倉儲單位 (cDWU) 來測量。

DWU 和 cDWU 均支援將計算相應增加或減少，並且在您不需使用資料倉儲時暫停計算。 這些作業全都依需求指定。 Gen2 會使用計算節點上的本機磁碟型快取來改善效能。 當您調整或暫停系統時，快取就會失效，因此，需要一段快取準備時間，才能達到最佳效能。  

當您增加資料倉儲單位時，正是以線性方式增加運算資源。 Gen2 提供最佳查詢效能和最大的規模。 Gen2 系統也會讓大部分使用快取。

### <a name="capacity-limits"></a>容量限制

每部 SQL 伺服器 (例如 myserver.database.windows.net) 都有[資料庫交易單位 (DTU)](../sql-database/sql-database-what-is-a-dtu.md) 配額，允許有特定數目的資料倉儲單位。 如需詳細資訊，請參閱[工作負載管理容量限制](sql-data-warehouse-service-capacity-limits.md#workload-management)。

## <a name="how-many-data-warehouse-units-do-i-need"></a>我需要多少資料倉儲單位

理想的資料倉儲單位數大部分取決於您的工作負載，以及您已載入系統的資料量。

為工作負載尋找最佳 DWU 的步驟：

1. 首先選取較小的 DWU。
2. 當您的測試資料載入系統時監視應用程式效能，觀察比較所選 DWU 數目與您觀察到的效能。
3. 針對定期的尖峰活動期間，識別任何其他需求。 顯示重要的工作負載尖峰與低谷活動中的可能需要經常進行調整。

SQL 資料倉儲是一個相應放大系統，可以佈建大量的計算以及查詢相當大量的資料。 若要查看真正用以調整的功能 (尤其是在較大的 DWU 上)，建議您在進行調整以確定有足夠資料可提供給 CPU 時調整資料集。 針對調整測試，我們建議至少使用 1 TB。

> [!NOTE]
>
> 如果工作可以在計算節點之間分割，則查詢效能只會隨更多的平行處理增加。 如果您發現調整並未變更效能，則可能需要調整資料表設計和/或您的查詢。 如需查詢微調指引，請參閱[管理使用者查詢](sql-data-warehouse-overview-manage-user-queries.md)。

## <a name="permissions"></a>權限

變更資料倉儲單位需要 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) 中所述的權限。

SQL DB 參與者和 SQL Server 參與者等 Azure 資源內建角色可以變更 DWU 設定。

## <a name="view-current-dwu-settings"></a>檢視目前的 DWU 設定

檢視目前的 DWU 設定：

1. 在 Visual Studio 中開啟 [SQL Server 物件總管]。
2. 連接到與邏輯 SQL Database 伺服器相關聯的 master 資料庫。
3. 從 sys.database_service_objectives 動態管理檢視中選取。 下列是一個範例：

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>變更資料倉儲單位

### <a name="azure-portal"></a>Azure 入口網站

變更 DWU 或 cDWU：

1. 開啟 [Azure 入口網站](https://portal.azure.com)、開啟您的資料庫，然後按一下 [調整]  。

2. 在 [調整]  下方，將滑桿向左或右移動來變更 DWU 設定。

3. 按一下 [儲存]  。 確認訊息隨即出現。 按一下 [是]  以確認或 [否]  以取消。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要變更 Dwu 或 Cdwu，請使用[組 AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell cmdlet。 下列範例會將裝載在 MyServer 伺服器上的資料庫 MySQLDW 的服務等級目標設定為 DW1000。

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

如需詳細資訊，請參閱 [SQL 資料倉儲的 PowerShell Cmdlet](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL

利用 T-SQL，您可以檢視目前的 DWU 或 cDWU 設定、變更設定，以及檢查進度。

變更 DWU 或 cDWU：

1. 連接到與您的邏輯 SQL Database 伺服器相關聯的 master 資料庫。
2. 使用 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) TSQL 陳述式。 下例範例會將資料庫 MySQLDW 的服務等級目標設定為 DW1000。

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>REST API

若要變更 DWU，請使用[建立或更新資料庫](/rest/api/sql/databases/createorupdate) REST API。 下列範例會設定為 DW1000 資料庫 MySQLDW 裝載在 MyServer 伺服器的服務等級目標。 此伺服器位於 ResourceGroup1 這個 Azure 資源群組。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

如需更多 REST API 範例，請參閱 [SQL 資料倉儲的 REST API](sql-data-warehouse-manage-compute-rest-api.md)。

## <a name="check-status-of-dwu-changes"></a>檢查 DWU 變更的狀態

DWU 變更可能需要幾分鐘的時間才能完成。 如果正在進行自動調整，請考慮實作邏輯，以確保在繼續進行其他動作之前，已完成特定作業。

透過各種端點檢查資料庫狀態，可讓您正確實作自動化。 入口網站會提供資料庫目前的狀態，也會在作業完成時提供通知，但不允許以程式設計方式檢查狀態。

您無法使用 Azure 入口網站來檢查相應放大作業的資料庫狀態。

檢查 DWU 變更的狀態：

1. 連接到與您的邏輯 SQL Database 伺服器相關聯的 master 資料庫。
2. 提交下列查詢以檢查資料庫狀態。

```sql
SELECT    *
FROM      sys.databases
;
```

1. 提交下列查詢以檢查作業的狀態

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

此 DMV 會傳回作業和作業，因為它是 IN_PROGRESS 或完成狀態等您 SQL 資料倉儲上各種管理作業的相關資訊。

## <a name="the-scaling-workflow"></a>調整工作流程

當您啟動調整規模作業時，系統會先刪除所有開啟的工作階段，回復任何開啟的交易，以確保一致的狀態。 針對調整規模作業，只有在這個交易回復完成後調整才會發生。  

- 相應增加作業中，系統會卸離所有計算節點，會佈建額外的計算節點，並再重新附加至儲存層。
- 針對相應減少作業，系統會中斷連結的所有計算節點，並再重新附加只有所需的節點到儲存層。

## <a name="next-steps"></a>後續步驟

若要深入了解管理效能，請參閱[適用於工作負載管理的資源類別](resource-classes-for-workload-management.md)和[記憶體和並行存取限制](memory-and-concurrency-limits.md)。
