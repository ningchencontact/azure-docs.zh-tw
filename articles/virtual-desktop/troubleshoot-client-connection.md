---
title: Windows 虛擬桌面中的遠端桌面用戶端連接-Azure
description: 如何解決在 Windows 虛擬桌面租使用者環境中設定用戶端連線時的問題。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: c6c7a57a2093445d3922f9349242c9a902df7370
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300711"
---
# <a name="remote-desktop-client-connections"></a>遠端桌面用戶端連線

使用本文來解決 Windows 虛擬桌面用戶端連線的問題。

## <a name="provide-feedback"></a>提供意見反應

我們目前不採用 Windows 虛擬桌面處於預覽狀態時的支援案例。 請瀏覽 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，與產品小組和活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="you-cant-open-a-web-client"></a>您無法開啟 web 用戶端

開啟另一個網站，確認有網際網路連線能力;例如， [www.Bing.com](https://www.bing.com)。

使用**nslookup**確認 DNS 可以解析 FQDN：

```cmd
nslookup rdweb.wvd.microsoft.com
```

請嘗試與其他用戶端連線，例如適用于 Windows 7 或 Windows 10 的遠端桌面用戶端，並檢查您是否可以開啟網頁用戶端。

### <a name="error-opening-another-site-fails"></a>Error:開啟另一個網站失敗

**原因：** 網路問題和/或中斷。

**補丁**請聯絡網路支援。

### <a name="error-nslookup-cannot-resolve-the-name"></a>Error:Nslookup 無法解析名稱

**原因：** 網路問題和/或中斷。

**補丁**聯絡網路支援

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Error:您無法連線，但其他用戶端可以連接

**原因：** 瀏覽器未如預期般運作，且已停止運作。

**補丁**請遵循這些指示來疑難排解瀏覽器。

1. 重新開機瀏覽器。
2. 清除瀏覽器 cookie。 請參閱[如何刪除 Internet Explorer 中的 cookie](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)檔案。
3. 清除瀏覽器快取。 請參閱[清除瀏覽器的瀏覽器](https://binged.it/2RKyfdU)快取。
4. 以私用模式開啟瀏覽器。

## <a name="web-client-stops-responding-or-disconnects"></a>Web 用戶端停止回應或中斷連線

嘗試使用另一個瀏覽器或用戶端進行連接。

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Error:其他瀏覽器和用戶端也會故障或無法開啟

**原因：** 網路和/或作業系統問題或中斷

**補丁**聯絡支援小組。

## <a name="web-client-keeps-prompting-for-credentials"></a>Web 用戶端會持續提示輸入認證

如果 Web 用戶端持續提示輸入認證，請遵循這些指示。

1. 確認 web 用戶端 URL 是否正確。
2. 確認認證適用于與 URL 系結的 Windows 虛擬桌面環境。
3. 清除瀏覽器 cookie。 請參閱[如何刪除 Internet Explorer 中的 cookie](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)檔案。
4. 清除瀏覽器快取。 請參閱[清除瀏覽器的瀏覽器](https://binged.it/2RKyfdU)快取。
5. 以私用模式開啟瀏覽器。

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>適用于 Windows 7 或 Windows 10 的遠端桌面用戶端停止回應或無法開啟

使用下列 PowerShell Cmdlet 來清除頻外（OOB）用戶端登錄。

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

流覽至 **%AppData%\RdClientRadc**並刪除所有內容。

卸載並重新安裝適用于 Windows 7 和 Windows 10 的遠端桌面用戶端。

## <a name="troubleshooting-end-user-connectivity"></a>針對使用者連線能力進行疑難排解

有時候使用者可以存取其摘要和本機資源，但仍有設定、可用性或效能問題，因而無法存取遠端資源。 在這些情況下，使用者會收到類似下列的訊息：

![遠端桌面連線錯誤訊息。](media/eb76b666808bddb611448dfb621152ce.png)

![無法連接到閘道錯誤訊息。](media/a8fbb9910d4672147335550affe58481.png)

請遵循這些一般的疑難排解指示，以取得用戶端連接錯誤碼。

1. 確認使用者名稱和問題發生的時間。
2. 開啟**PowerShell** ，並與報告問題的 Windows 虛擬桌面租使用者建立連接。
3. 使用 RdsTenant 確認與正確租使用者的連線 **。**
4. 使用**RdsHostPool**和**RdsSessionHost** Cmdlet，確認已在正確的主機集區上進行疑難排解。
5. 執行下列命令，以取得在指定的時間範圍內，連線類型的所有失敗活動清單：

    ```PowerShell
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. 使用前述 Cmdlet 輸出中的**ActivityId** ，執行下列命令：

    ```PowerShell
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

7. 命令會產生類似如下所示輸出的輸出。 請使用**ErrorCodeSymbolic**和**ErrorMessage**來疑難排解根本原因。

    ```PowerShell
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-o_add_user_to_group_failed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32error_no_such_member"></a>Error:O_ADD_USER_TO_GROUP_FAILED/無法將 USER = ≤ username ≥新增至群組 = 遠端桌面使用者。 原因：Win32.ERROR_NO_SUCH_MEMBER

**原因：** VM 尚未聯結至使用者物件所在的網域。

**補丁**將 VM 新增至正確的網域。 請參閱將[Windows Server 虛擬機器加入受控網域](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal)。

### <a name="error-nslookup-cannot-resolve-the-name"></a>Error:Nslookup 無法解析名稱

**原因：** 網路問題或中斷。

**補丁**聯絡網路支援

### <a name="error-connectionfailedclientprotocolerror"></a>Error:ConnectionFailedClientProtocolError

**原因：** 使用者嘗試連接的 Vm 未加入網域。

**補丁**將屬於主機集區一部分的所有 Vm 加入網域控制站。

### <a name="error-connectionfailedusersidinformationmismatch"></a>Error:ConnectionFailedUserSIDInformationMismatch
**原因：** 嘗試啟用遠端登入的使用者時，使用者的 Azure Active Directory （AD）權杖中的 SID 不符合網域控制站所傳回的 SID。 當嘗試以原本源自 Windows Server AD 的使用者登入 Azure Active Directory Domain Services （Azure AD DS）環境時，通常會發生此錯誤。

**補丁**目前不支援此案例。 只有源自 Azure Active Directory 的使用者可以登入連線到 Azure AD DS Windows 虛擬桌面 Vm。

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>使用者連接，但不顯示任何內容（沒有摘要）

使用者可以啟動遠端桌面用戶端，而且能夠進行驗證，不過使用者在 web 探索摘要中看不到任何圖示。

使用下列命令列，確認已將報告問題的使用者指派給應用程式群組：

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

確認使用者以正確的認證登入。

如果正在使用 web 用戶端，請確認沒有任何快取的認證問題。

## <a name="next-steps"></a>後續步驟

- 如需疑難排解 Windows 虛擬桌面和擴大追蹤的總覽, 請參閱[疑難排解總覽、意見反應和支援](troubleshoot-set-up-overview.md)。
- 若要針對在 Windows 虛擬桌面環境中建立租使用者和主機集區的問題進行疑難排解, 請參閱[建立租使用者和主機集](troubleshoot-set-up-issues.md)區。
- 若要在 Windows 虛擬桌面中設定虛擬機器 (VM) 時針對問題進行疑難排解, 請參閱[工作階段主機虛擬機器](troubleshoot-vm-configuration.md)設定。
- 若要針對搭配 Windows 虛擬桌面使用 PowerShell 時的問題進行疑難排解, 請參閱[Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)。
- 若要深入瞭解預覽服務，請參閱[Windows 虛擬桌面預覽環境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)進行疑難排解。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
- 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)。
