---
title: 追蹤 Microsoft 客戶合約的 Azure 信用額度餘額
description: 了解如何檢查 Microsoft 客戶合約的 Azure 信用額度餘額。
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 352737b3ea61a51a39e066d4211c8f4ceae74184
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490971"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>追蹤 Microsoft 客戶合約的 Azure 信用額度餘額

您可以在 Azure 入口網站中，檢查 Microsoft 客戶合約的 Azure 信用額度餘額。 您可以使用點數來支付費用，係由其信用額度。

當您使用產品所未涵蓋的信用額度，或您的使用量超過您的信用結餘，就會向您收費。 如需詳細資訊，請參閱 [Azure 信用額度未涵蓋的產品。 (#products-that-aren't-covered-by-azure-credits)。

這篇文章適用於計費的帳戶 Microsoft 客戶合約。 [檢查您是否擁有存取權的 Microsoft 客戶合約](#check-access-to-a-microsoft-customer-agreement)。

## <a name="check-your-credit-balance"></a>請檢查您的信用結餘

1. 登入 [Azure 入口網站]( https://portal.azure.com)。

2. 搜尋**成本管理 + 計費**。

    ![成本管理 + 計費入口網站中顯示搜尋螢幕擷取畫面](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  選取  **Azure 信用額度**從左上方。 根據您的存取權，您可能需要選取的帳單帳戶或帳單的設定檔，然後選取**Azure 信用額度**。

4. Azure 信用額度 頁面會顯示下列資訊：

   ![螢幕擷取畫面的信用結餘和帳單設定檔的交易](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | 詞彙               | 定義                           |
   |--------------------|--------------------------------------------------------|
   | 估計的平衡  | 估計的信用額度金額必須考慮所有計費後和暫止的交易 |
   | 目前餘額    | 自前期發票開立的信用額度數量。 它不包含任何暫止的交易 |
   | 交易       | 所有計費的交易影響您的 Azure 信用額度餘額 |

   當您預估的餘額遞減到 0 時，收費以您的所有使用量，包括適用於係由其信用額度的產品。

6. 選取 **信用額度清單**計費的設定檔的信用額度的檢視清單。 信用額度清單會提供下列資訊：

   ![帳單設定檔的信用額度清單的螢幕擷取畫面](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | 詞彙 | 定義 |
   |---|---|
   | 估計的平衡 | 您有減去未開立帳單的信用卡符合資格的費用，從您目前的餘額之後的 Azure 信用額度金額|
   | 目前餘額 | 您必須考慮未開立帳單的信用卡符合資格的費用之前的 Azure 點數的數量。 其計算方式收到新的 Azure 點數加入當時的前期發票開立的信用結餘|
   | source | 取得來源的信用額度 |
   | 開始日期 | 當您取得信用額度的日期 |
   | 到期日期 | 信用額度到期日期 |
   | 餘額 | 自前期發票開立平衡 |
   | 原始的數量 | 原始的信用額度 |
   | 狀態 | 信用額度的目前狀態。 狀態可以是作用中、 使用、 過期或即將到期 |

## <a name="how-credits-are-used"></a>如何使用信用額度

Microsoft 客戶合約的帳單帳戶，在中，您可以使用計費的設定檔來管理您的發票和付款方法。 每個計費的設定檔會產生每月的發票，並按發票付款的情況下，您在使用付款方法。

Azure 信用額度是其中一種付款方法。 您可以從 Microsoft 取得信用額度促銷的點數和服務層級的信用額度。 這些信用額度指派給帳單的設定檔。 發票時產生的計費的設定檔，信用額度會自動套用計費來計算您需要支付的金額的總量。 您支付剩餘的數量與另一個付款方法，例如核取或傳輸的連線。

## <a name="products-that-arent-covered-by-azure-credits"></a>未涵蓋的 Azure 信用額度的產品

 您的 Azure 信用額度不涵蓋下列產品。 您需支付使用這些產品，無論您的信用額度餘額：

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- 已註冊的使用者
- Openlogic
- 遠端存取權限 XenApp Essentials 已註冊的使用者
- Ubuntu Advantage
- Visual Studio Enterprise (每月)
- Visual Studio Enterprise (每年)
- Visual Studio Professional (每月)
- Visual Studio Professional (每年)
- Azure Marketplace 產品
- Azure 支援方案

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

- [了解 Microsoft 的客戶合約帳單帳戶](billing-mca-overview.md)
- [了解您的 Microsoft 客戶合約發票上的字詞](billing-mca-understand-your-invoice.md)
