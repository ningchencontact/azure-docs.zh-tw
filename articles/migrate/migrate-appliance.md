---
title: Azure Migrate 設備架構 |Microsoft Docs
description: 提供 Azure Migrate 設備的總覽
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: c31a9fde50de8190cdd7bc19600344a8e58cf60b
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827332"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 設備

本文說明 Azure Migrate 設備。 當您使用 Azure Migrate 評估和遷移工具來探索、評估和遷移要 Microsoft Azure 的應用程式、基礎結構和工作負載時, 您可以部署設備。 

[Azure Migrate](migrate-services-overview.md)提供中央中樞, 以追蹤內部部署應用程式和工作負載的探索、評估和遷移, 以及私人/公用雲端 Vm 至 Azure。 該中樞能提供 Azure Migrate 工具以進行評估和移轉，也提供協力廠商獨立軟體廠商 (ISV) 的供應項目。



## <a name="appliance-overview"></a>設備總覽

Azure Migrate 的裝置類型和使用方式如下所示。

**部署為** | **用於** | **詳細資料**
--- | --- |  ---
VMware VM | 使用 Azure Migrate 評估工具進行 VMware VM 評估。<br/><br/> 使用 Azure Migrate Server 遷移工具進行 VMware VM 無代理程式遷移 | 下載 OVA 範本, 並匯入 vCenter Server 以建立設備 VM。
Hyper-V VM | 使用 Azure Migrate 評估工具的 hyper-v VM 評估。 | 下載壓縮的 VHD 並匯入至 Hyper-v 以建立設備 VM。

## <a name="appliance-access"></a>設備存取

設定設備之後, 您可以透過 TCP 埠3389從遠端存取應用裝置 VM。 您也可以在埠44368上, 從遠端存取設備的 web 管理應用程式, 網址`https://<appliance-ip-or-name>:44368`為:。

## <a name="appliance-license"></a>設備授權
設備隨附 Windows Server 2016 評估授權, 其有效期為180天。 如果評估期接近到期日, 建議您下載並部署新的應用裝置, 或啟用設備 VM 的作業系統授權。

## <a name="appliance-agents"></a>設備代理程式
設備已安裝這些代理程式。

**代理程式** | **詳細資料**
--- | ---
探索代理程式 | 收集內部部署虛擬機器的設定資料
評定代理程式 | 分析內部部署環境, 以收集 VM 效能資料。
遷移介面卡 | 協調 VM 複寫, 並協調 Vm 與 Azure 之間的通訊。
遷移閘道 | 將已複寫的 VM 資料傳送至 Azure。


## <a name="appliance-deployment-requirements"></a>設備部署需求

