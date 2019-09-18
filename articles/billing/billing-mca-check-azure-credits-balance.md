---
title: 追蹤 Microsoft 客戶合約的 Azure 點數餘額
description: 了解如何查看 Microsoft 客戶合約的 Azure 點數餘額。
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
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490971"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>追蹤 Microsoft 客戶合約的 Azure 點數餘額

您可以在 Azure 入口網站中查看 Microsoft 客戶合約的 Azure 點數餘額。 您可以使用點數支付可由點數涵蓋的費用。

若您使用的產品並未以點數涵蓋，或您的使用量超過點數餘額，您就必須支付費用。 如需詳細資訊，請參閱「Azure 點數未涵蓋的產品」(#products-that-arent-covered-by-azure-credits)。

本文適用於 Microsoft 客戶合約的計費帳戶。 [請確認您是否有 Microsoft 客戶合約的存取權](#check-access-to-a-microsoft-customer-agreement)。

## <a name="check-your-credit-balance"></a>查看您的點數餘額

1. 登入 [Azure 入口網站]( https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]  。

    ![顯示在入口網站中搜尋 [成本管理 + 帳單] 的螢幕擷取畫面](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  從左側選取 [Azure 點數]  。 視存取權之不同，您可能必須選取計費帳戶或帳單設定檔，然後選取 [Azure 點數]  。

4. [Azure 點數] 頁面會顯示下列資訊：

   ![帳單設定檔的點數餘額和交易的螢幕擷取畫面](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | 詞彙               | 定義                           |
   |--------------------|--------------------------------------------------------|
   | 估計餘額  | 您在考量所有計費交易和擱置交易後所擁有的估計點數金額 |
   | 目前餘額    | 截至最後一張發票為止的點數金額。 其中不包含任何擱置交易 |
   | 交易       | 所有會對 Azure 點數餘額造成影響的帳單交易 |

   當您的估計餘額降到 0 時，您所有的使用量都必須付費，包括可由點數涵蓋的產品。

6. 選取 [點數清單]  ，以檢視帳單設定檔的點數清單。 點數清單會提供下列資訊：

   ![帳單設定檔點數清單的螢幕擷取畫面](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | 詞彙 | 定義 |
   |---|---|
   | 估計餘額 | 在目前餘額減去可用點數支付但尚未計費的費用後，您所擁有的 Azure 點數金額|
   | 目前餘額 | 在考量可用點數支付但尚未計費的費用前，您所擁有的 Azure 點數金額。 其計算方式，是將您收到的新 Azure 點數加上您最後一張發票上的點數餘額|
   | 來源 | 點數的取得來源 |
   | 開始日期 | 您取得點數的日期 |
   | 到期日期 | 點數到期的日期 |
   | 餘額 | 截至最後一張發票為止的餘額 |
   | 原始金額 | 原始點數金額 |
   | 狀態 | 點數的目前狀態。 狀態可以是有效、已使用、已過期或即將到期 |

## <a name="how-credits-are-used"></a>點數的使用方式

在 Microsoft 客戶合約的計費帳戶中，您可以使用帳單設定檔來管理發票和付款方式。 每個帳單設定檔都會按月產生發票，而您可以使用付款方式來支付發票費用。

Azure 點數是其中一種付款方式。 您會收到 Microsoft 提供的點數，例如促銷點數和服務等級點數。 這些點數會指派給帳單設定檔。 為帳單設定檔產生發票時，點數會自動套用至總計費金額，以計算您需要支付的金額。 您可以使用其他付款方式支付其餘金額，例如支票或電匯。

## <a name="products-that-arent-covered-by-azure-credits"></a>Azure 點數未涵蓋的產品

 您的 Azure 點數不涵蓋下列產品。 無論您有多少點數餘額，都必須支付這些產品的使用費用：

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

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查對 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

- [了解 Microsoft 客戶合約的計費帳戶](billing-mca-overview.md)
- [了解 Microsoft 客戶合約發票上的詞彙](billing-mca-understand-your-invoice.md)
