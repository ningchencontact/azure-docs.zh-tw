---
title: 控制 Azure 澳大利亞的輸出流量
description: 控制 Azure 中的輸出流量以符合澳大利亞政府對安全網際網路閘道需求的重要元素
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: grgale
ms.openlocfilehash: 7922846d161b7a0cbda32101b6bbb40a1741b323
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602079"
---
# <a name="controlling-egress-traffic-in-azure-australia"></a>控制 Azure 澳大利亞的輸出流量

保護 ICT 系統的基本元件是控制網路流量。 將通訊限制為只有系統運作所需的流量, 可減少危害的可能性。 可見度和控制您的應用程式和服務所通訊的外部系統, 有助於偵測遭入侵的系統, 以及嘗試或成功的資料外泄。 本文提供在 Azure 內輸出 (傳出) 網路流量如何運作的相關資訊, 並提供建議, 讓您針對與澳大利亞網路安全中心 (ACSC) 配合的連線到網際網路的系統, 來執行網路安全性控制ACSC 資訊安全手冊 (ISM) 的消費者指引和意圖。

## <a name="requirements"></a>需求

美國聯邦系統的整體安全性需求是在 ISM 中定義。 為了協助實現網路安全性的美國聯邦實體, ACSC 已發行_ACSC 保護:執行網路分割和隔離_, 並協助保護雲端環境中的系統, ACSC 已為租使用者發佈_雲端運算安全性_。

ACSC 檔概述了用來執行網路安全性和控制流量的內容, 並提供網路設計和設定的實用建議。

在 Azure 中控制輸出流量的下列主要需求已在 ACSC 檔中找到。

