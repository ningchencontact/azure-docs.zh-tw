---
title: Azure 虛擬 WAN 的全域傳輸網路架構 |Microsoft Docs
description: 了解全域傳輸網路架構的虛擬 WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 05/20/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 114d11f98c6181a03f5ce52527b5e2efea468c42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65965976"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>全域傳輸網路架構與虛擬 WAN

企業整合、 連接及控制以雲端為中心的新式企業 IT 使用量被採用全域的傳輸網路架構。 在新式的以雲端為中心 enterprise 中，網路流量不會不需要是總部的 backhauled。 全域傳輸網路架構根據網路等熟悉的概念和特有雲端和雲端架構的新概念。

![架構](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**圖 1：全域的傳輸網路與虛擬 WAN**

現代企業需要跨雲端和內部部署的無所不在超分散式應用程式、 資料和使用者之間的連線。 Azure 虛擬 WAN 可讓全域傳輸網路架構無所不在、 任意-到-任意 Vnet、 站台、 應用程式和使用者的全域散發組之間的連線。 Azure 虛擬 WAN 是 Microsoft 管理的服務。 所有的網路元件所組成的這項服務裝載，並由 Microsoft 管理。 如需虛擬 WAN 的詳細資訊，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)文章。

在 Azure 虛擬 WAN 架構中，Azure 區域作為的中樞的中，您可以選擇將您的分支。 一旦連線分支，您可以利用 Azure 的骨幹，若要建立新分支-VNet，並選擇性地分支-分支連線。

您可以建立單一虛擬 WAN 中樞的支點 （分支 Vnet，使用者），最大數目的區域中，然後連接到中樞的其他區域中的支點，以建立虛擬 WAN。 或者，如果支點分散在各地，您可以也具現化區域集線器，相互連線的中樞。 中樞都是屬於相同的虛擬 WAN，但可以是不同區域的原則相關聯。

## <a name="hub"></a>中樞和支點傳輸

全域傳輸網路架構根據傳統的中樞和支點連線模型，其中裝載的雲端網路 'hub' 可讓可轉移可能會分散在不同類型的 '支點' 的端點之間的連線。
  
在此模型中，支點可以是：

* 虛擬網路 (Vnet)
* 實體的新分支站台
* 遠端使用者
* Internet

