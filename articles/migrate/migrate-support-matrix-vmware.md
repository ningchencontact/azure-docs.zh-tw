---
title: VMware 評定及移轉的 azure 移轉支援矩陣
description: 摘要說明支援設定和評估和移轉 VMware Vm 至 Azure 中使用 Azure Migrate 服務的限制。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: raynew
ms.openlocfilehash: 567a6582e193208a7ff4aa37bafefe1dec4f4e8f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811579"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>VMware 評量和移轉的支援矩陣

您可以使用[Azure Migrate 服務](migrate-overview.md)評定，並將機器移轉至 Microsoft Azure 雲端。 本文摘要說明支援設定和評估和移轉內部部署 VMware Vm 的限制。


## <a name="vmware-scenarios"></a>VMware 案例

下表摘要說明支援的案例，適用於 VMware Vm。

**部署** | **詳細資料** 
--- | --- 
**評估內部部署 VMware Vm** | [設定](tutorial-prepare-vmware.md)您第一次評估。<br/><br/> [執行](scale-vmware-assessment.md)大規模評估。
**將 VMware Vm 移轉** | 您可以使用無代理程式的移轉，但有一些限制，進行移轉，或使用代理程式為基礎的移轉。 [深入了解](server-migrate-overview.md)


    


## <a name="azure-migrate-projects"></a>Azure Migrate 專案

**支援** | **詳細資料**
--- | ---
Azure 權限 | 您需要 「 參與者 」 或 「 擁有者權限的訂用帳戶中建立 Azure Migrate 專案。
VMware 限制  | 評估最多 35000 單一專案中的 VMware Vm。

專案可以包含 VMware Vm 和 HYPER-V Vm 到評定的上限。

## <a name="assessment-vmware-server-requirements"></a>評定 VMware 伺服器需求

下表摘要說明評估支援與 VMware 虛擬化伺服器的限制。

**支援** | **詳細資料**
--- | ---
**vCenter Server** | 您想要評估的 VMware Vm 必須由執行 5.5、 6.0、 6.5、 或 6.7 的一或多個 vCenter 伺服器管理。

## <a name="assessment-vcenter-server-permissions"></a>評估 vCenter 伺服器的權限

只評估，您需要的 vCenter Server 的唯讀帳戶。

## <a name="assessment-appliance-requirements"></a>評估設備需求

**支援** | **詳細資料**
--- | ---
**ESXi** | 應用裝置 VM 必須執行版本 5.5 或更新版本的 ESXi 主機上部署。
**Azure Migrate 專案** | 應用裝置可以是與單一專案建立關聯。
**vCenter Server** | 設備可以探索 vCenter Server 上的最多 10,000 個 VMware Vm。<br/> 應用裝置可以連線到一個 vCenter Server。


## <a name="assessment-url-access-requirements"></a>評定 URL 存取需求

Azure Migrate 設備需要網際網路連線到網際網路。

- 當您部署應用裝置時，Azure Migrate 會連線能力檢查，以在下表中摘要的 Url。
- 如果您使用以 URL 為基礎的 firewall.proxy，允許存取這些 Url，並確定 proxy 解析的查閱 Url 時收到的任何 CNAME 記錄。

**URL** | **詳細資料**  
--- | --- |
*.portal.azure.com  | 瀏覽至 Azure 入口網站中的 Azure Migrate。
*.windows.net | 登入您的 Azure 訂用帳戶。
*.microsoftonline.com | 建立設備，以使用 Azure Migrate 服務進行通訊的 Active Directory 應用程式。
management.azure.com | 建立設備，以使用 Azure Migrate 服務進行通訊的 Active Directory 應用程式。
dc.services.visualstudio.com | 上傳用於內部監視的應用程式記錄檔。
*.vault.azure.net | 管理在 Azure Key Vault 的祕密。
*.servicebus.windows.net | 應用裝置與 Azure Migrate 服務之間的通訊。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | 連接到 Azure Migrate 服務的 Url。
*.blob.core.windows.net | 將資料上傳至儲存體帳戶。


