---
title: "針對 Azure 備份代理程式進行疑難排解 | Microsoft Docs"
description: "針對 Azure 備份代理程式的安裝和註冊進行疑難排解"
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
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2017
---
# <a name="troubleshoot-azure-backup-agent-configuration-and-registration-issues"></a>針對 Azure 備份代理程式的設定和註冊問題進行疑難排解
## <a name="recommended-steps"></a>建議的步驟
請參閱下列表格中的建議動作，以解決您可能在設定或註冊 Azure 備份代理程式期間遇到的錯誤。

## <a name="invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>提供的保存庫認證無效。 檔案已損毀或沒有與復原服務關聯的最新認證。
| 錯誤詳細資料 | 可能的原因 | 建議動作 |
| ---     | ---     | ---    |
| **錯誤** </br> *提供的保存庫認證無效。檔案已損毀或沒有與復原服務關聯的最新認證。(識別碼：34513)* | <ul><li> 保存庫認證無效 (亦即，在將它們下載超過 48 小時之後才註冊)。<li>Azure 備份代理程式無法將暫存檔案下載到 Windows Temp 資料夾。 <li>保存庫認證位於網路位置上。 <li>TLS 1.0 已停用<li> 已設定的 Proxy 伺服器正在封鎖連線。 <br> |  <ul><li>下載新的保存庫認證。<li>移至 [網際網路選項] > [安全性] > [網際網路]。 接下來，選取 [自訂層級]，然後捲動，直到您看到檔案下載區段為止。 接著選取 [啟用]。<li>您可能也必須將該網站新增到[信任的網站](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)。<li>變更設定以使用 Proxy 伺服器。 接著提供 Proxy 伺服器詳細資料。 <li> 使日期和時間與您的電腦相符。<li>移至 C:/Windows/Temp，並檢查具有 .tmp 副檔名的檔案是否超過 60,000 或 65,000 個。 如果有，請刪除這些檔案。<li>您可以在伺服器上執行 SDP 套件來進行測試。 如果您收到敘述不允許檔案下載的錯誤，極可能 C:/Windows/Temp 目錄中具有大量檔案。<li>確定您已安裝 .NET Framework 4.6.2。 <li>如果您因 PCI 合規性而停用了 TLS 1.0，請參閱這個[疑難排解頁面](https://support.microsoft.com/help/4022913)。 <li>如果您已在伺服器上安裝防毒程式，請從防毒掃描中排除下列檔案： <ul><li>CBengine.exe<li>與 .NET Framework 相關的 CSC.exe。 伺服器上所安裝的每個 .NET 版本都有一個 CSC.exe。 排除受影響伺服器上繫結至所有 .NET Framework 版本的 CSC.exe 檔案。 <li>臨時資料夾或快取位置。 <br>臨時資料夾或快取位置路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch。

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure 復原服務代理程式無法連線至 Microsoft Azure 備份

| 錯誤詳細資料 | 可能的原因 | 建議動作 |
| ---     | ---     | ---    |
| **錯誤** </br><ol><li>*Microsoft Azure 復原服務代理程式無法連線至 Microsoft Azure 備份。(識別碼：100050) 請檢查您的網路設定，並確保您能夠連線至網際網路*<li>*(407) 需要 Proxy 驗證* |Proxy 正在封鎖連線。 |  <ul><li>移至 [網際網路選項] > [安全性] > [網際網路]。 接著，選取 [自訂層級]，然後捲動，直到您看到檔案下載區段為止。 選取 [啟用]。<li>您可能也必須將該網站新增到[信任的網站](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)。<li>變更設定以使用 Proxy 伺服器。 接著提供 Proxy 伺服器詳細資料。 <li>如果您已在伺服器上安裝防毒程式，請從防毒掃描中排除下列檔案。 <ul><li>CBEngine.exe (而不是 dpmra.exe)。<li>CSC.exe (與 .NET Framework 相關)。 伺服器上所安裝的每個 .NET 版本都有一個 CSC.exe。 排除受影響伺服器上繫結至所有 .NET Framework 版本的 CSC.exe 檔案。 <li>臨時資料夾或快取位置。 <br>臨時資料夾或快取位置路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch。

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>無法設定安全備份的加密金鑰

| 錯誤詳細資料 | 可能的原因 | 建議動作 |
| ---     | ---     | ---    |      
| **錯誤** </br>*無法設定安全備份的加密金鑰。由於發生內部服務錯誤「無效輸入錯誤」，導致目前的操作失敗。請在一段時間之後重試此操作。如果問題持續發生，請連絡 Microsoft 支援服務*。 |伺服器已經向另一個保存庫註冊。| 從該保存庫中將伺服器取消註冊，然後重新註冊。

## <a name="the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>啟動沒有成功完成。 由於發生內部服務錯誤 [0x1FC07]，導致目前的操作失敗

| 錯誤詳細資料 | 可能的原因 | 建議動作 |
| ---     | ---     | ---    |          
| **錯誤** </br><ol><li>*啟動沒有成功完成。由於發生內部服務錯誤 [0x1FC07]，導致目前的操作失敗。請在一段時間之後重試此操作。如果問題持續發生，請連絡 Microsoft 支援服務* <li>*錯誤 34506。未正確設定儲存於此電腦上的加密複雜密碼*。 | <li> 臨時資料夾所在的磁碟區沒有足夠的空間。 <li> 並未正確地將臨時資料夾移至其他位置。 <li> 遺失 OnlineBackup.KEK 檔案。 | <li>將臨時資料夾或快取位置移至具有相當於備份資料總大小之 5-10% 可用空間的磁碟區。 若要正確移動快取位置，請參閱[關於 Azure 備份代理程式的問題](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)中的步驟。<li> 確定 OnlineBackup.KEK 檔案存在。 <br>臨時資料夾或快取位置路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員
如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟
* 在[如何使用 Azure 備份代理程式備份 Windows Server](tutorial-backup-windows-server-to-azure.md) 上取得更多詳細資料。
* 如果您需要還原備份，請使用本文來 [還原檔案到 Windows 電腦](backup-azure-restore-windows-server.md)。
