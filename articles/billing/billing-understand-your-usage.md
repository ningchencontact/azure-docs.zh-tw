---
title: 瞭解您的詳細使用方式和費用 |Microsoft Docs
description: 瞭解如何閱讀並瞭解詳細的使用方式和費用
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
ms.openlocfilehash: 66b54c027cde6341b23aef2c10b43fa21bf357da
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383458"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>瞭解 Azure 使用量和費用檔案中的條款

詳細的使用量和費用檔案包含根據談判費率、購買 (例如, 保留期、Marketplace 費用) 和退款的指定期間的每日額定使用量。
費用不包括信用額度、稅金, 或其他費用或折扣。
下表涵蓋每種帳戶類型所包含的費用。

帳戶類型 | Azure 使用量 | Marketplace 使用量 | 訂購 | 退款
--- | --- | --- | --- | ---
Enterprise 合約 (EA) | 是 | 是 | 是 | 否
Microsoft 客戶合約 (MCA) | 是 | 是 | 是 | 是
隨用隨付 (PAYG) | 是 | 否 | 否 | 否

若要深入瞭解 Marketplace 訂單 (也稱為外部服務), 請參閱[瞭解您的 Azure 外部服務費用](billing-understand-your-azure-marketplace-charges.md)。

如需下載指示, 請參閱[如何取得您的 Azure 帳單發票和每日使用量資料](billing-download-azure-invoice-daily-usage-date.md)。
您可以在 Microsoft Excel 或其他試算表應用程式中開啟您的使用量和費用 CSV 檔案。

## <a name="list-of-terms-and-descriptions"></a>詞彙和描述的清單

下表描述最新版本的 Azure 使用量和費用檔案中使用的重要詞彙。
此清單涵蓋「隨用隨付」 (PAYG)、Enterprise 合約 (EA) 和 Microsoft 客戶合約 (MCA) 帳戶。

