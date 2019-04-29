---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 192a6f4841e9dc3a478da5e4b53594362955ca71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456416"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>所有的 Azure VPN 閘道 SKU 上是否都支援 BGP？
否，Azure **VpnGw1****VpnGw2****VpnGw3****Standard** 和 **HighPerformance** VPN 閘道支援 BGP。 **基本** SKU。

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>可以使用 BGP 與 Azure Policy-Based VPN 閘道嗎？
否，僅路由 VPN 閘道支援 BGP。

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>可以使用私人 ASN (自發系統編號) 嗎？
是，針對您的內部部署網路和 Azure 虛擬網路，您可以使用您自己的公用 ASN 或私人 ASN。

### <a name="can-i-use-32-bit-asns-autonomous-system-numbers"></a>可以使用 32 位元的 ASN (自發系統編號) 嗎？
否，Azure VPN 閘道目前支援 16 位元的 ASN。

### <a name="are-there-asns-reserved-by-azure"></a>是否有 Azure 所保留的 ASN 嗎？
是，下列是 Azure 針對內部和外部對等互連所保留的 ASN︰

* 公用 ASN：8074、8075、12076
* 私人 ASN：65515、65517、65518、65519、65520

連接到 Azure VPN 閘道時，您無法針對內部部署 VPN 裝置指定這些 ASN。

### <a name="are-there-any-other-asns-that-i-cant-use"></a>有我不能使用的任何其他 ASN 嗎？
是，下列是 [IANA 保留](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)的 ASN 且無法在 Azure VPN 閘道上進行設定：

23456、64496-64511、65535-65551 和 429496729

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>內部部署 VPN 網路和 Azure VNet 可以使用相同的 ASN 嗎？
否，如果您要將內部部署網路和 Azure VNet 與 BGP 連接，必須在內部部署網路與 Azure VNet 之間指派不同 ASN。 Azure VPN 閘道已將預設 ASN 指派為 65515 (無論跨單位連線是否啟用 BGP)。 可以通过在创建 VPN 网关时分配不同 ASN，或者在创建网关后更改 ASN 来覆盖此默认值。 您必須將內部部署 ASN 指派給對應 Azure 區域網路閘道。

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Azure VPN 閘道會通告我哪些位址首碼？
Azure VPN 閘道會通告下列路由至您的內部部署 BGP 裝置︰

* 您的 VNet 位址首碼
* 每個本機網路閘道的位址首碼已連線到 Azure VPN 閘道
* 從其他 BGP 對等互連工作階段識別的路由已連線到 Azure VPN 閘道， **除了預設路由或與任何 VNet 首碼重疊的路由**。

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>我可以向 Azure VPN 閘道公告多少個前置詞？
我們最多支援 4000 個前置詞。 如果前置詞數目超過此限制，則會捨棄 BGP 工作階段。

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>可以公告 Azure VPN 閘道的預設路由 (0.0.0.0/0) 嗎？
是。

請注意，這會強制所有 VNet 輸出流量流向您的內部部署站台，而且會阻礙 VNet VM 直接接受來自網際網路的公用通訊，例如從網際網路到 VM 的 RDP 或 SSH。

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>可以公告確切的前置詞做為我的虛擬網路前置詞嗎？

是，公告相同的前置詞做為任何一個虛擬網路位址前置詞，將由 Azure 平台進行封鎖或篩選。 但是，可播发属于虚拟网络内所拥有内容超集的前缀。 

例如，您的虛擬網路使用了位址空間 10.0.0.0/16，而您可以公告 10.0.0.0/8。 但您無法公告 10.0.0.0/16 或 10.0.0.0/24。

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>可以與我的 VNet 對 VNet 連線搭配使用 BGP 嗎？
是，您可以針對跨單位連線和 VNet 對 VNet 連線使用 BGP。

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>可以針對我的 Azure VPN 閘道混合使用 BGP 和非 BGP 連線嗎？
是，針對相同的 Azure VPN 閘道，您可以混合使用 BGP 和非 BGP 連線。

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Azure VPN 閘道是否支援 BGP 傳輸路由？
是，可支援 BGP 傳輸路由，但例外狀況為 Azure VPN 閘道 **不會** 公告其他的 BGP 對等互連的預設路由。 若要啟用跨多個 Azure VPN 閘道的路由傳輸，您必須在所有中繼 VNet 對 VNet 連線上啟用 BGP。 如需詳細資訊，請參閱[關於 BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md)。

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>是否可在 Azure VPN 閘道與我的內部部署網路之間擁有多個通道？
是，您可在 Azure VPN 閘道與內部部署網路之間建立多個 S2S VPN 通道。 請注意，所有這些通道將會計入您 Azure VPN 閘道的通道總數，而您必須在這兩個通道上啟用 BGP。

例如，如果您在 Azure VPN 閘道與其中一個內部部署網路之間有兩個備援通道，它們會在您的 Azure VPN 閘道的總配額 (標準為 10，HighPerformance 為 30) 中耗用 2 個通道。

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>在两个使用 BGP 的 Azure VNet 之间能否有多个隧道？
是，但是主動-主動組態中必須有至少一個虛擬網路閘道。

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>能否在 ExpressRoute/S2S VPN 共存配置中对 S2S VPN 使用 BGP？
是。 

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Azure VPN 閘道會對 BGP 對等互連 IP 使用什麼位址？
Azure VPN 閘道會配置針對虛擬網路定義的 GatewaySubnet 範圍的單一 IP 位址。 默认情况下，它是该范围的倒数第二个地址。 例如，如果您的 GatewaySubnet 是 10.12.255.0/27，範圍從 10.12.255.0 到 10.12.255.31，則 Azure VPN 閘道上的 BGP 對等互連 IP 位址會是 10.12.255.30。 当列出 Azure VPN 网关信息时，可以找到此信息。

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>我的 VPN 裝置上的 BGP 對等互連 IP 位址有哪些需求？
您內部部署 BGP 對等互連位址 **不得** 與您的 VPN 裝置的公用 IP 位址相同。 在 VPN 裝置上針對 BGP 對等互連 IP 使用不同的 IP 位址。 它可以是指派給裝置上的回送介面的位址，但請注意，它不能是 APIPA (169.254.x.x) 位址。 在代表位置的對應本機網路閘道中指定這個位址。

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>使用 BGP 時，應將區域網路閘道的位址首碼指定為什麼？
Azure 區域網路閘道會指定內部部署網路的起始位址首碼。 若具有 BGP，您必須配置 BGP 對等互連 IP 位址的主機首碼 (/32 首碼) 作為該內部部署網路的位址空間。 如果 BGP 對等互連 IP 為 10.52.255.254，您應該指定「10.52.255.254/32」作為代表此內部部署網路的區域網路閘道的 localNetworkAddressSpace。 這是為了確保 Azure VPN 閘道透過 S2S VPN 通道建立 BGP 工作階段。

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>我應該將什麼加入我的 BGP 對等互連工作階段的內部部署 VPN 裝置？
您應該在指向 IPsec S2S VPN 通道的 VPN 裝置上加入 Azure BGP 對等互連 IP 位址的主機路由。 例如，如果 Azure VPN 對等互連 IP 是「10.12.255.30」，您應該加入 VPN 裝置上具有比對 IPsec 通道介面的躍點介面的「10.12.255.30」主機路由。
