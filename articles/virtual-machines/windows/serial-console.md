---
title: Azure 虛擬機器序列主控台 | Microsoft Docs
description: Azure 虛擬機器的雙向序列主控台。
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 9c16114b4f8d335dc750b1beef1fb6204ae20e0f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="virtual-machine-serial-console-preview"></a>虛擬機器序列主控台 (預覽) 


Azure 上的虛擬機器序列主控台可讓您存取 Linux 和 Windows 虛擬機器的文字型主控台。 這個序列連線會連至虛擬機器的 COM1 序列埠，可讓您存取虛擬機器，而與虛擬機器的網路/作業系統狀態無關。 目前只能透過 Azure 入口網站存取虛擬機器的序列主控台，並且只允許具有虛擬機器之「VM 參與者」或更高存取權的使用者才能執行此操作。 

> [!Note] 
> 若您同意使用規定即可取得預覽。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版的 Microsoft Azure 增補使用條款] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。目前此服務為**公開預覽版**，虛擬機器的序列主控台存取權可供全球 Azure 區域使用。 目前在 Azure Government、「Azure 德國」及「Azure 中國」雲端並未提供序列主控台。

 

## <a name="prerequisites"></a>先決條件 

* 虛擬機器必須啟用[開機診斷](boot-diagnostics.md)功能 
* 使用序列主控台的帳戶必須具有 VM 的[參與者角色](../../active-directory/role-based-access-built-in-roles.md)和[開機診斷](boot-diagnostics.md)儲存體帳戶。 

