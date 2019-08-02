---
title: 控制 Azure 澳大利亞的輸入流量
description: 在 Azure 澳大利亞控制輸入流量以符合澳大利亞政府對安全網際網路閘道需求的指南
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 808a615885129af1be9b7fdcdb64d5a8c5a25e40
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571649"
---
# <a name="controlling-ingress-traffic-in-azure-australia"></a>控制 Azure 澳大利亞的輸入流量

保護 ICT 系統的核心元素是控制網路流量。 流量應限制為只有系統需要此功能, 才能降低危害的可能性。

本指南提供在 Azure 內輸入 (輸入) 網路流量如何運作的詳細資料, 以及針對連線到網際網路的系統執行網路安全性控制的建議。

網路控制項與澳大利亞網路安全中心 (ACSC) 消費者指導方針和 ACSC 資訊安全手冊 (ISM) 的意圖一致。

## <a name="requirements"></a>需求

美國聯邦系統的整體安全性需求是在 ISM 中定義。 為了協助實現網路安全性的美國聯邦實體, ACSC 已發行[ACSC 保護:執行網路分割和隔離](https://www.acsc.gov.au/publications/protect/network_segmentation_segregation.htm), 並協助保護雲端環境中的系統, ACSC 已為租使用者發佈[雲端運算安全性](https://www.acsc.gov.au/publications/protect/Cloud_Computing_Security_for_Tenants.pdf)。

這些指南會概述用來執行網路安全性和控制流量的內容, 並包含網路設計和設定的實際建議。

在服務信任入口網站的澳大利亞頁面中, Microsoft Azure 指南的 Microsoft[雲端運算安全性](https://aka.ms/au-irap), 著重于可讓您在 ACSC 發行集中符合建議的特定 Microsoft 技術。

在 ACSC 的發行集中所識別的下列重要需求, 對於控制 Azure 中的輸入流量非常重要:

|描述|Source|
|---|---|
|**使用主機型防火牆和 CSP 的網路存取控制, 將輸入和輸出 VM 網路連線限制為只有所需的埠/通訊協定, 以執行網路分割和隔離, 例如, 多層式架構。**| _租使用者的雲端運算_|
|執行租使用者 (包括租使用者的遠端使用者) 與雲端服務之間**適當的高頻寬、低延遲、可靠的網路**連線, 以符合租使用者的可用性需求  | _租使用者的雲端運算_|
|**只在網路層套用技術**。 每個主機和網路都應該在可能的情況下, 以可完全管理的最低層級分割和隔離。 在大部分的情況下, 分割和隔離會從資料連結層套用至應用層, 並將其包含在內;不過, 在敏感性環境中, 實體隔離可能是適當的。 主機型和網路範圍的量值應該以互補的方式部署, 並集中監視。 使用防火牆或安全性設備作為唯一的安全性措施並不夠。 |_ACSC 保護:執行網路分割和隔離_|
|**使用最低許可權的原則, 並需要‐‐知道**。 如果主機、服務或網路不需要與另一個主機、服務或網路通訊, 則不應允許。 如果主機、服務或網路只需要使用特定埠或通訊協定與另一個主機、服務或網路交談, 則應該停用任何其他埠或通訊協定。 在網路上採用這些原則將可補充使用者權限的 minimisation, 並大幅增加環境的整體安全性狀態。 |_ACSC 保護:執行網路分割和隔離_|
|**根據商務作業的敏感度或重要性, 區分主機和網路**。 視不同的安全性分類、安全性網域或特定主機或網路的可用性/完整性需求而定, 可以使用不同的硬體或平臺來進行分隔。 特別的是, 個別的管理網路, 並考慮實體隔離頻外管理網路的敏感性環境。 |_ACSC 保護:執行網路分割和隔離_|
|針對**所有其他實體, 識別、驗證及授權所有實體的存取權**。 所有使用者、主機和服務都應該將其存取限制為只有其他使用者、主機和服務, 才能執行其指定的職責或功能。 所有會略過或降級識別、驗證和 authorisation 服務強度的舊版或本機服務, 應予以停用, 且應密切監控其使用方式。 |_ACSC 保護:執行網路分割和隔離_|
|**執行允許清單的網路流量, 而不是拒絕清單**。 只允許存取已知良好的網路流量 (也就是已識別、已驗證和已授權), 而不會拒絕存取已知的不正確網路流量 (例如封鎖特定的位址或服務)。 允許清單會產生列入封鎖清單的絕佳安全性原則, 並大幅改善組織的容量, 以偵測及評估可能的網路入侵。 |_ACSC 保護:執行網路分割和隔離_|
|

本文針對使用基礎結構即服務 (IaaS) 和平臺即服務 (PaaS) 部署在 Azure 中的系統, 提供如何滿足這些需求的資訊和建議。 您也應該閱讀[控制 Azure 澳大利亞](gateway-egress-traffic.md)的輸出流量一文, 以充分瞭解如何控制 azure 內的網路流量。

## <a name="architecture"></a>架構

如果您牽涉到網路安全性和輸入流量控制的設計或執行, 您必須先瞭解 Azure 中的輸入網路流量如何跨 IaaS 和 PaaS 運作。 本節概述網路流量可能會到達裝載于 Azure 之資源的可能進入點, 以及可用來限制及控制該流量的安全性控制項。 本指南的其餘各節將詳細討論每個元件。

### <a name="architecture-components"></a>架構元件

此處顯示的架構圖表會描述網路流量可以用來連接到 Azure 中裝載之服務的可能路徑。 這些元件會根據它們提供給輸入流量的函式, 分割成 Azure、IaaS 輸入、PaaS 輸入和安全性控制。

![架構](media/ingress-traffic.png)

### <a name="azure-components"></a>Azure 元件

|元件 | 描述|
|---|---|
|**DDoS 保護** | 分散式阻斷服務 (DDoS) 攻擊會嘗試耗盡應用程式的資源, 讓合法使用者無法使用應用程式。 DDoS 攻擊可以鎖定可透過網際網路公開觸達的任何端點。 Azure 透過 Azure 平臺自動包含 DDoS 保護, 並提供額外的緩和功能, 可針對特定應用程式加以啟用, 以進行更細微的控制。|
| **流量管理員** | Azure 流量管理員是以網域名稱系統 (DNS) 為基礎的流量負載平衡器, 可將流量以最佳方式分散到跨 Azure 區域的服務, 同時提供高可用性和回應性。 流量管理員使用 DNS, 根據流量路由方法和端點的健康情況, 將用戶端要求導向到最適當的端點。|
| **ExpressRoute** | ExpressRoute 是用來使用 Microsoft 雲端服務的專用網路連接。 它是透過連線提供者布建, 提供比透過網際網路一般連接更可靠、速度更快、延遲更低和更高的安全性。 ExpressRoute 線路代表內部部署基礎結構與 Microsoft 雲端服務之間透過連線提供者的邏輯連線。|
| **ExpressRoute 私人對等互連** | ExpressRoute 私用對等互連是內部部署環境與私人 Azure 虛擬網路之間的連線。 私人對等互連可讓您存取部署在虛擬網路內的 Azure 服務, 例如虛擬機器。 透過私用對等互連存取的資源和虛擬網路會被視為組織核心網路的延伸。 私人對等互連會使用私人 IP 位址, 在內部部署網路和 Azure 虛擬網路之間提供雙向連線能力。|
| **ExpressRoute Microsoft 對等互連** | ExpressRoute Microsoft 對等互連是內部部署環境與 Microsoft 與 Azure 公用服務之間的連線。 這包括與 Office 365、Dynamics 365 和 Azure PaaS 服務的連線能力。 對等互連是透過組織或連線提供者所擁有的公用 IP 位址來建立。 預設不會透過 ExpressRoute Microsoft 對等互連來存取任何服務, 而且組織必須加入宣告所需的服務。 然後, 此程式會提供與網際網路上可用之相同端點的連接。|
|

### <a name="iaas-ingress-components"></a>IaaS 輸入元件

|元件 | 描述|
|---|---|
|**網路介面** | 網路介面是存在於 Azure 中的資源。 它會附加至虛擬機器, 並從與其相關聯的子網中, 指派私人、非網際網路可路由傳送的 IP 位址。 此 IP 位址是透過 Azure Resource Manager 動態或靜態方式指派。|
|**子網路** | 子網是在 VNet 內建立的 IP 位址範圍。 可以在 VNet 內建立多個子網以進行網路分割。|
| **虛擬網路 (VNet)** | VNet 是 Azure 中的基礎資源, 可提供部署資源和啟用通訊的平臺和界限。 VNet 存在於 Azure 區域內, 並定義 VNet 整合式資源的 IP 位址空間和路由界限, 例如虛擬機器。|
| **VNet 對等互連** | VNet 對等互連是 Azure 設定選項, 可在兩個 Vnet 之間進行直接通訊, 而不需要虛擬網路閘道。 一旦對等互連之後, 這兩個 Vnet 就可以直接通訊, 而其他設定可以控制虛擬網路閘道和其他傳輸選項的使用。|
| **公用 IP** | 「公用 IP」是一種資源, 可從指定的區域保留其中一個 Microsoft 擁有的公用、網際網路可路由 IP 位址, 以便在虛擬網路中使用。 它可以與特定的網路介面相關聯, 讓資源可以從網際網路、ExpressRoute 和 PaaS 系統存取。|
| **ExpressRoute 閘道** | ExpressRoute 閘道是虛擬網路中的物件, 可透過 ExpressRoute 線路上的私用對等互連, 提供從虛擬網路到內部部署網路的連線和路由。|
| **VPN 閘道** | VPN 閘道是虛擬網路中的物件, 可提供從虛擬網路到外部網路的加密通道。 加密的通道可以是站對站, 以進行與內部部署環境、其他虛擬網路或雲端環境的雙向通訊, 或點對站, 以便與單一端點進行通訊。|
| **PaaS VNet 整合** | 許多 PaaS 功能可以部署到虛擬網路或與之整合。 有些 PaaS 功能可以與 VNet 完全整合, 而且只能透過私人 IP 位址存取。 其他專案 (例如 Azure Load Balancer 和 Azure 應用程式閘道) 可以具有具有公用 IP 位址的外部介面, 以及虛擬網路內具有私人 IP 位址的內部介面。 在此情況下, 流量可以透過 PaaS 功能進入虛擬網路。|
|

### <a name="paas-ingress-components"></a>PaaS 輸入元件

|元件 | 描述|
|---|---|
|主機名稱 | Azure PaaS 功能是由資源建立時指派的唯一公用主機名稱所識別。 此主機名稱接著會註冊到公用 DNS 網域中, 它可以解析成公用 IP 位址。|
|**公用 IP** | 除非部署在 VNet 整合式設定中, 否則會透過網際網路可路由傳送的公用 IP 位址來存取 Azure PaaS 功能。 此位址可專用於特定資源 (例如公用 Load Balancer), 或可與具有多個實例 (例如儲存體或 SQL) 之共用進入點的特定功能相關聯。 此公用 IP 位址可從網際網路、ExpressRoute 或透過 Azure 骨幹網路的 IaaS 公用 IP 位址存取。|
|**服務端點** | 服務端點提供從虛擬網路到特定 PaaS 功能的直接私用連線。 服務端點僅適用于 PaaS 功能的子集, 可針對存取 PaaS 的 VNet 中的資源提供更高的效能和安全性。|
|

### <a name="security-controls"></a>安全性控制

|元件 | 描述|
|---|---|
|**網路安全性群組 (Nsg)** | Nsg 控制在 Azure 中進出虛擬網路資源的流量。 Nsg 會針對允許或拒絕的流量套用規則, 包括 Azure 中的流量, 以及 Azure 和外部網路 (例如內部部署或網際網路) 之間的流量。 Nsg 會套用至虛擬網路內的子網或個別的網路介面。|
|**PaaS 防火牆** | 許多 PaaS 功能 (例如儲存體和 SQL) 都有內建防火牆, 可控制特定資源的輸入網路流量。 您可以建立規則, 以允許或拒絕來自特定 IP 位址和/或虛擬網路的連線。|
|**PaaS 驗證和存取控制** | PaaS 功能是多層式安全性方法的一部分, 提供驗證使用者及控制許可權和存取的多種機制。|
|**Azure 原則** | Azure 原則是 Azure 中的一項服務, 可用於建立、指派和管理原則。 這些原則會使用規則來控制可以部署的資源類型, 以及這些資源的設定。 如果原則不符合需求, 或可用來進行監視以報告合規性狀態, 則可以使用原則來強制執行合規性。|
|

## <a name="general-guidance"></a>一般指導

若要在 Azure 內設計和建立安全的解決方案, 請務必瞭解並控制網路流量, 以便只進行已識別和授權的通訊。 本指南的目的和後續章節中的特定元件指引, 是描述可使用的工具和服務, 以套用_ACSC 保護中所述的原則:跨 Azure 工作負載執行_網路分割和隔離。 這包括詳細說明如何建立虛擬架構來保護資源, 而不可能套用內部部署環境中可能的相同傳統實體和網路控制。

### <a name="specific-focus-areas"></a>特定焦點區域

* 限制虛擬網路的進入點數目
* 限制公用 IP 位址的數目
* 請考慮使用虛擬網路的中樞和輪輻網路設計, 如 Microsoft 虛擬資料中心 (VDC) 檔中所述
* 使用內建的安全性功能為來自網際網路的輸入連線利用產品 (例如, 應用程式閘道、API 閘道、網路虛擬裝置)
* 將通訊流程限制為僅限系統功能所需的 PaaS 功能
* 在 VNet 整合式設定中部署 PaaS, 以增加隔離與控制
* 將系統設定為使用加密機制, 以配合 ACSC 取用者指引和 ISM
* 除了傳統的網路控制之外, 還可以使用以身分識別為基礎的保護, 例如驗證和以角色為基礎的存取控制
* 針對與內部部署網路的連線能力執行 ExpressRoute
* 執行 Vpn 以進行系統管理流量, 並與外部網路整合
* 利用 Azure 原則, 將區域和資源限制為只有系統功能才需要
* 利用 Azure 原則, 以針對可存取網際網路的資源強制執行基準安全性設定

### <a name="additional-resources"></a>其他資源

|Resource | 連結|
|---|---|
|澳大利亞法規和原則合規性檔, 包括取用者指引|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Azure 虛擬資料中心|[https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)|
|ACSC 網路分割|[https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)|
|租使用者的 ACSC 雲端安全性| [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)|
|ACSC 資訊安全性手冊|[https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)|

## <a name="component-guidance"></a>元件指引

本節針對在 Azure 中部署的系統輸入流量相關的個別元件, 提供進一步的指引。 每一節都會說明特定元件的目的, 並提供檔和設定指南的連結, 可用來協助設計和建立活動。

## <a name="azure"></a>Azure

Azure 內資源的所有通訊都會通過 Microsoft 維護的網路基礎結構, 以提供連線能力和安全性功能。 Microsoft 會自動設定一系列的保護, 以保護 Azure 平臺和網路基礎結構, 並提供其他功能作為 Azure 中的服務, 以控制網路流量並建立網路分割, 以及職責.

### <a name="ddos-protection"></a>DDoS保護

網際網路可存取的資源很容易遭受 DDoS 攻擊。 為了防範這些攻擊, Azure 提供基本和標準層級的 DDoS 保護。

「基本」會自動啟用為 Azure 平臺的一部分, 包括 alwayson 流量監視, 以及常見網路層級攻擊的即時緩和措施, 提供 Microsoft 線上服務所使用的相同 defences。 Azure 全域網路的整體規模可用於分散和減少跨區域攻擊流量。 為 IPv4 和 IPv6 Azure 公用 IP 位址提供保護

標準針對專為 Azure 虛擬網路資源調整的基本服務層級, 提供額外的緩和功能。 保護原則是透過專用的流量監視和機器學習演算法進行調整。 針對 IPv4 Azure 公用 IP 位址提供保護。

|Resource|連結|
|---|---|
|Azure DDoS 保護總覽|[https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)|
|Azure DDoS 最佳做法|[https://docs.microsoft.com/azure/security/azure-ddos-best-practices](https://docs.microsoft.com/azure/security/azure-ddos-best-practices)|
|管理 DDoS 保護|[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)|
|

### <a name="traffic-manager"></a>流量管理員

流量管理員可用來控制應用程式接收連線的端點, 藉此管理輸入流量。 為了防止因為網路安全性攻擊而失去系統或應用程式的可用性, 或在系統入侵後復原服務, 流量管理員可以用來將流量重新導向至可運作的應用程式實例。

|Resource|連結|
|---|---|
|流量管理員概觀 | [https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)|
|使用 Azure DNS 和流量管理員指南的嚴重損壞修復 | [https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager](https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager)|
|

### <a name="expressroute"></a>ExpressRoute

ExpressRoute 可用來建立從內部部署環境到 Azure 中裝載之系統的私用路徑。 此連接可透過增強的網路通訊隱私權, 提供更高的可靠性和保證的效能。 「Express Route」可讓「聯邦」實體控制來自內部部署環境的輸入流量, 並定義要用於輸入防火牆規則和存取控制清單的特定組織專用位址。

|Resource | 連結|
|---|---|
|ExpressRoute 概欟 | [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)|
|ExpressRoute 連線模型 | [https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models](https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models)|
|

### <a name="expressroute-private-peering"></a>ExpressRoute 私人對等互連

私用對等互連提供一個機制, 將內部部署環境擴充至僅使用私人 IP 位址的 Azure。 這可讓「聯邦」實體整合 Azure 虛擬網路和位址範圍與現有的內部部署系統和服務。 私用對等互連可確保跨 ExpressRoute 的通訊僅適用于組織所授權的虛擬網路。 如果您使用私人對等互連, 則必須將網路虛擬裝置 (NVA) (而非 Azure VPN 閘道) 實作為 ACSC 取用者指導方針所要求, 以建立內部部署網路的安全 VPN 通訊。

|Resource | 連結|
|---|---|
|ExpressRoute 私用對等互連總覽 | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|ExpressRoute 私用對等互連操作說明指南 | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private)|
|

### <a name="expressroute-microsoft-peering"></a>ExpressRoute Microsoft 對等互連

Microsoft 對等互連可提供與 Microsoft 公用服務的高速、低延遲連線, 而不需要穿越網際網路。 這可為連線提供更高的可靠性、效能和隱私權。 藉由使用路由篩選, 可將通訊限制為僅限於所需的 Azure 區域, 但這包括其他組織所裝載的服務, 而且可能需要在下列專案之間進行額外的篩選或檢查功能:內部部署環境和 Microsoft。

「自由」實體可以使用透過對等互連關聯性所建立的專用公用 IP 位址, 來唯一識別內部部署環境, 以便在 PaaS 功能內的防火牆和存取控制清單中使用。

另一種方式是, 可讓您使用 ExpressRoute Microsoft 對等互連作為 underlay 網路, 以透過 Azure VPN 閘道來建立 VPN 連線能力。 在此模型中, 不會透過 ExpressRoute 從內部內部部署網路到 Azure 公用服務進行作用中的通訊, 但要達成私人虛擬網路的安全連線, 可以符合 ACSC 取用者指導方針。

|Resource | 連結|
|---|---|
|ExpressRoute Microsoft 對等互連總覽 | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|ExpressRoute Microsoft 對等互連操作指南 | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft)|
|

## <a name="iaas-ingress"></a>IaaS 輸入

本節提供控制 IaaS 元件之輸入流量的元件指引。 IaaS 包含虛擬機器和其他可在 Azure 的虛擬網路內部署和管理的計算資源。 為了讓流量抵達使用 IaaS 部署的系統, 它必須具有虛擬網路的進入點, 您可以透過公用 IP 位址、虛擬網路閘道或虛擬網路對等互連關聯性來建立。

### <a name="network-interface"></a>網路介面

網路介面是虛擬機器所有流量的進入點。 網路介面可讓您設定 IP 位址, 並且可以用來套用 Nsg 或透過網路虛擬裝置來路由傳送流量。 虛擬機器的網路介面應已規劃並適當地設定, 以配合整體網路分割和隔離目標。

|Resource | 連結|
|---|---|
|建立、變更或刪除網路介面 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface)|
|網路介面 IP 位址 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)|
|

