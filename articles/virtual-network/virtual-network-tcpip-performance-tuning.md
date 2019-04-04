---
title: TCP/IP 的 Azure Vm 的效能微調 |Microsoft Docs
description: 了解各種常見 TCP/IP 效能微調技術以及其關聯至 Azure Vm。
services: virtual-network
documentationcenter: na
author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/30/2019
ms.author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
ms.openlocfilehash: 664c8b659152a370d7fb31907b6cdbcd414dce31
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905087"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>TCP/IP 的效能微調 Azure Vm

本文的目的是要討論一般 TCP/IP 效能微調技術和其在 Microsoft Azure 上執行的虛擬機器的考量。 請務必了解基本概念，並接著討論如何微調。

## <a name="common-tcpip-tuning-techniques"></a>常見的 TCP/IP 微調技術

### <a name="mtu-fragmentation-and-large-send-offload-lso"></a>MTU，分散程度，以及大型傳送卸載 (LSO)

#### <a name="explanation-of-mtu"></a>MTU 的說明

最大傳輸單位 (MTU) 是最大大小指定的框架 （封包），以位元組為單位，可以透過網路介面傳送。 MTU 是可設定的設定和預設使用 Azure Vm 的 MTU，大部分的網路裝置上的預設設定全域，1500 個位元組。

#### <a name="explanation-of-fragmentation"></a>片段的說明

超過一個網路介面的 MTU 傳送封包時，就會發生分散程度。 TCP/IP 堆疊會分成較小的片段 （片段） 符合介面 MTU，封包。 分散在 IP 層，就會發生，並且獨立於基礎的通訊協定 （例如 TCP)。 當 2000年個位元組封包傳送透過 1500年的 MTU 的網路介面時，然後它會細分成一個 1500年個位元組封包和一個 500 個位元組封包。

在來源與目的地之間路徑中的網路裝置可以選擇要卸除超過 MTU 的封包或來分割成較小的片段的封包。

#### <a name="the-dont-fragment-df-bit-in-an-ip-packet"></a>"(DF) 」 中的位元的 IP 封包

Don't Fragment 元是 IP 通訊協定標頭中的旗標。 Je-li nastavena DF 元，表示寄件者和接收者之間的路徑上的中繼網路裝置必須將封包片段。 有許多原因可能會設定此位元的原因 （請參閱以下的其中一個範例的路徑探索一節）。 當網路裝置接收的封包設定 Don't Fragment 元，且該封包超過裝置介面 MTU，則標準行為是要卸除封包，並將 「 需要的 ICMP 片段 」 封包傳送回的原始來源裝置封包。

#### <a name="performance-implications-of-fragmentation"></a>分散程度的效能含意

片段可能會有負面效能影響。 其中一個效能影響的主要原因是分散的 CPU/記憶體影響和重組的封包。 當網路裝置需要片段封包時，必須將 CPU/記憶體資源配置給執行分散程度。 相同必須發生在重組的封包。 網路裝置必須儲存的所有片段，直到在收到讓它可以重組它們的原始封包到。 這個片段/重組的程序也會造成延遲，因為片段/重組程序。

其他可能的負面效能方面是片段的分散的封包可能會失序。 失序的封包可能會導致特定類型的卸除順序則會要求重新將整個封包的封包-網路裝置。 卸除片段的一般案例包括安全性裝置，例如網路防火牆或網路裝置接收當緩衝區已耗盡。 當網路裝置的接收緩衝區已耗盡時，網路裝置正在嘗試重組分散的封包，但沒有資源可儲存及 reassume 封包。

片段可視為負數運算，但支援片段是所需的各種不同的網路連線透過網際網路。

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>優點和修改 MTU 的結果

做為一般的陳述式中，增加 MTU 可以建立更有效率的網路。 傳輸每個封包已加入至原始封包的其他標頭資訊。 詳細的封包表示額外負荷，更多的標頭，且網路連線因此比較沒有效率。

