---
title: 位置和連線提供者︰Azure ExpressRoute | Microsoft Docs
description: 本文提供提供服務所在位置以及如何連線到 Azure 區域的詳細概觀。 依位置排序。
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2018
ms.author: pareshmu
ms.openlocfilehash: b6ea9c64f8363821eb3e61c2979f249816675bf7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 合作夥伴和對等互連位置

> [!div class="op_single_selector"]
> * [依提供者的位置](expressroute-locations.md)
> * [依位置的提供者](expressroute-locations-providers.md)


本文中的資料表會提供有關 ExpressRoute 連線提供者、ExpressRoute 地理涵蓋範圍、透過 ExpressRoute 支援的 Microsoft 雲端服務，以及 ExpressRoute 系統整合者 (SI) 的資訊。

## <a name="partners"></a>ExpressRoute 連線提供者
所有的 Azure 區域和位置都支援 ExpressRoute。 以下地圖提供了 Azure 區域和 ExpressRoute 位置的清單。 ExpressRoute 位置是指 Microsoft 與數個服務提供者對等互連的位置。

![位置圖][0]

如果您至少與地緣政治區域內的一個 ExpressRoute 位置連線，您將有權存取地緣政治區域內所有區域中的 Azure 服務。 

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>地緣政治區域內 ExpressRoute 位置的 Azure 區域
下表提供地緣政治區域內 ExpressRoute 位置的 Azure 區域對應。

| **地緣政治區域** | **Azure 區域** | **ExpressRoute 位置** |
| --- | --- | --- |
| **北美洲** |美國東部、美國西部、美國東部 2、美國西部 2、美國中部、美國中南部、美國中北部、美國中西部、加拿大中部、加拿大東部 |亞特蘭大、芝加哥、達拉斯、丹佛、拉斯維加斯、洛杉磯、邁阿密、紐約、聖安東尼奧、西雅圖、矽谷、華盛頓特區、蒙特婁、魁北克市、多倫多 |
| **南美洲** |巴西南部 |聖保羅 |
| **歐洲** |法國中部、法國南部、北歐、西歐、英國西部、英國南部 |阿姆斯特丹、都柏林、倫敦、紐波特 (威爾斯)、巴黎 |
| **亞洲** |東亞、東南亞 |香港特別行政區、新加坡、新加坡 2 |
| **日本** |日本西部、日本東部 |大阪、東京 |
| **澳大利亞** |澳大利亞東南部、澳大利亞東部、澳大利亞中部、澳大利亞中部 2 |墨爾本、雪梨 |
| **印度** |印度西部、印度中部、印度南部 |辰內，孟買 |
| **南韓** |韓國中部、韓國南部 |釜山、首爾 |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>國家雲端的區域和地理政治界限
下表提供國家雲端的區域和地理政治界限等資訊。

| **地緣政治區域** | **Azure 區域** | **ExpressRoute 位置** |
| --- | --- | --- |
| **美國政府雲端** |美國愛荷華州政府、美國維吉尼亞州政府、美國國防部中部、美國國防部東部  |芝加哥、達拉斯、紐約、西雅圖、矽谷、華盛頓特區 |
| **中國** |中國北部、中國東部 |北京、上海 |
| **德國** |德國中部、德國東部 |柏林、法蘭克福 |

標準 ExpressRoute SKU 不支援跨地緣政治區域的連線。 您必須啟用 ExpressRoute 進階附加元件，以支援全球連線。 不支援連線至國家雲端環境。 如果有需要的話，您可以聯絡您的連線提供者。

## <a name="locations"></a>連線提供者位置

