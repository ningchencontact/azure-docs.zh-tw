---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 03af5efcd4a37203a82db503f8bc602b33de734d
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226544"
---
|**SKU**   | **S2S/VNet-to-VNet<br>通道** | **P2S<br> SSTP 連線** | **P2S<br> IKEv2 連線** | **彙總<br>輸送量基準測試** |
|---       | ---        | ---       | ---            | ---       |
|**VpnGw1**| 最大 30*   | 最大 128  | 最大 250       | 650 Mbps  |
|**VpnGw2**| 最大 30*   | 最大 128  | 最大 500       | 1 Gbps    |
|**VpnGw3**| 最大 30*   | 最大 128  | 最大 1000      | 1.25 Gbps |
|**基本** | 最大 10    | 最大 128  | 不支援  | 100 Mbps  | 

(*) 如果您需要超過 30 個 S2S VPN 通道，請使用[虛擬 WAN](../articles/virtual-wan/virtual-wan-about.md)。

* VPN 閘道的彙總輸送量基準是 S2S + P2S 的組合。 **如果您有許多 P2S 連線，S2S 連線即可能因為輸送量限制而受到負面影響。** 「彙總輸送量基準測試」是以透過單一閘道彙總之多個通道的量值為基礎。 由於網際網路流量條件和您的應用程式行為，這不是保證的輸送量。

* 這些連線數限制是個別的。 例如，您在 VpnGw1 SKU 上可以有 128 個 SSTP 連線和 250 個 IKEv2 連線。

* 在 [價格](https://azure.microsoft.com/pricing/details/vpn-gateway) 頁面上可以找到價格資訊。

* 可以在 [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) 頁面上找到 SLA (服務等級協定) 資訊。

* 只有使用資源管理員部署模型的 VPN 閘道支援 VpnGw1、VpnGw2 和 VpnGw3。
