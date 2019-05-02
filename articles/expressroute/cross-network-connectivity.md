---
title: Azure 的跨網路連線能力 |Microsoft Docs
description: 此頁面說明跨網路連線和 Azure 網路功能為基礎的解決方案的應用程式案例。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 3bc189cf269084fdb26f141a36755c96554cad7b
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2019
ms.locfileid: "64865992"
---
# <a name="cross-network-connectivity"></a>跨網路連線能力

Fabrikam Inc. 在美國東部具有大型實體據點和 Azure 部署。 Fabrikam 可透過 ExpressRoute，在其內部部署與 Azure 部署之間進行後端連線。 同樣地，Contoso Ltd.所具有的目前狀態和美國西部 Azure 部署。 Contoso 可透過 ExpressRoute，在其內部部署與 Azure 部署之間進行後端連線。  

Fabrikam Inc. 購得 Contoso Ltd。合併以後，Fabrikam 想要讓網路互連。 下圖說明這個案例：

 [![1]][1]

上圖的中間的虛線的箭頭表示所需的網路互相連線。 具體而言，有交叉連線所需的三種類型：1） 的 Fabrikam 和 Contoso Vnet 交叉連線，2） 跨內部區域與 Vnet 跨連接 （也就是，Fabrikam 在內部部署網路連線到 Contoso VNet 和 Contoso 內部網路連線到 Fabrikam VNet） 和 3) Fabrikam 和 Contoso跨內部部署網路連接。 

下表顯示私用對等互連的 ExpressRoute 的 Contoso Ltd.，再合併路由的表。

[![2]][2]

下表顯示 Contoso 的訂用帳戶，在合併之前的有效路由的 VM。 每個資料表，在 VNet 上的 VM 是了解 VNet 位址空間與 Contoso 內部網路，除了預設值。 

[![4]][4]

下表顯示的私用對等互連的 ExpressRoute 的 Fabrikam Inc.在合併之前的路由表。

[![3]][3]

下表顯示 Fabrikam 的訂用帳戶，在合併之前的有效路由的 VM。 每個資料表，在 VNet 上的 VM 並知道 「 VNet 位址空間和 Fabrikam 的內部部署網路，除了預設值。

[![5]][5]

在本文中，讓我們逐步瀏覽，並討論如何達成所需的交叉連線，使用下列的 Azure 網路功能：

* [虛擬網路對等互連][Virtual network peering] 
* [ExpressRoute 連線的虛擬網路][connection]
* [遍及全球的觸角][Global Reach] 

## <a name="cross-connecting-vnets"></a>跨 Vnet 連線

連接兩個虛擬網路時的虛擬網路對等互連 （VNet 對等互連） 提供最適合與最佳的網路效能。 VNet 對等互連支援對等互連的兩個 Vnet （通常稱為 VNet 對等互連） 的相同 Azure 區域內和兩個不同 Azure 區域 （通常稱為全域 VNet 對等互連） 中。 

讓我們設定全域之間 Contoso 和 Fabrikam Azure 訂用帳戶中的 Vnet 對等互連的 VNet。 如需如何建立虛擬網路對等互連兩個虛擬網路，請參閱[建立虛擬網路對等互連][ Configure VNet peering]文章。

下圖顯示之後設定全域 VNet 對等互連的網路架構。

[![6]][6]

下表會顯示 VM 的 Contoso 訂用帳戶的已知的路由。 請注意資料表的最後一個項目。 此項目是跨虛擬網路連接的結果。

[![7]][7]

下表會顯示 VM 的 Fabrikam 訂用帳戶的已知的路由。 請注意資料表的最後一個項目。 此項目是跨虛擬網路連接的結果。

[![8]][8]

VNet 對等互連直接連結兩個虛擬網路 (有沒有下一個躍點，請參閱*VNetGlobalPeering*上述的兩個資料表中的項目)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>跨 Vnet 連線至內部部署網路

我們可以到多個虛擬網路連線的 ExpressRoute 線路。 請參閱[訂用帳戶和服務限制][ Subscription limits]可以連線到 ExpressRoute 線路的虛擬網路最大數目。 

讓我們連接到 Contoso 訂用帳戶的 VNet 的 Fabrikam ExpressRoute 線路和同樣的 Contoso 的 ExpressRoute 線路 Fabrikam 訂用帳戶啟用虛擬網路與內部部署網路之間的跨連線的 VNet。 若要連接到不同的訂用帳戶中 ExpressRoute 線路的虛擬網路，我們需要建立和使用授權。  請參閱文章：[將虛擬網路連線到 ExpressRoute 線路][Connect-ER-VNet]。

