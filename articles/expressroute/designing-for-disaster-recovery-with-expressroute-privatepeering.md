---
title: 設計災害復原與 Azure ExpressRoute |Microsoft Docs
description: 此頁面提供使用 Azure ExpressRoute 時的災害復原的架構性建議。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: cf2b4e385de901254fde3c3d3e807feda98d5b41
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466078"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>ExpressRoute 私人對等互連的災害復原的設計

ExpressRoute 被設計為高可用性，以提供貨運公司客戶級 Microsoft 資源的私人網路連線。 換句話說，沒有單一的 Microsoft 網路內的 ExpressRoute 路徑中的失敗點。 若要最大化可用性的 ExpressRoute 線路的設計考量，請參閱[設計與 ExpressRoute 的高可用性][HA]。

不過，採用 Murphy 的熱門諺語-*如果任何項目可能發生錯誤，則會*-納入考量，本文中告訴我們專注於解決方案，超越可以使用單一 ExpressRoute 電路來處理的失敗。 換句話說，在這篇文章中讓我們深入建置強固的後端進行災害復原使用異地備援的 ExpressRoute 線路的網路連線的網路架構考量。

## <a name="need-for-redundant-connectivity-solution"></a>需要備援連線解決方案

有一些可能性和取得在已降級 （也就是網路服務提供者、 客戶或其他雲端服務提供者，Microsoft，是） 的整個區域服務的執行個體。 這類寬項區域性服務影響的根本原因包括自然遭遇災難。 因此，業務持續性和任務關鍵應用程式務必要規劃嚴重損壞修復。   

無論您是否執行您任務關鍵性應用程式中的 Azure 區域或內部部署或任何其他位置，您可以使用另一個 Azure 區域作為您的容錯移轉網站。 下列文章位址災害復原應用程式與前端存取檢視方塊：

- [企業級災害復原][Enterprise DR]
- [使用 Azure Site Recovery 的 SMB 災害復原][SMB DR]

如果您依賴您的內部部署網路與 Microsoft 之間的任務關鍵作業的 ExpressRoute 連線能力時，您的災害復原計劃也應該包括異地備援的網路連線。 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>使用多個 ExpressRoute 線路的挑戰

當您的相互連接同一組使用多個連線的網路時、 您引入網路之間的平行 path。 平行未正確架構時，可能會導致非對稱路由的路徑。 如果您在路徑中有具狀態的實體 （例如，NAT、 防火牆），非對稱路由可能會封鎖流量。  一般而言，透過 ExpressRoute 私人對等互連路徑不會遇到具狀態的實體，例如 NAT 或防火牆。 因此，非對稱路由，透過 ExpressRoute 私人對等互連不一定是封鎖流量。
 
不過，如果您流量的負載平衡跨異地備援的平行 path，無論您是否有可設定狀態的實體或不是，您會遇到不一致的網路效能。 在本文中，讓我們討論如何解決這些挑戰。

## <a name="small-to-medium-on-premises-network-considerations"></a>小型至中型在內部部署網路考量

我們來看一下範例網路，如下圖所示。 在範例中，Contoso 的內部部署位置與 Azure 區域中的 Contoso 的 VNet 之間建立異地備援的 ExpressRoute 連線能力。 在圖表中，綠色實線表示 （透過 ExpressRoute 1) 的慣用的路徑而虛線的則表示待命 （透過 ExpressRoute 2) 的路徑。

[![1]][1]

當您在設計災害復原的 ExpressRoute 連線能力時，您需要考慮：

- 使用異地備援的 ExpressRoute 線路
- 針對不同的 ExpressRoute 線路使用不同的服務提供者網路
- 每個 ExpressRoute 線路的設計[高可用性][HA]
- 終止客戶網路上的不同位置的不同 ExpressRoute 線路

根據預設，如果您的路由通告相同的所有 ExpressRoute 路徑，透過 Azure 將流量負載平衡內部繫結之間使用相同成本多路徑 (ECMP) 路由的所有 ExpressRoute 路徑。

不過，使用異地備援的 ExpressRoute 線路我們需要列入考量不同的網路效能與不同的網路路徑 （特別是針對網路延遲）。 若要取得更一致的網路效能，在正常作業期間，您可能要想提供最低延遲的 ExpressRoute 線路。

您可能會影響 Azure ExpressRoute 線路，而不用另一個使用其中一種 （效率順序列出） 的下列技術：

- 廣告慣用相較於其他 ExpressRoute 線路的 ExpressRoute 線路上的更特定的路由
- 連線到慣用的 ExpressRoute 線路連結虛擬網路上設定較高的連線權數
- 透過較慣用的 ExpressRoute 循環，較長的 AS 路徑 （如路徑前面加上） 中通告的路由

### <a name="more-specific-route"></a>更明確的路由

下圖說明影響使用更明確的路由通告的 ExpressRoute 路徑選取範圍。 在此所示的範例中，Contoso 內部/24 的 IP 範圍會通告為慣用的路徑 (ExpressRoute 1) 透過兩個/25 位址範圍，以及透過獨立的路徑 (ExpressRoute 2) / 24。

[![2]][2]

/ 25 是更具體的因為相較於/24，Azure 會將流量傳送流向 10.1.11.0/24 透過 ExpressRoute 1 處於正常狀態。 如果這兩個連線的 ExpressRoute 1 當機，然後 VNet 會看到 10.1.11.0/24 路由通告，只能透過 ExpressRoute 2;因此待命線路用在這種失敗狀態。

### <a name="connection-weight"></a>連線權數

下列螢幕擷取畫面說明如何設定 Azure 入口網站透過 ExpressRoute 連線的權數。

[![3]][3]

