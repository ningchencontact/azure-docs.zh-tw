---
title: 調整單一資料庫資源 - Azure SQL Database | Microsoft Docs
description: 本文說明如何在 Azure SQL Database 中調整單一資料庫可用的計算和儲存資源。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: 311015aff5ea7020043ad8e43fd987144cdcbf52
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206742"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>在 Azure SQL Database 中調整單一資料庫資源

本文說明如何在佈建的計算層中調整適用於單一資料庫的計算和儲存體資源。 或者，[無伺服器 （預覽） 計算層次](sql-database-serverless.md)提供計算自動調整和每秒使用的計算的帳單。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

## <a name="change-compute-size-vcores-or-dtus"></a>變更計算大小 （虛擬核心或 Dtu）

一開始選取的虛擬核心或 Dtu 數目之後, 您可以單一資料庫相應增加或相應減少以動態方式根據實際經驗，使用[Azure 入口網站](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)， [TRANSACT-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)， [PowerShell](/powershell/module/az.sql/set-azsqldatabase)，則[Azure CLI](/cli/azure/sql/db#az-sql-db-update)，或有[REST API](https://docs.microsoft.com/rest/api/sql/databases/update)。

下列影片示範如何動態變更服務層與計算大小，以提高單一資料庫的可用 DTU。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>變更服務層或重新調整計算大小的影響

變更服務層，或計算的單一資料庫的大小主要牽涉的服務，執行下列步驟：

1. 建立新的計算執行個體資料庫  

    新的計算執行個體，資料庫會建立要求的服務層與計算大小。 服務層和計算的大小變更的一些組合，資料庫的複本必須建立新的計算執行個體這牽涉到將資料複製，並會強烈影響的整體延遲。 不論如何，在此步驟中，在資料庫保持上線，連接可以繼續以導向至原始的計算執行個體中的資料庫。

2. 切換到新的計算執行個體的連線的路由

    會卸除現有的連線至原始的計算執行個體中的資料庫。 資料庫會建立任何新的連線，新的計算執行個體中。 服務層和計算的大小變更的一些組合，資料庫檔案中斷連結，並在切換期間重新附加上。  不論如何，此參數可能會導致短暫服務中斷時資料庫是無法使用，通常對小於 30 秒，而且通常只有幾秒鐘的時間。 如果有長時間執行交易時的連接會中斷執行，此步驟的持續時間可能更久才能復原中止的交易。 [加速資料庫復原](sql-database-accelerated-database-recovery.md)可以減少從中止長時間執行交易的影響。

> [!IMPORTANT]
> 在工作流程中的任何步驟期間，不會遺失任何資料。

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>變更服務層或重新調整計算大小的延遲

若要變更服務層，或重新調整單一資料庫或彈性集區的計算大小估計的延遲是經過參數化，如下所示：

|服務層|基本的單一資料庫，</br>標準 (S0 S1)|基本的彈性集區</br>標準 (S2-S12) </br>超大規模 </br>一般用途的單一資料庫或彈性集區|進階或業務關鍵的單一資料庫或彈性集區|
|:---|:---|:---|:---|
|**基本的單一資料庫，</br>標準 (S0 S1)**|&bull; &nbsp;獨立的空間使用的常數時間延遲</br>&bull; &nbsp;一般而言，小於 5 分鐘|&bull; &nbsp;使用複製資料的資料庫空間成正比的延遲</br>&bull; &nbsp;一般而言，小於 1 分鐘，每 GB 的可用空間|&bull; &nbsp;使用複製資料的資料庫空間成正比的延遲</br>&bull; &nbsp;一般而言，小於 1 分鐘，每 GB 的可用空間|
|**基本的彈性集區</br>標準 (S2-S12)</br>超大規模</br>一般用途的單一資料庫或彈性集區**|&bull; &nbsp;使用複製資料的資料庫空間成正比的延遲</br>&bull; &nbsp;一般而言，小於 1 分鐘，每 GB 的可用空間|&bull; &nbsp;獨立的空間使用的常數時間延遲</br>&bull; &nbsp;一般而言，小於 5 分鐘|&bull; &nbsp;使用複製資料的資料庫空間成正比的延遲</br>&bull; &nbsp;一般而言，小於 1 分鐘，每 GB 的可用空間|
|**進階或業務關鍵的單一資料庫或彈性集區**|&bull; &nbsp;使用複製資料的資料庫空間成正比的延遲</br>&bull; &nbsp;一般而言，小於 1 分鐘，每 GB 的可用空間|&bull; &nbsp;使用複製資料的資料庫空間成正比的延遲</br>&bull; &nbsp;一般而言，小於 1 分鐘，每 GB 的可用空間|&bull; &nbsp;使用複製資料的資料庫空間成正比的延遲</br>&bull; &nbsp;一般而言，小於 1 分鐘，每 GB 的可用空間|

> [!TIP]
> 若要監視進行中的作業，請參閱：[使用 SQL REST API 管理作業](https://docs.microsoft.com/rest/api/sql/operations/list)、[使用 CLI 管理作業](/cli/azure/sql/db/op)、[使用 T-SQL 監視作業](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)，以及下列兩個 PowerShell 命令：[取得 AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity)並[停止 AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity)。

### <a name="cancelling-service-tier-changes-or-compute-rescaling-operations"></a>取消服務層變更計算或重新調整作業

計算可以取消重新調整作業或變更服務層。

#### <a name="azure-portal"></a>Azure 入口網站

在 [資料庫概觀] 刀鋒視窗中，瀏覽至**通知**，然後按一下 [] 圖格，指出有進行中的作業：

![進行中作業](media/sql-database-single-database-scale/ongoing-operations.png)

接下來，按一下 [] 按鈕**取消此作業**。

![取消進行中作業](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

在 PowerShell 命令提示字元中，設定`$ResourceGroupName`， `$ServerName`，和`$DatabaseName`，然後執行下列命令：

```PowerShell
$OperationName = (az sql db op list --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --query "[?state=='InProgress'].name" --out tsv)
if(-not [string]::IsNullOrEmpty($OperationName))
    {
        (az sql db op cancel --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --name $OperationName)
        "Operation " + $OperationName + " has been canceled"
    }
    else
    {
        "No service tier change or compute rescaling operation found"
    }
```

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>其他考量事項變更服務層或重新調整運算大小

- 如果您要升級到較高服務層或計算大小，除非明確指定較大的大小 (大小上限)，否則資料庫大小上限不會增加。
- 若要將資料庫降級，資料庫已用的空間必須小於目標服務層和計算大小允許的大小上限。
- 從**進階**降級至**標準**層時，如果發生下列情況，將會產生額外的儲存體成本：(1) 以目標計算大小支援資料庫的大小上限，而且 (2) 大小上限超過目標計算大小的內含儲存體數量。 例如，如果大小上限為 500 GB 的 P1 資料庫縮減為 S3，即會產生額外的儲存體成本，因為 S3 可支援 500 GB 的大小上限，而且其內含儲存體數量只有 250 GB。 因此，額外的儲存體數量為 500 GB – 250 GB = 250 GB。 如需額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。 如果實際的使用空間量小於內含的儲存體數量，則可將資料庫大小上限降低至內含量，以避免造成額外成本。
- 升級資料庫時，若已啟用[異地複寫](sql-database-geo-replication-portal.md)，您必須先將其次要資料庫升級為所需的服務層與計算大小，然後再升級主要資料庫 (最佳效能的一般指引)。 升級至不同的版本時，必須先升級次要資料庫。
- 當您在啟用[異地複寫](sql-database-geo-replication-portal.md)的情況下將資料庫降級時，必須先將其主要資料庫降級為所需的服務層與計算大小，然後再將次要資料庫降級 (最佳效能的一般指引)。 降級至不同的版本時，必須先降級主要資料庫。
- 還原服務會針對各種服務層提供不同的供應項目。 如果降級至**基本**層，會有較短的備份保留期。 請參閱 [Azure SQL Database 備份](sql-database-automated-backups.md)。
- 完成變更之前，不會將新屬性套用至資料庫。

### <a name="billing-during-compute-rescaling"></a>計費期間重新調整計算

您需要支付使用最高服務層資料庫存在的時數 + 在該小時適用的計算大小，不論使用狀況或資料庫是否在作用中少於一小時。 例如，假設您建立了單一資料庫並在五分鐘後刪除，您的帳單就會反映一個資料庫時數的費用。

## <a name="change-storage-size"></a>變更儲存體大小

### <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

- 可以使用 1GB 為增量單位，將儲存體佈建到大小上限。 資料儲存體的最小可設定值是 5 GB
- 藉由使用 [Azure 入口網站](https://portal.azure.com)、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、[PowerShell](/powershell/module/az.sql/set-azsqldatabase)、[Azure CLI](/cli/azure/sql/db#az-sql-db-update) 或 [REST API](https://docs.microsoft.com/rest/api/sql/databases/update) 增加或減少其大小上限，即可佈建單一資料庫的儲存體。
- SQL Database 會自動為記錄檔配置 30% 的額外儲存體，為 TempDB 的每個虛擬核心配置 32GB 儲存體，但不超過 384GB。 TempDB 位於所有服務層中的已連結 SSD 上。
- 單一資料庫的儲存體價格為資料儲存體和記錄儲存體數量的總和乘以服務層的儲存體單價。 TempDB 的成本包含在虛擬核心價格內。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

### <a name="dtu-based-purchasing-model"></a>以 DTU 為基礎的購買模型

- 單一資料庫的 DTU 價格包含一定數量不額外收費的儲存體。 佈建超過內含量的額外儲存體會產生額外費用，以 250 GB 為單位最多增加到大小上限 1 TB，超過 1 TB 則以 256 GB 為單位增加。 如需了解內含儲存體數量與大小上限，請參閱[單一資料庫：儲存體大小與計算大小](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)。
- 可以透過使用 Azure 入口網站、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、[PowerShell](/powershell/module/az.sql/set-azsqldatabase)、[Azure CLI](/cli/azure/sql/db#az-sql-db-update) 或 [REST API](https://docs.microsoft.com/rest/api/sql/databases/update) 增加其大小上限，以佈建單一資料庫的額外儲存體。
- 單一資料庫之額外儲存體的價格為額外儲存體數量乘以服務層的額外儲存體單價。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>P11 和 P15 的限制時最大大小大於 1 TB

所有區域目前均可使用進階層中超過 1 TB 的儲存體，但下列區域除外：中國東部、中國北部、德國中部、德國東北部、美國中西部、美國 DoD 區域和美國政府中部。 在這些區域中，進階層中的儲存空間上限為 1 TB。 下列考量與限制適用於大小上限大於 1 TB 的 P11 和 P15 資料庫：

- 如果 P11 或 P15 資料庫大小上限曾經設定為值大於 1 TB，則它只能還原或複製到 P11 或 P15 資料庫。  接下來，資料庫可以會重新調整為不同的計算大小重新調整作業的時間在配置的空間數量未超過大小上限，新的計算大小的提供。
- 若是作用中異地複寫案例：
  - 設定異地複寫關聯性：如果 P11 或 P15 為主要資料庫，則次要資料庫也必須是 P11 或 P15；系統會拒絕使用較低的計算大小作為次要資料庫，因為這些資料庫無法支援 1 TB 以上的大小。
  - 在異地複寫關聯性中升級主要資料庫：將主要資料庫的大小上限變更為 1 TB 以上會在次要資料庫中觸發相同的變更。 這兩種升級方式都必須成功，在主要資料庫中的變更才會生效。 適用於大於 1-TB 選項的區域限制。 如果次要資料庫位於不支援大於 1 TB 的區域，就無法升級主要資料庫。
- 不支援使用匯入/匯出服務載入具有 1 TB 以上之 P11/P15 資料庫的功能。 使用 SqlPackage.exe 來[匯入](sql-database-import.md)和[匯出](sql-database-export.md)資料。

## <a name="next-steps"></a>後續步驟

如需整體資源限制，請參閱[SQL Database 以虛擬核心為基礎的資源限制 - 單一資料庫](sql-database-vcore-resource-limits-single-databases.md)和[SQL Database 以 DTU 為基礎的資源限制 - 彈性集區](sql-database-dtu-resource-limits-single-databases.md)。
