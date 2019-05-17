---
title: 使用 Azure Site Recovery 進行從 VMware VM 和實體伺服器至 Azure 之災害復原的支援矩陣 | Microsoft Docs
description: 摘要說明可支援使用 Azure Site Recovery 進行從 VMware VM 和實體伺服器至 Azure 之災害復原的作業系統和元件。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: raynew
ms.openlocfilehash: 2d1999077f6315658dbfd69473ddf5561bd76e0b
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540595"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>從 VMware VM 和實體伺服器至 Azure 之災害復原的支援矩陣

本文摘要說明使用 [Azure Site Recovery](site-recovery-overview.md) 將 VMware VM 災害復原至 Azure 所支援的元件和設定。

若要開始使用 Azure Site Recovery 搭配簡單的部署案例，請瀏覽[教學課程](tutorial-prepare-azure.md)。 您可以在[這裡](vmware-azure-architecture.md)深入了解 Azure Site Recovery 架構。

## <a name="deployment-scenario"></a>部署案例

**案例** | **詳細資料**
--- | ---
VMware Vm 的災害復原 | 將內部部署 VMware 虛擬機器複寫至 Azure。 您可以在 Azure 入口網站中或使用 [PowerShell](vmware-azure-disaster-recovery-powershell.md) 來部署此案例。
實體伺服器災害復原 | 將內部部署 Windows/Linux 實體伺服器複寫至 Azure。 您可以在 Azure 入口網站中部署此案例。

## <a name="on-premises-virtualization-servers"></a>內部部署虛擬化伺服器

**伺服器** | **需求** | **詳細資料**
--- | --- | ---
VMware | vCenter Server 6.5、6.7、6.0 或 5.5 或 vSphere 6.7、6.5、6.0 或 5.5 | 我們建議使用 vCenter 伺服器。<br/><br/> 我們建議 vSphere 主機與 vCenter 伺服器應位於和處理序伺服器相同的網路中。 根據預設，處理序伺服器元件會在組態伺服器上執行，因此這是您在其中設定組態伺服器的網路，除非您設定專用處理序伺服器。
實體 | N/A

## <a name="site-recovery-configuration-server"></a>Site Recovery 組態伺服器

組態伺服器屬於內部部署機器，可執行 Site Recovery 元件，包括組態伺服器、處理序伺服器和主要目標伺服器。 針對以所有需求來設定組態伺服器的 VMware 複寫，請使用 OVF 範本來建立 VMware 虛擬機器。 針對實體伺服器複寫，請手動設定組態伺服器機器。