下圖說明使用連線權數的影響 ExpressRoute 路徑選取。 預設連線權數為 0。 在下列範例中，ExpressRoute 1 的連線的權數設定為 100。 當 VNet 收到公告透過多個 ExpressRoute 線路的路由前置詞時，VNet 會偏好與最高權數的連線。

[![4]][4]

如果這兩個連線的 ExpressRoute 1 當機，然後 VNet 會看到 10.1.11.0/24 路由通告，只能透過 ExpressRoute 2;因此待命線路用在這種失敗狀態。

### <a name="as-path-prepend"></a>為路徑前面加上

下圖說明影響 ExpressRoute 路徑選取項目路徑前面加上使用。 在圖表中，路由通告，透過 ExpressRoute 1 表示 eBGP 的預設行為。 透過 ExpressRoute 2 路由公告，在內部部署網路的 ASN 前面會加上此外路由上作為路徑。 當透過多個 ExpressRoute 線路，每個 eBGP 路由選取程序中，接收相同的路由 VNet 偏好較短的路徑的路由。 

[![5]][5]

如果這兩個連線的 ExpressRoute 1 停機時，VNet 會看到只透過 ExpressRoute 2 10.1.11.0/24 路由通告。 因此，路徑會變得無關的時間。 因此，待命的線路會用於這種失敗狀態。

使用任何技術，如果您在影響偏好其中一個 ExpressRoute 於其他 Azure，您也需要確保內部部署網路的 Azure 繫結來避免非對稱流量的流量也偏好相同的 ExpressRoute 路徑。 一般而言，會使用本機喜好設定值，來影響來偏好兩條 ExpressRoute 線路高於其他內部部署網路。 本機喜好設定是內部的 BGP (iBGP) 計量。 最好使用具有最高的本機喜好設定值的 BGP 路由。

> [!IMPORTANT]
> 當您使用特定 ExpressRoute 線路為待命時，您需要主動管理它們，並定期測試容錯移轉作業。 
> 

## <a name="large-distributed-enterprise-network"></a>大型分散式的企業網路

當您有大型的分散式的企業網路時，您可能會有多個 ExpressRoute 線路。 在本節中，我們看看如何設計使用主動-主動的 ExpressRoute 線路，而不需要額外的待命線路進行災害復原。 

我們來看下圖所示的範例。 在範例中，contoso 公司有兩個內部部署位置連接到兩個 Contoso IaaS 部署，透過在兩個不同的對等互連位置的 ExpressRoute 線路的兩個不同 Azure 區域中。 

[![6]][6]

我們設計災害復原的架構會影響在跨地區位置 (region1/到 location2/location1 的 region2) 的流量路由方式。 我們來看一下將跨區域位置的流量路由傳送不同的兩個不同的嚴重損壞架構。

### <a name="scenario-1"></a>案例 1

在第一個案例中，讓我們來設計災害復原的 Azure 區域與內部部署網路之間的所有流量都流經本機 ExpressRoute 線路處於穩定狀態。 如果在本機的 ExpressRoute 線路故障時，然後遠端 ExpressRoute 線路用於 Azure 之間的所有流量並在內部部署網路。

案例 1 以下圖所示。 在圖表中，綠線表示 VNet1 和內部部署網路之間流量的路徑。 藍線表示 VNet2 和內部部署網路之間流量的路徑。 實線表示在穩定狀態所需的路徑和虛線的線條表示路徑中的對應的 ExpressRoute 線路，具有穩定的流量流程失敗。 

[![7]][7]

您可以設計架構，來影響偏好本機對等互連位置 ExpressRoute 內部部署網路連線繫結流量的 Vnet 中使用連線權數的案例。 若要完成此解決方案，您需要確保對稱的反向流量。 您可以使用本機喜好設定 iBGP 工作階段上您 （在其的 ExpressRoute 線路會在內部部署端終止） 的 BGP 路由器之間偏好的 ExpressRoute 線路。 下圖說明解決方案。 

[![8]][8]

### <a name="scenario-2"></a>案例 2

下圖說明案例 2。 在圖表中，綠線表示 VNet1 和內部部署網路之間流量的路徑。 藍線表示 VNet2 和內部部署網路之間流量的路徑。 在穩定狀態 （圖表中的實心線條），所有 Vnet 和內部部署位置之間的流量流程，是透過 Microsoft 骨幹大部分的情況下，以及透過之間的互相連線內部部署位置只能在失敗的狀態 （虛線中圖表中） 的 ExpressRoute。

[![9]][9]

下圖說明解決方案。 如所述，您也可以架構案例中使用更明確的路由 (選項 1) 或 AS 路徑前面加上 (選項 2) 會影響 VNet 路徑選取項目。 若要影響內部部署網路路由選取項目給 Azure 的繫結流量，您需要設定內部部署位置之間的互相連線，較不適合。 您設定為 偏好的互相連線的連結羅豪取決於內部部署網路內使用的路由通訊協定。 您可以使用本機喜好設定與 iBGP 或度量 IGP （OSPF 或 IS IS）。

[![10]][10]


## <a name="next-steps"></a>後續步驟

在本文中，我們會討論如何設計災害復原的 ExpressRoute 線路的私人對等互連連線。 下列文章位址災害復原應用程式與前端存取檢視方塊：

- [企業級災害復原][Enterprise DR]
- [使用 Azure Site Recovery 的 SMB 災害復原][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "小型到中型大小的內部部署網路考量"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "影響使用更明確的路由路徑選取項目"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "設定透過 Azure 入口網站的連線權數"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "影響路徑選取項目使用連線權數"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "影響使用為路徑的路徑選取在前面加上"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "大型分散式內部部署網路考量"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "案例 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "主動-主動 ExpressRoute 線路的解決方案 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "scenario 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "主動-主動 ExpressRoute 線路的解決方案 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





