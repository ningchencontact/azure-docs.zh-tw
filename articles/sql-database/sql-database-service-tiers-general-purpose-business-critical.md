---
title: Azure SQL Database - 一般用途和業務關鍵 | Microsoft Docs
description: 本文還討論一般用途和業務關鍵服務層，在以 vCore 為基礎的購買模型。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 02/23/2019
ms.openlocfilehash: e5426bb7c8eba9d58dbf0472360c6ce0b19c9bc4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66431338"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database 服務層

Azure SQL Database 是以會進行調整的雲端環境，以確保 99.99%可用性，即使被基礎結構失敗的 SQL Server database engine 架構為基礎。 Azure SQL Database，各有不同的架構模型中，會使用三個服務層。 這些服務層為：

- [一般用途](sql-database-service-tier-general-purpose.md)，可用於大部分的泛型的工作負載。
- [商務關鍵性](sql-database-service-tier-business-critical.md)，這專為低延遲工作負載，具有一個可讀取的複本。
- [超大規模](sql-database-service-tier-hyperscale.md)，這專為非常大型的資料庫 (最多 100 TB) 與多個可讀取的複本。

這篇文章討論一般用途和以 vCore 為基礎的購買模型中的商務關鍵性服務層的儲存體和備份考量。

> [!NOTE]
> 以 vCore 為基礎的購買模型中的超大規模服務層的相關資訊，請參閱[超大規模的服務層](sql-database-service-tier-hyperscale.md)。 如需以虛擬核心為基礎的購買模型與以 DTU 為基礎的購買模型的比較，請參閱 [Azure SQL Database 購買模型和資源](sql-database-purchase-models.md)。

## <a name="data-and-log-storage"></a>資料和記錄儲存體

下列因素會影響用於資料和記錄檔的儲存空間量：

- 資料檔案 (MDF) 和記錄檔 (LDF) 會使用配置的儲存體。
- 每個單一資料庫計算大小所支援的最大資料庫大小，預設大小上限為 32 GB。
- 當您設定所需的單一資料庫大小 （MDF 檔的大小） 時，則百分之 30 多個額外的儲存體會自動新增以支援 LDF 檔案中。
- SQL Database 受控執行個體的儲存體大小必須指定為 32 GB 的倍數。
- 您可以選取 10 GB 和支援的最大值之間任何單一資料庫的大小。
  - 標準或一般用途服務層中的儲存體，增加或減少的大小以 10 GB 為增量單位。
  - 在進階或業務關鍵服務層、 增加或減少大小 250 GB 為增量單位中的儲存體。
- 在一般用途服務層中，`tempdb`連結的 SSD 和此儲存體成本會包含在虛擬核心價格的使用。
- 在業務關鍵服務層中， `tempdb` MDF 和 LDF 檔案，與共用連結的 SSD 和`tempdb`虛擬核心價格的內含儲存體費用。

> [!IMPORTANT]
> 您會支付為 MDF 和 LDF 檔案配置的總儲存體。

若要監視 MDF 和 LDF 檔案的目前總大小，請使用[sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)。 若要監視個別 MDF 和 LDF 檔案的目前大小，請使用 [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

## <a name="backups-and-storage"></a>備份和儲存體

資料庫備份儲存體配置給支援時間點還原 (PITR) 和[長期保留 (LTR)](sql-database-long-term-retention.md) SQL Database 的能力。 這個儲存體會分別配置給每個資料庫，並以兩個不同的每一資料庫費用來計費。

- **PITR**：個別的資料庫備份複製到[讀取權限異地備援 (RA-GRS) 儲存體](../storage/common/storage-designing-ha-apps-with-ragrs.md)自動。 建立新的備份時，會動態地增加的儲存體大小。 每週完整備份、 每日差異備份，並複製每隔 5 分鐘的交易記錄備份，使用儲存體。 儲存體耗用量取決於資料庫和備份的保留期限的變動率。 您可以為每個資料庫設定 7 到 35 天的不同保留期限。 最小的儲存體數量等於 100%(1 x) 的資料庫大小會提供不收取額外費用。 對於大多數資料庫來說，此數量就足以儲存 7 天份的備份。
- **LTR**：SQL Database 會提供設定完整備份的長期保留，最多 10 年的選項。 如果您設定的 LTR 原則時，這些備份會自動儲存在 RA-GRS 儲存體，但您可以控制備份複製的頻率。 為了符合不同的合規性需求，您可以選取每週、 每月和/或每年備份的不同保留週期。 您選擇的設定會決定多少儲存空間將用於 LTR 備份。 若要預估 LTR 儲存體的成本，您可以使用 LTR 定價計算機。 如需詳細資訊，請參閱 < [SQL Database 長期保留](sql-database-long-term-retention.md)。

## <a name="next-steps"></a>後續步驟

- 針對特定的詳細資料計算大小和一般用途和業務關鍵服務層中的單一資料庫可用的儲存體大小，請參閱[單一資料庫的 SQL Database 以 vCore 為基礎的資源限制](sql-database-vcore-resource-limits-single-databases.md)。
- 如需特定詳細資料計算大小和可用的一般用途和業務關鍵服務層中的彈性集區的儲存體大小，請參閱[彈性集區的 SQL Database 以 vCore 為基礎的資源限制](sql-database-vcore-resource-limits-elastic-pools.md)。
