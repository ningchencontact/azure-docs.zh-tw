---
title: Azure Site Recovery 的新功能 | Microsoft Docs
description: 彙整 Azure Site Recovery 中所導入的新功能
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: raynew
ms.openlocfilehash: fece1adc9ad2f5844a7d6cc1e0e9cc92d44de6d8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57892926"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery 最新功能

[Azure Site Recovery](site-recovery-overview.md) 服務會持續更新並改進。 為了協助您掌握最新訊息，本文提供最新版本、新功能和新內容的相關資訊。 此頁面會定期更新。

如果您有 Site Recovery 功能的相關建議，歡迎您[提供任何意見反應](https://feedback.azure.com/forums/256299-site-recovery)。

## <a name="q1-2019"></a>Q1 2019 

### <a name="update-rollup-34-february-2019"></a>更新彙總套件 34 (第 2019 年 2 月)

[更新彙總套件 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery)提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者 （如彙總套件中所述） 的更新
**問題的修正程式** | 許多修正和增強功能 （如下所述的彙總套件）



### <a name="update-rollup-33-february-2019"></a>更新彙總套件 33 (第 2019 年 2 月)

[更新彙總套件 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery)提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者 （如彙總套件中所述） 的更新
**問題的修正程式** | 許多修正和增強功能 （如下所述的彙總套件）
**網路對應** | 針對 Azure VM 災害復原，您現在可以使用任何可用的目標網路時啟用複寫。 
**標準的 SSD** | 您現在可以設定 Azure Vm 使用的災害復原[標準的 SSD 磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)。
**儲存空間直接存取** | 您可以設定使用的 Azure VM 的應用程式上執行的應用程式的災害復原[儲存空間直接存取](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)以獲得高可用性。
**BRTFS 檔案系統** | 支援 VMware Vm，除了 Azure Vm。<br/><br/> 如果不支援：在啟用複寫後變更 BTRFS 檔案系統子磁碟區時，檔案系統分散到多個磁碟，或如果 BTRFS 檔案系統支援 RAID。



### <a name="update-rollup-32-january-2019"></a>更新彙總套件 32 (第 2019 年 1 月)

[更新彙總套件 31](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery)提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者 （如彙總套件中所述） 的更新
**問題的修正程式** | 許多修正和增強功能 （如下所述的彙總套件）
**適用於 Linux 的災害復原** | **Azure VM**：RedHat 工作站 6/7;支援新的核心版本的 Ubuntu、 Debian 和 SUSE。<br/><br/> **VMware Vm/實體伺服器**:Redhat Enterprise Linux 7.6;RedHat 工作站 6/7;Oracle Linux 6.10/7.6;新的 Ubuntu、 Debian 和 SUSE 的核心版本的支援。


### <a name="update-rollup-31-january-2019"></a>更新彙總套件 31 (第 2019 年 1 月)

[更新彙總套件 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者 （如彙總套件中所述） 的更新
**問題的修正程式** | 許多修正和增強功能 （如下所述的彙總套件）
**適用於 Linux 的災害復原** | **Azure VM**：Oracle Linux 6.8 和 6.9/7.0;UEK5 核心支援。<br/><br/> **VMware Vm/實體伺服器**:Oracle Linux 6.8 和 6.9/7.0;UEK5 核心支援。
**BRTFS 檔案系統** | Azure Vm 支援。
**LVM** | 新增 LVM 和 LVM2 磁碟區的支援。<br/><br/> /Boot 目錄磁碟分割和 LVM 磁碟區上才支援。
**Directories** | 這些目錄 seet 的加總為不同的資料分割或不在相同的系統磁碟的檔案系統的支援: / (root)、 /boot、 /usr、 /usr/local、 /var、 /etc。
**Windows Server 2008** | 新增動態磁碟支援。
**VMware VM 容錯移轉** | 改善 storvsc 和 vsbus 不是開機驅動程式的 VMware Vm 容錯移轉時間。
**UEFI 支援** | Azure Vm 不支援 UEFI 的開機類型。 您現在可以移轉具有 UEFI 的內部部署實體伺服器至 Azure Site Recovery。 Site Recovery 將 bios 開機類型轉換成移轉之前，先移轉伺服器。 Site Recovery 之前這項轉換 Vm 僅支援。 適用於實體伺服器執行 Windows Server 2012 或更新版本支援。
**可用性區域中的 azure Vm** | 您可以啟用部署在可用性區域的 Azure Vm 的複寫至另一個區域。 ou 現在可以啟用 Azure 虛擬機器的複寫，並設定為單一 VM 執行個體、 可用性設定組中的 VM 或 VM 在可用性區域中的 容錯移轉的目標。 此設定不會影響複寫。 [閱讀](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/)公告。


## <a name="q4-2018"></a>Q4 2018

### <a name="update-rollup-30-october-2018"></a>更新彙總套件 30 (年 10 月 2018)

[更新彙總套件 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30)提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者 （如彙總套件中所述） 的更新
**問題的修正程式** | 許多修正和增強功能 （如下所述的彙總套件）
**區域支援** | 站台澳洲中部 1 和澳大利亞中部 2 新增的復原支援。
**支援磁碟加密** | 新增 Azure Vm 與 Azure 磁碟加密 (ADE) 加密的 Azure AD 應用程式的災害復原的支援。 [深入了解](azure-to-azure-how-to-enable-replication-ade-vms.md)。
**磁碟排除** | 未初始化的磁碟現在會在 Azure VM 複寫期間自動排除。
**已啟用防火牆的儲存體** | 新增的支援[防火牆啟用儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-network-security)。<br/><br/> 您可以將 Azure Vm 複寫到災害復原的另一個 Azure 區域的防火牆已啟用儲存體帳戶上的非受控磁碟。<br/><br/> 您可以使用目標儲存體帳戶的防火牆已啟用儲存體帳戶，用於非受控磁碟。<br/><br/> 只能使用 PowerShell 的支援。


### <a name="update-rollup-29-october-2018"></a>更新彙總套件 29 (年 10 月 2018)

[更新彙總套件 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery)提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者 （如彙總套件中所述） 的更新
**問題的修正程式** | 許多修正和增強功能 （如下所述的彙總套件）

### <a name="automatic-updates-for-the-mobility-service-extension"></a>行動服務擴充功能的自動更新

Site Recovery 新增了行動服務擴充功能的自動更新選項。 行動服務擴充功能會安裝在 Site Recovery 所複寫的每個 Azure VM 上。 當您啟用複寫時，您會選取是否允許 Site Recovery 管理此擴充功能的更新。 更新不需要重新啟動 VM，也不會影響複寫。 [深入了解](azure-to-azure-autoupdate.md)。

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>使用加速網路功能為 VM 進行災害復原

加速網路可以對 VM 啟用單一根 I/O 虛擬化 (SR-IOV)，而提升其網路效能。 當您為 Azure VM 啟用複寫功能時，Site Recovery 會偵測是否已啟用加速網路。 如果是，則在容錯移轉之後，Site Recovery 將會自動對 [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) 和 [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms) 的目標複本 Azure VM 設定加速網路。 [深入了解](azure-vm-disaster-recovery-with-accelerated-networking.md)。

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Azure VM 災害復原的定價計算機

進行 Azure VM 的災害復原，會產生 VM 授權成本以及網路和儲存成本。 Azure 提供[定價計算機](https://aka.ms/a2a-cost-estimator)來協助您計算這些成本。 Site Recovery 現在提供[範例定價預估](https://aka.ms/a2a-cost-estimator)功能，可根據三層式應用程式計算範例部署的價格；該應用程式使用 6 個 VM，其中包含 12 個標準 HDD 磁碟和六個進階 SSD 磁碟。 此範例假設標準磁碟的資料變更率為每天 10 GB，進階磁碟則為 20 GB。 您可以針對本身的特定部署，變更估計成本的變數。 您可以指定 VM 的數目、受控磁碟的數目和類型，以及跨 VM 的預期總資料變更率。 此外，您也可以套用壓縮因數來估計頻寬成本。 [閱讀](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)公告。



## <a name="q3-2018"></a>Q3 2018 


### <a name="update-rollup-28-august-2018"></a>更新彙總套件 28 (年 8 月 2018)

[更新彙總套件 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery)提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者 （如彙總套件中所述） 的更新
**適用於 Linux 的災害復原** | **Azure VM**：新增支援 RedHat Enterprise Linux 6.10;CentOS 6.10。<br/><br/> **VMware Vm**:RedHat Enterprise Linux 6.10; CentOS 6.10.<br/><br/> 使用 GUID 磁碟分割表格 (GPT) 磁碟分割樣式，在傳統 BIOS 相容模式現在也支援以 Linux 為基礎的 Vm。
**雲端支援** | 支援在德國雲端的 Azure Vm 的災害復原。
**跨訂用帳戶的災害復原** | 將某一個區域中的 Azure Vm 複寫至不同的訂用帳戶，在相同的 Azure Active Directory 租用戶內的另一個區域的支援。 [深入了解](https://aka.ms/cross-sub-blog)。
**Windows Server 2008** | 支援移轉的電腦上執行的 Windows Server 2008 R2/2008年 64 位元和 32 位元。<br/><br/> 僅限移轉 （複寫和容錯移轉）。 不支援容錯回復。

### <a name="update-rollup-27-july-2018"></a>更新彙總套件 27 (7 月 2018)

[更新彙總套件 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery)提供下列更新。

**更新** | **詳細資料**
--- | ---
**提供者和代理程式** | Site Recovery 代理程式和提供者 （如彙總套件中所述） 的更新
**適用於 Linux 的災害復原** | **Azure VM**：Red Hat Enterprise Linux 7.5<br/><br/> **VMware Vm/實體伺服器**:Red Hat Enterprise Linux SUSE Linux Enterprise Server 12 7.5




## <a name="next-steps"></a>後續步驟

透過 [Azure 更新](https://azure.microsoft.com/updates/?product=site-recovery)頁面掌握相關更新的最新訊息。




 









