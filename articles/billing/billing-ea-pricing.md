---
title: 檢視及下載您的組織 Azure 定價 |Microsoft Docs
description: 了解如何檢視和下載定價或估計成本，以及您組織的價格。
services: ''
documentationcenter: ''
author: adpick
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: d4c64433089f14d845cea8be9adf0ef13675cfd1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57892467"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>檢視及下載您的組織 Azure 定價

Azure 客戶使用 Enterprise 合約 (EA) 或[Microsoft 客戶合約](#check-your-access-to-a-microsoft-customer-agreement)可能檢視並下載其價格從 Azure 入口網站。 如果您有 Microsoft 客戶合約，請參閱檢視及下載您的 Microsoft 客戶合約的定價。

## <a name="view-and-download-ea-pricing"></a>檢視及下載 EA 價格

根據企業系統管理員為組織所設定的原則，只有特定的系統管理角色可以提供組織 EA 價格資訊的存取權。 如需詳細資訊，請參閱[了解 Azure 中的 Azure Enterprise 合約系統管理角色](billing-understand-ea-roles.md)。

1. 以企業系統管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。
1. 針對 [成本管理 + 帳單] 進行搜尋。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. 在帳單帳戶下方，選取 [使用方式 + 費用]。

   ![在帳單下方顯示使用方式和費用的螢幕擷取畫面](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. 針對當月份選取 ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-ea-pricing/download-icon.png) [下載]。
1. 在 [價位表] 下方，選取 [下載 csv]。

   ![顯示價位表下載 csv 按鈕的螢幕擷取畫面](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="view-and-download-pricing-for-your-microsoft-customer-agreement"></a>檢視及下載您的 Microsoft 客戶合約的定價

您必須是計費的設定檔擁有者、 參與者、 讀取器或發票管理員檢視及下載定價。 若要深入了解計費角色適用於 Microsoft 客戶合約，請參閱[帳單設定檔角色和工作](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)。

### <a name="download-price-sheets-for-the-current-billing-period"></a>目前計費週期下載價位表

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 針對 [成本管理 + 帳單] 進行搜尋。
1. 選取計費的設定檔。 根據您的存取權，您可能需要先選取計費帳戶。
1. 在 **概觀**刀鋒視窗中，尋找下方至今的月份費用的下載連結。
1. 選取  **Azure 價位表**。
![從 [概觀] 顯示下載的螢幕擷取畫面](./media/billing-ea-pricing/open-pricing.png)

### <a name="download-price-sheets-for-billed-charges"></a>下載計費費用的價位表

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 針對 [成本管理 + 帳單] 進行搜尋。
1. 選取計費的設定檔。 根據您的存取權，您可能需要先選取計費帳戶。
1. 選取 [發票]。
1. 在發票方格中，尋找對應至您想要下載的價位表的發票的資料列。
1. 按一下省略符號 (`...`) 結尾的資料列。
![顯示選取的省略符號的螢幕擷取畫面](./media/billing-ea-pricing/billingprofile-invoicegrid.png)

1. 如果您想要查看中選取發票的服務的價格，請選取**發票價位表**。
1. 如果您想要查看針對給定的計費期間內的所有 Azure 服務的價格，請選取**Azure 價位表**。

![顯示包含價位表的內容功能表的螢幕擷取畫面](./media/billing-ea-pricing/contextmenu-pricesheet.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Azure 定價計算機與預估成本

您也可以使用貴組織的價格，以使用 Azure 價格計算機估算成本。

1. 移至 [Azure 價格計算機](https://azure.microsoft.com/pricing/calculator)。
1. 在右上方，選取 [登入]。
1. 在 [程式和供應項目] > [授權方案] 下方，選取 [Enterprise 合約 (EA)]。
1. 在 [程式和供應項目] > [選取的合約] 下方，選取 [未選取任何項目]。

    ![顯示價位表下載 csv 按鈕的螢幕擷取畫面](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. 選擇組織。
1. 選取 [套用] 。
1. 搜尋產品並新增至您的預估。
1. 顯示的預估價格取決於您針對組織所選取的價格。

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>請檢查您的存取權的 Microsoft 客戶合約
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>後續步驟

如果您是 EA 客戶，請參閱：

- [管理您的 EA 計費資訊適用於 Azure 的存取](billing-manage-access.md)
- [檢視及下載您 Microsoft Azure 發票](billing-download-azure-invoice.md)
- [檢視及下載您的 Microsoft Azure 使用量和費用](billing-download-azure-daily-usage.md)
- [了解 Enterprise 合約客戶的帳單](billing-understand-your-bill-ea.md)

如果您有 Microsoft 的客戶合約，請參閱：

- [了解 Microsoft 的客戶合約的價位表中的條款](billing-mca-understand-pricesheet.md)
- [檢視及下載您 Microsoft Azure 發票](billing-download-azure-invoice.md)
- [檢視及下載您的 Microsoft Azure 使用量和費用](billing-download-azure-daily-usage.md)
- [檢視及下載帳單設定檔的稅務文件](billing-mca-download-tax-document.md)
- [了解帳單設定檔的發票費用](billing-mca-understand-your-bill.md)
