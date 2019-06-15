---
title: 備份檔案和資料夾，使用 Azure 備份時的常見問題
description: 說明備份檔案和資料夾，使用 Azure 備份的常見問題。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: dacurwin
ms.openlocfilehash: 56dc87b1cdf36d761c46133004a05f8fa225a091
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808307"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>將檔案和資料夾備份的相關常見問題 

本文提供常見問題的解答備份檔案與資料夾中的 Microsoft Azure 復原服務 (MARS) 代理程式也就大量湧現[Azure 備份](backup-overview.md)服務。

## <a name="general"></a>一般

### <a name="why-does-the-mars-agent-need-net-framework-452-or-higher"></a>MARS 代理程式為何需要.NET framework 4.5.2 或更新版本？

中所提供的新功能[立即還原](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine)需要.NET Framework 4.5.2 或更高版本。

## <a name="configure-backups"></a>設定備份

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>哪裡可以下載最新版的 MARS 代理程式？ 
備份 Windows Server 電腦、 System Center DPM 與 Microsoft Azure 備份伺服器時使用的最新的 MARS 代理程式可供[下載](https://aka.ms/azurebackup_agent)。 

### <a name="how-long-are-vault-credentials-valid"></a>多久是有效的保存庫認證？
保存庫認證將於 48 小時後過期。 如果認證檔案到期，再次下載這個檔案從 Azure 入口網站。

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>哪些磁碟機從我可以備份檔案和資料夾？ 

您無法備份下列類型的磁碟機和磁碟區：

* 卸除式媒體：所有備份項目來源必須報告為固定式。
* 唯讀磁碟區：必須為可寫入磁碟區，才能使磁碟區陰影複製服務 (VSS) 正常運作。
* 離線的磁碟區：必須為線上磁碟區，才能使 VSS 正常運作。
* 網路共用：必須為本機磁碟區，才能使用線上備份來備份伺服器。
* 受 BitLocker 保護的磁碟區：磁碟區必須先解除鎖定才能執行備份。
* 檔案系統識別碼：NTFS 是唯一支援的檔案系統。

### <a name="what-file-and-folder-types-are-supported"></a>支援哪些檔案和資料夾的類型？

支援下列類型：

* 已加密
* 已壓縮
* 疏鬆
* 已壓縮 + 疏鬆
* 永久連結：不支援並略過
* 重新分析點：不支援並略過
* 已加密 + 疏鬆：不支援並略過
* 壓縮資料流：不支援並略過
* 重新分析點，包括 DFS 連結和連接點


### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>我是否可以使用 MARS 代理程式來備份 Azure VM 上的檔案和資料夾？  
是。 Azure 備份提供 VM 層級備份 Azure vm 使用 Azure VM 代理程式的 VM 擴充功能。 如果您想要在客體 Windows 作業系統，以在 VM 上備份檔案和資料夾，您可以安裝 MARS 代理程式，若要這麼做。 

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>可以使用 MARS 代理程式來備份檔案和資料夾上的暫存儲存體的 Azure vm 嗎？ 
是。 安裝 MARS 代理程式，並備份檔案和資料夾在客體 Windows 作業系統上的暫時儲存體。 -備份工作失敗的相關資料抹除暫存儲存體。
- 如果刪除暫存儲存體資料，您只能還原到非揮發性儲存體。

### <a name="how-do-i-register-a-server-to-another-region"></a>如何註冊的伺服器到另一個區域？

備份資料會傳送至保存庫註冊伺服器的資料中心。 變更資料中心的最簡單方式是解除安裝並重新安裝代理程式，然後在您需要的區域中註冊新的保存庫的機器

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>會的 MARS 代理程式支援 Windows Server 2012 重複資料刪除？
是。 MARS 代理程式會將重複資料刪除的資料轉換成一般資料中，準備備份作業時。 然後最佳化備份的資料、 加密資料，並接著傳送到保存庫的 加密的資料。

## <a name="manage-backups"></a>管理備份

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>如果我重新命名為備份設定的 Windows 機器發生什麼事？

當您重新命名的 Windows 機器時，則會停止所有目前設定的備份。

- 您必須向備份保存庫註冊新的機器名稱。
- 當您向保存庫註冊新的名稱時，第一項作業會*完整*備份。
- 如果您需要復原已備份到採用舊伺服器名稱之保存庫的資料，請還原到替代位置復原資料精靈 中使用選項。 [深入了解](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)。 

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>什麼是備份的最大的檔案路徑長度？
MARS 代理程式依存於 NTFS，並使用檔案路徑長度規格受限於[Windows API](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths)。 如果您想要保護的檔案超過允許的值，備份父資料夾或磁碟機。  

### <a name="what-characters-are-allowed-in-file-paths"></a>在檔案路徑中允許哪些字元？

MARS 代理程式依存於 NTFS，並允許[支援的字元](/windows/desktop/FileIO/naming-a-file#naming-conventions)中檔案的名稱/路徑。

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>「 Azure 備份有尚未設定此伺服器 」 的警告會出現。
即使您已設定備份原則，當儲存在本機伺服器上的備份排程設定都不是儲存在備份保存庫中的設定相同，則可能會出現這個警告。
- 當伺服器或設定已復原至已知的良好狀態時，備份排程可能會變成未同步處理。
- 如果您收到此警告，[設定](backup-azure-manage-windows-server.md)同樣地，備份原則，然後重新同步處理本機伺服器與 Azure 的執行隨選備份。


## <a name="manage-the-backup-cache-folder"></a>管理備份的快取資料夾

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>什麼是快取資料夾的最低大小需求？
快取資料夾的大小可決定您正在備份的資料量。
- 快取資料夾的磁碟區應該等於備份資料總大小至少 5-10%的可用空間。
- 如果磁碟區有可用空間低於 5%，增加磁碟區的大小，或將快取資料夾移至具有足夠空間的磁碟區。
- 如果您備份 Windows 系統狀態時，您會需要額外 30-35 GB 的可用空間中包含的快取資料夾的磁碟區
### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>如何變更的 MARS 代理程式的快取位置？


1. 若要停止備份引擎已提升權限命令提示字元中執行下列命令：

    ```PS C:\> Net stop obengine```

2. 請勿移動檔案。 相反地，請將快取空間資料夾複製到不同的磁碟機具有足夠空間。
3. 使用新的快取資料夾的路徑更新下列登錄項目。<br/>

    | 登錄路徑 | 登錄金鑰 | 值 |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*「新的快取資料夾位置」* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*「新的快取資料夾位置」* |

4. 重新啟動備份引擎，在提升權限的命令提示字元：

    ```PS C:\> Net start obengine```

5. 已成功使用新的位置的備份完成之後，您可以移除原始的快取資料夾。


### <a name="where-should-the-cache-folder-be-located"></a>應該快取資料夾位於何處？

快取資料夾不建議使用下列位置︰

* 網路共用/卸除式媒體：快取資料夾必須是在需要使用線上備份進行備份之伺服器的本機位置。 不支援網路位置或卸除式媒體，例如 USB 磁碟機
* 離線的磁碟區：快取資料夾必須在線上才能使用 Azure 備份代理程式進行預期的備份

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>有不支援的任何屬性的快取資料夾嗎？
快取資料夾不支援下列屬性或其組合︰

* 已加密
* 已刪除重複資料
* 已壓縮
* 疏鬆
* 重新分析點

快取資料夾和中繼資料 VHD 都不具有 Azure 備份代理程式所需的屬性。

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>是否有辦法調整備份所使用的頻寬量？
 
是，您可以使用**變更屬性**中選項的 MARS 代理程式調整的頻寬與時間。 [了解更多](backup-configure-vault.md#enable-network-throttling)* *。

## <a name="restore"></a>Restore

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>如果我取消進行中的還原作業，會發生什麼事？

如果取消進行中的還原作業時，就會停止還原程序。 還原在取消之前所有的檔案保持在設定的目的地 （原始或替代位置），而不需要任何回復。


## <a name="next-steps"></a>後續步驟

[了解](tutorial-backup-windows-server-to-azure.md)如何備份 Windows 電腦。
