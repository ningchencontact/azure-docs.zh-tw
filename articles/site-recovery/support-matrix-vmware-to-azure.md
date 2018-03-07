---
title: "用於將 VMware VM 和實體伺服器複寫至 Azure 的 Azure Site Recovery 支援對照表 | Microsoft Docs"
description: "摘要說明使用 Azure Site Recovery 將 VMware VM 複寫到 Azure 所支援的作業系統和元件。"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 01/11/2018
ms.author: raynew
ms.openlocfilehash: 31754cd765c90b9e36d16dc766b0a3546e6fd93e
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2018
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>將 VMware 和實體伺服器複寫至 Azure 的支援對照表


本文摘要說明使用 [Azure Site Recovery](site-recovery-overview.md) 將 VMware VM 災害復原至 Azure 所支援的元件和設定。


## <a name="supported-scenarios"></a>支援的案例

**案例** | **詳細資料**
--- | ---
**VMware VM** | 您可以針對內部部署 VMware VM 執行災害復原至 Azure。 您可以在 Azure 入口網站中或使用 PowerShell 來部署此案例。
**實體伺服器** | 您可以針對內部部署 Windows/Linux 實體伺服器執行災害復原至 Azure。 您可以在 Azure 入口網站中部署此案例。

## <a name="on-premises-virtualization-servers"></a>內部部署虛擬化伺服器

**伺服器** | **需求** | **詳細資料**
--- | --- | ---
**VMware** | vCenter Server 6.5、6.0 或 5.5 或 vSphere 6.5、6.0 或 5.5 | 我們建議使用 vCenter 伺服器。
**實體伺服器** | N/A


## <a name="replicated-machines"></a>複寫的電腦

下表摘要說明複寫功能所支援的電腦。 Site Recovery 針對支援的作業系統電腦，支援其上執行的任何工作負載複寫。

