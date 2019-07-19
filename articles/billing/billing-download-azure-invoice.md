---
title: 檢視及下載您 Microsoft Azure 發票
description: 說明如何查看和下載您的 Microsoft Azure 發票。
keywords: 帳單發票,發票下載,azure 發票,azure 使用量
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: banders
ms.openlocfilehash: 4dc5f88f7a5994e5bcd50d71c86bf1ba35b10734
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321774"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>檢視及下載您 Microsoft Azure 發票

針對大多數的訂用帳戶，您可以從 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)下載您的發票，或透過電子郵件傳送它。 如果您是 Azure Enterprise 合約客戶 (EA 客戶)，您無法下載您的組織發票。 發票會傳送給已設定接收註冊發票的人員。

只有特定角色具有可查看發票的許可權。 例如, 帳戶管理員或企業系統管理員。 如需取得帳單資訊存取權的詳細資訊, 請參閱[使用角色管理 Azure 計費的存取權](billing-manage-access.md)。

如果您有[Microsoft 客戶合約](#check-your-access-to-a-microsoft-customer-agreement), 您必須具有下列其中一個角色, 才能取得您的發票:

- 帳單設定檔擁有者
- 參與者
- 讀取者
- 發票管理員

如需 Microsoft 客戶合約計費角色的詳細資訊, 請參閱[帳單設定檔角色和](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)工作。

## <a name="noinvoice"></a>為什麼您可能無法取得發票

您沒有看到發票的可能原因如下︰

- 您的 Azure 訂閱不滿 30 天。

- Azure 會在您的發票期間結束時向您收取費用。 因此, 可能尚未產生發票。 等到計費期間結束。

- 您無權查看發票。 如果您有 Microsoft 客戶合約, 您必須是帳單設定檔擁有者、參與者、讀者或發票管理員。 對於其他訂用帳戶, 如果您不是帳戶管理員, 則可能不會看到舊的發票。 若要深入了解取得計費資訊的存取權，請參閱[使用角色管理 Azure 計費的存取權](billing-manage-access.md)。

- 如果您的訂用帳戶有免費試用或每月點數金額, 您只會在超過每月點數金額時取得發票。 如果您有 Microsoft 客戶合約, 則一律會收到發票。

## <a name="download-your-azure-invoices-pdf"></a>下載您的 Azure 發票 (.pdf)

對於大部分的訂用帳戶, 您可以從 Azure 入口網站下載發票。 如果您有 Microsoft 客戶合約, 請參閱[下載 Microsoft 客戶合約的發票](#download-invoices-for-a-microsoft-customer-agreement)。

### <a name="download-invoices-for-an-individual-subscription"></a>下載個別訂用帳戶的發票

1. 在 Azure 入口網站的 [[訂閱] 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)上選取您的訂用帳戶, 做為[具有發票存取權的使用者](billing-manage-access.md)。

2. 選取 [發票]  。

    ![顯示 [帳單和使用量] 選項的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. 按 [下載發票]  以檢視 PDF 發票的副本。 如果它顯示**無法使用**，請參閱[為什麼我沒有看到上期的發票？](#noinvoice)

    ![顯示計費期間、下載發票和每個計費期間總費用的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. 按一下計費期間，還可檢視每日使用量。

如需發票的詳細資訊，請參閱[了解 Microsoft Azure 帳單](billing-understand-your-bill.md)。 如需管理成本的協助, 請參閱[使用 Azure 計費與成本管理避免非預期的成本](billing-getting-started.md)。

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>下載 Microsoft 客戶合約的發票

系統會針對 Microsoft 客戶合約中的每個[帳單設定檔](billing-mca-overview.md#billing-profiles)產生發票。 您必須是帳單設定檔擁有者、參與者、讀者或發票管理員, 才能從 Azure 入口網站下載發票。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋*成本管理 + 帳單*。
1. 選取帳單設定檔。 根據您的存取權, 您可能需要先選取帳單帳戶。
1. 選取 [發票]  。
1. 在 [發票] 方格中, 尋找您想要下載之發票的資料列。
1. 按一下資料列結尾`...`處的省略號 ()。
    ![在資料列結尾顯示省略號的螢幕擷取畫面](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. 在 [下載] 內容功能表中, 選取 [**發票**]。

    ![顯示內容功能表的螢幕擷取畫面](./media/billing-download-azure-invoice/contextmenu.png)

如果您沒有看到上一個計費期間的發票, 請參閱[為什麼我沒有看到上一個計費週期的發票？](#noinvoice)

## <a name="get-your-invoice-in-email-pdf"></a>透過電子郵件取得發票 (.pdf)

您可以選擇加入並設定其他收件者來透過電子郵件接收您的 Azure 發票。 這項功能可能無法用於支援供應項目、Enterprise 合約或 Azure in Open 等特定訂用帳戶。 如果您有 Microsoft 客戶合約, 請參閱下一節:[在電子郵件中取得帳單設定檔發票](#get-your-subscriptions-invoices-in-email)。

### <a name="get-your-subscriptions-invoices-in-email"></a>在電子郵件中取得您的訂用帳戶發票

1. 在 [[訂閱] 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)上選取您的訂用帳戶。 針對您擁有的每個訂用帳戶選擇加入。 依序按一下 [發票]  和 [以電子郵件寄送我的發票]  。

    ![顯示選擇加入流程的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. 按一下 [選擇加入]  並接受條款。

    ![顯示加入流程步驟 2 的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. 當您已接受合約時, 您可以設定其他收件者。 移除收件者時，不會再儲存電子郵件地址。 如果您改變主意, 就需要重新新增它們。

    ![顯示加入流程步驟 3 的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

如果在依照這些步驟執行之後並未收到電子郵件，請在[設定檔的通訊喜好設定](https://account.windowsazure.com/profile)中確定您的電子郵件地址是正確的。

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>選擇不要在電子郵件中取得您的訂用帳戶發票

若要選擇不要透過電子郵件取得發票, 請遵循上述步驟, 然後按一下 [退出宣告以**電子郵件寄出的發票**]。 此選項會移除任何已設定為透過電子郵件接收發票的電子郵件地址。 如果您選擇重新加入, 可以重新設定收件者。

 ![顯示退出流程的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>以電子郵件取得您的 Microsoft 客戶合約發票

如果您有 Microsoft 客戶合約, 您可以選擇在電子郵件中取得您的發票。 所有帳單設定檔擁有者、參與者、讀者和發票經理都會以電子郵件取得發票。 讀者無法更新電子郵件發票喜好設定。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋**成本管理 + 帳單**。
1. 選取帳單設定檔。 根據您的存取權, 您可能需要先選取帳單帳戶。
1. 在 [設定]  下方，選取 [屬性]  。
1. 在 [**電子郵件發票**] 底下選取 [**更新電子郵件發票喜好**設定]。

    ![顯示電子郵件發票屬性的螢幕擷取畫面](./media/billing-download-azure-invoice/billingprofile-email.png)

1. 選取 [選擇**加入**]。
1. 按一下 [更新]  。

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>選擇不要以電子郵件取得您的 Microsoft 客戶合約發票

若要選擇不要透過電子郵件取得發票, 請遵循上述步驟, 然後按一下 [**退出**]。所有擁有者、參與者、讀者和發票經理都選擇不透過電子郵件取得發票。 如果您是讀者, 就無法變更電子郵件發票喜好設定。



## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>檢查您對 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入瞭解您的發票和費用, 請參閱:

- [查看及下載您的 Microsoft Azure 使用量和費用](billing-download-azure-daily-usage.md)
- [了解 Microsoft Azure 帳單](billing-understand-your-bill.md)
- [瞭解 Azure 發票上的字詞](billing-understand-your-invoice.md)
- [了解您 Microsoft Azure 詳細使用量上的字詞](billing-understand-your-usage.md)
- [查看貴組織的 Azure 定價](billing-ea-pricing.md)

如果您有 Microsoft 客戶合約, 請參閱:

- [瞭解帳單設定檔的發票費用](billing-mca-understand-your-bill.md)
- [瞭解帳單設定檔的發票上的字詞](billing-mca-understand-your-invoice.md)
- [瞭解您帳單設定檔的 Azure 使用量和費用檔案](billing-mca-understand-your-usage.md)
- [查看及下載帳單設定檔的稅務檔](billing-mca-download-tax-document.md)
- [查看貴組織的 Azure 定價](billing-ea-pricing.md)
