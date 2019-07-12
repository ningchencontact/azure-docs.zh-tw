---
title: Windows 虛擬桌面-Azure 中的租用戶和主應用程式集區建立
description: 如何解決問題，當您在 Windows 虛擬桌面環境中設定租用戶和工作階段主機虛擬機器 (VM)。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: v-chjenk
ms.openlocfilehash: 96a9d8fc7495ea473b0a3250b34251afc5f30c13
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786715"
---
# <a name="tenant-and-host-pool-creation"></a>建立租用戶和主機集區

使用這篇文章對您有設定 Windows 虛擬桌面工作階段主機虛擬機器 (Vm) 時的問題進行疑難排解。

## <a name="provide-feedback"></a>提供意見反應

我們目前不採用 Windows 虛擬桌面處於預覽狀態時的支援案例。 請瀏覽 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，與產品小組和活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="vms-are-not-joined-to-the-domain"></a>Vm 未加入網域

如果您有將 Vm 加入網域的問題，請遵循下列指示。

- 將 VM 使用中的程序，以手動方式加入[Windows Server 虛擬機器加入受控網域](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal)或使用[網域聯結範本](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)。
- 請試著 ping 在 VM 上的命令列中的網域名稱。
- 檢閱網域聯結中的錯誤訊息清單[疑難排解網域聯結的錯誤訊息](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)。

### <a name="error-incorrect-credentials"></a>Error:不正確的認證

**原因：** 發生時所輸入的認證在 Azure Resource Manager 範本介面修正的錯字。

**修正：** 請遵循下列指示來修正認證。

1. 以手動方式將 Vm 加入網域。
2. 重新部署之後已確認認證。 請參閱[使用 PowerShell 建立主應用程式集區](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)。
3. 將 Vm 加入網域，使用的範本[加入 AD 網域中的現有 Windows VM](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)。

### <a name="error-timeout-waiting-for-user-input"></a>Error:逾時等候使用者輸入

**原因：** 用來完成加入網域的帳戶可能有多重要素驗證 (MFA)。

**修正：** 請遵循下列指示來完成加入網域。

1. 暫時移除 MFA 的帳戶。
2. 使用服務帳戶。

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Error:在佈建期間所使用的帳戶沒有完成作業的權限

**原因：** 所使用的帳戶沒有權限，來將 Vm 加入網域，因為相容性和法規。

**修正：** 請遵循下列指示。

1. 使用屬於系統管理員群組成員的帳戶。
2. 授與所使用的帳戶的必要權限。

### <a name="error-domain-name-doesnt-resolve"></a>Error:網域名稱未解析

**原因 1：** Vm 位於與網域所在的虛擬網路 (VNET) 沒有相關聯的資源群組。

**1 的修正：** 建立 VNET 對等互連已在佈建 Vm 在 VNET 和網域控制站 (DC) 執行所在的 VNET 之間。 請參閱[建立虛擬網路對等互連-Resource Manager、 不同的訂用帳戶](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions)。

**原因 2：** 當使用 AadService (AADS) 時，尚未設定 DNS 項目。

**2 的修正：** 若要設定網域服務，請參閱[啟用 Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns)。

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>不會安裝 Windows 虛擬桌面代理程式和 Windows 虛擬桌面開機載入器

佈建 Vm 的建議的方式使用 Azure Resource Manager**建立及佈建 Windows 虛擬桌面裝載集區**範本。 範本會自動安裝的 Windows 虛擬桌面代理程式和 Windows 虛擬桌面代理程式開機載入器。

遵循下列指示來確認已安裝的元件，並檢查錯誤訊息。

