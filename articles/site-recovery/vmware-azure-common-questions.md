---
title: 常見問題 - 使用 Azure Site Recovery 進行從 VMware 至 Azure 的災害復原 | Microsoft Docs
description: 本文摘要說明一般問題 fir 的內部部署 VMware Vm 至 Azure 災害復原使用 Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 04/18/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: d0e39f9e24b3c486eccd71eb1c19823cfd33391a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004766"
---
# <a name="common-questions---vmware-to-azure-replication"></a>常見問題 - VMware 到 Azure 的複寫

當您部署內部部署 VMware Vm 的災害復原至 Azure，本文提供常見問題的解答。 

## <a name="general"></a>一般 
### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>我需要針對 VMware VM 災害復原？

[深入了解](vmware-azure-architecture.md)VMware Vm 的災害復原相關的元件。 

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>可以使用 Site Recovery 將 VMware Vm 移轉至 Azure 嗎？

是，除了使用 Site Recovery 設定 VMware vm 的完整的災害復原，您也可以使用 Site Recovery 將內部部署 VMware Vm 移轉至 Azure。 在此案例中，您會將內部部署 VMware VM 複寫至 Azure 儲存體。 然後，您會從內部部署環境容錯移轉至 Azure。 容錯移轉之後，您的應用程式和工作負載將可在 Azure VM 上使用及執行。 處理程序是類似於設定完整的災害復原，不同之處在於在移轉您無法從 Azure 容錯回復。


### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>我的 Azure 帳戶是否需要建立 VM 的權限？
如果您是訂用帳戶管理員，則會具有所需的複寫權限。 如果您不這樣做，您會需要建立 Azure VM 中的資源群組和您指定當您設定 Site Recovery 及寫入選取的儲存體帳戶的權限或受控磁碟會根據您設定的虛擬網路的權限。 [深入了解](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)。

