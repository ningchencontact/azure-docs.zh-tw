---
title: Azure Migrate - 常見問題集 (FAQ) | Microsoft Docs
description: 解說 Azure Migrate 的相關常見問題
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: snehaa
ms.openlocfilehash: 08a0312f12b3daab8b7f5e88da118b5bcbeb2f4c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807333"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate - 常見問題集 (FAQ)

本文包含有關 Azure Migrate 的常見問題集。 如果您在閱讀本文後仍有其他問題，請將問題張貼在 [Azure Migrate 論壇](https://aka.ms/AzureMigrateForum)。

## <a name="general"></a>一般

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Azure Migrate 支援哪些 Azure 地理區？
Azure Migrate 目前支援一些可以在其中建立 Azure Migrate 專案的地理位置。 即使您只可以在這些地理位置中建立專案，您仍可評定您的機器，其他目標位置。 專案地理位置只會用來儲存探索到的資料。

**地理位置** | **中繼資料儲存體位置**Azure Government |美國維吉尼亞州政府亞洲 |東南亞或東亞歐洲 |南歐或西歐 United Kingdom |英國南部或英國西部 United States |美國中部 」 或 「 美國西部 2

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Azure Migrate 和 Azure Site Recovery 有何不同？

Azure Migrate 提供的工具，幫助您探索、 評估及移轉至 Azure 的機器和工作負載。 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure)是災害復原解決方案。 這兩項服務共用某些元件。

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Azure Migrate 應用裝置 （VMware/實體伺服器）

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Azure Migrate 應用裝置如何連接到 Azure？

連線可透過網際網路，或您可以使用 ExpressRoute 與公用對等互連。

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>Azure Migrate Server 評定及移轉時需要哪些網路連線需求

針對 Url 和 Azure Migrate 所需的連接埠與 Azure 通訊，請檢閱[VMWare](migrate-support-matrix-vmware.md)並[HYPER-V](migrate-support-matrix-hyper-v.md)支援矩陣。

### <a name="can-i-harden-the-appliance-vmware-vm-i-set-up-with-the-ova-template"></a>我可以強化設備使用.OVA 範本設定的 VMware VM？

其他元件 （例如防毒程式） 可以加入 OVA 範本中，為 Azure Migrate 設備 re 保留所需的通訊和防火牆規則。   

### <a name="to-harden-the-azure-migrate-appliance-what-are-the-recommended-antivirus-av-exclusions"></a>若要強化 Azure Migrate 設備，建議的防毒 (AV) 排除項目為何？

您需要從應用裝置上的掃描中排除下列資料夾：

- 包含 Azure Migrate 服務二進位檔的資料夾。 排除所有子資料夾。
- %ProgramFiles%\ProfilerService  
- Azure Migrate Web 應用程式。 排除所有子資料夾。
- %SystemDrive%\inetpub\wwwroot
- 本機快取中的資料庫和記錄檔。 Azure Migrate 服務需要讀取/寫入存取此資料夾。
  - %SystemDrive%\Profiler

### <a name="what-data-is-collected-by-azure-migrate"></a>Azure Migrate 會收集哪些資料？

Azure Migrate 設備會收集中繼資料的內部部署 Vm，包括：

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

Tn 另外，如果您部署的相依性對應，相依性對應代理程式會收集資訊，包括機器 FQDN、 作業系統、 IP 位址、 MAC 位址的 VM，並傳入/傳出 VM 的 TCP 連線內執行的處理序。 此探索是選擇性的才使用啟用探索的相依性對應。

### <a name="is-there-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>已分析的 ESXi 主機環境是否有任何效能影響？

使用連續程式碼剖析的效能資料，Azure Migrate 應用裝置設定檔內部部署機器來測量 VM 的效能資料。 ESXi 主機，以及在 vCenter Server 上，這就會有幾乎為零的效能影響。

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>資料收集後會儲存在哪裡、儲存多久？

Azure Migrate 應用裝置所收集的資料會儲存在您建立移轉專案時指定的 Azure 位置。 資料會安全地儲存在 Microsoft 訂用帳戶，並刪除 Azure Migrate 專案時，會刪除。

