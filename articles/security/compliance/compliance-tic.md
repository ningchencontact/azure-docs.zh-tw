---
title: 適用於 Azure 的受信任網際網路連線指導
description: 適用於 Azure 與 SaaS 服務的受信任網際網路連線指導
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: 9d71efa35713500911c67d1df15612b64c8e97da
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990824"
---
# <a name="trusted-internet-connection-guidance"></a>受信任網際網路連線指導

## <a name="background"></a>背景

受信任網際網路連線 (TIC) 計畫的目的是使聯邦機構目前使用之各個外部網路連線的安全性達到最佳化和標準化。 該原則述於 OMB (管理和預算辦公室) [備忘錄 M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf) 內。

2007 年 11 月，OMB 建立了 TIC 計畫，以改善聯邦網路周邊安全性以及事件回應功能。 TIC 的設計目的在於對所有傳入和傳出的 .gov 流量執行網路分析，以識別特定簽名和以模式為基礎的資料，並找出行為異常現象，例如殭屍網路活動。 其規定機構需整合其外部網路連線，且所有流量必須經由託管在有限數量的網路端點 (稱為可信任的網際網路連線) 上的入侵偵測和防禦裝置 (稱為 EINSTEIN) 進行路由。

簡而言之，TIC 的目標是讓代理商了解以下事項：
- 誰在我的網路上 (授權或未經授權)？
- 何時存取我的網路？為何存取？
- 正在存取哪些資源？

現在所有機構的外部連線均必須透過 OMB 核准的 TIC 進行路由。 聯邦機構必須以 TIC 存取權限提供者 (TICAP) 的身分參與 TIC 計畫，或是與稱為受控的受信任網際網路通訊協定服務 (MTIPS) 提供者的主要一級網際網路服務提供者之一簽訂服務合約，藉以參與計畫。  TIC 包括機構和 MTIPS 提供者目前執行的強制性關鍵功能。 在目前版本的 TIC 中，EINSTEIN 版本 2 入侵偵測和 EINSTEIN 版本 3 加速 (3A) 入侵防禦裝置部署在每個 TICAP 和 MTIPS 上，而且機構與國土安全部 (DHS) 建立「備忘錄」(Memorandum of Understanding)，以將 EINSTEIN 功能部署至聯邦系統。

為了履行其保護 .gov 網路的責任，DHS 要求將機構 Netflow 資料之未經處理資料摘要與聯邦政府中的事件建立關聯，並使用專用工具執行分析。 DHS 路由器可在進入或退出介面時收集 IP 網路流量。 藉由分析網路流量資料，網路管理員可以確定流量來源和目的地、服務等級等資訊。網路流量資料視為「非內容資料」(例如，標頭、來源 IP、目的地 IP 等)，並允許 DHS 知道內容相關資訊；亦即，誰做了什麼事？做了多久？

採用內部部署基礎架構的安全性原則、指南和架構也包含在該計畫內。 隨著政府機構轉向雲端，以求節省成本、運作效率和創新，TIC 的實施需求在某些情況下會降低網路流量，並侷限政府使用者存取雲端型資料的速度和靈活性。

本文涵蓋政府機構如何使用 Microsoft Azure Government 幫助實現跨 IaaS 和 PaaS 服務的 TIC 原則合規性。

## <a name="summary-of-azure-networking-options"></a>Azure 網路選項摘要

連線到 Azure 服務時，有三個主要選項：

1. 直接網際網路連線：透過開放的網際網路連線直接連線到 Azure 服務。 媒介和連線均為公開， 是依賴應用程式和傳輸層級加密來確保隱私權。 頻寬受限於站台連線到網際網路的能力，而可使用多個作用中的提供者來確保復原能力
1. 虛擬私人網路：使用 VPN 閘道連線到您私人使用的 Azure 虛擬網路。
媒介公開，其會周遊站台的標準網際網路連線，但會在通道中加密連線以確保隱私權。 頻寬受限於 VPN 裝置和選擇的設定。 Azure 點對站連線通常限制為 100 Mbps，而站對站連線限制為 1.25 Gbps。
1. Microsoft ExpressRoute：ExpressRoute 會直接連線到 Microsoft 服務。 由於連線是透過獨立的光纖通道進行的，因此連線可以是公用連線或私人連線，取決於使用設定。 頻寬通常限制為最大 10 Gbps。