## <a name="assessment-port-requirements"></a>評估連接埠需求

**裝置** | **連接**
--- | --- 
應用裝置 | TCP 連接埠 3389，以允許遠端桌面連線至應用裝置上的傳入的連接。<br/> 輸入連接埠 44368 從遠端存取應用裝置管理應用程式使用的 URL 上的連線： https://<appliance-ip-or-name>:44368 <br/>若要將探索和效能的中繼資料傳送至 Azure Migrate 的連接埠 443 上的傳出連線。
vCenter 伺服器 | 輸入允許的應用裝置，以收集組態和效能評定的中繼資料的 TCP 連接埠 443 上的連線。 <br/> 應用裝置會連接到預設的連接埠 443 上的 vCenter。 如果 vCenter 伺服器接聽不同的連接埠，您可以修改此連接埠，當您設定探索。


## <a name="agentless-migration-vmware-server-requirements"></a>無代理程式的移轉-VMware 伺服器需求

下表摘要說明評估支援與 VMware 虛擬化伺服器的限制。

**支援** | **詳細資料**
--- | ---
**vCenter Server** | 您使用無代理程式的移轉進行移轉的 VMware Vm 必須由執行 5.5、 6.0、 6.5、 或 6.7 的一或多個 vCenter 伺服器管理。

## <a name="agentless-migration-vcenter-server-permissions"></a>無代理程式的移轉 vCenter 伺服器的權限

**Permissions** | **詳細資料**
--- | --- 
Datastore.Browse | 允許瀏覽 VM 進行疑難排解的快照集建立和刪除的記錄檔。
Datastore.LowLevelFileOperations | 允許讀取/寫入/刪除/重新命名作業資料存放區瀏覽器中，若要疑難排解的快照集建立和刪除。
VirtualMachine.Configuration.DiskChangeTracking | 允許啟用或停用變更追蹤的 VM 磁碟，若要提取的資料快照集之間變更的區塊
VirtualMachine.Configuration.DiskLease | 允許讀取使用 VMware vSphere 虛擬磁碟開發套件 (VDDK) 磁碟為虛擬機器的磁碟租用作業。
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | 允許開啟 VM，以讀取使用 VDDK 在磁碟上的磁碟。
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | 可讓讀取的作業相關聯的 VM，來下載記錄和疑難排解失敗時的檔案。 
VirtualMachine.SnapshotManagement.* | 允許建立和管理複寫的 VM 快照集。 
關閉虛擬 Machine.Interaction.Power | 可讓 VM 移轉至 Azure 期間，關閉電源。


## <a name="agentless-migration-vmware-vm-requirements"></a>無代理程式的移轉 VMware VM 需求

**支援** | **詳細資料**
--- | ---
**受支援的作業系統** | [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)並[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) Azure 所支援的作業系統可以使用無代理程式的移轉進行移轉。
**適用於 Azure 的必要的變更** | 某些 Vm 可能需要變更，以便它們可以在 Azure 中執行。 Azure Migrate 讓這些變更會自動針對下列作業系統：<br/> -Red Hat Enterprise Linux 6.5 + 7.0 +<br/> -CentOS 6.5 + 7.0 +</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> -Debian 7、 8<br/><br/> 對於其他作業系統中，您需要進行移轉之前以手動方式調整。 相關的文章包含有關如何執行這項操作的指示。
**Linux 開機** | 如果 /boot 專用的磁碟分割上，它應該位於 OS 磁碟，並不會分散到多個磁碟。<br/> /Boot 是否屬於根 （/） 磁碟分割，然後 '/' 的分割區應該位於 OS 磁碟，並不會跨越其他磁碟。
**UEFI 開機** | 移轉不支援使用 UEFI 開機的 Vm。
**加密的磁碟/磁碟區** | 不支援移轉具有加密的磁碟/磁碟區的 Vm。
**RDM/穿透式磁碟** | 如果 Vm 有 RDM 或傳遞磁碟，這些磁碟將不會複寫至 Azure。
**NFS** | 不會複寫的 Vm 上掛接為磁碟區的 NFS 磁碟區。
**目標磁碟** | Vm 只能移轉至 Azure 中的受控磁碟 (標準 HHD，進階 SSD)。


