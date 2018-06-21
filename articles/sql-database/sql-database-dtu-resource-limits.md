---
title: Azure SQL Database 以 DTU 為基礎的資源限制 | Microsoft Docs
description: 此頁面描述一些 Azure SQL Database 以 DTU 為基礎的常見資源限制。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: carlrab
ms.openlocfilehash: 9a6d347b707f9b58c5667c5d03d6374288e74267
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645118"
---
# <a name="azure-sql-database-dtu-based-resource-model-limits"></a>Azure SQL Database 以 DTU 為基礎的資源模型限制

> [!IMPORTANT]
> 如需以虛擬核心為基礎的資源限制，請參閱 [SQL Database 以虛擬核心為基礎的資源限制](sql-database-vcore-resource-limits.md)。

## <a name="single-database-storage-sizes-and-performance-levels"></a>單一資料庫：儲存體大小與效能層級

對於單一資料庫，下表顯示了單一資料庫在每個服務層和效能層級的可用資源。 您可以使用 [Azure 入口網站](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal)、[TRANSACT-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql)、[PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell)、[Azure CLI](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli) 或 [REST API](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api) 來為單一資料庫設定服務層、效能層級和儲存體數量。

### <a name="basic-service-tier"></a>基本服務層
| **效能等級** | **基本** |
| :--- | --: |
| 最大 DTU | 5 |
| 內含儲存體 (GB) | 2 |
| 最大儲存體選擇 (GB) | 2 |
| OLTP 記憶體內部儲存體上限 (GB) |N/A |
| 並行背景工作 (要求) 數上限 | 30 |
| 並行工作階段數上限 | 300 |
|||

