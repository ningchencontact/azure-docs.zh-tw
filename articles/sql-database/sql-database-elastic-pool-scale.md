---
title: 調整彈性集區資源 - Azure SQL Database | Microsoft Docs
description: 此頁面將針對 Azure SQL Database 中的彈性集區說明調整資源。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 3/14/2019
ms.openlocfilehash: f73fc58abfa6cde4133bd56858b7f26bf0c3d4a3
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204821"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>在 Azure SQL Database 中調整彈性集區

本文說明如何在 Azure SQL Database 中調整彈性集區和集區資料庫可用的計算和儲存資源。

## <a name="change-compute-resources-vcores-or-dtus"></a>變更計算資源 （虛擬核心或 Dtu）

一開始選取的虛擬核心或 Edtu 數目之後, 您可以調整彈性集區增加或相應減少以動態方式根據實際經驗，使用[Azure 入口網站](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)， [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)，則[Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)，則[REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update)。

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>變更服務層或重新調整計算大小的影響

變更服務層，或計算的彈性集區的大小類似的模式與單一資料庫和主要牽涉的服務，執行下列步驟：

1. 建立新的彈性集區的計算執行個體  

    要求的服務層與計算大小，會建立新的計算執行個體的彈性集區。 服務層和計算的大小變更的一些組合，每個資料庫的複本必須建立新的計算執行個體這牽涉到將資料複製，並會強烈影響的整體延遲。 不論如何，資料庫期間維持線上狀態此步驟中，並連接可以繼續導向至原始的計算執行個體中的資料庫。

2. 切換到新的計算執行個體的連線的路由

    會卸除現有的連接，原始的計算執行個體中的資料庫。 資料庫會建立任何新的連線，新的計算執行個體中。 服務層和計算的大小變更的一些組合，資料庫檔案中斷連結，並在切換期間重新附加上。  不論如何，此參數可能會導致短暫服務中斷資料庫時無法使用，通常為小於 30 秒，通常只有幾秒鐘的時間。 如果有長時間執行交易時的連接會中斷執行，此步驟的持續時間可能更久才能復原中止的交易。 [加速資料庫復原](sql-database-accelerated-database-recovery.md)可以減少從中止長時間執行交易的影響。

> [!IMPORTANT]
> 在工作流程中的任何步驟期間，不會遺失任何資料。

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>變更服務層或重新調整計算大小的延遲

若要變更服務層，或重新調整單一資料庫或彈性集區的計算大小估計的延遲是經過參數化，如下所示：

|服務層|基本的單一資料庫，</br>標準 (S0 S1)|基本的彈性集區</br>標準 (S2-S12) </br>超大規模 </br>一般用途的單一資料庫或彈性集區|進階或業務關鍵的單一資料庫或彈性集區|
|:---|:---|:---|:---|
|**基本的單一資料庫，</br>標準 (S0 S1)**|&bull; &nbsp;獨立的空間使用的常數時間延遲</br>&bull; &nbsp;一般而言，小於 5 分鐘|&bull; &nbsp;使用複製資料的資料庫空間成正比的延遲</br>&bull; &nbsp;一般而言，小於 1 分鐘，每 GB 的可用空間|&bull; &nbsp;使用複製資料的資料庫空間成正比的延遲</br>&bull; &nbsp;一般而言，小於 1 分鐘，每 GB 的可用空間|
|**基本的彈性集區</br>標準 (S2-S12)</br>超大規模</br>一般用途的單一資料庫或彈性集區**|&bull; &nbsp;使用複製資料的資料庫空間成正比的延遲</br>&bull; &nbsp;一般而言，小於 1 分鐘，每 GB 的可用空間|&bull; &nbsp;獨立的空間使用的常數時間延遲</br>&bull; &nbsp;一般而言，小於 5 分鐘|&bull; &nbsp;使用複製資料的資料庫空間成正比的延遲</br>&bull; &nbsp;一般而言，小於 1 分鐘，每 GB 的可用空間|
|**進階或業務關鍵的單一資料庫或彈性集區**|&bull; &nbsp;使用複製資料的資料庫空間成正比的延遲</br>&bull; &nbsp;一般而言，小於 1 分鐘，每 GB 的可用空間|&bull; &nbsp;使用複製資料的資料庫空間成正比的延遲</br>&bull; &nbsp;一般而言，小於 1 分鐘，每 GB 的可用空間|&bull; &nbsp;使用複製資料的資料庫空間成正比的延遲</br>&bull; &nbsp;一般而言，小於 1 分鐘，每 GB 的可用空間|