比方說，乙太網路標頭大小為 14 個位元組，再加上 4 個位元組框架檢查序列 (FCS) 以確保框架一致性。 如果傳送一個 2000年個位元組封包，在網路上新增的乙太網路額外負荷的 18 個位元組。 如果封包已分割成 1500年個位元組封包和 500 個位元組封包，則會有每個封包，乙太網路標頭-18 個位元組或 36 個位元組。 而單一的 2000年個位元組封包才會有 18 個位元組的乙太網路標頭。

請務必請注意，增加的 MTU 本身將不一定會建立更有效率的網路。 如果應用程式傳送只有 500 個位元組的封包，則會存在相同的標頭額外負荷，是否 MTU 是 1500 個位元組或 9000 個位元組。 為了讓網路更有效率，則它也必須使用較大的封包大小，相對於 MTU。

#### <a name="azure-and-vm-mtu"></a>Azure 與 VM MTU

預設為 Azure Vm 的 MTU 是 1500 個位元組。 Azure 虛擬網路堆疊會嘗試將片段為 1400 個位元組的封包。 不過，在 Azure 虛擬網路堆疊的封包 2006 個位元組時允許"Don't Fragment"位元設定 IP 標頭中。

請務必請注意，此片段並不代表 Azure 虛擬網路堆疊是原本就是效率不佳，因為它片段為 1400 個位元組的封包，而 Vm 有 1500年 MTU。 事實上是大量百分比的網路封包是遠小於 1400 或 1500 個位元組。

#### <a name="azure-and-fragmentation"></a>Azure 和片段

Azure 的虛擬網路堆疊的目前被設定為要卸除 「 次序不對的片段 」-這表示不以其原始的片段順序抵達的分散封包的數字。 這些封包會卸除歸功於 11 月 2018 呼叫 FragmentStack 宣布網路安全性弱點。

FragmentSmack 是 Linux 核心處理重組的分散的 IPv4 和 IPv6 封包方式的缺失。 遠端攻擊者可以使用這項缺點，觸發程序昂貴的片段重組作業，這導致目標系統上增加的 CPU 和阻絕服務。

#### <a name="tune-the-mtu"></a>微調 MTU

Azure Vm 支援可設定的 MTU，就如同任何其他作業系統。 不過，設定 MTU 時，應該考量，就會發生在 Azure 中，並有詳細說明上述的片段。

Azure 不鼓勵以增加其 VM MTU 的客戶。 本文被要詳細說明 Azure 如何實作 MTU 和執行今天的片段。

> [!IMPORTANT]
>增加 MTU 已不會顯示以改善效能，而且可能會有負面影響的應用程式的效能。
>
>

#### <a name="large-send-offload-lso"></a>大型傳送卸載 (LSO)

大型傳送卸載 (LSO) 可以卸載至乙太網路介面卡的封包分割，以改善網路效能。 啟用時，讓 LSO，使用的 TCP/IP 堆疊建立大型的 TCP 封包，並再傳送至乙太網路介面卡的分割，然後再將它轉送。 讓 LSO 的優點是它可以釋放 CPU 從分段的封包到符合的 MTU，而且該處理卸載至乙太網路介面，其中在硬體中執行的封包大小。 讓 LSO 之優點的詳細資訊可在[Microsoft 網路介面卡的文件中的效能](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso)。

啟用讓 LSO 時，Azure 客戶時可能會看到大型的畫面格大小執行封包擷取。 這些大型的畫面格大小可能會導致某些客戶認為該片段或不是正在使用 MTU 巨大。 LSO，與乙太網路介面卡可以公告較大的 MSS TCP/IP 堆疊，以建立較大的 TCP 封包。 此非分割的整個範圍然後轉送到乙太網路介面卡，並會顯示在 VM 上執行封包擷取。 不過，封包會分解成許多較小的框架所根據的乙太網路卡的 MTU 的乙太網路卡。

