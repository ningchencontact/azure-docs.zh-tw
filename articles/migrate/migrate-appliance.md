---
title: Azure Migrate 設備架構 |Microsoft Docs
description: 提供 Azure Migrate 應用裝置的概觀
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: c2c9ca3082aa9c2067a63f8d6304e8a229dac14a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811449"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 應用裝置

本文說明 Azure Migrate 應用裝置。 當您使用 Azure Migrate 評估和移轉工具來探索、 評估及移轉至 Microsoft Azure 的應用程式、 基礎結構，以及工作負載時，您可以部署應用裝置。 

[Azure Migrate](migrate-services-overview.md)提供一個中央中樞，來追蹤探索、 評估及移轉內部部署應用程式和工作負載和私密/公開金鑰雲端 Vm 至 Azure。 中樞會提供 Azure Migrate 的工具，來評估和移轉，以及第三方獨立軟體廠商 (ISV) 供應項目。



## <a name="appliance-overview"></a>設備概觀

Azure Migrate 的設備類型和使用方式如下所示。

**部署為** | **用於** | **詳細資料**
--- | --- |  ---
VMware VM | 使用 Azure Migrate 評量工具評估的 VMware VM。<br/><br/> VMware VM 與 Azure Migrate Server 移轉工具的無代理程式移轉 | 下載 OVA 範本並匯入到 vCenter Server 來建立設備 VM。
Hyper-V VM | 使用 Azure Migrate 評量工具評估的 HYPER-V VM。 | 下載 zip 壓縮的 VHD，並建立設備 VM 的 HYPER-V 匯入。

## <a name="appliance-access"></a>應用裝置存取

您已設定設備之後，您就可以透過 TCP 連接埠 3389，從遠端存取應用裝置 VM。 您可以也從遠端存取應用裝置中，URL 的通訊埠 44368 上的 web 管理應用程式： ``` https://<appliance-ip-or-name>:44368 ```。

## <a name="appliance-license"></a>應用裝置授權
設備隨附於 Windows Server 2016 的評估授權，也就是有效的 180 天。 如果試用期即將到期，我們建議您下載並部署新的裝置，或您啟動的應用裝置 VM 的作業系統授權。

## <a name="appliance-agents"></a>設備代理程式
設備已安裝這些代理程式。

**代理程式** | **詳細資料**
--- | ---
探索代理程式 | 從內部部署 Vm 收集組態資料。
評估代理程式 | 設定檔來收集 VM 的效能資料的內部部署環境。
移轉介面卡 | 用來協調 VM 複寫並協調 Vm 與 Azure 之間的通訊。
移轉閘道 | 將會複寫至 Azure VM 的資料。


## <a name="appliance-deployment-requirements"></a>應用裝置的部署需求

