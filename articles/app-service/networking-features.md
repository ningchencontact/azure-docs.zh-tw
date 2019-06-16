---
title: 網路部署功能-Azure App Service |Microsoft Docs
description: 如何使用各種網路功能的應用程式服務
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 666430a11fb95871eb601b2a38eb7b97ad16119f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66498945"
---
# <a name="app-service-networking-features"></a>App Service 網路功能

以多種方式可以部署在 Azure App Service 中的應用程式。 根據預設，App Service 裝載的應用程式會直接透過網際網路存取，並僅能連線到裝載的網際網路端點。 不過，許多客戶應用程式需要控制傳入和傳出網路流量。 有幾項功能可用來滿足這些需求的 App Service 中。 所面臨的挑戰了解哪些功能應該用來解決特定的問題。 本文件旨在協助客戶判斷應該使用哪些功能，根據一些使用案例範例。

有兩種主要的部署類型的 Azure App Service。 多租用戶公用服務，它會裝載 App Service 方案在免費、 共用、 基本、 標準、 進階和 Premiumv2 定價 Sku。 就單一租用戶應用程式服務 ase 裝載隔離的 SKU App Service 方案，直接在 Azure 虛擬網路 (VNet) 中。 如果您是在多租用戶服務，或在 ASE 中使用的功能會有所不同。 

## <a name="multi-tenant-app-service-networking-features"></a>多租用戶 App Service 中的網路功能 

Azure App Service 是一個分散式的系統。 處理傳入的 HTTP/HTTPS 要求的角色稱為 「 前端 」。 裝載客戶工作負載的角色稱為 「 背景工作角色 」。 在 App Service 部署的角色均在多租用戶網路中。 因為相同的 App Service 縮放單位中有許多不同的客戶，所以您無法在 App Service 網路直接連線到您的網路。 而不是將網路連接，我們需要處理的應用程式通訊的不同層面的功能。 處理要求，以您的應用程式的功能，不能解決問題，當從您的應用程式進行呼叫。 同樣地，解決問題的呼叫，從您的應用程式的功能不能解決您的應用程式的問題。  

| 輸入的功能 | 輸出的功能 |
|---------------------|-------------------|
| 應用程式指派的位址 | 混合式連線 |
| 存取限制 | 需要閘道的 VNet 整合 |
| 服務端點 | VNet 整合 （預覽） |

除非另有指明的所有功能可以一起使用。 您可以混用來解決各種問題的功能。

## <a name="use-case-and-features"></a>使用案例和功能

針對任何給定的使用案例，可能會有幾種方式來解決問題。  若要使用的正確功能有時是只使用案例本身以外的原因。 下列的輸入的使用案例會建議如何使用 App Service 網路功能來解決控制流量移至您的應用程式的問題。 
 
| 輸入的使用案例 | 功能 |
|---------------------|-------------------|
| 支援您的應用程式的 IP 型 SSL 需求 | 應用程式指派的位址 |
| 不共用，專用的輸入位址，您的應用程式 | 應用程式指派的位址 |
| 限制對您的應用程式的存取，從一組妥善定義的位址 | 存取限制 |
| 我在我的 VNet 中的私人 Ip 的應用程式公開 （expose) | ILB ASE </br> 透過服務端點的應用程式閘道 |
| 限制對我的應用程式的存取，從 VNet 中的資源 | 服務端點 </br> ILB ASE |
| 我的應用程式，在 我的 VNet 中的私人 ip 位址上公開 （expose) | ILB ASE </br> 輸入應用程式閘道與服務端點上的私人 IP |
| 保護我具有 WAF 的應用程式 | 應用程式閘道 + ILB ASE </br> 透過服務端點的應用程式閘道 </br> Azure 具有存取限制的大門 |
| 我的應用程式在不同區域的流量負載平衡 | Azure 具有存取限制的大門 | 
| 負載平衡流量相同的區域 | 透過服務端點的應用程式閘道 | 

