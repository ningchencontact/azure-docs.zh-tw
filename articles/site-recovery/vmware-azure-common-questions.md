---
title: 有關常見問題 VMware 到 Azure 的災害復原，使用 Azure Site Recovery |Microsoft Docs
description: 使用 Azure Site Recovery，以取得有關內部部署 VMware vm 至 Azure 的災害復原的常見問題解答。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 05/30/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 59be8e0585f0bedcafc868ee42f5113509c9c4ef
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417768"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>有關常見問題 VMware 複寫至 Azure

這篇文章會回答您部署至 Azure 的災害復原的內部部署 VMware 虛擬機器 (Vm) 時，可能會想到的常見問題。

## <a name="general"></a>一般

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>我需要針對 VMware VM 災害復原？

[了解所涉及的元件](vmware-azure-architecture.md)中的 VMware 虛擬機器的災害復原。

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>可以使用 Site Recovery 將 VMware Vm 移轉至 Azure 嗎？

是。 除了使用 Site Recovery 設定 VMware vm 的完整的災害復原，您也可以使用 Site Recovery 將內部部署 VMware Vm 移轉至 Azure。 在此案例中，您可以將內部部署 VMware Vm 複寫至 Azure 儲存體。 然後，您會從內部部署環境容錯移轉至 Azure。 容錯移轉之後，您的應用程式和工作負載將可在 Azure VM 上使用及執行。 此處理序像是在設立完整的災害復原，不同之處在於在移轉您無法從 Azure 容錯回復。

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>我的 Azure 帳戶是否需要建立 VM 的權限？

如果您是訂用帳戶管理員，則會具有所需的複寫權限。 如果您不是系統管理員，您會需要權限，採取下列動作：

- 建立 Azure VM 中的資源群組和虛擬網路，您就會指定當您設定站台復原您。
- 寫入選取的儲存體帳戶或根據您設定的受控的磁碟。

[了解更多](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)需必要權限。

### <a name="what-applications-can-i-replicate"></a>可以複寫哪些應用程式？

