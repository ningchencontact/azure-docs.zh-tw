---
title: 使用 Azure Site Recovery 進行從 VMware VM 和實體伺服器至 Azure 之災害復原的支援矩陣 | Microsoft Docs
description: 摘要說明 VMware Vm 和實體伺服器至 Azure 中使用 Azure Site Recovery 的災害復原的支援。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: raynew
ms.openlocfilehash: 3ff6a1a52048e805f9236349d4fc8d45a14b78ea
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341443"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>從 VMware VM 和實體伺服器至 Azure 之災害復原的支援矩陣

本文摘要說明支援的元件和設定的 VMware 虛擬機器的災害復原和實體伺服器至 azure [Azure Site Recovery](site-recovery-overview.md)。

- [了解更多](vmware-azure-architecture.md)關於 VMware VM/實體伺服器災害復原架構。
- 請依照我們[教學課程](tutorial-prepare-azure.md)試用災害復原。

## <a name="deployment-scenarios"></a>部署案例

**案例** | **詳細資料**
--- | ---
VMware Vm 的災害復原 | 將內部部署 VMware 虛擬機器複寫至 Azure。 您可以在 Azure 入口網站中或使用 [PowerShell](vmware-azure-disaster-recovery-powershell.md) 來部署此案例。
實體伺服器災害復原 | 將內部部署 Windows/Linux 實體伺服器複寫至 Azure。 您可以在 Azure 入口網站中部署此案例。

## <a name="on-premises-virtualization-servers"></a>內部部署虛擬化伺服器

**伺服器** | **需求** | **詳細資料**
--- | --- | ---
vCenter Server | 6\.7、 6.5、 6.0 或 5.5 版 | 我們建議您在災害復原部署中使用 vCenter 伺服器。
vSphere 主機 | 6\.7、 6.5、 6.0 或 5.5 版 | 我們建議 vSphere 主機與 vCenter 伺服器應位於和處理序伺服器相同的網路中。 根據預設處理序伺服器會在組態伺服器上執行。 [深入了解](vmware-physical-azure-config-process-server-overview.md)。


## <a name="site-recovery-configuration-server"></a>Site Recovery 組態伺服器

組態伺服器屬於內部部署機器，可執行 Site Recovery 元件，包括組態伺服器、處理序伺服器和主要目標伺服器。

- 適用於 VMware Vm，您可以下載的 OVF 範本來建立 VMware VM 設定組態伺服器。
- 針對實體伺服器，您在設定伺服器電腦手動設定。