### <a name="subnet"></a>Subnet

子網是在 Azure 內進行網路分割和隔離的重要元件。 子網可以用類似的方式來提供系統之間的分隔。 Nsg 可以套用至子網, 以限制輸入通訊流程僅限於系統功能所需的流量。 子網可以作為防火牆規則和存取控制清單的來源和目的地位址, 而且可以針對服務端點進行設定, 以提供 PaaS 功能的連線能力。

|Resource | 連結|
|---|---|
|加入、變更或刪除虛擬網路子網路 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)|
|

### <a name="virtual-network-vnet"></a>虛擬網路 (VNet)

Vnet 是 Azure 中網路的其中一個基本建立區塊。 虛擬網路會定義要在各種系統上使用的 IP 位址空間和路由界限。 虛擬網路會分割成子網, 而虛擬網路內的所有子網都有直接的網路路由。 藉由使用虛擬網路閘道 (ExpressRoute 或 VPN), 虛擬網路內的系統可供內部部署和外部環境存取。 瞭解虛擬網路和相關聯的設定參數和路由, 對於瞭解和控制輸入網路流量十分重要。

|Resource | 連結|
|---|---|
|虛擬網路總覽 | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)|
|規劃虛擬網路操作指南 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|
建立虛擬網路快速入門 | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)|
|

