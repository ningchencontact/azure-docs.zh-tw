---
title: Azure Migrate - 常見問題集 (FAQ) | Microsoft Docs
description: 解說 Azure Migrate 的相關常見問題
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: snehaa
ms.openlocfilehash: 0708502087ae6880d9559cf17f0ba9982b2ba040
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372479"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate - 常見問題集 (FAQ)

本文包含有關 Azure Migrate 的常見問題集。 如果您在閱讀本文後仍有其他問題，請將問題張貼在 [Azure Migrate 論壇](https://aka.ms/AzureMigrateForum)。

## <a name="general"></a>一般

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Azure Migrate 支援哪些 Azure 地理區？

您可以在這裡找到適用于[VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects)的清單, 以及[這裡的 hyper-v](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)。

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Azure Migrate 和 Azure Site Recovery 有何不同？

Azure Migrate 提供集中式的中樞, 以啟動、執行和追蹤機器和工作負載至 Azure 的探索、評估和遷移。 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure)是一種嚴重損壞修復解決方案。 Azure Migrate Server 遷移會利用後端中的 Azure Site Recovery 來啟用內部部署機器的隨即轉移遷移案例。

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Azure Migrate 設備 (VMware/實體伺服器)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Azure Migrate 設備如何連接到 Azure？

連線可以透過網際網路, 或您可以使用 ExpressRoute 搭配公用/Microsoft 對等互連。

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>Azure Migrate Server 評估和遷移所需的網路連線需求

如需 Azure Migrate 與 Azure 進行通訊所需的 Url 和埠, 請參閱[VMWare](migrate-support-matrix-vmware.md)和[hyper-v](migrate-support-matrix-hyper-v.md)支援矩陣。

### <a name="can-i-harden-the-appliance-vm-i-set-up-with-the-template"></a>我可以強化我使用範本設定的設備 VM 嗎？

您可以將其他元件 (例如防毒軟體) 新增到範本中, 只要 Azure Migrate 設備所需的通訊和防火牆規則保持不到。   

### <a name="what-data-is-collected-by-azure-migrate-appliance"></a>Azure Migrate 設備會收集哪些資料？

您可以在[這裡](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware)詳細說明 Azure Migrate 設備所收集的資料。

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>分析的 VMware 或 Hyper-v 環境是否有任何效能影響？

透過持續分析效能資料, Azure Migrate 設備會配置內部部署機器來測量 VM 效能資料。 這在 Hyper-v/ESXi 主機上以及 vCenter Server 上幾乎不會影響任何效能。

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>資料收集後會儲存在哪裡、儲存多久？

Azure Migrate 設備所收集的資料會儲存在您建立遷移專案時指定的 Azure 位置。 資料會安全地儲存在 Microsoft 訂用帳戶中, 並在您刪除 Azure Migrate 專案時刪除。

針對相依性視覺效果, 如果您在 Vm 上安裝代理程式, 則相依性代理程式所收集的資料會儲存在美國的 Azure 訂用帳戶所建立的 Log Analytics 工作區中。 刪除訂用帳戶中的 Log Analytics 工作區時，會刪除此資料。 [深入了解](concepts-dependency-visualization.md)。

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-appliance-during-continuous-profiling"></a>在連續分析期間, Azure Migrate 設備所上傳的資料量為何？

傳送至 Azure Migrate 的資料量會根據數個參數而有所不同。 若要提供指示編號, 包含10部機器的 Azure Migrate 專案 (每個都有一個磁片和一個 NIC), 每天大約會傳送 50 MB。 這是大約的值, 會根據 Nic 和磁片的資料點數目進行變更 (如果機器、Nic 或磁片數目增加, 傳送的資料為非線性)。

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>資料待用和傳輸中是否加密？

兩者皆為 [是]。 中繼資料會透過 HTTPs 安全地透過網際網路傳送至 Azure Migrate 服務。 中繼資料會儲存在[Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)中, 以及 Microsoft 訂用帳戶的[Azure blob 儲存體](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)中, 並且會進行待用加密。

