---
title: "連接到 Azure 的內部部署網路 |Microsoft 文件"
description: "說明並比較不同的方法可用來連接到 Microsoft 雲端服務，例如 Azure、 Office 365 和 Dynamics CRM Online。"
services: 
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: 
ms.assetid: 294d39ad-40e0-476a-a362-57911b1172b7
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: cherylmc
ms.openlocfilehash: 37d83d3b6dea1763d85f2411816ba2fee4279100
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2018
---
# <a name="connecting-your-on-premises-network-to-azure"></a>在內部部署網路連接到 Azure
Microsoft 提供數種類型的雲端服務。 雖然您可以透過公用網際網路連接到所有服務，您也可以連接到某些使用虛擬私人網路 (VPN) 通道，透過網際網路或透過直接、 私用連接到 Microsoft 的服務。 這篇文章可協助您判斷哪一個連接選項最符合您根據您使用的 Microsoft 雲端服務類型的需求。 大部分的組織使用多個連接類型，如下所述。

## <a name="connecting-over-the-public-internet"></a>透過公用網際網路連線
此連線類型會提供存取 Microsoft 雲端服務直接透過網際網路，如下所示。

![網際網路](./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "網際網路")

此連線通常是用來連接到 Microsoft 雲端服務的第一個類型。 下表列出此連接類型的優缺點。

| **優點** | **考量** |
| --- | --- |
| 不需要在內部部署網路的任何修改，只要所有用戶端裝置有無限制的存取所有 IP 位址和網際網路上的連接埠。 |雖然流量通常會使用 HTTPS 進行加密，它可以攔截在傳輸過程中因為周遊公用網際網路。 |
| 可以連接到公用網際網路中公開的所有 Microsoft 雲端服務。 |無法預期的延遲因為連線周遊網際網路。 |
| 使用現有的網際網路連線。 | |
| 不需要任何連線裝置的管理。 | |

此連接會有任何連線或頻寬成本，因為您使用現有的網際網路連線。

## <a name="connecting-with-a-point-to-site-connection"></a>連接點對站連接
這種連接類型透過網際網路，提供存取某些 Microsoft 雲端服務，透過安全通訊端通道通訊協定 」 (SSTP) 通道，如下所示。

![P2S](./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "點對站連接")

連線會透過現有的網際網路連線，但需要使用 Azure VPN 閘道。 下表列出此連接類型的優缺點。

| **優點** | **考量** |
| --- | --- |
| 不需要在內部部署網路的任何修改，只要所有用戶端裝置有無限制的存取所有 IP 位址和網際網路上的連接埠。 |使用 IPSec 的流量都會加密，但它可以攔截在傳輸過程中因為周遊公用網際網路。 |
| 使用現有的網際網路連線。 |無法預期的延遲因為連線周遊網際網路。 |
| 最多 200 Mb/s 每個閘道的輸送量。 |需要建立及管理的個別連接內部網路上的每個裝置之間的每個裝置需要連接到每個閘道。 |
| 可以用來連接到 Azure 的服務可以連接到 Azure 虛擬網路 (VNet)，例如 Azure 虛擬機器和 Azure 雲端服務。 |需要最基本 Azure VPN 閘道的進行中的管理。 |
| 不能用來連接到 Microsoft Office 365 或 Dynamics CRM Online。 | |
| 不能用來連接到無法連線至 VNet 的 Azure 服務。 | |

