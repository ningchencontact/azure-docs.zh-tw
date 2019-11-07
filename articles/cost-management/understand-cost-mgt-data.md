---
title: 了解 Azure 成本管理資料 | Microsoft Docs
description: 這篇文章可協助您進一步瞭解 Azure 成本管理中包含的資料，以及處理、收集、顯示和關閉的頻率。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 681ccc768b1fa3d5a968847d11987fbd83898b59
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721363"
---
# <a name="understand-cost-management-data"></a>了解成本管理資料

本文可協助您進一步瞭解 Azure 成本管理所包含的 Azure 成本和使用量資料。 它會說明處理、收集、顯示和關閉資料的頻率。 您須按月支付使用 Azure 的費用。 雖然計費週期是每月期間，週期的開始和結束日期會因訂用帳戶類型而有所不同。 「成本管理」接收使用量資料的頻率會隨著不同的因素而改變。 這類因素包括處理資料所需的時間，以及 Azure 服務向計費系統發出使用量資料的頻率。

成本管理包括所有使用量和購買專案，包括 Enterprise 合約（EA）帳戶的保留和協力廠商供應專案。 使用隨用隨付費率的 Microsoft 客戶合約帳戶和個別訂閱僅包含 Azure 和 Marketplace 服務的使用量。 不包含支援和其他成本。 成本會預估到發票產生且不會納入信用額度。

## <a name="supported-microsoft-azure-offers"></a>支援的 Microsoft Azure 優惠

