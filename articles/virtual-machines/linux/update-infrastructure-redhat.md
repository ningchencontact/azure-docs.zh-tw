---
title: Red Hat Update Infrastructure | Microsoft Docs
description: 了解適用於 Microsoft Azure 中隨選 Red Hat Enterprise Linux 執行個體的 Red Hat Update Infrastructure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: efc76616151776bc2f766f92ff9503413c6037d0
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774275"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>適用於 Azure 中隨選 Red Hat Enterprise Linux VM 的 Red Hat Update Infrastructure
 [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) 允許雲端提供者 (例如 Azure) 鏡像 Red Hat 代管的存放庫內容、建立具有 Azure 特定內容的自訂存放庫，以及讓它可供使用者 VM 使用。

Red Hat Enterprise Linux (RHEL) 隨用隨付 (PAYG) 映像預先設定為存取 Azure RHUI。 不需要任何其他設定。 若要取得最新的更新，請在 RHEL 執行個體備妥之後執行 `sudo yum update`。 此服務包含在 RHEL PAYG 軟體費用中。

有關 Azure 中 RHEL 映像的其他資訊 (包括發佈和保留原則)，可在[這裡](./rhel-images.md)取得。

如需所有 RHEL 版本的 Red Hat 支援原則資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)頁面。

## <a name="important-information-about-azure-rhui"></a>Azure RHUI 的重要資訊
* Azure RHUI 是更新基礎結構, 可支援在 Azure 中建立的所有 RHEL PAYG Vm。 這不會讓您無法使用訂用帳戶管理員或附屬或其他更新來源註冊 PAYG RHEL Vm, 但使用 PAYG VM 時, 將會產生間接的雙重計費。 請參閱下列重點以取得詳細資料。
* 對 Azure 代管之 RHUI 的存取，包含在 RHEL PAYG 映像價格中。 如果您將 PAYG RHEL VM 從 Azure 代管的 RHUI 取消註冊，這樣並不會將虛擬機器轉換成自備授權 (BYOL) 類型的虛擬機器。 如果您以另一個更新來源註冊相同的 VM，可能會產生「間接」雙重費用。 您需要支付 Azure RHEL 軟體費用， 而且還需要支付先前已購買的 Red Hat 訂用帳戶費用。 如果您持續需要使用非 Azure 託管 RHUI 的更新基礎結構, 請考慮註冊以使用[RHEL BYOS 映射](https://aka.ms/rhel-byos)。
* RHUI 的預設行為是在您執行`sudo yum update`時, 將 RHEL VM 升級至最新的次要版本。

    例如，如果您從 RHEL 7.4 PAYG 映像佈建 VM 並執行 `sudo yum update`，最終將會得到 RHEL 7.6 VM (RHEL7 系列中的最新次要版本)。

    若要避免此行為, 您可以切換至[擴充更新支援通道](#rhel-eus-and-version-locking-rhel-vms), 或建立您自己的映射, 如[建立及上傳適用于 Azure 的 Red Hat 型虛擬機器一](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)文所述。 如果您建立自己的映射, 您需要將它連線至不同的更新基礎結構 ([直接到 Red hat 內容傳遞伺服器](https://access.redhat.com/solutions/253273)或[red hat 衛星 server](https://access.redhat.com/products/red-hat-satellite))。



* Azure 中 RHEL PAYG 映像 (RHEL for SAP、RHEL for SAP HANA 和 RHEL for SAP Business Applications) 會連線至專用 RHUI 通道，以維持在 SAP 認證所需的特定 RHEL 次要版本。

* 只有 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653) \(英文\) 中的 VM 能夠存取 Azure 代管的 RHUI。 如果您透過內部部署網路基礎結構為所有 VM 流量設定 Proxy，則可能需要為 RHEL PAYG VM 設定使用者定義的路由，以便存取 Azure RHUI。

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS 和版本鎖定的 RHEL VM
有些客戶可能想要將其 RHEL 虛擬機器鎖定為特定 RHEL 次要版本。 您可以藉由將存放庫更新為指向「擴充更新支援」存放庫，將 RHEL 虛擬機器的版本鎖定為特定次要版本。 您也可以復原 EUS 版本鎖定作業。

>[!NOTE]
> RHEL 額外專案不支援 EUS。 這表示如果您要安裝的套件通常可從 RHEL 額外通道取得, 則在 EUS 時將無法這麼做。 Red Hat 額外產品生命週期詳述于[這裡](https://access.redhat.com/support/policy/updates/extras/)。

在撰寫本文時, RHEL < = 7.3 的 EUS 支援已結束。 如需詳細資訊, 請參閱[Red Hat 檔](https://access.redhat.com/support/policy/updates/errata/)中的「Red Hat Enterprise Linux 更長的支援附加元件」一節。
* RHEL 7.4 EUS 支援于2019年8月31日結束
* RHEL 7.5 EUS 支援于2020年4月30日結束
* RHEL 7.6 EUS 支援于2020年10月31日結束

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>將 RHEL VM 切換至 EUS (版本鎖定至特定次要版本)
使用下列指示, 將 RHEL VM 鎖定至特定次要版本 (以 root 身分執行):

>[!NOTE]
> 這僅適用於有 EUS 可供使用的 RHEL 版本。 撰寫本文時，這包括 RHEL 7.2-7.6。 如需更多詳細資料，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata) \(英文\) 頁面。

1. 停用非 EUS 存放庫：
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. 新增 EUS 存放庫：
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. 鎖定 releasever 變數 (以 root 身分執行):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > 上述指示會將 RHEL 次要版本鎖定為目前次要版本。 如果您要升級並鎖定到不是最新的次要版本，請輸入特定次要版本。 例如，`echo 7.5 > /etc/yum/vars/releasever` 會將您的 RHEL 版本鎖定為 RHEL 7.5

1. 更新您的 RHEL 虛擬機器
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>將 RHEL VM 切換回非 EUS (移除版本鎖定)
以 root 身分執行下列內容:
1. 移除 releasever 檔案:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. 停用 EUS 存放庫:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. 設定 RHEL VM
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```
    
1. 更新您的 RHEL 虛擬機器
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>RHUI 內容傳遞伺服器的 IP

在所有可使用 RHEL 隨選映像的地區，皆可使用 RHUI。 目前包含 [Azure 狀態儀表板](https://azure.microsoft.com/status/)頁面上所列的所有公用區域、Azure 美國政府和 Microsoft Azure 德國區域。

如果您使用網路組態來進一步限制來自 RHEL PAYG VM 的存取，請確定已允許下列 IP，如此 `yum update` 才能依據您所在的環境運作：


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="azure-rhui-infrastructure"></a>Azure RHUI 基礎結構


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>更新虛擬機器上已過期的 RHUI 用戶端憑證

如果您使用的是較舊的 RHEL VM 映像，例如 RHEL 7.4 (映像 URN：`RedHat:RHEL:7.4:7.4.2018010506`)，因為 SSL 用戶端憑證已過期，所以會碰到對 RHUI 的連線問題。 您會看到如下錯誤：「SSL 同儕節點拒絕了您已過期的憑證」或「錯誤:無法擷取存放庫的存放庫中繼資料 (repomd.xml): ...請確認其路徑正確，然後再試一次」_ 。 若要解決這個問題，請使用下列命令來更新虛擬機器上的 RHUI 用戶端套件：

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

或者，也可以執行 `sudo yum update`，如此也會更新用戶端憑證套件 (端視您的 RHEL 版本而定)，只是會看到其他存放庫顯示「SSL 憑證已過期」錯誤。 如果更新成功，其他 RHUI 存放庫應該會恢復正常連線，以便您可以順利執行 `sudo yum update`。

如果您在執行時發生`yum update`404 錯誤, 請嘗試下列動作以重新整理您的 yum 快取:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>針對 Azure RHUI 連線問題進行疑難排解
從 Azure RHEL PAYG VM 連線至 RHUI 時若發生問題，請依照下列步驟操作：

1. 檢查 Azure RHUI 端點的 VM 組態：

    1. 檢查 `/etc/yum.repos.d/rh-cloud.repo` 檔案 `[rhui-microsoft-azure-rhel*]` 區段的 `baseurl` 中是否包含對 `rhui-[1-3].microsoft.com` 的參考。 如果有此參考，您使用的便是新版 Azure RHUI。

    1. 如果它指向具有 `mirrorlist.*cds[1-4].cloudapp.net` 模式的位置，則需要進行組態更新。 您正在使用舊版 VM 快照集，請更新它以指向新版 Azure RHUI。

1. 只有 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653) \(英文\) 中的 VM 能夠存取 Azure 代管的 RHUI。

1. 如果您使用的是新設定，且已驗證 VM 是從 Azure IP 範圍連線，但仍無法連接至 Azure RHUI，請向 Microsoft 或 Red Hat 提出支援案例。

### <a name="infrastructure-update"></a>基礎結構更新

在 2016 年 9 月時，我們部署了更新的 Azure RHUI。 在 2017 年 4 月，我們關閉了舊版 Azure RHUI。 如果您在 2016 年 9 月或之後的時間使用 RHEL PAYG 映像 (或其快照集)，便會自動連線至新的 Azure RHUI。 不過，如果您的 VM 上有舊版快照集，則需要如下節所述手動更新其設定，才能存取 Azure RHUI。

新的 Azure RHUI 伺服器會透過 [Azure 流量管理員](https://azure.microsoft.com/services/traffic-manager/)來部署。 在流量管理員中，不論所在區域為何，任何 VM 皆可使用單一端點 (rhui-1.microsoft.com)。

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>使用 Azure RHUI 伺服器的手動更新程序
此程序僅供參考。 RHEL PAYG 映像已設定成能夠連線至 Azure RHUI。 若要手動更新設定以使用 Azure RHUI 伺服器，請完成下列步驟：

- RHEL 6：
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- RHEL 7：
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

## <a name="next-steps"></a>後續步驟
* 若要從 Azure Marketplace PAYG 映像建立 Red Hat Enterprise Linux VM 並使用 Azure 代管的 RHUI，請移至 [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/)。
* 若要深入了解 Azure 中的 Red Hat 映像，請移至[文件頁面](./rhel-images.md)。
* 如需所有 RHEL 版本的 Red Hat 支援原則資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)頁面。