下列輸出的使用案例會建議如何使用網路功能的應用程式服務來解決您的應用程式的輸出存取需求。 

| 輸出的使用案例 | 功能 |
|---------------------|-------------------|
| 在 Azure 虛擬網路位於相同區域中存取資源 | VNet 整合 </br> ASE |
| 在 Azure 虛擬網路位於不同區域中存取資源 | 需要閘道的 VNet 整合 </br> ASE 和 VNet 對等互連 |
| 保護透過服務端點的存取資源 | VNet 整合 </br> ASE |
| 未連接到 Azure 的私人網路中存取資源 | 混合式連線 |
| ExpressRoute 線路之間的存取資源 | （RFC 1918 位址現在限制） 的 VNet 整合 </br> ASE | 


### <a name="default-networking-behavior"></a>預設的網路功能的行為

Azure App Service 縮放單位會在每個部署中支援許多客戶。 免費與共用 SKU 方案裝載在多租用戶背景工作角色上的客戶工作負載。 基本，和計劃裝載客戶工作負載，專門用來只一個 App Service 方案 (ASP) 更新版本。 如果您有標準 App Service 方案時，所有在該計劃中的應用程式會執行相同的背景工作角色上。 如果您相應放大背景工作角色，所有該 ASP 中的應用程式將會複寫每個執行個體在 ASP 中的新背景工作角色上。 背景工作角色所使用的 Premiumv2 是不同於用於其他方案的背景工作角色。 每個 App Service 部署有一個用於所有輸入流量，該 App Service 部署中的應用程式的 IP 位址。 有不過隨處從 4 11 進行輸出呼叫所使用的位址。 這些位址是由所有應用程式中該 App Service 部署共用。 根據不同的背景工作類型的輸出位址會不同。 也就是說，免費、 共用、 基本、 標準和進階 asp 搭配使用的位址會不同於用來從 Premiumv2 Asp 傳出呼叫的位址。 如果您在屬性中查看應用程式，您可以看到您的應用程式所使用的輸入和輸出位址。 如果您需要鎖定 IP ACL 的相依性，使用 possibleOutboundAddresses。 

![應用程式屬性](media/networking-features/app-properties.png)

App Service 有許多用來管理服務的端點。  這些位址會在個別的文件中發佈，另外還有 AppServiceManagement IP 服務標記中。 AppServiceManagement 標記只會使用 App Service Environment (ASE) 您要允許這類流量。 App Service 內的 AppService IP 服務標籤中追蹤輸入的位址。 沒有任何包含 App Service 所使用的輸出位址的 IP 服務標籤。 

