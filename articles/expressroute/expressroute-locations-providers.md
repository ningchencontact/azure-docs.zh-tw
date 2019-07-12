---
title: 位置和連線提供者：Azure ExpressRoute | Microsoft Docs
description: 本文提供提供服務所在位置以及如何連線到 Azure 區域的詳細概觀。 依位置排序。
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: jaredr80
ms.openlocfilehash: 21d06dfaecb0448194355f13978483779a2ff6be
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805689"
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

| **地緣政治區域** | **區域** | **Azure 區域** | **ExpressRoute 位置** |
| --- | --- | --- | --- |
| **澳洲政府** | 1 | 澳大利亞中部、澳大利亞中部 2 |坎培拉、坎培拉 2 |
| **歐洲** | 1 |法國中部、法國南部、北歐、西歐、英國西部、英國南部 |阿姆斯特丹、 Amsterdam2、 都柏林、 法蘭克福、 倫敦、 London2、 馬賽、 紐波特 （威爾斯）、 巴黎、 蘇黎士 |
| **北美洲** | 1 |美國東部、美國西部、美國東部 2、美國西部 2、美國中部、美國中南部、美國中北部、美國中西部、加拿大中部、加拿大東部 |亞特蘭大、 芝加哥、 達拉斯、 丹佛、 內華達州拉斯維加斯、 洛杉磯、 邁阿密、 紐約、 聖安東尼奧、 西雅圖、 矽谷、 矽 Valley2、 Washington DC，華盛頓州 DC2、 蒙特婁、 魁北克市、 多倫多 |
| **亞洲** | 2 |東亞、東南亞 |香港特別行政區，吉隆坡，新加坡、 新加坡 2、 台北 |
| **印度** | 2 |印度西部、印度中部、印度南部 |辰內、辰內 2、孟買、孟買 2 |
| **日本** | 2 |日本西部、日本東部 |大阪、東京 |
| **大洋洲** | 2 |澳洲東南部、澳洲東部 |奧克蘭，墨爾本，伯斯省雪梨 | 
| **南韓** | 2 |南韓中部、南韓南部 |釜山、首爾|
| **UAE** | 3 | 阿拉伯聯合大公國中部、 阿拉伯聯合大公國北部 | 杜拜 Dubai2 |
| **南非** | 3 |南非西部、 南非北部 |開普敦、約翰尼斯堡 |
| **南美洲** | 3 |巴西南部 |聖保羅 |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>國家雲端的區域和地理政治界限
下表提供國家雲端的區域和地理政治界限等資訊。

| **地緣政治區域** | **Azure 區域** | **ExpressRoute 位置** |
| --- | --- | --- |
| **美國政府雲端** |US Gov 亞利桑那州、US Gov 愛荷華州、US Gov 德克薩斯州、US Gov 維吉尼亞州、US DoD 中部、US DoD 東部  |芝加哥、達拉斯、紐約、鳳凰城、聖安東尼奧、西雅圖、矽谷、華盛頓特區 |
| **中國東部** |中國東部、中國東部 2 |上海、上海 2 |
| **中國北部** |中國北部、中國北部 2 |北京、北京 2 |
| **德國** |德國中部、德國東部 |柏林、法蘭克福 |

標準 ExpressRoute SKU 不支援跨地緣政治區域的連線。 您必須啟用 ExpressRoute 進階附加元件，以支援全球連線。 不支援連線至國家雲端環境。 如果有需要的話，您可以聯絡您的連線提供者。

## <a name="locations"></a>連線提供者位置

