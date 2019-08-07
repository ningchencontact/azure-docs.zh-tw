---
title: 適用于 VMware 評估和遷移的 Azure Migrate 支援對照表
description: 摘要說明使用 Azure Migrate 服務, 將 VMware Vm 評估和遷移至 Azure 的支援設定和限制。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: raynew
ms.openlocfilehash: ed7e53570127f12dbe41932481a3286a78865dde
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828273"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>VMware 評量和移轉的支援矩陣

您可以使用[Azure Migrate](migrate-overview.md)來評估電腦, 並將機器遷移至 Microsoft Azure 雲端。 本文摘要說明評估和遷移內部部署 VMware Vm 的支援設定和限制。


## <a name="vmware-scenarios"></a>VMware 案例

下表摘要說明 VMware Vm 的支援案例。

**部署** | **詳細資料**
--- | ---
**評估內部部署 VMware Vm** | [設定](tutorial-prepare-vmware.md)您的第一個評估。<br/><br/> [執行](scale-vmware-assessment.md)大規模的評量。
**遷移 VMware VM** | 您可以使用無代理程式遷移來進行遷移, 或使用以代理程式為基礎的遷移。 [深入了解](server-migrate-overview.md)


## <a name="azure-migrate-projects"></a>Azure Migrate 專案

**支援** | **詳細資料**
--- | ---
**Azure 許可權** | 您需要訂用帳戶中的「參與者」或「擁有者」許可權, 才能建立 Azure Migrate 專案。
**VMware 限制**  | 在單一專案中評估最多35000個 VMware Vm。 您可以在 Azure 訂用帳戶中建立多個專案。
**專案限制** | 專案可以包含 VMware Vm 和 Hyper-v Vm, 最多可達評量限制。
**地理位置** | 您可以在數個地理位置中建立 Azure Migrate 專案。 雖然您只能在這些地理位置中建立專案, 但您可以評估或遷移其他目標位置的機器。 專案地理位置只會用來儲存探索到的資料。

**地理位置** | **中繼資料儲存位置**
--- | ---
Azure Government | US Gov 維吉尼亞州
亞太地區 | 東亞或東南亞
澳洲 | 澳大利亞東部或澳大利亞東南部
加拿大 | 加拿大中部或加拿大東部
歐洲 | 北歐或西歐
印度 | 印度中部或印度南部
日本 |  日本東部或日本西部
英國 | 英國南部或英國西部
美國 | 美國中部或美國西部2


 > [!NOTE]
 > Azure Government 的支援目前僅適用于[舊版](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)的 Azure Migrate。


## <a name="assessment-vcenter-server-requirements"></a>評量-vCenter Server 需求

下表摘要說明 VMware 虛擬化伺服器的評量支援和限制。

**支援** | **詳細資料**
--- | ---
**vCenter Server** | 您想要評估的 VMware Vm 必須由執行5.5、6.0、6.5 或6.7 的一或多部 vCenter Server 所管理。

## <a name="assessment-vcenter-server-permissions"></a>評量-vCenter Server 許可權

針對評量, 您需要 vCenter Server 的唯讀帳戶。

## <a name="assessment-appliance-requirements"></a>評量-設備需求

適用于 VMware 的 Azure Migrate 設備是使用匯入 vCenter Server 的 OVA 範本進行部署。

**支援** | **詳細資料**
--- | ---
**vCenter Server** | 您的 vCenter Server 需要足夠的資源來配置具有 32 GB RAM、8個 vcpu 和外部虛擬交換器的 VM。<br/><br/> 設備必須直接或透過 proxy 存取網際網路。
**ESXi** | 設備 VM 必須部署在執行5.5 版或更新版本的 ESXi 主機上。
**Azure Migrate 專案** | 應用裝置可以與單一專案相關聯。
**vCenter Server** | 設備可以在 vCenter Server 上探索最多10000個 VMware Vm。<br/> 設備可以連接到一個 vCenter Server。


## <a name="assessment-url-access-requirements"></a>評估-URL 存取需求

Azure Migrate 設備需要網際網路的連線能力。