相依性代理程式所收集的資料也會在傳輸 (安全 HTTPS) 中進行加密, 並儲存在使用者訂用帳戶的 Log Analytics 工作區中。 它也會進行待用加密。

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Azure Migrate 設備如何與 vCenter Server 和 Azure Migrate 服務通訊？

設備會使用您設定設備時所提供的認證, 連接到 vCenter Server (埠 443)。 它會使用 VMware PowerCLI 來查詢 vCenter Server, 以收集 vCenter Server 所管理之 Vm 的相關中繼資料。 它會收集關於 Vm (核心、記憶體、磁片、NIC 等) 的設定資料, 以及每個 VM 在上個月的效能歷程記錄。 收集的中繼資料接著會傳送至 Azure Migrate 伺服器評估 (透過網際網路透過 HTTPS) 進行評量。

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>是否可以將相同的設備連線到多部 vCenter server？

是, 單一 Azure Migrate 設備可用來探索多部 vCenter Server, 但不能同時使用。 您需要逐一執行探索。

### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>我已變更機器大小。 我可以重新執行評量嗎？

Azure Migrate 設備會持續收集內部部署環境的相關資訊。 不過, 評量是內部部署 Vm 的時間點快照集。 如果您變更想要評估之 VM 上的設定, 請使用 [重新計算] 選項, 以最新的變更來更新評量。

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate-server-assessment"></a>如何在 Azure Migrate Server 評估中探索多租使用者環境？

針對 VMware, 如果您有跨租使用者共用的環境, 而且您不想要探索另一個租使用者訂用帳戶中某個租使用者的 Vm, 請建立 vCenter Server 認證, 只存取您想要探索的 Vm。 然後在 Azure Migrate 設備中開始關閉探索時使用認證。

若為 Hyper-v, 探索會使用 Hyper-v 主機認證。如果 Vm 共用相同的 Hyper-v 主機, 則目前無法分隔探索。  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>可以使用單一遷移設備探索多少部 Vm？

您可以使用單一遷移設備探索最多10000個 VMware Vm 和最多5000部 Hyper-v Vm。  如果您的內部部署環境中有更多電腦, 請瞭解如何調整[hyper-v](scale-hyper-v-assessment.md)和[VMware](scale-vmware-assessment.md)評估。

## <a name="azure-migrate-server-assessment"></a>Azure Migrate：伺服器評量

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure Migrate:伺服器評估支援對實體伺服器進行評估嗎？

否, Azure Migrate 目前不支援實體伺服器的評量。

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate 是否需要 vCenter Server 才能探索 VMware 環境？

是, Azure Migrate 需要 vCenter Server 才能探索 VMware 環境。 它不支援探索不受 vCenter Server 管理的 ESXi 主機。

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>使用 Azure Migrate 的差異為何:伺服器評估和地圖工具組？