相依性視覺效果中，如果您的 Vm 上，安裝代理程式的相依性代理程式所收集的資料儲存在美國，在 Azure 訂用帳戶中建立 Log Analytics 工作區中。 刪除訂用帳戶中的 Log Analytics 工作區時，會刪除此資料。 [深入了解](concepts-dependency-visualization.md)。

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-during-continuous-profiling"></a>什麼是持續的程式碼剖析期間，Azure Migrate 所上傳的資料磁碟區？

大量資料傳送至 Azure Migrate 會根據數個參數而有所不同。 若要讓表示的數字，10 部的機器 （各有一個磁碟，另一個 NIC），Azure Migrate 專案會傳送，每天約 50 MB。 這是大約值，變更基礎資料點數目的 Nic 和磁碟 （傳送的資料是非線性如果機器、 Nic 或磁碟數目會增加）。

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>是資料加密待用和傳輸中？

是用於兩者。 Azure Migrate 服務來透過網際網路，透過 https 安全地傳送中繼資料。 中繼資料會儲存在[Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)，然後在[Azure blob 儲存體](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)中 Microsoft 訂用帳戶，而且待用加密。

相依性代理程式所收集的資料也會在傳輸中加密 (安全 HTTPS)，並會儲存在使用者訂用帳戶的 Log Analytics 工作區。 它也是待用加密。

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Azure Migrate 應用裝置如何與 vCenter Server 和 Azure Migrate 服務通訊？

應用裝置會連線到 vCenter 伺服器 （連接埠 443） 使用您設定設備時所提供的認證。 它會查詢 vCenter Server 來收集關於受 vCenter Server Vm 的中繼資料使用 VMware PowerCLI。 它的每個 VM 在過去一個月的效能歷程記錄以及收集關於 Vm （核心、 記憶體、 磁碟、 NIC 等），這兩個組態資料。 然後收集的中繼資料會傳送至 Azure Migrate Server 評估 （透過 HTTPS 透過網際網路） 以進行評量。 

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>我是否可以連線相同的應用裝置到多部 vCenter 伺服器？

是，單一 Azure Migrate 的設備可用來探索多部 vCenter 伺服器，但不是同時。 您必須逐一執行探索。

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>Site Recovery 使用的 OVA 範本是否已與 Azure Migrate 使用的 OVA 整合？

目前未整合。 Site Recovery 中的 .OVA 範本用來設定 VMware VM/實體伺服器複寫的 Site Recovery 設定伺服器。 。使用 Azure Migrate OVA 用來探索由 vCenter 伺服器管理，基於的評定及移轉的 VMware Vm。

### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>我已變更機器大小。 可以重新執行評量？
Azure Migrate 設備持續收集內部部署環境的相關資訊。 不過，評估是在內部部署 Vm 的時間點快照集。 如果您變更您想要評估的 VM 上的設定，請使用 'Recalculate' 選項以更新評估最新的變更。

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>如何在 Azure Migrate 中探索多租用戶環境？

適用於 VMware，如果您有會在租用戶之間共用的環境，您不想要探索的 Vm，另一個租用戶的訂用帳戶中的一個租用戶建立 vCenter Server 認證只能存取您想要探索這些 Vm。 當開始探索 Azure Migrate 設備中，然後使用的認證。

適用於 HYPER-V，此探索會使用 HYPER-V 主機的認證，如果 Vm 共用相同的 HYPER-V 主機，目前沒有任何方法可以分開的探索。  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>可以使用單一移轉設備探索多少 Vm？

您可以探索多達 10,000 的 VMware Vm，以及多達 5,000 的 HYPER-V Vm，使用單一移轉設備。  如果您有更多機器，在您的內部部署環境中，了解如何調整[HYPER-V](scale-hyper-v-assessment.md)並[VMware](scale-vmware-assessment.md)評量。


## <a name="azure-migrate-server-assessment"></a>Azure Migrate:Server 評定

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure 不會移轉：伺服器評估支援實體伺服器進行的評估嗎？

否，Azure Migrate 目前不支援評估的實體伺服器。 

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate 是否需要 vCenter Server 才能探索 VMware 環境？

是，Azure Migrate 需要 vCenter 伺服器來探索的 VMware 環境。 它不支援的未受管理的 vCenter 伺服器的 ESXi 主機的探索。

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>使用 Azure Migrate 之間的差異為何：Server 評定及 Map Toolkit？

