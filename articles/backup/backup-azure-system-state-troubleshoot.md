---
title: 使用 Azure 備份的系統狀態備份的疑難排解
description: 在 系統狀態備份的問題進行疑難排解。
services: backup
author: srinathvasireddy
manager: sivan
keywords: 如何備份;備份系統狀態
ms.service: backup
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: srinathvasireddy
ms.openlocfilehash: c9e5645fef952257580d74eb6e11911e2286bd79
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165122"
---
# <a name="troubleshoot-system-state-backup"></a>系統狀態備份的疑難排解

此文章說明您使用系統狀態備份時可能遇到之問題的解決方案。

## <a name="basic-troubleshooting"></a>基本疑難排解
我們建議您執行下列驗證，開始之前疑難排解系統狀態備份：

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

### <a name="limitation"></a>限制
- Microsoft 不建議使用系統狀態復原來復原到不同的硬體
- 系統狀態備份目前支援 「 內部 」 的 Windows 伺服器，這項功能不適用於 Azure Vm。

## <a name="pre-requisite"></a>必要條件

使用 Azure 備份的系統狀態備份進行疑難排解之前，請確定您執行以下必要條件檢查。  

### <a name="verify-windows-server-backup-is-installed"></a>確認已安裝 Windows Server Backup

請確定 Windows Server Backup 已安裝並啟用在伺服器中。 若要檢查安裝狀態，請執行下列 PowerShell 命令：

 ```
 PS C:\> Get-WindowsFeature Windows-Server-Backup
 ```
如果輸出會顯示**安裝狀態**作為**可用**，則表示 Windows Server backup 功能是可供安裝但尚未安裝在伺服器上。 但是如果未安裝 Windows Server Backup，然後使用其中一種下列方法來安裝它。

**方法 1：安裝 Windows Server Backup 使用 PowerShell**

若要安裝 Windows Server Backup 使用 PowerShell，執行下列命令：

  ```
  PS C:\> Install-WindowsFeature -Name Windows-Server-Backup
  ```

**方法 2：安裝 Windows Server Backup 使用 伺服器管理員**

若要安裝 Windows Server Backup 使用 伺服器管理員，請執行如下：

