---
title: Azure SQL Database 服務 - vCore | Microsoft Docs
description: 虛擬核心形式的採購模型可讓您獨立地調整計算和儲存體資源、符合內部部署效能，並獲得最佳價格。
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 07/16/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: d18076486704d5f03acd2253650762c3bd24b0af
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091487"
---
# <a name="choosing-a-vcore-service-tier-compute-memory-storage-and-io-resources"></a>選擇虛擬核心服務層、計算、記憶體、儲存體和 IO 資源

服務層是以一系列的效能等級、高可用性設計、錯誤隔離、儲存體類型和 IO 範圍來做區分。 客戶必須個別設定所需的儲存體和備份保留期。 使用虛擬核心模型時，單一資料庫和彈性集區可透過[適用於 SQL Server 的 Azure Hybrid Use Benefit](../virtual-machines/windows/hybrid-use-benefit-licensing.md) 來節省成本，最多可省下 30%。

下表可協助您了解這兩層的差異︰

||**一般用途**|**商務關鍵性**|
|---|---|---|
|適用對象|大部分的商業工作負載。 提供以預算為導向、平衡且可調整規模的計算與儲存體選項。|高 IO 需求的商務應用程式。 使用數個分開的複本，針對失敗提供最高的復原能力。|
|計算|1 到 80 vCore、Gen4 和 Gen5 |1 到 80 vCore、Gen4 和 Gen5|
|記憶體|Gen4：每個核心 7 GB<br>Gen5：每個核心 5.5 GB | Gen4：每個核心 7 GB<br>Gen5：每個核心 5.5 GB |
|儲存體|進階遠端儲存體，5 GB – 4 TB|本機 SSD 儲存體，5 GB – 4 TB|
|IO 輸送量 (大約)|每個虛擬核心 500 IOPS，且 IOPS 上限為 7000|每個虛擬核心 5000 IOPS，且 IOPS 上限為 200000|
|可用性|1 個複本、無讀取規模|3 個複本、1 個[讀取規模](sql-database-read-scale-out.md)、區域備援 HA|
|備份|RA-GRS、7-35 天 (預設為 7 天)|RA-GRS、7-35 天 (預設為 7 天)|
|記憶體內|N/A|支援|
|||

> [!IMPORTANT]
> 如果您需要的計算容量少於一個虛擬核心，請使用以 DTU 為基礎的購買模型。

請參閱 [SQL Database 常見問題集](sql-database-faq.md)以取得常見問題的解答。 

## <a name="storage-considerations"></a>儲存體考量

請考慮下列：
- 所配置的儲存體是供資料檔案 (MDF) 和記錄檔 (LDF) 檔案來使用的。
- 每個效能等級所支援的資料庫大小有其上限，預設大小上限為 32 GB。
- 在設定所需的資料庫大小 (MDF 的大小) 時，系統會自動另外加 30% 的儲存體空間以支援 LDF
- 您可以選取 10 GB 到所支援上限之間的任何資料庫大小
 - 針對標準儲存體，以 10-GB 為增量單位增加或減少大小
 - 針對進階儲存體，以 250-GB 為增量單位增加或減少大小
- 在一般用途服務層中，`tempdb` 會使用連結的 SSD，且這個儲存體成本會包含在虛擬核心價格中。
- 在商務關鍵性服務層中，`tempdb` 會與 MDF 和 LDF 檔案共用連結的 SSD，且 tempDB 儲存體成本會包含在虛擬核心價格中。

> [!IMPORTANT]
> 您必須支付為 MDF 和 LDF 所配置的總儲存體費用。

若要監視 MDF 和 LDF 目前總計的大小，請使用 [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)。 若要監視個別 MDF 和 LDF 檔案的目前大小，請使用 [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)。

## <a name="backups-and-storage"></a>備份和儲存體