有幾種方法可以滿足「受信任網際網路連線附錄 H (雲端注意事項)」需求，請參閱國土安全部的「受信任網際網路連線 (TIC) 參考架構文件 2.0 版」。 在本文件中，DHS TIC 指南將稱為 TIC 2.0。

若要啟用從部門或機構 (D/A) 到 Azure 或 Office 365 的連線，而不透過 D/A TIC 路由流量，D/A 必須使用加密通道及/或雲端服務提供者 (CSP) 的專用連線。 CSP 服務可以確保不向公用網際網路提供 D/A 雲端資產的連線，以便機構人員直接存取。

O365 遵守 TIC 2.0 附錄 H，使用已啟用 [Microsoft 對等互連](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains)的 Express Route，或使用 TLS 1.2. 加密所有流量的網際網路連線。  D/A 網路上的 D/A 終端使用者可以透過其機構網路和 TIC 基礎架構，利用網際網路進行連線。 所有 O365 的遠端網際網路存取皆遭到封鎖，並透過機構進行路由。 D/A 也可以使用啟用的 Microsoft 對等互連 (這是一種公用對等互連)，透過 Express Route 連線與 O365 連線。  

僅適用於 Azure，選項 2 (VPN) 和 3 (ExpressRoute) 與限制存取網際網路的服務結合使用時，皆可滿足這些要求。

![Microsoft 受信任網際網路連線 (TIC) 圖表](media/tic-diagram-a.png)

## <a name="how-azure-infrastructure-as-a-service-offerings-can-help-with-tic-compliance"></a>Azure 基礎結構即服務供應項目可協助 TIC 合規性

使用 IaaS 遵守 TIC 原則相當簡單，因為 Azure 客戶會管理自己的虛擬網路路由。

若要確實符合 TIC 參考架構，最主要的需求是確保虛擬網路 (VNet) 成為部門或機構網路的私人擴充功能。 若要成為_私人_擴充功能，除非透過內部部署的 TIC 網路連線，否則該原則會要求沒有任何流量從您的網路離開。 此流程稱為「強制通道」，用於 TIC 合規性時，此流程會透過 TIC，將來自 CSP 環境中任何系統的所有流量，通過組織網路上的內部部署閘道，路由傳出網際網路。

Azure IaaS TIC 合規性可分為兩個主要步驟：

1. 組態
1. 稽核

### <a name="azure-iaas-tic-compliance-configuration"></a>Azure IaaS TIC 合規性設定

若要以 Azure 設定好符合 TIC 的架構，必須先防止網際網路直接存取您的虛擬網路，然後透過內部部署網路強制網際網路流量。

#### <a name="prevent-direct-internet-access"></a>防止網際網路直接存取

Azure IaaS 網路透過由子網路組成的虛擬網路進行，該子網路與虛擬機器的網路介面控制器 (NIC) 相關聯。

支援 TIC 合規性最簡單的情況是確保虛擬機器或集合無法連接到任何外部資源。 使用網路安全性群組 (NSG) 可以確保與外部網路中斷連線，NSG 可用於控制虛擬網路中一或多個 NIC 或子網路的流量。 NSG 包含存取控制規則，可根據流量方向、通訊協定、來源位址和連接埠與目的地位址和連接埠，允許或拒絕流量。 NSG 的規則可以隨時變更，而變更時會套用至所有相關聯的執行個體。  若要深入了解如何建立 NSG，請參閱[教學課程：使用 Azure 入口網站透過網路安全性群組篩選網路流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)。

#### <a name="force-internet-traffic-through-on-premises-network"></a>強制網際網路流量通過內部部署網路