**元件** | **詳細資料**
--- | ---
電腦設定 | 複寫到 Azure 的電腦必須符合 [Azure 需求](#failed-over-azure-vm-requirements)。
電腦作業系統 (Windows) | 64 位元 Windows Server 2016 (Server Core，使用桌面體驗的伺服器)、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 (至少加裝 SP1)
電腦作業系統 (Linux) | Red Hat Enterprise Linux：5.2 至 5.11、6.1 至 6.9、7.0 至 7.4 <br/><br/>CentOS：5.2 至 5.11、6.1 至 6.9、7.0 至 7.4 <br/><br/>Ubuntu 14.04 LTS 伺服器 [(支援的核心版本)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Ubuntu 16.04 LTS 伺服器 [(支援的核心版本)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Oracle Enterprise Linux 6.4、6.5，執行 Red Hat 相容核心或 Unbreakable Enterprise Kernel 第 3 版 (UEK3) <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>(不支援 SLES 11 SP3 至 SLES 11 SP4 的複寫電腦升級。 若複寫電腦已從 SLES 11SP3 升級至 SLES 11 SP4，則您必須停用複寫以在升級後重新提供電腦防護。)

>[!NOTE]
>
> - 在 Linux 散發套件上，僅支援屬於散發套件的次要版本/更新的庫存核心。
>
> - 不支援在 Site Recovery 受保護的 VMware 虛擬機器或實體伺服器上對 Linux 散發套件的主要版本進行升級。 升級主要版本 (例如 CentOS 6.\* 至 CentOS 7.\*) 的作業系統時，請停用對機器的複寫，並升級機器的作業系統，然後再次啟用複寫。
>

### <a name="ubuntu-kernel-versions"></a>Ubuntu 核心版本


**支援的版本** | **Azure Site Recovery 行動服務版本** | **核心版本** |
--- | --- | --- |
14.04 LTS | 9.10 | 3.13.0-24-generic 至 3.13.0-121-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-generic 至 3.13.0-128-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-91-generic |
14.04 LTS | 9.12 | 3.13.0-24-generic 至 3.13.0-132-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-96-generic |
14.04 LTS | 9.13 | 3.13.0-24-generic 至 3.13.0-137-generic、<br/>3.16.0-25-generic 至 3.16.0-77-generic、<br/>3.19.0-18-generic 至 3.19.0-80-generic、<br/>4.2.0-18-generic 至 4.2.0-42-generic、<br/>4.4.0-21-generic 至 4.4.0-104-generic |
16.04 LTS | 9.10 | 4.4.0-21-generic 至 4.4.0-81-generic、<br/>4.8.0-34-generic 至 4.8.0-56-generic、<br/>4.10.0-14-generic 至 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-generic 至 4.4.0-91-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-32-generic |
16.04 LTS | 9.12 | 4.4.0-21-generic 至 4.4.0-96-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-35-generic |
16.04 LTS | 9.13 | 4.4.0-21-generic 至 4.4.0-104-generic、<br/>4.8.0-34-generic 至 4.8.0-58-generic、<br/>4.10.0-14-generic 至 4.10.0-42-generic |

## <a name="linux-file-systemsguest-storage-configurations"></a>Linux 檔案系統/客體存放裝置設定

**元件** | **支援**
--- | ---
檔案系統 | ext3、ext4、ReiserFS (僅 Suse Linux Enterprise Server)、XFS
磁碟區管理員 | LVM2
多重路徑軟體 | 裝置對應程式
並行虛擬存放裝置 | 不支援並行虛擬驅動程式所匯出的裝置。
多佇列區塊 IO 裝置 | 不支援。
使用 HP CCISS 儲存體控制器的實體伺服器 | 不支援。
目錄 | 下列目錄 (若設為獨立磁碟分割/檔案系統) 必須都位於來源伺服器的同一個 OS 磁碟：/(root)、/boot、/usr、/usr/local、/var、/etc。 </br></br> 如果 /(root) 磁碟區是 LVM 磁碟區，則 /boot 必須位在同一個磁碟上的獨立磁碟分割，而不是 LVM 磁碟區。<br/><br/>
XFSv5 | 「行動服務」自 9.10 和更新版本開始支援 XFS 檔案系統上的 XFSv5 功能，像是中繼資料總和檢查碼。 使用 xfs_info 公用程式來檢查磁碟分割的 XFS 超級區塊。 若 ftype 設為 1，則代表使用了 XFSv5 功能。



## <a name="network"></a>網路

**元件** | **支援**
--- | ---
主機網路 NIC 小組 | 支援 VMware VM。 <br/><br/>不支援實體機器複寫。
主機網路 VLAN | yes
主機網路 IPv4 | yes
主機網路 IPv6 | 否
客體/伺服器網路 NIC 小組 | 否
客體/伺服器網路 IPv4 | yes
客體/伺服器網路 IPv6 | 否
客體/伺服器網路靜態 IP (Windows) | yes
客體/伺服器網路靜態 IP (Linux) | yes <br/><br/>VM 設定為在容錯回復時使用 DHCP。  
客體/伺服器網路多重 NIC | yes


## <a name="azure-vm-network-after-failover"></a>Azure VM 網路 (容錯移轉後)

**元件** | **支援**
--- | ---
Azure ExpressRoute | yes
ILB | yes
ELB | yes
Azure 流量管理員 | yes
多個 NIC | yes
保留的 IP 位址 | yes
IPv4 | yes
保留來源 IP 位址 | yes
Azure 虛擬網路服務端點<br/><br/> (Azure 儲存體防火牆和虛擬網路) | 否

## <a name="storage"></a>儲存體
**元件** | **支援**
--- | ---
主機 NFS | VMware 為是<br/><br/> 實體伺服器為否。
主機 SAN (ISCSI) | yes
主機多重路徑 (MPIO) | 是，通過 Microsoft DSM、EMC PowerPath 5.7 SP4、EMC PowerPath DSM for CLARiiON 測試
客體/伺服器 VMDK | yes
客體/伺服器 EFI/UEFI| 部分 (僅適用於 Windows Server 2012 和更新版 VMware 虛擬機器的移轉到 Azure 作業) </br></br> 請參閱表格結尾處的附註。
客體/伺服器共用叢集磁碟 | 否
客體/伺服器加密磁碟 | 否
客體/伺服器 NFS | 否
客體/伺服器 SMB 3.0 | 否
客體/伺服器 RDM | yes<br/><br/> 實體伺服器為 N/A
客體/伺服器磁碟 > 1 TB | yes<br/><br/>最多 4,095 GB
客體/伺服器磁碟使用 4K 邏輯與 4k 實體磁區大小 | yes
客體/伺服器磁碟使用 4K 邏輯與 512 位元組實體磁區大小 | yes
客體/伺服器磁碟區使用等量磁碟 > 4 TB <br><br/>LVM 邏輯磁碟區管理 | yes
客體/伺服器 - 儲存體空間 | 否
客體/伺服器 熱新增/移除磁碟 | 否
客體/伺服器 - 排除磁碟 | yes
客體/伺服器多重路徑 (MPIO) | N/A

> [!NOTE]
> 將執行 Windows Server 2012 或更新版本的 VMware 虛擬機器進行 UEFI 開機，可移轉到 Azure。 適用以下限制：

> - 僅支援移轉到 Azure。 不支援容錯回復至內部部署 VMware 網站。
> - 伺服器的 OS 磁碟上不應有超過 4 個磁碟分割。
> - 需要行動服務 9.13 版或更新版本。
> - 實體伺服器不支援。

## <a name="azure-storage"></a>Azure 儲存體

**元件** | **支援**
--- | ---
LRS | yes
GRS | yes
RA-GRS | yes
非經常性儲存體 | 否
經常性存取儲存體| 否
區塊 Blob | 否
待用加密 (SSE)| yes
進階儲存體 | yes
匯入/匯出服務 | 否
虛擬網路服務端點<br/><br/> 設定在目標儲存體/快取儲存體帳戶 (用於儲存複寫資料) 上的儲存體防火牆和虛擬網路 | 否
一般用途 v2 儲存體帳戶 (經常性存取層和非經常性存取層) | 否

## <a name="azure-compute"></a>Azure 計算

**功能** | **支援**
--- | ---
可用性設定組 | yes
中樞 | yes   
受控磁碟 | yes

## <a name="azure-vm-requirements"></a>Azure VM 需求

您複寫到 Azure 的內部部署 VM 必須符合此表中摘要說明的 VM 需求。

**元件** | **需求** | **詳細資料**
--- | --- | ---
**客體作業系統** | 確認[支援的作業系統](#replicated machines)。 | 若不支援，則必要條件檢查會失敗。
**客體作業系統架構** | 64 位元 | 若不支援，則必要條件檢查會失敗。
**作業系統磁碟大小** | 最多 2,048 GB | 若不支援，則必要條件檢查會失敗。
**作業系統磁碟計數** | 1 | 若不支援，則必要條件檢查會失敗。
**資料磁碟計數** | 如果您複寫 *VMware VM 至 Azure*，計數為 64 或以下。 如果您複寫 *Hyper-V VM 至 Azure*，計數為 16 或以下。 | 若不支援，則必要條件檢查會失敗。
**資料磁碟 VHD 大小** | 最多 4,095 GB | 若不支援，則必要條件檢查會失敗。
**網路介面卡** | 支援多個介面卡。 |
**共用 VHD** | 不支援。 | 若不支援，則必要條件檢查會失敗。
**FC 磁碟** | 不支援。 | 若不支援，則必要條件檢查會失敗。
**硬碟格式** | VHD  <br/><br/> VHDX | 雖然 Azure 目前不支援 VHDX，但 Site Recovery 會在您容錯移轉至 Azure 時，自動將 VHDX 轉換為 VHD。 當您容錯回復到內部部署時，虛擬機器仍會繼續使用 VHDX 格式。
**BitLocker** | 不支援。 | 為電腦啟用複寫之前必須先停用 BitLocker。
**VM 名稱** | 從 1 到 63 個字元。<br/><br/> 只能使用字母、數字和連字號。<br/><br/> 電腦名稱必須以字母或數字為開頭或結尾。 | 更新 Site Recovery 中電腦屬性的值。
**VM type** | 第 1 代。<br/><br/> 第 2 代--Windows。 | OS 磁碟基本類型的第 2 代 VM (其中包含一或兩個格式化為 VHDX 的資料磁碟區) 且支援小於 300 GB 的磁碟空間。<br></br>不支援 Linux 第 2 代 VM。 若要深入了解，請參閱[災害復原至 Azure Enhanced](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)。|

## <a name="vault-tasks"></a>保存庫工作

**Action** | **支援**
--- | ---
在資源群組間移動保存庫<br/><br/> 內及跨訂用帳戶 | 否
跨資源群組間移動儲存體、網路、Azure VM<br/><br/> 內及跨訂用帳戶 | 否


## <a name="mobility-service"></a>行動服務

**名稱** | **說明** | **最新版本** | **詳細資料**
--- | --- | --- | --- | ---
**Azure Site Recovery 統一安裝** | 協調內部部署 VMware 伺服器與 Azure 之間的通訊  <br/><br/> 安裝在內部部署 VMware 伺服器上 | 9.12.4653.1 (可從入口網站取得) | [最新功能和修正](https://aka.ms/latest_asr_updates)
**行動服務** | 協調內部部署 VMware 伺服器/實體伺服器和 Azure/次要站台間複寫<br/><br/> 安裝於 VMware VM 上或您想要複寫的實體伺服器上 | 9.12.4653.1 (可從入口網站取得) | [最新功能和修正](https://aka.ms/latest_asr_updates)


## <a name="next-steps"></a>後續步驟
[了解如何](tutorial-prepare-azure.md)準備 Azure 的 VMware VM 災害復原。