**元件** | **需求**
--- |---
CPU 核心 | 8
RAM | 16 GB
磁碟數 | 3 個磁碟<br/><br/> 磁碟包括作業系統磁碟、處理序伺服器快取磁碟和用於容錯回復的保留磁碟機。
磁碟可用空間 | 處理序伺服器快取需要 600 GB 的空間。
磁碟可用空間 | 保留磁碟機需要 600 GB 的空間。
作業系統  | Windows Server 2012 R2 或 Windows Server 2016 |
作業系統地區設定 | 英文 (en-us)
PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")不需要設定伺服器版本[9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)。
Windows Server 角色 | 未啟用： <br/> - Active Directory Domain Services <br/>- 網際網路資訊服務 <br/> - Hyper-V |
群組原則| 未啟用： <br/> - 防止存取命令提示字元。 <br/> - 防止存取登錄編輯工具。 <br/> - 檔案附件的信任邏輯。 <br/> - 開啟指令碼執行。 <br/> [深入了解](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | 請確定您已執行下列動作：<br/><br/> - 沒有預先存在的預設網站 <br/> - 啟用[匿名驗證](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - 啟用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 設定  <br/> - 沒有預先存在的網站/應用程式接聽連接埠 443<br/>
NIC 類型 | VMXNET3 (部署為 VMware VM 時)
IP 位址類型 | 靜態
連接埠 | 443 (用於控制通道協調流程)<br/>9443 (用於資料傳輸)

## <a name="replicated-machines"></a>複寫的電腦

Site Recovery 支援複寫任何執行於所支援機器上的工作負載。

**元件** | **詳細資料**
--- | ---
機器設定 | 複寫到 Azure 的電腦必須符合 [Azure 需求](#azure-vm-requirements)。
機器工作負載 | Site Recovery 支援複寫任何執行於所支援機器上的工作負載 (如 Active Directory、SQL server 等)。 [深入了解](https://aka.ms/asr_workload)。
Windows 作業系統 | Windows Server 2019 (從[9.22 版本](service-updates-how-to.md#links-to-currently-supported-update-rollups))、 64 位元 Windows Server 2016 （Server Core、 伺服器含桌面體驗）、 Windows Server 2012 R2、 Windows Server 2012、 Windows Server 2008 R2 含在至少為 SP1。 </br> 從中[9.24 版本](https://support.microsoft.com/en-in/help/4503156)、 64 位元 Windows 10，64 位元 Windows 8.1，64 位元 Windows 8，64 位元 Windows 7 （Windows 7 RTM 不支援）</br>  [至少含 SP2 的 Windows Server 2008 -32 位元和 64 位元](migrate-tutorial-windows-server-2008.md) (僅限移轉)。 </br></br> 不支援 Windows 2016 Nano Server。
Linux 作業系統架構 | 只有 64 位元系統則支援。 不支援 32 位元系統
Linux 作業系統 | Red Hat Enterprise Linux：5.2 至 5.11<b>\*\*</b>、6.1 至 6.10<b>\*\*</b>、7.0 至 7.6 <br/><br/>CentOS：5.2 至 5.11<b>\*\*</b>、6.1 至 6.10<b>\*\*</b>、7.0 至 7.6 <br/><br/>Ubuntu 14.04 LTS 伺服器[（支援的核心版本）](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS 伺服器[（支援的核心版本）](#ubuntu-kernel-versions)<br/><br/>Debian 7/debian 8 [（支援的核心版本）](#debian-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 12 SP1、 SP2、 SP3、 SP4 [（支援的核心版本）](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 11 SP3<b>\*\*</b>、SUSE Linux Enterprise Server 11 SP4 * </br></br>Oracle Linux 6.4、 6.5、 6.6、 6.7、 6.8、 6.9、 6.10、 7.0、 7.1、 7.2、 7.3、 7.4、 7.5、 7.6 執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3、 4 和 5 (UEK3，UEK4，UEK5) <br/><br/></br>- 不支援將所複寫的機器從 SUSE Linux Enterprise Server 11 SP3 升級至 SP4。 若要升級，請停用複寫，然後在升級之後再次加以啟用。</br></br> - [深入了解](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)Azure 中對 Linux 及開放原始碼技術的支援。 Site Recovery 會協調容錯移轉以在 Azure 中執行 Linux 伺服器。 不過，Linux 廠商可能會將支援僅限於生命週期尚未結束的發行版本。<br/><br/> - 在 Linux 發行版本上，僅支援屬於發行版本之次要版本發行/更新的原生核心。<br/><br/> - 不支援跨主要 Linux 發行版本升級受保護的機器。 若要升級，請停用複寫、升級作業系統，然後再次啟用複寫。<br/><br/> - 執行 Red Hat Enterprise Linux 5.2-5.11 或 CentOS 5.2-5.11 的伺服器應該安裝 [Linux Integration Services (LIS) 元件](https://www.microsoft.com/download/details.aspx?id=55106)，如此機器才能在 Azure 中啟動。


### <a name="ubuntu-kernel-versions"></a>Ubuntu 核心版本


**支援的版本** | **Azure Site Recovery 行動服務版本** | **核心版本** |
--- | --- | --- |
14.04 LTS | [9.24][9.24 UR] | 3.13.0-167-generic 的 3.13.0-24-generic<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-143-generic 的 4.4.0-21-generic<br/>若要 4.15.0-1040-azure 4.15.0-1023-azure |
14.04 LTS | [9.23][9.23 UR] | 3.13.0-165-generic 的 3.13.0-24-generic<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-142-generic 的 4.4.0-21-generic<br/>若要 4.15.0-1037-azure 4.15.0-1023-azure |
14.04 LTS | [9.22][9.22 UR] | 3.13.0-24-generic 至 3.13.0-164-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-140-generic、<br/>4.15.0-1023-azure 至 4.15.0-1036-azure |
14.04 LTS | [9.21][9.21 UR] | 3.13.0-24-generic 至 3.13.0-163-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-140-generic、<br/>4.15.0-1023-azure 至 4.15.0-1035-azure |
|||
16.04 LTS | [9.23][9.24 UR] | 4.4.0-143-generic 的 4.4.0-21-generic<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>若要 4.15.0-46-generic 4.15.0-13-generic<br/>4.11.0-1018-azure 的 4.11.0-1009-azure<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>若要 4.15.0-1040-azure 4.15.0-1012-azure|
16.04 LTS | [9.23][9.23 UR] | 4.4.0-142-generic 的 4.4.0-21-generic<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>若要 4.15.0-45-generic 4.15.0-13-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>若要 4.15.0-1037-azure 4.15.0-1012-azure|
16.04 LTS | [9.22][9.22 UR] | 4.4.0-21-generic 至 4.4.0-140-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-43-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 至 4.15.0-1036-azure|
16.04 LTS | [9.21][9.21 UR] | 4.4.0-21-generic 至 4.4.0-140-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-42-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 至 4.15.0-1035-azure|
16.04 LTS | [9.20][9.20 UR] | 4.4.0-21-generic 至 4.4.0-138-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic、<br/>4.11.0-13-generic 至 4.11.0-14-generic、<br/>4.13.0-16-generic 至 4.13.0-45-generic、<br/>4.15.0-13-generic 至 4.15.0-38-generic<br/>4.11.0-1009-azure 至 4.11.0-1016-azure、<br/>4.13.0-1005-azure 至 4.13.0-1018-azure <br/>4.15.0-1012-azure 至 4.15.0-1025-azure|

### <a name="debian-kernel-versions"></a>Debian 核心版本


**支援的版本** | **Azure Site Recovery 行動服務版本** | **核心版本** |
--- | --- | --- |
Debian 7 | [9.21][9.21 UR]， [9.22][9.22 UR]，[9.23][9.23 UR]，[9.24] [9.24 UR]| 3.2.0-4-amd64 至 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.21][9.21 UR]，[9.22][9.22 UR]，[9.23][9.23 UR]，[9.24] [9.24 UR] | 3.16.0-4-amd64 至 3.16.0-7-amd64、4.9.0-0.bpo.4-amd64 至 4.9.0-0.bpo.8-amd64 |


### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 支援的核心版本

**版本** | **行動服務版本** | **核心版本** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 SP1、 SP2、 SP3 (SP4） | [9.24][9.24 UR] | SP1 3.12.49-11-default 至 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 至 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 至 4.4.120-92.70-default</br></br>若要 4.4.121-92.101-default SP2(LTSS) 4.4.121-92.73-default</br></br>若要 4.4.175-94.79-default SP3 4.4.73-5-default</br></br>若要 4.12.14-95.6-default SP4 4.12.14-94.41-default |
SUSE Linux Enterprise Server 12 SP1、 SP2、 SP3 (SP4） | [9.23][9.23 UR] | SP1 3.12.49-11-default 至 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 至 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 至 4.4.120-92.70-default</br></br>若要 4.4.121-92.101-default SP2(LTSS) 4.4.121-92.73-default</br></br>SP3 4.4.73-5-default 至 4.4.162-94.69-default</br></br>若要 4.12.14-95.6-default SP4 4.12.14-94.41-default |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3) | [9.22][9.22 UR] | SP1 3.12.49-11-default 至 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 至 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 至 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default 至 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default 至 4.4.162-94.72-default |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3) | [9.21][9.21 UR] | SP1 3.12.49-11-default 至 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default 至 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 至 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default 至 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default 至 4.4.156-94.72-default |


## <a name="linux-file-systemsguest-storage"></a>Linux 檔案系統/客體儲存體

**元件** | **支援**
--- | ---
檔案系統 | ext3、ext4、XFS
磁碟區管理員 | [9.20 版](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)之前， <br/> 1.適用於 LVM。 <br/> 2./boot LVM 磁碟區上不支援。 <br/> 3.不支援多個 OS 磁碟。<br/><br/>從[9.20 版本](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)上 LVM 的 /boot 及更新版本，才支援。 不支援多個 OS 磁碟。
並行虛擬存放裝置 | 不支援並行虛擬驅動程式所匯出的裝置。
多佇列區塊 IO 裝置 | 不支援。
使用 HP CCISS 儲存體控制器的實體伺服器 | 不支援。
裝置/掛接點的命名慣例 | 裝置名稱或掛接點名稱應該是唯一名稱。 請確定沒有任何兩個裝置/掛接點的名稱僅有大小寫之別。 </br> 範例：不允許將相同虛擬機器的兩個裝置命名為 *device1* 和 *Device1*。
目錄 | [9.20 版](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)之前， <br/> 1.下列目錄 (若設為獨立磁碟分割/檔案系統) 必須都位於來源伺服器的同一個 OS 磁碟：/(root)、/boot、/usr、/usr/local、/var、/etc。</br>2. /boot 應該位於磁碟分割區，而不是 LVM 磁碟區。<br/><br/> 自 [9.20 版](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)及更新版本起，即不適用上述限制。 不支援在跨多個磁碟的 LVM 磁碟區上執行 /boot。
開機目錄 | 不支援在虛擬機器上有多個開機磁碟 <br/><br/> 無法保護不含開機磁碟的機器
可用空間需求| /root 分割區上為 2 GB <br/><br/> 安裝資料夾上為 250 MB
XFSv5 | 自「行動服務」9.10 版開始，支援 XFS 檔案系統上的 XFSv5 功能，例如中繼資料總和檢查碼。 使用 xfs_info 公用程式來檢查磁碟分割的 XFS 超級區塊。 如果`ftype`設為 1，則 XFSv5 功能正在使用中。
BTRFS |9.22 版本中，從 BTRFS 支援，但下列情況除外</br>如果在啟用保護之後變更 BTRFS 檔案系統子磁碟區時，就不會支援 BTRFS。 </br>如果 BTRFS 檔案系統會散佈到多個磁碟，則不會支援 BTRFS。</br>如果 BTRFS 檔案系統支援 RAID，BTRFS 不支援。

## <a name="vmdisk-management"></a>VM/磁碟管理

**動作** | **詳細資料**
--- | ---
在複寫的 VM 上調整磁碟大小 | 支援。
在複寫的 VM 上新增磁碟 | 停用 VM 的複寫、新增磁碟，然後重新啟用複寫。 目前不支援在複寫的 VM 上新增磁碟。

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
Azure ExpressRoute | 有
ILB | 有
ELB | 有
Azure 流量管理員 | 有
多 NIC | 有
保留的 IP 位址 | 有
IPv4 | 有
保留來源 IP 位址 | 有
Azure 虛擬網路服務端點<br/> | 有
加速網路 | 無

## <a name="storage"></a>儲存體
**元件** | **支援**
--- | ---
動態磁碟 | 作業系統磁碟必須是基本磁碟。 <br/><br/>資料磁碟可以是動態磁碟
Docker 磁碟設定 | 無
主機 NFS | VMware 為是<br/><br/> 實體伺服器為否
主機 SAN (iSCSI/FC) | 有
主機 vSAN | VMware 為是<br/><br/> 實體伺服器為 N/A
主機多重路徑 (MPIO) | 是，通過 Microsoft DSM、EMC PowerPath 5.7 SP4、EMC PowerPath DSM for CLARiiON 測試
主機虛擬磁碟區 (VVol) | VMware 為是<br/><br/> 實體伺服器為 N/A
客體/伺服器 VMDK | 有
客體/伺服器共用叢集磁碟 | 無
客體/伺服器加密磁碟 | 無
客體/伺服器 NFS | 無
客體/伺服器 iSCSI | 無
客體/伺服器 SMB 3.0 | 無
客體/伺服器 RDM | 有<br/><br/> 實體伺服器為 N/A
客體/伺服器磁碟 > 1 TB | 有<br/><br/>最多 4,095 GB<br/><br/> 磁碟必須大於 1024 MB。
客體/伺服器磁碟使用 4K 邏輯與 4k 實體磁區大小 | 有
客體/伺服器磁碟使用 4K 邏輯與 512 位元組實體磁區大小 | 有
客體/伺服器磁碟區使用等量磁碟 > 4 TB <br/><br/>邏輯磁碟區管理 (LVM)| 有
客體/伺服器 - 儲存體空間 | 無
客體/伺服器 熱新增/移除磁碟 | 無
客體/伺服器 - 排除磁碟 | 有
客體/伺服器多重路徑 (MPIO) | 無
客體/伺服器 EFI/UEFI 開機 | 當移轉 VMware Vm 或實體伺服器執行 Windows Server 2012 或更新版本支援至 Azure。<br/><br/> 您只能複寫 Vm 移轉。 不支援容錯回復至內部部署。<br/><br/> 伺服器的 OS 磁碟上不應有超過 4 個磁碟分割。<br/><br/> 需要行動服務 9.13 版或更新版本。<br/><br/> 只支援 NTFS。

## <a name="replication-channels"></a>複寫通道

|**複寫類型**   |**支援**  |
|---------|---------|
|卸載的資料傳輸 (ODX)    |       無  |
|離線植入        |   無      |
| Azure 資料箱 | 無


## <a name="azure-storage"></a>Azure 儲存體

**元件** | **支援**
--- | ---
本地備援儲存體 | 有
異地備援儲存體 | 有
讀取權限異地備援儲存體 | 有
非經常性儲存體 | 無
經常性存取儲存體| 無
區塊 Blob | 無
待用資料加密 (儲存體服務加密)| 有
進階儲存體 | 有
匯入/匯出服務 | 無
設定在目標儲存體/快取儲存體帳戶 (用於儲存複寫資料) 上適用於虛擬網路的 Azure 儲存體防火牆 | 有
一般用途 v2 儲存體帳戶 (經常性存取層和非經常性存取層) | 無

## <a name="azure-compute"></a>Azure 計算

**功能** | **支援**
--- | ---
可用性設定組 | 有
可用性區域 | 無
中樞 | 有
受控磁碟 | 有

## <a name="azure-vm-requirements"></a>Azure VM 需求

您複寫到 Azure 的內部部署 VM 必須符合此表中摘要說明的 VM 需求。 Site Recovery 在執行必要條件檢查時，會於有一些需求不符合時失敗。

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

## <a name="azure-site-recovery-churn-limits"></a>Azure Site Recovery 的變換限制

下表提供 Azure Site Recovery 限制。 上述限制是以我們的測試為基礎，但無法涵蓋所有可能的應用程式 I/O 組合。 實際的結果會隨著您的應用程式 I/O 混合而有所不同。 為了獲得最佳結果，我們強烈建議[執行部署規劃工具](site-recovery-deployment-planner.md)和執行廣泛的應用程式藉由發出測試容錯移轉來測試以取得應用程式的真正的效能情況。

**複寫儲存體目標** | **平均來源磁碟 I/O 大小** |**平均來源磁碟資料變換** | **每日的來源磁碟資料變換總計**
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

以上是採用 30% I/O 重疊時的平均數字。 Site Recovery 能夠處理更高的輸送量 (以重疊比為基礎)、較大的寫入大小和實際工作負載 I/O 行為。 先前數字採用大約五分鐘的典型積壓。 也就是說，資料上傳之後，便會進行處理並在五分鐘內建立復原點。

## <a name="vault-tasks"></a>保存庫工作

**動作** | **支援**
--- | ---
在資源群組間移動保存庫<br/><br/> 订阅内和跨订阅移动 | 無
跨資源群組間移動儲存體、網路、Azure VM<br/><br/> 內及跨訂用帳戶 | 無


## <a name="download-latest-azure-site-recovery-components"></a>下載最新的 Azure Site Recovery 元件

**名稱** | **說明** | **最新版本下載指示**
--- | --- | ---
設定伺服器 | 協調內部部署 VMware 伺服器與 Azure 之間的通訊  <br/><br/> 安裝在內部部署 VMware 伺服器上 | 如需詳細資訊，請造訪我們的指引[全新安裝](vmware-azure-deploy-configuration-server.md)並[升級至最新版本的現有元件的](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)。
處理伺服器|預設會安裝在組態伺服器上。 負責接收複寫資料，以快取、壓縮和加密進行最佳化，然後將複寫資料傳送至 Azure 儲存體。 隨著部署規模擴大，您可以新增額外的個別處理序伺服器，以處理日較大的複寫流量。| 如需詳細資訊，請造訪我們的指引[全新安裝](vmware-azure-set-up-process-server-scale.md)並[升級至最新版本的現有元件的](vmware-azure-manage-process-server.md#upgrade-a-process-server)。
行動服務 | 協調內部部署 VMware 伺服器/實體伺服器和 Azure/次要站台間複寫<br/><br/> 安裝於 VMware VM 上或您想要複寫的實體伺服器上 | 如需詳細資訊，請造訪我們的指引[全新安裝](vmware-azure-install-mobility-service.md)並[升級至最新版本的現有元件的](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal)。

若要深入了解最新的功能，請瀏覽[最新版本資訊](https://aka.ms/ASR_latest_release_notes)。


## <a name="next-steps"></a>後續步驟
[了解如何](tutorial-prepare-azure.md)準備 Azure 的 VMware VM 災害復原。

[9.23 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
