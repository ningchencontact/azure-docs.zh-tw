---
title: "Azure Stack 整合系統的規劃考量 | Microsoft Docs"
description: "了解您現在可以規劃的內容並準備多節點 Azure Stack。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: cd14f0e5259e5c0b6cbf11790bbdf08164267ffa
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2018
---
# <a name="planning-considerations-for-azure-stack-integrated-systems"></a>Azure Stack 整合系統的規劃考量
如果您對 Azure Stack 整合式系統有興趣，您應瞭解一些有關部署的重大規劃考量，及系統如何融入您的資料中心。 本文提供這些考量的高階概觀，協助您為 Azure Stack 多節點系統做出重要的基礎結構決策。 與您的 OEM 硬體廠商一起將 Azure Stack 部署到您的資料中心時，瞭解這些考量有所助益。  

> [!NOTE]
> 您只能向經過授權的硬體廠商購買 Azure Stack 多節點系統。 

若要部署 Azure Stack，您必須進行一些決策，才能正確地整合 Azure Stack 與您的環境。 在規劃過程中，您必須將此資訊提供給解決方案提供者，並在部署開始前準備好與硬體廠商合作，讓程序快速且順暢地進行。

所需的資訊涵蓋網路功能、安全性和身分識別資訊，以及許多可能需要很多不同領域和決策制定者相關知識的重要決策。 因此，您必須與組織中多個小組的人員交涉，以確保您在部署開始前已備妥所有必要資訊。 在收集此資訊時，與您的硬體廠商洽談可能有所幫助，因為他們可能會提供有助於進行決策的建議。

在研究並收集必要資訊時，您可能必須對網路環境進行一些部署前的設定變更。 這可能包括保留 Azure Stack 解決方案的 IP 位址空間，設定您的路由器、交換器和防火牆，以便為新的 Azure Stack 解決方案交換器連線預作準備。 確定目前有主題領域專家在部署專案全程協助您進行規劃。

## <a name="management-considerations"></a>管理考量
Azure Stack 是密封的系統，已從權限和網路觀點鎖定基礎結構。 會套用網路存取控制清單 (ACL) 來封鎖所有未經授權的連入流量，及基礎結構元件之間所有不必要的通訊。 這會使得未經授權的使用者難以存取系統。

對於每日的管理與作業，系統管理員存取基礎結構不受限制。 Azure Stack 操作員必須透過系統管理員入口網站或 Azure Resource Manager (透過 Azure CLI、PowerShell 或 REST API) 管理系統。 其他管理工具 (例如 HYPER-V 管理員或容錯移轉叢集管理員) 不能存取系統。 為了協助保護系統，無法在 Azure Stack 基礎結構元件中安裝協力廠商軟體 (例如，代理程式)。 透過 Azure CLI、PowerShell 或 REST API，可與外部的管理與安全性軟體建立互通性。

需要較高層級的存取權來疑難排解無法透過警示中繼步驟解決的問題時，您必須與支援中心合作。 透過支援中心，有一個方法可將暫時的完整系統管理員存取權提供給系統，以執行更進階的操作。 

## <a name="identity-considerations"></a>身分識別考量

### <a name="choose-identity-provider"></a>選擇識別提供者
您必須考量要使用於 Azure Stack 部署 (可能是 Azure AD 或 AD FS) 的識別提供者。 重新部署不含完整系統的部署之後，您無法切換識別提供者。

您的識別提供者選項與租用戶虛擬機器、識別系統、其使用的帳戶、它們是否能加入 Active Directory 網域等等無關。這是分開的。

您可以在 [Azure Stack 整合式系統的部署決策](.\azure-stack-deployment-decisions.md)一文中深入了解如何選擇識別提供者。

### <a name="ad-fs-and-graph-integration"></a>AD FS 和 Graph 整合
如果您選擇將 AD FS 作為識別提供者來部署 Azure Stack，您必須透過同盟信任來整合 Azure Stack 上的 AD FS 執行個體與現有的 AD FS 執行個體。 這可讓現有 Active Directory 樹系中的身分識別來驗證 Azure Stack 中的資源。

