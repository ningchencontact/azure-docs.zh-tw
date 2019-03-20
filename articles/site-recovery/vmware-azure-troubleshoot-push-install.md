---
title: 針對啟用災害復原複寫時的行動服務推送安裝失敗進行疑難排解 | Microsoft Docs
description: 針對啟用災害復原複寫時的行動服務安裝錯誤進行疑難排解
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 02/27/2019
ms.openlocfilehash: 65b8253a307693d00f5eaefe7660d500dce49be4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58078647"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>針對行動服務推送安裝問題進行疑難排解

安裝行動服務是啟用複寫期間的關鍵步驟。 此步驟的成功與否僅取決於是否符合必要條件，以及使用支援的設定。 您在行動服務安裝期間最常面臨的失敗是因為以下原因：

* [認證/權限錯誤](#credentials-check-errorid-95107--95108)
* [登入失敗](#login-failures-errorid-95519-95520-95521-95522)
* [連線錯誤](#connectivity-failure-errorid-95117--97118)
* [檔案及印表機共用錯誤](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [WMI 失敗](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [不支援的作業系統](#unsupported-operating-systems)
* [不支援的開機設定](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [VSS 安裝失敗](#vss-installation-failures)
* [裝置名稱位於 GRUB 設定而非裝置 UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [LVM 磁碟區](#lvm-support-from-920-version)
* [重新啟動警告](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

當您啟用複寫時，Azure Site Recovery 會嘗試推送您虛擬機器上的安裝行動服務代理程式。 在這個過程中，組態伺服器會嘗試與虛擬機器連線，並複製代理程式。 若要啟用成功的安裝，請遵循下面所列的逐步疑難排解指引。

## <a name="credentials-check-errorid-95107--95108"></a>認證檢查 (錯誤識別碼：95107 & 95108)

* 確認啟用複寫期間所選擇的使用者帳戶是否**有效且精確**。
* Azure Site Recovery 需要**根**帳戶或具備**系統管理員權限**的使用者帳戶，才能執行推送安裝。 否則，來源機器上將會封鎖推送安裝。
  * 針對 Windows (**錯誤 95107**)，請確認使用者帳戶是否具有來源機器上的系統管理存取權 (本機或網域)。
  * 如果您未使用網域帳戶，必須停用本機電腦上的遠端使用者存取控制。
    * 若要停用遠端使用者存取控制，請在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 登錄機碼之下，新增 DWORD：LocalAccountTokenFilterPolicy。 將值設定為 1。 若要執行此步驟，請從命令提示字元處執行下列命令：

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * 針對 Linux (**錯誤 95108**)，您必須選擇根帳戶，才能成功安裝行動代理程式。 此外，SFTP 服務應執行。 在 sshd_config 檔案中啟用 SFTP 子系統與密碼驗證：
    1. 以 root 的身分登入。
    2. 前往 /etc/ssh/sshd_config 檔案，找到以 PasswordAuthentication 開頭這一行。
    3. 取消該行的註解，並將值變更為 yes。
    4. 找到以 Subsystem 開頭這一行，並取消其註解。
    5. 重新啟動 sshd 服務。

如果您想要修改所選使用者帳戶的認證，請遵循[此處](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)所提供的指示。

## <a name="insufficient-privileges-failure-errorid-95517"></a>權限不足失敗 (錯誤識別碼：95517)

如果選擇安裝行動代理程式的使用者沒有系統管理員權限，將不會允許設定伺服器/相應放大處理序伺服器將行動代理程式軟體複製到來源機器。 因此，此錯誤是存取遭拒失敗的結果。 請確定使用者帳戶具備系統管理員權限。

如果您想要修改所選使用者帳戶的認證，請遵循[此處](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)所提供的指示。

## <a name="insufficient-privileges-failure-errorid-95518"></a>權限不足失敗 (錯誤識別碼：95518)

主要網域和工作站之間的網域信任關係建立失敗時嘗試登入來源機器時，行動代理程式安裝會失敗，錯誤識別碼 95518。 因此，請確定用來安裝行動代理程式的使用者帳戶具有系統管理權限才能透過來源電腦的主要網域登入。

如果您想要修改所選使用者帳戶的認證，請遵循[此處](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)所提供的指示。

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>登入失敗 (錯誤識別碼：95519、95520、95521、95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>使用者帳戶的認證已被停用 (錯誤識別碼：95519)

啟用複寫期間選擇的使用者帳戶已經停用。 若要啟用使用者帳戶，請參閱[這篇文章](https://aka.ms/enable_login_user)，或使用實際的使用者名稱取代 *username* 以執行以下命令。
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>認證因多次嘗試登入失敗而被鎖定 (錯誤識別碼：95520)

多次重試存取機器失敗將會導致使用者帳戶遭到鎖定。 失敗原因可能是：

* 在組態設定期間提供的認證不正確，或
* 啟用複寫期間選擇的使用者帳戶是錯的

因此，請依照[此處](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)提供的指示修改選擇的認證，然後在一段時間之後重試作業。

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>來源電腦上的登入伺服器無法使用 (錯誤識別碼：95521)

當來源機器上無法使用登入伺服器時，就會發生此錯誤。 無法使用登入伺服器會導致登入要求失敗，因此無法安裝行動代理程式。 若要成功登入，請確定來源機器上可以使用登入伺服器並啟動登入服務。 如需詳細指示，請參閱 KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available)錯誤訊息：有可用的目前沒有登入伺服器。

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>登入服務未在來源電腦上執行 (錯誤識別碼：95522)

來源機器上的登入服務未執行，導致登入要求失敗。 因此，無法安裝行動代理程式。 若要解決此問題，請確定來源機器上的登入服務在執行中，才能成功登入。 若要啟動登入服務，請從命令提示字元執行命令 "net start Logon"，或從工作管理員啟動 "NetLogon" 服務。

## <a name="connectivity-failure-errorid-95117--97118"></a>**連線失敗 (錯誤識別碼：95117 & 97118)**

設定伺服器/相應放大處理序伺服器會嘗試連線來源 VM 以安裝行動代理程式。 若因為網路連線問題而無法連線來源機器時，就會發生此錯誤。 解決方式：

* 請確定您能夠從組態伺服器偵測來源機器。 如果您已選擇在啟用複寫期間相應放大處理序伺服器，請確保您能夠從處理序伺服器偵測您的來源機器。
  * 在來源伺服器機器的命令列中，如下所示使用 Telnet 來偵測搭配 https 連接埠 (135) 的組態伺服器/相應放大處理序伺服器，以查看是否有任何網路連線問題或防火牆連接埠封鎖問題。

     `telnet <CS/ scale-out PS IP address> <135>`
* 此外，對於 **Linux VM**，
  * 請檢查是否已安裝最新的 openssh、openssh-server 和 openssl 套件。
  * 請檢查並確定安全殼層 (SSH) 已啟用且正在連接埠 22 上執行。
  * SFTP 服務應執行。 若要在 sshd_config 檔案中啟用 SFTP 子系統與密碼驗證，
    * 以 root 的身分登入。
    * 前往 /etc/ssh/sshd_config 檔案，找到以 PasswordAuthentication 開頭這一行。
    * 取消該行的註解，並將值變更為 yes
    * 找到以 Subsystem 開頭這一行，並取消其註解
    * 重新啟動 sshd 服務。
* 如果一段時間之後沒有適當的回應，連線嘗試可能會失敗，或是因為連線的主機無法回應，則建立的連線可能失敗。
* 這可能是連線/網路/網域相關的問題。 也可能是由於 DNS 名稱解析問題或 TCP 連接埠耗盡的問題。 檢查您的網域中是否有任何這類的已知問題。

## <a name="connectivity-failure-errorid-95523"></a>連線失敗 (錯誤識別碼：95523)

當找不到來源電腦所在的網路，或可能已被刪除或無法再使用時，就會發生此錯誤。 解決錯誤的唯一方法是確保網路存在。

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>檔案及印表機共用服務檢查 (錯誤識別碼：95105 & 95106)

完成連線檢查之後，請確認您的虛擬機器上已啟用 [檔案及印表機共用服務]。 將行動代理程式複製到來源機器時，這些設定是必要的。

針對 **Windows 2008 R2 和先前版本**，

* 若要透過 Windows 防火牆啟用檔案與列印共用，
  * 請開啟 [控制台] -> [系統及安全性] -> [Windows 防火牆] -> 在左側窗格上，按一下 [進階設定] -> 在主控台樹狀目錄中按一下 [輸入規則]。
  * 找出 [檔案及印表機共用] (NB-Session-In) 和 [檔案及印表機共用] (Smb-in) 的規則。 針對每個規則，以滑鼠右鍵按一下該規則，然後按一下 [啟用規則]。
* 若要透過群組原則啟用檔案共用，
  * 移至 [開始]，輸入 gpmc.msc 並搜尋。
  * 在瀏覽窗格中，開啟下列資料夾：本機電腦原則、使用者設定、系統管理範本、Windows 元件，以及網路共用。
  * 在 [詳細資料] 窗格中，按兩下 [防止使用者共用其設定檔內的檔案]。 若要停用群組原則設定，並讓使用者能夠共用檔案，請按一下 [停用]。 按一下 [確定] 以儲存變更。 若要進一步了解，請參閱[啟用或停用檔案共用使用群組原則](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))。

針對**更新版本**，請遵循中提供的指示[安裝行動服務的 VMware Vm 和實體伺服器災害復原](vmware-azure-install-mobility-service.md)啟用檔案及印表機共用。

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Windows Management Instrumentation (WMI) 設定檢查 (錯誤識別碼：95103)

完成檔案和印表機服務檢查之後，請為私人、公用和網域設定檔啟用通過防火牆的 WMI 服務。 若要在來源機器上完成遠端執行，這些設定是必要的。 若要啟用：

* 移至 [控制台]，按一下 [安全性]，然後按一下 [Windows 防火牆]。
* 按一下 [變更設定]，然後按一下 [例外狀況] 索引標籤。
* 在 [例外狀況] 視窗中，選取 Windows Management Instrumentation (WMI) 的核取方塊，以啟用通過防火牆的 WMI 流量。 

您也可以在命令提示字元啟用通過防火牆的 WMI 流量。 請使用下列 `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes` 命令
您可以在下列文件中找到其他 WMI 疑難排解文件。

* [基本 WMI 測試](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI 疑難排解](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [WMI 指令碼和 WMI 服務問題的疑難排解](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>不支援的作業系統

失敗的另一個最常見原因可能是由於不支援的作業系統。 請確定您在可成功安裝行動服務的受支援作業系統/核心版本上。 請避免使用私人的修補程式。
若要檢視 Azure Site Recovery 所支援的作業系統和核心版本的清單，請參閱我們的[支援矩陣文件](vmware-physical-azure-support-matrix.md#replicated-machines)。

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>不支援的開機磁碟設定 (錯誤識別碼：95309、95310、95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>開機與系統磁碟分割/磁碟區不是同一個磁碟 (錯誤識別碼：95309)

9.20 之前的版本不支援開機和系統磁碟分割/磁碟區位於不同磁碟的設定。 從 [9.20 版](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)開始支援這樣的設定。 請使用最新版本以取得此支援。

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>開機磁碟無法使用 (錯誤識別碼：95310)

無法保護不含開機磁碟的虛擬機器。 這是為了確保在容錯移轉作業期間順利復原虛擬機器。 缺少開機磁碟會導致無法在容錯移轉之後啟動機器。 確定虛擬機器包含開機磁碟，然後重試此作業。 另請注意，不支援同部電腦上有多個開機磁碟。

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>來源電腦上存在多個開機磁碟 (錯誤識別碼：95311)

具有多個開機磁碟的虛擬機器不是[支援的組態](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)。

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>系統磁碟分割位於多個磁碟上 (錯誤識別碼：95313)

9.20 之前的版本不支援開機磁碟分割或磁碟區位於多個磁碟的設定。 從 [9.20 版](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)開始支援這樣的設定。 請使用最新版本以取得此支援。

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>啟用保護失敗，因為裝置名稱是在 GRUB 設定中提及，而非 UUID (錯誤識別碼：95320)

**可能的原因：** </br>
GRUB 組態檔 ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub") 可能包含 **root** 和 **resume** 參數的值作為實際裝置名稱，而不是 UUID。 Site Recovery 會強制執行 UUID 方法，因為裝置名稱可能會經由 VM 重新開機而變更，而且 VM 可能不會提出容錯移轉時的相同名稱而造成問題。 例如︰ </br>


- 下面這一行來自 GRUB 檔案 **/boot/grub2/grub.cfg**。 <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- 下面這一行來自 GRUB 檔案 **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

請觀察上述的粗體字串，其中 GRUB (而非 UUID) 具有 "root" 和 "resume" 參數的實際裝置名稱。
 
**修正方式：**<br>
裝置名稱應該取代為對應的 UUID。<br>


1. 執行命令 "blkid <device name>" 來尋找裝置的 UUID。 例如︰<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. 現在請以如 "root=UUID=<UUID>" 的格式，以裝置名稱的 UUID 取代裝置名稱。 比方說，如果我們裝置名稱取代為根的 UUID，並繼續上述檔案中的參數"/ boot/grub2/grub.cfg"，"/ boot/grub2/grub.cfg"或"/ 等/預設/grub： 然後將檔案中的行看起來像。 <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
3. 重新啟動保護

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>安裝行動服務已完成但有重新啟動警告 (錯誤識別碼：95265 與 95266)

Site Recovery 行動服務具有許多元件，其中一個名叫篩選器驅動程式。 篩選器驅動程式只會在系統重新啟動時被載入系統記憶體。 這代表對篩選器驅動程式的修正只能在新篩選器驅動程式載入時生效，而那只會在系統重新啟動時發生。

**請注意**，這只是警告，現有的複寫將繼續運作，即使在新代理程式更新之後也一樣。 您可以選擇重新啟動，每當您想要取得新的篩選器驅動程式，但如果您根本不必重新開機舊的篩選器驅動程式會保留在工作上的優勢。 因此，在更新且不重新啟動的情況下，除了篩選器驅動程式之外，**行動服務中其他增強功能和修正的優點都會生效**。 因此，建議使用，但不一定要在每次升級後重新開機。 如需重新開機時必要的資訊，請設定[重新啟動行動代理程式升級後的來源機器](https://aka.ms/v2a_asr_reboot)Azure Site Recovery 中的服務更新中的一節。

> [!TIP]
>如需排程維護時段期間的升級最佳作法，請參閱 <<c0> [ 支援的最新的 OS/核心版本](https://aka.ms/v2a_asr_upgrade_practice)Azure Site Recovery 中的服務更新中。

## <a name="lvm-support-from-920-version"></a>從 9.20 版開始支援 LVM

9.20 之前的版本只有資料磁碟支援 LVM。 /boot 應該位於磁碟分割區，而不是 LVM 磁碟區。

從 [9.20 版本](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)開始支援 [LVM 上的 OS 磁碟](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)。 請使用最新版本以取得此支援。

## <a name="insufficient-space-errorid-95524"></a>空間不足 (錯誤識別碼：95524)

將行動代理程式複製到來源機器時，至少需要 100 MB 的可用空間。 因此，請確保來源機器具有必要的可用空間，然後重試作業。

## <a name="vss-installation-failures"></a>VSS 安裝失敗

VSS 安裝是行動代理程式安裝的一部分。 此服務是用於產生應用程式一致復原點的程序之中。 在 VSS 安裝期間發生失敗的原因有許多種。 若要識別確切的錯誤，請參閱 **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**。 幾個常見錯誤及其解決步驟，將於下列小節中說明。

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS 錯誤 -2147023170 [0x800706BE] - 結束代碼 511

大部分在防毒軟體封鎖了 Azure Site Recovery 服務的作業時，會看到這個問題。 若要解決此問題︰

1. 排除[這裡](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)所提到的所有資料夾。
2. 遵循您的防毒軟體提供者所發佈的指導方針，以解除 Windows 中對 DLL 註冊的封鎖。

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS 錯誤 7 [0x7] - 結束代碼 511

這是執行階段錯誤，且是因記憶體不足以安裝 VSS 所導致。 請提升磁碟空間以成功完成此作業。

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS 錯誤 -2147023824 [0x80070430] - 結束代碼 517

此錯誤會在 Azure Site Recovery VSS 提供者服務[被標記為要進行刪除](https://msdn.microsoft.com/en-us/library/ms838153.aspx) \(英文\) 的情況下發生。 請嘗試執行下列命令列來在來源電腦上手動安裝 VSS

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS 錯誤 -2147023841 [0x8007041F] - 結束代碼 512

此錯誤會在 Azure Site Recovery VSS 提供者服務資料庫[已鎖定](https://msdn.microsoft.com/en-us/library/ms833798.aspx)的情況下發生。請嘗試執行下列命令列來在來源電腦上手動安裝 VSS

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-exit-code-806"></a>VSS 結束代碼 806

此錯誤會在用於安裝的使用者帳戶沒有執行 CSScript 命令之權限的情況下發生。 請為該使用者帳戶提供必要權限以執行指令碼，並重試該作業。

### <a name="other-vss-errors"></a>其他 VSS 錯誤

請嘗試執行下列命令列來在來源電腦上手動安裝 VSS 提供者服務

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>VSS 錯誤-0x8004E00F

在安裝行動代理程式，因為在 DCOM 和 DCOM 處於重大狀態的問題通常發生此錯誤。

您可以使用下列程序來判斷錯誤的原因。

**檢查安裝記錄檔**

1. 開啟安裝記錄檔位於 c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log。
2. 下列的錯誤表示此問題：

    取消註冊現有的應用程式...建立目錄物件取得的應用程式集合 

    錯誤：

    - 錯誤碼：-2147164145 [0x8004E00F]
    - 結束代碼：802

若要解決此問題：

請連絡[Microsoft Windows 平台小組](https://aka.ms/Windows_Support)以取得協助解決 DCOM 問題。

當 DCOM 問題解決時，請重新安裝 Azure Site Recovery VSS 提供者使用下列命令，以手動方式：
 
**C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd**
  
如果應用程式一致性不是很重要的災害復原需求，您可以略過安裝 VSS 提供者。 

略過 Azure Site Recovery VSS 提供者安裝，並以手動方式安裝 Azure Site Recovery VSS 提供者後安裝：

1. 安裝行動服務。 
   > [!Note]
   > 
   > 將安裝失敗後安裝設定 的步驟。 
2. 略過 VSS 安裝：
   1. 開啟位於的 Azure Site Recovery 行動服務安裝目錄：
   
      C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
   2. 修改 Azure Site Recovery VSS 提供者安裝指令碼**nMageVSSProvider_Install**並**InMageVSSProvider_Uninstall.cmd**永遠成功加入下列幾行：
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. 手動重新執行行動代理程式安裝。 
4. 當安裝成功，並移至下一個步驟中，**設定**，移除您所新增的行。
5. 若要安裝 VSS 提供者，請開啟以系統管理員命令提示字元並執行下列命令：
   
    **C:\Program 檔案 (x86) \Microsoft Azure Site Recovery\agent >.\InMageVSSProvider_Install.cmd**

9. 確認 ASR VSS 提供者已安裝為 Windows 服務中的服務，然後開啟 元件服務 MMC，若要確認已列出 ASR VSS 提供者。
10. 如果安裝 VSS 提供者持續失敗，使用 /CX，以解決在 CAPI2 中的權限錯誤。

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>VSS 提供者安裝失敗，因為非叢集電腦上啟用叢集服務

此問題會導致 Azure Site Recovery 行動代理程式安裝因為 COM +，導致安裝 VSS 提供者發生問題的 ASAzure 站台復原 VSS 提供者安裝步驟期間失敗。
 
### <a name="to-identify-the-issue"></a>若要找出問題

在記錄檔位於 C:\ProgramData\ASRSetupLogs\UploadedLogs 在組態伺服器上\<日期時間 > UA_InstallLogFile.log，您會發現下列例外狀況：

COM + 無法與 Microsoft 分散式交易協調器 (來自 HRESULT 的例外狀況：0x8004E00F)

若要解決此問題：

1.  請確認此機器的非叢集電腦，且，未使用叢集元件。
3.  如果未使用的元件，請從機器移除叢集元件。

## <a name="drivers-are-missing-on-the-source-server"></a>在來源伺服器上沒有的驅動程式

如果行動代理程式安裝失敗，請檢查以判斷某些必要的驅動程式是否遺漏某些控制集中 C:\ProgramData\ASRSetupLogs 下的記錄檔。
 
若要解決此問題：
  
1. 使用登錄編輯程式如 regedit.msc，開啟登錄。
2. 開啟 HKEY_LOCAL_MACHINE\SYSTEM 節點。
3. 在 [系統] 節點中，找出控制項集合。
4. 開啟每個控制項集合，並確認下列的 Windows 驅動程式會出現：

   - atapi
   - vmbus
   - storflt
   - storvsc
   - intelide
 
重新安裝任何缺少的驅動程式。

## <a name="next-steps"></a>後續步驟

[了解如何](vmware-azure-tutorial.md)為 VMware VM 設定災害復原。
