---
title: Enterprise 合約從移轉至 Microsoft 的客戶合約 Api-Azure |Microsoft Docs
description: 這篇文章可協助您了解 Microsoft Enterprise 合約 (EA) 移轉至 Microsoft 客戶協議的後果。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: e4c5607089efb247620766fb311b97cae3772770
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59279868"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Enterprise 合約從遷移到 Microsoft 的客戶合約 Api

這篇文章可協助您了解資料結構、 API 和其他系統整合之間的差異 Enterprise 合約 (EA) 和 Microsoft 客戶協議 (MCA) 帳戶。 Azure 成本管理支援的 Api，這兩種帳戶類型。 檢閱[設定為帳單帳戶](../billing/billing-mca-setup-account.md)Microsoft 客戶合約文件，然後再繼續。

使用現有的 EA 帳戶的組織應該檢閱與設定 MCA 帳戶搭配使用這篇文章。 之前，更新的 EA 帳戶，您必須將從舊的註冊移至新的一些最少的工作。 不過，移轉到 MCA 帳戶需要額外的工作。 額外的工作，是因為在基礎的計費子系統中，變更會影響所有成本相關的 Api 和服務供應項目。

## <a name="mca-apis-and-integration"></a>MCA Api 和整合

MCA Api 和新的整合可讓您：

- 已透過原生的 Azure Api 的完整 API 可用性。
- 在單一的帳單帳戶設定多張發票。
- 存取 Azure 服務使用量、 第三方 Marketplace 使用方式，與 Marketplace 購買項目合併的 API。
- 檢視成本計費 （如同註冊） 的設定檔之間使用 Azure 成本管理。
- 存取新的 Api 顯示成本、 成本超過預先定義的臨界值，且會自動匯出未經處理資料時收到通知。

## <a name="migration-checklist"></a>移轉檢查清單

下列項目協助您轉為 MCA Api。

