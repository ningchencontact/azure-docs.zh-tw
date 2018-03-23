---
title: Azure SQL Database 服務 | Microsoft Docs
description: 深入了解單一和集區資料庫的服務層，以提供效能層級和儲存體大小。
keywords: ''
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 02/28/2018
ms.author: carlrab
ms.openlocfilehash: ebfd44098a81b4a9d8863623458460ceb25f0d5d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="what-are-azure-sql-database-service-tiers"></a>何謂 Azure SQL Database 服務層？

[Azure SQL Database](sql-database-technical-overview.md) 為[單一資料庫](sql-database-single-database-resources.md)與[彈性集區](sql-database-elastic-pool.md)提供**基本**、**標準**和**進階**服務層。 SQL Database 為 [Azure SQL Database 受控執行個體](sql-database-managed-instance.md#managed-instance-service-tier)提供一個「一般用途」服務層。 服務層主要是以一系列的效能層級和儲存體大小選項以及價格區分。  所有服務層皆提供變更效能層級和儲存體大小的彈性。  單一資料庫和彈性集區會根據服務層、效能層級和儲存體大小，以每小時的方式計費。   

> [!IMPORTANT]
> 「SQL Database 受控執行個體」(目前為公開預覽版) 提供單一的一般用途服務層。 如需詳細資訊，請參閱 [Azure SQL Database 受控執行個體](sql-database-managed-instance.md)。 本文的其餘部分不適用於「受控執行個體」。

## <a name="choosing-a-service-tier"></a>選擇服務層

服務層的選擇主要視業務持續性、儲存體和效能需求而定。
| | **基本** | **標準** |**高級**  |
| :-- | --: |--:| --:| --:| 
|目標工作負載|開發與生產|開發與生產|開發與生產||
|執行時間 SLA|99.99%|99.99%|99.99%|預覽時，N/A|
|備份保留期|7 天|35 天|35 天|
|CPU|低|低、中、高|中、高|
|IO 輸送量 (大約) |每一 DTU 2.5 IOPS  | 每一 DTU 2.5 IOPS | 每一 DTU 48 IOPS|
|IO 延遲 (大約)|5 毫秒 (讀取)，10 毫秒 (寫入)|5 毫秒 (讀取)，10 毫秒 (寫入)|2 毫秒 (讀取/寫入)|
|資料行存放區索引和記憶體內部 OLTP|N/A|N/A|支援|
|||||

## <a name="performance-level-and-storage-size-limits"></a>效能層級和儲存體大小限制

單一資料庫的效能層級會以資料庫交易單位 (DTU) 表示，而彈性集區的效能層級則會以彈性資料庫交易單位 (eDTU) 表示。 如需 DTU 和 eDTU 的詳細資訊，請參閱[什麼是 DTU 和 eDTU？](sql-database-what-is-a-dtu.md)

### <a name="single-databases"></a>單一資料庫

|  | **基本** | **標準** | **高級** | 
| :-- | --: | --: | --: | --: |
| 儲存體大小上限* | 2 GB | 1 TB | 4 TB  | 
| DTU 上限 | 5 | 3000 | 4000 | |
||||||

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
> \* 大於內含儲存體數量的儲存體大小尚在預覽中，而且會產生額外成本。 如需詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。 
>
> \* 在進階層，目前於下列區域中提供超過 1 TB 的儲存體：澳大利亞東部、澳大利亞東南部、巴西南部、加拿大中部、加拿大東部、美國中部、法國中部、德國中部、日本東部、日本西部、韓國中部、美國中北部、北歐、美國中南部、東南亞、英國南部、英國西部、美國東部 2、美國西部、美國維吉尼亞州政府及西歐。 請參閱 [P11-P15 目前限制](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。  
> 

如需特定的效能層級和可用儲存體大小選項的詳細資訊，請參閱 [SQL Database 資源限制](sql-database-resource-limits.md)。


## <a name="next-steps"></a>後續步驟

- 了解[單一資料庫資源](sql-database-single-database-resources.md)。
- 若要深入了解彈性集區，請參閱[彈性集區](sql-database-elastic-pool.md)。
- 了解 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)
* 了解 [DTU 與 eDTU](sql-database-what-is-a-dtu.md)。
* 若要深入了解監視 DTU 使用量，請參閱[監視和效能調整](sql-database-troubleshoot-performance.md)。