Azure Migrate:伺服器評估將提供來協助移轉整備與評估版移轉至 Azure 的工作負載的移轉評定。 [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826)具有其他功能，例如較新版本的 Windows 用戶端和伺服器作業系統和軟體使用情況追蹤所規劃移轉。 若有這些情況，請繼續使用 MAP Toolkit。

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>如何為 Azure Migrate:Server 評定不同於 Azure Site Recovery Deployment Planner？

Azure Migrate:Server 評定是移轉規劃工具。 Azure Site Recovery Deployment Planner 是災害復原規劃工具。

- **從 VMware/HYPER-V 到 Azure 的移轉**:如果您想要將您的內部部署伺服器移轉至 Azure，請使用 Azure Migrate:規劃移轉的伺服器評估工具。 此工具會評估內部部署工作負載，並提供指引、 insights 和機制來協助您移轉至 Azure。 當您準備移轉計劃時，您可以使用 Azure Migrate 之類的工具：伺服器移轉，將機器移轉至 Azure。
- **從 VMware/Hyper-V 移轉至 Azure 的災難復原**：使用 Site Recovery 在 azure 的災害復原，使用 Site Recovery Deployment Planner 中的嚴重損壞修復規劃的內容。 Site Recovery 部署規劃工具會執行您的內部部署環境的深度，站台復原特定評估。 它提供成功的災害作業，例如複寫和容錯移轉 Vm 的 Site recovery 所需的建議。 

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Azure Migrate 會支援 Enterprise 合約 EA 為基礎的成本估計？