您可以將複寫的任何應用程式或符合在 VMware VM 上執行的工作負載[複寫需求](vmware-physical-azure-support-matrix.md#replicated-machines)。

- Site Recovery 支援應用程式感知複寫，使應用程式可以容錯移轉和容錯回復到智慧型狀態。
- Site Recovery 整合了 Microsoft 應用程式，例如 SharePoint、 Exchange、 Dynamics、 SQL Server 和 Active Directory。 它也密切與產業龍頭，包括 Oracle、 SAP、 IBM 和 Red Hat。

[深入了解](site-recovery-workload.md) 工作負載保護。

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>可以在 Azure 上使用的客體 OS server 授權嗎？

是，可以使用 Microsoft 軟體保證客戶[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)節省授權成本的 Windows Server 電腦的移轉到 Azure，或使用 Azure 進行災害復原。

## <a name="security"></a>安全性

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Site Recovery 需要存取 VMware 伺服器？

Site Recovery 需要存取 VMware 伺服器才能：

- 執行 Site Recovery 組態伺服器將 VMware VM 設定。
- 自動探索需要複寫的 VM。

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Site Recovery 需要哪些 VMware 虛擬機器存取權？

- 若要複寫，VMware VM 必須安裝並執行 Site Recovery 行動服務。 您可以手動部署工具，或您可以指定 Site Recovery 將執行服務的推入安裝，當您啟用 VM 複寫。
- 在複寫期間，VM 會與 Site Recovery 通訊，如下所示：
    - Vm 與組態伺服器進行複寫管理的 HTTPS 連接埠 443 上進行通訊。
    - Vm 會將複寫資料傳送至 HTTPS 連接埠 9443 上的處理序伺服器。 （可以修改此設定）。
    - 如果您啟用多部 VM 一致性，則 VM 會透過連接埠 20004 互相通訊。

### <a name="is-replication-data-sent-to-site-recovery"></a>複寫資料是否會傳送至 Site Recovery？

否，Site Recovery 不會攔截複寫的資料，並不擁有任何關於您的 Vm 上執行的資訊。 VMware hypervisor 與 Azure 儲存體之間交換複寫資料。 Site Recovery 並不具有攔截該資料的能力。 只有協調複寫與容錯移轉所需的中繼資料會被傳送給 Site Recovery 服務。  

Site Recovery 已通過 ISO 27001:2013 和 27018、 HIPAA 和 DPA 認證。 它正在進行 SOC2 和 FedRAMP JAB 評定。

## <a name="pricing"></a>價格

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>如何計算 VMware 災害復原的近似費用？

使用[定價計算機](https://aka.ms/asr_pricing_calculator)評估成本，同時使用 Site Recovery。

成本詳細預估值，執行的部署規劃工具[VMware](https://aka.ms/siterecovery_deployment_planner)並用[成本估計報告](https://aka.ms/asr_DP_costreport)。

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>是否有任何差別成本複寫到儲存體，或直接為受控磁碟？

受控的磁碟收取儲存體帳戶的方式稍有不同。 [了解更多](https://azure.microsoft.com/pricing/details/managed-disks/)有關受控磁碟定價。

## <a name="mobility-service"></a>行動服務

### <a name="where-can-i-find-the-mobility-service-installers"></a>哪裡可以找到行動服務安裝程式？

安裝程式位於 %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository 資料夾，在組態伺服器上。

## <a name="how-do-i-install-the-mobility-service"></a>如何安裝行動服務？

在您想要複寫的每個 VM，安裝服務的其中一種方法：

- [推入安裝](vmware-physical-mobility-service-overview.md#push-installation)
- [手動安裝](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui)從 UI 或 PowerShell
- 部署使用的部署工具，例如[System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>受控磁碟

### <a name="where-does-site-recovery-replicate-data-to"></a>其中 Site Recovery 將複寫資料？

Site Recovery 會將內部部署 VMware Vm 和實體伺服器複寫至 Azure 中的受控磁碟。

- Site Recovery 處理伺服器會將複寫記錄檔寫入的目標區域中的快取儲存體帳戶。
- 這些記錄檔用來建立復原點上具有前置詞的 Azure 受控磁碟**asrseeddisk**。
- 容錯移轉時，您選取的復原點用來建立新的目標受控的磁碟。 此受控的磁碟已連結至 Azure 中的 VM。
- （前 3 月 2019) 先前複寫的儲存體帳戶的 Vm 不會受到影響。

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>我可以對儲存體帳戶複寫新的機器？

沒有。 從 3 月 2019，開始在 Azure 入口網站中，您可以複寫至 Azure 只受控磁碟。

儲存體帳戶的新 Vm 的複寫可只能藉由使用 PowerShell 或 REST API （版本 2018年-01-10 或 2016年-08-10）。

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>複寫至受控磁碟的優點有哪些？

[了解如何](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/)Site Recovery 可簡化使用受控磁碟的災害復原。

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>機器受到保護之後可以變更受控磁碟類型嗎？

是，您可以輕鬆地[受控磁碟的類型變更](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage)的進行中的複寫。 在變更之前的型別，請確認 URL 所產生的受控磁碟沒有共用的存取簽章：

1. 移至**受控磁碟**在 Azure 入口網站上的資源，並檢查是否有共用的存取簽章 URL 橫幅**概觀**刀鋒視窗。
1. 如果出現橫幅，選取它來取消進行中的匯出。
1. 接下來的幾分鐘內變更磁碟類型。 如果您變更受控磁碟類型，等候要由 Azure Site Recovery 產生全新的復原點。
1. 使用新的復原點的任何測試容錯移轉或容錯移轉，在未來。

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>我可以切換至非受控磁碟的複寫從受控磁碟嗎？

沒有。 切換從 managed 到 unmanaged 不受支援。

## <a name="replication"></a>複寫

### <a name="what-are-the-replicated-vm-requirements"></a>複寫的 VM 有何需求？

[了解更多](vmware-physical-azure-support-matrix.md#replicated-machines)VMware Vm 和實體伺服器的支援需求的相關。

### <a name="how-often-can-i-replicate-to-azure"></a>複寫到 Azure 的頻率為何？

從 VMware VM 到 Azure 的複寫會持續執行。

### <a name="can-i-extend-replication"></a>我可以延伸複寫嗎？

不支援延伸的或鏈結的複寫。 請在 [意見反應論壇](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)中提出這項功能的要求。

### <a name="can-i-do-an-offline-initial-replication"></a>是否可執行離線初始複寫？

不支援離線複寫。 請在 [意見反應論壇](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)中提出這項功能的要求。

### <a name="what-is-asrseeddisk"></a>什麼是 asrseeddisk？

每個來源磁碟，資料會複寫至 Azure 中的受控磁碟。 此磁碟具有的前置詞**asrseeddisk**。 它會儲存在來源磁碟及所有復原點快照集的複本。

### <a name="can-i-exclude-disks-from-replication"></a>可以從複寫排除磁碟嗎？

是，您可以排除磁碟。

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>具有動態磁碟的 Vm 可以複寫？

動態磁碟可以複寫。 作業系統磁碟必須是基本磁碟。

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>如果我使用複寫群組以獲取多 VM 一致性，我是否可將新的 VM 新增至現有的複寫群組？

是，您可以將新的 VM 新增至現有的複寫群組，為這些 VM 啟用複寫。 不過：

- 開始複寫之後，您無法將 VM 加入現有的複寫群組。
- 您無法建立複寫群組，針對現有的 Vm。

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>我是否可藉由新增磁碟或調整其大小來修改要複寫的 VM？

VMware 複寫至 Azure，您可以修改磁碟大小。 如果您想要新增新的磁碟，您必須新增磁碟，然後重新啟用 VM 的保護。

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>我可以移轉在內部部署機器到新的 vCenter Server 而不會影響進行中的複寫？

沒有。 VMware Vcenter 或移轉的變更會影響進行中的複寫。 設定 Site Recovery 與新的 vCenter Server 並再次啟用複寫的機器。

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>我可以複寫到已在其上設定之虛擬網路 （含 Azure 防火牆） 快取或目標儲存體帳戶？

否，Site Recovery 不支援複寫至 Azure 儲存體的虛擬網路上。

## <a name="component-upgrade"></a>元件升級

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>我的行動服務代理程式 」 或 「 組態伺服器的版本舊，且我升級失敗。 該怎麼辦？

Site Recovery 會遵循到 N-4 支援模型。 [了解更多](https://aka.ms/asr_support_statement)有關如何從非常舊版本升級。

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>其中找到 「 版本資訊和更新彙總套件，適用於 Azure Site Recovery？

[了解新的更新](site-recovery-whats-new.md)，並[取得彙總資訊](service-updates-how-to.md)。

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>哪裡可以找到升級至 Azure 的災害復原的資訊？

[了解如何升級](https://aka.ms/asr_vmware_upgrades)。

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>我是否需要重新啟動來源機器，為每個升級？

建議，但為每個升級並非必要的重新開機。 [深入了解](https://aka.ms/asr_vmware_upgrades)。

## <a name="configuration-server"></a>組態伺服器

### <a name="what-does-the-configuration-server-do"></a>組態伺服器有何功用？

組態伺服器會執行內部部署 Site Recovery 元件，包括：

- 組態伺服器本身。 伺服器會協調內部部署元件與 Azure 之間的通訊，以及管理資料複寫。
- 處理序伺服器，做為複寫閘道。 此伺服器：
    1. 接收複寫資料。
    2. 運用快取、壓縮和加密將該資料最佳化。
    3. 將資料傳送至 Azure 儲存體。
  處理序伺服器也會在 Vm 上的行動服務的推入安裝，並執行的內部部署 VMware Vm 自動探索。
- 主要目標伺服器，在從 Azure 容錯回復期間，處理複寫資料。

[深入了解](vmware-azure-architecture.md)組態伺服器元件和程序。

### <a name="where-do-i-set-up-the-configuration-server"></a>我應在何處設定組態伺服器？

您需要在組態伺服器的單一、 高可用性、 內部部署 VMware VM。 針對實體伺服器災害復原，請在實體機器上安裝組態伺服器。

### <a name="what-do-i-need-for-the-configuration-server"></a>我該怎麼做為組態伺服器？

請檢閱[必要條件](vmware-azure-deploy-configuration-server.md#prerequisites)。

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>是否可以手動設定組態伺服器，而不要使用範本？

我們建議您[建立組態伺服器 VM](vmware-azure-deploy-configuration-server.md)藉由開啟虛擬機器格式 (OVF) 範本的最新版本。 如果您無法使用範本 （例如，如果您沒有 VMware 伺服器的存取權），[下載](physical-azure-set-up-source.md)來自入口網站設定組態伺服器的安裝檔。

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>組態伺服器是否可複寫到多個區域？

沒有。 若要複寫多個區域，您需要每個區域中的組態伺服器。

### <a name="can-i-host-a-configuration-server-in-azure"></a>是否可將組態伺服器裝載在 Azure 中？

雖然可以，但執行組態伺服器的 Azure VM 必須與您的內部部署 VMware 基礎結構和 Vm 通訊。 這項通訊會增加延遲，並會影響進行中的複寫。

### <a name="how-do-i-update-the-configuration-server"></a>如何更新組態伺服器？

[了解](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)如何更新組態伺服器。

- 您可以在找到最新的更新資訊[Azure 更新頁面](https://azure.microsoft.com/updates/?product=site-recovery)。
- 您可以從入口網站下載最新版本。 或者，您可以下載最新版的組態伺服器從直接[Microsoft 下載中心](https://aka.ms/asrconfigurationserver)。
- 如果您的版本比目前的版本還舊的四個以上版本，請參閱[支援陳述式](https://aka.ms/asr_support_statement)的升級指導方針。

### <a name="should-i-back-up-the-configuration-server"></a>我應該備份組態伺服器？

建議對於組態伺服器進行定期排程的備份。

- 成功容錯回復，容錯回復的 VM 必須存在於組態伺服器資料庫。
- 組態伺服器必須執行並處於連線狀態。
- [了解更多](vmware-azure-manage-configuration-server.md)關於常見的組態伺服器管理工作。

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>在設定組態伺服器時，是否可以手動下載並安裝 MySQL？

是。 下載 MySQL，並將它放在 C:\Temp\ASRSetup 資料夾中。 然後，手動安裝。 當您設定組態伺服器 VM 時，如果您接受條款，MySQL 在 [下載並安裝]  中就會列示為 [已安裝]  。

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>是否可以不要下載 MySQL 而讓 Site Recovery 加以安裝？

是。 下載 MySQL 安裝程式，並將它放在 C:\Temp\ASRSetup 資料夾中。 當您設定組態伺服器 VM 時，接受條款，然後選取**下載並安裝**。 在入口網站會使用您新增以安裝 MySQL 安裝程式。

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>組態伺服器 VM 是否可用於任何其他位置？

沒有。 VM 僅適用於組態伺服器。

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>是否可以複製組態伺服器並將它用於協調流程？

沒有。 設定全新的組態伺服器，以避免註冊問題。

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>我可以變更已註冊組態伺服器的保存庫嗎？

沒有。 組態伺服器相關聯的保存庫之後，就無法變更。 [了解](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)有關使用不同的保存庫註冊組態伺服器。

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>可以使用相同的組態伺服器的 VMware Vm 和實體伺服器災害復原嗎？

是，但請注意該實體機器可以容錯回復到 VMware VM。

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>哪裡可以下載組態伺服器的複雜密碼？

[了解](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)如何下載複雜密碼。

### <a name="where-can-i-download-vault-registration-keys"></a>哪裡可以下載保存庫註冊金鑰？

在 復原服務保存庫中，選取**組態伺服器**中**Site Recovery 基礎結構** > **管理**。 然後，在**伺服器**，選取**下載註冊金鑰**下載保存庫認證檔。

## <a name="process-server"></a>處理序伺服器

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>為什麼我無法啟用複寫時，請選取處理序伺服器？

在版本 9.24 和更新版本的目前顯示的更新[處理序伺服器，當您啟用複寫時的健全狀況](vmware-azure-enable-replication.md#enable-replication)。 若要避免節流處理序伺服器，並將狀況不良的處理序伺服器的使用降至最低，可協助這項功能。

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>如何更新到版本 9.24 或更新版本的正確的健全狀況資訊的處理序伺服器？

開頭[版本 9.24](service-updates-how-to.md#links-to-currently-supported-update-rollups)，已加入更多警示指出健全狀況的處理序伺服器。 [更新您的 Site Recovery 元件 9.24 或更新版本的版本](service-updates-how-to.md#links-to-currently-supported-update-rollups) 以便產生所有警示。

## <a name="failover-and-failback"></a>容錯移轉和容錯回復

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>可以使用內部部署處理序伺服器進行容錯回復？

我們強烈建議處理序伺服器在 Azure 中建立的容錯回復目的，以避免發生資料傳輸的延遲。 此外，如果來源 Vm 網路以在組態伺服器的 Azure 公開網路上時，務必要使用在 Azure 中建立的容錯回復處理序伺服器。

### <a name="can-i-keep-the-ip-address-on-failover"></a>可以保留的 IP 位址在容錯移轉嗎？

是，您可以保留的 IP 位址在容錯移轉。 請確定您指定中的目標 IP 位址**計算與網路**VM 容錯移轉前的設定。 此外，關閉機器在容錯移轉，以避免在容錯回復期間的 IP 位址衝突的時間。

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>我可以變更的目標 VM 大小或容錯移轉前的 VM 類型嗎？

是，您可以在容錯移轉之前隨時變更型別或 VM 的大小。 在入口網站中，使用**計算與網路**設定複寫的 VM。

### <a name="how-far-back-can-i-recover"></a>最多可復原至多久之前？

Vmware 到 Azure，您可以使用最舊的復原點為 72 小時。

### <a name="how-do-i-access-azure-vms-after-failover"></a>在容錯移轉之後如何存取 Azure VM？

容錯移轉之後，您可以在透過安全的網際網路連線、 透過站對站 VPN 或透過 Azure ExpressRoute 存取 Azure Vm。 若要連接，您必須準備幾件事。 [深入了解](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。

### <a name="is-failed-over-data-resilient"></a>已容錯移轉資料復原嗎？

Azure 是針對復原能力而設計的。 Site Recovery 是設計成可容錯移轉至次要 Azure 資料中心，所需的 Azure 服務等級協定 (SLA)。 容錯移轉時，我們會確保您的中繼資料和保存庫都保留在您的保存庫選擇的相同地理區域中。

### <a name="is-failover-automatic"></a>容錯移轉是自動發生的嗎？

[容錯移轉](site-recovery-failover.md)不會自動執行。 您可以在入口網站中，建立單一選取啟動容錯移轉，或者您可以使用[PowerShell](/powershell/module/az.recoveryservices)觸發容錯移轉。

### <a name="can-i-fail-back-to-a-different-location"></a>是否可容錯回復至不同的位置？

是。 如果您容錯移轉至 Azure，您可以容錯回復至不同的位置若原始無法使用。 [深入了解](concepts-types-of-failback.md#alternate-location-recovery-alr)。

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>為什麼要容錯回復私人對等互連需要 VPN 或 ExpressRoute？

當您從 Azure 容錯回復時，資料從 Azure 複製回您的內部部署 VM，而且需要私用存取。

### <a name="can-i-resize-the-azure-vm-after-failover"></a>容錯移轉後我是否可調整 Azure VM 的大小？

否，您無法在容錯移轉之後變更的大小或類型的目標 VM。

## <a name="automation-and-scripting"></a>自動化和指令碼

### <a name="can-i-set-up-replication-with-scripting"></a>是否可以使用指令碼設定複寫？

是。 您可以使用 Rest API、 PowerShell 或 Azure SDK 自動化 Site Recovery 的工作流程。 [深入了解](vmware-azure-disaster-recovery-powershell.md)。

## <a name="performance-and-capacity"></a>效能與容量

### <a name="can-i-throttle-replication-bandwidth"></a>是否可進行複寫頻寬節流？

是。 [深入了解](site-recovery-plan-capacity-vmware.md)。

## <a name="next-steps"></a>後續步驟

- [檢閱](vmware-physical-azure-support-matrix.md)支援需求。
- [設定](vmware-azure-tutorial.md) VMware 到 Azure 的複寫。
