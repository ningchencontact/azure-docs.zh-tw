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
ms.date: 04/02/2019
ms.author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
ms.openlocfilehash: 1e8605a41cbe610c971b891309b2149d221b8b27
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59426438"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>TCP/IP 的效能微調 Azure Vm

這篇文章討論一般 TCP/IP 效能微調技術和您用於在 Azure 上執行的虛擬機器時應考量一些事項。 它會提供技術的基本概觀，並探索如何調整。

## <a name="common-tcpip-tuning-techniques"></a>常見的 TCP/IP 微調技術

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU，分散程度，以及大型傳送卸載

#### <a name="mtu"></a>MTU

最大傳輸單位 (MTU) 是最大大小指定的框架 （封包），以位元組為單位，可以透過網路介面傳送。 MTU 是可設定的設定。 在 Azure Vm 上使用的預設 MTU，大部分的網路裝置上的預設設定全域，為 1,500 個位元組。

#### <a name="fragmentation"></a>分割

超過一個網路介面的 MTU 傳送封包時，就會發生分散程度。 TCP/IP 堆疊會分成較小的片段 （片段） 符合介面的 MTU，封包。 分散在 IP 層，就會發生，並且獨立於基礎的通訊協定 （例如 TCP)。 當 2000 個位元組封包傳送透過 1,500 MTU 的網路介面時，封包會被細分成一個 1,500 個位元組封包和一個 500 個位元組封包。

在來源與目的地之間路徑中的網路裝置可以是卸除封包超過 MTU 或分割成較小的片段的封包。

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>在 IP 封包中的位元 Don't Fragment

(DF) 位元是 IP 通訊協定標頭中的旗標。 DF 位元表示寄件者和接收者之間的路徑上的網路裝置必須將封包片段。 有許多原因，無法設定此位元。 （請參閱本文中的其中一個範例的 「 路徑 MTU 探索 」 一節。）網路裝置收到封包設定 Don't Fragment 元，並封包超過裝置的介面 MTU，標準行為時，若要卸除封包的裝置。 裝置會傳送 ICMP 片段所需的訊息傳回至原始封包的來源。

#### <a name="performance-implications-of-fragmentation"></a>分散程度的效能含意

片段可能會有負面效能影響。 其中一個的效能影響的主要原因是 CPU/記憶體影響的分散和重組的封包。 當網路裝置需要片段封包時，必須將 CPU/記憶體資源配置給執行分散程度。

在重組的封包時，就會發生相同的作業。 必須儲存的所有片段，直到在收到讓它可以重組它們的原始封包到網路裝置。 這個程序的分散和重組也會造成延遲。

其他可能的負面效能方面是片段的分散的封包可能送達順序。 封包會收到次序不對，某些類型的網路裝置可卸除它們。 當發生這種情況時，整個封包必須重新傳輸。

片段通常會卸除安全性裝置，例如網路防火牆或網路裝置接收當緩衝區已耗盡。 當網路裝置的接收緩衝區已耗盡時，網路裝置正在嘗試重組分散的封包，但沒有資源可儲存及 reassume 封包。

片段可視為負數運算，但支援片段為必要，當您透過網際網路連線不同的網路。

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>優點和修改 MTU 的結果

一般而言，您可以藉由增加 MTU，建立更有效率的網路。 傳輸每個封包已加入至原始封包的標頭資訊。 當片段會建立多個封包時，沒有額外負荷，更多的標頭，並如此網路效率較低。

以下是範例。 乙太網路標頭大小為 14 個位元組，再加上 4 個位元組框架核取序列，以確保框架的一致性。 如果傳送一個 2000 個位元組封包，則是會在網路上新增的乙太網路額外負荷的 18 個位元組。 如果封包已分割成 1,500 個位元組封包和 500 個位元組封包，每個封包會有 18 個位元組的乙太網路標頭，共 36 個位元組。

請記住，增加 MTU 不一定要建立更有效率的網路。 如果應用程式傳送只有 500 個位元組的封包，是否 MTU 是 1500 或 9,000 個位元組，將會存在相同的標頭負擔。 網路將會變得更有效率，只有在使用較大的封包大小受影響的 MTU 的。

#### <a name="azure-and-vm-mtu"></a>Azure 與 VM MTU

