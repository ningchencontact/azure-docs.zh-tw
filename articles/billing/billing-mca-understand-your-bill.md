---
title: 了解您的 Microsoft 客戶協議的發票-Azure 上的費用
description: 了解如何閱讀並了解發票的費用。
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
ms.openlocfilehash: ee250589133abb1944ff17e39dc650cbae4279c6
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490668"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>了解 Microsoft 的客戶合約發票費用

您可以瞭解您發票上的費用，藉由分析個別的交易。 Microsoft 客戶合約的帳單帳戶，請在產生的每個計費的設定檔的每個月的發票。 發票會包含上個月的所有費用。 您可以在 Azure 入口網站中檢視您的發票。 如需詳細資訊，請參閱 <<c0> [ 下載發票的 Microsoft 客戶合約](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement)。

這篇文章適用於計費的帳戶 Microsoft 客戶合約。 [檢查您是否擁有存取權的 Microsoft 客戶合約](#check-access-to-a-microsoft-customer-agreement)。

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>發票，Azure 入口網站中檢視交易

1. 登入 [Azure 入口網站](https://www.azure.com)。

2. 搜尋**成本管理 + 計費**。

    ![螢幕擷取畫面顯示 Azure 入口網站的搜尋，成本管理 + 計費](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 選取 **所有交易**從頁面的左側。 根據您的存取權，您可能必須選取計費的帳戶、 計費的設定檔或 [發票] 區段，然後選取**所有交易**。

4. 所有的 [交易] 頁面會顯示下列資訊：

    ![顯示計費的交易清單的螢幕擷取畫面](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |欄  |定義  |
    |---------|---------|
    |Date     | 交易日期  |
    |發票識別碼     | 交易有以計費發票識別碼。 如果您提交支援要求，則將識別碼分享 Azure 的支援，以加速您的支援要求 |
    |交易類型     |  購買、 cancel 和使用量的費用等交易的類型  |
    |產品系列     | 虛擬機器的計算或 Azure SQL database 的資料庫之類的產品類別目錄|
    |產品 sku     | 識別您產品的執行個體的唯一程式碼 |
    |Amount     |  交易的數量      |
    |發票區段     | 交易就會出現在本節中的帳單設定檔的發票 |
    |帳單設定檔     | 交易就會出現在帳單設定檔的發票 |

5. 搜尋以篩選的發票中交易的發票識別碼。

### <a name="view-transactions-by-invoice-sections"></a>依發票區段的 檢視交易

發票的各節可協助您組織的計費設定檔的發票的成本。 如需詳細資訊，請參閱 <<c0> [ 了解發票區段](billing-mca-overview.md#invoice-sections)。 產生發票時，用於計費的設定檔中的所有區段的費用會顯示在發票上。

下圖顯示範例的發票的費用會計部門發票一節。

![範例影像中顯示的發票區段資訊的詳細資料](./media/billing-understand-your-bill-mca/invoicesection-details.png)

當您已經識別的發票區段的費用時，您可以檢視交易，在 Azure 入口網站，以了解費用。

1. 前往 Azure 入口網站，檢視發票的交易中所有的 [交易] 頁面。 如需詳細資訊，請參閱 <<c0> [ 在 Azure 入口網站中檢視發票的交易](#view-transactions-for-an-invoice-in-the-azure-portal)。

2. 若要檢視交易的發票區段名稱來篩選。

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>檢閱暫止的估計您下一張發票的費用

Microsoft 客戶合約的帳單帳戶，請在會估計費用，並將其視為暫止，直到它們發票中。 您可以檢視擱置中的 Azure 入口網站，若要預估您的下一張發票的費用。 暫止的費用預估金額，它們不含稅。 在您下一張發票上的實際費用也會因暫止的費用。

### <a name="view-summary-of-pending-charges"></a>檢視暫止的費用的摘要

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋**成本管理 + 計費**。

   ![螢幕擷取畫面顯示 Azure 入口網站的搜尋，成本管理 + 計費](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 選取計費的設定檔。 根據您的存取權，您可能要選取的計費帳戶。 從計費的帳戶中，選取**帳單設定檔**然後選取 計費的設定檔。

4. 選取 **摘要**從畫面頂端的索引標籤。

5. 到目前的月份和上個月的費用，就會顯示 [費用] 區段。

   ![螢幕擷取畫面顯示 Azure 入口網站的搜尋，成本管理 + 計費](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

月-日的費用是當月的暫止的費用，並以此計費發票時產生的月份。 如果上個月的發票仍未產生，則上個月的費用也會暫止且會出現在您下一張發票。

### <a name="view-pending-transactions"></a>檢視暫止的交易

當您識別暫止的費用時，您可以了解藉由分析提供費用的個別交易費用。 此時，暫止的使用量費用不會顯示全部的 [交易] 頁面上。 您可以在 Azure 訂用帳戶 頁面上檢視暫止的使用量費用。 如需詳細資訊，請參閱[檢視暫止的使用費用](#view-pending-usage-charges)

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋**成本管理 + 計費**。

   ![螢幕擷取畫面顯示 Azure 入口網站的搜尋，成本管理 + 計費](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 選取計費的設定檔。 根據您的存取權，您可能要選取的計費帳戶。 從計費的帳戶中，選取**帳單設定檔**然後選取 計費的設定檔。

4. 選取 **所有交易**從頁面的左側。

5. 搜尋*暫止*。 使用**Timespan**篩選器來檢視暫止的目前或上個月的費用。

   ![顯示暫止的交易清單的螢幕擷取畫面](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>檢視暫止的使用費用

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋*成本管理 + 計費*。

   ![螢幕擷取畫面顯示 Azure 入口網站的搜尋，成本管理 + 計費](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 選取計費的設定檔。 根據您的存取權，您可能要選取的計費帳戶。 從計費的帳戶中，選取**帳單設定檔**然後選取 計費的設定檔。

4. 選取 **所有訂用帳戶**其中頁面的左側。

5. Azure 訂用帳戶頁面會顯示目前和上個月的費用為每個訂用帳戶計費的設定檔中。 月-日的費用是當月的暫止的費用，並以此計費發票時產生的月份。 如果上個月的發票仍未產生，則上個月的費用也會暫止。

    ![顯示帳單設定檔的 Azure 訂用帳戶清單的螢幕擷取畫面](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>分析您的 Azure 使用量費用

您可以使用 Azure 使用量和費用 CSV 檔案來分析您的使用量費用。 您可以下載檔案的發票或暫止的費用。 如需詳細資訊，請參閱 <<c0> [ 取得您的 Azure 帳單發票和每日使用量資料](billing-download-azure-invoice-daily-usage-date.md)。

### <a name="view-detailed-usage-by-invoice-section"></a>檢視發票區段的詳細的使用量

您可以篩選要協調發票區段的使用量費用的 Azure 使用量和費用檔案。

下列步驟會引導您完成協調計算費用會計部門發票區段：

![範例影像中顯示的發票區段資訊的詳細資料](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | 發票 PDF | Azure 使用量和費用 CSV |
 | --- | --- |
 |會計部門 |invoiceSectionName |
 |使用量費用-Microsoft Azure 方案 |productOrderName |
 |計算 |serviceFamily |

1. 篩選條件**invoiceSectionName** CSV 檔案中的資料行**Accounting Dept**。
2. 篩選條件**productOrderName** CSV 檔案中的資料行**Microsoft Azure 方案**。
3. 篩選條件**serviceFamily** CSV 檔案中的資料行**Microsoft.Compute**。

![螢幕擷取畫面，顯示使用量和費用的發票部分篩選的檔案](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>訂用帳戶來檢視詳細的使用量

您可以篩選來協調您的訂用帳戶的使用量計費 Azure 的 使用量和費用 CSV 檔案。 若要檢視所有訂用帳戶計費的設定檔中，請參閱[檢視暫止的使用費用](#view-pending-usage-charges)。

當您識別訂用帳戶的費用時，可用於 Azure 的使用量和費用 CSV 檔案分析費用。

下圖顯示 Azure 入口網站中的訂用帳戶清單。

![顯示帳單設定檔的 Azure 訂用帳戶清單的螢幕擷取畫面](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

篩選條件**subscriptionName**到 Azure 的 使用量和費用 CSV 檔案中的資料行**WA_Subscription** WA_Subscription 檢視詳細的使用量費用。

![螢幕擷取畫面，顯示使用量和費用依訂用帳戶篩選的檔案](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>支付帳單

發票底部會顯示，而無須支付帳單的相關指示。 [了解如何用多少付多少](billing-mca-understand-your-invoice.md#how-to-pay)。

如果您已經已支付您的帳單，您可以檢查 Azure 入口網站中的 [發票] 頁面上的付款的狀態。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入了解您的發票和詳細的使用量，請參閱：

- [如何取得您的 Azure 帳單發票和每日使用量資料](billing-download-azure-invoice-daily-usage-date.md)
- [了解您的 Microsoft 客戶合約發票上的字詞](billing-mca-understand-your-invoice.md)
- [了解您的 Microsoft 客戶合約使用量 CSV 的字詞](billing-mca-understand-your-usage.md)