1. 確認兩個元件都安裝檢查**控制台中** > **程式** > **程式和功能**。 如果**Windows 虛擬桌面代理程式**並**Windows 虛擬桌面代理程式開機載入器**是不可見的它們不安裝在 VM 上。
2. 開啟**檔案總管**並瀏覽至**C:\Windows\Temp\scriptlogs.log**。 如果遺漏的檔案，則表示已安裝的兩個元件的 PowerShell DSC 無法在所提供的安全性內容中執行。
3. 如果檔案**C:\Windows\Temp\scriptlogs.log**呈現、 開啟它，然後檢查是否有錯誤訊息。

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>Error:Windows 虛擬桌面代理程式和 Windows 虛擬桌面代理程式開機載入器已遺失。 C:\Windows\Temp\scriptlogs.log is also missing

**原因 1：** Azure Resource Manager 範本在輸入期間提供的認證不正確或權限不足。

**1 的修正：** 手動將遺漏的元件新增至使用的 Vm[使用 PowerShell 建立主應用程式集區](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)。

**原因 2：** PowerShell DSC 可以啟動並執行，但無法完成，因為它無法登入 Windows 虛擬桌面，並取得所需的資訊。

**2 的修正：** 請確認下列清單中的項目。

- 請確定該帳戶不需要 MFA。
- 請確認租用戶名稱正確，且租用戶存在於 Windows 的虛擬桌面。
- 確認帳戶至少擁有 RDS 參與者權限。

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>Error:驗證失敗，錯誤 C:\Windows\Temp\scriptlogs.log

**原因：** PowerShell DSC 可以執行，但無法連線到 Windows 虛擬桌面。

**修正：** 請確認下列清單中的項目。

- 手動與 Windows 虛擬桌面服務中註冊 Vm。
- 確認用來連線至 Windows 虛擬桌面的帳戶有權限的租用戶，以建立主應用程式集區。
- 確認帳戶不具 MFA。

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows 虛擬桌面代理程式未向 Windows 虛擬桌面服務

當 「 Windows 虛擬桌面代理程式 」 第一次安裝工作階段上裝載的 Vm （手動或透過 Azure Resource Manager 範本和 PowerShell DSC），它會提供註冊權杖。 下節將討論適用於 Windows 虛擬桌面代理程式 」 和 「 語彙基元的問題進行疑難排解。

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Error:歸類於 Get RdsSessionHost 指令程式的狀態會顯示狀態為無法使用