適用於 Azure Vm 的 MTU 的預設值為 1,500 個位元組。 Azure 虛擬網路堆疊會嘗試將片段為 1,400 個位元組的封包。 但虛擬網路堆疊的封包 2,006 個位元組時允許 Don't Fragment 位元設定 IP 標頭中。

請注意，虛擬網路堆疊不原本就是效率不佳，因為它會分割為 1,400 個位元組的封包，即使 Vm 有 1,500 MTU。 大量百分比的網路封包會遠小於 1,400 或 1,500 個位元組。

#### <a name="azure-and-fragmentation"></a>Azure 和片段

虛擬網路堆疊設定卸除 「 次序不對的片段，"也就是分散的封包不在其原始的片段順序抵達。 由於網路安全性的弱點可能會在 11 月 2018 呼叫 FragmentSmack 宣布，這些封包會卸除。

FragmentSmack 是 Linux 核心處理重組的分散的 IPv4 和 IPv6 封包方式的缺失。 遠端攻擊者可以使用觸發程序昂貴的片段重組作業，可能會導致更高的 CPU 和阻絕服務目標系統上的這項弱點。

#### <a name="tune-the-mtu"></a>微調 MTU

您可以設定 Azure VM MTU，您可以在任何其他作業系統。 您應該考慮在 Azure 中，上述步驟，就會發生的片段，但當您設定的 MTU。

我們不鼓勵以增加 VM Mtu 的客戶。 本文旨在說明 Azure 如何實作 MTU 和執行分散程度的詳細資料。

> [!IMPORTANT]
>增加 MTU 不知道改善效能，而且可能會有負面影響的應用程式的效能。
>
>

#### <a name="large-send-offload"></a>大型傳送卸載

大型傳送卸載 (LSO) 可以卸載至乙太網路介面卡的封包分割，以改善網路效能。 啟用 LSO 時，TCP/IP 堆疊會建立大型的 TCP 封包，並將它傳送至轉送它之前的區隔的乙太網路介面卡。 讓 LSO 的優點是它可以釋放 CPU 從分段的封包到符合的 MTU，而且該處理卸載至乙太網路介面，它會在硬體中的大小。 若要深入了解讓 LSO 的優點，請參閱[支援大型傳送卸載](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso)。

啟用讓 LSO 時，Azure 客戶可能會看到大型的畫面格大小，當他們執行封包擷取。 某些客戶認為在發生片段或大型 MTU，正在使用時，可能會導致這些大型的畫面格大小。 LSO，與乙太網路介面卡可以公告來建立較大的 TCP 封包的 TCP/IP 堆疊的較大的最大區段大小 (MSS)。 此非分割的整個範圍然後轉送到乙太網路介面卡，並會顯示在 VM 上執行封包擷取。 但是，封包會分解成許多較小的畫面格的乙太網路介面卡，根據乙太網路介面卡的 MTU。

### <a name="tcp-mss-window-scaling-and-pmtud"></a>TCP MSS 視窗調整功能和 PMTUD

#### <a name="tcp-maximum-segment-size"></a>TCP 最大區段大小

TCP 區段大小上限 (MSS) 是一種設定，以避免 TCP 封包的分散程度的 TCP 區段大小限制。 作業系統通常會使用這個公式，來設定 MSS:

`MSS = MTU - (IP header size + TCP header size)`

IP 標頭及 TCP 標頭為 20 個位元組或 40 個位元組總數。 因此 1,500 MTU 的介面必須 1,460 MSS。 但 MSS 是可設定。

此設定會同意在 TCP 三向交握當 TCP 工作階段設定在來源與目的地之間。 兩端傳送為 MSS 的值，並較小的兩個用於 TCP 連線。

請記住 Mtu 的來源和目的地不是唯一的因素決定 MSS 值。 中繼網路裝置，例如 VPN 閘道，包括 Azure VPN 閘道，可以調整獨立於來源和目的地的 MTU，以確保最佳的網路效能。

#### <a name="path-mtu-discovery"></a>路徑 MTU Discovery

交涉 MSS，但它可能不會指出實際的 MSS 可用。 這是因為來源與目的地之間路徑中的其他網路裝置可能會具有較低的 MTU 值比來源和目的地。 在此情況下，的裝置的 MTU 設定為小於封包將會卸除封包。 裝置會送回包含其 MTU 的 ICMP 需要 （類型 3、 4 程式碼） 的訊息。 此 ICMP 訊息可讓以適當地減少其路徑 MTU 來源主機。 程序稱為 「 路徑 MTU Discovery (PMTUD) 」。