### <a name="standard-service-tier"></a>標準服務層
| **效能等級** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|---:|
| 最大 DTU | 10 | 20 | 50 | 100 |
| 內含儲存體 (GB) | 250 | 250 | 250 | 250 |
| 最大儲存體選擇 (GB)* | 250 | 250 | 250 | 250, 500, 750, 1024 |
| OLTP 記憶體內部儲存體上限 (GB) | N/A | N/A | N/A | N/A |
| 並行背景工作 (要求) 數上限| 60 | 90 | 120 | 200 |
| 並行工作階段數上限 |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>標準服務層 (續)
| **效能等級** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|---:|
| 最大 DTU | 200 | 400 | 800 | 1600 | 3000 |
| 內含儲存體 (GB) | 250 | 250 | 250 | 250 | 250 |
| 最大儲存體選擇 (GB)* | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| OLTP 記憶體內部儲存體上限 (GB) | N/A | N/A | N/A | N/A |N/A |
| 並行背景工作 (要求) 數上限| 400 | 800 | 1600 | 3200 |6000 |
| 並行工作階段數上限 |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>高階服務層 
| **效能等級** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| 最大 DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| 內含儲存體 (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| 最大儲存體選擇 (GB)* | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| OLTP 記憶體內部儲存體上限 (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| 並行背景工作 (要求) 數上限| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| 並行工作階段數上限 | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||


> [!IMPORTANT]
> - 大於內含儲存體數量的儲存體大小為預覽版，而且會產生額外成本。 如需詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。 
>
> - 所有區域均可取得進階層中超過 1 TB 的儲存體，下列區域除外：英國北部、美國中西部、英國南部2、中國東部、USDoDCentral、德國中部、USDoDEast、US Gov (西南部)、US Gov (中南部)、德國東北部、中國北部、US Gov (東部)。 我們已規劃在更多區域推出。 在其他區域，進階層中的儲存空間上限為 1 TB。 請參閱 [P11-P15 目前限制](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。  
>

## <a name="single-database-change-storage-size"></a>單一資料庫：變更儲存體大小

- 單一資料庫的 DTU 價格包含一定數量不額外收費的儲存體。 佈建超過內含量的額外儲存體會產生額外費用，以 250 GB 為單位最多增加到大小上限 1 TB，超過 1 TB 則以 256 GB 為單位增加。 如需了解內含儲存體數量和大小上限，請參閱[單一資料庫：儲存體大小與效能層級](#single-database-storage-sizes-and-performance-levels)。
- 可藉由使用 [Azure 入口網站](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal)、[Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)、[Azure CLI](/cli/azure/sql/db#az_sql_db_update) 或 [REST API](/rest/api/sql/databases/update) 增加其大小上限，以佈建單一資料庫的額外儲存體。
- 單一資料庫之額外儲存體的價格為額外儲存體數量乘以服務層的額外儲存體單價。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

## <a name="single-database-change-dtus"></a>單一資料庫：變更 DTU

一開始選取服務層、效能層級和儲存體數量之後，您可以根據實際經驗，使用 [Azure 入口網站](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal)、[Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)、[Azure CLI](/cli/azure/sql/db#az_sql_db_update) 或 [REST API](/rest/api/sql/databases/update) 來動態地相應增加或相應減少單一資料庫。 

下列影片示範了如何動態變更效能層以增加單一資料庫的可用 DTU。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

變更資料庫的服務層和/或效能層級會在新的效能層級建立原始資料庫的複本，然後將連接切換到複本。 此程序期間不會遺失任何資料，但在我們切換到複本的短暫期間，資料庫的連接會停用，因此執行中的某些交易可能會回復。 切換的時間長度會有所不同，但 99% 的時間會小於 30 秒。 如果在連線停用時正在執行大型交易，則切換的時間長度可能會更長。 

整個向上調整程序的期間取決於資料庫變更前後的大小和服務層。 例如，在標準服務層內進行變更的 250 GB 資料庫應在六小時內完成。 對於進階服務層內變更效能層級的相同大小資料庫，應該在三小時內完成相應增加。

> [!TIP]
> 若要監視進行中的作業，請參閱：[使用 SQL REST API 管理作業](/rest/api/sql/Operations/List)、[使用 CLI 管理作業](/cli/azure/sql/db/op)、[使用 T-SQL 監視作業](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)，以及下列兩個 PowerShell 命令：[Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) 和 [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)。

* 如果您要升級到較高服務層或效能層級，除非您明確指定較大的大小 (大小上限)，否則資料庫大小上限不會增加。
* 若要將資料庫降級，資料庫已用的空間必須小於目標服務層和效能層級允許的大小上限。 
* 從**進階**降級至**標準**層時，如果發生下列情況，將會產生額外的儲存體成本：(1) 在目標效能層級中以資料庫的大小上限進行支援作業，而且 (2) 大小上限超過目標效能層級的內含儲存體數量。 例如，如果大小上限為 500 GB 的 P1 資料庫縮減為 S3，即會產生額外的儲存體成本，因為 S3 可支援 500 GB 的大小上限，而且其內含儲存體數量只有 250 GB。 因此，額外的儲存體數量為 500 GB – 250 GB = 250 GB。 如需額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。 如果實際的使用空間量小於內含的儲存體數量，則可將資料庫大小上限降低至內含量，以避免造成額外成本。 
* 升級資料庫時，若已啟用[異地複寫](sql-database-geo-replication-portal.md)，您必須先將其次要資料庫升級為所需的效能層，然後再升級主要資料庫 (最佳效能的一般指引)。 升級至不同的版本時，必須先升級次要資料庫。
* 當您在啟用[異地複寫](sql-database-geo-replication-portal.md)的情況下將資料庫降級時，必須先將其主要資料庫降級為所需的效能層級，然後再將次要資料庫降級 (最佳效能的一般指引)。 降級至不同的版本時，必須先降級主要資料庫。
* 還原服務會針對各種服務層提供不同的供應項目。 如果降級至**基本**層，會有較短的備份保留期 - 請參閱 [Azure SQL Database 備份](sql-database-automated-backups.md)。
* 完成變更之前，不會將新屬性套用至資料庫。

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>單一資料庫：當大小上限大於 1 TB 時，P11 和 P15 的限制

下列區域支援大小上限大於 1 TB 的 P11 和 P15 資料庫：澳大利亞東部、澳大利亞東南部、巴西南部、加拿大中部、加拿大東部、美國中部、法國中部、德國中部、日本東部、日本西部、南韓中部、美國中北部、北歐、美國中南部、東南亞、英國南部、英國西部、美國東部 2、美國西部、美國維吉尼亞州政府及西歐。 下列考量與限制適用於大小上限大於 1 TB 的 P11 和 P15 資料庫：

- 如果您在建立資料庫時選擇的大小上限大於 1 TB (使用的值為 4 TB 或 4096 GB)，但資料庫佈建在不受支援的區域，create 命令會失敗並產生錯誤。
- 若現有的 P11 和 P15 資料庫位於其中一個受支援區域，則能以 256 GB 為單位，將儲存體上限提高到 1 TB 以上，最多可增加到 4 TB。 若要查看您所在的區域是否支援較大的大小，請使用 [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) 函數或在 Azure 入口網站中檢查資料庫大小。 只有伺服器層級的主要登入或 dbmanager 資料庫角色的成員，才能執行現有的 P11 或 P15 資料庫升級。 
- 如果是在支援的區域中執行升級作業，則系統會立即更新組態。 在升級過程中，資料庫會保持離線狀態。 但是，在將實際的資料庫檔案升級至新的大小上限前，您無法完整地使用超過 1 TB 的儲存體。 所需的時間長短依進行升級的資料庫大小而定。 
- 建立或更新 P11 或 P15 資料庫時，您只能以 256 GB 為增加單位，在 1 TB 和 4 TB 的大小上限之間選擇。 在建立 P11/P15 時，系統會預先選取預設的 1 TB 儲存體選項。 若資料庫位於其中一個受支援區域，您可以將新的或現有單一資料庫的儲存體上限增加為 4 TB。 在其他所有區域中，無法將大小上限增加至 1 TB 以上。 當您選取 4 TB 的內含儲存體時，價格不會變更。
- 如果資料庫的大小上限設為大於 1 TB，即使使用的實際儲存體未達 1 TB，也無法變更為 1 TB。 因此，您無法將大小上限大於 1 TB 的 P11 或 P15 降級至 1 TB P11 或 1 TB P15，也無法降至較低的效能層，例如 P1-P6。 這項限制也適用於包括時間點、異地還原、長期備份保留期和資料庫備份等還原和複製案例。 一旦將資料庫的大小上限設定為 1 TB 以上，此資料庫的所有還原作業都必須以大於 1 TB 大小上限的 P11/P15 執行。
- 若是作用中異地複寫案例：
   - 設定異地複寫關聯性：如果 P11 或 P15 為主要資料庫，則次要資料庫也必須是 P11 或 P15；系統會拒絕使用較低的效能層級做為次要資料庫，因為這些資料庫無法支援 1 TB 以上的大小。
   - 在異地複寫關聯性中升級主要資料庫：將主要資料庫的大小上限變更為 1 TB 以上會在次要資料庫中觸發相同的變更。 這兩種升級方式都必須成功，在主要資料庫中的變更才會生效。 適用於大於 1-TB 選項的區域限制。 如果次要資料庫位於不支援大於 1 TB 的區域，就無法升級主要資料庫。
- 不支援使用匯入/匯出服務載入具有 1 TB 以上之 P11/P15 資料庫的功能。 使用 SqlPackage.exe 來[匯入](sql-database-import.md)和[匯出](sql-database-export.md)資料。

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>彈性集區：儲存體大小與效能層級

對於 SQL Database 彈性集區，下表顯示了每個服務層和效能層級的可用資源。 您可以使用 [Azure 入口網站](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal)、[PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell)、[Azure CLI](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli) 或 [REST API](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api)，設定服務層、效能層級和儲存體數量。

> [!NOTE]
> 根據 DTU 和服務層，彈性集區中個別資料庫的資源限制通常與集區外部之單一資料庫的資源限制相同。 例如，S2 資料庫的並行背景工作數上限是 120 個背景工作。 因此，如果集區中每個資料庫的最大 DTU 是 50 DTU (這相當於 S2)，標準集區中的資料庫最大並行背景工作數上限也會是 120 個背景工作。

### <a name="basic-elastic-pool-limits"></a>基本彈性集區限制

| 每集區 eDTU | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| 每個集區內含的儲存體 (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| 每個集區的最大儲存體選擇 (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| 每個集區的記憶體內部 OLTP 儲存體上限 (GB) | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A |
| 每個集區的最大 DB 數 | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| 每個集區的並行背景工作 (要求) 數上限 | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| 每集區並行工作階段數上限 | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| 每個資料庫的最小 eDTU 選擇 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| 每個資料庫的最大 eDTU 選擇 | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| 每個資料庫的儲存體上限 (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 
||||||||

### <a name="standard-elastic-pool-limits"></a>標準彈性集區限制

| 每集區 eDTU | **50** | **100** | **200** | **300** | **400** | **800**| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| 每個集區內含的儲存體 (GB) | 50 | 100 | 200 | 300 | 400 | 800 | 
| 每個集區的最大儲存體選擇 (GB)* | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 | 
| 每個集區的記憶體內部 OLTP 儲存體上限 (GB) | N/A | N/A | N/A | N/A | N/A | N/A | 
| 每個集區的最大 DB 數 | 100 | 200 | 500 | 500 | 500 | 500 | 
| 每個集區的並行背景工作 (要求) 數上限 | 100 | 200 | 400 | 600 | 800 | 1600 |
| 每集區並行工作階段數上限 | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| 每個資料庫的最小 eDTU 選擇 | 0, 10, 20, 50 | 0、10、20、50、100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| 每個資料庫的最大 eDTU 選擇 | 10, 20, 50 | 10、20、50、100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| 每個資料庫的儲存體上限 (GB)* | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>標準彈性集區限制 (續) 

| 每集區 eDTU | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| 每個集區內含的儲存體 (GB) | 1200 | 1600 | 2000 | 2500 | 3000 | 
| 每個集區的最大儲存體選擇 (GB)* | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| 每個集區的記憶體內部 OLTP 儲存體上限 (GB) | N/A | N/A | N/A | N/A | N/A | 
| 每個集區的最大 DB 數 | 500 | 500 | 500 | 500 | 500 | 
| 每個集區的並行背景工作 (要求) 數上限 | 2400 | 3200 | 4000 | 5000 | 6000 |
| 每集區並行工作階段數上限 | 30000 | 30000 | 30000 | 30000 | 30000 | 
| 每個資料庫的最小 eDTU 選擇 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| 每個資料庫的最大 eDTU 選擇 | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| 每個資料庫的最大儲存體選擇 (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits"></a>高階彈性集區限制

| 每集區 eDTU | **125** | **250** | **500** | **1000** | **1500**| 
|:---|---:|---:|---:| ---: | ---: | 
| 每個集區內含的儲存體 (GB) | 250 | 500 | 750 | 1024 | 1536 | 
| 每個集區的最大儲存體選擇 (GB)* | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| 每個集區的記憶體內部 OLTP 儲存體上限 (GB) | 1 | 2 | 4 | 10 | 12 | 
| 每個集區的最大 DB 數 | 50 | 100 | 100 | 100 | 100 | 
| 每個集區的並行背景工作 (要求) 數上限 | 200 | 400 | 800 | 1600 | 2400 | 
| 每集區並行工作階段數上限 | 30000 | 30000 | 30000 | 30000 | 30000 | 
| 每資料庫的 eDTU 下限 | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 | 
| 每資料庫的 eDTU 上限 | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000, 1500 |
| 每個資料庫的儲存體上限 (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>高階彈性集區限制 (續) 

| 每集區 eDTU | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: | 
| 每個集區內含的儲存體 (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| 每個集區的最大儲存體選擇 (GB)* | 2048 | 2560 | 3072 | 3548 | 4096|
| 每個集區的記憶體內部 OLTP 儲存體上限 (GB) | 16 | 20 | 24 | 28 | 32 |
| 每個集區的最大 DB 數 | 100 | 100 | 100 | 100 | 100 | 
| 每個集區的並行背景工作 (要求) 數上限 | 3200 | 4000 | 4800 | 5600 | 6400 |
| 每集區並行工作階段數上限 | 30000 | 30000 | 30000 | 30000 | 30000 | 
| 每個資料庫的最小 eDTU 選擇 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| 每個資料庫的最大 eDTU 選擇 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| 每個資料庫的儲存體上限 (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

> [!IMPORTANT]
> - 大於內含儲存體數量的儲存體大小為預覽版，而且會產生額外成本。 如需詳細資訊，請參閱 [SQL Database 價格頁面](https://azure.microsoft.com/pricing/details/sql-database/)。 大於內含儲存體數量的儲存體大小為預覽版，而且會產生額外成本。 如需詳細資訊，請參閱 [SQL Database 價格頁面](https://azure.microsoft.com/pricing/details/sql-database/)。
>
> - 所有區域均可取得進階層中超過 1 TB 的儲存體，下列區域除外：英國北部、美國中西部、英國南部2、中國東部、USDoDCentral、德國中部、USDoDEast、US Gov (西南部)、US Gov (中南部)、德國東北部、中國北部、US Gov (東部)。 我們已規劃在更多區域推出。 在其他區域，進階層中的儲存空間上限為 1 TB。 請參閱 [P11-P15 目前限制](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。  
>

如果彈性集區的所有 DTU 均已使用，則集區中的每個資料庫會收到等量的資源以處理查詢。 SQL Database 服務藉由確保運算時間的均等配量，提供資料庫之間的資源共用公平性。 彈性集區資源共用公平性不包括任何資源數量，否則當每個資料庫的最小 DTU 數設為非零的值時，便會對每個資料庫保證資源數量。

### <a name="database-properties-for-pooled-databases"></a>集區資料庫的資料庫屬性

下表描述集區資料表的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| 每資料庫的 eDTU 上限 |集區中任何資料庫可以使用的 eDTU 數目上限，是否可用則是根據集區中其他資料庫的使用量而定。 每個資料庫的 eDTU 數目上限不等於資料庫的資源保證。 這個設定是全域設定，會套用至集區中的所有資料庫。 將每個資料庫的 eDTU 設定為最上限，以處理資料庫使用率的尖峰。 某種程度的過量使用是可預期的情況，因為集區通常會假設資料庫的熱門和冷門使用模式；在這些模式中，所有資料庫不會同時處於尖峰期。 例如，假設每個資料庫的尖峰使用量是 20 個 DTU，且集區中的 100 個資料庫只有 20% 會同時暴增到尖峰。 如果每一資料庫的 eDTU 上限設為 20 個 eDTU，則以 5 倍的量過量使用集區，並將每集區 eDTU 設為 400 個是合理的作法。 |
| 每資料庫的 eDTU 下限 |集區中單一資料庫能夠保證的最小 eDTU 數。 這個設定是全域設定，會套用至集區中的所有資料庫。 每個資料庫最小 eDTU 建議設定為 0，同時也是預設值。 此屬性會設為 0 到每一資料庫的 eDTU 使用量平均值之間的任意數。 集區中資料庫數目和每個資料庫 eDTU 數目下限的乘積不能超過每個集區的 eDTU。 例如，如果集區有 20 個資料庫，且每個資料庫的最小 eDTU 設定為 10 eDTU，則每個集區 eDTU 必須至少為 200 個 eDTU。 |
| 每個資料庫的儲存體上限 |使用者所設定集區資料庫的資料庫大小上限。 不過，集區化的資料庫會共用配置的集區儲存體。 即使將「每個資料庫」的儲存體上限總計設定為大於可用總儲存體的「集區空間」總計，但所有資料庫實際使用的總空間不能超過可用的集區限制。 資料庫大小上限是指資料檔案的大小上限，並不包含記錄檔所使用的空間。 |
|||
 
## <a name="elastic-pool-change-storage-size"></a>彈性集區：變更儲存體大小

- 彈性集區的 eDTU 價格包含一定數量不額外收費的儲存體。 佈建超過內含量的額外儲存體會產生額外費用，以 250 GB 為單位最多增加到大小上限 1 TB，超過 1 TB 則以 256 GB 為單位增加。 如需了解內含儲存體數量和大小上限，請參閱[彈性集區：儲存體大小與效能層級](#elastic-pool-storage-sizes-and-performance-levels)。
- 可藉由使用 [Azure 入口網站](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)、[Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) 或 [REST API](/rest/api/sql/elasticpools/update) 增加其大小上限，以佈建彈性集區的額外儲存體。
- 彈性集區之額外儲存體的價格為額外儲存體數量乘以服務層的額外儲存體單價。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

## <a name="elastic-pool-change-edtus"></a>彈性集區：變更 eDTU

您可以使用 [Azure 入口網站](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)、[Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) 或 [REST API](/rest/api/sql/elasticpools/update)，根據資源需求來增加或減少彈性集區可用的資源。

- 重新調整集區 eDTU 時，會短暫中斷資料庫連線。 在為單一資料庫 (而非在集區中) 重新調整 DTU 時，也會發生相同的行為。 如需重新調整作業期間，資料庫中斷連線的持續時間和影響的詳細資訊，請參閱[重新調整單一資料庫的 DTU](#single-database-change-storage-size)。 
- 重新調整集區 eDTU 的持續時間可能取決於集區中所有資料庫使用的儲存空間總量。 一般情況下，重新調整延遲時間平均為每 100 GB 在 90 分鐘以內。 舉例來說，如果集區中所有資料庫使用的總空間為 200 GB，則重新調整集區的預期延遲時間為 3 小時內。 在標準層或基本層內的某些情況下，不論使用的空間量多寡，重新調整延遲時間可能會少於五分鐘。
- 一般而言，變更每個資料庫之 eDTU 下限或每個資料庫的 eDTU 上限的持續時間會在五分鐘內。
- 縮減集區 eDTU 的大小時，集區使用的空間必須小於目標服務層和集區 eDTU 允許的大小上限。
- 調整集區 eDTU 時，如果發生下列情況，會產生額外的儲存體成本：(1) 目標集區可支援集區的儲存體大小上限，而且 (2) 儲存體大小上限超過目標集區的內含儲存體數量。 例如，如果大小上限為 100 GB 的 100 eDTU 標準集區縮減為 50 eDTU 標準集區，則會產生額外的儲存體成本，因為目標集區可支援 100 GB 的大小上限，而且其內含儲存體數量只有 50 GB。 因此，額外的儲存體數量為 100 GB – 50 GB = 50 GB。 如需額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。 如果實際的使用空間量小於內含的儲存體數量，則可將資料庫大小上限降低至內含量，以避免造成額外成本。 

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>伺服器和資料庫的最大數量是多少？

| 最大值 | 值 |
| :--- | :--- |
| 每一伺服器的資料庫 | 5000 |
| 每個區域中每個訂用帳戶的伺服器數目 | 20 |
|||

> [!IMPORTANT]
> 每當資料庫數量接近每台伺服器的限制時，可能會出現下列情況：
> <br> •    使用 master 資料庫執行查詢時，延遲狀況增加。  這包含資源使用率統計資料的檢視，例如 sys.resource_stats。
> <br> •    管理作業以及涉及列舉伺服器中資料庫入口網站檢視點的轉譯作業，皆增加延遲狀況。

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>達到資料庫和彈性集區資源限制時，會發生什麼事？

### <a name="compute-dtus-and-edtus"></a>計算 (DTU 和 eDTU)

當資料庫計算使用率 (根據 DTU 和 eDTU 測量) 變高時，查詢延遲會增加，甚至可能逾時。在下列情況下，查詢可能會透過服務排入佇列，並在資源變成可用時，提供資源以供執行。
遇到高計算使用率時，緩和選項包括：

- 提高資料庫或彈性集區的效能層級，提供更多 DTU 或 eDTU 給資料庫。 請參閱[單一資料庫：變更 DTU](#single-database-change-dtus) 與[彈性集區：變更 eDTU](#elastic-pool-change-edtus)。
- 將查詢最佳化，以降低每個查詢的資源使用率。 如需詳細資訊，請參閱[查詢微調/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。

### <a name="storage"></a>儲存體

當使用的資料庫空間達到大小上限，若資料庫的插入和更新作業會增加資料大小，動作即會失敗，且用戶端會收到[錯誤訊息](sql-database-develop-error-messages.md)。 資料庫 SELECTS 與 DELETES 會繼續執行下去。

遇到高空間使用率時，緩和選項包括：

- 增加資料庫或彈性集區的大小上限，或變更效能層級，以取得更多的內含儲存體。 請參閱 [SQL Database 資源限制](sql-database-dtu-resource-limits.md)。
- 如果資料庫在彈性集區，也可以將資料庫移出集區，如此便不會與其他資料庫共用儲存空間。

### <a name="sessions-and-workers-requests"></a>工作階段和背景工作角色 (要求) 

工作階段與背景工作角色的數量上限取決於服務層和效能層級 (DTU 與 eDTU)。 達到工作階段或背景工作角色的限制時，會拒絕新要求，而且用戶端會收到錯誤訊息。 雖然應用程式能控制可用的連線數目，但並行背景工作角色的數目通常難以預估及控制。 特別是在尖峰負載期間，資料庫資源達到上限，背景工作角色也因為長時間執行查詢而不斷累積。 

當工作階段或背景工作角色出現高使用率時，緩和選項包括：
- 提高資料庫或彈性集區的服務層或效能層級。 請參閱[單一資料庫：變更儲存體大小](#single-database-change-storage-size)、[單一資料庫：變更 DTU](#single-database-change-dtus)、[彈性集區：變更儲存體大小](#elastic-pool-change-storage-size)，以及[彈性集區：變更 eDTU](#elastic-pool-change-edtus)。
- 如果背景工作角色的使用率增加是爭用計算資源所造成，則可將查詢最佳化，以減少每個查詢的資源使用率。 如需詳細資訊，請參閱[查詢微調/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。

## <a name="next-steps"></a>後續步驟

- 請參閱 [SQL Database 常見問題集](sql-database-faq.md)以取得常見問題的解答。
- 如需一般 Azure 限制的相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)。
- 如需 DTU 與 eDTU 的相關資訊，請參閱 [DTU 與 eDTU](sql-database-what-is-a-dtu.md)。
- 如需 tempdb 大小限制的相關資訊，請參閱 https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database。
