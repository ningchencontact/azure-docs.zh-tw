---
title: 設計使用 Azure ExpressRoute 的高可用性 |Microsoft Docs
description: 此頁面會提供使用 Azure ExpressRoute 時的高可用性架構的建議。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4984b30daf6170873cad9472bfed2d879af57efe
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466655"
---
# <a name="designing-for-high-availability-with-expressroute"></a>設計使用 ExpressRoute 的高可用性

ExpressRoute 被設計為高可用性，以提供貨運公司客戶級 Microsoft 資源的私人網路連線。 換句話說，沒有單一的 Microsoft 網路內的 ExpressRoute 路徑中的失敗點。 若要將可用性最大化，客戶與您的 ExpressRoute 線路的服務提供者區段應該也建構以獲得高可用性。 在此文章，先讓我們了解用於建置強固網路連線使用 ExpressRoute 的網路架構考量，則我們來看看可協助您改善您的 ExpressRoute 線路的高可用性的微調功能。


## <a name="architecture-considerations"></a>架構方面的考量

下圖說明建議用來連接使用 ExpressRoute 線路的 ExpressRoute 線路的可用性最大化。

 [![1]][1]

高可用性，請務必維護整個端對端網路的 ExpressRoute 線路的備援功能。 換句話說，您需要維護您的內部部署網路內的備援性，並不應該危害服務提供者網路中的備援。 維護最小的備援表示避免單一網路失敗點。 具有備援電源和冷卻網路裝置會進一步改善高可用性。

### <a name="first-mile-physical-layer-design-considerations"></a>第一步實體層設計考量

 如果您終止兩者的主要和次要的連線的 ExpressRoute 線路上相同客戶內部部署設備 (CPE)，您危害的高可用性，您的內部部署網路內。 此外，如果您設定這兩個主要和次要連線透過相同的連接埠的 CPE （無論是終止不同 subinterfaces 下的兩個連線，或合併夥伴網路內的兩個連線） 時，您強制交易夥伴危害其網路區段上的高可用性。 下圖說明此侵入。

[![2]][2]

相反地，如果您終止主要及次要連線的 ExpressRoute 線路，在不同的地理位置，則您無法危害著您的連線的網路效能。 如果負載平衡在主要和次要連線，會終止在不同地理位置上的主動流量，可能大幅提升網路延遲之間兩個路徑會導致次佳的網路效能。 

異地備援的設計考量，請參閱[設計災害復原與 ExpressRoute][DR]。

### <a name="active-active-connections"></a>主動-主動連線

Microsoft 網路設定為操作的主動-主動模式中的 ExpressRoute 線路的主要和次要的連線。 不過，透過您的路由通告，您可以強制在主動-被動模式中運作的 ExpressRoute 線路的備援連線。 公告更明確的路由和 BGP，因為路徑前面加上用來製作優先於另一個路徑的常見技巧。

若要改善高可用性，建議您操作這兩個連線的主動-主動模式中的 ExpressRoute 線路。 如果您讓在主動-主動模式中操作連接時，Microsoft 網路會在每個流程為基礎的連線進行負載平衡流量。

執行 ExpressRoute 線路的主要和次要連線在主動-被動模式下面這兩個連線使用的路徑在失敗後失敗的風險。 在切換失敗的常見原因是缺乏的被動連線，以及公告過時的路由的被動連線的主動管理。

或者，ExpressRoute 線路的主要和次要連線以主動-主動模式執行，會導致大約一半的流程失敗，並開始重新路由傳送，ExpressRoute 連線發生失敗。 因此，主動-主動模式會很有幫助改善時間來復原 (MTTR)。

### <a name="nat-for-microsoft-peering"></a>適用於 Microsoft 對等互連的 NAT 