- [檢查](migrate-support-matrix-vmware.md#assessment-appliance-requirements)VMware 應用裝置的部署需求, 以及設備需要存取的 url。
- [檢查](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements)hyper-v 設備的部署需求, 以及設備需要存取的 url。


## <a name="collected-performance-data-vmware"></a>收集的效能資料-VMware

以下是設備收集並傳送至 Azure 的 VMware VM 效能資料。

**資料** | **計數器** | **評量影響**
--- | --- | ---
CPU 使用率 | cpu.usage.average | 建議的 VM 大小/成本
記憶體使用量 | mem.usage.average | 建議的 VM 大小/成本
磁片讀取輸送量 (每秒 MB) | virtualDisk.read.average | 磁片大小、儲存體成本、VM 大小的計算
磁片寫入輸送量 (每秒 MB) | virtualDisk.write.average | 磁片大小、儲存體成本、VM 大小的計算
每秒的磁片讀取作業數 | virtualDisk.numberReadAveraged.average | 磁片大小、儲存體成本、VM 大小的計算
每秒的磁片寫入作業數 | virtualDisk.numberReadAveraged.average  | 磁片大小、儲存體成本、VM 大小的計算
NIC 讀取輸送量 (每秒 MB) | net.received.average | VM 大小的計算
NIC 寫入輸送量 (每秒 MB) | net.transmitted.average  |VM 大小的計算


## <a name="collected-metadata-vmware"></a>收集的中繼資料-VMware

以下是設備收集並傳送至 Azure 的 VMware VM 中繼資料完整清單。

**資料** | **計數器**
--- | --- 
**機器詳細資料** | 
VM 識別碼 | vm.Config.InstanceUuid 
VM 名稱 | vm.Config.Name
vCenter Server 識別碼 | VMwareClient.Instance.Uuid
VM 描述 | vm.Summary.Config.Annotation
授權產品名稱 | vm.Client.ServiceContent.About.LicenseProductName
作業系統類型 | vm.SummaryConfig.GuestFullName
開機類型 | vm.Config.Firmware
核心數 | vm.Config.Hardware.NumCPU
記憶體 (MB) | vm.Config.Hardware.MemoryMB
磁碟數 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
磁碟大小清單 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
網路介面卡清單 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualEthernet).count
CPU 使用率 | cpu.usage.average
記憶體使用量 |mem.usage.average
**每個磁片詳細資料** | 
磁碟機碼值 | disk.Key
Dikunit 號碼 | disk.UnitNumber
磁碟控制器機碼值 | disk.ControllerKey.Value
已佈建的 GB 數 | virtualDisk.DeviceInfo.Summary
磁片名稱 | 使用磁片產生的值。UnitNumber, 磁片。機碼, 磁片。ControllerKey。值
每秒的讀取作業數 | virtualDisk.numberReadAveraged.average
每秒的寫入作業數 | virtualDisk.numberReadAveraged.average
讀取輸送量 (每秒 MB) | virtualDisk.read.average
寫入輸送量 (每秒 MB) | virtualDisk.write.average
**每個 NIC 詳細資料** | 
網路介面卡名稱 | nic.Key
MAC 位址 | ((VirtualEthernetCard)nic).MacAddress
IPv4 位址 | vm.Guest.Net
IPv6 位址 | vm.Guest.Net
讀取輸送量 (每秒 MB) | net.received.average
寫入輸送量 (每秒 MB) | net.transmitted.average
**清查路徑詳細資料** | 
名稱 | container.GetType().Name
子物件的類型 | container.ChildType
參考詳細資料 | container.MoRef
父系詳細資料 | Container.Parent
每個 VM 的資料夾詳細資料 | ((Folder)container).ChildEntity.Type
每個 VM 的資料中心詳細資料 | ((Datacenter)container).VmFolder
每個主機資料夾的資料中心詳細資料 | ((Datacenter)container).HostFolder
每一主機的叢集詳細資料 | ((ClusterComputeResource) 容器)。設立
每個 VM 的主機詳細資料 | ((HostSystem) 容器)。機內



## <a name="collected-performance-data-hyper-v"></a>收集的效能資料-Hyper-v

以下是設備收集並傳送至 Azure 的 VMware VM 效能資料。

**效能計數器類別** | **計數器** | **評量影響**
--- | --- | ---
Hyper-v 虛擬處理器 | % Guest 執行時間 | 建議的 VM 大小/成本
Hyper-v 動態記憶體 VM | 目前壓力 (%)<br/> 來賓可見實體記憶體 (MB) | 建議的 VM 大小/成本
Hyper-v 虛擬存放裝置 | 讀取位元組/秒 | 磁片大小、儲存體成本、VM 大小的計算
Hyper-v 虛擬存放裝置 | 寫入位元組數/秒 | 磁片大小、儲存體成本、VM 大小的計算
Hyper-v 虛擬網路介面卡 | 接收的位元組數/秒 | VM 大小的計算
Hyper-v 虛擬網路介面卡 | 傳送的位元組數/秒 | VM 大小的計算

- CPU 使用率是所有連接至 VM 的虛擬處理器的所有使用量總和。
- 記憶體使用率為 (目前的壓力 * 來賓可見的實體記憶體)/100。
- 磁片和網路使用值是從列出的 Hyper-v 效能計數器收集而來。

## <a name="collected-metadata-hyper-v"></a>收集的中繼資料-Hyper-v

以下是設備收集並傳送至 Azure 的 Hyper-v VM 中繼資料完整清單。

