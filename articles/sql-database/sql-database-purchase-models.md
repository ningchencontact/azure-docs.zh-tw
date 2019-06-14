---
title: Azure SQL Database 購買模型 | Microsoft Docs
description: 深入了解適用於 Azure SQL Database 的購買模型。
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
ms.date: 04/26/2019
ms.openlocfilehash: 98c19732c372fbcda3ca8e746d002f94c2687b22
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66431362"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>虛擬核心與 DTU 購買模型之間選擇

Azure SQL Database 可讓您輕鬆地購買一個完全受控的平台，以符合您的效能和成本需求的服務 (PaaS) 資料庫引擎。 根據您所選擇 Azure SQL Database 的部署模型，您可以選取適合您的購買模型：

- [虛擬核心 (vCore) 為基礎的購買模型](sql-database-service-tiers-vcore.md)（建議選項）。 這個的購買模型提供佈建的計算層與無伺服器 （預覽） 計算層次之間選擇。 佈建的計算層中，使用中，您可以選擇一律佈建的計算資源的確切量您的工作負載。 無伺服器計算層中，您可以指定運算資源的自動調整可設定的計算範圍內。 與此計算層級中，您可以也會自動暫停和繼續工作負載活動為依據的資料庫。 每個時間單位的虛擬核心單位價格較低佈建的計算層中比在無伺服器計算層。
- [資料庫交易單位 (DTU) 為基礎的購買模型](sql-database-service-tiers-dtu.md)。 這個的購買模型提供配套的一般工作負載平衡的計算和儲存體套件。

不同的購買模型可供不同的 Azure SQL Database 部署模型：

- [Azure SQL Database](sql-database-technical-overview.md) 中的[單一資料庫](sql-database-single-databases-manage.md)與[彈性集區](sql-database-elastic-pool.md)部署選項會提供 [DTU 形式的購買模型](sql-database-service-tiers-dtu.md)和 [V 核心形式的購買模型](sql-database-service-tiers-vcore.md)。
- [受管理的執行個體](sql-database-managed-instance.md)Azure SQL Database 中的部署選項只提供[以 vCore 為基礎的購買模型](sql-database-service-tiers-vcore.md)。
- [超大規模的服務層](sql-database-service-tier-hyperscale.md)適用於使用單一資料庫[以 vCore 為基礎的購買模型](sql-database-service-tiers-vcore.md)。

下列資料表和圖表會比較和對照以 vCore 為基礎和以 DTU 為基礎的購買模型：

