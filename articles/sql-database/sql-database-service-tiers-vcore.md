---
title: Azure SQL Database 服務 - vCore | Microsoft Docs
description: VCore 為基礎的購買模型可讓您獨立調整計算和儲存體資源、符合內部部署效能, 並將價格優化。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 06/26/2019
ms.openlocfilehash: e5af3803ebb4cb0a88a082d3c85d0df68da8d1b8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566623"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>在 vCore 服務層級之間選擇, 並從 DTU 服務層進行遷移

以虛擬核心 (vCore) 為基礎的購買模型可讓您獨立調整計算和儲存體資源、符合內部部署效能, 並將價格優化。 它也可讓您選擇硬體的世代:

- **第4代**:最多24個以 Intel E5-2673 v3 (Haswell) 2.4-GHz 處理器為基礎的邏輯 Cpu, vCore = 1 PP (實體核心), 每一核心 7 GB, 連結的 SSD
- **第5代**:最多80以 Intel E5-2673 v4 (Broadwell) 2.3-GHz 處理器為基礎的邏輯 Cpu, vCore = 1 LP (超執行緒), 每個核心 5.1 GB, 快速 eNVM SSD

在 Gen4 硬體中，每個虛擬核心的記憶體會高出許多。 不過，Gen5 硬體可讓您大幅相應增加計算資源。

> [!IMPORTANT]
> AustraliaEast 區域已不再支援新的第4代資料庫。
> [!NOTE]
> 如需以 DTU 為基礎之服務層的相關資訊, 請參閱[以 dtu 為基礎的購買模型的服務層](sql-database-service-tiers-dtu.md)。 如需以 DTU 為基礎和以 vCore 為基礎的購買模型之服務層級之間差異的詳細資訊, 請參閱[Azure SQL Database 購買模型](sql-database-purchase-models.md)。

## <a name="service-tier-characteristics"></a>服務層特性

VCore 為基礎的購買模型提供三個服務層級: 一般用途、超大規模資料庫和業務關鍵。 這些服務層級是以一系列計算大小、高可用性設計、錯誤隔離方法、儲存體類型和 i/o 範圍來區分。

您必須個別設定所需的儲存體和備份保留期。 若要設定備份保留期限, 請開啟 [Azure 入口網站], 移至伺服器 (而不是資料庫), 然後移至 [**管理備份** > ] [**設定原則** > **時間點還原** > 設定]**7-35天**。

下表說明這三個層級之間的差異:

