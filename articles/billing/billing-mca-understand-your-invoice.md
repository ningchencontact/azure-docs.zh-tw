---
title: 了解您的 Microsoft 客戶合約發票 |Microsoft Docs
description: 了解如何閱讀並了解 MCA 帳單
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: e5dc8ac0716e194dd0949e2e270346ee338a792a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839724"
---
# <a name="understand-terms-on-your-microsoft-customer-agreement-invoice"></a>了解您的 Microsoft 客戶合約發票上的字詞

這篇文章適用於計費的帳戶 Microsoft 客戶合約。 [檢查您是否擁有存取權的 Microsoft 客戶合約](#check-access-to-a-microsoft-customer-agreement)。

發票會提供您的費用和付款指示的摘要。 您可以從 [Azure 入口網站](https://portal.azure.com/)下載可攜式文件格式 (.pdf) 的發票，或可透過電子郵件傳送。 如需詳細資訊，請參閱 <<c0> [ 檢視並下載您的 Microsoft Azure 發票](billing-download-azure-invoice.md)。

<!-- ## When am I billed?

You are invoiced on a monthly basis. You can find out which day of the month you receive invoices by checking *invoice date* under billing profile properties in the [Azure portal](https://portal.azure.com/). Charges that occur between the end of the billing period and the invoice date are included in the next month's invoice, since they are in the next billing period. The billing period start and end dates for each invoice are listed in the invoice PDF above **Billing Summary**. -->

## <a name="invoice-terms-and-descriptions"></a>發票字詞和描述

下列各節列出您在看到您的發票和描述每個詞彙的重要詞彙。

### <a name="invoice-summary"></a>發票摘要

**發票摘要**是第一頁的頂端，並顯示您帳單的設定檔和您的付款方式資訊。

![發票摘要區段](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| 詞彙 | 描述 |
| --- | --- |
| 買方 |您的法律實體，計費帳戶內容中找到的位址|
| 帳單收件者 |帳單計費接收發票，計費的設定檔屬性中找到的設定檔的地址|
| 帳單設定檔 |接收的發票計費的設定檔的名稱 |
| P.O. number |您選擇性指派以進行追蹤的訂購單號碼 |
| 發票編號 |用於追蹤 Microsoft 所產生的唯一發票編號 |
| 發票日期 |會產生發票，通常在計費週期結束後的 5 到 12 天的日期。 您可以檢查您發票的日期在計費的設定檔屬性。|
| 付款條件 |您如何需支付您 Microsoft 帳單。 *Net 30 天*表示您需要支付您使用核取或發票日期的 30 天內的連線傳輸。 |

### <a name="billing-summary"></a>帳單摘要

**帳單摘要**顯示自上一個計費期間、 任何已套用的信用額度、 稅金和總金額的對帳單的設定檔的費用到期。

![帳單摘要區段](./media/billing-understand-your-invoice-mca/billingsummary.png)

| 詞彙 | 描述 |
| --- | --- |
| Charges|此計費的設定檔，自上次計費期間，Microsoft 會收費的總數 |
| 學分 |您已收到傳回的信用額度 |
| 套用 azure 信用額度 | 會自動套用至 Azure 的費用每個計費週期的 azure 信用額度 |
| 小計 |稅前金額 |
| 稅金 |類型和您用多少付多少，根據您的帳單設定檔的國家/地區的稅務數量。 如果您沒有要支付稅金，您就不會看到您發票上的稅金。 |
| 預估的總節省 |預估的總金額，您儲存從有效的折扣。 如果適用的話，有效的折扣率依照採購單明細項目，在詳細資料下方發票一節。 |

### <a name="invoice-sections"></a>發票區段

對於每個您帳單的設定檔 下方的 發票 區段中，您會看到費用、 的套用的 Azure 信用額度金額、 稅金和總金額。

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>發票區段的詳細資料

詳細資料會顯示每個發票區段依產品訂單細分的成本。 在每個產品順序，成本細分所服務的型別。 您可以找到您的產品和服務的每日費用，在 Azure 入口網站和 Azure 使用量和費用 CSV。 若要了解更多，請參閱[了解發票費用的 Microsoft 客戶合約](billing-mca-understand-your-bill.md)。

應付總額的每個服務系列的計算方式是減去*Azure 信用額度*從*信用額度/費用*並加入*稅務*:

<!-- `Total = Charges/Credits - Azure Credit + Tax` -->

![發票區段的詳細資料](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| 詞彙 |描述 |
| --- | --- |
| 單價 | 有效的單位價格 （在定價貨幣） 服務的使用頻率的使用方式。 這是唯一的產品、 服務系列、 計量、 和供應項目。 |
| Qty | 購買或計費期間取用的數量 |
| 費用/信用額度 | 費用套用信用額度/退款之後的 net 數量 |
| Azure 信用額度 | 套用至費用/信用額度的 Azure 信用額度金額|
| 稅率 | 根據國家/地區的稅務 rate(s) |
| 稅額 | 若要購買的稅率的數量取決於稅率 |
| 總計 | 總金額購買 |

### <a name="how-to-pay"></a>如何用多少付多少

發票底部有指示，而無須支付您的帳單。 付款核取，網路，或在線上。 如果您用多少付多少線上，您可以使用信用卡/轉帳卡或 Azure 信用額度，如果適用的話。

### <a name="publisher-information"></a>發行者資訊

如果您有第三方服務，您的帳單中，每個 「 發行者 」 的地址與名稱列在您發票底部。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [了解帳單設定檔的發票費用](billing-mca-understand-your-bill.md)
- [如何取得您的 Azure 帳單發票和每日使用量資料](billing-download-azure-invoice-daily-usage-date.md)
- [檢視您組織的 Azure 定價](billing-ea-pricing.md)
- [檢視帳單設定檔的稅務文件](billing-mca-download-tax-document.md)
