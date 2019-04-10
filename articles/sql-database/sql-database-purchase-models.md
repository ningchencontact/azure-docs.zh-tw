---
title: Azure SQL Database 購買模型 | Microsoft Docs
description: 了解 Azure SQL Database 服務中屬於可用資料庫的購買模型。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 46a620900896d07273da22e53171330b85d3f1ec
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360184"
---
# <a name="azure-sql-database-purchasing-models"></a>Azure SQL Database 購買模型

Azure SQL Database 可讓您輕鬆地購買符合您效能和成本需求且完全受控的 PaaS 資料庫引擎。 您可以根據 Azure SQL Database 的部署模型，選擇符合您需求的購買模型：

- [以 V 核心形式的購買模型](sql-database-service-tiers-vcore.md) (建議)，可讓您選擇確切的儲存體容量，並計算所需的工作負載。
- [DTU 形式的購買模型](sql-database-service-tiers-dtu.md)，可讓您針對常見的工作負載，選擇搭配平衡的計算和儲存體套件。

Azure SQL Database 部署模型提供不同的購買模型：

- [Azure SQL Database](sql-database-technical-overview.md) 中的[單一資料庫](sql-database-single-databases-manage.md)與[彈性集區](sql-database-elastic-pool.md)部署選項會提供 [DTU 形式的購買模型](sql-database-service-tiers-dtu.md)和 [V 核心形式的購買模型](sql-database-service-tiers-vcore.md)。
- Azure SQL Database 中的[受控執行個體](sql-database-managed-instance.md)僅提供 [V 核心形式的購買模型](sql-database-service-tiers-vcore.md)部署選項。

> [!IMPORTANT]
> [超大規模服務層 (預覽)](sql-database-service-tier-hyperscale.md) 處於公開預覽階段，僅適用於使用 V 核心購買模型的單一資料庫。

下列資料表和圖表會比較和對照這兩種購買模型。

