---
title: 工作階段主機虛擬機器設定-Azure
description: 如何解決在設定 Windows 虛擬桌面工作階段主機虛擬機器時的問題。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: helohr
ms.openlocfilehash: 4c684a2db02b7587b6d81eaf2f034540250fc001
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841302"
---
# <a name="session-host-virtual-machine-configuration"></a>設定工作階段主機虛擬機器

使用本文來針對設定 Windows 虛擬桌面工作階段主機虛擬機器（Vm）時所發生的問題進行疑難排解。

## <a name="provide-feedback"></a>提供意見反應

請瀏覽 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，與產品小組和活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="vms-are-not-joined-to-the-domain"></a>Vm 未加入網域

如果您在將 Vm 加入網域時遇到問題，請遵循這些指示。

- 使用將[Windows Server 虛擬機器加入受控網域](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal)或使用加入[網域範本](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)中的程式，手動加入 VM。
- 嘗試從 VM 上的命令列 ping 功能變數名稱。
- 請參閱[疑難排解網域加入錯誤訊息](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)中的網域聯結錯誤訊息清單。

### <a name="error-incorrect-credentials"></a>Error:認證不正確

**原因：** 在 Azure Resource Manager 範本介面修正程式中輸入認證時，發生了錯誤的錯誤。

**補丁**請採取下列其中一個動作來解決。

- 手動將 Vm 新增至網域。
- 確認認證之後，重新部署範本。 請參閱[使用 PowerShell 建立主機集](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)區。
- 使用範本將 Vm 加入網域，並將[現有的 WINDOWS VM 加入 AD 網域](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)。

### <a name="error-timeout-waiting-for-user-input"></a>Error:等候使用者輸入時發生超時

**原因：** 用來完成加入網域的帳戶可能會有多重要素驗證（MFA）。

**補丁**請採取下列其中一個動作來解決。

- 暫時移除帳戶的 MFA。
- 使用服務帳戶。

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Error:布建期間使用的帳戶沒有完成作業的許可權

**原因：** 所使用的帳戶沒有將 Vm 加入網域的許可權，因為合規性與法規。

**補丁**請採取下列其中一個動作來解決。

- 使用屬於系統管理員群組成員的帳戶。
- 將所需的許可權授與所使用的帳戶。

### <a name="error-domain-name-doesnt-resolve"></a>Error:功能變數名稱無法解析

**原因 1：** Vm 位於與網域所在的虛擬網路（VNET）沒有關聯的虛擬網路上。

**修正 1:** 在布建 Vm 的 VNET 和執行網域控制站（DC）的 VNET 之間，建立 VNET 對等互連。 請參閱[建立虛擬網路對等互連-Resource Manager、不同的](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions)訂用帳戶。

**原因 2：** 使用 Azure Active Directory Domain Services （Azure AD DS）時，虛擬網路不會更新其 DNS 伺服器設定，以指向受管理的網域控制站。