1. 在  **Sever Manger**然後按一下**新增角色及功能**。 **新增角色及功能精靈**隨即出現。

    ![儀表板](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. 選取 [**安裝類型**然後按一下**下一步]** 。

    ![安裝類型](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. 從伺服器集區選取伺服器，然後按一下**下一步**。 在伺服器角色中，保留預設選項，然後按一下**下一步**。
4. 選取 [ **Windows Server Backup**中**功能**索引標籤，然後按一下**下一步]** 。

    ![特性](./media/backup-azure-system-state-troubleshoot/features.png)

5. 在 **確認**索引標籤上，按一下**安裝**開始安裝程序。
6. 在 **結果**索引標籤上，它會顯示在您的 Windows Server 成功安裝功能的 Windows Server Backup。

    ![結果](./media/backup-azure-system-state-troubleshoot/results.jpg)


### <a name="system-volume-information-permission"></a>系統磁碟區資訊 權限

請確定本機系統具有完整控制權**System Volume Information**資料夾位於已安裝 windows 的磁碟區。 這通常是**C:\System Volume Information**。 如果上述的權限未正確設定，Windows Server 備份可能會失敗

### <a name="dependent-services"></a>相依服務

請確定以下服務正處於執行狀態：

**服務名稱** | **啟動類型**
--- | ---
遠端程序呼叫 （rpc) | 自動
COM + 事件 System(EventSystem) | 自動
系統事件通知 Service(SENS) | 自動
磁碟區陰影 Copy(VSS) | 手動
Microsoft 軟體陰影複製 Provider(SWPRV) | 手動

### <a name="validate-windows-server-backup-status"></a>驗證 Windows Server Backup 狀態

若要驗證 Windows Server Backup 的狀態，請執行如下：

  * 請確定正在執行 WSB PowerShell

    -   執行`Get-WBJob`從提升權限的 PowerShell，請確定它不會傳回下列錯誤：

    > [!WARNING]
    > Get-WBJob:' Get WBJob' 詞彙無法辨識為 cmdlet、 函式、 指令碼檔案或可執行程式的名稱。 請檢查名稱拼字，如果名稱含有路徑，請確認路徑正確，然後再試一次。

    -   如果它失敗，發生這個錯誤重新安裝 Windows Server Backup 功能，如步驟 1 的必要條件中所述，在伺服器電腦上。

  * 確保 WSB 備份是否正常運作，執行下列命令，從提升權限的命令提示字元：

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >取代的 X，您要儲存系統狀態的磁碟區的磁碟機代號與備份映像。

    - 定期執行檢查作業的狀態`Get-WBJob`命令從提升權限的 PowerShell        
    - 備份作業完成之後檢查作業的最終狀態執行`Get-WBJob -Previous 1`命令

如果作業失敗時，它會表示 WSB 問題可能會導致 MARS 代理程式的系統狀態備份失敗。

## <a name="common-errors"></a>常見的錯誤

### <a name="vss-writer-timeout-error"></a>VSS 寫入器逾時錯誤

| 徵狀 | 原因 | 解決方案
| -- | -- | --
| MARS 代理程式失敗且錯誤訊息：「 WSB 作業失敗，發生 VSS 錯誤。 請檢查 VSS 來解決失敗的事件記錄檔 」<br/><br/> -下列錯誤記錄檔位於 VSS 應用程式事件記錄檔：」 的 VSS 寫入器已拒絕事件與錯誤 0x800423f2，凍結和解除凍結事件之間的寫入器的逾時過期。 」| VSS 寫入器無法完成的時間，因為缺少的電腦上的 CPU 和記憶體資源 <br/><br/> 另一個的備份軟體已經在使用 VSS 寫入器，因此快照集作業無法完成此備份 | 等候 CPU/記憶體釋出了系統上或中止花費太多的記憶體/CPU 的處理程序並再次嘗試操作 <br/><br/>  等候完成，並在機器上不執行任何備份時，請再次嘗試操作於稍後進行中的備份


### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>磁碟空間不足以增加陰影複製

| 徵狀 | 解決方案
| -- | --
| MARS 代理程式失敗且錯誤訊息：備份失敗，因為陰影複製磁碟區不包含系統檔案的磁碟區上成長因為磁碟空間不足 <br/><br/> -接在錯誤/警告記錄檔位於 volsnap 系統事件記錄檔：「 發生足夠的磁碟空間成長的 c： 由於此失敗的陰影複製的陰影複製儲存體的 c： 的磁碟區上所有 c： 有風險的要刪除的磁碟區陰影複製 」 | -釋放事件記錄檔中反白顯示的磁碟區中的空間，使其沒有足夠的空間成長時正在進行備份的陰影複製 <br/><br/> -如果您在設定陰影複製空間，我們可以限制陰影複製所使用的空間量，如需詳細資訊請參閱此[文章](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax)


### <a name="efi-partition-locked"></a>鎖定的 EFI 磁碟分割

| 徵狀 | 解決方案
| -- | --
| MARS 代理程式失敗且錯誤訊息：「 系統狀態回最多失敗，因為 EFI 系統磁碟分割已鎖定。 這可以是因為存取由協力廠商安全性的系統磁碟區或備份軟體 」 | -如果此問題是因為協力廠商安全性軟體，然後您需要連絡防毒廠商，讓它們可讓 MARS 代理程式 <br/><br/> -如果正在執行協力廠商備份軟體，然後等候它完成，然後重試後，註冊


## <a name="next-steps"></a>後續步驟

- 如需 Resource Manager 部署中的 Windows 系統狀態的詳細資訊，請參閱[備份 Windows Server 系統狀態](backup-azure-system-state.md)