|**購買模型**|**描述**|**適用對象**|
|---|---|---|
|以 DTU 為基礎的模型|此模型是以計算、儲存體和 IO 資源的配套量值為基礎。 單一資料庫的計算大小會以資料庫交易單位 (DTU) 表示，而彈性集區的計算大小則會以彈性資料庫交易單位 (eDTU) 表示。 如需 DTU 和 eDTU 的詳細資訊，請參閱[什麼是 DTU 和 eDTU？](sql-database-purchase-models.md#dtu-based-purchasing-model)。|適合想要簡單選項且該選項已預先設定好資源的客戶。|
|以虛擬核心為基礎的模型|此模型可讓您獨立地選擇計算和儲存體資源。 V 核心形式的購買模型也可讓您使用[適用於 SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，以節省成本。|適合重視彈性、控制力和透明度的客戶。|
||||  

![定價模型](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>計算成本

計算成本會反映已為應用程式佈建的總計算容量。 在業務關鍵服務層中，我們會自動配置至少 3 個複本。 為了反映此電腦資源的額外配置，V 核心形式的購買模型中的價格，比一般用途服務層中的業務關鍵服務層的價格高約 2.7 倍。 基於相同原因，業務關鍵服務層中較高的每一 GB 儲存體價格，則是反映 SSD 儲存體的高 IO 和低延遲性。 同時，這兩個服務層的備份儲存體成本之間沒有差別，因為兩種情況下都是使用標準儲存體類別。

## <a name="storage-costs"></a>儲存成本

不同類型的儲存體會以不同方式計費。 對於資料儲存體，您必須根據您所選取的資料庫或集區大小上限，為佈建的儲存體支付費用。 除非您減少或增加此最大值，否則成本不會變更。 備份儲存體與您執行個體的自動備份相關聯，而且是動態配置的。 增加您的備份保留期限，即會增加執行個體所取用的備份儲存體。

根據預設，系統會將您資料庫的 7 天自動備份複製到 RA-GRS 標準 Blob 儲存體。 每週完整備份、每日差異備份以及每 5 分鐘複製一次的交易記錄備份都使用此儲存體。 交易記錄檔的大小取決於資料庫的變動率。 會提供等於資料庫大小 100% 的最低儲存空間，且無額外費用。 備份儲存體的額外使用量會按每月每 GB 來收費。

如需儲存體價格的詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/sql-database/single/)頁面。

## <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

虛擬核心代表可在硬體世代與硬體實體特性 (例如，核心數目、記憶體、儲存體大小) 之間作選擇的邏輯 CPU。 虛擬核心形式的購買模型可讓您以彈性、可控制且透明的方式耗用個別資源，並讓您直接將內部部署工作負載需求平移到雲端。 此模型可讓您根據工作負載需求，選擇計算、記憶體和儲存體。 在 V 核心形式的購買模型中，您可以就[單一資料庫](sql-database-single-database-scale.md)、[彈性集區](sql-database-elastic-pool.md)及[受控執行個體](sql-database-managed-instance.md)選擇[一般用途](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)和[業務關鍵](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)服務層。 若為單一資料庫，您也可以選擇[超大規模服務層 (預覽)](sql-database-service-tier-hyperscale.md)。

虛擬核心形式的採購模型可讓您獨立地選擇計算和儲存體資源，並符合內部部署效能需求，以及獲得最佳價格。 在虛擬核心形式的購買模型中，客戶需支付下列費用：

- 計算 (服務層 + 虛擬核心數目和記憶體數量 + 硬體世代)
- 資料和記錄儲存體的類型和數量
- 備份儲存體 (RA-GRS)

> [!IMPORTANT]
> 計算、IO、資料和記錄儲存體則依每個資料庫或彈性集區來收費。 備份儲存體是依每個資料庫來收費。 如需受控執行個體費用的詳細資訊，請參閱[受控執行個體](sql-database-managed-instance.md)。
> **區域限制：** 如需目前支援的區域清單，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)。 如果您想要在目前不支援的區域中建立受控執行個體，您可以[透過 Azure 入口網站傳送支援要求](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance)。
上也提供本文中使用的原始碼。

如果您的單一資料庫或彈性集區取用超過 300 DTU，則轉換為 V 核心形式的購買模型可減少您的成本。 如果您決定轉換，您可以使用您選擇的 API 或使用 Azure 入口網站來轉換，且沒有任何停機時間。 不過，不需要轉換且轉換不會自動完成。 如果 DTU 形式的購買模型符合您的效能和商務需求，請繼續使用即可。 如果您決定要從 DTU 形式的購買模型轉換為 V 核心形式的購買模型，請使用下列經驗法則選取計算大小：

- 標準層中的每 100 DTU 需要至少 1 個一般用途層中的 V 核心
- 進階層中每 125 DTU 需要至少 1 個業務關鍵層中的 V 核心

## <a name="dtu-based-purchasing-model"></a>以 DTU 為基礎的購買模型

資料庫交易單位 (DTU) 代表混合了 CPU、記憶體、讀取和寫入的量值。 以 DTU 為基礎的購買模型會提供一組預先設定好的計算資源組合和所包含的儲存體，以期達成不同的應用程式效能等級。 想要簡單一點，使用已預先設定好的組合並每月支付固定費用的客戶，可能會發現以 DTU 為基礎的模型更適合他們的需求。 在 DTU 形式的購買模型中，客戶可以就[單一資料庫](sql-database-single-database-scale.md)與[彈性集區](sql-database-elastic-pool.md)選擇**基本**、**標準**和**進階**服務層。 此購買模型不適用於[受空執行個體](sql-database-managed-instance.md)。

### <a name="database-transaction-units-dtus"></a>資料庫交易單位 (DTU)

針對[服務層](sql-database-single-database-scale.md)中特定計算大小的單一資料庫，Microsoft 保證該資料庫具備特定的資源層級 (與 Azure 雲端的其他任何資料庫無關)，並提供可預測的效能等級。 資源數量會計算為資料庫交易單位 (DTU) 數量，且為計算、儲存體和 I/O 資源的配套測量。 這些資源的比率，原本是由專為一般實際 OLTP 工作負載所設計的 [OLTP 基準測試工作負載](sql-database-benchmark-overview.md)來判定。 若您的工作負載超過這些任一資源的數量，系統即會節流處理輸送量，因而導致效能變慢和逾時。 您工作負載使用的資源，不會影響到 Azure 雲端中其他 SQL Database 的可用資源，且其他工作負載所用的資源亦不會影響到您 SQL Database 的可用資源。

![週框方塊](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTU 最適合用於了解處於不同計算大小與服務層之各 Azure SQL Database 間的相對量。 例如，藉由提升資料庫的計算大小來使 DTU 加倍，等於讓該資料庫可用的資源集合加倍。 例如，相較於具有 5 個 DTU 的 Basic 資料庫，具有 1750 個 DTU 的 Premium P11 資料庫可提供 350 倍的 DTU 計算能力。  

若要工作負載的資源 (DTU) 使用量的深入見解，請使用[查詢效能見解](sql-database-query-performance.md)執行以下動作：

- 依 CPU/持續時間/執行計數識別排名最前面的查詢，對其進行微調可能有助於改善效能。 例如，IO 密集使用的查詢可能會因使用[記憶體內最佳化技術](sql-database-in-memory.md)獲得助益，可更加妥善地運用處於特定服務層和計算大小的可用記憶體。
- 向下鑽研查詢的詳細資料，以檢視其文字和資源使用量的歷程記錄。
- 存取效能微調建議，其會顯示 [SQL Database Advisor](sql-database-advisor.md) 執行的動作。

### <a name="elastic-database-transaction-units-edtus"></a>彈性資料庫交易單位 (eDTU)

與其針對 SQL Database 提供不一定隨時需要的隨時可用專屬資源集 (DTU)，您可選擇將資料庫置於 SQL Database 上的[彈性集區](sql-database-elastic-pool.md)以在各資料庫之間共用資源集區。 彈性集區中的共用資源，是以彈性資料庫交易單位 (eDTU) 來測量。 彈性集區提供符合成本效益的簡單解決方案，以管理多個不同且具備無法預測的使用模式資料庫的效能目標。 彈性集區可保證集區中的一個資料庫不會耗盡資源，同時可確保集區中的每個資料庫隨時至少有最低必要資源量可以使用。

集區以固定價格提供固定數目的 eDTU。 在彈性集區內，會給予個別資料庫彈性以在設定的界限內自動調整。 負載量較重的資料庫會取用更多的 eDTU 以滿足需求。 負載量較輕的資料庫會取用較少的 eDTU。 沒有負載的資料庫不會取用 eDTU。 針對整個集區佈建資源，而不是針對每個資料庫佈建資源，可簡化管理工作以提供可預測的集區預算。

其他 eDTU 可以新增至現有集區，而不會造成資料庫停機，且對集區中資料庫沒有影響。 同樣地，如果不再需要額外 eDTU，則隨時可以從現有集區中移除。 您可以增減集區中的資料庫，或限制資料庫在沈重負載下可以使用的 eDTU，以便為其他資料庫保留 eDTU。 如果可預測某個資料庫不會充分使用資源，您可以將它移出集區，並設定為具有可預測所需資源量的單一資料庫。

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>判斷工作負載所需的 DTU 數目

如果您希望將現有的內部部署或 SQL Server 虛擬機器工作負載遷移到 Azure SQL Database，您可以使用 [DTU 計算機](https://dtucalculator.azurewebsites.net/) \(英文\) 來估計所需的 DTU 數目。 對於現有的 Azure SQL Database 工作負載，您可以使用[查詢效能深入解析](sql-database-query-performance.md)來了解您的資料庫資源使用量 (DTU)，以深入了解如何將工作負載最佳化。 您也可以使用 [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV，來檢視最近一小時的資源耗用量。 或者，目錄檢視 [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) 會顯示最近 14 天的資源耗用量，不過會以五分鐘的平均值來表示，其精確度較低。

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>能受益於彈性資源集區的工作負載

集區適合於具備特定使用模式的大量資料庫。 針對指定的資料庫，此模式的特徵是低使用量平均與相對不頻繁的使用量高峰。 SQL Database 會自動評估現有 SQL Database 伺服器中資料庫過去的資源使用量，並在 Azure 入口網站中建議適當的集區組態。 如需詳細資訊，請參閱 [何時應該使用彈性集區？](sql-database-elastic-pool.md)

## <a name="purchase-model-frequently-asked-questions-faq"></a>購買模型的常見問題集 (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>是否需要先讓應用程式離線，才能從以 DTU 為基礎的資料庫轉換為以 vCore 為基礎的服務層

新的服務層提供簡單的線上轉換方法，類似現在將資料庫從標準版升級為進階版服務層的程序，反之亦然。 這項轉換可使用 Azure 入口網站、PowerShell、Azure CLI、T-SQL 或 REST API 來起始。 請參閱[管理單一資料庫](sql-database-single-database-scale.md)和[管理彈性集區](sql-database-elastic-pool.md)。

### <a name="can-i-convert-a-database-from-a-service-tier-using-the-vcore-based-purchase-to-a-service-tier-using-the-dtu-based-purchasing-model"></a>是否可以將使用 V 核心形式的購買之服務層的資料庫，轉換成使用 DTU 形式的購買之服務層的資料庫

是，您可以使用 Azure 入口網站、PowerShell、Azure CLI、T-SQL 或 REST API，輕鬆地將資料庫轉換為任何支援的效能目標。 請參閱[管理單一資料庫](sql-database-single-database-scale.md)和[管理彈性集區](sql-database-elastic-pool.md)。

## <a name="next-steps"></a>後續步驟

- 針對虛擬核心形式的購買模型，請參閱[虛擬核心形式的購買模型](sql-database-service-tiers-vcore.md)
- 如需以 DTU 為基礎的購買模型，請參閱[以 DTU 為基礎的購買模型](sql-database-service-tiers-dtu.md)。