- [檢閱](migrate-support-matrix-vmware.md#assessment-appliance-requirements)VMware 應用裝置和設備需要存取的 Url 的部署需求。
- [檢閱](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements)HYPER-V 應用裝置和設備需要存取的 Url 的部署需求。


## <a name="collected-performance-data-vmware"></a>收集的效能資料 VMware

以下是 VMware VM 的效能資料的設備會收集並傳送至 Azure。

**資料** | **計數器** | **評定影響**
--- | --- | ---
CPU 使用率 | cpu.usage.average | 建議的 VM 大小/成本
記憶體使用量 | mem.usage.average | 建議的 VM 大小/成本
磁碟讀取輸送量 (每秒 MB) | virtualDisk.read.average | 磁碟大小、 儲存體成本、 VM 大小的計算
磁碟寫入輸送量 (每秒 MB) | virtualDisk.write.average | 磁碟大小、 儲存體成本、 VM 大小的計算
磁碟讀取作業 / 秒 | virtualDisk.numberReadAveraged.average | 磁碟大小、 儲存體成本、 VM 大小的計算
磁碟寫入的每秒的作業 | virtualDisk.numberReadAveraged.average  | 磁碟大小、 儲存體成本、 VM 大小的計算
NIC 讀取輸送量 (每秒 MB) | net.received.average | 計算 VM 大小
NIC 寫入輸送量 (每秒 MB) | net.transmitted.average  |計算 VM 大小


## <a name="collected-metadata-vmware"></a>收集的中繼資料-VMware

以下是設備會收集並傳送至 Azure 的 VMware VM 中繼資料的完整清單。

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
核心數目 | vm.Config.Hardware.NumCPU
記憶體 (MB) | vm.Config.Hardware.MemoryMB
磁碟數量 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
磁碟大小清單 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
網路介面卡清單 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualEthernet).count
CPU 使用率 | cpu.usage.average
記憶體使用量 |mem.usage.average
**每個磁碟詳細資料** | 
磁碟機碼值 | disk.Key
Dikunit 數目 | disk.UnitNumber
磁碟控制器機碼值 | disk.ControllerKey.Value
已佈建的 GB 數 | virtualDisk.DeviceInfo.Summary
磁碟名稱 | 使用磁碟產生的值。UnitNumber，磁碟。索引鍵，磁碟。ControllerKey.VAlue
每秒的讀取作業數 | virtualDisk.numberReadAveraged.average
每秒的寫入作業數 | virtualDisk.numberReadAveraged.average
讀取輸送量 (每秒 MB) | virtualDisk.read.average
寫入輸送量 (每秒 MB) | virtualDisk.write.average
**每個 NIC 的詳細資料** | 
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
每個 VM 的 資料夾詳細資料 | ((Folder)container).ChildEntity.Type
每個 VM 的資料中心詳細資料 | ((Datacenter)container).VmFolder
每個主控件資料夾的資料中心詳細資料 | ((Datacenter)container).HostFolder
每個主機叢集的詳細資料 | ((ClusterComputeResource)container).Host
每個 VM 的主機詳細資料 | （(HostSystem) 容器）。VM



## <a name="collected-performance-data-hyper-v"></a>收集的效能資料-Hyper-v

以下是 VMware VM 的效能資料的設備會收集並傳送至 Azure。

**效能計數器類別** | **計數器** | **評定影響**
--- | --- | ---
HYPER-V Hypervisor 的虛擬處理器 | %客體執行的階段 | 建議的 VM 大小/成本
HYPER-V 動態記憶體 VM | 目前壓力 （%）<br/> Guest Visible Physical Memory (MB) | 建議的 VM 大小/成本
Hyper V 虛擬存放裝置 | 讀取位元組/秒 | 磁碟大小、 儲存體成本、 VM 大小的計算
Hyper V 虛擬存放裝置 | 寫入位元組/秒 | 磁碟大小、 儲存體成本、 VM 大小的計算
HYPER-V 虛擬網路介面卡 | 每秒接收的位元組 | 計算 VM 大小
HYPER-V 虛擬網路介面卡 | 每秒傳送的位元組 | 計算 VM 大小

- CPU 使用率是使用量的所有，連結至 VM 的所有虛擬處理器的總和。
- 記憶體使用率 (Current Pressure * Guest Visible Physical Memory) / 100。
- 從列出的 HYPER-V 效能計數器收集磁碟和網路使用率值。

## <a name="collected-metadata-hyper-v"></a>收集的中繼資料-Hyper-v

以下是設備會收集並傳送至 Azure 的 HYPER-V VM 中繼資料的完整清單。

