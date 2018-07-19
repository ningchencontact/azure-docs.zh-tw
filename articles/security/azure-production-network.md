---
title: Azure 生產網路
description: 本文提供 Microsoft Azure 生產網路的一般描述。
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
ms.openlocfilehash: 5c0bfae35464e73278a1efd9c04a03123bb9018a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101348"
---
# <a name="azure-production-network"></a>Azure 生產網路
Azure 生產網路的使用者包括存取自有 Microsoft Azure 應用程式的外部客戶，以及管理生產環境網路的內部 Microsoft Azure 支援人員。 本文討論用於建立 Azure 生產網路連線的安全性存取方法和保護機制。

## <a name="internet-routing-and-fault-tolerance"></a>網際網路 路由和容錯
全域備援內部和外部 Microsoft Azure 網域名稱服務 (WADNS) 基礎結構以及多個主要和次要網域名稱服務 (DNS) 伺服器叢集均提供容錯功能，而其他 Microsoft Azure 網路安全性控制器 (例如 NetScaler) 則用來防止分散式阻斷服務 (DDoS) 攻擊並保護 Microsoft Azure DNS 服務的完整性。

WADNS 伺服器位於多個資料中心設施。 WADNS 實作結合併次要/主要 DNS 伺服器階層，可公開解析 Azure 客戶的網域名稱。 網域名稱通常會解析成 CloudApp.net 位址，其中包裝客戶服務的虛擬 IP (VIP) 位址。 租用戶轉譯的 VIP 與內部專用 IP (DIP) 位址對應是由負責該 VIP 的 Microsoft 負載平衡器進行，這是 Azure 特有的現象。

Azure 裝載於美國境內分散各地的 Azure 資料中心，而且建置於實作強大且可擴充架構標準的最新路由平台上。 一些值得注意的功能包括：

- 如果發生中斷情形，以重通訊協定標籤交換 (MPLS) 為基礎的流量工程會提供有效的連結利用和優雅的服務降級
- 網路會經由「需求加一」(N + 1) 備援性架構或更佳的架構來實作。
- 資料中心在外部是由專用的高頻寬網路線路提供支援，這些線路會在多個對等互連點上透過全球 12,00 個網際網路服務提供者 (可提供每秒超過 2,000 GB (Gbps) 的邊緣容量) 重複連接資產。

因為 Microsoft 資料中心之間擁有自己的網路線路，這些特性可協助 Azure 供應項目達成 99.9+% 網路可用性，而不需要傳統的第三方網際網路服務提供者。

## <a name="connection-to-production-network-and-associated-firewalls"></a>連線至生產網路及相關聯的防火牆
Azure 網路網際網路流量流程原則會將流量導向位於美國境內最近區域資料中心的 Azure 生產網路。 Azure 生產資料中心會維護一致的網路架構和硬體，因此以下的流量流程說明一致適用於所有的資料中心。

將 Azure 的網際網路流量路由傳送至最接近的資料中心之後，就會建立存取路由器的連線。 這些存取路由器用於隔離 Azure 節點與客戶具現化 VM 之間的流量。 位於存取與邊緣位置的網路基礎結構裝置是套用輸入和/或輸出篩選條件的邊界點。 這些路由器是透過階層式 ACL 來設定，以篩選不必要的網路流量並套用資料傳輸速率限制 (如有必要的話)。 ACL 所允許的流量會路由傳送至負載平衡器。 散發路由器的設計訴求是只允許 Microsoft 核准的 IP 位址，以提供防詐騙功能，以及使用 ACL 建立的 TCP 連線。

外部負載平衡裝置位於存取路由器後方，可執行從網際網路可路由傳送的 IP 到 Azure 內部 IP 的網路位址轉譯 (NAT)。 它們也會將封包路由傳送至有效的生產內部 IP 和連接埠，並且當作保護機制來限制公開內部生產網路位址空間。

根據預設，Microsoft 會對傳輸到客戶網頁瀏覽器的所有流量 (包括登入以及之後的所有流量)，強制使用超文字安全傳輸通訊協定 (HTTPS)。 使用 TLS v1.2 可讓流量流經安全的通道。 存取和核心路由器上的 ACL 可確保流量的來源如同預期。

相較於傳統的安全性架構，此架構的重要區別就是在連線至 Azure 生產環境之前，通常不會期待有專用的硬體防火牆、特製化入侵偵測/預防裝置，或其他安全性設備。 客戶通常預期 Azure 網路中會有這些硬體防火牆裝置。不過，Azure 中並未運用上述任何裝置。 這些安全性功能幾乎都內建於執行 Azure 環境的軟體中，以便提供強大的多層式安全性機制，包括防火牆功能。 此外，重要安全性裝置的界限範圍和相關聯的擴充更易於管理及清查，因為它是由執行 Azure 的軟體所管理。

