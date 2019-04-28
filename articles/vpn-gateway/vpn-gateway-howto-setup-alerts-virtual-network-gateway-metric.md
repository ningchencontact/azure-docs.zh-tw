---
title: 如何設定 Azure VPN 閘道度量的警示
description: 若要設定 VPN 閘道的計量警示的步驟
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 890b096acba601ec20efaac21155da84e77a1f31
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769462"
---
# <a name="setting-up-alerts-on-vpn-gateway-metrics"></a>設定 VPN 閘道度量的警示

這篇文章可協助您設定 VPN 閘道計量的警示。 Azure 監視器提供的功能來設定適用於 Azure 資源的警示。 警示可以設定為 「 VPN 」 類型的虛擬網路閘道。


|**度量**   | **單位** | **資料粒度** | **說明** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| 位元/秒  | 5 分鐘| 平均閘道上的所有站對站連線的合併的頻寬的使用量。     |
|**P2SBandwidth**| 位元/秒  | 1 分鐘  | 平均閘道上的所有點對站連線的合併的頻寬的使用量。    |
|**P2SConnectionCount**| 計數  | 1 分鐘  | 在閘道上的計數的 P2S 連線。   |
|**TunnelAverageBandwidth** | 位元/秒    | 5 分鐘  | 在閘道上建立的通道的平均頻寬使用量。 |
|**TunnelEgressBytes** | 位元組 | 5 分鐘 | 在閘道上建立的通道上的連出流量。   |
|**TunnelEgressPackets** | 計數 | 5 分鐘 | 在閘道上建立的通道上的傳出封包數目。   |
|**TunnelEgressPacketDropTSMismatch** | 計數 | 5 分鐘 | 輸出封包的數目卸除 TS 不相符所造成的通道上。 |
|**TunnelIngressBytes** | 位元組 | 5 分鐘 | 在閘道上建立的通道上的連入流量。   |
|**TunnelIngressPackets** | 計數 | 5 分鐘 | 在閘道上建立的通道上的連入封包數目。   |
|**TunnelIngressPacketDropTSMismatch** | 計數 | 5 分鐘 | 卸除 TS 不相符所造成的通道上的連入封包數目。 |


## <a name="setup"></a>根據計量使用入口網站設定 Azure 監視器警示

下列範例步驟會建立警示，在虛擬網路的閘道： <br>

**計量：** 通道的平均頻寬 <br>
**條件：** 頻寬 > 10 個位元組/秒 <br>
**視窗：** 5 分鐘 <br>
**警示的動作：** 電子郵件 <br>



1. 瀏覽至虛擬網路閘道資源並從 [監視] 索引標籤中，選取 [警示]，然後建立新的警示規則或編輯現有的警示規則。

![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "建立")

2. 選取您的 VPN 閘道的資源。

![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "選取")

3. 選取 設定警示的計量![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "選取")
4. 設定訊號邏輯。 有三個訊號邏輯元件：

    a. 維度：如果計量具有維度，就可以選取特定維度值，讓警示只會評估該維度的資料。 這些都是選擇性的。<br>
    b. 條件：要評估的度量值的作業。<br>
    c. 時間：指定的計量資料，資料粒度和要評估警示的時間週期。<br>

![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "選取")

5. 若要檢視設定的規則，請按一下 [管理警示規則]![點對站](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "選取")

## <a name="next-steps"></a>後續步驟

若要設定通道診斷記錄檔的警示，請參閱[如何設定 VPN 閘道診斷記錄的警示](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)。
