---
title: 適用於 Windows 的 Azure 虛擬機器序列主控台 | Microsoft Docs
description: Azure Windows 虛擬機器的雙向序列主控台。
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
ms.date: 10/31/2018
ms.author: harijay
ms.openlocfilehash: 61b64b63a53318e0a703678d5525399fe13efa83
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432756"
---
# <a name="virtual-machine-serial-console-for-windows"></a>適用於 Windows 的虛擬機器序列主控台

Azure 入口網站上的虛擬機器 (VM) 序列主控台可讓您存取 Windows 虛擬機器的文字型主控台。 這個序列連線會連線至虛擬機器的 COM1 序列埠，讓您存取虛擬機器、未依存於虛擬機器的網路或作業系統狀態。 只能使用 Azure 入口網站存取虛擬機器的序列主控台。 只允許那些對虛擬機器具有「虛擬機器參與者」或更高級別存取角色的使用者。

如需 Linux VM 的序列主控台文件，請參閱[適用於 Linux 的虛擬機器序列主控台](serial-console-linux.md)。

> [!NOTE]
> 適用於虛擬機器的序列主控台已在全球 Azure 區域公開推出。 目前尚未在 Azure Government 或「Azure 中國」雲端中提供序列主控台。


## <a name="prerequisites"></a>必要條件

* 您正在其中存取序列主控台的 VM 必須使用資源管理部署模型。 不支援傳統部署。