## <a name="core-security-and-firewall-features"></a>核心安全性和防火牆功能
Azure 會在各種層級實作強固的軟體安全性和防火牆功能，以強制執行傳統環境中通常會有的安全性功能，進而保護核心安全性授權界限。

### <a name="azure-security-features"></a>Azure 安全性功能
Azure 會在生產網路內實作以主機為基礎的軟體防火牆。 核心 Azure 環境內有數個核心安全性和防火牆功能。 這些安全性功能反映出 Azure 環境中的深度防禦策略。 Microsoft Azure 中的客戶資料受到下列防火牆保護：

**Hypervisor 防火牆 (封包篩選器)**：此防火牆會在 Hypervisor 中實作，並由 FC 代理程式設定。 此防火牆會保護在 VM 內執行的租用戶，以免遭到未經授權的存取。 根據預設，VM 建立時會封鎖所有流量，然後 FC 代理程式會在篩選器中新增規則/例外狀況以允許經過授權的流量。

在此介紹兩種規則：

- 機器組態或基礎結構規則：依預設會封鎖所有通訊。 有一些例外狀況可允許 VM 傳送和接收動態主機設定通訊協定 (DHCP) 通訊、DNS 資訊，將流量傳送至「公用」網際網路，輸出到 FC 叢集內的其他 VM 和 OS 啟用伺服器。 VM 的連出目的地允許清單不包含 Microsoft Azure 路由器子網路和其他 Microsoft 屬性，因此這些規則當作其防禦層。
- 角色組態檔：根據租用戶的服務模型定義輸入 ACL。 例如，如果租用戶在特定 VM 的連接埠 80 上有 Web 前端，則會對所有 IP 位址開放連接埠 80。 如果 VM 有執行中的背景工作角色，則只會對相同租用戶內的 VM 開放此背景工作角色。

**原生主機防火牆**：Microsoft Azure 網狀架構和儲存體會在沒有 Hypervisor 的原生 OS 上執行，因此會使用上述兩組規則來設定 Windows 防火牆。

**主機防火牆**：主機防火牆可保護執行 Hypervisor 的主機分割區。 規則設計成僅允許 FC，並以跳躍方式在特定連接埠上與主機分割區通訊。 其他例外狀況包括允許 DHCP 回應與 DNS 回覆。 Azure 會使用機器組態檔，該檔案具有主機分割區的防火牆規則範本。 另外還有一些主機防火牆例外狀況，允許 VM 透過特定通訊協定/連接埠來與主機元件、線路伺服器和中繼資料伺服器通訊。

**客體防火牆**：客體作業系統的 Windows 防火牆片段，可由客戶在客戶 VM 和儲存體上進行設定。

Azure 功能的其他內建安全性功能：

- 基礎結構元件會獲派 IP 位址 (來自專用的 IP (DIP))。 網際網路上的攻擊者無法處理這些位址的流量，因為他們無法觸達 Microsoft。 網際網路閘道路由器會篩選單獨送至內部位址的封包，因此不會進入生產網路。 負載平衡器是唯一接受 VIP 導向流量的元件。
- 對於任何指定的案例，在所有內部節點上實作的防火牆都有三個主要安全性架構考量：

   - 它們位於 Load Balancer (LB) 後方並接受來自任何地方的封包。 這些預定要對外公開，而且會對應至傳統周邊防火牆中開啟的連接埠。
   - 只接受來自一組有限位址的封包。 這是對抗阻斷服務攻擊的深度防禦策略的一部分。 這類連線會以密碼編譯方式進行驗證。
   - 只能從精選的內部節點存取防火牆，在此情況下，防火牆只會接受來源 IP 位址列舉清單中的封包，而這些全都是 Azure 網路內的 DIP。 例如，公司網路上的攻擊者可以將要求導向這些位址，但除非封包的來源位址已在 Azure 網路內的列舉清單中，否則會封鎖這些要求。
   - 周邊的存取路由器會因為其已設定的靜態路由，封鎖送至 Azure 網路內部位址的輸出封包。

## <a name="next-steps"></a>後續步驟
若要深入了解 Microsoft 為保護 Azure 基礎結構執行了哪些動作，請參閱：

- [Azure 設施、廠房以及實體安全性](azure-physical-security.md)
- [Azure 基礎結構可用性](azure-infrastructure-availability.md)
- [Azure 資訊系統元件與界限](azure-infrastructure-components.md)
- [Azure 網路架構](azure-infrastructure-network.md)
- [Microsoft Azure SQL Database 安全性功能](azure-infrastructure-sql.md)
- [Azure 生產環境運作與管理](azure-infrastructure-operations.md)
- [監視 Azure 基礎結構](azure-infrastructure-monitoring.md)
- [Azure 基礎結構的完整性](azure-infrastructure-integrity.md)
- [在 Azure 中保護客戶資料](azure-protection-of-customer-data.md)
