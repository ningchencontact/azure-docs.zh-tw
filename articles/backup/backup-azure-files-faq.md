---
title: "備份 Azure 檔案服務常見問題集"
description: "本文提供有關如何在 Azure 中保護 Azure 檔案服務的詳細資料。 本摘要會顯示於搜尋結果中。"
services: backup
keywords: "請勿在未諮詢 SEO 之前新增或編輯關鍵字。"
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.service: backup
ms.workload: storage-backup-recovery
manager: carmonm
ms.openlocfilehash: d37e119709bc9d4643fcaa9512b5209d4139515e
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/22/2018
---
# <a name="questions-about-backing-up-azure-files"></a>有關備份 Azure 檔案服務的問題
本文提供有關備份 Azure 檔案服務的常見問題解答。 在某些答案中，有具有完整資訊的文章連結。 您也可以在 [論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)中張貼有關 Azure 備份服務的問題。

若要快速掃描本文中的各節內容，在**本文**下使用右方連結。

## <a name="configuring-the-backup-job-for-azure-files"></a>設定 Azure 檔案服務的備份作業

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-file-shares-br"></a>為何看不到某些我想要保護且包含有效檔案共用的儲存體帳戶？ <br/>
Azure 檔案備份目前為預覽狀態，僅支援可以針對備份設定的儲存體帳戶。 確定您所尋找的儲存體帳戶是支援的儲存體帳戶。

### <a name="why-cant-i-see-some-of-my-file-shares-in-the-storage-account-when-im-trying-to-configure-backup-br"></a>為何在嘗試設定備份時，在儲存體帳戶中看不到某些檔案共用？ <br/>
請檢查檔案共用是否已在相同的復原服務保存庫中受到保護。 確定您想要保護的檔案共用最近並未遭到刪除。

### <a name="why-cant-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync-br"></a>為何無法保護已連線到 Azure 檔案同步中同步群組的檔案共用？ <br/>
保護已連線到同步群組的 Azure 檔案共用目前處於有限預覽狀態。 請使用您的訂用帳戶識別碼寫信送至 [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) 以要求存取。 

### <a name="in-which-geos-can-i-back-up-azure-file-shares-br"></a>我可以在哪些地區備份 Azure 檔案共用 <br/>
Azure 檔案共用的備份功能目前處於預覽狀態，僅適用於下列地區。 
-   加拿大中部 (CNC)
-   加拿大東部 (CE) 
-   美國中部 (CUS)
-   東亞 (EA)
-   澳大利亞東部 (AE) 
-   印度中部 (INC) 
-   美國中北部 (NCUS) 
-   英國南部 (UKS) 
-   英國西部 (UKW) 
-   美國中西部 (WCUS)
-   美國西部 2 (WUS 2)

從 2 月 23 日起，Azure 檔案共用的備份功能適用於下列地區。
-   澳大利亞東南部 (ASE) 
-   巴西南部 (BRS) 
-   美國東部 (EUS) 
-   美國東部 2 (EUS2) 
-   北歐 (NE) 
-   美國中南部 (SCUS) 
-   東南亞 (SEA)
-   西歐 (WE) 
-   美國西部 (WUS)  

如果您在未列於上方的特定地區中需要使用該功能，請寫信送至 [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com)。

### <a name="how-many-file-shares-can-i-protect-in-a-vaultbr"></a>我可以在一個保存庫中保護多少個檔案共用？<br/>
在預覽期間，您可以在每個保存庫中保護最多 25 個儲存體帳戶的檔案共用。 您也可以在單一保存庫中保護最多 200 個檔案共用。 

## <a name="backup"></a>Backup 

### <a name="how-many-on-demand-backups-can-i-take-per-file-share-br"></a>每個檔案共用可以進行多少個隨選備份？ <br/>
在任何時間點，您可以針對一個檔案共用產生最多 200 個快照集，其中包含依照您的原則定義由 Azure 備份所產生的快照集。 如果您的備份因為達到此限制而開始失敗，請據以刪除隨選還原點。

### <a name="after-enabling-virtual-networks-on-my-storage-account-the-backup-of-file-shares-in-the-account-started-failing-why"></a>在我的儲存體帳戶上啟用虛擬網路之後，帳戶中的檔案共用備份卻開始失敗。 原因為何？
目前，已啟用虛擬網路的儲存體帳戶不支援 Azure 檔案備份。 在您想要備份的儲存體帳戶中停用虛擬網路。 

## <a name="restore"></a>Restore

### <a name="can-i-recover-from-a-deleted-file-share-br"></a>我可以從已刪除的檔案共用進行復原嗎？ <br/>
當您嘗試刪除檔案共用時，系統將會顯示您繼續執行時將會一併刪除的備份清單，並要求您進行確認。 您無法從已刪除的檔案共用進行還原。

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-a-file-share-br"></a>如果我停止保護檔案共用，是否可以從備份還原？ <br/>
是。 如果您在停止保護時選擇 [保留備份資料]，則您可以從所有現有的還原點進行還原。

## <a name="manage-backup"></a>管理備份

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it-br"></a>我可以存取 Azure 備份所產生的快照集並加以裝載嗎？ <br/>
在入口網站、PowerShell 或 CLI 中檢視快照集，即可存取 Azure Backup 所產生的所有快照集。 您可以使用這裡的程序進行裝載。

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups-br"></a>我可以為備份設定的保留期上限為何？ <br/>
Azure 檔案共用的備份可讓您保留每日備份最長 120 天。

### <a name="what-happens-when-i-change-the-backup-policy-for-a-file-share-br"></a>變更檔案共用的備份原則時會發生什麼狀況？ <br/>
在檔案共用上套用新原則後，就會遵循新原則的排程和保留期。 如果延長保留期，會標示現有的復原點，以依據新的原則加以保留。 如果縮短保留期，則會標示現有的復原點，以便在下次清除作業中剪除然後刪除。


## <a name="see-also"></a>另請參閱
此資訊僅只關於備份 Azure 檔案，若要深入了解 Azure 備份的其他領域，請參閱其他備份常見問題集：
-  [復原服務保存庫常見問題集](backup-azure-backup-faq.md)
-  [Azure VM 備份常見問題集](backup-azure-vm-backup-faq.md)
-  [Azure 備份代理程式常見問題集](backup-azure-file-folder-backup-faq.md)
