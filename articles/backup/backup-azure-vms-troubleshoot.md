---
title: 針對 Azure 虛擬機器的備份錯誤進行疑難排解
description: Azure 虛擬機器備份與還原的疑難排解
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 8/7/2018
ms.author: trinadhk
ms.openlocfilehash: 9bbaf23999c04eba5157ebe7dff73ed47418c99a
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2018
ms.locfileid: "53634179"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Azure 虛擬機器備份的疑難排解
您可以利用下表列出的資訊，針對使用 Azure 備份時所發生的錯誤進行疑難排解：

| 錯誤詳細資料 | 因應措施 |
| ------ | --- |
| 備份無法執行作業，因為虛擬機器 (VM) 已不存在： <br>請在不刪除備份資料的情況下停止保護虛擬機器。 如需詳細資訊，請參閱[停止保護虛擬機器](http://go.microsoft.com/fwlink/?LinkId=808124)。 |此錯誤會在已刪除主要 VM，但備份原則仍然在尋找 VM 來備份時發生。 若要修正此錯誤，請遵循下列步驟： <ol><li> 重新建立具有相同名稱和相同資源群組名稱 (**雲端服務名稱**) 的虛擬機器，<br>**or**</li><li> 停止保護虛擬機器 (不論是否刪除備份資料)。 如需詳細資訊，請參閱[停止保護虛擬機器](https://go.microsoft.com/fwlink/?LinkId=808124)。</li></ol> |
| 因為虛擬機器沒有網路連線，所以快照集作業失敗： <br>請確定 VM 可以存取網路。 若要讓快照集成功，請將 Azure 資料中心 IP 範圍設為允許清單或設定 Proxy 伺服器以取得網路存取。 如需詳細資訊，請參閱[針對 Azure 備份失敗進行疑難排解：與代理程式或延伸模組相關的問題](http://go.microsoft.com/fwlink/?LinkId=800034)。 <br><br>如果您已經使用 Proxy 伺服器，請確定已正確設定 Proxy 伺服器設定。 | 此錯誤會在您拒絕虛擬機器上的輸出網際網路連線時發生。 VM 快照集延伸模組需要網際網路連線，才能建立基礎磁碟的快照集。 請參閱 [ExtensionSnapshotFailedNoNetwork](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine)。 |
| Azure 虛擬機器代理程式 (VM 代理程式) 無法與 Azure 備份服務通訊： <br>請確認 VM 具有網路連線，且 VM 代理程式是最新版且正在執行。 如需詳細資訊，請參閱[針對 Azure 備份失敗進行疑難排解：與代理程式或延伸模組相關的問題](http://go.microsoft.com/fwlink/?LinkId=800034)。 |此錯誤會在 VM 代理程式發生問題，或對 Azure 基礎結構的網路存取被某種方式封鎖時發生。 深入了解[對 VM 快照集問題進行偵錯](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup)。 <br><br>如果 VM 代理程式未造成問題，請重新啟動 VM。 不正確的 VM 狀態可能會造成問題，重新啟動 VM 可重設此狀態。 |
| VM 處於失敗的佈建狀態： <br>請將 VM 重新啟動，並確定 VM 正在執行或已關機。 | 此錯誤會在因其中一個延伸模組發生失敗，而使 VM 處於失敗的佈建狀態時發生。 請移至延伸模組清單，查看是否有失敗的延伸模組並將它移除，然後嘗試重新啟動虛擬機器。 如果所有延伸模組都是處於執行中的狀態，請檢查 VM 代理程式服務是否正在執行。 若否，請重新啟動 VM 代理程式服務。 |
| 受控磁碟的 **VMSnapshot** 延伸模組作業失敗： <br>請重試備份作業。 如果問題持續發生，請遵循[針對 Azure 備份失敗進行疑難排解](http://go.microsoft.com/fwlink/?LinkId=800034)中的指示。 如果問題持續發生，請連絡 Microsoft 支援服務。 | Azure 備份服務無法觸發快照集時，就會發生此錯誤。 深入了解[對 VM 快照集問題進行偵錯](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#ExtentionOperationFailed-vmsnapshot-extension-operation-failed)。 |
| 備份無法複製虛擬機器的快照集，因為儲存體帳戶中的可用空間不足： <br>請確定儲存體帳戶具有與附加至虛擬機器之進階儲存體磁碟上的資料相等的可用空間。 | 針對 VM 備份堆疊 V1 上的進階 VM，我們會將快照集複製到儲存體帳戶。 此步驟是為了確定快照集上運作的備份管理流量不會限制可供使用進階磁碟的應用程式使用的 IOPS 數目。 <br><br>我們建議您僅配置 50% (17.5 TB) 的總儲存體帳戶空間。 然後 Azure 備份服務便可以將快照集複製到儲存體帳戶，並從儲存體帳戶中的這個複製位置將資料傳送到到保存庫。 |
| 備份無法執行操作，因為 VM 代理程式沒有回應。 |此錯誤會在 VM 代理程式發生問題，或對 Azure 基礎結構的網路存取被某種方式封鎖時發生。 針對 Windows VM，請檢查服務中的 VM 代理程式服務狀態，以及代理程式是否出現在 [控制台] 的 [程式集] 中。 <br><br>請嘗試從 [控制台] 中刪除該程式，然後以 [VM 代理程式](#vm-agent)中所述的方式重新安裝代理程式。 重新安裝代理程式之後，請觸發臨機操作備份以確認它。 |
| 復原服務延伸模組作業失敗： <br>請確定虛擬機器上有最新的 VM 代理程式，且 VM 代理程式服務正在執行中。 請重試備份作業。 如果備份作業失敗，請連絡 Microsoft 支援服務。 |此錯誤會在 VM 代理程式過期時發生。 請參閱[對 Azure 虛擬機器備份進行疑難排解](#updating-the-VM-agent)以更新 VM 代理程式。 |
| 虛擬機器不存在： <br>請確定該虛擬機器存在，或選取不同的虛擬機器。 |此錯誤會在已刪除主要 VM，但備份原則仍然在尋找 VM 來備份時發生。 若要修正此錯誤，請遵循下列步驟： <ol><li> 重新建立具有相同名稱和相同資源群組名稱 (**雲端服務名稱**) 的虛擬機器，<br>**or**<br></li><li>停止保護虛擬機器，但不刪除備份資料。 如需詳細資訊，請參閱[停止保護虛擬機器](https://go.microsoft.com/fwlink/?LinkId=808124)。</li></ol> |
| 命令執行已失敗： <br>另一個作業目前正在此項目上進行。 請等候前一個作業完成。 然後再重試作業。 |現有的備份作業正在執行，必須等到目前的作業完成，才會啟動新作業。 |
| 從復原服務保存庫複製 VHD 已逾時： <br>請稍候幾分鐘，然後重試一次此作業。 如果問題持續發生，請連絡 Microsoft 支援服務。 | 此錯誤會在儲存體端有暫時性錯誤，或備份服務未在逾時期限內收到足夠的儲存體帳戶 IOPS 以便將資料傳輸到保存庫時發生。 請務必遵循[設定 VM 時的最佳做法](backup-azure-vms-introduction.md#best-practices)。 請嘗試將 VM 移至其他尚未載入的儲存體帳戶，然後重試備份作業。|
| 備份失敗，發生內部錯誤： <br>請稍候幾分鐘，然後重試一次此作業。 如果問題持續發生，請連絡 Microsoft 支援服務。 |有兩個原因會使您收到此錯誤︰ <ul><li> 存取 VM 儲存體時發生暫時性問題。 請檢查 [Azure 狀態網站](https://azure.microsoft.com/status/)，以查看該區域中是否有計算、儲存體或網路問題。 問題解決後，請重試備份作業。 <li> 原始 VM 已被刪除，無法取得復原點。 若要保留已刪除 VM 的備份資料，但移除備份錯誤，請取消保護 VM 並選擇保留資料的選項。 這個動作會停止排定的備份作業和週期性錯誤訊息。 |
| 備份無法在選取項目上安裝 Azure 復原服務延伸模組： <br>VM 代理程式是 Azure 復原服務延伸模組的必要條件。 請安裝 Azure 虛擬機器代理程式並重新啟動註冊作業。 |<ol> <li>檢查是否已正確安裝 VM 代理程式。 <li>確定已正確設定 VM 設定上的旗標。</ol> 深入了解[安裝 VM 代理程式](#validating-vm-agent-installation)，以及如何驗證 VM 代理程式安裝。 |
| 延伸模組安裝失敗，發生錯誤「COM+ 無法與 Microsoft Distributed Transaction Coordinator 通話」。 |此錯誤通常表示 COM+ 服務未執行。 請連絡 Microsoft 支援服務來協助解決此問題。 |
| 快照集作業失敗且發生磁碟區陰影複製服務 (VSS) 作業錯誤：**這個磁碟機已由 BitLocker 磁碟機加密鎖定。您必須至 [控制台] 解除鎖定這個磁碟機。** |對 VM 上的所有磁碟機關閉 BitLocker，並檢查是否已解決 VSS 問題。 |
| VM 目前的狀態不允許備份。 |<ul><li>如果 VM 處於 [正在執行] 和 [關機] 之間的暫時性狀態，請等候狀態變更。 然後再觸發備份作業。 <li> 如果 VM 是 Linux VM 並使用安全性強化的 Linux 核心模組，請從安全性原則中排除 Azure Linux 代理程式路徑 **/var/lib/waagent**，並確定已安裝備份延伸模組。  |
| 找不到 Azure 虛擬機器。 |此錯誤會在已刪除主要 VM，但備份原則仍然在尋找已刪除的 VM 時發生。 請以下列方式修正此錯誤： <ol><li>重新建立具有相同名稱和相同資源群組名稱 (**雲端服務名稱**) 的虛擬機器， <br>**or** <li> 停用此 VM 的保護，使系統不會建立備份作業。 </ol> |
| 虛擬機器上沒有 VM 代理程式： <br>請安裝所有必要條件和 VM 代理程式。 接著請重新啟動作業。 |深入了解 [VM 代理程式安裝，以及如何驗證 VM 代理程式安裝](#vm-agent)。 |
| 快照集作業失敗，因為 VSS 寫入器處於不良狀態。 |請重新啟動處於不良狀態的 VSS 寫入器。 從提升權限的命令提示字元，執行 ```vssadmin list writers```。 輸出會包含所有 VSS 寫入器和其狀態。 針對每個狀態不是「[1] 穩定」的 VSS 寫入器，請從提升權限的命令提示字元執行下列命令，以重新啟動那些 VSS 寫入器： <ol><li>```net stop serviceName``` <li> ```net start serviceName```</ol>|
| 快照集作業失敗，因為設定的剖析失敗。 |此錯誤是因為 **MachineKeys** 目錄上的權限已變更︰**%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**。 <br> 執行下列命令，並確認 **MachineKeys** 目錄上的權限是預設的︰<br>**icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**。 <br><br>預設的權限如下︰ <ul><li>所有人：(R,W) <li>BUILTIN\Administrators：(F)</ul> 如果您發現 **MachineKeys** 目錄中的權限不同於預設權限，請依照下列步驟來修正權限，刪除憑證，並觸發備份： <ol><li>修正 **MachineKeys** 目錄上的權限。 在目錄中使用 Explorer 安全性屬性和進階安全性設定，來將權限重設為預設值。 從目錄中移除所有使用者物件 (預設值除外)，然後確定 [所有人] 權限有下列特殊存取權︰ <ul><li>列出資料夾/讀取資料 <li>讀取屬性 <li>讀取擴充屬性 <li>建立檔案/寫入資料 <li>建立資料夾/附加資料<li>寫入屬性<li>寫入擴充屬性<li>讀取權限 </ul><li>刪除 [發給] 是傳統部署模型或 **Windows Azure CRP 憑證產生器**的所有憑證：<ol><li>[在本機電腦主控台上開啟憑證](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx) \(機器翻譯\)。<li>在 [個人] > [憑證] 底下，刪除 [核發對象] 是傳統部署模型或 **Windows Azure CRP 憑證產生器**的所有憑證。</ol> <li>觸發 VM 備份作業。 </ol>|
| Azure 備份服務沒有足夠的 Azure Key Vault 權限以備份加密的虛擬機器。 |請使用[從還原的磁碟建立 VM](backup-azure-vms-automation.md) 中的步驟，在 PowerShell 中為備份服務提供這些權限。 |
|快照集延伸模組安裝失敗，發生錯誤「COM+ 無法與 Microsoft Distributed Transaction Coordinator 通話」。 | 從提升權限的命令提示字元中，啟動 Windows 服務 **COM+ System Application**。 例如 **net start COMSysApp**。 如果服務無法啟動，請採取下列步驟：<ol><li> 確定**分散式交易協調器**服務的登入帳戶是 [Network Service]。 如果不是，請將登入帳戶變更為 **Network Service**，然後重新啟動服務。 然後嘗試啟動 **COM+ System Application**。<li>如果 **COM+ System Application** 無法啟動，請使用下列步驟解除安裝並安裝**分散式交易協調器**服務： <ol><li>停止 MSDTC 服務。 <li>開啟命令提示字元，**cmd**。 <li>執行命令 ```msdtc -uninstall```。 <li>執行命令 ```msdtc -install```。 <li>啟動 MSDTC 服務。 </ol> <li>啟動 Windows 服務 **COM+ System Application**。 **COM+ System Application** 啟動後，請從 Azure 入口網站觸發備份作業。</ol> |
|  快照集作業失敗，因為發生 COM+ 錯誤。 | 建議從提升權限的命令提示字元重新啟動 Windows 服務 **COM+ System Application**，其命令為 **net start COMSysApp**。 如果問題持續發生，請重新啟動 VM。 如果重新啟動 VM 沒有幫助，請嘗試[移除 VMSnapshot 延伸模組](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#cause-3-the-backup-extension-fails-to-update-or-load)，並手動觸發備份。 |
| 備份無法凍結 VM 的一或多個掛接點，以取得檔案系統一致快照集。 | 請執行下列步驟： <ul><li>使用 **'tune2fs'** 命令檢查所有已裝載裝置的檔案系統狀態。 例如 **tune2fs -l /dev/sdb1 \**。| 對 [檔案系統狀態] 進行 grep。 <li>使用 **'umount'** 命令來卸載所有檔案系統狀態不清潔的裝置。 <li> 使用 **'fsck'** 命令對這些裝置執行檔案系統一致性檢查。 <li> 重新裝載裝置並嘗試備份。</ol> |
| 快照集作業失敗，因為無法建立安全網路通訊通道。 | <ol><li> 在提高權限的模式中執行 **regedit.exe**，來開啟登錄編輯程式。 <li> 識別系統中存在的所有 .NET Framework 版本。 它們位於登錄機碼 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft** 的階層下。 <li> 針對登錄機碼中的每個 .NET Framework，新增下列機碼︰ <br> **SchUseStrongCrypto"=dword:00000001**。 </ol>|
| 快照集作業失敗，因為無法安裝適用於 Visual Studio 2012 的 Visual C++ 可轉散發套件。 | 瀏覽至 C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion，並安裝 vcredist2012_x64。 確定允許此服務安裝的登錄機碼值已設定為正確的值。 也就是說，登錄機碼 **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** 的值已設定為 **3**，而非 **4**。 <br><br>如果您仍遇到安裝問題，請從提高權限的命令提示字元執行 **MSIEXEC /UNREGISTER**，再執行 **MSIEXEC /REGISTER**，以重新啟動安裝服務。  |


## <a name="jobs"></a>工作
| 錯誤詳細資料 | 因應措施 |
| --- | --- |
| 此作業類型不支援取消： <br>請等候作業完成。 |None |
| 此作業未處於可取消的狀態： <br>請等候作業完成。 <br>**or**<br> 選取的作業未處於可取消的狀態： <br>請等候作業完成。 |作業很可能已經快要完成。 請等候作業完成。|
| 備份無法取消作業，因為它並未正在進行： <br>僅支援針對進行中的作業進行取消。 請嘗試取消正在進行的作業。 |此錯誤發生的原因是因為暫時性的狀態。 請稍候再重試取消作業。 |
| 備份無法取消作業： <br>請等候作業完成。 |None |

## <a name="restore"></a>Restore
| 錯誤詳細資料 | 因應措施 |
| --- | --- |
| 還原失敗，發生雲端內部錯誤。 |<ol><li>您嘗試還原的雲端服務是使用 DNS 設定所設定。 您可以檢查： <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings**。<br>如果已設定 [位址]，則 DNS 設定便已設定。<br> <li>您嘗試還原到其中的雲端服務是使用 **ReservedIP** 所設定，而雲端服務中的現有 VM 目前處於停止狀態。 您可以使用下列 PowerShell Cmdlet 來檢查雲端服務是否已保留 IP：**$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName**。 <br><li>您嘗試將具有下列特殊網路組態的虛擬機器還原至相同的雲端服務： <ul><li>負載平衡器設定下的虛擬機器，內部與外部。<li>具有多個保留 IP 的虛擬機器。 <li>具有多個 NIC 的虛擬機器。 </ul><li>在 UI 中選取新的雲端服務，或參閱適用於具有特殊網路組態之 VM 的[還原考量](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)。</ol> |
| 選取的 DNS 名稱已有人使用： <br>請指定不同的 DNS 名稱並再試一次。 |此 DNS 名稱是指雲端服務名稱，其結尾通常是 **.cloudapp.net**。 此名稱必須是唯一的。 如果您遇到這個錯誤，您需要在還原期間選擇不同的 VM 名稱。 <br><br>  只有 Azure 入口網站的使用者才會看到這個錯誤。 透過 PowerShell 執行還原作業將會成功，因為它只會還原磁碟，並不會建立 VM。 當您在磁碟還原作業之後明確建立 VM 時，將會遇到此錯誤。 |
| 指定的虛擬網路設定不正確： <br>請指定不同的虛擬網路設定並再試一次。 |None |
| 指定的雲端服務所使用的保留 IP 不符合要還原之虛擬機器的設定： <br>請指定未使用保留 IP 的其他雲端服務。 或選擇另一個復原點來進行還原。 |None |
| 雲端服務已達到其輸入端點的數目限制： <br>請指定不同的雲端服務或使用現有的端點來重試作業。 |None |
| 復原服務保存庫和目標儲存體帳戶處於兩個不同的區域： <br>請確定還原作業中所指定的儲存體帳戶和您的復原服務保存庫皆位於相同的 Azure 區域中。 |None |
| 不支援針對還原作業所指定的儲存體帳戶： <br>僅支援具有本地備援或異地備援複寫設定的「基本」或「標準」儲存體帳戶。 請選取支援的儲存體帳戶。 |None |
| 針對還原作業所指定的儲存體帳戶類型未上線： <br>請確定針對還原作業所指定的儲存體帳戶類型已上線。 |此錯誤發生的原因可能是因為 Azure 儲存體中發生暫時性錯誤，或是因為運作中斷。 選擇另一個儲存體帳戶。 |
| 已達到資源群組配額： <br>請從 Azure 入口網站刪除一些資源群組，或連絡 Azure 支援以提高限制。 |None |
| 選取的子網路不存在： <br>請選取存在的子網路。 |None |
| 備份服務無權存取您訂用帳戶中的資源。 |若要解決此錯誤，請先使用[還原備份的磁碟](backup-azure-arm-restore-vms.md#create-new-restore-disks)中的步驟來還原磁碟。 然後使用[從還原的磁碟建立 VM](backup-azure-vms-automation.md#restore-an-azure-vm) 中的 PowerShell 步驟。 |

## <a name="backup-or-restore-takes-time"></a>備份或還原需花費很長的時間
如果您的備份花費超過 12 小時，或是還原花費超過 6 小時：
* 了解[增加備份時間的因素](backup-azure-vms-introduction.md#time-considerations)和[增加還原時間的因素](backup-azure-vms-introduction.md#restore-considerations)。
* 請務必遵循[備份最佳做法](backup-azure-vms-introduction.md#best-practices)。

## <a name="vm-agent"></a>VM 代理程式
### <a name="set-up-the-vm-agent"></a>設定 VM 代理程式
一般而言，VM 代理程式已存在於從 Azure 資源庫建立的 VM 中。 不過，從內部部署資料中心移轉的虛擬機器並不會安裝 VM 代理程式。 針對那些 VM，必須明確安裝 VM 代理程式。

#### <a name="windows-vms"></a>Windows VM

* 下載並安裝 [代理程式 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 您需要有系統管理員權限，才能完成安裝。
* 針對使用傳統部署模型建立的虛擬機器，請[更新 VM 屬性](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) \(英文\) 以指出代理程式已安裝。 Azure Resource Manager 虛擬機器不需要此步驟。

#### <a name="linux-vms"></a>Linux VM

* 從散發套件存放庫安裝最新版的代理程式。 如需套件名稱的詳細資料，請參閱 [Linux 代理程式存放庫](https://github.com/Azure/WALinuxAgent) \(英文\)。
* 針對使用傳統部署模型建立的 VM，請[使用此部落格](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) \(英文\) 來更新 VM 屬性並確認代理程式已安裝。 資源管理員虛擬機器不需要此步驟。

### <a name="update-the-vm-agent"></a>更新 VM 代理程式
#### <a name="windows-vms"></a>Windows VM

* 若要更新 VM 代理程式，請重新安裝 [VM 代理程式二進位檔](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 更新代理程式之前，請先確定 VM 代理程式更新期間不會進行任何備份作業。

#### <a name="linux-vms"></a>Linux VM

* 若要更新 Linux VM 代理程式，請遵循[更新 Linux VM 代理程式](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)一文中的指示。

    > [!NOTE]
    > 請一律使用散發套件存放庫來更新代理程式。 

    請勿從 GitHub 下載代理程式程式碼。 如果最新的代理程式不適用於您的散發套件，請連絡散發套件支援以了解如何取得最新的代理程式。 您也可以在 GitHub 存放庫中查看最新的 [Windows Azure Linux 代理程式](https://github.com/Azure/WALinuxAgent/releases)資訊。

### <a name="validate-vm-agent-installation"></a>驗證 VM 代理程式安裝

確認 Windows VM 上的 VM 代理程式版本：

1. 登入 Azure 虛擬機器，然後瀏覽至 C:\WindowsAzure\Packages 資料夾。 您應該會找到 **WaAppAgent.exe** 檔案。
2. 請以滑鼠右鍵按一下該檔案，然後移至 [內容]。 然後選取 [詳細資料] 索引標籤。[產品版本] 欄位應為 2.6.1198.718 或更高版本。

## <a name="troubleshoot-vm-snapshot-issues"></a>對 VM 快照集問題進行疑難排解
VM 備份仰賴發給底層儲存體的快照命令。 無法存取儲存體或快照集工作執行上的延遲，可能會造成備份作業失敗。 下列狀況可能導致快照集工作失敗：

- **因使用 NSG 而導致封鎖儲存體的網路存取**。 深入了解如何使用 IP 允許清單或透過 Proxy 伺服器來對儲存體[建立網路存取](backup-azure-arm-vms-prepare.md#establish-network-connectivity)。
- **已設定 SQL Server 備份的 VM 可能會造成快照集工作延遲**。 根據預設，VM 備份會在 Windows VM 上建立 VSS 完整備份。 執行 SQL Server 並已設定 SQL Server 備份的 VM，可能會遇到快照集延遲。 如果快照集延遲會導致備份失敗，請設定下列登錄機碼：

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

- **所報告的 VM 狀態不正確，因為 VM 已在 RDP 中關機**。 如果您使用遠端桌面來將虛擬機器關機，請確認該 VM 在入口網站中的狀態是否正確。 如果狀態不正確，請使用入口網站 VM 儀表板中的 [關機] 選項來關閉 VM。
- **如果有超過四個 VM 共用相同的雲端服務，請將 VM 分散到多個備份原則上**。 錯開備份時間，來讓同一時間開始的 VM 備份不超過四個。 請嘗試讓原則中的開始時間至少錯開一小時。
- **VM 執行會使用大量 CPU 或記憶體資源**。 若虛擬機器執行時的記憶體或 CPU 使用量非常高 (>90%)，快照集工作會被排入佇列並延遲。 最後它會逾時。如果發生此問題，請嘗試進行隨選備份。

## <a name="networking"></a>網路功能
如同所有的延伸模組，備份延伸模組需要存取公用網際網路才能運作。 沒有公用網際網路的存取權，可能會以各種不同方式的資訊清單形式顯現：

* 延伸模組安裝可能會失敗。
* 備份作業 (如磁碟快照集) 可能會失敗。
* 顯示備份作業狀態可能會失敗。

解決公用網際網路位址的需求，已在[這篇 Azure 支援部落格](https://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx) \(英文\) 中討論。 請檢查 VNET 的 DNS 設定，並確定 Azure URI 可以被解析。

正確完成名稱解析後，也必須提供 Azure IP 的存取權。 若要解除封鎖對 Azure 基礎結構的存取，請遵循下列步驟進行：

- 將 Azure 資料中心 IP 範圍列入允許清單：
   1. 取得要列入允許清單的 [Azure 資料中心 IP](https://www.microsoft.com/download/details.aspx?id=41653) 。
   1. 使用 [New-NetRoute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute) \(英文\) Cmdlet 來解除封鎖 IP。 在 Azure VM 內提升權限的 PowerShell 視窗中執行這個 Cmdlet。 請以系統管理員身分執行。
   1. 將規則加入 NSG (若已有 NSG)，以允許存取該 IP。
- 建立路徑來使 HTTP 流量得以流動：
   1. 如果您有一些網路限制，請部署 HTTP Proxy 伺服器來路由傳送流量。 其中一個網路限制的範例為網路安全性群組。 請在[建立網路連線](backup-azure-arm-vms-prepare.md#establish-network-connectivity)中查看部署 HTTP Proxy 伺服器的步驟。
   1. 將規則加入 NSG (若已有 NSG)，以允許從 HTTP Proxy 存取網際網路。

> [!NOTE]
> 必須在來賓內啟用 DHCP，IaaS VM 備份才能運作。 如果您需要靜態私人 IP，請透過 Azure 入口網站或 PowerShell 加以設定。 確定 VM 內的 DHCP 選項已啟用。
> 取得如何透過 PowerShell 設定靜態 IP 的詳細資訊：
> - [如何將靜態內部 IP 位址新增至現有的 VM](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
> - [針對指派至網路介面的私人 IP 位址變更配置方法](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
>
>