### <a name="tcpmss-window-scaling-and-pmtud"></a>TCP/MSS 視窗調整功能和 PMTUD

#### <a name="explanation-of-tcp-mss"></a>TCP MSS 的說明

TCP 最大區段大小 (MSS) 是用來設定 TCP 區段大小上限的設定，避免 TCP 封包的分散程度。 作業系統通常會設定為 MSS 的 MSS = MTU-IP 與 TCP 標頭的大小 （20 個位元組或總計的 40 個位元組）。 因此 1500 MTU 的介面會有 1460年 MSS。 不過，MSS，是可設定。

這項設定是在 TCP 三向交握同意，當 TCP 工作階段設定來源與目的地之間。 兩端傳送 MSS 值和較低的兩個用於 TCP 連線。

中繼網路裝置，例如 VPN 閘道，包括 Azure VPN 閘道，可以調整以確保最佳的網路效能的獨立於來源和目的地 MTU。 因此，請注意，來源和目的地單獨的 MTU 不實際的 MSS 值中唯一的因素。

#### <a name="explanation-of-path-mtu-discovery-pmtud"></a>路徑 MTU Discovery (PMTUD) 的說明

雖然 MSS 交涉時，它可能不會指出可用作來源與目的地之間路徑中的其他網路裝置的實際 MSS 可能低於 MTU 值的來源和目的地。 在此情況下，裝置的 MTU 設定為小於封包會卸除封包，並送回包含其 MTU 的網際網路控制訊息通訊協定 (ICMP) 片段所需 （類型 3、 4 程式碼） 訊息。 此 ICMP 訊息可讓以適當地減少其路徑 MTU 來源主機。 程序稱為 「 路徑 MTU 探索 」。

PMTUD 的程序是原本就是效率不佳，網路效能的影響。 封包傳送，其超過 MTU 的網路路徑，這些封包必須與較低的 MSS 重新傳輸。 如果寄件者不會收到的 ICMP 片段所需的封包，也許是因為網路防火牆中的路徑 （通常稱為 PMTUD blackhole），則寄件者不知道它需要降低 MSS，並會持續重新傳輸的封包。 基於這個理由，我們不建議增加 Azure VM MTU。

#### <a name="vpn-considerations-with-mtu"></a>VPN 與 MTU 的考量

使用 Vm 來執行封裝 （例如 IPSec Vpn) 的客戶可能會有其他封包大小和 MTU 的影響。 Vpn 會新增額外的標頭將會加入至原始的封包，因此增加封包大小，以及需要較小的 MSS。

設定 TCP MSS 固定 1350 位元組和通道介面的 MTU 設定以 1400年為目前適用於 Azure 的建議。 詳細資訊位於[VPN 裝置和 IPSec/IKE 參數頁面](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)。

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>延遲、 來回時間，和 TCP 視窗調整功能

#### <a name="latency-and-round-trip-time"></a>延遲和反覆存取時間

透過光纖相網路的網路延遲受到光線速度。 現實是，TCP 網路輸送量也是有效地控管 （實際的最大值） 因為來回時間 (RTT) 中兩個網路裝置之間。

| | | | |
|-|-|-|-|
|路由|Distance|單向的時間|來回時間 (RTT)|
|New York，San francisco|4,148 公里|21 的 ms|42 ms|
|倫敦到紐約|5,585 公里|28 毫秒|56 ms|
|紐約到雪梨|15,993 公里|80 毫秒|160 毫秒|

下表顯示兩個位置之間的直線距離不過，在網路中，之間的距離會通常比長的直線距離。 光線速度所控管，計算最小的 RTT 的簡單公式是： 最小的 RTT = 2 * (距離公里 / 速度是傳播)。

200 的標準值可用來傳播速度-值是 1 毫秒中周遊的計量 light 中的距離。

在範例中紐約 San francisco，它是 4,148 公里的直線距離。 最小的 RTT = 2 * (4,148 / 20)。 方程式的輸出會以毫秒為單位。

