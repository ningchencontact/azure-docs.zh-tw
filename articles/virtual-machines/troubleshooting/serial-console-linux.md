---
title: 適用于 Linux 的 Azure 序列主控台 |Microsoft Docs
description: Azure 虛擬機器和虛擬機器擴展集的雙向序列主控台。
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 4e0c91096d5efdcc9639a7127126d8e4b89ef068
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090150"
---
# <a name="azure-serial-console-for-linux"></a>適用于 Linux 的 Azure 序列主控台

Azure 入口網站中的序列主控台可讓您存取 Linux 虛擬機器 (Vm) 和虛擬機器擴展集實例的文字型主控台。 此序列連線會連線到 VM 或虛擬機器擴展集實例的 ttys0 序列埠, 以提供與網路或作業系統狀態無關的存取權。 您只能使用 Azure 入口網站來存取序列主控台, 而且只允許對 VM 或虛擬機器擴展集具有「參與者」或更高版本存取角色的使用者。

序列主控台的運作方式與 Vm 和虛擬機器擴展集實例相同。 在本檔中, 除非另有指示, 否則所有提及的 Vm 都會隱含地包含虛擬機器擴展集實例。

如需 Windows 的序列主控台檔, 請參閱[windows 的序列主控台](../windows/serial-console.md)。

> [!NOTE]
> 序列主控台已在全球 Azure 區域中正式推出。 目前尚未在 Azure Government 或「Azure 中國」雲端中提供序列主控台。


## <a name="prerequisites"></a>必要條件

- 您的 VM 或虛擬機器擴展集實例必須使用資源管理部署模型。 不支援傳統部署。

- 使用序列主控台的帳戶必須具有 VM 的[虛擬機器參與者角色](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)和[開機診斷](boot-diagnostics.md)儲存體帳戶

- 您的 VM 或虛擬機器擴展集實例必須有以密碼為基礎的使用者。 您可以使用 VM 存取擴充的[重設密碼](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password)功能來建立一個帳戶。 然後，選取 [支援與疑難排解] 區段中的 [重設密碼]。

