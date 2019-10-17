---
title: 開始使用 Azure 成本管理合作夥伴
description: 本文說明合作夥伴如何使用 Azure 成本管理功能，以及如何為他們的客戶啟用成本管理存取權。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 9d95e23cf92c7ee98291831d60088d610c3e5c52
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377690"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>開始使用 Azure 成本管理合作夥伴

Azure 成本管理原本就可供已上架客戶至 Microsoft 客戶合約的合作夥伴使用。 本文說明合作夥伴如何使用[Azure 成本管理](https://docs.microsoft.com/azure/cost-management/)功能。 它也會說明合作夥伴如何為其客戶啟用成本管理存取。 CSP 客戶在其 CSP 合作夥伴啟用時，可以使用成本管理功能。

CSP 合作夥伴會使用成本管理來執行下列動作：

- 瞭解發票成本，並將成本與客戶、訂用帳戶、資源群組和服務產生關聯。
- 使用可依客戶、訂用帳戶、資源群組、資源、計量、服務和許多其他維度來分析成本的功能，以直覺的方式瞭解 Azure 的成本[分析](quick-acm-cost-analysis.md)成本。
- 在成本分析中，查看已套用合作夥伴獲額點數（PEC）的資源成本。
- 當成本超過預算時，使用程式設計[預算](tutorial-acm-create-budgets.md)和警示來設定通知和自動化。
- 啟用可提供客戶存取成本管理資料的 Azure Resource Manager 原則。 接著，客戶可以使用[隨用隨付費率](https://azure.microsoft.com/pricing/calculator/)來查看其訂用帳戶的耗用量成本資料。

Azure 成本管理中提供的所有功能也適用于 REST Api。 使用 Api 將成本管理工作自動化。

## <a name="prerequisites"></a>必要條件

Azure 成本管理需要您的帳單帳戶或訂用帳戶的讀取權限。 您可以在資源的任何層級上授與存取權，從帳單帳戶或管理群組到您管理應用程式的個別資源群組。 為了讓訂用帳戶使用者可以查看定價和成本，您的帳單帳戶必須啟用「查看費用」的存取權。 如需啟用和指派存取權給 Azure 成本管理的詳細資訊，請參閱[指派資料的存取權](assign-access-acm-data.md)。 若要查看支援帳戶類型的完整清單，請參閱[瞭解成本管理資料](understand-cost-mgt-data.md)。


## <a name="how-cost-management-uses-scopes"></a>成本管理如何使用範圍

範圍可供您管理帳單資料、擁有付款特有的角色、查看發票，以及進行一般帳戶管理。 計費和帳戶角色會與用於資源管理（使用 RBAC）的範圍分開管理。 為了清楚區分不同範圍的意圖，包括存取控制差異，它們分別稱為計費範圍和 RBAC 範圍。

若要瞭解計費範圍和 RBAC 範圍，以及成本管理如何與範圍搭配運作，請參閱[瞭解並使用範圍](understand-work-scopes.md)。

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>使用合作夥伴租使用者計費範圍來管理成本

在您將客戶上架至 Microsoft 客戶合約之後，您的租使用者中會提供下列_計費範圍_。 使用範圍來管理成本管理中的成本。

### <a name="billing-account-scope"></a>帳單帳戶範圍

使用帳單帳戶範圍來查看您所有客戶和帳單設定檔的預付稅成本。 您也可以在 Microsoft 客戶合約上，為客戶查看以耗用量為基礎之產品的發票成本。 針對購買的產品，也會針對 Microsoft 客戶合約和 CSP 供應專案的客戶顯示發票成本。 目前，要在範圍中查看成本的預設貨幣是美元。 範圍的預算設定也是美元。

無論客戶的付費貨幣為何，合作夥伴都會使用範圍來設定預算，並管理其客戶、訂用帳戶、資源和資源群組之間的成本（以美元為單位）。

合作夥伴也會在成本分析視圖中，以特定計費貨幣來篩選成本。 選取 [**實際成本**] 清單，以查看支援的客戶帳單貨幣成本。

![顯示貨幣的實際成本選擇範例](./media/get-started-partners/actual-cost-selector.png)

使用計費範圍中的[分攤成本視圖](quick-acm-cost-analysis.md#customize-cost-views)，以在保留期限內查看保留實例分攤成本。

### <a name="billing-profile-scope"></a>帳單設定檔範圍

使用帳單設定檔範圍，針對發票中包含的所有產品和訂用帳戶，在您所有的客戶上，以帳單貨幣來查看預付稅成本。 您可以使用**InvoiceID**篩選，針對特定發票的帳單設定檔中的成本進行篩選。 篩選會顯示特定發票的耗用量和產品購買成本。 您也可以篩選發票上特定客戶的成本，以查看稅前成本。

在您將客戶上線至 Microsoft 客戶合約之後，您會收到客戶發票，其中顯示權利和購買產品的費用，例如 SaaS、Azure Marketplace 和保留區。 以相同的帳單貨幣計費時，發票也會顯示新的 Microsoft 客戶合約中未包含的客戶費用。

為協助根據客戶發票來協調費用，計費設定檔範圍可讓您查看針對客戶的發票所產生的所有成本。 就像發票，範圍會針對新的 Microsoft 客戶合約中的每個客戶顯示成本。 範圍也會顯示仍在目前 CSP 供應專案中的客戶權利產品的每個費用。

帳單設定檔和帳單帳戶範圍是顯示權利和購買型產品費用的唯一一種。

帳單設定檔會定義發票中包含的訂閱。 帳單設定檔是與 enterprise 合約註冊相同的功能。 註冊是產生發票的範圍。 同樣地，不以使用量為基礎的購買（例如 Azure Marketplace 和保留）僅適用于帳單設定檔範圍。

目前，客戶的帳單貨幣是在帳單設定檔範圍中查看成本時的預設貨幣。 計費設定檔範圍中設定的預算以計費貨幣為單位。

合作夥伴可以使用範圍來協調發票。 而且，他們會使用範圍來設定的帳單貨幣預算：

- 特定篩選的發票
- Customer
- Subscription
- Resource group
- 資源
- Azure 服務
- 計量器
- ResellerMPNID

### <a name="customer-scope"></a>客戶範圍

合作夥伴使用範圍來管理與上架至 Microsoft 客戶合約之客戶相關聯的成本。 此範圍可讓合作夥伴查看特定客戶的預付稅成本。 您也可以篩選特定訂用帳戶、資源群組或資源的稅前成本。

客戶範圍不包含目前 CSP 供應專案的客戶。 目前 CSP 供應專案的權利成本（而非 Azure 使用量）可在您套用客戶篩選時，于帳單帳戶和帳單設定檔範圍內取得。

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>合作夥伴存取成本管理中的計費範圍

只有具備**全域管理員**和系統**管理員代理程式**角色的使用者，才能夠直接在合作夥伴的 Azure 租使用者中管理和查看帳單帳戶、帳單設定檔和客戶的成本。 如需合作夥伴中心角色的詳細資訊，請參閱[指派使用者角色和許可權](/partner-center/permissions-overview)。

### <a name="enable-cost-management-in-the-customer-tenant"></a>在客戶租使用者中啟用成本管理

合作夥伴可以在客戶上架至 Microsoft 客戶合約之後，啟用成本管理的存取權。 接著，合作夥伴可以啟用原則，讓客戶能夠以隨用隨付零售費率來查看其計算成本。 成本會針對其在 RBAC 訂用帳戶和資源群組範圍內使用的使用量，顯示在客戶的帳單貨幣中。

當合作夥伴啟用了成本可見度的原則時，任何擁有訂用帳戶 Azure Resource Manager 存取權的使用者都可以使用隨用隨付費率來管理和分析成本。 對 Azure 訂用帳戶具有適當 RBAC 存取權的轉銷商和客戶有效，可以查看成本。

不論原則為何，如果合作夥伴有權存取訂用帳戶和資源群組，也可以查看成本。

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>啟用原則以查看 Azure 使用量費用

合作夥伴會使用下列資訊來啟用原則，以查看其客戶的 Azure 使用量費用。

在 Azure 入口網站中，登入合作夥伴租使用者，然後按一下 **成本管理 + 帳單**。 選取帳單帳戶，然後按一下 [**客戶**]。 客戶清單與帳單帳戶相關聯。

在客戶清單中，選取您想要允許的客戶來查看成本。

![選取成本管理中的客戶](./media/get-started-partners/customer-list.png)

在 [**設定**] 底下，按一下 [**原則**]。

針對所選客戶的訂用帳戶相關聯的**Azure 使用**費用，會顯示目前的成本可見度原則。
@no__t 0Policy，以允許客戶查看隨用隨付費用 @ no__t-1

當原則設定為 [**否**] 時，Azure 成本管理不適用於與客戶相關聯的訂用帳戶使用者。 除非由合作夥伴啟用，否則所有訂用帳戶使用者的成本可見度原則預設為停用。

當 [成本原則] 設定為 **[是]** 時，與客戶租使用者相關聯的訂用帳戶使用者可以查看隨用隨付費率的使用費用。

若已啟用成本可見度原則，所有具有訂用帳戶使用量的服務都會以隨用隨付費率顯示成本。 保留使用量會以零費用顯示實際和分攤成本。 購買和權利不會與特定訂用帳戶相關聯。 因此，購買不會顯示在訂用帳戶範圍。

若要查看客戶租使用者的成本，請開啟成本管理 + 帳單，然後按一下 [帳單帳戶]。 在帳單帳戶清單中，按一下帳單帳戶。

![選取帳單帳戶](./media/get-started-partners/select-billing-account.png)

在 [**帳單**] 底下，按一下 [ **Azure**訂用帳戶]，然後按一下 [客戶]。

![選取 Azure 訂用帳戶客戶](./media/get-started-partners/subscriptions-select-customer.png)

按一下 [**成本分析**] 並開始審查成本。
成本分析、預算和警示現在適用于訂用帳戶和資源群組 RBAC 範圍，以隨用隨付費率為基礎的成本。

![以客戶身分查看成本分析 ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

在 RBAC 範圍中，保留實例的分攤觀點和實際成本會顯示零的費用。 保留實例成本只會顯示在進行購買的計費範圍內。

## <a name="analyze-costs-in-cost-analysis"></a>在成本分析中分析成本

合作夥伴可以針對特定客戶或發票，探索並分析客戶在成本分析中的成本。 [篩選] 和 [群組依據] 功能可讓您依多個欄位來分析成本，包括：

| **欄位** | **說明** | **合作夥伴中心中的對等資料行** |
| --- | --- | --- |
| PartnerTenantID | 合作夥伴的 Azure Active Directory 租使用者識別碼 | 夥伴 Azure Active Directory TenantID，稱為「合作夥伴識別碼」。 以 GUID 格式。 |
| PartnerName | 合作夥伴 Azure Active Directory 租使用者的名稱 | 合作夥伴名稱 |
| CustomerTenantID | 客戶訂用帳戶的 Azure Active Directory 租使用者識別碼 | 客戶的組織識別碼。 例如，客戶 Azure Active Directory TenantID。 |
| CustomerName | 包含客戶訂用帳戶的 Azure Active Directory 租使用者名稱 | 客戶的組織名稱（如合作夥伴中心所報告）。 將發票與系統資訊協調的重要事項。 |
| ResellerMPNID | 與訂用帳戶相關聯之轉售商的 MPNID | 訂用帳戶之記錄轉銷商的 MPN 識別碼。 目前的活動無法使用。 |
| 訂用帳戶識別碼 | Microsoft 針對 Azure 訂用帳戶所產生的唯一識別碼 | N/A |
| subscriptionName | Azure 訂用帳戶的名稱 | N/A |
| billingProfileID | 帳單設定檔的識別碼。 它會以單一計費貨幣，將整個發票的成本群組在客戶之間。 | MCAPI 合作夥伴計費群組識別碼。 用於 API 要求，但未包含在回應中。 |
| invoiceID | 在特定交易出現的發票上的發票識別碼 | 出現指定交易的發票號碼。 |
| resourceGroup | Azure 資源群組的名稱。 用於資源生命週期管理。 | 資源群組的名稱。 |
| partnerEarnedCreditRate | 當合作夥伴以合作夥伴管理員連結存取為基礎時，所套用的折扣率（PEC）。 | 合作夥伴獲點數（PEC）的速率。 例如，0% 或 15%。 |
| partnerEarnedCreditApplied | 指出是否已套用合作夥伴的點數。 | N/A |

在[成本分析](quick-acm-cost-analysis.md)視圖中，您也可以[儲存視圖](quick-acm-cost-analysis.md#saving-and-sharing-customized-views)，並將資料匯出至[CSV 和 PNG](quick-acm-cost-analysis.md#automation-and-offline-analysis)檔案。

## <a name="view-partner-earned-credit-pec-resource-costs"></a>觀看合作夥伴的點數（PEC）資源成本

在 Azure 成本管理中，合作夥伴可以使用成本分析來查看收到 PEC 權益的成本。

在 Azure 入口網站中，登入合作夥伴租使用者，然後選取 **成本管理 + 帳單**。 在 [**成本管理**] 底下，按一下 [**成本分析**]。

[成本分析] 視圖會顯示合作夥伴的帳單帳戶費用。 選取合作夥伴、特定客戶或帳單設定檔所需的**範圍**，以協調發票。

在環圈圖中，按一下下拉式清單，然後選取 [ **PartnerEarnedCreditApplied** ] 以深入瞭解 [PEC 成本]。

![示範如何觀看合作夥伴的點數的範例](./media/get-started-partners/cost-analysis-pec1.png)

當**PartnerEarnedCreditApplied**屬性為_True_時，相關聯的成本就會有合作夥伴取得系統管理員存取權的權益。

當**PartnerEarnedCreditApplied**屬性為_False_時，相關聯的成本不符合所需的信用額度資格。 或者，購買的服務並不符合合作夥伴的點數。

服務使用量資料通常需要8-24 小時才會出現在成本管理中。 如需詳細資訊，請參閱[使用量資料更新頻率變化](understand-cost-mgt-data.md#usage-data-update-frequency-varies)。 在 Azure 成本管理中，從存取的時間開始，PEC 點數會出現在48小時內。


您也可以使用 [**群組依據**] 選項，依**PartnerEarnedCreditApplied**屬性進行分組和篩選。 使用選項來檢查執行和沒有 PEC 的成本。

![依據合作夥伴取得的信用額度來分組或篩選](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="cost-management-rest-apis"></a>成本管理 REST Api

合作夥伴、間接提供者和客戶可以使用下列各節中所述成本管理 Api 來執行一般工作。

### <a name="azure-cost-management-apis-for-partners"></a>適用于合作夥伴的 Azure 成本管理 Api

具有合作夥伴租使用者中計費範圍存取權的合作夥伴和使用者可以使用下列 Api。

#### <a name="to-get-a-list-of-billing-accounts"></a>取得帳單帳戶的清單

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>取得客戶清單

```
armclient get "providers/Microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers?api-version=2019-10-01-preview"
```
#### <a name="to-get-a-list-of-subscriptions"></a>取得訂用帳戶清單

```
armclient get "/providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/billingSubscriptions?api-version=2019-10-01-preview"
```

#### <a name="to-create-new-subscription"></a>若要建立新的訂用帳戶

```
armclient post "/providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview" @createsub.json -verbose
```

#### <a name="to-get-or-download-usage-for-azure-services"></a>取得或下載 Azure 服務的使用量

```
armclient GET /providers/Microsoft.Billing/BillingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-get-a-list-of-billing-profiles"></a>取得帳單設定檔的清單

```
armclient get "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/billingProfiles?api-version=2019-10-01-preview
```

#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>取得或下載所使用 Azure 服務的價位表

```
armclient post "/providers/Microsoft.Billing/BillingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/BillingProfiles/JUT6-EU3Q-BG7-TGB/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

#### <a name="to-get-customer-costs-for-the-last-two-months-sorted-by-month"></a>若要取得過去兩個月的客戶成本（依月份排序）

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryCustomer.json
```

#### <a name="to-get-azure-subscription-costs-for-the-last-two-months-sorted-by-month"></a>若要取得過去兩個月的 Azure 訂用帳戶成本，依月份排序

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQuerySubscription.json
```

#### <a name="to-get-daily-costs-for-the-current-month"></a>取得當月的每日成本

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>若要取得客戶的原則以查看成本

```
armclient get "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/policies/default?api-version=2019-10-01-preview"
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>設定客戶的原則以查看成本

```
armclient put "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/policies/default?api-version=2019-10-01-preview" @policy.json
```

### <a name="azure-cost-management-apis-for-indirect-providers"></a>間接提供者的 Azure 成本管理 Api

在客戶租使用者中具有 RBAC 範圍存取權的間接提供者可以使用下列 Api。 若要開始使用，請以使用者身分或服務主體登入。

#### <a name="to-get-the-billing-account-information"></a>取得帳單帳戶資訊

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>取得客戶清單

```
armclient get "providers/Microsoft.billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-resellers-associated-with-the-customer"></a>取得與客戶相關聯的轉售商清單

```
armclient get "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db?api-version=2019-10-01-preview&$expand=resellers
```

#### <a name="to-get-a-list-of-subscriptions-with-reseller-information"></a>取得具有轉售商資訊的訂用帳戶清單

```
armclient get "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-create-a-subscription"></a>若要建立訂用帳戶

```
armclient post "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview" @createsub_reseller.json
```

### <a name="azure-cost-management-apis-for-customers"></a>適用于客戶的 Azure 成本管理 Api

客戶使用下列資訊來存取 Api。 若要開始使用，請以使用者身分登入。

#### <a name="to-get-or-download-azure-consumption-usage-information-with-retail-rates"></a>以零售費率取得或下載 Azure 使用量使用量資訊

```
armclient post /subscriptions/66bada28-271e-4b7a-aaf5-c0ead63923d7/providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

## <a name="next-steps"></a>後續步驟
- [開始分析](quick-acm-cost-analysis.md)成本管理中的成本
- 在成本管理中[建立和管理預算](tutorial-acm-create-budgets.md)
