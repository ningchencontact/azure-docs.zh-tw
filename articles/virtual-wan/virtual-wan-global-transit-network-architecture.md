---
title: Azure 虛擬 WAN 全球傳輸網路架構 |Microsoft Docs
description: 瞭解適用于虛擬 WAN 的全球傳輸網路架構
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 07/23/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 2376c77ecc328788c842e045aafb618cbad39b0e
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2019
ms.locfileid: "68421421"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>全球傳輸網路架構和虛擬 WAN

企業採用全球傳輸網路架構來整合、連接及控制以雲端為中心的現代化企業 IT 足跡。 在新式以雲端為中心的企業中, 網路流量不需要 backhauled 至 HQ。 全球傳輸網路架構以熟悉的網路概念為基礎, 以及雲端和雲端架構特有的新概念。

![架構](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**圖 1：具有虛擬 WAN 的全球傳輸網路**

現代化企業需要跨雲端和內部部署的超分散式應用程式、資料和使用者之間進行廣泛的連線。 Azure 虛擬 WAN 藉由在 Vnet、網站、應用程式和使用者的全域分散式集合之間啟用各種不同的連線, 來允許全球傳輸網路架構。 Azure 虛擬 WAN 是一種受 Microsoft 管理的服務。 此服務所組成的所有網路元件都是由 Microsoft 託管及管理。 如需有關虛擬 WAN 的詳細資訊, 請參閱[虛擬 Wan 總覽](virtual-wan-about.md)文章。

在 Azure 虛擬 WAN 架構中, Azure 區域會作為中樞, 供您選擇用來連接分支。 連接分支之後, 您就可以利用 Azure 骨幹來建立分支對 VNet, 並選擇性地使用分支對分支連線能力。

您可以建立虛擬 WAN, 方法是在擁有最多輪輻 (分支、Vnet、使用者) 的區域中建立單一虛擬 WAN 中樞, 然後將其他區域中的輪輻連接到中樞。 或者, 如果輪輻散佈在不同的地理位置, 您也可以具現化區域中樞, 並將中樞互連。 中樞全都屬於相同的虛擬 WAN, 但它們可以與不同的地區原則相關聯。

## <a name="hub"></a>中樞和輪輻傳輸

全球傳輸網路架構是以傳統的中樞和輪輻連線模式為基礎, 其中雲端託管網路「中樞」可在可能分散于不同類型「輪輻」的端點之間啟用可轉移的連線。
  
在此模型中, 輪輻可以是:

* 虛擬網路 (Vnet)
* 實體分支網站
* 遠端使用者
* 網際網路

![中樞和輪輻全球運輸圖表](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**圖 2：中樞和輪輻**

[圖 2] 顯示全球網路的邏輯觀點, 地理位置分散的使用者、實體網站和 Vnet 透過雲端中裝載的網路中樞進行互連。 此架構可啟用網路端點之間的邏輯單一躍點傳輸連線。 輪輻會透過各種 Azure 網路服務 (例如 ExpressRoute 或站對站 VPN) 連接到中樞, 用於實體分支、適用于 Vnet 的 VNet 對等互連, 以及遠端使用者的點對站 VPN。

## <a name="crossregion"></a>跨區域連線能力

對於企業而言, 雲端足跡通常會遵循實體的使用量。 大部分的企業都會從最接近其實體網站和使用者的區域存取雲端。 全域網路架構的其中一個主要原則是啟用網路實體與端點之間的跨區域連線。 雲端使用量可以跨越多個區域。 這表示從一個區域中連線到雲端的分支流量, 可以使用中樞對中樞連線到位於不同區域的另一個分支或 VNet, 這目前在我們的藍圖中。

## <a name="any"></a>任何對任何連線能力

全域傳輸網路架構會透過中央網路中樞啟用*任何對任何連線能力*。 此架構可消除或減少建立和維護更複雜的完整網狀模式或部分網格連線性模型的需求。 此外, 中樞和輪輻與網狀架構網路中的路由控制較容易設定和維護。

全域架構內容中的任何對任何連線能力, 可讓具有全域散發使用者、分支、資料中心、Vnet 和應用程式的企業透過傳輸中樞彼此連接。 傳輸中樞會作為全域傳輸系統。

![流量路徑](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**圖 3：虛擬 WAN 流量路徑**

Azure 虛擬 WAN 支援下列全球傳輸連接路徑。 括弧中的字母會對應到 [圖 3]。

* 分支對 VNet (a)  
* 分支對分支 (b)
* 遠端使用者對 VNet (c)
* 遠端使用者對分支 (d)
* 使用 VNet 對等互連的 VNet 對 VNet (e)
* ExpressRoute 全球範圍 

### <a name="branchvnet"></a>分支對 VNet

分支對 VNet 是 Azure 虛擬 WAN 支援的主要路徑。 此路徑可讓您將分支連線至 azure Vnet 中部署的 Azure IAAS 企業工作負載。 分支可以透過 ExpressRoute 或站對站 VPN 連線到虛擬 WAN。 流量會傳輸到透過 VNet 連線連接到虛擬 WAN 中樞的 Vnet。

### <a name="branchbranch"></a>分支對分支

您可以使用 ExpressRoute 線路和/或站對站 VPN 連線, 將分支連接到 Azure 虛擬 WAN 中樞。 您可以將分支連接到最接近分支區域中的虛擬 WAN 中樞。

此選項可讓企業利用 Azure 骨幹來連接分支。 不過, 即使這項功能可供使用, 您還是應該衡量透過 Azure 虛擬 WAN 連接分支的優點, 與使用私人 WAN。

### <a name="usertovnet"></a>遠端使用者對 VNet

您可以使用從遠端使用者用戶端到虛擬 WAN 的點對站連線, 啟用對 Azure 的直接安全遠端存取。 企業遠端使用者不再需要使用公司 VPN hairpin 到雲端。

### <a name="usertobranch"></a>遠端使用者對分支

遠端使用者到分支路徑可讓使用點對站連線至 Azure 的遠端使用者, 透過雲端傳輸來存取內部部署工作負載和應用程式。 此路徑可讓遠端使用者彈性地存取部署在 Azure 和內部部署環境中的工作負載。 企業可以在 Azure 虛擬 WAN 中啟用以雲端為基礎的集中式安全遠端存取服務。

### <a name="vnetvnet"></a>使用 VNet 對等互連的 VNet 對 VNet 傳輸

若要將 Vnet 彼此連線以支援跨多個 Vnet 執行的多層式應用程式, 請使用 VNet 對等互連。 目前不支援透過 Azure 虛擬 WAN 進行 VNet 對 VNet 傳輸案例, 但在 Azure 藍圖上。 透過 VNet 對等互連連接 Vnet 是需要彼此連線的 Vnet 建議解決方案。 [閘道傳輸](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity)(在 VNet 對等互連的內容中) 不是虛擬 WAN 的必要項, 因為虛擬 WAN 會自動啟用閘道傳輸。

### <a name="globalreach"></a>ExpressRoute 全球範圍

ExpressRoute 是將內部部署網路連線至 Microsoft Cloud 的私用和可復原方式。 ExpressRoute Global 觸及是 ExpressRoute 的附加元件功能。 有了全球範圍, 您可以將 ExpressRoute 線路連結在一起, 以在內部部署網路之間建立私人網路。 使用 ExpressRoute 連線到 Azure 虛擬 WAN 的分支需要 ExpressRoute 全域觸達彼此通訊。

在此模型中, 使用 ExpressRoute 連線到虛擬 WAN 中樞的每個分支, 都可以使用分支對 VNet 路徑連接到 Vnet。 分支對分支流量不會傳輸中樞, 因為 ExpressRoute Global 觸及可透過 Azure WAN 提供更佳的路徑。

## <a name="security"></a>安全性與原則控制

虛擬網路中樞會進行互連, 而且可能會看到所有傳輸流量。 它可以是裝載中央網路功能和服務的位置, 例如雲端路由、網路原則和安全性, 以及網際網路存取控制。

## <a name="next-steps"></a>後續步驟

使用虛擬 WAN 建立連接。

* [使用虛擬 WAN 的站對站連線](virtual-wan-site-to-site-portal.md)
* [使用虛擬 WAN 的 ExpressRoute 連線](virtual-wan-expressroute-portal.md)
