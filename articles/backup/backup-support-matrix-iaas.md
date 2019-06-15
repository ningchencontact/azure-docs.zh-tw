---
title: Azure VM 備份的 Azure 備份支援矩陣
description: 摘要說明使用 Azure 備份服務來備份 Azure VM 時的支援設定和限制。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: raynew
ms.openlocfilehash: ef522785d5074187871c25c54deae84b156d69b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743184"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Azure VM 備份的支援矩陣
您可以使用[Azure 備份服務](backup-overview.md)備份內部部署機器和工作負載和 Azure 虛擬機器 (Vm)。 本文摘要說明支援設定和限制，當您使用 Azure 備份的 Azure Vm 備份。

其他支援矩陣：

- [一般的支援矩陣](backup-support-matrix.md)適用於 Azure 備份
- [支援矩陣](backup-support-matrix-mabs-dpm.md)Azure 備份伺服器/system Center Data Protection Manager (DPM) 備份
- [支援矩陣](backup-support-matrix-mars-agent.md)與 Microsoft Azure 復原服務 (MARS) 代理程式的備份

## <a name="supported-scenarios"></a>支援的案例

以下說明如何使用 Azure 備份服務來備份和還原 Azure VM。

**案例** | **備份** | **代理程式** |**Restore**
--- | --- | --- | ---
直接備份 Azure VM  | 備份整個 VM。  | Azure VM 上不需要代理程式。 Azure 備份會安裝並使用的延伸模組[Azure VM 代理程式](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)VM 上執行。 | 以下列方式進行還原：<br/><br/> - **建立基本 VM**。 這非常有用，如果 VM 不有任何特殊的設定，例如多個 IP 位址。<br/><br/> - **還原 VM 磁碟**。 還原磁碟。 然後將它附加至現有的 VM，或使用 PowerShell，從磁碟建立新的 VM。<br/><br/> - **取代 VM 磁碟**。 如果有 VM 存在，且該 VM 使用受控磁碟 (未加密)，您可以還原某個磁碟，並用它來取代 VM 上現有的磁碟。<br/><br/> - **還原特定檔案/資料夾**。 您可以從 VM，而不是從整個 VM 還原檔案/資料夾。
直接備份 Azure vm (僅 Windows)  | 備份特定檔案/資料夾/磁碟區。 | 安裝[Azure 復原服務代理程式](backup-azure-file-folder-backup-faq.md)。<br/><br/> 您可以將 MARS 代理程式與 Azure VM 代理程式的備份擴充功能一起執行，以在檔案/資料夾層級備份 VM。 | 還原特定資料夾/檔案。
將 Azure VM 備份至備份伺服器  | 備份檔案/資料夾/磁碟區;系統狀態/裸機 metal 檔案;System Center DPM 或以 Microsoft Azure 備份伺服器 (MABS) 的應用程式資料。<br/><br/> DPM/MABS 再備份到備份保存庫。 | 在 VM 上安裝 DPM/MABS 保護代理程式。 在 DPM/MABS 上安装 MARS 代理程式。| 還原檔案/資料夾/磁碟區、系統狀態/裸機檔案、應用程式資料。

