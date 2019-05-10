---
title: 安全性最佳作法來保護您的資產-Microsoft Azure
description: 本文提供一組作業的最佳作法，來保護您的資料、 應用程式和在 Azure 中的其他資產。
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 4a4677b5db730001df75d201d8e6d3149cb928e6
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409851"
---
# <a name="azure-operational-security-best-practices"></a>Azure 作業安全性最佳做法
本文提供一組作業的最佳作法，來保護您的資料、 應用程式和在 Azure 中的其他資產。

最佳作法是根據共識的意見，並使用目前的 Azure 平台功能及功能集。 隨著時間變更的意見和技術，這篇文章會更新以反映這些變更以規則為基礎。

## <a name="define-and-deploy-strong-operational-security-practices"></a>定義及部署增強式作業的安全性做法
Azure 作業安全性是指使用者可在 Azure 中用來保護其資料、應用程式和其他資產的服務、控制措施與功能。 Azure 作業安全性的基礎架構涵蓋透過 Microsoft 特有功能獲得的知識，包括[安全性開發週期 (SDL)](https://www.microsoft.com/sdl)、[Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) 方案，以及對網路安全性威脅型態的深層認知。

## <a name="manage-and-monitor-user-passwords"></a>管理及監視使用者密碼
下表列出與管理使用者密碼的一些最佳作法：

**最佳做法**：請確定您在雲端中有密碼保護的適當層級。   
**詳細資料**：請依照下列中的指導方針[Microsoft 密碼指引](https://www.microsoft.com/research/publication/password-guidance/)，其範圍的 Microsoft 身分識別平台 （Azure Active Directory、 Active Directory 和 Microsoft 帳戶） 的使用者。

**最佳做法**：監視與您的使用者帳戶相關的可疑動作。   
**詳細資料**：監視[風險的使用者](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)並[有風險的登入](../active-directory/reports-monitoring/concept-risk-events.md)藉由使用 Azure AD 安全性報告。

**最佳做法**：自動偵測並修復高風險的密碼。   
**詳細資料**：[Azure AD Identity Protection](../active-directory/identity-protection/overview.md)是可讓您的 Azure AD Premium P2 版本的功能：

- 偵測會影響組織身分識別的潛在弱點
- 針對偵測到的與您組織的身分識別有關的可疑動作，設定自動回應
- 調查可疑事件並採取適當動作以解決這些問題

## <a name="receive-incident-notifications-from-microsoft"></a>從 Microsoft 接收事件通知
請確定您的安全性作業小組收到 Microsoft Azure 的事件通知。 事件通知可讓您的安全性小組可讓您知道您已入侵 Azure 資源，因此它們可以快速回應，並修復潛在的安全性風險。

在 Azure 註冊入口網站中，您可以確保系統管理員連絡資訊包含通知安全性作業的詳細資料。 連絡人資訊為電子郵件地址和電話號碼。

## <a name="organize-azure-subscriptions-into-management-groups"></a>組織成管理群組的 Azure 訂用帳戶
如果貴組織有多個訂用帳戶，您可能需要一個方法來有效率地管理這些訂用帳戶的存取、原則和相容性。 [Azure 的管理群組](../governance/management-groups/create.md)提供上述訂用帳戶範圍的層級。 您組織到稱為管理群組容器的訂用帳戶，並將治理條件套用到管理群組。 管理群組內的所有訂用帳戶都會自動繼承套用到管理群組的條件。

您可以建置有彈性的結構的管理群組和訂用帳戶的目錄。 每個目錄會提供一個稱為根管理群組的單一最上層的管理群組。 這個根管理群組會建置於階層內，讓所有的管理群組和訂用帳戶摺疊於其中。 根管理群組可讓全域原則和 RBAC 指派在目錄層級套用。

以下是使用管理群組的一些最佳做法：

**最佳做法**：請確定加入新的訂用帳戶，適用於控管的元素，例如原則和權限。   
**詳細資料**：您可以使用根管理群組指派全企業的安全性項目套用至所有的 Azure 資產。 原則和權限是元素的範例。

**最佳做法**：對齊分割策略來控制和原則的一致性，每個區段中提供的點的管理群組的最上層的層級。   
**詳細資料**：建立根管理群組下的每個區段的單一管理群組。 請勿建立根下的其他管理群組。

**最佳做法**：限制管理群組深度，以避免妨礙作業和安全性的混淆。   
**詳細資料**：限制三種層級，包括根階層。

**最佳做法**：請小心選取要套用到整個企業根管理群組的項目。   
**詳細資料**：請確定根管理群組項目有明確的需要套用在每個資源，而且這是影響很小。

好的候選項目包括：

- 具有清楚的商業影響 （例如，與資料主權相關的限制） 的法規需求
- 使用負的幾乎不可能的需求會影響與稽核效果或 RBAC 原則之類的作業，已仔細檢閱的權限指派

**最佳做法**：請仔細規劃並測試在根管理群組上的所有企業級變更之後再套用它們 （原則、 RBAC 模型等等）。   
**詳細資料**：在根管理群組中的變更可能會影響在 Azure 上的每個資源。 它們可提供強大的方式，以確保整個企業的一致性，而錯誤或不正確的使用方式可能造成負面影響實際執行作業。 測試根管理群組的所有變更都測試實驗室或實際執行環境中試驗。

## <a name="streamline-environment-creation-with-blueprints"></a>簡化與藍圖的環境建立程序
[Azure 藍圖](../governance/blueprints/overview.md)服務能讓雲端架構設計人員和中央資訊技術群組定義可重複的 Azure 資源集合來實作，並遵循組織的標準、 模式和需求。 Azure 藍圖，讓開發小組快速地建置及使用一組新的環境內建元件以及它們要建立這些環境中組織合規性的信心，就能。

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>監視儲存體服務在行為上是否有非預期的變更
與傳統環境相比，在雲端環境託管的分散式應用程式一旦發生問題，無論要為其進行診斷或疑難排解，都更加複雜。 應用程式可以在 PaaS 或 IaaS 基礎架構、內部部署環境、行動裝置或是這幾種環境的組合上部署。 您應用程式的網路流量可能會周遊公用及私人網路，而且您的應用程式可能會使用多個儲存技術。

您應該持續監視您應用程式使用的儲存服務在行為上是否有任何非預期的變更 (例如回應時間更慢)。 使用記錄收集更多詳細的資料，並深入分析問題。 透過監視與記錄取得的診斷資訊將有助於判斷應用程式所遭遇問題的根本原因。 之後，您才能針對問題進行疑難排解，並決定該採取哪些合宜的步驟來矯正它。

[Azure 儲存體分析](../storage/storage-analytics.md)會執行記錄，並提供 Azure 儲存體帳戶的計量資料。 建議您使用此資料來追蹤要求、分析使用量趨勢，以及診斷儲存體帳戶的問題。

## <a name="prevent-detect-and-respond-to-threats"></a>預防、偵測及回應威脅
[Azure 資訊安全中心](../security-center/security-center-intro.md)可協助您預防、 偵測和回應威脅，藉由提供增加的可見性 （和控制） 您的 Azure 資源的安全性。 它會在您的 Azure 訂用帳戶之間提供整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於各種安全性解決方案。

資訊安全中心的免費層提供有限的安全性，只為您 Azure 資源。 標準層可將這些功能擴充到內部部署及其他雲端。 資訊安全中心標準可協助您尋找和修正安全性弱點、 套用存取與應用程式的控制項，封鎖惡意活動，請使用分析和情報，偵測到威脅及在遭受攻擊時迅速回應。 前 60 天可以免費試用資訊安全中心標準定價層。 我們建議您[將您的 Azure 訂用帳戶升級至資訊安全中心標準](../security-center/security-center-get-started.md)。

您可以使用資訊安全中心來集中檢視所有 Azure 資源安全性狀態。 只需看一眼，便可確認安全性控制項是否已就緒並正確設定，並快速找出任何需要注意的資源。

資訊安全中心也整合了[Windows Defender 進階威脅防護 (ATP)](../security-center/security-center-wdatp.md)，以提供全方位的端點偵測和回應 (EDR) 功能。 進行 Windows Defender ATP 整合後，您可以找出異常狀況。 您也可以偵測並回應在資訊安全中心監視的伺服器端點上的進階攻擊。

幾乎所有的企業組織都有以協助找出新興威脅，藉由合併來自不同的訊號收集裝置記錄檔資訊的安全性資訊和事件管理 (SIEM) 系統。 然後分析記錄資料分析系統，以協助識別什麼是 「 有趣 」 是不可避免的所有記錄檔收集和分析解決方案中的雜訊。

[Azure 的 Sentinel](../sentinel/overview.md)是可調整的雲端原生的安全性資訊及事件管理 (SIEM) 和安全性自動化的協調流程回應 （已攀升 2 倍） 解決方案。 Azure 的 Sentinel 提供智慧型安全性分析和威脅情報，透過警示偵測威脅與可見性、 主動尋找自動化的威脅回應。

以下是一些針對防止、 偵測和回應威脅的最佳作法：

**最佳做法**：使用雲端架構的 SIEM，增加的速度和您的 SIEM 解決方案的延展性。   
**詳細資料**：調查的功能和功能[Azure Sentinel](../sentinel/overview.md)並加以比較感的項目目前的功能使用內部部署。 考慮採用 Azure Sentinel 其是否符合貴組織的 SIEM 需求。

**最佳做法**：尋找最嚴重的安全性弱點，因此您可以排定調查的優先順序。   
**詳細資料**：檢閱您[Azure 安全分數](../security-center/security-center-secure-score.md)以查看所產生的計劃內建於 Azure 資訊安全中心與 Azure 原則的建議。 這些建議可協助位址最高風險，例如安全性更新、 endpoint protection、 加密、 安全性組態、 遺漏 WAF、 網際網路連線的 Vm，以及更多。

安全的分數，根據 Center 網際網路安全性 (CIS) 控制項，可讓您進行效能評定您組織的 Azure 安全性，以防止外部來源。 外部驗證可協助驗證及擴充您的小組的安全性策略。

**最佳做法**：監視機器、 網路、 儲存體和資料服務和應用程式探索，並優先處理潛在的安全性問題的安全性狀態。  
**詳細資料**：請遵循[安全性建議](../security-center/security-center-recommendations.md)在資訊安全中心開始，最高的優先順序項目。

**最佳做法**：資訊安全中心警示整合至您的安全性資訊及事件管理 (SIEM) 解決方案。   
**詳細資料**：大部分的組織使用的 SIEM 使用它作為中央的結算所需要的分析師回應安全性警示。 處理過的資訊安全中心所產生的事件都會發佈至 Azure 活動記錄檔，可透過 Azure 監視器的記錄檔之一。 Azure 監視器提供合併的管線，將您的所有監視資料路由傳送至 SIEM 工具。 請參閱[在資訊安全中心整合安全性解決方案](../security-center/security-center-partner-integration.md#exporting-data-to-a-siem)如需相關指示。 如果您使用 Azure 的 Sentinel，請參閱[Connect Azure 資訊安全中心](../sentinel/connect-azure-security-center.md)。

**最佳做法**：Azure 整合您的 SIEM 與記錄檔。   
**詳細資料**：使用[蒐集，並將資料匯出至 Azure 監視器](../azure-monitor/overview.md#integrate-and-export-data)。 這種做法很重要的啟用安全性事件的調查，而線上記錄保留期限制。 如果您使用 Azure 的 Sentinel，請參閱[將資料來源連接](../sentinel/connect-data-sources.md)。

**最佳做法**：加速您調查和追捕程序，並減少誤判，藉由整合到您的攻擊調查的端點偵測和回應 (EDR) 功能。   
**詳細資料**：[啟用 Windows Defender ATP 整合](../security-center/security-center-wdatp.md#enable-windows-defender-atp-integration)透過資訊安全中心安全性原則。 請考慮使用 Azure Sentinel 威脅追捕和事件回應。

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>監視以端對端案例為基礎的網路監視
客戶可以結合網路資源 (例如虛擬網路、ExpressRoute、應用程式閘道及負載平衡器)，在 Azure 中建置端對端網路。 每個網路資源都可進行監視。

[Azure 網路監看員](../network-watcher/network-watcher-monitoring-overview.md)是一個區域性服務。 使用其診斷和視覺化工具可監視並診斷位於和進出 Azure 的網路案例層級條件。

以下是網路監視及可用工具的最佳作法。

**最佳做法**：利用封包擷取自動化遠端網路監視作業。  
**詳細資料**：使用網路監看員無須登入您的 VM，就能監視及診斷網路問題。 您可以設定警示來觸發[封包擷取](../network-watcher/network-watcher-alert-triggered-packet-capture.md)，並能存取封包層級的即時效能資訊。 當您發現問題時，可以詳加調查，從而進行更好的診斷。

**最佳做法**：使用流量記錄獲取您網路流量的見解。  
**詳細資料**：使用[網路安全性群組流量記錄](../network-watcher/network-watcher-nsg-flow-logging-overview.md)，更深入了解您的網路流量模式。 流量記錄中的資訊可協助您收集合規性資料，並稽核及監視您的網路安全性設定檔。

**最佳做法**：診斷 VPN 連線問題。  
**詳細資料**：使用網路監看員[診斷最常見的 VPN 閘道及連線問題](../network-watcher/network-watcher-diagnose-on-premises-connectivity.md)。 您不僅可以找出問題，還可以使用詳細的記錄進一步調查。

## <a name="secure-deployment-by-using-proven-devops-tools"></a>使用經證實的 DevOps 工具進行安全的部署
使用下列 DevOps 最佳做法以確保您企業和團隊的生產力與效率。

**最佳做法**：自動化服務的建置及部署。  
**詳細資料**：[基礎架構即程式碼](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) \(英文\) 是一組技術和實作，可協助 IT 專業人員減輕每日建置及管理模組化基礎架構的負擔。 它可讓 IT 專業人員建置並維護其新式伺服器環境，就像是軟體開發人員建置並維護應用程式程式碼一樣。

您可以使用 [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) 搭配宣告式範本佈建應用程式。 在單一的範本中，您可以部署多個服務及其相依性。 您可以使用相同的範本，在應用程式生命週期的每個階段重複部署應用程式。

**最佳做法**：自動建置並部署至 Azure Web 應用程式或雲端服務。  
**詳細資料**：您可以設定至您 Azure DevOps Projects[自動建置和部署](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops)Azure web 應用程式或雲端服務。 Azure DevOps 會將二進位檔自動部署至 Azure 執行組建之後每個程式碼簽入, 之後。 套件建置程序等同於 Visual Studio 中的 [套件] 命令，而發佈步驟等同於 Visual Studio 中的 [發佈] 命令。

**最佳做法**：自動化發行管理。  
**詳細資料**：[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) \(英文\) 是用於自動化多階段部署及管理發行程序的解決方案。 建立受控持續部署管線以快速、輕鬆且經常發行。 您可以使用 Azure Pipelines，將發行程序自動化，而且可以擁有預先定義的核准工作流程。 在內部部署及部署至雲端、擴充，並視需要自訂。

**最佳做法**：在您啟動應用程式或將更新部署至生產環境之前，請先檢查該應用程式的效能。  
**詳細資料**：執行雲端[負載測試](https://docs.microsoft.com/azure/devops/test/load-test/overview.md?view=azure-devops#alternatives)來：

- 尋找您應用程式中的效能問題。
- 提升部署品質。
- 確定您的應用程式仍可使用。
- 確定您的應用程式可以處理後續上市或行銷活動的流量。

[Apache JMeter](https://jmeter.apache.org/)是免費的熱門的開放原始碼工具與強大的社群支援。

**最佳做法**：監視應用程式效能。  
**詳細資料**：[Azure Application Insights](../azure-monitor/app/app-insights-overview.md) 是多個平台上的 Web 開發人員所適用的可延伸應用程式效能管理 (APM) 服務。 使用 Application Insights 監視您的即時 Web 應用程式。 它會自動偵測效能異常。 其中包括分析工具可協助您診斷問題，並了解使用者實際如何運用您的應用程式。 它是設計來協助您持續改善效能和可用性。

## <a name="mitigate-and-protect-against-ddos"></a>減少並防範 DDoS
分散式阻斷服務 (DDoS) 是一種嘗試耗盡應用程式資源的攻擊類型。 目標是影響應用程式的可用性，及其處理合法要求的能力。 這些攻擊會變得越來越複雜，而且大小和影響越來越大。 它們可以鎖定可透過網際網路公開觸達的任何端點。

設計和建置 DDoS 復原需要規劃與設計各種不同的失敗模式。 以下是在 Azure 上建置 DDoS 復原服務的最佳做法。

**最佳做法**：確保安全性在應用程式的整個生命週期 (從設計和實作到部署與作業) 中具有優先順序。 應用程式可能存在一些錯誤 (bug)，讓非常小量的要求使用大量資源，因而導致服務中斷。  
**詳細資料**：若要協助保護在 Microsoft Azure 上執行的服務，您應該對您應用程式架構有良好的了解，而且必須專注於[軟體品質的五大要素](https://docs.microsoft.com/azure/architecture/guide/pillars)。 您應該了解一般流量、應用程式與其他應用程式之間的連線模型，以及公開至公用網際網路的服務端點。

確保應用程式有足夠的彈性，能夠處理以應用程式本身為目標的阻斷服務，至關重要。 從[安全性開發週期 (SDL)](https://www.microsoft.com/sdl) 開始，安全性和隱私權便會內建於 Azure 平台之中。 SDL 會在每個開發階段處理安全性，並確保 Azure 會持續更新以使其更為安全。

**最佳做法**：將您的應用程式設計為可[水平調整](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out)以滿足放大負載的需求，遭遇 DDoS 攻擊時尤其需要。 如果您的應用程式相依於服務的單一執行個體，它會建立單一失敗點。 佈建多個執行個體可讓系統更有彈性且更具延展性。  
**詳細資料**：對於 [Azure App Service](../app-service/app-service-value-prop-what-is.md)，請選擇可提供多個執行個體的 [App Service 方案](../app-service/overview-hosting-plans.md)。

對於 Azure 雲端服務，設定您的每個角色以使用[多個執行個體](../cloud-services/cloud-services-choose-me.md)。

對於 [Azure 虛擬機器](../virtual-machines/windows/overview.md)，確保 VM 架構包含多個 VM，而且每個 VM 都包含於[可用性設定組](../virtual-machines/virtual-machines-windows-manage-availability.md)中。 建議您使用虛擬機器擴展集，自動調整功能。

**最佳做法**：將應用程式中的安全性防禦分層，可降低成功攻擊的機會。 使用 Azure 平台的內建功能，為您的應用程式實作安全設計。  
**詳細資料**：攻擊的風險會隨著應用程式大小 (介面區) 而提高。 您可以使用允許清單來關閉負載平衡器 ([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) 和 [Azure 應用程式閘道](../application-gateway/application-gateway-create-probe-portal.md)) 上不需要的公開 IP 位址空間和接聽連接埠，來減少介面區。

[網路安全性群組](../virtual-network/security-overview.md)是減少攻擊面的另一種方法。 您可以使用[服務標記](../virtual-network/security-overview.md#service-tags)和[應用程式安全性群組](../virtual-network/security-overview.md#application-security-groups)，將建立安全性規則與設定網路安全性的複雜性近可能降低，直到成為應用程式結構的自然延伸。

應盡可能將 Azure 服務部署於[虛擬網路](../virtual-network/virtual-networks-overview.md)上。 此種做法可讓服務資源透過私人 IP 位址進行通訊。 根據預設，來自虛擬網路的 Azure 服務流量會使用公用 IP 位址作為來源 IP 位址。

使用[服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)會在從虛擬網路存取 Azure 服務時，將服務流量切換為使用虛擬網路私人位址作為來源 IP 位址。

我們通常會看到客戶的內部部署資源連同其在 Azure 中的資源遭受攻擊。 如果您將內部部署環境連線到 Azure，請將內部部署資源在公用網際網路上的曝光率降至最低。

Azure 有兩個 DDoS [服務供應項目](../virtual-network/ddos-protection-overview.md)，可防止網路攻擊：

- 基本保護預設會整合到 Azure 中，不需任何額外成本。 Azure 全球部署網路的規模與能力可提供防禦，透過一律啟動的流量監視和即時緩和，來抵禦常見的網路層攻擊。 基本保護不需要設定使用者也不需要變更應用程式，而且有助於保護所有 Azure 服務，包括像是 Azure DNS 的 PaaS 服務。
- 標準保護則針對網路攻擊，提供進階的 DDoS 防護功能。 其會自動調整以保護特定的 Azure 資源。 只需在建立虛擬網路時啟用保護即可。 您也可以在建立之後再啟用保護，而不需進行任何應用程式或資源變更。

## <a name="enable-azure-policy"></a>啟用 Azure 原則
[Azure 原則](../governance/policy/overview.md)是您用來建立、 指派和管理原則的 Azure 中的服務。 這些原則強制執行規則和效果對您的資源，讓這些資源能符合您的公司標準及服務等級協定。 Azure 原則會透過評估您的資源是否符合指派的策略來滿足此需求。

啟用 Azure 原則來監視和強制執行您的組織撰寫的原則。 這可確保您公司的合規性或法規安全性需求，集中管理您的混合式雲端工作負載的 安全性原則。 了解如何[建立和管理原則來強制執行合規性](../governance/policy/tutorials/create-and-manage.md)。 請參閱[Azure 原則定義結構](../governance/policy/concepts/definition-structure.md)概略的原則項目。

以下是一些安全性最佳作法之後採用 Azure 原則：

**最佳做法**：原則支援數種類型的效果。 您可以閱讀它們[Azure 原則定義結構](../governance/policy/concepts/definition-structure.md#policy-rule)。 企業營運造成負面影響所**拒絕**效果和**補救**生效，因此開始**稽核**效果來限制從所產生的負面影響的風險原則。   
**詳細資料**：[在稽核模式中啟動原則部署](../governance/policy/concepts/definition-structure.md#policy-rule)，然後稍後再進展到**拒絕**或是**補救**。 測試並檢閱稽核效果的結果，再移至**拒絕**或是**補救**。

如需詳細資訊，請參閱 <<c0> [ 建立和管理原則來強制執行合規性](../governance/policy/tutorials/create-and-manage.md)。

**最佳做法**：識別負責監視原則違規，並確保此正確的補救動作會執行快速的角色。   
**詳細資料**：具有指派的角色監視合規性，透過[Azure 入口網站](../governance/policy/how-to/get-compliance-data.md#portal)或透過[命令列](../governance/policy/how-to/get-compliance-data.md#command-line)。

**最佳做法**：Azure 原則是組織的書面原則技術表示法。 將所有的 Azure 原則對應到組織的原則，以減少混淆，並提高一致性。   
**詳細資料**：在貴組織的文件或 Azure 原則本身藉由將參考加入至組織的原則在 Azure 中的文件對應[原則描述](../governance/policy/concepts/definition-structure.md#display-name-and-description)或 Azure 原則[initiative](../governance/policy/concepts/definition-structure.md#initiatives)描述。

## <a name="monitor-azure-ad-risk-reports"></a>監視 Azure AD 的風險的報表
大部分的安全性缺口出現於當攻擊者藉由竊取使用者的身分識別來取得環境的存取權時。 探索遭入侵的身分識別並不容易。 Azure AD 會使用調適性機器學習服務演算法和啟發學習法，來偵測與您使用者帳戶相關的可疑動作。 偵測到的每個可疑動作都會儲存在名為[風險事件](../active-directory/reports-monitoring/concept-risk-events.md)的記錄中。 風險事件會記錄在 Azure AD 安全性報告。 如需詳細資訊，了解[風險使用者的安全性報告](../active-directory/reports-monitoring/concept-user-at-risk.md)並[有風險的登入安全性報告](../active-directory/reports-monitoring/concept-risky-sign-ins.md)。

## <a name="next-steps"></a>後續步驟
如需更多安全性最佳做法，請參閱 [Azure 安全性最佳做法與模式](security-best-practices-and-patterns.md)，以便在使用 Azure 設計、部署和管理雲端解決方案時使用。

下列資源可提供更多有關 Azure 安全性和相關 Microsoft 服務的一般資訊：
* [Azure 安全性小組部落格](https://blogs.msdn.microsoft.com/azuresecurity/) - Azure 安全性的最新資訊
* [Microsoft 安全性回應中心](https://technet.microsoft.com/library/dn440717.aspx) -- 可在其中回報 Microsoft 安全性弱點 (包括 Azure 的問題) 或透過電子郵件傳送給 secure@microsoft.com
