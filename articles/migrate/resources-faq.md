---
title: Azure Migrate - 常見問題集 (FAQ) | Microsoft Docs
description: 解說 Azure Migrate 的相關常見問題
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: snehaa
ms.openlocfilehash: 20a3b790310153ac5bda23fb72baf2ae61e1da52
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58003728"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate - 常見問題集 (FAQ)

本文包含有關 Azure Migrate 的常見問題集。 如果您在閱讀本文後仍有其他問題，請將問題張貼在 [Azure Migrate 論壇](https://aka.ms/AzureMigrateForum)。

## <a name="general"></a>一般

### <a name="does-azure-migrate-support-assessment-of-only-vmware-workloads"></a>Azure Migrate 是否僅支援 VMware 工作負載的評估？

是，Azure Migrate 目前僅支援 VMware 工作負載的評估。 支援的 HYPER-V 處於預覽狀態，請註冊[此處](https://aka.ms/migratefuture)預覽的存取。 將在未來啟用實體伺服器的支援。

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate 是否需要 vCenter Server 才能探索 VMware 環境？

是，Azure Migrate 需要 vCenter Server 才能探索 VMware 環境， 且不支援探索非由 vCenter Server 管理的 ESXi 主機。

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Azure Migrate 和 Azure Site Recovery 有何不同？

Azure Migrate 是一項評估服務，可協助您探索內部部署工作負載，並規劃移轉至 Azure 的作業。 搭配 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) 作為災難復原解決方案，可協助您將內部部署工作負載移轉至 Azure 中的 IaaS 虛擬機器。

### <a name="whats-the-difference-between-using-azure-migrate-for-assessments-and-the-map-toolkit"></a>使用 Azure Migrate 評量和 Map Toolkit 之間的差異為何？

[Azure Migrate](migrate-overview.md) 提供專為協助移轉整備與評估內部部署工作負載至 Azure 的移轉評量。 [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=7826) 有其他功能，例如較新版 Windows 用戶端和伺服器作業系統的移轉規劃及軟體使用情況追蹤。 若有這些情況，請繼續使用 MAP Toolkit。


### <a name="how-is-azure-migrate-different-from-azure-site-recovery-deployment-planner"></a>Azure Migrate 和 Azure Site Recovery 部署規劃工具有何不同？

Azure Migrate 是移轉規劃工具，而 Azure Site Recovery 部署規劃工具則是災難復原 (DR) 規劃工具。

**從 VMware 移轉至 Azure**：如果您要將內部部署工作負載移轉至 Azure，請使用 Azure Migrate 規劃移轉作業。 Azure Migrate 會評估內部部署工作負載，並提供指引、見解和機制，協助您移轉至 Azure。 將移轉計劃準備就緒後，您可以使用 Azure Site Recovery 和 Azure 資料庫移轉服務等服務，將機器移轉至 Azure。

**從 Hyper-V 移轉至 Azure**：正式運作版本的 Azure Migrate 目前支援移轉至 Azure 的 VMware 虛擬機器的評估。 支援對 HYPER-V 目前為預覽狀態，與生產環境支援。 如果您有興趣試用預覽版，請註冊[此處](https://aka.ms/migratefuture)。

**從 VMware/Hyper-V 移轉至 Azure 的災難復原**：如果您預計使用 Azure Site Recovery (Site Recovery) 在 Azure 上執行災難復原 (DR)，請使用 Site Recovery 部署規劃工具進行 DR 規劃。 Site Recovery 部署規劃工具可針對您的內部部署環境進行深層的 ASR 特定評估。 此外，還可針對如何成功執行複寫、虛擬機器容錯移轉等 DR 作業提供必要的 Site Recovery 建議。  

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Azure Migrate 支援哪些 Azure 地理區？

Azure Migrate 目前支援以歐洲、美國及 Azure Government 作為專案地理位置。 即使您只能在這些地理區建立移轉專案，您還是可以針對[多個目標位置](https://docs.microsoft.com/azure/migrate/how-to-modify-assessment#edit-assessment-properties)評估機器。 專案地理位置只會用來儲存探索到的資料。

**地理位置** | **中繼資料儲存位置**
--- | ---
Azure Government | 美國政府維吉尼亞州
亞洲 | 東南亞
歐洲 | 北歐或西歐
美國 | 美國東部或美國中西部

### <a name="how-does-the-on-premises-site-connect-to-azure-migrate"></a>內部部署站台如何連線至 Azure Migrate？

連線可以透過網際網路或使用 ExpressRoute 與公用對等互連。

### <a name="can-i-harden-the-vm-set-up-with-the-ova-template"></a>我可以使用 OVA 範本來強化 VM 設定嗎？

您可以將額外元件 (例如防毒元件) 新增到 OVA 範本中，只要 Azure Migrate 設備運作所需的通訊和防火牆規則保持不變即可。   

### <a name="to-harden-the-azure-migrate-appliance-what-are-the-recommended-antivirus-av-exclusions"></a>若要強化 Azure Migrate 設備，建議的防毒 (AV) 排除項目為何？

您需要將設備中的下列資料夾從防毒掃描中排除：

- 具有 Azure Migrate 服務二進位檔的資料夾。 排除所有子資料夾。
  %ProgramFiles%\ProfilerService  
- Azure Migrate Web 應用程式。 排除所有子資料夾。
  %SystemDrive%\inetpub\wwwroot
- 資料庫和記錄檔的本機快取。 Azure Migrate 服務需要此資料夾的 RW 存取。
  %SystemDrive%\Profiler

## <a name="discovery"></a>探索

### <a name="what-data-is-collected-by-azure-migrate"></a>Azure Migrate 會收集哪些資料？

Azure Migrate 支援兩種探索，分別是設備為基礎及代理程式為基礎的探索。
設備為基礎的探索會收集有關內部部署虛擬機器的中繼資料，設備收集的中繼資料完整清單如下：

**虛擬機器的組態資料**
- 虛擬機器顯示名稱 (在 vCenter 上)
- 虛擬機器清查路徑 (vCenter 中的主機/叢集/資料夾)
- IP 位址
- MAC 位址
- 作業系統
- 核心、磁碟、NIC 數目
- 記憶體大小、磁碟大小

**虛擬機器的效能資料**
- CPU 使用率
- 記憶體使用量
- 附加至虛擬機器的每個磁碟：
  - 磁碟讀取輸送量
  - 磁碟寫入輸送量
  - 每秒的磁碟讀取作業數
  - 每秒的磁碟寫入作業數
- 附加至虛擬機器的每個網路介面卡：
  - 網路輸入
  - 網路輸出

代理程式為基礎的探索之上的設備為基礎的探索選項，並協助客戶[相依性視覺化](how-to-create-group-machine-dependencies.md)的內部部署 Vm。 相依性代理程式會收集 FQDN、作業系統、IP 位址、MAC 位址、在虛擬機器內執行的程序，以及從虛擬機器傳出/傳入的 TCP 連線等詳細資料。 代理程式為基礎的探索是選擇性做法，如果您不想要視覺化虛擬機器的相依性，則可選擇不要安裝代理程式。

### <a name="would-there-be-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>是否會對分析的 ESXi 主機環境產生任何效能影響？

對於[一次性探索方法](https://docs.microsoft.com/azure/migrate/concepts-collector)，為了收集效能資料，vCenter Server 上的統計資料層級必須設為 3。 將它設定為這個層級會收集大量疑難排解資料，這些資料將儲存在 vCenter Server 資料庫中。 它可能因此導致 vCenter Server 出現一些效能問題。 對 ESXi 主機的影響微乎其微。

我們已經引入了效能資料的連續分析 (處於預覽狀態)。 透過連續分析，就不再需要變更 vCenter Server 統計資料層級來執行以效能為基礎的評估。 收集器設備現在將會分析內部部署機器，以測量虛擬機器的效能資料。 這對 ESXi 主機以及 vCenter Server 的效能影響幾乎為零。

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>資料收集後會儲存在哪裡、儲存多久？

收集器設備收集的資料會儲存在您建立移轉專案時所指定的 Azure 位置。 資料會安全地儲存在 Microsoft 訂用帳戶中，而使用者刪除 Azure Migrate 專案後，資料即會一併刪除。

針對相依性視覺化，如果您在 VM 上安裝代理程式，則相依性代理程式所收集的資料會儲存在於美國之使用者訂用帳戶中建立的 Log Analytics 工作區內。 刪除訂用帳戶中的 Log Analytics 工作區時，會刪除此資料。 [深入了解](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization)。

### <a name="what-is-the-volume-of-data-which-is-uploaded-by-azure-migrate-in-the-case-of-continuous-profiling"></a>在連續分析的情況下，上傳到 Azure Migrate 的資料量為何？

傳送到 Azure Migrate 的資料量會根據數個參數而不同。 如需代表性的數值，有十部機器 (每部有一個磁碟和一個 NIC) 的專案每天傳送約 50 MB。 這是近似值，且會隨 NIC 和磁碟的資料端點數目而變更 (如果機器、NIC 或磁碟的數目增加，傳送的資料會是非線性的)。

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>待用資料和傳輸中的資料是否會加密？

是，收集的資料在待用和傳輸時都會加密。 設備收集的中繼資料會安全地傳送至 Azure Migrate 服務 (使用 https 透過網際網路傳送)。 收集的中繼資料會儲存在 Microsoft 訂用帳戶的 [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) 和 [Azure Blob 儲存體](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)中，且待用時會加密。

相依性代理程式收集的資料傳輸時也會加密 (安全的 https 通道)，且會儲存在使用者訂用帳戶的 Log Analytics 工作區中； 資料待用時也會加密。

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>收集器如何與 vCenter Server 和 Azure Migrate 服務進行通訊？

收集器設備會以使用者在設備中提供的認證連線至 vCenter Server (連接埠 443)。 該收集器要求 vCenter Server 使用 VMware PowerCLI 收集 vCenter Server 管理的虛擬機器相關中繼資料， 並且會從 vCenter Server 收集虛擬機器 組態資料 (核心、記憶體、磁碟、NIC 等)，以及每個虛擬機器上個月的記錄。 收集的中繼資料則會傳送至 Azure Migrate 服務 (使用 https 透過網際網路傳送) 進行評估。 [深入了解](concepts-collector.md)

### <a name="can-i-connect-the-same-collector-appliance-to-multiple-vcenter-servers"></a>我是否可以將相同的收集器設備連線到多部 vCenter 伺服器？

可以，單一收集器設備可用來探索多部 vCenter 伺服器，但無法同時進行。 需要逐一執行探索。

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>Site Recovery 使用的 OVA 範本是否已與 Azure Migrate 使用的 OVA 整合？

目前未整合。 Site Recovery 中的 .OVA 範本用來設定 VMware VM/實體伺服器複寫的 Site Recovery 設定伺服器。 基於移轉評估目的，Azure Migrate 使用的 .OVA 用於探索 vCenter 伺服器管理的 VMware VM。

### <a name="i-changed-my-machine-size-can-i-rerun-the-assessment"></a>我已變更機器大小。 是否可以重新執行評量？

如果您已針對要評估的虛擬機器變更設定，觸發程序將使用收集器設備再次探索。 請在設備中使用 [重新開始收集] 選項來執行此動作。 收集完成後，請在入口網站中選取 [重新計算] 選項進行評估，以取得更新的評估結果。

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>如何在 Azure Migrate 中探索多租用戶環境？

如果您有一個在租用戶之間共用的環境，而且不想在一個租用戶的訂用帳戶中探索另一個租用戶的虛擬機器，您可以使用收集器設備中的 [範圍] 欄位來設定探索的範圍。 如果租用戶間要共用主機，請只針對屬於特定租用戶的虛擬機器，建立具有唯讀存取權的認證，然後在收集器設備中使用此認證，並將範圍指定為要進行探索的主機。 或者，您也可以在 vCenter Server 中建立資料夾 (例如 tenant1 對 folder1 及 tenant2 對 folder2)，在共用主機下，將 tenant1 的虛擬機器移到 folder1、並將 tenant2 的虛擬機器移到 tenant2，然後據以指定適當的資料夾來設定收集器中的探索範圍。

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>單一移轉專案中可以探索多少個虛擬機器？

您在單一移轉專案中可探索 1500 台虛擬機器。 如果您的內部部署環境中有多台機器，請[進一步了解](how-to-scale-assessment.md)如何在 Azure Migrate 中探索大型環境。


## <a name="assessment"></a>評量

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Azure Migrate 是否支援以 Enterprise 合約 (EA) 為基礎的成本估計？

Azure Migrate 目前不支援 [Enterprise 合約支援方案供應項目](https://azure.microsoft.com/offers/enterprise-agreement-support/)的成本估計。 因應措施是指定「預付型方案」做為供應項目，並在評量屬性的 [折扣] 欄位中手動指定折扣百分比 (適用於訂用帳戶)。

  ![折扣](./media/resources-faq/discount.png)

### <a name="what-is-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>如同內部部署調整大小和以效能為基礎的調整大小之間的差異為何？

當您將調整大小準則指定為如同內部部署調整大小時，Azure Migrate 不會考慮 VM 的效能資料，而會根據內部部署設定調整 VM 的大小。 如果調整大小準則是以效能為基礎，則會根據使用率資料調整大小。 例如，假設有一個內部部署 VM，其具有 4 個核心及 8GB 記憶體，以及 50% 的 CPU 使用率和 50% 的記憶體使用率。 如果調整大小準則為如同內部部署調整大小，則系統會建議具有 4 個核心和 8GB 記憶體的 Azure VM SKU；如果調整大小準則是以效能為基礎，則系統會建議 2 個核心和 4 GB 記憶體的 VM SKU，因為其在建議大小時會以使用率百分比為基礎。 同樣地，對於磁碟而言，磁碟調整大小取決於兩個評估屬性：調整大小準則和儲存體類型。 如果調整大小準則是以效能為基礎，且儲存體類型是自動的，則磁碟的 IOPS 和輸送量值會被用於辨別目標磁碟類型 (標準或進階)。 如果調整大小準則是以效能為基礎，且儲存體類型是進階的，則建議使用進階磁碟；Azure 中的進階磁碟 SKU 是根據內部部署磁碟的大小所選取的。 當調整大小準則與內部部署調整大小相同，且儲存體類型是標準或進階時，則會使用相同的邏輯來執行磁碟調整大小。

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>效能歷程記錄和百分位數使用率對大小建議有何影響？

這些屬性僅適用於以效能為基礎的調整大小。 Azure Migrate 會收集內部部署機器的效能記錄，並用其來建議在 Azure 中的 VM 大小和磁碟類型。 收集器設備會持續分析內部部署環境，每 20 秒便收集一次即時使用情況資料。 設備會彙總 20 秒範例，然後每 15 分鐘建立一個單一資料點。 為了建立單一資料點，設備會從所有 20 秒範例中選取尖峰值並將其傳送給 Azure。 當您在 Azure 中建立評估時，Azure Migrate 會根據效能持續時間和效能記錄的百分位數值，計算有效的使用率值，並將其用於調整大小。 例如，如果您已將效能持續時間設定為 1 天，並將百分位數值設定為 95 百分位數，Azure Migrate 會使用收集器針對過去一天所傳送的 15 分鐘取樣點，以遞增順序排序它們，並挑選第 95 個百分位數值作為有效使用率。 第 95 個百分位數值可確保您會忽略任何極端值，其在您選擇第 99 個百分位數的情況下很可能會出現。 如果您想選擇該期間的尖峰使用量，但不想錯過任何極端值，您應該選取第 99 個百分位數。

## <a name="dependency-visualization"></a>相依性視覺效果

> [!NOTE]
> 在 Azure Government 中無法使用相依性視覺效果功能。

### <a name="what-is-dependency-visualization"></a>什麼是相依性視覺效果？

相依性視覺效果可透過在執行評量前先交叉檢查機器相依性，讓您在評量要移轉的 VM 群組時更具信心。 相依性視覺效果可協助您確保沒有遺漏任何項目，避免在移轉至 Azure 時發生未預期的中斷情況。 Azure Migrate 會利用 Azure 監視器記錄檔，以啟用相依性視覺效果中的服務對應解決方案。

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>我是否需要付費才能使用相依性虛擬化功能？

沒有。 [在此](https://azure.microsoft.com/pricing/details/azure-migrate/)深入了解 Azure Migrate 定價。

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>我是否需要為相依性視覺效果安裝任何項目？

若要使用相依性視覺效果，您需要在您想要評估的每個內部部署機器上，下載並安裝代理程式。

- 必須在每個機器上安裝 [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。
- 必須在每個機器上安裝[相依性代理程式](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure)。
- 此外，如果您有無法連線至網際網路的機器，則必須在該機器上下載並安裝 Log Analytics 閘道。

您不需要在所要評量的機器上有這些代理程式，除非您使用相依性視覺效果。

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>是否可使用現有的工作區進行相依性視覺化？

是，Azure Migrate 現在允許您將現有的工作區連結至移轉專案，並將其用於相依性視覺效果。 [深入了解](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization#how-does-it-work)。

### <a name="can-i-export-the-dependency-visualization-report"></a>是否可以匯出相依性視覺效果報告？

否，相依性視覺效果無法匯出。 不過，由於 Azure Migrate 會將服務對應用於相依性視覺效果，因此您可以使用[服務對應 REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) 取得 JSON 格式的相依性。

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>如何自動安裝 Microsoft Monitoring Agent (MMA) 和相依性代理程式？

[此處](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples)是您可用於安裝相依性代理程式的指令碼。 [這裡](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent)提供有關如何使用命令列或自動化方法來安裝 MMA 的指示。 針對 MMA，您也可以利用 TechNet 上[這裡](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)提供的指令碼。

除了指令碼以外，您也可以利用 System Center Configuration Manager (SCCM)、[Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) 等部署工具來部署代理程式。

### <a name="what-are-the-operating-systems-supported-by-mma"></a>MMA 支援哪些作業系統？

MMA 所支援的 Windows 作業系統清單在[這裡](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)。
MMA 所支援的 Linux 作業系統清單在[這裡](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)。

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>相依性代理程式支援哪些作業系統？

相依性代理程式所支援的 Windows 作業系統清單在[這裡](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems)。
相依性代理程式所支援的 Linux 作業系統清單在[這裡](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems)。

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>我可以在 Azure Migrate 中將相依項視覺化超過一小時嗎？
否，Azure Migrate 最多可讓您持續視覺化相依性達一小時。 Azure Migrate 最多可讓您返回歷程記錄中過去一個月的特定日期，但您可將相依性視覺化的持續時間最多為 1 小時。 例如，您可以使用相依性對應中的持續時間功能來檢視昨天的相依性，但只能檢視它一小時。 不過，您可以使用 Azure 監視器記錄檔[查詢相依性資料](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)較長的持續期間內。

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>相依性視覺效果是否支援含有超過 10 個 VM 的群組？
針對所含 VM 在 10 個以內的群組，您可以[將群組的相依性視覺化](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)。 如果您的群組所含 VM 超過 10 個，則建議您先將群組分割成較小的群組，再將相依性視覺化。


## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure Migrate 概觀](migrate-overview.md)
- 了解如何[探索和評定](tutorial-assessment-vmware.md) VMware 環境
