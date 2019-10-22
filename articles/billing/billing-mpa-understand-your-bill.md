---
title: 了解 Microsoft 合作夥伴合約發票上的費用 - Azure
description: 學習如何閱讀並了解發票上的費用。
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2019
ms.author: banders
ms.openlocfilehash: 579a052f8bde780ac33de85c5a08d9b3e79d3096
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515759"
---
# <a name="understand-charges-on-your-microsoft-partner-agreement-invoice"></a>了解 Microsoft 合作夥伴合約發票上的費用

 在 Microsoft 合作夥伴合約的計費帳戶中，每個帳單設定檔都會按月產生發票。 發票中包含上個月的所有客戶費用。 您可以藉由在 Azure 入口網站中分析個別交易來了解發票上的費用。 您也可以在 Azure 入口網站中檢視您的發票。 如需詳細資訊，請參閱[如何從 Azure 入口網站下載發票](billing-download-azure-invoice.md)。

本文適用於 Microsoft 合作夥伴合約的計費帳戶。 [檢查您是否有 Microsoft 合作夥伴合約的存取權](#check-access-to-a-microsoft-partner-agreement)。

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>在 Azure 入口網站中檢視發票的交易

1. 登入 [Azure 入口網站](https://www.azure.com)。

2. 搜尋 [成本管理 + 帳單]  。

   <!--  ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. 從頁面左側選取 [所有交易]  。 根據您的存取權，您可能必須選取計費帳戶、帳單設定檔或客戶，然後選取 [所有交易]  。

4. [所有交易] 頁面會顯示下列資訊：

    <!-- ![Screenshot that shows the billed transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-all-transactions.png) -->

    |欄  |定義  |
    |---------|---------|
    |Date     | 交易日期  |
    |發票識別碼     | 交易計費所在發票的識別碼。 如果您提交支援要求，請將識別碼提供給 Azure 支援，以加速支援要求的處理 |
    |交易類型     |  交易的類型，例如購買、取消和使用量費用  |
    |產品系列     | 產品的類別，例如 Azure SQL Database 的虛擬機器或資料庫的計算|
    |產品 SKU     | 識別產品執行個體的唯一代碼 |
    |Amount     |  交易的金額      |
    |帳單設定檔     | 交易會顯示在此帳單設定檔的發票上 |

5. 搜尋發票識別碼以篩選發票的交易。

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>檢閱待決費用以預估您的下一張發票

在開立發票之前，系統會預估費用並將其視為擱置。 您可以在 Azure 入口網站中檢視 Microsoft 合作夥伴合約帳單設定檔的擱置費用，以預估下一張發票。 待決費用會受到預估，且不包含稅金。 下一張發票上的實際費用會與待決費用不同。

### <a name="view-pending-transactions"></a>檢視擱置交易

當您識別出待決費用時，您可以藉由分析在費用中有佔比的個別交易來了解費用。 此時，待決的使用量費用不會顯示在 [所有交易] 頁面上。 您可以在 Azure 訂用帳戶頁面上檢視待決的使用量費用。 如需詳細資訊，請參閱[檢視待決的使用量費用](#view-pending-usage-charges)

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]  。

   <!-- ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. 選取帳單設定檔。 視存取權之不同，您可能必須選取計費帳戶。 從計費帳戶中選取 [帳單設定檔]  ，然後選取帳單設定檔。

4. 從頁面左側選取 [所有交易]  。

5. 搜尋「擱置」  。 使用 [時間範圍]  篩選器來檢視當月或上個月的待決費用。

   <!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>檢視各客戶的擱置費用

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]  。

3. 選取帳單設定檔。 視存取權之不同，您可能必須選取計費帳戶。 從計費帳戶中選取 [帳單設定檔]  ，然後選取帳單設定檔。

4. 選取頁面左側的 [客戶]  。

    <!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

5. [客戶] 頁面會針對與帳單設定檔相關聯的每個客戶，顯示當月和上個月的費用。 當月費用是當月的待決費用，會在當月的發票產生時計費。 如果上個月的發票尚未產生，則上個月的費用也會列為待決。

### <a name="view-pending-usage-charges"></a>檢視待決的使用量費用

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]  。

3. 選取帳單設定檔。 視存取權之不同，您可能必須選取計費帳戶。 從計費帳戶中選取 [帳單設定檔]  ，然後選取帳單設定檔。

4. 選取頁面左側的 [Azure 訂用帳戶]  。

5. [Azure 訂用帳戶] 頁面會顯示帳單設定檔中每個訂用帳戶當月和上個月的費用。 當月費用是當月的待決費用，會在當月的發票產生時計費。 如果上個月的發票尚未產生，則上個月的費用也會列為待決。

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>分析您的 Azure 使用量費用

您可以使用 Azure 使用量和費用 CSV 檔案，分析您以使用量為基礎的費用。 您可以[從 Azure 入口網站下載您的 Azure 使用量和費用 CSV](billing-download-azure-daily-usage.md)。

您可以篩選 Azure 使用量和費用檔案，以調節發票 pdf 中所列的每項產品的使用量費用。 若要檢視特定產品的詳細使用費用，請將 Azure 使用量和費用 CSV 檔案中的 **product**  資料行篩選為只包含該產品的名稱。

您也可以篩選 Azure 使用量和費用 CSV 檔案中的 **customerName**  資料行，以查看每個客戶的每日使用費用。 如果您想要依 Azure 訂用帳戶檢視每日使用量費用，請篩選 **subscriptionName**  資料行。


## <a name="pay-your-bill"></a>支付您的帳單

支付帳單的指示會顯示在發票底部。 您可以在發票日期的 60 天內經由電匯或支票付費。

如果您已支付帳單，可以在 Azure 入口網站的 [發票] 頁面上查看付款的狀態。

## <a name="check-access-to-a-microsoft-partner-agreement"></a>檢查 Microsoft 合作夥伴合約的存取權
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入了解您的發票和詳細使用量，請參閱：