Azure 會自動建立系統路由，並將路由指派給虛擬網路中的每個子網路。 您無法建立系統路由，也無法移除系統路由，但是您可以使用自訂路由覆寫某些系統路由。 當您使用特定 Azure 功能時，Azure 會為每個子網路建立預設的系統路由，並將其他選擇性預設路由新增至特定子網路或每個子網路。 此路由確保虛擬網路中的流量保持在虛擬網路內，會卸除 IANA 指定的專用位址空間，例如 10.0.0.0/8 (除非包含在虛擬網路的位址空間中)，而且如非必要，請勿將 0.0.0.0/0 路由到虛擬網路的網際網路端點。

![TIC 強制通道](media/tic-diagram-c.png)

為了確保所有流量周遊 D/A TIC，離開虛擬網路的所有流量都必須透過內部部署連線進行路由。  您可以透過建立使用者定義的路由，或藉由交換邊界閘道協定 (BGP) 路由，在內部網路閘道和 Azure 虛擬網路閘道間建立自訂路由。 如需使用者定義路由的詳細資訊，請參閱 https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined。 如需邊界閘道協定的詳細資訊，另請參閱 https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol。

#### <a name="user-defined-routes"></a>使用者定義的路由

如果您使用的是路由式虛擬網路閘道，可以新增使用者定義路由 (UDR)，設定為 0.0.0.0/0 流量，藉以在 Azure 中完成強制通道，以便路由到虛擬網路閘道的「下一個躍點」。 Azure 會透過系統定義的路由，排定使用者定義路由的優先順序，因此會導致所有非 VNet 流量傳送到您的虛擬網路閘道，而該閘道可將其路由到內部部署。 定義完成後，此使用者定義路由必須與 Azure 環境中所有虛擬網路中現有或新建的所有子網路建立關聯。

![使用者定義的路由和 TIC](media/tic-diagram-d.png)

#### <a name="border-gateway-protocol"></a>邊界閘道協定

如果您使用 ExpressRoute 或已啟用邊界閘道協定 (BGP) 的虛擬網路閘道，則 BGP 是通告路由的慣用機制。 透過 BGP 通告路由 0.0.0.0/0，ExpressRoute 和 BGP 會感知虛擬網路閘道將確保此預設路由套用至虛擬網路中的所有子網路。

### <a name="azure-iaas-tic-compliance-auditing"></a>Azure IaaS TIC 合規性稽核

Azure 提供多種方式以稽核 TIC 合規性。

#### <a name="effective-routes"></a>有效路由

若要確認已傳播預設路由，您可以觀察特定 VM、特定 NIC，或使用者定義路由表的「有效路由」。 這可以透過 Azure 入口網站完成 (如 https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal 中所述)，或透過 PowerShell 完成 (如 https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell 中所述)。 [有效路由] 刀鋒視窗可讓您查看相關的使用者定義路由、BGP 通告路由，以及套用至相關實體的系統路由，如下所示。

![路由螢幕擷取畫面](media/tic-screen-1.png)

**注意**：您無法檢視 NIC 的有效路由，除非它與執行中的虛擬機器相關聯。

#### <a name="network-watcher"></a>網路監看員

Azure 網路監看員提供多種工具，可用於稽核 TIC 合規性。  請至 https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview 深入了解網路監看員。

##### <a name="network-security-groups-flow-logs"></a>網路安全性群組流程記錄 

Azure 網路監看員可擷取網路流量記錄，指出 IP 流量周圍的中繼資料。 除了其他資料以外，網路流量記錄包含目標的來源和目的地位址。 這與來自虛擬網路閘道、內部部署邊緣裝置及/或 TIC 的記錄結合，可讓您監控確實在內部部署路由的所有流量。 

## <a name="how-azure-platform-as-a-service-offerings-can-help-with-tic-compliance"></a>Azure 平台即服務供應項目如何協助 TIC 合規性