深入了解[VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)服務，其[定價](https://azure.microsoft.com/pricing/details/vpn-gateway)，和輸出的資料傳輸[定價](https://azure.microsoft.com/pricing/details/data-transfers)。

## <a name="connecting-with-a-site-to-site-connection"></a>使用站對站連接進行連接
這種連接類型透過網際網路，提供存取某些 Microsoft 雲端服務，透過 IPSec 通道，如下所示。

![S2S](./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "站對站連接")

連線會透過現有的網際網路連線，但需要使用與其相關聯的定價和傳出資料傳輸定價 Azure VPN 閘道。 下表列出此連接類型的優缺點。

| **優點** | **考量** |
| --- | --- |
| 在內部部署網路上的所有裝置可以與連接至 VNet，因此不需要個別的連線的每個裝置設定的 Azure 服務進行都通訊。 |使用 IPSec 的流量都會加密，但它可以攔截在傳輸過程中因為周遊公用網際網路。 |
| 使用現有的網際網路連線。 |無法預期的延遲因為連線周遊網際網路。 |
| 可用來連接到 Azure 的服務，可連接到虛擬機器之類的 VNet 和雲端服務。 |必須設定及管理驗證 VPN 裝置 * 內部部署。 |
| 最多 200 Mb/s 每個閘道的輸送量。 |需要最基本 Azure VPN 閘道的進行中的管理。 |
| 可以強制起始從雲端虛擬機器透過內部網路，用於檢查和記錄使用使用者定義的路由或邊界閘道通訊協定 (BGP) 的輸出流量 * *。 |不能用來連接到 Microsoft Office 365 或 Dynamics CRM Online。 |
| 不能用來連接到無法連線至 VNet 的 Azure 服務。 | |
| 如果您使用會起始連線在內部部署裝置的服務，而且您的安全性原則需要它，您可能需要在內部部署網路與 Azure 之間的防火牆。 | |

* * 檢視的清單[驗證 VPN 裝置](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable)。
* * * 深入了解使用[使用者定義的路由](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)或[BGP](../vpn-gateway/vpn-gateway-bgp-overview.md)強制從 Azure Vnet 路由至內部部署裝置。

## <a name="connecting-with-a-dedicated-private-connection"></a>使用專用的私人連接進行連接
這種連接類型提供存取所有的 Microsoft 雲端服務透過專用私人連接至 Microsoft 不會周遊網際網路，如下所示。

![ER](./media/guidance-connecting-your-on-premises-network-to-azure/er.png "ExpressRoute 連線")

連線需要使用 ExpressRoute 服務和連線提供者的連接。 下表列出此連接類型的優缺點。

| **優點** | **考量** |
| --- | --- |
| 透過公用網際網路無法流量攔截在傳輸過程中，因為使用服務提供者的專用的連接。 |需要在內部部署路由器管理。 |
| 最高 10 Gb/s 每個 ExpressRoute 電路和輸送量最多 2 Gb/s 至每個閘道的頻寬。 |需要連線服務提供者的專用的連接。 |
| 可預測延遲，所以它不會周遊網際網路的 Microsoft 專用的連接。 |（如果連接到 Vnet 的循環），則可能需要最少的一或多個 Azure VPN 閘道進行中的管理。 |
| 雖然您可以將流量加密，如有需要，則不需要加密的通訊。 |如果您使用會起始連線在內部部署裝置的雲端服務，您可能需要在內部部署網路與 Azure 之間的防火牆。 |
| 直接連接到所有的 Microsoft 雲端服務，與少數的例外狀況 *。 |需要網路位址轉譯 (NAT) 的內部 IP 位址的服務無法連線至 VNet.* * 輸入的 Microsoft 資料中心 |
| 可以強制從雲端虛擬機器透過內部網路，用於檢查和記錄使用 BGP 起始的輸出流量。 | |

* * 檢視[服務清單](../expressroute/expressroute-faqs.md#supported-services)，不能與 ExpressRoute。 連線到 Office 365，您必須核准您的 Azure 訂用帳戶。  請參閱[for Office 365 的 Azure ExpressRoute](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1)文件以取得詳細資料。
* * * 深入了解 ExpressRoute [NAT](../expressroute/expressroute-nat.md)需求。

深入了解[ExpressRoute](../expressroute/expressroute-introduction.md)，及其相關聯[定價](https://azure.microsoft.com/pricing/details/expressroute)，和[連線提供者](../expressroute/expressroute-locations.md#locations)。

## <a name="additional-considerations"></a>其他考量
* 上述選項的數個具有不同的最大限制它們可以支援 VNet 連線、 閘道連線，以及其他條件。 建議您檢閱 Azure[網路限制](../azure-subscription-service-limits.md#networking-limits)來了解是否其中任何會影響您選擇要使用的連線類型。
* 如果您打算從站對站 VPN 連線的閘道連接至相同的 VNet 與 ExpressRoute 閘道，您應該熟悉重要的條件約束第一次。 請參閱[共存設定 ExpressRoute 以及站台對站台連線](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations)文件以取得更多詳細資料。

## <a name="next-steps"></a>後續步驟
下列資源會說明如何實作本文涵蓋的連線類型。

* [實作的點對站連接](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [實作站對站連線](guidance-hybrid-network-vpn.md)
* [實作專用的私人連線](guidance-hybrid-network-expressroute.md)
* [實作專用的私人連線具有高可用性的站對站連線](guidance-hybrid-network-expressroute-vpn-failover.md)
