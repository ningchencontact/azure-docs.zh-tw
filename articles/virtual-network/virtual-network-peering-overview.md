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
ms.date: 11/15/2019
ms.author: anavin
ms.openlocfilehash: 59854d7d46f533510bea97a6845554fc0ce83dbb
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328363"
---
# <a name="virtual-network-peering"></a>虛擬網路對等互連

Virtual network peering enables you to seamlessly connect networks in [Azure Virtual Network](virtual-networks-overview.md). The virtual networks appear as one for connectivity purposes. The traffic between virtual machines uses the Microsoft backbone infrastructure. Like traffic between virtual machines in the same network, traffic is routed through Microsoft's *private* network only.

Azure supports the following types of peering:

* Virtual network peering: Connect virtual networks within the same Azure region.
* Global virtual network peering: Connecting virtual networks across Azure regions.

使用虛擬網路對等互連 (不論是本機還是全球) 的優點包括︰

* 不同虛擬網路的資源之間具有低延遲、高頻寬連線。
* The ability for resources in one virtual network to communicate with resources in a different virtual network.
* The ability to transfer data between virtual networks across Azure subscriptions, Azure Active Directory tenants, deployment models, and Azure regions.
* The ability to peer virtual networks created through the Azure Resource Manager.
* The ability to peer a virtual network created through Resource Manager to one created through the classic deployment model. 若要深入了解 Azure 部署模型，請參閱[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
* 建立對等互連時或建立對等互連之後，虛擬網路中的資源沒有停機時間。

對等互連虛擬網路之間的網路流量為私用。 虛擬網路之間的流量會保留在 Microsoft 骨幹網路上。 虛擬網路之間的通訊不需要公用網際網路、閘道或加密。

## <a name="connectivity"></a>連線能力

For peered virtual networks, resources in either virtual network can directly connect with resources in the peered virtual network.

在相同區域的對等互連虛擬網路中，虛擬機器之間的網路延遲與單一虛擬網路中的網路延遲相同。 網路輸送量為依照虛擬機器大小，按比例允許的頻寬。 對等互連內的頻寬沒有其他額外限制。

對等互連之虛擬網路中的虛擬機器之間的流量，會透過 Microsoft 骨幹基礎結構直接路由傳送，而不會透過閘道或透過公用網際網路來傳送。

You can apply network security groups in either virtual network to block access to other virtual networks or subnets.
When configuring virtual network peering, either open or close the network security group rules between the virtual networks. If you open full connectivity between peered virtual networks, you can apply network security groups to block or deny specific access. Full connectivity is the default option. To learn more about network security groups, see [Security groups](security-overview.md).

## <a name="service-chaining"></a>服務鏈結

Service chaining enables you to direct traffic from one virtual network to a virtual appliance or gateway in a peered network through user-defined routes.

To enable service chaining, configure user-defined routes that point to virtual machines in peered virtual networks as the *next hop* IP address. User-defined routes could also point to virtual network gateways to enable service chaining.

You can deploy hub-and-spoke networks, where the hub virtual network hosts infrastructure components such as a network virtual appliance or VPN gateway. 所有輪輻虛擬網路可以接著與中樞虛擬網路對等互連。 Traffic flows through network virtual appliances or VPN gateways in the hub virtual network.

虛擬網路對等互連可讓使用者定義路由中的下一個躍點成為對等互連虛擬網路中虛擬機器的 IP 位址或 VPN 閘道。 You can't route between virtual networks with a user-defined route that specifies an Azure ExpressRoute gateway as the next hop type. 若要深入了解使用者定義的路由，請參閱[使用者定義的路由概觀](virtual-networks-udr-overview.md#user-defined)。 To learn how to create a hub and spoke network topology, see [Hub-spoke network topology in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>閘道及內部部署連線能力

Each virtual network, including a peered virtual network, can have its own gateway. A virtual network can use its gateway to connect to an on-premises network. You can also configure [virtual network-to-virtual network connections](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) by using gateways, even for peered virtual networks.

When you configure both options for virtual network interconnectivity, the traffic between the virtual networks flows through the peering configuration. The traffic uses the Azure backbone.

You can also configure the gateway in the peered virtual network as a transit point to an on-premises network. In this case, the virtual network that is using a remote gateway can't have its own gateway. A virtual network has only one gateway. The gateway is either a local or remote gateway in the peered virtual network, as shown in the following diagram:

![虛擬網路對等互連傳輸](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Both virtual network peering and global virtual network peering support gateway transit.

Gateway transit between virtual networks created through different deployment models is supported. The gateway must be in the virtual network in the Resource Manager model. 若要深入了解如何使用閘道來進行傳輸，請參閱[設定 VPN 閘道以在虛擬網路對等互連中進行傳輸](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

When you peer virtual networks that share a single Azure ExpressRoute connection, the traffic between them goes through the peering relationship. That traffic uses the Azure backbone network. 您依然可以在每個虛擬網路中使用本機閘道來連線內部部署線路。 Otherwise, you can use a shared gateway and configure transit for on-premises connectivity.

## <a name="troubleshoot"></a>疑難排解

To confirm that virtual networks are peered, you can check effective routes. Check routes for a network interface in any subnet in a virtual network. 如果虛擬網路對等互連存在，則虛擬網路內的所有子網路都具有下一個躍點類型為「VNet 對等互連」的路由 (對每個對等互連的虛擬網路中的每個位址空間而言)。 For more information, see [Diagnose a virtual machine routing problem](diagnose-network-routing-problem.md).

You can also troubleshoot connectivity to a virtual machine in a peered virtual network using Azure Network Watcher. A connectivity check lets you see how traffic is routed from a source virtual machine's network interface to a destination virtual machine's network interface. For more information, see [Troubleshoot connections with Azure Network Watcher using the Azure portal](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine).

You can also try the [Troubleshoot virtual network peering issues](virtual-network-troubleshoot-peering-issues.md).

## Constraints for peered virtual networks<a name="requirements-and-constraints"></a>

只有在為虛擬網路建立全域的對等互連時，會受到下列限制：

* Resources in one virtual network can't communicate with the front-end IP address of a Basic Internal Load Balancer (ILB)  in a globally peered virtual network.
* Some services that use a Basic load balancer don't work over global virtual network peering. For more information, see [What are the constraints related to Global VNet Peering and Load Balancers?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

For more information, see [Requirements and constraints](virtual-network-manage-peering.md#requirements-and-constraints). To learn more about the supported number of peerings, see [Networking limits](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="permissions"></a>使用權限

To learn about permissions required to create a virtual network peering, see [Permissions](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>價格

There's a nominal charge for ingress and egress traffic that uses a virtual network peering connection. For more information, see [Virtual Network pricing](https://azure.microsoft.com/pricing/details/virtual-network).

Gateway Transit is a peering property that enables a virtual network to utilize a VPN/ExpressRoute gateway in a peered virtual network. Gateway transit works for both cross premises and network-to-network connectivity. Traffic to the gateway (ingress or egress) in the peered virtual network incurs virtual network peering charges. For more information, see [VPN Gateway pricing](https://azure.microsoft.com/pricing/details/vpn-gateway/) for VPN gateway charges and ExpressRoute Gateway pricing for ExpressRoute gateway charges.

>[!NOTE]
> A previous version of this document stated that virtual network peering charges would not apply with Gateway Transit. It now reflects accurate pricing per the pricing page.

## <a name="next-steps"></a>後續步驟

* You can create a peering between two virtual networks. The networks can belong to the same subscription, different deployment models in the same subscription, or different subscriptions. 完成下列其中一個案例的教學課程：

    |Azure 部署模型             | Subscription  |
    |---------                          |---------|
    |兩者皆使用 Resource Manager              |[相同](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[不同](create-peering-different-subscriptions.md)|
    |一個使用 Resource Manager、一個使用傳統部署模型  |[相同](create-peering-different-deployment-models.md)|
    |                                   |[不同](create-peering-different-deployment-models-subscriptions.md)|

* To learn how to create a hub and spoke network topology, see [Hub-spoke network topology in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* To learn about all virtual network peering settings, see [Create, change, or delete a virtual network peering](virtual-network-manage-peering.md).
* For answers to common virtual network peering and global virtual network peering questions, see [VNet Peering](virtual-networks-faq.md#vnet-peering).
