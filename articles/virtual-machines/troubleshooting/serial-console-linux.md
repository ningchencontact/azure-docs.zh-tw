---
title: Azure 適用於 Linux 的序列主控台 |Microsoft Docs
description: Azure 虛擬機器和虛擬機器擴展集的雙向序列主控台。
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: harijay
ms.openlocfilehash: 7019d80c05a1953f4e57f0f42d46588310911791
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141096"
---
# <a name="azure-serial-console-for-linux"></a>Azure 適用於 Linux 的序列主控台

在 Azure 入口網站中的序列主控台提供主控台的存取權以文字為基礎的 Linux 虛擬機器 (Vm) 和虛擬機器擴展集 （虛擬機器擴展集） 執行個體。 這個序列連線連線到 VM 或虛擬機器擴展集執行個體，提供存取權的網路或作業系統狀態無關的 COM1 序列埠。 序列主控台只可以使用 Azure 入口網站存取，只允許擁有的參與者存取權角色的使用者或更高版本以 VM 或虛擬機器擴展集。

序列主控台以相同方式運作的 Vm 和虛擬機器擴展集執行個體。 在此文件中，所有提及的 vm 會以隱含方式都包含虛擬機器擴展集執行個體除非另有指明，否則。

如需 Windows 的序列主控台文件，請參閱 <<c0> [ 序列主控台的 Windows](../windows/serial-console.md)。

> [!NOTE]
> 在全域 Azure 區域中，序列主控台已正式推出。 目前尚未在 Azure Government 或「Azure 中國」雲端中提供序列主控台。


## <a name="prerequisites"></a>必要條件

- 您的 VM 或虛擬機器擴展集執行個體必須使用資源管理部署模型。 不支援傳統部署。

- 您會使用序列主控台的帳戶必須具備[虛擬機器參與者 」 角色](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)vm 並[開機診斷](boot-diagnostics.md)儲存體帳戶

- 您的 VM 或虛擬機器擴展集執行個體必須具有以密碼為基礎的使用者。 您可以使用 VM 存取擴充的[重設密碼](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password)功能來建立一個帳戶。 然後，選取 [支援與疑難排解] 區段中的 [重設密碼]。