PMTUD 程序效率不佳，而且會影響網路效能。 封包傳送，其超過網路路徑的 MTU，封包必須與較低的 MSS 重新傳輸。 如果寄件者不會收到 ICMP 需要訊息，或許因為網路防火牆在路徑中 (通常稱為*PMTUD blackhole*)，寄件者並不知道它需要降低 MSS，並將持續重新傳輸的封包。 這就是為什麼我們不建議增加 Azure VM MTU。

#### <a name="vpn-and-mtu"></a>VPN 和 MTU

如果您使用執行封裝 （例如 IPsec Vpn) 的 Vm 時，有一些其他封包大小和 MTU 相關考量。 Vpn 會新增更多的標頭至封包，這樣會增加的封包大小，而且需要較小的 MSS。

適用於 Azure，我們建議您設定 TCP MSS 固定為 1,350 個位元組，並建立通道介面 MTU 1400。 如需詳細資訊，請參閱 < [VPN 裝置和 IPSec/IKE 參數頁面](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)。

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>延遲、 來回時間，和 TCP 視窗調整功能

#### <a name="latency-and-round-trip-time"></a>延遲和反覆存取時間

透過光纖相網路的網路延遲受到光線速度。 網路輸送量的 TCP 也可以有效地受到兩個網路裝置之間的往返時間 (RTT)。

| | | | |
|-|-|-|-|
|**路由**|**距離**|**單向的時間**|**RTT**|
|New York，San francisco|4,148 公里|21 的 ms|42 ms|
|倫敦到紐約|5,585 公里|28 毫秒|56 ms|
|紐約到雪梨|15,993 公里|80 毫秒|160 毫秒|

下表顯示兩個位置之間的直線距離。 在網路之間的距離是通常超過的直線距離。 以下是受到光線速度，計算最小的 RTT 的簡單公式：

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

您可以使用 200 速度的傳播。 這是以公尺為單位，光傳輸 1 毫秒中的距離。

讓我們看紐約 San francisco，做為範例。 直線距離是 4,148 公里。 您可以將該值插入方程式，我們得到以下結果：

`Minimum RTT = 2 * (4,148 / 200)`

方程式的輸出是以毫秒為單位。

如果您想要獲得最佳的網路效能，邏輯的選項是選取它們之間的距離最短的目的地。 您也應該設計您的虛擬網路，以最佳化流量的路徑，並降低延遲。 如需詳細資訊，請參閱這篇文章的 < 網路設計考量 > 一節。

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>在 TCP 上的延遲和來回時間效果

來回時間有直接影響最大的 TCP 輸送量。 TCP 通訊協定，在*視窗大小*是之前需要以接收來自接收者通知的寄件者可以透過 TCP 連線傳送的流量的最大數量。 如果 TCP MSS 設定為 1,460 TCP 視窗大小設為 65,535，寄件者可以傳送 45 的封包，以接收來自接收者通知之前。 如果寄件者並不會接到通知，將會重新傳輸的資料。 公式如下：

`TCP window size / TCP MSS = packets sent`

在此範例中，65,535 / 1,460 會捨入最多 45 個。

此 「 正在等候通知 」 狀態下，一個機制，確保可靠傳遞的資料，便會導致對 TCP 輸送量產生影響的 RTT。 寄件者等候通知的時間越長，它需要再等候傳送的資料。

以下是計算單一 TCP 連線的最大輸送量的公式：

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

下表顯示的最大 mb / 每秒的輸送量，單一 TCP 連線。 （為了便於閱讀，mb 用於為測量單位）。

| | | | |
|-|-|-|-|
|**TCP 視窗大小 （位元組）**|**RTT 延遲 （毫秒）**|**最大 mb/秒的輸送量**|**最大 mb/秒的輸送量**|
|65,535|1|65.54|524.29|
|65,535|30|2.18|17.48|
|65,535|60|1.09|8.74|
|65,535|90|.73|5.83|
|65,535|120|.55|4.37|

如果封包遺失，寄件者重新傳輸已傳送的資料時，將會縮小的 TCP 連線的最大輸送量。

#### <a name="tcp-window-scaling"></a>TCP 視窗調整功能

TCP 視窗調整功能是以動態方式增加 TCP 視窗大小，以允許更多資料要傳送通知之前所需的技術。 在上述範例中，45 的封包會傳送通知之前所需。 如果您增加之前需要通知可以傳送的封包數目，減少的寄件者正在等候收條，這樣會增加 TCP 最大輸送量的次數。

