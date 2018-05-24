---
title: Azure SQL Database 以虛擬核心為基礎的資源限制 | Microsoft Docs
description: 此頁面描述一些 Azure SQL Database 以虛擬核心為基礎的常見資源限制。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 05/15/2018
ms.author: carlrab
ms.openlocfilehash: d225af55a705d56a94bb0e8dcfcc938b64f3633a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210795"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-preview"></a>Azure SQL Database 以虛擬核心為基礎的購買模型限制 (預覽)

> [!IMPORTANT]
> 如需以 DTU 為基礎的購買模型限制，請參閱 [SQL Database 以 DTU 為基礎的資源限制](sql-database-dtu-resource-limits.md)。

## <a name="single-database-storage-sizes-and-performance-levels"></a>單一資料庫：儲存體大小與效能層級

對於單一資料庫，下表顯示了單一資料庫在每個服務層和效能層級的可用資源。 您可以使用 [Azure 入口網站](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal)、[TRANSACT-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql)、[PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell)、[Azure CLI](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli) 或 [REST API](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api) 來為單一資料庫設定服務層、效能層級和儲存體數量。

### <a name="general-purpose-service-tier"></a>一般用途服務層

#### <a name="generation-4-compute-platform"></a>第 4 代計算平台
|效能等級|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|H/W 產生|4|4|4|4|4|4|
|虛擬核心|1|2|4|8|16|24|
|記憶體 (GB)|7|14|28|56|112|168|
|資料行存放區支援|yes|yes|yes|yes|yes|yes|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|儲存體類型|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|
|IO 延遲 (大約)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|
|資料大小上限 (GB)|1024|1024|1536|3072|4096|4096|
|記錄大小上限|307|307|461|922|1229|1229|
|TempDB 大小 (DB)|32|64|128|256|384|384|
|目標 IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|並行背景工作 (要求) 數上限|200|400|800|1600|3200|4800|
|允許的工作階段數上限|30000|30000|30000|30000|30000|30000|
|複本數目|1|1|1|1|1|1|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|000
|讀取向外延展|N/A|N/A|N/A|N/A|N/A|N/A|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|
|||

#### <a name="generation-5-compute-platform"></a>第 5 代計算平台
|效能等級|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_48| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|H/W 產生|5|5|5|5|5|5|5|
|虛擬核心|2|4|8|16|24|32|48|80|
|記憶體 (GB)|11|22|44|88|132|176|264|440|
|資料行存放區支援|yes|yes|yes|yes|yes|yes|yes|yes|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|儲存體類型|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|
|IO 延遲 (大約)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|
|資料大小上限 (GB)|1024|1024|1536|3072|4096|4096|4096|4096|
|記錄大小上限|307|307|461|614|1229|1229|1229|1229|
|TempDB 大小 (DB)|64|128|256|384|384|384|384|384|
|目標 IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|並行背景工作 (要求) 數上限|200|400|800|1600|2400|3200|4800|8000|
|允許的工作階段數上限|30000|30000|30000|30000|30000|30000|30000|30000|
|複本數目|1|1|1|1|1|1|1|1|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|
|||

### <a name="business-critical-service-tier"></a>業務關鍵服務層

#### <a name="generation-4-compute-platform"></a>第 4 代計算平台
|效能等級|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W 產生|4|4|4|4|4|4|
|虛擬核心|1|2|4|8|16|24|
|記憶體 (GB)|7|14|28|56|112|168|
|資料行存放區支援|yes|yes|yes|yes|yes|yes|
|OLTP 記憶體內部儲存體 (GB)|1|2|4|8|20|36|
|儲存體類型|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|
|資料大小上限 (GB)|1024|1024|1024|1024|1024|1024|
|記錄大小上限|307|307|307|307|307|307|
|TempDB 大小 (DB)|32|64|128|256|384|384|
|目標 IOPS (64 KB)|5000|10000|20000|40000|80000|120000|
|IO 延遲 (大約)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|
|並行背景工作 (要求) 數上限|200|400|800|1600|3200|4800|
|允許的工作階段數上限|30000|30000|30000|30000|30000|30000|
|複本數目|3|3|3|3|3|3|
|多重 AZ|yes|yes|yes|yes|yes|yes|
|讀取向外延展|yes|yes|yes|yes|yes|yes|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|
|||

