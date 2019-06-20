---
title: 準備要上傳至 Azure 的 Windows VHD | Microsoft Docs
description: 了解如何準備 Windows VHD 或 VHDX，將它上傳至 Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: cc942aeb34d17e8dff064c6a21a3c7b2099c742a
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151019"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>準備 Windows VHD 或 VHDX 以上傳至 Azure

您將上傳 Windows 虛擬機器 (VM) 從內部部署至 Azure 之前，您必須準備虛擬硬碟 （VHD 或 VHDX）。 Azure 支援第 1 代和第 2 代 Vm 採用 VHD 檔案格式且具有固定大小磁碟。 允許的 VHD 大小上限為 1023 GB。 

在第 1 代 VM，您可以將轉換的 VHDX 檔案系統的 VHD。 您也可以轉換成固定大小磁碟的動態擴充磁碟。 但您無法變更 VM 的世代。 如需詳細資訊，請參閱 <<c0> [ 應該建立層代 1 或 2 在 HYPER-V 中的 VM？](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)並[層代 2 部 Vm （預覽） Azure 支援](generation-2.md)。

適用於 Azure Vm 支援原則的相關資訊，請參閱[適用於 Azure Vm 的 Microsoft 伺服器軟體支援](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)。

> [!NOTE]
> 這篇文章中的指示適用於 Windows Server 2008 R2 和更新版本的 Windows Server 作業系統的 64 位元版本。 如需在 Azure 中執行 32 位元作業系統的資訊，請參閱[32 位元作業系統，在 Azure Vm 中支援](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)。

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>將虛擬磁碟，固定的大小與 vhd 
如果您需要將虛擬磁碟轉換為 Azure 所需的格式，請使用本節中的其中一種方法。 轉換虛擬磁碟之前，請備份 VM。 請確定 Windows VHD 在本機伺服器上正常運作。 然後再嘗試進行轉換，或將它上傳至 Azure 時，才能解決 vm 本身的任何錯誤。

在轉換磁碟之後，建立使用磁碟的 VM。 啟動並登入 VM 以完成準備上傳。

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>使用 HYPER-V 管理員，將磁碟轉換 
1. 開啟 Hyper-V 管理員，然後在左側選取您的本機電腦。 在電腦清單上方功能表中，選取**動作** > **編輯磁碟**。
2. 在 **找出 Virtual Hard Disk**頁面上，選取您的虛擬磁碟。
3. 在 [**選擇動作**頁面上，選取**轉換** > **下一步]** 。
4. 如果您需要從 VHDX 進行轉換，選取**VHD** > **下一步**。
5. 如果您需要從動態擴充磁碟進行轉換，選取**固定大小** > **下一步**。
6. 尋找並選取用以儲存新 VHD 檔案的路徑。
7. 選取 [完成]  。

> [!NOTE]
> 使用提升權限的 PowerShell 工作階段來執行本文中的命令。

### <a name="use-powershell-to-convert-the-disk"></a>使用 PowerShell 轉換磁碟 
您可以在 Windows PowerShell 中使用 [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) 命令來轉換虛擬磁碟。 當您啟動 PowerShell 時，選取 [以系統管理員身分執行]  。 

下列範例命令會將磁碟從 VHDX 轉換為 VHD。 命令也從動態擴充磁碟，磁碟轉換為固定大小磁碟。

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

此命令中的值取代`-Path`您想要轉換的虛擬硬碟的路徑。 值取代`-DestinationPath`以新的路徑和轉換磁碟的名稱。