Azure PaaS 服務 (例如 Azure 儲存體) 可透過網際網路連線的 URL 存取。 具有已核准認證的任何人都可以從任何位置存取資源 (例如儲存體帳戶)，而不需要周遊 TIC。 因此，許多政府客戶誤解 Azure PaaS 服務未符合 TIC 原則。 實際上，如果許多 Azure PaaS 服務可以連結到虛擬網路 (VNet)，則可使用與上述符合 TIC 的 IaaS 環境相同的架構以符合 TIC 原則。 將 Azure PaaS 服務與 Azure VNet 整合，可讓您從該 VNet 私下存取該服務，並允許透過使用者定義的路由或 BGP 套用 0.0.0.0/0 的自訂路由，確保所有受網際網路繫結的流量皆進行內部部署路由，以周遊 TIC。  可以使用以下模式將某些Azure服務集成到Vnet中：

- **部署專用的服務執行個體**：可以將越來越多的 PaaS 服務部署為具有 VNet 附加端點的專用執行個體。 例如，App Service 環境 (ASE) 可以在「隔離」模式中進行部署，使其網路端點限制為 VNet。 然後，此 ASE 可以裝載許多 Azure PaaS 服務，例如 Web Apps、API 和函式。
- **VNet 服務端點**：越來越多的 PaaS 服務允許選項將其端點移動到 VNet 私人 IP，而不是公用位址。

以下列出截至 2018 年 5 月為止，支援將專用執行個體部署到 VNet 或服務端點的服務：*(可用性代表 Azure Commercial，Azure Government 可用性擱置)。

### <a name="service-endpoints"></a>服務端點

|服務                   |狀態            |
|--------------------------|------------------|
|Azure 金鑰保存庫            | 私人預覽  |
|Cosmos DB                 | 私人預覽  |
|身分識別                  | 私人預覽  |
|Azure Data Lake           | 私人預覽  |
|Sql Postgress/Mysql       | 私人預覽  |
|Azure SQL 資料倉儲  | 公開預覽   |
|Azure SQL                 | GA               |
|儲存體                   | GA               |

### <a name="vnet-injection"></a>VNet 插入

|服務                            |狀態            |
|-----------------------------------|------------------|
|SQL 受控執行個體               | 公開預覽   |
|Azure Container Service(AKS)       | 公開預覽   |
|Service Fabric                     | GA               |
|API 管理                     | GA               |
|Azure Active Directory             | GA               |
|Azure Batch                        | GA               |
|ASE                                | GA               |
|Redis                              | GA               |
|HDI                                | GA               |
|運算虛擬機器擴展集  | GA               |
|運算雲端服務              | GA               |

### <a name="vnet-integration-details"></a>VNet 整合詳細資料

下圖會逐步說明使用「VNet 插入」和「VNet 服務通道」存取 PaaS 服務的一般網路流程。  有關網路服務閘道、VNet 和服務標籤的其他資訊，請參閱此處：https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags。

![TIC 的 PaaS 連線選項](media/tic-diagram-e.png)

1. 使用 ExpressRoute 私人連線至 Azure。 具有強制通道功能之 ExpressRoute 私人對等互連是用於強制 ExpressRoute 上所有客戶 VNet 流量回到內部部署之中。 不需要 Microsoft 對等互連。
2. Azure VPN 閘道與 ExpressRoute Microsoft 對等互連結合使用，可用於重疊客戶 VNet 與內部部署邊緣之間端對端的 IPSec 加密。 
3. 使用網路安全性群組 (NSG) 控制與客戶 VNet 的網路連線，可讓客戶根據 IP、連接埠和通訊協定加以允許/拒絕。
4. 藉由為客戶服務建立服務端點，將客戶 VNet 擴展到 PaaS 服務。
5. PaaS 服務端點被保護為預設拒絕全部，並且僅允許從客戶 VNet 內的指定子網路存取。  預設拒絕也包含源自於網際網路的連線。
6. 需要存取客戶 VNet 中資源的任何其他 Azure 服務應為以下其中之一：  
  a. 直接部署至 VNet  
  b. 根據個別 Azure 服務的指導選擇性地允許。

#### <a name="option-1-dedicated-instance-vnet-injection"></a>選項 1：專用執行個體「VNet 插入」

