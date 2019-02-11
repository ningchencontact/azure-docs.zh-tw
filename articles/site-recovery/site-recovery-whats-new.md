---
title: Azure Site Recovery 的新功能 | Microsoft Docs
description: 彙整 Azure Site Recovery 中所導入的新功能
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: raynew
ms.openlocfilehash: 61e66a19b625141c69a9473373d3d5d808e18fde
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211112"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery 最新功能

[Azure Site Recovery](site-recovery-overview.md) 服務會持續更新並改進。 為了協助您掌握最新訊息，本文提供最新版本、新功能和新內容的相關資訊。 此頁面會定期更新。

如果您有 Site Recovery 功能的相關建議，歡迎您[提供任何意見反應](https://feedback.azure.com/forums/256299-site-recovery)。

## <a name="q1-2019"></a>Q1 2019

### <a name="linux-support"></a>支援 Linux

[更新彙總套件 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) 提供 Site Recovery 代理程式和提供者的更新。 這些更新新增了 Linux 的支援，如下所示：

- **Azure VM 的災害復原**：RedHat 工作站 6/7；適用於 Ubuntu、Debian 和 SUSE 的新核心版本。
- **從 VMware VM/實體伺服器到 Azure 的災害復原**：RedHat Enterprise Linux 7.6；RedHat 工作站 6/7；Oracle Linux 6.10/7.6 新核心版本 Ubuntu、Debian 和 SUSE。



## <a name="q4-2018"></a>Q4 2018

## <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Azure VM 災害復原的定價計算機

進行 Azure VM 的災害復原，會產生 VM 授權成本以及網路和儲存成本。 Azure 提供[定價計算機](https://aka.ms/a2a-cost-estimator)來協助您計算這些成本。 Site Recovery 現在提供[範例定價預估](https://aka.ms/a2a-cost-estimator)功能，可根據三層式應用程式計算範例部署的價格；該應用程式使用 6 個 VM，其中包含 12 個標準 HDD 磁碟和六個進階 SSD 磁碟。 此範例假設標準磁碟的資料變更率為每天 10 GB，進階磁碟則為 20 GB。 您可以針對本身的特定部署，變更估計成本的變數。 您可以指定 VM 的數目、受控磁碟的數目和類型，以及跨 VM 的預期總資料變更率。 此外，您也可以套用壓縮因數來估計頻寬成本。 [閱讀](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)公告。

### <a name="support-for-azure-vms-in-zones"></a>區域中的 Azure VM 支援

Azure 可用性區域是 Azure 區域內獨特的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 現在，您可以為 Azure VM 啟用複寫功能，並將容錯移轉的目標設為單一 VM 執行個體、可用性設定組中的 VM，或可用性區域中的 VM。 此設定不會影響複寫。 [閱讀](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/)公告。
 
### <a name="disaster-recovery-for-encrypted-vms"></a>加密 VM 的災害復原

Site Recovery 支援使用 Azure AD 應用程式透過 Azure 磁碟加密 (ADE) 進行加密的 Azure VM。 [深入了解](azure-to-azure-how-to-enable-replication-ade-vms.md)。

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>使用加速網路功能為 VM 進行災害復原

加速網路可以對 VM 啟用單一根 I/O 虛擬化 (SR-IOV)，而提升其網路效能。 當您為 Azure VM 啟用複寫功能時，Site Recovery 會偵測是否已啟用加速網路。 如果是，則在容錯移轉之後，Site Recovery 將會自動對 [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) 和 [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms) 的目標複本 Azure VM 設定加速網路。 [深入了解](azure-vm-disaster-recovery-with-accelerated-networking.md)。

### <a name="automatic-updates-for-the-mobility-service-extension"></a>行動服務擴充功能的自動更新

Site Recovery 新增了行動服務擴充功能的自動更新選項。 行動服務擴充功能會安裝在 Site Recovery 所複寫的每個 Azure VM 上。 當您啟用複寫時，您會選取是否允許 Site Recovery 管理此擴充功能的更新。 更新不需要重新啟動 VM，也不會影響複寫。 [深入了解](azure-to-azure-autoupdate.md)。

### <a name="support-for-standard-ssd-disks"></a>標準 SSD 磁碟的支援

Azure 導入了[標準固態硬碟 (SSD)](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd) 磁碟，可為特定應用程式提供符合成本效益的儲存解決方案，例如，需要一致效能、但磁碟 IOPS 不高的 Web 伺服器。 它們結合了進階 SSD 與標準 HDD 磁碟的元素。 Site Recovery 可讓使用標準 SSD 磁碟的 Azure VM 進行災害復原。 根據預設，在容錯移轉至目標區域後，磁碟類型將會保留。

### <a name="support-for-azure-storage-firewall"></a>Azure 儲存體防火牆的支援

您可以為帳戶開啟防火牆規則，將 Azure 儲存體帳戶的存取範圍限定於一組特定的網路。 您可以設定儲存體帳戶，使其依預設拒絕來自內部網路與網際網路的流量，然後針對來自特定 VNet 的流量授與存取權。 在已啟用防火牆的儲存體帳戶上，Site Recovery 支援將具有非受控磁碟的 VM 複寫到次要區域。 在目標區域中，針對非受控磁碟，您可以選取已啟用防火牆的儲存體帳戶。 您也可以將網路存取限定於來源 VM 所在的網路，以限制對快取儲存體帳戶的存取。 請注意，您必須[允許存取](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)受信任的 Microsoft 服務。

## <a name="q3-2018"></a>Q3 2018 

### <a name="linux-support"></a>支援 Linux

#### <a name="update-rollup-28"></a>更新彙總套件 28

[更新彙總套件 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) 提供 Site Recovery 代理程式和提供者的更新。 這些更新新增了 Linux 的支援，如下所示：

- **Azure VM 的災害復原**：現已支援 RedHat Enterprise Linux 6.10、CentOS 6.10、以傳統 BIOS 相容性模式使用 GUID 磁碟分割表格 (GPT) 磁碟分割樣式的 Linux 型 VM。
- **從 VMware VM/實體伺服器到 Azure 的災害復原**：現已支援 RedHat Enterprise Linux 6.10、CentOS 6.10、以傳統 BIOS 相容性模式使用 GUID 磁碟分割表格 (GPT) 磁碟分割樣式的 Linux 型 VM。

#### <a name="update-rollup-27"></a>更新彙總套件 27

[更新彙總套件 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) 提供 Site Recovery 代理程式和提供者的更新。 這些更新新增了 Linux 的支援，如下所示：

- **Azure VM 的災害復原**：Red Hat Enterprise Linux 7.5
- **從 VMware VM/實體伺服器到 Azure 的災害復原**：SUSE Linux Enterprise Server 12、Red Hat Enterprise Linux 7.5

### <a name="support-for-azure-vms-running-on-windows-server-2016"></a>支援在 Windows Server 2016 上執行的 Azure VM

在 Windows Server 2016 上執行的 Azure VM 可使用 Azure Site Recovery 在 Azure 區域間進行複寫。

### <a name="support-for-azure-vms-running-storage-spaces-direct"></a>支援執行儲存空間直接存取的 Azure VM

[儲存空間直接存取](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) (從 Windows Server 2016 開始提供) 將磁碟機組合在一起，然後集區中的容量來建立儲存空間。 儲存空間可以用於獨立 VM 上、在每個叢集節點上使用本機儲存體的 [Azure VM 客體叢集](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm)上，或叢集間的共用儲存體上。

## <a name="next-steps"></a>後續步驟

透過 [Azure 更新](https://azure.microsoft.com/updates/?product=site-recovery)頁面掌握相關更新的最新訊息。