- 當您部署設備時, Azure Migrate 會對下表中摘要說明的 Url 進行連線檢查。
- 如果您使用以 URL 為基礎的 proxy 來連線到網際網路, 請允許存取這些 Url, 確保 proxy 會解析查詢 Url 時所收到的任何 CNAME 記錄。

**URL** | **詳細資料**  
--- | --- |
*.portal.azure.com  | 流覽至 Azure 入口網站中的 Azure Migrate。
*.windows.net | 登入您的 Azure 訂用帳戶。
*.microsoftonline.com | 建立設備 Active Directory 應用程式, 以與 Azure Migrate 服務進行通訊。
management.azure.com | 建立設備 Active Directory 應用程式, 以與 Azure Migrate 服務進行通訊。
dc.services.visualstudio.com | 上傳用於內部監視的應用程式記錄。
*.vault.azure.net | 管理 Azure Key Vault 中的秘密。
*.servicebus.windows.net | 設備與 Azure Migrate 服務之間的通訊。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | 連接到 Azure Migrate 服務 Url。
*.blob.core.windows.net | 將資料上傳至儲存體帳戶。
http://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | 用於 Azure Migrate 設備更新。

## <a name="assessment-port-requirements"></a>評量-埠需求

**裝置** | **連接**
--- | ---
設備 | TCP 通訊埠3389上的輸入連線, 以允許應用裝置的遠端桌面連線。<br/><br/> 埠44368上的輸入連線, 可使用 URL 從遠端存取應用裝置管理應用程式:```https://<appliance-ip-or-name>:44368``` <br/><br/>埠443上的輸出連線, 以將探索和效能中繼資料傳送至 Azure Migrate。
vCenter Server | TCP 通訊埠443上的輸入連線, 以允許設備收集設定和效能中繼資料以進行評量。 <br/><br/> 根據預設, 設備會連線到埠443上的 vCenter。 如果 vCenter server 在不同的埠上接聽, 您可以在設定探索時修改埠。


## <a name="agentless-migration-vmware-server-requirements"></a>無代理程式遷移-VMware 伺服器需求

下表摘要說明 VMware 虛擬化伺服器的評量支援和限制。

**支援** | **詳細資料**
--- | ---
vCenter Server | 版本5.5、6.0、6.5 或6.7。
VMware vSphere | 5\.5、6.0、6.5 或6.7 版本

## <a name="agentless-migration-vcenter-server-permissions"></a>無代理程式遷移-vCenter Server 許可權

**Permissions** | **詳細資料**
--- | ---
Datastore.Browse | 允許流覽 VM 記錄檔, 以針對快照集的建立和刪除進行疑難排解。
Datastore.LowLevelFileOperations | 允許在資料存放區瀏覽器中進行讀取/寫入/刪除/重新命名作業, 以針對建立和刪除快照集進行疑難排解。
VirtualMachine.Configuration.DiskChangeTracking | 允許啟用或停用 VM 磁片的變更追蹤, 以在快照集之間提取已變更的資料區塊
VirtualMachine.Configuration.DiskLease | 允許 VM 的磁片租用作業使用 VMware vSphere 虛擬磁片開發工具組 (VDDK) 來讀取磁片。
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | 允許在 VM 上開啟磁片, 以使用 VDDK 來讀取磁片。
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | 允許與 VM 相關聯之檔案的讀取作業, 以下載記錄, 並在發生失敗時進行疑難排解。
VirtualMachine.SnapshotManagement.* | 允許建立和管理 VM 快照集以進行複寫。
虛擬機器。電源關閉 | 允許 VM 在遷移至 Azure 期間關閉電源。


## <a name="agentless-migration-vmware-vm-requirements"></a>無代理程式遷移-VMware VM 需求