為兩個位置之間的實體距離固定的現實中，如果最大的網路效能需要，則最合乎邏輯的選項是選取目的地使用的最小距離。 其次，在虛擬網路內的設計決策可以對最佳化流量的路徑，並降低延遲。 這些虛擬網路考量如以下的網路設計考量 」 一節所述。

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>在 TCP 上的延遲和來回時間效果

來回行程時間 (RTT) 具有最大的 TCP 輸送量很直接的影響。 TCP 通訊協定有視窗大小的概念。 視窗大小是可以在寄件者必須從接收者收到通知之前，先透過 TCP 連線傳送流量的最大數量。 如果 TCP MSS 設定為 1460，和 TCP 視窗大小設定為 65535 再寄件者可以傳送 45 的封包之前必須從接收者收到通知。 如果未收到收條，則會重新傳輸寄件者。 在此範例中，TCP 視窗大小 / TCP MSS = 傳送的封包。 或 65535 / 1460年會捨入最多 45 個。

此 「 正在等候通知 」 狀態下，做為建立的資料，可靠的傳遞機制是有效地造成影響 TCP 輸送量的 RTT。 寄件者等候通知的時間越長，越久它也之前必須等候傳送的資料。

計算單一 TCP 連線的最大輸送量的公式如下所示：視窗大小 / (RTT 延遲 （毫秒） / 1000年) = 最大值的位元組/秒。 下表針對可讀性格式化以 mb 為單位，並顯示 最大 mb / 每秒的輸送量，單一 TCP 連線。

| | | | |
|-|-|-|-|
|TCP 視窗大小 （位元組）|RTT 延遲<br/>以毫秒為單位|最大值<br/>每秒輸送量 （mb)|最大值<br/> 每秒輸送量的 mb|
|65535|1|65.54|524.29|
|65535|30|2.18|17.48|
|65535|60|1.09|8.74|
|65535|90|.73|5.83|
|65535|120|.55|4.37|

如果沒有任何封包遺失，它會減少 TCP 連線的最大輸送量時寄件者重新傳輸已傳送的資料。

#### <a name="explanation-of-tcp-window-scaling"></a>TCP 視窗調整功能的說明

TCP 視窗調整功能是以動態方式增加 TCP 視窗大小可讓更多資料要傳送通知之前所需的概念。 在上一個範例中，45 的封包會傳送通知之前所需。 如果數字會增加通知之前傳送的封包，然後 TCP 最大輸送量，也可以增加所減少的寄件者正在等候通知的次數。

TCP 輸送量是以簡易表所示：

| | | | |
|-|-|-|-|
|TCP 視窗大小<br/>以位元組為單位|RTT 延遲 （毫秒）|最大值<br/>每秒輸送量 （mb)|最大值<br/> 每秒輸送量的 mb|
|65535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

不過，TCP 視窗大小的 TCP 標頭值是只有 2 個位元組長，這表示接收視窗的最大值為 65535。 若要增加最大視窗大小，引進了 TCP 視窗調整比例。

縮放比例還有可以在作業系統中設定的設定。 使用縮放比例的 TCP 視窗大小的計算公式如下所示：TCP 視窗大小 = TCP 視窗大小，以位元組為單位\*(2 ^ 比例因素)。 如果視窗縮放比例是 3 和 65535 的視窗大小，計算方式如下所示：65535 \* (2 ^3) = 262,140 個位元組。 14 縮放比例會導致 TCP 視窗大小為 14 （允許的偏差上限），則 TCP 視窗大小將 1,073,725,440 位元組 (8.5 gb)。

#### <a name="support-for-tcp-window-scaling"></a>TCP 視窗調整功能的支援

Windows 具有可設定不同的縮放比例功能每個連接類型為基礎-有好幾種連線 （資料中心、 網際網路，等等）。 您所見的視窗調整連接分類使用 Get NetTCPConnection powershell 命令。

```powershell
Get-NetTCPConnection
```