## <a name="agentless-migration-appliance-requirements"></a>無代理程式的移轉設備需求


**支援** | **詳細資料**
--- | ---
**ESXi** | 應用裝置 VM 必須執行版本 5.5 或更新版本的 ESXi 主機上部署。
**Azure Migrate 專案** | 應用裝置可以是與單一專案建立關聯。
**vCenter Server** | 設備可以探索 vCenter Server 上的最多 10,000 個 VMware Vm。<br/> 應用裝置可以連線到一個 vCenter Server。
**VDDK** | 如果您執行無代理程式的移轉，Azure 移轉的伺服器移轉，必須應用裝置 VM 上安裝 VMware vSphere 虛擬磁碟開發套件 (VDDK)。

## <a name="agentless-migration-url-access-requirements"></a>無代理程式的移轉 URL 存取需求

Azure Migrate 設備需要網際網路連線到網際網路。

- 當您部署應用裝置時，Azure Migrate 會連線能力檢查，以在下表中摘要的 Url。
- 如果您使用以 URL 為基礎的 firewall.proxy，允許存取這些 Url，並確定 proxy 解析的查閱 Url 時收到的任何 CNAME 記錄。

**URL** | **詳細資料**  
--- | --- 
*.portal.azure.com | 瀏覽至 Azure 入口網站中的 Azure Migrate。
*.windows.net | 登入您的 Azure 訂用帳戶。
*.microsoftonline.com | 建立設備，以使用 Azure Migrate 服務進行通訊的 Active Directory 應用程式。
management.azure.com | 建立設備，以使用 Azure Migrate 服務進行通訊的 Active Directory 應用程式。
dc.services.visualstudio.com | 上傳用於內部監視的應用程式記錄檔。
*.vault.azure.net | 管理在 Azure Key Vault 的祕密。
*.servicebus.windows.net | 應用裝置與 Azure Migrate 服務之間的通訊。
*.discoverysrv.windowsazure.com<br/> *.migration.windowsazure.com<br/> *.hypervrecoverymanager.windowsazure.com | 連接到 Azure Migrate 服務的 Url。
*.blob.core.windows.net | 將資料上傳至儲存體帳戶。


## <a name="agentless-migration-port-requirements"></a>無代理程式的移轉連接埠需求

**裝置** | **連接**
--- | --- 
應用裝置 | 輸出 TCP 連接埠 3389 將複寫的資料上傳至 Azure，以及與 Azure Migrate 通訊，複寫和移轉。
vCenter 伺服器 | TCP 連接埠 443 連線至允許的應用裝置，可協調複寫-建立快照集，複製資料的輸入的連線發行的快照集
vSphere/EXSI 主機 | 傳入 TCP 通訊埠 902 的設備，以從快照集複寫資料。 


## <a name="agent-based-migration-vmware-server-requirements"></a>代理程式為基礎的移轉-VMware 伺服器需求

下表摘要說明評估支援與 VMware 虛擬化伺服器的限制。

**支援** | **詳細資料**
--- | ---
**vCenter server/ESXI** | 您移轉的 VMware Vm 必須由一或多個 vCenter 伺服器管理執行 5.5、 6.0、 6.5、 或 6.7、 或在 vSphere 5.5、 6.0、 6.5 或 6.7 版 ESXI 主機上執行。

### <a name="agent-based-migration-vcenter-server-permissions"></a>代理程式為基礎的移轉 vCenter 伺服器的權限

