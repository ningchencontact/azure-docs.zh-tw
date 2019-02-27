---
title: Azure VM 備份的 Azure 備份支援矩陣
description: 摘要說明使用 Azure 備份服務來備份 Azure VM 時的支援設定和限制。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: b99d6285942bafe5467827c30b5ba2e42094fdf3
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56431052"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Azure VM 備份的支援矩陣
您可以使用 [Azure 備份服務](backup-overview.md)來備份內部部署機器與工作負載，以及 Azure VM。 本文將摘要說明使用 Azure 備份來備份 Azure 虛擬機器 (VM) 時的支援設定和限制。

其他支援矩陣：

- Azure 備份的[一般支援矩陣](backup-support-matrix.md)
- Microsoft Azure 備份伺服器/DOM 備份的[支援矩陣](backup-support-matrix-mabs-dpm.md)
- 使用 MARS 代理程式進行備份時的[支援矩陣](backup-support-matrix-mars-agent.md)



## <a name="supported-scenarios"></a>支援的案例

以下說明如何使用 Azure 備份服務來備份和還原 Azure VM。


**案例** | **備份** | **代理程式** |**Restore** 
--- | --- | --- | ---
**直接備份 Azure VM** | 備份整個 VM  | Azure VM 上不需要代理程式。 Azure 備份會對執行於 VM 上的 [Azure VM 代理程式](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)安裝並使用擴充功能。 | 以下列方式進行還原：<br/><br/> - **建立基本 VM**。 如果 VM 沒有特殊組態 (例如多個 IP 位址)，這將有其效用。<br/><br/> - **還原 VM 磁碟**。 還原磁碟。 然後，將其連結至現有 VM，或使用 PowerShell 從磁碟建立新的 VM。<br/><br/> - **取代 VM 磁碟**。 如果有 VM 存在，且該 VM 使用受控磁碟 (未加密)，您可以還原某個磁碟，並用它來取代 VM 上現有的磁碟。<br/><br/> - **還原特定檔案/資料夾**。 您可以還原 VM 中的檔案/資料夾，而不是整個 VM。  
**直接備份 Azure VM (僅限 Windows)** | 備份特定檔案/資料夾/磁碟區 | 安裝 [Microsoft Azure 復原服務 (MARS) 代理程式](backup-azure-file-folder-backup-faq.md)。<br/><br/> 您可以將 MARS 代理程式與 Azure VM 代理程式的備份擴充功能一起執行，以在檔案/資料夾層級備份 VM。 | 還原特定資料夾/檔案。
**將 Azure VM 備份至備份伺服器** |  將檔案/資料夾/磁碟區、系統狀態/裸機檔案、應用程式資料備份至 System Center DPM 或 Microsoft Azure 備份伺服器 (MAB 伺服器)。<br/><br/> 然後，DPM/MABS 再備份至備份保存庫 | 在 VM 上安裝 DPM/MABS 保護代理程式。 在 DPM/MABS 上安装 MARS 代理程式。| 還原檔案/資料夾/磁碟區、系統狀態/裸機檔案、應用程式資料。 