![App Service 的輸入和輸出的圖表](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>應用程式指派的位址 

應用程式指派的位址功能為 offshoot 之以 IP 為主的 SSL 功能，並且藉由設定 SSL，您的應用程式存取。 這項功能可用於 IP 型 SSL 的呼叫，但它也可用來讓應用程式只有一個位址。 

![應用程式指派的位址圖表](media/networking-features/app-assigned-address.png)

當您使用的應用程式指派的位址時，還是會經歷相同的前端角色來處理所有的連入流量到 App Service 縮放單位決定您的流量。 不過，指派給您的應用程式的位址僅供您的應用程式。 這項功能的使用案例是：

* 支援您的應用程式的 IP 型 SSL 需求
* 設定專用的位址不會與任何其他項目共用的應用程式

您可以了解如何設定您的應用程式，本教學課程的地址[設定 IP 為主的 SSL][appassignedaddress]。 

### <a name="access-restrictions"></a>存取限制 

存取限制功能可讓您篩選**輸入**起始 IP 位址為基礎的要求。 篩選的動作會在背景工作會從上游的前端角色執行您的應用程式所在的位置。 前端角色是上游從背景工作角色，因為存取限制功能可以視為網路層級保護您的應用程式。 此功能可讓您建置一份允許和拒絕會依照優先順序進行評估的位址區塊。 這就像是存在於 Azure 網路的網路安全性群組 (NSG) 功能。  在 ASE 中，或在多租用戶服務，您可以使用這項功能。 ILB ASE 搭配使用時，您可以限制存取私人的位址區塊。

![存取限制](media/networking-features/access-restrictions.png)

在您想要用來限制可以用來連線到您的應用程式的 IP 位址的情況下，可協助存取限制功能。 使用這項功能的案例包括：

* 限制對您的應用程式的存取，從一組妥善定義的位址 
* 您可以限制存取，即將透過負載平衡服務，例如 Azure 大門。 如果您想要鎖定您的輸入流量至 Azure 大門，建立規則以允許流量從 147.243.0.0/16 和 2a01:111:2050:: / 44。 

![使用前端的存取限制](media/networking-features/access-restrictions-afd.png)

如果您想要鎖定應用程式存取權，使它只能達到您的 Azure 虛擬網路 (VNet) 中的資源，您會需要靜態的公用位址，您的來源會在您的 VNet。 如果資源沒有公用的位址，您應該改為使用服務端點功能。 了解如何上啟用這項功能，本教學課程[設定存取限制][iprestrictions]。

### <a name="service-endpoints"></a>服務端點

服務端點可讓您鎖住**輸入**存取您的應用程式，使來源位址都必須來自一組您選取的子網路。 這項功能可搭配 IP 存取限制。 服務端點會設定 IP 存取限制為相同的使用者體驗。 您可以建立允許/拒絕清單的存取規則，其中包含您的 Vnet 中的公用位址，以及子網路。 這項功能支援的案例，例如：

![服務端點](media/networking-features/service-endpoints.png)

* 設定應用程式閘道與您的應用程式，以鎖定您的應用程式的輸入流量
* 限制存取您的應用程式，您的 VNet 中的資源。 這可能包括 Vm、 Ase 或甚至是其他使用 VNet 整合的應用程式 

![與應用程式閘道的服務端點](media/networking-features/service-endpoints-appgw.png)

您可以進一步了解設定服務端點，與您的應用程式教學課程中上[設定服務端點的存取限制][serviceendpoints]
 
### <a name="hybrid-connections"></a>混合式連線

App Service 混合式連線可讓您的應用程式，讓**輸出**指定的 TCP 端點的呼叫。 端點可以在內部 VNet 中或任何位置，可讓 Azure 連接埠 443 的輸出流量。 此功能需要稱為 Windows Server 2012 或更新版本的主機上的混合式連線管理員 (HCM) 的轉送代理程式安裝。 HCM 必須能夠連線到 Azure 轉送連接埠 443。 從 App Service 混合式連線 UI，在入口網站中，您可以下載 HCM。 

![混合式連線網路流程](media/networking-features/hybrid-connections.png)

App Service 混合式連線功能為基礎的 Azure 轉送混合式連線功能。 App Service 會使用僅支援進行輸出呼叫您的應用程式從 TCP 主機和連接埠功能的一種特殊的形式。 此主機和連接埠只需要解決安裝 HCM 在主機上。 當應用程式，App Service 中的進行混合式連線中所定義的連接埠與主機的 DNS 查閱時，流量會自動重新導向前往透過混合式連線並解混合式連線管理員。 若要深入了解混合式連線，請閱讀文件上[App Service 混合式連線][hybridconn]

這項功能通常用於：

* 在未連線到 Azure 中使用 VPN 或 ExpressRoute 的私人網路中存取資源
* 支援原形的內部部署應用程式至 App Service，而不需要也必須將支援的資料庫  
* 安全地提供存取權的單一主機和每個混合式連接的連接埠。 大部分的網路功能開啟網路的存取權，並使用混合式連線您只有單一主機和連接埠可以連線到。
* 涵蓋其他輸出連線能力的方法未涵蓋的案例
* 其中的應用程式可以輕鬆地運用在內部部署資源的 App Service 中執行開發 

功能可讓您存取內部資源，而不需要輸入的防火牆洞，因為它是常用的開發人員。 其他輸出 App Service 網路功能是非常 Azure 虛擬網路相關項目。 混合式連線並沒有在透過 VNet 的相依性，而且可用於更廣泛的網路需求。 請務必請注意，App Service 混合式連線功能不會不小心或知道您會在其上執行的動作。 這是說，您可以使用它來存取資料庫、 web 服務或任意的 TCP 通訊端，大型主機上。 此功能基本上是透過通道傳送 TCP 封包。 

最受歡迎的開發混合式連線時，它也會在許多實際執行應用程式以及。 它適合用來存取 web 服務或資料庫，但不是適當的情況下建立多很棒的連線。 

### <a name="gateway-required-vnet-integration"></a>需要閘道的 VNet 整合 

閘道所需的應用程式服務 VNet 整合功能可讓您的應用程式，讓**輸出**要求至 Azure 虛擬網路。 此功能的運作方式是連接您的應用程式，會以點對站 VPN 與 vnet 的虛擬網路閘道執行所在的主機。 當您設定此功能時，您的應用程式取得指派給每個執行個體的點對站位址的其中一個。 這項功能可讓您存取在傳統或 Resource Manager Vnet，在任何區域中的資源。 

![需要閘道的 VNet 整合](media/networking-features/gw-vnet-integration.png)

此功能可解決存取其他 Vnet 中的資源的問題，甚至可以用來透過至其他 Vnet 或甚至是內部 VNet 連線。 不適用於 ExpressRoute 連線的 Vnet，但會使用站對站 VPN 連線的網路。 這是通常不適合使用這項功能，從應用程式在 App Service 環境 (ASE)，，因為 ASE 已在您的 VNet。 這項功能可解決使用案例如下：

* 存取您的 Azure 虛擬網路中的私人 Ip 的資源 
* 存取資源內部是否有站對站 VPN 
* 存取對等互連 Vnet 中的資源 

啟用這項功能時，您的應用程式會使用目的地 VNet 已使用的 DNS 伺服器。 您可以深入了這項功能的相關文件上[應用程式服務 VNet 整合][vnetintegrationp2s]。 

### <a name="vnet-integration"></a>VNet 整合

VNet 整合功能非常有用，但仍無法解決存取資源透過 ExpressRoute，就會需要閘道。 之上需要連線到透過 ExpressRoute 連線，就能夠保護服務的服務端點發出呼叫的應用程式的需要。 若要解決這兩個這些額外的需求，已加入另一個 VNet 整合功能。 新的 VNet 整合功能可讓您在 Resource Manager VNet 位於相同區域中的子網路放置應用程式後的端。 無法從 App Service 環境，也就是已在 VNet 中使用這項功能。 這個功能可讓您：

* 存取相同的區域中的 Resource Manager Vnet 中的資源
* 存取服務端點以進行保護的資源 
* 存取是透過 ExpressRoute 或 VPN 連線的可存取的資源

![VNet 整合](media/networking-features/vnet-integration.png)

這項功能處於預覽狀態，並不應該用於生產工作負載。 若要深入了解這項功能，請閱讀文件上[應用程式服務 VNet 整合][vnetintegration]。

## <a name="app-service-environment"></a>App Service 環境 

App Service Environment (ASE) 是單一租用戶部署 Azure App Service，在您的 VNet 中執行。 ASE 可讓使用案例，例如：

* 存取您的 VNet 中的資源
* 透過 ExpressRoute 存取資源
* 公開 （expose) 您使用私人位址在 VNet 中的應用程式 
* 服務端點之間的存取資源 