**資料** | **WMI 類別** | **WMI 類別屬性**
--- | --- | ---
**機器詳細資料** | 
BIOS _ Msvm_BIOSElement 的序號 | BIOSSerialNumber
VM 類型 (Gen 1 或 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
VM 顯示名稱 | Msvm_VirtualSystemSettingData | ElementName
VM 版本 | Msvm_ProcessorSettingData | VirtualQuantity
記憶體 （位元組） | Msvm_MemorySettingData | VirtualQuantity
可供 VM 的最大記憶體 | Msvm_MemorySettingData | 限制
啟用動態記憶體 | Msvm_MemorySettingData | DynamicMemoryEnabled
作業系統名稱/版本/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsciExchangeItems 名稱的資料
VM 電源狀態 | Msvm_ComputerSystem | EnabledState
**每個磁碟詳細資料** | 
磁碟識別碼 | Msvm_VirtualHardDiskSettingData | VirtualDiskId
虛擬硬碟類型 | Msvm_VirtualHardDiskSettingData | type
虛擬硬碟大小 | Msvm_VirtualHardDiskSettingData | MaxInternalSize
虛擬硬碟父系 | Msvm_VirtualHardDiskSettingData | ParentPath
**每個 NIC 的詳細資料** | 
IP 位址 (綜合 Nic) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
已啟用 DHCP (綜合 Nic) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
網路介面卡識別碼 (綜合 Nic) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC 的 MAC 位址 (綜合 Nic) | Msvm_SyntheticEthernetPortSettingData | 位址
網路介面卡識別碼 (使用舊式 Nic) | MsvmEmulatedEthernetPortSetting 資料 | InstanceID
網路介面卡 MAC 識別碼 (使用舊式 Nic) | MsvmEmulatedEthernetPortSetting 資料 | 位址




## <a name="discovery-and-collection-process"></a>探索和集合的程序

設備進行通訊與 vCenter Server 和 HYPER-V 主機/叢集使用下列程序。


1. **開始探索**:
    - 當您開始探索 HYPER-V 應用裝置上時，它會與為 HYPER-V 主機上之 WinRM 連接埠 5985 (HTTP) 和 5986 (HTTPS) 進行通訊。
    - 當您開始探索 VMware 應用裝置上時，它會與預設的 TCP 連接埠 443 上的 vCenter server 進行通訊。 如果 vCenter 伺服器接聽不同的連接埠，您可以在應用裝置的 web 應用程式來進行設定。
2. **收集的中繼資料和效能資料**:
    - 設備會收集從 HYPER-V 主機的 HYPER-V VM 的資料連接埠 5985 和 5986 上使用通用訊息模型 (CIM) 工作階段。
    - 根據預設，收集從 vCenter Server 的 VMware VM 資料的連接埠 443 與通訊，應用裝置。
3. **將資料傳送**:應用裝置會傳送至 Azure Migrate Server 評量和 Azure 移轉的伺服器移轉所收集的資料，透過 SSL 連接埠 443。
    - 效能資料，該設備會收集即時使用量資料。
        - 適用於 VMware，每隔 20 秒和每個效能標準的 HYPER-V，每隔 30 秒會收集效能資料。
        - 收集的資料被彙總，為十分鐘建立單一資料點。
        - 尖峰使用率值選取所有 20 個/30 第二個資料點，並傳送至 Azure，評量計算。
        - 根據評估屬性 (第 50/第 90/95th/99th) 中指定的百分位數值，十分鐘點都會依照遞增順序，並適當的百分位數值來計算評量
    - 伺服器移轉設備會開始收集 VM 資料，並將它複寫至 Azure。
4. **評定與移轉**:您現在可以從使用 Azure Migrate Server 評量應用裝置所收集的中繼資料來建立評定。 此外，您也可以開始將使用 Azure 移轉的伺服器移轉，以協調將無代理程式 VM 複寫的 VMware Vm 移轉。


![架構](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>設備升級

由於應用裝置上執行的 Azure Migrate 代理程式會更新，會升級應用裝置。

- 這會自動發生因為自動更新預設會啟用應用裝置上。
- 您可以變更此預設設定，以手動更新代理程式。
- 若要停用自動更新，設定登錄機碼設備自動更新，HKLM\SOFTWAREMicrosoft\Azure 中。

### <a name="set-agent-updates-to-manual"></a>為 手動設定代理程式更新

手動更新，請確定您更新應用裝置上的所有代理程式，在此同時，使用**更新**每一個過期的代理程式，應用裝置上的按鈕。 您可以切換的更新設定回到在任何時間自動更新。

## <a name="next-steps"></a>後續步驟

[了解如何](tutorial-assess-vmware.md#set-up-the-appliance-vm)來設定適用於 VMware 的設備。
[了解如何](tutorial-assess-hyper-v.md#set-up-the-appliance-vm)來設定適用於 HYPER-V 的設備。

