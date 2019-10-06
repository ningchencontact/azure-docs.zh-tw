---
title: Microsoft Azure 備份伺服器和 System Center DPM 的支援矩陣
description: 本文摘要說明您使用 Microsoft Azure 備份伺服器或 System Center DPM 來備份內部部署和 Azure VM 資源時的 Azure 備份支援。
author: dcurwin
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: dacurwin
manager: carmonm
ms.openlocfilehash: d14cd996fe0e5a67cc3b554fe1e12146cd6b8e1c
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981074"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>使用 Microsoft Azure 備份 Server 或 System Center DPM 進行備份的支援矩陣

您可以使用[Azure 備份服務](backup-overview.md)來備份內部部署機器和工作負載，以及 Azure 虛擬機器（vm）。 本文摘要說明使用 Microsoft Azure 備份 Server （MABS）或 System Center Data Protection Manager （DPM）和 Azure 備份來備份機器的支援設定和限制。

## <a name="about-dpmmabs"></a>關於 DPM/MABS

[System CENTER DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807)是企業解決方案，可設定、協助及管理企業電腦和資料的備份和復原。 它是 [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) 產品套件的一部分。

MABS 是可以用來備份內部部署實體伺服器、Vm 和其上執行之應用程式的伺服器產品。

MABS 是以 System Center DPM 為基礎，並提供類似的功能，但有一些差異：

- MABS 不需要 System Center 授權即可執行。
- 針對 MABS 和 DPM，Azure 提供長期的備份儲存體。 此外，DPM 可讓您將資料備份在磁帶上進行長期儲存。 MABS 未提供這項功能。
- 您可以使用次要 DPM 服務器來備份主要 DPM 服務器。 次要伺服器會保護主伺服器資料庫和儲存在主伺服器上的資料來源複本。 如果主伺服器失敗，次要伺服器可以繼續保護主伺服器所保護的工作負載，直到主伺服器再次可用為止。  MABS 未提供這項功能。