下表顯示連線的位置以及每個位置的服務提供者。 如果您想檢視服務提供者及其可提供服的各個位置，請參閱[服務提供者位置](expressroute-locations.md#locations)。 

**本機的 Azure 區域**是所[ExpressRoute 本機](expressroute-faqs.md)可以存取每個對等互連位置。 **n/a**指出，ExpressRoute 本機無法使用該對等互連的位置。


### <a name="production-azure"></a>生產 Azure
| **Location** | **對等互連位置擁有者** | **本機的 Azure 區域** | **服務提供者** |
| --- | --- | --- | --- |
| **阿姆斯特丹** | Equinix | 西歐 | Aryaka Networks、AT&T NetBond、British Telecom、Colt、Equinix、euNetworks、GÉANT、InterCloud、Interxion、KPN、 IX Reach、Level 3 Communications、Megaport、NTT Communications、Orange、Tata Communications、TeleCity Group、Telefonica、Telenor、Telia Carrier、Verizon、Zayo |
| **阿姆斯特丹2** | Interxion | 西歐 | CenturyLink Cloud Connect、 DE CIX、 Interxion、 Vodafone |
| **亞特蘭大** | Equinix | n/a | Equinix、Megaport |
| **奧克蘭** | Vocus | n/a | Devoli，Kordia、 Megaport |
| **斧山** |LG CNS | 南韓南部 | LG CNS |
| **坎培拉** | CDC | 澳大利亞中部 | CDC |
| **坎培拉2** | CDC | 澳大利亞中部 2| CDC |
| **開普敦** | Teraco | 南非西部 | Internet Solutions - Cloud Connect、Liquid Telecom、Teraco |
| **辰內** | Tata Communications | 印度南部 | Global CloudXchange (GCX)、SIFY、Tata Communications |
| **辰內2** | Airtel | 印度南部 | Airtel |
| **芝加哥** | Equinix | 美國中北部 | Aryaka Networks、 At&t NetBond AT、 CenturyLink Cloud Connect、 Cologix、 Comcast、 Coresite、 Equinix、 InterCloud、 Internet2、 Level 3 Communications、 Megaport、 PacketFabric、 PCCW Global Limited、 衝刺 （sprint)、 Telia 電信業者、 Verizon、 Zayo |
| **達拉斯** | Equinix | n/a | Aryaka Networks、 AT At&t NetBond、 Cologix、 Equinix、 Internet2、 Level 3 Communications、 Megaport、 Neutrona Networks、 Telmex Uninet Telia 貨運公司，Transtelco、 Verizon、 Zayo|
| **丹佛** | CoreSite | 美國中西部 | CoreSite、 Megaport、 Zayo |
| **Dubai** | Etisalat 阿拉伯聯合大公國 | 阿拉伯聯合大公國北部 | Etisalat 阿拉伯聯合大公國 |
| **Dubai2** | du datamena | 阿拉伯聯合大公國北部 | du datamena Orixcom |
| **都柏林** | Equinix | 北歐 | Colt、eir、Equinix、Interxion、Megaport |
| **法蘭克福** | Interxion | n/a | DE-CIX、 Interxion |
| **中國香港特別行政區** | Equinix | 東亞 | Aryaka Networks、 British Telecom、 CenturyLink Cloud Connect、 首席 Telecom、 China Telecom Global、 Equinix、 Megaport、 NTT Communications、 Orange、 PCCW Global Limited、 Tata Communications、 Telia 電信業者、 Verizon |
| **約翰尼斯堡** | Teraco | 南非北部 | British Telecom、 Internet Solutions-Cloud Connect、 Liquid Telecom、 Teraco |
| **吉隆坡** | TIME dotCom | n/a | TIME dotCom |
| **拉斯維加斯** | 參數 | n/a | CenturyLink Cloud Connect、Megaport |
| **倫敦** | Equinix | 英國南部 | AT&T NetBond、British Telecom、Colt、Equinix、InterCloud、Internet Solutions - Cloud Connect、Interxion、Jisc、Level 3 Communications、Megaport、MTN、NTT Communications、Orange、PCCW Global Limited、Tata Communications、Telehouse - KDDI、Telenor、Telia Carrier、Verizon、Vodafone、Zayo |
| **London2** | Telehouse | 英國南部 | IX 觸達、 Equinix |
| **洛杉磯** | CoreSite | n/a | CoreSite、Equinix、Megaport、Neutrona Networks、NTT、Zayo |
| **馬賽** |Interxion | 法國南部 | DE-CIX、 Interxion、 豹網路 |
| **墨爾本** | NEXTDC | 澳大利亞東南部 | AARNet，Devoli，Equinix、 Megaport、 NEXTDC、 Optus、 Telstra Corporation，TPG Telecom |
| **邁阿密** | Equinix | n/a | C3ntro+、Equinix、Megaport、Neutrona Networks |
| **蒙特婁** | Cologix | n/a | Bell Canada、Cologix、Telus、Zayo |
| **孟買** | Tata Communications | 印度西部 | Global CloudXchange (GCX)、 依賴 Jio、 Sify、 Tata Communications、 Verizon |
| **孟買2** | Airtel | 印度西部 | Airtel、Sify、Vodafone Idea |
| **紐約** | Equinix | n/a | CenturyLink Cloud Connect、 Coresite、 Equinix、 InterCloud、 Megaport、 封包、 Zayo |
| **Newport(Wales)** | 新一代資料 | 英國西部 | British Telecom、 Colt、 Level 3 Communications、 新一代資料 |
| **大阪** | Equinix | 日本西部 | Colt、 Equinix、 Internet Initiative Japan Inc.-IIJ、 NTT Communications、 NTT SmartConnect、 Softbank |
| **巴黎** | Interxion | 法國中部 | CenturyLink Cloud Connect、 Colt、 Equinix、 Intercloud、 Interxion、 Orange、 Telia 電信業者、 Zayo |
| **伯斯** | NEXTDC | n/a | Megaport、 NextDC |
| **魁北克市** | 4Degrees | 加拿大東部 | Bell Canada、Megaport |
| **聖安東尼奧** | CyrusOne | 美國中南部 | CenturyLink Cloud Connect、Megaport |
| **聖保羅** | Equinix | 巴西南部 | Aryaka Networks、Ascenty Data Centers、British Telecom、Equinix、Level 3 Communications、Neutrona Networks、Orange、Tata Communications、Telefonica、UOLDIVEOW |
| **Seattle** | Equinix | 美國西部 2 | Aryaka Networks、 Equinix、 Level 3 Communications、 Megaport、 Telus、 Zayo |
| **首爾** | KINX | 南韓中部 | KINX、LG CNS、Sejong Telecom |
| **矽谷** | Equinix | 美國西部 | Aryaka Networks、 AT & T NetBond、 British Telecom、 CenturyLink Cloud Connect、 Comcast、 Coresite、 Equinix、 InterCloud、 IX 觸達，封包、 PacketFabric、 Level 3 Communications、 Megaport、 Orange、 衝刺 （sprint）、 Tata Communications、 Verizon、 Zayo |
| **矽 Valley2** | Coresite | 美國西部 | Coresite | 
| **新加坡** | Equinix | 東南亞 | Aryaka Networks、AT&T NetBond、British Telecom、Epsilon Global Communications、Equinix、InterCloud、Level 3 Communications、Megaport、NTT Communications、Orange、SingTel、Tata Communications、Telstra Corporation、Verizon、Vodafone |
| **新加坡 2** | 全域切換 | 東南亞 | Colt、 Epsilon Global Communications、 Megaport、 SingTel |
| **雪梨** | Equinix | 澳洲東部 | AARNet、 AT & T NetBond，British Telecom，Devoli，Equinix，Kordia 的 Megaport、 NEXTDC、 NTT Communications，Optus，橙色，Telstra Corporation，TPG Telecom、 Verizon |
| **台北** | Chief Telecom | n/a | 首席 Telecom、 FarEasTone |
| **東京** | Equinix | 日本東部 | Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Colt、Equinix、Internet Initiative Japan Inc. - IIJ、NTT Communications、NTT EAST、Orange、Softbank、Verizon |
| **多倫多** | Cologix | 加拿大中部 | At&t netbond、 Bell Canada、 CenturyLink Cloud Connect、 Cologix、 Equinix、 IX 觸達 Megaport、 Telus、 Verizon、 Zayo |
| **華盛頓** | Equinix | 美國東部、 美國東部 2 | Aryaka Networks、 AT At&t NetBond、 British Telecom、 CenturyLink Cloud Connect、 Cologix、 Comcast、 Coresite、 Equinix、 Internet2、 InterCloud、 Level 3 Communications、 Megaport、 Neutrona Networks、 NTT Communications、 Orange、 PacketFabric、 衝刺 （sprint）、 Tata通訊、 Telia 電信業者、 Verizon、 Zayo |
| **華盛頓特區2** | Coresite | 美國東部、 美國東部 2 |Coresite | 
| **Zurich** | Interxion | n/a | Intercloud、 Interxion |

 **+** 表示即將推出