- 您的 VM 或虛擬機器擴展集執行個體必須具有[開機診斷](boot-diagnostics.md)啟用。

    ![開機診斷設定](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- 如需有關 Linux 發行版本的特定設定，請參閱[序列主控台 Linux 發行版本可用性](#serial-console-linux-distribution-availability)。



## <a name="get-started-with-the-serial-console"></a>開始使用序列主控台
序列主控台的 Vm 和虛擬機器擴展集是只能透過 Azure 入口網站存取：

### <a name="serial-console-for-virtual-machines"></a>虛擬機器序列主控台
適用於 Vm 的序列主控台就如同按一下這麼直接**序列主控台**內**支援與疑難排解**在 Azure 入口網站中的區段。
  1. 開啟 [Azure 入口網站](https://portal.azure.com)。

  1. 瀏覽至**的所有資源**選取虛擬機器。 將會開啟該 VM 的概觀頁面。

  1. 向下捲動至 [支援與疑難排解] 區段，然後選取 [序列主控台]。 這會開啟含有序列主控台的新窗格，並開始連線。

     ![Linux 的序列主控台視窗](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>虛擬機器擴展集的序列主控台
每個執行個體為基礎的虛擬機器擴展集上使用序列主控台。 您必須瀏覽至虛擬機器擴展集的個別執行個體，才可以看到**序列主控台** 按鈕。 如果您的虛擬機器擴展集並沒有啟用開機診斷，請確定您更新您的虛擬機器擴展集模型，以啟用開機診斷，然後才能存取序列主控台再升級至新的模型的所有執行個體。
  1. 開啟 [Azure 入口網站](https://portal.azure.com)。

  1. 瀏覽至**的所有資源**，然後選取 虛擬機器擴展集。 [概觀] 頁面的虛擬機器擴展集隨即開啟。

  1. 瀏覽至**執行個體**

  1. 選取虛擬機器擴展集執行個體

  1. 從**支援與疑難排解**區段中，選取**序列主控台**。 這會開啟含有序列主控台的新窗格，並開始連線。

     ![Linux 虛擬機器擴展集的序列主控台](./media/virtual-machines-serial-console/vmss-start-console.gif)


> [!NOTE]
> 序列主控台需要一個已設定密碼的本機使用者。 Vm 或只使用 SSH 公用金鑰設定的虛擬機器擴展集將無法登入序列主控台。 若要建立具有密碼的本機使用者，請使用 [VM 存取擴充功能](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) (可透過在入口網站中選取 [重設密碼] 來使用此功能)，然後建立具有密碼的本機使用者。
> 您也可以[使用 GRUB 來開機進入單一使用者模式](./serial-console-grub-single-user-mode.md)，以重設帳戶中的系統管理員密碼。

## <a name="serial-console-linux-distribution-availability"></a>序列主控台 Linux 發佈可用性
若要讓序列主控台正確運作，必須設定讓客體作業系統將主控台訊息讀取並寫入至序列連接埠。 大多數[已背書的 Azure Linux 發行版本](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)都已預設設定序列主控台。 在 Azure 入口網站的 [支援與疑難排解] 區段中選取 [序列主控台]，可以存取序列主控台。

配送映像      | 序列主控台存取
:-----------|:---------------------
Red Hat Enterprise Linux    | 預設啟用的序列主控台存取。
CentOS      | 預設啟用的序列主控台存取。
Ubuntu      | 預設啟用的序列主控台存取。
CoreOS      | 預設啟用的序列主控台存取。
SUSE        | Azure 上提供的較新 SLES 映像已預設啟用序列主控台存取。 如果您使用 Azure 上較舊的 SLES 版本 (10 或更舊)，請依照這篇 [KB 文章](https://www.novell.com/support/kb/doc.php?id=3456486) \(英文\) 來啟用序列主控台。
Oracle Linux        | 預設啟用的序列主控台存取。
自訂 Linux 映像     | 若要啟用自訂 Linux VM 映像的序列主控台，請在 */etc/inittab* 檔案中啟用主控台存取以在 `ttyS0` 上執行終端機。 例如： `S0:12345:respawn:/sbin/agetty -L 115200 console vt102` 。 如需有關如何正確建立自訂映像的詳細資訊，請參閱[在 Azure 中建立及上傳 Linux VHD](https://aka.ms/createuploadvhd)。 如果您正在建置自訂核心，則可以考慮啟用這些核心旗標：`CONFIG_SERIAL_8250=y` 與 `CONFIG_MAGIC_SYSRQ_SERIAL=y`。 組態檔通常位於 */boot/* 路徑中。

> [!NOTE]
> 如果您在序列主控台中沒有看到任何項目，請確定您的 VM 上已啟用開機診斷。 按下**Enter**會經常修正問題，執行任何動作是否出現序列主控台。

## <a name="common-scenarios-for-accessing-the-serial-console"></a>存取序列主控台的常見案例

案例          | 序列主控台中的動作
:------------------|:-----------------------------------------
損毀的 *FSTAB* 檔案 | 按下 **Enter** 鍵以繼續，並使用文字編輯器來修正 *FSTAB* 檔案。 您可能必須在單一使用者模式下，才能進行此操作。 如需詳細資訊，請參閱 > 一節序列主控台[如何修正 fstab 問題](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)並[使用序列主控台存取 GRUB 和單一使用者模式](serial-console-grub-single-user-mode.md)。
不正確的防火牆規則 |  如果您已設定 iptables 來封鎖 SSH 連線，您可以使用序列主控台而不需要 SSH 互動與您的 VM。 更多詳細資料，請參閱[iptables 相關自定義 man 頁面](https://linux.die.net/man/8/iptables)。<br>同樣地，如果您的 firewalld 封鎖 SSH 存取，您可以透過序列主控台存取虛擬機器，並重新設定 firewalld。 更多詳細資料可在[firewalld 文件](https://firewalld.org/documentation/)。
檔案系統損毀/檢查 | 請參閱的序列主控台[因為檔案系統錯誤而無法啟動的 Azure Linux VM](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck)如需詳細的疑難排解步驟的詳細資料已損毀的檔案系統使用序列主控台。
SSH 組態問題 | 存取序列主控台，然後變更設定。 序列主控台可以使用不論 VM 的 SSH 組態，因為它不需要有網路連線能力的 VM。 疑難排解指南將會位於[針對 SSH 連線至 Azure Linux VM 失敗，發生錯誤，或被拒](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection)。 更多詳細資料位於[詳細 SSH 疑難排解步驟，連接到 Azure 中的 Linux VM 的問題](./detailed-troubleshoot-ssh-connection.md)
與開機載入器互動 | 從序列主控台刀鋒視窗內重新啟動您的 VM，以存取 Linux VM 上的 GRUB。 如需詳細資訊和散發版本特有的資訊，請參閱[使用序列主控台存取 GRUB 和單一使用者模式](serial-console-grub-single-user-mode.md)。

## <a name="disable-the-serial-console"></a>停用的序列主控台
根據預設，所有訂用帳戶已啟用的序列主控台存取。 您可以停用訂用帳戶層級或 VM/虛擬機器擴展集層級的序列主控台。 請注意必須在工作順序的序列主控台中的 VM 上已啟用開機診斷。

### <a name="vmvirtual-machine-scale-set-level-disable"></a>VM/虛擬機器擴展集層級停用
可以停用特定 VM 或虛擬機器擴展集藉由停用開機診斷設定的序列主控台。 關閉 Azure 入口網站停用 VM 或虛擬機器擴展集的序列主控台的 開機診斷。 如果您使用序列主控台上的虛擬機器擴展集，請確定您的虛擬機器擴展集執行個體升級至最新的模型。

> [!NOTE]
> 若要啟用或停用訂用帳戶的序列主控台，您必須有該訂用帳戶的寫入權限。 這些權限包括系統管理員或擁有者角色。 自訂角色也可以有寫入權限。

### <a name="subscription-level-disable"></a>訂用帳戶層級的停用
透過[停用主控台 REST API 呼叫](/rest/api/serialconsole/console/disableconsole)，即可針對整個訂用帳戶停用序列主控台。 您可以使用 [API 文件] 頁面上提供的 [試用] 功能，針對訂用帳戶停用和啟用序列主控台。 針對 **subscriptionId** 輸入您的訂用帳戶識別碼，針對 **default** 輸入 **default**，然後選取 [執行]。 Azure CLI 命令尚未提供使用。

![REST API 試用](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

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

## <a name="serial-console-security"></a>序列主控台安全性

### <a name="access-security"></a>存取安全性
對序列主控台的存取權，僅限於對虛擬機器具有[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)或更高級別存取角色的使用者。 如果 Azure Active Directory 租用戶需要 Multi-Factor Authentication (MFA)，則存取序列主控台也會需要 MFA，因為序列主控台是透過 [Azure 入口網站](https://portal.azure.com)進行存取。

### <a name="channel-security"></a>通道安全性
在網路上來回傳送的所有資料都會經過加密。

### <a name="audit-logs"></a>稽核記錄
目前對序列主控台進行的所有存取都會記錄在虛擬機器的[開機診斷](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics)記錄中。 這些記錄的存取權均由 Azure 虛擬機器系統管理員所擁有和控制。

> [!CAUTION]
> 不會記錄主控台的存取密碼。 但是，如果在主控台內執行的命令包含或輸出密碼、祕密、使用者名稱，或任何其他形式的個人識別資訊 (PII)，則這些命令將會寫入至 VM 開機診斷記錄。 它們將與所有其他可見文字一起寫入，作為序列主控台回滾函式實作的一部分。 這些記錄是循環的，只有具有診斷儲存體帳戶讀取權限的個人才能存取它們。 但是，我們建議遵循使用遠端桌面的最佳作法，以取得可能涉及祕密和/或 PII 的任何項目。

### <a name="concurrent-usage"></a>並行使用方式
如果在某位使用者已連線至序列主控台的情況下，另一位使用者成功取得該相同虛擬機器的存取權，系統就會將第一位使用者中斷連線，而讓第二位使用者連線至相同的工作階段。

> [!CAUTION]
> 這表示中斷連線的使用者將不會被登出。（透過 SIGHUP 或類似的機制） 強制在中斷連線時登出的功能仍然正在規劃中。 就 Windows 而言，在特殊系統管理主控台 (SAC) 中會啟用自動逾時，不過，針對 Linux，您則可以設定終端機逾時設定。 為此，請在您用以登入主控台之使用者的 *.bash_profile* 或 *.profile* 檔案中新增 `export TMOUT=600`。 此設定將在 10 分鐘後逾時。

## <a name="accessibility"></a>協助工具
存取範圍是 Azure 的序列主控台的焦點。 為此，我們已確認序列主控台完全可供存取。

### <a name="keyboard-navigation"></a>鍵盤導覽
使用鍵盤上的 **Tab** 鍵，在 Azure 入口網站中的序列主控台介面上導覽。 您的位置會在螢幕上醒目顯示。 若要離開序列主控台視窗的焦點，請在鍵盤上按 **Ctrl**+**F6**。

### <a name="use-serial-console-with-a-screen-reader"></a>螢幕助讀程式搭配使用序列主控台
序列主控台具備內建的螢幕助讀程式支援。 在開啟螢幕助讀程式的情況下瀏覽，可讓螢幕助讀程式大聲讀出目前所選按鈕的替代文字。

## <a name="errors"></a>Errors
因為大部分的錯誤都是暫時性的，因此重試您的連線通常可以修正這些錯誤。 下表顯示錯誤清單及緩和措施。 這些錯誤和緩和措施適用於這兩個 Vm 和虛擬機器擴展集執行個體。

Error                            |   緩和
:---------------------------------|:--------------------------------------------|
無法擷取 *&lt;VMNAME&gt;* 的開機診斷設定。 若要使用序列主控台，請確定已針對此 VM 啟用診斷開機診斷。 | 請確定 VM 已啟用[開機診斷](boot-diagnostics.md)。
VM 處於已停止 (已解除配置) 狀態。 啟動 VM 並重試序列主控台連線。 | VM 必須處於已啟動狀態，才能存取序列主控台。
您沒有可使用此 VM 與序列主控台的必要權限。 請確定您至少具有「虛擬機器參與者」角色權限。| 存取序列主控台需要特定權限。 如需詳細資訊，請參閱[必要條件](#prerequisites)。
無法判斷開機診斷儲存體帳戶 *&lt;STORAGEACCOUNTNAME&gt;* 的資源群組。 請確認已針對此 VM 啟用開機診斷，且您具有此儲存體帳戶的存取權。 | 存取序列主控台需要特定權限。 如需詳細資訊，請參閱[必要條件](#prerequisites)。
Web 通訊端已關閉或無法開啟。 | 您可能需要將 `*.console.azure.com` 列入允許清單。 有一個更詳細但較冗長的方法，就是將 [Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)列入允許清單，而此範圍會經常變更。
存取此 VM 的開機診斷儲存體帳戶時，遇到「禁止」回應。 | 請確定開機診斷沒有帳戶防火牆。 必須要有可存取的開機診斷儲存體帳戶，序列主控台才能運作。

## <a name="known-issues"></a>已知問題
我們已了解序列主控台的一些問題。 以下是這些問題的清單，以及減輕其影響的步驟。 這些問題與緩解方式套用這兩個 Vm 和虛擬機器擴展集執行個體。

問題                           |   緩和
:---------------------------------|:--------------------------------------------|
在連線橫幅之後按下 **Enter** 鍵並不會顯示登入提示。 | 如需詳細資訊，請參閱[按 Enter 鍵沒有任何作用](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md) \(英文\)。 如果您執行自訂的 VM、 強化後的應用裝置或導致無法連線至序列埠的 Linux GRUB 組態，就會發生此問題。
序列主控台文字只會佔用部分的螢幕大小 (通常在使用文字編輯器之後)。 | 序列主控台不支援對視窗大小進行交涉 ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt))，這表示系統將不會傳送 SIGWINCH 訊號以更新螢幕大小，而且 VM 將無法得知您終端機的大小。 安裝 xterm 或類似的公用程式，以向您提供 `resize` 命令，然後再執行 `resize`。
貼上長字串沒有作用。 | 序列主控台會將貼上至終端機的字串長度限制為 2048 個字元，以防止多載序列連接埠頻寬。
序列主控台無法與儲存體帳戶防火牆搭配使用。 | 根據設計，序列主控台無法與開機診斷儲存體帳戶上啟用的儲存體帳戶防火牆搭配使用。


## <a name="frequently-asked-questions"></a>常見問題集

**問：如何傳送意見反應？**

A. 在 https://aka.ms/serialconsolefeedback 建立 GitHub 問題來提供意見反應。 或者，您也可以透過 azserialhelp@microsoft.com 或在 https://feedback.azure.com 的虛擬機器類別中傳送意見反應 (較不建議)。

**問：序列主控台是否支援複製/貼上？**

A. 是。 請使用 **Ctrl**+**Shift**+**C** 與 **Ctrl**+**Shift**+**V** 來針對終端機進行複製及貼上。

**問：我是否可以使用序列主控台，而不使用 SSH 連線？**

A. 雖然這在技術上應該是可行的，但序列主控台主要是要作為無法透過 SSH 進行連線時的疑難排解工具。 基於以下兩個原因，建議您不要以序列主控台取代 SSH：

- 序列主控台沒有如 SSH 一樣多的頻寬。 因為它是純文字的連線，所以很難進行更多 GUI 大量互動。
- 目前，只有透過使用使用者名稱和密碼才能存取序列主控台。 因為 SSH 金鑰的安全性遠高於使用者名稱/密碼的組合，因此就登入安全性的觀點而言，我們建議 SSH 應比序列主控台優先使用。

**問：誰可以針對我的訂用帳戶啟用或停用序列主控台？**

A. 若要以訂用帳戶層級的規模啟用或停用序列主控台，您必須有該訂用帳戶的寫入權限。 具有寫入權限的角色包括系統管理員或擁有者角色。 自訂角色也可以有寫入權限。

**問：誰可以存取序列主控台的 我的 VM/虛擬機器擴展集？**

A. 您必須擁有的虛擬機器參與者 」 角色或更高的 VM 或虛擬機器擴展集來存取序列主控台。

**問：我的序列主控台沒有顯示任何內容，該怎麼辦？**

A. 您映像的序列主控台存取設定很有可能是錯誤的。 如需有關設定您的映像以啟用序列主控台的詳細資訊，請參閱[序列主控台 Linux 發行版本可用性](#serial-console-linux-distribution-availability)。

**問：序列主控台是否適用於虛擬機器擴展集？**

A. 是 ！ 請參閱[虛擬機器擴展集的序列主控台](#serial-console-for-virtual-machine-scale-sets)

**問：如果我設定我的 VM 或虛擬機器擴展集使用 SSH 金鑰驗證時，我仍然可以使用序列主控台連接到我 VM/虛擬機器擴展集執行個體嗎？**

A. 是。 因為序列主控台並不需要 SSH 金鑰，因此您只需要設定使用者名稱/密碼組合就可以。 您可以透過在 Azure 入口網站中選取 [重設密碼] 並使用這些認證登入序列主控台來執行此操作。

## <a name="next-steps"></a>後續步驟
* 使用序列主控台來[存取 GRUB 與單一使用者模式](serial-console-grub-single-user-mode.md)。
* 使用序列主控台來進行 [NMI 和 SysRq 呼叫](serial-console-nmi-sysrq.md)。
* 了解如何使用序列主控台[在各種散發版本中啟用 GRUB](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)。
* 序列主控台也適用於 [Windows VM](../windows/serial-console.md)。
* 深入了解[開機診斷](boot-diagnostics.md)。