**支援** | **詳細資料**
--- | ---
**受支援的作業系統** | Azure 支援的[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)作業系統可使用無代理程式遷移來進行遷移。
**Azure 的必要變更** | 有些 Vm 可能需要變更, 才能在 Azure 中執行。 Azure Migrate 會針對下列作業系統自動進行這些變更:<br/> -Red Hat Enterprise Linux 6.5 +、7.0 +<br/> -CentOS 6.5 +、7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS、16.04 LTS、18.04 LTS<br/> -Debian 7、8<br/><br/> 如果是其他作業系統, 您必須在進行遷移之前手動進行調整。 相關文章包含如何執行這項操作的指示。
**Linux 開機** | 如果/boot 是在專用磁碟分割上, 它應該位於 OS 磁片上, 而不會散佈到多個磁片上。<br/> 如果/boot 是根 (/) 分割區的一部分, 則 '/' 磁碟分割應該位於 OS 磁片上, 而不是跨越其他磁片。
**UEFI 開機** | 不支援使用 UEFI 開機的 Vm 進行遷移。
**磁碟大小** | 2 TB 的 OS 磁片;4 TB 的資料磁片。
**磁片限制** |  每個 VM 最多60個磁片。
**加密的磁片/磁片區** | 不支援使用加密磁片/磁片區的 Vm 進行遷移。
**共用磁碟叢集** | 不支援。
**獨立磁片** | 不支援。
**RDM/傳遞磁片** | 如果 Vm 有 RDM 或 passthrough 磁片, 這些磁片將不會複寫到 Azure。
**NFS** | 裝載為 Vm 上之磁片區的 NFS 磁片區不會複寫。
**iSCSI 目標** | 無代理程式遷移不支援具有 iSCSI 目標的 Vm。
**多重路徑 IO** | 不支援。
**儲存體 vMotion** | 不支援。 如果 VM 使用儲存體 vMotion, 複寫將無法正常執行。
**組合的 Nic** | 不支援。
**IPv6** | 不支援。
**目標磁片** | Vm 只能遷移至 Azure 中的受控磁片 (標準 HDD、premium SSD)。
**同時複寫** | 每個 vCenter Server 100 個 Vm。 如果您有更多, 請以100的批次方式來遷移它們。


## <a name="agentless-migration-appliance-requirements"></a>無代理程式遷移-設備需求


**支援** | **詳細資料**
--- | ---
**ESXi** | 設備 VM 必須部署在執行5.5 版或更新版本的 ESXi 主機上。
**Azure Migrate 專案** | 應用裝置可以與單一專案相關聯。
**vCenter Server** | 設備可以在 vCenter Server 上探索最多10000個 VMware Vm。<br/> 設備可以連接到一個 vCenter Server。
**VDDK** | 如果您是使用 Azure Migrate 伺服器遷移來執行無代理程式遷移, 則必須在應用裝置 VM 上安裝 VMware vSphere VDDK。

## <a name="agentless-migration-url-access-requirements"></a>無代理程式遷移-URL 存取需求

Azure Migrate 設備需要網際網路連線到網際網路。

- 當您部署設備時, Azure Migrate 會對下表中摘要說明的 Url 進行連線檢查。
- 如果您使用以 URL 為基礎的 proxy, 請允許存取這些 Url, 並確定 proxy 會解析查詢 Url 時所收到的任何 CNAME 記錄。

**URL** | **詳細資料**  
--- | ---
*.portal.azure.com | 流覽至 Azure 入口網站中的 Azure Migrate。
*.windows.net | 登入您的 Azure 訂用帳戶。
*.microsoftonline.com | 建立設備 Active Directory 應用程式, 以與 Azure Migrate 服務進行通訊。
management.azure.com | 建立設備 Active Directory 應用程式, 以與 Azure Migrate 服務進行通訊。
dc.services.visualstudio.com | 上傳用於內部監視的應用程式記錄。
*.vault.azure.net | 管理 Azure Key Vault 中的秘密。
*.servicebus.windows.net | 設備與 Azure Migrate 服務之間的通訊。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | 連接到 Azure Migrate 服務 Url。
*.blob.core.windows.net | 將資料上傳至儲存體帳戶。
http://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | 用於 Azure Migrate 設備更新。


## <a name="agentless-migration-port-requirements"></a>無代理程式遷移-埠需求

**裝置** | **連接**
--- | ---
設備 | 埠443上的輸出連線可將複寫的資料上傳至 Azure, 以及與 Azure Migrate 服務進行通訊, 以協調複寫與遷移。
vCenter Server | 埠443上的輸入連線可讓設備協調複寫-建立快照集、複製資料、發行快照集
vSphere/EXSI 主機 | TCP 埠902上用於設備的輸入, 以從快照集複寫資料。