您也可以整合 Azure Stack 中的 Graph 服務與現有的 Active Directory。 這可讓您在 Azure Stack 中管理角色型存取控制 (RBAC)。 委派資源的存取權後，Graph 元件會使用 LDAP 通訊協定來查閱現有 Active Directory 樹系中的使用者帳戶。

下圖顯示整合的 AD FS 和 Graph 流量。
![顯示 AD FS 與 Graph 流量的圖表](media/azure-stack-deployment-planning/ADFSIntegration.PNG)

## <a name="licensing-model"></a>授權模型

您必須決定要使用哪個授權模型。 針對已連線的部署，您可以選擇使用時付費或以容量為基礎的授權。 使用時付費需要連線至 Azure 來報告使用量，然後透過 Azure 商務計費。 如果您的部署中斷與網際網路的連線，只能使用以容量為基礎的授權。 如需授權模式的詳細資訊，請參閱 [Microsoft Azure Stack 封裝和定價](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)。

## <a name="naming-decisions"></a>命名決策

您將需要思考如何規劃 Azure Stack 命名空間，尤其是區域名稱和外部網域名稱。 公開端點之 Azure Stack 部署的完整網域名稱 (FQDN) 是這兩個名稱的組合 ( &lt;*region*&gt;&lt;*external_FQDN*&gt;)，例如 *east.cloud.fabrikam.com*。在此範例中，Azure Stack 入口網站將位於下列 URL：

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

下表摘要這些網域命名決策。

| Name | 說明 | 
| -------- | ------------- | 
|區域名稱 | 您的第一個 Azure Stack 區域名稱。 這個名稱會用於 Azure Stack 管理的公用虛擬 IP 位址 (VIP) 之 FQDN 的一部分。 一般而言，區域名稱會是資料中心位置之類的實體位置識別碼。 | 
| 外部網域名稱 | 含外部 VIP 之端點的網域名稱系統 (DNS) 區域名稱。 針對這些公用 VIP 在 FQDN 中使用。 | 
| 私人 (內部) 網域名稱 | 在 Azure Stack 上為基礎結構管理建立的網域 (和內部 DNS 區域) 的名稱。 
| | |

## <a name="certificate-requirements"></a>憑證需求

針對部署，您將需要為公開端點提供安全通訊端層 (SSL) 憑證。 概括而言，憑證具有下列需求：

- 您可以使用單一萬用字元憑證或以使用一組專用的憑證，並只針對端點 (例如儲存體和金鑰保存庫) 使用萬用字元。
- 憑證可以由大眾信任的憑證授權單位 (CA) 或由客戶管理的 CA 發出。

如需有關部署 Azure Stack 時需要哪些 PKI 憑證以及如何取得這些憑證的詳細資訊，請參閱 [Azure Stack 公開金鑰基礎結構憑證需求](azure-stack-pki-certs.md)。  


> [!IMPORTANT]
> 所提供的 PKI 憑證資訊應作為一般指引。 取得 Azure Stack 的任何 PKI 憑證之前，請與您的 OEM 硬體合作夥伴接洽。 它們將提供更詳細的憑證指引和需求。



## <a name="time-synchronization"></a>時間同步處理
您必須選擇用來同步處理 Azure Stack 的特定時間伺服器。  時間同步處理對於 Azure Stack 及其基礎結構角色而言很重要，因為它用於產生 Kerberos 票證，而這些票證用於內部服務的彼此驗證。

您必須指定時間同步處理伺服器的 IP，雖然基礎結構中的大部分元件都可以解析 URL，但有些元件只支援 IP 位址。 如果您使用已中斷連線的部署選項，您必須指定您公司網路上的時間伺服器，且確定您可從 Azure Stack 中的基礎結構網路觸達該伺服器。 這可能需要在規劃部署專案之網路整合部分期間的其他考量。


## <a name="network-connectivity"></a>網路連線
本節提供的 Azure Stack 網路基礎結構資訊可協助您做出有關如何將 Azure Stack 妥善整合到現有網路環境的重要決策。 

