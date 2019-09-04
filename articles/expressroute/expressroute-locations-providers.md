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
ms.date: 09/03/2019
ms.author: cherylmc
ms.openlocfilehash: 5469bd835b189df66c22a3fc13e91c435b4be73f
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257704"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 合作夥伴和對等互連位置

> [!div class="op_single_selector"]
> * [依提供者的位置](expressroute-locations.md)
> * [依位置的提供者](expressroute-locations-providers.md)


本文中的表格提供 ExpressRoute 地理涵蓋範圍和位置、ExpressRoute 連線提供者, 以及 ExpressRoute 系統整合者 (SIs) 的相關資訊。

> [!Note]
> Azure 區域和 ExpressRoute 位置是兩個不同的概念, 瞭解兩者之間的差異對於探索 Azure 混合式網路連線非常重要。 
>
>

## <a name="azure-regions"></a>Azure 區域
Azure 區域是「Azure 計算」、「網路」和「儲存體」資源所在的全球資料中心。 建立 Azure 資源時, 客戶需要選取資源位置。 資源位置會決定資源建立所在的 Azure 資料中心 (或可用性區域)。

## <a name="expressroute-locations"></a>ExpressRoute 位置
ExpressRoute 位置 (有時稱為「對等位置」或「符合我的位置」) 是 Microsoft Enterprise edge (MSEE) 裝置所在的共同位置設施。 ExpressRoute 位置是 Microsoft 網路的進入點, 而且會全域散發, 讓客戶有機會連接到全球各地的 Microsoft 網路。 這些位置是 ExpressRoute 合作夥伴和 ExpressRoute Direct 客戶向 Microsoft 網路發出交叉連線的地方。 一般而言, ExpressRoute 位置不需要符合 Azure 區域。 例如, 客戶可以在*西雅圖*對等互連位置中, 建立資源位置為*美國東部*的 ExpressRoute 線路。

如果您至少與地緣政治區域內的一個 ExpressRoute 位置連線，您將有權存取地緣政治區域內所有區域中的 Azure 服務。 

## <a name="locations"></a>地緣政治區域內 ExpressRoute 位置的 Azure 區域
下表提供地緣政治區域內 ExpressRoute 位置的 Azure 區域對應。

| **地緣政治區域** | **區域** | **Azure 區域** | **ExpressRoute 位置** |
| --- | --- | --- | --- |
| **澳洲政府** | 1 | 澳大利亞中部、澳大利亞中部 2 |坎培拉、坎培拉 2 |
| **歐洲** | 1 |法國中部、法國南部、北歐、西歐、英國西部、英國南部 |阿姆斯特丹、阿姆斯特丹2、哥本哈根、都柏林、法蘭克福、倫敦、倫敦2、馬賽、紐波特 (威爾士)、巴黎、斯德哥爾摩、蘇黎世 |
| **北美洲** | 1 |美國東部、美國西部、美國東部 2、美國西部 2、美國中部、美國中南部、美國中北部、美國中西部、加拿大中部、加拿大東部 |亞特蘭大、芝加哥、達拉斯、丹佛、內華達州、洛杉磯、邁阿密、紐約、San Antonio、西雅圖、矽谷、矽 Valley2、華盛頓特區、華盛頓特區、蒙特利爾、魁北克市、多倫多 |
| **亞洲** | 2 |東亞、東南亞 |香港特別行政區、吉隆坡、新加坡、新加坡2、臺北 |
| **印度** | 2 |印度西部、印度中部、印度南部 |辰內、辰內 2、孟買、孟買 2 |
| **日本** | 2 |日本西部、日本東部 |大阪、東京 |
| **大洋洲** | 2 |澳洲東南部、澳洲東部 |奧克蘭, 墨爾本, 通過, 悉尼 | 
| **南韓** | 2 |南韓中部、南韓南部 |釜山、首爾|
| **阿拉伯聯合大公國** | 3 | 阿拉伯聯合大公國中部, 阿拉伯聯合大公國北部 | 杜拜、Dubai2 |
| **南非** | 3 |南非西部, 南非北部 |開普敦、約翰尼斯堡 |
| **南美洲** | 3 |巴西南部 |Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>適用于國家雲端的 Azure 區域和地緣政治界限
下表提供國家雲端的區域和地理政治界限等資訊。

| **地緣政治區域** | **Azure 區域** | **ExpressRoute 位置** |
| --- | --- | --- |
| **美國政府雲端** |US Gov 亞利桑那州、US Gov 愛荷華州、US Gov 德克薩斯州、US Gov 維吉尼亞州、US DoD 中部、US DoD 東部  |芝加哥、達拉斯、紐約、鳳凰城、聖安東尼奧、西雅圖、矽谷、華盛頓特區 |
| **中國東部** |中國東部、中國東部 2 |上海、上海 2 |
| **中國北部** |中國北部、中國北部 2 |北京、北京 2 |
| **德國** |德國中部、德國東部 |柏林、法蘭克福 |

