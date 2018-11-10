---
title: 遠端桌面服務未在 Azure VM 上啟動 | Microsoft Docs
description: 了解如何針對在連線到虛擬機器時的遠端桌面服務問題進行疑難排解 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: a9967aec61aaab5bc6b4517407f36e2a6c7342c8
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238857"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>遠端桌面服務未在 Azure VM 上啟動

本文將說明如何針對在遠端桌面服務 (TermService) 未啟動或無法啟動時連線到 Azure 虛擬機器 (VM) 的問題進行疑難排解。

>[!NOTE]
>Azure 建立和處理資源的部署模型有二種： [資源管理員和傳統](../../azure-resource-manager/resource-manager-deployment-model.md)。 本文將說明如何使用 Resource Manager 部署模型。 建議您針對新的部署使用此模型，而不要使用傳統部署模型。

## <a name="symptoms"></a>徵兆

當您嘗試連線到 VM 時，會遇到下列案例：

- VM 螢幕擷取畫面顯示作業系統已完全載入且正在等待認證。

    ![VM 狀態的螢幕擷取畫面](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- 您使用事件檢視器從遠端檢視 VM 中的事件記錄時，看到遠端桌面服務 (TermServ) 未啟動或無法啟動。 以下是記錄範例︰

    **記錄名稱**：     系統 </br>
    **來源**：        服務控制管理員 </br>
    **日期**：          12/16/2017 11:19:36 AM</br>
    **事件識別碼**：      7022</br>
    **工作分類**：無</br>
    **層級**：         錯誤</br>
    **關鍵字**：      傳統</br>
    **使用者**：          N/A</br>
    **電腦**：vm.contoso.com</br>
    **描述**：遠端桌面服務啟動時無反應。 

    您也可以使用序列存取主控台功能來尋找這些錯誤，請使用下列查詢： 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>原因
 
此問題的發生原因是遠端桌面服務未在 VM 上執行。 原因可能取決於下列案例： 

- 已將 TermService 服務設定為**停用**。 
- TermService 服務當機或停止回應。 

## <a name="solution"></a>解決方法

若要針對此問題進行疑難排解，請使用「序列主控台」，或藉由將 VM 的 OS 磁碟連結至復原 VM 來[修復離線的 VM](#repair-the-vm-offline)。

### <a name="use-serial-console"></a>使用序列主控台

1. 選取 [支援與疑難排解] > [序列主控台] 來存取[序列主控台](serial-console-windows.md)。 如果已在 VM 上啟用此功能，您就能成功連線該 VM。

2. 針對 CMD 執行個體建立新通道。 輸入 **CMD** 以啟動通道來取得通道名稱。

3. 切換至執行 CMD 執行個體的通道。 在此案例中，應該是通道 1。

   ```
   ch -si 1
   ```

4. 再按一次 **Enter** 鍵，然後輸入 VM 的有效使用者名稱和密碼 (本機或網域識別碼)。

5. 查詢 TermService 服務的狀態。

   ```
   sc query TermService
   ```

6. 如果服務狀態顯示**已停止**，請嘗試啟動服務。

    ```
    sc start TermService
     ``` 

7. 再次查詢服務，以確定服務已成功啟動。

   ```
   sc query TermService
   ```
    如果服務無法啟動，請根據您所收到的錯誤訊息來遵循解決方案：

    |  Error |  建議 |
    |---|---|
    |5- 拒絕存取 |請參閱 [TermService 服務因為拒絕存取錯誤而停止](#termService-service-is-stopped-because-of-access-denied-error) |
    |1058 - ERROR_SERVICE_DISABLED  |請參閱 [TermService 服務已停用。](#termService-service-is-disabled)  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題|
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題    |
    |1070 - ERROR_SERVICE_START_HANG   | 請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題  |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | 請參閱 [TermService 服務已停用](#termService-service-is-disabled)  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題 |
    |1753   |請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題   |

#### <a name="termservice-service-is-stopped-because-of-access-denied-error"></a>TermService 服務因為拒絕存取錯誤而停止

1. 連線至[序列主控台](serial-console-windows.md#)並開啟 PowerShell 執行個體。
2. 執行下列指令碼來下載處理程序監視工具：

        remove-module psreadline  
        $source = "https://download.sysinternals.com/files/ProcessMonitor.zip" 
        $destination = "c:\temp\ProcessMonitor.zip" 
        $wc = New-Object System.Net.WebClient 
        $wc.DownloadFile($source,$destination) 
3. 現在啟動 procmon 追蹤：

        procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML 
4. 啟動出現拒絕存取錯誤的服務，以重現問題： 

        sc start TermService 
        
    當服務失敗時，請繼續並終止處理程序監視工具的追蹤：

        procmon /Terminate 
5. 收集  **c:\temp\ProcMonTrace.PML** 檔案，並使用 procmon 開啟檔案，然後以 [結果是拒絕存取] **** 來進行篩選，如下列螢幕擷取畫面所示：

    ![依處理程序監視工具的結果篩選](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. 修正輸出上的登錄機碼、資料夾或檔案。 之所以會有此問題，通常是因為服務上使用的登入帳戶沒有存取這些物件的 ACL 權限。 若要知道登入帳戶的正確 ACL 權限，您可以在狀況良好的 VM 上查看。 

#### <a name="termservice-service-is-disabled"></a>TermService 服務已停用

1.  將服務還原為其預設的啟動值：

        sc config TermService start= demand 
        
2.  啟動服務：

        sc start TermService 
3.  再次查詢其狀態，以確保服務正在執行：     sc 查詢 TermService 
4.  嘗試使用遠端桌面來連線至 VM。


### <a name="repair-the-vm-offline"></a>修復離線的 VM

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>將 OS 磁碟連結至復原 VM

1. [將 OS 磁碟連結至復原 VM](../windows/troubleshoot-recovery-disks-portal.md)。
2. 啟動復原 VM 的遠端桌面連線。 確定連結的磁碟在磁碟管理主控台中標示為 [線上]。 記下指派給已連結 OS 磁碟的磁碟機代號。
3.  開啟提高權限的命令提示字元執行階段 (**以系統管理員身分執行**)，然後執行下列指令碼。 我們假設指派給已連結 OS 磁碟的磁碟機代號是 F。請將它取代為您 VM 中的適當值。 

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
        REM Set default values back on the broken service 
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
4. [卸離 OS 磁碟並重新建立 VM](../windows/troubleshoot-recovery-disks-portal.md)，然後檢查問題是否已解決。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