- 熟悉的新[計費帳戶的 Microsoft 客戶合約](../billing/billing-mca-overview.md)。
- 決定您使用，並請參閱下一節中的哪些會被取代的 Api。
- 您熟悉[Azure Resource Manager REST Api](/rest/api/azure)。
- 如果您未使用 Azure Resource Manager Api，[向 Azure AD 中註冊您的用戶端應用程式](/rest/api/azure/#register-your-client-application-with-azure-ad)。
- 更新任何程式碼來[使用 Azure AD 驗證](/rest/api/azure/#create-the-request)。
- 更新任何程式設計的程式碼來取代 MCA API 呼叫的 EA API 呼叫。
- 更新的錯誤處理使用的新錯誤碼。
- 檢閱其他整合供應項目，例如 Cloudyn 和 Power BI 中，針對其他所需的動作。

## <a name="ea-apis-replaced-with-mca-apis"></a>EA Api 取代 MCA Api

EA Api 會使用 API 金鑰進行驗證和授權。 MCA Api 會使用 Azure AD 驗證。

| 目的 | EA API | MCA API |
| --- | --- | --- |
| 餘額與信用額度 | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| 使用方式 (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 使用量 (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Marketplace 使用量 (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 計費期間 | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| 價位表 | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/…/billingProfiles/…/invoices/… /pricesheet/default/download 格式 = json|csv Microsoft.Billing/billingAccounts/../billingProfiles/../providers/Microsoft.Consumption/pricesheets/download  |
| 保留購買 | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| 保留建議 | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| 保留使用情況 | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup>所提供的 azure 服務和第三方 Marketplace 使用量[使用量詳細資料 API](/rest/api/consumption/usagedetails)。

下列 Api 可用於 MCA 計費帳戶：

| 目的 | Microsoft 的客戶合約 (MCA) API |
| --- | --- |
| 計費帳戶<sup>2</sup> | Microsoft.Billing/billingAccounts |
| 帳單設定檔<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| 發票區段<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| 發票 | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| 計費訂用帳戶 | {scope}/billingSubscriptions |

<sup>2</sup> Api 傳回的物件，也就是的範圍，其中成本管理體驗在 Azure 入口網站和 Api 操作清單。 如需成本管理範圍的詳細資訊，請參閱[了解及使用範圍](understand-work-scopes.md)。

如果您使用任何現有的 EA Api 時，您需要將其更新為支援 MCA 計費帳戶。 下表顯示整合的其他變更：

| 目的 | 舊的供應項目 | 新的供應項目 |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure 成本管理](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft 使用深入解析](/power-bi/desktop-connect-azure-consumption-insights)內容套件和連接器 | [Microsoft Azure 耗用量 Insights Power BI 應用程式](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview)和[Azure Consumption Insights 連接器](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>若要取得平衡及參與名單的 Api

[取得平衡摘要](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary)API 可讓您的每月摘要：

- 餘額
- 新增購買商品
- Azure Marketplace 服務費用
- 調整
- 服務超額費用

所有的使用情況 Api 會使用 Azure AD 進行驗證和授權的原生 Azure Api 所取代。 如需有關呼叫 Azure REST Api 的詳細資訊，請參閱[開始使用 REST](/rest/api/azure/#create-the-request)。

取得平衡摘要 API 是由 Microsoft.Billing/billingAccounts/billingProfiles/availableBalance API 取代。

若要取得可用的餘額 api 可用餘額：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>若要取得成本和使用方式 Api

從 Azure 服務使用量、 第三方 Marketplace 使用量以及各種其他的 Marketplace 購買項目使用下列 Api 取得成本的每日明細。 下列不同的 Api 已合併為 Azure 服務和第三方 Marketplace 使用方式。 以取代舊的 Api [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails) API。 它會加入 Marketplace 購買項目，先前只有摘要中顯示餘額的日期。

- [取得使用方式詳細資料/下載](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [取得使用方式詳細資料/提交](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [取得使用方式詳細資料/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [取得使用方式詳細資料/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [取得 marketplace 市集費用/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [取得 marketplace 市集費用/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

所有的使用情況 Api 會使用 Azure AD 進行驗證和授權的原生 Azure Api 所取代。 如需有關呼叫 Azure REST Api 的詳細資訊，請參閱[開始使用 REST](/rest/api/azure/#create-the-request)。

耗用量/使用情況詳細資料 API 會取代所有先前的 Api。

若要取得使用方式詳細資料 api 的使用方式詳細資料：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

使用方式詳細資料 API 中，如同所有的成本管理 Api，可在多個範圍。 開立發票的成本，因為您通常會收到註冊層級，用於計費的設定檔範圍。  如需成本管理範圍的詳細資訊，請參閱[了解及使用範圍](understand-work-scopes.md)。

| 類型 | 識別碼格式 |
| --- | --- |
| 計費帳戶 | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| 帳單設定檔 | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| 訂用帳戶 | `/subscriptions/{subscriptionId}` |
| 資源群組 | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

您可以使用下列查詢字串參數來更新任何程式碼。

| 舊的參數 | 新的參數 |
| --- | --- |
| `billingPeriod={billingPeriod}` | 不支援 |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

您也變更回應的主體。

舊的回應內文：

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

新的回應內文：

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

從資料變更的屬性名稱包含陣列的使用量記錄_值_。 使用具有詳細屬性的一般清單的每一筆記錄。 不過，每個記錄的所有詳細資料現在都在一個名為的巢狀屬性現在_屬性_，除了 tag。 新的結構會與其他 Azure Api 一致的。 某些屬性名稱已變更。 下表顯示對應的屬性。

| 舊的屬性 | 新的屬性 | 注意 |
| --- | --- | --- |
| 帳戶識別碼 | N/A | 未追蹤的訂用帳戶建立者。 使用 invoiceSectionId （如同 departmentId）。 |
| AccountNameAccountOwnerId 和 AccountOwnerEmail | N/A | 未追蹤的訂用帳戶建立者。 使用 invoiceSectionName （如同 departmentName）。 |
| 其他資訊 | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | 請注意，這些屬性相反。 如果 isAzureCreditEnabled 為 true，ChargesBilledSeparately 會是 false。 |
| ConsumedQuantity | quantity | &nbsp; |
| 已使用的服務 | consumedService | 確切的字串值可能會不同。 |
| 已使用的服務識別碼 | None | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| 日期和 usageStartDate | 日期 | &nbsp;  |
| 天 | None | 剖析日期的日期。 |
| 部門識別碼 | invoiceSectionId | 確切的值不同。 |
| DepartmentName | invoiceSectionName | 確切的字串值可能會不同。 如有需要請設定發票區段，以符合部門。 |
| ExtendedCost 和成本 | costInBillingCurrency | &nbsp;  |
| InstanceId | ResourceId | &nbsp;  |
| 是定期計費 | None | &nbsp;  |
| 位置 | location | &nbsp;  |
| 計量類別目錄 | meterCategory | 確切的字串值可能會不同。 |
| 計量識別碼 | meterId | 確切的字串值不同。 |
| 計量名稱 | meterName | 確切的字串值可能會不同。 |
| 計量地區 | meterRegion | 確切的字串值可能會不同。 |
| 計量子類別目錄 | meterSubCategory | 確切的字串值可能會不同。 |
| 月 | None | 剖析日期的月份。 |
| 供應項目名稱 | None | 使用 publisherName 和 productOrderName。 |
| OfferId | None | &nbsp;  |
| 訂單編號 | None | &nbsp;  |
| PartNumber | None | 使用 meterId 和 productOrderName 來唯一識別價格。 |
| 方案名稱 | productOrderName | &nbsp;  |
| 產品 | 產品 |   |
| ProductId | productId | 確切的字串值不同。 |
| 發行者名稱 | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | 確切的字串值不同。 |
| 資源位置 | resourceLocation | &nbsp;  |
| 資源位置識別碼 | None | &nbsp;  |
| 資源費率 | effectivePrice | &nbsp;  |
| 服務管理員識別碼 | N/A | &nbsp;  |
| 服務資訊 1 | serviceInfo1 | &nbsp;  |
| 服務資訊 2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | 確切的字串值可能會不同。 |
| ServiceTier | meterSubCategory | 確切的字串值可能會不同。 |
| 儲存體服務識別碼 | N/A | &nbsp;  |
| 訂閱帳戶 GuID | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| 標記 | tags | Tags 屬性適用於根物件，而不要巢狀的屬性的屬性。 |
| 測量單位 | unitOfMeasure | 確切的字串值不同。 |
| usageEndDate | 日期 | &nbsp;  |
| Year | None | 剖析日期的年份。 |
| (新) | billingCurrency | 使用費用的貨幣。 |
| (新) | billingProfileId | 帳單的設定檔 （與註冊相同） 的唯一識別碼。 |
| (新) | billingProfileName | 計費的設定檔 （與註冊相同） 的名稱。 |
| (新) | chargeType | 使用此選項，來區分 Azure 服務使用量、 Marketplace 使用量以及各種購買項目。 |
| (新) | invoiceId | 發票的唯一識別碼。 目前，請開啟本月的空白。 |
| (新) | publisherType | 購買的 「 發行者 」 的型別。 空的使用方式。 |
| (新) | serviceFamily | 購買的型別。 空的使用方式。 |
| (新) | servicePeriodEndDate | 購買的服務結束日期。 |
| (新) | servicePeriodStartDate | 開始日期購買的服務。 |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>計費週期 API 取代發票 API

MCA 計費帳戶不會使用計費週期。 相反地，他們會使用特定計費週期範圍費用的發票。 [計費週期 API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)發票 API 取代。 所有的使用情況 Api 會使用 Azure AD 進行驗證和授權的原生 Azure Api 所取代。 如需有關呼叫 Azure REST Api 的詳細資訊，請參閱[開始使用 REST](/rest/api/azure/#create-the-request)。

若要取得發票，發票 api:

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>價位表 Api

本節討論現有的價位表 Api，並提供建議，可前往價位表 API 適用於 Microsoft 客戶合約。 它也討論價位表 API 適用於 Microsoft 客戶合約，並說明價位表中的欄位。 [企業取得價位表](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet)並[Enterprise 可讓您取得計費週期](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)取代適用於 Microsoft 客戶合約 (Microsoft.Billing/billingAccounts/billingProfiles 的價位表 API 的 Api/ pricesheet)。 新的 API 支援 JSON 和 CSV 格式，以非同步的其餘部分格式。 所有的使用情況 Api 會使用 Azure AD 進行驗證和授權的原生 Azure Api 所取代。 如需有關呼叫 Azure REST Api 的詳細資訊，請參閱[開始使用 REST](/rest/api/azure/#create-the-request)。

### <a name="billing-enterprise-apis"></a>計費企業版 Api

若要取得價格和計費週期的資訊計費企業版 Api 搭配企業註冊中。 驗證和授權使用 Azure Active Directory web 語彙基元。

若要取得所指定的企業註冊價位表與計費週期 Api 適用的價格：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>價位表 API 適用於 Microsoft 客戶協議

若要檢視所有的 Azure 耗用量和 Marketplace 取用服務的價格使用價位表 API 適用於 Microsoft 客戶合約。 計費的設定檔所示的價格會套用至屬於計費的設定檔的所有訂用帳戶。

若要檢視所有的 Azure 耗用量 services 價位表資料採用 CSV 格式使用價位表 API:

| 方法 | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

若要檢視所有的 Azure 耗用量 services 價位表資料以 JSON 格式使用價位表 API:

| 方法 | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

使用 API 會傳回整個帳戶的價位表。 不過，您也可以取得 PDF 格式價位表的精簡的版本。 此摘要包括的特定發票收費的 Azure 耗用量和 Marketplace 取用服務。 發票可由 {invoiceId}，這是相同**發票編號**發票摘要 PDF 檔案中所示。 以下是範例。

![顯示對應的發票編號 InvoiceId 範例映像](./media/migrate-cost-management-api/invoicesummary.png)

若要檢視與價位表 API 的發票資訊以 CSV 格式：

| 方法 | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

若要檢視與價位表 API 的發票資訊以 JSON 格式：

| 方法 | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

您也可以查看所有的 Azure 耗用量或 Marketplace 取用服務的預估的價格目前開啟的計費週期或服務週期中。

若要檢視的價位表 API 取用服務的預估的價格以 CSV 格式：

| 方法 | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

若要檢視的價位表 API 取用服務的預估的價格以 JSON 格式：

| 方法 | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Microsoft 客戶合約價位表 Api 都*非同步 REST Api*。 從較舊的同步 Api，api 回應變更。 API 回應主體也變更。

#### <a name="old-response-body"></a>舊的回應主體

以下是同步的 REST API 回應範例：

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>新的回應主體

Api 支援[Azure REST 非同步](../azure-resource-manager/resource-manager-async-operations.md)格式。 使用 GET 呼叫 API，您會收到下列回應：

```
No Response Body

HTTP Status 202 Accepted
```

下列標頭會傳送輸出的位置：

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

請另一個 GET 呼叫的位置。 直到作業完成或失敗狀態的 GET 呼叫的回應都是相同的。 完成時，GET 呼叫位置的回應會傳回下載 URL。 就如同在同一時間執行作業。 以下是範例：

```
HTTP Status 200

                                    {
                            “id”: “providers/Microsoft.Consumption/operationresults/{operationId}”,
                            “name”: {operationId},
                           “type”: “Microsoft.Consumption/operationResults”,
                           “properties” : {
                                  “downloadUrl”: {urltoblob},
                                  “vaildTill”: “Date”
}
                     }
```

用戶端也可以 GET 呼叫給`Azure-AsyncOperation`。 此端點會傳回作業的狀態。

下表顯示在較舊 Enterprise 取得價位表 API 中的欄位。 它包含在新的價位表中的對應欄位適用於 Microsoft 客戶合約：

| 舊的屬性 | 新的屬性 | 注意 |
| --- | --- | --- |
| billingPeriodId  | _不適用_ | 不適用。 適用於 Microsoft 客戶合約、 發票和相關聯的價位表取代 billingPeriodId 的概念。 |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | 確切的字串值可能會不同。 |
| includedQuantity  | includedQuantity | 不適用於在 Microsoft 客戶合約的服務。 |
| partNumber  | _不適用_ | 請改用 productOrderName （如同 offerId） 和 meterid 的組合。 |
| unitPrice  | unitPrice | 單價是適用於 Microsoft 客戶 Agreement 中取用服務。 |
| currencyCode  | pricingCurrency | Microsoft 客戶合約有定價貨幣與帳單貨幣的價格表示法。 CurrencyCode 會對應到在 Microsoft 客戶合約 pricingCurrency。 |
| offerId | productOrderName | 而不是 OfferId，您可以使用 productOrderName，但和 OfferId 不相同。 不過，productOrderName 以及計量器判斷 Microsoft 客戶合約中的定價相關 meterId 和 Offerid 在舊版的註冊項目。 |

## <a name="consumption-price-sheet-api-operations"></a>耗用量價位表 API 作業

對於企業合約，您可以使用耗用量的價位表 API[取得](/rest/api/consumption/pricesheet/get)並[取得的計費週期](/rest/api/consumption/pricesheet/getbybillingperiod)subscriptionId 或計費期間範圍內的作業。 API 會使用 Azure 資源管理驗證。

若要取得價位表 API 的領域的價位表資訊：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

若要取得依計費週期與價位表 API 的價位表的資訊：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

除了上述的 API 端點，使用適用於 Microsoft 客戶合約的下列項目：

**價位表 API 適用於 Microsoft 客戶合約 (非同步 REST API)**

此 API 是適用於 Microsoft 客戶合約，並提供額外的屬性。

**帳單帳戶的計費的設定檔範圍的價位表**

此 API 是現有的 API。 更新以供計費的設定檔中的帳單帳戶中的價位表。

## <a name="price-sheet-for-a-scope-by-billing-account"></a>計費帳戶範圍的價位表

您的帳單帳戶中的註冊範圍取得價位表時，會使用 azure Resource Manager 驗證。

若要取得在註冊帳戶的帳單帳戶中的價位表：

| 方法 | 要求 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Microsoft 客戶合約，請在下一節中使用的資訊。 它提供用於 Microsoft 客戶協議的欄位屬性。

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>計費的設定檔範圍中的計費帳戶的價位表

更新的價位表的帳單帳戶 API 取得價位表，以 CSV 格式。 若要取得的 MCA 計費的設定檔範圍內的價位表：

| 方法 | 要求 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

在 EA 註冊範圍，API 回應和屬性都相同。 屬性會對應至相同的 MCA 屬性。

針對較舊的屬性[Azure 資源管理員價位表 Api](/rest/api/consumption/pricesheet)和相同的新屬性會在下表。

| 舊 Azure 資源管理員價位表 API 屬性  | 新的 Microsoft 客戶合約價位表 API 屬性   | 描述 |
| --- | --- | --- |
| 計量識別碼 | _meterId_ | 計量的唯一識別碼。 MeterId 相同。 |
| 計量名稱 | meterName | 計量的名稱。 計量代表的 Azure 服務部署的資源。 |
| 計量類別  | service | 計量的分類類別目錄的名稱。 與 Microsoft 客戶合約價位表中的服務相同。 確切的字串值不同。 |
| 計量子類別 | meterSubCategory | 計量 subclassification 類別的名稱。 根據服務中的高層級的功能集差異的分類。 例如，基本的 SQL DB vs 標準的 SQL DB。 |
| 計量區域 | meterRegion | &nbsp;  |
| 單位 | _不適用_ | 可以從 unitOfMeasure 剖析。 |
| 測量單位 | unitOfMeasure | &nbsp;  |
| 組件編號 | _不適用_ | 而不是 partNumber，使用 productOrderName 和 MeterId 來唯一識別的價格計費的設定檔。 會列出欄位，而不是在 MCA 發票 partNumber MCA 發票上。 |
| 單價 | unitPrice | Microsoft 客戶合約單價。 |
| 貨幣代碼 | pricingCurrency | Microsoft 客戶合約代表貨幣的價格和計費貨幣的價格。 貨幣代碼是在 Microsoft 客戶合約 pricingCurrency 相同。 |
| 內含數量 | includedQuantity | 不適用於在 Microsoft 客戶合約的服務。 顯示值為零。 |
|  服務 ID  | productOrderName | 而不是 OfferId，使用 productOrderName。 不同 OfferId，不過 productOrderName 以及計量器判斷 Microsoft 客戶合約中的定價。 在舊版的註冊相關 meterId 和 Offerid。 |

適用於 Microsoft 客戶合約的價格不同於 Enterprise 合約定義。 中的企業註冊服務的價格是唯一的產品、 PartNumber、 計量、 和供應項目。 PartNumber 不會用於 Microsoft 客戶合約中。

是 Microsoft 的客戶合約一部分的 Azure 耗用量服務價格是 productOrderName 和 meterId 唯一的。 它們代表的服務計量和產品計劃。

要先協調之間價位表，以及使用方式詳細資料 API 中的使用量，您可以使用 productOrderName 和 meterId。

有計費擁有者、 參與者、 讀取器設定檔和發票管理員權限的使用者可以下載價位表。

價位表包含的服務的價格以使用量為基礎的價格。 服務包括 Azure 耗用量和 Marketplace 耗用量。 每個服務期間結尾的最新價格為鎖定，且在單一服務內套用使用量。 對於 Azure 耗用量的服務，服務期間是通常是某個日曆月份。

### <a name="retired-price-sheet-api-fields"></a>已停用的價位表 API 欄位

下列欄位不提供 Microsoft 客戶合約價位表 Api 或有相同的欄位。

|已停用的欄位| 描述|
|---|---|
| billingPeriodId | 不適用。 對應於 InvoiceId MCA。 |
| offerId | 不適用。 對應至在 MCA productOrderName。 |
| meterCategory  | 不適用。 對應至在 MCA 中的服務。 |
| unit | 不適用。 可以從 unitOfMeasure 剖析。 |
| currencyCode | 在 MCA pricingCurrency 相同。 |
| meterLocation | 在 MCA meterRegion 相同。 |
| partNumber partnumber | 不適用，因為組件編號未列在 MCA 發票。 而不是 partnumber，使用的 meterId productOrderName 組合來唯一識別價格。 |
| totalIncludedQuantity | 不適用。 |
| pretaxStandardRate  | 不適用。 |

## <a name="reservation-instance-charge-api-replaced"></a>保留執行個體費用 API 取代

您可以取得帳單與保留購買的交易[保留的執行個體費用 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges)。 新的 API 包括所有購買項目，包括第三方 Marketplace 供應項目。 所有的使用情況 Api 會使用 Azure AD 進行驗證和授權的原生 Azure Api 所取代。 如需有關呼叫 Azure REST Api 的詳細資訊，請參閱[開始使用 REST](/rest/api/azure/#create-the-request)。 保留的執行個體費用 API 就會取代交易 API。

若要購買交易與交易 API 取得保留項目：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>建議取代的 Api

保留的執行個體購買建議 Api 提供的虛擬機器使用量最後 7、 30 或 60 天。 Api 也會提供保留購買建議。 這些包括：

- [共用保留執行個體建議 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [單一保留的執行個體建議 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

所有的使用情況 Api 會使用 Azure AD 進行驗證和授權的原生 Azure Api 所取代。 如需有關呼叫 Azure REST Api 的詳細資訊，請參閱[開始使用 REST](/rest/api/azure/#create-the-request)。 Api 先前所列的保留項目建議會取代[Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) API。

若要取得保留區保留建議 api 的建議：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>保留使用情況 Api 取代

您可以在註冊中使用保留的執行個體使用情況 API 來取得保留使用情況。 如果在註冊中有多個保留的執行個體，您也可以取得所有保留的執行個體購買使用此 API 的使用方式。

這些包括：

- [保留執行個體的使用方式詳細資料](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)
- [保留的執行個體使用量摘要](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

所有的使用情況 Api 會使用 Azure AD 進行驗證和授權的原生 Azure Api 所取代。 如需有關呼叫 Azure REST Api 的詳細資訊，請參閱[開始使用 REST](/rest/api/azure/#create-the-request)。 Api 先前所列的保留項目建議會取代[Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)並[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) Api。

若要取得保留的詳細資料 api 的保留詳細資料：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

若要取得與保留項目摘要 API 的保留項目摘要：

| 方法 | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>會將從 Cloudyn 成本管理

使用組織[Cloudyn](https://cloudyn.com)應該開始使用[Azure 成本管理](https://azure.microsoft.com/services/cost-management/)任何成本管理需求。 成本的管理可在 Azure 入口網站中沒有上架與八小時的延遲。 如需詳細資訊，請參閱 <<c0> [ 成本管理文件](index.yml)。

使用 Azure 成本管理，您可以：

- 檢視一段時間，針對預先定義的預算的成本。 分析每日成本模式，以找出並停止消費異常狀況。 細分成本標記、 資源群組、 服務和位置。
- 建立預算設定使用量和成本限制和重要的臨界值時收到通知。 設定自動化與自訂事件觸發程序，並強制執行由您主導的固定限制的動作群組。
- 最佳化成本和使用方式與從 Azure Advisor 的建議。 探索購買保留項目最佳化、 縮小未充分使用的虛擬機器，並刪除未使用的資源，以維持在預算內。
- 排程的成本和使用方式資料匯出至 CSV 檔案每日發佈至儲存體帳戶。 自動化與外部系統將計費資料保持同步並是最新的整合。

## <a name="power-bi-integration"></a>Power BI 整合

如果您使用 Power BI 成本報告時，您需要下列轉換：

- Microsoft Azure 耗用量 Insights Power BI 應用程式
- Azure Consumption Insights 桌面連接器


組織想最大的彈性，建議您使用連接器。 不過，還有適用於快速安裝的 Power BI 應用程式。

- 安裝[Microsoft Azure Consumption Insights Power BI 應用程式](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview)
- [與 Azure Consumption Insights 連接器進行連線](/power-bi/desktop-connect-azure-consumption-insights)

較舊的 Consumption Insights 內容套件和連接器曾在註冊層級。 您需要至少讀取的存取。 新的耗用量 Insights Power BI 應用程式和新的 Azure Consumption Insights connector 可計費的設定檔使用者。 檢閱成本，或跨帳單設定檔中檢視成本需要其他選項的小組都應該使用[成本分析](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/costanalysis)Azure 入口網站。

## <a name="next-steps"></a>後續步驟

- 讀取[成本管理文件](index.yml)以了解如何監視及控制 Azure 消費。 或者，如果您想要將使用成本管理的資源使用最佳化。
