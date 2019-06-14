---
title: Windows 虛擬桌面-Azure 中的遠端桌面用戶端連線
description: 如何解決問題，當您設定 Windows 虛擬桌面的租用戶環境中的用戶端連線。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: f88dee579e44a01dc1a7404ef6a670de34063552
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65833562"
---
# <a name="remote-desktop-client-connections"></a>遠端桌面用戶端連線

您可以使用本文來解決 Windows 的虛擬桌面用戶端連線的問題。

## <a name="provide-feedback"></a>提供意見反應

我們目前不採用 Windows 虛擬桌面處於預覽狀態時的支援案例。 請瀏覽 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，與產品小組和活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="you-cant-open-a-web-client"></a>您無法開啟 web 用戶端

確認開啟另一個網站; 沒有網際網路連線能力例如， [www.Bing.com](https://www.bing.com)。

使用**nslookup**確認 DNS 可將 FQDN 解析：

```cmd
nslookup rdweb.wvd.microsoft.com
```

請嘗試使用另一個用戶端，例如適用於 Windows 7 或 Windows 10 中，並檢查看看是否您可以開啟 web 用戶端的遠端桌面用戶端連線。

### <a name="error-opening-another-site-fails"></a>Error:開啟另一個站台就會失敗

**原因：** 網路問題及/或中斷。

**修正：** 請連絡網路支援。

### <a name="error-nslookup-cannot-resolve-the-name"></a>Error:Nslookup 無法解析名稱

**原因：** 網路問題及/或中斷。

**修正：** 請連絡網路支援

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Error:您無法連線，但其他用戶端可以連線

**原因：** 瀏覽器不其運作方式，可預期的和已停止運作。

**修正：** 請遵循下列指示來進行疑難排解的瀏覽器。

1. 重新啟動瀏覽器。
2. 清除瀏覽器 cookie。 請參閱[如何刪除 Internet Explorer 中的 cookie 檔案](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)。
3. 清除瀏覽器快取。 請參閱[清除您的瀏覽器的瀏覽器快取](https://binged.it/2RKyfdU)。
4. 在私用模式中開啟的瀏覽器。

## <a name="web-client-stops-responding-or-disconnects"></a>Web 用戶端會停止回應或中斷連線

請嘗試使用其他瀏覽器或用戶端連線。

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Error:其他瀏覽器及用戶端也故障或無法開啟

**原因：** 網路和/或作業的系統問題或中斷情形

**修正：** 請連絡客戶支援小組。

## <a name="web-client-keeps-prompting-for-credentials"></a>Web 用戶端會提示輸入認證

如果 Web 用戶端會提示輸入認證時，請遵循這些指示。

1. 確認 web 用戶端 URL 正確。
2. 確認認證適用於 Windows 的虛擬桌面環境繫結至的 URL。
3. 清除瀏覽器 cookie。 請參閱[如何刪除 Internet Explorer 中的 cookie 檔案](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)。
4. 清除瀏覽器快取。 請參閱[清除您的瀏覽器的瀏覽器快取](https://binged.it/2RKyfdU)。
5. 在私用模式中開啟的瀏覽器。

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Windows 7 或 Windows 10 的遠端桌面用戶端會停止回應，或是無法開啟

您可以使用下列 PowerShell cmdlet 來清除 頻外 (OOB) 用戶端登錄。

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

瀏覽至 **%AppData%\RdClientRadc**並刪除所有的內容。

解除安裝然後重新安裝適用於 Windows 7 與 Windows 10 的遠端桌面用戶端。

## <a name="troubleshooting-end-user-connectivity"></a>使用者連接性疑難排解

有時候使用者可以存取他們的摘要和本機資源，但仍有組態、 可用性或效能問題，使其無法存取遠端資源。 在這些情況下，使用者會收到訊息類似如下：

![遠端桌面連線錯誤訊息。](media/eb76b666808bddb611448dfb621152ce.png)

![無法連線到閘道錯誤訊息。](media/a8fbb9910d4672147335550affe58481.png)

請遵循下列疑難排解的一般指示，用戶端連線錯誤代碼。

1. 確認使用者名稱，而且當已發生問題的時間。
2. 開啟**PowerShell**並連接至 Windows 虛擬桌面租用戶回報問題。
3. 確認連線到正確的租用戶與**Get RdsTenant。**
4. 使用**Get RdsHostPool**並**Get RdsSessionHost** cmdlet，可讓您確認的疑難排解是在正確的主應用程式集區。
5. 執行下列命令來取得所有失敗的活動，針對指定的時間範圍的連接類型的清單：

    ```PowerShell
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. 使用**ActivityId**從先前的 cmdlet 輸出中，執行下列命令：

    ```PowerShell
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

7. 此命令會產生輸出類似如下所示的輸出。 使用**ErrorCodeSymbolic**並**ErrorMessage**進行疑難排解的根本原因。

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

### <a name="error-oaddusertogroupfailed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32errornosuchmember"></a>Error:O_ADD_USER_TO_GROUP_FAILED / 無法將使用者新增 = ≤username≥ 群組 = Remote Desktop Users。 原因：Win32.ERROR_NO_SUCH_MEMBER

**原因：** VM 尚未加入網域的使用者物件的位置中。

**修正：** 將 VM 新增到正確的網域。 請參閱[Windows Server 虛擬機器加入受控網域](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal)。

### <a name="error-nslookup-cannot-resolve-the-name"></a>Error:Nslookup 無法解析名稱

**原因：** 網路問題或中斷。

**修正：** 請連絡網路支援

### <a name="error-connectionfailedclientprotocolerror"></a>Error:ConnectionFailedClientProtocolError

**原因：** Vm 使用者嘗試連線到未加入網域。

**修正：** 加入屬於網域控制站的主應用程式集區的所有 Vm。

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>使用者連線，但不是顯示任何內容 （任何摘要）

使用者可以啟動遠端桌面用戶端，並能夠進行驗證，但是使用者不會看到 web 探索摘要中的任何圖示。

確認使用者回報的問題已藉由使用這個命令列指派給應用程式群組：

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

確認使用者登入正確的認證。

如果 web 用戶端正在使用中，確認沒有任何快取的認證的問題。

## <a name="next-steps"></a>後續步驟

- 如需疑難排解 Windows 的虛擬桌面和擴大追蹤的概觀，請參閱 <<c0> [ 疑難排解概觀、 意見反應和支援](troubleshoot-set-up-overview.md)。
- 若要針對問題進行疑難排解在 Windows 虛擬桌面環境中建立的租用戶和主應用程式集區時，請參閱[租用戶，然後主應用程式集區建立](troubleshoot-set-up-issues.md)。
- 若要設定虛擬機器 (VM) 中 Windows 虛擬桌面時疑難排解問題，請參閱[工作階段主機的虛擬機器設定](troubleshoot-vm-configuration.md)。
- 若要使用 PowerShell 與 Windows 虛擬桌面時，請疑難排解問題，請參閱[Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)。
- 若要深入了解預覽服務，請參閱[Windows Desktop 預覽環境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署進行疑難排解](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
- 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)。