#### <a name="generation-5-compute-platform"></a>第 5 代計算平台
|效能等級|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_48|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|H/W 產生|5|5|5|5|5|5|5|5|
|虛擬核心|2|4|8|16|24|32|48|80|
|記憶體 (GB)|11|22|44|88|132|176|264|440|
|資料行存放區支援|yes|yes|yes|yes|yes|yes|yes|yes|
|OLTP 記憶體內部儲存體 (GB)|1.571|3.142|6.284|15.768|25.252|37.936|68.104|131.64|
|儲存體類型|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|
|IO 延遲 (大約)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|
|資料大小上限 (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|記錄大小上限|307|307|307|307|614|1229|1229|1229|
|TempDB 大小 (DB)|64|128|256|384|384|384|384|384|
|目標 IOPS (64 KB)|5000|10000|20000|40000|60000|80000|120000|200000
|並行背景工作 (要求) 數上限|200|400|800|1600|2400|3200|4800|8000|
|允許的工作階段數上限|30000|30000|30000|30000|30000|30000|30000|30000|
|複本數目|1|1|1|1|1|1|1|1|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|
|||

## <a name="single-database-change-storage-size"></a>單一資料庫：變更儲存體大小

- 可以使用 1GB 為增量單位，將儲存體佈建到大小上限。 資料儲存體的最小可設定值是 5 GB 
- 藉由使用 [Azure 入口網站](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal)、[Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)、[Azure CLI](/cli/azure/sql/db#az_sql_db_update) 或 [REST API](/rest/api/sql/databases/update) 增加或減少其大小上限，即可佈建單一資料庫的儲存體。
- SQL Database 會自動為記錄檔配置 30% 的額外儲存體，為 TempDB 的每個虛擬核心配置 32GB 儲存體，但不超過 384GB。 TempDB 位於所有服務層中的已連結 SSD 上。
- 單一資料庫的儲存體價格為資料儲存體和記錄儲存體數量的總和乘以服務層的儲存體單價。 TempDB 的成本包含在虛擬核心價格內。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

## <a name="single-database-change-vcores"></a>單一資料庫：變更虛擬核心

一開始選取虛擬核心數目之後，您可以根據實際經驗，使用 [Azure 入口網站](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal)、[Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)、[Azure CLI](/cli/azure/sql/db#az_sql_db_update) 或 [REST API](/rest/api/sql/databases/update) 來動態地相應增加或相應減少單一資料庫。 

變更資料庫的服務層和/或效能層級會在新的效能層級建立原始資料庫的複本，然後將連接切換到複本。 此程序期間不會遺失任何資料，但在我們切換到複本的短暫期間，資料庫的連接會停用，因此執行中的某些交易可能會回復。 切換的時間長度會有所不同，但通常是在 4 秒以下，而 99% 的時間會小於 30 秒。 如果在連線停用時正在執行大型交易，則切換的時間長度可能會更長。 

整個向上調整程序的期間取決於資料庫變更前後的大小和服務層。 例如，在一般用途服務層內進行變更的 250 GB 資料庫應在六小時內完成。 對於業務關鍵服務層內變更效能層級的相同大小資料庫，應該在三小時內完成相應增加。

> [!TIP]
> 若要監視進行中的作業，請參閱：[使用 SQL REST API 管理作業](/rest/api/sql/Operations/List)、[使用 CLI 管理作業](/cli/azure/sql/db/op)、[使用 T-SQL 監視作業](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)，以及下列兩個 PowerShell 命令：[Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) 和 [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)。

* 如果您要升級到較高服務層或效能層級，除非您明確指定較大的大小 (大小上限)，否則資料庫大小上限不會增加。
* 若要將資料庫降級，資料庫已用的空間必須小於目標服務層和效能層級允許的大小上限。 
* 升級資料庫時，若已啟用[異地複寫](sql-database-geo-replication-portal.md)，您必須先將其次要資料庫升級為所需的效能層，然後再升級主要資料庫 (最佳效能的一般指引)。 升級至不同的版本時，必須先升級次要資料庫。
* 當您在啟用[異地複寫](sql-database-geo-replication-portal.md)的情況下將資料庫降級時，必須先將其主要資料庫降級為所需的效能層級，然後再將次要資料庫降級 (最佳效能的一般指引)。 降級至不同的版本時，必須先降級主要資料庫。
* 完成變更之前，不會將新屬性套用至資料庫。

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>彈性集區：儲存體大小與效能層級

對於 SQL Database 彈性集區，下表顯示了每個服務層和效能層級的可用資源。 您可以使用 [Azure 入口網站](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal)、[PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell)、[Azure CLI](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli) 或 [REST API](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api)，設定服務層、效能層級和儲存體數量。

> [!NOTE]
> 彈性集區中個別資料庫的資源限制通常與集區外部具有相同效能層級的單一資料庫資源限制相同。 例如，GP_Gen4_1 資料庫的並行背景工作上限是 200 個背景工作。 因此，GP_Gen4_1 集區中資料庫的並行背景工作上限也是 200 個背景工作。 請注意，GP_Gen4_1 集區中的並行背景工作總數為 210。

### <a name="general-purpose-service-tier"></a>一般用途服務層

#### <a name="generation-4-compute-platform"></a>第 4 代計算平台
|效能等級|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W 產生|4|4|4|4|4|4|
|虛擬核心|1|2|4|8|16|24|
|記憶體 (GB)|7|14|28|56|112|168|
|資料行存放區支援|yes|yes|yes|yes|yes|yes|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|儲存體類型|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|
|資料大小上限 (GB)|512|756|1536|2048|3584|4096|
|記錄大小上限|154|227|461|614|1075|1229|
|TempDB 大小 (DB)|32|64|128|256|384|384|
|目標 IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|IO 延遲 (大約)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|
|並行背景工作 (要求) 數上限|210|420|840|1680|3360|5040|
|允許的工作階段數上限|30000|30000|30000|30000|30000|30000|
|集區密度上限|100|200|500|500|500|500|
|最小/最大彈性集區點擊停止|0, 0.25, 0.5, 1|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0.25, 0.5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|
|複本數目|1|1|1|1|1|1|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|N/A|N/A|N/A|N/A|N/A|N/A|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|
|||

#### <a name="generation-5-compute-platform"></a>第 5 代計算平台
|效能等級|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_48|GP_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|H/W 產生|5|5|5|5|5|5|5|5|
|虛擬核心|2|4|8|16|24|32|48|80|
|記憶體 (GB)|11|22|44|88|132|176|264|440|
|資料行存放區支援|yes|yes|yes|yes|yes|yes|yes|yes|
|OLTP 記憶體內部儲存體 (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|儲存體類型|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|進階 (遠端) 儲存體|
|資料大小上限 (GB)|512|756|1536|2048|3072|4096|4096|4096|
|記錄大小上限|154|227|461|614|922|1229|1229|1229|
|TempDB 大小 (DB)|64|128|256|384|384|384|384|384|
|目標 IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|IO 延遲 (大約)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|5-7 毫秒 (寫入)<br>5-10 毫秒 (讀取)|
|並行背景工作 (要求) 數上限|210|420|840|1680|2520|3360|5040|8400
|允許的工作階段數上限|30000|30000|30000|30000|30000|30000|30000|30000|
|集區密度上限|100|200|500|500|500|500|500|500|
|最小/最大彈性集區點擊停止|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0.25, 0.5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|0, 0.5, 1, 2, 4, 8, 16, 24, 32|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 48|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 48, 80|
|複本數目|1|1|1|1|1|1|1|1|
|多重 AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|讀取向外延展|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|
|||

### <a name="business-critical-service-tier"></a>業務關鍵服務層

#### <a name="generation-4-compute-platform"></a>第 4 代計算平台
|效能等級|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W 產生|4|4|4|4|4|4|
|虛擬核心|1|2|4|8|16|24|
|記憶體 (GB)|7|14|28|56|112|168|
|資料行存放區支援|yes|yes|yes|yes|yes|yes|
|OLTP 記憶體內部儲存體 (GB)|1|2|4|8|20|36|
|儲存體類型|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|
|資料大小上限 (GB)|1024|1024|1024|1024|1024|1024|
|記錄大小上限|307|307|307|307|307|307|
|TempDB 大小 (DB)|32|64|128|256|384|384|
|目標 IOPS (64 KB)|5000|10000|20000|40000|80000|120000|
|IO 延遲 (大約)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|
|並行背景工作 (要求) 數上限|210|420|840|1680|3360|5040|
|允許的工作階段數上限|30000|30000|30000|30000|30000|30000|
|集區密度上限|N/A|50|100|100|100|100|
|最小/最大彈性集區點擊停止|N/A|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0.25, 0.5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|
|多重 AZ|yes|yes|yes|yes|yes|yes|
|讀取向外延展|yes|yes|yes|yes|yes|yes|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|
|||

#### <a name="generation-5-compute-platform"></a>第 5 代計算平台
|效能等級|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_48|BC_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|H/W 產生|5|5|5|5|5|5|5|5|
|虛擬核心|2|4|8|16|24|32|48|80|
|記憶體 (GB)|11|22|44|88|132|176|264|440|
|資料行存放區支援|yes|yes|yes|yes|yes|yes|yes|yes|
|OLTP 記憶體內部儲存體 (GB)|1.571|3.142|6.284|15.768|25.252|37.936|68.104|131.64|
|儲存體類型|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|本機 SSD|
|IO 延遲 (大約)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|1-2 毫秒 (寫入)<br>1-2 毫秒 (讀取)|
|資料大小上限 (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|記錄大小上限|307|307|307|307|614|1229|1229|1229|
|TempDB 大小 (DB)|64|128|256|384|384|384|384|384|
|目標 IOPS (64 KB)|5000|10000|20000|40000|60000|80000|120000|200000
|並行背景工作 (要求) 數上限|210|420|840|1680|2520|3360|5040|8400|
|允許的工作階段數上限|30000|30000|30000|30000|30000|30000|30000|30000|
|集區密度上限|N/A|50|100|100|100|100|100|100|
|最小/最大彈性集區點擊停止|N/A|0, 0.25, 0.5, 1, 2, 4|0, 0.25, 0.5, 1, 2, 4, 8|0, 0.25, 0.5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|0, 0.5, 1, 2, 4, 8, 16, 24, 32|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 48|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 48, 80|
|多重 AZ|yes|yes|yes|yes|yes|yes|yes|yes|
|讀取向外延展|yes|yes|yes|yes|yes|yes|yes|yes|
|內含備份儲存體|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|1X DB 大小|
|||

如果彈性集區的所有虛擬核心都是忙碌中，則集區中的每個資料庫會收到等量的計算資源以處理查詢。 SQL Database 服務藉由確保運算時間的均等配量，提供資料庫之間的資源共用公平性。 彈性集區資源共用公平性不包括任何資源數量，否則當每個資料庫的最小虛擬核心數設為非零的值時，便會對每個資料庫保證資源數量。

### <a name="database-properties-for-pooled-databases"></a>集區資料庫的資料庫屬性

下表描述集區資料表的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| 每個資料庫的虛擬核心上限 |集區中任何資料庫可以使用的虛擬核心數目上限，是否可用則是根據集區中其他資料庫的使用量而定。 每個資料庫的虛擬核心數目上限不等於資料庫的資源保證。 這個設定是全域設定，會套用至集區中的所有資料庫。 將每個資料庫的虛擬核心設定為最上限，以處理資料庫使用率的尖峰。 某種程度的過量使用是可預期的情況，因為集區通常會假設資料庫的熱門和冷門使用模式；在這些模式中，所有資料庫不會同時處於尖峰期。|
| 每個資料庫的虛擬核心下限 |集區中單一資料庫能夠保證的虛擬核心數下限。 這個設定是全域設定，會套用至集區中的所有資料庫。 每個資料庫最小虛擬核心建議設定為 0，同時也是預設值。 此屬性會設為 0 到每一資料庫的虛擬核心使用量平均值之間的任意數。 集區中資料庫數目和每個資料庫虛擬核心數目下限的乘積不能超過每個集區的虛擬核心。|
| 每個資料庫的儲存體上限 |使用者所設定集區資料庫的資料庫大小上限。 集區資料庫會共用配置的集區儲存體，所以資料庫可以觸達的大小會限制為較小的剩餘集區儲存體和資料庫大小。 資料庫大小上限是指資料檔案的大小上限，並不包含記錄檔所使用的空間。 |
|||
 
## <a name="elastic-pool-change-storage-size"></a>彈性集區：變更儲存體大小

- 可以將儲存體佈建到大小上限： 
 - 針對標準儲存體，以 10 GB 為增量單位增加或減少大小
 - 針對進階儲存體，以 250 GB 為增量單位增加或減少大小
- 藉由使用 [Azure 入口網站](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)、[Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) 或 [REST API](/rest/api/sql/elasticpools/update) 增加或減少其大小上限，即可佈建彈性集區的儲存體。
- 彈性集區儲存體的價格為儲存體數量乘以服務層的儲存體單價。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

## <a name="elastic-pool-change-vcores"></a>彈性集區：變更虛擬核心

您可以使用 [Azure 入口網站](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal)、[PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)、[Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) 或 [REST API](/rest/api/sql/elasticpools/update)，根據資源需求來增加或減少彈性集區的效能層級。

- 重新調整集區虛擬核心時，會短暫中斷資料庫連線。 在為單一資料庫 (而非在集區中) 重新調整 DTU 時，也會發生相同的行為。 如需重新調整作業期間，資料庫中斷連線的持續時間和影響的詳細資訊，請參閱[重新調整單一資料庫的 DTU](#single-database-change-storage-size)。 
- 重新調整集區虛擬核心的持續時間可能取決於集區中所有資料庫使用的儲存空間總量。 一般情況下，重新調整延遲時間平均為每 100 GB 在 90 分鐘以內。 舉例來說，如果集區中所有資料庫使用的總空間為 200 GB，則重新調整集區的預期延遲時間為 3 小時內。 在標準層或基本層內的某些情況下，不論使用的空間量多寡，重新調整延遲時間可能會少於五分鐘。
- 一般而言，變更每個資料庫之虛擬核心下限或每個資料庫的虛擬核心上限的持續時間會在五分鐘內。
- 縮減集區虛擬核心的大小時，集區使用的空間必須小於目標服務層和集區虛擬核心允許的大小上限。

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

### <a name="compute-vcores"></a>計算 (虛擬核心)

當資料庫計算使用率 (根據虛擬核心使用率來測量) 變高時，查詢延遲會增加，甚至可能逾時。在下列情況下，查詢可能會透過服務排入佇列，並在資源變成可用時，提供資源以供執行。
遇到高計算使用率時，緩和選項包括：

- 提高資料庫或彈性集區的效能層級，提供更多虛擬核心給資料庫。 請參閱[單一資料庫：變更虛擬核心](#single-database-change-vcores)與[彈性集區：變更虛擬核心](#elastic-pool-change-vcores)。
- 將查詢最佳化，以降低每個查詢的資源使用率。 如需詳細資訊，請參閱[查詢微調/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。

### <a name="storage"></a>儲存體

當使用的資料庫空間達到大小上限，若資料庫的插入和更新作業會增加資料大小，動作即會失敗，且用戶端會收到[錯誤訊息](sql-database-develop-error-messages.md)。 資料庫 SELECTS 與 DELETES 會繼續執行下去。

遇到高空間使用率時，緩和選項包括：

- 增加資料庫或彈性集區的大小上限，或變更效能層級以增加儲存體的上限。 請參閱 [SQL Database 以虛擬核心為基礎的資源限制](sql-database-vcore-resource-limits.md)。
- 如果資料庫在彈性集區，也可以將資料庫移出集區，如此便不會與其他資料庫共用儲存空間。

### <a name="sessions-and-workers-requests"></a>工作階段和背景工作角色 (要求) 

工作階段與背景工作角色的數量上限取決於服務層和效能層級。 達到工作階段或背景工作角色的限制時，會拒絕新要求，而且用戶端會收到錯誤訊息。 雖然應用程式能控制可用的連線數目，但並行背景工作角色的數目通常難以預估及控制。 特別是在尖峰負載期間，資料庫資源達到上限，背景工作角色也因為長時間執行查詢而不斷累積。 

當工作階段或背景工作角色出現高使用率時，緩和選項包括：
- 提高資料庫或彈性集區的服務層或效能層級。 請參閱[單一資料庫：變更儲存體大小](#single-database-change-storage-size)、[單一資料庫：變更虛擬核心](#single-database-change-vcores)、[彈性集區：變更儲存體大小](#elastic-pool-change-storage-size)，以及[彈性集區：變更虛擬核心](#elastic-pool-change-vcores)。
- 如果背景工作角色的使用率增加是爭用計算資源所造成，則可將查詢最佳化，以減少每個查詢的資源使用率。 如需詳細資訊，請參閱[查詢微調/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。

## <a name="next-steps"></a>後續步驟

- 請參閱 [SQL Database 常見問題集](sql-database-faq.md)以取得常見問題的解答。
- 如需一般 Azure 限制的相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)。
