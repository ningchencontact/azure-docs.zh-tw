---
title: 了解詳細的使用量和費用 |Microsoft Docs
description: 了解如何閱讀並了解詳細的使用量和費用
author: bandersmsft
manager: micflan
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: 2eb9f8e19be2a7b6220bc34bf4ce0c72c4ac0b4f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275064"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>了解您的 Azure 使用量和費用檔案中的詞彙

詳細的使用量和費用檔案包含根據交涉的費率，購買項目 （例如，保留項目、 Marketplace 費用） 和退費記錄一段指定的每日評分最高的使用量。
費用不包含信用額度、 稅金或其他費用或折扣。
下列表格涵蓋哪些費用會包含每一個帳戶類型。

帳戶類型 | Azure 使用量 | Marketplace 使用方式 | 購買項目 | 退款
--- | --- | --- | --- | ---
Enterprise 合約 (EA) | 是 | 是 | 是 | 否
Microsoft 客戶合約 (MCA) | 是 | 是 | 是 | 是
隨用隨付 (PAYG) | 是 | 否 | 否 | 否

若要深入了解 Marketplace 訂單 （也稱為外部服務），請參閱[了解您的 Azure 外部服務費用](billing-understand-your-azure-marketplace-charges.md)。

請參閱[如何取得您的 Azure 帳單發票和每日使用量資料](billing-download-azure-invoice-daily-usage-date.md)如需下載指示。
使用逗點分隔值 (.csv) 檔案格式，您可以開啟試算表應用程式中的使用量和費用檔案。

## <a name="list-of-terms-and-descriptions"></a>字詞和描述的清單

下表描述最新版的 Azure 使用量和費用檔案中使用的重要詞彙。
本清單已涵蓋隨用隨付 (PAYG) 」、 「 Enterprise 合約 (EA) 和 「 Microsoft 客戶協議 (MCA) 帳戶。