## <a name="agent-based-migration-vmware-server-requirements"></a>以代理程式為基礎的遷移-VMware 伺服器需求

下表摘要說明 VMware 虛擬化伺服器的評量支援和限制。

**支援** | **詳細資料**
--- | ---
vCenter Server | 版本5.5、6.0、6.5 或6.7。
VMware vSphere | 版本5.5、6.0、6.5 或6.7。

### <a name="agent-based-migration-vcenter-server-permissions"></a>以代理程式為基礎的遷移-vCenter Server 許可權

VCenter Server 的唯讀帳戶。

## <a name="agent-based-migration-replication-appliance-requirements"></a>以代理程式為基礎的遷移-複寫設備需求

如需使用 Azure Migrate Server 遷移來進行 VMware Vm 和實體伺服器之代理程式式遷移的複寫[設備](migrate-replication-appliance.md)需求, 請在表格中摘要說明。

> [!NOTE]
> 當您使用 Azure Migrate 中樞提供的 OVA 範本來設定複寫設備時, 設備會執行 Windows Server 2016, 並符合支援需求。 如果您在實體伺服器上手動設定複寫設備, 請確定它符合需求。



**元件** | **需求**
--- | ---
 | **VMware 設定**(VMware VM 應用裝置)
PowerCLI | 如果複寫設備在 VMware VM 上執行, 則應該安裝[PowerCLI 6.0 版](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1)。
NIC 類型 | VMXNET3 (如果設備是 VMware VM)
 | **硬體設定**