Azure Migrate 目前不支援的成本估計[Enterprise 合約的供應項目](https://azure.microsoft.com/offers/enterprise-agreement-support/)。 因應措施是指定隨用隨付優惠，並以手動方式指定 '折扣' 欄位的評量屬性中的 折扣百分比 （適用於訂用帳戶）。

  ![折扣](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>做為內部部署調整大小和效能為基礎的調整大小的差異為何？

- 在內部部署調整大小，Azure Migrate 不會考慮 VM 的效能資料。 它會根據內部部署組態的 Vm 調整大小。 中以效能為基礎的調整大小，調整大小根據使用量資料。
- 比方說，當內部部署 VM 有 4 個核心，8 GB 記憶體的 50 %cpu 使用率和 50%的記憶體使用率，作為在內部部署調整大小建議 Azure VM SKU 具有 4 個核心和 8 GB 記憶體。 以效能為基礎的調整大小，不過，建議的 VM SKU，2 個核心和 4 GB，因為被視為使用率百分比。
- 同樣地，磁碟調整大小會取決於兩個評量屬性-調整大小準則和儲存體類型。
= 如果調整大小準則是以效能為基礎，而且儲存體類型為自動，識別目標磁碟類型 （標準或進階） 時，會被視為磁碟的 IOPS 和輸送量值。
- 如果調整大小準則是以效能為基礎的儲存體類型是 premium，建議使用進階磁碟。 選取 SKU 時的進階磁碟為基礎的內部部署磁碟大小。 相同的邏輯用來執行時調整大小準則是如在內部部署調整大小，而儲存體類型是標準或進階磁碟大小。

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>效能歷程記錄和百分位數使用率對大小建議有何影響？

這些屬性僅適用於以效能為基礎的調整大小。

- Azure Migrate 會收集內部部署機器的效能歷程記錄，並使用它來建議您在 Azure 中的 VM 大小和磁碟類型。
- 持續應用裝置的設定檔來收集即時使用量資料每隔 20 秒的內部部署環境。 設備會彙總 20 秒範例，然後每 15 分鐘建立一個單一資料點。 為了建立單一資料點，設備會從所有 20 秒範例中選取尖峰值並將其傳送給 Azure。
- 當您在 （根據的效能持續時間和效能歷程記錄的百分位數值） 的 Azure 中建立評量時，Azure Migrate 有效使用量值，計算，並使用調整大小。
- 比方說，如果您將設定為一天的效能持續時間的百分位數 95 百分位數，Azure Migrate 會使用最後一天的收集器所傳送的 15 分鐘的取樣點，排序它們以遞增順序，而會挑選做為第 95 個百分位數值有效的使用率。
- 第 95 個百分位數值可確保您正在忽略任何極端值可能會發生，如果您使用的第 99 個百分位數。 如果您想選擇該期間的尖峰使用量，但不想錯過任何極端值，您應該選取第 99 個百分位數。

### <a name="what-is-dependency-visualization"></a>什麼是相依性視覺效果？

相依性視覺效果可讓您更有自信地評估移轉的 Vm 群組。 它交叉檢查機器相依性再進行評量。 相依性視覺效果，有助於確保，沒有遺漏任何項目，並避免非預期的中斷，當您移轉到 Azure。 Azure Migrate 會利用 Azure 監視器的記錄檔，以啟用相依性視覺效果中的服務對應解決方案。

> [!NOTE]
> 在 Azure Government 中無法使用相依性視覺效果功能。

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>我是否需要付費才能使用相依性虛擬化功能？

資料分割 [深入了解](https://azure.microsoft.com/pricing/details/azure-migrate/) Azure Migrate 定價。

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>我是否需要為相依性視覺效果安裝任何項目？

若要使用相依性視覺效果，您需要在您想要評估的每個內部部署機器上，下載並安裝代理程式。

- 必須在每個機器上安裝 [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。
- 必須在每個機器上安裝[相依性代理程式](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure)。
- 此外，如果您有無法連線至網際網路的機器，則必須在該機器上下載並安裝 Log Analytics 閘道。

除非您要使用相依性視覺效果，您不需要這些代理程式。

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>是否可使用現有的工作區進行相依性視覺化？

是，您可以將現有的工作區連結至移轉專案，並利用它的相依性視覺效果。 [深入了解](concepts-dependency-visualization.md#how-does-it-work)。

### <a name="can-i-export-the-dependency-visualization-report"></a>是否可以匯出相依性視覺效果報告？

否，您無法匯出相依性視覺效果。 不過，由於 Azure Migrate 會將服務對應用於相依性視覺效果，因此您可以使用[服務對應 REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) 取得 JSON 格式的相依性。

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>如何自動安裝 Microsoft Monitoring Agent (MMA) 和相依性代理程式？

[使用此指令碼](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples)安裝的代理程式。 [請遵循下列指示](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent)安裝 MMA 使用命令列或自動化。 MMA，對於利用[這段指令碼](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)。

除了指令碼中，您可以使用像是 System Center Configuration Manager 的部署工具[Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration)等，以部署代理程式。

### <a name="what-operating-systems-are-supported-by-mma"></a>MMA 支援哪些作業系統？

- [檢閱](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)MMA 所支援的 Windows 作業系統的清單。
- [檢閱]https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) MMA 所支援的 Linux 作業系統的清單。

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>相依性代理程式所支援的作業系統有哪些？

[檢閱](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems)相依性代理程式所支援之 Windows 作業系統。
[檢閱](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems)相依性代理程式所支援的 Linux 作業系統的清單。

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>可以超過一小時的視覺化中 Azure Migrate 的相依性嗎？
否，您可以將長達一小時的相依性視覺化。 您可以移回至特定的日期在歷程記錄中最上個月，但是視覺效果的最大持續時間是一小時。 比方說，您可以使用相依性對應中的持續時間，來檢視昨天，相依性，但只能一小時視窗檢視它。 不過，您可以使用 Azure 監視器記錄檔[查詢相依性資料](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)較長的持續期間內。

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-ten-vms"></a>相依性視覺效果支援具有超過 10 部 Vm 的群組嗎？
您可以[群組的相依性視覺化](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)具有最多十個 Vm。 如果您有超過 10 部 Vm 的群組時，我們建議您將分割中的群組，以較小的群組，並呈現視覺化相依性。

## <a name="azure-migrate-server-migration"></a>Azure Migrate:伺服器移轉

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>如何為 Azure Migrate:Azure Site Recovery 從不同的伺服器移轉嗎？

Azure Migrate:伺服器移轉會利用代理程式為基礎的移轉，伺服器至 Azure 的 Site Recovery 的複寫引擎。
## <a name="next-steps"></a>後續步驟
請參閱 [Azure Migrate 概觀](migrate-services-overview.md)