> [!NOTE]
> 若要解析 Azure Stack 的外部 DNS 名稱 (例如 www.bing.com)，您必須提供 Azure Stack 可用來轉送 Azure Stack 對其而言不具權威性之 DNS 要求的 DNS 伺服器。 如需有關 Azure Stack DNS 需求的詳細資訊，請參閱 [Azure Stack 資料中心整合 - DNS](azure-stack-integrate-dns.md)。

### <a name="physical-network-design"></a>實體網路設計
Azure Stack 解決方案需要有彈性且高可用性的實體基礎結構，以支援其作業和服務。 以下是我們的建議設計圖：

![建議的 Azure Stack 網路設計](media/azure-stack-deployment-planning/recommended-design.png)


### <a name="logical-networks"></a>邏輯網路
邏輯網路代表基礎實體網路基礎結構的抽象概念。 其用來組織及簡化主機、虛擬機器和服務的網路指派。 建立邏輯網路時會建立網路站台，以定義虛擬區域網路 (VLAN)、IP 子網路，以及與每個實體位置中邏輯網路相關聯的 IP 子網路/VLAN 配對。
Azure Stack 的網路基礎結構會使用交換器上設定的下列邏輯網路：

### <a name="network-infrastructure"></a>網路基礎結構
Azure Stack 的網路基礎結構包含交換器上設定的數個邏輯網路。 下圖顯示這些邏輯網路，及其如何整合機架頂端 (TOR)、基礎板管理控制器 (BMC) 與界限 (客戶網路) 交換器。

![邏輯網路圖表和交換器連線](media/azure-stack-deployment-planning/NetworkDiagram.png)

#### <a name="bmc-network"></a>BMC 網路
此網路專門用來將所有基礎板管理控制器 (也稱為服務處理器，例如 iDRAC、iLO、iBMC 等) 連線至管理網路。 如果硬體生命週期主機 (HLH) 存在，它將會位於此網路，並提供用於維護及/或監視硬體的 OEM 專用軟體。 

#### <a name="private-network"></a>私人網路
此 /24 (254 個主機 IP) 網路由 Azure Stack 區域所私有 (不擴充到超出 Azure Stack 區域的界限交換器裝置)，而且分成兩個子網路：

- **儲存體網路**。 /25 (126 個主機 IP) 網路用於支援使用儲存體空間直接存取 (S2D) 和伺服器訊息區 (SMB) 儲存體流量，以及虛擬機器即時移轉。 
- **內部虛擬 IP 網路**。 /25 網路專屬於適用軟體負載平衡器 (SLB) 的僅內部用 VIP。

#### <a name="azure-stack-infrastructure-network"></a>Azure Stack 基礎結構網路
此 /24 網路專屬於內部 Azure Stack 元件，以便這些元件通訊和交換本身的資料。 此子網路需要可路由傳送的 IP 位址，但會使用存取控制清單 (ACL) 來保持由解決方案所私有。  它不應該傳送超出界限交換器，但大小等同於 /27 網路的非常小範圍除外，而當其中一些服務需要存取外部資源及/或網際網路時則會利用 /27 網路。 

#### <a name="public-infrastructure-network"></a>公用基礎結構網路
此 /27 網路是先前所述 Azure Stack 基礎結構子網路中非常小的範圍，其不需要公用 IP 位址，但它卻需要透過 NAT 或 Transparent Proxy 存取網際網路。 此網路將會配置給緊急修復主控台系統 (ERCS)。 ERCS VM 在向 Azure 註冊期間需要存取網際網路，而且應可路由傳送至您的管理網路，以便進行疑難排解。

#### <a name="public-vip-network"></a>公用 VIP 網路
公用 VIP 網路會指派給 Azure Stack 中的網路控制器。 它不是交換器上的邏輯網路。 SLB 會針對租用戶工作負載使用位址集區並指派 /32 網路。 在交換器路由表上，這些 /32 IP 會公告為可透過 BGP 使用的路由。 此網路包含外部可存取的 IP 位址或公用 IP 位址。 Azure Stack 基礎結構使用來自此公用 VIP 網路的至少 8 個位址，而其餘位址則由租用戶 VM 使用。 此子網路上的網路大小範圍從最小 /26 (64 部主機) 到最大 /22 (1022 部主機)，我們建議您規劃 /24 網路。

