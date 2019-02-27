---
title: 支援矩陣：使用 Azure 備份來備份執行 Microsoft Azure 復原服務 (MARS) 代理程式的機器
description: 本文摘要說明 Azure 備份在備份執行 Microsoft Azure 復原服務 (MARS) 代理程式的機器時有哪些支援。
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 9d0f751e8d0bc0275cc5fd2c47aaba7a6058931c
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430932"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>使用 Microsoft Azure 復原服務 (MARS) 代理程式進行備份的支援矩陣

您可以使用 [Azure 備份服務](backup-overview.md)來備份內部部署機器與應用程式及 Azure VM。 本文摘要說明使用 Microsoft Azure 復原服務 (MARS) 代理程式來備份機器的支援設定和限制。

## <a name="about-the-mars-agent"></a>關於 MARS 代理程式

Azure 備份會使用 MARS 代理程式將內部部署機器和 Azure VM 中的資料備份到 Azure 中的備份復原服務保存庫。 MARS 代理程式的用法如下：
- 在內部部署 Windows 機器上執行代理程式，以便其直接備份至 Azure 中的備份復原服務保存庫。
- 在 Windows Azure VM 上執行代理程式，以便其直接備份到保存庫。
- 在 Microsoft Azure 備份伺服器 (MABS) 或 System Center Data Protection Manager (DPM) 伺服器上執行代理程式。 在此案例中，機器和工作負載會備份到 MABS/DPM，然後使用 MARS 代理程式將 MABS/DPM 備份到 Azure 中的保存庫。 

您可以備份的內容取決於安裝的代理程式。

