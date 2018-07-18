---
title: Azure SQL Database 以 DTU 為基礎的資源限制單一資料庫 | Microsoft Docs
description: 此頁面將針對 Azure SQL Database 中的單一資料庫，說明一些以 DTU 為基礎的常見資源限制。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 5a7abb7d67de59ea326b5180cf94e3594cd06576
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753382"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>使用以 DTU 為基礎的購買模型的單一資料庫資源限制 

本文使用以 DTU 為基礎的購買模型，來提供 Azure SQL Database 彈性集區的詳細資源限制。

如需適用於彈性集區的「以 DTU 為基礎的購買模型資源限制」，請參閱[以 DTU 為基礎的資源限制 - 彈性集區](sql-database-vcore-resource-limits-elastic-pools.md)。 如需以虛擬核心為基礎的資源限制，請參閱[以虛擬核心為基礎的資源限制 - 單一資料庫](sql-database-vcore-resource-limits-single-databases.md)和[以虛擬核心為基礎的資源限制 - 彈性集區](sql-database-vcore-resource-limits-elastic-pools.md)。

## <a name="single-database-storage-sizes-and-performance-levels"></a>單一資料庫：儲存體大小與效能層級

對於單一資料庫，下表顯示了單一資料庫在每個服務層和效能層級的可用資源。 您可以使用 [Azure 入口網站](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases)、[PowerShell](sql-database-servers-databases-manage.md#powershell-manage-logical-servers-and-databases)、[Azure CLI](sql-database-servers-databases-manage.md#azure-cli-manage-logical-servers-and-databases) 或 [REST API](sql-database-servers-databases-manage.md#rest-api-manage-logical-servers-and-databases) 來為單一資料庫設定服務層、效能層級和儲存體數量。

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
| 最大儲存體選擇 (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| OLTP 記憶體內部儲存體上限 (GB) | N/A | N/A | N/A | N/A |
| 並行背景工作 (要求) 數上限| 60 | 90 | 120 | 200 |
| 並行工作階段數上限 |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>標準服務層 (續)
| **效能等級** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|---:|
| 最大 DTU | 200 | 400 | 800 | 1600 | 3000 |
| 內含儲存體 (GB) | 250 | 250 | 250 | 250 | 250 |
| 最大儲存體選擇 (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| OLTP 記憶體內部儲存體上限 (GB) | N/A | N/A | N/A | N/A |N/A |
| 並行背景工作 (要求) 數上限| 400 | 800 | 1600 | 3200 |6000 |
| 並行工作階段數上限 |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>高階服務層 
| **效能等級** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| 最大 DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| 內含儲存體 (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| 最大儲存體選擇 (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| OLTP 記憶體內部儲存體上限 (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| 並行背景工作 (要求) 數上限| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| 並行工作階段數上限 | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||


> [!IMPORTANT]
> 所有區域目前均可取得進階層中超過 1 TB 的儲存體，下列區域除外：英國北部、美國中西部、英國南部2、中國東部、USDoDCentral、德國中部、USDoDEast、US Gov (西南部)、US Gov (中南部)、德國東北部、中國北部、US Gov (東部)。 在其他區域，進階層中的儲存空間上限為 1 TB。 請參閱 [P11-P15 目前限制](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。  

## <a name="single-database-change-storage-size"></a>單一資料庫：變更儲存體大小

- 單一資料庫的 DTU 價格包含一定數量不額外收費的儲存體。 佈建超過內含量的額外儲存體會產生額外費用，以 250 GB 為單位最多增加到大小上限 1 TB，超過 1 TB 則以 256 GB 為單位增加。 如需了解內含儲存體數量和大小上限，請參閱[單一資料庫：儲存體大小與效能層級](#single-database-storage-sizes-and-performance-levels)。
- 可藉由使用 [Azure 入口網站](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases)、[Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)、[Azure CLI](/cli/azure/sql/db#az_sql_db_update) 或 [REST API](/rest/api/sql/databases/update) 增加其大小上限，以佈建單一資料庫的額外儲存體。
- 單一資料庫之額外儲存體的價格為額外儲存體數量乘以服務層的額外儲存體單價。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

## <a name="single-database-change-dtus"></a>單一資料庫：變更 DTU

一開始選取服務層、效能層級和儲存體數量之後，您可以根據實際經驗，使用 [Azure 入口網站](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases)、[Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)、[Azure CLI](/cli/azure/sql/db#az_sql_db_update) 或 [REST API](/rest/api/sql/databases/update) 來動態地相應增加或相應減少單一資料庫。 

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

## <a name="next-steps"></a>後續步驟

- 請參閱 [SQL Database 常見問題集](sql-database-faq.md)以取得常見問題的解答。
- 如需一般 Azure 限制的相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)。
- 如需 DTU 與 eDTU 的相關資訊，請參閱 [DTU 與 eDTU](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)。
- 如需 tempdb 大小限制的相關資訊，請參閱 https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database。