## <a name="open-the-serial-console"></a>開啟序列主控台
只有透過 [Azure 入口網站](https://portal.azure.com)，才能存取虛擬機器的序列主控台。 以下是透過入口網站存取虛擬機器之序列主控台的步驟 

  1. 開啟 Azure 入口網站
  2. 在左功能表中，選取 [虛擬機器]。
  3. 按一下清單中的 VM。 將會開啟該 VM 的概觀頁面。
  4. 向下捲動至 [支援與疑難排解] 區段，然後按一下 [序列主控台 (預覽)] 選項。 將會開啟含有序列主控台的新窗格，並開始連線。

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

### <a name="disable-feature"></a>停用功能
您可以藉由停用特定 VM 的開機診斷設定，來停用該 VM 的序列主控台功能。

## <a name="serial-console-security"></a>序列主控台安全性 

### <a name="access-security"></a>存取安全性 
僅限具有虛擬機器之 [VM 參與者](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)或更高存取權的使用者，才能存取序列主控台。 如果您的 AAD 租用戶要求使用 Multi-Factor Authentication，則存取序列主控台時也會需要 MFA，因為會透過 [Azure 入口網站](https://portal.azure.com)進行其存取。

### <a name="channel-security"></a>通道安全性
系統會加密在網路上來回傳送的所有資料。

### <a name="audit-logs"></a>稽核記錄
目前對序列主控台進行的所有存取都會記錄在虛擬機器的[開機診斷](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics)記錄中。 這些記錄的存取權均由 Azure 虛擬機器系統管理員所擁有和控制。  

>[!CAUTION] 
雖然系統不會記錄主控台的存取密碼，但如果在主控台內執行的命令包含或會輸出密碼、祕密、使用者名稱或任何其他形式的「個人識別資訊」(PII)，系統就會在實作序列主控台之回捲功能的過程中，將這些資訊與所有其他可見文字一起寫入至虛擬機器開機診斷記錄。 這些記錄是循環性的，並且只有具備診斷儲存體帳戶之讀取權限的個人才能存取這些記錄，不過，建議您針對任何可能涉及祕密和/或 PII 的情況，都依循使用「遠端桌面」的最佳做法。 

### <a name="concurrent-usage"></a>並行使用方式
如果在某位使用者已連線至序列主控台的情況下，另一位使用者成功取得該相同虛擬機器的存取權，系統就會將第一位使用者中斷連線，而讓第二位使用者連線，其方式類似於讓第一位使用者起立並離開實體主控台，而讓新使用者坐下進行操作。

>[!CAUTION] 
這意謂著系統不會將中斷連線的使用者登出！ 強制在中斷連線時登出的功能 (透過 SIGHUP 或類似的機制) 仍在規劃中。 就 Windows 而言，在 SAC 中會啟用自動逾時，不過，針對 Linux，您則可以設定終端機逾時設定。 


## <a name="accessing-serial-console-for-windows"></a>存取 Windows 的序列主控台 
Azure 上的新版 Windows Server 映像將會預設啟用[特殊系統管理主控台](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC)。 在伺服器版本的 Windows 上可支援 SAC，但在用戶端版本 (例如 Windows 10、Windows 8 或 Windows 7) 上則不支援。 若要為使用 2018 年 2 月或更舊版映像建立的 Windows 虛擬機器啟用序列主控台，請依照下列步驟進行操作： 

1. 透過「遠端桌面」連線至您的 Windows 虛擬機器
2. 從系統管理命令提示字元中執行下列命令 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. 重新啟動系統以啟用 SAC 主控台

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

如有需要，也可以離線啟用 SAC： 

1. 將您想要為其設定 SAC 的 Windows 磁碟以資料磁碟的形式連結至現有的 VM。 
2. 從系統管理命令提示字元中執行下列命令 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>如何知道是否已啟用 SAC 

如果未啟用 [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx)，序列主控台將不會顯示 SAC 提示。 在某些情況下，它可能會顯示「VM 健康情況」資訊，否則會顯示為空白。  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>讓開機功能表顯示在序列主控台中 

如果您需要讓 Windows 開機載入器提示顯示在序列主控台中，您可以將下列額外選項新增至 Windows 開機載入器。

1. 透過「遠端桌面」連線至您的 Windows 虛擬機器
2. 從系統管理命令提示字元中執行下列命令 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. 重新啟動系統以啟用開機功能表

> [!NOTE] 
> 目前並未支援功能鍵，如果您需要進階的開機選項，請使用 bcdedit /set {current} onetimeadvancedoptions on，如需更多詳細資料，請參閱 [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set)

## <a name="common-scenarios-for-accessing-windows-serial-console"></a>存取 Windows 序列主控台的常見案例 
案例          | 序列主控台中的動作                
:------------------|:----------------------------------------
不正確的防火牆規則 | 存取序列主控台並修正 iptables 或 Windows 防火牆規則 
檔案系統損毀/檢查 | 存取序列主控台並在登入 SAC CMD 之後復原檔案系統
RDP 設定問題 | 存取序列主控台並登入 cmd 通道。 檢查終端機服務的健康情況並視需要重新啟動。
網路鎖定系統| 存取序列主控台並登入 cmd 通道。 藉由 [netsh](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts) 命令列來檢查防火牆狀態。 

## <a name="errors"></a>Errors
大多數錯誤本質上都是暫時性的，重試連線將可解決這些錯誤。 下表顯示錯誤清單及緩和措施 

Error                            |   緩和 
:---------------------------------|:--------------------------------------------|
無法擷取 '<VMNAME>' 的開機診斷設定。 若要使用序列主控台，請確定已針對此 VM 啟用診斷開機診斷。 | 請確定 VM 已啟用[開機診斷](boot-diagnostics.md)。 
VM 處於已停止 (已解除配置) 狀態。 啟動 VM 並重試序列主控台連線。 | 虛擬機器必須處於已啟動狀態，才能存取序列主控台
您沒有可使用此 VM 序列主控台的必要權限。 請確定您至少具有「VM 參與者」角色權限。| 若要存取序列主控台，必須具有特定權限才能存取。 如需詳細資料，請參閱[存取需求](#prerequisites)
無法判斷開機診斷儲存體帳戶 '<STORAGEACCOUNTNAME>' 的資源群組。 請確認已針對此 VM 啟用開機診斷，且您具有此儲存體帳戶的存取權。 | 若要存取序列主控台，必須具有特定權限才能存取。 如需詳細資料，請參閱[存取需求](#prerequisites)

## <a name="known-issues"></a>已知問題 
由於我們仍處於序列主控台存取的預覽階段，因此我們正在處理一些已知問題，以下是這些問題的清單及可能的因應措施 

問題                           |   緩和 
:---------------------------------|:--------------------------------------------|
沒有虛擬機器擴展集執行個體序列主控台的相關選項 | 在預覽階段，不支援存取虛擬機器擴展集執行個體的序列主控台。
在出現連線橫幅後按 Enter 鍵並未顯示登入提示 | [按 Enter 鍵沒有任何作用](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md) \(英文\)
連線至 Windows VM 時只有顯示健康情況資訊| [Windows 健康情況訊號](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md) \(英文\)

## <a name="frequently-asked-questions"></a>常見問題集 
**問：如何傳送意見反應？**

A. 前往 https://aka.ms/serialconsolefeedback \(英文\) 來以問題形式提供意見反應。 或者，您也可以 (較不建議) 透過 azserialhelp@microsoft.com 或在 http://feedback.azure.com 的虛擬機器類別中傳送意見反應 

**問：我收到「現有主控台的 OS 類型 "Windows" 與要求的 OS 類型 Linux 相衝突？」錯誤**

A. 這是已知的問題，若要修正此問題，只需以 Bash 模式開啟 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 並重試即可。

**問：我無法存取序列主控台，哪裡可以找到支援案例？**

A. 此預覽版功能是透過「Azure 預覽條款」提供的。 對此功能的支援最好是透過上述管道處理。 

## <a name="next-steps"></a>後續步驟
* 序列主控台也適用於 [inux](../linux/serial-console.md) VM
* 深入了解[開機診斷](boot-diagnostics.md)