這份表格說明這些關聯性：

| | | | |
|-|-|-|-|
|**TCP 視窗大小 （位元組）**|**RTT 延遲 （毫秒）**|**最大 mb/秒的輸送量**|**最大 mb/秒的輸送量**|
|65,535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

但是 TCP 視窗大小的 TCP 標頭值是只有 2 個位元組長，這表示接收視窗的最大值為 65,535。 若要增加最大視窗大小，引進了 TCP 視窗調整比例。

縮放比例還有您可以設定的作業系統中的設定。 以下是使用縮放比例計算的 TCP 視窗大小的公式：

`TCP window size = TCP window size in bytes \* (2^scale factor)`

以下是 3 和視窗大小為 65,535 的視窗調整係數的計算：

`65,535 \* (2^3) = 262,140 bytes`

14 縮放比例會導致 TCP 視窗大小為 14 （允許的偏差上限）。 TCP 視窗大小會 1,073,725,440 位元組 (8.5 gb)。

#### <a name="support-for-tcp-window-scaling"></a>TCP 視窗調整功能的支援

Windows 可以針對不同的連接類型設定不同的縮放比例。 （連線的類別包括資料中心、 網際網路，等等）。您使用`Get-NetTCPConnection`PowerShell 命令來檢視視窗調整連線類型：

```powershell
Get-NetTCPConnection
```

您可以使用`Get-NetTCPSetting`PowerShell 命令來檢視每個類別的值：

```powershell
Get-NetTCPSetting
```

您可以在 Windows 中設定的初始的 TCP 視窗大小和 TCP 的縮放比例，使用`Set-NetTCPSetting`PowerShell 命令。 如需詳細資訊，請參閱 <<c0> [ 組 NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps)。

```powershell
Set-NetTCPSetting
```

這些是有效 TCP 設定`AutoTuningLevel`:

| | | | |
|-|-|-|-|
|**AutoTuningLevel**|**縮放比例**|**縮放比例倍數**|**公式<br/>計算視窗大小上限**|
|已停用|None|None|視窗大小|
|受限制|4|2^4|視窗大小 * (2 ^4)|
|很高的限制|2|2^2|視窗大小 * (2 ^2)|
|正常|8|2^8|視窗大小 * (2 ^8)|
|實驗性|14|2^14|視窗大小 * (2 ^14)|

這些設定是最有可能會影響 TCP 效能，但請記住，整個網際網路、 Azure 中的控制之外的許多其他因素也可能會影響 TCP 效能。

#### <a name="increase-mtu-size"></a>增加 MTU 大小

因為較大的 MTU 代表大 MSS，您可能會懷疑是否增加 MTU 可以增加 TCP 效能。 大概沒有。 有的利與弊超出只是 TCP 流量的封包大小。 如前文所述，對 TCP 輸送量效能影響的最重要因素是 TCP 視窗大小、 封包遺失和 RTT。

> [!IMPORTANT]
> 我們不建議的 Azure 客戶變更虛擬機器上的預設 MTU 值。
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>加速網路，並接收端調整

#### <a name="accelerated-networking"></a>加速網路

虛擬機器網路功能已在過去需要 hypervisor/主機和客體 VM 上大量的 CPU。 主機 CPU，包括所有虛擬網路封裝及去除封裝會在軟體中處理傳輸到主應用程式的每個封包。 經歷主應用程式更多流量，因此較高的 CPU 負載。 如果主機 CPU 忙著處理其他作業的也會影響網路輸送量和延遲。 Azure 會解決這個問題的加速網路。

加速網路功能提供一致的 ultralow 網路延遲，透過 Azure 與 SR-IOV 這類技術的內部的可程式化硬體。 加速的網路可將大部分的 Azure 軟體定義網路堆疊出 Cpu 再到 FPGA 為基礎的 SmartNICs。 這項變更可讓使用者以回收計算週期的應用程式將放置於較少負載的 VM 上，降低抖動和不一致的延遲。 換言之，效能可能會更具確定性。

加速的網路允許在客體 VM 來略過主應用程式，並建立直接與主機的 SmartNIC 資料路徑，以改善效能。 以下是加速網路的一些優點：