使用 ASE 時，您不需要使用 VNet 整合或服務端點等功能，因為 ASE 已在您的 VNet。 如果您想要存取資源，例如 SQL 或儲存體透過服務端點，可讓 ASE 子網路上的服務端點。 如果您想要存取 VNet 中的資源，則不需要其他設定。  如果您想要透過 ExpressRoute 存取的資源，您已在 VNet，並不需要進行任何設定 ASE 或其內的應用程式。 

由於 ILB ASE 中的應用程式可以公開私用的 IP 位址上，您可以輕鬆地新增 WAF 裝置，以公開 （expose） 只是應用程式，您想要網際網路，並保留其餘的安全。 它本身做即可輕鬆開發多層式應用程式。 

有幾件事，尚無法從多租用戶服務可能來自 ASE。 其中包括像是：

* 公開 （expose) 的私人 IP 位址上的應用程式
* 安全與不屬於您的應用程式的網路控制的所有輸出流量 
* 裝載在單一租用戶的服務中的應用程式 
* 相應增加為許多更多執行個體中的多租用戶的服務可能會比 
* 您的應用程式載入私人 CA 用戶端憑證以用於保護端點的私人 ca 
* 強制所有在系統中，而不需要任何能夠在應用程式層級停用託管的應用程式的 TLS 1.1 
* 所有的未與任何客戶共用您 ASE 中應用程式提供專用的輸出位址 

