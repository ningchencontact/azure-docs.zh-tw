---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/10/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8c3f727c6154a0364f151d22000d2684c361676a
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2018
ms.locfileid: "39037205"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Azure 虛擬網路閘道 (VPN 閘道) 與 Azure 虛擬 WAN vpngateway 之間有什麼差異？

虛擬 WAN 提供大規模站對站連線，是針對輸送量、延展性和易用性而建置的。 CPE 分支裝置會自動佈建，並且連線到 Azure 虛擬 WAN。 這些裝置都可以從 SD-WAN 和 VPN 合作夥伴日益成長的生態系統中取用。

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>啟動時支援哪些裝置提供者 (虛擬 WAN 合作夥伴)？ 

目前，Citrix 與 Riverbed 支援完全自動化的虛擬 WAN 體驗。 未來幾個月會有更多合作夥伴上線，包括：Nokia Nuage、Palo Alto 和 Checkpoint。 如需詳細資訊，請參閱[虛擬 WAN 合作夥伴](https://aka.ms/virtualwan)。

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>我是否必須使用偏好的合作夥伴裝置？

否。 您可以使用任何支援 VPN 的裝置，該裝置需符合 IKEv2 IPsec 支援的預覽需求。

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>虛擬 WAN 合作夥伴如何將與 Azure 虛擬 WAN 的連線自動化？

軟體定義的連線解決方案通常會使用控制器或裝置佈建中心，來管理它們的分支裝置。 控制器可以使用 Azure API，將與 Azure 虛擬 WAN 的連線自動化。 如需運作方式的詳細資訊，請參閱[虛擬 WAN 合作夥伴自動化](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)。

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>虛擬 WAN 是否會變更任何現有的連線功能？   

現有的 Azure 連線功能不會有任何變更。

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>虛擬 WAN 是否有任何新的 Resource Manager 資源可用？
  
是，虛擬 WAN 導入了新的 Resource Manager 資源。 如需詳細資訊，請參閱[概觀](https://go.microsoft.com/fwlink/p/?LinkId=2004389)。

### <a name="are-there-any-special-requirements-to-join-the-preview"></a>加入預覽是否有任何特殊需求？ 

設定 Azure 虛擬 WAN 之前，您必須先在預覽版中註冊您的訂用帳戶。 若要註冊，您可以將內含訂用帳戶識別碼的電子郵件傳送到 <azurevirtualwan@microsoft.com>。 當訂用帳戶註冊完成之後，您會收到電子郵件。 在您收到訂用帳戶已註冊的確認之前，您都無法使用入口網站中的虛擬 WAN。

考量：

* 預覽僅限於 Azure 公用區域。
* 每個虛擬中樞支援多達 100 個連線。 每個連線是由 2 個通道組成，其組態為主動-主動。 通道會在 Azure 虛擬中樞 vpngateway 內終止。
* 如果符合以下情形，請考慮使用此預覽版本：
  * 您想要部署每個虛擬中樞少於 1 Gbps 的彙總頻寬。
  * 您的 VPN 裝置可支援路由型組態和 IKEv2 IPsec 連線。
  * 您想要使用 SD-WAN 進行探索，並且從啟動合作夥伴 (Riverbed 和 Citrix) 處操作分支裝置。<br>或<br>您想要設定分支到分支和分支到 Azure 的連線，包括內部部署裝置的組態管理。 (自我設定)

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Azure 虛擬 WAN 是否支援全域 VNet 對等互連？ 

 否。

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>連線到虛擬中樞的輪輻 VNet 之間，是否可以彼此通訊？

是。 您可以在連線到虛擬中樞的輪輻之間直接進行 VNet 對等互連。 如需詳細資訊，請參閱[虛擬網路對等互連](../articles/virtual-network/virtual-network-peering-overview.md)。

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>是否可以使用 Azure 虛擬 WAN 部署及使用我的最愛網路虛擬設備 (在 NVA VNet 中)？

是，您可以將最愛的網路虛擬設備 (NVA) VNet 連線至 Azure 虛擬 WAN，但是需要中樞在 GA 才會提供的路由傳送功能。 所有連線到 NVA VNet 的輪輻都必須額外連線到虛擬中樞。 

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>NVA VNet 是否可以有虛擬網路閘道？

否。 如果 NVA VNet 連線到虛擬中樞，則不能有虛擬網路閘道。 

### <a name="is-there-support-for-bgp"></a>是否支援 BGP？

是，有支援 BGP。 若要確保來自 NVA VNet 的路由會適當地公告，如果輪輻已連線至 NVA VNet，後者接著連線至虛擬中樞，則輪輻必須停用 BGP。 此外，請將輪輻 VNet 連線至虛擬中樞。

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>是否可以使用虛擬中樞的 UDR 來引導流量？

UDR 和路由傳送功能將在 GA 時提供。

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>虛擬 WAN 是否有任何授權或價格資訊？
 
預覽期間不會額外收費。 目前的 [Azure VPN 和輸出價格](https://azure.microsoft.com/pricing/details/vpn-gateway/)在預覽期間仍然有效。