---
title: Azure DDoS 保護的最佳做法與參考架構 | Microsoft Docs
description: 了解如何使用記錄資料來取得應用程式的深入解析。
services: security
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2018
ms.author: barclayn
ms.openlocfilehash: 4b2d785f5b9095a2decfc65ec46808ff6f65c38e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2018
---
# <a name="azure-ddos-protection-best-practices-and-reference-architecture"></a>Azure DDoS 保護：最佳做法與參考架構

本文的目標對象為 IT 決策者和安全性人員。 預期他們已熟悉 Azure、網路及安全性。

設計分散式阻斷服務 (DDoS) 復原需要規劃與設計各種不同的失敗模式。 本文件所提供的最佳做法適用於在 Azure 中設計應用程式，使其在發生 DDoS 攻擊具備恢復功能。

## <a name="types-of-attacks"></a>攻擊類型

DDoS 是一種用於努力耗盡應用程式資源的攻擊類型。 目標是影響應用程式的可用性，以及處理合法要求的能力。 攻擊會變得越來越複雜，而且大小和影響越來越大。 DDoS 攻擊可以鎖定可透過網際網路公開觸達的任何端點。

Azure 提供持續保護來抵禦 DDoS 攻擊。 此保護預設會整合到 Azure 平台，不需任何額外成本。 除了平台提供的核心 DDoS 保護，我們也提供名為「[Azure DDoS 保護標準](https://azure.microsoft.com/services/ddos-protection/)」的新供應項目，它提供進階的 DDoS 防護功能來抵禦網路攻擊，而且會自動調整以保護您特有的 Azure 資源。 只需在建立新的虛擬網路時啟用保護即可。 您也可以在初始建立之後完成保護，而不需進行任何應用程式或資源變更。

![](media/azure-ddos-best-practices/image1.png)

DDoS 攻擊大致上可分為三 (3) 個不同類別

### <a name="volumetric-attacks"></a>體積型攻擊

體積型攻擊是最常見的 DDoS 攻擊類型。 體積型攻擊是目標為網路與傳輸層的暴力密碼破解攻擊。 它們會嘗試將諸如網路連結的資源耗盡。 這些攻擊通常會使用多個受感染的系統，以大量看似合法的流量填滿網路層。 在此類型的攻擊中，會使用不同的網路層通訊協定，例如，網際網路控制訊息通訊協定 (ICMP)、使用者資料包通訊協定 (UDP) 和傳輸控制通訊協定 (TCP)。

最常使用的網路層 DDoS 攻擊是 TCP SYN 洪水攻擊、ICMP 回應、UDP 洪水攻擊、DNS 及 NTP 放大攻擊。 此類型的攻擊不只可用來中斷服務，也可用來作為更惡毒且具針對性之網路入侵的煙幕。 最近的體積型攻擊範例是影響到 GitHub 的 [Memcached 惡意探索](https://www.wired.com/story/github-ddos-memcached/) \(英文\)。 此攻擊的目標為 UDP 連接埠 11211，並產生 1.35 Tb/秒的攻擊量。

### <a name="protocol-attacks"></a>通訊協定攻擊

通訊協定攻擊的目標為應用程式通訊協定。 它們嘗試使用基礎結構裝置中的所有可用資源，例如防火牆、應用程式伺服器和負載平衡器。 通訊協定攻擊會使用格式不正確或包含通訊協定異常狀況的封包。 這些攻擊的運作方式是傳送大量開啟要求，伺服器和其他通訊裝置會回應並等候封包回應。 目標會嘗試回應開啟要求，最終導致目標系統毀損。

通訊協定型 DDoS 攻擊的最常見範例是 TCP SYN 洪水攻擊。 在此攻擊中，會將連續的 TCP SYN 要求導向至目標，並可用來使其癱瘓。 目標則是讓目標沒有回應。 除了作為應用程式層的攻擊，2016 年的 Dyn 中斷也被視為目標為 Dyn DNS 伺服器連接埠 53 的 TCP SYN 洪水攻擊。

### <a name="resource-attacks"></a>資源攻擊

資源攻擊的目標為應用程式層。 它們會盡力觸發後端程序來癱瘓目標系統。 資源攻擊會濫用看似正常但會為伺服器帶來需要大量 CPU 查詢的流量。 耗盡資源所需的流量相對低於其他類型的攻擊。 您很難分辨資源攻擊中的流量與合法流量，因此很難偵測到它。 最常見的資源攻擊位於 HTTP/HTTPS 和 DNS 服務上。

## <a name="shared-responsibility-in-the-cloud"></a>雲端中共同承擔的責任

需要深入防禦策略，以抵禦日益增加的攻擊類型和複雜度。 安全性是客戶和 Microsoft 之間應共同承擔的責任。 Microsoft 將其稱為[共同承擔的責任模型](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/) \(英文\)。 下圖顯示責任分工。

![](media/azure-ddos-best-practices/image2.png)

Azure 客戶可藉由檢閱我們的最佳做法，以及全域建置分散式應用程式並針對失敗進行設計及測試，從中受益。

## <a name="fundamental-best-practices"></a>基本的最佳做法

DDoS 攻擊與日俱增。 下一節提供在 Azure 上建置 DDoS 復原服務的規範性指引。

### <a name="design-for-security"></a>安全性設計

客戶應該確保安全性在應用程式的整個生命週期 (從設計和實作到部署與作業) 中具有優先順序。 應用程式可能存在一些錯誤 (bug)，可允許數量相對較低的建構要求過度使用大量資源，因而導致服務中斷。 若要保護在 Microsoft Azure 上執行的服務，客戶應該對其應用程式架構有良好的了解，而且必須專注於[軟體品質的 5 大要素](https://docs.microsoft.com/azure/architecture/guide/pillars)。
客戶應該了解一般流量、應用程式與其他應用程式之間的連線模型，以及公開至公用網際網路的服務端點。

此外，確保應用程式有足夠的彈性，能夠處理以應用程式本身為目標的阻斷服務，至關重要。 從[安全性開發週期 (SDL)](https://www.microsoft.com/sdl/default.aspx) 開始，通常會將安全性和隱私權內建於 Azure 平台。 SDL 會在每個開發階段處理安全性，並確保 Azure 會持續更新以使其更為安全。

### <a name="design-for-scalability"></a>延展性設計

延展性是系統處理負載增加的能力。 客戶必須將應用程式設計為可[水平調整](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out)以滿足放大負載的需求，特別是在 DDoS 攻擊的事件中。 如果您的應用程式相依於服務的單一執行個體，它會建立單一失敗點。 佈建多個執行個體，可改善復原能力和延展性。

對於 [Azure App Service](../app-service/app-service-value-prop-what-is.md)，選取可提供多個執行個體的 [App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 對於 Azure 雲端服務，設定您的每個角色以使用[多個執行個體](../cloud-services/cloud-services-choose-me.md)。 對於 [Azure 虛擬機器 (VM)](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，確保 VM 架構包含多個 VM，而且每個 VM 都包含於[可用性設定組](../virtual-machines/virtual-machines-windows-manage-availability.md)中。 建議您使用[虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)，自動調整功能。

### <a name="defense-in-depth"></a>深層防禦

深層防禦背後的概念是使用各種不同的防禦策略來管理風險。 將應用程式中的安全性防禦分層，可降低成功攻擊的機會。 我們建議客戶透過 Azure 平台的內建功能，為其應用程式實作安全設計。

例如，攻擊的風險會隨著應用程式大小或介面區而提高。 建議設定白名單來關閉公開的 IP 位址空間以及負載平衡器 ([ALB](../load-balancer/load-balancer-get-started-internet-portal.md)/[應用程式閘道](../application-gateway/application-gateway-create-probe-portal.md)) 上不需要的接聽連接埠，或透過[網路安全性群組 (NSG)](../virtual-network/virtual-networks-nsg.md)，藉以縮減介面區。
您可以使用[服務標記](/virtual-network/security-overview.md)和[應用程式安全性群組](/virtual-network/security-overview.md)，將建立安全性規則的複雜性降至最低，並將網路安全性設定為應用程式結構的自然延伸。

客戶應該盡可能將 Azure 服務部署於[虛擬網路](../virtual-network/virtual-networks-overview.md)上。 這可讓服務資源透過私人 IP 位址進行通訊。 根據預設，來自虛擬網路的 Azure 服務流量會使用公用 IP 位址作為來源 IP 位址。 使用[服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)，會在從虛擬網路存取 Azure 服務時，將服務流量切換為使用虛擬網路私人位址作為來源 IP 位址。

我們通常會看到客戶的內部部署資源在 Azure 中的資源旁遭受攻擊。 如果您將內部部署環境連線到 Azure，建議您盡量少讓內部部署資源暴露於公用網際網路上。 您可以藉由在 Azure 中部署已知的公用實體，來使用縮放和進階的 DDoS 保護功能。 由於這些可公開存取的實體通常是 DDoS 攻擊的目標，因此，將它們放置於 Azure，可降低對您內部部署資源的影響。

## <a name="azure-ddos-protection"></a>Azure DDoS 保護

Azure 提供兩個 DDoS 服務供應項目，來保護您免於受到網路攻擊 (第 3 層和第4 層)：DDoS 基本保護與 DDoS 標準保護。 

### <a name="azure-ddos-basic-protection"></a>Azure DDoS 基本保護

基本保護預設會整合到 Azure，不需任何額外成本。 Azure 全球部署網路的完整規模與功能可提供防禦，透過一律啟動的流量監視和即時防護來抵禦常見的網路層攻擊。 DDoS 基本保護不需要進行任何使用者設定或應用程式變更。 所有的 Azure 服務 (包括像是 Azure DNS 的 PaaS 服務) 都會受到 DDoS 基本保護所防護。

![](media/azure-ddos-best-practices/image3.png)

Azure 的基本 DDoS 保護會由軟體和硬體元件所組成。 軟體控制平面會決定何時、何處及何種類型的傳輸應該透過分析並移除攻擊流量的硬體裝置來操縱。 控制平面會根據涵蓋整個基礎結構範圍的 DDoS 保護「原則」進行此決策，這會以靜態方式設定並通用於所有 Azure 客戶。

例如，DDoS 保護原則會指定要在哪個流量「觸發」保護 (也就是應該透過清除裝置路由傳送租用戶的流量)，之後再指定清除應用裝置應如何「減緩」攻擊。

Azure 的 DDoS 保護基本服務已將目標設為基礎結構保護與 Azure 平台的保護。 當流量明顯超過某個可能在多租用戶環境中影響多位客戶的速率時，它會降低流量。 它不會提供警示或為每位客戶自訂原則。

### <a name="azure-ddos-standard-protection"></a>Azure DDoS 標準保護

標準保護提供增強的 DDoS 防護功能，並會自動調整以保護您在虛擬網路中特有的 Azure 資源。 只需在任何新的或現有的虛擬網路上啟用保護，而不需進行任何應用程式或資源變更。 它具有數個優於基本服務的優點，包括記錄、警示與遙測。 以下概述 Azure DDoS 保護標準服務的重要差異指標。

#### <a name="adaptive-realtime-tuning"></a>調適性即時調整

Azure DDoS 保護基本服務可協助保護客戶，但只會保護他們以防止對其他客戶產生影響。 例如，如果合法連入流量的正常流量小於涵蓋整個基礎結構範圍的 DDoS 保護原則「觸發程序比率」，而且服務是針對該流量所佈建，則可能不會注意到對於該客戶資源的 DDoS 攻擊。 更普遍的說法是，最新攻擊 (例如多向量 DDoS) 的複雜本質以及租用戶的應用程式專屬行為會呼叫針對每位客戶自訂的保護原則。
這可使用兩種見解來實現：

1. 自動學習每位客戶 (每個 IP) 的第 3/4 層流量模式，以及
2. 假設 Azure 的規模允許它承受大量流量，從而將誤判數目降至最低。

![](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring--alerting"></a>DDoS 保護遙測、監視及警示

利用 DDoS 保護標準，我們會在遭受 DDoS 攻擊的持續期間，透過 [Azure 監視器](/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md)公開豐富的遙測。 警示可以針對 DDoS 保護所公開的任何 Azure 監視器計量進行設定。 記錄可以透過 Azure 監視器診斷介面來與 Splunk (Azure 事件中心)、Azure Log Analytics 及 Azure 儲存體整合，以進行進階分析。

##### <a name="ddos-mitigation-policies"></a>DDoS 防護原則

在 Azure 入口網站中，按一下 [監視器] \>[計量]。 在 [計量] 畫面上，選取資源群組、公用 IP 位址的資源類型，以及您的 Azure 公用 IP 位址。 DDoS 計量將顯示於可用計量窗格下方。

DDoS 保護標準會在啟用 DDoS 的 VNET 中，針對受保護資源的每個公用 IP 套用**三個自動調整的防護原則 (TCP SYN、TCP 和 UDP)**。 您可以藉由選取**「要觸發 DDoS 防護的輸入封包」**計量來檢視原則閾值。

![](media/azure-ddos-best-practices/image7.png)

原則閾值會透過機器學習架構的網路流量分析進行自動設定。 只有在超出原則閾值時，才會針對遭受攻擊的 IP 位址進行 DDoS 防護。

##### <a name="under-ddos-attack"></a>遭受 DDoS 攻擊

如果公用 IP 位址遭受攻擊，當我們針對攻擊流量執行防護時，即會將「是否正遭受 DDoS 攻擊」的計量值切換為 1。

![](media/azure-ddos-best-practices/image8.png)

建議您針對此計量設定警示，以便在公用 IP 位址上有作用中的 DDoS 防護正在執行時收到通知。

如需詳細資訊，請參閱[使用 Azure 入口網站管理 Azure DDoS 保護標準](../virtual-network/ddos-protection-manage-portal.md)。

#### <a name="resource-attacks"></a>資源攻擊

特別是針對應用程式層上的資源攻擊，客戶應該設定 Web 應用程式防火牆 (WAF) 來協助維護 Web 應用程式的安全。 WAF 會檢查傳入的 Web 流量，以封鎖 SQL 插入式攻擊、跨網站指令碼攻擊、DDoS 攻擊，以及其他第 7 層攻擊。 Azure 提供 [WAF 作為一項應用程式閘道功能](../application-gateway/application-gateway-web-application-firewall-overview.md)，用來進行 Web 應用程式的集中式保護，免於遭遇常見的攻擊和弱點。 透過 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1)，您可以取得由 Azure 夥伴所提供且可能更適合您需求的各種 WAF 供應項目。

即使是 Web 應用程式防火牆，也很容易受到體積型與狀態耗盡攻擊。 強烈建議您在 WAF 虛擬網路上開啟 DDoS 標準保護，以提供保護來抵禦體積型與通訊協定攻擊。 如需詳細資訊，請參閱參考架構小節。

### <a name="protection-planning"></a>保護規劃

規劃與準備對於了解在發生 DDoS 攻擊時系統將如何執行，至關重要。 這個規劃與準備也有助於您設計事件管理回應方案。

客戶應確保已在網際網路對向端點的虛擬網路上啟用 DDoS 保護標準。 設定 DDoS 警示，有助於持續關注在您基礎結構上發生的任何潛在攻擊。 客戶應該獨立監視其應用程式。 他們必須了解應用程式的正常行為。 如果應用程式在遭受 DDoS 攻擊時並未如預期般運作，則必須採取步驟。

#### <a name="ddos-attacks-orchestration"></a>DDoS 攻擊協調流程

最好的做法是測試對於服務如何回應攻擊的假設，即使是透過執行定期模擬發生之前也一樣。 在測試期間，驗證您的服務或應用程式會繼續如預期般運作，而且不會對使用者體驗產生任何中斷。 從技術和處理程序觀點來識別其中的差距，然後併入 DDoS 回應策略中。 一般建議是在預備環境或非尖峰時間執行這類測試，以便將對生產環境的影響降到最低。

我們已與 [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) \(英文\) 合作建立一個介面，Azure 客戶可以針對啟用 DDoS 保護的公用端點產生流量，以進行模擬。 [BreakPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) \(英文\) 模擬將可讓您：

- 驗證 Microsoft Azure DDoS 保護如何保護您的 Azure 資源，免於受到 DDoS 攻擊

- 在遭受 DDoS 攻擊時，將您的事件回應程序最佳化

- 記載 DDoS 合規性

- 訓練您的網路安全性小組

網路安全性是一場在防禦上需要不斷創新的持續戰鬥。 Azure 的 DDoS 標準保護是一項最先進的技術，可為客戶提供有效的解決方案來防禦日益複雜的 DDoS 攻擊。

## <a name="components-of-a-ddos-response-strategy"></a>DDoS 回應策略的元件

在許多情況下，當 DDoS 攻擊將目標鎖定為您的 Azure 資源時，從使用者觀點來看，只需最少的介入。 儘管如此，納入 DDoS 防護以成為組織事件回應策略的一部分，確保可將對商務持續性的影響降到最低。

### <a name="microsoft-threat-intelligence"></a>Microsoft 威脅情報

Microsoft 擁有廣泛的威脅情報網路，該網路會使用延伸安全性社群的共同知識，來支援 Microsoft 線上服務、Microsoft 合作夥伴，以及網際網路安全性社群內的關聯性。 作為重要的基礎結構提供者，Microsoft 會收到關於威脅的早期警告，以獲取從其他 Microsoft 線上服務和 Microsoft 全球客戶群中學到的威脅情報。 所有的 Microsoft 威脅情報均會合併回其 Azure DDoS 保護產品。

此外，Microsoft 的 Digital Crimes Unit (DCU) 會針對殭屍網路執行攻擊性策略，而殭屍網路為適用於 DDoS 攻擊的常見命令和控制來源。

### <a name="perform-a-risk-evaluation-of-your-azure-resources"></a>針對您的 Azure 資源執行風險評估

請務必持續不斷地了解遭受 DDoS 攻擊的風險範圍。 客戶應該定期問自己：需要保護哪些公開可用的新 Azure 資源？ 服務中有單一失敗點嗎？ 如何將服務隔離以限制攻擊所產生的影響，同時仍能讓有效的客戶可以使用服務？ 有應該啟用 DDoS 保護標準但尚未啟用的虛擬網路嗎？ 我的服務是否會在跨多個區域進行容錯移轉時處於主動/主動狀態？

### <a name="customer-ddos-response-team"></a>客戶 DDoS 回應小組

建立 DDoS 回應小組，是確保有效且快速回應攻擊的關鍵步驟。 客戶必須識別將在組織中監督規劃與執行的各種連絡人。 DDoS 回應小組應該徹底了解 Azure DDoS 保護標準服務，而且應該善於識別與減緩攻擊，以便與各種內部和外部客戶進行協調，可視需要包括 Microsoft 技術支援小組。

Microsoft 建議納入 DDoS 回應小組規劃與模擬練習 (包括調整測試)，以作為服務可用性與持續性規劃的標準部分。 

### <a name="during-an-attack"></a>遭受攻擊時

Azure 的 DDoS 保護標準將會識別 DDoS 攻擊並降低風險，而不需使用者介入。 若要在針對受保護的公用 IP 風險降低時收到通知，您可以在「是否正遭受 DDoS 攻擊」計量下方[設定警示](../virtual-network/ddos-protection-manage-portal.md)。 您可以視需要進一步檢閱並建立適用於其他 DDoS 計量的警示，以了解攻擊的範圍、要卸除的流量等。

#### <a name="when-to-contact-microsoft-support"></a>連絡 Microsoft 支援服務的時機

- 如果在遭受 DDoS 攻擊時，您發現受保護資源的效能嚴重降低或無法使用該資源。

- 如果您認為 DDoS 保護服務並未如預期般運作。 DDoS 保護服務將只會在符合下列準則時起始防護：

    - 「要觸發 DDoS 防護 (TCP/TCP SYN/UDP) 的原則」計量值低於在受保護的公用 IP 資源上收到的流量。

- 如果您預計將會有病毒式事件，而此事件將導致您的網路流量大幅提高。

- 如果有心人士威脅要針對您的資源發動 DDoS 攻擊。

針對影響商務的重大問題，請建立嚴重性 A 的[支援票證](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

### <a name="post-attack-steps"></a>張貼攻擊步驟

遭受攻擊之後進行事後檢討，並視需要重新調整 DDoS 回應策略，永遠都是個不錯的策略。 需考量的事項：

- 是否因為缺少可調整規模的架構而使得服務或使用者體驗發生任何中斷？

- 哪些應用程式或服務發生最多？

- DDoS 回應策略的有效性如何，以及如何進一步改善它？

如果您懷疑受到 DDoS 攻擊，請透過一般的 Azure 支援通道呈報。

## <a name="ddos-protection-reference-architectures"></a>DDoS 保護參考架構

DDoS 保護標準是針對[部署於虛擬網路的服務](../virtual-network/virtual-network-for-azure-services.md)設計的。 對於其他服務，將適用預設的 DDoS 基本保護。 以下概述的特定參考架構會依案例排列，並與架構模式組合在一起。

### <a name="virtual-machine-windowslinux-workloads"></a>虛擬機器 (Windows/Linux) 工作負載

#### <a name="application-running-on-load-balanced-vms"></a>在負載平衡的 VM 上執行的應用程式

此參考架構顯示一組經過證實的作法，可用來在負載平衡器後方的擴展集中執行多個 Windows 虛擬機器 (VM)，以提升可用性和延展性。 此架構可以用於任何無狀態的工作負載，例如網頁伺服器。

![](media/azure-ddos-best-practices/image9.png)

在此架構中，會將工作負載散發到多個 VM 執行個體。 有個單一公用 IP 位址，而且會使用負載平衡器來將網際網路流量散發到 VM。 DDoS 保護標準會在關聯公用 IP 的 Azure (網際網路) 負載平衡器虛擬網路上啟用。

負載平衡器會將連入網際網路要求散發到 VM 執行個體。 VM 擴展集允許以手動方式或自動根據預先定義的規則，相應縮小或放大 VM 數目。 如果資源受到 DDoS 攻擊，這就很重要。 如需此參考架構的詳細資訊，請參閱這篇[文章](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)。

#### <a name="applications-running-on-windows-n-tier"></a>在 Windows 多層式架構上執行的應用程式

有許多方式可實作多層式架構。 下圖顯示典型的 3 層式架構 Web 應用程式。 此架構是根據[執行負載平衡的 VM 以獲得延展性和可用性](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)建置的。 Web 及 Business 層會使用負載平衡的 VM。

![](media/azure-ddos-best-practices/image10.png)

在上述架構中，會在虛擬網路上啟用 DDoS 保護標準。 虛擬網路中的所有公用 IP 都將取得第 3 層/第 4 層 DDoS 保護。 針對第 7 層保護，應該部署 WAF SKU 中的應用程式閘道。 如需此參考架構的詳細資訊，請參閱這篇[文章](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier)。

#### <a name="paas-web-application"></a>PaaS Web 應用程式

此參考架構示範在單一區域中執行的 Azure App Service 應用程式。 針對使用 [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) 和 [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/) 的 Web 應用程式，此架構會示範一組經過證實的作法。
針對容錯移轉案例設定待命區域。

![](media/azure-ddos-best-practices/image11.png)

流量管理員會將連入要求路由傳送到其中一個區域的應用程式閘道。 正常作業期間，它會將要求路由傳送到主要區域的應用程式閘道。 如果該區域變得無法使用，流量管理員就會容錯移轉到待命區域的應用程式閘道。

所有來自網際網路且預定送往 Web 應用程式的流量都會透過流量管理員，路由傳送到[應用程式閘道的公用 IP 位址](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)。 在此案例中，應用程式服務 (Web 應用程式) 本身不會直接面向外部，且會受到應用程式閘道保護。 建議您設定應用程式閘道 WAF SKU (防止模式) ，來提供保護以避免受到第 7 層 (HTTP/HTTPS/Web 通訊端) 攻擊。 此外，將 Web 應用程式設定為[只接受來自應用程式閘道 IP 位址的流量](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) \(英文\)。

如需此參考架構的詳細資訊，請參閱這篇[文章](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)。

### <a name="mitigation-for-non-web-paas-services"></a>適用於非 Web PaaS 服務的防護功能

#### <a name="hdinsight-on-azure"></a>Azure 上的 HDInsight

此參考架構示範如何針對 Azure 上的 [HDInsight 叢集](https://docs.microsoft.com/azure/hdinsight/)設定 DDoS 保護標準。 您必須確定 HDInsight 叢集會連結到虛擬網路，並在該虛擬網路上啟用 DDoS 保護。

![](media/azure-ddos-best-practices/image12.png)

![](media/azure-ddos-best-practices/image13.png)

在此架構中，來自網際網路且預定送往 HDInsight 叢集的流量，會路由傳送到與 HDInsight 閘道負載平衡器相關聯的公用 IP。 閘道負載平衡器接著會將流量直接傳送到前端節點或背景工作節點。 假設已在 HDInsight 虛擬網路上啟用 DDoS 保護標準，虛擬網路中的所有公用 IP 都將受到第 3 層/第 4 層 DDoS 保護。 上述參考架構可以與多層式/多區域參考架構相結合。

如需此參考架構的詳細資訊，請參閱[使用 Azure 虛擬網路延伸 Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文。

### <a name="azure-api-management"></a>Azure API 管理

此參考架構會保護 [API 管理](../api-management/api-management-key-concepts.md)資源的公用端點，其會將 API 發佈給組織外部的客戶。 APIM 應該部署於外部虛擬網路以啟用 DDoS 保護。

![](media/azure-ddos-best-practices/image15.png)

藉由設定外部虛擬網路，透過公用負載平衡器，從公用網際網路存取 API 管理閘道和開發人員入口網站。 在此架構中，會在 APIM 虛擬網路的外部虛擬網路上啟用 DDoS 保護標準。 流量會從網際網路中路由傳送到 APIM 的公用 IP 位址，其會受到保護以防止受到第 3 層/第 4 層網路攻擊。 若要提供保護以防止受到第 7 層 HTTP/HTTPS 攻擊，您可以在 WAF 模式中設定應用程式閘道。

部署於虛擬網路且可針對 DDoS 保護標準設定的其他服務清單，可以在[這裡](../virtual-network/virtual-network-for-azure-services.md)進行維護。 DDoS 保護標準僅支援 Azure Resource Manager 資源。 原本就不支援在具備公用 IP 的 VNET 中插入的應用程式服務環境 (ASE) 部署。 如需保護 ASE 環境的詳細資訊，請參閱本節。

## <a name="next-steps"></a>後續步驟

* [Azure DDoS 保護產品頁面](https://azure.microsoft.com/services/ddos-protection/)

* [Azure DDoS 保護部落格](http://aka.ms/ddosblog) \(英文\)

* [Azure DDoS 保護文件](../virtual-network/ddos-protection-overview.md)