### <a name="what-applications-can-i-replicate"></a>可以複寫哪些應用程式？
您可以複寫符合[複寫需求](vmware-physical-azure-support-matrix.md##replicated-machines)的 VMware VM 上執行的任何應用程式或工作負載。
- Site Recovery 支援應用程式感知複寫，可讓應用程式容錯移轉及容錯回復至智慧型狀態。
- Site Recovery 除了與 Microsoft 應用程式 (例如 SharePoint、Exchange、Dynamics、SQL Server 及 Active Directory) 整合之外，還與產業龍頭 (包括 Oracle、SAP、IBM 及 Red Hat) 密切合作。
- [深入了解](site-recovery-workload.md) 工作負載保護。

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>可以在 Azure 上使用的客體 OS server 授權嗎？
是，「微軟軟體保證」客戶可以使用 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，節省遷移至 Azure 之 **Windows Server 電腦**的授權成本，或使用 Azure 進行災害復原。

## <a name="security"></a>安全性

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Site Recovery 需要有 VMware 伺服器的哪些存取權？
Site Recovery 需要存取 VMware 伺服器才能：

- 執行 Site Recovery 設定伺服器，將 VMware VM 設定
- 自動探索需要複寫的 VM。 


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Site Recovery 需要有 VMware VM 的哪些存取權？

- VMware VM 必須已安裝並執行 Site Recovery 行動服務，才能進行複寫。 您可以手動部署工具，或指定 Site Recovery 應在您為 VM 啟用複寫時執行服務的推送安裝。 
- 在複寫期間，VM 會與 Site Recovery 通訊，如下所示：
    - VM 會在連接埠 HTTPS 443 上與組態伺服器進行通訊，以進行複寫管理。
    - VM 會透過連接埠 HTTPS 9443 (可修改) 將複寫資料傳送到處理序伺服器。
    - 如果您啟用多部 VM 一致性，則 VM 會透過連接埠 20004 互相通訊。


### <a name="is-replication-data-sent-to-site-recovery"></a>複寫資料是否會傳送至 Site Recovery？
否，Site Recovery 不會攔截複寫的資料，也不會擁有任何關於您的 VM 上執行哪些項目的資訊。 複寫資料會在 VMware Hypervisor 與 Azure 儲存體之間交換。 Site Recovery 並不具有攔截該資料的能力。 只有協調複寫與容錯移轉所需的中繼資料會被傳送給 Site Recovery 服務。  

Site Recovery 已通過 ISO 27001:2013、27018、HIPAA、DPA 認證，並且正在進行 SOC2 和 FedRAMP JAB 評定程序。


## <a name="pricing"></a>價格
### <a name="how-can-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>如何計算 VMware 災害復原的近似費用？

您可以使用[定價計算機](https://aka.ms/asr_pricing_calculator)評估成本，同時使用 Site Recovery。

預估成本的詳細值，執行的部署規劃工具[VMware](https://aka.ms/siterecovery_deployment_planner)，並使用[成本估計報告](https://aka.ms/asr_DP_costreport)。

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>是否有任何差別成本複寫到儲存體，或直接為受控磁碟？

受控的磁碟會被收取稍有不同的儲存體帳戶。 [了解更多](https://azure.microsoft.com/pricing/details/managed-disks/)有關受控的磁碟定價。

## <a name="mobility-service"></a>行動服務

### <a name="where-can-i-find-the-mobility-service-installers"></a>哪裡可以找到行動服務安裝程式？
安裝程式位於 **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**組態伺服器上的資料夾。

## <a name="how-do-i-install-the-mobility-service"></a>如何安裝行動服務？
您想要複寫，使用多種方法，每個 VM 上安裝：
- [推入安裝](vmware-physical-mobility-service-overview.md#push-installation)
- [手動安裝](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui)從 UI 或 Powershell。
- 部署使用的部署工具，例如[System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)。

## <a name="managed-disks"></a>受控磁碟

### <a name="where-does-site-recovery-replicate-data-to"></a>其中 Site Recovery 將複寫資料？

Site Recovery 會將內部部署 VMware Vm 和實體伺服器複寫至 Azure 中的受控磁碟。
- Site Recovery 處理伺服器會將複寫記錄檔寫入的目標區域中的快取儲存體帳戶。
- 這些記錄檔會用來建立受控磁碟上的復原點。
- 容錯移轉時，您選取的復原點用來建立目標的受控的磁碟。
- 先前已複寫到儲存體帳戶 （之前年 3 月 2019) 的 Vm 不會受到影響。


### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>我可以對儲存體帳戶複寫新的機器？

否，從年 3 月 2019，開始在入口網站中，您可以將複寫至 Azure 受控磁碟。 

儲存體帳戶的新 Vm 的複寫才可以使用 PowerShell 或 REST API （版本 2018年-01-10 或 2016年-08-10）。

### <a name="what-are-the-benefits-in-replicating-to-managed-disks"></a>複寫至受控磁碟的優點有哪些？

[了解如何](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/)Site Recovery 可簡化使用受控磁碟的災害復原。


### <a name="can-i-change-the-managed-disk-type-after-machine-is-protected"></a>機器受到保護後可以變更受控的磁碟類型嗎？

是，您可以輕鬆地[受控磁碟的類型變更](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage)。 在變更之前的型別，請確定您撤銷磁碟 SAS URL，方法是前往 Azure 入口網站中的 受控磁碟資源。 從 [概觀] 刀鋒視窗中，取消任何進行中的匯出。 一旦已撤銷該 SA URL，請接下來的幾分鐘內變更磁碟類型。 不過，如果您變更受控的磁碟類型時，等候要由 Azure Site Recovery 產生全新的復原點。 適用於任何測試容錯移轉或容錯移轉從現在開始使用新的復原點。

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>我可以切換至非受控磁碟的複寫從受控磁碟嗎？

否，從切換 managed 到 unmanaged 不受支援。

## <a name="replication"></a>複寫


### <a name="what-are-the-replicated-vm-requirements"></a>複寫的 VM 有何需求？

[了解更多](vmware-physical-azure-support-matrix.md##replicated-machines)需 VMware VM/實體伺服器需求和支援。

### <a name="how-often-can-i-replicate-to-azure"></a>複寫到 Azure 的頻率為何？
從 VMware VM 到 Azure 的複寫會持續執行。


### <a name="can-i-extend-replication"></a>我可以延伸複寫嗎？
不支援延伸的或鏈結的複寫。 請在 [意見反應論壇](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)中提出這項功能的要求。

### <a name="can-i-do-an-offline-initial-replication"></a>是否可執行離線初始複寫？
不支援此做法。 請在 [意見反應論壇](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)中提出這項功能的要求。

### <a name="can-i-exclude-disks-from-replication"></a>可以從複寫排除磁碟嗎？
是，您可以排除磁碟。

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>是否可使用動態磁碟來複寫 VM？
動態磁碟可以複寫。 作業系統磁碟必須是基本磁碟。

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>如果我使用複寫群組以獲取多 VM 一致性，我是否可將新的 VM 新增至現有的複寫群組？
是，您可以將新的 VM 新增至現有的複寫群組，為這些 VM 啟用複寫。
- 在起始複寫之後，您無法 VM 新增至現有的複寫群組。
- 您無法建立複寫群組，針對現有的 Vm。

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>我是否可藉由新增磁碟或調整其大小來修改要複寫的 VM？

對於從 VMware 到 Azure 的複寫，您可以修改磁碟大小。 如果您想要新增磁碟，您必須新增磁碟，並重新啟用對 VM 的保護。

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>我可以移轉在內部部署機器到新的 vCenter Server 而不會影響進行中的複寫？
否，變更 Vcenter 或移轉會影響進行中的複寫。 您必須設定 Site Recovery 與新的 vCenter Server，並再次啟用複寫的機器。

### <a name="can-i-replicate-to-a-cachetarget-storage-account-which-has-a-vnet-with-azure-storage-firewalls-configured-on-it"></a>我可以複寫到具有 VNet （與 Azure 儲存體防火牆） 在其上設定的快取/目標儲存體帳戶？
否，Site Recovery 不支援在 Vnet 上的儲存體的複寫。





## <a name="component-upgrade"></a>元件升級

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>我的行動服務代理程式 」 或 「 組態伺服器的版本是舊的和我升級失敗。 該怎麼辦？  

Site Recovery 會遵循到 N-4 支援模型。 [了解更多](https://aka.ms/asr_support_statement)有關如何從非常舊版本升級。

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-azure-site-recovery"></a>哪裡可以找到 Azure Site Recovery 的版本資訊/更新彙總套件？

[了解](site-recovery-whats-new.md)有關新的更新，並[取得彙總資訊](service-updates-how-to.md)。

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>哪裡可以找到升級至 Azure 的災害復原的資訊？

[深入了解](https://aka.ms/asr_vmware_upgrades)升級。

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>我是否需要重新啟動來源機器，為每個升級？

雖然建議，但並不是每次升級都必須這麼做。 [深入了解](https://aka.ms/asr_vmware_upgrades)。


## <a name="configuration-server"></a>組態伺服器

### <a name="what-does-the-configuration-server-do"></a>組態伺服器有何功用？

組態伺服器會執行內部部署 Site Recovery 元件，包括：
- 組態伺服器本身，以協調內部部署與 Azure 之間的通訊，以及管理資料複寫。
- 處理序伺服器，可作為複寫閘道。 負責接收複寫資料，以快取、壓縮和加密進行最佳化，然後將複寫資料傳送至 Azure 儲存體。 處理序伺服器也會在 Vm 上，行動服務的推入安裝，並執行的內部部署 VMware Vm 自動探索。
- 主要目標伺服器，會在從 Azure 容錯回復期間處理複寫資料。

[深入了解](vmware-azure-architecture.md)組態伺服器元件和程序。

### <a name="where-do-i-set-up-the-configuration-server"></a>我應在何處設定組態伺服器？
您需要具有高可用性的單一內部部署 VMware VM，供組態伺服器使用。 針對實體伺服器災害復原，您可以在實體機器上安裝組態伺服器。

### <a name="what-do-i-need-for-the-configuration-server"></a>我該怎麼做為組態伺服器？

請檢閱[必要條件](vmware-azure-deploy-configuration-server.md#prerequisites)。

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>是否可以手動設定組態伺服器，而不要使用範本？
我們建議您[建立組態伺服器 VM](vmware-azure-deploy-configuration-server.md)使用最新版的 OVF 範本。 如果原因，您不能例如您沒有 VMware 伺服器的存取權，您可以[下載](physical-azure-set-up-source.md)來自入口網站設定組態伺服器的安裝檔。

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>組態伺服器是否可複寫到多個區域？
沒有。 若要這樣做，您需要在每個區域中的組態伺服器。

### <a name="can-i-host-a-configuration-server-in-azure"></a>是否可將組態伺服器裝載在 Azure 中？
雖然可行，但執行組態伺服器的 Azure VM 將需要與您的內部部署 VMware 基礎結構和 VM 通訊。 這會增加延遲，並會影響進行中的複寫。

### <a name="how-do-i-update-the-configuration-server"></a>如何更新組態伺服器？

[了解](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)如何更新組態伺服器。
- 您可以在找到最新的更新資訊[Azure 更新頁面](https://azure.microsoft.com/updates/?product=site-recovery)。
- 您可以從入口網站下載最新版本。 或者，您可以直接下載最新版本的組態伺服器[Microsoft 下載中心](https://aka.ms/asrconfigurationserver)。
- 如果您的版本是四個以上的版本比目前的版本還舊，請參閱我們[支援陳述式](https://aka.ms/asr_support_statement)的升級指導方針。

### <a name="should-i-back-up-the-configuration-server"></a>我應該備份組態伺服器？
建議對於組態伺服器進行定期排程的備份。
- 成功容錯回復，容錯回復的 VM 必須存在於組態伺服器資料庫。
- 組態伺服器必須執行並處於連線狀態。
- [了解更多](vmware-azure-manage-configuration-server.md)關於常見的組態伺服器管理工作。

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>在設定組態伺服器時，是否可以手動下載並安裝 MySQL？

是。 請下載 MySQL 並將它放在 **C:\Temp\ASRSetup** 資料夾中。 然後再手動加以安裝。 當您設定組態伺服器 VM 時，如果您接受條款，MySQL 在 [下載並安裝] 中就會列示為 [已安裝]。

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>是否可以不要下載 MySQL 而讓 Site Recovery 加以安裝？

是。 請下載 MySQL 安裝程式，並將它放在 **C:\Temp\ASRSetup** 資料夾中。  當您設定組態伺服器 VM 時，接受條款，然後按一下**下載並安裝**。 在入口網站會使用您新增以安裝 MySQL 安裝程式。
 
### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>組態伺服器 VM 是否可用於任何其他位置？
否，VM 只能用於組態伺服器上。 

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>是否可以複製組態伺服器並將它用於協調流程？
否，您應該設定全新的組態伺服器以避免註冊問題。

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>我可以變更已註冊組態伺服器的保存庫嗎？
沒有。 組態伺服器相關聯的保存庫之後，就無法變更。 檢閱[這篇文章](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)若要了解重新註冊。

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>是否可將相同的組態伺服器用於 VMware VM 和實體伺服器的災害復原
是，但請注意該實體機器只能容錯回復到 VMware VM。

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>哪裡可以下載組態伺服器的複雜密碼？
[了解如何](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)下載複雜密碼。

### <a name="where-can-i-download-vault-registration-keys"></a>哪裡可以下載保存庫註冊金鑰？

在 復原服務保存庫中，按一下**組態伺服器**中**Site Recovery 基礎結構** > **管理**。 然後在**伺服器**，選取**下載註冊金鑰**下載保存庫認證檔。







## <a name="failover-and-failback"></a>容錯移轉和容錯回復
### <a name="can-i-use-the-process-server-at-on-premises-for-failback"></a>是否可用於處理序伺服器在內部部署容錯回復？
我們強烈建議處理序伺服器在 Azure 中建立的容錯回復目的，以避免發生資料傳輸的延遲。 此外，如果來源 Vm 網路以在組態伺服器的 Azure 公開網路上時，務必要使用在 Azure 中建立的容錯回復處理序伺服器。

### <a name="can-i-retain-the-ip-address-on-failover"></a>容錯移轉時是否可以保留 IP 位址？
是，您可以在容錯轉時保留 IP 位址。 請確定您在 VM 容錯移轉之前的 '計算與網路 設定中指定目標 IP 位址。 此外，關閉機器在容錯移轉，以避免在容錯回復期間的 IP 位址衝突的時間。

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>我可以變更的目標 VM 大小或容錯移轉前的 VM 類型嗎？
是，您可以變更的型別或複寫的 Vm，入口網站中的計算和網路設定的容錯移轉之前隨時 VM 的大小。

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
