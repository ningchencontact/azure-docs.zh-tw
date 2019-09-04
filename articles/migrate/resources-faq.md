---
title: Azure Migrate-常見問題 (FAQ) |Microsoft Docs
description: 解說 Azure Migrate 的相關常見問題
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: snehaa
ms.openlocfilehash: 46c6ac52e1afb6c1619b814580a1059fd3dfedda
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279493"
---
# <a name="azure-migrate-frequently-asked-questions-faq"></a>Azure Migrate：常見問題集 (FAQ)

本文會回答有關 Azure Migrate 的常見問題。 如果您在閱讀本文後有進一步的查詢, 請將其張貼在[Azure Migrate 論壇](https://aka.ms/AzureMigrateForum)。

## <a name="general"></a>一般

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Azure Migrate 支援哪些 Azure 地理位置？

請參閱[VMware 清單](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects)和[hyper-v 清單](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)。

### <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Azure Migrate 和 Azure Site Recovery 之間有何差異？

Azure Migrate 提供集中式中樞來開始您的遷移、執行和追蹤機器和工作負載的探索和評估, 以及執行和追蹤機器和工作負載至 Azure 的遷移。 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure)是一種嚴重損壞修復解決方案。 Azure Migrate Server 遷移會在後端使用 Azure Site Recovery, 以啟用內部部署機器的隨即轉移的遷移案例。

## <a name="azure-migrate-appliance"></a>Azure Migrate 設備

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Azure Migrate 設備如何連接到 Azure？

連線可以透過網際網路, 或您可以使用 Azure ExpressRoute 搭配公用/Microsoft 對等互連。

### <a name="what-are-the-network-connectivity-requirements-for-azure-migrate-server-assessment-and-migration"></a>Azure Migrate server 評估與遷移的網路連線需求為何？

如需啟用 Azure Migrate 與 Azure 進行通訊所需之 Url 和埠的相關資訊, 請參閱[VMware](migrate-support-matrix-vmware.md)和[hyper-v](migrate-support-matrix-hyper-v.md)支援矩陣。

### <a name="can-i-harden-the-appliance-vm-that-i-set-up-with-the-template"></a>我可以強化我使用範本設定的設備 VM 嗎？

您可以將元件 (例如, 防毒軟體) 新增至範本, 只要您保留 Azure Migrate 設備所需的通訊和防火牆規則。

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Azure Migrate 設備會收集哪些資料？

您可以[在 Azure Migrate 檔中](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware), 查看 Azure Migrate 應用裝置所收集資料的詳細資訊。

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>分析的 VMware 或 Hyper-v 環境是否有任何效能影響？

Azure Migrate 設備會持續分析內部部署機器, 以測量 VM 效能資料。 在 Hyper-v/ESXi 主機或 vCenter Server 上, 此分析幾乎不會影響效能。

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>收集的資料會儲存在哪裡, 以及有多長的時間？

Azure Migrate 設備所收集的資料會儲存在您建立遷移專案時所選擇的 Azure 位置。 資料會安全地儲存在 Microsoft 訂用帳戶中, 並在您刪除 Azure Migrate 專案時刪除。

針對相依性視覺效果, 如果您在 Vm 上安裝代理程式, 則相依性代理程式所收集的資料會儲存在美國的 Azure 訂用帳戶所建立的 Log Analytics 工作區中。 刪除訂用帳戶中的 Log Analytics 工作區時，會刪除此資料。 如需詳細資訊, 請參閱相依性[視覺效果](concepts-dependency-visualization.md)。

### <a name="what-volume-of-data-is-uploaded-by-the-azure-migrate-appliance-during-continuous-profiling"></a>在連續分析期間, Azure Migrate 設備會上傳了哪些資料量？

傳送至 Azure Migrate 的資料量會根據數個參數而有所不同。 為讓您瞭解磁片區: 具有10部機器的 Azure Migrate 專案 (每個都有一個磁片和一個 NIC) 每天大約會傳送 50 MB。 這個值是近似值。 它會根據 Nic 和磁片的資料點數目來變更。 (如果電腦、Nic 或磁片數目增加, 則傳送的資料增加為非線性)。

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>資料是否會在待用和傳輸中加密？

是, 兩者都是。 中繼資料會透過 HTTPS 安全地透過網際網路傳送至 Azure Migrate 服務。 中繼資料會儲存在[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)資料庫和 Microsoft 訂用帳戶的[Azure Blob 儲存體](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)中。 中繼資料會在待用時加密。