客戶可使用 VNet 插入選擇性地將給定 Azure 服務的專用執行個體 (例如 HDInsight) 部署到他們自己的 VNet。 服務執行個體已部署至客戶 VNet 中的專用子網路。 VNet 插入允許透過非網際網路可路由位址存取服務資源。  內部部署執行個體可以直接透過 ExpressRoute 或站點到站點 VPN，透過 VNet 位址空間存取這些服務執行個體，而不是開啟防火牆到公用網際網路位址空間。 透過連結到端點的專用執行個體，可以使用與 IaaS TIC 合規性相同的原則，預設路由以確保將網際網路繫結流量重新導向內部部署的虛擬網路閘道繫結。 可以透過給定子網路的網路安全性群組 (NSG) 進一步控制傳入和傳出存取。

![VNet 插入概觀圖表](media/tic-diagram-f.png)

#### <a name="option-2-vnet-service-endpoints"></a>選項 2：VNet 服務端點 

越來越多 Azure 多租用戶服務提供「服務端點」功能，這是一種整合到 Azure VNets 的替代方法。 VNet 服務端點可透過直接連線，將您的 VNet IP 位址空間和 VNet 的身分識別延伸至服務。  從 VNet 到 Azure 服務的流量一定會保留在 Azure 骨幹網路上。 為服務啟用服務端點後，可以透過服務公開的原則將服務的連線限制為該 VNet。 Azure 服務在平台中強制執行存取檢查，而且只有在請求來自允許的 VNet/子網路時，才會授與對鎖定資源的存取權，及/或如果使用 ExpressRoute，則用於識別內部部署流量的兩個 IP。 這可用於有效防止傳入/傳出流量直接離開 PaaS 服務。

![服務端點概觀圖表](media/tic-diagram-g.png)

## <a name="using-cloud-native-tools-for-network-situational-awareness"></a>對於網路局勢意識使用雲端原生工具

Azure 提供多種雲端原生工具，以協助確保您具備了解網路流量所需的局勢意識。 它們不需要遵守 TIC 原則，但可大幅提高安全性功能。

### <a name="azure-policy"></a>Azure 原則