深入了解備份[使用備份的伺服器](backup-architecture.md#architecture-back-up-to-dpmmabs)和 關於[支援需求](backup-support-matrix-mabs-dpm.md)。

## <a name="supported-backup-actions"></a>支援的備份動作

**Action** | **支援**
--- | ---
建立 Windows Azure VM 時啟用備份 | 支援： <br/><br/> Windows Server 2019 (資料中心/Datacenter Core/Standard) <br/><br/> Windows Server 2016 (資料中心/Datacenter Core/Standard) <br/><br/> Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> Windows Server 2008 R2 （RTM 和 SP1 Standard）
建立 Linux VM 時啟用備份 | 支援：<br/><br/> - Ubuntu Server：18.04 17.10、 17.04，16.04 (LTS)、 14.04 (LTS)<br/><br/> - Red Hat：RHEL 6.7、6.8、6.9、7.2、7.3、7.4<br/><br/> - SUSE Linux Enterprise Server：11 SP4，12 SP2，12 SP3，15 <br/><br/> - Debian：8、9<br/><br/> - CentOS：6.9、7.3<br/><br/> -Oracle Linux:6.7、6.8、6.9、7.2、7.3
備份 VM 關機/離線的 VM | 支援。<br/><br/> 僅建立絕對一致快照集，而非應用程式一致快照集。
備份後移轉至受控磁碟的磁碟 | 支援。<br/><br/> 備份會繼續運作。 不需採取任何動作。
啟用資源群組鎖定後備份受控磁碟 | 不支援。<br/><br/> Azure 備份無法刪除較舊的資源點，且備份會開始失敗時還原點的最大限制為止。
修改 VM 的備份原則 | 支援。<br/><br/> VM 將備份在新原則中使用的排程和保留設定。 如果延長保留期設定，就會標示現有的復原點，並加以保留。 如果它們縮減，就會在下一步 的清除作業中剪除並最終刪除現有的復原點。
取消備份作業 | 在快照集程序中支援。<br/><br/> 在快照集傳輸至保存庫期間不支援。
將 VM 備份至不同的區域或訂用帳戶 |  不支援。
每日備份 (透過 Azure VM 擴充功能) | 每天執行一次排程備份。<br/><br/> 您可以讓每日最多四個隨選備份。
每日備份 (透過 MARS 代理程式) | 每天執行三次排程備份。
每日備份 (透過 DPM/MABS) | 每天執行兩次排程備份。
每月/每年備份   | 在使用 Azure VM 擴充功能進行備份時不支援。 僅支援每日和每週備份。<br/><br/> 您可以設定依每月/每年的保留期間來保留每日/每週備份的原則。
自動時鐘調整 | 不支援。<br/><br/> 備份 VM 時，azure 備份不會自動調整日光節約時間變更。<br/><br/>  請視需要手動修改原則。
[混合式備份的安全性功能](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |  不支援停用的安全性功能。
備份 VM 的機器有時間變更 | 不支援。<br/><br/> 如果機器有時間變更為未來日期時間之後啟用該 VM; 備份不過即使還原時間變更，是不會保證成功的備份。  


## <a name="operating-system-support-windows"></a>作業系統支援 (Windows)

備份 Windows Azure Vm 時下, 表摘要說明支援的作業系統。

**案例** | **OS 支援**
--- | ---
使用 Azure VM 代理程式擴充功能進行備份 | Windows 用戶端：不支援<br/><br/>Windows Server 2019 (資料中心/Datacenter Core/Standard) <br/><br/> Windows Server 2016 (資料中心/Datacenter Core/Standard) <br/><br/> Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> Windows Server 2008 R2 （RTM 和 SP1 Standard）
使用 MARS 代理程式進行備份 | [支援的](backup-support-matrix-mars-agent.md#support-for-direct-backups)作業系統。
使用 DPM/MABS 備份 | 使用 [MABS](backup-mabs-protection-matrix.md) 和 [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) 進行備份時支援的作業系統。

## <a name="support-for-linux-backup"></a>Linux 備份的支援

以下是您備份 Linux 機器時所支援的項目。

**Action** | **支援**
--- | ---
使用 Linux Azure VM 代理程式備份 Linux Azure VM | 檔案一致備份。<br/><br/> 使用[自訂指令碼](backup-azure-linux-app-consistent.md)進行應用程式一致備份。<br/><br/> 在還原期間，您可以建立新的 VM，還原磁碟並使用它來建立 VM 時，或還原磁碟並使用它來取代現有的 VM 上的磁碟。 您也可以還原個別檔案和資料夾。
使用 MARS 代理程式備份 Linux Azure VM | 不支援。<br/><br/> MARS 代理程式只能安裝在 Windows 機器上。
使用 DPM/MABS 備份 Linux Azure VM | 不支援。

## <a name="operating-system-support-linux"></a>作業系統支援 (Linux)

針對 Azure VM Linux 備份，Azure 備份支援 [Azure 所背書的 Linux 散發套件清單](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。 請注意：

- Azure 備份不支援 Core OS Linux。
- Azure 備份不支援 32 位元作業系統。
- 其他攜帶您自己的 Linux 散發套件可能運作，只要[適用於 Linux 的 Azure VM 代理程式](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)可用的 VM 上，且只要支援 Python。
- 如果沒有 Python 安裝 2.7 版，azure 備份不支援 proxy 設定 Linux VM。


## <a name="backup-frequency-and-retention"></a>備份頻率和保留期

**設定** | **限制**
--- | ---
每個受保護的執行個體 （機器/工作負載） 的最大復原點 | 9999.
復原點的到期時間上限 | 沒有限制。
備份至保存庫的頻率上限 (Azure VM 擴充功能) | 一天一次。
備份至保存庫的頻率上限 (MARS 代理程式) | 每天備份三次。
備份至 DPM/MABS 的頻率上限 | SQL Server 每隔 15 分鐘。<br/><br/> 一小時一次針對其他工作負載。
復原點保留期 | 每日、 每週、 每月和每年一次。
最大保留期間 | 視備份頻率而定。
DPM/MABS 磁碟上的復原點 | 檔案伺服器和應用程式伺服器的 448 64。<br/><br/> 內部部署 DPM 的磁帶復原點沒有限制。

## <a name="supported-restore-methods"></a>支援的還原方法

**還原方法** | **詳細資料**
--- | ---
建立新的 VM | 您可以在還原程序中建立 VM。 <br/><br/> 此選項會啟動基本 VM 並加以執行。 您可以指定 VM 名稱、資源群組、虛擬網路、子網路和儲存體。  
還原磁碟 | 您可以還原磁碟，並使用它來建立 VM。<br/><br/> 選取此選項時，Azure 備份會將資料從保存庫複製到您選取的儲存體帳戶。 還原作業會產生範本。 您可以下載此範本，用它來指定自訂的 VM 設定，並建立 VM。<br/><br/> 相較於前述用來建立 VM 的選項，此選項可讓您指定更多設定。<br/><br/>
取代現有的磁碟 | 您可以還原某個磁碟，並以這個還原的磁碟取代 VM 上目前的磁碟。
還原檔案 | 您可以從選取的復原點來復原檔案。 您可以下載指令碼，用以從復原點掛接 VM 磁碟。 您接著瀏覽以尋找您想要復原，而當您完成時，將磁碟取消掛接的檔案/資料夾的磁碟區。

## <a name="support-for-file-level-restore"></a>檔案層級還原的支援

**Restore** | **支援**
--- | ---
跨作業系統還原檔案 | 您可以在任何與備份的 VM 具有相同 (或相容) 作業系統的機器上還原檔案。 請參閱[相容作業系統資料表](backup-azure-restore-files-from-vm.md#system-requirements)。
還原傳統 VM 上的檔案 | 不支援。
從加密的 VM 還原檔案 | 不支援。
從有網路限制的儲存體帳戶還原檔案 | 不支援。
使用 Windows 儲存空間還原 VM 上的檔案 | 不支援在相同的 VM 上還原。<br/><br/> 請在相容的 VM 上還原檔案。
使用 LVM/RAID 陣列還原 Linux VM 上的檔案 | 不支援在相同的 VM 上還原。<br/><br/> 請在相容的 VM 上還原。
還原具有特殊網路設定的檔案 | 不支援在相同的 VM 上還原。 <br/><br/> 請在相容的 VM 上還原。

## <a name="support-for-vm-management"></a>VM 管理的支援

下表摘要說明在 VM 管理工作，例如新增或取代 VM 磁碟的備份的支援。

**Restore** | **支援**
--- | ---
跨訂用帳戶/區域 (region)/區域 (zone) 進行還原。 | 不支援。
還原至現有的 VM | 使用更換磁碟選項。
在儲存體帳戶已啟用 Azure 儲存體服務加密 (SSE) 的情況下還原磁碟 | 不支援。<br/><br/> 請還原至未啟用 SSE 的帳戶。
還原至混合儲存體帳戶 | 不支援。<br/><br/> 根據儲存體帳戶類型，所有已還原的磁碟都將是進階或標準磁碟，而非混合磁碟。
使用區域備援儲存體 (ZRS) 還原到儲存體帳戶 | 支援 (會備份在 2019 年 1 月之後的 vm 和位置[可用性區域](https://azure.microsoft.com/global-infrastructure/availability-zones/)可用)
將 VM 直接還原至可用性設定組 | 受控磁碟，您可以還原磁碟，並使用範本中的 [可用性集] 選項。<br/><br/> 不支援非受控磁碟。 對於非受控磁碟，請還原磁碟，然後在可用性設定組中建立 VM。
升級至受管理的 VM 之後，還原備份的非受控 Vm| 支援。<br/><br/> 您可以還原磁碟，然後建立受控 VM。
在 VM 移轉至受控磁碟之前將 VM 還原至還原點 | 支援。<br/><br/> 您可以還原至非受控磁碟 (預設值)、將還原的磁碟轉換為受控磁碟，然後使用受控磁碟建立 VM。
還原已刪除的 VM。 | 支援。<br/><br/> 您可以從復原點還原 VM。
透過入口網站還原屬於多網域控制站 (DC) 組態的 DC VM | 如果您還原磁碟，並使用 PowerShell 建立 VM 時才受到支援。
在不同的虛擬網路中還原 VM |   支援。<br/><br/> 虛擬網路必須位於相同的訂用帳戶和區域而定。

## <a name="vm-compute-support"></a>VM 計算支援

**計算** | **支援**
--- | ---
VM 大小 |   至少有 2 個 CPU 核心和 1 GB RAM 的任何 Azure VM 大小。<br/><br/> [深入了解。](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
備份[可用性設定組](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets)中的 VM | 支援。<br/><br/> 您無法還原在可用的集合中的 VM 所使用的選項來快速建立 VM。 相反地，當您還原 VM 時，還原磁碟，並使用它來部署 VM，或還原磁碟，並使用它來取代現有的磁碟。
備份[可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview)中的 VM |  不支援。
使用部署的 Vm 備份[Hybrid Use Benefit (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | 支援。
在中部署的 Vm 備份[擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |  不支援。
從部署的 Vm 備份[Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=virtual-machine-images)<br/><br/> （由 Microsoft、 協力廠商發行） |  支援。<br/><br/> VM 必須執行支援的作業系統。<br/><br/> 復原 VM 上的檔案時，只能還原至相容的作業系統 (而非較舊或較新版的作業系統)。 我們不要還原備份做為 Vm，因為這些需求購買資訊，但只為磁碟的 Azure Marketplace Vm。
將自訂映像 （第三方） 從備份部署的 Vm |   支援。<br/><br/> VM 必須執行支援的作業系統。<br/><br/> 復原 VM 上的檔案時，只能還原至相容的作業系統 (而非較舊或較新版的作業系統)。
將 Vm 移轉至 Azure 備份  | 支援。<br/><br/> 若要備份 VM，必須在已移轉的機器上安裝 VM 代理程式。
備份多部 VM 一致性 | Azure 備份不提供跨多個 Vm 的資料和應用程式的一致性。
備份與[診斷設定](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | 不支援。 <br/><br/> 還原 Azure VM 的診斷設定使用來觸發[新建](backup-azure-arm-restore-vms.md#create-a-vm)選項則還原會失敗。


## <a name="vm-storage-support"></a>VM 儲存體支援

**元件** | **支援**
--- | ---
Azure VM 資料磁碟 | 備份具有 16 個或較少資料磁碟的 VM。 <br/><br/> 支援最多 4 TB 磁碟大小。
資料磁碟大小 | 個別磁碟最多可達 4095 GB。<br/><br/> 如果您的保存庫執行最新版的 Azure 備份 （又稱為立即還原 」），磁碟大小最多 4 TB 支援。 [深入了解](backup-instant-restore-capability.md)。  
儲存體類型 | 標準的 HDD 上，標準的 SSD 進階 SSD。 <br/><br/> 如果您的保存庫升級為最新版的 Azure VM 備份 （稱為立即還原 」），則支援標準的 SSD。 [深入了解](backup-instant-restore-capability.md)。
受控磁碟 | 支援。
加密磁碟 | 支援。<br/><br/> 使用 Azure 磁碟加密啟用的 azure Vm 可以備份 （不論有無 Azure AD 應用程式）。<br/><br/> 加密的 VM 無法在檔案/資料夾層級復原。 您必須復原整個 VM。<br/><br/> 您可以對已受到 Azure 備份保護的 VM 啟用加密。
已啟用寫入加速器的磁碟 | 不支援。<br/><br/> 如果您執行最新版的 Azure VM 備份 (也稱為[立即還原](backup-instant-restore-capability.md))，則可以將已啟用寫入加速器的磁碟排除在備份作業外。
備份已刪除重複資料的磁碟 | 不支援。
將磁碟新增至受保護的 VM | 支援。
在受保護的 VM 上調整磁碟大小 | 支援。
共用存放裝置| 不建議備份使用叢集共用磁碟區 (CSV) 或向外延展檔案伺服器的 Vm。 CSV 寫入器都可能會在備份期間失敗項目。 [還原]，包含 CSV 磁碟區的磁碟可能會不來增加。

> [!NOTE]
> Azure 備份，建議您不調整大小的磁碟。


## <a name="vm-network-support"></a>VM 網路支援

**元件** | **支援**
--- | ---
網路介面 (Nic) 數目 | 特定 Azure VM 大小所支援的 NIC 數目上限。<br/><br/> 在還原程序中建立 VM 時，系統會建立 NIC。<br/><br/> 已還原 VM 上的 NIC 數目，會與在 VM 啟用保護時的 NIC 數目相對應。 啟用保護之後，移除 Nic，並不會影響計數。
內部/外部負載平衡器 |   支援。 <br/><br/> [深入了解](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)如何還原具有特殊網路設定的 VM。
多個保留的 IP 位址 |    支援。 <br/><br/> [深入了解](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)如何還原具有特殊網路設定的 VM。
具有多個網路介面卡的 VM  | 支援。 <br/><br/> [深入了解](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)如何還原具有特殊網路設定的 VM。
具有公用 IP 位址的 VM    | 支援。<br/><br/> 現有的公用 IP 位址相關聯的 NIC 時，或建立的位址，並與 NIC 關聯之後還原完成。
NIC/子網路上的網路安全性群組 (NSG)。 |   支援。
保留的 IP 位址 (靜態) | 不支援。<br/><br/> 您無法備份具有保留的 IP 位址並沒有定義之端點的 VM。
動態 IP 位址 |    支援。<br/><br/> 如果來源上的 NIC 的 VM 使用動態 IP 位址，依預設已還原的 VM 上的 NIC 會使用它太。
Azure 流量管理員   | 支援。<br/><br/>如果備份的 VM 是在 「 流量管理員 」 中，手動將還原的 VM 加入相同的流量管理員執行個體。
Azure DNS | 支援。
自訂 DNS |    支援。
透過 HTTP Proxy 建立輸出連線 | 支援。<br/><br/> 不支援已驗證的 Proxy。
虛擬網路服務端點   | 支援。<br/><br/> 防火牆與虛擬網路的儲存體帳戶設定應該允許從所有網路存取。



## <a name="vm-security-and-encryption-support"></a>虛擬機器的安全性和加密的支援

Azure 備份支援傳輸中和待用資料的加密：

對 Azure 的網路流量：

- 從伺服器備份到復原服務保存庫的流量會使用進階加密標準 256 加密。
- 備份資料會透過安全的 HTTPS 連結來傳送。
- 備份資料會在復原服務保存庫中以加密格式儲存。
- 只有您持有可將此資料解除鎖定的複雜密碼。 Microsoft 無法解密在任何時間點備份的資料。

  > [!WARNING]
  > 設定保存庫之後，您只能存取加密金鑰。 Microsoft 絕不會持有金鑰複本，也沒有存取金鑰的權限。 如果金鑰遺失，Microsoft 將無法復原備份資料。

資料安全性：

- 當備份 Azure Vm，您需要設定加密*內*虛擬機器。
- Azure 備份支援 Azure 磁碟加密，在 Windows 虛擬機器，我們會使用 BitLocker **dm crypt** Linux 虛擬機器上。
- 在後端，Azure 備份會使用 [Azure 儲存體服務加密](../storage/common/storage-service-encryption.md)，以保護待用資料。


**機器** | **傳輸中** | **待用**
--- | --- | ---
不含 DPM/MABS 的內部部署 Windows 機器 | ![是][green] | ![是][green]
Azure VM | ![是][green] | ![是][green]
使用 DPM 的內部部署/Azure VM | ![是][green] | ![是][green]
使用 MABS 的內部部署/Azure VM | ![是][green] | ![是][green]



## <a name="vm-compression-support"></a>VM 壓縮支援

Backup 支援備份的資料傳輸的壓縮下, 表摘要說明。 請注意：

- 針對 Azure Vm，VM 延伸模組會讀取的資料儲存體網路直接從 Azure 儲存體帳戶。 您不需要壓縮此流量。
- 如果您使用 DPM 或 MABS，您可以藉由壓縮資料，才能以 DPM/MABS 備份來節省頻寬。

**機器** | **壓縮到 MABS DPM (TCP)** | **壓縮至保存庫 (HTTPS)**
--- | --- | ---
不含 DPM/MABS 的內部部署 Windows 機器 | NA | ![是][green]
Azure VM | NA | NA
使用 DPM 的內部部署/Azure VM | ![是][green] | ![是][green]
使用 MABS 的內部部署/Azure VM | ![是][green] | ![是][green]


## <a name="next-steps"></a>後續步驟

- [Azure Vm 備份](backup-azure-arm-vms-prepare.md)。
- [直接備份 Windows 機器](tutorial-backup-windows-server-to-azure.md)，而不使用備份伺服器。
- [設定 MABS](backup-azure-microsoft-azure-backup.md) 以備份至 Azure，然後將工作負載備份至 MABS。
- [設定 DPM](backup-azure-dpm-introduction.md) 以備份至 Azure，然後將工作負載備份至 DPM。

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