Microsoft 對等互連被設計用於公用端點之間的通訊。 因此情況下，在內部部署私人端點是網路位址轉譯 (Nat) 與客戶或合作夥伴網路上的公用 IP 之前他們透過 Microsoft 對等互連進行通訊。 假設您使用這兩個主要和次要連線在主動-主動模式中，位置和方式您 NAT 將會影響您復原下列其中一個 ExpressRoute 連線失敗的速度。 在下圖說明兩種不同的 NAT 選項：

[![3]][3]

在選項 1 中，NAT 會套用之後分割 ExpressRoute 的主要和次要連線之間的流量。 若要符合可設定狀態的 NAT 需求，獨立的 NAT 集區會使用主要和次要裝置之間，以便回傳流量會抵達相同的邊緣裝置透過此流程輸出。

在選項 2 中，常見的 NAT 集區會使用之前先分割 ExpressRoute 的主要和次要連線之間的流量。 請務必要如此區分，常見的 NAT 集區之前先分割流量並不表示簡介單點失敗，進而危害的高可用性。

使用 ExpressRoute 連線發生失敗時，選項 1，能夠連線到對應的 NAT 集區已中斷。 因此，中斷的流程只會重新建立透過 TCP 或下列對應的視窗逾時的應用程式層。 如果有任何一個 NAT 集區使用前端的任何內部部署伺服器，而且從 Azure 無法達到如果對應的連線失敗，內部部署伺服器，直到連線修正的問題。

而使用選項 2 中，NAT 連線到。 即使主要或次要的連線失敗 因此，網路層級本身可以重新路由傳送封包，並協助更快速的復原失敗後。 

> [!NOTE]
> 如果您使用 NAT 選項 1 （獨立 NAT 集區的主要和次要的 ExpressRoute 連線），並從 NAT 集區的其中一個 IP 位址的內部部署伺服器的連接埠對應，伺服器將無法透過 ExpressRoute 連線到線路時對應連線會失敗。
> 

## <a name="fine-tuning-features-for-private-peering"></a>微調私人對等互連的功能

在本節中，讓我們檢閱 （取決於您的 Azure 部署，及如何區分您要 MTTR） 選擇性功能，協助改善您的 ExpressRoute 線路的高可用性。 具體來說，讓我們檢閱時區感知部署 ExpressRoute 虛擬網路閘道，以及雙向轉送偵測 (BFD)。

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>可用性區域了解 ExpressRoute 虛擬網路閘道

Azure 區域中的可用性區域是由容錯網域和更新網域組成。 如果您選擇的區域備援的 Azure IaaS 部署，您也可以設定終止 ExpressRoute 私人對等互連的區域備援的虛擬網路閘道。 若要進一步了解，請參閱[關於 Azure 可用性區域中的區域備援的虛擬網路閘道][zone redundant vgw]. To configure zone-redundant virtual network gateway, see [Create a zone-redundant virtual network gateway in Azure Availability Zones][conf zone redundant vgw]。

### <a name="improving-failure-detection-time"></a>提高失敗偵測時間

ExpressRoute 支援 BFD 透過私人對等互連。 BFD 的失敗偵測時間透過 Microsoft Enterprise Edge (MSEEs) 與內部部署端，其 BGP 鄰近項目之間的層級 2 網路從降低約 3 分鐘 （預設值） 為小於第二個。 快速失敗偵測時間有助於 hastening 失敗復原。 若要進一步了解，請參閱[透過 ExpressRoute 的設定 BFD][BFD]。

## <a name="next-steps"></a>後續步驟

在本文中，我們會討論如何設計高可用性的 ExpressRoute 線路連線能力。 ExpressRoute 線路的對等互連點已釘選到 地理位置，因此可能會受到嚴重失敗，會影響整個的位置。 

設計考量，以建置異地備援的網路連線到 Microsoft 骨幹，可以承受災難性的失敗，會影響整個區域，請參閱[設計災害復原與 ExpressRoute 私人對等互連][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "建議的方法來連接使用 ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Suboptimal 最後一哩的連線"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "NAT 選項"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