### <a name="vnet-peering"></a>VNet 對等互連

VNet 對等互連是用來提供兩個虛擬網路之間的直接通訊路徑。 一旦建立對等互連之後, 一虛擬網路中的主機就會直接在另一個虛擬網路中的主機具有高速路由路徑。 Nsg 仍適用于流量, 因為一般和先進的設定參數可用來定義是否允許透過虛擬網路閘道或從其他外部系統進行通訊。

|Resource | 連結|
|---|---|
|虛擬網路對等互連總覽 |  [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)|
|建立、變更或刪除虛擬網路對等互連 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="public-ip-on-vnet"></a>VNET 上的公用 IP

公用 IP 位址是用來提供在虛擬網路中部署之服務的輸入通訊路徑。 美國聯邦的實體應謹慎規劃公用 IP 位址的配置, 並只將其指派給有正版需求的資源。 作為一般的設計做法, 公用 IP 位址應該配置給具有內建安全性功能 (例如應用程式閘道或網路虛擬裝置) 的資源, 以提供安全且受控制的公用進入點給虛擬網路。

|Resource | 連結|
|---|---|
|公用 IP 位址總覽 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)|
|建立、變更或刪除公用 IP 位址 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)|
|

### <a name="expressroute-gateway"></a>ExpressRoute 閘道

