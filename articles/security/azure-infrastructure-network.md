---
title: Azure 網路架構
description: 本文提供 Microsoft Azure 基礎結構網路的一般描述。
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 67781f196b445c9330e0dcb1fc7d8b0a1a53cbc0
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101303"
---
# <a name="azure-network-architecture"></a>Azure 網路架構
Azure 網路架構會遵循改良版業界標準核心/散發/存取模型 (具有不同的硬體層級)。 這些層級包括：

- 核心 (資料中心路由器)
- 散發 (存取路由器和 L2 彙總) - 散發層可分隔 L3 路由與 L2 切換
- 存取 (L2 主機交換器)

此網路架構有兩個層級的第 2 層交換器，這一層可彙總來自另一層的流量和第 2 層迴圈，以納入備援。 這可提供一些優點，例如更有彈性的 VLAN 使用量，以及改善連接埠調整。 此架構讓 L2 與 L3 有所區別，允許在網路的各個不同層中使用硬體，以及將一層中的錯誤降至最低，以免影響其他層。 使用主幹容許進行資源共用，例如 L3 基礎結構的連線。

## <a name="network-configuration"></a>網路組態
資料中心內 Microsoft Azure 叢集的網路架構包含下列裝置：

- 路由器 (資料中心、存取路由器和邊界分葉路由器)
- 交換器 (彙總與機櫃頂端交換器)
- Digi CM
- PDU 或 Nucleon

下圖提供叢集內 Azure 網路架構的高階概觀。 Azure 有兩個不同的架構。 某些現有的 Azure 客戶與共用服務位於預設 LAN 架構 (DLA)，而新的區域和虛擬客戶將透過 Quantum 10 (Q10) 架構存取。 DLA 架構是傳統樹狀結構設計，具有作用中的被動存取路由器以及套用至存取路由器的安全性 ACL。 Quantum 10 架構是路由器的 Clos/網格設計，其中的 ACL 並不會在路由器套用，但是經由軟體負載平衡 (SLB) 或軟體定義的 VLAN 路由傳送。

![Azure 網路][1]

### <a name="quantum-10-devices"></a>Quantum 10 裝置
Quantum 10 設計會進行遍佈於 CLOS/網格設計中多個裝置的第 3 層交換。 Q10 設計的優點包括容量更大，以及更有能力調整現有的網路基礎結構。 此設計運用邊界分葉路由器、主幹式交換器和機櫃頂端交換器，透過允許容錯的多個路由將流量傳遞至叢集。 安全性服務 (例如網路位址轉譯) 是透過軟體負載平衡進行處理，而不是在硬體裝置上處理。

### <a name="access-routers"></a>存取路由器
散發/存取 L3 路由器 (AR) 可執行散發和存取層的主要路由功能。 這些裝置會成對部署，而且是子網路的預設閘道。 視容量而定，每個 AR 配對都可以支援多個 L2 彙總交換器配對。 最大數目取決於裝置的容量，以及失敗網域。 以預計容量為基礎的典型數字就是每個 AR 配對有三個 L2 彙總交換器配對。

### <a name="l2-aggregation-switches"></a>L2 彙總交換器  
這些裝置可當作 L2 流量的彙總點。 它們是 L2 網狀架構的散發層，可以處理大量流量。 因為這些裝置彙總流量，所以需要 802.1q 功能，而且會使用連接埠彙總和 10GE 等高頻寬技術。

### <a name="l2-host-switches"></a>L2 主機交換器
主機會直接連線到這些交換器。 它們可以是固定在機櫃上的交換器，或部署於底座。 802.1q 標準容許將一個 VLAN 指定為原生 VLAN，並將該 VLAN 視為正常 (未標記) 乙太網路框架。 在正常情況下，原生 VLAN 上的訊框會在 802.1q 主幹連接埠上傳輸或接收 (未標記)。 此功能專為移轉到 802.1q 並與非 802.1q 裝置相容而設計。 在此架構中，只有網路基礎結構會利用原生 VLAN。

此架構會指定原生 VLAN 選取標準，以確保 AR 裝置對每個主幹和 L2Aggregation 至 L2Aggregation 主幹都有唯一的原生 VLAN。 L2Aggregation 至 L2Host 交換器主幹具有非預設的原生 VLAN。

### <a name="link-aggregation-8023ad"></a>連結彙總 (802.3ad)
連結彙總 (LAG) 可讓多個個別的連結組合在一起，並可視為單一邏輯連結。 用來指定連接埠-通道介面的號碼必須標準化，才可讓作業偵錯變得更容易，而網路的其餘部分會在連接埠-通道的兩端使用相同的號碼。 這需有標準，才能判斷連接埠-通道的哪一端要用來定義下一個可用的號碼。

針對 L2Agg 至 L2Host 交換器指定的號碼，就是在 L2Agg 端使用的連接埠-通道號碼。 因為 L2Host 端的號碼範圍更加受限，所以標準是在 L2Host 端使用號碼 1 和 2，分別指朝向 "a" 和 "b" 端的連接埠-通道。