**Permissions** | **詳細資料**
--- | --- 
Datastore.AllocateSpace | 允許的資料存放區上的空間配置，為虛擬機器快照式、 複製或虛擬磁碟。 
Datastore.Browse | 允許瀏覽 VM 進行疑難排解的快照集建立和刪除的記錄檔。
Datastore.LowLevelFileOperations | 允許讀取、 寫入、 刪除和重新命名作業進行疑難排解的快照集建立/刪除資料存放區瀏覽器中。
Datastore.UpdateVirtualMachineFiles | 允許 resignatured 資料存放區之後更新資料存放區上的 VM 檔案的路徑。
Network.AssignNetwork | 允許指派給 VM 資源的網路。
AssignVirtualMachineToResourcePool | 允許的資源集區 VM 的指派。
Resource.MigratePoweredOffVirtualMachine | 允許電源關閉 VM 到不同的資源集區或主機的移轉。
Resource.MigratePoweredOnVirtualMachine | 允許使用 vMotion 到不同的資源集區或主機上開啟 VM 的移轉。
Tasks.CreateTask | 允許的擴充功能來建立使用者定義的工作。
Tasks.UpdateTask | 允許延伸模組更新使用者定義的工作。
VirtualMachine.Configuration。 | 允許設定 VM 選項和裝置。
虛擬 Machine.Interaction.AnswerQuestion | 允許 VM 狀態轉換或執行階段錯誤的問題的解決的方式。
Virtual Machine.Interaction.DeviceConnection | 允許變更 VM 的 disconnectable 虛擬裝置的連線的狀態。
Virtual Machine.Interaction.ConfigureCDMedia | 允許的虛擬 DVD 或 CD-ROM 裝置的設定。
Virtual Machine.Interaction.ConfigureFloppyMedia | 允許設定虛擬軟碟機的裝置。
Virtual Machine.Interaction.PowerOff | 可讓您要移轉至 Azure 期間，關閉電源的 VM。
虛擬 Machine.Interaction.PowerOn | 允許開啟 VM 電源關閉、 電源和繼續已暫停的 VM。
Virtual Machine.Interaction.VMwareToolsInstall | 允許掛接及卸載 VMware 工具 CD 安裝程式做為客體作業系統的 CD-ROM。
VirtualMachine.Inventory.CreateNew | 允許建立 VM，以及所需的資源配置。
VirtualMachine.Inventory.Register | 允許加入現有的 VM 到 vCenter Server 或主應用程式清查。
VirtualMachine.Inventory.Unregister | 允許取消註冊的 Vm 從 vCenter Server 或主應用程式清查。
VirtualMachine.Provisioning.AllowVirtualMachineFilesUpload | 允許的 VM，包括 vmx、 磁碟、 記錄和 nvram 與相關聯檔案的寫入作業。
VirtualMachine.Provisioning.AllowVirtualMachineDownload | 允許讀取的作業，若要下載記錄檔用於疑難排解的 VM 相關聯的檔案。
VirtualMachine.SnapshotManagement.RemoveSnapshot | 允許移除從快照集的歷程記錄快照集。

## <a name="agent-based-migration-replication-appliance-requirements"></a>代理程式為基礎的移轉複寫設備需求

需求[複寫設備](migrate-replication-appliance.md)用於代理程式為基礎的移轉 VMware Vm 和實體的資料表中摘要說明使用 Azure 移轉的伺服器移轉的伺服器。

> [!NOTE]
> 當您設定複寫設備使用 OVA 所提供的範本在 Azure Migrate 中樞時，應用裝置執行 Windows Server 2016，並符合支援需求。 如果您設定複寫設備，以手動方式在實體伺服器上，則請確定它符合需求。



**元件** | **需求** 
--- | ---
 | **VMware 設定**（VMware VM 應用裝置）
**PowerCLI** | [PowerCLI 6.0 版](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1)應在複寫應用裝置是否執行於 VMware VM 上安裝。
**NIC 類型** | VMXNET3 （如果設備是 VMware VM）
 | **硬體設定** 