|**購買模型**|**說明**|**適用對象**|
|---|---|---|
|以 DTU 為基礎的模型|此模型根據計算、 儲存體和 I/O 資源的配套量值。 在單一資料庫的 Dtu 和彈性集區的彈性資料庫交易單位 (Edtu) 表示計算大小。 如需 Dtu 和 Edtu 的詳細資訊，請參閱[什麼是 Dtu 和 Edtu？](sql-database-purchase-models.md#dtu-based-purchasing-model)。|最適合客戶，想要簡單、 預先設定資源選項。|
|以虛擬核心為基礎的模型|此模型可讓您獨立地選擇計算和儲存體資源。 以虛擬核心為基礎的購買模型也可讓您使用[適用於 SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，以節省成本。|適合重視彈性、控制力和透明度的客戶。|
||||  

![定價模型比較](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>計算成本

### <a name="provisioned-compute-costs"></a>佈建的計算成本

在佈建的計算層中，計算成本會反映已佈建應用程式的總計算容量。

在業務關鍵服務層級中，我們會自動配置至少 3 個複本。 為了反映這額外的計算資源配置，以 vCore 為基礎的購買模型中的價格會是大約 2.7 x 高業務關鍵服務層中比在一般用途服務層。 同樣地，在業務關鍵服務層中的每 GB 更高儲存體價格反映 SSD 儲存體的低延遲與高 I/O。

因為這兩層使用標準儲存體的備份儲存體的成本是業務關鍵服務層和一般用途服務層相同。

### <a name="serverless-compute-costs"></a>無伺服器運算成本

如無伺服器計算層會計算成本和計算容量的定義方式說明，請參閱 < [SQL Database 無伺服器 （預覽）](sql-database-serverless.md)。

## <a name="storage-costs"></a>儲存成本

不同類型的儲存體會以不同方式計費。 對於資料儲存體，您需支付已佈建的儲存體，根據您選取的最大資料庫或集區大小。 除非您減少或增加此最大值，否則成本不會變更。 備份儲存體與您執行個體的自動備份相關聯，而且是動態配置的。 增加您備份保留期限可增加您的執行個體所使用的備份儲存體。

根據預設，7 天的自動備份您的資料庫會複製到讀取權限異地備援儲存體 (RA-GRS) 標準 Blob 儲存體帳戶。 每週完整備份、 每日差異備份，並複製每隔 5 分鐘的交易記錄備份，會使用此儲存體。 交易記錄檔的大小取決於資料庫的變動率。 最小的儲存體數量等於 100%的資料庫大小會提供不收取額外費用。 備份儲存體的額外使用量，會按月以 GB 為單位收費。

如需儲存體價格的詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/sql-database/single/)頁面。

## <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

虛擬核心 (vCore) 代表邏輯 CPU，並提供您選項來選擇各硬體世代與硬體 （例如核心、 記憶體和儲存體大小的數字） 的實體特性。 以 vCore 為基礎的購買模型提供彈性、 控制、 透明的個別資源耗用量，並直接的方法來轉譯在內部部署至雲端的工作負載需求。 此模型可讓您可以選擇根據您的工作負載需求的計算、 記憶體和儲存體資源。

在以 vCore 為基礎的購買模型，您可以選擇[一般用途](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)並[業務關鍵](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)服務層[單一資料庫](sql-database-single-database-scale.md)， [彈性集區](sql-database-elastic-pool.md)，並[受管理執行個體](sql-database-managed-instance.md)。 為單一資料庫，您也可以選擇[超大規模的服務層](sql-database-service-tier-hyperscale.md)。

以 vCore 為基礎的購買模型可讓您獨立地選擇計算和儲存體資源、 符合內部部署的效能，並獲得最佳價格。 在以 vCore 為基礎的購買模型，您需支付：

- 計算資源 （服務層 + 虛擬核心數目和記憶體 + 硬體世代的數量）。
- 類型和數量的資料和記錄的儲存體。
- 備份儲存體 (RA-GRS)。

> [!IMPORTANT]
> 計算資源、 I/O 和資料和記錄的儲存體收取每個資料庫或彈性集區。 備份儲存體是依每個資料庫計費。 如需受控執行個體費用的詳細資訊，請參閱[受控執行個體](sql-database-managed-instance.md)。
> **區域限制：** 如需目前支援的區域清單，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)。 在 目前不支援，在區域中建立的受管理的執行個體[透過 Azure 入口網站將支援要求傳送](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance)。

如果您的單一資料庫或彈性集區耗用超過 300 個 Dtu，轉換為以 vCore 為基礎的購買模型可能會降低您的成本。 使用您選擇的 API，或使用 Azure 入口網站中，而且完全不停機，您可以將轉換。 不過，轉換並非必要，且不會自動完成。 如果以 DTU 為基礎的購買模型符合您的效能和商務需求，請繼續使用即可。

若要從以 DTU 為基礎的購買模型轉換以 vCore 為基礎的購買模型，請使用下列規則的經驗法則選取的計算大小：

- 在標準層中每 100 Dtu 需要至少 1 個 vCore 的一般用途服務層中。
- 在進階層中每 125 Dtu 需要至少 1 個 vCore 業務關鍵服務層中。

## <a name="dtu-based-purchasing-model"></a>以 DTU 為基礎的購買模型

資料庫交易單位 (DTU) 表示 CPU、 記憶體、 讀取和寫入的混合量的值。 以 DTU 為基礎的購買模型會提供一組預先設定好的計算資源組合和所包含的儲存體，以期達成不同的應用程式效能等級。 如果您偏好簡單的預先設定的資源組合和支付固定的費用的每個月，以 DTU 為基礎的模型可能更適合您的需求。

在以 DTU 為基礎的購買模型，您可以選擇基本、 標準和 premium 服務層之間同時[單一資料庫](sql-database-single-database-scale.md)並[彈性集區](sql-database-elastic-pool.md)。 以 DTU 為基礎的購買模型不適用於[受管理執行個體](sql-database-managed-instance.md)。

### <a name="database-transaction-units-dtus"></a>資料庫交易單位 (DTU)

單一資料庫在特定計算中的大小[服務層](sql-database-single-database-scale.md)，Microsoft 保證該資料庫的資源的特定層級 （而不是 Azure 雲端中的其他任何資料庫）。 這項保證提供可預測的效能等級。 為資料庫配置的資源數量的計算方式為的 Dtu 數目，並為計算、 儲存體和 I/O 資源的配套量值。

這些資源之間的比率原先由[線上交易處理 (OLTP) 基準測試工作負載](sql-database-benchmark-overview.md)設計成能夠代表實際 OLTP 工作負載。 當您的工作負載超過這些任一資源的數量時，您的輸送量會進行節流，導致效能變慢和逾時。

您的工作負載所使用的資源不會影響到其他 Azure 雲端中的 SQL 資料庫的可用資源。 同樣地，其他工作負載所使用的資源不會影響您的 SQL database 的可用資源。

![週框方塊](./media/sql-database-what-is-a-dtu/bounding-box.png)

Dtu 是最有助於了解配置給在不同計算大小和服務層的 Azure SQL database 的相對資源。 例如:

- Dtu 加倍藉由增加資料庫的計算大小，等於讓該資料庫可用的資源集合加倍。
- 1750 個 Dtu 的 premium 服務層 P11 資料庫可提供 350 倍的 DTU 計算超過 5 個 Dtu 的基本服務層資料庫的能力。  

若要深入探索您的工作負載的資源 (DTU) 耗用量，請使用[查詢效能深入解析](sql-database-query-performance.md)來：

- 可以微調有可能會改善效能的 CPU/持續時間/執行計數識別排名最前面的查詢。 比方說，我 i/o 密集型查詢可能會受益於[在記憶體內最佳化技術](sql-database-in-memory.md)為了更妥善運用可用的記憶體，在特定的服務層，計算大小。
- 向下切入至查詢，以檢視其文字和其資源使用量的歷程記錄的詳細資料。
- 存取效能微調建議，顯示所採取的動作[SQL Database 建議程式](sql-database-advisor.md)。

### <a name="elastic-database-transaction-units-edtus"></a>彈性資料庫交易單位 (Edtu)

永遠都可以使用的 SQL 資料庫，而不是比提供一組專用的資源 (Dtu)，可能不一定需要您可以將這些資料庫[彈性集區](sql-database-elastic-pool.md)。 彈性集區中的資料庫位於單一 Azure SQL Database 伺服器和共用資源集區。

彈性資料庫交易單位 (Edtu) 來測量在彈性集區中的共用的資源。 彈性集區提供簡單、 符合成本效益的解決方案，以管理多個資料庫有顯著不同且無法預測的使用模式的效能目標。 彈性集區可確保所有資源無法都使用由集區的單一資料庫，同時確保集區中的每個資料庫一律具有最少的所需的資源。

集區以固定價格提供固定數目的 eDTU。 在彈性集區中個別資料庫可以自動調整規模設定的界限內。 較重負載之下的資料庫會耗用較多 Edtu 以滿足需求。 在較低負載下的資料庫會耗用較少的 Edtu。 沒有負載的資料庫不會取用 eDTU。 資源會佈建，讓整個集區，而不是每個資料庫，因為彈性集區會簡化管理工作，並提供可預測的預算，集區。

您可以在現有的集區資料庫不必停機，而不會影響在集區的資料庫上新增額外的 Edtu。 同樣地，如果您不再需要額外 Edtu，移除其現有的集區在任何時間。 您也可以新增至資料庫，或隨時減少從集區的資料庫。 若要保留的其他資料庫的 Edtu，限制資料庫可以使用的負載過重的 Edtu 數。 如果資料庫一致的方式 underuses 資源，將它移出集區，並將它設定為單一資料庫與可預測所需的資源數量。

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>判斷工作負載所需的 DTU 數目

如果您想要移轉現有內部部署或 Azure SQL Database，使用 SQL Server 虛擬機器工作負載[DTU 計算機](https://dtucalculator.azurewebsites.net/)來估計所需的 Dtu 數目。 對於現有的 Azure SQL Database 工作負載中，使用[查詢效能深入解析](sql-database-query-performance.md)了解您的資料庫資源耗用量 (Dtu)，並取得更深入的見解，來最佳化您的工作負載。 [Sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)動態管理檢視 (DMV) 可讓您檢視過去一小時內的資源耗用量。 [Sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)目錄檢視會顯示過去 14 天，但精確度較低的五分鐘平均值的資源耗用量。

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>能受益於彈性資源集區的工作負載

集區非常適合使用低資源使用率平均和相對不頻繁的使用量高峰的資料庫。 SQL Database 會自動評估現有的 SQL Database 伺服器上的資料庫的歷程記錄資源使用量，並且建議您在 Azure 入口網站中適當的集區設定。 如需詳細資訊，請參閱 <<c0> [ 何時該考慮 SQL Database 彈性集區？](sql-database-elastic-pool.md)。

## <a name="frequently-asked-questions-faqs"></a>常見問題集 (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>我是否需要採取應用程式離線，從以 DTU 為基礎的服務層轉換成虛擬核心為基礎的服務層？

沒有。 您不需要讓應用程式離線。 新的服務層會提供類似於現有的處理序，將資料庫從標準升級至 premium 服務層和利用其他方式的簡單線上轉換方法。 您可以使用 Azure 入口網站、 PowerShell、 Azure CLI、 T-SQL、 或 REST API，來啟動這項轉換。 請參閱[管理單一資料庫](sql-database-single-database-scale.md)和[管理彈性集區](sql-database-elastic-pool.md)。

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>可以將轉換的資料庫從以 vCore 為基礎的購買模型中的服務層以 DTU 為基礎的購買模型中的服務層？

是，您可以輕鬆轉換您的資料庫到任何支援的效能目標，使用 Azure 入口網站、 PowerShell、 Azure CLI、 T-SQL、 或 REST API。 請參閱[管理單一資料庫](sql-database-single-database-scale.md)和[管理彈性集區](sql-database-elastic-pool.md)。

## <a name="next-steps"></a>後續步驟

- 如需有關以 vCore 為基礎的購買模型的詳細資訊，請參閱[虛擬核心為基礎的購買模型](sql-database-service-tiers-vcore.md)。
- 如需有關以 DTU 為基礎的購買模型的詳細資訊，請參閱[以 DTU 為基礎的購買模型](sql-database-service-tiers-dtu.md)。