ExpressRoute 閘道會從內部部署環境提供進入點, 並應加以部署以符合安全性、可用性、財務和效能需求。 ExpressRoute 閘道提供定義的網路頻寬, 並在部署後產生使用成本。 虛擬網路只能有一個 ExpressRoute 閘道, 但這可以連接到多個 ExpressRoute 線路, 並可透過 VNet 對等互連供多個虛擬網路運用, 讓多個虛擬網路能夠共用頻寬和連線能力。 請小心使用 ExpressRoute 閘道設定內部部署環境與虛擬網路之間的路由, 以確保使用已知且受控制的網路進入點的端對端連線能力。 使用 ExpressRoute 閘道的美國聯邦實體也必須部署網路虛擬裝置, 以建立與內部部署環境的 VPN 連線, 以符合 ACSC 取用者指導方針。

|Resource | 連結|
|---|---|
|ExpressRoute 閘道總覽 | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)|
|為 ExpressRoute 設定虛擬網路閘道 | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)|
|

### <a name="vpn-gateway"></a>VPN 閘道

Azure VPN 閘道提供來自外部網路的輸入網路點, 以確保站對站或點對站連線的安全。 VPN 閘道提供定義的網路頻寬, 並在部署後產生使用成本。 使用 VPN 閘道的美國聯邦實體應確保其已根據 ACSC 取用者指引進行設定。 虛擬網路只能有一個 VPN 閘道, 但這可以使用多個通道進行設定, 並可透過 VNet 對等互連供多個虛擬網路利用, 讓多個虛擬網路能夠共用頻寬和連線能力。 VPN 閘道可以透過網際網路或透過 Microsoft 對等互連的 ExpressRoute 來建立。