**元件** | **需求**
--- |---
CPU 核心 | 8
RAM | 16 GB
磁碟數量 | 3 個磁碟<br/><br/> 磁碟包括作業系統磁碟、處理序伺服器快取磁碟和用於容錯回復的保留磁碟機。
磁碟可用空間 | 600 GB 的處理序伺服器快取的空間。
磁碟可用空間 | 600 GB 的空間，用於保留磁碟機。
作業系統  | Windows Server 2012 R2 或含桌面體驗的 Windows Server 2016 |
作業系統地區設定 | 英文 (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | 不需要設定伺服器版本[9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)或更新版本。 
Windows Server 角色 | 未啟用 Active Directory 網域服務;Internet Information Services (IIS) 或 HYPER-V。 
群組原則| - 防止存取命令提示字元。 <br/> - 防止存取登錄編輯工具。 <br/> - 檔案附件的信任邏輯。 <br/> - 開啟指令碼執行。 <br/> - [了解更多](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | 請確定您已執行下列動作：<br/><br/> - 沒有預先存在的預設網站 <br/> - 啟用[匿名驗證](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - 啟用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 設定  <br/> - 沒有預先存在的網站/應用程式接聽連接埠 443<br/>
NIC 類型 | VMXNET3 (部署為 VMware VM 時)
IP 位址類型 | Static
連接埠 | 443 用於控制通道協調流程<br/>9443 資料傳輸

## <a name="replicated-machines"></a>複寫的電腦

Site Recovery 支援複寫任何執行於所支援機器上的工作負載。

**元件** | **詳細資料**
--- | ---
機器設定 | 複寫到 Azure 的電腦必須符合 [Azure 需求](#azure-vm-requirements)。
機器工作負載 | Site Recovery 支援複寫任何執行於所支援機器上的工作負載。 [深入了解](https://aka.ms/asr_workload)。
Windows | Windows Server 2019 (支援從[更新彙總套件 34](https://support.microsoft.com/help/4490016) （行動服務版本 9.22） 及更新版本。<br/> Windows Server 2016 （64 位元伺服器核心、 含有桌面體驗的伺服器）<br/> - Windows Server 2012 R2, Windows Server 2012<br/> Windows Server 2008 R2 含在至少為 SP1。<br/> -Windows Server 2008，64 與 32 位元與在至少含 SP2]。 支援只移轉。 [深入了解](migrate-tutorial-windows-server-2008.md)。<br/> Windows 10，Windows 8.1，Windows 8，Windows 7 64 位元 (支援從[更新彙總套件 36](https://support.microsoft.com/help/4503156) （9.22 行動服務版本及更新版本）。 不支援 Windows 7 RTM。 
Linux | 只有 64 位元系統則支援。 不支援 32 位元系統。<br/><br/>每一部 Linux 伺服器應有[Linux Integration Services (LIS) 元件](https://www.microsoft.com/download/details.aspx?id=55106)安裝。 需要在測試容錯移轉/容錯移轉之後，在 Azure 中開機伺服器。 如果 LIS 元件遺失，請務必安裝[元件](https://www.microsoft.com/download/details.aspx?id=55106)之前啟用要在 Azure 中開機之電腦的複寫。 <br/><br/> Site Recovery 會協調容錯移轉以在 Azure 中執行 Linux 伺服器。 不過，Linux 廠商可能會將支援僅限於生命週期尚未結束的發行版本。<br/><br/> 在 Linux 散發套件上，僅支援屬於散發套件次要版本/更新的庫存核心。<br/><br/> 不支援升級各主要 Linux 散發套件版本的受保護機器。 若要升級，請停用複寫、升級作業系統，然後再次啟用複寫。<br/><br/> [了解更多](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)有關適用於 Linux 和開放原始碼技術，在 Azure 中的支援。
Linux Red Hat Enterprise | centos:5.2 至 5.11</b><br/> 若要 6.10 6.1</b><br/> 若要 7.6 7.0<br/> <br/> 不需要伺服器執行 Red Hat Enterprise Linux 5.2-5.11 6.1 6.10 [Linux Integration Services (LIS) 元件](https://www.microsoft.com/download/details.aspx?id=55106)預先安裝。 請務必安裝[元件](https://www.microsoft.com/download/details.aspx?id=55106)之前啟用要在 Azure 中開機之電腦的複寫。
Linux：CentOS | centos:5.2 至 5.11</b><br/> 若要 6.10 6.1</b><br/> 若要 7.6 7.0<br/> <br/> 不需要伺服器執行 CentOS 5.2-5.11 6.1 6.10 [Linux Integration Services (LIS) 元件](https://www.microsoft.com/download/details.aspx?id=55106)預先安裝。 請務必安裝[元件](https://www.microsoft.com/download/details.aspx?id=55106)之前啟用要在 Azure 中開機之電腦的複寫。
Ubuntu | Ubuntu 14.04 LTS 伺服器[（檢閱支援的核心版本）](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS 伺服器[（檢閱支援的核心版本）](#ubuntu-kernel-versions)
Debian | Debian 7/debian 8 [（檢閱支援的核心版本）](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1、 SP2、 SP3、 SP4 [（檢閱支援的核心版本）](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3、SUSE Linux Enterprise Server 11 SP4<br/> 不支援從 SUSE Linux Enterprise Server 11 SP3 的複寫的機器升級到 SP4。 若要升級，停用複寫，並在升級之後重新啟用。
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6<br/><br/> 執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3、 4 和 5 （UEK3、 UEK4 UEK5） 


### <a name="ubuntu-kernel-versions"></a>Ubuntu 核心版本


**支援的版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
14.04 LTS | [9.24][9.25 UR]  | 3.13.0-169-generic 的 3.13.0-24-generic<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-146-generic 的 4.4.0-21-generic<br/>若要 4.15.0-1042-azure 4.15.0-1023-azure |
14.04 LTS | [9.24][9.24 UR] | 3.13.0-167-generic 的 3.13.0-24-generic<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-143-generic 的 4.4.0-21-generic<br/>若要 4.15.0-1040-azure 4.15.0-1023-azure |
14.04 LTS | [9.23][9.23 UR] | 3.13.0-165-generic 的 3.13.0-24-generic<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-142-generic 的 4.4.0-21-generic<br/>若要 4.15.0-1037-azure 4.15.0-1023-azure |
14.04 LTS | [9.22][9.22 UR] | 3.13.0-24-generic 至 3.13.0-164-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-140-generic、<br/>4.15.0-1023-azure 至 4.15.0-1036-azure |
|||
16.04 LTS | [9.25][9.25 UR] | 4.4.0-146-generic 的 4.4.0-21-generic<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>若要 4.15.0-48-generic 4.15.0-13-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>若要 4.15.0-1042-azure 4.15.0-1012-azure|
16.04 LTS | [9.24][9.24 UR] | 4.4.0-143-generic 的 4.4.0-21-generic<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>若要 4.15.0-46-generic 4.15.0-13-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>若要 4.15.0-1040-azure 4.15.0-1012-azure|
16.04 LTS | [9.23][9.23 UR] | 4.4.0-142-generic 的 4.4.0-21-generic<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>若要 4.15.0-45-generic 4.15.0-13-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>若要 4.15.0-1037-azure 4.15.0-1012-azure|
16.04 LTS | [9.22][9.22 UR] | 4.4.0-21-generic 至 4.4.0-140-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-43-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 至 4.15.0-1036-azure|

### <a name="debian-kernel-versions"></a>Debian 核心版本


**支援的版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
Debian 7 | [9.21][9.21 UR], [9.22][9.22 UR]，[9.23][9.23 UR]， [9.24][9.24 UR]| 3.2.0-4-amd64 至 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.25][9.25 UR] | 3.16.0-4-amd64 to 3.16.0-8-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.8-amd64 |
Debian 8 | [9.22][9.22 UR],[9.23][9.23 UR]， [9.24][9.24 UR] | 3.16.0-4-amd64 至 3.16.0-7-amd64、4.9.0-0.bpo.4-amd64 至 4.9.0-0.bpo.8-amd64 |


### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 支援的核心版本

**版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 SP1、 SP2、 SP3 (SP4） | [9.25][9.25 UR] | SP1 3.12.49-11-default 至 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 至 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 至 4.4.120-92.70-default</br></br>若要 4.4.121-92.104-default SP2(LTSS) 4.4.121-92.73-default</br></br>若要 4.4.176-94.88-default SP3 4.4.73-5-default</br></br>若要 4.4.176-4.25-azure SP3 4.4.138-4.7-azure</br></br>若要 4.12.14-95.13-default SP4 4.12.14-94.41-default</br>若要 4.12.14-6.9-azure SP4 4.12.14-6.3-azure |
SUSE Linux Enterprise Server 12 SP1、 SP2、 SP3 (SP4） | [9.24][9.24 UR] | SP1 3.12.49-11-default 至 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 至 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 至 4.4.120-92.70-default</br></br>若要 4.4.121-92.101-default SP2(LTSS) 4.4.121-92.73-default</br></br>若要 4.4.175-94.79-default SP3 4.4.73-5-default</br></br>若要 4.12.14-95.6-default SP4 4.12.14-94.41-default |
SUSE Linux Enterprise Server 12 SP1、 SP2、 SP3 (SP4） | [9.23][9.23 UR] | SP1 3.12.49-11-default 至 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 至 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 至 4.4.120-92.70-default</br></br>若要 4.4.121-92.101-default SP2(LTSS) 4.4.121-92.73-default</br></br>SP3 4.4.73-5-default 至 4.4.162-94.69-default</br></br>若要 4.12.14-95.6-default SP4 4.12.14-94.41-default |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3) | [9.22][9.22 UR] | SP1 3.12.49-11-default 至 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 至 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 至 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default 至 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default 至 4.4.162-94.72-default |


## <a name="linux-file-systemsguest-storage"></a>Linux 檔案系統/客體儲存體

**元件** | **支援**
--- | ---
檔案系統 | ext3、ext4、XFS
磁碟區管理員 | -支援 LVM。<br/> -/ 從支援開機上 LVM[更新彙總套件 31](https://support.microsoft.com/help/4478871/) （行動服務版本 9.20） 及更新版本。 它不支援舊版的行動服務。<br/> -不支援多個 OS 磁碟。
並行虛擬存放裝置 | 不支援並行虛擬驅動程式所匯出的裝置。
多佇列區塊 IO 裝置 | 不支援。
使用 HP CCISS 儲存體控制器的實體伺服器 | 不支援。
裝置/掛接點的命名慣例 | 裝置名稱或掛接點名稱應該是唯一名稱。<br/> 請確定沒有兩個裝置/掛接點，有區分大小寫的名稱。 比方說在相同的 VM 做為命名裝置*device1*並*Device1*不受支援。
目錄 | 如果您執行早於版本 9.20 的行動服務版本 (發行[更新彙總套件 31](https://support.microsoft.com/help/4478871/))，則會套用這些限制：<br/><br/> -這些目錄 （如果設定為不同的資料分割/檔案系統） 必須在來源伺服器上相同的 OS 磁碟: / (root)、 /boot、 /usr、 /usr/local、 /var、 /etc。</br> -/Boot 目錄應該位於磁碟分割區，並不是 LVM 磁碟區。<br/><br/> 從版本 9.20 及更新版本，不適用這些限制。 
開機目錄 | -多個開機磁碟的 VM 上支援 <br/> -不支援跨多個磁碟 LVM 磁碟區上的開機。<br/> -不含開機磁碟機器無法複寫。
可用空間需求| /root 分割區上為 2 GB <br/><br/> 安裝資料夾上為 250 MB
XFSv5 | 中繼資料總和檢查碼，例如的 XFS 檔案系統上的 XFSv5 功能是支援 （行動服務 」 9.10 版開始）。<br/> 使用 xfs_info 公用程式來檢查磁碟分割的 XFS 超級區塊。 如果`ftype`設為 1，則 XFSv5 功能正在使用中。
BTRFS | 從支援 BTRFS[更新彙總套件 34](https://support.microsoft.com/help/4490016) （行動服務版本 9.22） 及更新版本。 如果，不支援 BTRFS:<br/><br/> -BTRFS 檔案系統 subvolume 會在啟用保護之後變更。</br> -BTRFS 檔案系統會散佈到多個磁碟。</br> -BTRFS 檔案系統支援 RAID。

## <a name="vmdisk-management"></a>VM/磁碟管理

**Action** | **詳細資料**
--- | ---
在複寫的 VM 上調整磁碟大小 | 支援。
在複寫的 VM 上新增磁碟 | 不支援。<br/> 停用 VM 的複寫、 新增磁碟，然後再重新啟用複寫。

## <a name="network"></a>網路

**元件** | **支援**
--- | ---
主機網路 NIC 小組 | 支援 VMware VM。 <br/><br/>不支援實體機器複寫。
主機網路 VLAN | 是。
主機網路 IPv4 | 是。
主機網路 IPv6 | 沒有。
客體/伺服器網路 NIC 小組 | 沒有。
客體/伺服器網路 IPv4 | 是。
客體/伺服器網路 IPv6 | 沒有。
客體/伺服器網路靜態 IP (Windows) | 是。
客體/伺服器網路靜態 IP (Linux) | 是。 <br/><br/>VM 設定為在容錯回復時使用 DHCP。
客體/伺服器網路多重 NIC | 是。


## <a name="azure-vm-network-after-failover"></a>Azure VM 網路 (容錯移轉後)

**元件** | **支援**
--- | ---
Azure ExpressRoute | 是
ILB | 是
ELB | 是
Azure 流量管理員 | 是
多個 NIC | 是
保留的 IP 位址 | 是
IPv4 | 是
保留來源 IP 位址 | 是
Azure 虛擬網路服務端點<br/> | 是
加速網路 | 否

## <a name="storage"></a>儲存體
**元件** | **支援**
--- | ---
動態磁碟 | OS 磁碟必須是基本磁碟。 <br/><br/>資料磁碟可以是動態磁碟
Docker 磁碟設定 | 否
主機 NFS | VMware 為是<br/><br/> 實體伺服器為否
主機 SAN (iSCSI/FC) | 是
主機 vSAN | VMware 為是<br/><br/> 實體伺服器為 N/A
主機多重路徑 (MPIO) | 是，通過 Microsoft DSM、EMC PowerPath 5.7 SP4、EMC PowerPath DSM for CLARiiON 測試
主機虛擬磁碟區 (VVol) | VMware 為是<br/><br/> 實體伺服器為 N/A
客體/伺服器 VMDK | 是
客體/伺服器共用叢集磁碟 | 否
客體/伺服器加密磁碟 | 否
客體/伺服器 NFS | 否
客體/伺服器 iSCSI | 否
客體/伺服器 SMB 3.0 | 否
客體/伺服器 RDM | 是<br/><br/> 實體伺服器為 N/A
客體/伺服器磁碟 > 1 TB | 是<br/><br/>最多 4,095 GB<br/><br/> 磁碟必須大於 1024 MB。
客體/伺服器磁碟使用 4K 邏輯與 4k 實體磁區大小 | 是
客體/伺服器磁碟使用 4k 邏輯與 512 位元組實體磁區大小 | 是
客體/伺服器磁碟區使用等量磁碟 > 4 TB <br/><br/>邏輯磁碟區管理 (LVM)| 是
客體/伺服器 - 儲存體空間 | 否
客體/伺服器 熱新增/移除磁碟 | 否
客體/伺服器 - 排除磁碟 | 是
客體/伺服器多重路徑 (MPIO) | 否
客體/伺服器 GPT 磁碟分割 | 從支援五個資料分割[更新彙總套件 37](https://support.microsoft.com/help/4508614/) （行動服務版本 9.25） 及更新版本。 四個支援先前。
客體/伺服器 EFI/UEFI 開機 | -當您執行行動服務版本 9.13 或更新版本支援。<br/> -移轉 VMware Vm 或實體伺服器執行 Windows Server 2012 或更新版本支援至 Azure。<br/> -您只可以將 Vm 複寫進行移轉。 不支援容錯回復至內部部署。<br/> -只支援 NTFS 與不支援安全的 UEFI 開機類型。 <br/> 磁碟磁區大小應該是 512 個位元組，每個實體磁區。

## <a name="replication-channels"></a>複寫通道

|**複寫類型**   |**支援**  |
|---------|---------|
|卸載的資料傳輸 (ODX)    |       否  |
|離線植入        |   否      |
| Azure 資料箱 | 否

## <a name="azure-storage"></a>Azure 儲存體

**元件** | **支援**
--- | ---
本地備援儲存體 | 是
異地備援儲存體 | 是
讀取權限異地備援儲存體 | 是
非經常性儲存體 | 否
經常性存取儲存體| 否
區塊 Blob | 否
-待用加密 (SSE)| 是
進階儲存體 | 是
匯入/匯出服務 | 否
適用於 Vnet 的 azure 儲存體防火牆 | 是。<br/> 在 目標儲存體/快取儲存體帳戶 （用來儲存複寫資料） 上設定。
一般用途 v2 儲存體帳戶 （經常性存取與非經常性存取層） | 否

## <a name="azure-compute"></a>Azure 計算

**功能** | **支援**
--- | ---
可用性設定組 | 是
可用性區域 | 否
中樞 | 是
受控磁碟 | 是

## <a name="azure-vm-requirements"></a>Azure VM 需求

內部部署複寫至 Azure 的虛擬機器必須符合此表中摘要說明 Azure VM 需求。 Site Recovery 執行時複寫的必要條件檢查，檢查會失敗，如果某些需求不符合。

**元件** | **需求** | **詳細資料**
--- | --- | ---
客體作業系統 | 驗證複寫的機器[所支援的作業系統](#replicated-machines)。 | 若不支援，則檢查會失敗。
客體作業系統架構 | 64 位元。 | 若不支援，則檢查會失敗。
作業系統磁碟大小 | 最多 2,048 GB。 | 若不支援，則檢查會失敗。
作業系統磁碟計數 | 1 | 若不支援，則檢查會失敗。
資料磁碟計數 | 64 或以下。 | 若不支援，則檢查會失敗。
資料磁碟大小 | 最多 4,095 GB | 若不支援，則檢查會失敗。
網路介面卡 | 支援多個介面卡。 |
共用 VHD | 不支援。 | 若不支援，則檢查會失敗。
FC 磁碟 | 不支援。 | 若不支援，則檢查會失敗。
BitLocker | 不支援。 | 為電腦啟用複寫之前必須先停用 BitLocker。 |
VM 名稱 | 從 1 到 63 個字元。<br/><br/> 只能使用字母、數字和連字號。<br/><br/> 電腦名稱必須以字母或數字為開頭或結尾。 |  更新 Site Recovery 中電腦屬性的值。

## <a name="churn-limits"></a>變換限制

下表提供 Azure Site Recovery 限制。 
- 這些限制會根據我們的測試，但不涵蓋所有可能的應用程式 I/O 組合。
- 實際的結果會隨著您的應用程式 I/O 混合而有所不同。
- 為了獲得最佳結果，我們強烈建議您執行[部署規劃工具](site-recovery-deployment-planner.md)，並執行廣泛的應用程式可讓您測試使用測試容錯移轉，以取得應用程式真正的效能情況。

**複寫目標** | **平均來源磁碟 I/O 大小** |**平均來源磁碟資料變換** | **每日的來源磁碟資料變換總計**
---|---|---|---
標準儲存體 | 8 KB | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 8 KB  | 2 MB/秒 | 每個磁碟 168 GB
進階 P10 或 P15 磁碟 | 16 KB | 4 MB/秒 |  每個磁碟 336 GB
進階 P10 或 P15 磁碟 | 32 KB 或更大 | 8 MB/秒 | 每個磁碟 672 GB
進階 P20、P30、P40 或 P50 磁碟 | 8 KB    | 5 MB/秒 | 每個磁碟 421 GB
進階 P20、P30、P40 或 P50 磁碟 | 16 KB 或更大 |20 MB/秒 | 每個磁碟的 1684 GB


**來源資料變換** | **上限**
---|---
每個 VM 的平均資料變換| 25 MB/秒
VM 上所有磁碟的尖峰資料變換 | 54 MB/秒
處理序伺服器支援的每日資料變換上限 | 2 TB

- 以上是採用 30% I/O 重疊時的平均數字。
- Site Recovery 能夠處理更高的輸送量 (以重疊比為基礎)、較大的寫入大小和實際工作負載 I/O 行為。
- 這些數字採用大約五分鐘的典型積壓。 也就是說，資料上傳之後，便會進行處理並在五分鐘內建立復原點。

## <a name="vault-tasks"></a>保存庫工作

**Action** | **支援**
--- | ---
在資源群組間移動保存庫 | 否
移動保存庫內及跨訂用帳戶 | 否
跨資源群組間移動儲存體、網路、Azure VM | 否
移動儲存體、 網路、 區域內和跨訂用帳戶的 Azure Vm。 | 否


## <a name="obtain-latest-components"></a>取得最新的元件

**名稱** | **說明** | **詳細資料**
--- | --- | ---
組態伺服器 | 安裝在內部。<br/> 協調內部部署 VMware 伺服器或實體機器，與 Azure 之間的通訊。 | - [深入了解](vmware-physical-azure-config-process-server-overview.md)組態伺服器。<br/> - [深入了解](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)升級至最新版本。<br/> - [深入了解](vmware-azure-deploy-configuration-server.md)設定組態伺服器。 
處理序伺服器 | 預設會安裝在組態伺服器上。<br/> 接收複寫資料、 以快取、 壓縮和加密進行最佳化，並將它傳送至 Azure。<br/> 隨著部署規模擴大，您可以新增額外的處理序伺服器，以處理更大的磁碟區的複寫流量。 | - [深入了解](vmware-physical-azure-config-process-server-overview.md)處理序伺服器。<br/> - [深入了解](vmware-azure-manage-process-server.md#upgrade-a-process-server)升級至最新版本。<br/> - [深入了解](vmware-physical-large-deployment.md#set-up-a-process-server)設定向外延展處理序伺服器。
行動服務 | 安裝在 VMware VM 或您想要複寫實體伺服器。<br/> 協調內部部署 VMware 伺服器/實體伺服器和 Azure 之間複寫。| - [深入了解](vmware-physical-mobility-service-overview.md)行動服務。<br/> - [深入了解](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal)升級至最新版本。<br/> 



## <a name="next-steps"></a>後續步驟
[了解如何](tutorial-prepare-azure.md)準備 Azure 的 VMware VM 災害復原。

[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