![中樞和支點全域傳輸圖表](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**圖 2：中樞和支點**

[圖 2] 顯示其中地理位置分散的使用者、 實體的網站和 Vnet 會彼此互相透過網路的中樞裝載在雲端的全域網路的邏輯檢視。 此架構可讓網路端點之間的邏輯的一個躍點傳輸連線。 支點會由各種 Azure 網路服務，例如 ExpressRoute 或站台對站 VPN 實體的分支，VNet 對等互連的 Vnet，以及為遠端使用者的點對站 VPN 連線至中樞。

## <a name="crossregion"></a>跨區域連接

企業中，針對雲端耗用量通常會遵循實際使用量。 大多數的企業從其實體站台和使用者最接近的區域存取雲端。 其中一個全域網路架構的索引鍵的主體是以啟用網路實體與端點之間的跨區域連線。 雲端使用量可以跨越多個區域。 這表示，另一個分支或使用中樞-中樞連線目前處於預覽狀態的不同區域中的 VNet，可以觸達流量從已連線到雲端，在一個區域中的分支。

## <a name="any"></a>任意-到-任意的連線

全域傳輸網路架構可讓*任意-到-任意連線*透過中央網路集線器。 此架構可消除或減少完整網狀或部分的網狀連線模型來建立及維護更複雜。 此外，在中樞和支點與網狀結構網路路由的控制項是容易設定和維護。

任意-到-任意的連線內容中的通用架構中，可讓具備分散在世界各地的使用者、 分支、 資料中心、 Vnet 和應用程式透過傳輸集線器連接到彼此的企業。 傳輸中樞做為全域的傳輸系統。

![傳輸路徑](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**圖 3：虛擬 WAN 流量路徑**

Azure 虛擬 WAN 支援下列全域的傳輸連線路徑。 在括號中的字母對應圖 3。

* 分支對 VNet (a)  
* 分支-分支 (b)
* 遠端使用者對 VNet (c)
* 遠端使用者-至-分支 (d)
* VNet 對 VNet 使用 VNet 對等互連 (e)
* ExpressRoute 遍及全球的觸角 

### <a name="branchvnet"></a>若要對 VNet 的分支

分支-VNet 是 Azure 虛擬 WAN 支援的主要路徑。 這個路徑可讓您連接到 Azure IAAS 企業工作負載部署在 Azure Vnet 中的 分支。 分支可以連接到虛擬 WAN 透過 ExpressRoute 或站對站 VPN。 連線到虛擬 WAN 中樞，透過 VNet 連線的 vnet 流量傳輸。

### <a name="branchbranch"></a>分支-分支

分支可以連接到 Azure 虛擬 WAN 中樞，使用 ExpressRoute 線路和/或站對站 VPN 連線。 您可以連接分支到虛擬 WAN 分支最接近的區域中的中樞。

此選項可讓企業利用 Azure 的骨幹，連接的分支。 不過，即使這項功能可供使用，您應該衡量透過 Azure 虛擬 WAN 連線分支，與使用私用 WAN 的優點。

### <a name="usertovnet"></a>遠端使用者對 VNet

您可以啟用直接且安全的遠端存取，使用點對站連線從遠端使用者用戶端虛擬 WAN 到 azure。 企業遠端使用者不再需要在雲端中使用公司的 VPN 的 hairpin。

### <a name="usertobranch"></a>遠端是 「 使用者-分支

遠端是 「 使用者-分支路徑可讓遠端使用者使用 Azure 存取內部部署工作負載和應用程式的點對站連線傳送到雲端。 這個路徑可讓遠端使用者彈性存取工作負載，部署在 Azure 和內部部署。 企業可以啟用在 Azure 虛擬 WAN 的中央雲端安全的遠端存取服務。

### <a name="vnetvnet"></a>使用 VNet 對等互連的 VNet 對 VNet 傳輸

若要連接到彼此的 Vnet，以支援跨多個 Vnet 所實作的多層式應用程式，使用 VNet 對等互連。 透過 Azure 虛擬 WAN 的 VNet 對 VNet 傳輸案例目前不支援，但位於 Azure 的藍圖。 透過 VNet 對等互連連線的 Vnet 是建議的解決方案需要彼此連線的 vnet。 [閘道傳輸](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity)（在 VNet 對等互連的內容） 並不需要虛擬 WAN 因為虛擬 WAN 會自動啟用閘道傳輸。

### <a name="globalreach"></a>ExpressRoute 遍及全球的觸角

ExpressRoute 是私人且有彈性的方式，您的內部部署網路連線到 Microsoft 雲端。 ExpressRoute 觸及全球範圍是 ExpressRoute 的附加元件功能。 使用觸及全球範圍，您可以連結方式來產生您的內部部署網路之間的私人網路的 ExpressRoute 線路。 連線到 Azure 虛擬 WAN 使用 ExpressRoute 的分支需要 ExpressRoute 觸及全球範圍來彼此通訊。

在此模型中，使用 ExpressRoute 的虛擬 WAN 中樞連線的每個分支可以連線到 Vnet 使用的分支到 VNet 的路徑。 分支-分支流量不會傳輸集線器，因為 ExpressRoute 全域連線到透過 WAN Azure 啟用較佳的路徑。

## <a name="security"></a>安全性和原則控制

中樞虛擬網路互連，可能會看到所有的傳輸流量。 它可以是主控件集中的網路功能和服務，例如這類雲端路由、 網路原則和安全性和網際網路存取控制的位置。

## <a name="next-steps"></a>後續步驟

建立使用虛擬 WAN 連線。

* [使用虛擬 WAN 的站對站連線](virtual-wan-site-to-site-portal.md)
* [使用虛擬 WAN 的點對站連線](virtual-wan-point-to-site-portal.md)
* [使用虛擬 WAN 的 ExpressRoute 連線](virtual-wan-expressroute-portal.md)