CPU 核心 | 8
RAM | 16 GB
磁碟數 | 態OS 磁片、進程伺服器快取磁片和保留磁片磁碟機。
可用磁碟空間 (快取) | 600 GB
可用磁碟空間 (保留磁碟) | 600 GB
**軟體設定** |
作業系統 | Windows Server 2016 或 Windows Server 2012 R2
作業系統地區設定 | 英文 (en-us)
TLS | 應啟用 TLS 1.2。
.NET Framework | 應該在電腦上安裝 .NET Framework 4.6 或更新版本 (啟用強式密碼編譯)。
MySQL | MySQL 應該安裝在設備上。<br/> 應該安裝 MySQL。 您可以手動安裝, 或 Site Recovery 可以在應用裝置部署期間進行安裝。
其他應用程式 | 不要在複寫設備上執行其他應用程式。
Windows Server 角色 | 請勿啟用這些角色： <br> - Active Directory Domain Services <br>- 網際網路資訊服務 <br> - Hyper-V
群組原則 | 請勿啟用這些群組原則： <br> - 防止存取命令提示字元。 <br> - 防止存取登錄編輯工具。 <br> - 檔案附件的信任邏輯。 <br> - 開啟指令碼執行。 <br> [深入了解](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 沒有預先存在的預設網站 <br> - 沒有預先存在的網站/應用程式接聽連接埠 443 <br>- 啟用[匿名驗證](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - 啟用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 設定
**網路設定** |
IP 位址類型 | Static
連接埠 | 443 (控制通道協調流程)<br>9443 (資料傳輸)
NIC 類型 | VMXNET3

### <a name="replication-appliance-url-access"></a>複寫設備 URL 存取

複寫設備需要這些 Url 的存取權。

**URL** | **詳細資料**
--- | ---
\*.backup.windowsazure.com | 用於所複寫資料的轉送和協調
\*.store.core.windows.net | 用於所複寫資料的轉送和協調
\*.blob.core.windows.net | 用於存取儲存體帳戶來儲存複寫的資料
\*.hypervrecoverymanager.windowsazure.com | 用於複寫管理作業和協調
https:\//management.azure.com | 用於複寫管理作業和協調
*.services.visualstudio.com | 用於遙測目的 (此為選擇性項目)
time.nist.gov | 用於檢查系統時間與通用時間之間的時間同步處理。
time.windows.com | 用於檢查系統時間與通用時間之間的時間同步處理。
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | OVF 安裝程式需要存取這些 Url。 其可供 Azure Active Directory 用於管理存取控制和身分識別
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | 完成 MySQL 下載


#### <a name="mysql-installation-options"></a>MySQL 安裝選項

您可以使用其中一種方法, 將 MySQL 安裝在複寫應用裝置上。

**方法** | **詳細資料**
--- | ---
手動下載並安裝 | 下載 MySQL 應用程式 & 將它放在 C:\Temp\ASRSetup 資料夾中, 然後手動安裝。<br/> 當您設定應用裝置時, MySQL 會顯示為已安裝。
不需要線上下載 | 將 MySQL installer 應用程式放在資料夾 C:\temp\asrsetup 中。中 當您安裝設備並按一下以下載並安裝 MySQL 時, 安裝程式將會使用您新增的安裝程式。
在 Azure Migrate 中下載並安裝 | 當您安裝設備, 並會提示您提供 MySQL 時, 請選取 [**下載並安裝**]。



## <a name="agent-based-migration-vmware-vm-requirements"></a>以代理程式為基礎的遷移-VMware VM 需求

**支援** | **詳細資料**
--- | ---
**機器工作負載** | Azure Migrate 支援在支援的機器上, 遷移執行的任何工作負載 (例如 Active Directory、SQL server 等)。
**作業系統** | 如需最新資訊, 請參閱 Site Recovery 的[作業系統支援](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)。 Azure Migrate 提供相同的 VM 作業系統支援。
**Linux 檔案系統/來賓儲存體** | 如需最新資訊, 請參閱[Linux 檔案系統](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)的 Site Recovery 支援。 Azure Migrate 具有相同的 Linux 檔案系統支援。
**網路/儲存體** | 如需最新資訊, 請參閱 Site Recovery 的[網路](../site-recovery/vmware-physical-azure-support-matrix.md#network)和[儲存體](../site-recovery/vmware-physical-azure-support-matrix.md#storage)必要條件。 Azure Migrate 提供相同的網路/儲存需求。
**Azure 需求** | 如需最新資訊, 請參閱 Site Recovery 的[Azure 網路](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)、[儲存體](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)和[計算](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute)需求。 Azure Migrate 對於 VMware 遷移具有相同的需求。
**行動服務** | 行動服務代理程式必須安裝在您想要遷移的每個 VM 上。
**UEFI 開機** | Azure 中已遷移的 VM 會自動轉換為 BIOS 開機 VM。<br/><br/> OS 磁片應該有最多四個磁碟分割, 而且磁片區應該使用 NTFS 格式化。
**目標磁片** | Vm 只能遷移至 Azure 中的受控磁片 (標準 HDD、premium SSD)。
**磁碟大小** | 2 TB 的 OS 磁片;適用于資料磁片的 8 TB。
**磁片限制** |  每個 VM 最多63個磁片。
**加密的磁片/磁片區** | 不支援使用加密磁片/磁片區的 Vm 進行遷移。
**共用磁碟叢集** | 不支援。
**獨立磁片** | 支援。
**傳遞磁片** | 支援。
**NFS** | 裝載為 Vm 上之磁片區的 NFS 磁片區不會複寫。
iSCSI 目標 | 無代理程式遷移不支援具有 iSCSI 目標的 Vm。
**多重路徑 IO** | 不支援。
**儲存體 vMotion** | 支援
**組合的 Nic** | 不支援。
**IPv6** | 不支援。




## <a name="agent-based-migration-url-access-requirements"></a>以代理程式為基礎的遷移-URL 存取需求

在 VMware Vm 上執行的行動服務需要網際網路連線能力。

當您部署行動服務時, 它會對下表中摘要說明的 Url 進行連線檢查。


**URL** | **詳細資料**  
--- | ---
*.portal.azure.com | 流覽至 Azure 入口網站中的 Azure Migrate。
*.windows.net | 登入您的 Azure 訂用帳戶。
*.microsoftonline.com | 建立設備 Active Directory 應用程式, 以與 Azure Migrate 服務進行通訊。
management.azure.com | 建立設備 Active Directory 應用程式, 以與 Azure Migrate 服務進行通訊。
dc.services.visualstudio.com | 上傳用於內部監視的應用程式記錄。
*.vault.azure.net | 管理 Azure Key Vault 中的秘密。
*.servicebus.windows.net | 設備與 Azure Migrate 服務之間的通訊。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | 連接到 Azure Migrate 服務 Url。
*.blob.core.windows.net | 將資料上傳至儲存體帳戶。

## <a name="agent-based-migration-port-requirements"></a>以代理程式為基礎的遷移-埠需求

**裝置** | **連接**
--- | ---
VM | 在 Vm 上執行的行動服務會與埠 HTTPS 443 輸入上的內部部署複寫設備 (設定伺服器) 進行通訊, 以進行複寫管理。<br/><br/> VM 會透過輸入連接埠 HTTPS 9443 將複寫資料傳送至處理伺服器 (在設定伺服器電腦上執行)。 您可以修改此連接埠。
複寫設備 | 複寫設備會透過埠 HTTPS 443 輸出來協調與 Azure 的複寫。
處理伺服器 | 進程伺服器會接收復寫資料、將其優化並加以加密, 並透過埠443輸出將它傳送至 Azure 儲存體。<br/> 根據預設, 進程伺服器會在複寫設備上執行。

## <a name="azure-vm-requirements"></a>Azure VM 需求

複寫至 Azure 的所有內部部署 Vm 都必須符合此表中摘要說明的 Azure VM 需求。 當 Site Recovery 執行複寫的必要條件檢查時, 如果不符合某些需求, 此檢查將會失敗。

**元件** | **需求** | **詳細資料**
--- | --- | ---
客體作業系統 | 針對[使用無代理](#agentless-migration-vmware-vm-requirements)程式複寫的 vmware vm, 以及[使用代理式複寫的 vmware vm](#agent-based-migration-vmware-vm-requirements), 驗證支援的作業系統。<br/> 您可以遷移在支援的作業系統上執行的任何工作負載。 | 若不支援，則檢查會失敗。
客體作業系統架構 | 64 位元。 | 若不支援，則檢查會失敗。
作業系統磁碟大小 | 最多 2,048 GB。 | 若不支援，則檢查會失敗。
作業系統磁碟計數 | 1 | 若不支援，則檢查會失敗。
資料磁碟計數 | 64 或以下。 | 若不支援，則檢查會失敗。
資料磁碟大小 | 最多 4,095 GB | 若不支援，則檢查會失敗。
網路介面卡 | 支援多個介面卡。 |
共用 VHD | 不支援。 | 若不支援，則檢查會失敗。
FC 磁碟 | 不支援。 | 若不支援，則檢查會失敗。
BitLocker | 不支援。 | 為電腦啟用複寫之前必須先停用 BitLocker。
VM 名稱 | 從 1 到 63 個字元。<br/> 只能使用字母、數字和連字號。<br/><br/> 電腦名稱必須以字母或數字為開頭或結尾。 |  更新 Site Recovery 中電腦屬性的值。
在遷移後連接-Windows | 若要在遷移後連線至執行 Windows 的 Azure Vm:<br/> -在遷移之前, 在內部部署 VM 上啟用 RDP。 確定已針對 [公用] 設定檔新增 TCP 和 UDP 規則，且在 [Windows 防火牆] > [允許的應用程式] 中已針對所有設定檔允許 RDP。<br/> 針對站對站 VPN 存取, 請啟用 rdp, 並允許**Windows 防火牆** -> 中的 rdp**允許的應用程式和功能**用於**網域和專用**網。 此外, 請檢查作業系統的 SAN 原則是否設定為**OnlineAll**。 [深入了解](https://support.microsoft.com/kb/3031135)。 |
在遷移後連接-Linux | 若要在使用 SSH 進行遷移之後連線到 Azure Vm:<br/> 在進行遷移之前, 請在內部部署機器上, 確認安全殼層服務已設定為 [啟動], 且防火牆規則允許 SSH 連線。<br/> 容錯移轉之後, 在 Azure VM 上, 允許已容錯移轉的 VM 上的網路安全性群組規則之 SSH 埠的連入連線, 以及它所連接的 Azure 子網。 此外, 請新增 VM 的公用 IP 位址。 |  


## <a name="next-steps"></a>後續步驟

[準備 VMware](tutorial-prepare-vmware.md)評估和遷移。