**資料** | **WMI 類別** | **WMI 類別屬性**
--- | --- | ---
**機器詳細資料** | 
BIOS _ Msvm_BIOSElement 的序號 | BIOSSerialNumber
VM 類型 (Gen 1 或 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
VM 顯示名稱 | Msvm_VirtualSystemSettingData | ElementName
VM 版本 | Msvm_ProcessorSettingData | VirtualQuantity
記憶體 (位元組) | Msvm_MemorySettingData | VirtualQuantity
VM 可以使用的最大記憶體 | Msvm_MemorySettingData | 限制
已啟用動態記憶體 | Msvm_MemorySettingData | DynamicMemoryEnabled
作業系統名稱/版本/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems 名稱資料
VM 電源狀態 | Msvm_ComputerSystem | EnabledState
**每個磁片詳細資料** | 
磁片識別碼 | Msvm_VirtualHardDiskSettingData | VirtualDiskId
虛擬硬碟類型 | Msvm_VirtualHardDiskSettingData | Type
虛擬硬碟大小 | Msvm_VirtualHardDiskSettingData | MaxInternalSize
虛擬硬碟父系 | Msvm_VirtualHardDiskSettingData | ParentPath
**每個 NIC 詳細資料** | 
IP 位址 (綜合 Nic) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP 已啟用 (綜合 Nic) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC 識別碼 (綜合 Nic) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC 位址 (綜合 Nic) | Msvm_SyntheticEthernetPortSettingData | 地址
NIC 識別碼 (舊版 Nic) | MsvmEmulatedEthernetPortSetting 資料 | InstanceID
NIC MAC 識別碼 (舊版 Nic) | MsvmEmulatedEthernetPortSetting 資料 | 地址




## <a name="discovery-and-collection-process"></a>探索和收集程式

設備會使用下列程式, 與 vCenter Server 和 Hyper-v 主機/叢集通訊。


1. **開始探索**:
    - 當您在 Hyper-v 應用裝置上啟動探索時, 它會與 WinRM 埠 5985 (HTTP) 和 5986 (HTTPS) 上的 Hyper-v 主機通訊。
    - 當您在 VMware 設備上啟動探索時, 它預設會與 TCP 埠443上的 vCenter server 通訊。 如果 vCenter server 在不同的埠上接聽, 您可以在應用裝置 web 應用程式中加以設定。
2. **收集中繼資料和效能資料**:
    - 設備會使用通用訊息模型 (CIM) 會話, 從埠5985和5986上的 Hyper-v 主機收集 Hyper-v VM 資料。
    - 設備預設會與埠443通訊, 以從 vCenter Server 收集 VMware VM 資料。
3. **傳送資料**:設備會將收集到的資料傳送至 Azure Migrate Server 評估, 並透過 SSL 埠 443 Azure Migrate 伺服器遷移。
    - 針對效能資料, 應用裝置會收集即時使用量資料。
        - VMware 每隔20秒會收集一次效能資料, 針對每個效能計量, 每隔30秒為 Hyper-v。
        - 收集的資料會匯總以建立10分鐘的單一資料點。
        - [尖峰使用率] 值會從所有20/30 的第二個資料點中選取, 並傳送至 Azure 進行評估計算。
        - 根據評估屬性中指定的百分位數值 (第50個/第10部/第 95/第 99), 以遞增順序排序十分鐘點, 並使用適當的百分位數值來計算評量
    - 針對伺服器遷移, 應用裝置會開始收集 VM 資料, 並將其複寫至 Azure。
4. **評估與遷移**:您現在可以從使用 Azure Migrate Server 評估的設備所收集的中繼資料來建立評量。 此外, 您也可以使用 Azure Migrate Server 遷移來開始遷移 VMware Vm, 以協調無代理程式 VM 複寫。


![架構](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>設備升級

設備會隨著應用裝置上執行的 Azure Migrate 代理程式更新而升級。

- 這會自動發生, 因為預設會在設備上啟用自動更新。
- 您可以變更此預設設定, 以手動更新代理程式。
- 若要停用自動更新, 請移至 [登錄編輯程式] > HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance, 並將登錄機碼 [自動更新] 設定為 0 (DWORD)。
 
### <a name="set-agent-updates-to-manual"></a>將代理程式更新設定為手動

若要進行手動更新, 請確定您會使用設備上每個過期代理程式的 [**更新**] 按鈕, 同時更新設備上的所有代理程式。 您可以隨時將更新設定切換回 [自動更新]。

## <a name="next-steps"></a>後續步驟

[瞭解如何](tutorial-assess-vmware.md#set-up-the-appliance-vm)設定 VMware 的應用裝置。
[瞭解如何](tutorial-assess-hyper-v.md#set-up-the-appliance-vm)設定 hyper-v 的應用裝置。