下圖顯示網路架構之後設定 ExpressRoute 的虛擬網路交叉連線。

[![9]][9]

下表顯示的私用對等互連的 ExpressRoute 的 Contoso Ltd.之後, 跨虛擬網路連接到內部部署網路透過 ExpressRoute 的路由表。 請參閱 「 路由表有屬於兩個虛擬網路的路由。

[![10]][10]

下表顯示的私用對等互連的 ExpressRoute 的 Fabrikam Inc.之後跨虛擬網路連接到內部部署網路透過 ExpressRoute 的路由表。 請參閱 「 路由表有屬於兩個虛擬網路的路由。

[![11]][11]

下表會顯示 VM 的 Contoso 訂用帳戶的已知的路由。 請注意*虛擬網路閘道*資料表的項目。 VM 會看到兩個內部部署網路的路由。

[![12]][12]

下表會顯示 VM 的 Fabrikam 訂用帳戶的已知的路由。 請注意*虛擬網路閘道*資料表的項目。 VM 會看到兩個內部部署網路的路由。

[![13]][13]

>[!NOTE]
>在 Fabrikam 和/或 Contoso 訂用帳戶您也可以讓輪輻 Vnet 到個別的中樞 VNet （中樞和支點設計不會說明這篇文章中的架構圖）。 Expressroute 在中樞 VNet 閘道之間的交叉連線也可讓東和西的中樞和支點之間的通訊。
>

## <a name="cross-connecting-on-premises-networks"></a>跨連接內部部署網路

ExpressRoute 觸及全球範圍提供連線至不同的 ExpressRoute 線路的內部部署網路之間的連線。 讓我們設定 Contoso 和 Fabrikam ExpressRoute 線路之間的觸及全球範圍。 ExpressRoute 線路位於不同訂用帳戶，因為我們需要建立和使用授權。 請參閱[設定 ExpressRoute 觸及全球範圍][ Configure Global Reach]文章，引導您逐步解說。

下圖顯示設定觸及全球範圍之後的網路架構。

[![14]][14]

下表顯示的私用對等互連的 ExpressRoute 的 Contoso Ltd.，設定觸及全球範圍之後的路由表。 請參閱 「 路由表有屬於兩個內部部署網路的路由。 

[![15]][15]

下表顯示的私用對等互連的 ExpressRoute 的 Fabrikam Inc.設定觸及全球範圍之後的路由表。 請參閱 「 路由表有屬於兩個內部部署網路的路由。

[![16]][16]

## <a name="next-steps"></a>後續步驟

請參閱[虛擬網路常見問題集][VNet-FAQ]，針對任何進一步的問題在 VNet 和 VNet 對等互連。 請參閱[ExpressRoute 常見問題集][ ER-FAQ]任何進一步的問題，在 ExpressRoute 上和虛擬網路連線。

遍及全球的觸角推出國家/地區的國家/地區/區域為基礎。 若要查看是否可用的國家/地區，您想要觸及全球範圍，請參閱[ExpressRoute 觸及全球範圍][Global Reach]。

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "應用程式案例"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "合併之前的 Contoso ExpressRoute 路由表"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "合併之前的 Fabrikam ExpressRoute 路由表"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "Contoso VM 將再合併路由"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "Fabrikam VM 將再合併路由"
[6]: ./media/cross-network-connectivity/vnet-peering.png "之後 VNet 對等互連的架構"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "Contoso VM 路由傳送後 VNet 對等互連"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "Fabrikam VM 路由傳送後 VNet 對等互連"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "之後對象的 Expressroute 交叉連線的架構"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "Contoso ExpressRoute 路由表之後交叉連線 ExR 和 Vnet"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "Fabrikam ExpressRoute 路由表之後交叉連線 ExR 和 Vnet"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "後的 Contoso VM 路由交叉連線 ExR 和 Vnet"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "之後的 Fabrikam VM 路由交叉連線 ExR 和 Vnet"
[14]: ./media/cross-network-connectivity/globalreach.png "設定觸及全球範圍之後的架構"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "觸及全球範圍後的 Contoso ExpressRoute 路由表"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "Fabrikam ExpressRoute 之後觸及全球範圍的路由表"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq