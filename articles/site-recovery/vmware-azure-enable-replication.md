---
title: 啟用 VMware Vm 複寫至 Azure Site Recovery 與 Azure 的災害復原 |Microsoft Docs'
description: 本文說明如何使用 Azure Site Recovery 將 VMware Vm 啟用複寫至 Azure 的災害復原。
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 05/10/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: add0f8252bdae6857b28deeb7de4c1d09973e452
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540773"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>讓 VMware VM 能夠複寫至 Azure

本文說明如何啟用將內部部署 VMware VM 複寫至 Azure 的作業。

## <a name="prerequisites"></a>必要條件

本文假設您已經：

- [設定您的內部部署來源環境](vmware-azure-set-up-source.md)。
- [設定您在 Azure 中的目標環境](vmware-azure-set-up-target.md)。

## <a name="before-you-start"></a>開始之前
當您要複寫 VMware 虛擬機器時，請注意這項資訊：

* 您的 Azure 使用者帳戶必須具有特定[權限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能將新的虛擬機器複寫至 Azure。
* 系統會每隔 15 分鐘探索 VMware VM 一次。 它可能需要 15 分鐘或更久，才會出現在 Azure 入口網站，在探索之後的 Vm。 同樣地，探索可能需要 15 分鐘或更久，當您將加入新的 vCenter 伺服器或 vSphere 主機。
* 它可能需要 15 分鐘或更久 （例如 VMware 工具安裝） 的虛擬機器上的環境變更，在入口網站中更新。
* 您可以查看 VMware Vm 的上次探索時間：請參閱**上次連絡時間**欄位**組態伺服器**vCenter server/vSphere 主機的頁面。
* 若要新增虛擬機器的複寫，而不等候已排定的探索，反白選取組態伺服器 （但不要按它），然後選取**重新整理**。
* 當您啟用複寫時，如果虛擬機器已準備好時，處理序伺服器會自動在其上安裝 Azure Site Recovery 行動服務。

## <a name="enable-replication"></a>啟用複寫

請遵循本節中的步驟之前，請注意下列資訊：
* Azure Site Recovery 現在會直接複寫至受控磁碟的所有新的複寫。 處理序伺服器會將複寫記錄檔寫入的目標區域中的快取儲存體帳戶。 這些記錄檔用來建立複本受控磁碟中的復原點。
* 在容錯移轉時，您選取的復原點用來建立目標受控磁碟。
* 先前已設定為複寫到目標儲存體帳戶的 Vm 不會受到影響。
* 只有透過 Representational State Transfer (REST) API 和 Powershell 使用新的虛擬機器的儲存體帳戶的複寫。 使用 Azure REST API 2016-08-10 或 2018年-01-10 版本複寫到儲存體帳戶。

1. 移至**步驟 2:** 複寫應用程式 > 來源。 為第一次啟用複寫之後，請選取 **+ 複寫**保存庫啟用額外的虛擬機器的複寫中。
2. 在 [來源] 頁面 > [來源] 中，選取組態伺服器。
3. 針對**機器類型**，選取**虛擬機器**或是**實體機器**。
4. 在 [vCenter/vSphere Hypervisor] 中，選取管理 vSphere 主機的 vCenter 伺服器，或選取主機。 此設定不相關，如果您複寫實體電腦。
5. 選取處理序伺服器。 如果不沒有建立任何額外的處理序伺服器，組態伺服器的內建的處理序伺服器可在下拉式清單中。 每個處理序伺服器的健全狀況狀態會顯示根據建議的限制和其他參數。 選擇 狀況良好的處理序伺服器。 A[重要](vmware-physical-azure-monitor-process-server.md#process-server-alerts)無法選擇處理序伺服器。 您可以[疑難排解及解決](vmware-physical-azure-troubleshoot-process-server.md)錯誤**或是**設定[向外延展處理序伺服器](vmware-azure-set-up-process-server-scale.md)。
    ![啟用複寫來源視窗](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> 從[9.24 版本](service-updates-how-to.md#links-to-currently-supported-update-rollups)，以增強的處理序伺服器的健康情況警示導入其他警示。 升級所有警示，以便產生 9.24 版本或更新版本的 Site Recovery 元件。

6. 針對**目標**，選取您想要用來建立容錯移轉虛擬機器的訂用帳戶和資源群組。 選擇您想要在 Azure 中用於容錯移轉 Vm 的部署模型。
2. 選取 Azure 網路和 Azure Vm 在容錯移轉之後，將會連接到的子網路。 網路必須位於與 Site Recovery 服務保存庫相同的區域。

   選取 **立即設定選取的機器**將網路設定套用到您選定要提供保護的所有虛擬機器。 選取 **稍後再設定**以選取每個虛擬機器的 Azure 網路。 如果您沒有網路，則必須建立一個。 若要使用 Azure Resource Manager 建立網路，請選取**新建**。 選取的子網路，如果適用的話，，然後選取**確定**。
   
   ![啟用複寫目標視窗](./media/vmware-azure-enable-replication/enable-rep3.png)

1. 針對**虛擬機器** > **選取 虛擬機器**，選取您想要複寫的每部虛擬機器。 您只能選取可以啟用複寫的虛擬機器。 然後選取 [確定]。 如果您無法看到或選取特定的任何虛擬機器，請參閱[來源機器未列在 Azure 入口網站](https://aka.ms/doc-plugin-VM-not-showing)來解決此問題。

    ![啟用複寫選取虛擬機器視窗](./media/vmware-azure-enable-replication/enable-replication5.png)

1. 針對**屬性** > **設定屬性**，選取 處理序伺服器自動安裝在虛擬機器上的 Site Recovery 行動服務所使用的帳戶。 而且請選擇要複寫至取決於您的資料變換圖樣的目標受控磁碟的類型。
10. 根據預設，會複寫來源虛擬機器的所有磁碟。 若要從複寫排除磁碟，請清除**Include**任何您不想要複寫的磁碟的核取方塊。 然後選取 [確定]。 您可以稍後再設定其他屬性。 深入了解[排除磁碟](vmware-azure-exclude-disk.md)。

    ![啟用複寫設定屬性 視窗](./media/vmware-azure-enable-replication/enable-replication6.png)

1. 在**複寫設定** > **設定複寫設定**，確認已選取正確的複寫原則。 您可以修改複寫原則設定，在**設定** > **複寫原則** > ***原則名稱*** >  **編輯設定**。 您套用到原則的變更也會套用到複寫和新虛擬機器。
1. 啟用**多部 VM 一致性**如果您想要虛擬機器聚集成一個複寫群組。 指定群組名稱，然後再選取**確定**。

    > [!NOTE]
    >    * 複寫群組中的虛擬機器一起複寫，並在容錯移轉時會有共用損毀一致和應用程式一致復原點。
    >    * 將 VM 與實體伺服器一起收集，讓它們鏡像您的工作負載。 啟用多 VM 一致性可能會影響工作負載的效能。 只有當虛擬機器執行相同的工作負載，且需要一致性，請執行這項操作。

    ![啟用複寫 視窗](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. 選取 [啟用複寫]。 您可以追蹤進度**啟用保護**在作業**設定** > **工作** > **Site Recovery 作業**. 執行「完成保護」作業之後，虛擬機器即準備好進行容錯移轉。

## <a name="view-and-manage-vm-properties"></a>檢視及管理 VM 屬性

接下來，請確認來源虛擬機器的屬性。 請記住，Azure VM 名稱應該符合 [Azure 虛擬機器需求](vmware-physical-azure-support-matrix.md#replicated-machines)。

1. 移至**設定** > **複寫的項目**，然後選取 虛擬機器。 **Essentials**頁面會顯示 VM 的設定與狀態相關資訊。
1. 在 [屬性] 中，您可以檢視 VM 的複寫和容錯移轉資訊。
1. 在 **計算與網路** > **計算屬性**，您可以變更多個 VM 屬性。 

    ![計算與網路的 [屬性] 視窗](./media/vmware-azure-enable-replication/vmproperties.png)

    * Azure VM 名稱：如有必要，請修改以符合 Azure 需求，名稱。
    * 目標 VM 大小或 VM 類型：預設 VM 大小會選擇根據目標 Azure 區域中包含的磁碟計數、 NIC 計數、 CPU 核心計數、 記憶體和可用的 VM 角色大小的幾個參數。 Azure Site Recovery 會挑選第一個可用 VM 大小能符合所有準則。 您可以選取不同的 VM 大小，根據您的需求，在容錯移轉之前的任何時間。 請注意，VM 磁碟大小也根據來源磁碟大小，而且只能在容錯移轉之後加以變更。 深入了解磁碟大小和 IOPS 比率[在 Windows 上的虛擬機器磁碟的延展性和效能目標](../virtual-machines/windows/disk-scalability-targets.md)。

    *  資源群組:您可以選取[資源群組](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines)，從虛擬電腦將成為其後置容錯移轉的一部分。 您可以變更此設定在容錯移轉之前的任何時間。 容錯移轉之後，如果您將虛擬機器移轉至不同的資源群組，該虛擬機器的保護設定會中斷。
    * 可用性設定組：您可以選取[可用性設定組](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines)如果您的虛擬機器需要容錯移轉後的一部分。 當您選取可用性設定組時，記住下列資訊：

        * 只有屬於指定的資源群組的可用性設定組列出。  
        * 在不同的虛擬網路上的 Vm 不能在相同的可用性設定組。
        * 只有相同大小的虛擬機器可在相同的可用性設定組中。
1. 您也可以新增目標網路、 子網路，以及指派給 Azure VM 的 IP 位址的相關資訊。
2. 在 [磁碟] 中，您可以看見 VM 上將要複寫的作業系統和資料磁碟。

### <a name="configure-networks-and-ip-addresses"></a>設定網路和 IP 位址

您可以設定目標 IP 位址。 如果您未提供地址，容錯移轉的虛擬機器會使用 DHCP。 如果您設定的位址在容錯移轉時無法使用，容錯移轉就無法運作。 如果位址可用於測試容錯移轉網路，您可以使用相同的目標 IP 位址進行測試容錯移轉。

網路介面卡的數目取決於大小，您指定目標虛擬機器，如下所示：

- 如果來源虛擬機器上的網路介面卡數目小於或等於針對目標 VM 大小所允許的介面卡數目，目標會有相同數目的介面卡做為來源。
- 如果來源虛擬機器的介面卡數目超過針對目標 VM 大小所允許的數目，則會使用目標大小的最大值。 例如，如果來源虛擬機器具有兩張網路介面卡，目標 VM 大小支援四張，目標虛擬機器會有兩張介面卡。 如果來源 VM 具有兩張介面卡，但目標大小僅支援一張，目標 VM 會有一個介面卡。
- 如果虛擬機器有多張網路介面卡，就會全部連線至相同的網路。 此外，顯示在清單中的第一個配接器會變成*預設*Azure 虛擬機器中的網路介面卡。 

### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

Microsoft 軟體保證客戶可以使用 Azure Hybrid Benefit，節省移轉至 Azure 的 Windows Server 電腦的授權成本。 優點也適用於 Azure 的災害復原。 若您符合資格，您可以指派到容錯移轉時，Site Recovery 會建立虛擬機器的優點。 若要這樣做，請遵循下列步驟：
1. 移至**電腦與網路屬性**複寫的虛擬機器。
2. 當系統詢問您是否具備 Windows Server 授權，讓您符合 Azure Hybrid Benefit 的資格，請回答。
3. 請確認您有可用來將權益套用至將會在容錯移轉建立 VM 的軟體保證的合格 Windows Server 授權。
4. 儲存複寫的虛擬機器的設定。

深入了解 [Azure Hybrid Benefit](https://aka.ms/azure-hybrid-benefit-pricing)。

## <a name="resolve-common-issues"></a>解決常見的問題

* 每個磁碟應該小於 4 TB。
* OS 磁碟應該是基本磁碟，而非動態磁碟。
* 針對層代 2/已啟用 UEFI 的虛擬機器，作業系統系列應為 Windows，而且開機磁碟應小於 300 GB。

## <a name="next-steps"></a>後續步驟

虛擬機器達到受保護的狀態後，請嘗試[容錯移轉](site-recovery-failover.md)來檢查您的應用程式是否出現在 Azure 中。

* 了解如何[清除註冊與保護設定](site-recovery-manage-registration-and-protection.md)，以停用複寫。
* 了解如何[使用 Powershell 自動化您的虛擬機器的複寫](vmware-azure-disaster-recovery-powershell.md)。
