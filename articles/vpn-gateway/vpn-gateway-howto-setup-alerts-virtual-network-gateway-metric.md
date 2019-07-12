---
title: 設定 Azure VPN 閘道度量的警示
description: 若要設定 VPN 閘道的計量警示的步驟
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: d57663f683ba4e2107ec6813a19fac7b2dcdd26a
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605220"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>設定 VPN 閘道度量的警示

這篇文章可協助您設定 Azure VPN 閘道度量的警示。 Azure 監視器提供的功能來設定適用於 Azure 資源的警示。 您可以設定虛擬網路閘道的 「 VPN 」 類型的警示。


|**度量**   | **單位** | **資料粒度** | **說明** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| 位元組/秒  | 5 分鐘| 平均閘道上的所有站對站連線的合併的頻寬的使用量。     |
|**P2SBandwidth**| 位元組/秒  | 1 分鐘  | 平均閘道上的所有點對站連線的合併的頻寬的使用量。    |
|**P2SConnectionCount**| Count  | 1 分鐘  | 在閘道上的點對站連線的計數。   |
|**TunnelAverageBandwidth** | 位元組/秒    | 5 分鐘  | 在閘道上建立的通道的平均頻寬使用量。 |
|**TunnelEgressBytes** | 位元組 | 5 分鐘 | 在閘道上建立的通道上的連出流量。   |
|**TunnelEgressPackets** | Count | 5 分鐘 | 在閘道上建立的通道上的傳出封包數目。   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 分鐘 | 輸出封包計數卸除的流量選取器捨棄所造成的通道上。 |
|**TunnelIngressBytes** | 位元組 | 5 分鐘 | 在閘道上建立的通道上的連入流量。   |
|**TunnelIngressPackets** | Count | 5 分鐘 | 在閘道上建立的通道上的連入封包數目。   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 分鐘 | 卸除的流量選取器捨棄所造成的通道上的連入封包數目。 |


## <a name="setup"></a>設定使用 Azure 入口網站，根據計量的 Azure 監視器警示

下列範例步驟會在虛擬網路的閘道上建立警示：

- **計量：** TunnelAverageBandwidth
- **條件：** 頻寬 > 10 個位元組/秒
- **視窗：** 5 分鐘
- **警示的動作：** Email



1. 移至虛擬網路閘道資源，然後選取**警示**從**監視** 索引標籤。然後建立新的警示規則，或編輯現有的警示規則。

   ![選取項目建立警示規則](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "建立")

2. 選取您的 VPN 閘道的資源。

   ![[選取] 按鈕和資源的清單中的 VPN 閘道](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "選取")

3. 選取計量設定警示。

   ![度量的清單中選取公制](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "選取")
4. 設定訊號邏輯。 有三個元件：

    a. **維度**：如果計量具有維度，您可以選取特定維度值，以便警示評估總該維度的資料。 這些都是選擇性的。

    b. **條件**：這是要評估的度量值的作業。

    c. **時間**:指定的計量資料，資料粒度和要評估警示的時間週期。

   ![詳細資料設定訊號邏輯](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "選取")

5. 若要檢視設定的規則，請選取**管理警示規則**。

   ![管理警示規則 按鈕](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "選取")

## <a name="next-steps"></a>後續步驟

若要設定通道診斷記錄的警示，請參閱[VPN 閘道診斷記錄檔上設定警示](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)。