**修正 2:** 若要更新包含 Azure AD DS 之虛擬網路的 DNS 設定，請參閱[更新 Azure 虛擬網路的 dns 設定](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance#update-dns-settings-for-the-azure-virtual-network)。

**原因3：** 網路介面的 DNS 伺服器設定不會指向虛擬網路上的適當 DNS 伺服器。

**修正3：** 請遵循 [變更 DNS 伺服器] 中的步驟，採取下列其中一個動作來解決。
- 使用[變更 dns 伺服器](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#change-dns-servers)中的步驟，將網路介面的 DNS 伺服器設定變更為**自訂**，並指定虛擬網路上 DNS 伺服器的私人 IP 位址。
- 將網路介面的 DNS 伺服器設定從 [[變更 dns 伺服器](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#change-dns-servers)] 的步驟變更為 [**繼承自虛擬網路**]，然後使用 [[變更 dns 伺服器](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network#change-dns-servers)] 中的步驟變更虛擬網路的 dns 伺服器設定。

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>未安裝 Windows 虛擬桌面代理程式和 Windows 虛擬桌面開機載入器

布建 Vm 的建議方式是使用 Azure Resource Manager**建立和布建 Windows 虛擬桌面主機集**區範本。 此範本會自動安裝 Windows 虛擬桌面代理程式和 Windows 虛擬桌面 Agent 開機載入器。

請遵循這些指示來確認已安裝元件，並檢查是否有錯誤訊息。

1. 勾選 [**控制台** > ] [**程式** > ] [程式**和功能**]，確認已安裝這兩個元件。 如果**Windows 虛擬桌面 agent**和**Windows 虛擬桌面 agent 開機載入**器不可見，則不會安裝在 VM 上。
2. 開啟 [檔案**瀏覽器**] 並流覽至**C:\Windows\Temp\scriptlogs.log**。 如果檔案遺失，則表示安裝這兩個元件的 PowerShell DSC 無法在提供的安全性內容中執行。
3. 如果檔案**C:\Windows\Temp\scriptlogs.log**存在，請將它開啟，並檢查是否有錯誤訊息。

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>Error:缺少 Windows 虛擬桌面代理程式和 Windows 虛擬桌面代理程式啟動載入器。 C:\Windows\Temp\scriptlogs.log 也遺失

**原因 1：** 在 Azure Resource Manager 範本的輸入期間提供的認證不正確或許可權不足。

**修正 1:** 使用 [使用[PowerShell 建立主機集](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)區]，手動將遺失的元件新增至 vm。

**原因 2：** PowerShell DSC 能夠啟動並執行，但無法完成，因為它無法登入 Windows 虛擬桌面並取得所需的資訊。

**修正 2:** 確認下列清單中的專案。

- 請確定帳戶沒有 MFA。
- 請確認租使用者名稱正確，且租使用者存在於 Windows 虛擬桌面中。
- 確認帳戶至少具有 RDS 參與者許可權。

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>Error:驗證失敗，C:\Windows\Temp\scriptlogs.log 發生錯誤

**原因：** PowerShell DSC 能夠執行，但無法連接到 Windows 虛擬桌面。

**補丁**確認下列清單中的專案。

- 手動向 Windows 虛擬桌面服務註冊 Vm。
- 確認用來連線到 Windows 虛擬桌面的帳戶在租使用者上具有建立主機集區的許可權。
- 確認帳戶沒有 MFA。

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows 虛擬桌面代理程式未向 Windows 虛擬桌面服務註冊

第一次在工作階段主機 Vm 上安裝 Windows 虛擬桌面代理程式（手動或透過 Azure Resource Manager 範本和 PowerShell DSC）時，它會提供註冊權杖。 下一節涵蓋適用于 Windows 虛擬桌面代理程式和權杖的疑難排解問題。

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Error:RdsSessionHost Cmdlet 中的狀態會顯示為 [無法使用] 狀態

![RdsSessionHost Cmdlet 會將狀態顯示為 [無法使用]。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**原因：** 代理程式無法將自己更新為新的版本。

**補丁**請遵循這些指示以手動更新代理程式。

1. 在工作階段主機 VM 上下載新版本的代理程式。
2. 啟動 [工作管理員]，然後在 [服務] 索引標籤中，停止 RDAgentBootLoader 服務。
3. 針對新版本的 Windows 虛擬桌面代理程式執行安裝程式。
4. 當系統提示您輸入註冊權杖時，請移除專案 INVALID_TOKEN，然後按 [下一步] （不需要新的權杖）。
5. 完成安裝精靈。
6. 開啟 [工作管理員]，然後啟動 RDAgentBootLoader 服務。

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Error:Windows 虛擬桌面 Agent 登錄專案 IsRegistered 顯示0的值

**原因：** 註冊權杖已過期，或已產生，其到期值為999999。

**補丁**請遵循這些指示來修正代理程式登錄錯誤。

1. 如果已經有註冊權杖，請移除 RDSRegistrationInfo。
2. 使用 Rds-NewRegistrationInfo 產生新的 token。
3. 確認-ExpriationHours 參數設定為72（最大值為99999）。

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Error:Windows 虛擬桌面代理程式在執行 RdsSessionHost 時未回報心跳

**原因 1：** RDAgentBootLoader 服務已停止。

**修正 1:** 啟動 [工作管理員]，如果 [服務] 索引標籤報告 RDAgentBootLoader 服務的 [已停止] 狀態，請啟動服務。

**原因 2：** 埠443可能已關閉。

**修正 2:** 請遵循這些指示來開啟埠443。

1. 從[Sysinternal 工具](https://docs.microsoft.com/sysinternals/downloads/psping)下載 PSPing 工具，確認埠443已開啟。
2. 在執行代理程式的工作階段主機 VM 上安裝 PSPing。
3. 以系統管理員身分開啟命令提示字元，併發出下列命令：

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. 確認 PSPing 已從 RDBroker 傳回信息：

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

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>針對 Windows 虛擬桌面並存堆疊的問題進行疑難排解

Windows 虛擬桌面並存堆疊會隨著 Windows Server 2019 自動安裝。 使用 Microsoft Installer （MSI）在 Microsoft Windows Server 2016 或 Windows Server 2012 R2 上安裝並存堆疊。 針對 Microsoft Windows 10，會使用**enablesxstackrs**來啟用 Windows 虛擬桌面並存堆疊。

在工作階段主機集區 Vm 上安裝或啟用並存堆疊的主要方式有三種：

- 使用 Azure Resource Manager**建立和布建新 Windows 虛擬桌面主機集**區範本
- 藉由在主要映射上包含及啟用
- 在每部 VM 上手動安裝或啟用（或使用延伸模組/PowerShell）

如果您遇到 Windows 虛擬桌面並存堆疊的問題，請從命令提示字元輸入**qwinsta**命令，確認已安裝或啟用並存堆疊。

如果已安裝並啟用並存堆疊， **qwinsta**的輸出會列出輸出中的**rdp-sxs** 。

![安裝或啟用並存堆疊的 qwinsta 會在輸出中列為 rdp-sxs。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

檢查下列登錄專案，並確認它們的值相符。 如果登錄機碼遺失或值不相符，請遵循[使用 PowerShell 建立主機集](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)區中的指示，以瞭解如何重新安裝並存堆疊。

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Error:O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE 錯誤碼。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**原因：** 並存堆疊不會安裝在工作階段主機 VM 上。

**補丁**請遵循這些指示，在工作階段主機 VM 上安裝並存堆疊。

1. 使用遠端桌面通訊協定（RDP）以本機系統管理員身分直接進入工作階段主機 VM。
2. 下載並匯入[Windows 虛擬桌面 powershell 模組](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)以用於您的 powershell 會話（如果您尚未這麼做），然後執行此 Cmdlet 來登入您的帳戶：
    
    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```
    
3. 透過使用[PowerShell 建立主機集](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)區，安裝並存堆疊。

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>如何修正 Windows 虛擬桌面並存堆疊而無法故障

在已知情況下，可能會導致並存堆疊故障：

- 未遵循步驟的正確順序來啟用並存堆疊
- 自動更新至 Windows 10 增強型光碟（EVD）
- 缺少遠端桌面工作階段主機（RDSH）角色
- 執行 enablesxsstackrc 多次
- 在不具備本機系統管理員許可權的帳戶中執行 enablesxsstackrc

本節中的指示可協助您卸載 Windows 虛擬桌面並存堆疊。 卸載並存堆疊之後，請移至[使用 PowerShell 建立主機集](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)區中的「向 Windows 虛擬桌面主機集區註冊 VM」，以重新安裝並存堆疊。

用來執行補救的 VM 必須位於與 VM 相同的子網和網域中，但並存堆疊無法運作。

請依照下列指示，從相同的子網和網域執行補救：

1. 使用標準遠端桌面通訊協定（RDP）連接到將套用修正的 VM。
2. 從 https://docs.microsoft.com/sysinternals/downloads/psexec 下載 PsExec。
3. 將下載的檔案解壓縮。
4. 以本機系統管理員身分啟動命令提示字元。
5. 流覽至已解壓縮 PsExec 的資料夾。
6. 從命令提示字元中，使用下列命令：

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname 是 VM 的電腦名稱稱，具有故障的並存堆疊。

7. 按一下 [同意] 以接受 PsExec 授權合約。

    ![軟體授權合約螢幕擷取畫面。](media/SoftwareLicenseTerms.png)

    >[!Note]
    >只有第一次執行 PsExec 時，才會顯示此對話方塊。

8. 在具有故障並存堆疊的 VM 上開啟命令提示字元會話之後，請執行 qwinsta，並確認名為 rdp-sxs 的專案可供使用。 如果不是，VM 上就不會有並存堆疊，因此問題不會系結至並存堆疊。

    ![系統管理員命令提示字元](media/AdministratorCommandPrompt.png)

9. 執行下列命令，這會列出在 VM 上安裝的 Microsoft 元件，且其並存堆疊無法正常運作。

    ```cmd
        wmic product get name
    ```

10. 使用上述步驟中的產品名稱執行下列命令。

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. 卸載所有開頭為「遠端桌面」的產品。

12. 卸載所有 Windows 虛擬桌面元件之後，請遵循作業系統的指示：

13. 如果您的作業系統是 Windows Server，請重新開機具有故障並存堆疊的 VM （不論是使用 Azure 入口網站或從 PsExec 工具）。

如果您的作業系統是 Microsoft Windows 10，請繼續執行下列指示：

14. 從執行 PsExec 的 VM 中，開啟 [檔案瀏覽器]，並將 disablesxsstackrc 複製到 VM 的系統磁片磁碟機，其中的並存堆疊無法運作。

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname 是 VM 的電腦名稱稱，具有故障的並存堆疊。

15. 建議的程式：從 PsExec 工具啟動 PowerShell，然後流覽至上一個步驟中的資料夾，並執行 disablesxsstackrc。 或者，您可以執行下列 Cmdlet：

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. 當 Cmdlet 完成執行時，請使用故障的並存堆疊重新開機 VM。

## <a name="remote-licensing-model-isnt-configured"></a>未設定遠端授權模型

如果您使用系統管理帳戶登入 Windows 10 企業版的多會話，您可能會收到一則通知，指出「遠端桌面授權模式未設定，遠端桌面服務會在 X 天內停止運作。 在連接代理人伺服器上，使用伺服器管理員來指定遠端桌面授權模式。」

如果時間限制過期，就會出現一則錯誤訊息，指出「遠端會話已中斷連線，因為這部電腦沒有可用的遠端桌面用戶端存取使用權」。

如果您看到其中一個訊息，這表示您需要開啟 [群組原則編輯器]，並手動將授權模式設定為 [**每位使用者**]。 手動設定程式會根據您所使用的 Windows 10 企業版多個會話版本而有所不同。 下列各節將說明如何檢查您的版本號碼，以及要對每個版本進行的動作。

>[!NOTE]
>Windows 虛擬桌面只有在您的主機集區包含 Windows Server 工作階段主機時，才需要 RDS 用戶端存取許可證（CAL）。 若要瞭解如何設定 RDS CAL，請參閱[使用用戶端存取許可證來授權您的 RDS 部署](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-client-access-license)。

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>找出您所使用的 Windows 10 企業版多會話版本

若要檢查哪個版本的 Windows 10 企業版多會話：

1. 使用您的系統管理員帳戶登入。
2. 在 [開始] 功能表旁的搜尋列中輸入 "About"。
3. 選取 [**關於您的電腦**]。
4. 檢查「版本」旁邊的數位。 數位應該是 "1809" 或 "1903"，如下圖所示。
   
    ![Windows 規格視窗的螢幕擷取畫面。 版本號碼會以藍色反白顯示。](media/windows-specifications.png)

既然您已經知道您的版本號碼，請直接跳到相關的章節。

### <a name="version-1809"></a>版本1809

如果您的版本號碼顯示為 "1809"，您可以升級至 Windows 10 企業版的多會話版本1903，或使用最新的映射重新部署主機集區。

若要升級至 Windows 10，版本1903：

1. 如果您還沒有這麼做，請下載並安裝[Windows 10 5 月2019更新](https://support.microsoft.com/help/4028685/windows-10-get-the-update)。
2. 使用您的系統管理員帳戶登入您的電腦。
3. 執行**gpedit.msc**開啟 [群組原則編輯器]。
4. 在 [電腦設定] 底下，移至**系統管理範本** >  **Windows 元件** > **遠端桌面服務** > **遠端桌面工作階段主機** > **授權**.
5. 選取 **[設定遠端桌面授權模式]** 。
6. 在開啟的視窗中，先選取 [**已啟用**]，然後在 [選項] 底下，依據**使用者**指定 RD 工作階段主機伺服器的授權模式，如下圖所示。
    
    ![[設定遠端桌面授權模式] 視窗的螢幕擷取畫面，其依據步驟6中的指示所設定。](media/group-policy-editor-per-user.png)

7. 選取 [套用]。
8. 選取 [確定]。
9.  重新啟動您的電腦。

若要使用最新的映射重新部署主機集區：

1. 請遵循[使用 Azure Marketplace 建立主機集](create-host-pools-azure-marketplace.md)區中的指示，直到系統提示您選擇映射作業系統版本。 您可以選擇具有或不含 Office365 ProPlus 的 Windows 10 企業版多重會話。
2. 使用您的系統管理員帳戶登入您的電腦。
3. 執行**gpedit.msc**開啟 [群組原則編輯器]。
4. 在 [電腦設定] 底下，移至**系統管理範本** >  **Windows 元件** > **遠端桌面服務** > **遠端桌面工作階段主機** > **授權**.
5. 選取 **[設定遠端桌面授權模式]** 。
6. 在開啟的視窗中，先選取 [**已啟用**]，然後在 [選項] 底下，依據**使用者**指定 RD 工作階段主機伺服器的授權模式。
7. 選取 [套用]。
8. 選取 [確定]。
9.  重新啟動您的電腦。

### <a name="version-1903"></a>版本1903

如果您的版本號碼顯示為 "1903"，請遵循下列指示：

1. 使用您的系統管理員帳戶登入您的電腦。
2. 執行**gpedit.msc**開啟 [群組原則編輯器]。
3. 在 [電腦設定] 底下，移至**系統管理範本** >  **Windows 元件** > **遠端桌面服務** > **遠端桌面工作階段主機** > **授權**.
4. 選取 **[設定遠端桌面授權模式]** 。
6. 在開啟的視窗中，先選取 [**已啟用**]，然後在 [選項] 底下，依據**使用者**指定 RD 工作階段主機伺服器的授權模式，如下圖所示。
    
    ![[設定遠端桌面授權模式] 視窗的螢幕擷取畫面，其依據步驟6中的指示所設定。](media/group-policy-editor-per-user.png)

7. 選取 [套用]。
8. 選取 [確定]。
9.  重新啟動您的電腦。

## <a name="next-steps"></a>後續步驟

- 如需疑難排解 Windows 虛擬桌面和擴大追蹤的總覽, 請參閱[疑難排解總覽、意見反應和支援](troubleshoot-set-up-overview.md)。
- 若要針對在 Windows 虛擬桌面環境中建立租使用者和主機集區的問題進行疑難排解, 請參閱[建立租使用者和主機集](troubleshoot-set-up-issues.md)區。
- 若要在 Windows 虛擬桌面中設定虛擬機器 (VM) 時針對問題進行疑難排解, 請參閱[工作階段主機虛擬機器](troubleshoot-vm-configuration.md)設定。
- 若要疑難排解 Windows 虛擬桌面用戶端連線的問題, 請參閱[遠端桌面用戶端連接](troubleshoot-client-connection.md)。
- 若要針對搭配 Windows 虛擬桌面使用 PowerShell 時的問題進行疑難排解, 請參閱[Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)。
- 若要深入瞭解此服務，請參閱[Windows 虛擬桌面環境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)進行疑難排解。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
- 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)。
