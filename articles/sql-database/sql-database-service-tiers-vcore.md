---
title: Azure SQL Database 服務 - vCore | Microsoft Docs
description: 以 vCore 為基礎的購買模型可讓您獨立調整計算和儲存體資源、 符合內部部署的效能，並獲得最佳價格。
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
ms.date: 05/06/2019
ms.openlocfilehash: 520dba611e6791fca990e21173424a914c3d8e14
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693343"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>選擇 虛擬核心服務層，並從 DTU 服務層移轉

虛擬核心 (vCore) 為基礎的購買模型可讓您獨立調整計算和儲存體資源、 符合內部部署的效能，並獲得最佳價格。 它也可讓您選擇的硬體世代：

- **第 4 代**:最多 24 的邏輯 Cpu 根據 Intel E5 2673 v3 (Haswell) 2.4-GHz 處理器，vCore = 1 PP （實體核心），每個核心，7GB 附加 SSD
- **第 5 代**:最多 80 的邏輯 Cpu 根據 Intel E5 2673 v4 (Broadwell) 2.3-GHz 處理器，vCore = 1 LP （超-執行緒），每個核心，快速 eNVM SSD 5.1 GB

在 Gen4 硬體中，每個虛擬核心的記憶體會高出許多。 不過，Gen5 硬體可讓您大幅相應增加計算資源。

> [!NOTE]
> 如需以 DTU 為基礎的服務層的詳細資訊，請參閱[服務層針對以 DTU 為基礎的購買模型](sql-database-service-tiers-dtu.md)。 如需以 DTU 為基礎的服務層和以 vCore 為基礎的購買模型之間的差異資訊，請參閱[購買模型的 Azure SQL Database](sql-database-purchase-models.md)。

## <a name="service-tier-characteristics"></a>服務層的特性

以 vCore 為基礎的購買模型提供三種服務層次： 一般用途、 超大規模及業務關鍵。 這些服務層是由一連串的計算大小、 高可用性設計、 錯誤隔離方法、 類型和大小的儲存體和 I/O 範圍做區分。

您必須個別設定所需的儲存體和備份保留期。 設定備份保留期限，請開啟 Azure 入口網站，請移至伺服器 （不是資料庫），並然後移至**管理的備份** > **設定原則** >  **點的時間還原組態** >  **-grs、7-35 天**。

下表說明三個層之間的差異：

||**一般用途**|**業務關鍵**|**Hyperscale**|
|---|---|---|---|
|適用對象|大部分的商業工作負載。 供應項目預算為導向、 平衡，且可調整計算和儲存體選項。|具有高 I/O 需求的商務應用程式。 使用數個分開的複本，以提供最高的復原失敗。|使用可高度擴充的儲存體和讀取級別需求的大多數商務工作負載。|
|計算|**佈建計算**:<br/>第 4 代：1 到 24 個 v 核心<br/>第 5 代：2 為 80 個 Vcore<br/>**無伺服器計算**:<br/>第 5 代：0.5-4 個 Vcore|**佈建計算**:<br/>第 4 代：1 到 24 個 v 核心<br/>第 5 代：2 為 80 個 Vcore|**佈建計算**:<br/>第 4 代：1 到 24 個 v 核心<br/>第 5 代：2 為 80 個 Vcore|
|記憶體|**佈建計算**:<br/>第 4 代：每個虛擬核心 7GB<br/>第 5 代：每個虛擬核心 5.1 GB<br/>**無伺服器計算**:<br/>第 5 代：每個虛擬核心 3 GB|**佈建計算**:<br/>第 4 代：每個虛擬核心 7GB<br/>第 5 代：每個虛擬核心 5.1 GB |**佈建計算**:<br/>第 4 代：每個虛擬核心 7GB<br/>第 5 代：每個虛擬核心 5.1 GB|
|儲存體|使用遠端存放裝置。<br/>**單一資料庫佈建計算**:<br/>5 GB – 4 TB<br/>**單一資料庫的無伺服器計算**:<br/>5 GB - 1 TB<br/>**受控執行個體**:32 GB - 8 TB |使用本機 SSD 儲存體。<br/>**單一資料庫佈建計算**:<br/>5 GB – 4 TB<br/>**受控執行個體**:<br/>32 GB - 4 TB |所需的儲存體有彈性的自動成長。 支援最多 100 TB 的儲存體。 使用本機的緩衝集區快取與本機資料存放區的本機 SSD 儲存體。 使用 Azure 的遠端儲存體做為最終的長期資料存放區。 |
|I/O 輸送量 （大約）|**單一資料庫**:7000 的最大值 IOPS 與每個虛擬核心 500 IOPS。<br/>**受控執行個體**:取決於[的檔案大小](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)。|每個虛擬核心 5000 IOPS，且 IOPS 上限為 200,000|超大規模是多層式架構與多個層級快取。 有效的 IOPs 將取決於工作負載。|
|可用性|1 個複本，沒有讀取級別複本|3 個複本、1 個[讀取規模複本](sql-database-read-scale-out.md)、<br/>區域備援的高可用性 (HA)|1 個讀寫複本，再加上 0 到 4 之間[讀取級別複本](sql-database-read-scale-out.md)|
|備份|[讀取權限異地備援儲存體 (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md)、-grs、7-35 天 （預設的 7 天）|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md)、7-35 天 (預設為 7 天)|Azure 的遠端儲存體中的快照集式備份。 還原時可使用這些快照集進行快速復原。 備份是瞬間完成，而且不會影響計算的 I/O 效能。 還原都很快速，並不是資料大小的作業 （擷取分鐘的時間，而非小時或天）。|
|記憶體內|不支援|支援|不支援|
|||