相依性代理程式所收集的資料也會在傳輸 (安全 HTTPS) 中進行加密。 它會儲存在您訂用帳戶的 Log Analytics 工作區中。 它也會在靜止時加密。

### <a name="how-does-the-azure-migrate-appliance-communicate-with-vcenter-server-and-the-azure-migrate-service"></a>Azure Migrate 設備如何與 vCenter Server 和 Azure Migrate 服務通訊？

設備會使用您在設定設備時所提供的認證, 連接到 vCenter Server (埠 443)。 它會使用 VMware PowerCLI 來查詢 vCenter Server, 以收集 vCenter Server 所管理之 Vm 的相關中繼資料。 它會收集關於 Vm (核心、記憶體、磁片、Nic 等等) 的設定資料, 以及每個 VM 在過去一個月的效能歷程記錄。 收集的中繼資料接著會傳送至 Azure Migrate Server 評估 (透過網際網路透過 HTTPS) 進行評量。

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>是否可以將相同的應用裝置連接到多個 vCenter Server 實例？

資料分割 設備與 vCenter Server 之間會有一對一的對應。 如果您需要在多個 vCenter Server 實例上探索 Vm, 您必須部署多個應用裝置。


### <a name="i-changed-my-machine-size-can-i-run-the-assessment-again"></a>我已變更機器大小。 我可以再次執行評量嗎？

Azure Migrate 設備會持續收集內部部署環境的相關資訊。 但評量是內部部署 Vm 的時間點快照集。 如果您在想要評估的 VM 上變更設定, 請使用 [重新計算] 選項, 以最新的變更來更新評量。

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment-in-azure-migrate-server-assessment"></a>如何在 Azure Migrate Server 評估的多租使用者環境中執行探索？

針對 VMware, 如果您有跨租使用者共用的環境, 而且不想要探索另一個租使用者訂用帳戶中某個租使用者的 Vm, 請建立 vCenter Server 認證, 而只能存取您想要探索的 Vm。 然後, 當您在 Azure Migrate 設備中啟動探索時, 請使用這些認證。

針對 Hyper-v, 探索會使用 Hyper-v 主機認證。 如果 Vm 共用相同的 Hyper-v 主機, 目前沒有任何方法可以分隔探索。  

### <a name="how-many-vms-can-i-discover-with-a-single-migration-appliance"></a>我可以使用單一遷移設備探索多少部 Vm？

您最多可以探索10000個 VMware Vm, 以及多達5000部具有單一遷移設備的 Hyper-v Vm。 如果您的內部部署環境中有更多電腦, 請瞭解如何調整[hyper-v](scale-hyper-v-assessment.md)和[VMware](scale-vmware-assessment.md)評估。

### <a name="can-i-delete-the-azure-migrate-appliance-from-the-project"></a>我可以從專案中刪除 Azure Migrate 設備嗎？
目前不支援從專案刪除設備。 若要刪除設備, 唯一的方法是刪除具有 Azure Migrate 專案且與設備相關聯的資源群組, 但這也會刪除其他已註冊的設備、已探索的清查、評估和所有其他 Azure 成品與資源群組中的專案相關聯。

## <a name="azure-migrate-server-assessment"></a>Azure Migrate 伺服器評量

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure Migrate Server 評定支援對實體伺服器進行評估嗎？

否, Azure Migrate 目前不支援實體伺服器的評量。

### <a name="does-azure-migrate-need-vcenter-server-to-perform-discovery-in-a-vmware-environment"></a>Azure Migrate 需要 vCenter Server 在 VMware 環境中執行探索嗎？

是, Azure Migrate 需要 vCenter Server 在 VMware 環境中執行探索。 它不支援探索不受 vCenter Server 管理的 ESXi 主機。

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate Server 評估與地圖工具組之間有何差異？

