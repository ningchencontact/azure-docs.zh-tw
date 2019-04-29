---
title: Azure SQL Database 超大規模資料庫常見問題集 | Microsoft Docs
description: 回答客戶針對超大規模資料庫服務層級中的 Azure SQL 資料庫 (通常稱為「超大規模」資料庫) 經常提出的問題。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/26/2019
ms.openlocfilehash: 7746d7256add185be0c67123edf63ea09b6b05a4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111827"
---
# <a name="faq-about-azure-sql-hyperscale-databases"></a>關於 Azure SQL 超大規模資料庫的常見問題

本文將回答客戶在考慮使用 Azure SQL Database 超大規模資料庫服務層級中的資料庫 (通常稱為「超大規模」資料庫，目前處於公開預覽階段) 時經常提出的問題。 本文將說明「超大規模資料庫」支援的案例，以及在一般情況下與「SQL Database 超大規模資料庫」相容的跨功能服務。

- 此常見問題集適用於大致了解超大規模資料庫服務層級，而想就其特定的問題與顧慮尋求解答的讀者。
- 此常見問題集並非 SQL Database 超大規模資料庫的使用指南，也不會解答關於操作方式的問題。 如有此需求，建議您參閱 [Azure SQL Database 超大規模資料庫](sql-database-service-tier-hyperscale.md)文件。

## <a name="general-questions"></a>一般問題

### <a name="what-is-a-hyperscale-database"></a>什麼是超大規模資料庫

超大規模資料庫是超大規模資料庫服務層級之中採用超大規模相應放大儲存體技術的 Azure SQL 資料庫。 超大規模資料庫最多可支援 100 TB 的資料，並提供高輸送量和高效能，以及快速調整以因應工作負載需求的能力。 調整對應用程式而言是透明的 (連線性、查詢處理等等)，運作方式與任何其他 SQL 資料庫相同。

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>哪些資源類型和購買模型支援超大規模資料庫

超大規模資料庫服務層級僅適用於 Azure SQL Database 中的單一資料庫，且採用的必須是以虛擬核心為基礎的購買模型。  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>超大規模資料庫服務層級與一般用途和商務關鍵服務層有何不同

以虛擬核心為基礎的服務層主要可根據可用性、儲存體類型和 IOPS 來區分。

- 一般用途服務層針對 IO 延遲或容錯移轉時間並非優先考量的案例提供了一組平衡的計算和儲存體選項，適用於大多數的商務工作負載。
- 超大規模資料庫服務層級對於非常大的資料庫工作負載可發揮最佳效益。
- 商務關鍵服務層適用於 IO 延遲是優先考量的商務工作負載。

| | 資源類型 | 一般用途 |  超大規模資料庫 | 業務關鍵 |
|:---|:---:|:---:|:---:|:---:|
| **適用對象** |全部|  大部分的商業工作負載。 提供以預算為導向且平衡的計算與儲存體選項。 | 具有大型資料容量需求，且能夠自動調整儲存體並流暢地調整計算的資料應用程式。 | 具有高交易率和最低延遲 IO 的 OLTP 應用程式。 使用數個分開的複本，針對失敗提供最高的復原能力。|
|  **資源類型** ||單一資料庫/彈性集區/受控執行個體 | 單一資料庫 | 單一資料庫/彈性集區/受控執行個體 |
| **計算大小**|單一資料庫/彈性集區 * | 1 到 80 個虛擬核心 | 1 到 80 個虛擬核心* | 1 到 80 個虛擬核心 |
| |受控執行個體 | 8 個、16 個、24 個、32 個、40 個、64 個、80 個虛擬核心 | N/A | 8 個、16 個、24 個、32 個、40 個、64 個、80 個虛擬核心 |
| **儲存體類型** | 全部 |進階遠端儲存體 (每個執行個體) | 與本機 SSD 快取分離的儲存體 (每個執行個體) | 超快速本機 SSD 儲存體 (每個執行個體) |
| **儲存體大小** | 單一資料庫/彈性集區 | 5 GB – 4 TB | 最多 100 TB | 5 GB – 4 TB |
| | 受控執行個體  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **IO 輸送量** | 單一資料庫** | 每個虛擬核心 500 IOPS，且 IOPS 上限為 7000 | 目前未知 | 5000 IOPS，IOPS 上限為 200,000|
| | 受控執行個體 | 視檔案大小而定 | N/A | 受控執行個體：視檔案大小而定|
|**可用性**|全部|1 個複本、無讀取規模、無本機快取 | 多個複本、最多 15 個讀取規模、部分本機快取 | 3 個複本、1 個讀取規模、區域備援 HA、完整本機快取 |
|**備份**|全部|RA-GRS、7-35 天 (預設為 7 天)| RA-GRS、7-35 天 (預設值為 7 天)、常數時間點復原 (PITR) | RA-GRS、7-35 天 (預設為 7 天) |

