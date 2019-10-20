---
title: 開始使用 Azure 成本管理合作夥伴
description: 本文說明合作夥伴如何使用 Azure 成本管理功能，以及如何為他們的客戶啟用成本管理存取權。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 661e1b6e3811ff2b5ae25e4fd59764a69e9ebe48
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596664"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>開始使用 Azure 成本管理合作夥伴

Azure 成本管理原本就可供已上架客戶至 Microsoft 客戶合約的合作夥伴使用。 本文說明合作夥伴如何使用[Azure 成本管理](https://docs.microsoft.com/azure/cost-management/)功能。 它也會說明合作夥伴如何為其客戶啟用成本管理存取。 客戶可以在其 CSP 合作夥伴啟用時，使用成本管理功能。

CSP 合作夥伴會使用成本管理來執行下列動作：

- 瞭解發票成本，並將成本與客戶、訂用帳戶、資源群組和服務產生關聯。
- 使用可依客戶、訂用帳戶、資源群組、資源、計量、服務和許多其他維度來分析成本的功能，以直覺的方式瞭解 Azure 的成本[分析](quick-acm-cost-analysis.md)成本。
- 在成本分析中，查看已套用合作夥伴獲額點數（PEC）的資源成本。
- 當成本超過預算時，使用程式設計[預算](tutorial-acm-create-budgets.md)和警示來設定通知和自動化。
- 啟用可提供客戶存取成本管理資料的 Azure Resource Manager 原則。 接著，客戶可以使用[隨用隨付費率](https://azure.microsoft.com/pricing/calculator/)來查看其訂用帳戶的耗用量成本資料。

以下範例顯示所有客戶的成本。
![Example 顯示所有客戶的成本 ](./media/get-started-partners/customer-costs1.png)

以下範例顯示單一客戶的成本。
![Example 顯示單一客戶 ](./media/get-started-partners/customer-costs2.png) 的成本

Azure 成本管理中提供的所有功能也適用于 REST Api。 使用 Api 將成本管理工作自動化。

## <a name="prerequisites"></a>必要條件

Azure 成本管理需要您的帳單帳戶或訂用帳戶的讀取權限。 您可以在資源的任何層級上授與存取權，從帳單帳戶或管理群組到您管理應用程式的個別資源群組。 如需啟用和指派帳單帳戶 Azure 成本管理存取權的詳細資訊，請參閱[指派使用者角色和許可權](/partner-center/permissions-overview)。 **全域管理員**和**管理員代理程式**角色可以管理帳單帳戶的成本。

若要查看支援帳戶類型的完整清單，請參閱[瞭解成本管理資料](understand-cost-mgt-data.md)。


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
- Customer
- Subscription
- Resource group
- 資源
- Azure 服務
- 計量器
- ResellerMPNID

### <a name="customer-scope"></a>客戶範圍

合作夥伴使用範圍來管理與上架至 Microsoft 客戶合約之客戶相關聯的成本。 此範圍可讓合作夥伴查看特定客戶的預付稅成本。 您也可以篩選特定訂用帳戶、資源群組或資源的稅前成本。

客戶範圍不包含目前 CSP 供應專案的客戶。 此範圍僅包含擁有 Microsoft 客戶合約的客戶。 目前 CSP 供應專案的權利成本（而非 Azure 使用量）可在您套用客戶篩選時，于帳單帳戶和帳單設定檔範圍內取得。

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
![Policy，以允許客戶查看隨用隨付費用 ](./media/get-started-partners/cost-management-billing-policies.png)

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

| **欄位** | **說明** |
| --- | --- |
| PartnerTenantID | 合作夥伴的 Azure Active Directory 租使用者識別碼 |
| PartnerName | 合作夥伴 Azure Active Directory 租使用者的名稱 |
| CustomerTenantID | 客戶訂用帳戶的 Azure Active Directory 租使用者識別碼 |
| CustomerName | 包含客戶訂用帳戶的 Azure Active Directory 租使用者名稱 |
| ResellerMPNID | 與訂用帳戶相關聯之轉售商的 MPNID |
| 訂用帳戶識別碼 | Microsoft 針對 Azure 訂用帳戶所產生的唯一識別碼 |
| subscriptionName | Azure 訂用帳戶的名稱 |
| billingProfileID | 帳單設定檔的識別碼。 它會以單一計費貨幣，將整個發票的成本群組在客戶之間。
| invoiceID | 在特定交易出現的發票上的發票識別碼 |
| resourceGroup | Azure 資源群組的名稱。 用於資源生命週期管理。 |
| partnerEarnedCreditRate | 當合作夥伴以合作夥伴管理員連結存取為基礎時，所套用的折扣率（PEC）。 |
| partnerEarnedCreditApplied | 指出是否已套用合作夥伴的點數。 |

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

合作夥伴和客戶可以使用下列各節中所述成本管理 Api 來執行一般工作。

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Azure 成本管理 Api-直接與間接提供者

具有合作夥伴租使用者中計費範圍存取權的合作夥伴，可以使用下列 Api 來查看發票成本。

在訂用帳戶範圍中的 Api 可由合作夥伴呼叫，而不論其是否具有訂用帳戶的存取權，都可以使用此原則。 具有訂用帳戶存取權的其他使用者（例如客戶或轉銷商），只有在合作夥伴啟用客戶租使用者的成本原則之後，才能呼叫 Api。


#### <a name="to-get-a-list-of-billing-accounts"></a>取得帳單帳戶的清單

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>取得客戶清單

```
armclient get "providers/Microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers?api-version=2019-10-01-preview"
```
#### <a name="to-get-a-list-of-subscriptions"></a>取得訂用帳戶清單

```
armclient get "/providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/billingSubscriptions?api-version=2019-10-01-preview"
```

#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>若要取得客戶的原則以查看成本

```
armclient get "providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/policies/default?api-version=2019-10-01-preview"
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>設定客戶的原則以查看成本

```
armclient put "providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/policies/default?api-version=2019-10-01-preview" @policy.json
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>若要取得帳單帳戶的 Azure 服務使用量

```
armclient GET /providers/Microsoft.Billing/BillingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>下載客戶的 Azure 服務使用量

下列 get 呼叫是非同步作業。

```
armclient get providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

呼叫回應中所傳回的 `Location` URI，以檢查作業狀態。 當狀態為 [*已完成*] 時，[`downloadUrl`] 屬性會包含一個連結，供您用來下載產生的報告。


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>取得或下載所使用 Azure 服務的價位表

首先，請使用下列文章。

```
armclient post "/providers/Microsoft.Billing/BillingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/billingProfiles/YYYY-YYYY-YYY-YYYY-YYY/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

然後，呼叫非同步作業屬性值。 例如：

```
armclient get "providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/billingProfiles/YYYY-YYYY-YYY-YYYY-YYY/pricesheetDownloadOperations/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX?sessiontoken=0:11186&api-version=2019-10-01-preview"
```
前述 get 呼叫會傳回包含價位表的下載連結。

#### <a name="to-get-customer-costs-for-the-last-two-months-sorted-by-month"></a>若要取得過去兩個月的客戶成本（依月份排序）

```
armclient post providers/microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryCustomer.json
```

#### <a name="to-get-azure-subscription-costs-for-the-last-two-months-sorted-by-month"></a>若要取得過去兩個月的 Azure 訂用帳戶成本，依月份排序

```
armclient post providers/microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQuerySubscription.json
```

#### <a name="to-get-daily-costs-for-the-current-month"></a>取得當月的每日成本

```
armclient post providers/microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

#### <a name="create-a-budget-for-a-partner"></a>建立合作夥伴的預算

```
armclient put providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01 @budgetCreate.json
```


#### <a name="create-a-budget-for-a-customer"></a>為客戶建立預算

```
armclient put providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/providers/Microsoft.Consumption/budgets/test-partner-demo?api-version=2019-10-01 @budgetCreate.json
```
#### <a name="delete-a-budget"></a>刪除預算

```
armclient delete providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```


## <a name="next-steps"></a>後續步驟
- [開始分析](quick-acm-cost-analysis.md)成本管理中的成本
- 在成本管理中[建立和管理預算](tutorial-acm-create-budgets.md)
