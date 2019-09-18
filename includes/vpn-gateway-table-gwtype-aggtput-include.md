---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 65c1011e6e005c190d1ae5d51fdd009f66a20956
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70919651"
---
|**SKU**   | **S2S/VNet-to-VNet<br>通道** | **P2S<br> SSTP 連線** | **P2S<br> IKEv2/OpenVPN 連線** | **彙總<br>輸送量基準測試** | **BGP** | **Zone-redundant** |
|---       | ---        | ---       | ---            | ---       | --- | --- |
|**基本** | 最大 10    | 最大 128  | 不支援  | 100 Mbps  | 不支援| 否 |
|**VpnGw1**| 最大 30*   | 最大 128  | 最大 250       | 650 Mbps  | 支援 | 否 |
|**VpnGw2**| 最大 30*   | 最大 128  | 最大 500       | 1 Gbps    | 支援 | 否 |
|**VpnGw3**| 最大 30*   | 最大 128  | 最大 1000      | 1.25 Gbps | 支援 | 否 |
|**VpnGw1AZ**| 最大 30*   | 最大 128  | 最大 250       | 650 Mbps  | 支援 | yes |
|**VpnGw2AZ**| 最大 30*   | 最大 128  | 最大 500       | 1 Gbps    | 支援 | yes |
|**VpnGw3AZ**| 最大 30*   | 最大 128  | 最大 1000      | 1.25 Gbps | 支援 | yes |

(*) 如果您需要超過 30 個 S2S VPN 通道，請使用[虛擬 WAN](../articles/virtual-wan/virtual-wan-about.md)。

* 這些連線數限制是個別的。 例如，您在 VpnGw1 SKU 上可以有 128 個 SSTP 連線和 250 個 IKEv2 連線。

* 在 [價格](https://azure.microsoft.com/pricing/details/vpn-gateway) 頁面上可以找到價格資訊。

* 可以在 [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) 頁面上找到 SLA (服務等級協定) 資訊。

* 只有使用資源管理員部署模型的 VPN 閘道支援 VpnGw1、VpnGw2、VpnGw3、VpnGw1AZ、VpnGw2AZ 和 VpnGw3AZ。

* 基本 SKU 會被視為舊版 SKU。 基本 SKU 有某些功能限制。 您無法調整使用基本 SKU 的閘道大小來成為新的閘道 SKU，您必須改以變更為新的 SKU，而需要刪除並重新建立 VPN 閘道。 請先確認其有支援您需要的功能，再使用基本 SKU。

* 「彙總輸送量基準測試」是以透過單一閘道彙總之多個通道的量值為基礎。 VPN 閘道的彙總輸送量基準是 S2S + P2S 的組合。 **如果您有許多 P2S 連線，S2S 連線即可能因為輸送量限制而受到負面影響。** 由於網際網路流量條件和您的應用程式行為，彙總輸送量基準測試不是保證的輸送量。

* 為了協助我們的客戶了解 VpnGw SKU 的相對效能，我們使用了可公開取得的 iPerf 和 CTSTraffic 工具來測量效能。 下表列出使用不同演算法的效能測試結果。 如您所見，當我們針對 IPsec 加密和完整性使用 GCMAES256 演算法時，將會取得最佳效能。 當您針對 IPsec 加密和完整性使用 AES256 和 SHA256 時，我們會取得平均效能。 當您針對 IPsec 加密和完整性使用 DES3 和 SHA256 時，我們會取得最低效能。

|**SKU**   | **使用的<br>演算法** | **觀察到的<br>輸送量** | **觀察到的<br>每秒封包數** |
|---       | ---                 | ---            | ---                    |
|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58,000<br>50,000<br>50,000|
|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55,000|
|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1.25 Gbps<br>550 Mbps<br>120 Mbps | 105,000<br>90,000<br>60,000|
|**VpnGw1AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58,000<br>50,000<br>50,000|
|**VpnGw2AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55,000|
|**VpnGw3AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1.25 Gbps<br>550 Mbps<br>120 Mbps | 105,000<br>90,000<br>60,000|