您可以看到每個類別使用 Get NetTCPSetting powershell 命令的值。

```powershell
Get-NetTCPSetting
```

在 Windows 中可以設定 「 初始的 TCP 視窗大小和 TCP 縮放因數 」，透過組 NetTCPSetting powershell 命令。 詳細資訊，參閱[組 NetTCPSetting 頁面](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps)

```powershell
Set-NetTCPSetting
```

有效的 TCP 設定 AutoTuningLevel 如下所示。

| | | | |
|-|-|-|-|
|AutoTuningLevel|縮放比例|縮放比例倍數|公式<br/>計算最大視窗大小|
|已停用|None|None|視窗大小|
|受限制|4|2^4|視窗大小 * (2 ^4)|
|高限制|2|2^2|視窗大小 * (2 ^2)|
|正常|8|2^8|視窗大小 * (2 ^8)|
|實驗性|14|2^14|視窗大小 * (2 ^14)|

這些設定都是最有可能會影響 TCP 效能，請注意，整個網際網路、 Azure 中的控制之外的許多其他因素也可能會影響 TCP 效能。

#### <a name="increase-mtu-size"></a>增加 MTU 大小

邏輯問題是 「 可以增加 MTU 增加 TCP 效能較大的 MTU 代表大 MSS 」？ 答案很簡單 – 可能未正確。 如所述，有的利與弊封包大小適用於超過只是 TCP 流量。 如上所述，最重要的因素會影響 TCP 輸送量的效能是 TCP 視窗大小、 封包遺失和 RTT。

> [!IMPORTANT]
> Azure 不建議的 Azure 客戶，修改虛擬機器上的預設 MTU 值。
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>加速網路，並接收端調整

#### <a name="accelerated-networking"></a>加速網路

虛擬機器網路功能已在過去到 VM 客體與 Hypervisor/主機上需要大量 CPU。 每個傳輸到主機的封包會在軟體中處理，由主應用程式 CPU-包括所有虛擬網路封裝/de-capsulation。 因此，更多流量，會經歷主應用程式，則較高的 CPU 負載。 而且，如果主機 CPU 正忙於執行其他作業，然後，也會影響網路輸送量和延遲。 透過加速網路已解決此問題。

加速網路功能提供一致的超低網路延遲，透過 Azure 的內部的可程式化硬體和 SR-IOV 之類的技術。 藉由移動 Azure 的軟體定義網路堆疊的大部分關閉 Cpu 到 FPGA 為基礎的 SmartNICs，計算和回收的使用者應用程式，將較少的負載放在 VM 上、 降低抖動和不一致的延遲時間週期。 換言之，效能可能會更具確定性。

加速的網路藉由允許略過主應用程式，並建立的資料路徑，直接與主機的 SmartNIC 客體 VM 達到的效能改進。 加速網路的優點如下：

- **較低的延遲 / 較高每秒封包 (pps)**:從資料路徑移除虛擬交換器會減少主機中封包在處理原則時所花的時間，並增加 VM 內可處理的封包數目。

- **減少抖動**:虛擬交換器處理視需要套用的原則數量和正在進行處理的 CPU 工作負載而定。 將原則強制執行卸載到硬體透過將封包直接傳遞到 VM、移除主機到 VM 的通訊，以及所有軟體插斷和環境切換，而減少變化。

- **降低 CPU 使用率**:略過主機中的虛擬交換器可減少處理網路流量的 CPU 使用率。

必須明確啟用加速的網路，每個 VM 為基礎。 啟用加速網路的 VM 上的指示位於[建立具有加速網路的頁面的 Linux 虛擬機器](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)。

#### <a name="receive-side-scaling-rss"></a>接收端調整 (RSS)

接收端調整是一種網路驅動程式技術，將接收的網路流量分散更有效率地散發處理跨多個 Cpu 接收多處理器系統中。 簡單地說，RSS 會讓系統能夠處理更大量的接收流量，因為它會使用所有可用的 Cpu，而不是其中一個。 RSS 的更多技術討論，請參閱[接收端調整 頁面的簡介](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling)。

