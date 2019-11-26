---
title: 有關 Azure Migrate 的常見問題
description: 取得有關 Azure Migrate 服務常見問題的解答。
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: dc7dff0119ec849b447754ae54a45911038f6c48
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284460"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate：常見的問題

本文會回答有關 Azure Migrate 的常見問題。 如果您在閱讀本文後有進一步的查詢，請將其張貼在[Azure Migrate 論壇](https://aka.ms/AzureMigrateForum)。

## <a name="general"></a>一般

### <a name="which-azure-geographies-are-supported"></a>支援哪些 Azure 地理位置？

查看適用于[VMWARE VM](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects)和[hyper-v vm](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)的 Azure Migrate 支援地理位置。

### <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Azure Migrate 和 Site Recovery 之間有何差異？

Azure Migrate 提供集中式的中樞，以管理和追蹤內部部署機器和工作負載至 Azure 的探索、評估和遷移。 [Azure Site Recovery](../site-recovery/site-recovery-overview.md)是一種嚴重損壞修復解決方案。 Azure Migrate：伺服器遷移工具會使用一些後端 Site Recovery 功能，以進行部分內部部署機器的隨即轉移。

### <a name="how-do-i-delete-an-azure-migrate-project"></a>如何? 刪除 Azure Migrate 專案嗎？

請遵循[這些指示](how-to-delete-project.md)來刪除專案。 檢查您在 Azure Migrate 專案中探索、評估和遷移機器和工作負載時所建立[的資源](how-to-delete-project.md#created-resources)。


## <a name="azure-migrate-appliance"></a>Azure Migrate 設備

### <a name="how-does-the-appliance-connect-to-azure"></a>設備如何連接到 Azure？

連接可以透過網際網路，或使用 Azure ExpressRoute 搭配公用/Microsoft 對等互連。

### <a name="what-network-connectivity-is-needed-for-azure-migrate-server-assessment-and-migration"></a>Azure Migrate server 評估和遷移需要何種網路連線？

請參閱[VMware](migrate-support-matrix-vmware.md)和[hyper-v](migrate-support-matrix-hyper-v.md)支援矩陣，以取得 Azure Migrate 與 Azure 進行通訊所需的 url 和埠的相關資訊。

### <a name="can-i-harden-the-appliance-vm-created-with-the-template"></a>我可以強化以範本建立的設備 VM 嗎？

您可以將元件（例如，防毒軟體）新增至範本，只要您保留 Azure Migrate 設備所需的通訊和防火牆規則。

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Azure Migrate 設備會收集哪些資料？

檢查設備所收集的資料，如下所示：
- VMware Vm 的[效能資料](migrate-appliance.md#collected-performance-data-vmware)和[元](migrate-appliance.md#collected-metadata-vmware)資料。
- Hyper-v Vm 的[效能資料](migrate-appliance.md#collected-performance-data-hyper-v)和[元](migrate-appliance.md#collected-metadata-hyper-v)資料。


### <a name="does-appliance-analysis-impact-performance"></a>設備分析是否會影響效能？

Azure Migrate 設備會持續分析內部部署機器，以測量 VM 效能資料。 在 Hyper-v/ESXi 主機或 vCenter Server 上，此分析幾乎不會影響效能。

### <a name="where-and-how-long-is-collected-data-stored"></a>收集的資料會儲存在何處和多久？

Azure Migrate 設備所收集的資料會儲存在您建立遷移專案時所選擇的 Azure 位置。 資料會安全地儲存在 Microsoft 訂用帳戶中，並在您刪除 Azure Migrate 專案時刪除。

針對相依性視覺效果，收集的資料會儲存在美國，這是在 Azure 訂用帳戶中建立的 Log Analytics 工作區中。 刪除訂用帳戶中的 Log Analytics 工作區時，會刪除此資料。 [深入瞭解](concepts-dependency-visualization.md)相依性視覺效果。

### <a name="what-volume-of-data-is-uploaded-during-continuous-profiling"></a>連續分析期間會上傳的資料量為何？

傳送至 Azure Migrate 的資料量會根據數個參數而有所不同。 為了讓您瞭解磁片區，有10部機器的 Azure Migrate 專案（每個都有一個磁片和一個 NIC）每天大約會傳送 50 MB。 這個值是近似值，而且會根據 Nic 和磁片的資料點數目進行變更。 如果電腦、Nic 或磁片數目增加，則傳送的資料增加為非線性。

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>資料在待用和傳輸中是否加密？

是，兩者都是。
- 中繼資料會透過 HTTPS 安全地透過網際網路傳送至 Azure Migrate 服務。
- 中繼資料會儲存在[Azure Cosmos 資料庫](../cosmos-db/database-encryption-at-rest.md)資料庫和 Microsoft 訂用帳戶的[azure Blob 儲存體](../storage/common/storage-service-encryption.md)中。 中繼資料會進行待用加密。
- 相依性分析的資料也會在傳輸中加密（安全 HTTPS）。 它會儲存在您訂用帳戶的 Log Analytics 工作區中。 它也會進行待用加密。

### <a name="how-does-the-appliance-communicate-with-vcenter-server-and-azure-migrate"></a>設備如何與 vCenter Server 和 Azure Migrate 通訊？

1. 設備會使用您在設定設備時提供的認證，連接到 vCenter Server （埠443）。
2. 設備會使用 VMware PowerCLI 來查詢 vCenter Server，以收集 vCenter Server 所管理之 Vm 的相關中繼資料。 它會收集關於 Vm （核心、記憶體、磁片、Nic）的設定資料，以及每個 VM 在過去一個月的效能歷程記錄。
3. 收集的中繼資料接著會傳送至 Azure Migrate：伺服器評估（透過網際網路透過 HTTPS）以進行評量。

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>是否可以將相同的應用裝置連接到多個 vCenter Server 實例？

號 設備與 vCenter Server 之間會有一對一的對應。 若要探索多個 vCenter Server 實例上的 Vm，您必須部署多個應用裝置。


### <a name="machine-size-changed-can-i-run-the-assessment-again"></a>已變更機器大小。 我可以再次執行評量嗎？

Azure Migrate 設備會持續收集內部部署環境的相關資訊。 但評量是內部部署 Vm 的時間點快照集。 如果您在想要評估的 VM 上變更設定，請使用 [重新計算] 選項，以最新的變更來更新評量。

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment"></a>如何在多租使用者環境中執行探索？

- 針對 VMware，如果您的環境在租使用者之間共用，而且您不想要探索另一個租使用者的訂用帳戶中某個租使用者的 Vm，請建立 vCenter Server 認證，而只能存取您想要探索的 Vm。 然後，當您在 Azure Migrate 設備中啟動探索時，請使用這些認證。
- 針對 Hyper-v，探索會使用 Hyper-v 主機認證。 如果 Vm 共用相同的 Hyper-v 主機，目前沒有任何方法可以分隔探索。  

### <a name="how-many-vms-can-i-discover-with-a-single-appliance"></a>我可以使用單一設備探索多少部 Vm？

您最多可以探索10000個 VMware Vm，以及多達5000部具有單一遷移設備的 Hyper-v Vm。 如果您的內部部署環境中有更多電腦，請瞭解如何調整[hyper-v](scale-hyper-v-assessment.md)和[VMware](scale-vmware-assessment.md)評估。

### <a name="can-i-delete-the-azure-migrate-appliance-from-the-project"></a>我可以從專案中刪除 Azure Migrate 設備嗎？

目前不支援從專案刪除設備。

- 若要刪除設備，唯一的方法是刪除資源群組，其中包含與設備相關聯的 Azure Migrate 專案。
- 不過，刪除資源群組也會刪除其他已註冊的設備、找到的清查、評量，以及與資源群組中的專案相關聯的所有其他 Azure 元件。

## <a name="azure-migrate-server-assessment"></a>Azure Migrate 伺服器評量


### <a name="does-azure-migrate-need-vcenter-server-for-vmware-vm-discovery"></a>Azure Migrate 需要 VMWare VM 探索的 vCenter Server 嗎？

是，Azure Migrate 需要 vCenter Server 在 VMware 環境中執行探索。 它不支援探索不受 vCenter Server 管理的 ESXi 主機。

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate Server 評估與地圖工具組之間有何差異？

伺服器評估提供評估以協助進行遷移，以及評估要遷移至 Azure 的工作負載。 [Microsoft 評估及規劃（MAP）工具](https://www.microsoft.com/download/details.aspx?id=7826)組可協助進行其他工作，包括較新版本的 Windows 用戶端/伺服器作業系統的遷移規劃，以及軟體使用方式追蹤。 在這些情況下，請繼續使用地圖工具組。

### <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>伺服器評估與 Site Recovery 部署規劃工具之間有何差異？

伺服器評估是一種遷移計畫工具。 Site Recovery 部署規劃工具是一種嚴重損壞修復規劃工具。

- **規劃將內部部署遷移至 azure**：如果您想要將內部部署伺服器遷移至 azure，請使用伺服器評估來進行遷移計畫。 它會評估內部部署工作負載，並提供指引和工具來協助您進行遷移。 準備好遷移計畫之後，您可以使用工具（包括 Azure Migrate 伺服器遷移），將機器遷移至 Azure。
- **規劃 Azure**的嚴重損壞修復：如果您想要使用 Site Recovery 來設定從內部部署環境到 Azure 的嚴重損壞修復，請使用 Site Recovery 部署規劃工具。 針對嚴重損壞修復的目的，部署規劃工具提供內部部署環境的深度、Site Recovery 特定評估。 它會提供有關嚴重損壞修復的建議，例如複寫和容錯移轉。

### <a name="whats-the-difference-between-as-on-premises-and-performance-based-sizing"></a>As 內部部署和以效能為基礎的調整大小之間有何差異？

使用內部部署調整大小，Azure Migrate 不會考慮要進行評估的 VM 效能資料。 它會根據內部部署設定來評估 VM 大小。 使用以效能為基礎的調整大小，調整大小取決於使用量資料。

- 例如，如果內部部署 VM 具有4個核心和 8 GB 的記憶體（50% CPU 使用率和50% 的記憶體使用率），將會發生下列情況：
    - 內部部署調整大小會建議具有四個核心和 8 GB 記憶體的 Azure VM SKU。
    - 以效能為基礎的大小調整將會建議有兩個核心和 4 GB 記憶體的 VM SKU，因為會考慮使用率百分比。

- 同樣地，磁片大小取決於兩個評估屬性：調整大小準則和儲存體類型。
    - 如果調整大小準則是以效能為基礎，且儲存體類型是自動的，Azure Migrate 會在識別目標磁片類型（標準或高階）時，將磁片的 IOPS 和輸送量值納入考慮。
    - 如果調整大小準則是以效能為基礎，且儲存體類型為 premium，Azure Migrate 會根據內部部署磁片的大小來建議 premium 磁片 SKU。 當調整大小為 [內部部署] 且儲存體類型為 [標準] 或 [premium] 時，會將相同的邏輯套用至磁片大小調整。

### <a name="does-performance-history-and-utilization-percentile-impact-size-recommendations"></a>效能歷程記錄和使用量百分位數是否會影響大小的建議？

這些屬性僅適用於以效能為基礎的調整大小。

- Azure Migrate 會收集內部部署機器的效能歷程記錄，並使用它來建議 Azure 中的 VM 大小和磁片類型。
- 設備會持續分析內部部署環境，以便每隔20秒收集一次即時使用方式資料。
- 設備會匯總20秒的樣本，並每隔15分鐘建立一個單一資料點。
- 若要建立資料點，設備會從所有20秒範例中選取尖峰值。
- 應用裝置會將此資料點傳送至 Azure。

當您在 Azure 中建立評估時，根據效能持續時間和效能歷程百分位數值，Azure Migrate 會計算有效的使用率值，並將其用於調整大小。

- 例如，如果您將 [效能持續時間] 設為 [一天]，並將 [百分位數] 值設定為 [95 個百分位數]，Azure Migrate 會以遞增順序排序收集器過去一天所傳送的15分鐘取樣點，並挑選第95個百分位數值作為有效的方面.
- 使用第95個百分位數值可確保忽略極端值。 如果您使用第99個百分位數，則可以包含極端值。 如果您想要挑選期間的尖峰使用量，但沒有遺漏任何極端值，請選取第99個百分位數。

## <a name="server-assessment---dependency-visualization"></a>伺服器評估-相依性視覺效果


### <a name="what-is-dependency-visualization"></a>什麼是相依性視覺效果？

使用相依性視覺效果來評估 Vm 群組，以便更安心地進行遷移。 相依性視覺效果會在您執行評量之前，先交叉檢查機器相依性。 它有助於確保不會留下任何內容，因此可在您遷移至 Azure 時避免非預期的中斷。 Azure Migrate 使用了 Azure 監視器中的 [服務對應] 解決方案來啟用相依性視覺效果。 [深入瞭解](concepts-dependency-visualization.md)。

> [!NOTE]
> Azure Government 無法使用相依性視覺效果。

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>我是否需要支付使用相依性視覺效果的費用？
號 [深入了解](https://azure.microsoft.com/pricing/details/azure-migrate/) Azure Migrate 定價。

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>我是否需要為相依性視覺效果安裝任何項目？

若要使用相依性視覺效果，您需要在您想要評估的每個內部部署機器上，下載並安裝代理程式。

您必須在每部電腦上安裝下列代理程式：
- [Microsoft Monitoring Agent （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。
- [Dependency agent](../azure-monitor/platform/agents-overview.md#dependency-agent)。
- 如果您的電腦沒有網際網路連線，則需要下載並安裝 Log Analytics 閘道。

除非您要使用相依性視覺效果，否則不需要這些代理程式。

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>是否可使用現有的工作區進行相依性視覺化？

是，您可以將現有的工作區附加至遷移專案，並將其用於相依性視覺效果。 [深入了解](concepts-dependency-visualization.md#how-does-it-work)。

### <a name="can-i-export-the-dependency-visualization-report"></a>是否可以匯出相依性視覺效果報告？

否，無法匯出相依性視覺效果。 不過，Azure Migrate 會使用服務對應，而且您可以使用[服務對應 REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections)來取得 JSON 格式的相依性。

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>如何自動安裝 Microsoft Monitoring Agent （MMA）和 Dependency Agent？

使用此[腳本來安裝 Dependency agent](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)。 遵循這些指示，使用命令列或自動化[來安裝 MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) 。 針對 MMA，請使用[此腳本](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)。

除了腳本以外，您也可以使用 System Center Configuration Manager 和[Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration)等部署工具來部署代理程式。


### <a name="what-operating-systems-are-supported-by-mma"></a>MMA 支援哪些作業系統？

- 查看[MMA 所支援的 Windows 作業系統](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)清單。
- 查看[MMA 所支援的 Linux 作業系統](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)清單。

### <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>我可以將相依性視覺化超過一小時嗎？
號 您最多可以將相依性視覺化一小時。 您可以回到歷程記錄中的特定日期，一開始就是一個月，但視覺效果的最大持續時間為一小時。 例如，您可以使用相依性對應中的持續時間來查看昨天的相依性，但只能查看一個小時範圍內的相依性。 不過，您可以使用 Azure 監視器記錄，在較長的持續期間內查詢相依性[資料](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)。

### <a name="can-i-use-dependency-visualization-for-groups-of-more-than-10-vms"></a>我可以針對超過10個 Vm 的群組使用相依性視覺效果嗎？
您可以將包含最多10個 Vm 的群組的相依性[視覺化](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)。 如果您的群組具有10部以上的 Vm，建議您將群組分割成較小的群組，然後將相依性視覺化。

## <a name="azure-migrate-server-migration"></a>Azure Migrate 伺服器移轉

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-site-recovery"></a>Azure Migrate Server 遷移與 Site Recovery 之間有何差異？

- 內部部署 VMware Vm 的無代理程式遷移在 Azure Migrate Server 遷移內是原生的。
- 若要遷移 Hyper-v Vm、實體伺服器和以代理程式為基礎的 VMware Vm，Azure Migrate Server 遷移會使用 Azure Site Recovery 複寫引擎。


## <a name="next-steps"></a>後續步驟
閱讀[Azure Migrate 的總覽](migrate-services-overview.md)。