|Resource | 連結|
|---|---|
|VPN 閘道總覽 | [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)|
|規劃與設計 VPN 閘道 | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
|適用于澳大利亞政府機關的 VPN 閘道設定|[澳大利亞政府機關所需的 IPSEC 設定](vpn-gateway.md)|
|

### <a name="paas-vnet-integration"></a>PaaS VNet 整合

利用 PaaS 可提供增強的功能和可用性, 並減少管理額外負荷, 但必須適當地加以保護。 若要增加控制權、強制執行網路分割, 或為應用程式和服務提供安全的輸入進入點, 可以將許多 PaaS 功能與虛擬網路整合。

為了提供安全的進入點, 您可以使用外部、公開的介面和內部的私用介面來設定 PaaS 功能 (例如應用程式閘道), 以與應用程式服務進行通訊。 這可避免需要設定具有公用 IP 位址的應用程式伺服器, 並將其公開至外部網路。

為了使用 PaaS 做為系統或應用程式架構的整合元件, Microsoft 提供了多項機制, 可將 PaaS 部署到虛擬網路。 部署方法會限制來自外部網路 (例如網際網路) 的輸入存取, 同時提供與內部系統和應用程式的連線能力與整合。 範例包括 App Service 環境、SQL 受控實例等等。

|Resource | 連結|
|---|---|
|Azure 服務的虛擬網路整合 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)|
|整合您的應用程式與 Azure 虛擬網路操作指南 | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)|
|