CPU 核心 | 8 
RAM | 16 GB
磁碟數量 | 三個：作業系統磁碟、 處理序伺服器快取磁碟，並保留磁碟機。
可用磁碟空間 （快取） | 600 GB
可用磁碟空間 (保留磁碟) | 600 GB
**軟體設定** | 
作業系統 | Windows Server 2016 或 Windows Server 2012 R2
作業系統地區設定 | 英文 (en-us)
TLS | 應該啟用 TLS 1.2。
.NET Framework | .NET framework 4.6 或更新版本應該安裝在電腦上 （有啟用強式密碼編譯。
MySQL | 應該在應用裝置上安裝 MySQL。<br/> 應該安裝 MySQL。 您可以手動安裝，或是 Site Recovery 可以將它安裝在應用裝置部署期間。 
其他應用程式 | 您不應該複寫應用裝置上執行其他應用程式。
Windows Server 角色 | 請勿啟用這些角色： <br> - Active Directory Domain Services <br>- 網際網路資訊服務 <br> - Hyper-V 
群組原則 | 請勿啟用這些群組原則： <br> - 防止存取命令提示字元。 <br> - 防止存取登錄編輯工具。 <br> - 檔案附件的信任邏輯。 <br> - 開啟指令碼執行。 <br> [深入了解](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 沒有預先存在的預設網站 <br> - 沒有預先存在的網站/應用程式接聽連接埠 443 <br>- 啟用[匿名驗證](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - 啟用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 設定 
**網路設定** | 
IP 位址類型 | Static 
連接埠 | 443 (控制通道協調流程)<br>9443 (資料傳輸) 
NIC 類型 | VMXNET3 

### <a name="replication-appliance-url-access"></a>複寫設備 URL 存取

複寫應用裝置必須能夠存取下列 Url。

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

可以使用其中一種方法在複寫應用裝置上安裝 MySQL。

**安裝** | **詳細資料**
--- | ---
手動下載並安裝 | 下載 MySQL 應用程式與將它放在資料夾 C:\Temp\ASRSetup，然後手動安裝。<br/> 當您設定 MySQL 會顯示為已安裝的應用裝置。 
不會下載線上 | 將 MySQL 安裝程式應用程式放在 C:\Temp\ASRSetup 資料夾中。 當您安裝應用裝置，並按一下以下載並安裝 MySQL 時，則安裝程式會使用您加入安裝程式。 
從 Azure 下載移轉 | 當您安裝應用裝置，並適用於 MySQL 提示時，選取**下載並安裝**。



## <a name="agent-based-migration-vmware-vm-requirements"></a>代理程式為基礎的移轉 VMware VM 需求

**支援** | **詳細資料**
--- | ---
**機器工作負載** | Azure Migrate 支援移轉的任何工作負載 (例如 Active Directory、 SQL server 等) 支援的機器上執行。
**作業系統** | 如需最新資訊，請檢閱[作業系統支援](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)Site recovery。 Azure Migrate 提供相同的虛擬機器的作業系統支援。
**Linux 檔案系統/客體儲存體** | 如需最新資訊，請檢閱[Linux 檔案系統支援](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)Site recovery。 Azure Migrate 會有相同的 Linux 檔案系統支援。
**網路/儲存體** | 如需最新資訊，請檢閱[網路](../site-recovery/vmware-physical-azure-support-matrix.md#network)並[儲存體](../site-recovery/vmware-physical-azure-support-matrix.md#storage)Site Recovery 的必要條件。 Azure Migrate 提供相同的網路/儲存體需求。
**Azure 需求** | 如需最新資訊，請檢閱[Azure 網路](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)，[儲存體](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)，並[計算](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute)站台復原的需求。 Azure Migrate 會有相同的 VMware 移轉的需求。
**行動服務** | 必須在每個您想要移轉的 VM 上安裝行動服務代理程式。
**目標磁碟** | Vm 只能移轉至 Azure 中的受控磁碟 (標準 HHD，進階 SSD)。

   

## <a name="agent-based-migration-url-access-requirements"></a>代理程式為基礎的移轉 URL 存取需求

VMware Vm 上執行的行動服務需要網際網路連線到網際網路。

- 當您部署的行動服務時，它會連線能力檢查下表摘要說明的 Url。
- 如果您使用以 URL 為基礎的 firewall.proxy，允許存取這些 Url，並確定 proxy 解析的查閱 Url 時收到的任何 CNAME 記錄。

**URL** | **詳細資料**  
--- | --- 
*.portal.azure.com | 瀏覽至 Azure 入口網站中的 Azure Migrate。
*.windows.net | 登入您的 Azure 訂用帳戶。
*.microsoftonline.com | 建立設備，以使用 Azure Migrate 服務進行通訊的 Active Directory 應用程式。 
management.azure.com | 建立設備，以使用 Azure Migrate 服務進行通訊的 Active Directory 應用程式。
dc.services.visualstudio.com | 上傳用於內部監視的應用程式記錄檔。
*.vault.azure.net | 管理在 Azure Key Vault 的祕密。
*.servicebus.windows.net | 應用裝置與 Azure Migrate 服務之間的通訊。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | 連接到 Azure Migrate 服務的 Url。
*.blob.core.windows.net | 將資料上傳至儲存體帳戶。

## <a name="agent-based-migration-port-requirements"></a>代理程式為基礎的移轉連接埠需求

**裝置** | **連接**
--- | --- 
VM | 在 Vm 上執行的行動服務與內部部署組態伺服器通訊連接埠 HTTPS 443 上輸入，針對複寫管理。<br/><br/> VM 會透過輸入連接埠 HTTPS 9443 將複寫資料傳送至處理伺服器 (在設定伺服器電腦上執行)。 您可以修改此連接埠。
複寫應用裝置 | 複寫設備會協調透過輸出連接埠 HTTPS 443 與 Azure 的複寫。
處理序伺服器 | 處理伺服器會透過輸出連接埠 443 接收複寫資料、將其最佳化並加密，然後傳送至 Azure 儲存體。<br/> 根據預設處理序伺服器會複寫應用裝置上執行。

## <a name="azure-vm-requirements"></a>Azure VM 需求

所有內部部署複寫至 Azure 的虛擬機器必須符合此表中摘要說明 Azure VM 需求。 Site Recovery 執行時複寫的必要條件檢查，檢查會失敗，如果某些需求不符合。

**元件** | **需求** | **詳細資料**
--- | --- | ---
客體作業系統 | 確認支援的作業系統[使用無代理程式複寫的 VMware Vm](#agentless-migration-vmware-vm-requirements)，並針對[使用代理程式為基礎的複寫的 VMware Vm](#agent-based-migration-vmware-vm-requirements)。<br/> 您可以將任何支援的作業系統上執行的工作負載的移轉。 | 若不支援，則檢查會失敗。
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
在移轉 Windows 之後連線 | 若要連接至執行 Windows，在移轉後 Azure Vm:<br/> -移轉前上啟用 RDP，在內部部署 VM。 確定已針對 [公用]  設定檔新增 TCP 和 UDP 規則，且在 [Windows 防火牆]   > [允許的應用程式]  中已針對所有設定檔允許 RDP。<br/> 站對站 VPN 存取，請啟用 RDP，並允許在 RDP **Windows 防火牆** -> **允許應用程式和功能**如**網域和私人**網路。 此外，請檢查作業系統的 SAN 原則設為**OnlineAll**。 [深入了解](https://support.microsoft.com/kb/3031135)。 | 
連線之後移轉-Linux | 若要使用 SSH 的移轉後連接到 Azure Vm:<br/> 移轉前，在內部部署的電腦，請檢查安全殼層服務設為 [開始]，和防火牆規則允許 SSH 連線。<br/> 容錯移轉之後，Azure VM 上允許連入連線的網路安全性群組規則在容錯移轉的 VM，以及它所連接的 Azure 子網路的 SSH 連接埠。 此外，新增 vm 的公用 IP 位址。 |  


## <a name="next-steps"></a>後續步驟

[準備適用於 VMware](tutorial-prepare-vmware.md)評定及移轉。








