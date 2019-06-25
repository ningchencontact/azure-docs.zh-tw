---
title: 將資料庫從 SQL Server 執行個體移轉到 Azure SQL Database 受控執行個體 |Microsoft Docs
description: 了解如何將資料庫從 SQL Server 執行個體移轉到 Azure SQL Database 受控執行個體。
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
manager: craigg
ms.date: 02/11/2019
ms.openlocfilehash: 9fe6ab797eaa325ad802702e95f5a0e5b8e4fef4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070417"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>將 SQL Server 遷移至 Azure SQL Database 受控執行個體

在此文章中，您將了解用來將 SQL Server 2005 或更新版本執行個體移轉到 [Azure SQL Database 受控執行個體](sql-database-managed-instance.md)的方法。 如需移轉至單一資料庫或彈性集區的相關資訊，請參閱[移轉至單一或集區資料庫](sql-database-cloud-migrate.md)。 如需從其他平台移轉的移轉資訊，請參閱 [Azure 資料庫移轉指南](https://datamigration.microsoft.com/) \(英文\)。

概括而言，資料庫移轉程序看起來像這樣：

![移轉程序](./media/sql-database-managed-instance-migration/migration-process.png)

- [評估受控執行個體的相容性](#assess-managed-instance-compatibility)
- [選擇應用程式連線選項](sql-database-managed-instance-connect-app.md)
- [部署到最佳大小的受控執行個體](#deploy-to-an-optimally-sized-managed-instance)
- [選取移轉方法並進行移轉](#select-migration-method-and-migrate)
- [監視應用程式](#monitor-applications)

> [!NOTE]
> 若要將個別資料庫遷移至單一資料庫或彈性集區，請參閱[將 SQL Server 資料庫遷移至 Azure SQL Database](sql-database-single-database-migrate.md)。

## <a name="assess-managed-instance-compatibility"></a>評估受控執行個體的相容性

首先，判斷受控執行個體是否與您應用程式的資料庫需求相容。 受控執行個體部署選項旨在為大部分內部部署或虛擬機器上使用 SQL Server 的現有應用程式，提供簡單的隨即轉移。 不過，有時候您可能需要尚不支援的功能，而且實作因應措施的成本非常高。

使用[資料移轉小幫手 (DMA)](https://docs.microsoft.com/sql/dma/dma-overview)，可偵測影響 Azure SQL Database 資料庫功能的潛在相容性問題。 DMA 尚不支援將受控執行個體做為移轉目的地，但建議您針對 Azure SQL Database 執行評估，並針對產品文件，仔細檢閱提報的功能同位和相容性問題清單。 請參閱 [Azure SQL Database 功能](sql-database-features.md)，以檢查是否有一些回報的執行問題不是受控執行個體中造成作業無法繼續執行個問題，因為造成無法無移轉到 Azure SQL Database 的大部分問題在受控執行個體中都已移除。 例如跨資料庫查詢、相同執行個體內的跨資料庫交易、其他 SQL 來源的連結伺服器、CLR、全域暫存資料表、執行個體層級檢視、Service Broker 等功能皆可在受控執行個體中使用。

若受控制執行個體部署選項並未移除一些回報的執行問題，您可能需要考慮替代選項，例如 [Azure 虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)。 以下是一些範例：

- 如果您需要直接存取作業系統或檔案系統，例如在具有 SQL Server 的相同虛擬機器上安裝第三方或自訂代理程式。
- 如果您的執行個體與尚不支援的功能有緊密相依性，例如 FileStream / FileTable、PolyBase 及跨執行個體交易等功能。
- 如果一定要將它保持在特定版本的 SQL Server (2012年執行個體)。
- 如果您的計算需求遠低於受控執行個體提供的功能 (例如，只需要一個虛擬核心)，而且資料庫彙總不是可接受的選項。

如果您已經解決所有識別移轉封鎖程式，並繼續移轉至受控執行個體，請注意，某些變更可能會影響工作負載的效能：
- 強制性的完整復原模式並定期自動備份排程可能會影響您的工作負載或維護/ETL 動作的效能，如果您定期使用簡單/大量記錄模式或停止隨選的備份。
- 不同的伺服器或資料庫層級設定，例如追蹤旗標或相容性層級
- 您使用例如透明資料庫加密 (TDE) 」 或 「 自動容錯移轉群組的新功能可能會影響 CPU 和 IO 使用量。

管理的執行個體保證 99.99%可用性，即使在重要的案例中，因此無法停用這些功能所造成的額外負荷。 如需詳細資訊，請參閱 <<c0> [ 可能會導致不同的效能，在 SQL Server 和受控執行個體的根本原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)。

### <a name="create-performance-baseline"></a>建立效能基準

如果您需要比較您的受控執行個體與原始工作負載的 SQL Server 上執行的工作負載的效能，您必須建立要用於比較效能基準線。 您必須在您的 SQL Server 執行個體量值的參數包括： 
- [監視 SQL Server 執行個體上的 CPU 使用量](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131)並記錄平均值，並達到 CPU 使用率的尖峰。
- [監視您的 SQL Server 執行個體上的記憶體使用量](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage)，並判斷不同的元件，例如緩衝集區所使用的記憶體數量計劃快取中，資料行存放區集區[記憶體內部 OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)，依此類推。此外，您應該會發現 Page Life Expectancy memory 效能計數器的平均值和尖峰的值。
- 監視來源 SQL Server 執行個體使用的磁碟 IO 使用量[sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)檢視或[效能計數器](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage)。
- 藉由檢查動態管理檢視或查詢存放區，如果您從 SQL Server 2016 + 版本移轉，監視工作負載和查詢效能或您的 SQL Server 執行個體。 找出平均持續時間和 CPU 使用量，您要與受控執行個體執行的查詢比較它們的工作負載中最重要的查詢。

> [!Note]
> 如果您發現任何問題，SQL Server 上您工作負載，例如高 CPU 使用量、 固定的記憶體不足的壓力、 tempdb 或幾的問題，您應該嘗試解決您的來源 SQL Server 執行個體上進行的基準和移轉前。 移轉知道任何新的系統 migh 問題會造成非預期的結果，並使其失效的任何效能比較。

此活動的結果為您應具有詳實記載和 CPU、 記憶體和 IO 使用量，在您的來源系統上的尖峰值，以及平均值和最大持續時間和平均 CPU 使用量，以及主控項的最重要的查詢工作負載中。 您應該稍後使用這些值，來比較您的受控執行個體的工作負載的來源 SQL Server 上的工作負載的基準效能的效能。

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>部署到最佳大小的受控執行個體

受控執行個體專為打算移至雲端的內部工作負載量身訂做。 它引進[新的購買模型](sql-database-service-tiers-vcore.md)，提供更大的彈性來選取適合您工作負載的正確資源層級。 在內部部署的環境中，您可能習慣使用實體核心數目與 IO 頻寬來調整這些工作負載大小。 受控執行個體的購買模型是以虛擬核心 (vCore) 為基礎，再個別加上額外儲存體與可用 IO。 相對於目前使用的內部部署方案，VCore 模型可讓您較簡單地了解雲端中的計算需求。 這個新模型可讓您在雲端中具有正確大小的目的地環境。 一些一般指導方針，以幫助您選擇的正確服務層和特性如下所示：
- [監視 SQL Server 執行個體上的 CPU 使用量](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131)和核取多少計算您目前使用 （使用動態管理檢視、 SQL Server Management Studio 或其他監視工具） 的能力。 您可以佈建受控執行個體符合您使用 SQL Server，注意，CPU 特性可能需要進行調整以符合需要的核心數目[受控執行個體安裝所在的 VM 特性](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#hardware-generation-characteristics)。
- 請檢查您的 SQL Server 執行個體上的可用記憶體數量，然後選擇[服務層有相符的記憶體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#hardware-generation-characteristics)。 它會有用來測量來判斷 SQL Server 執行個體上的頁面存留時間期望值[您需要額外的記憶體](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444)。
- 量值一般用途和業務關鍵服務層之間進行選擇的檔案子系統的 IO 的延遲。

您可以選擇計算和儲存體資源，在部署時間以及之後進行變更，且不會造成您的應用程式使用的停機時間[Azure 入口網站](sql-database-scale-resources.md):

![受控執行個體大小](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

若要了解如何建立 VNet 基礎結構和受控執行個體，請參閱[建立受控執行個體](sql-database-managed-instance-get-started.md)。

> [!IMPORTANT]
> 請務必讓您目的地 VNet 和子網路永遠符合[受控執行個體的 VNet 需求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。 任何相容性問題都可能會讓您無法建立新的執行個體，或使用已經建立的執行個體。 深入了解[建立新的 ](sql-database-managed-instance-create-vnet-subnet.md) 並[ 設定現有的 ](sql-database-managed-instance-configure-vnet-subnet.md) 網路。

## <a name="select-migration-method-and-migrate"></a>選取移轉方法並進行遷移

受控執行個體部署選項鎖定的是需要將大量資料庫從內部部署或 IaaS 資料庫實作移轉的使用者案例。 如果您需要隨即轉移定期使用執行個體層級和/或跨資料庫功能的應用程式後端，那麼這是最佳選擇。 如果這是您的情況，您可以將整個執行個體移動至 Azure 中對應的環境，而不需要重新建構您的應用程式。

若要移動 SQL 執行個體，您需要謹慎規劃下列作業：

- 移轉必須共置的所有資料庫 (在相同執行個體上執行)
- 移轉您應用程式依賴的執行個體層級物件，包括登入、認證、SQL Agent 作業和運算子，以及伺服器層級觸發程序。

受控執行個體是受控的服務，可讓您將一些固定的 DBA 活動委派至平台，因為這些活動已內建。 因此，某些執行個體層級的資料就不需要遷移，例如，定期備份的維護作業或 Alwayson 組態，因為已內建[高可用性](sql-database-high-availability.md)。

受控執行個體支援下列資料庫移轉選項 (這些是目前唯一支援的移轉方法)：

- Azure 資料庫移轉服務 - 幾乎零停機時間的移轉。
- 原生`RESTORE DATABASE FROM URL` - 從 SQL Server 使用原生備份且需要一些停機時間。

### <a name="azure-database-migration-service"></a>Azure 資料庫移轉服務

[Azure 資料庫移轉服務 (DMS)](../dms/dms-overview.md) 是一個完全受控的服務，能夠從多個資料庫來源無縫移轉到 Azure 資料平台，將停機時間降到最低。 此服務可簡化將現有第三方和 SQL Server 資料庫移動至 Azure 時所需的工作。 公開預覽中的部署選項包括 Azure SQL Database 中的資料庫，以及 Azure 虛擬機器中的 SQL Server 資料庫。 DMS 是移轉企業工作負載的建議方法。

如果您在內部部署 SQL Server 上使用 SQL Server Integration Services (SSIS)，DMS 尚未支援移轉 SSIS 目錄 (SSISDB)，該目錄儲存 SSIS 套件，但是您可以在 Azure Data Factory (ADF) 中佈建 Azure-SSIS Integration Runtime (IR)，這樣會在受控執行個體中建立新的 SSISDB，然後您可以將套件重新部署至其中，請參閱[在 ADF 中建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。

若要深入了解 DMS 的此案例和組態步驟，請參閱[使用 DMS 將內部部署資料庫遷移至受控執行個體](../dms/tutorial-sql-server-to-managed-instance.md)。  

### <a name="native-restore-from-url"></a>從 URL 原生還原

從 SQL Server 內部部署環境或[虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 產生的原生備份 (.bak 檔案) (可從 [Azure 儲存體](https://azure.microsoft.com/services/storage/)取得) 進行還原，是受控執行個體部署選項的重要功能之一，讓您可以快速且輕鬆地進行離線資料庫移轉。

下圖會提供程序的高階概觀：

![移轉流程](./media/sql-database-managed-instance-migration/migration-flow.png)

下表詳述根據您執行的來源 SQL Server 版本，可以使用的方法：

|步驟|SQL 引擎和版本|備份 / 還原方法|
|---|---|---|
|將備份放至 Azure 儲存體|SQL 2012 SP1 CU2 之前的版本|直接將 .bak 檔案上傳到 Azure 儲存體|
||2012 SP1 CU2 - 2016|使用已被取代的 [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) 語法直接備份|
||2016 和更新版本|使用 [WITH SAS CREDENTIAL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) 直接備份|
|從 Azure 儲存體還原至受控執行個體|[使用 SAS 認證從 URL 還原](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - 使用原生還原選項將受到[透明資料加密](transparent-data-encryption-azure-sql.md)保護的資料庫遷移到受控執行個體時，來自內部部署或 IaaS SQL Server 的對應憑證必須在資料庫還原之前進行遷移。 如需詳細步驟，請參閱[將 TDE 憑證遷移至受控執行個體](sql-database-managed-instance-migrate-tde-certificate.md)
> - 不支援系統資料庫還原。 若要移轉執行個體層級物件 (儲存在 master 或 msdb 資料庫中)，我們建議透過指令碼來找出這些物件，並在目的地執行個體上執行 T-SQL 指令碼。

如需說明如何使用 SAS 認證將資料庫備份還原至受控執行個體的快速入門，請參閱[從備份還原至受控執行個體](sql-database-managed-instance-get-started-restore.md)。

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>監視應用程式

當您完成移轉至受控執行個體時，您應該追蹤應用程式行為和工作負載的效能。 此程序包含下列活動：
- [比較受控執行個體上執行的工作負載的效能](#compare-performance-with-the-baseline)具有[您在來源 SQL Server 上建立效能基準線](#create-performance-baseline)。
- 持續[監視您的工作負載的效能](#monitor-performance)來識別潛在的問題和改進。

### <a name="compare-performance-with-the-baseline"></a>比較效能基準線

您需要採取立即成功移轉後的第一個活動是比較的基準工作負載效能的工作負載的效能。 此活動的目標是要確認您受控執行個體上的工作負載效能符合您的需求。 

大部分的情況下將資料庫移轉至受控執行個體保留資料庫設定和其原始的相容性層級。 盡可能以降低的一些相較於您的來源 SQL Server 的效能降低的風險，就會保留原始的設定。 如果使用者資料庫在移轉之前的相容性層級為 100 或以上，則移轉後相容性層級維持不變。 如果使用者資料庫在移轉之前的相容性層級為 90，那在升級後的資料庫中，相容性層級會設定為 100，這是受控執行個體中能支援的最低相容性層級。 系統資料庫的相容性層級是 140。 因為移轉至受控執行個體實際上會移轉至 SQL Server Database Engine 的最新版本中，您應該注意，您需要重新測試您的工作負載，以避免一些令人驚訝的效能問題的效能。

做為必要條件，請確定您已完成下列活動：
- 對齊的受控執行個體的設定，以從來源 SQL Server 執行個體設定，請調查各種執行個體、 資料庫、 temdb 設定和組態。 請確定您已變更相容性層級或加密等的設定，然後再執行第一次的效能比較，或接受您所啟用的新功能的一些可能會影響某些查詢的風險。 若要減少移轉的風險，請只在進行效能監視後變更資料庫相容性層級。
- 實作[儲存體最佳作法指導方針一般用途](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525)例如預先配置大小的檔案，以便取得更佳的效能。
- 深入了解[主要環境差異可能會導致受控執行個體和 SQL Server 的效能差異]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)並找出可能會影響效能的風險。
- 請確定您保留已啟用的查詢存放區，並在您受控執行個體上的自動調整。 這些功能可讓您測量工作負載效能，並自動修正潛在的效能問題。 了解如何使用查詢存放區做為最佳的工具，可用來取得有關資料庫相容性層級變更之前和之後的工作負載效能的資訊中所述[較新的 SQL Server 版本，在升級期間保持效能穩定性](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
一旦您已備妥環境類似盡量使用您的內部部署環境，您可以開始執行您的工作負載並測量效能。 測量程序應該包含相同的參數，您測量[當您建立基準效能，您的工作負載量值的來源 SQL Server 上](#create-performance-baseline)。
如此一來，您應該比較的基準線的效能參數，並識別重要的差異。

> [!NOTE]
> 在許多情況下，您就無法取得受控執行個體和 SQL Server 上完全相符的效能。 受控執行個體是 SQL Server 資料庫引擎，但基礎結構和受控執行個體上的高可用性組態可能會造成一些差異。 您可能預期某些查詢要快得多，而其他可能會變慢。 比較的目標是要驗證的受控執行個體中的工作負載效能是否符合 SQL Server 上的效能 （在平均值中），並找出是否有任何重大效能的查詢不符合您的原始效能。

可能的效能比較結果：
- 受控執行個體上的工作負載效能對齊或更高的 SQL Server 上的工作負載效能。 在此情況下您已經成功確認移轉能順利。
- 大部分的效能參數和中工作負載會正常執行，但有些例外狀況與效能降低的查詢。 在此情況下，您必須找出差異和它們的重要性。 如果有一些重要的查詢，以降低效能，您應該調查為基礎的 SQL 計劃變更或查詢只達到某些資源限制。 風險降低在此情況下可以套用一些提示上的重要查詢 （例如已變更的相容性層級，舊版基數估計工具） 是直接或使用計畫指南 rebuild 或 create statistics 和可能會影響計劃的索引。 
- 大多數的查詢會變慢的相較於您的來源 SQL Server 受控執行個體。 在此情況下嘗試識別根本原因的差異，例如[達到某些資源限制]( sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)喜歡 IO 限制、 記憶體限制、 執行個體記錄的速率限制等等。如果不有任何可能會導致不同的資源限制，請嘗試變更資料庫相容性層級，或變更資料庫設定喜歡舊版基數估計，然後重新啟動測試。 檢閱受管理的執行個體或查詢存放區檢視所提供的建議，以找出迴歸效能的查詢。

> [!IMPORTANT]
> 受控執行個體已預設啟用的內建的自動計畫更正功能。 這項功能可確保運作正常中貼上作業的查詢就不會在未來的降低。 請確定已啟用這項功能，而且您有夠長的工作負載執行與舊的設定之前您若要讓受控執行個體若要了解的基準效能和計劃變更新的設定。

進行變更的參數，或升級服務層，直到您取得符合您需求的工作負載效能，同時回歸到最適合的設定。

### <a name="monitor-performance"></a>監視效能

一旦您是在完全受控的平台上，而且您已驗證的工作負載效能會比 SQL Server 工作負載效能，需要 SQL Database 服務的一部分自動提供的優點。 

即使您不在受管理的執行個體中進行一些變更，在移轉期間，可能會有高，您會將一些新功能時操作您的執行個體，以利用最新的資料庫引擎增強功能開啟。 某些變更才會啟用一次[已變更資料庫相容性層級](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)。


比方說，您不需要在受控執行個體上建立備份 (服務會自動為您執行備份)。 您無法再擔心如何排程、使用及管理備份。 受控執行個體使用[時間點復原 (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore)，讓您能夠還原到此保留期限內的任何時間點。 此外，您不需要擔心如何設定為高可用性，因為[高可用性](sql-database-high-availability.md)已內建。

若要加強安全性，請考慮使用[Azure Active Directory 驗證](sql-database-security-overview.md)，[稽核](sql-database-managed-instance-auditing.md)，[威脅偵測](sql-database-advanced-data-security.md)，[資料列層級安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)，並[動態資料遮罩](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking))。

除了進階的管理和安全性功能，受控執行個體提供進階的工具，可協助您將一組[監視和調整您的工作負載](sql-database-monitor-tune-overview.md)。 [Azure SQL 分析](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql)可讓您監視大量的受管理的執行個體，並集中管理大量的執行個體與資料庫的監視。 [自動調整](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)受控執行個體中持續監視您的 SQL 計劃執行統計資料的效能，並自動修正所識別之的效能問題。

## <a name="next-steps"></a>後續步驟

- 如需受控執行個體的詳細資訊，請參閱[受控執行個體是什麼？](sql-database-managed-instance.md)。
- 如需包含從備份進行還原的教學課程，請參閱[建立受控執行個體](sql-database-managed-instance-get-started.md)。
- 如需示範使用 DMS 進行移轉的教學課程，請參閱[使用 DMS 將內部部署資料庫遷移到受控執行個體](../dms/tutorial-sql-server-to-managed-instance.md)。  
