---
title: 疑難排解 Azure 備份代理程式
description: 疑難排解安裝和註冊 Azure 備份代理程式
services: backup
author: saurabhsensharma
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: saurse
ms.openlocfilehash: 437b175efad081b8382d80be8427aa074920fd3e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705044"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>疑難排解 Microsoft Azure 復原服務 (MARS) 代理程式

這篇文章說明如何解決的錯誤，您可能會看到期間設定、 註冊、 備份和還原。

## <a name="basic-troubleshooting"></a>基本疑難排解

建議在開始進行疑難排解 Microsoft Azure 復原服務 (MARS) 代理程式之前，請檢查下列項目：

- [確保是最新的 MARS 代理程式](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)。
- [請確定您已在 MARS 代理程式與 Azure 之間的網路連線](https://aka.ms/AB-A4dp50)。
- 確定 MARS 正在執行 （在服務主控台中）。 如果您要重新啟動，然後重試此作業。
- [確認 5%到 10%可用磁碟區空間中的暫存資料夾位置](https://aka.ms/AB-AA4dwtt)。
- [檢查是否有另一個處理程序或防毒軟體會干擾 Azure 備份](https://aka.ms/AB-AA4dwtk)。
- 如果排定的備份失敗，但手動備份的運作，請參閱[不執行備份，根據排程](https://aka.ms/ScheduledBackupFailManualWorks)。
- 請確定您的作業系統具有最新的更新。
- [請確定不支援的磁碟機和具有不支援屬性的檔案會從備份中排除](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)。
- 請確定受保護的系統上的時鐘已到正確的時區。
- [請確定.NET Framework 4.5.2 或更新版本已經安裝在伺服器上](https://www.microsoft.com/download/details.aspx?id=30653)。
- 如果您想要重新註冊您的伺服器至保存庫：
  - 請確定伺服器上解除安裝代理程式，而且，它會從入口網站中刪除。
  - 使用原先用來註冊伺服器的相同複雜密碼。
- 針對離線備份，請確定您開始備份之前，先在來源和複本電腦上安裝 Azure PowerShell 3.7.0。
- 如果 Azure 虛擬機器上執行的備份代理程式，請參閱[這篇文章](https://aka.ms/AB-AA4dwtr)。

## <a name="invalid-vault-credentials-provided"></a>提供的保存庫認證無效

**錯誤訊息**：提供的保存庫認證無效。 檔案已損毀或沒有與復原服務關聯的最新認證。 (識別碼：34513)

| 原因 | 建議動作 |
| ---     | ---    |
| **保存庫認證無效** <br/> <br/> 保存庫認證檔案可能已損毀，或可能已過期。 （比方說，他們可能已下載超過 48 小時之前註冊的時間。）| 從 Azure 入口網站上的復原服務保存庫下載新的認證。 (請參閱中的步驟 6[下載 MARS 代理程式](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent)一節。)然後採取下列步驟，視需要： <ul><li> 如果您已安裝並註冊 MARS，開啟 Microsoft Azure 備份代理程式 MMC 主控台，然後選取**註冊的伺服器**中**動作**窗格，即可完成新的註冊認證。 <br/> <li> 如果新的安裝失敗，請嘗試重新安裝使用新的認證。</ul> **注意**：如果您已下載多個保存庫認證檔案，只有最新的檔案接下來的 48 小時內是有效。 我們建議您下載新的保存庫認證檔。
| **Proxy 伺服器/防火牆封鎖了註冊** <br/>或 <br/>**沒有網際網路連線** <br/><br/> 如果您的電腦或 proxy 伺服器的網際網路連線能力有限，而且您不確定存取所需的 url，註冊將會失敗。| 執行下列步驟：<br/> <ul><li> 使用您的 IT 團隊，以確保系統具有網際網路連線。<li> 如果您不需要 proxy 伺服器，請確定 [proxy] 選項未選取，當您註冊代理程式。 [請檢查您的 proxy 設定](#verifying-proxy-settings-for-windows)。<li> 如果您有防火牆/proxy 伺服器，使用您網路的小組，以確保這些 Url 和 IP 位址存取：<br/> <br> **URL**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**IP 位址**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>請嘗試在完成上述的疑難排解步驟之後重新註冊。
| **防毒軟體封鎖註冊** | 如果您尚未在伺服器上安裝的防毒軟體，請將需要排除規則新增至這些檔案和資料夾的防毒掃描： <br/><ui> <li> CBengine.exe <li> CSC.exe<li> 臨時資料夾中。 其預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch。 <li> 在 C:\Program Files\Microsoft Azure Recovery Services Agent\Bin [bin] 資料夾中。

### <a name="additional-recommendations"></a>其他建議
- 移至 C:/Windows/Temp，並檢查具有 .tmp 副檔名的檔案是否超過 60,000 或 65,000 個。 如果有，請刪除這些檔案。
- 請確定電腦的日期和時間比對的當地時區。
- 請確定[這些網站](backup-configure-vault.md#verify-internet-access)會新增至您在 Internet Explorer 的信任網站。

### <a name="verifying-proxy-settings-for-windows"></a>Windows 驗證的 proxy 設定

1. 下載從 PsExec [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec)頁面。
1. 執行`psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"`從提升權限的命令提示字元。

   此命令會開啟 Internet Explorer。
1. 移至**工具** > **網際網路選項** > **連線** > **LAN 設定**。
1. 請檢查系統帳戶的 proxy 設定。
1. 如果沒有任何 proxy 設定，而且會提供 proxy 詳細資料，請移除詳細資料。
1. 如果 proxy 設定，但不正確的 proxy 詳細資料，請確定**Proxy IP**並**連接埠**詳細資料正確無誤。
1. 關閉 Internet Explorer。

## <a name="unable-to-download-vault-credential-file"></a>無法下載保存庫認證檔

| 錯誤   | 建議動作 |
| ---     | ---    |
|無法下載保存庫認證檔案。 (識別碼：403) | <ul><li> 請嘗試使用不同的瀏覽器中，下載保存庫認證，或採取下列步驟： <ul><li> 啟動 Internet Explorer。 選取 [F12]。 </li><li> 移至**網路**索引標籤並清除快取和 cookie。 </li> <li> 重新整理頁面。<br></li></ul> <li> 檢查訂用帳戶是否已停用/已過期。<br></li> <li> 檢查任何防火牆規則會封鎖下載。 <br></li> <li> 請確定您未耗盡 （每個保存庫的 50 部機器） 的保存庫的限制。<br></li>  <li> 請確定使用者具有 Azure 備份權限，才能下載保存庫認證，並向保存庫註冊伺服器。 請參閱[使用角色型存取控制來管理 Azure 備份復原點](backup-rbac-rs-vault.md)。</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure 復原服務代理程式無法連線至 Microsoft Azure 備份

| 錯誤  | 可能的原因 | 建議動作 |
| ---     | ---     | ---    |
| <br /><ul><li>Microsoft Azure 復原服務代理程式無法連線至 Microsoft Azure 備份。 (識別碼：100050) 請檢查您的網路設定，並確保您能夠連線到網際網路。<li>（407） 要求 proxy 驗證。 |Proxy 正在封鎖連線。 |  <ul><li>在 Internet Explorer 中，移至**工具** > **網際網路選項** > **安全性** > **網際網路**. 選取 **自訂層級**向下捲動至**檔案下載**一節。 選取 [啟用]  。<p>您可能也必須新增[Url 和 IP 位址](backup-configure-vault.md#verify-internet-access)到 Internet Explorer 中信任的網站。<li>變更設定以使用 Proxy 伺服器。 接著提供 Proxy 伺服器詳細資料。<li> 如果您的電腦具有受限的網際網路存取，請確定電腦或 proxy 上的防火牆設定允許這些[Url 和 IP 位址](backup-configure-vault.md#verify-internet-access)。 <li>如果您有伺服器上安裝的防毒軟體，請從防毒掃描中排除這些檔案： <ul><li>CBEngine.exe (而不是 dpmra.exe)。<li>CSC.exe (與 .NET Framework 相關)。 沒有針對每個伺服器上安裝的.NET Framework 版本的 CSC.exe。 排除受影響的伺服器上的所有.NET Framework 版本的 CSC.exe 檔案。 <li>臨時資料夾或快取位置。 <br>臨時資料夾或快取路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch。<li>在 C:\Program Files\Microsoft Azure Recovery Services Agent\Bin [bin] 資料夾中。


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>無法設定安全備份的加密金鑰

| 錯誤 | 可能的原因 | 建議動作 |
| ---     | ---     | ---    |
| <br />無法設定安全備份的加密金鑰。 啟動未完全成功，但加密複雜密碼已儲存至下列的檔案。 |<li>伺服器已經向另一個保存庫註冊。<li>在設定期間，複雜密碼已損毀。| 取消註冊該伺服器從保存庫，並以新的複雜密碼再次註冊。

## <a name="the-activation-did-not-complete-successfully"></a>啟動沒有成功完成

| 錯誤  | 可能的原因 | 建議動作 |
|---------|---------|---------|
|<br />啟動沒有成功完成。 由於發生內部服務錯誤 [0x1FC07]，導致目前的操作失敗。 請在一段時間之後重試此作業。 如果問題持續發生， 請連絡 Microsoft 支援服務。     | <li> 臨時資料夾位於磁碟區沒有足夠的空間。 <li> 臨時資料夾移不正確。 <li> 遺失 OnlineBackup.KEK 檔案。         | <li>升級至[最新版本](https://aka.ms/azurebackup_agent)的 MARS 代理程式。<li>將臨時資料夾或快取位置移至 介於 5%和備份資料總大小的 10%的可用空間的磁碟區。 若要正確移動快取位置，請參閱中的步驟[檔案和資料夾備份常見問題](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)。<li> 確定 OnlineBackup.KEK 檔案存在。 <br>*臨時資料夾或快取路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*。        |

## <a name="encryption-passphrase-not-correctly-configured"></a>未正確設定加密複雜密碼

| 錯誤  | 可能的原因 | 建議動作 |
|---------|---------|---------|
| <br />錯誤 34506。 未正確設定這部電腦上儲存的加密複雜密碼。    | <li> 臨時資料夾位於磁碟區沒有足夠的空間。 <li> 臨時資料夾移不正確。 <li> 遺失 OnlineBackup.KEK 檔案。        | <li>升級至[最新版本](https://aka.ms/azurebackup_agent)的 MARS 代理程式。<li>將臨時資料夾或快取位置移至 介於 5%和備份資料總大小的 10%的可用空間的磁碟區。 若要正確移動快取位置，請參閱中的步驟[檔案和資料夾備份常見問題](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)。<li> 確定 OnlineBackup.KEK 檔案存在。 <br>*臨時資料夾或快取路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*。         |


## <a name="backups-dont-run-according-to-schedule"></a>備份不根據排程執行
如果排定的備份不會自動觸發，但手動備份的正常運作，請嘗試下列動作：

- 請確定 Windows Server 備份排程不會與 Azure 檔案和資料夾的備份排程相衝突。

- 請確定線上備份的狀態設定為**啟用**。 若要確認狀態，執行下列步驟：

  1. 在 工作排程器，依序展開**Microsoft** ，然後選取**線上備份**。
  1. 按兩下**Microsoft OnlineBackup**並移至**觸發程序** 索引標籤。
  1. 如果要將狀態設定為核取**已啟用**。 如果沒有，請選取**編輯**，選取**已啟用**，然後選取**確定**。

- 請確定此使用者帳戶來執行工作為 **系統**或是**本機系統管理員群組**伺服器上。 若要驗證的使用者帳戶，請前往**一般**索引標籤，然後檢查**安全性**選項。

- 請確定伺服器上已安裝 PowerShell 3.0 或更新版本。 若要檢查 PowerShell 版本，請執行下列命令，並確認`Major`版本號碼會是 3 或更新版本：

  `$PSVersionTable.PSVersion`

- 請確定此路徑會是一部分`PSMODULEPATH`環境變數：

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- 如果 PowerShell 執行原則`LocalMachine`是設定為受限制，就會觸發備份工作的 PowerShell cmdlet 可能會失敗。 執行下列命令，在提高權限的模式，以檢查並設定執行原則設為`Unrestricted`或`RemoteSigned`:

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- 請確定沒有遺失或損毀的 PowerShell 模組 MSOnlineBackup 檔案。 如果有任何遺失或損毀的檔案，請採取下列步驟：

  1. 從任何具有正常運作的 MARS 代理程式的電腦，請從 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules 複製 MSOnlineBackup 資料夾。
  1. 在有問題的電腦、 貼上複製的檔案，在相同的資料夾位置 (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)。

     如果在電腦上已經有 MSOnlineBackup 資料夾，貼上的檔案，或取代任何現有的檔案。


> [!TIP]
> 若要確保以一致的方式套用變更，請執行上述步驟之後重新啟動伺服器。


## <a name="troubleshoot-restore-problems"></a>針對還原問題進行疑難排解

Azure 備份可能未成功掛接復原磁碟區，即使數分鐘後仍未成功。 您可能會在程序期間收到錯誤訊息。 若要開始一般復原，請採取下列步驟：

1.  如果已經執行數分鐘，請取消掛接程序。

2.  檢查您是否擁有備份代理程式的最新版本。 若要檢查版本，在**動作**MARS 主控台，選取窗格**有關 Microsoft Azure 復原服務代理程式**。 確認 [版本]  號碼等於或高於[這篇文章](https://go.microsoft.com/fwlink/?linkid=229525)中提到的版本。 選取此連結可[下載最新版](https://go.microsoft.com/fwLink/?LinkID=288905)。

3.  移至**裝置管理員** > **存放控制器**並找出**Microsoft iSCSI 啟動器**。 如果您找到它，請直接前往步驟 7。

4.  如果找不到的 Microsoft iSCSI 啟動器服務，嘗試尋找的項目底下**裝置管理員** > **存放控制器**名為**不明裝置**使用硬體識別碼**ROOT\ISCSIPRT**。

5.  以滑鼠右鍵按一下**不明裝置**，然後選取**更新驅動程式軟體**。

6.  藉由選取 [自動搜尋更新的驅動程式軟體]  的選項，以更新驅動程式。 此更新應變更**不明裝置**要**Microsoft iSCSI 啟動器**:

    ![Azure 備份裝置管理員的螢幕擷取畫面，已反白顯示儲存體控制器](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  移至**工作管理員** > **服務 （本機）**  > **Microsoft iSCSI 啟動器服務**:

    ![Azure 備份工作管理員的螢幕擷取畫面，已反白顯示服務 (本機)](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  重新啟動 Microsoft iSCSI 啟動器服務。 若要這樣做，請以滑鼠右鍵按一下服務，然後選取**停止**。 然後再按一下滑鼠右鍵並選取**啟動**。

9.  使用 [立即還原](backup-instant-restore-capability.md) 重試復原。

如果復原仍然失敗，請重新啟動您的伺服器或用戶端。 如果您不想要重新啟動，或如果復原仍然失敗即使您重新啟動伺服器，請嘗試[從另一部電腦復原](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員
如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟
* 取得詳細[如何使用 Azure 備份代理程式備份 Windows Server](tutorial-backup-windows-server-to-azure.md)。
* 如果您需要還原的備份，請參閱[將檔案還原到 Windows 電腦](backup-azure-restore-windows-server.md)。