Azure Migrate：伺服器評估提供遷移評估來協助遷移準備, 以及評估工作負載以遷移至 Azure。 [Microsoft 評估及規劃 (MAP) 工具](https://www.microsoft.com/download/details.aspx?id=7826)組有其他功能, 例如適用于較新版本的 Windows 用戶端和伺服器作業系統的遷移規劃, 以及軟體使用方式追蹤。 若有這些情況，請繼續使用 MAP Toolkit。

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>如何 Azure Migrate:伺服器評估與 Azure Site Recovery 部署規劃工具不同嗎？

Azure Migrate：伺服器評估是一種遷移計畫工具。 Azure Site Recovery 部署規劃工具是一種嚴重損壞修復規劃工具。

- **從 VMware/Hyper-v 遷移至 Azure**:如果您想要將內部部署伺服器遷移至 Azure, 請使用 Azure Migrate:用於遷移計畫的伺服器評估工具。 此工具會評估內部部署工作負載, 並提供指引、深入解析和機制, 協助您遷移至 Azure。 準備好您的遷移計畫之後, 您就可以使用像是 Azure Migrate 的工具:伺服器遷移, 將機器遷移至 Azure。
- **從 VMware/Hyper-V 移轉至 Azure 的災難復原**：若要使用 Site Recovery 在 Azure 中進行嚴重損壞修復, 請使用 Site Recovery 部署規劃工具進行嚴重損壞修復規劃。 Site Recovery 部署規劃工具會針對您的內部部署環境進行深入的 Site Recovery 特定評估。 它會針對成功的災難作業 (例如複寫和 Vm 容錯移轉) 提供 Site Recovery 所需的建議。

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Azure Migrate 是否支援以 Enterprise 合約 (EA) 為基礎的成本估計？

Azure Migrate 目前不支援[Enterprise 合約供應](https://azure.microsoft.com/offers/enterprise-agreement-support/)專案的成本估計。 因應措施是將隨用隨付指定為供應專案, 並在評量屬性的 [折扣] 欄位中手動指定折扣百分比 (適用于訂用帳戶)。

  ![折扣](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>身為內部部署調整大小和以效能為基礎的大小之間有何差異？

- 在中, 當內部部署調整大小時, Azure Migrate 不會考慮 VM 效能資料。 它會根據內部部署設定來調整 Vm 大小。 -在以效能為基礎的調整大小中, 調整大小取決於使用量資料。
- 例如, 如果內部部署 VM 具有4個核心和 8 GB 的記憶體, 並具有 50% CPU 使用率和 50% 的記憶體使用率, 則內部部署調整大小會建議具有四個核心和 8 GB 記憶體的 Azure VM SKU。 不過, 以效能為基礎的大小調整會建議兩個核心的 VM SKU 和 4 GB, 因為會考慮使用率百分比。
- 同樣地, 磁片大小取決於兩個評估屬性: 調整大小準則和儲存體類型。
= 如果調整大小準則是以效能為基礎, 且儲存體類型是自動, 則識別目標磁片類型 (標準或高階) 時, 會考慮磁片的 IOPS 和輸送量值。
- 如果調整大小準則是以效能為基礎, 且儲存體類型為 premium, 則建議使用 premium 磁片。 Premium 磁片 SKU 是根據內部部署磁片的大小來選取。 當調整大小準則是作為內部部署調整大小, 且儲存體類型是 standard 或 premium 時, 會使用相同的邏輯來進行磁片大小調整。

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>效能歷程記錄和百分位數使用率對大小建議有何影響？

這些屬性僅適用於以效能為基礎的調整大小。

- Azure Migrate 會收集內部部署機器的效能歷程記錄, 並使用它來建議 Azure 中的 VM 大小和磁片類型。
- 設備會持續分析內部部署環境, 每隔20秒收集一次即時使用方式資料。 設備會彙總 20 秒範例，然後每 15 分鐘建立一個單一資料點。 為了建立單一資料點，設備會從所有 20 秒範例中選取尖峰值並將其傳送給 Azure。
- 當您在 Azure 中建立評量 (根據效能持續時間和效能歷程百分位數值) 時, Azure Migrate 會計算有效的使用率值, 並將其用於調整大小。
- 例如, 如果您將效能持續時間設定為一天, 並將百分位數值設為95個百分位數, Azure Migrate 會使用收集器針對最後一天所傳送的15分鐘取樣點, 以遞增順序排序, 並挑選第95個百分位數的值做為有效的使用率。
- 第95個百分位數值可確保您忽略任何極端值, 如果您使用第99個百分位數, 就會發生這種情況。 如果您想選擇該期間的尖峰使用量，但不想錯過任何極端值，您應該選取第 99 個百分位數。

### <a name="what-is-dependency-visualization"></a>什麼是相依性視覺效果？

相依性視覺效果可讓您更安心地評估 Vm 群組以進行遷移。 它會在您執行評量之前, 先交叉檢查機器相依性。 相依性視覺效果可協助確保不會留下任何內容, 並避免在您遷移至 Azure 時出現非預期的中斷。 Azure Migrate 會利用 Azure 監視器記錄中的服務對應解決方案來啟用相依性視覺效果。

> [!NOTE]
> 在 Azure Government 中無法使用相依性視覺效果功能。

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>我是否需要付費才能使用相依性虛擬化功能？

資料分割 [深入了解](https://azure.microsoft.com/pricing/details/azure-migrate/) Azure Migrate 定價。

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>我是否需要為相依性視覺效果安裝任何項目？

若要使用相依性視覺效果，您需要在您想要評估的每個內部部署機器上，下載並安裝代理程式。

- 必須在每個機器上安裝 [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。
- 必須在每個機器上安裝[相依性代理程式](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure)。
- 此外，如果您有無法連線至網際網路的機器，則必須在該機器上下載並安裝 Log Analytics 閘道。

除非您要使用相依性視覺效果, 否則不需要這些代理程式。

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>是否可使用現有的工作區進行相依性視覺化？

是, 您可以將現有的工作區附加至遷移專案, 並將其運用於相依性視覺效果。 [深入了解](concepts-dependency-visualization.md#how-does-it-work)。

### <a name="can-i-export-the-dependency-visualization-report"></a>是否可以匯出相依性視覺效果報告？

否, 無法匯出相依性視覺效果。 不過，由於 Azure Migrate 會將服務對應用於相依性視覺效果，因此您可以使用[服務對應 REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) 取得 JSON 格式的相依性。

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>如何自動安裝 Microsoft Monitoring Agent (MMA) 和相依性代理程式？

[使用此腳本](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples)來安裝代理程式。 [遵循這些指示](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent), 使用命令列或自動化來安裝 MMA。 針對 MMA, 請利用[此腳本](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)。

除了腳本以外, 您還可以使用 System Center Configuration Manager、 [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration)等部署工具來部署代理程式。

### <a name="what-operating-systems-are-supported-by-mma"></a>MMA 支援哪些作業系統？

- [查看](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)MMA 支援的 Windows 作業系統清單。
- [查看](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)MMA 所支援的 Linux 作業系統清單。

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>相依性代理程式支援哪些作業系統？

[檢查](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems)Dependency agent 所支援的 Windows 作業系統。
[檢查](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems)Dependency agent 所支援的 Linux 作業系統清單。

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>我可以將 Azure Migrate 中的相依性視覺化超過一小時嗎？
否, 您最多可以將相依性視覺化一小時。 您可以回到過去一個月的歷程記錄中的特定日期, 但視覺效果的最大持續時間為一小時。 例如, 您可以使用相依性對應中的持續時間來查看昨天的相依性, 但只能查看一個小時的時段。 不過, 您可以使用 Azure 監視器記錄, 在較長的持續期間內查詢相依性[資料](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)。

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>相依性視覺效果是否支援含有超過 10 個 VM 的群組？
您可以將最多10個 Vm 的群組相依性[視覺化](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)。 如果您的群組具有10部以上的 Vm, 建議您將群組分割成較小的群組, 然後將相依性視覺化。

## <a name="azure-migrate-server-migration"></a>Azure Migrate：伺服器移轉

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>如何 Azure Migrate:伺服器遷移與 Azure Site Recovery 不同嗎？

Azure Migrate：伺服器遷移利用 Site Recovery 的複寫引擎來進行 VMware Vm 的代理程式式遷移、將 Hyper-v Vm 遷移至 Azure, 以及將實體伺服器遷移至 Azure。 遷移 VMware Vm 的無代理程式選項原本就是在伺服器遷移中建立。

## <a name="next-steps"></a>後續步驟
請參閱 [Azure Migrate 概觀](migrate-services-overview.md)