為了支援 SQL Database 的還原時間點 (PITR) 和長期保留 (LTR) 功能，系統會配置儲存體供資料庫備份使用。 這個儲存體會分別配置給每個資料庫，並以兩個不同的每一資料庫費用來計費。 

- **PITR**：個別的資料庫備份會自動複製到 RA-GRS 儲存體。 儲存體大小會隨著新備份的建立而動態地增加。  每週完整備份、每日差異備份以及每 5 分鐘複製一次的交易記錄備份都使用此儲存體。 儲存體耗用量取決於資料庫的變動率及保留期限。 您可以為每個資料庫設定 7 到 35 天的不同保留期限。 系統會提供等於資料大小 1 倍的最小儲存體數量，且無額外費用。 對於大多數資料庫來說，此數量就足以儲存 7 天份的備份。
- 
  **LTR**：SQL Database 提供選項讓您設定完整備份的長期保留期，最長可達 10 年之久。 如果啟用 LTR 原則，這些備份會自動儲存在 RA-GRS 儲存體中，但您可以控制備份的複製頻率。 為了符合不同的合規性需求，您可以針對每週、每月和/或每年備份選取不同的保留期限。 此設定會定義要將多少儲存體用於 LTR 備份。 您可以使用 LTR 定價計算機來估算 LTR 儲存體的成本。 如需詳細資訊，請參閱[長期保存](sql-database-long-term-retention.md)。

## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

在以虛擬核心為基礎的購買模型中，您可以使用[適用於 SQL Server 的 Azure Hybrid Use Benefit](../virtual-machines/windows/hybrid-use-benefit-licensing.md)，以折扣優惠在 SQL Database 上交換現有授權。 這個 Azure 權益可讓您使用具備軟體保證的內部部署 SQL Server 授權，在 Azure SQL Database 上使用內部部署 SQL Server 授權省下最高 30% 的成本。

![定價](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-of-single-databases-with-geo-replication-links"></a>使用異地複寫連結遷移單一資料庫

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

## <a name="migration-of-failover-groups"></a>遷移容錯移轉群組 

在遷移具有多個資料庫的容錯移轉群組時，必須個別遷移主要和次要資料庫。 過程中適用相同的考量和排序規則。 資料庫轉換成以虛擬核心為基礎的模型之後，容錯移轉群組在相同的原則設定下仍會有效。 

## <a name="creation-of-a-geo-replication-secondary"></a>建立異地複寫次要資料庫

您只能使用和主要資料庫相同的服務層來建立異地次要資料庫。 對於記錄產生率較高的資料庫，建議您使用和主要資料庫相同的效能等級來建立次要資料庫。 如果您要在彈性集區中為單一主要資料庫建立異地次要資料庫，建議您讓集區具有符合主要資料庫效能等級的 `maxVCore` 設定。 如果您要在彈性集區中為另一個彈性集區中的主要資料庫建立異地次要資料庫，建議您讓集區具有相同的 `maxVCore` 設定

## <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>使用資料庫複製將以 DTU 為基礎的資料庫轉換成以虛擬核心為基礎的資料庫。

您可以將任何資料庫 (具有以 DTU 為基礎的效能等級) 複製到資料庫 (具有以虛擬核心為基礎的效能等級)，而不會有任何限制或特殊排序，但前提是目標效能等級支援來源資料庫的最大資料庫大小。 這是因為複製資料庫時會在複製作業開始時建立資料的快照集，而且不會在來源和目標之間執行資料同步。 

## <a name="next-steps"></a>後續步驟

- 如需特定效能等級的詳細資訊和單一資料庫可用的儲存體大小選項，請參閱[單一資料庫 SQL Database 以虛擬核心為基礎的資源限制](sql-database-vcore-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels)
- 如需特定效能等級的詳細資訊和彈性集區可用的儲存體大小選項，請參閱[彈性集區 SQL Database 以虛擬核心為基礎的資源限制](sql-database-vcore-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels)。
