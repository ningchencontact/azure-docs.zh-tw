---
title: 使用 Azure 備份進行系統狀態備份的疑難排解
description: 針對系統狀態備份中的問題進行疑難排解。
author: srinathvasireddy
manager: sivan
keywords: 如何備份;備份系統狀態
ms.service: backup
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: srinathv
ms.openlocfilehash: 8996270acb1525697f29b4251bf4e11d2db62fdf
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465366"
---
# <a name="troubleshoot-system-state-backup"></a>針對系統狀態備份進行疑難排解

本文說明您在使用系統狀態備份時可能會遇到之問題的解決方案。

## <a name="basic-troubleshooting"></a>基本疑難排解
我們建議您在開始針對系統狀態備份進行疑難排解之前, 先執行下列驗證:

- [確定 Microsoft Azure 復原服務 (MARS) 代理程式為最新狀態](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [確保 MARS 代理程式和 Azure 之間具有網路連線能力](https://aka.ms/AB-A4dp50)
- 確保 Microsoft Azure 復原服務正在執行中 (在服務主控台中)。 視需要重新開機並重試作業
- [確保草稿資料夾位置具有 5-10% 的磁碟區空間可供使用](https://aka.ms/AB-AA4dwtt)
- [檢查是否有其他程序或防毒軟體在干擾 Azure 備份](https://aka.ms/AB-AA4dwtk)
- [已排程的備份失敗，但可以手動備份](https://aka.ms/ScheduledBackupFailManualWorks)
- 確保您的作業系統已更新到最新版本
- [請確定不支援的磁片磁碟機和具有不受支援屬性的檔案已從備份中排除](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- 確保受保護系統上的**系統時鐘**已設定為正確的時區 <br>
- [確定伺服器至少具有 .Net Framework 4.5.2 版和更新版本](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- 如果您嘗試在保存庫中**重新登錄伺服器**，則： <br>
  - 請確保已將伺服器上的代理程式解除安裝，並已從入口網站中刪除 <br>
  - 使用與一開始登錄伺服器時所用的相同複雜密碼 <br>
- 在離線備份的情況下, 請確定在開始離線備份作業之前, 已在來源和複製電腦上安裝 Azure PowerShell 版本3.7。0
- [當備份代理程式在 Azure 虛擬機器上執行時的考慮](https://aka.ms/AB-AA4dwtr)

### <a name="limitation"></a>限制
- Microsoft 不建議使用系統狀態復原來復原到不同的硬體
- 系統狀態備份目前支援「內部部署」 Windows 伺服器, 這是 Azure Vm 無法使用的功能。

## <a name="pre-requisite"></a>必要條件

在使用 Azure 備份進行系統狀態備份的疑難排解之前, 請確定您執行下列先決條件檢查。  

### <a name="verify-windows-server-backup-is-installed"></a>確認已安裝 Windows Server Backup

確定伺服器中已安裝並啟用 Windows Server Backup。 若要檢查安裝狀態, 請執行下列 PowerShell 命令:

 ```
 PS C:\> Get-WindowsFeature Windows-Server-Backup
 ```
如果輸出顯示 [**安裝狀態**] 為 [**可用**], 則表示 Windows server backup 功能可供安裝, 但不會安裝在伺服器上。 不過, 如果未安裝 Windows Server Backup, 請使用下列其中一種方法來進行安裝。

**方法 1：使用 PowerShell 安裝 Windows Server Backup**

若要使用 PowerShell 安裝 Windows Server Backup, 請執行下列命令:

  ```
  PS C:\> Install-WindowsFeature -Name Windows-Server-Backup
  ```

**方法 2：使用伺服器管理員安裝 Windows Server Backup**

若要使用伺服器管理員安裝 Windows Server Backup, 請執行下列步驟:

1. 在 [**伺服器**管理員] 中, 按一下 [**新增角色及功能**]。 [**新增角色及功能] wizard**隨即出現。

    ![儀表板](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. 選取 [**安裝類型**], 然後按 **[下一步]** 。

    ![安裝類型](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. 從伺服器集區中選取伺服器, 然後按 **[下一步]** 。 在 [伺服器角色] 中, 保留預設選項, 然後按 **[下一步]** 。
4. 選取 [**功能**] 索引標籤**Windows Server Backup** , 然後按 **[下一步]** 。

    ![特性](./media/backup-azure-system-state-troubleshoot/features.png)

5. 在 [**確認**] 索引標籤中, 按一下 [**安裝**] 以開始安裝程式。
6. 在 [**結果**] 索引標籤中, 它會顯示 Windows Server Backup 功能已成功安裝在您的 Windows 伺服器上。

    ![結果](./media/backup-azure-system-state-troubleshoot/results.jpg)


### <a name="system-volume-information-permission"></a>系統磁碟區資訊許可權

請確定本機系統具有 windows 安裝所在磁片區中 [**系統磁碟區資訊**] 資料夾的 [完全控制]。 這通常是**C:\System 的磁片區資訊**。 如果未正確設定上述許可權, Windows Server backup 可能會失敗

### <a name="dependent-services"></a>相依服務

請確定下列服務處於執行中狀態:

**服務名稱** | **啟動類型**
--- | ---
遠端程序呼叫 (RPC) | 自動
COM + 事件系統 (EventSystem) | 自動
系統事件通知服務 (SENS) | 自動
磁片區陰影複製 (VSS) | 手動
Microsoft 軟體陰影複製提供者 (SWPRV) | 手動

### <a name="validate-windows-server-backup-status"></a>驗證 Windows Server Backup 狀態

若要驗證 Windows Server Backup 狀態, 請執行下列動作:

  * 確定 WSB PowerShell 正在執行

    -   從`Get-WBJob`提高許可權的 PowerShell 執行, 並確定它不會傳回下列錯誤:

    > [!WARNING]
    > Get-wbjob:' Get-wbjob ' 一詞無法辨識為 Cmdlet、函式、指令檔或可運作程式的名稱。 請檢查名稱拼字，如果名稱含有路徑，請確認路徑正確，然後再試一次。

    -   如果因此錯誤而失敗, 則請在伺服器電腦上重新安裝 Windows Server Backup 功能, 如步驟1必要條件中所述。

  * 從提高許可權的命令提示字元執行下列命令, 以確定 WSB 備份正常運作:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >以您要儲存系統狀態備份映射的磁片區磁碟機號取代 X。

    - 從提高許可權的 PowerShell 執行`Get-WBJob`命令, 以定期檢查作業的狀態        
    - 備份作業完成後, 請執行`Get-WBJob -Previous 1`命令來檢查作業的最終狀態

如果作業失敗, 它會指出 WSB 問題, 這會導致 MARS 代理程式系統狀態備份失敗。

## <a name="common-errors"></a>常見錯誤

### <a name="vss-writer-timeout-error"></a>VSS 寫入器逾時錯誤

| 徵兆 | 原因 | 解決方法
| -- | -- | --
| -MARS 代理程式失敗, 並出現錯誤訊息:「WSB 作業失敗, 發生 VSS 錯誤。 檢查 VSS 事件記錄檔以解決失敗」<br/><br/> -VSS 應用程式事件記錄檔中出現下列錯誤記錄檔:「VSS 寫入器已拒絕事件, 錯誤為 0x800423f2, 寫入器的超時時間已在凍結和解除凍結事件之間過期」。| 因為電腦上缺少 CPU 和記憶體資源, 所以 VSS 寫入器無法及時完成 <br/><br/> 另一個備份軟體已在使用 VSS 寫入器, 因此無法完成此備份的快照集操作 | 等候 CPU/記憶體在系統或中止處理常式佔用太多記憶體/CPU 時釋放, 然後再次嘗試操作 <br/><br/>  等待進行中的備份完成, 然後在電腦上沒有執行中的備份時, 稍後再嘗試操作。


### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>磁碟空間不足, 無法成長陰影複製

| 徵兆 | 解決方法
| -- | --
| -MARS 代理程式失敗, 並出現錯誤訊息:因為磁片區中包含系統檔案的磁碟空間不足, 所以備份失敗。 <br/><br/> -Volsnap 系統事件記錄檔中出現下列錯誤/警告記錄檔:「磁片區 C 上的磁碟空間不足: 若要為 C: 增加陰影複製的陰影複製儲存區」, 這是因為此失敗, 所有 volume C 的陰影複本都有刪除的風險。」 | -釋放事件記錄檔中反白顯示磁片區的空間, 讓陰影複製有足夠的空間可在備份進行時成長 <br/><br/> -設定陰影複製空間時, 我們可以限制用於陰影複製的空間量。如需詳細資訊, 請參閱這[篇文章](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax)


### <a name="efi-partition-locked"></a>EFI 磁碟分割已鎖定

| 徵兆 | 解決方法
| -- | --
| MARS 代理程式失敗, 並出現錯誤訊息:「系統狀態備份失敗, 因為 EFI 系統磁碟分割已鎖定。 這可能是因為協力廠商安全性或備份軟體的系統磁碟分割存取所致」 | -如果問題是因為協力廠商安全性軟體所造成, 則您必須聯絡防病毒廠商, 讓他們可以允許 MARS 代理程式 <br/><br/> -如果協力廠商備份軟體正在執行, 請等候它完成, 然後再試一次


## <a name="next-steps"></a>後續步驟

- 如需 Resource Manager 部署中 Windows 系統狀態的詳細資訊, 請參閱[備份 Windows Server 系統狀態](backup-azure-system-state.md)
