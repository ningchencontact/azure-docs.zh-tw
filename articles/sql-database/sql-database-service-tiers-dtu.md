---
title: Azure SQL Database 服務層 - DTU | Microsoft Docs
description: 深入了解單一和集區資料庫的服務層，以提供效能層級和儲存體大小。
services: sql-database
author: sachinpMSFT
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 5d16763fc8f3331082b98216d25190b945d95b60
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411815"
---
# <a name="choosing-a-dtu-based-service-tier-performance-level-and-storage-resources"></a>選擇以 DTU 為基礎的服務層、效能等級和儲存體資源 

服務層是以一系列效能等級來做區分，這些等級各有一定數量的內含儲存體、一定的備份保留期和一定的價格。 所有服務層皆可彈性變更效能等級而不需停機。 單一資料庫和彈性集區會根據服務層和效能等級，以每小時為單位來計費。

> [!IMPORTANT]
> SQL Database 受控執行個體 (目前為公開預覽版) 不支援以 DTU 為基礎的購買模型。 如需詳細資訊，請參閱 [Azure SQL Database 受控執行個體](sql-database-managed-instance.md)。 

## <a name="choosing-a-dtu-based-service-tier"></a>選擇以 DTU 為基礎的服務層

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

## <a name="single-database-dtu-and-storage-limits"></a>單一資料庫 DTU 和儲存空間限制

單一資料庫的效能層級會以資料庫交易單位 (DTU) 表示，而彈性集區的效能層級則會以彈性資料庫交易單位 (eDTU) 表示。 如需 DTU 和 eDTU 的詳細資訊，請參閱[什麼是 DTU 和 eDTU？](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)

||基本|標準|進階|
| :-- | --: | --: | --: | --: |
| 儲存體大小上限 | 2 GB | 1 TB | 4 TB  | 
| DTU 上限 | 5 | 3000 | 4000 | |
||||||

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>彈性集區 eDTU、儲存體及集區資料庫限制

| | **基本** | **標準** | **高級** | 
| :-- | --: | --: | --: | --: |
| 每個資料庫的儲存體大小上限  | 2 GB | 1 TB | 1 TB | 
| 每個集區的儲存體大小上限 | 156 GB | 4 TB | 4 TB | 
| 每個資料庫的 eDTU 上限 | 5 | 3000 | 4000 | 
| 每個集區的 eDTU 上限 | 1600 | 3000 | 4000 | 
| 每個集區的資料庫數目上限 | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> 所有區域目前均可取得進階層中超過 1 TB 的儲存體，下列區域除外：美國中西部、中國東部、USDoDCentral、德國中部、USDoDEast、US Gov (西南部)、US Gov (愛荷華州)、德國東北部、中國北部。 在其他區域，進階層中的儲存空間上限為 1 TB。 請參閱 [P11-P15 目前限制](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。  

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

## <a name="next-steps"></a>後續步驟

- 如需特定效能等級的詳細資訊和單一資料庫可用的儲存體大小選項，請參閱[單一資料庫 SQL Database 以 DTU 為基礎的資源限制](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels)。
- 如需特定效能等級的詳細資訊和彈性集區可用的儲存體大小選項，請參閱 [SQL Database 以 DTU 為基礎的資源限制](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels)。