||**一般用途**|**業務關鍵**|**超大規模資料庫**|
|---|---|---|---|
|最適用的對象|大部分的商業工作負載。 提供以預算為導向、平衡且可調整的計算和儲存體選項。|具有高 i/o 需求的商務應用程式。 使用數個隔離的複本, 為失敗提供最高的復原能力。|具有可高度擴充性的儲存體和讀取規模需求的大多數商務工作負載。|
|計算|已布**建的計算**:<br/>第 4 代：1到24虛擬核心<br/>第 5 代：2至80虛擬核心<br/>**無伺服器計算**:<br/>第 5 代：0.5-4 虛擬核心|已布**建的計算**:<br/>第 4 代：1到24虛擬核心<br/>第 5 代：2至80虛擬核心|已布**建的計算**:<br/>第 4 代：1到24虛擬核心<br/>第 5 代：2至80虛擬核心|
|記憶體|已布**建的計算**:<br/>第 4 代：每個虛擬核心 7GB<br/>第 5 代：每個虛擬核心 5.1 GB<br/>**無伺服器計算**:<br/>第 5 代：每個虛擬核心 3 GB|已布**建的計算**:<br/>第 4 代：每個虛擬核心 7GB<br/>第 5 代：每個虛擬核心 5.1 GB |已布**建的計算**:<br/>第 4 代：每個虛擬核心 7GB<br/>第 5 代：每個虛擬核心 5.1 GB|
|存放區|使用遠端存放。<br/>**單一資料庫布建計算**:<br/>5 GB – 4 TB<br/>**單一資料庫無伺服器計算**:<br/>5 GB - 1 TB<br/>**受控實例**:32 GB - 8 TB |使用本機 SSD 儲存體。<br/>**單一資料庫布建計算**:<br/>5 GB – 4 TB<br/>**受控實例**:<br/>32 GB - 4 TB |視需要彈性自動成長儲存體。 最多可支援 100 TB 的儲存體。 會針對本機緩衝集區快取和本機資料儲存體使用本機 SSD 儲存體。 使用 Azure 遠端儲存體作為最終長期資料存放區。 |
|I/o 輸送量 (近似)|**單一資料庫**:500 IOPS (每個 vCore 具有7000的最大 IOPS)。<br/>**受控實例**:視檔案[大小而](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)定。|每個虛擬核心 5000 IOPS，且 IOPS 上限為 200,000|超大規模資料庫是多層式架構, 在多個層級進行快取。 有效的 IOPs 將視工作負載而定。|
|可用性|1個複本、無讀取規模複本|3 個複本、1 個[讀取規模複本](sql-database-read-scale-out.md)、<br/>區域冗余高可用性 (HA)|1個讀寫複本, 加上 0-4[個讀取規模複本](sql-database-read-scale-out.md)|
|備份|[讀取權限異地多餘儲存體 (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 天 (預設為7天)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md)、7-35 天 (預設為 7 天)|Azure 遠端儲存體中以快照集為基礎的備份。 還原時可使用這些快照集進行快速復原。 備份是即時的, 不會影響計算 i/o 效能。 還原速度很快, 而且不是資料大小的作業 (需要幾分鐘, 而不是小時或數天)。|
|記憶體內|不支援|支援|不支援|
|||

> [!NOTE]
> 您可以在基本服務層級取得免費的 Azure SQL 資料庫, 並搭配 Azure 免費帳戶。 如需詳細資訊, 請參閱[使用您的 Azure 免費帳戶建立受控雲端資料庫](https://azure.microsoft.com/free/services/sql-database/)。

- 如需 vCore 資源限制的詳細資訊, 請參閱[單一資料庫中的 vCore 資源限制](sql-database-vcore-resource-limits-single-databases.md)和[受控實例中的 vCore 資源限制](sql-database-managed-instance.md#vcore-based-purchasing-model)。
- 如需一般用途和業務關鍵服務層級的詳細資訊, 請參閱[一般用途和業務關鍵服務層級](sql-database-service-tiers-general-purpose-business-critical.md)。
- 如需有關以 vCore 為基礎的購買模型中超大規模資料庫服務層級的詳細資訊, 請參閱[超大規模資料庫服務層級](sql-database-service-tier-hyperscale.md)。  

## <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

在 vCore 為基礎的購買模型的已布建計算層中, 您可以使用[SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/), 在 SQL Database 上交換您現有的授權以享有折扣優惠。 這項 Azure 權益可讓您使用內部部署 SQL Server 授權搭配軟體保證, 最多省下 30% 的 Azure SQL Database。

![定價](./media/sql-database-service-tiers/pricing.png)

使用 Azure Hybrid Benefit, 您可以選擇只使用現有的 SQL database engine SQL Server 授權 (基礎計算定價) 來支付基礎 Azure 基礎結構的費用, 也可以為基礎結構和 SQL Server 付費授權 (包含授權的定價)。

您可以使用 Azure 入口網站或使用下列其中一個 Api, 來選擇或變更您的授權模型:

- 若要使用 PowerShell 來設定或更新授權類型:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- 若要使用 Azure CLI 來設定或更新授權類型:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- 若要使用 REST API 來設定或更新授權類型:

  - [資料庫 - 建立或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [資料庫 - 更新](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Managed Instances - Create Or Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>從以 DTU 為基礎的模型遷移到 vCore 為基礎的模型

### <a name="migrate-a-database"></a>移轉資料庫

將資料庫從以 DTU 為基礎的購買模型遷移到 vCore 為基礎的購買模型, 類似于以 DTU 為基礎的購買模型中的 standard 和 premium 服務層級之間的升級或降級。

### <a name="migrate-databases-with-geo-replication-links"></a>遷移具有異地複寫連結的資料庫

從以 DTU 為基礎的模型遷移到 vCore 為基礎的購買模型, 類似于在 standard 和 premium 服務層級中的資料庫之間升級或降級異地複寫關聯性。 在遷移期間, 您不需要停止異地複寫, 但必須遵循下列排序規則:

- 升級時，您必須先升級次要資料庫，然後再升級主要資料庫。
- 降級時，順序相反︰您必須先降級主要資料庫，然後再降級次要資料庫。

當您在兩個彈性集區之間使用異地複寫時, 建議您將一個集區指定為主要複本, 並將另一個做為次要資料庫。 在此情況下, 當您要遷移彈性集區時, 您應該使用相同的排序指引。 不過, 如果您有同時包含主要和次要資料庫的彈性集區, 請將具有較高使用率的集區視為主要複本, 並據以遵循順序規則。  

下表提供特定遷移案例的指引:

|目前的服務層級|目標服務層級|遷移類型|使用者動作|
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

\*標準層中的每個 100 Dtu 需要至少 1 vCore, 而進階層中的每個 125 Dtu 至少需要 1 vCore。

### <a name="migrate-failover-groups"></a>遷移容錯移轉群組

在遷移具有多個資料庫的容錯移轉群組時，必須個別遷移主要和次要資料庫。 過程中適用相同的考量和排序規則。 將資料庫轉換成 vCore 為基礎的購買模型之後, 容錯移轉群組將會使用相同的原則設定繼續作用。

### <a name="create-a-geo-replication-secondary-database"></a>建立異地複寫次要資料庫

您只能使用與主資料庫相同的服務層級, 來建立異地複寫次要資料庫 (地理位置)。 對於記錄產生率較高的資料庫, 我們建議使用與主要複本相同的計算大小來建立異地次要資料庫。

如果您要在彈性集區中為單一主資料庫建立異地次要資料庫, 請確定集區`maxVCore`的設定符合主資料庫的計算大小。 如果您要為另一個彈性集區中的主要複本建立異地次要資料庫, 建議您讓集區具有`maxVCore`相同的設定。

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>使用資料庫複製, 將以 DTU 為基礎的資料庫轉換成以 vCore 為基礎的資料庫

您可以將任何資料庫 (具有以 DTU 為基礎的計算大小) 複製到資料庫 (具有以虛擬核心為基礎的計算大小)，而不會有任何限制或特殊排序，但前提是目標計算大小支援來源資料庫的最大資料庫大小。 資料庫複製會在複製作業開始時建立資料的快照集, 而不會同步處理來源與目標之間的資料。

## <a name="next-steps"></a>後續步驟

- 如需單一資料庫可用的特定計算大小和儲存體大小選項, 請參閱[SQL Database 單一資料庫的 vCore 為基礎的資源限制](sql-database-vcore-resource-limits-single-databases.md)。
- 如需彈性集區可用的特定計算大小和儲存體大小選項, 請參閱彈性集區[SQL Database vCore 為基礎的資源限制](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)。