> [!NOTE]
> 您可以取得免費的 Azure SQL database 搭配使用 Azure 免費帳戶的基本服務層。 如需詳細資訊，請參閱 <<c0> [ 使用 Azure 免費帳戶建立受管理的雲端資料庫](https://azure.microsoft.com/free/services/sql-database/)。

- 更多虛擬核心資源限制的詳細資訊，請參閱[單一資料庫中的虛擬核心資源限制](sql-database-vcore-resource-limits-single-databases.md)並[vCore 的受控執行個體中的資源限制](sql-database-managed-instance.md#vcore-based-purchasing-model)。
- 如需有關的一般用途和業務關鍵服務層的詳細資訊，請參閱 <<c0> [ 一般用途和業務關鍵服務層](sql-database-service-tiers-general-purpose-business-critical.md)。
- 如需在以 vCore 為基礎的購買模型中的超大規模服務層的詳細資訊，請參閱[超大規模的服務層](sql-database-service-tier-hyperscale.md)。  

## <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

在佈建的計算層的虛擬核心為基礎的購買模型中，您可以交換您現有的授權，以折扣優惠在 SQL Database 上使用[適用於 SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)。 這項 Azure 權益可讓您節省最多 30%的 Azure SQL Database 藉由使用具有軟體保證的內部部署 SQL Server 授權。

![定價](./media/sql-database-service-tiers/pricing.png)

使用 Azure Hybrid Benefit，您可以選擇只需支付基礎 Azure 基礎結構的 SQL 資料庫引擎本身 （基底計算定價），使用您現有的 SQL Server 授權，或支付基礎結構和 SQL Server授權 （隨附授權的價格）。

您可以選擇，或變更您的授權模型，使用 Azure 入口網站或使用其中一種下列 Api:

- 若要設定或使用 PowerShell 更新授權類型：

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- 若要設定或使用 Azure CLI 來更新授權類型：

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- 若要設定或使用 REST API 來更新授權類型：

  - [資料庫 - 建立或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [資料庫 - 更新](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Managed Instances - Create Or Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>從以 DTU 為基礎的模型移轉至以 vCore 為基礎的模型

### <a name="migrate-a-database"></a>移轉資料庫

從以 DTU 為基礎的購買模型時使用的資料庫移轉至以 vCore 為基礎的購買模型是類似於升級或降級以 DTU 為基礎的購買模型中的標準和進階服務層之間。

### <a name="migrate-databases-with-geo-replication-links"></a>移轉具有異地複寫連結的資料庫

從以 DTU 為基礎的模型移轉至以 vCore 為基礎的購買模型是類似於升級或降級資料庫中的標準和進階服務層之間的異地複寫關聯性。 在移轉期間，您不需要停止異地複寫，但您必須遵循這些排序規則：

- 升級時，您必須先升級次要資料庫，然後再升級主要資料庫。
- 降級時，順序相反︰您必須先降級主要資料庫，然後再降級次要資料庫。

當您使用異地複寫兩個彈性集區之間時，我們建議您將一個集區指定為主要和次要資料庫作為其他。 在此情況下，當您要移轉的彈性集區時，您應該使用相同的編序指引。 不過，如果您有包含主要和次要資料庫的彈性集區，視為主要的使用率較高的集區，並據此遵循排序規則。  

下表提供特定移轉案例的指引：

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

\* 在標準層中每 100 Dtu 需要至少 1 個 vCore，並在進階層中每 125 Dtu 需要至少 1 個 vCore。

### <a name="migrate-failover-groups"></a>遷移容錯移轉群組

在遷移具有多個資料庫的容錯移轉群組時，必須個別遷移主要和次要資料庫。 過程中適用相同的考量和排序規則。 資料庫會轉換成以虛擬核心為基礎的購買模型之後，容錯移轉群組有效日期會持續使用相同的原則設定。

### <a name="create-a-geo-replication-secondary-database"></a>建立異地複寫次要資料庫

您可以建立異地複寫次要資料庫 （異地次要資料庫） 只能藉由使用相同的服務層，與您用於主要資料庫。 具有較高的記錄檔產生速率的資料庫，我們建議使用相同的計算大小，與主要資料庫建立異地次要資料庫。

如果您要在單一的主要資料庫的彈性集區中建立異地次要資料庫，請確定`maxVCore`設定集區符合主要資料庫的計算大小。 如果您要建立異地次要資料庫的主要另一個彈性集區中，我們建議的集區具有相同`maxVCore`設定。

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>若要將以 DTU 為基礎的資料庫轉換成虛擬核心為基礎的資料庫使用資料庫複本

您可以將任何資料庫 (具有以 DTU 為基礎的計算大小) 複製到資料庫 (具有以虛擬核心為基礎的計算大小)，而不會有任何限制或特殊排序，但前提是目標計算大小支援來源資料庫的最大資料庫大小。 資料庫複本建立的複製作業的開始時間的資料快照集，並不會同步處理來源與目標之間的資料。

## <a name="next-steps"></a>後續步驟

- 針對特定的計算大小和單一資料庫可用的儲存體大小選項，請參閱[單一資料庫的 SQL Database 以 vCore 為基礎的資源限制](sql-database-vcore-resource-limits-single-databases.md)。
- 針對特定的計算大小和可用的彈性集區的儲存體大小選項，請參閱[彈性集區的 SQL Database 以 vCore 為基礎的資源限制](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)。
