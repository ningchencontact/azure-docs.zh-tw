---
title: Azure SQL Database 服務 - DTU | Microsoft Docs
description: 深入了解單一和集區資料庫的服務層，以提供效能層級和儲存體大小。
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 05/22/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 075e04db27006719ec7d6e08eb2696436d1010f4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648644"
---
# <a name="dtu-based-purchasing-model-for-azure-sql-database"></a>適用於 Azure SQL Database 之以 DTU 為基礎的購買模型 



  [Azure SQL Database](sql-database-technical-overview.md) 針對計算、儲存體和 IO 資源提供了兩種購買模型：以 DTU 為基礎的購買模型和以虛擬核心為基礎的購買模型 (預覽)。 下列資料表和圖表會比較和對照這兩種購買模型。

> [!IMPORTANT]
> 針對以虛擬核心為基礎的購買模型 (預覽)，請參閱[以虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)。


|**購買模型**|**說明**|**適用對象**|
|---|---|---|
|以 DTU 為基礎的模型|此模型是以計算、儲存體和 IO 資源的配套量值為基礎。 單一資料庫的效能層級會以資料庫交易單位 (DTU) 表示，而彈性集區的效能層級則會以彈性資料庫交易單位 (eDTU) 表示。 如需 DTU 和 eDTU 的詳細資訊，請參閱[什麼是 DTU 和 eDTU？](sql-database-what-is-a-dtu.md)|適合想要簡單選項且該選項已預先設定好資源的客戶。| 
|以虛擬核心為基礎的模型|此模型可讓您獨立地調整計算和儲存體資源。 它還可任您使用適用於 SQL Server 的 Azure Hybrid Benefit 來節省成本。|適合重視彈性、控制力和透明度的客戶。|
||||  

![定價模型](./media/sql-database-service-tiers/pricing-model.png)

## <a name="dtu-based-purchasing-model"></a>以 DTU 為基礎的購買模型

資料庫輸送量單位 (DTU) 代表混合了 CPU、記憶體、讀取和寫入的量值。 以 DTU 為基礎的購買模型會提供一組預先設定好的計算資源組合和所包含的儲存體，以期達成不同的應用程式效能等級。 想要簡單一點，使用已預先設定好的組合並每月支付固定費用的客戶，可能會發現以 DTU 為基礎的模型更適合他們的需求。 在以 DTU 為基礎的購買模型中，客戶可以就[單一資料庫](sql-database-single-database-resources.md)與[彈性集區](sql-database-elastic-pool.md)選擇**基本**、**標準**和**進階**服務層。 服務層是以一系列效能等級來做區分，這些等級各有一定數量的內含儲存體、一定的備份保留期和一定的價格。 所有服務層皆可彈性變更效能等級而不需停機。 單一資料庫和彈性集區會根據服務層和效能等級，以每小時為單位來計費。

> [!IMPORTANT]
> SQL Database 受控執行個體 (目前為公開預覽版) 不支援以 DTU 為基礎的購買模型。 如需詳細資訊，請參閱 [Azure SQL Database 受控執行個體](sql-database-managed-instance.md)。 

## <a name="choosing-a-service-tier-in-the-dtu-based-purchasing-model"></a>在以 DTU 為基礎的購買模型中選擇服務層

服務層的選擇主要視業務持續性、儲存體和效能需求而定。
||基本|標準|進階|
| :-- | --: |--:| --:| --:| 
|目標工作負載|開發與生產|開發與生產|開發與生產||
|執行時間 SLA|99.99%|99.99%|99.99%|預覽時，N/A|
|備份保留期|7 天|35 天|35 天|
|CPU|低|低、中、高|中、高|
|IO 輸送量 (大約) |每一 DTU 2.5 IOPS| 每一 DTU 2.5 IOPS | 每一 DTU 48 IOPS|
|IO 延遲 (大約)|5 毫秒 (讀取)，10 毫秒 (寫入)|5 毫秒 (讀取)，10 毫秒 (寫入)|2 毫秒 (讀取/寫入)|
|資料行存放區索引 |N/A|S3 和更新版本|支援|
|記憶體內部 OLTP|N/A|N/A|支援|
|||||

## <a name="performance-level-and-storage-size-limits-in-the-dtu-based-purchasing-model"></a>以 DTU 為基礎的購買模型中的效能等級和儲存體大小限制

單一資料庫的效能層級會以資料庫交易單位 (DTU) 表示，而彈性集區的效能層級則會以彈性資料庫交易單位 (eDTU) 表示。 如需 DTU 和 eDTU 的詳細資訊，請參閱[什麼是 DTU 和 eDTU？](sql-database-what-is-a-dtu.md)

### <a name="single-databases"></a>單一資料庫

||基本|標準|進階|
| :-- | --: | --: | --: | --: |
| 儲存體大小上限* | 2 GB | 1 TB | 4 TB  | 
| DTU 上限 | 5 | 3000 | 4000 | |
||||||

如需特定效能等級的詳細資訊和單一資料庫可用的儲存體大小選項，請參閱[單一資料庫 SQL Database 以 DTU 為基礎的資源限制](sql-database-dtu-resource-limits.md#single-database-storage-sizes-and-performance-levels)。

### <a name="elastic-pools"></a>彈性集區

| | **基本** | **標準** | **高級** | 
| :-- | --: | --: | --: | --: |
| 每個資料庫的儲存體大小上限*  | 2 GB | 1 TB | 1 TB | 
| 每個集區的儲存體大小上限* | 156 GB | 4 TB | 4 TB | 
| 每個資料庫的 eDTU 上限 | 5 | 3000 | 4000 | 
| 每個集區的 eDTU 上限 | 1600 | 3000 | 4000 | 
| 每個集區的資料庫數目上限 | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> - 大於內含儲存體數量的儲存體大小為預覽版，而且會產生額外成本。 如需詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。 
> - 所有區域均可取得進階層中超過 1 TB 的儲存體，下列區域除外：英國北部、美國中西部、英國南部2、中國東部、USDoDCentral、德國中部、USDoDEast、US Gov (西南部)、US Gov (中南部)、德國東北部、中國北部、US Gov (東部)。 我們已規劃在更多區域推出。 在其他區域，進階層中的儲存空間上限為 1 TB。 請參閱 [P11-P15 目前限制](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。  
> 
如需特定效能等級的詳細資訊和彈性集區可用的儲存體大小選項，請參閱 [SQL Database 以 DTU 為基礎的資源限制](sql-database-dtu-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels)。



## <a name="next-steps"></a>後續步驟

- 如需特定效能等級的詳細資訊和可用的儲存體大小選項，請參閱 [SQL Database 以 DTU 為基礎的資源限制](sql-database-dtu-resource-limits.md)和 [SQL Database 以虛擬核心為基礎的資源限制](sql-database-vcore-resource-limits.md)。
- 請參閱 [SQL Database 常見問題集](sql-database-faq.md)以取得常見問題的解答。
- 了解 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)
