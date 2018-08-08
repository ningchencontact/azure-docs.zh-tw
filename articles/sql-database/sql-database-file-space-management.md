---
title: Azure SQL Database 檔案空間管理 | Microsoft Docs
description: 此頁面說明如何使用 Azure SQL Database 管理檔案空間，以及提供如何判斷您是否需要壓縮資料庫和如何執行資料庫壓縮作業的程式碼範例。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: how-to
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 1ecc0ce08ef42f5f5935bca29e8269be2ea142f0
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2018
ms.locfileid: "39416001"
---
# <a name="manage-file-space-in-azure-sql-database"></a>以 Azure SQL Database 管理檔案空間

本文說明 Azure SQL Database 中不同類型的儲存空間，以及需要由客戶管理為資料庫與彈性集區配置的檔案空間時所能採取的步驟。

## <a name="overview"></a>概觀

在 Azure SQL Database 中，儲存體大小計量會顯示在 Azure 入口網站，而下列 API 會測量資料庫和彈性集區已使用的資料頁：
- Azure Resource Manager 型計量 API 包括 PowerShell [get-metrics](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermmetric)
- T-SQL：[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- T-SQL：[sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL：[sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

有些工作負載模式為資料庫的基礎資料檔案配置的空間，大於資料檔案中已使用資料頁數量。 當使用的空間增加，隨後卻將資料刪除時，就會發生這種情況。 當資料刪除後，並不會自動回收配置的檔案空間。 在這種情況下，為資料庫或集區配置的空間可能會超過為資料庫設定 (或支援) 的支援上限 的資料庫，因此，即使實際使用的資料庫空間小於空間大小上限，也會防止資料成長或防止變更效能層級。 若要減輕此問題的影響，您可能需要壓縮資料庫，以減少資料庫中已配置但未使用的空間。

因為可能會對資料庫效能造成影響，所以 SQL Database 服務不會自動壓縮資料庫檔案，以回收未使用的配置空間。 不過，您可以依照[回收未使用的配置空間](#reclaim-unused-allocated-space)中所述的步驟，在選擇的時間壓縮資料庫中的檔案。 

> [!NOTE]
> 不同於資料檔案，SQL Database 服務會自動壓縮記錄檔，因為該作業並不會影響資料庫效能。

## <a name="understanding-the-types-of-storage-space-for-a-database"></a>了解資料庫的儲存體空間類型

若要管理檔案空間，您需要了解下列和單一資料庫與彈性集區兩者的資料庫儲存體相關的詞彙。

|儲存體空間詞彙|定義|註解|
|---|---|---|
|**使用的資料空間**|以 8 KB 分頁用來儲存資料庫資料的空間量。|一般而言，這個使用的空間會在插入 (刪除) 時增加 (減少)。 在某些情況下，根據作業與任何分割中涉及的資料量和模式而定，使用的空間並不會隨插入或刪除而變更。 例如，從每個資料頁刪除一個資料列，不見得會減少使用的空間。|
|**配置的空間**|可用以儲存資料庫資料的格式化檔案空間量|配置的空間會自動成長，但永遠不會在刪除後減少。 此行為可確保未來能更快地插入，因為不需要重新格式化空間。|
|**已配置但未使用的空間**|配置給資料庫的未使用資料檔案空間量。|這是指配置的空間與使用的空間之間的差異數量，並代表壓縮資料檔案可回收的空間量上限。|
|**大小上限**|資料庫可以使用的資料空間量上限。|配置的資料空間不得成長超過資料大小上限。|
||||

下圖說明儲存體空間類型之間的關聯性。

![儲存體空間類型和關聯性](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-database-for-storage-space-information"></a>查詢資料庫，以取得儲存體空間資訊

若要判斷個別資料庫是否有已配置但未使用的資料空間可供回收，請使用下列查詢：

### <a name="database-data-space-used"></a>使用的資料庫資料空間
修改下列查詢，以傳回資料庫已使用的資料空間量 (MB)。

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-allocated-and-allocated-space-unused"></a>配置的資料庫資料與未使用的配置空間
修改下列查詢，以傳回已配置的資料庫資料量與已配置但未使用的空間量。

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-max-size"></a>資料庫大小上限
修改下列查詢，以傳回資料庫大小上限 (位元組)。

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="query-an-elastic-pool-for-storage-space-information"></a>查詢彈性集區，以取得儲存體空間資訊

若要判斷每個集區資料庫的彈性集區中是否有已配置但未使用的資料空間可供回收，請使用下列查詢：

### <a name="elastic-pool-data-space-used"></a>使用的彈性集區資料空間
修改下列查詢，以傳回彈性集區已使用的資料空間量。

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-allocated-and-allocated-space-unused"></a>配置的彈性集區資料與未使用的配置空間

修改下列 PowerShell 指令碼，以傳回資料表，列出為彈性集區中每個資料庫配置的總空間與未使用的配置空間。 此資料表會根據已配置但未使用空間量從最大至最小來排序資料庫。  

用於判斷為集區中每個資料庫所配置空間的查詢結果，可以加總來判斷已配置的彈性集區空間。 配置的彈性集區空間不應超過彈性集區大小上限。  

```powershell
# Resource group name
$resourceGroupName = "rg1" 
# Server name
$serverName = "ls2" 
# Elastic pool name
$poolName = "ep1"
# User name for server
$userName = "name"
# Password for server
$password = "password"

# Get list of databases in elastic pool
$databasesInPool = Get-AzureRmSqlElasticPoolDatabase `
    -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# For each database in the elastic pool,
# get its space allocated in MB and space allocated unused in MB.
# Requires SQL Server PowerShell module – see here to install.  
foreach ($database in $databasesInPool)
{
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn `
        -Database $database.DatabaseName `
        -Username $userName `
        -Password $password `
        -Query $sqlCommand)
}
# Display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort `
    -Property DatabaseDataSpaceAllocatedUnusedInMB `
    -Descending | Format-Table
```

下列螢幕擷取畫面是指令碼輸出的其中一個範例：

![彈性集區的配置空間和未使用配置空間範例](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-max-size"></a>彈性集區大小上限

使用下列 T-SQL 查詢，以傳回彈性資料庫大小上限 (MB)。

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>回收未使用的配置空間

一旦您確定有想要回收的未使用配置空間，請使用下列命令來壓縮配置的資料庫空間。 

> [!IMPORTANT]
> 針對彈性集區中的資料庫，應該先壓縮已配置但未使用空間最多的資料庫，以更快速回收檔案空間。  

使用下列命令，以壓縮指定資料庫中的所有資料檔案：

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'<database_name>')
```

如需有關此命令的詳細資訊，請參閱 [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql)。

> [!IMPORTANT] 
> 在資料庫資料檔案壓縮後，索引可能會變得分散而失去效能最佳化效益，請考慮重建資料庫索引。 如果發生這種情況，則應該重建索引。 如需索引分散和如何重建索引的詳細資訊，請參閱[重新組織與重建索引](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)。

## <a name="next-steps"></a>後續步驟

- 如需資料庫大小限制的詳細資訊，請參閱：
  - [適用於單一資料庫的 Azure SQL Database 以虛擬核心為基礎的購買模型限制](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)
  - [使用以 DTU 為基礎的購買模型的單一資料庫資源限制](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)
  - [針對彈性集區，Azure SQL Database 虛擬核心形式的購買模型限制](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)
  - [使用以 DTU 為基礎的購買模型的彈性集區資源限制](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools)
- 如需有關 `SHRINKDATABASE` 命令的詳細資訊，請參閱 [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql)。 
- 如需索引分散和如何重建索引的詳細資訊，請參閱[重新組織與重建索引](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)。