### <a name="national-cloud-environments"></a>國家雲端環境

### <a name="us-government-cloud"></a>美國政府雲端
| **Location** | **服務提供者** |
| --- | --- |
| **芝加哥** |AT&T NetBond、Equinix、Level 3 Communications、Verizon |
| **達拉斯** |Equinix、Megaport、Verizon |
| **紐約** |Equinix、CenturyLink Cloud Connect、Verizon |
| **Phoenix** | AT At&t NetBond、 CenturyLink Cloud Connect、 Megaport |
| **聖安東尼奧** | CenturyLink Cloud Connect、Megaport |
| **矽谷** | Equinix、LLevel 3 Communications、Verizon |
| **Seattle** | Equinix、Megaport |
| **華盛頓** |AT At&t NetBond、 CenturyLink Cloud Connect、 Equinix、 Level 3 Communications、 Megaport、 Verizon |

### <a name="china"></a>中國
| **Location** | **服務提供者** |
| --- | --- |
| **北京** |China Telecom |
| **北京 2** | China Telecom、 GDS |
| **上海** |China Telecom |
| **上海 2** | China Telecom、 GDS |

若要深入了解，請參閱 [ExpressRoute (中國)](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>德國
| **位置** | **服務提供者** |
| --- | --- |
| **柏林** |e-shelter、Megaport+、T-Systems |
| **法蘭克福** |Colt、Equinix、Interxion |

## <a name="c1partners"></a>透過 Exchange 提供者連線
如果上一節中未列出您的連線提供者，您仍然可以建立連線。

* 請洽詢您的連線提供者，以了解他們是否連線到上方表格中列出的任何 Exchange 提供者。 您可以檢查下列連結，以收集 Exchange 提供者所提供之服務的相關詳細資訊。 已有數個連線提供者連線到乙太網路 Exchange。
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  
* 請您的連線提供者將您的網路延伸到選擇的對等互連位置。
  * 請確保您的連線提供者以高可用性的方式延伸您的連線，因此不會有單一失敗點。
* 排序一個 ExpressRoute 循環，將 Exchange 視為連線至 Microsoft 的連線提供者。
  * 依照 [建立 ExpressRoute 循環](expressroute-howto-circuit-classic.md) 中的步驟來設定連線。

## <a name="c1partners"></a>透過額外服務提供者連線
| **Location** | **Exchange** | **連線提供者** |
| --- | --- | --- |
| **阿姆斯特丹** | Equinix、Telecity | BICS、CloudXpress、Eurofiber、Fastweb S.p.A、Gulf Bridge International、MainOne、Nianet、Post、Proximus、TDC Erhverv、Telecom Italia Sparkle、Telia |
| **亞特蘭大** | Equinix| 皇冠 Castle
| **開普敦** | Teraco | MTN |
| **芝加哥** | Equinix | 皇冠 Castle，範圍的 Enterprise、 Windstream |
| **達拉斯** | Equinix、Megaport | Axtel、c3ntro Telecom、 Cox Business 皇冠 Castle 資料 Foundry、 廣泛的 Enterprise、 Transtelco |
| **法蘭克福** | Telecity | BICS、Cinia、Nianet、QSC AG |
| **漢堡** | Equinix | Cinia |
| **中國香港特別行政區** | Equinix | Chief、Macroview Telecom |
| **約翰尼斯堡** | Teraco | MTN |
| **倫敦** | BICS、Equinix、euNetworks、Telecity | Bezeq International Ltd.、 CoreAzure Telecommunications Limited、 Exponential E、 HSO、 NexGen Networks、 Proximus、 Tamares Telecom、 Zain |
| **洛杉磯** | Equinix |皇冠 Castle，範圍的 Enterprise、 Transtelco |
| **馬德里** | Level3 | Zertia |
| **蒙特婁** | Cologix、Equinix | Airgate Technologies, Inc.Cogeco Peer 1、Rogers、Zirro |
| **紐約** |Equinix、Megaport | Altice Business 皇冠 Castle 頻譜 Enterprise、 Webair |
| **巴黎** | Equinix | Proximus |
| **魁北克市** | Megaport | Fibrenoire |
| **聖保羅** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **矽谷** |Coresite、 Equinix | Cox Business、 廣泛企業、 Windstream，X2nsat Inc. |
| **新加坡** |Equinix |1CLOUDSTAR、BICS、Epsilon Telecommunications Limited、LGA Telecom、United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **雪梨** | Megaport | Macquarie Telecom Group|
| **東京** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **多倫多** | Equinix、Megaport | Airgate Technologies Inc.、 Beanfield Metroconnect、 Cogeco Peer 1、 IVedha Inc、 Rogers、 Thinktel、 Zirro|
| **華盛頓** |Equinix | Altice Business、 BICS，Cox Business，皇冠 Castle，Gtt Communications Inc、 Epsilon Telecommunications Limited、 Masergy、 Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute 系統整合者
根據您的網路規模，為符合您的需求而啟用私人連線可能有一定的難度。 您可以使用下表所列出的任何系統整合者來協助您開始使用 ExpressRoute。

| **Continent** | **系統整合者** |
| --- | --- |
| **亞洲** |Avanade Inc.、OneAs1a |
| **澳大利亞** | Ensyst、IT Consultancy、MOQdigital、Vigilant.IT |
| **歐洲** |Avanade Inc.、Altogee、Bright Skies GmbH、Inframon、MSG Services、New Signature、Nelite、Orange Networks、sol-tec |
| **北美洲** |Avanade Inc.、Equinix Professional Services、FlexManage、Lightstream、Perficient、Presidio |
| **南美洲** |Avanade Inc.、Venha Pra Nuvem |
## <a name="next-steps"></a>後續步驟
* 如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。
* 請確定符合所有必要條件。 請參閱 [ExpressRoute 必要條件](expressroute-prerequisites.md)。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "位置圖"
