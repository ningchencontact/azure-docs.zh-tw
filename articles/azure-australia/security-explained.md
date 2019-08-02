---
title: Azure Australia 安全性說明
description: 關於澳大利亞地區的資訊, 並符合澳大利亞政府原則、法規和法規的特定需求。
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 212d14e31c152e50c216f2f34fb225c29fe054b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571428"
---
# <a name="azure-australia-security-explained"></a>Azure Australia 安全性說明

本文針對澳大利亞政府機關調查、設計和部署 Microsoft Azure 的澳大利亞, 解決一些常見的問題和領域。

## <a name="irap-and-certified-cloud-services-list-ccsl-documents"></a>IRAP 和認證的雲端服務清單 (CCSL) 檔

澳大利亞網路安全中心 (ACSC) 提供認證的信件、認證報告, 以及將服務新增至 CCSL 時的取用者指南。

Microsoft 目前列示于 CCSL for Azure、Office 365 和 Dynamics 365 CRM。

Microsoft 會在[Microsoft 服務信任入口網站](https://aka.ms/au-irap)的澳大利亞特定頁面上, 將我們的審核、評估和 ACSC 認證檔提供給客戶和合作夥伴。

## <a name="dissemination-limiting-markers-dlm-and-protected-certification"></a>散佈限制標記 (DLM) 與受保護的認證

擁有已核准供政府組織使用之系統 (包括雲端服務) 的程式, 定義于澳大利亞網路安全中心 (ACSC) 所產生和發佈的[資訊安全手冊 (ISM)](https://acsc.gov.au/infosec/ism/)中。 澳大利亞網路安全中心 (ACSC) 是適用于網路安全性和雲端認證的 .ASD 中的實體。

核准程式有兩個步驟:

1. 安全性評估 (IRAP)-註冊的專業人員針對 ISM 中的技術控制項評估系統、服務和解決方案, 並評估控制項是否已有效實行的程式。 評量也會識別核准授權單位在發出核准以進行操作之前, 所需考慮的任何特定風險 (ATO)。

1. 作業的核准–政府機關的資深主管會正式 recognises, 並接受系統對其處理、儲存和通訊之資訊的剩餘風險。  此程式的輸入是安全性評量。

在受保護層級的 Azure 服務評量, 可識別儲存和處理受保護和較低資料所需的安全性控制措施, 已確認準備就緒, 並且有效地運作。

## <a name="australian-data-classification-changes"></a>澳大利亞資料分類變更

在2018年10月1日, 律師一般的部門已公開對保護安全性原則架構 (PSPF) 所做的變更, 特別是新的[敏感性和分類資訊系統](https://www.protectivesecurity.gov.au/information/sensitive-classified-information/Pages/default.aspx)。

![修改過的 PSPF 分類](media/pspf-classifications.png)

所有在 PSPF 下運作的澳大利亞機關和組織都會受到這些變更的影響。 影響目前 IRAP/CCSL 認證的主要變更是目前已淘汰的散佈限制標記 (DLM)。 標示**官方:敏感性**會取代用於保護機密資訊的各種 DLMs。 這項變更也引進了三個資訊管理標記, 可套用至所有敏感度和分類層級的所有官方資訊。 **受保護**的分類會保持不變。

「未分類」一詞會從新系統中移除, 而非政府資訊則會套用非官方的詞彙, 但不需要正式標示。

## <a name="choosing-an-azure-region-for-my-official-sensitive-and-protected-workloads"></a>為我的官方選擇 Azure 區域:敏感性和受保護的工作負載

Azure **官方:敏感性**和**受保護**的認證服務會部署到所有四個澳大利亞資料中心區域 (澳大利亞東部、澳大利亞東南部、澳大利亞中部和澳大利亞中部 2); 不過, 認證報告由ACSC 建議在有服務可供使用時, 將**受保護**的資料部署至坎培拉中的 Azure Government 區域。 如需有關**受保護**認證的 Azure 服務的詳細資訊, 請從[此 STP 的 [澳大利亞] 頁面](https://aka.ms/au-irap)取得。

>[!NOTE]
>Microsoft 建議所有敏感性和分類的政府資料都部署到澳大利亞中部和澳大利亞中部2區域, 因為它們是專為政府需求而設計和操作的。

如需 Azure 澳大利亞區域的特殊特性的詳細資訊, 請[Azure 澳大利亞中部區域](https://azure.microsoft.com/global-infrastructure/australia/)。

## <a name="how-microsoft-separates-classified-and-official-data"></a>Microsoft 如何分隔分類和官方資料

Microsoft 會在澳大利亞操作 Azure 和 Office 365, 如同所有資料都有敏感性和 (或) 分類, 將我們的安全性控制提升到該高階長條。

支援 Azure 的基礎結構可能會提供多個分類的資料。  但正如我們的假設, 客戶資料已分類, 適當的控制項就已就緒。 Microsoft 已採用符合 PSPF 需求的服務的基準安全性狀態, 來儲存和處理**受保護**的分類資訊。

為了確保我們的客戶在 Azure 中有一個租使用者不會有其他租使用者的風險, Microsoft 會實行全面的防護深度控制項。

除了在 Microsoft Azure 平臺內執行的功能之外, 其他客戶可設定的控制項 (例如使用客戶管理的金鑰進行加密、嵌套虛擬化和即時系統管理存取) 可以進一步降低風險。 在坎培拉 Azure Government 的澳大利亞地區內, 只有澳大利亞 & 紐西蘭政府和國家關鍵基礎結構組織的正式允許清單程式已就緒。 此社區雲端可為組織提供額外的保證, 以 cotenant 風險。

Microsoft Azure**受保護**的憑證報表會確認這些控制項是否有效, 以儲存和處理**受保護**的分類資料及其隔離。

## <a name="relevance-of-irapccsl-to-state-government-and-critical-infrastructure-providers"></a>IRAP/CCSL 與州政府和重要基礎結構提供者的相關性

許多州政府和重要基礎結構提供者會將聯邦政府需求納入其安全性原則和保證架構中。 這些組織也會處理**官方官方** **:敏感性**和某種程度的**受保護**分類資料, 從其與聯邦政府或他們自己的互動都有。

澳大利亞政府逐漸專注于保護非政府資料的原則和法規, 而這會對澳大利亞的安全性和經濟平安造成影響。 因此, Azure 澳大利亞區域和 CCSL 認證與所有這些產業相關。

![重要基礎結構磁區](media/nci-sectors.png)

Microsoft 認證示範 Azure 服務已受到全面、嚴格且正式的安全性保護評估, 並已核准處理這類高度敏感的資料。

## <a name="location-and-control-of-microsoft-data-centres"></a>Microsoft 資料中心的位置與控制

這是政府和重要基礎結構的必要需求, 可以明確知道資料中心的位置, 以及處理其資料的雲端服務擁有權。  Microsoft 是超大規模資料庫雲端提供者的獨特功能, 可提供有關這些位置與擁有權的廣泛資訊。

Microsoft 的 Azure 澳大利亞地區 (澳大利亞中部和澳大利亞中部 2) 會在 CDC 資料中心的設施內運作。  CDC 資料中心的擁有權是以美國聯邦 Superannuation Corporation 的 48% 擁有權來控制、Infratil 的 48% 擁有權 (以 NZ 為基礎、雙澳大利亞和新的紐西蘭股票交換列出長期基礎結構資產基金)和 4% 的澳大利亞管理。  

CDC 資料中心的管理與澳大利亞政府簽訂了合約保證, 以限制未來的擁有權和控制轉讓。 透過 Microsoft 與 CDC 資料中心的合作關係, 這項供應鏈與擁有權的透明度, 符合[整個政府主控策略](https://www.dta.gov.au/our-projects/whole-government-hosting-strategy)和**認證主權資料中心**定義的原則。

## <a name="the-azure-services-that-are-included-in-the-current-ccsl-certification"></a>包含在目前 CCSL 認證中的 Azure 服務

在2017年6月, ACSC 認證的 41 Azure 服務, 可用於儲存和處理未**分類的資料:DLM**層級。 在2018年4月, 這些服務的24個已通過**保護**的分類資料認證。

在澳大利亞的 Azure 區域中, ACSC 認證的 Azure 服務可用性會如下所示 (以粗體顯示的認證會**受到保護**):

|Azure 澳洲中部區域|非區域服務和其他區域|
|---|---|
|API 管理, 應用程式閘道, 應用程式服務,**自動化, Azure 入口網站, 備份, 批次, 雲端服務**, Cosmos DB, 事件中樞, **ExpressRoute**, HDInsight, **Key Vault**, Load Balancer, Log Analytics,**多因素驗證**、Redis Cache、 **Resource Manager、服務匯流排、Service Fabric、Site Recovery、SQL Database、儲存體**、流量管理員、**虛擬機器、虛擬網路、VPN 閘道**|**Azure Active Directory**, CDN, 資料目錄, 匯**入匯出, 資訊保護, IOT 中樞**, Machine Learning, 媒體服務,**通知中樞**, Power BI,**安全中心,** 排程器, 搜尋, 串流分析|
|

Microsoft 發佈了[Microsoft Azure 合規性的總覽](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/44/Microsoft%20Azure%20Compliance%20Offerings.pdf), 其中列出了 Azure 進行的所有全球、政府、產業和地區合規性及評估程式的所有範圍內服務, 包括 IRAP/CCSL。

## <a name="azure-service-not-listed-or-assessed-at-a-lower-level-than-i-need"></a>Azure 服務未列出或評估的層級比我需要的低

未經認證或已**正式認證的服務:敏感性**但未**受保護**, 可以搭配使用, 或作為裝載**受保護**資料之解決方案的一部分, 前提是服務可能是:
1. 未加密儲存或處理**受保護**的資料, 或
1. 您已完成風險評估, 並已核准服務自行儲存**受保護**的資料。

如果您想要使用未包含在 CCSL 上的服務來儲存和處理**官方**資料, 您可以, 但 ISM 會要求您在使用雲端服務提供者輸入或更新合約之前, 先通知 ACSC 撰寫這項功能。

**受保護**工作負載的機構所使用的任何服務, 仍然需要進行安全性評估和核准, 並配合在[DTA 安全雲端策略](https://www.dta.gov.au/files/cloud-strategy/secure-cloud-strategy.pdf)中的 ISM 和代理程式管理的 IRAP 評估程式中所述的程式。

![DTA 安全雲端策略認證程式](media/certification.png)

Microsoft 會持續評估我們的服務, 以確保平臺安全且適用于澳大利亞政府用途, 如果您需要目前不在**受保護**CCSL 的服務協助, 請與 Microsoft 聯繫。

由於 Microsoft 在 CCSL 上有一系列的服務在未經分類的**DLM**和**PROTECTED**分類上經過認證, 所以 ISM 會要求我們至少每兩年執行一次 IRAP 的服務評量。 Microsoft 科學家年度評量, 這也是包含其他服務以供考慮的機會。

## <a name="certified-protected-gateway-in-azure"></a>Azure 中經認證保護的閘道

由於「閘道合併」程式下允許的 SIGs 數目限制, Microsoft 不會操作政府認證的安全網際網路閘道 (SIG)。  但是, 您可以在 Microsoft Azure 內設定 SIG 的預期和必要功能。

透過 Azure 服務的**受保護**憑證, ACSC 對於連線到 Azure 的機構有特定的建議, 以及在安全性網域之間執行網路分割 (例如,**受保護**的與網際. 這些建議包括使用網路安全性群組、防火牆和虛擬私人網路。  ACSC 建議使用虛擬閘道應用裝置。 Azure 中有數個可用的虛擬裝置, 在「有線評估產品」清單上具有相當的實體, 或已針對「通用條件保護設定檔」進行評估, 並列在「通用條件」入口網站上。 這些產品都是由 .ASD 相互副檔名被, 成為通用條件辨識安排 (CCRA) 的簽字。

Microsoft 已產生指導方針, 說明如何執行以 Azure 為基礎的功能, 以提供保護不同安全性網域之間界限所需的安全性功能, 結合時, 會形成等同于認證的 SIG。 有一些合作夥伴可以協助設計和執行這些功能, 以及一些可執行相同動作的合作夥伴解決方案。

## <a name="security-clearances-and-citizenship-of-microsoft-support-personnel"></a>Microsoft 支援人員的安全性證明與公民

Microsoft 會使用已篩選和定型的安全性人員來全域操作我們的服務。  具有 unescorted 實體存取悉尼和墨爾本設施的人員具有澳大利亞政府基準安全性的證明。 澳大利亞中部和澳大利亞中部2區域中的人員都具有最低負面調查 1 (NV1) 的間隙 (適用于**秘密**資料)。 這可為客戶提供額外的保證, 亦即 Azure 資料中心內的人員可高度信任。

Microsoft 有一個零的「存取原則」, 其中的存取權是透過根據角色型存取控制的一次性、僅限系統管理來授與。 在大部分的情況下, 我們的系統管理員不需要存取客戶資料的許可權或許可權, 就能疑難排解和維護服務。  遠端執行工作的高程度自動化和腳本會否定直接存取客戶資料的需求。

律師-一般部門已確認 Azure 中 Microsoft 的人員安全性原則和程式, 與 PSPF 存取在 INFOSEC-9 中所布建資訊的意圖一致。

## <a name="storing-international-traffic-of-arms-regulations-itar-or-export-administration-regulations-ear-data"></a>儲存跨國法規 (ITAR) 或匯出管理法規 (EAR) 資料的國際流量

Azure 技術控制項可協助客戶滿足其匯出控制資料的義務, 這在 Azure 中是全域相同的。 重要的是, 沒有任何正式的評估和憑證架構可用於匯出控制的資料, 因此不會有「ITAR/耳」的刻度。

針對 Azure Government 和 Office 365 美國政府國防部, 我們已將額外的合約和流程措施提供給客戶, 以支援出口控制項。 這些額外的合約條款和 Azure 區域的保證美國國家/地區支援和管理不適用於澳大利亞。

這並不表示澳大利亞的 Azure 無法用於 ITAR/EAR, 但您需要清楚地瞭解您透過匯出授權所強加的限制, 而且必須在使用 Azure 儲存之前, 先執行額外的保護以符合這些義務該資料。 例如, 您可能需要將國籍建立為 Azure Active Directory 的屬性、使用 Azure 資訊保護來強制執行資料的加密規則, 並將它限制為僅供我們使用, 以及在匯出授權中包含任何其他 nationalities。在儲存于 Azure 之前, 使用客戶金鑰或保存您自己的金鑰來 ITAR 資料, 並在清單中開啟, 以加密內部部署的所有資料。

由於 ITAR 不是正式的認證, 因此您必須瞭解與匯出授權相關聯的限制和限制, 然後再透過 Azure 中是否有足夠的控制項來符合這些需求。 在此情況下, 要仔細考慮的其中一個問題是, 我們的工程師可能不會在匯出授權上核准國籍。

## <a name="next-steps"></a>後續步驟

請參閱[azure VPN 閘道](vpn-gateway.md)上的文章, 以取得 ISM 相容的設定, 並實現與 Azure 澳大利亞的 VPN 連線能力