- 您的 VM 或虛擬機器擴展集實例必須啟用[開機診斷](boot-diagnostics.md)。

    ![開機診斷設定](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- 如需有關 Linux 發行版本的特定設定，請參閱[序列主控台 Linux 發行版本可用性](#serial-console-linux-distribution-availability)。

- 您的 VM 或虛擬機器擴展集實例必須設定為的序列輸出`ttys0`。 這是 Azure 映射的預設值, 但您會想要在自訂映射上再次檢查。 詳細資料[如下](#custom-linux-images)。


## <a name="get-started-with-the-serial-console"></a>開始使用序列主控台
Vm 和虛擬機器擴展集的序列主控台只能透過 Azure 入口網站進行存取:

### <a name="serial-console-for-virtual-machines"></a>虛擬機器的序列主控台
Vm 的序列主控台非常簡單, 只要在 Azure 入口網站的 [**支援 + 疑難排解**] 區段中按一下**序列主控台**即可。
  1. 開啟 [Azure 入口網站](https://portal.azure.com)。

  1. 流覽至 [**所有資源**], 然後選取虛擬機器。 將會開啟該 VM 的概觀頁面。

  1. 向下捲動至 [支援與疑難排解] 區段，然後選取 [序列主控台]。 這會開啟含有序列主控台的新窗格，並開始連線。

     ![Linux 序列主控台視窗](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>虛擬機器擴展集的序列主控台
序列主控台適用于虛擬機器擴展集的每個實例。 您必須先流覽至虛擬機器擴展集的個別實例, 才會看到 [**序列主控台**] 按鈕。 如果您的虛擬機器擴展集未啟用開機診斷, 請務必更新您的虛擬機器擴展集模型以啟用開機診斷, 然後將所有實例升級至新的模型, 才能存取序列主控台。
  1. 開啟 [Azure 入口網站](https://portal.azure.com)。

  1. 流覽至 [**所有資源**], 然後選取虛擬機器擴展集。 虛擬機器擴展集的 [總覽] 頁面隨即開啟。

  1. 流覽至**實例**

  1. 選取虛擬機器擴展集實例

  1. 從 [**支援 + 疑難排解**] 區段中, 選取 [**序列主控台**]。 這會開啟含有序列主控台的新窗格，並開始連線。

     ![Linux 虛擬機器擴展集序列主控台](./media/virtual-machines-serial-console/vmss-start-console.gif)


> [!NOTE]
> 序列主控台需要一個已設定密碼的本機使用者。 只使用 SSH 公開金鑰設定的 Vm 或虛擬機器擴展集, 將無法登入序列主控台。 若要建立具有密碼的本機使用者，請使用 [VM 存取擴充功能](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) (可透過在入口網站中選取 [重設密碼] 來使用此功能)，然後建立具有密碼的本機使用者。
> 您也可以[使用 GRUB 來開機進入單一使用者模式](./serial-console-grub-single-user-mode.md)，以重設帳戶中的系統管理員密碼。

## <a name="serial-console-linux-distribution-availability"></a>序列主控台 Linux 發佈可用性
若要讓序列主控台正確運作，必須設定讓客體作業系統將主控台訊息讀取並寫入至序列連接埠。 大多數[已背書的 Azure Linux 發行版本](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)都已預設設定序列主控台。 在 Azure 入口網站的 [支援與疑難排解] 區段中選取 [序列主控台]，可以存取序列主控台。

> [!NOTE]
> 如果您在序列主控台中沒有看到任何項目，請確定您的 VM 上已啟用開機診斷。 按**Enter**通常會修正序列主控台中沒有顯示任何內容的問題。

發佈      | 序列主控台存取
:-----------|:---------------------
Red Hat Enterprise Linux    | 預設啟用的序列主控台存取。
CentOS      | 預設啟用的序列主控台存取。
Ubuntu      | 預設啟用的序列主控台存取。
CoreOS      | 預設啟用的序列主控台存取。
SUSE        | Azure 上提供的較新 SLES 映像已預設啟用序列主控台存取。 如果您使用 Azure 上較舊的 SLES 版本 (10 或更舊)，請依照這篇 [KB 文章](https://www.novell.com/support/kb/doc.php?id=3456486) \(英文\) 來啟用序列主控台。
Oracle Linux        | 預設啟用的序列主控台存取。

### <a name="custom-linux-images"></a>自訂 Linux 映像
若要啟用自訂 Linux VM 映像的序列主控台，請在 */etc/inittab* 檔案中啟用主控台存取以在 `ttyS0` 上執行終端機。 例如： `S0:12345:respawn:/sbin/agetty -L 115200 console vt102` 。

您也會想要新增 ttys0 做為序列輸出的目的地。 如需有關設定自訂映射以使用序列主控台的詳細資訊, 請參閱在[Azure 中建立及上傳 LINUX VHD](https://aka.ms/createuploadvhd#general-linux-system-requirements)的一般系統需求。

如果您正在建置自訂核心，則可以考慮啟用這些核心旗標：`CONFIG_SERIAL_8250=y` 與 `CONFIG_MAGIC_SYSRQ_SERIAL=y`。 組態檔通常位於 */boot/* 路徑中。

## <a name="common-scenarios-for-accessing-the-serial-console"></a>存取序列主控台的常見案例

狀況          | 序列主控台中的動作
:------------------|:-----------------------------------------
損毀的 *FSTAB* 檔案 | 按下 **Enter** 鍵以繼續，並使用文字編輯器來修正 *FSTAB* 檔案。 您可能必須在單一使用者模式下，才能進行此操作。 如需詳細資訊, 請參閱[如何修正 fstab 問題](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)和[使用序列主控台來存取 GRUB 與單一使用者模式](serial-console-grub-single-user-mode.md)的序列主控台一節。
不正確的防火牆規則 |  如果您已將 iptables 設定為封鎖 SSH 連線, 您可以使用序列主控台來與您的 VM 互動, 而不需要 SSH。 如需更多詳細資料, 請參閱[iptables man 頁面](https://linux.die.net/man/8/iptables)。<br>同樣地, 如果您的 firewalld 封鎖了 SSH 存取, 您可以透過序列主控台存取 VM, 然後重新設定 firewalld。 您可以在[firewalld 檔](https://firewalld.org/documentation/)中找到更多詳細資料。
檔案系統損毀/檢查 | 請參閱 Azure Linux VM 的序列主控台區段[因為檔案系統錯誤而無法啟動](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck), 如需使用序列主控台疑難排解損毀檔案系統的詳細資訊。
SSH 設定問題 | 存取序列主控台，然後變更設定。 無論 VM 的 SSH 設定為何, 都可以使用序列主控台, 因為它不需要 VM 讓網路連線正常運作。 針對[Azure LINUX VM 失敗、發生錯誤或遭到拒絕的 SSH 連線進行疑難排解時,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection)可取得疑難排解指南。 如需更多詳細資料, 請前往[在 Azure 中連線到 LINUX VM 問題的詳細 SSH 疑難排解步驟](./detailed-troubleshoot-ssh-connection.md)
與開機載入器互動 | 從序列主控台刀鋒視窗內重新啟動您的 VM，以存取 Linux VM 上的 GRUB。 如需更多詳細資料和散發版本特定的資訊, 請參閱[使用序列主控台存取 GRUB 和單一使用者模式](serial-console-grub-single-user-mode.md)。

## <a name="disable-the-serial-console"></a>停用序列主控台
根據預設, 所有訂用帳戶都已啟用序列主控台存取。 您可以在訂用帳戶層級或 VM/虛擬機器擴展集層級停用序列主控台。 請注意, 您必須在 VM 上啟用開機診斷, 序列主控台才能正常執行。

### <a name="vmvirtual-machine-scale-set-level-disable"></a>VM/虛擬機器擴展集層級停用
您可以停用 [開機診斷] 設定, 針對特定的 VM 或虛擬機器擴展集停用序列主控台。 關閉 Azure 入口網站的開機診斷, 以停用 VM 或虛擬機器擴展集的序列主控台。 如果您在虛擬機器擴展集上使用序列主控台, 請務必將您的虛擬機器擴展集實例升級為最新的模型。

> [!NOTE]
> 若要啟用或停用訂用帳戶的序列主控台，您必須有該訂用帳戶的寫入權限。 這些權限包括系統管理員或擁有者角色。 自訂角色也可以有寫入權限。

### <a name="subscription-level-disable"></a>訂用帳戶層級的停用
透過[停用主控台 REST API 呼叫](/rest/api/serialconsole/console/disableconsole)，即可針對整個訂用帳戶停用序列主控台。 此動作需要訂用帳戶的參與者層級存取權或更新版本。 您可以使用 [API 文件] 頁面上提供的 [試用] 功能，針對訂用帳戶停用和啟用序列主控台。 針對 **subscriptionId** 輸入您的訂用帳戶識別碼，針對 **default** 輸入 **default**，然後選取 [執行]。 Azure CLI 命令尚未提供使用。

若要重新啟用訂用帳戶的序列主控台, 請使用 [[啟用主控台] REST API 呼叫](/rest/api/serialconsole/console/enableconsole)。

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
> 這表示中斷連線的使用者將不會被登出。在進行中斷連線時強制登出的功能 (使用 SIGHUP 或類似的機制) 仍在藍圖中。 就 Windows 而言，在特殊系統管理主控台 (SAC) 中會啟用自動逾時，不過，針對 Linux，您則可以設定終端機逾時設定。 為此，請在您用以登入主控台之使用者的 *.bash_profile* 或 *.profile* 檔案中新增 `export TMOUT=600`。 此設定將在 10 分鐘後逾時。

## <a name="accessibility"></a>協助工具選項
協助工具是 Azure 序列主控台的主要焦點。 為此，我們已確認序列主控台完全可供存取。

### <a name="keyboard-navigation"></a>鍵盤導覽
使用鍵盤上的 **Tab** 鍵，在 Azure 入口網站中的序列主控台介面上導覽。 您的位置會在螢幕上醒目顯示。 若要離開序列主控台視窗的焦點，請在鍵盤上按 **Ctrl**+**F6**。

### <a name="use-serial-console-with-a-screen-reader"></a>使用序列主控台與螢幕閱讀程式
序列主控台具備內建的螢幕助讀程式支援。 在開啟螢幕助讀程式的情況下瀏覽，可讓螢幕助讀程式大聲讀出目前所選按鈕的替代文字。

## <a name="errors"></a>錯誤
因為大部分的錯誤都是暫時性的，因此重試您的連線通常可以修正這些錯誤。 下表顯示錯誤清單及緩和措施。 這些錯誤和緩和措施適用于 Vm 和虛擬機器擴展集實例。

Error                            |   風險降低
:---------------------------------|:--------------------------------------------|
無法擷取 *&lt;VMNAME&gt;* 的開機診斷設定。 若要使用序列主控台，請確定已針對此 VM 啟用診斷開機診斷。 | 請確定 VM 已啟用[開機診斷](boot-diagnostics.md)。
VM 處於已停止 (已解除配置) 狀態。 啟動 VM 並重試序列主控台連線。 | VM 必須處於已啟動狀態，才能存取序列主控台。
您沒有可使用此 VM 與序列主控台的必要權限。 請確定您至少具有「虛擬機器參與者」角色權限。| 存取序列主控台需要特定權限。 如需詳細資訊，請參閱[必要條件](#prerequisites)。
無法判斷開機診斷儲存體帳戶 *&lt;STORAGEACCOUNTNAME&gt;* 的資源群組。 請確認已針對此 VM 啟用開機診斷，且您具有此儲存體帳戶的存取權。 | 存取序列主控台需要特定權限。 如需詳細資訊，請參閱[必要條件](#prerequisites)。
Web 通訊端已關閉或無法開啟。 | 您可能需要將 `*.console.azure.com` 列入允許清單。 有一個更詳細但較冗長的方法，就是將 [Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)列入允許清單，而此範圍會經常變更。
存取此 VM 的開機診斷儲存體帳戶時，遇到「禁止」回應。 | 請確定開機診斷沒有帳戶防火牆。 必須要有可存取的開機診斷儲存體帳戶，序列主控台才能運作。

## <a name="known-issues"></a>已知問題
我們已了解序列主控台的一些問題。 以下是這些問題的清單，以及減輕其影響的步驟。 這些問題和緩和措施適用于 Vm 和虛擬機器擴展集實例。

問題                           |   風險降低
:---------------------------------|:--------------------------------------------|
在連線橫幅之後按下 **Enter** 鍵並不會顯示登入提示。 | 如需詳細資訊，請參閱[按 Enter 鍵沒有任何作用](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md) \(英文\)。 如果您執行的是自訂 VM、強化設備, 或是導致 Linux 無法連線到序列埠的 GRUB 設定, 就可能會發生此問題。
序列主控台文字只會佔用部分的螢幕大小 (通常在使用文字編輯器之後)。 | 序列主控台不支援對視窗大小進行交涉 ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt))，這表示系統將不會傳送 SIGWINCH 訊號以更新螢幕大小，而且 VM 將無法得知您終端機的大小。 安裝 xterm 或類似的公用程式，以向您提供 `resize` 命令，然後再執行 `resize`。
貼上長字串沒有作用。 | 序列主控台會將貼上至終端機的字串長度限制為 2048 個字元，以防止多載序列連接埠頻寬。
序列主控台無法與儲存體帳戶防火牆搭配使用。 | 根據設計，序列主控台無法與開機診斷儲存體帳戶上啟用的儲存體帳戶防火牆搭配使用。
序列主控台無法搭配使用具有階層命名空間之 Azure Data Lake Storage Gen2 的儲存體帳戶使用。 | 這是階層命名空間的已知問題。 若要減輕問題, 請確定您的 VM 開機診斷儲存體帳戶不是使用 Azure Data Lake Storage Gen2 建立的。 只有在建立儲存體帳戶時, 才能設定此選項。 您可能必須在未啟用 Azure Data Lake Storage Gen2 的情況下, 建立個別的開機診斷儲存體帳戶來減輕此問題。
SLES BYOS 映射中的鍵盤輸入不穩定。 鍵盤輸入只會偶爾被辨識。 | 這是 Plymouth 套件的問題。 Plymouth 不應該在 Azure 中執行, 因為您不需要啟動顯示畫面, 而 Plymouth 會干擾平臺使用序列主控台的能力。 移除 Plymouth `sudo zypper remove plymouth` , 然後重新開機。 或者, 在行尾附加`plymouth.enable=0` , 以修改 GRUB 設定的核心行。 若要這麼做, 您可以[在開機時編輯開機專案](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles), 或編輯中`/etc/default/grub`的 GRUB_CMDLINE_LINUX 行`grub2-mkconfig -o /boot/grub2/grub.cfg`, 以重建 GRUB, 然後重新開機。


## <a name="frequently-asked-questions"></a>常見問題集

**問：如何傳送意見反應？**

A. 在 https://aka.ms/serialconsolefeedback 建立 GitHub 問題來提供意見反應。 或者，您也可以透過 azserialhelp@microsoft.com 或在 https://feedback.azure.com 的虛擬機器類別中傳送意見反應 (較不建議)。

**問：序列主控台是否支援複製/貼上？**

A. 是的。 請使用 **Ctrl**+**Shift**+**C** 與 **Ctrl**+**Shift**+**V** 來針對終端機進行複製及貼上。

**問：我是否可以使用序列主控台，而不使用 SSH 連線？**

A. 雖然這在技術上應該是可行的，但序列主控台主要是要作為無法透過 SSH 進行連線時的疑難排解工具。 基於以下兩個原因，建議您不要以序列主控台取代 SSH：

- 序列主控台沒有如 SSH 一樣多的頻寬。 因為它是純文字的連線，所以很難進行更多 GUI 大量互動。
- 目前，只有透過使用使用者名稱和密碼才能存取序列主控台。 因為 SSH 金鑰的安全性遠高於使用者名稱/密碼的組合，因此就登入安全性的觀點而言，我們建議 SSH 應比序列主控台優先使用。

**問：誰可以針對我的訂用帳戶啟用或停用序列主控台？**

A. 若要以訂用帳戶層級的規模啟用或停用序列主控台，您必須有該訂用帳戶的寫入權限。 具有寫入權限的角色包括系統管理員或擁有者角色。 自訂角色也可以有寫入權限。

**問：誰可以存取我的 VM/虛擬機器擴展集的序列主控台？**

A. 您必須擁有 VM 或虛擬機器擴展集的虛擬機器參與者角色或更高的許可權, 才能存取序列主控台。

**問：我的序列主控台沒有顯示任何內容，該怎麼辦？**

A. 您映像的序列主控台存取設定很有可能是錯誤的。 如需有關設定您的映像以啟用序列主控台的詳細資訊，請參閱[序列主控台 Linux 發行版本可用性](#serial-console-linux-distribution-availability)。

**問：序列主控台是否適用於虛擬機器擴展集？**

A. 是的，它是！ 請參閱[序列主控台以取得虛擬機器擴展集](#serial-console-for-virtual-machine-scale-sets)

**問：如果我只使用 SSH 金鑰驗證來設定 VM 或虛擬機器擴展集, 仍然可以使用序列主控台來連線到我的 VM/虛擬機器擴展集實例嗎？**

A. 是的。 因為序列主控台並不需要 SSH 金鑰，因此您只需要設定使用者名稱/密碼組合就可以。 您可以透過在 Azure 入口網站中選取 [重設密碼] 並使用這些認證登入序列主控台來執行此操作。

## <a name="next-steps"></a>後續步驟
* 使用序列主控台來[存取 GRUB 與單一使用者模式](serial-console-grub-single-user-mode.md)。
* 使用序列主控台來進行 [NMI 和 SysRq 呼叫](serial-console-nmi-sysrq.md)。
* 了解如何使用序列主控台[在各種散發版本中啟用 GRUB](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)。
* 序列主控台也適用於 [Windows VM](../windows/serial-console.md)。
* 深入了解[開機診斷](boot-diagnostics.md)。