![取得 RdsSessionHost cmdlet 會顯示狀態為無法使用。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**原因：** 代理程式無法自行更新至新的版本。

**修正：** 請遵循這些指示來手動更新代理程式。

1. 下載新的工作階段主機 VM 上的代理程式版本。
2. 啟動 工作管理員，並在 服務 索引標籤中，停止 RDAgentBootLoader 服務。
3. 執行新版本的 Windows 虛擬桌面代理程式安裝程式。
4. 當系統提示您註冊權杖，移除項目 INVALID_TOKEN 然後按下一步 （新的權杖不必要的）。
5. 完成安裝精靈。
6. 開啟 工作管理員，並啟動 RDAgentBootLoader 服務。

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Error:Windows 虛擬桌面代理程式登錄項目 IsRegistered 顯示值為 0

**原因：** 註冊權杖已過期或已產生的 999999 的到期值。

**修正：** 請遵循這些指示來修正代理程式登錄時發生錯誤。

1. 如果已註冊權杖，則會移除與移除 RDSRegistrationInfo。
2. 產生新的 Rds NewRegistrationInfo 語彙基元。
3. 確認-ExpriationHours 參數設定為 72 （最大值為 99999）。

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Error:Windows 虛擬桌面代理程式未回報活動訊號，執行 Get RdsSessionHost 時

**原因 1：** RDAgentBootLoader 服務已停止。

**1 的修正：** 啟動 [工作管理員] 和 [服務] 索引標籤會回報 RDAgentBootLoader 服務已停止的狀態，如果啟動服務。

**原因 2：** 可能會關閉連接埠 443。

**2 的修正：** 請遵循下列指示開啟連接埠 443。

1. 確認所下載的 PSPing 工具已開啟連接埠 443 [Sysinternal 工具](https://docs.microsoft.com/sysinternals/downloads/psping)。
2. 在工作階段主機代理程式執行所在的 VM 上安裝 PSPing。
3. 開啟命令提示字元，以系統管理員身分，並發出下列命令：

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. 確認從 RDBroker 該 PSPing 收到資訊：

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>使用 Windows 的虛擬桌面並排顯示堆疊進行疑難排解

Windows 虛擬桌面並排顯示堆疊會自動安裝與 Windows Server 2019。 您可以使用 Microsoft Installer (MSI) 來安裝 Microsoft Windows Server 2016 或 Windows Server 2012 R2 上的並排顯示堆疊。 適用於 Microsoft Windows 10 中，使用啟用的 Windows 虛擬桌面的並排顯示堆疊**enablesxstackrs.ps1**。

有三種主要方式，並排顯示堆疊取得安裝或啟用工作階段主應用程式集區 Vm 上：

- 使用 Azure Resource Manager**建立及佈建新的 Windows 虛擬桌面主應用程式集區**範本
- 所包含，並在主要映像上啟用
- 安裝或以手動方式啟用每個 VM 上 （或延伸模組/PowerShell）

如果您遇到問題的 Windows 虛擬桌面的並排顯示堆疊，請輸入**qwinsta**命令從命令提示字元，以確認並排顯示堆疊是安裝或啟用。

輸出**qwinsta**會列出**rdp sxs**時安裝並啟用並排顯示堆疊輸出中。

![安裝或啟用與 qwinsta 列為 rdp-sxs 在輸出中，就會並排顯示堆疊。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

請查看下面所列的登錄項目，並確認其值能符合。 如果登錄機碼已遺失或值不相符，請依照下列中的指示[使用 PowerShell 建立主應用程式集區](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)如何重新安裝並排顯示堆疊上。

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-oreverseconnectstackfailure"></a>Error:O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE 錯誤碼。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**原因：** 並排顯示堆疊未安裝在工作階段主機 VM 上。

**修正：** 請遵循這些指示來安裝並排顯示堆疊在工作階段主機 VM 上。

1. 若要直接進入工作階段主機 VM，以本機系統管理員身分，使用遠端桌面通訊協定 (RDP)。
2. 下載並匯入[的 Windows 虛擬桌面 PowerShell 模組](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)來使用您的 PowerShell 工作階段中，如果您還沒有這麼做。
3. 並排顯示堆疊使用安裝[使用 PowerShell 建立主應用程式集區](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)。

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>如何修正功能有問題的 Windows 虛擬桌面並排顯示堆疊

那里已知的情況下，可能會造成故障的並排顯示堆疊：

- 未遵循正確的順序的步驟來啟用並排顯示堆疊
- 自動更新至 Windows 10 增強多功能光碟 (EVD)
- 遺漏的遠端桌面工作階段主機 (RDSH) 角色
- 多次執行 enablesxsstackrc.ps1
- 在帳戶中執行 enablesxsstackrc.ps1 沒有本機系統管理員權限

在本節中的指示可協助您解除安裝 Windows 的虛擬桌面並排顯示堆疊。 一旦您解除安裝並排顯示堆疊，請移至 「 註冊與 Windows 虛擬桌面的主應用程式集區的 VM 」 中[使用 PowerShell 建立主應用程式集區](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)重新安裝並排顯示堆疊。

用來執行修復 VM 必須位於相同的子網路和網域與 VM 的故障的並排顯示堆疊。

請遵循下列指示執行補救，從相同的子網路和網域：

1. 使用標準遠端桌面通訊協定 (RDP) 從連線到 VM 將會套用修正程式。
2. 下載 PsExec 從 https://docs.microsoft.com/sysinternals/downloads/psexec 。
3. 解壓縮下載的檔案。
4. 以本機系統管理員身分啟動命令提示字元。
5. 瀏覽至其中 PsExec 已解壓縮的資料夾。
6. 從命令提示字元中，使用下列命令：

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname 是故障的並排顯示堆疊的 VM 的電腦名稱。

7. 按一下 同意接受 PsExec 授權合約。

    ![軟體授權合約螢幕擷取畫面。](media/SoftwareLicenseTerms.png)

    >[!Note]
    >只有第一次執行 PsExec 會顯示此對話方塊。

8. 與故障的並排顯示堆疊在 VM 上，開啟命令提示字元工作階段之後，請執行 qwinsta，並確認名為 rdp sxs 的項目可供使用。 如果沒有，並排顯示堆疊不存在的 VM 上，因此問題不會繫結至並排顯示堆疊。

    ![系統管理員命令提示字元](media/AdministratorCommandPrompt.png)

9. 執行下列命令，其中會列出與故障的並排顯示堆疊在 VM 上安裝的 Microsoft 元件。

    ```cmd
        wmic product get name
    ```

10. 從上述步驟，與產品名稱執行下列命令。

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. 解除安裝所有產品開頭為 「 遠端桌面 」。

12. 所有的 Windows 虛擬桌面元件已解除安裝之後，請遵循適用於您作業系統的指示：

13. 如果您的作業系統是 Windows 伺服器，重新啟動 VM 具有運作失常的並排顯示堆疊 （無論是使用 Azure 入口網站，或從 PsExec 工具）。

如果您的作業系統 Microsoft Windows 10，請繼續下面的指示：

14. 從執行 PsExec 的 VM，開啟檔案總管，並將 disablesxsstackrc.ps1 複製到 VM 的功能不正常的並排顯示堆疊的系統磁碟機。

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname 是故障的並排顯示堆疊的 VM 的電腦名稱。

15. 建議的程序： 從 PsExec 工具，啟動 PowerShell 並瀏覽至資料夾，從上一個步驟並執行 disablesxsstackrc.ps1。 或者，您可以執行下列 cmdlet:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. 當完成 cmdlet 執行時，重新啟動 VM 的故障的並排顯示堆疊。

## <a name="remote-licensing-model-is-not-configured"></a>未設定遠端授權模型

如果您登入 Windows 10 企業版使用系統管理帳戶的多重工作階段時，您可能會收到通知，指出 「 未設定遠端桌面的授權模式，遠端桌面服務將會停止運作於 X 天。 在連線代理人伺服器上，使用伺服器管理員指定的遠端桌面授權模式。 」 如果您看到此訊息，這表示您需要手動設定的授權模式**每個使用者**。

若要手動設定的授權模式：  

1. 移至您 **[開始] 功能表**搜尋方塊中，然後尋找並開啟**gpedit.msc**存取本機群組原則編輯器。 
2. 移至 **電腦組態** > **系統管理範本** > **Windows 元件** >  **遠端桌面服務** > **遠端桌面工作階段主機** > **授權**。 
3. 選取 **設定的遠端桌面授權模式**並將它變更為**每位使用者**。

我們目前正在研究通知和寬限期的逾時問題，並計劃在未來的更新中解決它們。 

## <a name="next-steps"></a>後續步驟

- 如需疑難排解 Windows 的虛擬桌面和擴大追蹤的概觀，請參閱 <<c0> [ 疑難排解概觀、 意見反應和支援](troubleshoot-set-up-overview.md)。
- 若要針對問題進行疑難排解在 Windows 虛擬桌面環境中建立的租用戶和主應用程式集區時，請參閱[租用戶，然後主應用程式集區建立](troubleshoot-set-up-issues.md)。
- 若要設定虛擬機器 (VM) 中 Windows 虛擬桌面時疑難排解問題，請參閱[工作階段主機的虛擬機器設定](troubleshoot-vm-configuration.md)。
- 若要疑難排解使用 Windows 的虛擬桌面用戶端連線的問題，請參閱[遠端桌面用戶端連線](troubleshoot-client-connection.md)。
- 若要使用 PowerShell 與 Windows 虛擬桌面時，請疑難排解問題，請參閱[Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)。
- 若要深入了解預覽服務，請參閱[Windows Desktop 預覽環境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署進行疑難排解](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
- 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)。