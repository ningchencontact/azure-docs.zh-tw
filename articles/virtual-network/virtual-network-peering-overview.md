---
title: Azure 虛擬網路對等互連
titlesuffix: Azure Virtual Network
description: 了解 Azure 中的虛擬網路對等互連。
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: anavin
ms.openlocfilehash: 22521abbc341fa9999738dd51301d3f84c18627a
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919316"
---
# <a name="virtual-network-peering"></a>虛擬網路對等互連

虛擬網路對等互連可讓您順暢地連接 Azure[虛擬網路](virtual-networks-overview.md)。 經過對等互連後，所有虛擬網路就可以作為一個整體來進行連線。 在對等互連之虛擬網路中的虛擬機器之間的流量，會透過 Microsoft 骨幹基礎結構路由傳送，其原理就像在相同虛擬網路中的虛擬機器之間，流量只會透過「私人」IP 位址來路由傳送。 Azure 支援：
* VNet 對等互連 - 連接相同 Azure 區域內的 VNet
* 全球 VNet 對等互連 - 連接各 Azure 區域內的 VNet

使用虛擬網路對等互連 (不論是本機還是全球) 的優點包括︰

* 對等互連虛擬網路之間的網路流量為私用。 虛擬網路之間的流量會保留在 Microsoft 骨幹網路上。 虛擬網路之間的通訊不需要公用網際網路、閘道或加密。
* 不同虛擬網路的資源之間具有低延遲、高頻寬連線。
* 一旦虛擬網路對等互連，某個虛擬網路中的資源與不同虛擬網路中資源通訊的能力。
* 跨 Azure 訂用帳戶、部署模型和跨 Azure 區域傳輸資料的能力。
* 能夠將透過 Azure Resource Manager 所建立的虛擬網路對等互連，或將透過 Resource Manager 所建立的虛擬網路與透過傳統部署模型所建立的虛擬網路對等互連。 若要深入了解 Azure 部署模型，請參閱[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
* 建立對等互連時或建立對等互連之後，虛擬網路中的資源沒有停機時間。

## <a name="connectivity"></a>連線能力

虛擬網路對等互連後，任一虛擬網路中的資源可以直接與對等互連虛擬網路中的資源連線。

在相同區域的對等互連虛擬網路中，虛擬機器之間的網路延遲與單一虛擬網路中的網路延遲相同。 網路輸送量為依照虛擬機器大小，按比例允許的頻寬。 對等互連內的頻寬沒有其他額外限制。

對等互連之虛擬網路中的虛擬機器之間的流量，會透過 Microsoft 骨幹基礎結構直接路由傳送，而不會透過閘道或透過公用網際網路來傳送。

如有需要，可以將網路安全性群組套用在任一個虛擬網路，以封鎖其他虛擬網路或子網路的存取權限。
設定虛擬網路對等互連時，您可以開啟或關閉虛擬網路之間的網路安全性群組規則。 如果您開啟對等互連的虛擬網路 (預設選項) 之間的完整連線，您可以將網路安全性群組套用至特定子網路或虛擬機器，以封鎖或拒絕特定的存取。 若要深入了解網路安全性群組，請參閱[網路安全性群組概觀](security-overview.md)。

## <a name="service-chaining"></a>服務鏈結

您可以設定使用者定義的路由，指向對等互連虛擬網路中當作「下一個躍點」IP 位址的虛擬機器，或指向虛擬網路閘道，以啟用服務鏈結。 服務鏈結可讓您透過使用者定義的路由，將流量從一個虛擬網路導向對等互連虛擬網路中的虛擬設備或虛擬網路閘道。

您可以部署中樞和輪輻網路，其中的中樞虛擬網路可以裝載基礎結構元件，例如網路虛擬設備或 VPN 閘道。 所有輪輻虛擬網路可以接著與中樞虛擬網路對等互連。 流量可以通過在中樞虛擬網路中的網路虛擬設備或 VPN 閘道。 

虛擬網路對等互連可讓使用者定義路由中的下一個躍點成為對等互連虛擬網路中虛擬機器的 IP 位址或 VPN 閘道。 不過，您不能使用指定 ExpressRoute 閘道作為下一個躍點類型的使用者定義路由，在虛擬網路之間路由傳送。 若要深入了解使用者定義的路由，請參閱[使用者定義的路由概觀](virtual-networks-udr-overview.md#user-defined)。 若要了解如何建立中樞和輪輻網路拓撲，請參閱[中樞和輪輻網路拓撲](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="gateways-and-on-premises-connectivity"></a>閘道及內部部署連線能力

不論每個虛擬網路是否與其他虛擬網路對等互連，它們仍可以擁有自己的閘道並使用它來連線至內部部署網路。 即使虛擬網路已對等互連，您也可以使用閘道來設定[虛擬網路對虛擬網路連線](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

當針對虛擬網路內部連線的兩個選項已設定時，虛擬網路之間的流量將會透過對等互連設定流動 (也就是透過 Azure 骨幹)。

當虛擬網路已對等互連時，您也可以將對等互連虛擬網路中的閘道設定為內部部署網路的傳輸點。 在这种情况下，使用远程网关的虚拟网络没有自己的网关。 虛擬網路只能擁有一個閘道。 閘道可以是本機或遠端閘道 (在對等互連的虛擬網路中)，如下圖所示：

![虛擬網路對等互連傳輸](./media/virtual-networks-peering-overview/figure04.png)

VNet 對等互連和全域 VNet 對等互連支援閘道傳輸。 只有當閘道位於虛擬網路 (Resource Manager) 時，才支援透過不同部署模型 （Resource Manager 和傳統） 建立虛擬網路之間的閘道傳輸。 若要深入了解如何使用閘道來進行傳輸，請參閱[設定 VPN 閘道以在虛擬網路對等互連中進行傳輸](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

當共用單一 Azure ExpressRoute 連線的虛擬網路已對等互連時，它們之間的流量會經過對等互連關聯性 (也就是透過 Azure 骨幹網路)。 您依然可以在每個虛擬網路中使用本機閘道來連線內部部署線路。 此外，您也可以使用共用閘道並設定內部部署連線的傳輸。

## <a name="troubleshoot"></a>疑難排解

若要確認虛擬網路對等互連，您可以針對虛擬網路的任何子網路中的網路介面[檢查有效的路由](diagnose-network-routing-problem.md)。 如果虛擬網路對等互連存在，則虛擬網路內的所有子網路都具有下一個躍點類型為「VNet 對等互連」的路由 (對每個對等互連的虛擬網路中的每個位址空間而言)。

您也可以使用網路監看員的[連線能力檢查](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，針對對等互連虛擬網路中的虛擬機器連線能力進行疑難排解。 連線能力檢查可讓您查看流量是以何種方式從來源虛擬機器的網路介面傳送至目的地虛擬機器的網路介面。

您也可以嘗試[虛擬網路對等互連問題的疑難排解員](https://support.microsoft.com/help/4486956/troubleshooter-for-virtual-network-peering-issues)。

## <a name="requirements-and-constraints"></a>需求和限制

只有在為虛擬網路建立全域的對等互連時，會受到下列限制：
- 一個虛擬網路中的資源無法與全域對等互連的虛擬網路中的基本的內部負載平衡器的前端 IP 位址通訊。 基本 Load Balancer 支援只存在於相同區域內。 標準 Load Balancer 支援存在，VNet 對等互連和全域 VNet 對等互連。 使用全域 VNet 對等互連上無法運作的基本負載平衡器服務會記載[這裡。](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)

若要深入了解需求和限制，請參閱[虛擬網路對等互連需求和限制](virtual-network-manage-peering.md#requirements-and-constraints)。 若要了解您可為虛擬網路建立之對等互連數目的限制，請參閱 [Azure 網路限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 

## <a name="permissions"></a>權限

若要了解建立虛擬網路對等互連所需的權限，請閱讀[虛擬網路對等互連權限](virtual-network-manage-peering.md#permissions)。

## <a name="pricing"></a>價格

我們會針對使用虛擬網路對等互連連線的輸入和輸出流量收取少許費用。 如需有關 VNet 對等互連和全域 VNet 對等互連定價的詳細資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/virtual-network)。

閘道傳輸是一個對等互連的屬性，可讓虛擬網路利用對等虛擬網路中的 VPN 閘道，來進行跨部署或 VNet 對 VNet 連線。 在此案例中，通過遠端閘道的流量需收取 [VPN 閘道費用](https://azure.microsoft.com/pricing/details/vpn-gateway/)，且不會產生 [VNet 對等互連費用](https://azure.microsoft.com/pricing/details/virtual-network)。 比方說，如果 VNetA 具有內部部署連線的 VPN 閘道之 VNetB 對等互連至 VNetA 適當設定的屬性，在內部部署環境中之 VNetB 流量是只需付費輸出每個 VPN 閘道定價。 VNet 對等互連費用將不適用。 了解如何[為虛擬網路對等互連設定 VPN 閘道傳輸](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="next-steps"></a>後續步驟

* 虛擬網路對等互連是建立於虛擬網路之間，而這兩個虛擬網路是透過存在於相同或不同訂用帳戶中的相同或不同部署模型所建立。 完成下列其中一個案例的教學課程：

    |Azure 部署模型             | 訂用帳戶  |
    |---------                          |---------|
    |兩者皆使用 Resource Manager              |[相同](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[不同](create-peering-different-subscriptions.md)|
    |一個使用 Resource Manager、一個使用傳統部署模型  |[相同](create-peering-different-deployment-models.md)|
    |                                   |[不同](create-peering-different-deployment-models-subscriptions.md)|

* 了解如何建立[中樞和輪輻網路拓撲](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)。
* 了解所有[虛擬網路對等互連設定，以及如何變更它們](virtual-network-manage-peering.md)。
* 透過我們的 [VNet 對等互連常見問題集](virtual-networks-faq.md#vnet-peering)，取得 VNet 對等互連和全域 VNet 對等互連常見問題的解答
