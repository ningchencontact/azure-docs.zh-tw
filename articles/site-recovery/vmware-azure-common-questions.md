---
title: 常見問題 - 使用 Azure Site Recovery 進行從 VMware 至 Azure 的災害復原 | Microsoft Docs
description: 本文摘錄了使用 Azure Site Recovery 設定從內部部署 VMware VM 至 Azure 之災害復原時的常見問題
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 03/21/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 4237e259d1ba9cb826d89eba212b6931d933626d
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051915"
---
# <a name="common-questions---vmware-to-azure-replication"></a>常見問題 - VMware 到 Azure 的複寫

本文將解答部署從內部部署 VMware VM 至 Azure 之災害復原時的常見問題。 如果您在閱讀本文後有問題，請將問題張貼在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)。

## <a name="general"></a>一般
### <a name="how-is-site-recovery-priced"></a>Site Recovery 是如何定價的？
請檢閱[Azure Site Recovery 定價](https://azure.microsoft.com/pricing/details/site-recovery/)詳細資料。

### <a name="how-do-i-pay-for-azure-vms"></a>如何支付 Azure VM 費用？
在複寫期間，資料會複寫至 Azure 儲存體，您無須為任何 VM 變更付費。 當您容錯移轉到 Azure 時，Site Recovery 會自動建立 Azure IaaS 虛擬機器。 之後就會針對您在 Azure 中取用的運算資源進行計費。

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>VMware 到 Azure 的複寫有何功能？
- **災害復原**：您可以設定完整的災害復原。 在此案例中，您會將內部部署 VMware VM 複寫至 Azure 儲存體。 其後，如果您的內部部署基礎結構無法使用，您即可進行容錯移轉至 Azure。 當您執行容錯移轉時，系統會使用複寫的資料建立 Azure VM。 您可以在 Azure VM 上存取應用程式和工作負載，直到您的內部部署資料中心恢復可用性為止。 接著，您可以從 Azure 容錯回復至內部部署網站。
- **移轉**：您可以使用 Site Recovery 將內部部署 VMware VM 移轉至 Azure。 在此案例中，您會將內部部署 VMware VM 複寫至 Azure 儲存體。 然後，您會從內部部署環境容錯移轉至 Azure。 容錯移轉之後，您的應用程式和工作負載將可在 Azure VM 上使用及執行。

## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>在 Azure 中需要什麼？
您需要 Azure 訂用帳戶、 復原服務保存庫、 快取儲存體帳戶、 受控的磁碟和虛擬網路。 保存庫中，快取儲存體帳戶、 受管理磁碟和網路必須位於相同區域中。

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>我的 Azure 帳戶是否需要建立 VM 的權限？
如果您是訂用帳戶管理員，則會具有所需的複寫權限。 如果您不這樣做，您會需要建立 Azure VM 中的資源群組和您指定當您設定 Site Recovery 及寫入選取的儲存體帳戶的權限或受控磁碟會根據您設定的虛擬網路的權限。 [深入了解](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)。

### <a name="can-i-use-guest-os-server-license-on-azure"></a>可以在 Azure 上使用客體 OS 伺服器授權嗎？
是，「微軟軟體保證」客戶可以使用 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，節省遷移至 Azure 之 **Windows Server 電腦**的授權成本，或使用 Azure 進行災害復原。

## <a name="pricing"></a>價格

### <a name="how-are-licensing-charges-handled-during-replication-after-failover"></a>容錯移轉之後，在複寫期間系統會如何處理授權費用？

如需詳細資訊，請參閱[此處](https://aka.ms/asr_pricing_FAQ)有關授權的常見問題集。

### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>如何計算使用 Site Recovery 期間的大約費用？

您可以使用[定價計算機](https://aka.ms/asr_pricing_calculator)來估計使用 Azure Site Recovery 時的成本。 預估成本的詳細值，執行部署規劃工具 (https://aka.ms/siterecovery_deployment_planner)及分析[成本估計報告](https://aka.ms/asr_DP_costreport)。

### <a name="is-there-any-difference-in-cost-when-i-replicate-directly-to-managed-disk"></a>是否有任何差異的成本時我將直接複寫到受控磁碟？

受控的磁碟會被收取稍有不同的儲存體帳戶。 請參閱下列範例的來源磁碟大小 100 GiB。 此範例旨在差異儲存體成本。 這項成本不包含快照集、 快取儲存體和交易成本。

* Vs 的標準儲存體帳戶。標準的 HDD 受控磁碟

    - **佈建儲存體磁碟，Azure Site recovery**:S10
    - **標準儲存體帳戶收取使用磁碟區**： 每月美金 5
    - **收取已佈建的磁碟區上的標準受控的磁碟**： 每月美金 5.89

* Vs premium 儲存體帳戶。進階 SSD 受控的磁碟 
    - **佈建儲存體磁碟，Azure Site recovery**:P10
    - **進階儲存體帳戶收取已佈建的磁碟區上**： 每月美金 17.92
    - **進階受控的磁碟收取已佈建的磁碟區上**： 每月美金 17.92

深入了解[詳細的受控磁碟定價](https://azure.microsoft.com/pricing/details/managed-disks/)。

### <a name="do-i-incur-additional-charges-for-cache-storage-account-with-managed-disks"></a>我是否會負擔額外費用快取儲存體帳戶具有受控磁碟？

否，您不會產生額外的費用，快取。 快取是一定的 VMware 至 Azure 架構的一部分。 當您複寫至標準儲存體帳戶時，此快取儲存體就會是相同的目標儲存體帳戶的一部分。

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>我已經使用 Azure Site Recovery 一個多月了。 我的每個受保護的執行個體是否前 31 天仍然都免費？

是，不論您已經使用 Azure Site Recovery 多久。 每個受保護的執行個體在前 31 天皆無 Azure Site Recovery 費用。 例如，如果您過去 6 個月保護了 10 個執行個體，而現在您將第 11 個執行個體連線到 Azure Site Recovery，則第 11 個執行個體的前 31 天不會產生任何 Azure Site Recovery 費用。 而前 10 個執行個體則因為受保護已超過 31 天，所以會繼續產生 Azure Site Recovery 費用。

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>在這前 31 天，我是否須負擔任何其他 Azure 費用？

是，即使受保護的執行個體前 31 天的 Azure Site Recovery 免費，您仍有可能需要負擔 Azure 儲存體、儲存體交易與資料傳輸的費用。 復原的虛擬機器也可能會產生 Azure 計算費用。

### <a name="what-charges-do-i-incur-while-using-azure-site-recovery"></a>使用 Azure Site Recovery 時有哪些費用？

如需詳細資訊，請參閱[成本相關常見問題集](https://aka.ms/asr_pricing_FAQ)。

### <a name="is-there-a-cost-associated-to-perform-dr-drillstest-failover"></a>執行 DR 演練/測試容錯移轉是否有相關成本？

DR 演練沒有個別的成本。 在測試容錯移轉後建立虛擬機器之後，會有計算費用。

## <a name="azure-site-recovery-components-upgrade"></a>Azure Site Recovery 元件升級

### <a name="my-mobility-agentconfiguration-serverprocess-server-version-is-very-old-and-my-upgrade-has-failed-how-should-i-upgrade-to-latest-version"></a>我的行動代理程式/設定伺服器/處理序伺服器版本非常舊，而我的升級失敗。 我要如何升級至最新版本？

Azure Site Recovery 會遵循 N-4 支援模型。 請參閱我們的[支援聲明](https://aka.ms/asr_support_statement)，以了解有關如何從非常舊的版本升級的詳細資料。

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-azure-site-recovery"></a>哪裡可以找到 Azure Site Recovery 的版本資訊/更新彙總套件？

如需版本資訊的詳細資訊，請參閱[文件](https://aka.ms/asr_update_rollups)。 您可以在每個更新彙總中找到個別元件的安裝連結。

### <a name="how-should-i-upgrade-site-recovery-components-for-on-premises-vmware-or-physical-site-to-azure"></a>如何將內部部署 VMware 或實體站台的 Site Recovery 元件升級至 Azure？

請參閱[此處](https://aka.ms/asr_vmware_upgrades)所提供的指導方針升級您的元件。

## <a name="is-reboot-of-source-machine-mandatory-for-each-upgrade"></a>每次升級都需要重新啟動來源機器嗎？

雖然建議，但並不是每次升級都必須這麼做。 如需明確的指導方針，請參閱[此處](https://aka.ms/asr_vmware_upgrades)。

## <a name="on-premises"></a>內部部署

### <a name="what-do-i-need-on-premises"></a>我的內部部署環境需要什麼？

在內部部署上，您需要：
- 安裝在單一 VMware VM 上的 Site Recovery 元件。
- 至少有一個 ESXi 主機的 VMware 基礎結構，此外也建議要有 vCenter 伺服器。
- 一或多個可複寫的 VMware VM。

[深入了解](vmware-azure-architecture.md) VMware 到 Azure 的架構。

可使用下列方式來部署內部部署組態伺服器：

- 我們建議您使用 OVA 範本並搭配預先安裝組態伺服器的方式，將組態伺服器部署為 VMware VM。
- 如果您基於某些原因無法使用範本，則可以手動設定組態伺服器。 [深入了解](physical-azure-disaster-recovery.md#set-up-the-source-environment)。



### <a name="where-do-on-premises-vms-replicate-to"></a>內部部署 VM 會複寫到何處？
資料會複寫到 Azure 儲存體。 當您執行容錯移轉時，Site Recovery 會自動從儲存體帳戶中建立 Azure Vm，或受控磁碟上，根據您的組態。

## <a name="replication"></a>複寫

### <a name="what-applications-can-i-replicate"></a>可以複寫哪些應用程式？
您可以複寫符合[複寫需求](vmware-physical-azure-support-matrix.md##replicated-machines)的 VMware VM 上執行的任何應用程式或工作負載。 Site Recovery 支援應用程式感知複寫，可讓應用程式容錯移轉及容錯回復至智慧型狀態。 Site Recovery 除了與 Microsoft 應用程式 (例如 SharePoint、Exchange、Dynamics、SQL Server 及 Active Directory) 整合之外，還與產業龍頭 (包括 Oracle、SAP、IBM 及 Red Hat) 密切合作。 [深入了解](site-recovery-workload.md) 工作負載保護。

### <a name="can-i-protect-a-virtual-machine-that-has-docker-disk-configuration"></a>是否可以保護有 Docker 磁碟設定的虛擬機器？

否，這是不支援的案例。

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>是否可透過站對站 VPN 複寫至 Azure？
Site Recovery 可透過公用端點將資料從內部部署環境複寫至 Azure 儲存體，或使用 ExpressRoute 公用對等互連進行複寫。 不支援透過站對站 VPN 網路的複寫。

### <a name="can-i-replicate-to-azure-with-expressroute"></a>是否可透過 ExpressRoute 複寫至 Azure？
是的，ExpressRoute 可用來將 VM 複寫至 Azure。 Site Recovery 會透過公用端點，將資料複寫至 Azure 儲存體。 您必須設定[公用對等互連](../expressroute/expressroute-circuit-peerings.md#publicpeering)或 [Microsoft 對等互連](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)，以使用 Site Recovery 複寫的 ExpressRoute。 Microsoft 對等互連是建議用於複寫的路由網域。 請確認也符合[網路需求](vmware-azure-configuration-server-requirements.md#network-requirements)以進行複寫。 VM 容錯移轉至 Azure 虛擬網路之後，您可以使用[私人對等互連](../expressroute/expressroute-circuit-peerings.md#privatepeering)加以存取。

### <a name="how-can-i-change-storage-account-after-machine-is-protected"></a>虛擬機器受保護之後如何變更儲存體帳戶？

您需要停用和啟用升級或降級的儲存體帳戶類型的複寫。

### <a name="can-i-replicate-to-storage-accounts-for-new-machine"></a>我可以複寫到新的機器的儲存體帳戶？

否，從 3 月 19'，您可以複寫至 Azure 上的受控磁碟從入口網站。 複寫至新的機器的儲存體帳戶才可透過 REST API 和 Powershell。 使用 API 2016-08-10 或 2018年-01-10 版本複寫到儲存體帳戶。

### <a name="what-are-the-benefits-in-replicating-to-managed-disks"></a>複寫至受控磁碟的優點有哪些？

閱讀有關[Azure Site Recovery 可簡化使用受控磁碟的災害復原](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/)。

### <a name="how-can-i-change-managed-disk-type-after-machine-is-protected"></a>機器受到保護之後，如何變更受控磁碟類型？

是，您可以輕鬆地變更受控磁碟的類型。 [深入了解](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage)。 不過，一旦變更受控的磁碟類型，請確定您等候最新的復原點，如果您要執行測試容錯移轉或容錯移轉後此活動會產生。

### <a name="can-i-switch-the-replication-from-managed-disks-to-unmanaged-disks"></a>我可以切換至非受控磁碟的複寫從受控磁碟嗎？

否，從切換 managed 到 unmanaged 不支援。

### <a name="why-cant-i-replicate-over-vpn"></a>為何我無法透過 VPN 進行複寫？

當您複寫至 Azure 時，複寫流量到達 「 Azure 儲存體的公用端點，因此您只能複寫透過公用網際網路透過 ExpressRoute （公用對等互連），和 VPN 無法運作。

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>我可以使用複寫 Riverbed SteelHeads 嗎？

我們的合作夥伴 Riverbed，在使用 Azure Site Recovery 提供詳細的指導方針。 請參閱其[解決方案指南](https://community.riverbed.com/s/article/DOC-4627)。

### <a name="what-are-the-replicated-vm-requirements"></a>複寫的 VM 有何需求？

若要進行複寫，VMware VM 必須執行支援的作業系統。 此外，VM 必須符合 Azure VM 的需求。 請在[支援矩陣](vmware-physical-azure-support-matrix.md##replicated-machines)中深入了解相關資訊。

### <a name="how-often-can-i-replicate-to-azure"></a>複寫到 Azure 的頻率為何？
從 VMware VM 到 Azure 的複寫會持續執行。

### <a name="can-i-retain-the-ip-address-on-failover"></a>容錯移轉時是否可以保留 IP 位址？
是，您可以在容錯轉時保留 IP 位址。 進行容錯移轉之前，請務必在 [計算與網路] 刀鋒視窗中提及目標 IP 位址。 此外，請務必在容錯移轉時將虛擬機器關機，以避免在容錯移轉時發生 IP 衝突。

### <a name="can-i-extend-replication"></a>我可以延伸複寫嗎？
不支援延伸的或鏈結的複寫。 請在 [意見反應論壇](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)中提出這項功能的要求。

### <a name="can-i-do-an-offline-initial-replication"></a>是否可執行離線初始複寫？
不支援此做法。 請在 [意見反應論壇](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)中提出這項功能的要求。

### <a name="can-i-exclude-disks"></a>是否可排除磁碟嗎？
是的，您可以將磁碟排除於複寫外。

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>我可以變更的目標 VM 大小或容錯移轉前的 VM 類型嗎？
是，您可以變更類型或大小的 VM 容錯移轉之前隨時前往 計算和網路設定，從入口網站的複寫項目。

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>是否可使用動態磁碟來複寫 VM？
動態磁碟可以複寫。 作業系統磁碟必須是基本磁碟。

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>如果我使用複寫群組以獲取多 VM 一致性，我是否可將新的 VM 新增至現有的複寫群組？
是，您可以將新的 VM 新增至現有的複寫群組，為這些 VM 啟用複寫。 您無法在複寫起始後將 VM 新增至現有的複寫群組，且無法為現有的 VM 建立複寫群組。

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>我是否可藉由新增磁碟或調整其大小來修改要複寫的 VM？

對於從 VMware 到 Azure 的複寫，您可以修改磁碟大小。 如果您想要新增磁碟，您必須新增磁碟，並重新啟用對 VM 的保護。

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-without-impacting-ongoing-replication"></a>我可以移轉到新的 Vcenter 的內部部署機器上不會影響進行中的複寫？
否，變更 Vcenter 或移轉會影響進行中的複寫。 您要設定 Azure Site Recovery 與新的 Vcenter 和對機器啟用複寫。

### <a name="can-i-replicate-to-cachetarget-storage-account-which-has-a-vnet-with-azure-storage-firewalls-configured-on-it"></a>我是否可以複寫至設有 Vnet (含 Azure 儲存體防火牆) 的快取/目標儲存體帳戶？
否，Azure Site Recovery 不支援複寫至 Vnet 上的儲存體。

## <a name="configuration-server"></a>組態伺服器

### <a name="what-does-the-configuration-server-do"></a>組態伺服器有何功用？
組態伺服器會執行內部部署 Site Recovery 元件，包括：
- 設定伺服器，會協調內部部署與 Azure 之間的通訊，以及管理資料複寫。
- 處理序伺服器，可作為複寫閘道。 它會接收複寫資料、使用快取、壓縮和加密加以最佳化，然後將其傳送至 Azure 儲存體。處理序伺服器也會在您要複寫的 VM 上安裝行動服務，並且在內部部署 VMware VM 上執行自動探索。
- 主要目標伺服器，會在從 Azure 容錯回復期間處理複寫資料。

[深入了解](vmware-azure-architecture.md)組態伺服器元件和程序。

### <a name="where-do-i-set-up-the-configuration-server"></a>我應在何處設定組態伺服器？
您需要具有高可用性的單一內部部署 VMware VM，供組態伺服器使用。

### <a name="what-are-the-requirements-for-the-configuration-server"></a>組態伺服器有何需求？

請檢閱[必要條件](vmware-azure-deploy-configuration-server.md#prerequisites)。

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>是否可以手動設定組態伺服器，而不要使用範本？
建議您使用最新版的 OVF 範本來[建立組態伺服器 VM](vmware-azure-deploy-configuration-server.md)。 如果您因故無法這麼做 (例如您沒有 VMware 伺服器的存取權)，您可以從入口網站[下載統一安裝檔案](physical-azure-set-up-source.md)，並在 VM 上執行。

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>組態伺服器是否可複寫到多個區域？
沒有。 若要這麼做，您必須在每個區域中設定組態伺服器。

### <a name="can-i-host-a-configuration-server-in-azure"></a>是否可將組態伺服器裝載在 Azure 中？
雖然可行，但執行組態伺服器的 Azure VM 將需要與您的內部部署 VMware 基礎結構和 VM 通訊。 這會加入延遲，並影響進行中的複寫。

### <a name="how-do-i-update-the-configuration-server"></a>如何更新組態伺服器？
[深入了解](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)如何更新組態伺服器。 您可以在 [Azure 更新頁面](https://azure.microsoft.com/updates/?product=site-recovery)中找到最新的更新資訊。 您也可以直接從 [Microsoft 下載中心](https://aka.ms/asrconfigurationserver)下載最新版的設定伺服器。 如果您的版本比目前版本早 4 個版本，請參閱我們的[支援聲明](https://aka.ms/asr_support_statement)以取得升級指導方針。

### <a name="should-i-backup-the-deployed-configuration-server"></a>我是否應該備份已部署的組態伺服器？
建議對於組態伺服器進行定期排程的備份。 為了成功容錯回復，進行容錯回復的虛擬機器必須存在於組態伺服器資料庫，而且組態伺服器必須執行中且處於連線狀態。 您可在[此處](vmware-azure-manage-configuration-server.md)深入了解常見組態伺服器管理工作。

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>在設定組態伺服器時，是否可以手動下載並安裝 MySQL？

是。 請下載 MySQL 並將它放在 **C:\Temp\ASRSetup** 資料夾中。 然後再手動加以安裝。 當您設定組態伺服器 VM 時，如果您接受條款，MySQL 在 [下載並安裝] 中就會列示為 [已安裝]。

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>是否可以不要下載 MySQL 而讓 Site Recovery 加以安裝？

是。 請下載 MySQL 安裝程式，並將它放在 **C:\Temp\ASRSetup** 資料夾中。  當您設定組態伺服器 VM 時，只要接受條款，再按一下 [下載並安裝]，入口網站就會使用您新增的安裝程式來安裝 MySQL。
 
### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>組態伺服器 VM 是否可用於任何其他位置？
否，VM 只能用於組態伺服器上。 

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>是否可以複製組態伺服器並將它用於協調流程？
否，您應該設定全新的組態伺服器以避免註冊問題。

### <a name="can-i-change-the-vault-registered-in-the-configuration-server"></a>是否可變更組態伺服器中註冊的保存庫？
沒有。 保存庫在組態伺服器中註冊後，即無法變更。 檢閱[這篇文章](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)了解重新註冊步驟。

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>是否可將相同的組態伺服器用於 VMware VM 和實體伺服器的災害復原
是，但請注意，實體機器只能容錯回復到 VMware VM。

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>哪裡可以下載組態伺服器的複雜密碼？
請[檢閱此文](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)，以了解如何下載複雜密碼。

### <a name="where-can-i-download-vault-registration-keys"></a>哪裡可以下載保存庫註冊金鑰？

在 [復原服務保存庫] 的 [管理] > [Site Recovery Infrastructure] \(Site Recovery 基礎結構\) > [設定伺服器] 中。 在 [伺服器] 中，選取 [下載註冊金鑰] 以下載保存庫認證檔案。



## <a name="mobility-service"></a>行動服務

### <a name="where-can-i-find-the-mobility-service-installers"></a>哪裡可以找到行動服務安裝程式？
安裝程式存放在組態伺服器的 **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** 資料夾中。

## <a name="how-do-i-install-the-mobility-service"></a>如何安裝行動服務？
您可以使用[推送安裝](vmware-physical-mobility-service-overview.md#push-installation)，或是從 UI 或 PowerShell 進行[手動安裝](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui)，在您想要複寫的每個 VM 上安裝。 或者，也可以使用 [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md) 之類的部署工具來部署。



## <a name="security"></a>安全性

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Site Recovery 需要有 VMware 伺服器的哪些存取權？
Site Recovery 需要存取 VMware 伺服器才能：

- 設定執行組態伺服器與其他內部部署 Site Recovery 元件的 VMware VM。 [深入了解](vmware-azure-deploy-configuration-server.md)
- 自動探索需要複寫的 VM。 了解如何準備帳戶以進行自動探索。 [深入了解](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Site Recovery 需要有 VMware VM 的哪些存取權？

- VMware VM 必須已安裝並執行 Site Recovery 行動服務，才能進行複寫。 您可以手動部署工具，或指定 Site Recovery 應在您為 VM 啟用複寫時執行服務的推送安裝。 若要進行推送安裝，Site Recovery 必須要有可用來安裝服務元件的帳戶。 [深入了解](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation)。 處理序伺服器 (依預設在組態伺服器上執行) 會執行此安裝。 [深入了解](vmware-azure-install-mobility-service.md)行動服務安裝。
- 在複寫期間，VM 會與 Site Recovery 通訊，如下所示：
    - VM 會在連接埠 HTTPS 443 上與組態伺服器進行通訊，以進行複寫管理。
    - VM 會透過連接埠 HTTPS 9443 (可修改) 將複寫資料傳送到處理序伺服器。
    - 如果您啟用多部 VM 一致性，則 VM 會透過連接埠 20004 互相通訊。


### <a name="is-replication-data-sent-to-site-recovery"></a>複寫資料是否會傳送至 Site Recovery？
否，Site Recovery 不會攔截複寫的資料，也不會擁有任何關於您的 VM 上執行哪些項目的資訊。 複寫資料會在 VMware Hypervisor 與 Azure 儲存體之間交換。 Site Recovery 並不具有攔截該資料的能力。 只有協調複寫與容錯移轉所需的中繼資料會被傳送給 Site Recovery 服務。  

Site Recovery 已通過 ISO 27001:2013、27018、HIPAA、DPA 認證，並且正在進行 SOC2 和 FedRAMP JAB 評定程序。

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>是否可將內部部署中繼資料保留在地理區域內？
是。 當您在某個區域中建立保存庫時，我們會確保 Site Recovery 所使用的所有中繼資料都會保留在該區域的地理界限內。

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery 會將複寫加密嗎？
是，傳輸中加密和 [Azure 中的加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)均受支援。


## <a name="failover-and-failback"></a>容錯移轉和容錯回復
### <a name="can-i-use-the-process-server-at-on-premises-for-failback"></a>是否可用於處理序伺服器在內部部署容錯回復？
強烈建議處理序伺服器在 Azure 中建立的容錯回復目的來避免發生資料傳輸的延遲。 此外，如果您將來源 Vm 網路以在組態伺服器的 Azure 公開網路，然後務必要使用在 Azure 中建立的容錯回復處理序伺服器。

### <a name="how-far-back-can-i-recover"></a>最多可復原至多久之前？
針對 VMware 到 Azure 的復原，您可以使用的最舊復原點為 72 小時。

### <a name="how-do-i-access-azure-vms-after-failover"></a>在容錯移轉之後如何存取 Azure VM？
容錯移轉後，您可以透過安全的網際網路連線、透過站對站 VPN 或透過 Azure ExpressRoute 存取 Azure VM。 您必須做好一些準備才能連線。 [深入了解](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>容錯移轉後的資料可復原嗎？
Azure 是針對復原能力而設計的。 Site Recovery 設計成可根據 Azure SLA 容錯移轉至次要 Azure 資料中心。 當容錯移轉發生時，我們會確保您的中繼資料和保存庫都保留在您為保存庫選擇的相同地理區域中。

### <a name="is-failover-automatic"></a>容錯移轉是自動發生的嗎？
[容錯移轉](site-recovery-failover.md)不會自動執行。 您起始容錯移轉，在入口網站中，按一下，或您可以使用[PowerShell](/powershell/module/az.recoveryservices)觸發容錯移轉。

### <a name="can-i-fail-back-to-a-different-location"></a>是否可容錯回復至不同的位置？
是的，在容錯移轉至 Azure 之後，如果原始位置無法使用，您可以容錯回復至不同的位置。 [深入了解](concepts-types-of-failback.md#alternate-location-recovery-alr)。

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>為何需要以 VPN 或 ExpressRoute 進行容錯回復？
您從 Azure 容錯回復時，Azure 中的資料會重新複製到內部部署 VM，因此需要私用存取。

### <a name="can-i-resize-the-azure-vm-after-failover"></a>容錯移轉後我是否可調整 Azure VM 的大小？
否，在容錯移轉之後，您就無法變更目標 VM 的大小或類型。


## <a name="automation-and-scripting"></a>自動化和指令碼

### <a name="can-i-set-up-replication-with-scripting"></a>是否可以使用指令碼設定複寫？
是。 您可以使用 Rest API、PowerShell 或 Azure SDK 將 Site Recovery 的工作流程自動化。[深入了解](vmware-azure-disaster-recovery-powershell.md)。

## <a name="performance-and-capacity"></a>效能與容量
### <a name="can-i-throttle-replication-bandwidth"></a>是否可進行複寫頻寬節流？
是。 [深入了解](site-recovery-plan-capacity-vmware.md)。


## <a name="next-steps"></a>後續步驟
* [檢閱](vmware-physical-azure-support-matrix.md)支援需求。
* [設定](vmware-azure-tutorial.md) VMware 到 Azure 的複寫。
