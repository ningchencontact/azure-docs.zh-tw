---
title: 在您的發票上建立區段以彙整成本 - Azure
description: 了解如何使用發票區段來彙整成本。
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.topic: conceptual
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: eadaf34dc5bdd93af532362e8f8542de3f17f414
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490797"
---
# <a name="create-sections-on-your-invoice-to-organize-your-costs"></a>在您的發票上建立區段以彙整成本

您可以在發票上建立區段，以依據部門、開發環境或組織的需求來彙整成本。 接著，為其他人授與適當權限，使其能夠建立將費用計入該區段的 Azure 訂用帳戶。 訂用帳戶的任何使用量費用和購買隨後都會計入該區段。 您可以在 Azure 入口網站中檢視該發票區段的總費用，或在 Azure 成本分析中加以檢閱。 如需詳細資訊，請參閱[依發票區段檢視交易](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections)。

本文適用於 Microsoft 客戶合約的計費帳戶。 [請確認您是否有 Microsoft 客戶合約的存取權](#check-access-to-a-microsoft-customer-agreement)。

## <a name="create-an-invoice-section-in-the-azure-portal"></a>在 Azure 入口網站中建立發票區段

若要建立發票區段，您必須是**帳單設定檔擁有者**或**帳單設定檔參與者**。 如需詳細資訊，請參閱[管理帳單設定檔的發票區段](billing-understand-mca-roles.md#manage-invoice-sections-for-billing-profile)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]  。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-mca-section-invoice/billing-search-cost-management-billing.png)

3. 從左側窗格中選取 [發票區段]  。 視存取權之不同，您可能必須選取帳單設定檔或計費帳戶，然後選取 [發票區段]  。

   ![顯示發票區段清單的螢幕擷取畫面](./media/billing-mca-section-invoice/mca-select-invoice-sections.png)

4. 從頁面頂端選取 [新增]  。

5. 輸入發票區段的名稱，然後選取帳單設定檔。 您會看到此帳單設定檔的發票區段反映出每個訂用帳戶的使用量，和您指派給該區段的購買。 

   ![顯示發票區段建立頁面的螢幕擷取畫面](./media/billing-mca-section-invoice/mca-create-invoice-section.png)

6. 選取 [建立]  。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查對 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

- [為 Microsoft 客戶合約建立額外的 Azure 訂用帳戶](billing-mca-create-subscription.md)
- [授與他人建立 Azure 訂用帳戶的權限](billing-mca-create-subscription.md#give-others-permission)
- [向其他計費帳戶中的使用者接管 Azure 訂用帳戶的帳單擁有權](billing-mca-request-billing-ownership.md)