## <a name="paas-ingress"></a>PaaS 輸入

PaaS 功能可提供增加功能和簡化管理的機會, 但在定址網路分割和隔離的需求方面帶來了複雜性。 PaaS 功能通常是使用公用 IP 位址進行設定, 而且可以從網際網路存取。  使用 PaaS 功能建立系統時, 應特別注意系統內的元件與建立來僅允許此通訊的網路安全性規則之間的所有必要通訊流程。 針對安全性的深度防禦方法, 應該使用加密、驗證和適當的存取控制和許可權來設定 PaaS 功能。  

### <a name="hostname"></a>主機名稱

PaaS 功能是由主機名稱唯一識別, 可讓相同服務的多個實例裝載在相同的公用 IP 位址上。 建立資源並存在於 Microsoft 擁有的 DNS 網域中時, 會指定唯一的主機名稱。 授權服務的特定主機名稱可用於具有應用層級篩選功能的安全性工具內。 某些服務也可以視需要設定自訂網域。

|Resource | 連結|
|---|---|
|Azure 服務所使用的許多公用命名空間可透過 Powershell 取得, 方法是執行 Add-azurermenvironment 命令 | [https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment](https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment)|
|設定 Azure 雲端服務的自訂網域名稱 | 應用程式服務和其他人可以擁有自訂網域[https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal](https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal)|
|

