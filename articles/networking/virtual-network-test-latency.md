---
title: 在 Azure 虛擬網路中測試 Azure 虛擬機器的網路延遲 |Microsoft Docs
titleSuffix: Azure Virtual Network latency
description: 瞭解如何在虛擬網路上測試 Azure Vm 之間的網路延遲
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: d5efc1b802246597b4ee545b4d805e4f0d10ebb5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166560"
---
# <a name="testing-network-latency"></a>測試網路延遲

使用針對此工作所設計的工具測量網路延遲，會產生最精確的結果。 SockPerf （適用于 Linux）和 Latte （適用于 Windows）等公開可用的工具，是可以隔離和測量網路延遲的工具範例，同時排除其他類型的延遲，例如應用程式延遲。 這些工具著重于影響應用程式效能的網路流量類型，也就是 TCP 和 UDP。 其他常見的連線工具（例如 ping）有時可能會用於測量延遲，但這些結果可能無法代表實際工作負載中所使用的網路流量。 &#39;這是因為大部分的這些工具都採用 ICMP 通訊協定，其處理方式不同于應用程式流量，而結果可能不適用於使用 TCP 和 UDP 的工作負載。 針對大部分應用程式所使用的通訊協定，若要進行精確的網路延遲測試，SockPerf （適用于 Linux）和 Latte （適用于 Windows）會產生最相關的結果。 本檔涵蓋這兩種工具。

## <a name="overview"></a>概觀

使用兩個 Vm （一個當做傳送者，另一個做為接收者），會建立雙向通道以雙向傳送和接收封包，以測量來回時間（RTT）。

您可以使用這些步驟來測量兩個虛擬機器（Vm）之間的網路延遲，或甚至兩部實體電腦之間的延遲。 延遲測量有助於下列案例：

- 為部署的 Vm 之間的網路延遲建立基準測試
- 比較在進行相關變更之後，網路延遲變更的影響：
  - OS 或網路堆疊軟體，包括設定變更
  - VM 部署方法，例如部署至區域或 PPG
  - VM 屬性，例如加速網路或大小變更
  - 虛擬網路，例如路由或篩選變更

### <a name="tools-for-testing"></a>測試控管
為了測量延遲，我們有兩個不同的選項，一個用於 Windows 系統，另一個用於以 Linux 為基礎的系統

若為 Windows： latte （Windows） [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

針對 Linux： SockPerf （Linux） [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

使用這些工具可確保只測量 TCP 或 UDP 承載傳遞時間，而非 ICMP （Ping）或應用程式未使用的其他封包類型，而不會影響其效能。

### <a name="tips-for-an-optimal-vm-configuration"></a>最佳 VM 設定的秘訣：

- 使用最新版的 Windows 或 Linux
- 啟用加速網路以獲得最佳結果
- 使用[Azure 鄰近放置群組](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)來部署 vm
- 較大的 Vm 一般會比較小的 Vm 執行效能更佳

### <a name="tips-for-analysis"></a>分析的秘訣

- 在部署、設定和優化完成後，儘早建立基準
- 一律比較新的結果與基準，或從某個測試到另一個具有受控制變更的測試
- 每當觀察到或規劃變更時重複測試


## <a name="testing-vms-running-windows"></a>測試執行 Windows 的 Vm：

## <a name="get-latteexe-onto-the-vms"></a>在 Vm 上取得 Latte

下載最新版本： [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

請考慮將 Latte 放在不同的資料夾中，例如 c:\tools

## <a name="allow-latteexe-through-the-windows-firewall"></a>允許 Latte 通過 Windows 防火牆

在接收者上，于 Windows 防火牆上建立允許規則，以允許 Latte 流量抵達。 最簡單的方式是依名稱允許整個 Latte 程式，而不是允許特定的 TCP 埠輸入。

允許 Latte 通過 Windows 防火牆，如下所示：

netsh advfirewall firewall 新增規則程式 =\<路徑\>\\latte。 .exe 名稱 =&quot;Latte&quot; 通訊協定 = 任何 dir = in action = allow enable = yes profile = ANY


例如，如果您將 latte 複製到 &quot;c：\\tools&quot; 資料夾中，這會是命令：

```cmd
netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

## <a name="running-latency-tests"></a>正在執行延遲測試

在接收者上啟動 latte （從 CMD 執行，而不是從 PowerShell）：

latte-&lt;接收者 IP 位址&gt;：&lt;埠&gt;-i &lt;反復專案&gt;

圍繞65k 反復專案的時間夠長，可傳回代表的結果。

任何可用的埠號碼都沒問題。

如果 VM 的 IP 位址是10.0.0.4，它會如下所示：

```cmd
latte -a 10.0.0.4:5005 -i 65100
```

在寄件者上啟動 latte （從 CMD 執行，而不是從 PowerShell）：

latte-c-\<接收者 IP 位址\>：\<埠\>-i \<反復專案\>


產生的命令與接收者相同，但新增 &quot;-c&quot; 表示這是 &quot;用戶端&quot; 或傳送者：

```cmd
latte -c -a 10.0.0.4:5005 -i 65100
```

等候結果。 視 Vm 的距離而定，可能需要幾分鐘的時間才能完成。 在執行較長的測試之前，請考慮以較少的反覆運算開始測試是否成功。



## <a name="testing-vms-running-linux"></a>測試執行 Linux 的 Vm

使用 SockPerf。 它可以從[https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

### <a name="install-sockperf-on-the-vms"></a>在 Vm 上安裝 SockPerf

在 Linux Vm （「傳送端」和「接收者」）上，執行下列命令以在您的 Vm 上準備 SockPerf。 主要散發版本會提供命令。

#### <a name="for-rhel--centos-follow-these-steps"></a>若為 RHEL/CentOS，請遵循下列步驟：
```bash
#CentOS / RHEL - Install GIT and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu-follow-these-steps"></a>若為 Ubuntu，請遵循下列步驟：
```bash
#Ubuntu - Install GIT and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros-copy-compile-and-install-sockperf-according-to-the-following-steps"></a>針對所有散發版本，請根據下列步驟複製、編譯及安裝 SockPerf：
```bash
#Bash - all distros

#From bash command line (assumes git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>在 Vm 上執行 SockPerf

完成 SockPerf 安裝之後，Vm 就準備好執行延遲測試。 

首先，在接收者上啟動 SockPerf。

任何可用的埠號碼都沒問題。 在此範例中，我們使用埠12345：
```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```
既然伺服器正在接聽，用戶端就可以開始將封包傳送到它接聽的埠上的伺服器，在此案例中為12345。

大約100秒夠長，可傳回代表結果，如下列範例所示：
```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

等候結果。 視 Vm 的距離而定，反復專案的數目會有所不同。 請考慮從較短的大約5秒測試開始，以在執行較長的測試之前測試是否成功。

這個 SockPerf 範例會使用350位元組訊息大小，因為這是典型的平均大小封包。 訊息大小可以調整為較高或較低，以達到更精確表示將在 Vm 上執行之工作負載的結果。


## <a name="next-steps"></a>後續步驟
* 使用[Azure 鄰近放置群組](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)來改善延遲
* 深入瞭解如何針對您的案例將[vm 的網路功能優化](../virtual-network/virtual-network-optimize-network-bandwidth.md)。
* 了解如如何[將頻寬配置給虛擬機器](../virtual-network/virtual-machine-network-throughput.md)
* 深入了解 [Azure 虛擬網路常見問題集 (FAQ)](../virtual-network/virtual-networks-faq.md)