### <a name="vlans"></a>VLAN
網路架構會使用 VLAN 將多個伺服器組成單一廣播網域。 VLAN 號碼符合 802.1q 標準，可支援 VLAN 號碼 1 – 4094。

### <a name="customer-vlans"></a>客戶 VLAN
客戶有各種可透過 Azure 入口網站部署的 VLAN 實作選項，以符合其解決方案的隔離和架構需求。 這些解決方案會透過虛擬機器部署。 如需客戶參考架構範例，請瀏覽 [Azure 參考架構](https://docs.microsoft.com/azure/architecture/reference-architectures/)。

### <a name="edge-architecture"></a>邊緣架構
Azure 資料中心是以高度備援且妥善佈建的網路基礎結構為基礎。 Azure 資料中心內的網路會經由「需求加一」(N + 1) 備援性架構或更佳的架構來實作。 資料中心之內和之間的完整容錯移轉功能，有助於確保網路和服務可用性。 資料中心在外部是由專用的高頻寬網路線路提供支援，這些線路會在多個對等互連點上透過全球 1200 個網際網路服務提供者 (可透過網路提供超過 2,000 Gbps 的潛在邊緣容量) 重複連接資產。

在 Microsoft Azure 網路的邊緣和存取層篩選路由器，可在封包層級提供完善的安全性，以防止未經授權嘗試連線到 Azure。 這些路由器有助於確保封包的實際內容包含預期格式的資料，而且符合預期的用戶端/伺服器通訊配置。 Azure 會實作由下列網路隔離和存取控制元件所組成的階層式架構：

- 邊緣路由器 - 區隔應用程式環境與網際網路。 邊緣路由器的設計訴求是提供反詐騙保護，並使用存取控制清單 (Acl) 來限制存取權。
- 散發 (存取) 路由器 - 存取路由器的設計訴求是只允許 Microsoft 核准的 IP 位址，以提供防詐騙功能，以及使用 ACL 建立的連線。

### <a name="a10-ddos-mitigation-architecture"></a>A10 DDOS 風險降低架構
阻斷服務攻擊會繼續對 Microsoft 線上服務的可靠性帶來實質威脅。 隨著攻擊變得更具針對性、更複雜，以及 Microsoft 的服務遍及各地時，首要之務就是提供有效的機制來識別及降低這些攻擊的影響。

下列詳細資料從網路架構的觀點說明如何實作 A10 DDOS 風險降低系統。  
Microsoft Azure 會在 DCR (資料中心路由器) 使用 A10 網路裝置，以提供自動化偵測和風險降低。 A10 解決方案會利用 Azure 網路監視，進行 Netflow 封包取樣以及判斷是否有任何攻擊。 一旦偵測到攻擊，就會將 A10 裝置當作清除程式來降低攻擊的風險。 降低風險之後，便允許乾淨的流量直接從 DCR 進入 Azure 資料中心。 A10 解決方案用於保護 Azure 網路基礎結構。

A10 解決方案中的 DDoS 保護包括：

- UDP IPv4 和 IPv6 洪水保護
- ICMP IPv4 和 IPv6 洪水保護
- TCP IPv4 和 IPv6 洪水保護
- IPv4 和 IPv6 的 TCP SYN 攻擊保護
- 片段攻擊

> [!NOTE]
> 依預設，會為所有的 Azure 客戶提供 DDoS 保護。
>
>

## <a name="network-connection-rules"></a>網路連線規則
Azure 會在其網路上部署可在封包層級提供安全性的邊緣路由器，以防止未經授權嘗試連線到 Microsoft Azure。 這些路由器可確保封包的實際內容包含預期格式的資料，而且符合預期的用戶端/伺服器通訊配置。

邊緣路由器可區隔應用程式環境與網際網路。 邊緣路由器的設計訴求是提供反詐騙保護，並使用存取控制清單 (Acl) 來限制存取權。 這些邊緣路由器乃使用階層式 ACL 方法進行設定，以限制能夠傳輸邊緣路由器及存取路由器的網路通訊協定。

網路裝置位於存取和邊緣位置，並當作套用輸入和/或輸出篩選條件的邊界點。

## <a name="next-steps"></a>後續步驟
若要深入了解 Microsoft 為保護 Azure 基礎結構執行了哪些動作，請參閱：

- [Azure 設施、廠房以及實體安全性](azure-physical-security.md)
- [Azure 基礎結構可用性](azure-infrastructure-availability.md)
- [Azure 資訊系統元件與界限](azure-infrastructure-components.md)
- [Azure 生產網路](azure-production-network.md)
- [Microsoft Azure SQL Database 安全性功能](azure-infrastructure-sql.md)
- [Azure 生產環境運作與管理](azure-infrastructure-operations.md)
- [監視 Azure 基礎結構](azure-infrastructure-monitoring.md)
- [Azure 基礎結構的完整性](azure-infrastructure-integrity.md)
- [在 Azure 中保護客戶資料](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-network/network-arch.png