\* 超大規模服務層不支援彈性集區

### <a name="who-should-use-the-hyperscale-service-tier"></a>誰應使用超大規模資料庫服務層級

超大規模資料庫服務層級主要適用於具有大型內部部署 SQL Server 資料庫，且想要藉由移至雲端來現代化其應用程式的客戶，或是已使用 SQL Server 資料庫而想要大幅擴充資料庫成長潛能的客戶。 超大規模資料庫也適用於想要兼顧高效能和高延展性的客戶。 使用超大規模資料庫，您將可：

- 支援最多 100 TB 的資料庫大小
- 快速備份資料庫而無須考慮資料庫大小 (備份以檔案快照集為基礎)
- 快速還原資料庫而無須考慮資料庫大小 (還原會從檔案快照集執行)
- 提高記錄輸送量進而加快交易認可速度，而無須考慮資料庫大小
- 將讀取規模相應放大至一或多個唯讀節點，以卸除讀取工作負載，並作為熱待命。
- 快速相應增加計算 (依常數時間) 以充分因應大量工作負載中，然後再依常數時間相應減少。 其運作方式類似於 P6 到 P11 之間的相應增加和相應減少，但速度會快得多，因為這並非資料作業的大小。

### <a name="what-regions-currently-support-hyperscale"></a>目前有哪些區域支援超大規模資料庫

超大規模資料庫目前可在下列區域的單一資料庫中使用：西部 US1、 美國西部 2、 東亞 US1、 美國中部、 西歐、 北歐、 東南亞、 日本東部、 韓國中部、 澳大利亞東南部、 和澳大利亞東部。

### <a name="can-i-create-multiple-hyperscale-databases-per-sql-database-server"></a>是否可為每個 SQL Database 伺服器建立多個超大規模資料庫

是。 如需每個 SQL Database 伺服器的超大規模資料庫數目限制的詳細資訊，請參閱[適用於 SQL Database 伺服器上之單一和集區資料庫的 SQL Database 資源限制](sql-database-resource-limits-database-server.md)。

### <a name="what-are-the-performance-characteristic-of-a-hyperscale-database"></a>超大規模資料庫有哪些效能特性

SQL Database 超大規模資料庫架構不僅支援大型資料庫，同時也可提供高效能和高輸送量。 在公開預覽期間不提供確切的效能設定檔和特性。

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>超大規模資料庫有何延展性

「SQL Database 超大規模資料庫」可根據您的工作負載需求提供快速延展性。

- **相應增加/減少**

  使用超大規模資料庫時，您可以在 CPU、記憶體等資源方面相應增加主要計算大小，然後再依常數時間相應減少。 由於儲存體是共用的，因此相應增加和相應減少並非資料作業的大小。  
- **相應縮小/相應放大**

  使用超大規模資料庫時，您也將能夠佈建一或多個額外的計算節點，用來支應您的讀取要求。 這表示，您可以將這些額外的計算節點作為唯讀節點，用以卸除主要計算節點的讀取工作負載。 除了作為唯讀節點，這些節點也可在主要節點進行容錯移轉時作為熱待命節點。

  佈建每個這些額外計算節點的作業也可在常數時間內完成，而且這是線上作業。 您可以將連接字串的 `ApplicationIntent` 引數設為 `read_only`，藉以連線至這些額外的唯讀計算節點。 任何標示為 `read-only` 的連線都會自動路由至其中一個額外的唯讀計算節點。

