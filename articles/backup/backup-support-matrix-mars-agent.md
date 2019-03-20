---
title: 支援矩陣：使用 Azure 備份來備份執行 Microsoft Azure 復原服務 (MARS) 代理程式的機器
description: 本文摘要說明 Azure 備份支援，當您備份執行 Microsoft Azure 復原服務 (MARS) 代理程式的機器。
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 3e2c6a550a9358656fd0870c7e785d131c5b6380
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894388"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>使用 Microsoft Azure 復原服務 (MARS) 代理程式進行備份的支援矩陣

您可以使用[Azure 備份服務](backup-overview.md)備份內部部署機器和應用程式，並將 Azure 虛擬機器 (Vm) 備份。 本文摘要說明支援設定和限制，當備份機器的情況下，您在使用 Microsoft Azure 復原服務 (MARS) 代理程式。

## <a name="the-mars-agent"></a>MARS 代理程式

Azure Backup 將備份資料從內部部署機器和 Azure Vm 備份的復原服務保存庫，在 Azure 中使用 MARS 代理程式。 MARS 代理程式可以：
- 在內部部署 Windows 機器上執行，以便它們可以直接備份至 Azure 備份復原服務保存庫。
- 在 Windows Vm 上執行，以便它們可以直接備份到保存庫。
- Microsoft Azure 備份伺服器 (MABS) 或 System Center Data Protection Manager (DPM) 伺服器上執行。 在此案例中，機器和工作負載備份到 MABS 或 DPM 伺服器。 MARS 代理程式，然後將此伺服器備份到 Azure 的保存庫。 

