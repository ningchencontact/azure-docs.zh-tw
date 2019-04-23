---
title: Azure SQL Database 服務 - vCore | Microsoft Docs
description: 以虛擬核心為基礎的購買模型可讓您獨立地調整計算和儲存體資源、符合內部部署效能，並獲得最佳價格。
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
ms.date: 02/07/2019
ms.openlocfilehash: edba858f9be3350034ff48ea16d3c9137254bb97
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357940"
---
# <a name="vcore-service-tiers-azure-hybrid-benefit-and-migration"></a>V 核心服務層、Azure Hybrid Benefit 及移轉

以虛擬核心為基礎的購買模型可讓您獨立地調整計算和儲存體資源、符合內部部署效能，並獲得最佳價格。 它也可讓您選擇硬體世代：

- Gen4 - 最多 24 個以 Intel E5-2673 v3 (Haswell) 2.4 GHz 處理器為基礎的邏輯 CPU，虛擬核心 = 1 PP (實體核心)，每一核心 7 GB，連結的 SSD
- Gen5 - 最多 80 個以 Intel E5-2673 v4 (Broadwell) 2.3 GHz 處理器為基礎的邏輯 CPU，虛擬核心 = 1 LP (超執行緒)，每一核心 5.1 GB，快速 eNVM SSD

在 Gen4 硬體中，每個虛擬核心的記憶體會高出許多。 不過，Gen5 硬體可讓您大幅相應增加計算資源。

> [!NOTE]
> 如需 DTU 形式的服務層相關資訊，請參閱 [DTU 形式的服務層](sql-database-service-tiers-dtu.md)。 如需如何區分 DTU 形式服務層及 V 核心形式服務層的相關資訊，請參閱 [Azure SQL Database 購買模型](sql-database-purchase-models.md)。

## <a name="service-tier-characteristics"></a>服務層的特性

虛擬核心模型提供三個服務層：一般用途、超大規模資料庫與業務關鍵。 服務層以一系列的計算大小、高可用性設計、錯誤隔離、儲存體類型和大小，以及 IO 範圍來區分。 您必須個別設定所需的儲存體和備份保留期。 在 Azure 入口網站中，移至 [伺服器] (不是資料庫) > [受控備份] > [設定原則] > [還原時間點設定] > 7-35 天。

下表可協助您了解這三層的差異︰

||**一般用途**|**商務關鍵性**|**超大規模 (預覽)**|
|---|---|---|---|
|適用對象|大部分的商業工作負載。 提供以預算為導向、平衡且可調整規模的計算與儲存體選項。|高 IO 需求的商務應用程式。 使用數個分開的複本，針對失敗提供最高的復原能力。|具有可高度擴充的儲存體和讀取規模需求的多數商務工作負載|
|計算|第 4 代：1 到 24 個虛擬核心<br/>第 5 代：1 到 80 個虛擬核心|第 4 代：1 到 24 個虛擬核心<br/>第 5 代：1 到 80 個虛擬核心|第 4 代：1 到 24 個虛擬核心<br/>第 5 代：1 到 80 個虛擬核心|
|記憶體|第 4 代：每個核心 7 GB<br>第 5 代：每個核心 5.1 GB | 第 4 代：每個核心 7 GB<br>第 5 代：每個核心 5.1 GB |第 4 代：每個核心 7 GB<br>第 5 代：每個核心 5.1 GB|
|儲存體|使用遠端儲存體：<br/>單一資料庫：5 GB – 4 TB<br/>受控執行個體：32 GB - 8 TB |使用本機 SSD 儲存體：<br/>單一資料庫：5 GB – 4 TB<br/>受控執行個體：32 GB - 4 TB |儲存體可依需求彈性地自動成長。 可支援多達 100 TB 以上的儲存體。 本機緩衝區集區快取和本機資料儲存可使用本機 SSD 儲存體。 以 Azure 遠端儲存體作為最終的長期資料存放區。 |
|IO 輸送量 (大約)|單一資料庫：每個虛擬核心 500 IOPS，且 IOPS 上限為 7000</br>受控執行個體：視[檔案大小](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)而定|每個虛擬核心 5000 IOPS，且 IOPS 上限為 200,000|TBD|
|可用性|1 個複本、無讀取規模|3 個複本、1 個[讀取規模複本](sql-database-read-scale-out.md)、<br/>區域備援 HA|?|
|備份|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md)、7-35 天 (預設為 7 天)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md)、7-35 天 (預設為 7 天)|Azure 遠端儲存體中有以快照集為基礎的備份，還原時可使用這些快照集進行快速復原。 備份可迅速完成，且不會影響計算的 IO 效能。 還原速度非常快，而且不是資料作業的大小 (以分鐘而非小時或天來計算)。|
|記憶體內|不支援|支援|不支援|
|||