下列資訊顯示 Azure 成本管理中目前支援的 [Microsoft Azure 供應項目](https://azure.microsoft.com/support/legal/offer-details/)。 Azure 供應項目是您 Azure 訂用帳戶的類型。 資料會在中提供，成本管理從 date 中的**可用資料**開始。 如果訂用帳戶變更了供應專案，將無法使用供應專案變更日期之前的成本。

| **類別**  | **供應項目名稱** | **配額識別碼** | **供應項目號碼** | **可用的資料** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | 2014 5 月<sup>1</sup> |
| **Enterprise 合約 (EA)** | Enterprise 開發/測試                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | 2014 5 月<sup>1</sup> |
| **Enterprise 合約 (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | 2014 5 月<sup>1</sup> |
| **Microsoft 客戶合約** | [Microsoft Azure 計畫](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | N/A | 2019年3月<sup>3</sup>日 |
| **Microsoft 客戶合約** | [開發/測試的 Microsoft Azure 計畫](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | N/A | 2019年3月<sup>3</sup>日 |
| **合作夥伴支援的 Microsoft 客戶合約** | Microsoft Azure 計畫 | CSP_2015-05-01、CSP_MG_2017-12-01 和 CSPDEVTEST_2018-05-01<br><br>配額識別碼會重複用於 Microsoft 客戶合約和舊版 CSP 訂用帳戶。 目前僅支援 Microsoft 客戶合約訂閱。 | N/A | 2019年10月 |
| **Microsoft Developer Network (MSDN)** | [MSDN 平臺](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2018年10月<sup>2 日</sup> |
| **隨用隨付** | [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2018年10月<sup>2 日</sup> |
| **隨用隨付** | [隨用隨付開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2018年10月<sup>2 日</sup> |
| **隨用隨付** | [Microsoft 合作夥伴網路](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2018年10月<sup>2 日</sup> |
| **隨用隨付** | [免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2018年10月<sup>2 日</sup> |
| **隨用隨付** | [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2018年10月<sup>2 日</sup> |
| **隨用隨付** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P、MS-AZR-0122P - MS-AZR-0125P、MS-AZR-0128P - MS-AZR-0130P | 2018年10月<sup>2 日</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2018年10月<sup>2 日</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2018年10月<sup>2 日</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2018年10月<sup>2 日</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2018年10月<sup>2 日</sup> |
| **Visual Studio** | [Visual Studio Enterprise： BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2018年10月<sup>2 日</sup> |

_<sup>**1**</sup>若是2014之前的資料，請造訪[Azure 企業版入口網站](https://ea.azure.com)。_

_<sup>**2**</sup>針對2018年10月2日前的資料，請造訪[Azure 帳戶中心](https://account.azure.com/subscriptions)。_

_<sup>**3**</sup> Microsoft 客戶合約于2019年3月開始，且在此時間點之前沒有任何歷程記錄資料。_

_<sup>**4**</sup>點數型和預付型訂用帳戶的歷程記錄資料可能不符合您的發票。請參閱以下的歷程[記錄資料可能不符合發票](#historical-data-might-not-match-invoice)。_

尚不支援下列供應專案：

| 類別  | **供應項目名稱** | **配額識別碼** | **供應項目號碼** |
| --- | --- | --- | --- |
| **Azure Germany** | [Azure 德國隨用隨付](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **雲端解決方案提供者 (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **雲端解決方案提供者 (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **雲端解決方案提供者 (CSP)** | CSP 中適用於 Microsoft Cloud Germany 的 Azure Germany   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **隨用隨付**                 | Azure 學生入門版 | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **隨用隨付** | [適用于學生的 Azure](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **隨用隨付**                 | [Microsoft Azure 贊助](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **支援方案** | 標準支援                    | Default_2014-09-01 | MS-AZR-0041P |
| **支援方案** | 專業指導支援         | Default_2014-09-01 | MS-AZR-0042P |
| **支援方案** | 開發人員支援                   | Default_2014-09-01 | MS-AZR-0043P |
| **支援方案** | 德國支援方案                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **支援方案** | Azure Government 標準支援   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **支援方案** | Azure Government 專業指導支援 | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **支援方案** | Azure Government 開發人員支援  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>判斷您的供應專案類型
如果您未看到訂用帳戶的資料，而您想要確認訂用帳戶是否屬於支援的供應項目，您可以驗證您的訂用帳戶是否受支援。 若要驗證 Azure 訂用帳戶是否受支援，請登入 [Azure 入口網站](https://portal.azure.com)。 然後，在左側功能表窗格中選取 [所有服務]。 在服務清單中，選取 [訂用帳戶]。 在訂用帳戶清單功能表中，按一下您要確認的的訂用帳戶。 您的訂用帳戶會顯示在 [概觀] 索引標籤上，您會看到 [供應項目] 和 [供應項目識別碼]。 下圖說明一個範例。

![顯示供應項目和供應項目識別碼的訂用帳戶概觀範例](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>包含在成本管理中的成本

下表顯示「成本管理」中包含或未包含的資料。 系統會預估所有成本，直到產生發票為止。 顯示的成本不包括免費和預付的點數。

**成本和使用量資料**

| **包含** | **未包含** |
| --- | --- |
| Azure 服務使用量<sup>5</sup>        | 支援費用 - 如需詳細資訊，請參閱[發票條款說明](../billing/billing-understand-your-invoice.md)。 |
| Marketplace 供應專案使用量<sup>6</sup> | 稅金 - 如需詳細資訊，請參閱[發票條款說明](../billing/billing-understand-your-invoice.md)。 |
| Marketplace 購買<sup>6</sup>      | 點數 - 如需詳細資訊，請參閱[發票條款說明](../billing/billing-understand-your-invoice.md)。 |
| 保留購買<sup>7</sup>      |  |
| 保留購買的分期<sup>7</sup>      |  |

_<sup>**5**</sup> Azure 服務使用量是以保留和談判價格為基礎。_

_<sup>**6**</sup> Marketplace 購買目前不適用於隨用隨付、MSDN 及 Visual Studio 優惠。_

_<sup>**7**</sup>保留購買僅適用于 ENTERPRISE 合約（EA）帳戶。_

**Metadata**

| **包含** | **未包含** |
| --- | --- |
| 資源標記<sup>8</sup> | 資源群組標記 |

_會套用<sup>**8**</sup>個資源標記，因為使用量是從每個服務發出，而且無法追溯到歷程記錄使用量。_

## <a name="rated-usage-data-refresh-schedule"></a>評比使用量資料重新整理排程

成本與使用量資料可從 Azure 入口網站和[支援 API](index.yml) 的 [成本管理 + 計費] 中取得。 檢閱成本時，請記住以下幾點：

- 目前計費期間的預估費用每天會更新 6 次。
- 目前計費期間的預估費用可能隨著您產生更多使用量而變更。
- 每次更新將持續累積，包含前次更新的所有明細項目和資訊。
- Azure 會在計費期間結束後的 72 小時 (三個日曆天) 內終止或_關閉_目前計費期間。

下列範例說明計費期間的結束方式。

Enterprise 合約 (EA) 訂用帳戶 – 如果計費月份在 3 月 31 日結束，預估費用將會在 72 個小時內更新。 在此範例中，即 4 月 4 日午夜 (UTC)。

隨用隨付訂用帳戶 – 如果計費月份在 5 月 15 日結束，預估費用將可在 72 個小時內更新。 在此範例中，即 5 月 19 日午夜 (UTC)。

### <a name="rerated-data"></a>重新評估的資料

無論您是使用[成本管理 api](index.yml)、Power BI 或 Azure 入口網站來抓取資料，都會預期目前計費週期的費用 rerated，因此會變更，直到發票關閉為止。

## <a name="cost-management-data-fields"></a>成本管理資料欄位

在使用量詳細資料檔和成本管理 Api 中可找到下列資料欄位。 對於下列粗體欄位，合作夥伴可以使用 [成本分析] 中的 [篩選] 和 [群組依據] 功能，依多個欄位來分析成本。 粗體欄位僅適用于合作夥伴支援的 Microsoft 客戶合約。

| **欄位名稱** | **說明** |
| --- | --- |
| invoiceId | 針對特定交易顯示在發票上的發票識別碼。 |
| previousInvoiceID | 參考原始發票會有退款（負成本）。 只有在有退款時才會填入。 |
| billingAccountName | 代表合作夥伴之帳單帳戶的名稱。 它會在上架至 Microsoft 客戶合約的客戶和已進行權利購買的 CSP 客戶（如 SaaS、Azure Marketplace 和保留專案）中，產生所有成本。 |
| billingAccountID | 代表合作夥伴之帳單帳戶的識別碼。 |
| billingProfileID | 帳單設定檔的識別碼，會在上架至 Microsoft 客戶合約的客戶與已進行權利購買的 CSP 客戶（例如 SaaS、Azure Marketplace 和）之間，以單一計費貨幣來分組發票的成本。能. |
| billingProfileName | 帳單設定檔的名稱，會在上架至 Microsoft 客戶合約的客戶與已進行權利購買的 CSP 客戶（例如 SaaS、Azure Marketplace 和）之間，以單一計費貨幣來分組發票的成本。能. |
| invoiceSectionName | 在發票中計費的專案名稱。 不適用於合作夥伴所上架的 Microsoft 客戶合約。 |
| invoiceSectionID | 要在發票中計費之專案的識別碼。 不適用於合作夥伴所上架的 Microsoft 客戶合約。 |
| **CustomerTenantID** | 客戶的訂用帳戶之 Azure Active Directory 租&#39;使用者的識別碼。 |
| **CustomerName** | 客戶的訂用帳戶 Azure Active Directory 租使用者&#39;的名稱。 |
| **CustomerTenantDomainName** | 客戶的訂用帳戶之 Azure Active Directory 租使用者&#39;的功能變數名稱。 |
| **PartnerTenantID** | 夥伴&#39;s Azure Active Directory 租使用者的識別碼。 |
| **PartnerName** | 合作夥伴 Azure Active Directory 租使用者的名稱。 |
| **ResellerMPNID** | 與訂用帳戶相關聯之轉售商的 MPNID。 |
| costCenter | 與訂用帳戶相關聯的成本中心。 |
| billingPeriodStartDate | 計費循環開始日期，如發票所示。 |
| billingPeriodEndDate | 計費循環結束日期，如發票所示。 |
| servicePeriodStartDate | 評等計費期間的開始日期。 Azure 服務的價格會在評等期間決定。 |
| servicePeriodEndDate | 服務使用量針對費用進行評分時的結束日期。 Azure 服務的價格取決於評等期間。 |
| 日期 | 針對 Azure 耗用量資料，它會以分級顯示使用量的日期。 針對 [保留實例]，它會顯示 [已購買] 日期。 針對週期性費用和一次費用（例如 Marketplace 和支援），它會顯示購買日期。 |
| productID | 依耗用量或購買而產生的產品識別碼。 這是 productID 和 SKuID 的串連索引鍵，如合作夥伴中心所示。 |
| product | 依耗用量或購買而產生的產品名稱（如發票所示）。 |
| serviceFamily | 顯示購買或收費產品的服務系列。 例如，儲存體或計算。 |
| productOrderID | 訂用帳戶所屬資產或 Azure 方案名稱的識別碼。 例如，Azure 方案。 |
| productOrderName | 訂用帳戶所屬的 Azure 方案名稱。 例如，Azure 方案。 |
| consumedService | 傳統 EA 使用量詳細資料中使用的已取用服務（舊版分類法）。 |
| meterID | 測量耗用量的計量識別碼。 |
| meterName | 識別測量耗用量的計量名稱。 |
| meterCategory | 識別最上層服務的使用方式。 |
| meterSubCategory | 定義可能會影響費率的 Azure 服務類型或子類別。 |
| meterRegion | 針對根據資料中心位置定價的某些服務，識別資料中心的位置。 |
| 訂用帳戶識別碼 | Microsoft 針對 Azure 訂用帳戶所產生的唯一識別碼。 |
| subscriptionName | Azure 訂用帳戶的名稱。 |
| 條款 | 顯示供應專案有效性的詞彙。 例如，保留實例會顯示保留實例的每年12個月。 針對一次性購買或週期性購買，此一詞彙會針對 SaaS、Azure Marketplace 和支援顯示一個月。 不適用於 Azure 耗用量。 |
| publisherType （firstParty，thirdPartyReseller，thirdPartyAgency） | 發行者的類型，可將發行者識別為第一方、協力廠商轉銷商或協力廠商機關。 |
| partNumber | 未使用的保留實例和 Azure Marketplace 服務的元件編號。 |
| publisherName | 服務發行者的名稱，包括 Microsoft 或協力廠商發行者。 |
| reservationId | 購買之保留實例的識別碼。 |
| reservationName | 保留實例的名稱。 |
| reservationOrderId | 保留實例的「訂單」。 |
| frequency | 保留實例的付款頻率。 |
| resourceGroup | 用於生命週期資源管理的 Azure 資源組名。 |
| instanceID （或） ResourceID | 資源實例的識別碼。 |
| resourceLocation | 資源位置的名稱。 |
| 位置 | 資源的正規化位置。 |
| effectivePrice | 服務的有效單位價格（以定價貨幣表示）。 產品、服務系列、計量及供應專案都是唯一的。 在計費帳戶的價位表中搭配定價使用。 當有階層式定價或包含的數量時，會顯示耗用量的混合價格。 |
| 數量 | 已購買或耗用的測量數量。 計費期間所使用的計量數量。 |
| unitOfMeasure | 識別服務的計費單位。 例如，GB 和小時。 |
| pricingCurrency | 定義單位價格的貨幣。 |
| billingCurrency | 定義計費成本的貨幣 |
| chargeType | 定義成本在 Azure 成本管理中所代表的費用類型，例如購買和退款。 |
| costinBillingCurrency | 計費貨幣中的稅金前的 ExtendedCost 或混合成本。 |
| costinPricingCurrency | 使用定價貨幣來與價格相互關聯的 ExtendedCost 或混合成本。 |
| **costinUSD** | 以美元為單位的預估 ExtendedCost 或混合成本。 |
| **paygCostInBillingCurrency** | 如果定價是零售價格，則會顯示成本。 以計費貨幣顯示隨用隨付價格。 僅適用于 RBAC 範圍。 |
| **paygCostInUSD** | 如果定價是零售價格，則會顯示成本。 顯示隨用隨付價格，以美元為單位。 僅適用于 RBAC 範圍。 |
| exchangeRate | 用來從定價貨幣轉換為計費貨幣的匯率。 |
| exchangeRateDate | 用來從定價貨幣轉換成&#39;計費貨幣的匯率日期。 |
| isAzureCreditEligible | 指出成本是否符合 Azure 點數的付款資格。 |
| serviceInfo1 | 舊版欄位，可擷取選擇性的服務特定中繼資料。 |
| serviceInfo2 | 舊版欄位，可擷取選擇性的服務特定中繼資料。 |
| additionalInfo | 服務專屬的中繼資料。 例如，虛擬機器的影像類型。 |
| tags | 您指派給計量的標記。 使用標記為帳單記錄分組。 例如，您可以使用標記，根據使用計量的部門散發成本。 |
| **partnerEarnedCreditRate** | 當合作夥伴以合作夥伴系統管理員連結存取為基礎時，適用的折扣率。 |
| **partnerEarnedCreditApplied** | 指出是否已套用合作夥伴的點數。 |


## <a name="usage-data-update-frequency-varies"></a>使用量資料更新頻率不相同

您產生的使用量資料在成本管理中的可用性取決於某些因素，包括：

- Azure 服務 (例如儲存體、計算、CDN 和 SQL) 發出使用量資料的頻率。
- 透過評比引擎和成本管理管線處理使用量資料所花費的時間。

某些服務會比其他服務更頻繁地發出使用量資料。 因此，您在「成本管理」中可能會看到某些服務的資料比其他發出資料頻率較低的服務更快出現。 服務的使用量通常需要 8-24 小時才會出現在「成本管理」中。 請記住，在尚未結算的月份中，該資料會隨著您產生更多使用量而重新整理，因為更新會持續累積。

## <a name="historical-data-might-not-match-invoice"></a>歷程記錄資料可能不符合發票

信用額度型和預付型供應專案的歷程記錄資料可能不符合您的發票。 某些 Azure 隨用隨付、MSDN 和 Visual Studio 供應專案可以將 Azure 點數和預付費用套用到發票。 不過，成本管理中顯示的歷程記錄資料僅以您預估的耗用量費用為基礎。 成本管理歷程記錄資料不包含付款和信用額度。 因此，針對下列供應專案顯示的歷程記錄資料可能不會與您的發票完全相符。

- 適用于學生的 Azure （MS-AZR-0017P-0170P）
- Azure in Open （MS-AZR-0017P-0111P）
- Azure Pass （MS-AZR-0017P-0120P、MS-MS-AZR-0017P-0123P、MS-MS-AZR-0017P-0125P、MS-MS-AZR-0017P-0128P、MS-MS-AZR-0017P-0129P）
- 免費試用（MS-AZR-0017P-0044P）
- MSDN (MS-AZR-0062P)
- Visual Studio （MS-AZR-0017P-0029P、MS-MS-AZR-0017P-0059P、MS-MS-AZR-0017P-0060P、MS-MS-AZR-0017P-0063P、MS-MS-AZR-0017P-0064P）

## <a name="see-also"></a>另請參閱

- 如果您尚未完成成本管理的第一個快速入門，請在[開始分析成本](quick-acm-cost-analysis.md)閱讀它。
