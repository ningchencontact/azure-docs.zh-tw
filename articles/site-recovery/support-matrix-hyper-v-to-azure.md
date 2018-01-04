---
title: "HYPER-V 複寫 Azure 的支援矩陣 |Microsoft 文件"
description: "摘要說明支援的元件和 HYPER-V 複寫至 Azure Site recovery 的 Azure 需求"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 12/31/2017
ms.author: raynew
ms.openlocfilehash: 5918c56c2b7d01c884bf846e3a7d621b3393bb96
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2018
---
# <a name="support-matrix-for-hyper-v-replication-to-azure"></a>HYPER-V 複寫至 Azure 的支援矩陣


本文摘要說明支援的元件和設定的內部部署 HYPER-V Vm 的災害復原至 Azure 時，使用[Azure Site Recovery](site-recovery-overview.md)服務。


## <a name="supported-scenarios"></a>支援的案例

**案例** | **詳細資料**
--- | --- 
**Hyper-V (含 VMM)** | 您可以在 System Center Virtual Machine Manager (VMM) 網狀架構管理的 HYPER-V 主機上執行的 vm 執行災害復原至 Azure<br/><br/> 您可以部署在 Azure 入口網站，或使用 PowerShell 在此案例。<br/><br/> 當 HYPER-V 主機由 VMM 管理時，您也可以執行災害復原至次要內部部署網站。 讀取[本文](tutorial-vmm-to-vmm.md)若要深入了解這種情況。
**不具備 VMM Hyper-v** | 您可以執行災害復原至 Azure 的 Vm 不由 VMM 管理的 HYPER-V 主機上執行。<br/><br/> 您可以部署在 Azure 入口網站，或使用 Powershell 在此案例。 


## <a name="on-premises-servers"></a>內部部署伺服器

**伺服器** | **需求** | **詳細資料**
--- | --- | ---
**Hyper-v （無 VMM 執行）** | 具有最新更新的 Windows Server 2016、Windows Server 2012 R2。 | 當您在站台復原中設定 HYPER-V 站台時，不支援混用在執行 Windows Server 2016 和 2012 R2 的主機。<br/><br/> 對於執行 Windows Server 2016 的主機上的 Vm，不支援復原到替代位置。
**Hyper-v （執行與 VMM）** | VMM 2016，VMM 2012 R2 | 如果使用 VMM 時，應該 VMM 2016 中管理 Windows Server 2016 的主機。<br/><br/> 目前並不支援混合的 Windows Server 2016 和 2012 R2 上執行的 HYPER-V 主機的 VMM 雲端。<br/><br/> 不支援包含現有 VMM 2012 R2 伺服器至 2016年的升級的環境。


## <a name="replicated-vms"></a>複寫的 Vm


下表摘要說明 VM 支援。 站台復原支援任何支援的作業系統上執行的工作負載。 

 **元件** | **詳細資料**