您可以從[Microsoft 下載中心](https://www.microsoft.com/en-us/download/details.aspx?id=57520)下載 MABS。 它可以在內部部署或在 Azure VM 上執行。

DPM 和 MABS 皆提供備份多種不同應用程式和伺服器與用戶端作業系統的支援。 它們適用於多種備份案例：

- 您可以使用系統狀態或裸機備份在機器層級進行備份。
- 您可以備份特定磁碟區、共用、資料夾和檔案。
- 您可以使用優化的應用程式感知設定來備份特定的應用程式。

## <a name="dpmmabs-backup"></a>DPM/MABS 備份

使用 DPM/MABS 和 Azure 備份的備份可運作，如下所示：

1. DPM/MABS 保護代理程式會安裝在每一部要備份的電腦上。
1. 電腦和應用程式會備份至 DPM/MABS 上的本機儲存體。
1. 在 DPM 伺服器/MABS 上安裝 Microsoft Azure 復原服務 (MARS) 代理程式。
1. MARS 代理程式會使用 Azure 備份，將 DPM/MABS 磁片備份至 Azure 中的備份復原服務保存庫。

其他資訊：

- [深入了解](backup-architecture.md#architecture-back-up-to-dpmmabs) MABS 架構。
- 請參閱 MARS 代理程式[支援的內容](backup-support-matrix-mars-agent.md)。

## <a name="supported-scenarios"></a>支援的案例

**案例** | **代理程式** | **位置**
--- | --- | ---
**備份內部部署機器/工作負載** | DPM/MABS 保護代理程式會在您想要備份的機器上執行。<br/><br/> DPM/MABS 伺服器上的 MARS 代理程式。<br/> 啟用這項功能所需的 Microsoft Azure 復原服務代理程式或 Azure 備份代理程式的最低版本是 2.0.8719.0。  | DPM/MABS 必須在內部部署環境中執行。

## <a name="supported-deployments"></a>支援的部署

DPM/MABS 可以部署如下表中的摘要說明。

**部署** | **支援** | **詳細資料**
--- | --- | ---
**部署於內部部署** | 實體伺服器<br/><br/>Hyper-V VM<br/><br/> VMware VM | 如果 DPM/MABS 是安裝為 VMware VM，則只會備份在這些 Vm 上執行的 VMware Vm 和工作負載。
**部署為 Azure Stack VM** | 僅限 MABS | DPM 無法用來備份 Azure Stack VM。
**部署為 Azure VM** | 保護在這些 Vm 上執行的 Azure Vm 和工作負載 | 在 Azure 中執行的 DPM/MABS 無法備份內部部署機器。

## <a name="supported-mabs-and-dpm-operating-systems"></a>支援的 MABS 和 DPM 作業系統

Azure 備份可以備份正在執行下列任何作業系統的 DPM/MABS 實例。 作業系統應執行最新的服務套件和更新。

**案例** | **DPM/MABS**
--- | ---
**Azure VM 上的 MABS** | Windows Server 2012 R2。<br/><br/> Windows 2016 Datacenter。<br/><br/> Windows 2019 Datacenter。<br/><br/> 我們建議您從 marketplace 開始使用映射。<br/><br/> 最低 A2 標準，具有兩個核心和 3.5 GB 的 RAM。
**Azure VM 上的 DPM** | System Center 2012 R2 (含 Update 3 或更新版本)。<br/><br/> 符合 [System Center 需求](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server)的 Windows 作業系統。<br/><br/> 我們建議您從 marketplace 開始使用映射。<br/><br/> 最低 A2 標準，具有兩個核心和 3.5 GB 的 RAM。
**內部部署 MABS** | 支援的 64 位元作業系統：<br/><br/> MABS v3 和更新版本：Windows Server 2019 （Standard、Datacenter、Essentials）。 <br/><br/> MABS v2 和更新版本：Windows Server 2016 （Standard、Datacenter、Essentials）。<br/><br/> 所有 MABS 版本： Windows Server 2012 R2。<br/><br/>所有 MABS 版本：Windows Storage Server 2012 R2。
**內部部署 DPM** | 實體伺服器/Hyper-V VM：System Center 2012 SP1 或更新版本。<br/><br/> VMware VM：System Center 2012 R2 (含 Update 5 或更新版本)。

## <a name="management-support"></a>管理支援

**問題** | **詳細資料**
--- | ---
**安裝** | 在單一用途的電腦上安裝 DPM/MABS。<br/><br/> 請勿在網域控制站、安裝應用程式伺服器角色的電腦、執行 Microsoft Exchange System Center Operations Manager Server 的電腦上，或在叢集節點上安裝 DPM/MABS。<br/><br/> [檢查所有 DPM 系統需求](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server)。
**網域** | DPM/MABS 應該加入網域。 請先安裝 DPM/MABS，再將其加入網域。 不支援在部署之後將 DPM/MABS 移至新網域。
**儲存體** | DPM 2016/MABS v2 和更新版本支援新式備份儲存體（MB）。 MBS 不適用於 MABS v1。
**MABS 升級** | 您可以直接安裝 MABS v3，或是從 MABS v2 升級至 MABS v3。 [深入了解](backup-azure-microsoft-azure-backup.md#upgrade-mabs)。
**移動 MABS** | 使用 MABS 時，支援將 MABS 移至新伺服器，同時保留儲存體。<br/><br/> 伺服器必須與原始伺服器同名。 如果您想要保留相同的儲存體集區，並使用相同的 MABS 資料庫來儲存資料復原點，則不可變更名稱。<br/><br/> 您將需要 MABS 資料庫的備份，因為您將必須加以還原。

## <a name="mabs-support-on-azure-stack"></a>Azure Stack 上的 MABS 支援

您可以將 MABS 部署在 Azure Stack VM 上，以便從單一位置管理 Azure Stack VM 和工作負載的備份。

**元件** | **詳細資料**
--- | ---
**Azure Stack VM 上的 MABS** | 至少為 A2 大小。 我們建議您從 Azure Marketplace 開始使用 Windows Server 2012 R2 或 Windows Server 2016 映射。<br/><br/> 請勿在 MABS VM 上安裝其他任何專案。
**MABS 儲存體** | 針對 MABS VM 使用個別的儲存體帳戶。 在 MABS 上執行的 MARS 代理程式需要快取位置的暫存儲存體，並保存從雲端還原的資料。
**MABS 儲存體集區** | MABS 儲存集區的大小取決於連接至 MABS VM 的磁片數目和大小。 每個 Azure Stack VM 大小皆有磁碟數目上限。 例如，A2 是四個磁碟。
**MABS 保留期** | 請勿將備份的資料保留在本機 MABS 磁片上超過五天。
**MABS 相應增加** | 若要相應增加您的部署，您可以增加 MABS VM 的大小。 例如，您可以從 A 變更為 D 系列。<br/><br/> 您也可以確保您會定期將資料卸載備份至 Azure。 如有必要，您可以部署額外的 MABS 伺服器。
**MABS 上的 .NET Framework** | MABS VM 必須安裝 .NET Framework 3.3 SP1 或更新版本。
**MABS 網域** | MABS VM 必須加入網域。 具有管理員權限的網域使用者必須在 VM 上安裝 MABS。
**Azure Stack VM 資料備份** | 您可以備份檔案、資料夾和應用程式。
**支援的備份** | 這些作業系統支援您想要備份的 Vm：<br/><br/> Windows Server 半年通道（Datacenter、Enterprise、Standard）<br/><br/> Windows Server 2016、Windows Server 2012 R2、Windows Server 2008 R2
**Azure Stack Vm 的 SQL Server 支援** | 備份 SQL Server 2016、SQL Server 2014 SQL Server 2012 SP1。<br/><br/> 備份和復原資料庫。
**Azure Stack VM 的 SharePoint 支援** | Sharepoint 2016、SharePoint 2013、SharePoint 2010。<br/><br/> 備份和復原伺服器陣列、資料庫、前端和 web 伺服器。
**已備份 VM 的網路需求** | Azure Stack 工作負載中的所有 Vm 都必須屬於相同的虛擬網路，而且屬於相同的訂用帳戶。

## <a name="dpmmabs-networking-support"></a>DPM/MABS 網路支援

### <a name="url-access"></a>URL 存取

DPM 伺服器/MABS 需要存取下列 URL：

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>DPM/MABS 與 Azure 備份的連線

必須連線至 Azure 備份服務才能正常執行備份，且 Azure 訂用帳戶必須作用中。 下表說明不符合這兩個條件時的行為。

**MABS 對 Azure** | **訂用帳戶** | **備份/還原**
--- | --- | ---
已連線 | 有效 | 備份至 DPM/MABS 磁片。<br/><br/> 備份至 Azure。<br/><br/> 從磁片還原。<br/><br/> 從 Azure 還原。
已連線 | 已過期/取消佈建 | 不會備份至磁碟或 Azure。<br/><br/> 如果訂用帳戶已過期，您可以從磁片或 Azure 還原。<br/><br/> 如果訂用帳戶已解除委任，您就無法從磁片或 Azure 還原。 Azure 復原點已刪除。
未連線超過 15 天 | 有效 | 不會備份至磁碟或 Azure。<br/><br/> 您可以從磁碟或 Azure 還原。
未連線超過 15 天 | 已過期/取消佈建 | 不會備份至磁碟或 Azure。<br/><br/> 如果訂用帳戶已過期，您可以從磁片或 Azure 還原。<br/><br/> 如果訂用帳戶已解除委任，您就無法從磁片或 Azure 還原。 Azure 復原點已刪除。

## <a name="dpmmabs-storage-support"></a>DPM/MABS 儲存體支援

備份至 DPM/MABS 的資料會儲存在本機磁片儲存體上。

**儲存體** | **詳細資料**
--- | ---
**MBS** | DPM 2016/MABS v2 和更新版本支援新式備份儲存體（MB）。 MBS 不適用於 MABS v1。
**Azure VM 上的 MABS 儲存體** | 資料會儲存在連接至 DPM/MABS VM 的 Azure 磁片上，並在 DPM/MABS 中管理。 可用於 DPM/MABS 存放集區的磁片數目會受限於 VM 的大小。<br/><br/> A2 VM：4 個磁碟；A3 VM：8 個磁碟；A4 VM：16 個磁碟，每個磁碟的大小上限為 1 TB。 這會決定可用的備份儲存集區總數。<br/><br/> 您可以備份的資料量取決於連結的磁碟數目和大小。
**Azure VM 上的 MABS 資料保留** | 我們建議您在 DPM/MABS Azure 磁片上保留一天的資料，並從 DPM/MABS 備份至保存庫，以延長保留時間。 藉此，您就可以藉由將資料卸載至 Azure 備份來保護較大量的資料。

### <a name="modern-backup-storage-mbs"></a>新式備份儲存體 (MBS)

從 DPM 2016/MABS v2 （在 Windows Server 2016 上執行）和更新版本，您可以利用新式備份儲存體（MB）。

- MBS 備份會儲存在復原檔案系統 (ReFS) 磁碟上。
- MB 使用 ReFS 區塊複製來加快備份速度，並更有效率地使用儲存空間。
- 當您將磁片區新增至本機 DPM/MABS 存放集區時，您會使用磁碟機號來設定它們。 隨後，您可以在不同的磁碟區上設定工作負載儲存體。
- 當您建立用來將資料備份至 DPM/MABS 的保護群組時，您可以選取所要使用的磁碟機。 例如，您可能會將 SQL 或其他高 IOPS 工作負載的備份儲存在高效能磁片磁碟機上，並將較不常備份的工作負載儲存在較低的效能磁片磁碟機上。

## <a name="supported-backups-to-mabs"></a>MABS 支援的備份

下表摘要說明可從內部部署機器和 Azure VM 備份至 MABS 的項目。

**備份** | **版本** | **MABS** | **詳細資料** |
--- | --- | --- | --- |
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>(32/64 位元) | MABS v3、v2 | 內部部署。 | 磁碟區/共用/資料夾/檔案。<br/><br/> 支援已刪除重複資料的磁碟區。<br/><br/> 磁碟區至少必須是 1 GB 和 NTFS。 |
**Windows Server 2016 (Datacenter、Standard、非 Nano)**<br/><br/> 64/32 位元 | MABS v3、v2 | 內部部署/Azure VM。| 磁碟區/共用/資料夾/檔案；系統狀態/裸機。<br/><br/> 支援已刪除重複資料的磁碟區。 |
**Windows Server 2012 R2 (Datacenter 和 Standard)**<br/><br/> 64/32 位元 | MABS v3、v2 | 內部部署/Azure VM。 | **內部部署保護**：磁碟區/共用/資料夾/檔案；系統狀態/裸機。<br/><br/> **Azure VMprotection**：磁碟區/共用/資料夾/檔案。<br/><br/> 支援已刪除重複資料的磁碟區。 |
**Windows Server 2012 SP1 (Datacenter 和 Standard)**<br/><br/> 64/32 位元 | MABS v3、v2 <br/><br/> 必須安裝 [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)。 | 內部部署/Azure VM。 | **內部部署保護**：磁碟區/共用/資料夾/檔案；系統狀態/裸機。<br/><br/> **Azure VM 保護**：磁碟區/共用/資料夾/檔案。<br/><br/> 支援已刪除重複資料的磁碟區。 |
**Windows 2008 R2 SP1 (Standard 和 Enterprise)**<br/><br/> 64/32 位元 | 支援 MABS v3、v2。<br/><br/> 必須安裝 [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)。 | 內部部署/Azure VM。 |   **內部部署保護**：磁碟區/共用/資料夾/檔案；系統狀態/裸機。<br/><br/> **Azure VM 保護**：磁碟區/共用/資料夾/檔案。<br/><br/> 支援已刪除重複資料的磁碟區。 |
**Windows 2008 R2 (Standard 和 Enterprise)**<br/><br/> 64/32 位元 | 若為 MABS v3、v2，OS 必須執行 SP1。 | 內部部署/Azure VM。 | **內部部署保護**：磁碟區/共用/資料夾/檔案；系統狀態/裸機。<br/><br/> **Azure VM 保護**：磁碟區/共用/資料夾/檔案。<br/><br/> 支援已刪除重複資料的磁碟區。 |
**Windows Server 2008 SP2**<br/><br/> 64/32 位元 | MABS v3、v2 | 當 MABS 部署為 VMware VM 時，支援 MABS v2、v3。<br/><br/> 不支援在 Azure VM 上執行的 MABS。 | 磁碟區/共用/資料夾/檔案；系統狀態/裸機。 |
**Windows Storage Server 2008** | MABS v3、v2 | MABS 做為內部部署實體伺服器/Hyper-v VM。 <br/><br/> 不支援在 Azure VM 上執行的 MABS。 | 磁碟區/共用/資料夾/檔案；系統狀態/裸機。
**SQL Server 2017** | MABS v3 | 內部部署/Azure VM。| 備份 SQL Server 資料庫。<br/><br/> 不支援 SQL Server 叢集備份。<br/><br/>不支援儲存在 CSV 上的資料庫。 |
**SQL Server 2016/2016 SP1** | MABS v3、v2 | 內部部署/Azure VM。| 備份 SQL Server 資料庫。<br/><br/> 不支援 SQL Server 叢集備份。<br/><br/>不支援儲存在 CSV 上的資料庫。 |
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | MABS v3、v2 | 內部部署/Azure VM。| 備份 SQL Server 資料庫。<br/><br/> 不支援 SQL Server 叢集備份。<br/><br/>不支援儲存在 CSV 上的資料庫。 |
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | MABS v3、v2 | 內部部署。 | 備份獨立 Exchange 伺服器、DAG 下的資料庫。<br/><br/> 復原信箱、DAG 下的信箱資料庫。<br/><br/> 不支援 ReFS。<br/><br/> 備份非共用磁碟叢集。<br/><br/> 備份已設定連續複寫的 Exchange Server。 |
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | MABS v3、v2 | 內部部署/Azure VM。 | 備份伺服器陣列、前端網頁伺服器。<br/><br/> 復原伺服器陣列、資料庫、web 應用程式、檔案或清單專案、SharePoint 搜尋、前端網頁伺服器。<br/><br/> 您無法使用內容資料庫的 SQL Server AlwaysOn 來備份伺服器陣列。 |
**Windows Server 2016 上的 Hyper-V**<br/><br/> **Windows Server 2008 R2 (含 SP1)** | MABS v3、v2 | 內部部署。 | **Hyper-V 主機上的 MABS 代理程式**：備份整個 VM 和主機資料檔案。 備份使用本機儲存體的 VM、叢集中使用 CSV 儲存體的 VM，使用 SMB 檔案伺服器儲存體的 VM。<br/><br/> **客體 VM 上的 MABS 代理程式**：備份在 VM 上執行的工作負載。 CSV。<br/><br/> **復原**：VM、VHD/磁碟區/資料夾/檔案的項目層級復原。<br/><br/> **Linux VM**：當 Hyper-v 在 Windows Server 2012 R2 和更新版本上執行時進行備份。 Linux VM 的復原會對整個機器執行。 |
**VMware VM：vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3、v2 | 內部部署。 | 備份 Csv、NFS 和 SAN 儲存體上的 VMware Vm。<br/><br/> 復原整個 VM。<br/><br/> Windows/Linux 備份。<br/><br/> 檔案/資料夾的項目層級復原 (僅適用於 Windows)。<br/><br/> 不支援 VMware vApps。<br/><br/> Linux VM 的復原會對整個機器執行。 |

## <a name="supported-backups-to-dpm"></a>DPM 支援的備份

下表摘要說明可從內部部署機器和 Azure VM 備份至 DPM 的項目。

**備份** | **DPM** | **詳細資料**
--- | --- | ---
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>(32/64 位元) | 僅限內部部署。<br/><br/> 若要使用 DPM 2012 R2 備份 Windows 10，建議您安裝[Update 11](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager)。 | 磁碟區/共用/資料夾/檔案。<br/><br/> 支援已刪除重複資料的磁碟區。<br/><br/> 磁碟區至少必須是 1 GB 和 NTFS。
**Windows Server 2016 (Datacenter、Standard、非 Nano)**<br/><br/> 64/32 位元 | 內部部署/Azure VM。<br/><br/> 僅限 DPM 2016。| 磁碟區/共用/資料夾/檔案；系統狀態/裸機。<br/><br/> 支援已刪除重複資料的磁碟區。
**Windows Server 2012 R2 (Datacenter 和 Standard)**<br/><br/> 64/32 位元 | 內部部署/Azure VM。 | **內部部署保護**：磁碟區/共用/資料夾/檔案；系統狀態/裸機。<br/><br/> **Azure VM 保護**：磁碟區/共用/資料夾/檔案。<br/><br/> DPM 2012 R2 和更新版本支援重復資料刪除的磁片區。
**Windows Server 2012 SP1 (Datacenter 和 Standard)**<br/><br/> 64/32 位元 | 內部部署/Azure VM。 | **內部部署保護**：磁碟區/共用/資料夾/檔案；系統狀態/裸機。<br/><br/> **Azure VM 保護**：磁碟區/共用/資料夾/檔案。<br/><br/> DPM 2012 R2 和更新版本支援重復資料刪除的磁片區。
**Windows 2008 R2 SP1 (Standard 和 Enterprise)**<br/><br/> 64/32 位元 | 內部部署/Azure VM。<br/><br/> 必須安裝 [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)。 |   **內部部署保護**：磁碟區/共用/資料夾/檔案；系統狀態/裸機。<br/><br/> **Azure VM 保護**：磁碟區/共用/資料夾/檔案。
**Windows 2008 R2 (Standard 和 Enterprise)**<br/><br/> 64/32 位元 | 內部部署。<br/><br/> DPM 無法安裝為 VMware VM。<br/><br/> 不支援在 Azure VM 上執行的 DPM。 | **內部部署保護**：磁碟區/共用/資料夾/檔案；系統狀態/裸機。
**Windows Server 2008 SP2**<br/><br/> 64/32 位元 | 僅限內部部署。<br/><br/> 支援作為 VMware VM 執行的 DPM。 不支援以實體伺服器或 Hyper-V VM 的形式執行。 | 磁碟區/共用/資料夾/檔案；系統狀態/裸機。
**Windows Storage Server 2008** | 作為實體伺服器或 Hyper-V VM 執行的內部部署 DPM。 | 磁碟區/共用/資料夾/檔案；系統狀態/裸機。
**SQL Server 2017** | DPM SAC;執行更新彙總套件5或更新版本的 DPM 2016。<br/><br/> 內部部署/Azure VM。| 備份 SQL Server 資料庫。<br/><br/> 不支援 SQL Server 叢集備份。<br/><br/>不支援儲存在 CSV 上的資料庫。
**SQL Server 2016 SP1** | 不支援 DPM 2012 R2；支援 DPM SAC，以及執行更新彙總套件 4 或更新版本的 DPM 2016。<br/><br/> 內部部署/Azure VM。| 備份 SQL Server 資料庫。<br/><br/> 不支援 SQL Server 叢集備份。<br/><br/>不支援儲存在 CSV 上的資料庫。
**SQL Server 2016** | 不支援 DPM 2012 R2。 支援 DPM SAC、更新彙總套件2（含）以後版本的 DPM 2016。<br/><br/> 內部部署/Azure VM。| 備份 SQL Server 資料庫。<br/><br/> 不支援 SQL Server 叢集備份。<br/><br/>不支援儲存在 CSV 上的資料庫。
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | SQL Server 2014，其中包含執行更新彙總套件4和更新版本的 DPM 2012 R2。<br/><br/> 內部部署/Azure VM。| 備份 SQL Server 資料庫。<br/><br/> 不支援 SQL Server 叢集備份。<br/><br/>不支援儲存在 CSV 上的資料庫。
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | 使用 Exchange 2016 時，DPM 2012 R2 需要更新彙總套件 9 或更新版本。<br/><br/> 內部部署 | 備份獨立 Exchange 伺服器、DAG 下的資料庫。<br/><br/> 復原信箱、DAG 下的信箱資料庫。<br/><br/> 不支援 ReFS。<br/><br/> 備份非共用磁碟叢集。<br/><br/> 備份已設定連續複寫的 Exchange Server。
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | DPM 2016 和更新版本上的 SharePoint 2016。<br/><br/>內部部署/Azure VM。 | 備份伺服器陣列、前端網頁伺服器。<br/><br/> 復原伺服器陣列、資料庫、web 應用程式、檔案或清單專案、SharePoint 搜尋、前端網頁伺服器。<br/><br/> 您無法使用內容資料庫的 SQL Server AlwaysOn 來備份伺服器陣列。
**Windows Server 2016 上的 Hyper-V**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (含 SP1)** | 2016上的 hyper-v 支援 DPM 2016 和更新版本。<br/><br/> 內部部署。 | **Hyper-V 主機上的 MABS 代理程式**：備份整個 VM 和主機資料檔案。 備份使用本機儲存體的 VM、叢集中使用 CSV 儲存體的 VM，使用 SMB 檔案伺服器儲存體的 VM。<br/><br/> **客體 VM 上的 MABS 代理程式**：備份在 VM 上執行的工作負載。 CSV。<br/><br/> **復原**：VM、VHD/磁碟區/資料夾/檔案的項目層級復原。<br/><br/> **Linux VM**：當 Hyper-v 在 Windows Server 2012 R2 和更新版本上執行時進行備份。 Linux VM 的復原會對整個機器執行。
**VMware VM：vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3、v2 <br/><br/> DPM 2012 R2 需要 System Center 更新彙總套件 1 <br/><br/>內部部署。 | 備份 Csv、NFS 和 SAN 儲存體上的 VMware Vm。<br/><br/> 復原整個 VM。<br/><br/> Windows/Linux 備份。<br/><br/> 檔案/資料夾的項目層級復原 (僅適用於 Windows)。<br/><br/> 不支援 VMware vApps。<br/><br/> Linux VM 的復原會對整個機器執行。

- DPM/MABS 所備份的叢集工作負載應該與 DPM/MABS 位於相同網域，或位於子域/受信任網域中。
- 您可以使用 NTLM/憑證驗證，在不受信任的網域或工作群組中備份資料。

## <a name="next-steps"></a>後續步驟

- [深入了解](backup-architecture.md#architecture-back-up-to-dpmmabs) MABS 架構。
- [檢閱](backup-support-matrix-mars-agent.md) MARS 代理程式支援的作業。
- [設定](backup-azure-microsoft-azure-backup.md) MABS 伺服器。
- [設定 DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180)。
