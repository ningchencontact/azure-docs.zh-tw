---
title: 針對啟用災害復原複寫時的行動服務推送安裝失敗進行疑難排解 | Microsoft Docs
description: 針對啟用災害復原複寫時的行動服務安裝錯誤進行疑難排解
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 10/29/2018
ms.openlocfilehash: 1a8396f91f1e6f863d99be17dc8d00133a1bdd3a
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52162527"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>針對行動服務推送安裝問題進行疑難排解

安裝行動服務是啟用複寫期間的關鍵步驟。 此步驟的成功與否僅取決於是否符合必要條件，以及使用支援的設定。 您在行動服務安裝期間最常面臨的失敗是因為以下原因

* 認證/權限錯誤
* 連線錯誤
* 不支援的作業系統
* VSS 安裝失敗

當您啟用複寫時，Azure Site Recovery 會嘗試推送您虛擬機器上的安裝行動服務代理程式。 在這個過程中，組態伺服器會嘗試與虛擬機器連線，並複製代理程式。 若要啟用成功的安裝，請遵循下面所列的逐步疑難排解指引。

## <a name="credentials-check-errorid-95107--95108"></a>認證檢查 (錯誤碼：95107 & 95108)

* 確認啟用複寫期間所選擇的使用者帳戶是否**有效且精確**。
* Azure Site Recovery 需要 [系統管理員權限] 才能執行推送安裝。
  * 針對 Windows，請確認使用者帳戶是否具有來源機器上的系統管理存取權 (本機或網域)。
  * 如果您未使用網域帳戶，必須停用本機電腦上的遠端使用者存取控制。
    * 若要停用遠端使用者存取控制，請在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 登錄機碼之下，新增 DWORD：LocalAccountTokenFilterPolicy。 將值設定為 1。 若要執行此步驟，請從命令提示字元處執行下列命令：

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * 針對 Linux，您必須選擇根帳戶，才能成功安裝行動代理程式。

如果您想要修改所選使用者帳戶的認證，請遵循[此處](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)所提供的指示。

## <a name="connectivity-check-errorid-95117--97118"></a>**連線能力檢查 (錯誤碼：95117 & 97118)**

* 請確定您能夠從組態伺服器偵測來源機器。 如果您已選擇在啟用複寫期間相應放大處理序伺服器，請確保您能夠從處理序伺服器偵測您的來源機器。
  * 在來源伺服器機器的命令列中，如下所示使用 Telnet 來偵測搭配 https 連接埠 (135) 的組態伺服器/相應放大處理序伺服器，以查看是否有任何網路連線問題或防火牆連接埠封鎖問題。

     `telnet <CS/ scale-out PS IP address> <135>`
  * 檢查服務狀態 **InMage Scout VX Agent-Sentinel/Outpost**。 如果服務不在執行中，請啟動服務。
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
* 這可能是連線/網路/網域相關的問題。 也可能是由於 DNS 名稱解析問題或 TCP 連接埠耗盡的問題。 請檢查您的網域中是否有任何這類的已知問題。

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>檔案及印表機共用服務檢查 (錯誤碼：95105 & 95106)

完成連線檢查之後，請確認您的虛擬機器上已啟用 [檔案及印表機共用服務]。

針對 **Windows 2008 R2 和先前版本**，

* 若要透過 Windows 防火牆啟用檔案與列印共用，
  * 請開啟 [控制台] -> [系統及安全性] -> [Windows 防火牆] -> 在左側窗格上，按一下 [進階設定] -> 在主控台樹狀目錄中按一下 [輸入規則]。
  * 找出 [檔案及印表機共用] (NB-Session-In) 和 [檔案及印表機共用] (Smb-in) 的規則。 針對每個規則，以滑鼠右鍵按一下該規則，然後按一下 [啟用規則]。
* 若要透過群組原則啟用檔案共用，
  * 移至 [開始]，輸入 gpmc.msc 並搜尋。
  * 在 [導覽] 窗格中，開啟下列資料夾：本機電腦原則、使用者設定、系統管理範本、Windows 元件，以及網路共用。
  * 在 [詳細資料] 窗格中，按兩下 [防止使用者共用其設定檔內的檔案]。 若要停用群組原則設定，並讓使用者能夠共用檔案，請按一下 [停用]。 按一下 [確定] 以儲存變更。 若要深入了解，請按一下[這裡](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))。

針對**新版本**，請依照[這裡](vmware-azure-install-mobility-service.md)提供的指示來啟用檔案及印表機共用。

## <a name="windows-management-instrumentation-wmi-configuration-check"></a>Windows Management Instrumentation (WMI) 設定檢查

完成檔案和印表機服務檢查之後，請啟用通過防火牆的 WMI 服務。

* 在 [控制台] 中按一下 [安全性]，然後按一下 [Windows 防火牆]。
* 按一下 [變更設定]，然後按一下 [例外狀況] 索引標籤。
* 在 [例外狀況] 視窗中，選取 Windows Management Instrumentation (WMI) 的核取方塊，以啟用通過防火牆的 WMI 流量。 

您也可以在命令提示字元啟用通過防火牆的 WMI 流量。 請使用下列 `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes` 命令
您可以在下列文件中找到其他 WMI 疑難排解文件。

* [基本 WMI 測試](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI 疑難排解](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [WMI 指令碼和 WMI 服務問題的疑難排解](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>不支援的作業系統

失敗的另一個最常見原因可能是由於不支援的作業系統。 請確定您在可成功安裝行動服務的受支援作業系統/核心版本上。

若要深入了解 Azure Site Recovery 支援哪些作業系統，請參閱我們的[支援矩陣文件](vmware-physical-azure-support-matrix.md#replicated-machines)。

## <a name="vss-installation-failures"></a>VSS 安裝失敗

VSS 安裝是行動代理程式安裝的一部分。 此服務是用於產生應用程式一致復原點的程序之中。 在 VSS 安裝期間發生失敗的原因有許多種。 若要識別確切的錯誤，請參閱 **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**。 幾個常見錯誤及其解決步驟，將於下列小節中說明。

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS 錯誤 -2147023170 [0x800706BE] - 結束代碼 511

此問題最常發生於防毒軟體封鎖 Azure Site Recovery 服務之作業的情況。 若要解決這個問題：

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

## <a name="next-steps"></a>後續步驟

[了解如何](vmware-azure-tutorial.md)為 VMware VM 設定災害復原。