> [!NOTE]
>
> - 如果是變更服務層，或重新調整彈性集區的計算，在集區中的所有資料庫使用的空間總和應該用於計算估計值。
> - 在移動資料庫從彈性集區，只將資料庫所使用的空間會影響延遲，而不使用彈性集區的空間。
>
> [!TIP]
> 若要監視進行中的作業，請參閱：[使用 SQL REST API 管理作業](https://docs.microsoft.com/rest/api/sql/operations/list)、[使用 CLI 管理作業](/cli/azure/sql/db/op)、[使用 T-SQL 監視作業](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)，以及下列兩個 PowerShell 命令：[取得 AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity)並[停止 AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity)。

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>其他考量事項變更服務層或重新調整運算大小

- 虛擬核心或彈性集區的 Edtu，縮減大小時，集區使用的空間必須小於允許的目標服務層和集區 Edtu 大小上限。
- 重新調整時的虛擬核心或彈性集區的 Edtu，如果集區 （1） 的儲存體最大大小支援的目標集區中，而且 （2） 儲存體大小上限超過目標集區的內含儲存體數量，適用於產生額外的儲存體成本。 例如，如果大小上限為 100 GB 的 100 eDTU 標準集區縮減為 50 eDTU 標準集區，則會產生額外的儲存體成本，因為目標集區可支援 100 GB 的大小上限，而且其內含儲存體數量只有 50 GB。 因此，額外的儲存體數量為 100 GB – 50 GB = 50 GB。 如需額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。 如果實際的使用空間量小於內含的儲存體數量，則可將資料庫大小上限降低至內含量，以避免造成額外成本。

### <a name="billing-during-rescaling"></a>重新調整期間的計費

您需要支付使用最高服務層資料庫存在的時數 + 在該小時適用的計算大小，不論使用狀況或資料庫是否在作用中少於一小時。 例如，假設您建立了單一資料庫並在五分鐘後刪除，您的帳單就會反映一個資料庫時數的費用。

## <a name="change-elastic-pool-storage-size"></a>變更彈性集區儲存體大小

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

### <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

- 可以將儲存體佈建到大小上限：

  - 針對標準或一般用途服務層級中的儲存體，增加或減少大小 (以 10 GB 為增量單位)
  - 針對進階或商務關鍵性服務層級中的儲存體，增加或減少大小 (以 250 GB 為增量單位)
- 增加或減少其大小上限，即可佈建彈性集區的儲存體。
- 彈性集區儲存體的價格為儲存體數量乘以服務層的儲存體單價。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

### <a name="dtu-based-purchasing-model"></a>以 DTU 為基礎的購買模型

- 彈性集區的 eDTU 價格包含一定數量不額外收費的儲存體。 佈建超過內含量的額外儲存體會產生額外費用，以 250 GB 為單位最多增加到大小上限 1 TB，超過 1 TB 則以 256 GB 為單位增加。 如需了解內含儲存體數量和大小上限，請參閱[彈性集區：儲存體大小與計算大小](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)。
- 可藉由使用 [Azure 入口網站](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)、[PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)、[Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) 或 [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update) 增加其大小上限，以佈建彈性集區的額外儲存體。
- 彈性集區之額外儲存體的價格為額外儲存體數量乘以服務層的額外儲存體單價。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

## <a name="next-steps"></a>後續步驟

如需整體資源限制，請參閱[SQL Database 以虛擬核心為基礎的資源限制 - 彈性集區](sql-database-vcore-resource-limits-elastic-pools.md)和[SQL Database 以 DTU 為基礎的資源限制 - 彈性集區](sql-database-dtu-resource-limits-elastic-pools.md)。