- [深入了解](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders)使用 MARS 代理程式的備份架構。
- [深入了解]() MABS/DPM 的[備份架構](backup-architecture.md#architecture-back-up-to-dpmmabs)和[需求](backup-support-matrix-mabs-dpm.md)。


## <a name="supported-installation"></a>支援的安裝

**安裝** | **詳細資料**
--- | ---
**下載最新版的 MARS 代理程式** | 您可以從保存庫或[從這裡直接下載](https://aka.ms/azurebackup_agent)最新版本的代理程式。
**直接在機器上安裝** | 您可以直接在內部部署 Windows 伺服器或執行任何[支援作業系統]()的 Windows Azure VM 上安裝 MARS 代理程式。
**在備份伺服器上安裝** | 當您將 DPM 或 MABS 設定為備份至 Azure 時，您可以在伺服器上下載並安裝 MARS 代理程式。 您可以根據備份伺服器支援矩陣中的[支援作業系統](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)來安裝代理程式。

> [!NOTE]
> 根據預設，可用於備份的 Azure VM 會安裝 Azure 備份擴充功能。 此擴充功能會備份整個 VM。 如果您想要備份特定的資料夾和檔案，而不是完整 VM，您可以在 Azure VM 上安裝 MARS 代理程式，讓其與擴充功能一起執行。
> 當您在 Azure VM 上執行 MARS 代理程式時，代理程式會備份 VM 上暫時儲存體中的檔案/資料夾。 如果從暫時儲存體中移除檔案/資料夾，或移除暫時儲存體，則備份將會失敗。


## <a name="cache-folder-support"></a>快取資料夾支援

當您使用 MARS 代理程式進行備份時，代理程式會擷取資料的快照集，並先將其儲存在本機快取資料夾中，然後再傳送至 Azure。 快取 (暫存) 資料夾會有一些需求。

**快取** | **詳細資料**
--- | ---
**快取大小** |  快取資料夾大小上的可用空間應至少為備份資料整體大小的 5-10%。 
**快取位置** | 快取資料夾必須位在要備份的機器中，而且必須為線上狀態。<br/><br/> 快取資料夾不得位於網路共用、卸除式媒體或離線的磁碟區中。 
**快取資料夾** | 快取資料夾應該加密、位在刪除重複資料的磁碟區或位在屬於已壓縮/疏鬆/重新分析點的資料夾上
**修改快取位置** | 若要變更快取位置，您可以停止備份引擎 (net stop bengine)，並將快取資料夾複製到新的磁碟機 (請確定有足夠的空間)。 然後將 HKLM\SOFTWARE\Microsoft\Windows Azure Backup 下的兩個登錄項目 (Config/ScratchLocation 和 Config/CloudBackupProvider/ScratchLocation) 更新為新位置，並重新啟動引擎。

## <a name="networking-and-access-support"></a>網路功能與存取支援

### <a name="url-access"></a>URL 存取

MARS 伺服器需要存取這些 URL：

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="throttling-support"></a>節流支援

**功能** | **詳細資料**
--- | ---
頻寬控制 | 支援<br/><br/> 使用 MARS 代理程式中的**變更屬性**來調整頻寬。
網路節流 | 不適用於執行 Windows Server 2008 R2、Windows Server 2008 SP2 或 Windows 7 的備份機器。

## <a name="support-for-direct-backups"></a>直接備份的支援

下表摘要說明哪些在內部部署機器和 Azure VM 上執行的作業系統可以使用 MARS 代理程式直接備份至 Azure。

- 所有作業系統皆為 64 位元。
- 所有作業系統應執行最新的服務套件和更新。
- 若要深入了解哪些 DPM 和 MABS 伺服器可以使用 MARS 代理程式進行備份，請檢閱[此資料表](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)。

**作業系統** | **檔案/資料夾** | **系統狀態**
--- | --- | ---
Windows 10 (企業版、專業版、家用版) | yes | 否
Windows 8.1 (企業版、專業版)| yes |否
Windows 8 (企業版、專業版) | yes | 否
Windows 7 (Ultimate、企業版、專業版、家用進階版/基本版、入門版) | yes | 否
Windows Server 2016 (Standard、Datacenter、Essentials) | yes | yes
Windows Server 2012 R2(Standard、Datacenter、Essentials) | yes | yes
Windows Server 2012 (Standard、Datacenter、Essentials) | yes | yes
Windows Server 2008 R2 (Standard、Enterprise、Datacenter、Foundation) | yes | yes
Windows Server 2008 SP2 (Standard、Datacenter、Foundation) | yes | 否
Windows Storage Server 2016/2012 R2/2012 (Standard、Workgroup) | yes | 否


## <a name="backup-limits"></a>備份限制

Azure 備份會對資料來源 (可以備份的檔案/資料夾) 限制大小。 從單一磁碟區中選取的備份項目大小不能超過此表摘要的大小。

**作業系統** | **大小限制**
--- | ---
Windows Server 2012 或更新版本 |  54,400 GB
Windows Server 2008 R2 SP1 |    1700 GB
Windows Server 2008 SP2 | 1700 GB
Windows 8 或更新版本  | 54,400 GB
Windows 7   | 1700 GB


## <a name="supported-file-types-for-backup"></a>支援的備份檔案類型

**類型** | **支援** 
--- | --- 
已加密   | yes 
已壓縮 | yes
疏鬆 | yes 
已壓縮和疏鬆 | yes
永久連結  | 不支援<br/><br/> Skipped
重新分析點   | 不支援<br/><br/> Skipped
加密和疏鬆 |  不支援<br/><br/> Skipped
壓縮資料流   | 不支援<br/><br/> Skipped
疏鬆資料流   | 不支援<br/><br/> Skipped
One Drive (同步的檔案為疏鬆串流)    | 不支援 

## <a name="supported-drivesvolumes-for-backup"></a>支援的備份磁碟機/磁碟區

**磁碟機/磁碟區** | **支援** | **詳細資料**
--- | --- | ---
唯讀磁碟區   | 不支援 | 必須是可寫入的磁碟區，才能使 VSS 正常運作。
離線磁碟區 | 不支援 |   必須為線上磁碟區，才能使 VSS 正常運作。
網路共用   | 不支援 |   必須是伺服器上可用於備份的本機磁碟區。
受 BitLocker 保護的磁碟區 | 不支援 |   磁碟區必須先解除鎖定，才能讓備份正常運作。
檔案系統識別碼  | 不支援 |   僅限 NTFS。
卸除式媒體 | 不支援 |   所有備份項目來源都必須是固定型態。
刪除重複資料的磁碟機 | 支援。<br/><br/> Azure 備份會將刪除重複資料的資料轉換成一般資料。 這可將資料最佳化、加密、儲存及傳送至保存庫。

## <a name="support-for-initial-offline-backup"></a>支援初始離線備份

Azure 備份支援「離線植入」，可使用磁碟將初始備份資料傳輸到 Azure。 如果您的初始備份可能有幾 TB，則適用此方式。 支援離線備份的狀況：

- 在執行 MARS 代理程式的內部部署機器上直接備份檔案和資料夾。
- 從 DPM 伺服器或 MABS 備份工作負載和檔案。
- 離線備份不能用於系統狀態檔案。


## <a name="support-for-restore"></a>支援還原

- Azure 備份的新[立即還原](/backup-instant-restore-capability.md)版本可讓您先還原資料，再將資料複製到保存庫。<br/><br/> 若要使用這項功能，要備份的機器必須執行 .NET Framework 4.5.2 或更高版本。
- 備份無法還原到執行舊版作業系統的目標電腦。 例如，從 Windows 7 電腦建立的備份可以在 Windows 8 或更新版本的電腦上還原。 但是，從 Windows 8 電腦建立的備份無法還原至 Windows 7 電腦。


## <a name="next-steps"></a>後續步驟
- [深入了解](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders)使用 MARS 代理程式的備份架構。
- [了解](backup-support-matrix-mabs-dpm.md)您在 Microsoft Azure 備份伺服器 (MABS) 或 System Center DPM 上執行 MARS 代理程式時的支援內容。