#### <a name="switch-infrastructure-network"></a>交換器基礎結構網路
此 /26 網路是一個子網路，其中包含可路由傳送的點對點 IP /30 (2 個主機 IP) 子網路和回送 (這是用於頻內交換器管理與 BGP 路由器識別碼的專用 /32 子網路)。 此 IP 位址範圍必須可在 Azure Stack 解決方案外部路由傳送至您的資料中心，而這些 IP 位址可以是私人或公用 IP。 例如，在多重租用戶服務提供者案例中可能需要公用 IP，然而在嚴格控制的企業部署中可能偏好私人 IP。

#### <a name="switch-management-network"></a>交換器管理網路
此 /29 (6 個主機 IP) 網路專門用來與交換器的管理連接埠連線。 其允許部署、管理和疑難排解的頻外存取。 它是從上述的交換器基礎結構網路計算而來。

### <a name="transparent-proxy"></a>TRANSPARENT PROXY
Azure Stack 解決方案不支援一般 Web Proxy。 如果資料中心要求所有流量使用 Proxy，您必須設定 Transparent Proxy 以處理來自機架 (Rack) 的所有流量，進而根據原則進行處理，並分隔您網路上各區域的流量。 Transparent Proxy (也稱為攔截、內嵌或強制 Proxy) 會攔截網路層上的一般通訊，而不需要任何特殊的用戶端設定。 用戶端不需要留意 Proxy 的存在。

### <a name="publish-azure-stack-services"></a>發佈 Azure Stack 服務

您必須將 Azure Stack 服務開放給 Azure Stack 的外部使用者使用。 Azure Stack 會設定其基礎結構角色的各種端點。 這些端點是來自公用 IP 位址集區的指派 VIP。 會針對外部 DNS 區域中的每個端點建立一個 DNS 項目，這是在部署時間所指定。 例如，系統會將 portal.*&lt;region>.&lt;fqdn>* 的 DNS 主機項目指派給使用者入口網站。

#### <a name="ports-and-urls"></a>連接埠和 URL

若要讓外部網路使用 Azure Stack 服務 (例如入口網站、Azure Resource Manager、DNS 等)，您必須允許針對特定 URL、連接埠和通訊協定允許至這些端點的輸入流量。
 
在上行連結至傳統 Proxy 伺服器的部署中，您必須允許特定連接埠和 URL 的連出通訊。 其中包括用於身分識別、Marketplace 摘要整合、修補和更新、註冊和使用資料的連接埠和 URL。

如需詳細資訊，請參閱
- [輸入連接埠和通訊協定](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)
- [輸出連接埠和 URL](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)

### <a name="connect-azure-stack-to-azure"></a>將 Azure Stack 連線至 Azure

