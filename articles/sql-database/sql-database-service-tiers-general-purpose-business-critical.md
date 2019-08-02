---
title: Azure SQL Database - 一般用途和業務關鍵 | Microsoft Docs
description: 本文討論以 vCore 為基礎的購買模型中的一般用途和商務關鍵服務層級。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 02/23/2019
ms.openlocfilehash: decb4428321d5083d6ba7af134e223eb2fa5a912
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566696"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database 服務層級

Azure SQL Database 是以針對雲端環境調整的 SQL Server 資料庫引擎架構為基礎, 以確保 99.99% 的可用性, 即使發生基礎結構失敗也是一樣。 Azure SQL Database 中會使用三個服務層級, 每個都有不同的架構模型。 這些服務層級為：

- [一般用途](sql-database-service-tier-general-purpose.md), 專為大部分的一般工作負載所設計。
- [商務關鍵性](sql-database-service-tier-business-critical.md), 專為具有一個可讀取複本的低延遲工作負載所設計。
- [超大規模資料庫](sql-database-service-tier-hyperscale.md), 這是針對非常大型的資料庫 (最多 100 TB) 所設計, 具有多個可讀取的複本。

本文討論 vCore 為基礎的購買模型中, 一般用途和業務關鍵服務層級的儲存體和備份考慮。

> [!NOTE]
> 如需有關以 vCore 為基礎的購買模型中超大規模資料庫服務層級的詳細資訊, 請參閱[超大規模資料庫服務層級](sql-database-service-tier-hyperscale.md)。 如需以虛擬核心為基礎的購買模型與以 DTU 為基礎的購買模型的比較，請參閱 [Azure SQL Database 購買模型和資源](sql-database-purchase-models.md)。

## <a name="data-and-log-storage"></a>資料和記錄儲存體

下列因素會影響資料和記錄檔所使用的儲存空間量:

- 已配置的儲存體會由資料檔案 (MDF) 和記錄檔 (LDF) 使用。
- 每個單一資料庫計算大小都支援資料庫大小上限, 預設大小上限為 32 GB。
- 當您設定所需的單一資料庫大小 (MDF 檔案的大小) 時, 將會自動新增額外的 30% 的儲存體, 以支援 LDF 檔案。
- SQL Database 受控實例的儲存體大小必須以 32 GB 的倍數來指定。
- 您可以選取 10 GB 到支援的最大值之間的任何單一資料庫大小。
  - 針對標準或一般用途服務層級中的儲存體, 增加或減少大小 (以 10 GB 為增量單位)。
  - 針對 premium 或業務關鍵服務層級中的儲存體, 增加或減少大小 (以 250-GB 為單位)。
- 在一般用途服務層級中`tempdb` , 會使用連接的 SSD, 而此儲存成本會包含在 vCore 價格中。
- 在業務關鍵服務層級中`tempdb` , 會與 MDF 和 LDF 檔案共用附加的 SSD, `tempdb`而儲存體成本則包含在 vCore 價格中。

> [!IMPORTANT]
> 系統會向您收取配置給 MDF 和 LDF 檔案的總儲存體費用。

若要監視您的 MDF 和 LDF 檔案的目前大小總計, 請使用[sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)。 若要監視個別 MDF 和 LDF 檔案的目前大小，請使用 [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

## <a name="backups-and-storage"></a>備份和儲存體

會設定資料庫備份的儲存體, 以支援 SQL Database 的時間點還原 (PITR) 和[長期保留 (LTR)](sql-database-long-term-retention.md)功能。 這個儲存體會分別配置給每個資料庫，並以兩個不同的每一資料庫費用來計費。

- **PITR**：個別的資料庫備份會自動複製到[讀取權限異地冗余 (RA-GRS) 儲存體](../storage/common/storage-designing-ha-apps-with-ragrs.md)。 當建立新的備份時, 儲存體大小會以動態方式增加。 每週完整備份、每日差異備份和交易記錄備份都使用此儲存體, 每5分鐘複製一次。 儲存體耗用量取決於資料庫的變更率和備份的保留期限。 您可以為每個資料庫設定 7 到 35 天的不同保留期限。 最小儲存體數量等於資料庫大小的 100% (1x), 免費提供。 對於大多數資料庫來說，此數量就足以儲存 7 天份的備份。
- **LTR**：SQL Database 提供選項, 讓您設定完整備份的長期保留期, 最長可達10年。 如果您設定 LTR 原則, 這些備份會自動儲存在 GRS 儲存體中, 但您可以控制複本備份的頻率。 若要符合不同的合規性需求, 您可以針對每週、每月和/或每年備份選取不同的保留週期。 您選擇的設定會決定要將多少儲存體用於 LTR 備份。 若要估計 LTR 儲存體的成本, 您可以使用 LTR 定價計算機。 如需詳細資訊, 請參閱[SQL Database 長期保留](sql-database-long-term-retention.md)。

## <a name="next-steps"></a>後續步驟

- 如需一般用途和業務關鍵服務層級中, 單一資料庫可用的特定計算大小和儲存體大小的詳細資訊, 請參閱[針對單一資料庫 SQL Database vCore 為基礎的資源限制](sql-database-vcore-resource-limits-single-databases.md)。
- 如需一般用途和業務關鍵服務層級中彈性集區可用的特定計算大小和儲存體大小的詳細資訊, 請參閱[針對彈性集區 SQL Database vCore 為基礎的資源限制](sql-database-vcore-resource-limits-elastic-pools.md)。