標準 ExpressRoute SKU 不支援跨地緣政治區域的連線。 您必須啟用 ExpressRoute 進階附加元件，以支援全球連線。 不支援連線至國家雲端環境。 如果有需要的話，您可以聯絡您的連線提供者。

## <a name="partners"></a>ExpressRoute 連線提供者

下表顯示連線的位置以及每個位置的服務提供者。 如果您想檢視服務提供者及其可提供服的各個位置，請參閱[服務提供者位置](expressroute-locations.md)。 

**本機 Azure 區域**是在每個對等互連位置的[ExpressRoute 本機](expressroute-faqs.md)可以存取的區域。 **n/a**表示 ExpressRoute 本機無法在該對等互連位置使用。


### <a name="production-azure"></a>生產 Azure
| **位置** | **擁有者和位址** | **本機 Azure 區域** | **服務提供者** |
| --- | --- | --- | --- |
| **阿姆斯特丹** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 西歐 | Aryaka Networks、AT&T NetBond、British Telecom、Colt、Equinix、euNetworks、GÉANT、InterCloud、Interxion、KPN、 IX Reach、Level 3 Communications、Megaport、NTT Communications、Orange、Tata Communications、TeleCity Group、Telefonica、Telenor、Telia Carrier、Verizon、Zayo |
| **阿姆斯特丹2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 西歐 | CenturyLink Cloud Connect、CIX、Interxion、Vodafone |
| **亞特蘭大** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | n/a | Equinix、Megaport |
| **奧克蘭** | [Vocus Group NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | n/a | Devoli、Kordia、Megaport、Spark NZ、Vocus Group NZ |
| **斧山** | [LG CNS](https://datacenter.lgcns.com/Contents/En/Menu_1/Locations_1.aspx) | 南韓南部 | LG CNS |
| **坎培拉** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 澳大利亞中部 | CDC |
| **坎培拉2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 澳大利亞中部 2| CDC |
| **開普敦** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 南非西部 | Internet Solutions - Cloud Connect、Liquid Telecom、Teraco |
| **辰內** | Tata Communications | 印度南部 | Global CloudXchange (GCX)、SIFY、Tata Communications |
| **辰內2** | Airtel | 印度南部 | Airtel |
| **芝加哥** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 美國中北部 | Aryaka networks 網路, 位於 & T NetBond、CenturyLink Cloud Connect、Cologix、Comcast、Coresite、Equinix、InterCloud、Internet2、Level 3 通訊、Megaport、PacketFabric、PCCW GLOBAL LIMITED Global 有限、短期衝刺、Telia 貨運公司、Verizon、Zayo |
| **日** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | n/a | Interxion |
| **達拉斯** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | n/a | Aryaka networks 網路, 位於 & T NetBond、Cologix、Equinix、Internet2、Level 3 通訊、Megaport、Neutrona Networks、Telmex Uninet、Telia 電訊廠商、Transtelco、Verizon、Zayo|
| **丹佛** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 美國中西部 | CoreSite、Megaport、Zayo |
| **杜拜** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 阿拉伯聯合大公國北部 | Etisalat 阿拉伯聯合大公國 |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 阿拉伯聯合大公國北部 | du datamena, Orixcom |
| **都柏林** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 北歐 | Colt、eir、Equinix、Interxion、Megaport |
| **法蘭克福** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | n/a | CIX、Interxion、橙色 |
| **香港特別行政區** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 東亞 | Aryaka networks Networks, 英屬電信, CenturyLink Cloud Connect, 首席電信, 中國電信全球, Equinix, Megaport, NTT 通訊, 橙色, PCCW GLOBAL LIMITED 全球有限, Tata 通訊, Telia 電訊商, Verizon |
| **約翰尼斯堡** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 南非北部 | 英屬電信, 網際網路解決方案-雲端連線, 液體電信, 橙色, Teraco |
| **吉隆坡** | [TIME dotCom Menara 的目標](https://www.aims.com.my/co-location/points-of-presence.html) | n/a | TIME dotCom |
| **拉斯維加斯** | [交換器 LV](https://www.switch.com/las-vegas) | n/a | CenturyLink Cloud Connect、Megaport |
| **倫敦** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 英國南部 | AT&T NetBond、British Telecom、Colt、Equinix、InterCloud、Internet Solutions - Cloud Connect、Interxion、Jisc、Level 3 Communications、Megaport、MTN、NTT Communications、Orange、PCCW Global Limited、Tata Communications、Telehouse - KDDI、Telenor、Telia Carrier、Verizon、Vodafone、Zayo |
| **London2** | [Telehouse 北部2](https://www.telehouse.com/global-data-centers/emea/london-data-centers/telehouse-north-two/) | 英國南部 | IX 觸及、Equinix |
| **洛杉磯** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | n/a | CoreSite、Equinix、Megaport、Neutrona Networks、NTT、Zayo |
| **馬賽** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 法國南部 | CIX、Interxion、Jaguar 網路 |
| **墨爾本** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 澳大利亞東南部 | AARNet、Devoli、Equinix、Megaport、NEXTDC、Optus、Telstra Corporation、TPG 電信 |
| **邁阿密** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | n/a | C3ntro+、Equinix、Megaport、Neutrona Networks |
| **蒙特婁** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | n/a | 鐘加拿大、Cologix、Megaport、Telus、Zayo |
| **孟買** | Tata Communications | 印度西部 | Global CloudXchange (GCX)、依賴 Jio、Sify、Tata 通訊、Verizon |
| **孟買2** | Airtel | 印度西部 | Airtel、Sify、Vodafone Idea |
| **紐約** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | n/a | CenturyLink Cloud Connect、Coresite、Equinix、InterCloud、Megaport、Packet、Zayo |
| **Newport(Wales)** | [新一代資料](https://www.nextgenerationdata.co.uk) | 英國西部 | 英屬電信, Colt, 層級3通訊, 新一代資料 |
| **大阪** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 日本西部 | Colt, Equinix, 網際網路方案日本 Inc.-IIJ, NTT 通訊, NTT SmartConnect, Softbank |
| **巴黎** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 法國中部 | CenturyLink Cloud Connect、Colt、Equinix、Intercloud、Interxion、橙色、Telia 電訊廠商、Zayo |
| **伯斯** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | n/a | Megaport、NextDC |
| **魁北克市** | [有利](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 加拿大東部 | Bell Canada、Megaport |
| **聖安東尼奧** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 美國中南部 | CenturyLink Cloud Connect、Megaport |
| **聖保羅** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 巴西南部 | Aryaka Networks、Ascenty Data Centers、British Telecom、Equinix、Level 3 Communications、Neutrona Networks、Orange、Tata Communications、Telefonica、UOLDIVEOW |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 美國西部 2 | Aryaka networks 網路、Equinix、層級3通訊、Megaport、Telus、Zayo |
| **首爾** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 南韓中部 | KINX、LG CNS、Sejong Telecom |
| **矽谷** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 美國西部 | Aryaka networks 網路, 位於 & T NetBond、英屬電信、CenturyLink Cloud Connect、Comcast、Coresite、Equinix、InterCloud、Internet2、IX 觸及、封包、PacketFabric、Level 3 通訊、Megaport、橙色、短期衝刺、Tata 通訊、Telia 貨運公司、Verizon、Zayo |
| **晶片 Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 美國西部 | Coresite | 
| **新加坡** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 東南亞 | Aryaka Networks、AT&T NetBond、British Telecom、Epsilon Global Communications、Equinix、InterCloud、Level 3 Communications、Megaport、NTT Communications、Orange、SingTel、Tata Communications、Telstra Corporation、Verizon、Vodafone |
| **新加坡 2** | [全域交換器 Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 東南亞 | Colt, Epsilon 全球通訊, Megaport, SingTel |
| **斯德哥爾摩** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | n/a | Telia 貨運公司 |
| **雪梨** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 澳大利亞東部 | AARNet, 位於 & T NetBond、英屬電信、Devoli、Equinix、Kordia、Megaport、NEXTDC、NTT 通訊、Optus、橙色、Spark NZ、Telstra Corporation、TPG 電信、Verizon、Vocus Group NZ |
| **台北** | Chief Telecom | n/a | 電信、FarEasTone |
| **東京** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 日本東部 | Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Colt、Equinix、Internet Initiative Japan Inc. - IIJ、NTT Communications、NTT EAST、Orange、Softbank、Verizon |
| **多倫多** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 加拿大中部 | 在 & T NetBond, 鐘加拿大, CenturyLink Cloud Connect, Cologix, Equinix, IX 觸及 Megaport, Telus, Verizon, Zayo |
| **華盛頓** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 美國東部、美國東部2 | Aryaka networks 網路, 位於 & T NetBond、英屬電信、CenturyLink Cloud Connect、Cologix、Comcast、Coresite、Equinix、Internet2、InterCloud、Level 3 通訊、Megaport、Neutrona 網路、NTT 通訊、橙色、PacketFabric、短期衝刺、Tata通訊, Telia 貨運公司, Verizon, Zayo |
| **華盛頓特區2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 美國東部、美國東部2 |Coresite | 
| **蘇黎世** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | n/a | Intercloud、Interxion |

 **+** 表示即將推出

### <a name="national-cloud-environments"></a>國家雲端環境

### <a name="us-government-cloud"></a>美國政府雲端
| **位置** | **服務提供者** |
| --- | --- |
| **芝加哥** |AT&T NetBond、Equinix、Level 3 Communications、Verizon |
| **達拉斯** |Equinix、Megaport、Verizon |
| **紐約** |Equinix、CenturyLink Cloud Connect、Verizon |
| **Phoenix** | 在 & T NetBond, CenturyLink Cloud Connect, Megaport |
| **聖安東尼奧** | CenturyLink Cloud Connect、Megaport |
| **矽谷** | Equinix、LLevel 3 Communications、Verizon |
| **Seattle** | Equinix、Megaport |
| **華盛頓** |在 & T NetBond、CenturyLink Cloud Connect、Equinix、Level 3 通訊、Megaport、Verizon |

### <a name="china"></a>中國
| **位置** | **服務提供者** |
| --- | --- |
| **北京** |China Telecom |
| **北京 2** | 中國電信, GDS |
| **上海** |China Telecom |
| **上海 2** | 中國電信, GDS |

若要深入了解，請參閱 [ExpressRoute (中國)](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>德國
| **位置** | **服務提供者** |
| --- | --- |
| **柏林** |e-shelter、Megaport+、T-Systems |
| **法蘭克福** |Colt、Equinix、Interxion |

## <a name="c1partners"></a>透過 Exchange 提供者的連線能力
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

## <a name="c1partners"></a>透過其他服務提供者的連線能力
| **位置** | **Exchange** | **連接提供者** |
| --- | --- | --- |
| **阿姆斯特丹** | Equinix, Interxion, Level 3 通訊, Telecity | BICS、CloudXpress、Eurofiber、Fastweb s.p.a、Kalaam、不帶群島、MainOne 電信巴林 B、Nianet、Proximus、後電信盧森堡、Erhverv、TDC Italia、電信 Telekom 火花、Deutschland GmbH Telia、 |
| **亞特蘭大** | Equinix| Crown Castle
| **開普敦** | Teraco | MTN |
| **芝加哥** | Equinix| Crown Castle, Windstream 的頻譜企業 |
| **達拉斯** | Equinix、Megaport | Axtel, C3ntro 電信, Cox Business, Crown Castle, 資料 Foundry, 頻譜企業, Transtelco |
| **法蘭克福** | Interxion、Telecity | BICS、Cinia、Nianet、QSC AG、Telekom Deutschland GmbH |
| **漢堡** | Equinix | Cinia |
| **香港特別行政區** | Equinix | Chief、Macroview Telecom |
| **約翰尼斯堡** | Teraco | MTN |
| **倫敦** | BICS、Equinix、euNetworks、Telecity | Bezeq 國際有限公司、CoreAzure、Epsilon 電信有限、指數 E、HSO、NexGen Networks、Proximus、Tamares 電信、Zain |
| **洛杉磯** | Equinix |Crown Castle, Transtelco 的頻譜企業 |
| **馬德里** | Level3 | Zertia |
| **蒙特婁** | Cologix、Equinix | Airgate Technologies, Inc.Aptum 技術, Rogers, Zirro |
| **紐約** |Equinix、Megaport | Altice Business、Crown Castle、彩虹 Enterprise、Webair |
| **巴黎** | Equinix | Proximus |
| **魁北克市** | Megaport | Fibrenoire |
| **聖保羅** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **矽谷** |Coresite、Equinix | Cox Business, 頻譜 Enterprise, Windstream, X2nsat Inc。 |
| **新加坡** |Equinix |1CLOUDSTAR, BICS, CMC 電信, Epsilon 電信限制, LGA 電信, 美國資訊公路 (UIH) |
| **Slough** | Equinix | HSO|
| **雪梨** | Megaport | Macquarie Telecom Group|
| **東京** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **多倫多** | Equinix、Megaport | Airgate 技術 Inc.、Beanfield Metroconnect、Aptum 技術、IVedha Inc.、Rogers、Thinktel、Zirro|
| **華盛頓** |Equinix | Altice Business、BICS、Cox Business、Crown Castle、Gtt communication Inc.、Epsilon 電信限制、Masergy、Windstream |

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
