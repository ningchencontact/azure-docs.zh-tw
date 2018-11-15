---
title: 針對 Azure 備份代理程式進行疑難排解
description: 針對 Azure 備份代理程式的安裝和註冊進行疑難排解
services: backup
author: saurabhsensharma
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 7/25/2018
ms.author: saurse
ms.openlocfilehash: 9b31b250499bc49135a606bde8b26f6ff45cde31
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2018
ms.locfileid: "51278499"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>針對 Microsoft Azure 復原服務 (MARS) 代理程式進行疑難排解

以下是您可能會在設定、註冊、備份和還原時遇到問題的解決方式。

## <a name="invalid-vault-credentials-provided"></a>提供的保存庫認證無效
| 錯誤詳細資料 | 可能的原因 | 建議動作 |
| ---     | ---     | ---    |
| **錯誤** </br> *提供的保存庫認證無效。檔案已損毀或沒有與復原服務關聯的最新認證。(識別碼：34513)* | <ul><li> 保存庫認證無效 (亦即，在將它們下載超過 48 小時之後才註冊)。<li>MARS 代理程式無法將檔案下載到 Windows Temp 目錄。 <li>保存庫認證位於網路位置上。 <li>TLS 1.0 已停用。<li> 已設定的 Proxy 伺服器正在封鎖連線。 <br> |  <ul><li>下載新的保存庫認證。<li>移至 [網際網路選項] > [安全性] > [網際網路]。 接下來，選取 [自訂層級]，然後捲動，直到您看到檔案下載區段為止。 接著選取 [啟用]。<li>您可能也必須將該網站新增到[信任的網站](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)。<li>變更設定以使用 Proxy 伺服器。 接著提供 Proxy 伺服器詳細資料。 <li> 使日期和時間與您的電腦相符。<li>移至 C:/Windows/Temp，並檢查具有 .tmp 副檔名的檔案是否超過 60,000 或 65,000 個。 如果有，請刪除這些檔案。 您可以在伺服器上執行 SDP 套件來確認。 如果您收到敘述不允許檔案下載的錯誤，可能是因為 C:/Windows/Temp 目錄中有大量檔案。<li>確定您已安裝 .NET Framework 4.6.2。 <li>如果您因 PCI 合規性而停用了 TLS 1.0，請參閱這個[疑難排解頁面](https://support.microsoft.com/help/4022913)。 <li>如果您已在伺服器上安裝防毒程式，請從防毒掃描中排除下列檔案： <ul><li>CBengine.exe<li>與 .NET Framework 相關的 CSC.exe。 伺服器上所安裝的每個 .NET 版本都有一個 CSC.exe。 排除受影響伺服器上繫結至所有 .NET Framework 版本的 CSC.exe 檔案。 <li>臨時資料夾或快取位置。 <br>臨時資料夾或快取位置路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch。

## <a name="the-mars-agent-was-unable-to-connect-to-azure-backup"></a>MARS 代理程式無法連線至 Azure 備份

| 錯誤詳細資料 | 可能的原因 | 建議動作 |
| ---     | ---     | ---    |
| **錯誤** </br><ul><li>*Microsoft Azure 復原服務代理程式無法連線至 Microsoft Azure 備份。(識別碼：100050) 請檢查您的網路設定，並確保您能夠連線至網際網路*<li>*(407) 需要 Proxy 驗證* |Proxy 伺服器正在封鎖連線。 |  <ul><li>移至 [網際網路選項] > [安全性] > [網際網路]。 接著，選取 [自訂層級]，然後捲動，直到您看到檔案下載區段為止。 選取 [啟用]。<li>您可能也必須將該網站新增到[信任的網站](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)。<li>變更設定以使用 Proxy 伺服器。 接著提供 Proxy 伺服器詳細資料。 <li>如果您已在伺服器上安裝防毒程式，請從防毒掃描中排除下列檔案。 <ul><li>CBEngine.exe (而不是 dpmra.exe)。<li>CSC.exe (與 .NET Framework 相關)。 伺服器上所安裝的每個 .NET 版本都有一個 CSC.exe。 排除受影響伺服器上繫結至所有 .NET Framework 版本的 CSC.exe 檔案。 <li>臨時資料夾或快取位置。 <br>臨時資料夾或快取位置路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch。

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>無法設定安全備份的加密金鑰

| 錯誤詳細資料 | 可能的原因 | 建議動作 |
| ---     | ---     | ---    |      
| **錯誤** </br>*無法設定安全備份的加密金鑰。啟動未完全成功，但是加密複雜密碼已儲存至下列檔案*。 |<li>伺服器已經向另一個保存庫註冊。<li>在設定期間，複雜密碼已損毀。| 請從該保存庫取消註冊伺服器，然後用新的複雜密碼重新註冊。

## <a name="the-activation-did-not-complete-successfully"></a>啟動沒有成功完成

| 錯誤詳細資料 | 可能的原因 | 建議動作 |
|---------|---------|---------|
|**錯誤** </br><ol>*啟動沒有成功完成。由於發生內部服務錯誤 [0x1FC07]，導致目前的操作失敗。請在一段時間之後重試此作業。如果問題持續發生，請連絡 Microsoft 支援服務*     | <li> 臨時資料夾所在的磁碟區沒有足夠的空間。 <li> 並未正確地將臨時資料夾移至其他位置。 <li> 遺失 OnlineBackup.KEK 檔案。         | <li>升級至[最新版本](https://aka.ms/azurebackup_agent)的 MARS 代理程式。<li>將臨時資料夾或快取位置移至具有可用空間等於備份資料總大小 5-10% 的磁碟區。 若要正確移動快取位置，請參閱[關於 Azure 備份代理程式的問題](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)中的步驟。<li> 確定 OnlineBackup.KEK 檔案存在。 <br>臨時資料夾或快取位置路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch。        |

## <a name="encryption-passphrase-not-correctly-configured"></a>未正確設定加密複雜密碼

| 錯誤詳細資料 | 可能的原因 | 建議動作 |
|---------|---------|---------|
|**錯誤** </br><ol>*錯誤 34506。未正確設定儲存於此電腦上的加密複雜密碼*。    | <li> 臨時資料夾所在的磁碟區沒有足夠的空間。 <li> 並未正確地將臨時資料夾移至其他位置。 <li> 遺失 OnlineBackup.KEK 檔案。        | <li>升級至[最新版本](https://aka.ms/azurebackup_agent)的 MARS 代理程式。<li>將臨時資料夾或快取位置移至具有相當於備份資料總大小之 5-10% 可用空間的磁碟區。 若要正確移動快取位置，請參閱[關於 Azure 備份代理程式的問題](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)中的步驟。<li> 確定 OnlineBackup.KEK 檔案存在。 <br>臨時資料夾或快取位置路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch。         |

## <a name="backups-dont-run-according-to-the-schedule"></a>備份未依據排程執行
如果排程的備份未自動觸發、而手動備份運作未發生任何問題時，請嘗試下列動作： 
 
- 查看是否已在伺服器上安裝 PowerShell 3.0 或更新版本。 若要檢查 PowerShell 版本，請執行下列命令，並確認「主要」版本號碼等於或大於 3。

  `$PSVersionTable.PSVersion`

- 查看下列路徑是否是 *PSMODULEPATH* 環境變數的一部分。

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- 如果將 *LocalMachine* 的 Powershell 執行原則設定為受限制，則觸發備份工作的 Powershell cmdlet 可能會失敗。 在提高權限的模式中執行下列命令，以檢查執行原則，並加以設定為 Unrestricted 或 RemoteSigned。

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- 移至 [控制台] > [系統管理工具] > [工作排程器]。 依序展開 [Microsoft]，然後選取 [線上備份]。 按兩下 [Microsoft-OnlineBackup]，並移至 [觸發程序] 索引標籤。請確定狀態設為 [已啟用]。 如果不是，請選取 [編輯]，然後選取 [已啟用] 核取方塊。 在 [一般] 索引標籤上，移至 [安全性選項]。 請確定為執行工作而選取的使用者帳戶為 **SYSTEM**，或伺服器上的**本機系統管理員群組**。

> [!TIP]
> 為確保會一致地套用所進行的變更，請在執行上述步驟之後重新啟動伺服器。


## <a name="troubleshoot-restore-issues"></a>針對還原問題進行疑難排解

Azure 備份可能未成功掛接復原磁碟區，即使數分鐘後仍未成功。 您也可能會在流程中收到錯誤訊息。 若要開始進行一般復原，請遵循下列步驟： 

1.  如果已執行數分鐘，請取消進行中的掛接程序。

2.  請查看是否使用最新版的備份代理程式。 若要找出版本，請在 MARS 主控台的 [動作] 窗格中，選取 [關於 Microsoft Azure 復原服務代理程式]。 確認 [版本] 號碼等於或高於[這篇文章](https://go.microsoft.com/fwlink/?linkid=229525)中提到的版本。 您可以從[這裡](https://go.microsoft.com/fwLink/?LinkID=288905)下載最新版本。

3.  移至 [裝置管理員] > [儲存體控制器]，然後找出 [Microsoft iSCSI 啟動器]。 如果您可以找到它，請直接移至步驟 7。 

4.  如果您找不到 Microsoft iSCSI 啟動器服務，請試試看能否在 [裝置管理員] > [儲存體控制器] 底下名為 [不明裝置]、硬體識別碼為 **ROOT\ISCSIPRT** 的項目。

5.  以滑鼠右鍵按一下 [不明裝置]，然後選取 [更新驅動程式軟體]。

6.  藉由選取 [自動搜尋更新的驅動程式軟體] 的選項，以更新驅動程式。 完成更新後應該會將 [不明裝置] 變更為 [Microsoft iSCSI 啟動器]，如下所示。 

    ![Azure 備份裝置管理員的螢幕擷取畫面，已反白顯示儲存體控制器](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  移至 [工作管理員]  >  [服務 (本機)]  >  [Microsoft iSCSI 啟動器服務]。 

    ![Azure 備份工作管理員的螢幕擷取畫面，已反白顯示服務 (本機)](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  重新啟動 Microsoft iSCSI 啟動器服務。 若要這樣做，請用滑鼠右鍵按一下服務，選取 [停止]、按一下滑鼠右鍵，然後選取 [啟動]。

9.  使用 [立即還原] 重試復原。 

如果復原仍然失敗，請重新啟動您的伺服器或用戶端。 如果不想重新啟動，或即使重新啟動伺服器之後仍然復原失敗，請嘗試從其他電腦復原。 依照[此文章](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)中的步驟執行。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員
如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟
* 在[如何使用 Azure 備份代理程式備份 Windows Server](tutorial-backup-windows-server-to-azure.md) 上取得更多詳細資料。
* 如果您需要還原備份，請使用本文來 [還原檔案到 Windows 電腦](backup-azure-restore-windows-server.md)。
