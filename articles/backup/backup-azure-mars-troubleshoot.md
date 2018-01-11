---
title: "疑難排解 Azure Backup Agent |Microsoft 文件"
description: "疑難排解安裝和註冊的 Azure Backup Agent"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shreeshd
editor: 
ms.assetid: 778c6ccf-3e57-4103-a022-367cc60c411a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/4/2017
ms.author: saurse;markgal;
ms.openlocfilehash: 52a32d61dd69110ace560fd1e52389140f322678
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2017
---
# <a name="troubleshoot-azure-backup-agent-configuration-and-registration-issues"></a>疑難排解 Azure 備份代理程式設定和註冊問題
## <a name="recommended-steps"></a>建議的步驟
請參閱下表以解決期間的組態或註冊的 Azure 備份代理程式可能會遇到的錯誤中建議的動作。

## <a name="invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>提供的保存庫認證無效。 檔案已損毀或沒有與復原服務關聯的最新認證。
| 錯誤詳細資料 | 可能的原因 | 建議的行動 |
| ---     | ---     | ---    |
| **錯誤** </br> *提供的保存庫認證無效。檔案已損毀或沒有與復原服務關聯的最新認證。(識別碼：34513)* | <ul><li> 保存庫認證不正確 （亦即，它們已下載多個至少 48 小時才能註冊時）。<li>Azure Backup Agent 不能將暫存檔案下載至 Windows Temp 資料夾。 <li>保存庫認證是在網路位置。 <li>TLS 1.0 已停用<li> 設定的 proxy 伺服器會封鎖連接。 <br> |  <ul><li>下載新的保存庫認證。<li>移至**網際網路選項** > **安全性** > **網際網路**。 接下來，選取**自訂層級**，和捲動，直到您看到檔案下載 > 一節。 然後選取**啟用**。<li>您可能也必須將網站加到[信任的網站](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)。<li>變更使用 proxy 伺服器設定。 然後提供 proxy 伺服器的詳細資料。 <li> 符合您的電腦的日期和時間。<li>移至 c: / Windows/Temp，並檢查是否有多個 60,000 或 65000.tmp 副檔名的檔案。 如果有，請刪除這些檔案。<li>您可以在伺服器上執行 SDP 套件來進行測試。 如果您收到錯誤訊息，不允許檔案下載，則很可能有大量的 c: / Windows/Temp 目錄中的檔案。<li>確定您已安裝的.NET framework 4.6.2。 <li>如果您已停用 TLS 1.0，因為 PCI 合規性，請參閱這[疑難排解頁面](https://support.microsoft.com/help/4022913)。 <li>如果您有伺服器上安裝的防毒軟體，請從防毒程式掃描排除下列檔案： <ul><li>CBengine.exe<li>CSC.exe，與.NET Framework。 沒有為每個.NET 版本都會安裝在伺服器上 CSC.exe。 排除 CSC.exe 檔案繫結到所有受影響的伺服器上的.NET framework 版本。 <li>臨時資料夾或快取位置。 <br>*[草稿] 資料夾或快取位置路徑的預設位置是 C:\Program Files\Microsoft Azure 復原服務 Agent\Scratch*。

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure 復原服務代理程式無法連線到 Microsoft Azure 備份

| 錯誤詳細資料 | 可能的原因 | 建議的行動 |
| ---     | ---     | ---    |
| **錯誤** </br><ol><li>*Microsoft Azure 復原服務代理程式無法連線至 Microsoft Azure 備份。(識別碼：100050) 請檢查您的網路設定，並確保您能夠連線至網際網路*<li>*(407) 需要 Proxy 驗證* |封鎖連線的 proxy。 |  <ul><li>請進入**網際網路選項** > **安全性** > **網際網路**。 然後選取**自訂層級**和捲動，直到您看到檔案下載 > 一節。 選取 [啟用]。<li>您可能也必須將網站加到[信任的網站](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)。<li>變更使用 proxy 伺服器設定。 然後提供 proxy 伺服器的詳細資料。 <li>如果您有伺服器上安裝的防毒軟體，請從防毒程式掃描排除下列檔案。 <ul><li>CBEngine.exe （而不是 dpmra.exe)。<li>CSC.exe （.NET Framework 相關）。 沒有為每個.NET 版本都會安裝在伺服器上 CSC.exe。 排除 CSC.exe 檔案繫結到所有受影響的伺服器上的.NET framework 版本。 <li>臨時資料夾或快取位置。 <br>*[草稿] 資料夾或快取位置路徑的預設位置是 C:\Program Files\Microsoft Azure 復原服務 Agent\Scratch*。

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>無法設定安全備份的加密金鑰

| 錯誤詳細資料 | 可能的原因 | 建議的行動 |
| ---     | ---     | ---    |      
| **錯誤** </br>*無法設定安全備份的加密金鑰。由於發生內部服務錯誤「無效輸入錯誤」，導致目前的操作失敗。請在一段時間之後重試此操作。如果問題持續發生，請連絡 Microsoft 支援*。 |伺服器已經註冊另一個保存庫。| 從保存庫伺服器取消登錄，並再次註冊。

## <a name="the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>啟動沒有成功完成。 由於發生內部服務錯誤 [0x1FC07]，導致目前的操作失敗

| 錯誤詳細資料 | 可能的原因 | 建議的行動 |
| ---     | ---     | ---    |          
| **錯誤** </br><ol><li>*啟動沒有成功完成。由於發生內部服務錯誤 [0x1FC07]，導致目前的操作失敗。請在一段時間之後重試此操作。如果問題持續發生，請連絡 Microsoft 支援服務* <li>*錯誤 34506。在此電腦上儲存的加密複雜密碼未正確設定*。 | <li> 臨時資料夾位於空間不足，無法在磁碟區上。 <li> 臨時資料夾已不正確地移到另一個位置。 <li> 遺漏 OnlineBackup.KEK 檔案。 | <li>移動的磁碟區的可用空間等於 5-10%的備份資料大小總計的臨時資料夾或快取位置。 若要正確地移動快取位置，請參閱中的步驟[疑問 Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)。<li> 請確認 OnlineBackup.KEK 檔案存在。 <br>*[草稿] 資料夾或快取位置路徑的預設位置是 C:\Program Files\Microsoft Azure 復原服務 Agent\Scratch*。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員
如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟
* 取得更多詳細資料上[如何備份使用 Azure Backup Agent 的 Windows Server](tutorial-backup-windows-server-to-azure.md)。
* 如果您需要還原備份，請使用本文來 [還原檔案到 Windows 電腦](backup-azure-restore-windows-server.md)。