詞彙 | 帳戶類型 | 描述
--- | --- | ---
AccountName | EA | 註冊帳戶的顯示名稱。
帳戶擁有者識別碼 | EA | 註冊帳戶的唯一識別碼。
其他資訊 | 全部 | 服務專屬的中繼資料。 例如，虛擬機器的影像類型。
BillingAccountId | EA, MCA | 計費帳戶根目錄的唯一識別碼。
BillingAccountName | EA, MCA | 計費帳戶的名稱。
BillingCurrency | EA, MCA | 計費帳戶相關聯的貨幣。
BillingPeriod | EA | 計費期間的費用。
BillingPeriodEndDate | EA, MCA | 在計費週期結束日期。
BillingPeriodStartDate | EA, MCA | 在計費週期開始日期。
BillingProfileId | EA, MCA | EA 註冊或 MCA 帳單設定檔的唯一識別碼。
BillingProfileName | EA, MCA | EA 註冊或 MCA 帳單設定檔的名稱。
ChargeType | EA, MCA | 指出其電量是否表示使用量 (**使用量**)，購買 (**購買**)，或退款 (**退款**)。
ConsumedQuantity | PAYG | 請參閱數量。
已使用的服務 | 全部 | 免費的服務名稱相關聯。
成本 | EA | 請參閱 CostInBillingCurrency。
CostCenter | EA, MCA | 定義用於追蹤成本 （僅適用於開啟 MCA 帳戶的計費期間） 的訂用帳戶成本中心。
CostInBillingCurrency | MCA | 在 帳單的貨幣信用額度或稅前費用的成本。
CostInPricingCurrency | MCA | 成本的定價貨幣信用額度或稅前費用。
貨幣 | PAYG | 請參閱 BillingCurrency。
date | EA, MCA | 付費的使用方式或購買日期。
ExchangeRateDate | MCA | 建立貨幣匯率的日期。
ExchangeRatePricingToBilling | MCA | 用來將帳單的貨幣轉換的成本的定價貨幣的匯率。
頻率 | EA, MCA | 指出是否必須是重複的費用。 費用可能是發生一次 (**OneTime**)，請重複每月或每年的基礎 (**週期性**)，或根據使用方式 (**UsageBased**)。
IncludedQuantity | PAYG | 目前計費週期內免費包含的計量數量。
InstanceId | PAGY | ResourceId，請參閱。
InvoiceId | EA, MCA | 唯一的文件識別碼列出 PDF 發票上。
InvoiceSection | MCA | 請參閱 InvoiceSectionName。
InvoiceSectionId | EA, MCA | EA 部門或 MCA 發票區段的唯一識別碼。
InvoiceSectionName | EA, MCA | EA 部門或 MCA 發票區段的名稱。
IsAzureCreditEligible | EA, MCA | 指出是否能夠使用 Azure 信用額度支付的費用 (值：True、 False)。
IsEstimated | 全部 | 指出是否在計費週期關閉/完成。 使用方式資料可以變更在計費期間內，直到產生發票。 這些記錄會標示為 「 預估 」。
位置 | EA, MCA | 正在執行資源的資料中心位置。
計量類別目錄 | 全部 | 計量的分類類別目錄的名稱。 例如，*雲端服務*並*網路*。
計量識別碼 | 全部 | 計量的唯一識別碼。
計量名稱 | 全部 | 計量表的名稱。
計量地區 | 全部 | 資料中心位置計價的服務名稱的位置為基礎。 請參閱位置。
計量子類別目錄 | 全部 | 計量 subclassification 類別的名稱。
OfferId | EA, MCA | 購買的供應項目名稱。
PartNumber | EA | 用來取得特定的計量器定價的識別碼。
計劃名稱 | EA | Marketplace 方案名稱。
PreviousInvoiceId | MCA | 如果這個行項目是退款原始發票的參考。
PricingCurrency | MCA | 交涉的價格為基礎的評等時所使用的貨幣。
Products | MCA | 請參閱產品名稱。
ProductId | EA, MCA | 產品的唯一識別碼。
ProductName | EA | 產品的名稱。
ProductOrderId | EA, MCA | 產品順序的唯一識別碼。
ProductOrderName | EA, MCA | 產品順序的唯一名稱。
PublisherName | EA, MCA | Marketplace 服務的 「 發行者 」。
PublisherType | EA, MCA | 「 發行者 」 的型別 (值： firstParty thirdPartyReseller，thirdPartyAgency)。
數量 | EA, MCA | 購買或耗用的單位數目。
費率 | PAYG | 請參閱 UnitPrice。
ReservationId | EA, MCA | 購買的保留執行個體的唯一識別碼。
ReservationName | EA, MCA | 購買的保留執行個體名稱。
ResourceGroupId | EA, MCA | 唯一識別項[資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)資源為中。
resourceGroupName | EA, MCA | 名稱[資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)資源為中。
resourceId | EA, MCA | 唯一識別碼[Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources)資源。
資源位置 | EA, MCA | 正在執行資源的資料中心位置。 請參閱位置。
ResourceName | EA | 資源名稱。
ResourceType | MCA | 資源執行個體的型別。
ServiceFamily | EA, MCA | 服務服務所屬的系列。
服務資訊 1 | 全部 | 服務專屬的中繼資料。
服務資訊 2 | 全部 | 舊版欄位，以選擇性的服務特定中繼資料。
ServicePeriodEndDate | MCA | 分級期間的結束日期，定義並鎖定已使用或購買服務的定價。
ServicePeriodStartDate | MCA | 分級期間的開始日期，定義並鎖定已使用或購買服務的定價。
SubscriptionId | 全部 | 訂用帳戶的唯一識別碼。
SubscriptionName | 全部 | 訂用帳戶的名稱。
標記 | 全部 | 指派給資源的標記。 不包含資源群組的標記。 可用來群組或散發內部的計費功能的成本。 如需詳細資訊，請參閱[使用標記組織您的 Azure 資源](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)。
單位 | PAYG | 請參閱 UnitOfMeasure。
測量單位 | 全部 | 服務的計費的測量單位。 例如，計算服務是按每小時計費。
UnitPrice | EA | 費用的每單位價格。
UsageDate | PAYG | 請參閱日期。

請注意，某些欄位而異的大小寫和帳戶類型之間的間距。
舊版的隨用隨付的使用方式檔案會有另一個區塊的陳述式和每日使用量。

## <a name="ensure-that-your-charges-are-correct"></a>請確定您的費用正確

若要深入了解詳細的使用量和費用，請參閱如何了解您[隨用隨付](./billing-understand-your-bill.md)或是[Microsoft 客戶合約](billing-mca-understand-your-bill.md)發票。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [檢視及下載您 Microsoft Azure 發票](billing-download-azure-invoice.md)
- [檢視及下載您的 Microsoft Azure 使用量和費用](billing-download-azure-daily-usage.md)