描述|Source
--------------- |------------------
**執行網路分割和隔離**, 例如使用多層式架構, 使用主機型防火牆和網路存取控制, 將輸入和輸出網路連線限制為只有所需的埠和通訊協定。| [租使用者的雲端運算](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
執行租使用者 (包括租使用者的遠端使用者) 與雲端服務之間**適當的高頻寬、低延遲、可靠的網路**連線, 以符合租使用者的可用性需求  | [ACSC 保護:執行網路分割和隔離](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**只在網路層套用技術**。 每個主機和網路都應該在可能的情況下, 以可完全管理的最低層級分割和隔離。 在大部分情況下, 這適用于從資料連結層到應用層的最新狀態;不過, 在敏感性環境中, 實體隔離可能是適當的。 主機型和網路範圍的量值應該以互補的方式部署, 並集中監視。 只是執行防火牆或安全性設備, 因為唯一的安全性措施並不夠。 |[ACSC 保護:執行網路分割和隔離](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**使用最低許可權的原則, 並需要‐‐知道**。 如果主機、服務或網路不需要與另一個主機、服務或網路通訊, 則不應允許此情況。 如果主機、服務或網路只需要與特定埠或通訊協定上的另一個主機、服務或網路交談, 則應該限制為只有那些埠和通訊協定。 在網路上採用這些原則將可補充使用者權限的 minimisation, 並大幅增加環境的整體安全性狀態。 |[ACSC 保護:執行網路分割和隔離](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**根據商務作業的敏感度或重要性, 區分主機和網路**。 這可能包括使用不同的硬體或平臺, 視不同的安全性分類、安全性網域或特定主機或網路的可用性/完整性需求而定。 特別的是, 個別的管理網路, 並考慮實體隔離頻外管理網路的敏感性環境。 |[ACSC 保護:執行網路分割和隔離](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
針對**所有其他實體, 識別、驗證及授權所有實體的存取權**。 所有的使用者、主機和服務都應該要能夠存取所有其他的使用者、主機和服務, 但僅限於執行其指定的職責或功能所需的所有使用者。 所有會略過或降級識別、驗證和 authorisation 服務強度的舊版或本機服務應該盡可能停用, 並受到嚴密的監控。 |[ACSC 保護:執行網路分割和隔離](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**執行允許清單的網路流量, 而不是拒絕清單**。 只允許存取已知良好的網路流量 (已識別、已驗證和已授權的流量), 而不是拒絕存取已知的不正確網路流量 (例如封鎖特定的位址或服務)。 允許清單會產生更上層的安全性原則來拒絕清單, 並大幅改善您的容量, 以偵測及評估可能的網路入侵。 |[ACSC 保護:執行網路分割和隔離](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**定義允許的網站清單, 並封鎖所有未列出的網站,** 可有效地移除敵人所使用的其中一種最常見的資料傳遞和外泄技術。 如果使用者有合法的需求可以存取許多網站, 或是快速變更的網站清單,您應該考慮這類實現的成本。 即使是相對寬鬆的允許清單, 也能提供比依賴拒絕清單的更佳安全性, 或完全沒有任何限制, 同時仍可降低執行成本。 寬鬆允許清單的範例可能是允許整個澳大利亞子域 (也就是 ' * 澳大利亞 '), 或允許來自 Alexa 網站排名的前1000網站 (篩選動態網域名稱系統 (DDNS) 網域和其他不適當的網域)。| [澳大利亞政府資訊安全手冊 (ISM)](https://www.cyber.gov.au/ism)
|

本文提供如何控制離開 Azure 環境之網路流量的相關資訊和建議。 其中涵蓋使用「基礎結構即服務」 (IaaS) 和「平臺即服務」 (PaaS) 部署于 Azure 中的系統。

[閘道輸入流量](gateway-ingress-traffic.md)一文會處理進入您 Azure 環境的網路流量, 並在本文中提供完整的網路控制涵蓋範圍。

## <a name="architecture"></a>架構

若要適當地控制輸出流量, 當您設計和執行網路安全性時, 您必須先瞭解 Azure 中的輸出網路流量如何在 IaaS 和 PaaS 之間運作。 本節概述如何處理裝載于 Azure 中的資源所產生的輸出流量, 以及可用於限制及控制該流量的安全性控制項。

### <a name="architecture-components"></a>架構元件

此處顯示的架構圖說明當結束部署到虛擬網路中子網的系統時, 網路流量可能會採取的路徑。 虛擬網路中的流量會在子網層級受到管理和控管, 並將路由和安全性規則套用至中包含的資源。 與輸出流量相關的元件會分成系統、有效路由、下一個躍點類型、安全性控制和 PaaS 輸出。

![架構](media/egress-traffic.png)

### <a name="systems"></a>系統工程

系統是 Azure 資源和相關元件, 會在屬於虛擬網路一部分的 IP 子網內產生輸出流量。

| 元件 | 描述 |
| --- | ---|
|虛擬網路 (VNet) | VNet 是 Azure 中的基礎資源, 可提供部署資源和啟用通訊的平臺和界限。 VNet 存在於 Azure 區域內, 並定義 VNet 整合式資源的 IP 位址空間和路由界限, 例如虛擬機器。|
|Subnet | 子網是在 VNet 內建立的 IP 位址範圍。 可以在 VNet 內建立多個子網以進行網路分割。|
|網路介面| 網路介面是存在於 Azure 中的資源。 它會附加至虛擬機器, 並從與其相關聯的子網中, 指派私人、非網際網路可路由傳送的 IP 位址。 此 IP 位址是透過 Azure Resource Manager 動態或靜態方式指派。|
|公用 IP| 「公用 IP」是一種資源, 可從指定的區域保留其中一個 Microsoft 擁有的公用、網際網路可路由 IP 位址, 以便在虛擬網路中使用。 它可以與特定網路介面或 PaaS 資源相關聯, 讓資源能夠與網際網路、ExpressRoute 和其他 PaaS 系統進行通訊。|
|

### <a name="routes"></a>路由

輸出流量所採用的路徑取決於該資源的有效路由, 這是由學會自所有可能來源的路由組合和 Azure 路由邏輯應用程式所決定的路由結果組。

| 元件 | 描述 |
|--- | ---|
|系統路由| Azure 會自動建立系統路由，並將路由指派給虛擬網路中的每個子網路。 系統路由無法建立或移除, 但有些則可以使用自訂路由來覆寫。 Azure 會為每個子網建立預設的系統路由, 並在使用特定 Azure 功能時, 將其他選擇性預設路由新增至特定子網或每個子網。|
|服務端點| 服務端點提供從子網到特定 PaaS 功能的直接、私人輸出連線。 服務端點僅適用于 PaaS 功能的子集, 可針對存取 PaaS 的 VNet 中的資源提供更高的效能和安全性。|
|路由表| 路由表是 Azure 中的資源, 可建立來指定使用者定義的路由 (Udr), 以透過邊界閘道協定來補充或覆寫系統路由或路由學會。 每個 UDR 會指定網路、網路遮罩和下一個躍點。 路由表可以與子網建立關聯, 而且相同的路由表可以與多個子網建立關聯, 但子網只能有零個或一個路由表。|
|邊界閘道協定 (BGP)| BGP 是一種自發的系統路由通訊協定。 「自發系統」是一種網路或網路群組, 位於一般系統管理和一般路由原則之下。 BGP 是用來在自主系統之間交換路由資訊。 透過虛擬網路閘道, 可將 BGP 整合到虛擬網路。|
|

### <a name="next-hop-types-defined"></a>定義的下一個躍點類型

Azure 中的每個路由都包含網路範圍和相關聯的子網路遮罩, 以及下一個躍點, 以決定流量的處理方式。

下一個躍點類型 | 描述
---- | ----
**虛擬網路** | 在虛擬網路位址空間內的位址範圍之間路由傳送流量。 Azure 建立路由所用的位址首碼，會與每個虛擬網路位址空間中定義的位址範圍相對應。 如果虛擬網路位址空間已定義多個位址範圍, Azure 就會為每個位址範圍建立個別的路由。 Azure 會使用針對每個位址範圍所建立的路由, 自動在 VNet 內的子網之間路由傳送流量。
**VNet 對等互連** | 在兩個虛擬網路之間建立虛擬網路對等互連時, 會將每個虛擬網路的每個位址範圍的路由新增至它所對等互連的虛擬網路。 流量會以虛擬網路中的子網相同的方式, 在對等互連的虛擬網路之間路由傳送。
**虛擬網路閘道** | 將虛擬網路閘道新增至虛擬網路時, 會新增一或多個虛擬網路閘道列為下一個躍點類型的路由。 包括在局域網路閘道資源內設定的路由, 以及透過 BGP 學會的任何路由。
**虛擬裝置** | 虛擬裝置通常會執行網路應用程式 (例如防火牆)。 虛擬裝置允許進行額外的流量處理, 例如篩選、檢查或位址轉譯。 具有虛擬裝置躍點類型的每個路由也必須指定下一個躍點 IP 位址。
**VirtualNetworkServiceEndpoint** | 當服務端點啟用時, 會將特定服務的公用 IP 位址新增為具有下一個 VirtualNetworkServiceEndpoint 躍點的子網路由。 系統會針對虛擬網路內個別子網上的個別服務啟用服務端點。 Azure 服務的公用 IP 位址會定期變更。 當位址變更時，Azure 會自動管理路由表中的位址。
**Internet** | 具有下一個網際網路躍點的流量將會結束虛擬網路, 並自動從相關聯 Azure 區域中可用的動態集區轉譯為公用 IP 位址, 或使用針對該資源設定的公用 IP 位址。 如果目的地位址適用于其中一個 Azure 服務, 則流量會透過 Azure 的骨幹網路直接路由傳送至服務, 而不會將流量路由傳送到網際網路。 不論虛擬網路存在哪一個 Azure 區域，或 Azure 服務執行個體部署在哪一個 Azure 區域，Azure 服務之間的流量都不會周遊網際網路。
**無** | 下一個躍點為 none 的流量會遭到捨棄。 Azure 會針對保留的位址首碼建立系統預設路由, 而不是下一個躍點類型。 下一個躍點為 none 的路由也可以使用路由表來新增, 以防止流量路由傳送到特定網路。
|

### <a name="security-controls"></a>安全性控制

控制 | 描述
----- | -----
**網路安全性群組 (Nsg)** | Nsg 控制在 Azure 中進出虛擬網路資源的流量。 Nsg 會針對允許或拒絕的流量套用規則, 包括 Azure 中的流量, 以及 Azure 和外部網路 (例如內部部署或網際網路) 之間的流量。 Nsg 會套用至虛擬網路內的子網或個別的網路介面。
**Azure 防火牆** | Azure 防火牆是受控的雲端式網路安全性服務, 可保護 Azure 虛擬網路資源。 它是完全具狀態防火牆即服務，具有內建的高可用性和不受限制的雲端延展性。 Azure 防火牆可以根據 IP 位址、通訊協定和埠來設定傳統網路篩選規則, 但也支援以完整功能變數名稱 (FQDN)、服務標籤和內建威脅情報為基礎的篩選。
**網路虛擬裝置 (NVA)** | 網路虛擬裝置是虛擬機器媒體, 可為 Azure 提供網路功能、安全性及其他功能。 Nva 以虛擬網路和部署中的 Vm 形式支援網路功能和服務。 Nva 可以用來解決特定需求、與管理和操作工具整合, 或提供與現有產品的一致性。 Azure 支援各式各樣不同的第三方網路虛擬設備，包括 Web 應用程式防火牆 (WAF)、防火牆、閘道/路由器、應用程式傳遞控制器 (ADC) 及 WAN 最佳化程式。
**服務端點原則 (預覽)** | 虛擬網路服務端點原則可讓您篩選 Azure 服務的虛擬網路流量, 僅允許特定 Azure 服務資源, 而不是透過服務端點。 端點原則可針對流向 Azure 服務的虛擬網路流量提供更細微的存取控制。
**Azure 原則** | Azure 原則是 Azure 中的一項服務, 可用於建立、指派和管理原則。 這些原則會使用規則來控制可以部署的資源類型, 以及這些資源的設定。 如果原則不符合需求, 或可用來進行監視以報告合規性狀態, 則可以使用原則來強制執行合規性。
|

### <a name="paas-egress"></a>PaaS 輸出

大部分的 PaaS 資源都不會產生輸出流量, 但會回應輸入要求 (例如應用程式閘道、儲存體、SQL Database 等), 或是從其他資源 (例如服務匯流排和 Azure 轉送) 轉送資料。 PaaS 資源 (例如應用程式服務到儲存體或 SQL 資料庫) 之間的網路通訊會由 Azure 控制和包含, 並透過身分識別和其他資源設定控制來保護, 而不是網路分割或隔離。

部署到虛擬網路的 PaaS 資源會接收專用的 IP 位址, 並受限於任何路由控制和 Nsg, 其方式與虛擬網路中的其他資源相同。 不存在於虛擬網路內的 PaaS 資源將會利用在資源的所有實例之間共用的 IP 位址集區, 這是透過 Microsoft 檔發佈, 或可透過 Azure Resource Manager 來判斷。

## <a name="general-guidance"></a>一般指導

若要在 Azure 內設計和建立安全的解決方案, 請務必瞭解並控制網路流量, 以便只進行已識別和授權的通訊。 本指南的目的和後續章節中的特定元件指引, 是描述可使用的工具和服務, 以套用[ACSC 保護中所述的原則:跨 Azure 工作負載執行](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)網路分割和隔離。 這包括詳細說明如何建立虛擬架構來保護資源, 而不可能套用內部部署環境中可能的相同傳統實體和網路控制。

### <a name="guidance"></a>指引

* 限制虛擬網路的輸出點數目
* 針對不需要對網際網路進行直接輸出通訊的所有子網, 覆寫系統預設路由
* 設計和執行完整的網路架構, 以識別並控制對 Azure 資源的所有輸入和輸出點
* 請考慮使用虛擬網路的中樞和輪輻網路設計, 如 Microsoft 虛擬資料中心 (VDC) 檔中所述
* 利用具有內建安全性功能的產品, 以輸出連線到網際網路 (例如, Azure 防火牆、網路虛擬裝置或 Web proxy)
* 使用身分識別控制項 (例如角色型存取、條件式存取和多重要素驗證 (MFA)) 來限制網路設定許可權
* 執行鎖定以防止修改或刪除網路設定的重要元素
* 在 VNet 整合式設定中部署 PaaS, 以增加隔離與控制
* 針對與內部部署網路的連線能力執行 ExpressRoute
* 執行 Vpn 以與外部網路整合
* 利用 Azure 原則, 將區域和資源限制為只有系統功能才需要
* 利用 Azure 原則, 以強制執行資源的基準安全性設定
* 利用網路監看員和 Azure 監視器在 Azure 內的網路流量進行記錄、審核和可見度

### <a name="resources"></a>資源

項目 | 連結
-----------| ---------
_澳大利亞法規和原則合規性檔, 包括取用者指引_ | [https://aka.ms/au-irap](https://aka.ms/au-irap)
_Azure 虛擬資料中心_ | [https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)
_ACSC 網路分割_ | [https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
_租使用者的 ACSC 雲端安全性_ | [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
_ACSC 資訊安全性手冊_ | [https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)
|

## <a name="component-guidance"></a>元件指引

本節針對 Azure 中部署之系統的輸出流量, 提供有關個別元件的進一步指引。 每一節都會說明特定元件的目的, 並提供檔和設定指南的連結, 可用來協助設計和建立活動。

### <a name="systems-security"></a>系統安全性

Azure 內資源的所有通訊都會通過 Microsoft 維護的網路基礎結構, 以提供連線能力和安全性功能。 Microsoft 會自動設定一系列的保護, 以保護 Azure 平臺和網路基礎結構, 並提供其他功能作為 Azure 中的服務, 以控制網路流量並建立網路分割, 以及職責.

### <a name="virtual-network-vnet"></a>虛擬網路 (VNet)

虛擬網路是 Azure 中網路的其中一個基本組建區塊。 虛擬網路會定義要在各種系統上使用的 IP 位址空間和路由界限。 虛擬網路會分割成子網, 而虛擬網路內的所有子網都有直接的網路路由。 藉由使用虛擬網路閘道 (ExpressRoute 或 VPN), 虛擬網路內的系統可以與內部部署和外部環境整合, 而且透過 Azure 提供的網路位址轉譯 (NAT) 和公用 IP 位址配置, 系統可以連接到網際網路或其他 Azure 區域和服務。 瞭解虛擬網路和相關聯的設定參數和路由, 對於瞭解和控制輸出網路流量十分重要。

當虛擬網路形成 Azure 中的基底位址空間和路由界限時, 它可以做為網路分割和隔離的主要建立區塊。

| Resource | 連結 |
| --- | --- |
| *虛擬網路總覽* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) |
| *規劃虛擬網路操作指南*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) |
| *建立虛擬網路快速入門* | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
|

### <a name="subnet"></a>Subnet

子網是在 Azure 內進行網路分割和隔離的重要元件。 子網可以用來提供系統之間的分隔。 子網是虛擬網路內的資源, 其中會套用 Nsg、路由表和服務端點。 子網可以作為防火牆規則和存取控制清單的來源和目的地位址。

應該規劃虛擬網路中的子網, 以符合工作負載和系統的需求。 與設計或執行子網相關的個人, 應參閱網路分割的 ACSC 指導方針, 以決定如何在子網內將系統群組在一起。

|Resource|連結|
|---|---|
|*新增、變更或刪除虛擬網路子網* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
|

### <a name="network-interface"></a>網路介面

網路介面是來自虛擬機器的所有傳出流量的來源。 網路介面可讓您設定 IP 位址, 並可用來套用 Nsg 或透過 NVA 路由傳送流量。 虛擬機器的網路介面應已規劃並適當地設定, 以配合整體網路分割和隔離目標。

|Resource|連結|
|---|---|
|*建立、變更或刪除網路介面* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface) |
|*網路介面 IP 位址*               | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
|

### <a name="vnet-integrated-paas"></a>VNet 整合式 PaaS

PaaS 可提供增強的功能和可用性並降低管理額外負荷, 但必須適當地加以保護。 若要增加控制權、強制執行網路分割, 或為應用程式和服務提供安全的輸出點, 可以將許多 PaaS 功能與虛擬網路整合。

Microsoft 使用 PaaS 作為系統或應用程式架構的整合式元件, 提供將 PaaS 部署到虛擬網路的多個機制。 部署方法可協助限制存取, 同時提供與內部系統和應用程式的連線能力和整合。 範例包括 App Service 環境、SQL 受控實例等等。

將 PaaS 部署到已實作為路由和 NSG 控制的虛擬網路時, 請務必瞭解資源的特定通訊需求, 包括來自 Microsoft 服務的管理存取權, 以及當您回復來自這些服務的連入要求時, 會需要通訊流量。

| Resource  | 連結  |
| --- | --- |
| *Azure 服務的虛擬網路整合* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services) |
| *整合您的應用程式與 Azure 虛擬網路操作指南* | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
|

### <a name="public-ip"></a>公用 IP

在虛擬網路外部通訊時, 會使用公用 IP 位址。 這包括 PaaS 資源, 以及下一個網際網路躍點的任何路由。 美國聯邦的實體應謹慎規劃公用 IP 位址的配置, 並只將其指派給有正版需求的資源。 作為一般的設計做法, 公用 IP 位址應配置給虛擬網路 (例如 Azure 防火牆、VPN 閘道或網路虛擬裝置) 的受控輸出點。

|Resource|連結|
|---|---|
|*公用 IP 位址總覽*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) |
|*建立、變更或刪除公用 IP 位址* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
|

## <a name="effective-routes"></a>有效的路由

有效路由是由系統路由、服務端點、路由表和 BGP 與 Azure 路由邏輯的應用所決定的路由結果組。 當輸出流量從子網路送出時，Azure 會根據目的地 IP 位址選取路由 (使用最長的首碼比對演算法)。 如果多個路由包含相同的位址首碼，則 Azure 會根據下列優先順序選取路由類型：

1. 使用者定義路由
2. BGP 路由
3. 系統路由

請務必注意, 與虛擬網路、虛擬網路對等互連或虛擬網路服務端點相關之流量的系統路由是慣用的路由, 即使 BGP 路由較特定也是一樣。

在 Azure 中設計或執行路由拓撲的人員, 應該瞭解 Azure 如何路由傳送流量, 並開發架構, 以平衡系統的必要功能, 以及所需的安全性和可見度。 請小心規劃環境, 以避免過度介入和路由行為的例外狀況, 因為這會增加複雜度, 而且可能會讓疑難排解和錯誤的發現更棘手和耗時。

| Resource | 連結  |
| --- | --- |
| *查看有效路由* | <https://docs.microsoft.com/azure/virtual-network/manage-route-table#view-effective-routes> |
|

### <a name="system-routes"></a>系統路由

對於[系統路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#system-routes), 與設計或虛擬網路的執行相關的個人應該瞭解預設系統路由, 以及可用來補充或覆寫這些路由的選項。

### <a name="service-endpoints"></a>服務端點

在子網上啟用[服務端點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)會提供相關聯 PaaS 資源的直接通訊路徑。 這可以藉由限制只有該服務的可用通訊路徑, 來提供更高的效能和安全性。 使用服務端點時, 會引進潛在的資料外泄路徑, 因為預設設定允許存取 PaaS 服務的所有實例, 而不是應用程式或系統所需的特定實例。

「自由」實體應該評估與提供 PaaS 資源直接存取相關的風險, 包括誤用路徑的可能性和結果。

若要減少與服務端點相關聯的潛在風險, 請盡可能執行服務端點原則, 或考慮在 Azure 防火牆或 NVA 子網上啟用服務端點, 並透過它來路由傳送來自特定子網的流量, 其中還有額外的可以套用篩選、監視或檢查。

|Resource|連結|
|---|---|
|*教學課程：透過使用 Azure 入口網站的虛擬網路服務端點, 限制對 PaaS 資源的網路存取* |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

### <a name="route-tables"></a>路由表

路由表提供系統管理員設定的機制, 以控制 Azure 內的網路流量。 您可以使用路由表, 將流量轉送至 Azure 防火牆或 NVA、直接連線到外部資源, 或覆寫 Azure 系統路由。 路由表也可以用來防止透過虛擬網路閘道學會的網路, 藉由停用虛擬網路閘道路由傳播, 使其無法供子網中的資源使用。

|Resource|連結|
|---|---|
|*路由概念-自訂路由* |[https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)|
|*教學課程：路由網路流量* |[https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)|
|

### <a name="border-gateway-protocol-bgp"></a>邊界閘道協定 (BGP)

BGP 可以由虛擬網路閘道使用, 以動態方式與內部部署或其他外部網路交換路由資訊。 當虛擬網路是透過 expressroute 私人對等互連來設定, 而且在 Azure VPN 閘道上啟用時, BGP 會套用至虛擬網路。

在 Azure 中設計或執行虛擬網路和虛擬網路閘道所牽涉到的個人, 應該需要一些時間來瞭解 Azure 中適用于 BGP 的行為和設定選項。

|Resource|連結|
|---|---|
|*路由概念:BGP* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools)|
|*ExpressRoute 路由需求* | [https://docs.microsoft.com/azure/expressroute/expressroute-routing](https://docs.microsoft.com/azure/expressroute/expressroute-routing)|
|*關於搭配 Azure VPN 閘道的 BGP* |[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)|
|*教學課程：透過 ExpressRoute Microsoft 對等互連設定站對站 VPN* |[https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering](https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering)|
|

## <a name="next-hop-types"></a>下一個躍點類型

### <a name="virtual-network"></a>虛擬網路

具有虛擬網路下一個躍點的路由會自動新增為系統路由, 但也可以新增至使用者定義的路由, 以將流量導向回到已覆寫系統路由之實例中的虛擬網路。

### <a name="vnet-peering"></a>VNet 對等

VNet 對等互連可在兩個不同的虛擬網路之間進行通訊。 必須在每個虛擬網路上啟用設定 VNet 對等互連, 但虛擬網路不需要位於相同的區域、訂用帳戶, 或與相同的 Azure Active Directory (Azure AD) 租使用者相關聯。

設定 VNet 對等互連時, 與設計或執行 VNet 對等互連相關的人員, 請務必瞭解四個相關聯的設定參數, 以及它們如何套用到對等的每一方:

1. **允許虛擬網路存取：** 選取 [**已啟用**] (預設) 以啟用兩個虛擬網路之間的通訊。 讓虛擬網路能夠彼此通訊，會讓連線到任一虛擬網路的資源能夠以相同的頻寬和延遲彼此通訊，彷彿這些資源是連線到相同的虛擬網路。
2. **允許轉寄的流量：** 核取此方塊可允許網路流量 (不是來自虛擬網路的流量)*轉送*流量, 以透過對等互連流向此虛擬網路。 此設定是用來執行中樞和輪輻網路拓撲的基礎。
3. **允許閘道傳輸：** 核取此方塊可讓對等互連虛擬網路利用連結到此虛擬網路的虛擬網路閘道。 允許使用虛擬網路閘道資源在虛擬網路上啟用*閘道傳輸*, 但只有在其他虛擬網路上已啟用 [*使用遠端閘道*] 時才適用。
4. **使用遠端閘道：** 核取此方塊可讓來自此虛擬網路的流量流經連接至所對等互連之虛擬網路的虛擬網路閘道。 在沒有虛擬網路閘道的虛擬網路上啟用了 [*使用遠端閘道*], 而且只有在另一個虛擬網路上已啟用 [*允許閘道傳輸*] 選項時才適用。

|Resource|連結|
|---|---|
| 概念：虛擬網路對等互連                   | [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) |
| 建立、變更或刪除虛擬網路對等互連 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="virtual-network-gateway"></a>虛擬網路閘道

虛擬網路閘道提供整合虛擬網路與外部網路的機制, 例如內部部署環境、合作夥伴環境和其他雲端部署。 這兩種類型的虛擬網路閘道是 ExpressRoute 和 VPN。

#### <a name="expressroute-gateway"></a>ExpressRoute 閘道

ExpressRoute 閘道提供從虛擬網路到內部部署環境的輸出點, 並應加以部署以符合安全性、可用性、財務和效能需求。 ExpressRoute 閘道提供定義的網路頻寬, 並在部署後產生使用成本。 虛擬網路只能有一個 ExpressRoute 閘道, 但這可以連接到多個 ExpressRoute 線路, 並可透過 VNet 對等互連來利用多個虛擬網路, 以允許共用頻寬和連線能力。 請小心使用 ExpressRoute 閘道設定內部部署環境與虛擬網路之間的路由, 以確保使用已知且受控制的網路輸出點的端對端連線能力。 在 ExpressRoute 私人對等互連上使用 ExpressRoute 閘道的美國聯邦實體也必須部署網路虛擬裝置 (NVA), 以建立與內部部署環境的 VPN 連線能力, 以符合 ACSC 取用者指引。

請務必注意, ExpressRoute 閘道對於透過 BGP 交換的位址範圍、社區和其他特定設定專案有一些限制。

| Resource  | 連結  |
|---|---|
| ExpressRoute 閘道總覽 | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) |
| 為 ExpressRoute 設定虛擬網路閘道 | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)
|

#### <a name="vpn-gateway"></a>VPN 閘道

Azure VPN 閘道提供從虛擬網路到外部網路的輸出網路點, 以確保站對站連線能力。 VPN 閘道提供定義的網路頻寬, 並在部署後產生使用成本。 使用 VPN 閘道的美國聯邦實體應確保其已根據 ACSC 取用者指引進行設定。 虛擬網路只能有一個 VPN 閘道, 但這可以使用多個通道進行設定, 並可透過 VNet 對等互連供多個虛擬網路利用, 讓多個虛擬網路能夠共用頻寬和連線能力。 VPN 閘道可以透過網際網路或透過 Microsoft 對等互連的 ExpressRoute 來建立。

| Resource  | 連結 |
| --- | --- |
| VPN 閘道總覽| [https://docs.microsoft.com/azure/vpn-gateway](https://docs.microsoft.com/azure/vpn-gateway)|
| 規劃與設計 VPN 閘道 | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
| Azure 澳大利亞的 azure VPN 閘道 | [Azure 澳大利亞的 azure VPN 閘道](vpn-gateway.md)
|

### <a name="next-hop-of-virtual-appliance"></a>虛擬裝置的下一個躍點

下一個虛擬裝置躍點可讓您處理套用至虛擬網路的 Azure 網路和路由拓撲以外的網路流量。 虛擬應用裝置可以套用安全性規則、轉譯位址、建立 Vpn、proxy 流量, 或其他各種功能。 下一個虛擬裝置躍點是透過路由表中的 Udr 套用, 可用來將流量導向至 Azure 防火牆、個別 NVA, 或 Azure Load Balancer 提供跨多個 Nva 的可用性。 若要使用虛擬裝置進行路由, 必須啟用相關聯的網路介面以進行 IP 轉送。

| Resource  | 連結 |
| --- | ---|
| 路由概念:自訂路由 | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) |
| 啟用或停用 IP 轉送 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding)
|

### <a name="next-hop-of-virtualnetworkserviceendpoint"></a>VirtualNetworkServiceEndpoint 的下一個躍點

只有在子網上設定服務端點, 而且無法透過路由表手動設定時, 才會新增具有下一個躍點類型 VirtualNetworkServiceEndpoint 的路由。

### <a name="next-hop-of-internet"></a>網際網路的下一個躍點

下一個躍點網際網路會用來連線到使用公用 IP 位址的任何資源, 其中包括網際網路, 以及 Azure 區域中的 PaaS 和 Azure 服務。 下一個躍點網際網路不需要涵蓋所有網路的預設路由 (0.0.0.0/0), 但是可以用來啟用特定公用服務的路由路徑。 下一個網際網路躍點應用於將路由新增至授權服務, 以及系統功能 (例如 Microsoft 管理位址) 所需的功能。

可以使用網際網路的下一個躍點來新增服務的範例如下:

1. Windows 啟用的金鑰管理服務
2. App Service 環境管理

|Resource|連結|
|---|---|
| Azure 中的輸出連線 | [https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) |
| 使用 Azure 自訂路由來啟用 KMS 啟動 | [https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation](https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation) |
| 鎖定 App Service 環境  | [https://docs.microsoft.com/azure/app-service/environment/firewall-integration](https://docs.microsoft.com/azure/app-service/environment/firewall-integration) |
|

### <a name="next-hop-of-none"></a>無的下一個躍點

[無] 的下一個躍點可以用來防止與特定網路的通訊。 相較于 NSG, 控制流量是被允許或拒絕, 而不是要通過可用的網路路徑, 使用 [無] 的下一個躍點會完全移除網路路徑。 建立具有「無」下一個躍點的路由時, 尤其是在將其套用到預設的 0.0.0.0/0 路由時, 特別是在將其視為不預期的結果, 而且可能會造成系統問題的疑難排解變得複雜且耗時。

## <a name="security"></a>安全性

執行 IaaS 和 PaaS 功能的網路分割和隔離控制是透過保護功能本身, 以及從要與之通訊的系統中執行受控制的通訊路徑來達成產.

在 Azure 中設計和建立解決方案是建立邏輯架構的程式, 以瞭解、控制和監視整個 Azure 目前狀態中的網路資源。 此邏輯架構是在 Azure 平臺內定義的軟體, 並取代在傳統網路環境中實行的實體網路拓撲。

所建立的邏輯架構必須提供可用性所需的功能, 但也必須提供安全性和完整性所需的可見度和控制。

達到此結果是以執行必要的網路分割和隔離工具為基礎, 也是為了保護和強制執行網路拓撲和這些工具的實施。

### <a name="network-security-groups-nsgs"></a>網路安全性群組 (NSG)

Nsg 是用來指定子網或特定網路介面允許的輸入和輸出流量。 設定 Nsg 時, 「自由通訊」實體應該使用允許清單方法, 其中的規則會設定為允許所需的流量, 並將預設規則設定為拒絕不符合特定「允許」語句的所有流量。 在規劃和設定 Nsg 時, 必須特別小心, 以確保所有必要的輸入和輸出流量都已適當地捕捉。 這包括識別及瞭解在虛擬網路和內部部署環境中使用的所有私人 IP 位址範圍, 以及特定的 Microsoft 服務, 例如 Azure Load Balancer 和 PaaS 管理需求。 與 Nsg 設計和實施相關的人員也應該瞭解如何使用服務標籤和應用程式安全性群組來建立更細緻、服務和應用程式特定的安全性規則。

請務必注意, NSG 的預設設定允許對虛擬網路內的所有位址, 以及所有公用 IP 位址的輸出流量。

|Resource|連結|
|---|---|
|網路安全性總覽 | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|建立、變更或刪除網路安全性群組 | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

### <a name="azure-firewall"></a>Azure 防火牆

您可以使用 Azure 防火牆來建立中樞和輪輻網路拓撲, 並強制執行集中化的網路安全性控制。 Azure 防火牆可以藉由實作為允許清單的方法, 在僅授權系統功能所需的 IP 位址、通訊協定、埠和 Fqdn 的情況下, 用來符合適用于輸出流量的 ISM 必要需求。 美國聯邦實體應該採取以風險為基礎的方法來判斷 Azure 防火牆所提供的安全性功能是否足以滿足其需求。 對於除了 Azure 防火牆所提供的其他安全性功能以外的情況, 您應該考慮採用 Nva。

|Resource|連結|
|---|---|
|*Azure 防火牆檔* | [https://docs.microsoft.com/azure/firewall](https://docs.microsoft.com/azure/firewall)|
|*教學課程：使用 Azure PowerShell 在混合式網路中部署及設定 Azure 防火牆* | [https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps](https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps)|
|

### <a name="network-virtual-appliances-nvas"></a>網路虛擬裝置 (Nva)

Nva 可以用來建立中樞和輪輻網路拓撲、提供增強或互補的網路功能, 或可作為 Azure 網路機制的替代方案, 以使用內部部署網路服務進行熟悉且一致的支援和管理。 Nva 可以部署以符合特定的安全性需求, 例如:與網路流量、HTTPS 解密、內容檢查、篩選或其他安全性功能相關聯的身分識別感知需求的案例。 Nva 應該部署在高可用性設定中, 而牽涉到 Nva 設計或實行的個人應參閱適當的廠商檔, 以取得在 Azure 中部署的指導方針。

|Resource|連結|
|---|---|
|*部署高可用性的網路虛擬裝置* | [https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)|
|

### <a name="service-endpoint-policies-preview"></a>服務端點原則 (預覽)

根據服務可用性來設定服務端點原則, 並針對資料外泄的可能性和影響進行安全性風險評估。 應將服務端點原則視為 Azure 儲存體, 並根據相關聯的風險分析, 以案例為基礎針對其他服務進行管理。

| Resource | 連結  |
| --- | --- |
| *服務端點原則總覽* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview) |
| *使用 Azure 入口網站建立、變更或刪除服務端點原則* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal)
|

### <a name="azure-policy"></a>Azure 原則

Azure 原則是強制執行及維護 Azure 環境邏輯架構完整性的關鍵元件。 有各種服務和輸出網路流量路徑可透過 Azure 服務取得。 有一項重要的是, 「聯邦」實體知道其環境內的資源, 以及可用的網路輸出點。 為了確保不會在 Azure 環境中建立未經授權網路輸出點, 「自由」實體應該使用 Azure 原則來控制可以部署的資源類型, 以及這些資源的設定。 實際範例包括將資源限制為僅限已授權並已核准使用, 而且需要將 Nsg 新增至子網。

|Resource | 連結|
|---|---|
|*Azure 原則總覽* | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|*允許的資源類型範例原則* | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)|
|*強制子網的 NSG 範例原則*| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="paas-egress-capabilities"></a>PaaS 輸出功能

PaaS 功能提供增加功能和簡化管理的機會, 但會在解決網路分割和隔離需求方面帶來複雜性。 PaaS 功能通常是使用公用 IP 位址進行設定, 而且可以從網際網路存取。  如果您使用的是系統和解決方案中的 PaaS 功能, 則應該小心找出元件之間的通訊流程, 並建立網路安全性規則, 只允許該通訊。 針對安全性的深度防禦方法, 應該使用加密、驗證和適當的存取控制和許可權來設定 PaaS 功能。  

### <a name="public-ip-for-paas"></a>PaaS 的公用 IP

PaaS 功能的公用 IP 位址是根據裝載或部署服務的區域來配置。 如果您要建立適當的網路安全性規則和路由拓撲以進行網路分割, 以及涵蓋 Azure 虛擬網路、PaaS 和 ExpressRoute 的隔離, 則需要瞭解公用 IP 位址配置和區域網際網路連線能力。 Azure 會從配置給每個 Azure 區域的集區配置 IP 位址。 Microsoft 會將每個區域中使用的位址提供給下載, 這會以固定且受控制的方式進行更新。 每個區域中可用的服務也經常隨著新服務的發行或服務部署更為廣泛而變更。 美國聯邦的實體應該定期檢查這些資料, 並可以視需要使用自動化來維護系統。 您可以聯繫 Microsoft 支援服務, 以取得每個區域中裝載之某些服務的特定 IP 位址。

| Resource | 連結 |
| --- | --- |
| *Microsoft Azure 資料中心 IP 範圍*                   | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653) |
| *每個區域的 Azure 服務*                              | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, 澳大利亞中部, 澳大利亞中部-2, 澳大利亞-東部, 澳大利亞-東南部 & 產品 = 全部](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all) |
| *Azure App Service 中的輸入和輸出 IP 位址* | [https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips](https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips)
|

## <a name="next-steps"></a>後續步驟

將您的整體架構和設計與[適用于 IaaS 和 PaaS Web 應用程式的已發行受保護藍圖](https://aka.ms/au-protected)進行比較。