### <a name="public-ip-for-paas"></a>PaaS 的公用 IP

PaaS 功能的公用 IP 位址是根據裝載或部署服務的區域來配置。 若要建立適當的網路安全性規則和路由拓撲以進行網路分割, 並隔離涵蓋 Azure 虛擬網路、PaaS 和 ExpressRoute 和網際網路連線能力, 請務必瞭解公用 IP 位址配置和區域必填。 Azure 會從配置給每個 Azure 區域的集區配置 IP 位址。 Microsoft 會將每個區域中使用的位址提供給下載, 這會以固定且受控制的方式進行更新。 每個區域中可用的服務也經常隨著新服務的發行或服務部署更為廣泛而變更。 美國聯邦的實體應該定期檢查這些資料, 並且可以視需要運用自動化來維護系統。 您可以聯繫 Microsoft 支援服務, 以取得每個區域中裝載之某些服務的特定 IP 位址。

|Resource | 連結|
|---|---|
|Microsoft Azure 資料中心 IP 範圍 | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653)|
|每個區域的 Azure 服務 | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, 澳大利亞中部, 澳大利亞中部-2, 澳大利亞-東部, 澳大利亞-東南部 & 產品 = 全部](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all)|
|

### <a name="service-endpoints"></a>服務端點

虛擬網路服務端點會針對虛擬網路內的子網提供高速、私人輸入網路連線, 以取用特定的 PaaS 功能。 如需完整的網路分割和 PaaS 功能的隔離, 必須將 PaaS 功能設定為只接受必要虛擬網路的連接。 並非所有 PaaS 功能都支援包含服務端點和傳統 IP 位址型規則的防火牆規則組合, 因此請小心瞭解應用程式功能和管理所需的通訊流程如此一來, 這些安全性控制的執行並不會影響服務的可用性。

|Resource | 連結|
|---|---|
|服務端點概觀 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)
|教學課程 |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

## <a name="security"></a>安全性

執行 IaaS 和 PaaS 功能的網路分割和隔離控制是透過保護功能本身, 以及從要與之通訊的系統中執行受控制的通訊路徑來達成產.