對於混合式雲端情節，您必須規劃如何將 Azure Stack 連線至 Azure。 支援兩種方法將 Azure Stack 中的虛擬網路連線至 Azure 中的虛擬網路： 
- **網站間**。 透過 IPsec (IKE v1 和 IKE v2) 的虛擬私人網路 (VPN) 連線。 此類型的連線需要 VPN 裝置或路由及遠端存取服務 (RRAS)。 如需 Azure 中 VPN 閘道的詳細資訊，請參閱[關於 VPN 閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。 會透過這個通道的通訊加密，並且是安全的。 不過，頻寬會受限於通道的最大輸送量 (100-200 Mbps)。
- **輸出 NAT**。 根據預設，Azure Stack 中的所有虛擬機器皆可透過輸出 NAT 連線到外部網路。 Azure Stack 中建立的每個虛擬網路會被指派一個公用 IP 位址。 無論虛擬機器被直接指派公用 IP 位址，或是位於含公用 IP 位址的負載平衡器後方，它皆可使用虛擬網路的 VIP，透過輸出 NAT 進行外部存取。 這只適用於虛擬機器所起始，並針對外部網路 (網際網路或內部網路) 的通訊。 它不能與來自外部的虛擬機器進行通訊。

#### <a name="hybrid-connectivity-options"></a>混合式連線能力選項

對於混合式連線能力，務必考慮您想要提供何種部署及其部署位置。 您必須考量是否需要隔離每個租用戶的網路流量，以及是否也有內部網路或網際網路部署。

- **單一租用戶 Azure Stack**。 Azure Stack 部署看起來 (至少從網路觀點) 就像是一個租用戶。 可能有許多租用戶訂用帳戶，但就像任何內部網路服務，所有流量都會透過相同的網路傳輸。 一個訂用帳戶會與另一個訂用帳戶透過相同的網路連線傳輸網路流量，並且不需要透過加密通道隔離。

- **多租用戶 Azure Stack**。 一個 Azure Stack 部署，其中每個租用戶訂用帳戶流量與 Azure Stack 外部的網路繫結，必須將其與其他租用戶的網路流量隔離。
 
- **內部網路部署**。 位於公司內部網路上 Azure Stack 部署，一般位於私人 IP 位址空間與一或多個防火牆之後。 公用 IP 位址並非真正公用，因為它們無法透過公用網際網路直接路由。

- **網際網路部署**。 一個 Azure Stack 部署，其連線到公用網際網路，並針對公用 IP 位址使用網際網路的可路由公用 IP 位址。 部署仍可放在防火牆後面，但公用 VIP 範圍可從公用網際網路和 Azure 直接連線。
 
下表摘要說明混合式連線情節，包含優點、缺點和使用案例。

| 案例 | 連線方法 | 優點 | 缺點 | 適用對象 |
| -- | -- | --| -- | --|
| 單一租用戶 Azure Stack，內部網路部署 | 輸出 NAT | 用於更快傳輸的更佳頻寬。 簡單實作；沒有必要的閘道。 | 不會加密流量；TOR 外部無隔離或加密。 | 同樣信任所有租用戶的企業部署。<br><br>具有至 Azure 的 Azure ExpressRoute 電路的企業。 |
| 多租用戶 Azure Stack，內部網路部署 | 站對站 VPN | 從租用戶 VNet 到目的地的流量是安全的。 | 頻寬會受限於站對站 VPN 通道。<br><br>需要虛擬網路上的閘道和目的地網路上的 VPN 裝置。 | 必須避免其他租用戶接觸其部分租用戶流量的企業部署。 |
| 單一租用戶 Azure Stack，網際網路部署 | 輸出 NAT | 用於更快傳輸的更佳頻寬。 | 不會加密流量；TOR 外部無隔離或加密。 | 裝載情節，其中租用戶取得其自己的 Azure Stack 部署和 Azure Stack 環境的專屬電路。 例如，ExpressRoute 和多重通訊協定標籤交換 (MPLS)。
| 多租用戶 Azure Stack，網際網路部署 | 站對站 VPN | 從租用戶 VNet 到目的地的流量是安全的。 | 頻寬會受限於站對站 VPN 通道。<br><br>需要虛擬網路上的閘道和目的地網路上的 VPN 裝置。 | 裝載情節，其中的提供者要提供多租用戶雲端，讓租用戶彼此不信任且必須加密流量。
|  |  |  |  |  |

#### <a name="using-expressroute"></a>使用 ExpressRoute

您可以針對單一租用戶內部網路和多租用戶情節，透過 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 將 Azure Stack 連線至 Azure。 您將需要透過[連線提供者](https://docs.microsoft.com/azure/expressroute/expressroute-locations)佈建的 ExpressRoute 電路。

下圖顯示單一租用戶情節的 ExpressRoute (其中「客戶的連線」是 ExpressRoute 電路)。

![顯示單一租用戶 ExpressRoute 情節的圖表](media/azure-stack-deployment-planning/ExpressRouteSingleTenant.PNG)

下圖顯示多租用戶情節的 ExpressRoute。

![顯示多租用戶 ExpressRoute 情節的圖表](media/azure-stack-deployment-planning/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>外部監視
若要從您的 Azure Stack 部署和裝置取得所有警示的單一檢視，並針對票證將警示整合至現有的 IT 服務管理工作流程，您可以整合 Azure Stack 與外部的資料中心監視解決方案。

Azure Stack 解決方案隨附的硬體生命週期主機 (HLH) 是 Azure Stack 外部的電腦，其執行 OEM 硬體廠商為硬體提供的管理工具。 您可以使用這些工具，或與資料中心內的現有監視解決方案直接整合的其他解決方案。

下表摘要列出目前的可用選項清單。

| 領域 | 外部監視解決方案 |
| -- | -- |
| Azure Stack 軟體 | - [Operations Manager 的 Azure Stack 管理組件](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>- [Nagios 外掛程式](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>- REST 型 API 呼叫 | 
| 實體伺服器 (透過 IPMI 的 BMC) | - Operations Manager 廠商管理組件<br>- OEM 硬體廠商提供的解決方案<br>- 硬體廠商 Nagios 外掛程式 | OEM 合作夥伴支援的監視解決方案 (包含) | 
| 網路裝置 (SNMP) | - Operations Manager 網路裝置探索<br>- OEM 硬體廠商提供的解決方案<br>- Nagios 交換器外掛程式 |
| 租用戶訂用帳戶健康情況監視 | - [System Center Management Pack for Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

請注意下列需求：
- 您使用的解決方案必須是無代理程式。 您無法在 Azure Stack 元件內部安裝協力廠商代理程式。 
- 如果您想要使用 System Center Operations Manager，這需要 Operations Manager 2012 R2 或 Operations Manager 2016。

## <a name="backup-and-disaster-recovery"></a>備份和災害復原

規劃備份及災害復原牽涉到規劃這兩個基礎 Azure Stack 基礎結構 (用來裝載 IaaS 虛擬機器和 PaaS 服務)，以及租用戶應用程式和資料。 您必須分開規劃它們。

### <a name="protect-infrastructure-components"></a>保護基礎結構元件

Azure Stack 會將基礎結構元件備份到您指定的共用。

- 您在現有的 Windows 型檔案伺服器或協力廠商裝置上需要有外部 SMB 檔案共用。
- 您應該將這個相同的共用使用於網路交換器與硬體生命週期主機的備份。 OEM 硬體廠商將協助提供這些元件的備份和還原指引，因為這些是 Azure Stack 外部的作業。 您負責根據 OEM 廠商的建議來執行備份工作流程。

如果發生重大資料遺失，您可以使用基礎結構來重新植入部署資料，例如部署輸入和識別碼、服務帳戶、CA 根憑證、同盟的資源 (在中斷連線的部署中)、計劃、優惠、訂用帳戶、配額、RBAC 原則與角色指派，和金鑰保存庫密碼。
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>保護 IaaS 虛擬機器上的租用戶應用程式

Azure Stack 不會備份租用戶應用程式和資料。 您必須針對 Azure Stack 的外部目標規劃備份及災害復原保護。 租用戶保護是租用戶驅動的活動。 對於 IaaS 虛擬機器，可在來賓技術中使用租用戶來保護檔案資料夾、應用程式資料和系統狀態。 不過，作為企業或服務提供者，您可能想要在相同資料中心或雲端外部提供備份和復原解決方案。

若要備份 Linux 或 Windows IaaS 虛擬機器，您必須使用含來賓作業系統存取權的備份產品來保護檔案、資料夾、作業系統狀態和應用程式資料。 您可以使用 Azure 備份，System Center Data Center Protection Manager 或支援的協力廠商產品。

若要將資料複寫到次要位置並協調應用程式在發生災難時容錯移轉，您可以使用 Azure Site Recovery 或支援的協力廠商產品。 (在初始版本的整合系統中，Azure Site Recovery 不會支援容錯回復。 不過，您可以透過手動程序完成容錯回復。)此外，支援原生複寫 (如 Microsoft SQL Server) 的應用程式可將資料複寫到正在執行應用程式的另一個位置。

> [!IMPORTANT]
> 在初始版本的整合系統中，我們將支援在 IaaS 虛擬機器的來賓層級上工作的保護技術。 您無法在基礎的基礎結構伺服器上安裝代理程式。

## <a name="next-steps"></a>後續步驟

- 如需使用案例、購買、合作夥伴和 OEM 硬體廠商的詳細資訊，請參閱 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 產品頁面。
- 如需 Azure Stack 整合系統的藍圖和地理可用性的詳細資訊，請參閱白皮書：[Azure Stack：Azure 的延伸模組](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)。 