## <a name="deep-dive-questions"></a>深入問題

### <a name="can-i-mix-hyperscale-and-single-databases-a-my-sql-database-server"></a>是否可在 SQL Database 伺服器中混用超大規模資料庫與單一資料庫

 是，您可以這麼做。

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>使用「超大規模資料庫」時是否需要變更應用程式設計模型

否，您的應用程式設計模型應保持原狀。 您應如常使用連接字串，並使用其他一般模式與 Azure SQL 資料庫進行互動。

### <a name="what-transaction-isolation-levels-are-going-to-be-default-on-sql-database-hyperscale-database"></a>SQL Database 超大規模資料庫上會有哪些預設的交易隔離等級

主要節點上的交易隔離等級是 RCSI (讀取認可快照集隔離)。 在讀取規模次要節點上，隔離等級是快照集。

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-sql-database-hyperscale"></a>內部部署或 IaaS SQL Server 授權是否可用在 SQL Database 超大規模資料庫上

是，[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 適用於「超大規模資料庫」。 每個 SQL Server Standard 核心可對應至 1 個超大規模資料庫虛擬核心。 每個 SQL Server Enterprise 核心可對應至 4 個超大規模資料庫虛擬核心。 次要複本不需要 SQL 授權。 Azure Hybrid Benefit 價格會自動套用至讀取規模 (次要) 複本。

### <a name="what-kind-of-workloads-is-sql-database-hyperscale-designed-for"></a>SQL Database 超大規模資料庫是針對何種工作負載而設計的

「SQL Database 超大規模資料庫」支援所有 SQL Server 工作負載，但主要針對 OLTP 進行最佳化。 您也可以用它來處理混合式和分析 (資料超市) 工作負載。

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-sql-database-hyperscale"></a>如何在 Azure SQL 資料倉儲與 SQL Database 超大規模資料庫之間做選擇

如果您目前以 SQL Server 作為資料倉儲而執行互動式分析查詢，「SQL Database 超大規模資料庫」將是絕佳選項，因為您可用最低的成本裝載相對較小的資料倉儲 (例如，數 TB 到數十 TB)，並且可將資料倉儲工作負載直接移轉至「SQL Database 超大規模資料庫」，而不需要變更 T-SQL 程式碼。

如果您使用平行處理資料倉儲 (PDW)、Teradata 或其他巨量平行處理器 (MPP) 資料倉儲，以複雜的查詢大規模執行資料分析，則 SQL 資料倉儲將是最佳選擇。
  
## <a name="sql-database-hyperscale-compute-questions"></a>SQL Database 超大規模資料庫的計算問題

### <a name="can-i-pause-my-compute-at-any-time"></a>我是否可隨時暫停計算

沒有。

### <a name="can-i-provision-a-compute-with-extra-ram-for-my-memory-intensive-workload"></a>我是否可為記憶體密集工作負載佈建額外增加 RAM 的計算

沒有。 若要有更多的 RAM，您需要升級至更高的計算大小。 Gen4 硬體提供的 RAM 比 Gen5 硬體多。 如需詳細資訊，請參閱[超大規模資料庫儲存體和計算大小](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier-preview-for-provisioned-compute-tier)。

### <a name="can-i-provision-multiple-compute-nodes-of-different-sizes"></a>我是否可以佈建不同大小的多個計算節點

沒有。

### <a name="how-many-read-scale-replicas-are-supported"></a>可支援多少個讀取規模複本

在公開預覽期間，依預設建立的超大規模資料庫會有一個讀取規模複本 (共計兩個複本)。 如果您要新增或移除讀取規模複本，請使用 Azure 入口網站提出支援要求。

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-nodes"></a>是否需要佈建額外的計算節點才能取得高可用性

在超大規模資料庫中，高可用性會提供於儲存體層級上。 您只需要一個複本即可提供高可用性。 計算複本失效時，將會自動建立新複本，且不會遺失資料。

但如果只有一個複本，則在容錯移轉之後可能需要一些時間才能在新複本中建置本機快取。 在快取重建階段中，資料庫會直接從頁面伺服器提取資料，而導致 IOPS 和查詢效能降低。

針對需要高可用性的關鍵任務應用程式，您應佈建至少 2 個計算節點，包括主要計算節點在內 (預設值)。 如此，在執行容錯移轉時即有熱待命節點可供使用。

## <a name="data-size-and-storage-questions"></a>資料大小和儲存體問題

### <a name="what-is-the-max-db-size-supported-with-sql-database-hyperscale"></a>「SQL Database 超大規模資料庫」最多可支援多大的資料庫

100 TB

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>「超大規模資料庫」的交易記錄大小為何

「超大規模資料庫」的交易記錄可說是無限制的。 您無須擔心記錄輸送量偏高的系統上是否會有記錄空間不足的問題。 不過，如果工作負載持續湧現，記錄產生速率可能會受到節流控制。 尖峰和平均記錄產生速率尚未得知 (目前仍為預覽階段)。

### <a name="does-my-temp-db-scale-as-my-database-grows"></a>暫存資料庫是否會隨著資料庫成長而調整

您的 `tempdb` 資料庫位於本機 SSD 儲存體，會根據您佈建的計算大小設定。 您的 `tempdb` 已進行最佳化和配置，可提供最大效能優勢。 `tempdb` 大小是不可設定的，儲存體子系統會替您加以管理。

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-the-data-files"></a>資料庫大小會自動成長，還是必須由我管理資料檔案的大小

您的資料庫大小會在您插入/擷取更多資料時自動成長。

### <a name="what-is-the-smallest-database-size-that-sql-database-hyperscale-supports-or-starts-with"></a>SQL Database 超大規模資料庫支援或一開始的最小資料庫大小為何

5 GB

### <a name="in-what-increments-does-my-database-size-grow"></a>資料庫大小成長的遞增量為何

1 GB

### <a name="is-the-storage-in-sql-database-hyperscale-local-or-remote"></a>「SQL Database 超大規模資料庫」中的儲存體是本機還是遠端的

在「超大規模資料庫」中，資料檔案會儲存在 Azure 標準儲存體中。 資料會大量快取到計算節點近端電腦的本機 SSD 儲存體上。 此外，計算節點會有本機 SSD 和記憶體內部 (緩衝集區等等) 的快取，以降低從遠端節點提取資料的頻率。

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>我是否可管理或定義「超大規模資料庫」的檔案或檔案群組

否
  
### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>我是否可為資料庫佈建資料成長的強制上限

否

### <a name="how-are-data-files-laid-out-with-sql-database-hyperscale"></a>「SQL Database 超大規模資料庫」會如何配置資料檔案

資料檔案由頁面伺服器所控制。 當資料大小成長時，即會新增資料檔案和相關聯的頁面伺服器節點。

### <a name="is-database-shrink-supported"></a>是否支援資料庫縮減

否

### <a name="is-database-compression-supported"></a>是否支援資料庫壓縮

是

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>如果我有很大的資料表，資料表的資料是否會分散到多個資料檔案

是。 與指定的資料表相關聯的資料頁面可能會移至多個資料檔案中，但全都屬於相同的檔案群組。 SQL Server 會使用[按比例填滿策略](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy)將資料分散到多個資料檔案。

## <a name="data-migration-questions"></a>資料移轉問題

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>是否可將現有的 Azure SQL 資料庫移至超大規模資料庫服務層級

是。 您可以將現有的 Azure SQL 資料庫移至「超大規模資料庫」。 在公開預覽中，這是單向的移轉。 您無法將資料庫從超大規模資料庫移到另一個服務層級中。 建議您建立一份生產資料庫副本，並遷移至超大規模資料庫移轉以進行概念證明 (POC)。
  
### <a name="can-i-move-my-hyperscale-databases-to-other-editions"></a>是否可將超大規模資料庫移至其他版本

沒有。 在公開預覽階段，您無法將超大規模資料庫移至其他服務層。

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>移轉至超大規模資料庫服務層級之後是否會失去任何運作性或功能

是。 在超大規模在公開預覽期間不支援 Azure SQL Database 中的長期保留備份。 您以超大規模移轉資料庫之後，這項功能會停止運作。

### <a name="can-i-move-my--on-premises-sql-server-database-or-my-sql-server-virtual-machine-database-to-hyperscale"></a>我是否可將內部部署 SQL Server 資料庫或 SQL Server 虛擬機器資料庫移至「超大規模資料庫」

是。 您可以使用所有現有的移轉技術來移轉至「超大規模資料庫」，包括 BACPAC、異動複寫、邏輯資料載入等。 另請參閱 [Azure 資料庫移轉服務](../dms/dms-overview.md)。

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>從內部部署或虛擬機器環境移轉至「超大規模資料庫」期間的停機時間有多長？如何盡量縮短

此停機時間與您將資料庫移轉至 Azure SQL Database 中的單一資料庫時產生的停機時間相同。 移轉大小未超過 10 TB 的資料庫時，您可以使用[異動複寫](replication-to-sql-database.md#data-migration-scenario
)盡可能縮短停機時間。 針對非常大型的資料庫 (10+ TB)，則應考慮使用 ADF、Spark 或其他資料移動技術來移轉資料。

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-sql-database-hyperscale"></a>將某個數量的資料移至「SQL Database 超大規模資料庫」需要多少時間

尚未得知 (目前仍為預覽階段)

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-and-sql-data-warehouse"></a>我是否可從 Blob 儲存體讀取資料並執行快速載入 (例如 Polybase 和 SQL 資料倉儲)

您可以從 Azure 儲存體讀取資料，並將資料載入超大規模資料庫中 (就像使用一般的單一資料庫一樣)。 Azure SQL Database 目前不支援 PolyBase。 您可以使用 [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)，或透過 [SQL 的 Spark 連接器](sql-database-spark-connector.md)在 [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) 中執行 Spark 作業，藉以執行 Polybase。 SQL 的 Spark 連接器支援大量插入。

「超大規模資料庫」不支援簡單復原或大量記錄模式。 若要提供高可用性，必須使用完整復原模式。 不過，「超大規模資料庫」憑藉新的記錄架構，而可提供優於單一資料庫的資料擷取速率。

### <a name="does-sql-database-hyperscale-allow-provisioning-multiple-nodes-for-ingesting-large-amounts-of-data"></a>「SQL Database 超大規模資料庫」是否允許佈建多個節點以擷取大量資料

沒有。 「SQL Database 超大規模資料庫」屬於 SMP 架構，而不是非對稱多工處理或多重主機架構。 您只能建立多個複本以相應放大唯讀工作負載。

### <a name="what-is-the-oldest-sql-server-version-will-sql-database-hyperscale-support-migration-from"></a>可移轉至「SQL Database 超大規模資料庫」的 SQL Server 最舊版本為何

SQL Server 2005。 如需詳細資訊，請參閱[移轉至單一資料庫或集區資料庫](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database)。 若要了解相容性問題，請參閱[解決資料庫移轉相容性問題](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues)。

### <a name="does-sql-database-hyperscale-support-migration-from-other-data-sources-such-as-aurora-mysql-oracle-db2-and-other-database-platforms"></a>「SQL Database 超大規模資料庫」是否支援從其他資料來源進行移轉，例如 Aurora、MySQL、Oracle、DB2 和其他資料庫平台

是。 如果資料來源不是 SQL Server，則需進行邏輯移轉。 您可以使用 [Azure 資料庫移轉服務](../dms/dms-overview.md)進行邏輯移轉。

## <a name="business-continuity-and-disaster-recovery-questions"></a>商務持續性和災害復原問題

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>超大規模資料庫隨附的 SLA 為何

一般而言，在公開預覽期間並不會提供 SLA。 不過，「超大規模資料庫」會提供與目前的 SQL DB 供應項目相同層級的高可用性。 請參閱 [SLA](https://azure.microsoft.com/support/legal/sla/)。

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Azure SQL Database 服務是否會為我管理資料庫備份

是

### <a name="how-often-are-the-database-backups-taken"></a>資料庫備份多久執行一次

SQL Database 超大規模資料庫並沒有傳統的完整、差異和記錄備份。 它會有資料檔案的一般快照集，而產生的記錄只會依原狀在設定的保留期限內保存，或供您使用。

### <a name="does-sql-database-hyperscale-support-point-in-time-restore"></a>「SQL Database 超大規模資料庫」是否支援時間點還原

是

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-with-backuprestore-in-sql-database-hyperscale"></a>「SQL Database 超大規模資料庫」中的備份/還原具有怎樣的復原點目標 (RPO)/復原時間目標 (RTO)

RPO 為 0 分鐘。RTO 目標小於 10 分鐘，無論資料庫大小為何。 但在公開預覽期間，實際的還原時間可能會較長。

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>大型資料庫的備份是否會影響主要資料庫的計算效能

沒有。 備份由儲存子系統管理，並使用檔案快照集。 這並不會影響到主要資料庫上的使用者工作負載。

### <a name="can-i-perform-geo-restore-with-a-sql-database-hyperscale-database"></a>是否可使用 SQL Database 超大規模資料庫執行異地還原

否，在公開預覽期間不支援。

### <a name="can-i-setup-geo-replication-with-sql-database-hyperscale-database"></a>是否可使用 SQL Database 超大規模資料庫設定異地複寫

否，在公開預覽期間不支援。

### <a name="do-my-secondary-compute-nodes-get-geo-replicated-with-sql-database-hyperscale"></a>我的第二個計算節點是否可使用「SQL Database 超大規模資料庫」進行異地複寫

否，在公開預覽期間不支援。

### <a name="can-i-take-a-sql-database-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-sql-server-in-vm"></a>是否可建立 SQL Database 超大規模資料庫備份，而後將其還原至內部部署伺服器或 VM 中 SQL Server

沒有。 超大規模資料庫的儲存格式會與傳統 SQL Server 不同，且您無法控制備份或加以存取。 若要從 SQL Database 超大規模資料庫中取出您的資料，請使用匯出服務，或搭配使用指令碼和 BCP。

## <a name="cross-feature-questions"></a>跨功能問題

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>移轉至超大規模資料庫服務層級之後是否會失去任何運作性或功能

是。 在超大規模在公開預覽期間不支援 Azure SQL Database 中的長期保留備份。 您以超大規模移轉資料庫之後，這項功能會停止運作。

### <a name="will-polybase-work-with-sql-database-hyperscale"></a>Polybase 適用於「SQL Database 超大規模資料庫」

沒有。 Azure SQL Database 不支援 PolyBase。

### <a name="does-the-compute-have-support-for-r-and-python"></a>計算是否支援 R 和 Python

沒有。 Azure SQL Database 不支援 R 和 Python。

### <a name="are-the-compute-nodes-containerized"></a>計算節點是否會容器化

沒有。 您的資料庫位於計算 VM 上，而不是容器中。

## <a name="performance-questions"></a>效能問題

### <a name="how-much-throughput-can-i-push-on-the-largest-sql-database-hyperscale-compute"></a>在最大的 SQL Database 超大規模資料庫計算上最多可推送多少輸送量

尚未得知 (目前仍為預覽階段)

### <a name="how-many-iops-do-i-get-on-the-largest-sql-database-hyperscale-compute"></a>在最大的 SQL Database 超大規模資料庫計算上最多可達到多少 IOPS

尚未得知 (目前仍為預覽階段)

### <a name="does-my-throughput-get-affected-by-backups"></a>備份是否會影響到輸送量

沒有。 計算會分離於儲存層以外，以避免影響到計算。

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-nodes"></a>在佈建額外的計算節點時是否會影響到輸送量

由於儲存體是共用的，且主要與次要計算節點之間並不會執行直接的實體複寫，因此在技術上來說，主要節點上的輸送量將會因為新增讀取規模節點而受到影響。 不過，我們可以對持續湧現的工作負載進行節流控制，讓次要節點能夠套用記錄，並且讓頁面伺服器趕上進度，並避免次要節點上的讀取效能不佳。

## <a name="scalability-questions"></a>延展性問題

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-node"></a>相應增加和相應減少計算節點需要多少時間

數分鐘

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>我的資料庫在相應增加/減少作業進行時是否會離線

沒有。 相應增加和相應減少會在線上執行。

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>進行調整作業時是否可能發生連線中斷

如果具有目標大小的計算節點發生容錯移轉，相應增加或相應減少就會導致現有連線中斷。 新增讀取複本並不會導致連線中斷。

### <a name="is-the-scaling-up-and-down-of-compute-nodes-automatic-or-end-user-triggered-operation"></a>計算節點的相應增加和相應減少是自動作業還是使用者觸發的作業

使用者。 並非自動的。  

### <a name="does-my-tempb-also-grow-as-the-compute-is-scaled-up"></a>我的 `tempb` 是否會隨著計算的相應增加而成長

是。 暫存資料庫將隨著計算的成長而自動相應增加。  

### <a name="can-i-provision-multiple-primary-computes-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>我是否可以佈建多個主要計算，例如有多個主要計算前端可提高並行層級的多重主機系統

沒有。 只有主要計算節點會接受讀取/寫入要求。 次要計算節點僅接受唯讀要求。

## <a name="read-scale-questions"></a>讀取規模問題

### <a name="how-many-secondary-compute-nodes-can-i-provision"></a>我可以佈建多少個次要計算節點

在公開預覽期間，依預設會為超大規模資料庫建立兩個複本。 如果您想要調整複本數目，請使用 Azure 入口網站提出支援要求。

### <a name="how-do-i-connect-to-these-secondary-compute-nodes"></a>如何連線至這些次要計算節點

您可以將連接字串的 `ApplicationIntent` 引數設為 `read_only`，藉以連線至這些額外的唯讀計算節點。 任何標示為 `read-only` 的連線都會自動路由至其中一個額外的唯讀計算節點。  

### <a name="can-i-create-a-dedicated-endpoint-for-the-read-scale-replica"></a>是否可以建立讀取規模複本的專用端點

沒有。 在公開預覽期間，您只能藉由指定 `ApplicationIntent=ReadOnly` 來連線至讀取規模複本。

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>系統是否會對讀取工作負載進行智慧型負載平衡

沒有。 在預覽期間，唯讀工作負載會重新導向至隨機的讀取規模複本。

### <a name="can-i-scale-updown-the-secondary-compute-nodes-independently-of-the-primary-compute"></a>是否可在主要計算節點外獨立相應增加/減少次要計算節點

否，在公開預覽期間不支援。

### <a name="do-i-get-different-temp-db-sizing-for-my-primary-compute-and-my-additional-secondary-compute-nodes"></a>主要計算節點和其他次要計算節點是否可以有不同的暫存資料庫大小

沒有。 您的 `tempdb` 會根據計算大小佈建而設定，在公開預覽期間，次要計算節點的大小會與主要計算節點相同。

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-nodes"></a>是否可以在次要計算節點上新增索引和檢視

沒有。 超大規模資料庫會共用儲存體，這表示所有計算節點會看到相同的資料表、索引和檢視。 如果您希望次要節點上有適用於讀取的額外索引 – 您必須在主要節點上加以新增。

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-node"></a>主要和次要計算節點之間會有多少延遲

從在主要節點上認可交易時起算，兩者幾乎沒有延遲，或只有數毫秒的延遲，視記錄產生速率而定。

## <a name="next-steps"></a>後續步驟

如需超大規模資料庫服務層級的詳細資訊，請參閱[ (預覽)](sql-database-service-tier-hyperscale.md)。
