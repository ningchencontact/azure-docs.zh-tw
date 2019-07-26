---
title: 瞭解 Azure 中的 Microsoft 客戶合約發票
description: 瞭解如何閱讀並瞭解 Azure 中的 Microsoft 客戶合約帳單
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: a5f77120c1d4e8a6721f3bc207132bee19a7772f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383549"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>您的 Microsoft 客戶合約發票中的條款

本文適用于 Microsoft 客戶合約的 Azure 計費帳戶。 [檢查您是否有權存取 Microsoft 客戶合約](#check-access-to-a-microsoft-customer-agreement)。

您的發票會提供您的費用和付款指示的摘要。 您可以從 [Azure 入口網站](https://portal.azure.com/)下載可攜式文件格式 (.pdf) 的發票，或可透過電子郵件傳送。 如需詳細資訊, 請參閱[View and 下載您的 Microsoft Azure 發票](billing-download-azure-invoice.md)。

## <a name="billing-period"></a>計費期間

您會以每月為基礎的發票。 您可以在[Azure 入口網站](https://portal.azure.com/)中, 檢查 [帳單配置檔案屬性] 底下的 [*發票日期*], 找出您收到發票的月份日期。 在計費期間結束和發票日期之間發生的費用會包含在下個月的發票中, 因為它們在下一個計費期間。 每張發票的計費期間開始和結束日期會列在 [**計費摘要**] 上方的 [發票] PDF 中。

## <a name="invoice-terms-and-descriptions"></a>發票條款和描述

下列各節列出您在發票上看到的重要詞彙, 並提供每個詞彙的說明。

### <a name="invoice-summary"></a>發票摘要

**發票摘要**位於第一頁的頂端, 並顯示您的帳單設定檔和支付方式的相關資訊。

![發票摘要區段](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| 詞彙 | 描述 |
| --- | --- |
| 買方 |您的法律實體位址, 可在帳單帳戶屬性中找到|
| 帳單收件者 |接收發票之帳單設定檔的帳單位址, 可在帳單配置檔案屬性中找到|
| 計費設定檔 |接收發票的帳單設定檔名稱 |
| 郵政 號 |您選擇性指派以進行追蹤的訂購單號碼 |
| 發票編號 |用於追蹤用途的唯一、Microsoft 產生的發票編號 |
| 發票日期 |產生發票的日期, 通常是在計費週期結束後的五到12天。 您可以在帳單配置檔案屬性中檢查發票日期。|
| 付款條件 |您對 Microsoft 帳單的費用。 *Net 30 天*表示您會在發票日期的30天內支付費用。 |

### <a name="billing-summary"></a>計費摘要

[**計費摘要**] 會顯示自前一個計費週期起的帳單設定檔費用、已套用的信用額度、稅金, 以及逾期的總金額。

![計費摘要區段](./media/billing-understand-your-invoice-mca/billingsummary.png)

| 詞彙 | 描述 |
| --- | --- |
| 收費|自上次計費期間起, 此帳單設定檔的 Microsoft 費用總數 |
| 參與名單 |您從傳回的信用額度 |
| 適用的 Azure 信用額度 | 每個計費週期自動套用至 Azure 費用的 azure 信用額度 |
| 小計 |稅金金額為逾期 |
| 稅金 |依據帳單設定檔的國家/地區, 您所支付的稅金類型和數量。 如果您不需要支付稅金, 則您不會在發票上看到稅額。 |
| 預估總節省量 |從有效折扣中儲存的預估總金額。 如果適用的話, 有效的折扣率會列在 [依發票的詳細資料] 區段中的 [購買明細專案] 底下。 |

### <a name="invoice-sections"></a>發票區段

針對帳單設定檔底下的每個發票區段, 您將會看到費用、套用的 Azure 信用額度金額、稅金, 以及應付金額總計。

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>依發票區段的詳細資料

詳細資料會顯示依產品訂單細分的每個發票區段成本。 在每個產品訂單中, 成本會依服務的類型細分。 您可以在 Azure 入口網站和 Azure 使用量和費用 CSV 中找到產品和服務的每日費用。 若要深入瞭解, 請參閱[瞭解 Microsoft 客戶合約發票的費用](billing-mca-understand-your-bill.md)。

針對每個服務系列所應付的總金額, 計算方式是將*Azure 點數*減去*信用額度/費用*並增加*稅金*:


![依發票區段的詳細資料](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| 詞彙 |描述 |
| --- | --- |
| 單價 | 服務的有效單位價格 (以價格貨幣計算), 用來計算使用量的速率。 這對於產品、服務系列、計量及供應專案而言是唯一的。 |
| 數量 | 計費期間所購買或使用的數量 |
| 費用/信用額度 | 套用信用額度/退款後的淨費用金額 |
| Azure 信用額度 | 適用于費用/信用額度的 Azure 信用額度金額|
| 稅率 | 稅率 (依國家/地區而定) |
| 稅額 | 根據稅率購買的稅金費用 |
| 總計 | 購買所應付的總金額 |

### <a name="how-to-pay"></a>如何支付費用

在發票底部, 有帳單的相關指示。 您可以使用支票、線路或線上付款。 如果您在線上付費, 則可以使用信用卡或 Azure 點數 (如果適用的話)。

### <a name="publisher-information"></a>發行者資訊

如果您的帳單中有協力廠商服務, 則會在發票底部列出每個發行者的名稱和位址。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要協助嗎? 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [瞭解帳單設定檔發票的費用](billing-mca-understand-your-bill.md)
- [如何取得您的 Azure 帳單發票和每日使用量資料](billing-download-azure-invoice-daily-usage-date.md)
- [查看貴組織的 Azure 定價](billing-ea-pricing.md)
- [查看帳單設定檔的稅務檔](billing-mca-download-tax-document.md)