在 Azure 中設計和建立解決方案是建立邏輯架構的程式, 以瞭解、控制和監視整個 Azure 目前狀態中的網路資源。 此邏輯架構是在 Azure 平臺內定義的軟體, 並取代在傳統網路環境中實行的實體網路拓撲。

所建立的邏輯架構必須提供可用性所需的功能, 但也必須提供安全性和完整性所需的可見度和控制。

達到此結果是以執行必要的網路分割和隔離工具為基礎, 也是為了保護和強制執行網路拓撲和這些工具的實施。

本指南中提供的資訊可用來協助識別需要允許的輸入流量來源, 以及可進一步控制或限制流量的方式。

### <a name="network-security-groups-nsgs"></a>網路安全性群組 (NSG)

Nsg 是用來指定子網或特定網路介面允許的輸入和輸出流量。 設定 Nsg 時, 「自由通訊」實體應該使用允許清單方法, 其中的規則會設定為允許所需的流量, 並將預設規則設定為拒絕不符合特定「允許」語句的所有流量。 規劃和設定 Nsg 時必須特別小心, 以確保正確地捕捉所有必要的輸入和輸出流量。 這包括識別和瞭解在 Azure 虛擬網路和內部部署環境中使用的所有私人 IP 位址範圍, 以及特定的 Microsoft 服務, 例如 Azure Load Balancer 和 PaaS 管理需求。 與網路安全性群組的設計和部署相關的人員也應該瞭解如何使用服務標籤和應用程式安全性群組來建立更細緻、服務和應用程式特定的安全性規則。

|Resource | 連結|
|---|---|
|網路安全性總覽 | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)
|建立、變更或刪除網路安全性群組 | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

## <a name="paas-firewall"></a>PaaS 防火牆

PaaS 防火牆是一種可套用至特定 PaaS 服務的網路存取控制功能。 它允許設定來自特定虛擬網路的 IP 位址篩選或篩選, 以限制特定 PaaS 實例的輸入流量。 對於包含防火牆的 PaaS 功能, 應該設定網路存取控制原則, 只允許根據應用程式需求所需的輸入流量。  

|Resource | 連結|
|---|---|
|Azure SQL Database 和 SQL 資料倉儲 IP 防火牆規則 | [https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)|
|存放裝置網路安全性 | [https://docs.microsoft.com/azure/storage/common/storage-network-security](https://docs.microsoft.com/azure/storage/common/storage-network-security)|
|

## <a name="paas-authentication-and-access-control"></a>PaaS 驗證和存取控制

根據 PaaS 功能及其用途, 使用網路控制來限制存取可能不可行或不可行。 作為 PaaS 的多層式安全性模型的一部分, Azure 提供各種驗證和存取控制機制來限制對服務的存取, 即使允許網路流量也是如此。 PaaS 功能的一般驗證機制包括 Azure Active Directory、應用層級驗證, 以及共用金鑰或存取簽章。 一旦安全地識別使用者, 就可以使用角色來控制使用者可以執行的動作。 這些工具可以使用為替代方案或免費的量值, 以限制對服務的存取。

|Resource | 連結|
|---|---|
|控制及授與 SQL Database 和 SQL 資料倉儲的資料庫存取權 | [https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)|
|Azure 儲存體服務的授權 | [https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services)|
|

## <a name="azure-policy"></a>Azure 原則

Azure 原則是強制執行及維護 Azure 環境邏輯架構完整性的關鍵元件。 透過 Azure 服務提供各種不同的服務和輸入網路流量路徑, 聯邦的實體務必留意其環境內的資源, 以及可用的網路進入點。 為了確保不會在 Azure 環境中建立未經授權網路輸入點, 「自由」實體應該利用 Azure 原則來控制可以部署的資源類型, 以及這些資源的設定。 實際範例包括將資源限制為僅限已授權和已核准的使用, 在儲存體上強制執行 HTTPS 加密, 並要求將 Nsg 新增至子網。

|Resource | 連結|
|---|---|
|Azure 原則總覽 | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|允許的資源類型範例原則 | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)
|確認 HTTPS 儲存體帳戶範例原則|[https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account](https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account)_
|強制子網的 NSG 範例原則| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="next-steps"></a>後續步驟

請參閱關於[閘道輸出流量管理和控制](gateway-egress-traffic.md)的文章, 以瞭解如何使用 azure 中的閘道元件, 管理流量從 azure 環境流向其他網路的詳細資料。
