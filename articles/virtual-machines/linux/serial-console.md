---
title: Azure 虛擬機器序列主控台 | Microsoft Docs
description: Azure 虛擬機器的雙向序列主控台。
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 99d09455ed73b366fb3acfb414b9bd095df6319b
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/26/2018
ms.locfileid: "36962363"
---
# <a name="virtual-machine-serial-console-preview"></a>虛擬機器序列主控台 (預覽) 


Azure 上的虛擬機器序列主控台可讓您存取 Linux 和 Windows 虛擬機器的文字型主控台。 這個序列連線會連至虛擬機器的 COM1 序列埠，可讓您存取虛擬機器，而與虛擬機器的網路/作業系統狀態無關。 目前只能透過 Azure 入口網站存取虛擬機器的序列主控台，並且只允許具有虛擬機器之「VM 參與者」或更高存取權的使用者才能執行此操作。 

> [!Note] 
> 若您同意使用規定即可取得預覽。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版的 Microsoft Azure 增補使用條款] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。目前此服務為**公開預覽版**，虛擬機器的序列主控台存取權可供全球 Azure 區域使用。 目前在 Azure Government、「Azure 德國」及「Azure 中國」雲端並未提供序列主控台。


## <a name="prerequisites"></a>先決條件 

* 您必須使用資源管理部署模型。 不支援傳統部署。 
* 虛擬機器必須啟用[開機診斷](boot-diagnostics.md)功能 
* 使用序列主控台的帳戶必須具有 VM 的[參與者角色](../../role-based-access-control/built-in-roles.md)和[開機診斷](boot-diagnostics.md)儲存體帳戶。 
* 如需了解 Linux 發行版本的特定設定，請參閱[存取 Linux 的序列主控台](#accessing-serial-console-for-linux)


## <a name="open-the-serial-console"></a>開啟序列主控台
只有透過 [Azure 入口網站](https://portal.azure.com)，才能存取虛擬機器的序列主控台。 以下是透過入口網站存取虛擬機器之序列主控台的步驟 

  1. 開啟 Azure 入口網站
  2. 在左功能表中，選取 [虛擬機器]。
  3. 按一下清單中的 VM。 將會開啟該 VM 的概觀頁面。
  4. 向下捲動至 [支援與疑難排解] 區段，然後按一下 [序列主控台 (預覽)] 選項。 將會開啟含有序列主控台的新窗格，並開始連線。

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> 序列主控台需要一個已設定密碼的本機使用者。 此時，僅設定 SSH 公開金鑰的 VM 將無法存取序列主控台。 若要建立具有密碼的本機使用者，請依照 [VM 存取延伸模組](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension)的指示來建立具有密碼的本機使用者。

### <a name="disable-feature"></a>停用功能
您可以藉由停用特定 VM 的開機診斷設定，來停用該 VM 的序列主控台功能。

## <a name="serial-console-security"></a>序列主控台安全性 

### <a name="access-security"></a>存取安全性 
僅限具有虛擬機器之 [VM 參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)或更高存取權的使用者，才能存取序列主控台。 如果您的 AAD 租用戶要求使用 Multi-Factor Authentication，則存取序列主控台時也會需要 MFA，因為會透過 [Azure 入口網站](https://portal.azure.com)進行其存取。

### <a name="channel-security"></a>通道安全性
系統會加密在網路上來回傳送的所有資料。

### <a name="audit-logs"></a>稽核記錄
目前對序列主控台進行的所有存取都會記錄在虛擬機器的[開機診斷](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics)記錄中。 這些記錄的存取權均由 Azure 虛擬機器系統管理員所擁有和控制。  

>[!CAUTION] 
雖然系統不會記錄主控台的存取密碼，但如果在主控台內執行的命令包含或會輸出密碼、祕密、使用者名稱或任何其他形式的「個人識別資訊」(PII)，系統就會在實作序列主控台之回捲功能的過程中，將這些資訊與所有其他可見文字一起寫入至虛擬機器開機診斷記錄。 這些記錄是循環性的，並且只有具備診斷儲存體帳戶之讀取權限的個人才能存取這些記錄，不過，建議您針對任何可能涉及祕密和/或 PII 的情況，都依循使用 SSH 主控台的最佳做法。 

### <a name="concurrent-usage"></a>並行使用方式
如果在某位使用者已連線至序列主控台的情況下，另一位使用者成功取得該相同虛擬機器的存取權，系統就會將第一位使用者中斷連線，而讓第二位使用者連線，其方式類似於讓第一位使用者起立並離開實體主控台，而讓新使用者坐下進行操作。

>[!CAUTION] 
這意謂著系統不會將中斷連線的使用者登出！ 強制在中斷連線時登出的功能 (透過 SIGHUP 或類似的機制) 仍在規劃中。 就 Windows 而言，在 SAC 中會啟用自動逾時，不過，針對 Linux，您則可以設定終端機逾時設定。 若要這麼做，只需在您用來登入主控台之使用者的 .bash_profile 或 .profile 中新增 `export TMOUT=600`即可讓工作階段在 10 分鐘後逾時。

### <a name="disable-feature"></a>停用功能
您可以藉由停用特定 VM 的開機診斷設定，來停用該 VM 的序列主控台功能。

## <a name="common-scenarios-for-accessing-serial-console"></a>存取序列主控台的常見案例 
案例          | 序列主控台中的動作                |  OS 適用性 
:------------------|:-----------------------------------------|:------------------
損毀的 FSTAB 檔案 | 輸入金鑰以使用文字編輯器繼續並修正 FSTAB 檔案。 請參閱[如何修正 FSTAB 問題](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) \(機器翻譯\) | Linux 
不正確的防火牆規則 | 存取序列主控台並修正 iptables 或 Windows 防火牆規則 | Linux/Windows 
檔案系統損毀/檢查 | 存取序列主控台並復原檔案系統 | Linux/Windows 
SSH/RDP 設定問題 | 存取序列主控台並變更設定 | Linux/Windows 
網路鎖定系統| 透過入口網站存取序列主控台以管理系統 | Linux/Windows 
與開機載入器互動 | 透過序列主控台存取 GRUB/BCD | Linux/Windows 

## <a name="accessing-serial-console-for-linux"></a>存取 Linux 的序列主控台
為了讓序列主控台正確運作，必須設定讓客體作業系統將主控台訊息讀取並寫入至序列連接埠。 大多數[已背書的 Azure Linux 發行版本](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)都已預設設定序列主控台。 只要在入口網站中按一下 [序列主控台] 區段，即可存取該主控台。 

### <a name="access-for-red-hat"></a>存取 Red Hat 
Azure 上提供的 RedHat 映像已預設啟用主控台存取。 Red Hat 中的單一使用者模式會要求啟用根使用者 (預設為停用)。 如果您需要啟用單一使用者模式，請依下列指示進行操作：

1. 透過 SSH 登入 Red Hat 系統
2. 啟用根使用者的密碼 
 * `passwd root` (設定強式根密碼)
3. 確定根使用者只能透過 ttyS0 進行登入
 * `edit /etc/ssh/sshd_config` 並確定將 PermitRootLogin 設定為 no
 * `edit /etc/securetty file` 以只允許透過 ttyS0 進行登入 

現在，如果系統開機進入單一使用者模式，您將能夠透過根密碼進行登入。

或者，針對 RHEL 7.4 以上或 6.9 以上的版本，您也可以在 GRUB 提示字元中啟用單一使用者模式，請參閱[這裡](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)的指示

### <a name="access-for-ubuntu"></a>Ubuntu 的存取 
Azure 上提供的 Ubuntu 映像已預設啟用主控台存取。 如果系統開機進入「單一使用者模式」，您無須額外的認證即可存取。 

### <a name="access-for-coreos"></a>CoreOS 的存取
Azure 上提供的 CoreOS 映像已預設啟用主控台存取。 如有必要，可以透過變更 GRUB 參數讓 GRUB 系統可以開機進入「單一使用者模式」，而新增 `coreos.autologin=ttyS0` 則可讓核心使用者登入序列主控台並可供使用。 

### <a name="access-for-suse"></a>SUSE 的存取
Azure 上提供的 SLES 映像已預設啟用主控台存取。 如果您在 Azure 上使用舊版 SLES，請依照這篇 [KB 文章](https://www.novell.com/support/kb/doc.php?id=3456486) \(英文\) 來啟用序列主控台。 SLES 12 SP3 以上版本的新版映像也允許在系統開機進入緊急模式時，透過序列主控台存取。

### <a name="access-for-centos"></a>CentOS 的存取
Azure 上提供的 CentOS 映像已預設啟用主控台存取。 針對「單一使用者模式」，請依照類似上述 Red Hat 映像的指示進行操作。 

### <a name="access-for-oracle-linux"></a>Oracle Linux 的存取
Azure 上提供的 Oracle Linux 映像已預設啟用主控台存取。 針對「單一使用者模式」，請依照類似上述 Red Hat 映像的指示進行操作。

### <a name="access-for-custom-linux-image"></a>自訂 Linux 映像的存取
若要啟用自訂 Linux VM 映像的序列主控台，請在 /etc/inittab 中啟用主控台存取以在 ttyS0 上執行終端機。 以下是在 inittab 檔案中新增此設定的範例 

`S0:12345:respawn:/sbin/agetty -L 115200 console vt102` 

## <a name="errors"></a>Errors
大多數錯誤本質上都是暫時性的，重試連線將可解決這些錯誤。 下表顯示錯誤清單及緩和措施 

Error                            |   緩和 
:---------------------------------|:--------------------------------------------|
無法擷取 '<VMNAME>' 的開機診斷設定。 若要使用序列主控台，請確定已針對此 VM 啟用診斷開機診斷。 | 請確定 VM 已啟用[開機診斷](boot-diagnostics.md)。 
VM 處於已停止 (已解除配置) 狀態。 啟動 VM 並重試序列主控台連線。 | 虛擬機器必須處於已啟動狀態，才能存取序列主控台
您沒有可使用此 VM 序列主控台的必要權限。 請確定您至少具有「VM 參與者」角色權限。| 若要存取序列主控台，必須具有特定權限才能存取。 如需詳細資料，請參閱[存取需求](#prerequisites)
無法判斷開機診斷儲存體帳戶 '<STORAGEACCOUNTNAME>' 的資源群組。 請確認已針對此 VM 啟用開機診斷，且您具有此儲存體帳戶的存取權。 | 若要存取序列主控台，必須具有特定權限才能存取。如需詳細資料，請參閱[存取需求](#prerequisites)

## <a name="known-issues"></a>已知問題 
由於我們仍處於序列主控台存取的預覽階段，因此我們正在處理一些已知問題，以下是這些問題的清單及可能的因應措施 

問題                           |   緩和 
:---------------------------------|:--------------------------------------------|
沒有虛擬機器擴展集執行個體序列主控台的相關選項 |  在預覽階段，不支援存取虛擬機器擴展集執行個體的序列主控台。
在出現連線橫幅後按 Enter 鍵並未顯示登入提示 | [按 Enter 鍵沒有任何作用](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md) \(英文\)


## <a name="frequently-asked-questions"></a>常見問題集 
**問：如何傳送意見反應？**

A. 前往 https://aka.ms/serialconsolefeedback \(英文\) 來以問題形式提供意見反應。 或者，您也可以 (較不建議) 透過 azserialhelp@microsoft.com 或在 http://feedback.azure.com 的虛擬機器類別中傳送意見反應

**問：我收到「現有主控台的 OS 類型 "Windows" 與要求的 OS 類型 Linux 相衝突？」錯誤**

A. 這是已知的問題，若要修正此問題，只需以 Bash 模式開啟 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 並重試即可。

**問：我無法存取序列主控台，哪裡可以找到支援案例？**

A. 此預覽版功能是透過「Azure 預覽條款」提供的。 對此功能的支援最好是透過上述管道處理。 

## <a name="next-steps"></a>後續步驟
* 序列主控台也適用於 [Windows](../windows/serial-console.md) VM
* 深入了解[開機診斷](boot-diagnostics.md)
