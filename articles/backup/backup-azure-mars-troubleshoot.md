---
title: 針對 Azure 備份代理程式進行疑難排解
description: 針對 Azure 備份代理程式的安裝和註冊進行疑難排解
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: saurse
ms.openlocfilehash: 2c2ed46ed6e4a5d6663387777d3425d18b50500e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060213"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>針對 Microsoft Azure 復原服務 (MARS) 代理程式進行疑難排解

以下是您可能會在設定、註冊、備份和還原時遇到問題的解決方式。

## <a name="basic-troubleshooting"></a>基本疑難排解

我們建議您執行下列驗證，開始之前疑難排解 Microsoft Azure 復原服務 (MARS) 代理程式：

- [確保 Microsoft Azure 復原服務 (MARS) 代理程式是最新狀態](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [確保 MARS 代理程式和 Azure 之間具有網路連線能力](https://aka.ms/AB-A4dp50)
- 確保 Microsoft Azure 復原服務正在執行中 (在服務主控台中)。 視需要重新開機並重試作業
- [確保草稿資料夾位置具有 5-10% 的磁碟區空間可供使用](https://aka.ms/AB-AA4dwtt)
- [檢查是否有其他程序或防毒軟體在干擾 Azure 備份](https://aka.ms/AB-AA4dwtk)
- [已排程的備份失敗，但可以手動備份](https://aka.ms/ScheduledBackupFailManualWorks)
- 確保您的作業系統已更新到最新版本
- [請確定不支援的磁碟機和具有不支援屬性的檔案會從備份中排除](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- 確保受保護系統上的**系統時鐘**已設定為正確的時區 <br>
- [確定伺服器至少具有 .Net Framework 4.5.2 版和更新版本](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- 如果您嘗試在保存庫中**重新登錄伺服器**，則： <br>
  - 請確保已將伺服器上的代理程式解除安裝，並已從入口網站中刪除 <br>
  - 使用與一開始登錄伺服器時所用的相同複雜密碼 <br>
- 如果是離線備份，確保 離線備份作業開始之前，在來源和複製電腦上安裝 Azure PowerShell 3.7.0 版
- [Azure 虛擬機器上執行備份代理程式時的考量](https://aka.ms/AB-AA4dwtr)

## <a name="invalid-vault-credentials-provided"></a>提供的保存庫認證無效

**錯誤訊息**：提供的保存庫認證無效。 檔案已損毀或沒有與復原服務關聯的最新認證。 (識別碼：34513)

| 原因 | 建議的動作 |
| ---     | ---    |
| **保存庫認證不正確** <br/> <br/> 保存庫認證檔案可能已損毀，或可能已過期 （也就是下載超過 48 小時之前註冊時）| 從復原服務保存庫，從 Azure 入口網站下載新的認證 (請參閱*步驟 6*下方[**下載 MARS 代理程式**](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent)區段)，並執行如下： <ul><li> 如果您已經安裝並註冊 Microsoft Azure 備份代理程式，然後開啟 [Microsoft Azure 備份代理程式] MMC 主控台，並選擇**註冊伺服器**從完成 active directory 註冊新下載的 [動作] 窗格認證 <br/> <li> 如果新的安裝失敗，然後再次嘗試重新安裝使用新的認證</ul> **注意**：如果多個保存庫認證檔案會下載之前，只有最新下載的檔案是有效 48 小時內。 因此建議您下載全新新的保存庫認證檔。
| **Proxy 伺服器/防火牆封鎖<br/>或<br/>沒有網際網路連線** <br/><br/> 如果您的電腦或 Proxy 伺服器具有受限的網際網路存取則沒有列出必要的 Url 註冊將會失敗。| 若要解決此問題，請執行如下：<br/> <ul><li> 使用您的 IT 團隊，以確保系統具有網際網路連線<li> 如果您沒有使用 Proxy 伺服器，然後確定 [proxy] 選項未選取註冊代理程式時，請確認 proxy 設定步驟列出[這裡](#verifying-proxy-settings-for-windows)<li> 如果您需要防火牆/proxy 伺服器與網路小組確保下, 面 Url 和 IP 位址可以存取<br/> <br> **URL**<br> - *www.msftncsi.com* <br>-  *.Microsoft.com* <br> -  *.WindowsAzure.com* <br>-  *.microsoftonline.com* <br>-  *.windows.net* <br>**IP 位址**<br> - *20.190.128.0/18* <br> - *40.126.0.0/18* <br/></ul></ul>請嘗試重新註冊之後完成上述的疑難排解步驟
| **防毒軟體封鎖** | 如果您有伺服器上安裝的防毒軟體，然後從防毒掃描新增需要排除規則，下列檔案： <br/><ui> <li> *CBengine.exe* <li> *CSC.exe*<li> 臨時資料夾，預設位置是*C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch* <li> 在 [bin] 資料夾*C:\Program Files\Microsoft Azure Recovery Services Agent\Bin*

### <a name="additional-recommendations"></a>其他建議
- 移至*c: / Windows/Temp*並檢查是否有超過 60,000 或 65,000 具有.tmp 副檔名的檔案。 如果有，請刪除這些檔案
- 請確定電腦的日期和時間會比對與當地時區
- 請確定[下列](backup-configure-vault.md#verify-internet-access)網站會新增至 IE 信任的網站

### <a name="verifying-proxy-settings-for-windows"></a>Windows 驗證的 proxy 設定

- 下載**psexec**從[這裡](https://docs.microsoft.com/sysinternals/downloads/psexec)
- 執行此程序`psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"`命令從提升權限的提示字元：
- 這會啟動*Internet Explorer*視窗
- 移至*工具* -> *網際網路選項* -> *連線* -> *區域網路設定*
- 確認 proxy 設定*系統*帳戶
- 如果沒有任何 proxy 設定，而且會提供 proxy 詳細資料，然後移除詳細資料
-   如果 proxy 設定不正確的 proxy 詳細資料，請確定*Proxy IP*並*連接埠*詳細資料是否正確
- 關閉*Internet Explorer*

## <a name="unable-to-download-vault-credential-file"></a>無法下載保存庫認證檔

| 錯誤詳細資料 | 建議動作 |
| ---     | ---    |
|無法下載保存庫認證檔案。 (識別碼：403) | <ul><li> 請嘗試使用不同的瀏覽器下載保存庫認證，或執行下列步驟： <ul><li> 啟動 IE，按下 F12 鍵。 </li><li> 移至 [網路]  索引標籤，清除 IE 的快取和 Cookie </li> <li> 重新整理頁面<br>(或)</li></ul> <li> 檢查訂用帳戶是否已停用/過期<br>(或)</li> <li> 檢查是否有任何防火牆規則封鎖保存庫認證檔的下載 <br>(或)</li> <li> 確定您還沒有耗盡保存庫的限制 (每個保存庫 50 部電腦)<br>(或)</li>  <li> 確認使用者具有必要下載保存庫認證和保存庫註冊伺服器，請參閱 Azure 備份權限[文章](backup-rbac-rs-vault.md)</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure 復原服務代理程式無法連線至 Microsoft Azure 備份

| 錯誤詳細資料 | 可能的原因 | 建議動作 |
| ---     | ---     | ---    |
| **錯誤** <br /><ol><li>*Microsoft Azure 復原服務代理程式無法連線至 Microsoft Azure 備份。(識別碼：100050) 請檢查您的網路設定，並確保您能夠連線至網際網路*<li>*(407) 需要 Proxy 驗證* |Proxy 正在封鎖連線。 |  <ul><li>啟動 [IE]   > [設定]  [網際網路選項] >    > [安全性]   > [網際網路]  。 接著，選取 [自訂層級]  ，然後捲動，直到您看到檔案下載區段為止。 選取 [啟用]  。<li>您可能也必須在 IE [信任的網站](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins)中新增這些網站。<li>變更設定以使用 Proxy 伺服器。 接著提供 Proxy 伺服器詳細資料。<li> 如果您的電腦具有受限的網際網路存取，請確定電腦或 proxy 上的防火牆設定允許這些[Url](backup-configure-vault.md#verify-internet-access)並[IP 位址](backup-configure-vault.md#verify-internet-access)。 <li>如果您已在伺服器上安裝防毒程式，請從防毒掃描中排除下列檔案。 <ul><li>CBEngine.exe (而不是 dpmra.exe)。<li>CSC.exe (與 .NET Framework 相關)。 伺服器上所安裝的每個 .NET 版本都有一個 CSC.exe。 排除受影響伺服器上繫結至所有 .NET Framework 版本的 CSC.exe 檔案。 <li>臨時資料夾或快取位置。 <br>臨時資料夾或快取位置路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch  。<li>Bin 資料夾 C:\Program Files\Microsoft Azure Recovery Services Agent\Bin



## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>無法設定安全備份的加密金鑰

| 錯誤詳細資料 | 可能的原因 | 建議動作 |
| ---     | ---     | ---    |
| **錯誤** <br />*無法設定安全備份的加密金鑰。啟動未完全成功，但是加密複雜密碼已儲存至下列檔案*。 |<li>伺服器已經向另一個保存庫註冊。<li>在設定期間，複雜密碼已損毀。| 請從該保存庫取消註冊伺服器，然後用新的複雜密碼重新註冊。

## <a name="the-activation-did-not-complete-successfully"></a>啟動沒有成功完成

| 錯誤詳細資料 | 可能的原因 | 建議動作 |
|---------|---------|---------|
|**錯誤** <br />*啟動沒有成功完成。由於發生內部服務錯誤 [0x1FC07]，導致目前的操作失敗。請稍後再重試此操作。如果問題持續發生，請連絡 Microsoft 支援服務*     | <li> 臨時資料夾所在的磁碟區沒有足夠的空間。 <li> 並未正確地將臨時資料夾移至其他位置。 <li> 遺失 OnlineBackup.KEK 檔案。         | <li>升級至[最新版本](https://aka.ms/azurebackup_agent)的 MARS 代理程式。<li>將臨時資料夾或快取位置移至具有可用空間等於備份資料總大小 5-10% 的磁碟區。 若要正確移動快取位置，請參閱[關於 Azure 備份代理程式的問題](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)中的步驟。<li> 確定 OnlineBackup.KEK 檔案存在。 <br>臨時資料夾或快取位置路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch  。        |

## <a name="encryption-passphrase-not-correctly-configured"></a>未正確設定加密複雜密碼

| 錯誤詳細資料 | 可能的原因 | 建議動作 |
|---------|---------|---------|
|**錯誤** <br />*錯誤 34506。未正確設定儲存於此電腦上的加密複雜密碼*。    | <li> 臨時資料夾所在的磁碟區沒有足夠的空間。 <li> 並未正確地將臨時資料夾移至其他位置。 <li> 遺失 OnlineBackup.KEK 檔案。        | <li>升級至[最新版本](https://aka.ms/azurebackup_agent)的 MARS 代理程式。<li>將臨時資料夾或快取位置移至具有相當於備份資料總大小之 5-10% 可用空間的磁碟區。 若要正確移動快取位置，請參閱[關於 Azure 備份代理程式的問題](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)中的步驟。<li> 確定 OnlineBackup.KEK 檔案存在。 <br>臨時資料夾或快取位置路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch  。         |


## <a name="backups-dont-run-according-to-the-schedule"></a>備份未依據排程執行
如果排程的備份未自動觸發、而手動備份運作未發生任何問題時，請嘗試下列動作：

- 請確定 Windows Server 備份排程不會衝突與 Azure 檔案和資料夾備份的排程。

- 請確定 線上備份狀態設為**啟用**。 若要確認狀態執行如下：

  - 開啟**工作排程器**並展開**Microsoft**，然後選取**Online Backup**。
  - 按兩下 [Microsoft-OnlineBackup]  ，並移至 [觸發程序]  索引標籤。
  - 驗證是否要將狀態設定為**已啟用**。 如果沒有，然後選取**編輯** > **Enabled**核取方塊，然後按一下**確定**。

- 請確定此使用者帳戶來執行工作為 **系統**或是**本機系統管理員群組**伺服器上。 若要驗證的使用者帳戶，請前往**一般**索引標籤，然後檢查**安全性**選項。

- 請確定伺服器上已安裝 PowerShell 3.0 或更新版本。 若要檢查 PowerShell 版本，請執行下列命令，並確認「主要」  版本號碼等於或大於 3。

  `$PSVersionTable.PSVersion`

- 請確定下列路徑的一部分*PSMODULEPATH*環境變數

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- 如果將 *LocalMachine* 的 Powershell 執行原則設定為受限制，則觸發備份工作的 Powershell cmdlet 可能會失敗。 執行下列命令，在提升權限的模式中，以檢查並設定執行原則設為*Unrestricted*或*RemoteSigned*

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- 請確定沒有遺漏或損毀**PowerShell**模組**MSonlineBackup**。 如果沒有任何遺失或損毀的檔案，若要解決此問題，請執行如下：

  - 從任何電腦是否正常運作的 MARS 代理程式有 MSOnlineBackup 將資料夾複製從 *(C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)* 路徑。
  - 此資訊貼上有問題的機器中的相同路徑 *(C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)* 。
  - 如果 **MSOnlineBackup** 資料夾已經存在電腦中，貼上或取代它的內容檔案。


> [!TIP]
> 為確保會一致地套用所進行的變更，請在執行上述步驟之後重新啟動伺服器。


## <a name="troubleshoot-restore-issues"></a>針對還原問題進行疑難排解

Azure 備份可能未成功掛接復原磁碟區，即使數分鐘後仍未成功。 您也可能會在流程中收到錯誤訊息。 若要開始進行一般復原，請遵循下列步驟：

1.  如果已執行數分鐘，請取消進行中的掛接程序。

2.  請查看是否使用最新版的備份代理程式。 若要找出版本，請在 MARS 主控台的 [動作]  窗格中，選取 [關於 Microsoft Azure 復原服務代理程式]  。 確認 [版本]  號碼等於或高於[這篇文章](https://go.microsoft.com/fwlink/?linkid=229525)中提到的版本。 您可以從[這裡](https://go.microsoft.com/fwLink/?LinkID=288905)下載最新版本。

3.  移至 [裝置管理員]   > [儲存體控制器]  ，然後找出 [Microsoft iSCSI 啟動器]  。 如果您可以找到它，請直接移至步驟 7。

4.  如果您找不到 Microsoft iSCSI 啟動器服務，請試試看能否在 [裝置管理員]   > [儲存體控制器]  底下名為 [不明裝置]  、硬體識別碼為 **ROOT\ISCSIPRT** 的項目。

5.  以滑鼠右鍵按一下 [不明裝置]  ，然後選取 [更新驅動程式軟體]  。

6.  藉由選取 [自動搜尋更新的驅動程式軟體]  的選項，以更新驅動程式。 完成更新後應該會將 [不明裝置]  變更為 [Microsoft iSCSI 啟動器]  ，如下所示。

    ![Azure 備份裝置管理員的螢幕擷取畫面，已反白顯示儲存體控制器](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  移至 [工作管理員]   >  [服務 (本機)]   >  [Microsoft iSCSI 啟動器服務]  。

    ![Azure 備份工作管理員的螢幕擷取畫面，已反白顯示服務 (本機)](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  重新啟動 Microsoft iSCSI 啟動器服務。 若要這樣做，請用滑鼠右鍵按一下服務，選取 [停止]  、按一下滑鼠右鍵，然後選取 [啟動]  。

9.  使用[**立即還原**](backup-instant-restore-capability.md)重試復原。

如果復原仍然失敗，請重新啟動您的伺服器或用戶端。 如果不想重新啟動，或即使重新啟動伺服器之後仍然復原失敗，請嘗試從其他電腦復原。 依照[此文章](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)中的步驟執行。

## <a name="need-help-contact-support"></a>需要協助嗎？ 联系支持人员
如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟
* 在[如何使用 Azure 備份代理程式備份 Windows Server](tutorial-backup-windows-server-to-azure.md) 上取得更多詳細資料。
* 如果您需要還原備份，請使用本文來 [還原檔案到 Windows 電腦](backup-azure-restore-windows-server.md)。