* 您正在其中存取序列主控台的 VM 必須啟用[開機診斷](boot-diagnostics.md)。

    ![開機診斷設定](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* 使用序列主控台的帳戶必須具有 VM 的[虛擬機器參與者角色](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)和[開機診斷](boot-diagnostics.md)儲存體帳戶。

* 您正在其中存取序列主控台的 VM 必須具有密碼型帳戶。 您可以使用 VM 存取擴充的[重設密碼](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password)功能來建立一個帳戶。 然後，選取 [支援與疑難排解] 區段中的 [重設密碼]。


## <a name="get-started-with-the-serial-console"></a>開始使用序列主控台
只有透過 Azure 入口網站，才能存取虛擬機器的序列主控台：

  1. 開啟 [Azure 入口網站](https://portal.azure.com)。
  1. 在左功能表上，選取 [虛擬機器]。
  1. 在清單中選取您的 VM。 將會開啟該 VM 的概觀頁面。
  1. 向下捲動至 [支援與疑難排解] 區段，然後選取 [序列主控台]。 這會開啟含有序列主控台的新窗格，並開始連線。

## <a name="enable-serial-console-functionality"></a>啟用序列主控台功能

> [!NOTE]
> 如果您在序列主控台中沒有看到任何項目，請確定您的 VM 上已啟用開機診斷。

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>在自訂或舊版的映像中啟用序列主控台
Azure 上的新版 Windows Server 映像預設會啟用[特殊系統管理主控台](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC)。 在伺服器版本的 Windows 上可支援 SAC，但在用戶端版本 (例如 Windows 10、Windows 8 或 Windows 7) 上則不支援。

至於舊版 Windows Server 映像 (在 2018 年 2 月前建立的映像)，您可以透過 Azure 入口網站的執行命令功能自動啟用序列主控台。 在 Azure 入口網站中，選取 [執行命令]，然後從清單中選取名為 **EnableEM** 的命令。

![執行命令清單](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

或者，若要為 2018 年 2 月前所建立的 Windows 虛擬機器啟用序列主控台，請遵循下列步驟：

1. 透過使用「遠端桌面」連線至您的 Windows 虛擬機器
1. 從系統管理命令提示字元中執行下列命令：
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. 重新啟動系統以啟用 SAC 主控台。

    ![SAC 主控台](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

如有需要，也可以離線啟用 SAC：

1. 將您想要為其設定 SAC 的 Windows 磁碟以資料磁碟的形式連結至現有的 VM。

1. 從系統管理命令提示字元中執行下列命令：
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>如何得知 SAC 是否已啟用？

如果未啟用 [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx)，序列主控台將不會顯示 SAC 提示。 在某些情況下會顯示 VM 健康情況資訊，在其他情況下則會空白。 如果您使用在 2018 年 2 月之前建立的 Windows Server 映像，SAC 可能不會啟用。

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>在序列主控台中啟用 Windows 開機功能表

如果您需要讓 Windows 開機載入器提示顯示在序列主控台中，您可以將下列額外選項新增至開機組態資料。 如需詳細資訊，請參閱 [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set)。

1. 透過使用「遠端桌面」連線至您的 Windows 虛擬機器。

1. 從系統管理命令提示字元中執行下列命令：
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. 重新啟動系統以啟用開機功能表

> [!NOTE]
> 您為開機管理程式功能表設定來顯示的逾時，將會影響您的 OS 開機時間。 如果您認為 10 秒的逾時值太短或太長，請將其設定為不同的值。

## <a name="use-serial-console"></a>使用序列主控台

### <a name="use-cmd-or-powershell-in-serial-console"></a>在序列主控台中使用 CMD 或 PowerShell

1. 連線到序列主控台。 如果連線成功，則提示為 **SAC>**：

    ![連線到 SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  輸入 `cmd` 來建立具有 CMD 執行個體的通道。

1.  輸入 `ch -si 1` 以切換至執行 CMD 執行個體的通道。

1.  按 **Enter** 鍵，然後輸入具有系統管理權限的登入認證。

1.  當您輸入有效的認證之後，即會開啟 CMD 執行個體。

1.  若要啟動 PowerShell 執行個體，在 CMD 執行個體中輸入 `PowerShell`，然後按 **Enter** 鍵。

    ![開啟 PowerShell 執行個體](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>使用序列主控台進行 NMI 呼叫
非遮罩式插斷 (NMI) 旨在建立虛擬機器上軟體不會忽略的訊號。 在過去，NMI 已用來監視系統上需要特定回應時間的硬體問題。 現在，程式設計人員和系統管理員通常會使用 NMI 作為對無回應系統進行偵錯和疑難排解的機制。

使用序列主控台命令列中的鍵盤圖示，即可將 NMI 傳送至 Azure 虛擬機器。 NMI 序列傳遞出去後，虛擬機器組態將會控制系統的回應方式。 您可以將 Windows 設定成在收到 NMI 時當機並建立記憶體傾印檔案。

![傳送 NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

如需有關將 Windows 設定成在收到 NMI 時建立損毀傾印的資訊，請參閱：[如何使用 NMI 來產生損毀傾印檔案](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file) \(機器翻譯\)。

### <a name="use-function-keys-in-serial-console"></a>在序列主控台中使用功能鍵
系統會啟用功能鍵供 Windows VM 中的序列主控台使用。 序列主控台下拉式清單中的 F8 提供方便進入 [進階開機設定] 功能表的簡單方式，但序列主控台與所有其他功能鍵相容。 因此視您使用序列主控台的電腦而定，可能需要按鍵盤上的 **Fn** + **F1** (或 F2、F3 等等) 鍵。

### <a name="use-wsl-in-serial-console"></a>在序列主控台中使用 WSL
Windows Server 2019 或更新版本已支援適用於 Linux 的 Windows 子系統 (WSL)，所以如果您是執行 Windows Server 2019 或更新版本，也可以啟用 WSL 以在序列主控台內使用。 這對已經熟悉 Linux 命令使用者來說可能有許多好處。 若要啟用 Windows Server 的 WSL，請參閱[安裝指南](https://docs.microsoft.com/windows/wsl/install-on-server)。

## <a name="disable-serial-console"></a>停用序列主控台
根據預設，所有訂用帳戶都已啟用所有 VM 的序列主控台存取權。 您可以在訂用帳戶層級或 VM 層級停用序列主控台。

> [!NOTE]
> 若要啟用或停用訂用帳戶的序列主控台，您必須有該訂用帳戶的寫入權限。 這些權限包括 (但不限於) 系統管理員或擁有者角色。 自訂角色也可以有寫入權限。

### <a name="subscription-level-disable"></a>訂用帳戶層級的停用
透過[停用主控台 REST API 呼叫](/rest/api/serialconsole/console/disableconsole)，即可針對整個訂用帳戶停用序列主控台。 您可以使用 [API 文件] 頁面上提供的 [試用] 功能，針對訂用帳戶停用和啟用序列主控台。 針對 **subscriptionId** 輸入您的訂用帳戶識別碼，針對 **default** 輸入 "default"，然後選取 [執行]。 Azure CLI 命令尚未提供使用。

![REST API 試用](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

或者，您也可以在 Cloud Shell 中使用下面的一組 Bash 命令，針對訂用帳戶停用、啟用序列主控台，以及檢視該主控台的停用狀態：

* 針對訂用帳戶取得序列主控台的停用狀態：
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* 針對訂用帳戶停用序列主控台：
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* 針對訂用帳戶啟用序列主控台：
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>VM 層級的停用
您可以藉由停用特定 VM 的開機診斷設定，停用該 VM 的序列主控台。 只要從 Azure 入口網站關閉開機診斷，系統就會停用該 VM 的序列主控台。

## <a name="serial-console-security"></a>序列主控台安全性

### <a name="access-security"></a>存取安全性
對序列主控台的存取權，僅限於對虛擬機器具有[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)或更高級別存取角色的使用者。 如果 Azure Active Directory 租用戶需要 Multi-Factor Authentication (MFA)，則存取序列主控台也會需要 MFA，因為序列主控台是透過 [Azure 入口網站](https://portal.azure.com)進行存取。

### <a name="channel-security"></a>通道安全性
在網路上來回傳送的所有資料都會經過加密。

### <a name="audit-logs"></a>稽核記錄
目前對序列主控台進行的所有存取都會記錄在虛擬機器的[開機診斷](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics)記錄中。 這些記錄的存取權均由 Azure 虛擬機器系統管理員所擁有和控制。

>[!CAUTION]
不會記錄主控台的存取密碼。 但是，如果在主控台內執行的命令包含或輸出密碼、祕密、使用者名稱，或任何其他形式的個人識別資訊 (PII)，則這些命令將會寫入至 VM 開機診斷記錄檔。 它們將與所有其他可見文字一起寫入，作為序列主控台回滾函式實作的一部分。 這些記錄是循環的，只有具有診斷儲存體帳戶讀取權限的個人才能存取它們。 但是，我們建議遵循使用遠端桌面的最佳作法，以取得可能涉及祕密和/或 PII 的任何項目。

### <a name="concurrent-usage"></a>並行使用方式
如果在某位使用者已連線至序列主控台的情況下，另一位使用者成功取得該相同虛擬機器的存取權，系統就會將第一位使用者中斷連線，而讓第二位使用者連線至相同的工作階段。

>[!CAUTION]
這表示中斷連線的使用者將不會被登出。強制在中斷連線時登出的功能 (透過使用 SIGHUP 或類似的機制) 仍在規劃中。 就 Windows 而言，在 SAC 中會啟用自動逾時，不過，針對 Linux，您則可以設定終端機逾時設定。

## <a name="accessibility"></a>協助工具
協助工具是 Azure 序列主控台的按鍵焦點。 為此，我們已確保視障和聽障人士以及無法使用滑鼠的使用者都能存取序列主控台。

### <a name="keyboard-navigation"></a>鍵盤導覽
使用鍵盤上的 **Tab** 鍵，在 Azure 入口網站中的序列主控台介面上導覽。 您的位置會在螢幕上醒目顯示。 若要離開序列主控台視窗的焦點，請在鍵盤上按 **Ctrl**+**F6**。

### <a name="use-the-serial-console-with-a-screen-reader"></a>透過螢幕助讀程式使用序列主控台
序列主控台具備內建的螢幕助讀程式支援。 在開啟螢幕助讀程式的情況下瀏覽，可讓螢幕助讀程式大聲讀出目前所選按鈕的替代文字。

## <a name="common-scenarios-for-accessing-the-serial-console"></a>存取序列主控台的常見案例
案例          | 序列主控台中的動作
:------------------|:-----------------------------------------
不正確的防火牆規則 | 存取序列主控台，然後修正 Windows 防火牆規則。
檔案系統損毀/檢查 | 存取序列主控台，然後復原檔案系統。
RDP 設定問題 | 存取序列主控台，然後變更設定。 如需詳細資訊，請參閱 [RDP 文件](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access) \(機器翻譯\)。
網路鎖定系統 | 從 Azure 入口網站存取序列主控台以管理系統。 某些網路命令列在 [Windows 命令：CMD 和 PowerShell](serial-console-cmd-ps-commands.md)。
與開機載入器互動 | 透過序列主控台存取 BCD。 如需資訊，請參閱[啟用序列主控台中的 Windows 開機功能表](#enable-the-windows-boot-menu-in-the-serial-console)。


## <a name="errors"></a>Errors
因為大部分的錯誤都是暫時性的，因此重試您的連線通常可以修正這些錯誤。 下表顯示錯誤清單及緩和措施。

Error                            |   緩和
:---------------------------------|:--------------------------------------------|
無法擷取 *&lt;VMNAME&gt;* 的開機診斷設定。 若要使用序列主控台，請確定已針對此 VM 啟用診斷開機診斷。 | 請確定 VM 已啟用[開機診斷](boot-diagnostics.md)。
VM 處於已停止 (已解除配置) 狀態。 啟動 VM 並重試序列主控台連線。 | 虛擬機器必須處於已啟動狀態，才能存取序列主控台
您沒有可使用此 VM 序列主控台的必要權限。 請確定您至少具有「虛擬機器參與者」角色權限。| 存取序列主控台需要特定權限。 如需詳細資訊，請參閱[必要條件](#prerequisites)。
無法判斷開機診斷儲存體帳戶 *&lt;STORAGEACCOUNTNAME&gt;* 的資源群組。 請確認已針對此 VM 啟用開機診斷，且您具有此儲存體帳戶的存取權。 | 存取序列主控台需要特定權限。 如需詳細資訊，請參閱[必要條件](#prerequisites)。
存取此 VM 的開機診斷儲存體帳戶時，遇到「禁止」回應。 | 請確定開機診斷沒有帳戶防火牆。 必須要有可存取的開機診斷儲存體帳戶，序列主控台才能運作。
Web 通訊端已關閉或無法開啟。 | 您可能需要將 `*.console.azure.com` 列入允許清單。 有一個更詳細但較冗長的方法，就是將 [Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)列入允許清單，而此範圍會經常變更。
連線至 Windows VM 時只有顯示健康情況資訊| 如果您的 Windows 映像未啟用特殊系統管理主控台，則會發生此錯誤。 請參閱[在自訂或舊版的映像中啟用序列主控台](#enable-the-serial-console-in-custom-or-older-images)，以取得在 Windows VM 上手動啟用 SAC 的指示。 如需詳細資訊，請參閱 [Windows 健康情況訊號](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)。

## <a name="known-issues"></a>已知問題
我們已了解序列主控台的一些問題。 以下是這些問題的清單，以及減輕其影響的步驟。

問題                             |   緩和
:---------------------------------|:--------------------------------------------|
在連線橫幅之後按下 **Enter** 鍵並不會顯示登入提示。 | 如需詳細資訊，請參閱[按 Enter 鍵沒有任何作用](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md) \(英文\)。 如果您執行的是自訂 VM、強化設備，或是導致 Windows 無法正確地連線至序列埠的開機組態，則可能發生此錯誤。 如果您執行的是 Windows 10 用戶端 VM，也將發生此錯誤，因為只會將 Windows Server VM 設定為已啟用 EMS。
在核心偵錯啟用時，無法在出現 SAC 提示時輸入。 | 透過 RDP 連線至 VM，並從更高權限的命令提示字元執行 `bcdedit /debug {current} off`。 如果您無法執行 RDP 連線，可以改為將作業系統磁碟連結至另一個 Azure VM，並在連結作為資料磁碟時透過執行 `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` 加以修改，然後再將磁碟交換回來。
在 SAC 中貼到 PowerShell 中時，如果原始內容中有重複字元，就會產生第三個字元。 | 因應措施是執行 `Remove-Module PSReadLine` 從目前的工作階段中卸載 PSReadLine 模組。 此動作不會刪除或解除安裝模組。
某些鍵盤輸入會產生奇怪的 SAC 輸出 (例如 **[A**、**[3~**)。 | SAC 提示字元不支援 [VT100](https://aka.ms/vtsequences) 逸出序列。
貼上長字串沒有作用。 | 序列主控台會將貼上至終端機的字串長度限制為 2048 個字元，以防止多載序列連接埠頻寬。
序列主控台無法與儲存體帳戶防火牆搭配使用。 | 根據設計，序列主控台無法與開機診斷儲存體帳戶上啟用的儲存體帳戶防火牆搭配使用。


## <a name="frequently-asked-questions"></a>常見問題集

**問：如何傳送意見反應？**

A. 在 https://aka.ms/serialconsolefeedback 建立 GitHub 問題來提供意見反應。 或者，您也可以透過 azserialhelp@microsoft.com 或在 http://feedback.azure.com 的虛擬機器類別中傳送意見反應 (較不建議)。

**問：序列主控台是否支援複製/貼上？**

A. 是。 請使用 **Ctrl**+**Shift**+**C** 與 **Ctrl**+**Shift**+**V** 來針對終端機進行複製及貼上。

**問：誰可以針對我的訂用帳戶啟用或停用序列主控台？**

A. 若要以訂用帳戶層級的規模啟用或停用序列主控台，您必須有該訂用帳戶的寫入權限。 具有寫入權限的角色包括系統管理員或擁有者角色。 自訂角色也可以有寫入權限。

**問：誰可以存取我 VM 的序列主控台？**

A. 您必須具有 VM 的虛擬機器參與者角色或更高級別的角色，才能存取 VM 的序列主控台。

**問：我的序列主控台沒有顯示任何內容，該怎麼辦？**

A. 您映像的序列主控台存取設定很有可能是錯誤的。 如需有關設定您的映像以啟用序列主控台的詳細資訊，請參閱[在自訂或舊版的映像中啟用序列主控台](#enable-the-serial-console-in-custom-or-older-images)。

**問：序列主控台是否適用於虛擬機器擴展集？**

A. 目前尚不支援存取虛擬機器擴展集執行個體的序列主控台。

## <a name="next-steps"></a>後續步驟
* 如需您可以在 Windows SAC 中使用之 CMD 及 PowerShell 命令的深入指南，請參閱 [Windows 命令：CMD 和 PowerShell](serial-console-cmd-ps-commands.md)。
* 序列主控台也適用於 [Linux](serial-console-linux.md) VM。
* 深入了解[開機診斷](boot-diagnostics.md)。