Azure Migrate Server 評估提供遷移評估來協助遷移準備, 以及評估工作負載以遷移至 Azure。 [Microsoft 評估及規劃 (MAP) 工具](https://www.microsoft.com/download/details.aspx?id=7826)組可協助進行其他工作, 像是較新版本的 Windows 用戶端和伺服器作業系統的遷移規劃和軟體使用方式追蹤。 若有這些情況，請繼續使用 MAP Toolkit。

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-azure-site-recovery-deployment-planner"></a>Azure Migrate Server 評估和 Azure Site Recovery 部署規劃工具之間有何差異？

Azure Migrate Server 評估是一種遷移規劃工具。 Azure Site Recovery 部署規劃工具是一種嚴重損壞修復規劃工具。

**從 VMware/Hyper-v 遷移至 Azure**:如果您打算將內部部署伺服器遷移至 Azure, 請使用 Azure Migrate Server 評估來進行遷移規劃。 它會評估內部部署工作負載, 並提供指引、深入解析和工具, 協助您將伺服器遷移至 Azure。 準備好您的遷移計畫之後, 您可以使用 Azure Migrate Server 遷移之類的工具, 將機器遷移至 Azure。

**從 VMware/hyper-v 到 Azure 的**嚴重損壞修復:若要透過 Site Recovery 的嚴重損壞修復至 Azure, 請使用 Site Recovery 部署規劃工具進行規劃。 Site Recovery 部署規劃工具會針對您的內部部署環境進行深入的 Site Recovery 特定評估。 它會提供 Site Recovery 成功的嚴重損壞作業所需的建議, 例如 Vm 的複寫和容錯移轉。

### <a name="does-azure-migrate-support-cost-estimation-for-the-enterprise-agreement-ea-program"></a>Azure Migrate 是否支援 Enterprise 合約 (EA) 方案的成本估計？

Azure Migrate 目前不支援[Enterprise 合約計畫](https://azure.microsoft.com/offers/enterprise-agreement-support/)的成本估計。 因應措施是指定預付型方案做為**供應**專案, 並在評量屬性的 [**折扣**] 方塊中手動指定折扣百分比 (適用于訂**用**帳戶):

  ![評定屬性](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>身為內部部署調整大小和以效能為基礎的大小之間有何差異？

使用內部部署調整大小, Azure Migrate 不會考慮 VM 效能資料。 它會根據內部部署設定來調整 Vm 大小。 使用以效能為基礎的調整大小, 調整大小取決於使用量資料。

例如, 假設內部部署 VM 具有4個核心和 8 GB 的記憶體, 50% CPU 使用率和 50% 的記憶體使用率。 針對此 VM, 內部部署調整大小會建議具有四個核心和 8 GB 記憶體的 Azure VM SKU。 以效能為基礎的大小調整會建議具有兩個核心和 4 GB 記憶體的 VM SKU, 因為會考慮使用率百分比。

同樣地, 磁片大小取決於兩個評估屬性: 調整大小準則和儲存體類型。 如果調整大小準則是以效能為基礎, 且儲存體類型是自動的, Azure Migrate 會在識別目標磁片類型 (標準或高階) 時, 考慮磁片的 IOPS 和輸送量值。

如果調整大小準則是以效能為基礎, 且儲存體類型為 premium, Azure Migrate 建議 premium 磁片。 Premium 磁片 SKU 是根據內部部署磁片的大小來選取。 當調整大小準則為 [內部部署大小] 且儲存體類型為 [標準] 或 [premium] 時, 會使用相同的邏輯來進行磁片大小調整。

### <a name="what-impact-does-performance-history-and-utilization-percentile-have-on-size-recommendations"></a>效能歷程記錄和使用量百分位數對大小建議有何影響？

這些屬性僅適用於以效能為基礎的調整大小。

Azure Migrate 會收集內部部署機器的效能歷程記錄, 並使用它來建議 Azure 中的 VM 大小和磁片類型。

設備會持續分析內部部署環境, 每隔20秒收集一次即時使用方式資料。 設備會匯總20秒的樣本, 並每隔15分鐘建立一個單一資料點。 若要建立資料點, 設備會從所有20秒範例中選取尖峰值。 應用裝置會將此資料點傳送至 Azure。

當您在 Azure 中建立評量 (根據效能持續時間和效能歷程百分位數值) 時, Azure Migrate 會計算有效的使用率值, 並將其用於調整大小。

例如, 假設您將效能持續時間設定為一天, 而百分位數值設為95個百分位數。 Azure Migrate 會以遞增的順序排序收集器過去一天所傳送的15分鐘取樣點, 並挑選第95個百分位數值作為有效使用率。

第95個百分位數值可確保您忽略任何極端值。 如果您使用第99個百分位數, 則可以包含極端值。 如果您想要挑選週期的尖峰使用量, 而不想錯過任何極端值, 請選取第99個百分位數。

### <a name="what-is-dependency-visualization"></a>什麼是相依性視覺效果？

相依性視覺效果可讓您更安心地評估 Vm 群組以進行遷移。 它會在您執行評量之前, 先交叉檢查機器相依性。 相依性視覺效果有助於確保當您遷移至 Azure 時, 不會留下任何內容, 以避免發生非預期的中斷。 Azure Migrate 使用 Azure 監視器記錄中的服務對應方案來啟用相依性視覺效果。

> [!NOTE]
> Azure Government 中無法使用相依性視覺效果。

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>我是否需要支付使用相依性視覺效果的費用？

資料分割 如需詳細資訊, 請參閱[Azure Migrate 定價](https://azure.microsoft.com/pricing/details/azure-migrate/)。

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>我是否需要為相依性視覺效果安裝任何項目？

若要使用相依性視覺效果，您需要在您想要評估的每個內部部署機器上，下載並安裝代理程式。

您必須在每部電腦上安裝下列代理程式:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。
- [Dependency agent](../azure-monitor/platform/agents-overview.md#dependency-agent)。
- 如果您的電腦沒有網際網路連線, 則需要下載並安裝 Log Analytics 閘道。

除非您要使用相依性視覺效果, 否則不需要這些代理程式。

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>是否可使用現有的工作區進行相依性視覺化？

是, 您可以將現有的工作區附加至遷移專案, 並將其用於相依性視覺效果。 如需詳細資訊, 請參閱相依性[視覺效果](concepts-dependency-visualization.md#how-does-it-work)一文中的「如何運作」。

### <a name="can-i-export-the-dependency-visualization-report"></a>是否可以匯出相依性視覺效果報告？

否, 無法匯出相依性視覺效果。 但是因為 Azure Migrate 使用相依性視覺效果的服務對應, 您可以使用[服務對應 REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections)來取得 JSON 格式的相依性。

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>如何自動安裝 Microsoft Monitoring Agent (MMA) 和 Dependency Agent？

使用此[腳本來安裝 Dependency agent](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)。 遵循這些指示, 使用命令列或自動化[來安裝 MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) 。 針對 MMA, 請使用[此腳本](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)。

除了腳本以外, 您也可以使用 System Center Configuration Manager 和[Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration)等部署工具來部署代理程式。

### <a name="what-operating-systems-are-supported-by-mma"></a>MMA 支援哪些作業系統？

- 查看[MMA 所支援的 Windows 作業系統](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)清單。
- 查看[MMA 所支援的 Linux 作業系統](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)清單。

### <a name="what-operating-systems-are-supported-by-the-dependency-agent"></a>相依性代理程式支援哪些作業系統？

查看[適用於 VM 的 Azure 監視器支援的 Windows 和 Linux 作業系統](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)清單。

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>我可以將 Azure Migrate 中的相依性視覺化超過一小時嗎？
資料分割 您最多可以將相依性視覺化一小時。 您可以回到歷程記錄中的特定日期, 一開始就是一個月, 但視覺效果的最大持續時間為一小時。 例如, 您可以使用相依性對應中的持續時間來查看昨天的相依性, 但只能針對一小時的時段進行查看。 不過, 您可以使用 Azure 監視器記錄, 在較長的持續期間內查詢相依性[資料](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)。

### <a name="can-i-use-dependency-visualization-for-groups-that-contain-more-than-10-vms"></a>我可以針對包含超過10個 Vm 的群組使用相依性視覺效果嗎？
您可以將包含最多10個 Vm 的群組的相依性[視覺化](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)。 如果您的群組具有10部以上的 Vm, 建議您將群組分割成較小的群組, 然後將相依性視覺化。

## <a name="azure-migrate-server-migration"></a>Azure Migrate 伺服器移轉

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-azure-site-recovery"></a>Azure Migrate Server 遷移與 Azure Site Recovery 之間有何差異？

Azure Migrate Server 遷移會使用 Site Recovery 複寫引擎來進行 VMware Vm 的代理程式式遷移、Hyper-v Vm 的遷移, 以及將實體伺服器遷移至 Azure。 遷移 VMware Vm 的無代理程式選項原本就內建在伺服器遷移中。

## <a name="next-steps"></a>後續步驟
閱讀[Azure Migrate 的總覽](migrate-services-overview.md)。
