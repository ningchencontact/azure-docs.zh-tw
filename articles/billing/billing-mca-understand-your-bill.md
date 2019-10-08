---
title: 了解 Microsoft 客戶合約發票上的費用 - Azure
description: 學習如何閱讀並了解發票上的費用。
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 8ab6cf061531a1ef3c72b2f36d25932c7498d291
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345279"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>了解 Microsoft 客戶合約發票上的費用

您可以藉由分析個別交易來了解發票上的費用。 在 Microsoft 客戶合約的計費帳戶中，每個帳單設定檔都會按月產生發票。 發票中包含上個月的所有費用。 您可以在 Azure 入口網站中檢視您的發票。 如需詳細資訊，請參閱[下載 Microsoft 客戶合約的發票](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement)。

本文適用於 Microsoft 客戶合約的計費帳戶。 [請確認您是否有 Microsoft 客戶合約的存取權](#check-access-to-a-microsoft-customer-agreement)。

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>在 Azure 入口網站中檢視發票的交易

1. 登入 [Azure 入口網站](https://www.azure.com)。

2. 搜尋 [成本管理 + 帳單]  。

    ![顯示在 Azure 入口網站中搜尋 [成本管理 + 帳單] 的螢幕擷取畫面](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 從頁面左側選取 [所有交易]  。 根據您的存取權，您可能必須選取計費帳戶、帳單設定檔或發票區段，然後選取 [所有交易]  。

4. [所有交易] 頁面會顯示下列資訊：

    ![顯示計費交易清單的螢幕擷取畫面](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |欄  |定義  |
    |---------|---------|
    |Date     | 交易日期  |
    |發票識別碼     | 交易計費所在發票的識別碼。 如果您提交支援要求，請將識別碼提供給 Azure 支援，以加速支援要求的處理 |
    |交易類型     |  交易的類型，例如購買、取消和使用量費用  |
    |產品系列     | 產品的類別，例如 Azure SQL Database 的虛擬機器或資料庫的計算|
    |產品 SKU     | 識別產品執行個體的唯一代碼 |
    |Amount     |  交易的金額      |
    |發票區段     | 交易會顯示在帳單設定檔的這個發票區段上 |
    |帳單設定檔     | 交易會顯示在此帳單設定檔的發票上 |

5. 搜尋發票識別碼以篩選發票的交易。

### <a name="view-transactions-by-invoice-sections"></a>依發票區段檢視交易

發票區段可協助您彙整帳單設定檔的發票成本。 如需詳細資訊，請參閱[了解發票區段](billing-mca-overview.md#invoice-sections)。 發票產生時，帳單設定檔中所有區段的費用都會顯示在發票上。

下圖顯示範例發票上的會計部門發票區段的費用。

![依發票區段資訊顯示詳細資料的範例影像](./media/billing-understand-your-bill-mca/invoicesection-details.png)

當您識別出發票區段的費用時，您可以在 Azure 入口網站中檢視交易以了解費用。

1. 移至 Azure 入口網站中的 [所有交易] 頁面，以檢視發票的交易。 如需詳細資訊，請參閱[在 Azure 入口網站中檢視發票的交易](#view-transactions-for-an-invoice-in-the-azure-portal)。

2. 依發票區段名稱篩選以檢視交易。

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>檢閱待決費用以預估您的下一張發票

在 Microsoft 客戶合約的計費帳戶中，費用在開立發票前，將會受到預估且被視為待決。 您可以在 Azure 入口網站中檢視待決費用，以預估下一張發票。 待決費用會受到預估，且不包含稅金。 下一張發票上的實際費用會與待決費用不同。

### <a name="view-summary-of-pending-charges"></a>檢視待決費用的摘要

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]  。

   ![顯示在 Azure 入口網站中搜尋 [成本管理 + 帳單] 的螢幕擷取畫面](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 選取帳單設定檔。 視存取權之不同，您可能必須選取計費帳戶。 從計費帳戶中選取 [帳單設定檔]  ，然後選取帳單設定檔。

4. 從畫面頂端選取 [摘要]  索引標籤。

5. 費用區段會顯示當月費用和上個月的費用。

   ![顯示在 Azure 入口網站中搜尋 [成本管理 + 帳單] 的螢幕擷取畫面](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

當月費用是當月的待決費用，會在當月的發票產生時計費。 如果上個月的發票尚未產生，則上個月的費用也會列為待決，且會顯示在下一張發票上。

### <a name="view-pending-transactions"></a>檢視擱置交易

當您識別出待決費用時，您可以藉由分析在費用中有佔比的個別交易來了解費用。 此時，待決的使用量費用不會顯示在 [所有交易] 頁面上。 您可以在 Azure 訂用帳戶頁面上檢視待決的使用量費用。 如需詳細資訊，請參閱[檢視待決的使用量費用](#view-pending-usage-charges)

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]  。

   ![顯示在 Azure 入口網站中搜尋 [成本管理 + 帳單] 的螢幕擷取畫面](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 選取帳單設定檔。 視存取權之不同，您可能必須選取計費帳戶。 從計費帳戶中選取 [帳單設定檔]  ，然後選取帳單設定檔。

4. 從頁面左側選取 [所有交易]  。

5. 搜尋「擱置」  。 使用 [時間範圍]  篩選器來檢視當月或上個月的待決費用。

   ![顯示擱置交易清單的螢幕擷取畫面](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>檢視待決的使用量費用

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]  。

   ![顯示在 Azure 入口網站中搜尋 [成本管理 + 帳單] 的螢幕擷取畫面](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 選取帳單設定檔。 視存取權之不同，您可能必須選取計費帳戶。 從計費帳戶中選取 [帳單設定檔]  ，然後選取帳單設定檔。

4. 選取頁面左側的 [所有訂用帳戶]  。

5. [Azure 訂用帳戶] 頁面會顯示帳單設定檔中每個訂用帳戶當月和上個月的費用。 當月費用是當月的待決費用，會在當月的發票產生時計費。 如果上個月的發票尚未產生，則上個月的費用也會列為待決。

    ![顯示帳單設定檔的 Azure 訂用帳戶清單的螢幕擷取畫面](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>分析您的 Azure 使用量費用

您可以使用 Azure 使用量和費用 CSV 檔案，分析您以使用量為基礎的費用。 您可以下載發票或待決費用的檔案。 如需詳細資訊，請參閱[取得您的 Azure 帳單發票和每日使用量資料](billing-download-azure-invoice-daily-usage-date.md)。

### <a name="view-detailed-usage-by-invoice-section"></a>依發票區段檢視詳細使用量

您可以篩選 Azure 使用量和費用檔案，以協調發票區段的使用量費用。

下列步驟會逐步引導您協調會計部門發票區段的計算費用：

![依發票區段資訊顯示詳細資料的範例影像](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | 發票 PDF | Azure 使用量和費用 CSV |
 | --- | --- |
 |會計部門 |invoiceSectionName |
 |使用量費用 - Microsoft Azure 方案 |productOrderName |
 |計算 |serviceFamily |

1. 將 CSV 檔案中的 **invoiceSectionName** 資料行篩選到 [會計部門]  。
2. 將 CSV 檔案中的 **productOrderName** 資料行篩選到 [Microsoft Azure 方案]  。
3. 將 CSV 檔案中的 **serviceFamily** 資料行篩選到 [Microsoft.Compute]  。

![顯示依發票區段篩選的使用量和費用檔案的螢幕擷取畫面](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>依訂用帳戶檢視詳細使用量

您可以篩選 Azure 使用量和費用 CSV 檔案，以協調訂用帳戶的使用量費用。 若要檢視帳單設定檔中的所有訂用帳戶，請參閱[檢視待決的使用量費用](#view-pending-usage-charges)。

當您識別出訂用帳戶的費用時，請使用 Azure 使用量和費用 CSV 檔案來分析費用。

下圖顯示 Azure 入口網站中的訂用帳戶清單。

![顯示帳單設定檔的 Azure 訂用帳戶清單的螢幕擷取畫面](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

將 Azure 使用量和費用 CSV 檔案中的 **subscriptionName** 資料行篩選到 **WA_Subscription**，以檢視 WA_Subscription 的詳細使用量費用。

![顯示依訂用帳戶篩選的使用量和費用檔案的螢幕擷取畫面](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>支付您的帳單

支付帳單的指示會顯示在發票底部。 [了解如何支付](billing-mca-understand-your-invoice.md#how-to-pay)。

如果您已支付帳單，可以在 Azure 入口網站的 [發票] 頁面上查看付款的狀態。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查對 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入了解您的發票和詳細使用量，請參閱：

- [如何取得您的 Azure 帳單發票和每日使用量資料](billing-download-azure-invoice-daily-usage-date.md)
- [了解 Microsoft 客戶合約發票上的詞彙](billing-mca-understand-your-invoice.md)
- [了解 Microsoft 客戶合約使用量 CSV 中的詞彙](billing-mca-understand-your-usage.md)