![ASE 的 VNet 中](media/networking-features/app-service-environment.png)

ASE 提供隔離且專用的應用程式在裝載其最佳意涵，但會帶來一些管理方面的挑戰。 操作 ASE 之前需要考量一些事項如下：
 
 * ASE VNet 內執行，但沒有 VNet 外部的相依性。 必須允許這些相依性。 深入了解[App Service Environment 的網路功能考量][networkinfo]
 * ASE 無法立即調整多租用戶的服務一樣。 您需要能夠調整需求，而不是被動的縮放比例。 
 * ASE 有較高提前與它相關聯的成本。 為了要充分利用您的 ASE，您應該將許多工作負載放入一個 ASE 計劃而非將它用於小型工作
 * 在 ASE 中的應用程式無法在 ASE 並不會針對其他一些應用程式限制存取。
 * ASE 所在的子網路和網路的任何規則套用到與該 ASE 的所有流量。 如果您想要指派一個應用程式的輸入的流量規則，請使用 存取限制。 

## <a name="combining-features"></a>合併功能 

記下的多租用戶服務的功能可以一起使用，來解決更複雜使用案例。 兩個較常見的使用案例如下所示，但它們只是範例。 了解的各種功能的作用，您可以解決幾乎所有的系統架構需求。

### <a name="inject-app-into-a-vnet"></a>插入 VNet 中的應用程式

如何將您的應用程式放在 VNet 中，是常見的要求。 將您的應用程式放到 VNet，表示應用程式的輸入和輸出端點是在 VNet 中。 ASE 提供最佳的解決方案來解決這個問題，但您可以取得大部分的所需使用多租用戶服務中藉由結合的功能。 例如，您也可以裝載內部網路的私用的輸入和輸出位址的唯一應用程式：

* 使用私用的輸入和輸出位址建立應用程式閘道
* 保護您的應用程式與服務端點的流量 
* 使用新的 VNet 整合，因此您的應用程式的後端是在您的 VNet 

此部署樣式不會提供到網際網路的輸出流量的專用地址或讓您能夠鎖定從您的應用程式的所有輸出流量。  此部署樣式可讓您的項目則會只得到與 ASE 搭配比。 

### <a name="create-multi-tier-applications"></a>建立多層式應用程式

多層式應用程式是應用程式，其中 API 後端應用程式只能存取從前端層。 若要建立多層式應用程式，您可以：

* 使用 VNet 整合連線與 VNet 中子網路的前端 web 應用程式的後端
* 使用服務端點來保護您的 API 應用程式的輸入的流量至只來自前端 web 應用程式所使用的子網路

![多層式應用程式](media/networking-features/multi-tier-app.png)

您可以使用相同的 API 應用程式之其他前端應用程式及 API 應用程式與其子網路服務端點使用 VNet 整合的多個前端應用程式。  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl#bind-your-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