深入了解使用備份伺服器的備份 (backup-architecture.md#architecture-back-up-to-dpmmabs) 以及[支援需求](backup-support-matrix-mabs-dpm.md)。


## <a name="supported-backup-actions"></a>支援的備份動作

**Action** | **支援**
--- | ---
建立 Windows Azure VM 時啟用備份 | 支援：Windows Server 2016 (Datacenter/Datacenter Core)、Windows Server 2012 R2 DataCenter、Windows Server 2008 R2 SP1
建立 Linux VM 時啟用備份 | 支援：<br/><br/> - Ubuntu Server：1710、1704、1604 (LTS)、1404 (LTS)<br/><br/> - Red Hat：RHEL 6.7、6.8、6.9、7.2、7.3、7.4<br/><br/> - SUSE Linux Enterprise Server：11 SP4、12 SP2、12 SP3<br/><br/> - Debian：8、9<br/><br/> - CentOS：6.9、7.3<br/><br/> Oracle Linux：6.7、6.8、6.9、7.2、7.3
備份已關閉/離線/正在搜尋 VM 的 VM | 支援。<br/><br/> 僅建立絕對一致快照集，而非應用程式一致快照集。
移轉至受控磁碟後備份磁碟。 | 支援。<br/><br/> 備份會繼續運作。 不需採取任何動作。
啟用資源群組鎖定後備份受控磁碟 | 不支援。<br/><br/> 在達到還原點上限時，備份將無法刪除較舊的資源點，且備份會開始失敗。
修改 VM 的備份原則 | 支援。<br/><br/> VM 會使用或新原則中的排程和保留期設定進行備份。 如果延長保留期設定，就會標示現有的復原點，並加以保留。 如果縮短保留期，則會在下一個清除作業中剪除現有的復原點，且最終加以刪除。
取消備份作業 | 在快照集程序中支援。<br/><br/> 在快照集傳輸至保存庫期間不支援。 
將 VM 備份至不同的區域或訂用帳戶 |  不支援。
每日備份 (透過 Azure VM 擴充功能) | 每天執行一次排程備份。<br/><br/> 一天最多可以執行四次隨選備份。
每日備份 (透過 MARS 代理程式) | 每天執行三次排程備份。 
每日備份 (透過 DPM/MABS) | 每天執行兩次排程備份。
每月/每年備份   | 在使用 Azure VM 擴充功能進行備份時不支援。 僅支援每日和每週備份。<br/><br/> 您可以設定依每月/每年的保留期間來保留每日/每週備份的原則。
自動時鐘調整 | 不支援。<br/><br/> Azure 備份在備份 Azure VM 時，不會考量因日光節約變更而自動執行的時鐘調整。<br/><br/>  請視需要手動修改原則。
[混合式備份的安全性功能](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |  不支援停用這些功能。



## <a name="operating-system-support-windows"></a>作業系統支援 (Windows)

下表摘要說明備份 Windows Azure VM 時所支援的作業系統。

**案例** | **OS 支援** 
--- | ---
使用 Azure VM 代理程式擴充功能進行備份 | Windows 用戶端：不支援<br/><br/> Windows Server：Windows Server 2008 R2 或更新版本 
使用 MARS 代理程式進行備份 | [支援的](backup-support-matrix-mars-agent.md#support-for-direct-backups)作業系統。
透過 DPM/MABS 進行備份 | 使用 [MABS](backup-mabs-protection-matrix.md) 和 [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) 進行備份時支援的作業系統。

## <a name="support-for-linux-backup"></a>Linux 備份的支援

以下是您備份 Linux 機器時所支援的項目。

**Action** | **支援**
--- | --- 
使用 Linux Azure VM 代理程式備份 Linux Azure VM | 檔案一致備份。<br/><br/> 使用[自訂指令碼](backup-azure-linux-app-consistent.md)進行應用程式一致備份。<br/><br/> 在還原期間，您可以建立新的 VM、還原某個磁碟並用它來建立 VM，或還原某個磁碟並用它來取代現有 VM 上的磁碟。 您也可以還原個別檔案和資料夾。
使用 MARS 代理程式備份 Linux Azure VM | 不支援。<br/><br/> MARS 代理程式只能安裝在 Windows 機器上。
使用 DPM/MABS 備份 Linux Azure VM | 不支援。

## <a name="operating-system-support-linux"></a>作業系統支援 (Linux)

針對 Azure VM Linux 備份，Azure 備份支援 [Azure 所背書的 Linux 散發套件清單](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。 請注意：

- Azure 備份不支援 Core OS Linux。
- Azure 備份不支援 32 位元作業系統。
- 只要 VM 上已有[適用於 Linux 的 Azure VM 代理程式](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)，並且支援 Python，其他自備 Linux 散發套件即可運作。



## <a name="backup-frequency-and-retention"></a>備份頻率和保留期

**設定** | **限制** 
--- | --- 
每個受保護執行個體的復原點數目上限 (機器/工作負載) | 9999
復原點的到期時間上限 | 沒有限制
備份至保存庫的頻率上限 (Azure VM 擴充功能) | 一天 1 次
備份至保存庫的頻率上限 (MARS 代理程式) | 每天備份三次。
備份至 DPM/MABS 的頻率上限 | 每隔 15 分鐘 (SQL Server)<br/><br/> 一小時一次 (其他工作負載)。
復原點保留期 | 每日、每週、每月、每年。
最大保留期間 | 視備份頻率而定。
DPM/MABS 磁碟上的復原點 | 64 (檔案伺服器)、448 (應用程式伺服器)。<br/><br/> 內部部署 DPM 的磁帶復原點沒有限制。


## <a name="supported-restore-methods"></a>支援的還原方法

**還原方法** | **詳細資料**
--- | ---
**建立新的 VM** | 您可以在還原程序中建立 VM。 <br/><br/> 此選項會啟動基本 VM 並加以執行。 您可以指定 VM 名稱、資源群組、虛擬網路、子網路和儲存體。  
**還原磁碟** | 您可以還原某個磁碟並用它來建立 VM<br/><br/> 選取此選項時，Azure 備份會將資料從保存庫複製到您選取的儲存體帳戶。 還原作業會產生範本。 您可以下載此範本，並用它來指定自訂 VM 設定，然後建立 VM。<br/><br/> 相較於前述用來建立 VM 的選項，此選項可讓您指定更多設定。<br/><br/>
**取代現有的磁碟** | 您可以還原某個磁碟，並以這個還原的磁碟取代 VM 上目前的磁碟。
**還原檔案** | 您可以從選取的復原點來復原檔案。 您可以下載指令碼，用以從復原點掛接 VM 磁碟。 接著，請瀏覽磁碟區以找出您要復原的檔案/資料夾，並在完成後卸載磁碟。

## <a name="support-for-file-level-restore"></a>檔案層級還原的支援

**Restore** | **支援**
--- | --- 
跨作業系統還原檔案 | 您可以在任何與備份的 VM 具有相同 (或相容) 作業系統的機器上還原檔案。 請參閱[相容的作業系統表格](backup-azure-restore-files-from-vm.md#system-requirements)。
還原傳統 VM 上的檔案 | 不支援。 
從加密的 VM 還原檔案 | 不支援。
從有網路限制的儲存體帳戶還原檔案 | 不支援。
使用 Windows 儲存空間還原 VM 上的檔案 | 不支援在相同的 VM 上還原。<br/><br/> 請在相容的 VM 上還原檔案。
使用 LVM/RAID 陣列還原 Linux VM 上的檔案 | 不支援在相同的 VM 上還原。<br/><br/> 請在相容的 VM 上還原。
還原具有特殊網路設定的檔案 | 不支援在相同的 VM 上還原。 <br/><br/> 請在相容的 VM 上還原。

## <a name="support-for-vm-management"></a>VM 管理的支援

下表摘要說明在執行 VM 管理工作 (例如新增或取代 VM 磁碟) 期間的備份支援。

**Restore** | **支援** 
--- | --- 
跨訂用帳戶/區域 (region)/區域 (zone) 進行還原。 | 不支援 
還原至現有的 VM | 使用更換磁碟選項。
在儲存體帳戶已啟用 Azure 儲存體服務加密 (SSE) 的情況下還原磁碟 | 不支援。<br/><br/> 請還原至未啟用 SSE 的帳戶。
還原至混合儲存體帳戶 | 不支援。<br/><br/> 根據儲存體帳戶類型，所有已還原的磁碟都將是進階或標準磁碟，而非混合磁碟。 
使用 ZRS 還原儲存體帳戶 | 不支援
將 VM 直接還原至可用性設定組 | 對於受控磁碟，您可以還原磁碟，並使用範本中的可用性設定組選項。<br/><br/> 不支援非受控磁碟。 對於非受控磁碟，請還原磁碟，然後在可用性設定組中建立 VM。
升級至受控 VM 後還原非受控 VM 的備份 | 支援。<br/><br/> 您可以還原磁碟，然後建立受控 VM。
在 VM 移轉至受控磁碟之前將 VM 還原至還原點 | 支援。<br/><br/> 您可以還原至非受控磁碟 (預設值)、將還原的磁碟轉換為受控磁碟，然後使用受控磁碟建立 VM。
還原已刪除的 VM。 | 支援。<br/><br/> 您可以從復原點還原 VM。
透過入口網站還原屬於多網域控制站 (DC) 組態的 DC VM | 如果您使用 PowerShell 還原磁碟並建立 VM，則支援此作業。
還原不同 VNet 中的 VM |  支援。<br/><br/> VNet 必須位於相同的訂用帳戶和區域中。




## <a name="vm-compute-support"></a>VM 計算支援

**計算** | **支援** 
--- | --- 
VM 大小 |   至少有 2 個 CPU 核心和 1 GB RAM 的任何 Azure VM 大小。<br/><br/> [深入了解](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) 
備份[可用性設定組](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets)中的 VM | 支援。<br/><br/> 您無法使用快速建立 VM 的選項來還原可用性設定組中的 VM。 在還原 VM 時，您必須還原磁碟並用它來部署 VM，或還原磁碟並用它來取代現有的磁碟。 
備份[可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview)中的 VM |  不支援。  
備份使用 [Hybrid Use Benefit (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) 部署的 VM | 支援。
備份在[擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)中部署的 VM |   不支援   
備份從 [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=virtual-machine-images) 部署的 VM<br/><br/> (由 Microsoft 或第三方發佈) |   支援。<br/><br/> VM 必須執行支援的作業系統。<br/><br/> 復原 VM 上的檔案時，只能還原至相容的作業系統 (而非較舊或較新版的作業系統)。
備份從自訂映像 (第三方) 部署的 VM |    支援。<br/><br/> VM 必須執行支援的作業系統。<br/><br/> 復原 VM 上的檔案時，只能還原至相容的作業系統 (而非較舊或較新版的作業系統)。
備份已移轉至 Azure 的 VM   | 支援。<br/><br/> 若要備份 VM，必須在已移轉的機器上安裝 VM 代理程式。 



## <a name="vm-storage-support"></a>VM 儲存體支援

**元件** | **支援**
--- | ---
Azure VM 資料磁碟 | 備份具有 16 個或較少資料磁碟的 VM。
資料磁碟大小 | 個別磁碟最多可達 4095 GB。<br/><br/> 如果您執行最新版的 Azure VM 備份 (也稱為「立即還原」)，則最多可支援 4 TB 的磁碟大小。 [深入了解](backup-instant-restore-capability.md)。
儲存體類型 | 標準 HDD、標準 SSD、進階 SSD <br/><br/> 如果您執行最新版的 Azure VM 備份 (也稱為「立即還原」)，則支援標準 SSD。 [深入了解](backup-instant-restore-capability.md)。
受控磁碟 | 支援
加密磁碟 | 支援。<br/><br/> 可以備份已啟用 Azure 磁碟加密 (ADE) 的 Azure VM (具有或不具 Azure AD 應用程式)。<br/><br/> 加密的 VM 無法在檔案/資料夾層級復原。 您必須復原整個 VM。<br/><br/> 您可以對已受到 Azure 備份保護的 VM 啟用加密。
已啟用寫入加速器的磁碟 | 不支援。<br/><br/> 如果您執行最新版的 Azure VM 備份 (也稱為[立即還原](backup-instant-restore-capability.md))，則可以將已啟用寫入加速器的磁碟排除在備份作業外。
備份已刪除重複資料的磁碟 | 不支援。
將磁碟新增至受保護的 VM | 支援。
在受保護的 VM 上調整磁碟大小 | 支援。

## <a name="vm-network-support"></a>VM 網路支援


**元件** | **支援**
--- | ---
NIC 數目 | 特定 Azure VM 大小所支援的 NIC 數目上限。<br/><br/> 在還原程序中建立 VM 時，系統會建立 NIC。<br/><br/> 已還原 VM 上的 NIC 數目，會與在 VM 啟用保護時的 NIC 數目相對應。 在啟用之後移除 NIC 並不影響計數。
內部/外部負載平衡器 |   支援。 <br/></br> [深入了解](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)如何還原具有特殊網路設定的 VM。
多個保留的 IP 位址 |  支援。 <br/></br> [深入了解](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)如何還原具有特殊網路設定的 VM。
具有多個網路介面卡的 VM  | 支援。 <br/></br> [深入了解](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)如何還原具有特殊網路設定的 VM。
具有公用 IP 位址的 VM    | 支援。<br/><br/> 您必須將現有的公用 IP 位址與 NIC 相關聯，或是在還原完成後建立位址，並將其與 NIC 相關聯。
NIC/子網路上的網路安全性群組 (NSG)。 |   支援。
保留的 IP 位址 (靜態) | 不支援。<br/><br/> 您無法備份使用保留的 IP 位址且未定義端點的 VM。
動態 IP 位址 |    支援。<br/><br/> 如果來源 VM 上的 NIC 使用動態 IP 位址，則已還原 VM 上的 NIC 依預設也會使用動態位址。
流量管理員 | 支援<br/><br/>上也提供本文中使用的原始碼。 如果備份的 VM 位於流量管理員中，則您必須手動將已還原的 VM 新增至相同的流量管理員。 
Azure DNS | 支援。
自訂 DNS |    支援。
透過 HTTP Proxy 建立輸出連線 | 支援。<br/><br/> 不支援已驗證的 Proxy。 
VNet 服務端點  | 支援。<br/><br/> 防火牆和 VNet 儲存體帳戶設定應允許來自所有網路的存取。 



## <a name="vm-securityencryption-support"></a>VM 安全性/加密支援

Azure 備份支援傳輸中和待用資料的加密：

對 Azure 的網路流量：
- 從伺服器到復原服務保存庫的備份流量會使用進階加密標準 256 進行加密。
- 備份資料會透過安全的 HTTPS 連結來傳送。
- 備份資料會在復原服務保存庫中以加密格式儲存。
- 只有您持有可將此資料解除鎖定的複雜密碼。 Microsoft 無法解密在任何時間點備份的資料。
    > [!WARNING]
    > 設定保存庫後，只有您能夠存取加密金鑰。 Microsoft 絕不會持有金鑰複本，也沒有存取金鑰的權限。 如果金鑰遺失，Microsoft 將無法復原備份資料。
資料安全性：
- 備份 Azure VM 時，您必須在虛擬機器*內*設定加密。
- Azure 備份支援 Azure 磁碟加密，其會在 Windows 虛擬機器上使用 BitLocker，而在 Linux 虛擬機器上使用 **dm-crypt**。
- 在後端，Azure 備份會使用 [Azure 儲存體服務加密](../storage/common/storage-service-encryption.md)，以保護待用資料。


**機器** | **傳輸中** | **待用**
--- | --- | ---
不含 DPM/MABS 的內部部署 Windows 機器 | ![yes][green] | ![yes][green]
Azure VM | ![yes][green] | ![yes][green] 
使用 DPM 的內部部署/Azure VM | ![yes][green] | ![yes][green]
使用 MABS 的內部部署/Azure VM | ![yes][green] | ![yes][green]



## <a name="vm-compression-support"></a>VM 壓縮支援

備份支援壓縮備份流量，如下表所說明。 請注意：

- 對於 Azure VM，VM 擴充功能會透過儲存體網路直接從 Azure 儲存體帳戶讀取資料，因此不需要壓縮此流量。
- 如果您使用 DPM 或 MABS，則可以先壓縮資料再將其備份至 DPM/MABS，以節省頻寬。 

**機器** | **壓縮到 MABS DPM (TCP)** | **壓縮到保存庫 (HTTPS)**
--- | --- | ---
不含 DPM/MABS 的內部部署 Windows 機器 | NA | yes
Azure VM | NA | NA
使用 DPM 的內部部署/Azure VM | ![yes][green] | ![yes][green]
使用 MABS 的內部部署/Azure VM | ![yes][green] | ![yes][green]




## <a name="next-steps"></a>後續步驟

- [備份 Azure VM](backup-azure-arm-vms-prepare.md)
- [直接備份 Windows 機器](tutorial-backup-windows-server-to-azure.md)，而不使用備份伺服器。
- [設定 MABS](backup-azure-microsoft-azure-backup.md) 以備份至 Azure，然後將工作負載備份至 MABS。
- [設定 DPM](backup-azure-dpm-introduction.md) 以備份至 Azure，然後將工作負載備份至 DPM。

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png

