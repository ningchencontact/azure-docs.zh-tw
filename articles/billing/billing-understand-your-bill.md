---
title: 了解 Azure 帳單 | Microsoft Docs
description: 學習如何閱讀並了解 Azure 訂用帳戶的使用情形和帳單
services: ''
documentationcenter: ''
author: tonguyen10
manager: alherz
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: cwatson
ms.openlocfilehash: e1d9c348f83600b48f4b202c8660d9387dfc5484
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856362"
---
# <a name="understand-your-bill-for-microsoft-azure"></a>了解 Microsoft Azure 帳單
若要了解您的 Azure 帳單，請比較您包含詳細每日使用量檔案的發票，與 Azure 入口網站中的成本管理報告。

本文不適用於 Azure Enterprise 合約客戶 (EA 客戶)。 若您是 EA 客戶，請參閱[了解 Azure Enterprise 合約客戶的帳單](billing-understand-your-bill-ea.md)。  

如需 Azure 雲端解決方案提供者 (Azure CSP) 計畫的計費方式說明 (包括計費週期、定價和使用量)，請參閱 [Azure CSP 計費概觀](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/)。

## <a name="charges"></a>檢閱您的費用

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

若您的發票上有一筆您想要了解詳細資訊的費用，您可以將使用量與成本和使用量檔案或 Azure 入口網站進行比較。

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>選項 1：將使用量和成本與使用量檔案進行比較

詳細使用量 CSV 檔案會顯示依計費期間和每日使用量的費用。 若要取得檔案，請參閱[取得您的 Azure 帳單發票和每日使用量資料](billing-download-azure-invoice-daily-usage-date.md)。

使用量費用會顯示在計量等級中。 下列字詞在發票和詳細使用量檔案代表相同意義。 例如，發票上的計費週期與詳細使用量檔案中所顯示的計費期間相同。

 | 發票 (PDF) | 詳細使用量 (CSV)|
 | --- | --- |
|計費週期 | 計費期間 |
 |Name |計量類別 |
 |類型 |計量子類別 |
 |資源 |計量名稱 |
 |區域 |計量區域 |
 |已耗用 |已耗用的數量 |
 |已包括 |已包括的數量 |
 |可計費 |超額數量 |

您發票的**使用量費用**區段會包含您計費期間所耗用的每個計量器總計值。 例如，下列螢幕擷取畫面顯示 Azure 排程器服務的使用量費用。

![發票使用量費用](./media/billing-understand-your-bill/1.png)

詳細使用量 CSV 的**明細**區段會顯示相同的費用。 「已使用」數量和「值」會與發票相符。

![CSV 使用量費用](./media/billing-understand-your-bill/2.png)

若要查看這筆費用的每日明細，請移至 CSV 的 [每日使用量] 區段。 在 [計量類別] 下方篩選「排程器」。 您可以查看哪幾天有使用計量以及取用的量。 *資源*和*資源群組*資訊也會列出以供比較。 「已使用」值加起來應該等於發票上所顯示的值。

![CSV 中的 [每日使用量] 區段](./media/billing-understand-your-bill/3.png)

若要取得每日成本，請將「已使用」數量乘上**明細**區段中的「費率」值。

若要深入了解，請參閱：

- [了解您的 Azure 發票](billing-understand-your-invoice.md)
- [了解您的 Azure 詳細使用量](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-with-the-azure-portal"></a>選項 2：將使用量與成本與 Azure 入口網站進行比較

Azure 入口網站也可協助您確認費用。 若要取得您發票上使用量與費用的快速概觀，請檢視成本管理圖表。

1. 在 Azure 入口網站中，前往[訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 選取您的訂用帳戶 > [成本分析]。 
1. 依 [時間範圍] 篩選。
1. 繼續先前的範例，您會看到 Azure 排程器服務的使用費。

   ![Azure 入口網站中的成本分析檢視](./media/billing-understand-your-bill/4.png)

1. 選取該列來查看每日成本明細。

   ![Azure 入口網站中的成本記錄檢視](./media/billing-understand-your-bill/5.png)

若要進一步了解，請參閱[使用 Azure 計費與成本管理避免非預期的成本](billing-getting-started.md#costs)。

## <a name="external"></a>外部服務會分開計費

外部服務 (或 Marketplace) 費用為協力廠商軟體廠商建立的資源費用。 您可以從 Azure Marketplace 取得並使用這些資源。 例如，Barracuda Firewall 是由協力廠商提供的 Azure Marketplace 資源。 適用於該防火牆和其相對應計量的所有費用都會顯示為外部服務費用。

外部服務費用會分開計費。 費用不會顯示在 Azure 發票上。 若要深入了解，請參閱[了解您的 Azure 外部服務費用](billing-understand-your-azure-marketplace-charges.md)。

## <a name="resources-billed-by-usage-meters"></a>依使用量計量計費的資源

Azure 不會直接根據資源成本計費。 資源費用會使用一或多個計量來計算。 這些計量會用來追蹤資源於存留期間內的使用量。 接著，這些計量會用來計算帳單金額。

例如，當您建立單一 Azure 資源 (例如虛擬機器) 時，它便會建立一或多個計量執行個體。 這些計量是用來追蹤一段時間內的資源使用量。 每種計量都會發出使用量記錄，Azure 會使用這些記錄來計費。

例如，在 Azure 中建立的單一虛擬機器 (VM) 可能會建立下列計量來追蹤其使用量：

- 計算時數
- IP 位址時數
- 資料傳入
- 資料傳出
- 標準受控磁碟
- 標準受控磁碟作業
- 標準 IO 磁碟
- 標準 IO - 區塊 Blob 讀取
- 標準 IO - 區塊 Blob 寫入
- 標準 IO - 區塊 Blob 刪除

建立 VM 時，上述每一個計量便會開始發出使用量記錄。 Azure 計量系統中會追蹤此使用量和計量的價格。

## <a name="payment"></a>支付您的帳單

如果您設定信用卡或轉帳卡作為付款方式，我們會在計費期間結束後的 10 天內自動扣款。 在您的信用卡對帳單上，明細項目的名稱會是 **MSFT Azure**。

若要變更用來支付費用的信用卡或轉帳卡，請參閱[新增、更新或移除 Azure 的信用卡或轉帳卡](billing-how-to-change-credit-card.md)。

如果您透過[開立發票來付款](billing-how-to-pay-by-invoice.md)，請將您的款項匯至發票底部所列的位置。

若要檢查付款狀態，請[建立支援票證](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。


## <a name="tips-for-cost-management"></a>成本管理的秘訣

- 使用下列工具來估計成本：
  - [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)
  - [擁有權總成本 (TCO) 計算機](https://aka.ms/azure-tco-calculator)
  - [每種服務的詳細定價資訊](https://azure.microsoft.com/pricing/)
- [定期在 Azure 入口網站檢閱您的使用量和成本](billing-getting-started.md#costs)。

## <a name="learn-more"></a>深入了解

- [取得您的 Azure 計費週期和每日使用量資料](billing-download-azure-invoice-daily-usage-date.md)
- [了解您 Microsoft Azure 發票上的字詞](billing-understand-your-invoice.md)
- [了解您 Microsoft Azure 詳細使用量上的字詞](billing-understand-your-usage.md)
- [Azure 入口網站成本管理](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [使用 Azure 計費與成本管理避免非預期的成本](billing-getting-started.md#costs)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