備份選項取決於安裝代理程式。 如需詳細資訊，請參閱 <<c0> [ 使用 MARS 代理程式的 Azure 備份架構](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。 MABS 和 DPM 的備份架構的相關資訊，請參閱[備份至 DPM 或 MABS](backup-architecture.md#architecture-back-up-to-dpmmabs)。 另請參閱[需求](backup-support-matrix-mabs-dpm.md)備份架構。

**安裝** | **詳細資料**
--- | ---
下載最新版的 MARS 代理程式 | 您可以從保存庫或[從這裡直接下載](https://aka.ms/azurebackup_agent)最新版本的代理程式。
直接在電腦上安裝 | 您可以直接在內部部署 Windows 伺服器上，或所執行的是 Windows VM 上，安裝的 MARS 代理程式[支援的作業系統](https://docs.microsoft.com/en-us/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems)。
在備份伺服器上安裝 | 當您將 DPM 或 MABS 設定為備份至 Azure 時，您可以在伺服器上下載並安裝 MARS 代理程式。 您可以在上安裝代理程式[支援的作業系統](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)備份伺服器支援矩陣中。

> [!NOTE]
> 根據預設，會啟用備份的 Azure Vm 具有 Azure 備份的延伸模組安裝。 此擴充功能會備份整個 VM。 如果您想要備份特定的資料夾和檔案，而不是完整 VM，您可以在 Azure VM 上安裝 MARS 代理程式，讓其與擴充功能一起執行。
> 當您在 Azure VM 上執行的 MARS 代理程式時，它會備份檔案或資料夾會在 VM 上的暫存儲存位置。 如果檔案或資料夾移除暫存儲存體，或移除暫存儲存體，備份會失敗。


## <a name="cache-folder-support"></a>快取資料夾支援

當您使用 MARS 代理程式來備份資料時，代理程式會擷取資料的快照集，並將它儲存在本機快取資料夾，再將資料傳送至 Azure。 [快取 （臨時）] 資料夾中有幾項需求：

**快取** | **詳細資料**
--- | ---
大小 |  快取資料夾中的可用空間應該至少 5 到 10%的備份資料的整體大小。 
位置 | 快取資料夾必須要備份的電腦上本機儲存，而且它必須在線上。 在網路共用上、 卸除式媒體，或離線的磁碟區上，不應該是快取資料夾。 
資料夾 | 重複資料刪除磁碟區或資料夾的壓縮、 疏鬆，或有重新分析點，則應該加密快取資料夾。
位置的變更 | 您可以藉由停止備份引擎來變更快取位置 (`net stop bengine`) 並將快取資料夾複製到新的磁碟機。 （請確定新的磁碟機具有足夠的空間）。然後更新兩個登錄項目底下**HKLM\SOFTWARE\Microsoft\Windows Azure 備份**(**Config/ScratchLocation**和**Config/CloudBackupProvider/ScratchLocation**) 到新位置，重新啟動引擎。

## <a name="networking-and-access-support"></a>網路功能與存取支援

### <a name="url-access"></a>URL 存取

MARS 伺服器需要存取這些 URL：

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

### <a name="throttling-support"></a>節流支援

**功能** | **詳細資料**
--- | ---
頻寬控制 | 支援。 在 MARS 代理程式，使用**變更屬性**來調整頻寬。
網路節流 | 不能用於執行 Windows Server 2008 R2、 Windows Server 2008 SP2 或 Windows 7 的備份機器。

## <a name="support-for-direct-backups"></a>直接備份的支援

您可以使用 MARS 代理程式，以將備份直接部署至 Azure 在內部部署機器和 Azure Vm 執行某些作業系統上。 作業系統必須是 64 位元，而且應該執行的最新的服務套件和更新。 下表摘要說明這些作業系統：

**作業系統** | **檔案/資料夾** | **系統狀態**
--- | --- | ---
Windows 10 (企業版、專業版、家用版) | 是 | 否
Windows 8.1 (企業版、專業版)| 是 |否
Windows 8 (企業版、專業版) | 是 | 否
Windows 7 (Ultimate、企業版、專業版、家用進階版/基本版、入門版) | 是 | 否
Windows Server 2016 (Standard、Datacenter、Essentials) | 是 | 是
Windows Server 2012 R2(Standard、Datacenter、Essentials) | 是 | 是
Windows Server 2012 (Standard、Datacenter、Essentials) | 是 | 是
Windows Server 2008 R2 (Standard、Enterprise、Datacenter、Foundation) | 是 | 是
Windows Server 2008 SP2 (Standard、Datacenter、Foundation) | 是 | 否
Windows Storage Server 2016/2012 R2/2012 （Standard、 Workgroup） | 是 | 否

如需詳細資訊，請參閱 <<c0> [ 支援的 MABS 和 DPM 作業系統](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)。

## <a name="backup-limits"></a>備份限制

Azure 備份會限制可以備份檔案或資料夾的資料來源的大小。 您從單一磁碟區備份的項目不能超過此表中摘要說明的大小：

**作業系統** | **大小限制**
--- | ---
Windows Server 2012 或更新版本 |  54,400 GB
Windows Server 2008 R2 SP1 |    1,700 GB
Windows Server 2008 SP2 | 1,700 GB
Windows 8 或更新版本  | 54,400 GB
Windows 7   | 1,700 GB


## <a name="supported-file-types-for-backup"></a>支援的備份檔案類型

**類型** | **支援** 
--- | --- 
已加密   | 支援。 
已壓縮 | 支援。
疏鬆 | 支援。
已壓縮和疏鬆 | 支援。
永久連結  | 不支援。 略過。
重新分析點   | 不支援。 略過。
加密和疏鬆 |  不支援。 略過。
壓縮資料流   | 不支援。 略過。
疏鬆資料流   | 不支援。 略過。
（已同步的檔案是疏鬆資料流） 的 OneDrive  | 不支援。 

## <a name="supported-drives-or-volumes-for-backup"></a>支援的磁碟機或磁碟區進行備份

**磁碟機/磁碟區** | **支援** | **詳細資料**
--- | --- | ---
唯讀磁碟區   | 不支援 | 只有當磁碟區是可寫入，則適用於大量複製陰影服務 (VSS)。
離線磁碟區 | 不支援 |   磁碟區已上線時，只有 VSS 才能運作。
網路共用   | 不支援 |   磁碟區必須是本機伺服器上。
受 BitLocker 保護的磁碟區 | 不支援 |   備份開始之前，必須解除鎖定磁碟區。
檔案系統識別碼  | 不支援 |   只支援 NTFS。
卸除式媒體 | 不支援 |   所有的備份項目來源必須具有*修正*狀態。
刪除重複資料的磁碟機 | 支援 | Azure 備份會將刪除重複資料的資料轉換成一般資料。 最佳化、 加密、 存放區，以及將資料傳送至保存庫。

## <a name="support-for-initial-offline-backup"></a>支援初始離線備份

Azure 備份支援*離線植入*來使用磁碟，將初始備份資料傳輸至 Azure。 這項支援是初始備份可能位於 tb 的大小範圍是否有幫助。 支援離線備份的狀況：

- 直接執行的 MARS 代理程式的內部部署機器上檔案和資料夾的備份。
- 從 DPM 伺服器或 MABS 備份工作負載和檔案。

離線備份不能用於系統狀態檔案。

## <a name="support-for-data-restoration"></a>還原資料的支援

藉由使用[立即還原](backup-instant-restore-capability.md)功能的 Azure 備份，您可以還原資料複製到保存庫之前。 您正在備份的電腦必須執行.NET Framework 4.5.2 或更高版本。

備份無法還原到執行舊版作業系統的目標電腦。 例如，在 Windows 8 或更新版本，可以還原從執行 Windows 7 的電腦建立的備份。 但是，從執行 Windows 8 的電腦建立的備份無法還原執行 Windows 7 的電腦上。

## <a name="next-steps"></a>後續步驟
- 深入了解[備份使用 MARS 代理程式的架構](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。
- 了解什麼有支援時您[MABS 或 DPM 伺服器上執行的 MARS 代理程式](backup-support-matrix-mabs-dpm.md)。