> [!NOTE]
> 您可以取得免費的 Azure SQL database 搭配使用 Azure 免費帳戶探索 Azure 的基本服務層。 如需相關資訊，請參閱[使用您的免費 Azure 免費帳戶，建立受管理的雲端資料庫](https://azure.microsoft.com/free/services/sql-database/)。

- 如需詳細資訊，請參閱[單一資料庫中的虛擬核心資源限制](sql-database-vcore-resource-limits-single-databases.md)和[受控執行個體中的虛擬核心資源限制](sql-database-managed-instance.md#vcore-based-purchasing-model)。
- 如需一般用途與商務關鍵性服務層的詳細資訊，請參閱[一般用途與商務關鍵性服務層](sql-database-service-tiers-general-purpose-business-critical.md)。
- 如需以虛擬核心為基礎的購買模型中超大規模服務層的詳細資訊，請參閱[超大規模服務層](sql-database-service-tier-hyperscale.md)。  

> [!IMPORTANT]
> 如果您需要的計算容量少於一個虛擬核心，請使用以 DTU 為基礎的購買模型。

## <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

在以虛擬核心為基礎的購買模型中，您可以使用[適用於 SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，以折扣優惠在 SQL Database 上交換現有授權。 這個 Azure 權益可讓您使用具備軟體保證的內部部署 SQL Server 授權，在 Azure SQL Database 上使用內部部署 SQL Server 授權省下最高 30% 的成本。

![定價](./media/sql-database-service-tiers/pricing.png)

使用 Azure Hybrid Benefit 時，您可以選擇使用 SQL 資料庫引擎本身現有的 SQL Server 授權來僅支付相關 Azure 基礎結構的費用 (**BasePrice**)，或是同時支付相關基礎結構和 SQL Server 授權的費用 (**LicenseIncluded**)。 您可以使用 Azure 入口網站或下列其中一個 API，來選擇或變更授權模型。

- 使用 PowerShell 來設定或更新授權類型：

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql)

- 使用 Azure CLI 來設定或更新授權類型：

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- 使用 REST API 來設定或更新授權類型：

  - [資料庫 - 建立或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [資料庫 - 更新](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Managed Instances - Create Or Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migration-from-dtu-model-to-vcore-model"></a>從 DTU 模型移轉至虛擬核心模型

### <a name="migration-of-a-database"></a>資料庫移轉

將資料庫從以 DTU 為基礎的購買模型移轉至以虛擬核心為基礎的購買模型，類似於在以 DTU 為基礎的購買模型中的標準和高階資料庫之間進行升級或降級。

### <a name="migration-of-databases-with-geo-replication-links"></a>使用異地複寫連結來移轉資料庫

從以 DTU 為基礎的模型遷移到以虛擬核心為基礎的模型時，其程序類似於在標準和進階資料庫之間進行異地複寫關聯性的升級或降級。 此遷移程序不需要終止異地複寫，但使用者必須遵守排序規則。 升級時，您必須先升級次要資料庫，然後再升級主要資料庫。 降級時，順序相反︰您必須先降級主要資料庫，然後再降級次要資料庫。

在兩個彈性集區之間使用異地複寫時，建議您將其中一個集區指定為主要集區，將另一個集區指定為次要集區。 在此情況下，遷移彈性集區時應使用相同的指引。  但技術上來說，彈性集區可以同時包含主要和次要資料庫。 在此情況下，若要正確地遷移，請將使用率較高的集區當作「主要」集區，並據此遵循排序規則。  

下表提供特定遷移案例的指引：

|目前的服務層|目標服務層|遷移類型|使用者動作|
|---|---|---|---|
|標準|一般用途|橫向|可依任何順序遷移，但必須確保適當的虛擬核心大小調整*|
|進階|業務關鍵|橫向|可依任何順序遷移，但必須確保適當的虛擬核心大小調整*|
|標準|業務關鍵|升級|必須先遷移次要|
|業務關鍵|標準|降級|必須先遷移主要|
|進階|一般用途|降級|必須先遷移主要|
|一般用途|進階|升級|必須先遷移次要|
|業務關鍵|一般用途|降級|必須先遷移主要|
|一般用途|業務關鍵|升級|必須先遷移次要|
||||

\* 標準層中每 100 DTU 需要至少 1 個虛擬核心，進階層中每 125 DTU 需要至少 1 個虛擬核心

### <a name="migration-of-failover-groups"></a>遷移容錯移轉群組

在遷移具有多個資料庫的容錯移轉群組時，必須個別遷移主要和次要資料庫。 過程中適用相同的考量和排序規則。 資料庫轉換成以虛擬核心為基礎的模型之後，容錯移轉群組在相同的原則設定下仍會有效。

### <a name="creation-of-a-geo-replication-secondary"></a>建立異地複寫次要資料庫

您只能使用和主要資料庫相同的服務層來建立異地次要資料庫。 對於記錄產生率較高的資料庫，建議您使用和主要資料庫相同的計算大小來建立次要資料庫。 如果您要在彈性集區中為單一主要資料庫建立異地次要資料庫，建議您讓集區具有符合主要資料庫計算大小的 `maxVCore` 設定。 如果您要在彈性集區中為另一個彈性集區中的主要資料庫建立異地次要資料庫，建議您讓集區具有相同的 `maxVCore` 設定

### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>使用資料庫複製，將 DTU 形式的資料庫轉換為 V 核心形式的資料庫

您可以將任何資料庫 (具有以 DTU 為基礎的計算大小) 複製到資料庫 (具有以虛擬核心為基礎的計算大小)，而不會有任何限制或特殊排序，但前提是目標計算大小支援來源資料庫的最大資料庫大小。 資料庫複製會在複製作業開始時建立資料的快照集，而且不會在來源和目標之間執行資料同步。

## <a name="next-steps"></a>後續步驟

- 如需特定計算大小的詳細資訊和單一資料庫可用的儲存體大小選項，請參閱[單一資料庫 SQL Database 以虛擬核心為基礎的資源限制](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)
- 如需特定計算大小的詳細資訊和彈性集區可用的儲存體大小選項，請參閱[彈性集區的 SQL Database V 核心形式資源限制](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)。