詞彙 | 帳戶類型 | 描述
--- | --- | ---
AccountName | EA、PAYG | EA 註冊帳戶或 PAYG 帳單帳戶的顯示名稱。
AccountOwnerId | EA、PAYG | EA 註冊帳戶或 PAYG 帳單帳戶的唯一識別碼。
AdditionalInfo | 所有 | 服務專屬的中繼資料。 例如，虛擬機器的影像類型。
BillingAccountId | 所有 | 根帳單帳戶的唯一識別碼。
BillingAccountName | 所有 | 帳單帳戶的名稱。
BillingCurrency | 所有 | 與帳單帳戶相關聯的貨幣。
BillingPeriod | EA、PAYG | 費用的計費期間。
BillingPeriodEndDate | 所有 | 計費週期的結束日期。
BillingPeriodStartDate | 所有 | 計費週期的開始日期。
BillingProfileId | 所有 | EA 註冊、PAYG 訂閱、MCA 帳單設定檔或 AWS 合併帳戶的唯一識別碼。
BillingProfileName | 所有 | EA 註冊、PAYG 訂閱、MCA 帳單設定檔或 AWS 合併帳戶的名稱。
ChargeType | 所有 | 指出費用是否代表使用量 (**使用量**)、購買 (**購買**) 或退款 (**退款**)。
ConsumedService | 所有 | 與費用相關聯的服務名稱。
CostCenter | EA、MCA | 為訂用帳戶定義的成本中心, 用於追蹤成本 (僅適用于 MCA 帳戶的開放計費期間)。
成本 | EA、PAYG | 請參閱 CostInBillingCurrency。
CostInBillingCurrency | MCA | 在信用額度或稅金前的計費貨幣費用。
CostInPricingCurrency | MCA | 在信用額度或稅金之前的定價貨幣費用。
Currency | EA、PAYG | 請參閱 BillingCurrency。
Date | 所有 | 費用的使用量或購買日期。
EffectivePrice | 所有 | 期間的混合單位價格。 混合的價格會平均計算單價的任何波動, 例如評分階層處理, 這可減少數量隨時間增加的價格。
ExchangeRateDate | MCA | 兌換匯率的建立日期。
ExchangeRatePricingToBilling | MCA | 用來將定價貨幣中的成本轉換成計費貨幣的匯率。
頻率 | 所有 | 指出是否預期會重複收費。 費用可能會發生一次 (**OneTime**)、每月或每年重複 (**週期性**), 或以使用方式 (**UsageBased**) 為基礎。
IncludedQuantity | PAYG | 目前計費期間免費包含的計量數量。
InstanceId | PAGY | 請參閱 ResourceId。
InvoiceId | PAYG, MCA | 以發票 PDF 列出的唯一檔識別碼。
InvoiceSection | MCA | 請參閱 InvoiceSectionName。
InvoiceSectionId | EA、MCA | EA 部門或 MCA invoice 區段的唯一識別碼。
InvoiceSectionName | EA、MCA | EA 部門或 MCA invoice 區段的名稱。
IsAzureCreditEligible | 所有 | 指出費用是否有資格支付使用 Azure 點數 (值:True、False)。
Location | MCA | 正在執行資源的資料中心位置。
MeterCategory | 所有 | 計量分類類別目錄的名稱。 例如,*雲端服務*和*網路*。
計量識別碼 | 所有 | 計量的唯一識別碼。
MeterName | 所有 | 計量的名稱。
MeterRegion | 所有 | 根據位置定價之服務的資料中心位置名稱。 請參閱位置。
MeterSubCategory | 所有 | 計量 subclassification 類別目錄的名稱。
OfferId | 所有 | 購買的供應專案名稱。
PartNumber | EA、PAYG | 用來取得特定計量定價的識別碼。
PlanName | EA、PAYG | Marketplace 方案名稱。
PreviousInvoiceId | MCA | 如果此明細專案為退款, 則為原始發票的參考。
PricingCurrency | MCA | 根據談判價格進行分級時所使用的貨幣。
產品 | 所有 | 產品名稱。
ProductId | MCA | 產品的唯一識別碼。
ProductOrderId | 所有 | 產品訂單的唯一識別碼。
ProductOrderName | 所有 | 產品訂單的唯一名稱。
PublisherName | 所有 | Marketplace 服務的發行者。
PublisherType | 所有 | 發行者的類型 (值:**Azure**、 **AWS**、 **Marketplace**)。
數量 | 所有 | 購買或耗用的單位數。
ReservationId | EA、MCA | 已購買保留實例的唯一識別碼。
ReservationName | EA、MCA | 所購買保留實例的名稱。
ResourceGroup | 所有 | 資源所在的[資源組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)名。
resourceId | 所有 | [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources)資源的唯一識別碼。
ResourceLocation | 所有 | 正在執行資源的資料中心位置。 請參閱位置。
ResourceName | EA、PAYG | 資源的名稱。
ResourceType | MCA | 資源實例的類型。
ServiceFamily | MCA | 服務所屬的服務系列。
ServiceInfo1 | 所有 | 服務專屬的中繼資料。
ServiceInfo2 | 所有 | 具有選擇性服務特定中繼資料的舊版欄位。
ServicePeriodEndDate | MCA | 已取用或已購買服務已定義和鎖定定價的評等期間的結束日期。
ServicePeriodStartDate | MCA | 已使用或已購買服務的已定義和已鎖定定價之評等期間的開始日期。
訂用帳戶 ID | 所有 | Azure 訂用帳戶的唯一識別碼。
SubscriptionName | 所有 | Azure 訂用帳戶的名稱。
Tags | 所有 | 指派給資源的標記。 不包含資源群組標記。 可以用來分組或散發內部退款的成本。 如需詳細資訊，請參閱[使用標記組織您的 Azure 資源](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)。
UnitOfMeasure | 所有 | 服務計費的測量單位。 例如, 計算服務是以每小時計費。
UnitPrice | EA、PAYG | 費用的每單位價格。

請注意, 某些欄位的大小寫和帳戶類型之間的間距可能有所不同。
較舊版本的隨用隨付使用量檔案具有個別的語句和每日使用量區段。

### <a name="list-of-terms-from-older-apis"></a>舊版 Api 的條款清單
下表將舊版 Api 中使用的詞彙對應到新的詞彙。 請參閱上表中的說明。

舊詞彙 | 新字詞
--- | ---
ConsumedQuantity | 數量
IncludedQuantity | N/A
InstanceId | resourceId
速率 | EffectivePrice
單位 | UnitOfMeasure
UsageDate | Date
UsageEnd | Date
UsageStart | Date


## <a name="ensure-charges-are-correct"></a>確保費用正確

若要深入瞭解詳細的使用量和費用, 請閱讀如何瞭解您的[隨用隨付](./billing-understand-your-bill.md)或[Microsoft 客戶合約](billing-mca-understand-your-bill.md)發票。

## <a name="need-help-contact-us"></a>需要協助嗎? 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [查看和下載您的 Microsoft Azure 發票](billing-download-azure-invoice.md)
- [查看及下載您的 Microsoft Azure 使用量和費用](billing-download-azure-daily-usage.md)