--- | ---
VM 設定 | 複寫至 Azure 的 Vm 必須符合[Azure 需求](#failed-over-azure-vm-requirements)。
客體作業系統 | 任何客體 OS [Azure 支援](https://technet.microsoft.com/library/cc794868.aspx)。<br/><br/> 不支援 Windows Server 2016 Nano Server。




## <a name="hyper-v-network-configuration"></a>HYPER-V 網路組態

**元件** | **Hyper-V (含 VMM)** | **不具備 VMM Hyper-v**
--- | --- | ---
主機網路： NIC 小組 | 是
主機網路： VLAN | 是
主機網路： IPv4 | 是
主機網路： IPv6 | 否
來賓 VM 網路： NIC 小組 | 否
來賓 VM 網路： IPv4 | 是
來賓 VM 網路： IPv6 | 否
來賓 VM 網路： 靜態 IP (Windows) | 是
來賓 VM 網路： 靜態 IP (Linux) | 否
來賓 VM 網路： 多重-NIC | 是



## <a name="azure-vm-network-configuration-after-failover"></a>Azure VM 網路組態 （容錯移轉後）

**元件** | **Hyper-V (含 VMM)** | **不具備 VMM Hyper-v**
--- | --- | ---
ExpressRoute | 是 | 是
ILB | 是 | 是
ELB | 是 | 是
流量管理員 | 是 | 是
多個 NIC | 是 | 是
保留的 IP | 是 | 是
IPv4 | 是 | 是
保留 來源 IP 位址 | 是 | 是
VNET 服務端點<br/><br/> （azure 儲存體防火牆和虛擬網路） | 否 | 否


## <a name="hyper-v-host-storage"></a>HYPER-V 主機儲存體

**儲存體** | **Hyper-V (含 VMM)** | 不具備 VMM Hyper-v
--- | --- | --- | ---
NFS | NA | NA
SMB 3.0 | 是 | 是
SAN (ISCSI) | 是 | 是
多重路徑 (MPIO)。 使用測試：<br></br> Microsoft DSM，EMC PowerPath 5.7 SP4<br/><br/> CLARiiON 的 EMC PowerPath DSM | 是 | 是

## <a name="hyper-v-vm-guest-storage"></a>HYPER-V VM 客體儲存體

**儲存體** | **Hyper-V (含 VMM)** | 不具備 VMM Hyper-v
--- | --- | ---
VMDK | NA | NA
VHD/VHDX | 是 | 是
第 2 代 VM | 是 | 是
EFI/UEFI| 是 | 是
共用叢集磁碟 | 否 | 否
已加密磁碟 | 否 | 否
NFS | NA | NA
SMB 3.0 | 否 | 否
RDM | NA | NA
磁碟 > 1 TB | 是，最多為 4095 GB | 是，最多為 4095 GB
磁碟： 4k 邏輯與實體磁區 | 不支援： Gen 1 Gen 2 | 不支援： Gen 1 Gen 2
磁碟： 4k 邏輯和 512 個位元組的實體磁區 | 是 |  是
使用等量磁碟的磁碟區 > 1 TB<br/><br/> LVM 邏輯磁碟區管理 | 是 | 是
儲存空間 | 是 | 是
熱新增/移除磁碟 | 否 | 否
排除磁碟 | 是 | 是
多重路徑 (MPIO) | 是 | 是

## <a name="azure-storage"></a>Azure 儲存體

**元件** | **Hyper-V (含 VMM)** | **VMM 沒有 Hyper-V)**
--- | --- | ---
LRS | 是 | 是
GRS | 是 | 是
RA-GRS | 是 | 是
非經常性儲存體 | 否 | 否
經常性存取儲存體| 否 | 否
區塊 Blob | 否 | 否
待用加密 (SSE)| 是 | 是
進階儲存體 | 是 | 是
匯入/匯出服務 | 否 | 否
VNET 服務端點 （Azure 儲存體防火牆和 Vnet），以用於複寫資料的快取儲存體帳戶的目標 | 否 | 否


## <a name="azure-compute-features"></a>Azure 計算功能

**功能** | **Hyper-V (含 VMM)** | **不具備 VMM Hyper-v**
--- | --- | ---
可用性設定組 | 是 | 是
中樞 | 是 | 是  
受控磁碟 | 是，用於容錯移轉<br/><br/> 不支援的受管理的磁碟的容錯回復 | 是，用於容錯移轉<br/><br/> 不支援的受管理的磁碟的容錯回復

## <a name="azure-vm-requirements"></a>Azure VM 的需求

複寫至 Azure 的內部部署 Vm 必須符合此表中摘要說明 Azure VM 需求。

**元件** | **需求** | **詳細資料**
--- | --- | ---
**客體作業系統** | 站台復原支援所有的作業系統[Azure 支援](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)。  | 如果不支援，則先決條件檢查會失敗。
**客體作業系統架構** | 64 位元 | 如果不支援，則先決條件檢查會失敗。
**作業系統磁碟大小** | 高達 2048 GB 用於第 1 代 Vm。<br/><br/> 300 GB 的第 2 代 Vm。  | 如果不支援，則先決條件檢查會失敗。
**作業系統磁碟計數** | 1 | 如果不支援，則先決條件檢查會失敗。
**資料磁碟計數** | 16 或更低  | 如果不支援，則先決條件檢查會失敗。
**資料磁碟 VHD 大小** | 最多 4095 GB | 如果不支援，則先決條件檢查會失敗。
**網路介面卡** | 支援多個介面卡 |
**共用 VHD** | 不支援 | 如果不支援，則先決條件檢查會失敗。
**FC 磁碟** | 不支援 | 如果不支援，則先決條件檢查會失敗。
**硬碟格式** | VHD  <br/><br/> VHDX | Site Recovery 會容錯移轉至 Azure 時，將 VHDX 自動轉換成 VHD。 當您容錯回復到內部部署時，虛擬機器仍會繼續使用 VHDX 格式。
**Bitlocker** | 不支援 | 您為 VM 啟用之前，必須停用 Bitlocker。
**VM 名稱** | 介於 1 到 63 個字元。 只能使用字母、數字和連字號。 VM 名稱必須以字母或數字為開頭或結尾。 | 更新 VM 中的屬性 Site Recovery 中的值。
**VM type** | 第 1 代<br/><br/> 第 2 代 -- Windows | OS 磁碟基本類型的第 2 代 VM (其中包含一或兩個格式化為 VHDX 的資料磁碟區) 且支援小於 300 GB 的磁碟空間。<br></br>不支援 Linux 第 2 代 VM。 [深入了解](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="recovery-services-vault-actions"></a>復原服務保存庫動作

**Action** |  **Hyper-V (含 VMM)** | **不具備 VMM Hyper-v**
--- | --- | --- 
在資源群組間移動保存庫<br/><br/> 內及跨訂用帳戶 | 否 | 否 
跨資源群組間移動儲存體、網路、Azure VM<br/><br/> 內及跨訂用帳戶 | 否 | 否 


## <a name="provider-and-agent"></a>提供者和代理程式

若要確定您的部署相容這篇文章中的設定，請確定您正在執行的最新的提供者和代理程式版本。

**名稱** | **說明** | **詳細資料**
--- | --- | --- | --- | ---
**Azure Site Recovery 提供者** | 協調內部部署伺服器與 Azure 之間的通訊 <br/><br/> 與 VMM 的 HYPER-V: VMM 伺服器上安裝<br/><br/> 無 VMM 的 HYPER-V： 安裝在 HYPER-V 主機上| 最新版本： 5.1.2700.1 （可從入口網站）<br/><br/> [最新功能和修正](https://aka.ms/latest_asr_updates)
**Microsoft Azure 復原服務 (MARS) 代理程式** | 協調 HYPER-V VM 與 Azure 之間的複寫<br/><br/> （不論有無 VMM） 在內部部署 HYPER-V 伺服器上安裝 | 最新的代理程式，可從入口網站






## <a name="next-steps"></a>後續步驟
深入了解如何[準備 Azure](tutorial-prepare-azure.md)災害復原的內部部署 HYPER-V Vm。 
