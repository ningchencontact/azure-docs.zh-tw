---
title: 了解 Microsoft 的客戶合約條款，在您的 Azure 使用量和費用 CSV |Microsoft Docs
description: 了解如何閱讀並了解 Azure 使用量和費用 CSV 帳單設定檔的區段
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: 8f71f42386ce49d4d7178cb03d28d74edacd7e39
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371303"
---
# <a name="understand-terms-on-your-azure-usage-and-charges-csv-for-a-microsoft-customer-agreement"></a>了解 Microsoft 的客戶合約條款，在您的 Azure 使用量和費用 CSV

這篇文章適用於計費的帳戶 Microsoft 客戶合約。 [檢查您是否擁有存取權的 Microsoft 客戶合約](#check-access-to-a-microsoft-customer-agreement)。

Azure 使用量和費用 CSV 檔案包含目前計費週期的每日及計量層級使用量費用。

若要取得 Azure 使用量和費用檔案，請參閱[檢視和下載 Azure 使用量和費用，Microsoft 客戶合約](billing-download-azure-daily-usage.md)。
此檔案以逗號分隔值 (.csv) 檔案格式提供，您可以使用試算表應用程式開啟這個檔案。

使用量費用是訂用帳戶的**每月**總費用。 使用量費用不會將任何信用額度或折扣列入考慮。

## <a name="changes-in-the-enterprise-agreement-azure-usage-and-charges-csv"></a>Enterprise 合約的 Azure 使用量和費用 CSV 中的變更

如果您是 EA 客戶，您會發現，帳單的設定檔的 Azure 使用量 CSV 檔案中的條款位於不同條款 EA Azure 使用量 CSV 檔案。 以下是 EA 使用條款帳務設定檔使用詞彙的對應：

| EA Azure 使用情況 CSV | Microsoft 客戶合約的 Azure 使用量和費用 CSV |
| --- | --- |
| Date | date |
| 月| date |
| 天 | date |
| Year | date |
| Products | product |
| 計量識別碼 | meterID |
| 計量類別目錄 | meterCategory |
| 計量子類別目錄 | meterSubCategory |
| 計量地區 | meterRegion |
| 計量名稱 | meterName |
| ConsumedQuantity | quantity |
| ResourceRate | effectivePrice | <!-- this was highlighted -->
| ExtendedCost | cost |
| 資源位置 | resourceLocation |
| 已使用的服務 | consumedService |
| InstanceId | instanceId |
| 服務資訊 1 | serviceInfo1 |
| 服務資訊 2 | serviceInfo2 |
| 其他資訊 | additionalInfo |
| 標記 | 标记 |
| StoreServiceIdentifier | N/A |
| DepartmentName | invoiceSection | <!-- this was highlighted -->
| CostCenter | costCenter |
| 測量單位 | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible | <!-- this was highlighted -->

<!-- TO DO: Marketplace CSV? -->

## <a name="detailed-terms-and-descriptions-in-your-azure-usage-and-charges-file"></a>詳細的字詞和描述您的 Azure 使用量和費用檔案中

下節說明 Azure 的使用量和費用檔案中所示的重要字詞。

詞彙 | 描述
--- | ---
invoiceId | PDF 發票上所列的唯一文件識別碼
previousInvoiceId | 如果這個行項目是退款原始發票的參考
billingAccountName | 計費帳戶的名稱
billingAccountId | 計費帳戶的根的唯一識別碼
billingProfileId | 計費仍會累積會立發票費用的設定檔名稱
billingProfileName | 仍會累積會立發票費用的計費 」 設定檔的唯一識別碼
invoiceSectionId | [發票] 區段的唯一識別碼
invoiceSectionName | 發票區段的名稱
costCenter | 定義用於追蹤成本 （僅可在開放的計費期間） 的訂用帳戶的成本中心
billingPeriodStartDate | 為其產生發票計費週期開始日期
billingPeriodEndDate | 為其產生發票的計費期間結束日期
servicePeriodStartDate | 分級期間的開始日期已定義以及鎖定已使用或購買服務的定價
servicePeriodEndDate | 分級期間的結束日期已定義以及鎖定已使用或購買服務的定價
date | 針對 Azure 與 Marketplace 基於使用方式的費用，這是評等的日期。 一次購買項目 （Marketplace 中的 保留項目） 或固定週期性費用 （支援供應項目），這是在購買日期。
serviceFamily | 服務所屬的服務系列
productOrderId | 產品順序的唯一識別碼
productOrderName | 產品順序的唯一名稱
consumedService | 已使用的服務名稱
meterId | 計量的唯一識別碼
meterName | 計量的名稱
meterCategory | 計量的分類類別目錄的名稱。 例如，*雲端服務*，*網路*，依此類推。
meterSubCategory | 計量子分類類別目錄的名稱
meterRegion | 服務計量表可使用的區域名稱。 識別資料中心位置為基礎的定價特定服務的資料中心的位置。
供應項目 | 供應項目購買的名稱
productId | 產品因為而產生費用的唯一識別碼
product | 因為而產生費用的產品名稱
訂用帳戶識別碼 | 因為而產生費用的訂用帳戶的唯一識別碼
subscriptionName | 因為而產生費用的訂用帳戶的名稱
reservationId | 購買的保留執行個體的唯一識別碼
reservationName | 購買的保留執行個體的名稱
publisherType | 「 發行者 」 的型別 (值： firstParty thirdPartyReseller，thirdPartyAgency)
publisherName | 「 發行者 」 的 Marketplace 服務
resourceGroupId | 與資源相關聯的資源群組的唯一識別碼
resourceGroupName | 與資源相關聯的資源群組名稱
ResourceId | 資源執行個體的唯一識別碼
resourceType | 資源執行個體的類型
resourceLocation | 識別正在執行資源的資料中心位置。
location | 如果針對相同的區域設定不同的資源位置的資源標準化的位置
quantity | 購買或耗用的單位數
unitOfMeasure | 服務的計費的測量單位。 例如，計算服務是按每小時計費。
chargeType | 付費的型別。 值： <ul><li>AsCharged-使用方式：產生的費用會根據 Azure 服務的使用量。 這包括針對不會被收取因為保留執行個體的 Vm 的使用方式。</li><li>AsCharged PurchaseMarketplace:從 Marketplace 購買的一次性或固定週期性費用</li><li>AsCharged UsageMarketplace:Marketplace 服務計費單位的耗用量為基礎的費用</li></ul>
isAzureCreditEligible | 旗標，指出是否有資格使用要使用的 Azure 信用額度支付服務費用 (值：True、 False)
serviceInfo1 | 服務特定中繼資料
serviceInfo2 | 舊版欄位，可擷取選擇性的服務特定中繼資料
additionalInfo | 其他服務特定的中繼資料。
标记 | 您將指派給資源的標記

### <a name="how-do-i-make-sure-that-the-charges-in-my-azure-usage-and-charges-file-are-correct"></a>我如何確定我的 Azure 使用量和費用檔案中的費用是否正確？

如果您想要更多詳細資料，在您詳細的使用量檔案費用，請參閱[了解帳單設定檔的發票費用](billing-mca-understand-your-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [檢視及下載您 Microsoft Azure 發票](billing-download-azure-invoice.md)
- [檢視及下載您的 Microsoft Azure 使用量和費用](billing-download-azure-daily-usage.md)