Azure 原則 (https://azure.microsoft.com/services/azure-policy/) 是一項 Azure 服務，可為您的組織提供更好的稽核和實施合規性方案的能力。  目前可在 Azure 商務雲端中的公開預覽中使用，但尚無法在政府的 Microsoft Azure 中使用，具 TIC 意識的客戶可以開始規劃及測試其原則規則，以確保未來的合規性。 Azure 原則的目標是訂用帳戶層級，並提供集中式介面來管理計劃、原則定義、稽核和強制執行結果，以及異常管理。 除了許多內建的 Azure 原則定義以外，管理員可以透過簡單的 json 範本設定來定義自己的自訂定義。對於許多客戶而言，Microsoft 建議盡可能強制執行稽核的優先順序。

下列範例原則對於 TIC 合規性案例可能很有用：

|原則  |範例案例  |起始範本  |
|---------|---------|---------|
|強制執行使用者定義的路由表 |     確保所有虛擬網路上的預設路由指向已核准的虛擬網路閘道，以便路由到內部部署 | https://docs.microsoft.com/azure/azure-policy/scripts/no-user-def-route-table |
|稽核是否未針對區域啟用網路監看員  | 請確定已經針對所有使用區域啟用網路監看員  | https://docs.microsoft.com/azure/azure-policy/scripts/net-watch-not-enabled |
|對每個子網路使用 NSG X  | 確保具有已封鎖網際網路流量的 NSG (或已核准 NSG 的集合) 會套用至每個 VNet 中的所有子網路 | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-subnet |
|對每個 NIC 使用 NSG X | 確保具有已封鎖網際網路流量的 NSG 會套用至所有虛擬機器上的所有 NIC。 | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-nic |
|針對 VM 網路介面使用已核准的 VNet  | 確保所有 NIC 都在已核准的 VNet 上 | https://docs.microsoft.com/azure/azure-policy/scripts/use-approved-vnet-vm-nics |
|允許的位置 | 確保將所有資源部署到具有合規性 VNets 和網路監看員設定的區域  | https://docs.microsoft.com/azure/azure-policy/scripts/allowed-locs |
|不允許的資源類型，例如 PublicIPs  | 禁止部署沒有合規性計畫的資源類型。 例如，此原則可用於禁止部署公用 IP 位址資源。 雖然 NSG 規則可用於有效阻止傳入網際網路流量，但避免使用公用 IP 可進一步減少攻擊面。    | https://docs.microsoft.com/azure/azure-policy/scripts/not-allowed-res-type  |

### <a name="azure-traffic-analyticshttpsazuremicrosoftcomen-inblogtraffic-analytics-in-preview"></a>Azure [流量分析](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/)

Azure 網路監看員的流量分析使用流量記錄資料和其他記錄來提供網路流量的高階概觀。 此資料可用於稽核 TIC 合規性並找出有問題的地方。 高階儀表板可用於快速篩選哪些虛擬機器與網際網路通訊，為 TIC 路由提供重點清單。

![流量分析螢幕擷取畫面](media/tic-traffic-analytics-1.png)

「地區地圖」可用於快速識別虛擬機器之網際網路流量的可能實體目的地，可讓您識別和分級可疑位置或模式變更。

![流量分析螢幕擷取畫面](media/tic-traffic-analytics-2.png)

網路拓撲地圖可用於快速調查現有的虛擬網路：

![流量分析螢幕擷取畫面](media/tic-traffic-analytics-3.png)

### <a name="azure-network-watcher-next-hop"></a>Azure 網路監看員下一個躍點

網路監看員監控之區域中的網路可以進行「下一個躍點」測試，允許簡單的入口網站型存取，以輸入樣本網路流量的來源和目的地，以供網路監看員解析「下一個躍點」目的地。 這可以用於虛擬機器和範例網際網路位址，以確保「下一個躍點」確實是網路虛擬閘道。

![網路監看員下一個躍點](media/tic-network-watcher.png)

## <a name="conclusions"></a>結論

可輕鬆設定 Microsoft Azure、Office 365 和 Dynamics 365 存取，有助於符合 2018 年 5 月撰寫和定義的 TIC 2.0 附錄 H 指南的規定。  Microsoft 知悉本指南會隨時更改，並會在推出新指南時，盡力幫助客戶及時符合指南。

## <a name="appendix-tic-patterns-for-common-workloads"></a>附錄：常見工作負載的 TIC 模式

| 類別 | 工作負載 | IaaS | 專用的 PaaS / VNet 插入  | 服務端點  |
|---------|---------|---------|---------|--------|
| 計算 | Linux 虛擬機器 | 是 | | |
| 計算 | Windows 虛擬機器 | 是 | | |
| 計算 | 虛擬機器擴展集 | 是 | | |
| 計算 | Azure Functions | | 透過 App Service Environment (ASE) | |
| Web 與行動 | 內部 Web 應用程式 | | 透過 App Service Environment (ASE) | |
| Web 與行動 | 內部行動應用程式 | | 透過 App Service Environment (ASE) | |
| Web 與行動 | API Apps | | 透過 App Service Environment (ASE) | |
| 容器 | Azure Container Service (ACS) | | | 是 |
| 容器 | Azure Container Service (AKS)* | | | 是 |
| 資料庫 | SQL Database | | Azure SQL Database 受控執行個體* | Azure SQL |
| 資料庫 | 適用於 MySQL 的 Azure 資料庫 | | | 是 |
| 資料庫 | 適用於 PostgreSQL 的 Azure 資料庫 | | | 是 |
| 資料庫 | SQL 資料倉儲 | | | 是 |
| 資料庫 | Azure Cosmos DB | | | 是 |
| 資料庫 | Redis 快取 | | 是 | |
| 儲存體 | Blob | 是 | | |
| 儲存體 | 檔案 | 是 | | |
| 儲存體 | 佇列 | 是 | | |
| 儲存體 | 資料表 | 是 | | |
| 儲存體 | 磁碟 | 是 | | |

*：截至 2018 年 5 月的 Azure Government 公開預覽  
*：截至 2018 年 5 月的 Azure Government 個人版預覽
