---
title: 存取 Avere vFXT 控制台 - Azure
description: 如何連線到 vFXT 叢集和瀏覽器型 Avere 控制台以設定 Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: f989f4d103efecf2b6e206287dd8b7b300a1796d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57856836"
---
# <a name="access-the-vfxt-cluster"></a>存取 vFXT 叢集

若要變更設定並監視 Avere vFXT 叢集，請使用 Avere 控制台。 Avere 控制台是叢集的瀏覽器型圖形化介面。

vFXT 叢集位於私人虛擬網路內，因此您必須建立 SSH 通道，或使用其他方法來連線叢集的管理 IP 位址。 有兩個基本步驟： 

1. 建立工作站與私人 Vnet 之間的連線 
1. 在網頁瀏覽器中載入叢集的控制台 

> [!NOTE] 
> 本文假設您已經在叢集中控制器上，或在叢集虛擬網路內的另一個 VM 上，設定公用 IP 位址。 本文說明如何使用該 VM 作為主機來存取叢集。 如果您使用 VPN 或 ExpressRoute 存取 Vnet，請跳至[連線到 Avere 控制台](#connect-to-the-avere-control-panel-in-a-browser)。

連線之前，請確定本機電腦上已安裝建立叢集控制器時所使用的 SSH 公開/私密金鑰組。 如果您需要協助，請閱讀 [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) 或 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) 適用的 SSH 金鑰文件。 (如果您使用密碼而非公開金鑰，在連線時系統將會提示您輸入密碼。) 

## <a name="ssh-tunnel-with-a-linux-host"></a>使用 Linux 主機的 SSH 通道

如果使用 Linux 型用戶端，請使用這種形式的 SSH 通道命令： 

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

此命令會透過叢集控制器的 IP 位址，連線到叢集的管理 IP 位址。

範例：

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

如果您使用 SSH 公開金鑰建立叢集，而且用戶端系統上已安裝相符的金鑰，便會自動驗證。 如果您使用密碼，系統將會提示您輸入密碼。

## <a name="ssh-tunnel-with-a-windows-host"></a>使用 Windows 主機的 SSH 通道

此範例會使用一般 Windows 型終端機公用程式 (PuTTY)。

請在 PuTTY **hostname** 欄位中填入叢集控制器的使用者名稱及其 IP 位址：*your_username*\@*controller_public_IP*。

範例： ``azureuser@203.0.113.51``

在 [設定] 面板中：

1. 展開左側的 [連線] > [SSH]。 
1. 按一下 [通道]。 
1. 輸入來源連接埠，例如 8443。 
1. 在目的地中，輸入 vFXT 叢集的管理 IP 位址和連接埠 443。 
   範例： ``203.0.113.51:443``
1. 按一下 [新增] 。
1. 按一下 [開啟] 。

![顯示按一下哪裡可新增通道的 Putty 應用程式螢幕擷取畫面](media/avere-vfxt-ptty-numbered.png)

如果您使用 SSH 公開金鑰建立叢集，而且用戶端系統上已安裝相符的金鑰，便會自動驗證。 如果您使用密碼，系統將會提示您輸入密碼。

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>在瀏覽器中，連線到 Avere 控制台

此步驟使用網頁瀏覽器連線到 vFXT 叢集上執行的設定公用程式。

* 針對 SSH 通道連線，開啟網頁瀏覽器並巡覽至 `https://127.0.0.1:8443` 。 

  您在建立通道時連線到叢集 IP 位址，因此您只需要在瀏覽器中使用 localhost IP 位址。 如果您使用 8443 以外的本機連接埠，請改為使用連接埠號碼。

* 如果您使用 VPN 或 ExpressRoute 連至叢集，請在瀏覽器中瀏覽至叢集管理 IP 位址。 範例： ``https://203.0.113.51``

根據您的瀏覽器，您可能需要按一下 [進階]，並確認否安全地前往到該頁面。

輸入您在建立叢集時所提供的使用者名稱 `admin` 和系統管理密碼。

![填入使用者名稱 'admin' 和密碼的 Avere 登入頁面螢幕擷取畫面](media/avere-vfxt-gui-login.png)

按一下 [登入]，或按鍵盤上的 Enter 鍵。

## <a name="next-steps"></a>後續步驟

既然您可以存取叢集，請啟用 [支援](avere-vfxt-enable-support.md)。
