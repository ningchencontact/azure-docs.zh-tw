---
title: 開始使用 Azure 成本管理合作夥伴
description: 本文說明合作夥伴如何使用 Azure 成本管理功能，以及如何為他們的客戶啟用成本管理存取權。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 10889f6d872510fb53e76ab3722343aa2ee6a5e8
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293907"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>開始使用 Azure 成本管理合作夥伴

Azure 成本管理原本就適用于已將客戶上架至 Microsoft 客戶合約並已[購買 Azure 方案](/partner-center/purchase-azure-plan)的合作夥伴。 本文說明合作夥伴如何使用[Azure 成本管理](../index.yml)功能來查看 Azure 方案中訂用帳戶的成本。 它也會說明合作夥伴如何為其客戶啟用成本管理存取。 客戶可以在其 CSP 合作夥伴啟用時，使用成本管理功能。

CSP 合作夥伴會使用成本管理來執行下列動作：

- 瞭解發票成本，並將成本與客戶、訂用帳戶、資源群組和服務產生關聯。
- 使用可依客戶、訂用帳戶、資源群組、資源、計量、服務和許多其他維度來分析成本的功能，以直覺的方式瞭解 Azure 的成本[分析](quick-acm-cost-analysis.md)成本。
- 在成本分析中，查看已套用合作夥伴獲額點數（PEC）的資源成本。
- 當成本超過預算時，使用程式設計[預算](tutorial-acm-create-budgets.md)和警示來設定通知和自動化。
- 啟用可提供客戶存取成本管理資料的 Azure Resource Manager 原則。 接著，客戶可以使用[隨用隨付費率](https://azure.microsoft.com/pricing/calculator/)來查看其訂用帳戶的耗用量成本資料。
- 使用隨用隨付訂用帳戶將其成本和使用量資料匯出至儲存體 blob。

以下範例顯示所有客戶的成本。
![範例顯示所有客戶的成本](./media/get-started-partners/customer-costs1.png)

以下範例顯示單一客戶的成本。
![範例顯示單一客戶](./media/get-started-partners/customer-costs2.png) 的成本

Azure 成本管理中提供的所有功能也適用于 REST Api。 使用 Api 將成本管理工作自動化。

## <a name="prerequisites"></a>必要條件

身為合作夥伴，Azure 成本管理僅適用于 Azure 方案上的訂用帳戶。

若要在 Azure 入口網站中啟用 Azure 成本管理，您必須確認客戶接受 Microsoft 客戶合約（代表客戶），並將客戶轉換至 Azure 方案。 只有轉換至 Azure 方案的訂用帳戶成本可在 Azure 成本管理中取得。

Azure 成本管理需要您的帳單帳戶或訂用帳戶的讀取權限。

如需啟用和指派帳單帳戶 Azure 成本管理存取權的詳細資訊，請參閱[指派使用者角色和許可權](/partner-center/permissions-overview)。 **全域管理員**和**管理員代理程式**角色可以管理帳單帳戶的成本。

若要存取訂用帳戶範圍的 Azure 成本管理，任何具有訂用帳戶之 RBAC 存取權的使用者都可以查看零售（隨用隨付）費率的費用。 不過，必須啟用客戶租使用者的成本可見度原則。 若要查看支援帳戶類型的完整清單，請參閱[瞭解成本管理資料](understand-cost-mgt-data.md)。


## <a name="how-cost-management-uses-scopes"></a>成本管理如何使用範圍

範圍可供您管理帳單資料、擁有付款特有的角色、查看發票，以及進行一般帳戶管理。 計費和帳戶角色會與用於資源管理（使用 RBAC）的範圍分開管理。 為了清楚區分不同範圍的意圖，包括存取控制差異，它們分別稱為計費範圍和 RBAC 範圍。

若要瞭解計費範圍和 RBAC 範圍，以及成本管理如何與範圍搭配運作，請參閱[瞭解並使用範圍](understand-work-scopes.md)。

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>使用合作夥伴租使用者計費範圍來管理成本

在您將客戶上架至 Microsoft 客戶合約之後，您的租使用者中會提供下列_計費範圍_。 使用範圍來管理成本管理中的成本。

### <a name="billing-account-scope"></a>帳單帳戶範圍

使用帳單帳戶範圍來查看您所有客戶和帳單設定檔的預付稅成本。 僅針對客戶在 Microsoft 客戶合約上以耗用量為基礎的產品顯示發票成本。 不過，針對購買的產品，會針對 Microsoft 客戶合約和 CSP 供應專案的客戶顯示發票成本。 目前，要在範圍中查看成本的預設貨幣是美元。 範圍的預算設定也是美元。

無論客戶計費的貨幣為何，合作夥伴都會使用計費帳戶範圍來設定預算，並管理其客戶、訂用帳戶、資源和資源群組之間的成本（以美元為單位）。

合作夥伴也會在成本分析視圖中，以特定計費貨幣來篩選成本。 選取 [**實際成本**] 清單，以查看支援的客戶帳單貨幣成本。

![顯示貨幣的實際成本選擇範例](./media/get-started-partners/actual-cost-selector.png)

使用計費範圍中的[分攤成本視圖](quick-acm-cost-analysis.md#customize-cost-views)，以在保留期限內查看保留實例分攤成本。

### <a name="billing-profile-scope"></a>帳單設定檔範圍

使用帳單設定檔範圍，針對發票中包含的所有產品和訂用帳戶，在您所有的客戶上，以帳單貨幣來查看預付稅成本。 您可以使用**InvoiceID**篩選，針對特定發票的帳單設定檔中的成本進行篩選。 篩選會顯示特定發票的耗用量和產品購買成本。 您也可以篩選發票上特定客戶的成本，以查看稅前成本。

在您將客戶上線至 Microsoft 客戶合約之後，您會收到發票，其中包含這些客戶在 Microsoft 客戶合約上所有產品（耗用量、購買和權利）的所有費用。 以相同的貨幣來計費時，這些發票也會包含權利和購買產品的費用，例如 SaaS、Azure Marketplace 和保留，適用于仍在 CSP 供應專案中的客戶。

為協助根據客戶發票來協調費用，計費設定檔範圍可讓您查看針對客戶的發票所產生的所有成本。 就像發票，範圍會針對新的 Microsoft 客戶合約中的每個客戶顯示成本。 範圍也會顯示仍在目前 CSP 供應專案中的客戶權利產品的每個費用。

帳單設定檔和計費帳戶範圍是唯一適用的範圍，會顯示權利和購買產品的費用，例如 Azure Marketplace 和保留購買。

帳單設定檔會定義發票中包含的訂閱。 帳單設定檔是與 enterprise 合約註冊相同的功能。 帳單設定檔是產生發票的範圍。

目前，客戶的帳單貨幣是在帳單設定檔範圍中查看成本時的預設貨幣。 在帳單設定檔範圍設定的預算以計費貨幣為單位。

合作夥伴可以使用範圍來協調發票。 而且，他們會使用範圍來設定下列專案的計費貨幣預算：

- 特定篩選的發票
- 客戶
- 訂閱
- 資源群組
- 資源
- Azure 服務
- 計量器
- ResellerMPNID

### <a name="customer-scope"></a>客戶範圍

合作夥伴使用範圍來管理與上架至 Microsoft 客戶合約之客戶相關聯的成本。 此範圍可讓合作夥伴查看特定客戶的預付稅成本。 您也可以篩選特定訂用帳戶、資源群組或資源的稅前成本。

客戶範圍不包含目前 CSP 供應專案的客戶。 此範圍僅包含擁有 Microsoft 客戶合約的客戶。 目前 CSP 供應專案的權利成本（而非 Azure 使用量）可在您套用客戶篩選時，于帳單帳戶和帳單設定檔範圍內取得。

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>合作夥伴存取成本管理中的計費範圍

只有具備**全域管理員**和系統**管理員代理程式**角色的使用者，才能夠直接在合作夥伴的 Azure 租使用者中管理和查看帳單帳戶、帳單設定檔和客戶的成本。 如需合作夥伴中心角色的詳細資訊，請參閱[指派使用者角色和許可權](/partner-center/permissions-overview)。

## <a name="enable-cost-management-in-the-customer-tenant"></a>在客戶租使用者中啟用成本管理

合作夥伴可以在客戶上架至 Microsoft 客戶合約之後，啟用成本管理的存取權。 接著，合作夥伴可以啟用原則，讓客戶能夠以隨用隨付零售費率來查看其計算成本。 成本會針對其在 RBAC 訂用帳戶和資源群組範圍內使用的使用量，顯示在客戶的帳單貨幣中。

當合作夥伴啟用了成本可見度的原則時，任何擁有訂用帳戶 Azure Resource Manager 存取權的使用者都可以使用隨用隨付費率來管理和分析成本。 對 Azure 訂用帳戶具有適當 RBAC 存取權的轉銷商和客戶有效，可以查看成本。

不論原則為何，如果合作夥伴有權存取訂用帳戶和資源群組，也可以查看成本。

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>啟用原則以查看 Azure 使用量費用

合作夥伴會使用下列資訊來啟用原則，以查看其客戶的 Azure 使用量費用。

在 Azure 入口網站中，登入合作夥伴租使用者，然後選取 **成本管理 + 帳單**。 選取相關的 Microsoft 合作夥伴合約帳單帳戶，然後選取 [**客戶**]。 客戶清單與帳單帳戶相關聯。

在客戶清單中，選取您想要允許的客戶來查看成本。

![選取成本管理中的客戶](./media/get-started-partners/customer-list.png)

在 [**設定**] 下選取 [**原則**]。

針對所選客戶的訂用帳戶相關聯的**Azure 使用**費用，會顯示目前的成本可見度原則。
![原則，以允許客戶查看隨用隨付費用](./media/get-started-partners/cost-management-billing-policies.png)

當原則設定為 [**否**] 時，Azure 成本管理不適用於與客戶相關聯的訂用帳戶使用者。 除非由合作夥伴啟用，否則所有訂用帳戶使用者的成本可見度原則預設為停用。

當 [成本原則] 設定為 **[是]** 時，與客戶租使用者相關聯的訂用帳戶使用者可以查看隨用隨付費率的使用費用。

若已啟用成本可見度原則，所有具有訂用帳戶使用量的服務都會以隨用隨付費率顯示成本。 保留使用量會以零費用顯示實際和分攤成本。 購買和權利不會與特定訂用帳戶相關聯。 因此，購買不會顯示在訂用帳戶範圍。

若要查看客戶租使用者的成本，請開啟 [**成本管理 + 帳單**]，然後選取相關的 Microsoft 合作夥伴合約帳單帳戶。

![選取帳單帳戶](./media/get-started-partners/select-billing-account.png)

在 [**帳單**] 底下，選取 [ **Azure**訂用帳戶]，然後選取一個客戶。

![選取 Azure 訂用帳戶客戶](./media/get-started-partners/subscriptions-select-customer.png)

選取 [**成本分析**] 並開始審查成本。
成本分析、預算和警示適用于訂用帳戶和資源群組 RBAC 範圍，以隨用隨付費率為基礎的成本。

![以客戶身分查看成本分析 ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

在 RBAC 範圍中，保留實例的分攤觀點和實際成本會顯示零的費用。 保留實例成本只會顯示在進行購買的計費範圍內。

## <a name="analyze-costs-in-cost-analysis"></a>在成本分析中分析成本

具有合作夥伴租使用者中計費範圍存取權的合作夥伴，可以針對特定客戶或發票，探索並分析客戶在成本分析中的已開票成本。 在[成本分析](quick-acm-cost-analysis.md)視圖中，您也可以[儲存視圖](quick-acm-cost-analysis.md#saving-and-sharing-customized-views)，並將資料匯出至[CSV 和 PNG](quick-acm-cost-analysis.md#automation-and-offline-analysis)檔案。

具有客戶租使用者中訂用帳戶存取權的 RBAC 使用者也可以分析客戶租使用者中訂閱的零售成本、儲存視圖，以及將資料匯出至 CSV 和 PNG 檔案。

您可以使用 [成本分析] 中的 [篩選] 和 [群組依據] 功能，依多個欄位來分析成本。 下一節會顯示合作夥伴特定的欄位。

## <a name="data-fields"></a>資料欄位

在使用量詳細資料檔和成本管理 Api 中可找到下列資料欄位。 如果可以使用，則會顯示合作夥伴中心對等資訊。 對於下列粗體欄位，合作夥伴可以使用 [成本分析] 中的 [篩選] 和 [群組依據] 功能，依多個欄位來分析成本。 粗體欄位僅適用于合作夥伴支援的 Microsoft 客戶合約。

| **欄位名稱** | **說明** | **合作夥伴中心對等** |
| --- | --- | --- |
| invoiceId | 針對特定交易顯示在發票上的發票識別碼。 | 顯示交易的發票號碼。 |
| previousInvoiceID | 參考原始發票會有退款（負成本）。 只有在有退款時才會填入。 | N/A |
| billingAccountName | 代表合作夥伴之帳單帳戶的名稱。 它會在上架至 Microsoft 客戶合約的客戶和已進行權利購買的 CSP 客戶（如 SaaS、Azure Marketplace 和保留專案）中，產生所有成本。 | N/A |
| billingAccountID | 代表合作夥伴之帳單帳戶的識別碼。 | MCAPI 合作夥伴商務根識別碼。 用於要求中，但未包含在回應中。|
| billingProfileID | 帳單設定檔的識別碼，會在上架至 Microsoft 客戶合約的客戶與已進行權利購買的 CSP 客戶（例如 SaaS、Azure Marketplace 和）之間，以單一計費貨幣來分組發票的成本。能. | MCAPI 合作夥伴計費群組識別碼。 用於要求中，但未包含在回應中。 |
| billingProfileName | 帳單設定檔的名稱，會在上架至 Microsoft 客戶合約的客戶與已進行權利購買的 CSP 客戶（例如 SaaS、Azure Marketplace 和）之間，以單一計費貨幣來分組發票的成本。能. | N/A |
| invoiceSectionName | 在發票中計費的專案名稱。 不適用於合作夥伴所上架的 Microsoft 客戶合約。 | N/A |
| invoiceSectionID | 要在發票中計費之專案的識別碼。 不適用於合作夥伴所上架的 Microsoft 客戶合約。 | N/A |
| **CustomerTenantID** | 客戶訂用帳戶的 Azure Active Directory 租使用者識別碼。 | 客戶的組織識別碼-客戶的 Azure Active Directory TenantID。 |
| **CustomerName** | 客戶訂用帳戶的 Azure Active Directory 租使用者名稱。 | 客戶的組織名稱，如合作夥伴中心所示。 將發票與系統資訊協調的重要事項。 |
| **CustomerTenantDomainName** | 客戶訂用帳戶之 Azure Active Directory 租使用者的功能變數名稱。 | 客戶 Azure Active Directory 租使用者網域。 |
| **PartnerTenantID** | 合作夥伴的 Azure Active Directory 租使用者識別碼。 | 合作夥伴 Azure Active Directory 名為合作夥伴識別碼的租使用者識別碼（以 GUID 格式）。 |
| **PartnerName** | 合作夥伴 Azure Active Directory 租使用者的名稱。 | 合作夥伴名稱。 |
| **ResellerMPNID** | 與訂用帳戶相關聯之轉售商的 MPNID。 | 訂用帳戶的轉售商記錄 MPN 識別碼。 目前的活動無法使用。 |
| costCenter | 與訂用帳戶相關聯的成本中心。 | N/A |
| billingPeriodStartDate | 計費循環開始日期，如發票所示。 | N/A |
| billingPeriodEndDate | 計費循環結束日期，如發票所示。 | N/A |
| servicePeriodStartDate | 評等計費期間的開始日期。 Azure 服務的價格會在評等期間決定。 | 合作夥伴中心內的 ChargeStartDate。 計費循環開始日期，但向先前的計費週期中呈現先前不收費潛在使用量資料的日期除外。 時間一律是一天的開始時間 (0:00)。 |
| servicePeriodEndDate | 服務使用量針對費用進行評分時的結束日期。 Azure 服務的價格取決於評等期間。 | N/A |
| date | 針對 Azure 耗用量資料，它會以分級顯示使用量的日期。 針對 [保留實例]，它會顯示 [已購買] 日期。 針對週期性費用和一次費用（例如 Marketplace 和支援），它會顯示購買日期。 | N/A |
| productID | 依耗用量或購買而產生的產品識別碼。 這是 productID 和 SKuID 的串連索引鍵，如合作夥伴中心所示。 | 產品的識別碼。 |
| product | 依耗用量或購買而產生的產品名稱（如發票所示）。 | 目錄中的產品名稱。 |
| serviceFamily | 顯示購買或收費產品的服務系列。 例如，儲存體或計算。 | N/A |
| productOrderID | 訂用帳戶所屬資產或 Azure 方案名稱的識別碼。 例如，Azure 方案。 | N/A |
| productOrderName | 訂用帳戶所屬的 Azure 方案名稱。 例如，Azure 方案。 | N/A|
| consumedService | 傳統 EA 使用量詳細資料中使用的已取用服務（舊版分類法）。 | [合作夥伴中心] 中顯示的服務。 例如，Microsoft 儲存體、Microsoft Compute 和 microsoft.operationalinsights。 |
| meterID | 測量耗用量的計量識別碼。 | 已使用之計量的識別碼。 |
| meterName | 識別測量耗用量的計量名稱。 | 已使用計量的名稱。 |
| meterCategory | 識別最上層服務的使用方式。 | 使用的最上層服務。 |
| meterSubCategory | 定義可能會影響費率的 Azure 服務類型或子類別。 | 可能會影響費率的 Azure 服務類型。|
| meterRegion | 針對根據資料中心位置定價的某些服務，識別資料中心的位置。 | 適用且已填入之服務的資料中心地區位置。 |
| 訂用帳戶識別碼 | Microsoft 針對 Azure 訂用帳戶所產生的唯一識別碼。 | N/A |
| subscriptionName | Azure 訂用帳戶的名稱。 | N/A |
| 條款 | 顯示供應項目有效性的詞彙。 例如，保留實例會顯示保留實例的每年12個月。 針對一次性購買或週期性購買，此一詞彙會針對 SaaS、Azure Marketplace 和支援顯示一個月。 不適用於 Azure 耗用量。 | N/A |
| publisherType （firstParty，thirdPartyReseller，thirdPartyAgency） | 發行者的類型，可將發行者識別為第一方、協力廠商轉銷商或協力廠商機關。 | N/A |
| partNumber | 未使用的保留實例和 Azure Marketplace 服務的元件編號。 | N/A |
| publisherName | 服務發行者的名稱，包括 Microsoft 或協力廠商發行者。 | 產品發行者的名稱。|
| reservationId | 購買之保留實例的識別碼。 | N/A |
| reservationName | 保留實例的名稱。 | N/A |
| reservationOrderId | 保留實例的「訂單」。 | N/A |
| frequency | 保留實例的付款頻率。 | N/A |
| resourceGroup | 用於生命週期資源管理的 Azure 資源組名。 | 資源群組的名稱。 |
| instanceID （或） ResourceID | 資源實例的識別碼。 | 顯示為包含完整資源屬性的 ResourceURI。 |
| resourceLocation | 資源位置的名稱。 | 資源的位置。 |
| 位置 | 資源的正規化位置。 | N/A |
| effectivePrice | 服務的有效單位價格（以定價貨幣表示）。 產品、服務系列、計量及供應專案都是唯一的。 在計費帳戶的價位表中搭配定價使用。 當有階層式定價或包含的數量時，會顯示耗用量的混合價格。 | 進行調整後的單位價格。 |
| 數量 | 已購買或耗用的測量數量。 計費期間所使用的計量數量。 | 單位數。 請確定它符合您在對帳期間計費系統中的資訊。 |
| unitOfMeasure | 識別服務的計費單位。 例如，GB 和小時。 | 識別服務的計費單位。 例如，GB、小時和10000秒。 |
| pricingCurrency | 定義單位價格的貨幣。 | 價格清單中的貨幣。|
| billingCurrency | 定義計費成本的貨幣。 | 客戶地理區域的貨幣。 |
| chargeType | 定義成本在 Azure 成本管理中所代表的費用類型，例如購買和退款。 | 費用或調整的類型。 目前的活動無法使用。 |
| costinBillingCurrency | 計費貨幣中的稅金前的 ExtendedCost 或混合成本。 | N/A |
| costinPricingCurrency | 使用定價貨幣來與價格相互關聯的 ExtendedCost 或混合成本。 | N/A |
| **costinUSD** | 以美元為單位的預估 ExtendedCost 或混合成本。 | N/A |
| **paygCostInBillingCurrency** | 如果定價是零售價格，則會顯示成本。 以計費貨幣顯示隨用隨付價格。 僅適用于 RBAC 範圍。 | N/A |
| **paygCostInUSD** | 如果定價是零售價格，則會顯示成本。 顯示隨用隨付價格，以美元為單位。 僅適用于 RBAC 範圍。 | N/A |
| exchangeRate | 用來從定價貨幣轉換為計費貨幣的匯率。 | 在合作夥伴中心稱為 PCToBCExchangeRate。 用來計費貨幣匯率的定價貨幣。|
| exchangeRateDate | 用來從定價貨幣轉換成計費貨幣的匯率日期。 | 在合作夥伴中心稱為 PCToBCExchangeRateDat。 計費貨幣匯率日期的定價貨幣。|
| isAzureCreditEligible | 指出成本是否符合 Azure 點數的付款資格。 | N/A |
| serviceInfo1 | 舊版欄位，可擷取選擇性的服務特定中繼資料。 | 內部 Azure 服務中繼資料。 |
| serviceInfo2 | 舊版欄位，可擷取選擇性的服務特定中繼資料。 | 服務資訊。 例如，虛擬機器的映像類型和 ExpressRoute 的 ISP 名稱。|
| additionalInfo | 服務專屬的中繼資料。 例如，虛擬機器的影像類型。 | 其他資料行中未涵蓋的任何其他資訊。 服務特定的中繼資料。 例如，虛擬機器的影像類型。|
| tags | 您指派給計量的標記。 使用標記為帳單記錄分組。 例如，您可以使用標記，根據使用計量的部門散發成本。 | 客戶新增的標記。|
| **partnerEarnedCreditRate** | 當合作夥伴以合作夥伴系統管理員連結存取為基礎時，適用的折扣率。 | 合作夥伴獲點數（PEC）的速率。 例如，0% 或15%。 |
| **partnerEarnedCreditApplied** | 指出是否已套用合作夥伴的點數。 | N/A |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>觀看合作夥伴的點數（PEC）資源成本

在 Azure 成本管理中，合作夥伴可以使用成本分析來查看收到 PEC 權益的成本。

在 Azure 入口網站中，登入合作夥伴租使用者，然後選取 **成本管理 + 帳單**。 在 [**成本管理**] 底下，選取 [**成本分析**]。

[成本分析] 視圖會顯示合作夥伴的帳單帳戶費用。 選取合作夥伴、特定客戶或帳單設定檔所需的**範圍**，以協調發票。

在環圈圖中，選取下拉式清單，然後選取 [ **PartnerEarnedCreditApplied** ] 以深入瞭解 [PEC 成本]。

![示範如何觀看合作夥伴的點數的範例](./media/get-started-partners/cost-analysis-pec1.png)

當**PartnerEarnedCreditApplied**屬性為_True_時，相關聯的成本就會有合作夥伴取得系統管理員存取權的權益。

當**PartnerEarnedCreditApplied**屬性為_False_時，相關聯的成本不符合所需的信用額度資格。 或者，購買的服務並不符合合作夥伴的點數。

服務使用量資料通常需要8-24 小時才會出現在成本管理中。 如需詳細資訊，請參閱[使用量資料更新頻率變化](understand-cost-mgt-data.md#usage-data-update-frequency-varies)。 在 Azure 成本管理中，從存取的時間開始，PEC 點數會出現在48小時內。


您也可以使用 [**群組依據**] 選項，依**PartnerEarnedCreditApplied**屬性進行分組和篩選。 使用選項來檢查執行和沒有 PEC 的成本。

![依據合作夥伴取得的信用額度來分組或篩選](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>將成本資料匯出至 Azure 儲存體

具有合作夥伴租使用者中計費範圍存取權的合作夥伴，可以將其成本和使用量資料匯出至 Azure 儲存體的 blob。 Blob 必須位於合作夥伴租使用者中的訂用帳戶，而不是共用服務訂用帳戶或客戶的訂用帳戶。 若要啟用成本資料匯出，建議您在合作夥伴租使用者中設定獨立的隨用隨付訂用帳戶，以裝載匯出的成本資料。 系統會在隨用隨付訂用帳戶中託管的 Azure 儲存體 blob 上建立匯出儲存體帳戶。 根據夥伴建立匯出的範圍，相關聯的資料會定期自動匯出至儲存體帳戶。

具有訂用帳戶之 RBAC 存取權的使用者，也可以將成本資料匯出至客戶租使用者中任何訂用帳戶所裝載的 Azure 儲存體 blob。

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>在合作夥伴租使用者或客戶租使用者中建立匯出

在 Azure 入口網站中，登入合作夥伴租使用者或客戶租使用者，然後選取 **成本管理 + 帳單**。 選取適當的範圍，例如 Microsoft 合作夥伴合約帳單帳戶，然後選取 [**成本分析**]。 當頁面載入時，選取 [**匯出**]。 選取 [排程匯出] 底下的 [**查看所有匯出**]。

![選取匯出和查看所有匯出](./media/get-started-partners/export01.png)

接下來，選取 [**新增**] 並輸入名稱，然後選取匯出類型。 選取 [**儲存體**] 索引標籤，然後輸入必要的資訊。

![[新增匯出] 和 [選取儲存體] 索引標籤](./media/get-started-partners/export02.png)

當您在合作夥伴租使用者中建立匯出時，請選取合作夥伴租使用者中的隨用隨付訂用帳戶。 使用該訂閱建立 Azure 儲存體帳戶。

針對客戶租使用者中的 RBAC 使用者，選取客戶租使用者中的訂用帳戶。 使用訂用帳戶建立 Azure 儲存體帳戶。

檢查內容，然後選取 [**建立**] 以排程匯出。

若要確認匯出清單中的資料，請選取儲存體帳戶名稱。 在 [儲存體帳戶] 頁面上，選取 [**容器**]，然後選取容器。 流覽至對應的資料夾，然後選取 CSV 檔案。 選取 [**下載**] 以取得 CSV 檔案並加以開啟。 匯出的資料會類似成本資料，類似于 Azure 入口網站的使用量詳細資料。

![匯出資料的範例](./media/get-started-partners/example-export-data.png)

## <a name="cost-management-rest-apis"></a>成本管理 REST Api

合作夥伴和客戶可以使用下列各節中所述成本管理 Api 來執行一般工作。

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Azure 成本管理 Api-直接與間接提供者

具有合作夥伴租使用者中計費範圍存取權的合作夥伴，可以使用下列 Api 來查看發票成本。

在訂用帳戶範圍中的 Api 可由合作夥伴呼叫，而不論其是否具有訂用帳戶的存取權，都可以使用此原則。 具有訂用帳戶存取權的其他使用者（例如客戶或轉銷商），只有在合作夥伴啟用客戶租使用者的成本原則之後，才能呼叫 Api。


#### <a name="to-get-a-list-of-billing-accounts"></a>取得帳單帳戶的清單

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>取得客戶清單

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>取得訂用帳戶清單

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>取得一段時間的發票清單

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

API 呼叫會傳回發票陣列，其中包含與下列 JSON 程式碼類似的元素。

```
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/{billingAccountID}/billingProfiles/{BillingProfileID}/invoices/{InvoiceID}",
      "name": "{InvoiceID}",
      "properties": {
        "amountDue": {
          "currency": "USD",
          "value": x.xx
        },
        ...
    }
```

使用上述傳回的識別碼域值，並將它取代為範圍，以查詢使用量詳細資料。

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

此範例會傳回與特定發票相關聯的使用量記錄。


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>若要取得客戶的原則以查看成本

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>設定客戶的原則以查看成本

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>若要取得帳單帳戶的 Azure 服務使用量

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>下載客戶的 Azure 服務使用量

下列 get 呼叫是非同步作業。

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

呼叫回應中所傳回的 `Location` URI，以檢查作業狀態。 當狀態為 [*已完成*] 時，[`downloadUrl`] 屬性會包含一個連結，供您用來下載產生的報告。


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>取得或下載所使用 Azure 服務的價位表

首先，請使用下列文章。

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

然後，呼叫非同步作業屬性值。 例如：

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
前述 get 呼叫會傳回包含價位表的下載連結。


#### <a name="to-get-aggregated-costs"></a>取得匯總成本

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>建立合作夥伴的預算

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>為客戶建立預算

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>刪除預算

```
DELETE
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>後續步驟
- [開始分析](quick-acm-cost-analysis.md)成本管理中的成本
- 在成本管理中[建立和管理預算](tutorial-acm-create-budgets.md)