### <a name="convert-from-vmware-vmdk-disk-format"></a>從 VMware VMDK 磁碟格式進行轉換
如果您在 Windows VM 映像[VMDK 檔案格式](https://en.wikipedia.org/wiki/VMDK)，使用[Microsoft 虛擬機器轉換器](https://www.microsoft.com/download/details.aspx?id=42497)將它轉換成 VHD 格式。 如需詳細資訊，請參閱 <<c0> [ 如何將 VMware VMDK 轉換為 HYPER-V VHD](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)。

## <a name="set-windows-configurations-for-azure"></a>設定適用於 Azure 的 Windows 設定

在想要上傳至 Azure VM 上執行下列命令，從[提升權限的命令提示字元視窗](https://technet.microsoft.com/library/cc947813.aspx):

1. 在路由表上移除任何靜態持續路由：
   
   * 若要檢視路由表，在命令提示字元視窗上執行 `route print`。
   * 檢查`Persistence Routes`區段。 如果持續的路由，請使用`route delete`命令來移除它。
2. 移除 WinHTTP Proxy：
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    如果 VM 需要搭配特定的 proxy，則將 proxy 例外狀況加入 Azure IP 位址 ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)) 讓 VM 可以連線到 Azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. 將磁碟 SAN 原則設為[ `Onlineall` ](https://technet.microsoft.com/library/gg252636.aspx):
   
    ```PowerShell
    diskpart 
    ```
    在 [開啟命令提示字元] 視窗中，輸入下列命令：

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. 設定的 Windows 的 Coordinated Universal Time (UTC) 時間。 也必須將 Windows 時間服務的啟動類型 (`w32time`) 到`Automatic`:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. 設定為高效能的電源設定檔：

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. 請確定環境變數`TEMP`和`TMP`會設定它們的預設值為：

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>檢查 Windows 服務
請確定每個下列的 Windows 服務設定為 Windows 預設值。 這些服務都必須設定以確保 VM 連線的最小。 若要重設啟動設定，請執行下列命令：
   
```PowerShell
Set-Service -Name bfe -StartupType Automatic
Set-Service -Name dhcp -StartupType Automatic
Set-Service -Name dnscache -StartupType Automatic
Set-Service -Name IKEEXT -StartupType Automatic
Set-Service -Name iphlpsvc -StartupType Automatic
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Automatic
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Automatic
Set-Service -Name RemoteRegistry -StartupType Automatic
```

## <a name="update-remote-desktop-registry-settings"></a>更新遠端桌面登錄設定
請確定遠端存取會正確地設定下列設定：

>[!NOTE] 
>當您執行時，您可能會收到一則錯誤訊息`Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name &lt;object name&gt; -value &lt;value&gt;`。 您可以放心忽略此訊息。 它只是表示網域不會推送該設定，透過群組原則物件。

1. 遠端桌面通訊協定 (RDP) 已啟用：
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. RDP 連接埠已正確設定。 預設連接埠是 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    當您部署 VM 時，會針對連接埠 3389 建立預設規則。 如果您想要變更連接埠號碼，這麼做之後，在 Azure 中部署 VM。

3. 接聽程式正在每個網路介面中進行接聽：
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. 設定 RDP 連線的網路層級驗證 (NLA) 模式：
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -force
     ```

5. 設定 Keep-Alive 值：
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" -Value 1 -Type DWord -force
    ```
6. 重新連線：
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" -Value 0 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" -Value 1 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" -Value 0 -Type DWord -force
    ```
7. 並行連線數目的限制：
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" -Value 4294967295 -Type DWord -force
    ```
8. 移除任何自我簽署的憑證，繫結至 RDP 接聽程式：
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    此程式碼可確保當您部署的 VM 可以連接您在開始。 如果您需要檢閱此資訊之後，您可以在動作在 Azure 中部署 VM。

9. 如果 VM 將會為網域的一部分，請檢查以確定不會還原先前的設定，下列的原則。 
    
    | 目標                                     | 原則                                                                                                                                                       | 值                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP 已啟用                           | 電腦設定\原則\Windows 設定\系統管理範本\元件\遠端桌面服務\遠端桌面工作階段主機\連線         | 允許使用者使用遠端桌面服務從遠端連線                                  |
    | NLA 群組原則                         | 設定\系統管理範本\元件\遠端桌面服務\遠端桌面工作階段主機\安全性                                                    | 遠端存取需要使用者驗證，使用 NLA |
    | 保持連線的設定                      | 電腦設定\原則\Windows 設定\系統管理範本\Windows 元件\遠端桌面服務\遠端桌面工作階段主機\連線 | 設定 Keep-Alive 連線間隔                                                 |
    | 重新連線設定                       | 電腦設定\原則\Windows 設定\系統管理範本\Windows 元件\遠端桌面服務\遠端桌面工作階段主機\連線 | 自動重新連線                                                                   |
    | 有限的數目的連線設定 | 電腦設定\原則\Windows 設定\系統管理範本\Windows 元件\遠端桌面服務\遠端桌面工作階段主機\連線 | 限制連線數目                                                              |

## <a name="configure-windows-firewall-rules"></a>設定 Windows 防火牆規則
1. 在三個設定檔 （網域、 標準和公用），以開啟 「 Windows 防火牆：

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. 在以允許 WinRM 透過三種防火牆設定檔 （網域、 私用和公用） 的 PowerShell 中執行下列命令，並啟用 PowerShell 遠端服務：
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. 啟用下列防火牆規則以允許 RDP 流量：

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. 啟用檔案及印表機共用，讓 VM 能夠回應 ping 命令，在虛擬網路內的規則：

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. 如果 VM 將會為網域的一部分，請檢查下列的 Azure AD 原則，以確定不還原先前的設定。 

    | 目標                                 | 原則                                                                                                                                                  | 值                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | 啟用 Windows 防火牆設定檔 | 電腦設定\原則\Windows 設定\系統管理範本\網路\網路連線\Windows 防火牆\網域設定檔\Windows 防火牆   | 保護所有網路連線         |
    | 啟用 RDP                           | 電腦設定\原則\Windows 設定\系統管理範本\網路\網路連線\Windows 防火牆\網域設定檔\Windows 防火牆   | 允許輸入遠端桌面例外狀況 |
    |                                      | 電腦設定\原則\Windows 設定\系統管理範本\網路\網路連線\Windows 防火牆\標準設定檔\Windows 防火牆 | 允許輸入遠端桌面例外狀況 |
    | 啟用 ICMP-V4                       | 電腦設定\原則\Windows 設定\系統管理範本\網路\網路連線\Windows 防火牆\網域設定檔\Windows 防火牆   | 允許 ICMP 例外狀況                   |
    |                                      | 電腦設定\原則\Windows 設定\系統管理範本\網路\網路連線\Windows 防火牆\標準設定檔\Windows 防火牆 | 允許 ICMP 例外狀況                   |

## <a name="verify-the-vm"></a>驗證 VM 

請確定 VM 處於狀況良好且安全的與 RDP 存取： 

1. 若要確定磁碟狀況良好且一致，請檢查在下一步 的 VM 重新啟動磁碟：

    ```PowerShell
    Chkdsk /f
    ```
    請確定此報表會顯示全新且狀況良好的磁碟。

2. 設定開機組態資料 (BCD) 設定。 

    > [!NOTE]
    > 使用較高權限的 PowerShell 視窗執行下列命令。
   
   ```powershell
    cmd

    bcdedit /set {bootmgr} integrityservices enable
    bcdedit /set {default} device partition=C:
    bcdedit /set {default} integrityservices enable
    bcdedit /set {default} recoveryenabled Off
    bcdedit /set {default} osdevice partition=C:
    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set {bootmgr} displaybootmenu yes
    bcdedit /set {bootmgr} timeout 5
    bcdedit /set {bootmgr} bootems yes
    bcdedit /ems {current} ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200

    exit
   ```
3. 傾印記錄檔能幫助您疑難排解 Windows 損毀問題。 啟用傾印記錄檔收集：

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name CrashDumpEnabled -Type DWord -force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name DumpFile -Type ExpandString -force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name NMICrashDump -Type DWord -force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -name DumpFolder -Type ExpandString -force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -name CrashCount -Type DWord -force -Value 10
    New-ItemProperty -Path $key -name DumpType -Type DWord -force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. 確認 Windows Management Instrumentation (WMI) 存放庫是一致的：

    ```PowerShell
    winmgmt /verifyrepository
    ```
    如果存放庫損毀，請參閱 [WMI：存放庫損毀或不](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)。

5. 請確定沒有其他應用程式正在使用連接埠 3389。 在 Azure 中，此連接埠是由 RDP 服務所使用。 若要查看哪些連接埠會使用在 VM 上，執行`netstat -anob`:

    ```PowerShell
    netstat -anob
    ```

6. 若要上傳 Windows VHD 做為網域控制站：

   * 遵循[這些額外的步驟](https://support.microsoft.com/kb/2904015)來準備磁碟。

   * 請確定您知道的目錄服務還原模式 (DSRM) 密碼，以防您必須以 DSRM 啟動的 VM，在某個時間點。 如需詳細資訊，請參閱 < [Set DSRM password](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx)。

7. 請確定您知道內建的 administrator 帳戶和密碼。 您可能想要重設目前的本機系統管理員密碼，並確定您可以使用此帳戶來登入 Windows 透過 RDP 連線。 此存取權限會受到 「 允許登入遠端桌面服務透過 「 群組原則物件。 檢視此物件在本機群組原則編輯器這裡：

    電腦設定\Windows 設定\安全性設定\本機原則\使用者權限指派

8. 請檢查下列的 Azure AD 原則，藉此確定您沒有封鎖透過 RDP 或網路的 RDP 存取：

    - 電腦設定\Windows 設定\安全性設定\本機原則\使用者權限指派\拒絕從網路存取此電腦

    - 電腦設定\Windows 設定\安全性設定\本機原則\使用者權限指派\拒絕從遠端桌面服務登入


9. 請檢查下列的 Azure AD 原則，藉此確定您沒有要移除的任何必要的存取帳戶：

   - 電腦設定 \windows 設定 \ 本機原則 \ 使用者權限 Assignment\Access 此電腦的網路

   原則應該會列出下列群組：

   - 系統管理員

   - 備份操作員

   - 所有人

   - 使用者

10. 重新啟動 VM，以確保 Windows 仍然狀況良好，並可透過 RDP 連線進行連接。 此時，您可能要在您本機 HYPER-V 以確定 VM 完全開始建立 VM。 然後測試以確保您可以連線到透過 RDP 的 VM。

11. 移除任何額外的傳輸驅動程式介面 (TDI) 篩選。 比方說，移除軟體分析 TCP 封包或額外的防火牆。 如果您需要檢閱此資訊之後，您可以在動作在 Azure 中部署 VM。

12. 解除安裝任何其他協力廠商軟體或相關實體的元件或任何其他虛擬化技術的驅動程式。

### <a name="install-windows-updates"></a>安裝 Windows 更新
在理想情況下，您應該保留隨時更新機器*修補程式等級*。 如果這不可行，請確定已安裝下列更新：

| 元件               | Binary         | Windows 7 SP1，Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1，Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709，Windows Server 2016 v1709 | Windows 10 v1803，Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| 儲存體                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| 網路                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Core                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| 遠端桌面服務問題 | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| 安全性                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
### 判斷何時要使用 Sysprep <a id="step23"></a>    

系統準備工具 (Sysprep) 是您可以執行以重設 Windows 安裝程序。 Sysprep 會移除所有個人資料並重設數個元件提供 「 現成 」 體驗。 

您通常會執行 Sysprep 來建立的範本，您可以用來部署數個其他具有特定設定的 Vm。 範本會呼叫*一般化映像*。

如果您想要從一部磁碟建立只有一個 VM，您不需要使用 Sysprep。 相反地，您可以在其中建立 VM*特製化映像*。 如需如何從特殊化磁碟建立 VM 的資訊，請參閱：

- [從特殊化磁碟建立 VM](create-vm-specialized.md)
- [從特殊化 VHD 磁碟建立 VM](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

如果您想要建立一般化映像，您需要執行 Sysprep。 如需詳細資訊，請參閱[如何使用 Sysprep:簡介](https://technet.microsoft.com/library/bb457073.aspx)。 

並非每個角色或 Windows 為基礎的電腦已安裝的應用程式可支援一般化映像。 因此在執行此程序之前，請確定 Sysprep 支援電腦的角色。 如需詳細資訊，請參閱[伺服器角色的 Sysprep 支援](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) \(英文\)。

### <a name="generalize-a-vhd"></a>將一般化 VHD

>[!NOTE]
> 執行之後`sysprep.exe`在下列步驟中，開啟 關閉 VM。 請勿將它重新開啟直到您在 Azure 中建立從該映像。

1. 登入 Windows VM。
1. 以系統管理員身分執行**命令提示字元**。 
1. 將目錄變更`%windir%\system32\sysprep`。 然後執行 `sysprep.exe`。
1. 在 [系統準備工具]  對話方塊中，選取 [進入系統全新體驗 (OOBE)]  ，並確認已勾選 [一般化]  核取方塊。

    ![系統準備工具](media/prepare-for-upload-vhd-image/syspre.png)
1. 在 [關機選項]  中選取 [關機]  。
1. 選取 [確定]  。
1. 當 Sysprep 完成時，關閉 VM。 不用**重新啟動**關閉 VM。

現在已準備好上傳 VHD。 如需如何從一般化磁碟建立 VM 的詳細資訊，請參閱[將一般化的 VHD 上傳並使用它來在 Azure 中建立新的 VM](sa-upload-generalized.md)。


>[!NOTE]
> 自訂*unattend.xml*不支援的檔案。 雖然我們支援`additionalUnattendContent`屬性，提供有限的支援新增[microsoft windows shell-安裝](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup)到選項*unattend.xml*檔案，Azure 的佈建代理程式使用。 您可以使用，例如[additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet)加入 FirstLogonCommands 和 LogonCommands。 如需詳細資訊，請參閱 < [additionalUnattendContent FirstLogonCommands 範例](https://github.com/Azure/azure-quickstart-templates/issues/1407)。


## <a name="complete-the-recommended-configurations"></a>完成建議的設定
下列設定不會影響 VHD 上傳。 不過，我們強烈建議您設定它們。

* 安裝[Azure 虛擬機器代理程式](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 然後您可以啟用 VM 擴充功能。 VM 延伸模組實作大部分的重要功能，您可能想要使用您的 Vm。 您必須擴充功能，例如，若要重設密碼，或設定 RDP。 如需詳細資訊，請參閱 < [Azure 虛擬機器代理程式概觀](../extensions/agent-windows.md)。
* 在 Azure 中建立 VM 之後，我們建議您將分頁檔放*暫存磁碟機的磁碟區*來改善效能。 您可以如下設定檔案位置：

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -force
   ```
  如果資料磁碟連結至 VM 時，通常是暫時的磁碟機磁碟區的代號*D*。這項指定可能不同，視您的設定和可用的磁碟機數目而定。

## <a name="next-steps"></a>後續步驟
* [將 Windows VM 映像上傳至 Azure 供 Resource Manager 部署使用](upload-generalized-managed.md)
* [針對 Azure Windows 虛擬機器啟用問題進行疑難排解](troubleshoot-activation-problems.md)