RSS，才能達到最大效能，在 VM 上啟用加速網路時。 可以也有好處中沒有啟用加速的網路的 Vm 上使用 RSS。 如何判斷是否已啟用 RSS，以及設定啟用它，請參閱概觀[Azure 虛擬機器 頁面的最佳化網路輸送量](http://aka.ms/FastVM)。

### <a name="tcp-time-wait-and-time-wait-assassination"></a>TCP 時間等候和時間等候暗殺

另一個常見的問題會影響網路和應用程式的效能是 TCP 時間等候的設定。 在忙碌的 Vm 的大括弧和右許多通訊端，為用戶端或伺服器 （來源 IP:Source 連接埠 + 目的地 IP:Destination 連接埠）、 TCP、 正常作業期間指定的通訊端可以結束時間等候狀態的相當長的時間。 此 「 等候時間 」 狀態下，是要允許在關閉通訊端上傳遞任何其他資料。 因此，TCP/IP 堆疊通常會防止重複使用的通訊端以無訊息方式卸除用戶端的 TCP SYN 封包。

這段時間內的通訊端處於等候狀態設定，但為 240 秒範圍從 30 秒的時間。 通訊端是有限的資源，並可以在任何時候使用的通訊端數目都可設定 （數字通常位於大約 30,000 潛在的通訊端）。 如果這個數字用完，或用戶端和伺服器有不相符的時間等候設定，且 VM 會嘗試重複使用通訊端的時間等候狀態，則會失敗新連線，如 TCP SYN 封包會以無訊息方式卸除。

通常可在 TCP/IP 堆疊的作業系統內設定對外的通訊端，以及 TCP 時間等候設定和通訊端重複使用的連接埠範圍的值。 變更這些數字可能改善延展性，但根據案例，可能會造成互通性問題，應謹慎使用變更。

稱為 「 時間等候暗殺的功能引進來解決此問題的調整限制。 時間等候暗殺允許序號的新連線的 IP 封包中超過序號的最後一個封包，從先前的連線時在某些情況下重複使用通訊端。 在此情況下，作業系統將允許建立新的連接 （接受新的同步認可），並強制關閉先前的時間等候狀態的連線。 這項功能目前都支援在 Azure 中 Windows Vm 上，支援其他 Vm 內應該加以調查與個別的 OS 廠商的 Azure 客戶。

設定 TCP 時間等候設定與來源連接埠範圍的文件位於[您可以修改以改善網路效能 頁面的設定](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance)。

## <a name="virtual-network-factors-that-can-affect-performance"></a>虛擬網路因素可能會影響效能

### <a name="vm-maximum-outbound-throughput"></a>VM 的最大輸出輸送量

Azure 提供各種 VM 大小和類型，各有不同的效能功能組合。 其中一個這類的效能功能是網路輸送量 （或頻寬），以 mb / 秒 (Mbps)。 因為虛擬機器裝載於共用硬體，網路容量必須平均分給共用相同硬體的虛擬機器。 相對於較小的虛擬機器，大型的虛擬機器會配置較多的頻寬。

配置給每個虛擬機器的網路頻寬是依據從虛擬機器的流出 (輸出) 流量來計量。 所有離開虛擬機器的網路流量都會計算到配置的限制，不會因為目的地不同而有差異。 比方說，如果虛擬機器有 1,000 Mbps 的限制，該限制會套用是否以另一部虛擬機器在相同的虛擬網路中，或 Azure 外部的輸出流量為目的端。
輸入則不會直接計量或限制。 不過，還有其他因素，例如 CPU 和儲存體限制可能會影響虛擬機器處理內送資料的能力。

加速的網路是設計來改善網路效能，包括延遲、 輸送量和 CPU 使用率的功能。 雖然加速網路可以改善虛擬機器的輸送量，它可以這麼做只達虛擬機器的配置頻寬。

Azure 虛擬機器必須連結一個 (但可以有數個) 網路介面。 配置給虛擬機器的頻寬，是虛擬機器上所有網路介面的所有輸出流量總和。 換句話說，配置的頻寬是以每個虛擬機器為依據，跟連結多少個網路介面到虛擬機器無關。
 
預期的輸出輸送量，以及每個 VM 大小所支援的網路介面數目是這裡詳述。 若要查看的最大輸送量，選取類型，例如一般用途，然後選取在產生的頁面上，例如 Dv2 系列的大小系列。 每個數列都包含網路規格，標題為 「 最大 Nic 的最後一欄的資料表 / 預期的網路效能 (Mbps)。

輸送量限制會套用至虛擬機器。 輸送量不會受到下列因素影響：

- **網路介面數目**:頻寬限制，都會累積所有的輸出流量從虛擬機器。

- **加速網路**:此功能，能幫助您達到已發佈的限制，但它不會變更限制。

- **流量目的地**:所有目的地都會計入輸出限制。

- **通訊協定**：透過所有通訊協定的所有輸出流量都會計入限制。

A[可找到每個 VM 類型的最大頻寬的資料表，請瀏覽此頁面](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)，然後按一下個別的 VM 類型。 在每個類型 頁面中，資料表會顯示的最大 Nic 和最大預期的網路頻寬。

VM 的網路頻寬的詳細資訊位於[虛擬機器的網路頻寬](http://aka.ms/AzureBandwidth)。

### <a name="internet-performance-considerations"></a>網際網路效能考量

如本文所討論，在網際網路和 Azure 的控制之外的因素可能會影響網路效能。 這些因素包括：

- **延遲**:兩個目的地之間的往返時間可能會受到中繼網路流量不採用 「 」 的最短距離路徑可能並不理想對等互連路徑上的問題

- **封包遺失**:封包遺失可能被因網路壅塞、 實體路徑問題，以及在執行網路裝置

- **MTU 大小/片段**:沿著路徑的片段可能會導致延遲資料抵達或封包抵達順序，這可能會影響傳遞的封包

路徑追蹤是很好的工具來測量網路效能特性 （例如封包遺失和延遲） 每個來源和目的地裝置之間的網路路徑。

### <a name="network-design-considerations"></a>網路設計考量

上述的考量，以及虛擬網路的拓撲，可能會影響虛擬網路的效能。 例如，中樞和支點設計 backhauls 全域到單一中樞虛擬網路的流量將會介紹網路延遲，並因此影響整體網路效能。 同樣地，網路流量通過的網路裝置數目也會影響整體延遲。 例如，在中樞和支點設計中，如果流量通過支點網路虛擬設備和中樞虛擬應用裝置之前傳送到網際網路，然後延遲會造成網路虛擬設備。

### <a name="azure-regions-virtual-networks-and-latency"></a>Azure 區域、 虛擬網路和延遲

Azure 區域是組成多個地理區域內存在的資料中心。 這些資料中心可能無法實際彼此相鄰，並在某些情況下可能會分隔最高達 10 公里。 虛擬網路是 Azure 的實體資料中心網路上的邏輯重疊和虛擬網路並不代表任何特定的網路拓撲，在資料中心內。 例如，VM A 和 B VM 位於相同虛擬網路和子網路，但可能位於不同機架上，資料列或甚至是資料中心。 它們可能會分隔英呎數光纖電纜或公里的光纖電纜。 此狀況可能會造成不同的 Vm 之間的變數延遲 （幾毫秒差異）。

透過可用性設定組和可用性區域的組態，則可以影響此地理位置，因此兩個 Vm 之間的延遲，不過，在區域中的資料中心之間的距離是特定區域，而且主要受到在區域中的資料中心拓撲。

### <a name="source-nat-port-exhaustion"></a>來源 NAT 連接埠耗盡

在 Azure 中的部署可以在公用網際網路及/或公用 IP 空間的 Azure 外部端點進行通訊。 當執行個體啟動了此輸出連線時，Azure 會動態地將私人 IP 位址對應到公用 IP 位址。 建立此對應之後，此輸出起源流程的傳回流量便也可以觸達流程起源的私人 IP 位址。

針對每個輸出的連接，Azure Load Balancer 必須維護此對應的一段時間。 與 Azure 的多租用戶本質，維護此對應的每個輸出的流程，為每個 VM 可以是需要大量資源。 因此，沒有限制，會設定並根據 Azure 虛擬網路的組態。 或者，更精確地說-指定 Azure VM 可以只讓輸出連線數目在指定的時間。 這些限制會重試時，Azure VM 將會防止進行任何進一步的輸出連線。

這種行為，不過，可設定。 如需詳細資訊 [SNAT 和 SNAT 連接埠耗盡，] 請參閱[這篇文章](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)。

## <a name="measure-network-performance-on-azure"></a>量值在 Azure 上的網路效能

這篇文章中的效能最大值的數目相關的網路延遲 / 反覆存取兩個 Vm 之間的時間 (RTT)。 本節提供如何測試延遲/RTT，以及 TCP 效能和 VM 網路效能的一些建議。 可微調上面所討論的 TCP/IP 和網路值，並使用如下所述的技巧來測試效能。 延遲、 MTU，MSS 和視窗大小的值可以用於上面所列的計算，並為觀察在測試期間的實際值，就可以比較理論的最大值。

### <a name="measure-round-trip-time-and-packet-loss"></a>量值的來回時間和封包遺失

TCP 效能會非常依賴 RTT 和封包遺失。 測量 RTT 和封包遺失的最簡單方式使用 ping 公用程式可在 Windows 和 Linux。 Ping 的輸出會顯示來源和目的地，以及封包遺失之間的最小/最大/平均延遲。 Ping 預設會使用 ICMP 通訊協定。 若要測試 TCP RTT，則 PsPing 可用。 更多有關 PsPing 位於[此連結](https://docs.microsoft.com/sysinternals/downloads/psping)。

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>量值的 TCP 連線的實際輸送量

NTttcp 是用來測試 Linux 或 Windows VM 的 TCP 效能的工具。 可以調整 TCP 的各種設定，並使用 NTttcp 測試的優點。 在下方的連結，就可以找到 NTttcp 的詳細資訊。

- [頻寬/輸送量測試 (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [NTttcp 公用程式](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>量值的虛擬機器的實際頻寬

效能測試的不同 VM 類型、 加速網路，並依此類推，可以使用測試名為 Iperf，也適用於 Linux 和 Windows 的工具。 Iperf 可以使用 TCP 或 UDP，若要測試的整體網路輸送量。 使用 Iperf 的 TCP 輸送量測試會受到影響 （例如延遲、 RTT，等等），這篇文章所述的因素。 因此，UDP 可能會產生更好的結果，只是測試的最大輸送量。

下面，就可以找到其他資訊：

- [Expressroute 的網路效能疑難排解](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [如何驗證傳輸到虛擬網路的 VPN 輸送量](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>偵測沒有效率的 TCP 行為

Azure 客戶可能會看到與 TCP 旗標 （SACK、 DUP 通知、 重新傳輸，和快速重新傳輸） TCP 封包中可能表示網路效能問題的封包擷取。 這些封包會特別指出由於封包遺失網路效率不足的情況。 不過，封包遺失不一定是來自 Azure 的效能問題。 效能問題可能是應用程式、 作業系統或其他問題，可能不會直接與 Azure 平台的結果。 務必也要注意，某些重新傳輸或網路上的重複通知是正常現象，TCP 通訊協定建置可靠。 並在封包擷取這些 TCP 封包的辨識項不一定表示系統的網路問題除非過多。

不過，它應該清楚地加以說明，這些封包類型的 TCP 輸送量未達其最大的效能 – 基於其他各節所述的指示。
