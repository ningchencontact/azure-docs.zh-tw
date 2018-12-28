---
title: Azure SQL Database - 一般用途和業務關鍵 | Microsoft Docs
description: 本文討論 V 核心購買模型中的一般用途和業務關鍵服務層。
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan, moslake
manager: craigg
ms.date: 11/30/2018
ms.openlocfilehash: 7de4415dd332254c595b6687dfb9d8db01c42362
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871701"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database 服務層

Azure SQL Database 是以會針對雲端環境調整的 SQL Server 資料庫引擎架構為基礎，以確保 99.99% 的可用性 (即使在基礎結構失敗的情況下)。 Azure SQL Database 中使用三個架構模型：
- [一般目的](sql-database-service-tier-general-purpose.md)專門用於大部分的一般工作負載。
- [業務關鍵](sql-database-service-tier-business-critical.md)專門用於具有一個可讀複本的低延遲工作負載。
- [超大規模](sql-database-service-tier-hyperscale.md)專門用於具有多個可讀複本的大型資料庫 (最多 100 TB)。

本文討論以 V 核心為基礎的購買模型中，一般用途和業務關鍵服務層的儲存體和備份考量。

> [!NOTE]
> 如需 V 核心形式購買模型中超大規模服務層的詳細資訊，請參閱[超大規模服務層](sql-database-service-tier-hyperscale.md)。 如需 V 核心形式購買模型與 DTU 形式購買模型的比較，請參閱 [Azure SQL Database 購買模型和資源](sql-database-service-tiers.md)。

## <a name="data-and-log-storage"></a>資料和記錄儲存體

請考慮下列：

- 所配置的儲存體是供資料檔案 (MDF) 和記錄檔 (LDF) 檔案來使用的。
- 每個單一資料庫計算大小所支援的資料庫大小有其上限，預設大小上限為 32 GB。
- 在設定所需的單一資料庫大小 (MDF 的大小) 時，系統會自動另外加 30% 的儲存體空間以支援 LDF
- 受控執行個體中的儲存體大小必須指定為 32 GB 的倍數。
- 您可以選取 10 GB 到所支援上限之間的任何單一資料庫大小
  - 針對標準儲存體，以 10-GB 為增量單位增加或減少大小
  - 針對進階儲存體，以 250-GB 為增量單位增加或減少大小
- 在一般用途服務層中，`tempdb` 會使用連結的 SSD，且這個儲存體成本會包含在虛擬核心價格中。
- 在商務關鍵性服務層中，`tempdb` 會與 MDF 和 LDF 檔案共用連結的 SSD，且 tempDB 儲存體成本會包含在虛擬核心價格中。

> [!IMPORTANT]
> 您必須支付為 MDF 和 LDF 所配置的總儲存體費用。

若要監視 MDF 和 LDF 目前總計的大小，請使用 [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)。 若要監視個別 MDF 和 LDF 檔案的目前大小，請使用 [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

## <a name="backups-and-storage"></a>備份和儲存體

為了支援 SQL Database 的還原時間點 (PITR) 和[長期保留 (LTR)](sql-database-long-term-retention.md) 功能，系統會配置儲存體供資料庫備份使用。 這個儲存體會分別配置給每個資料庫，並以兩個不同的每一資料庫費用來計費。

- **PITR**：個別的資料庫備份會自動複製到 [RA-GRS 儲存體](../storage/common/storage-designing-ha-apps-with-ragrs.md)。 儲存體大小會隨著新備份的建立而動態地增加。  每週完整備份、每日差異備份以及每 5 分鐘複製一次的交易記錄備份都使用此儲存體。 儲存體耗用量取決於資料庫的變動率及保留期限。 您可以為每個資料庫設定 7 到 35 天的不同保留期限。 系統會提供等於資料大小 1 倍的最小儲存體數量，且無額外費用。 對於大多數資料庫來說，此數量就足以儲存 7 天份的備份。
- **LTR**：SQL Database 提供選項讓您設定完整備份的長期保留期，最長可達 10 年之久。 如果啟用 LTR 原則，這些備份會自動儲存在 RA-GRS 儲存體中，但您可以控制備份的複製頻率。 為了符合不同的合規性需求，您可以針對每週、每月和/或每年備份選取不同的保留期限。 此設定會定義要將多少儲存體用於 LTR 備份。 您可以使用 LTR 定價計算機來估算 LTR 儲存體的成本。 如需詳細資訊，請參閱[長期保存](sql-database-long-term-retention.md)。

## <a name="next-steps"></a>後續步驟

- 如需特定計算大小的詳細資訊和一般用途與業務關鍵服務層中單一資料庫可用的儲存體大小選項，請參閱[單一資料庫 SQL Database 以 V 核心為基礎的資源限制](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)
- 如需特定計算大小的詳細資訊和一般用途與業務關鍵服務層中彈性集區可用的儲存體大小選項，請參閱[彈性集區 SQL Database 以 V 核心為基礎的資源限制](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)。