- **較低的延遲 / 較高每秒封包 (pps)**:資料路徑移除虛擬交換器時，排除在原則處理的主機的封包花費的時間，並增加可以在 VM 中處理的封包數目。

- **減少抖動**:虛擬交換器處理視需要套用的原則數量和正在進行處理的 cpu 的工作負載而定。 藉由直接將封包交給 VM，並消除 VM 主機通訊及所有軟體插斷和內容切換，卸載至硬體強制的原則移除，而減少變化。

- **降低 CPU 使用率**:略過主機中的虛擬交換器可減少處理網路流量的 CPU 使用率。

若要使用加速的網路，您需要明確地在每個適用的 VM 上啟用它。 請參閱[建立有加速網路的 Linux 虛擬機器](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)如需相關指示。

#### <a name="receive-side-scaling"></a>接收端調整

接收的端調整 (RSS) 是一種網路驅動程式技術，將接收的網路流量分散更有效率地散發接收跨多個 Cpu 處理，多處理器系統中。 簡單地說，RSS 會讓系統能夠處理接收的流量，因為它會使用所有可用的 Cpu，而不是其中一個。 RSS 更多技術的討論，請參閱[接收端調整簡介](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling)。

若要獲得最佳效能，在 VM 上啟用加速的網路時，您必須啟用 RSS。 RSS 也可以在未使用的 Vm 上的優點加速網路來提供。 如需如何判斷是否已啟用 RSS，以及如何加以啟用的概觀，請參閱 <<c0> [ 最佳化網路輸送量，Azure 虛擬機器的](http://aka.ms/FastVM)。

### <a name="tcp-timewait-and-timewait-assassination"></a>TCP TIME_WAIT 和 TIME_WAIT 暗殺

TCP TIME_WAIT 是另一個常見的設定，會影響網路和應用程式的效能。 在忙碌的 Vm 開啟及關閉許多通訊端，為用戶端或伺服器 （來源 IP:Source 連接埠 + 目的地 IP:Destination 連接埠），在 TCP 正常作業期間，指定通訊端可以得到 TIME_WAIT 狀態的長時間。 TIME_WAIT 狀態是要允許關閉之前，通訊端上傳遞任何其他資料。 因此 TCP/IP 堆疊通常會防止重複使用的通訊端以無訊息方式卸除用戶端的 TCP SYN 封包。

通訊端是時間的在 TIME_WAIT 量是時間的可設定的。 它為 240 秒範圍從 30 秒。 通訊端是有限的資源，且設定可以在任何時候使用的通訊端數目。 （可用的通訊端數目通常是大約 30,000）。如果使用可用的通訊端，或用戶端和伺服器有 TIME_WAIT 設定不相符，而 VM 會嘗試重複使用通訊端在 TIME_WAIT 的狀態，新的連線將會失敗為無訊息地捨棄的封包的 TCP SYN。

通常可以在 TCP/IP 堆疊的作業系統內設定對外的通訊端的連接埠範圍值。 相同的動作適用於 TCP TIME_WAIT 設定和通訊端重複使用。 變更這些數字可能改善延展性。 但是，您也可以視情況而定，這些變更可能會造成互通性問題。 您應該小心，如果您變更這些值。

您可以使用 TIME_WAIT 暗殺，若要解決此調整限制。 TIME_WAIT 暗殺允許重複使用在某些情況下，例如新的連接將 IP 封包的序號超過的序號的最後一個封包，從先前的連線時的通訊端。 在此情況下，作業系統將允許建立新的連接 （它會接受新的同步/認可），並強制關閉先前處於 TIME_WAIT 狀態的連線。 在 Azure 中 Windows Vm 支援這項功能。 若要深入了解其他 Vm 中的支援，請向 OS 廠商。

若要深入了解 TCP TIME_WAIT 設定與來源連接埠範圍，請參閱[您可以修改以改善網路效能的設定](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance)。

## <a name="virtual-network-factors-that-can-affect-performance"></a>虛擬網路可能會影響效能的因素

### <a name="vm-maximum-outbound-throughput"></a>VM 的最大輸出輸送量

Azure 提供各種不同的 VM 大小和類型，各有不同的效能功能組合。 這些功能的其中一個是網路輸送量 （或頻寬），該值會以 mb / 秒 (Mbps)。 因為虛擬機器裝載於共用硬體，網路容量必須相當使用相同的硬體的虛擬機器之間共用。 較大的虛擬機器會配置更多頻寬較小的虛擬機器。

配置給每個虛擬機器的網路頻寬是依據從虛擬機器的流出 (輸出) 流量來計量。 所有離開虛擬機器的網路流量都會計算到配置的限制，不會因為目的地不同而有差異。 比方說，如果虛擬機器有 1,000 Mbps 的限制，該限制會套用是否輸出流量的目的地中相同的虛擬網路或 Azure 外部的另一個虛擬機器。

輸入則不會直接計量或限制。 但還有其他因素，例如 CPU 和儲存體，可能會影響虛擬機器的能力來處理內送資料的限制。

加速的網路被設計來改善網路效能，包括延遲、 輸送量和 CPU 使用率。 加速的網路可提升虛擬機器的輸送量，但它可以這麼做只到虛擬機器的配置的頻寬。

Azure 虛擬機器擁有至少一個附加到它們的網路介面。 它們可能會有數個。 配置給虛擬機器的頻寬會是跨所有連接至機器的網路介面的所有輸出流量總和。 換句話說，針對每個虛擬機器，不論多少個網路介面連接至機器配置頻寬。

預期的輸出輸送量，以及每個 VM 大小所支援的網路介面數目會詳述[大小的 Windows Azure 中的虛擬機器](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)。 若要查看的最大輸送量，選取類型，例如**一般用途**，然後尋找 在產生的頁面上 （例如，"Dv2 系列 」） 的大小系列的相關章節。 每個數列，沒有資料表，其中提供網路的規格，在過去的專欄中，標題為 「 最大 Nic / 預期的網路頻寬 (Mbps)。 」

輸送量限制會套用至虛擬機器。 輸送量不會受到下列因素：

- **網路介面數目**:頻寬限制適用於所有輸出流量總和從虛擬機器。

- **加速網路**:這項功能，能幫助您達到已發佈的限制，但它不會變更限制。

- **流量目的地**:所有目的地都會計入輸出限制。

- **通訊協定**：透過所有通訊協定的所有輸出流量都會計入限制。

如需詳細資訊，請參閱 <<c0> [ 虛擬機器的網路頻寬](http://aka.ms/AzureBandwidth)。

### <a name="internet-performance-considerations"></a>網際網路效能考量

如本文所討論，在網際網路和 Azure 的控制之外的因素可能會影響網路效能。 以下是一些因素：

- **延遲**:中繼網路上的問題，不接受 「 最短的 「 距離路徑的流量和次佳的對等互連路徑，可能會影響兩個目的地之間的往返時間。

- **封包遺失**:封包遺失可能因網路壅塞、 實體路徑問題，以及績效不佳的網路裝置。

- **MTU 大小/片段**:沿著路徑的片段可能會導致延遲在資料抵達或封包抵達順序，這可能會影響的封包傳遞。

路徑追蹤是很好的工具，測量網路效能特性 （例如封包遺失和延遲） 每個裝置來源與目的地裝置之間的網路路徑。

### <a name="network-design-considerations"></a>網路設計考量

稍早在本文中討論的考量，以及虛擬網路的拓撲，可能會影響網路效能。 比方說，backhauls 全域流量至單一中樞虛擬網路的中樞和支點設計將介紹網路延遲，這會影響整體網路效能。

網路流量通過的網路裝置數目也會影響整體延遲。 例如，在中樞和支點設計中，如果流量會通過支點網路虛擬設備和中樞虛擬應用裝置之前傳送到網際網路的網路虛擬設備可能會造成延遲。

### <a name="azure-regions-virtual-networks-and-latency"></a>Azure 區域、 虛擬網路和延遲

Azure 區域是組成多個地理區域內存在的資料中心。 這些資料中心可能無法實際彼此相鄰。 在某些情況下它們是以最高達 10 公里分隔。 虛擬網路是 Azure 的實體資料中心網路上的邏輯覆疊。 虛擬網路並不代表任何特定的網路拓撲，在資料中心內。

比方說，兩個 Vm 位於相同虛擬網路和子網路可能位於不同機架、 資料列或甚至是資料中心。 英呎數光纖電纜或公里的光纖電纜可以分隔它們。 這項差異可能會造成不同的 Vm 之間的變數延遲 （幾毫秒差異）。

可用性設定組和可用性區域的設定會影響 Vm 的地理位置和兩個 Vm 之間的可能產生延遲。 但在區域中的資料中心之間的距離是特定區域及主要受影響區域中的資料中心拓撲。

### <a name="source-nat-port-exhaustion"></a>來源 NAT 連接埠耗盡

在 Azure 中的部署可以與公用網際網路上，及/或公用 IP 空間中的 Azure 外部端點進行通訊。 當執行個體啟動輸出連線時，Azure 會動態地將私人 IP 位址對應到公用 IP 位址。 Azure 會建立此對應之後，輸出起源流程的傳回流量也可以到達的私人 IP 位址資料流程的起源。

針對每個輸出的連接，Azure 負載平衡器需要維護此對應的一段時間。 Azure 的多租用戶本質，維護此對應的每個輸出的流程，為每個 VM 可能會耗用大量資源。 因此，有一些限制; 會設定它們，根據 Azure 虛擬網路的組態。 或者，更精確地說就是，Azure VM 可以只讓輸出連線數目在指定的時間。 當達到這些限制時，則 VM，將無法進行更多輸出連線。

但是，就可以設定此行為。 如需有關 SNAT 和 SNAT 連接埠耗盡，請參閱[這篇文章](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)。

## <a name="measure-network-performance-on-azure"></a>量值在 Azure 上的網路效能

這篇文章中的效能最大值的數目相關的網路延遲 / 反覆存取兩個 Vm 之間的時間 (RTT)。 本節提供如何測試延遲/RTT 以及如何測試 TCP 效能與 VM 網路效能的一些建議。 您可以調整和效能測試前文所述使用這一節所述的技巧 TCP/IP 和 網路值。 您可以延遲、 MTU，MSS 和視窗大小值插入稍早提供的計算，並比較理論的最大值為您在測試期間觀察到的實際值。

### <a name="measure-round-trip-time-and-packet-loss"></a>量值的來回時間和封包遺失

TCP 效能會非常依賴 RTT 和封包遺失。 PING 公用程式可在 Windows 和 Linux 提供最簡單的方式，來測量 RTT 和封包遺失。 PING 的輸出會顯示來源與目的地之間的最小值/最大/平均延遲。 它也會顯示封包遺失。 PING 預設會使用 ICMP 通訊協定。 您可以使用 PsPing 測試 TCP RTT。 如需詳細資訊，請參閱 < [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)。

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>量值的 TCP 連線的實際輸送量

NTttcp 是用於測試的 Linux 或 Windows VM 的 TCP 效能的工具。 您可以變更 TCP 的各種設定，並接著使用 NTttcp 測試優點。 如需詳細資訊，請參閱這些資源：

- [頻寬/輸送量測試 (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [NTttcp 公用程式](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>量值的虛擬機器的實際頻寬

您可以測試的不同 VM 類型，加速網路、 效能等等，使用名為 iPerf 的工具。 iPerf 是也適用於 Linux 和 Windows。 iPerf 可以使用 TCP 或 UDP，若要測試的整體網路輸送量。 iPerf 的 TCP 輸送量測試會受到影響 （例如延遲和 RTT） 這篇文章所述的因素。 因此 UDP 可能會產生更好的結果，如果您只想要測試的最大輸送量。

如需詳細資訊，請參閱這些文章：

- [Expressroute 的網路效能疑難排解](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [如何驗證傳輸到虛擬網路的 VPN 輸送量](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>偵測沒有效率的 TCP 行為

在封包擷取，Azure 客戶可能會看到 TCP 封包來搭配 TCP 旗標 （SACK DUP 通知、 重新傳輸，和快速重新傳輸），可能表示網路效能問題。 這些封包會特別指出網路效率不足的情況所產生的封包遺失。 但是，封包遺失不一定會由 Azure 的效能問題所造成。 效能問題可能是應用程式的問題、 作業系統問題或可能不會直接與 Azure 平台的其他問題的結果。

此外，請記住一些重新傳輸和重複的通知，在網路上一般。 TCP 通訊協定建置可靠。 在封包擷取這些 TCP 封包的辨識項未必表示有系統的網路問題，除非它們過多。

儘管如此，這些封包類型是 TCP 輸送量未達到其最大效能，基於本文的其他小節所述的指示。

## <a name="next-steps"></a>後續步驟

既然您已了解 TCP/IP Azure Vm 的效能微調，您可能想要了解其他考量[計劃的虛擬網路](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-vnet-plan-design-arm)或[深入了解連接和設定虛擬網路](https://docs.microsoft.com/en-us/azure/virtual-network/).