下表顯示連線的位置以及每個位置的服務提供者。 如果您想檢視服務提供者及其可提供服的各個位置，請參閱[服務提供者位置](expressroute-locations.md#locations)。 


### <a name="production-azure"></a>生產 Azure
| **位置** | **服務提供者** |
| --- | --- |
| **阿姆斯特丹** |Aryaka Networks、AT&T NetBond、British Telecom、Colt、Equinix、euNetworks、GÉANT、InterCloud、Internet Solutions - Cloud Connect、Interxion、KPN、Level 3 Communications、Megaport、NTT Communications、Orange、Tata Communications、TeleCity Group、Telefonica、Telenor、Verizon、Zayo |
| **亞特蘭大** |Equinix |
| **斧山** |LG CNS |
| **辰內** | Airtel+、Global CloudXchange (GCX)、SIFY、Tata Communications |
| **芝加哥** |AT&T NetBond、Comcast、Coresite、Equinix、PacketFabric、Level 3 Communications、Megaport、Verizon、Zayo |
| **達拉斯** |Aryaka Networks、AT&T NetBond、Cologix、Equinix、Level 3 Communications、Megaport、Telmex Uninet+、Verizon、Zayo|
| **丹佛** |CoreSite、Megaport |
| **都柏林** |Colt、eir、Interxion、Megaport、Telecity Group |
| **香港** |Aryaka Networks、British Telecom、China Telecom Global、Equinix、Megaport、NTT Communications、Orange、PCCW Global Limited、Tata Communications、Verizon |
| **拉斯維加斯** |Level 3 Communications、Megaport |
| **倫敦** |AT&T NetBond、British Telecom、Colt、Equinix、InterCloud、Internet Solutions - Cloud Connect、Interxion、Jisc、Level 3 Communications、Megaport、MTN、NTT Communications、Orange、Tata Communications、Telecity Group、Telehouse - KDDI、Telenor、Verizon、Vodafone、Zayo |
| **洛杉磯** |CoreSite、Equinix、Megaport、NTT、Zayo |
| **墨爾本** |AARNet、Equinix、Megaport、NEXTDC、Optus+、Telstra Corporation |
| **邁阿密** |C3ntro+、Equinix、Megaport、Neutrona Networks |
| **蒙特婁** |Bell Canada、Cologix、Telus、Zayo |
| **孟買** |Airtel+、Global CloudXchange (GCX)、Sify、Tata Communications |
| **紐約** |CenturyLink Cloud Connect、Coresite、Equinix、Megaport、Zayo |
| **Newport(Wales)** |Level 3 Communications、Next Generation Data |
| **大阪** |Equinix、Internet Initiative Japan Inc. - IIJ、NTT Communications、NTT SmartConnect、Softbank |
| **巴黎** |Colt、Intercloud、Interxion、Equinix、Orange |
| **魁北克市** | Bell Canada、Megaport |
| **聖安東尼奧** |CenturyLink Cloud Connect、Megaport |
| **聖保羅** |Ascenty Data Centers+、Equinix、Level 3 Communications、Neutrona Networks、Telefonica、UOLDIVEO |
| **Seattle** |Equinix、Level 3 Communications、Megaport |
| **首爾** |KINX、LG CNS、Sejong Telecom |
| **矽谷** |Aryaka Networks、AT&T NetBond、British Telecom、Comcast、Coresite、Equinix、PacketFabric、Level 3 Communications、Megaport、Orange、Tata Communications、Verizon、Zayo Group |
| **新加坡** |Aryaka Networks、AT&T NetBond、British Telecom、Epsilon Global Communications、Equinix、InterCloud、Level 3 Communications、Megaport、NTT Communications、Orange、SingTel、Tata Communications、Verizon |
| **新加坡 2** |Megaport、SingTel |
| **雪梨** |AARNet、AT&T NetBond、British Telecom、Equinix、Megaport、NEXTDC、NTT Communications、Optus、Orange、Telstra Corporation、Verizon |
| **東京** |Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Colt、Equinix、Internet Initiative Japan Inc. - IIJ、NTT Communications、Softbank、Verizon |
| **多倫多** |AT&T NetBond、Bell Canada、CenturyLink Cloud Connect、Cologix、Console、Equinix、Megaport、Telus、Zayo |
| **華盛頓** |Aryaka Networks、AT&T NetBond、British Telecom、Comcast、Coresite、Equinix、Internet2、InterCloud、Level 3 Communications、Megaport、NTT Communications、Orange、Sprint、Tata Communications、Verizon、Zayo |

 **+** 表示即將推出

### <a name="national-cloud-environments"></a>國家雲端環境

### <a name="us-government-cloud"></a>美國政府雲端
| **位置** | **服務提供者** |
| --- | --- |
| **芝加哥** |AT&T NetBond、Equinix、Level 3 Communications、Verizon |
| **達拉斯** |Equinix、Megaport、Verizon |
| **紐約** |Equinix、Level 3 Communications+、Verizon |
| **矽谷** | Equinix、Level 3 Communications |
| **Seattle** | Equinix |
| **華盛頓** |AT&T NetBond、Equinix、Level 3 Communications、Verizon |

### <a name="china"></a>中國
| **位置** | **服務提供者** |
| --- | --- |
| **北京** |China Telecom |
| **上海** |China Telecom |

若要深入了解，請參閱 [ExpressRoute (中國)](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>德國
| **位置** | **服務提供者** |
| --- | --- |
| **柏林** |Colt+、e-shelter、Megaport+、T-Systems |
| **法蘭克福** |Colt、Equinix、Interxion |

## <a name="c1partners"></a>透過 Exchange 提供者連線
如果上一節中未列出您的連線提供者，您仍然可以建立連線。

* 請洽詢您的連線提供者，以了解他們是否連線到上方表格中列出的任何 Exchange 提供者。 您可以檢查下列連結，以收集 Exchange 提供者所提供之服務的相關詳細資訊。 已有數個連線提供者連線到乙太網路 Exchange。
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](http://www.interxion.com/)
  * [IX Reach](https://www.ixreach.com/services/cloud-connectivity/microsoft-azure/)
  * [NextDC](http://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* 請您的連線提供者將您的網路延伸到選擇的對等互連位置。
  * 請確保您的連線提供者以高可用性的方式延伸您的連線，因此不會有單一失敗點。
* 排序一個 ExpressRoute 循環，將 Exchange 視為連線至 Microsoft 的連線提供者。
  * 依照 [建立 ExpressRoute 循環](expressroute-howto-circuit-classic.md) 中的步驟來設定連線。

## <a name="c1partners"></a>透過額外服務提供者連線
| **位置** | **Exchange** | **連線提供者** |
| --- | --- | --- |
| **阿姆斯特丹** | Equinix、Telecity | BICS、Eurofiber、Fastweb S.p.A、MainOne、Nianet、Post、Proximus、Telia |
| **芝加哥** | Equinix | Lightower、Windstream |
| **達拉斯** | Equinix、Megaport | Axtel、C3ntro Telecom、Cox Business、Data Foundry、Transtelco |
| **法蘭克福** | Telecity | BICS、Nianet、QSC AG |
| **香港** | Equinix | Macroview Telecom |
| **倫敦** | Equinix、euNetworks、Telecity | Bezeq International Ltd.、Epsilon、Exponential E、HSO、NexGen Networks、Tamares Telecom、Zain |
| **洛杉磯** | Equinix |Transtelco |
| **馬德里** | Level3 | Zertia |
| **蒙特婁** | Cologix、Equinix | Airgate Technologies. Inc、Cogeco Peer 1、Rogers、Zirro |
| **紐約** |Equinix、Megaport | Altice Business、Lightower、Webair |
| **Seattle** |Equinix | Alaska Communications |
| **矽谷** |Equinix | Cox Business、Windstream |
| **新加坡** |Equinix |1CLOUDSTAR、BICS、Epsilon Telecommunications Limited、LGA Telecom、United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **雪梨** | Megaport | Macquarie Telecom Group|
| **東京** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **多倫多** | Equinix | Airgate Technologies. Inc、Cogeco Peer 1、Rogers、Thinktel、Zirro|
| **華盛頓** |Equinix | Altice Business、BICS、Gtt Communications Inc、Epsilon、Lightower、Masergy、Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute 系統整合者
根據您的網路規模，為符合您的需求而啟用私人連線可能有一定的難度。 您可以使用下表所列出的任何系統整合者來協助您開始使用 ExpressRoute。

| **Continent** | **系統整合者** |
| --- | --- |
| **亞洲** |Avanade Inc.、OneAs1a |
| **澳大利亞** | Ensyst、IT Consultancy、MOQdigital、Vigilant.IT |
| **歐洲** |Avanade Inc.、Altogee、Bright Skies GmbH、Inframon、MSG Services、New Signature、Nelite、Orange Networks、sol-tec |
| **北美洲** |Avanade Inc.、Equinix Professional Services、FlexManage、Lightstream、Perficient、Presidio |
| **南美洲** |Avanade Inc. |
## <a name="next-steps"></a>後續步驟
* 如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。
* 請確定符合所有必要條件。 請參閱 [ExpressRoute 必要條件](expressroute-prerequisites.md)。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "位置圖"
