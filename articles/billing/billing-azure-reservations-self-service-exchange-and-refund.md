---
title: 適用于 Azure 保留的自助交換和退款
description: 瞭解您可以如何交換或退款 Azure 保留。
author: yashesvi
manager: yashesvi
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2019
ms.author: banders
ms.openlocfilehash: 828bc3784a118a81adc4391b1bf222c00ee2025a
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543013"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>適用于 Azure 保留的自助交換和退款

Azure 保留可提供彈性, 以協助滿足您不斷演進的需求。 您可以用保留來交換相同類型的其他保留。 您也可以退還不再需要的保留，每年最多 50,000 美元。

自助式交換和取消功能不適用於美國政府 Enterprise 合約客戶。 支援其他美國政府訂用帳戶類型, 包括隨用隨付和 CSP。

您必須擁有保留訂單的擁有者存取權, 才能交換或退款現有的保留。

## <a name="exchange-an-existing-reserved-instance"></a>交換現有的保留實例

您可以使用[Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)中的三個快速步驟來交換您的保留。

1. 選取您想要退款的保留區, 然後按一下 [ **Exchange**]。  
    ![顯示要傳回之保留的範例影像](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. 選取您要購買的 VM 產品，並輸入數量。 請確定新的購買總計大於退貨總數。 在[購買之前, 請先決定適當的大小](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)。  
    ![顯示要使用 exchange 購買之 VM 產品的範例影像](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. 檢查並完成交易。  
    ![顯示要向 exchange 購買之 VM 產品的範例影像, 完成退貨](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

若要退款保留, 請移至 [**保留詳細資料**], 然後按一下 [**退款**]。

## <a name="how-transactions-are-processed"></a>交易的處理方式

首先, Microsoft 會取消現有的保留, 並退款該保留的以比例計算的金額。 如果有交換, 則會處理新的購買。 Microsoft 會根據您的帳戶類型和付款條件, 使用下列其中一種方法來處理退款:

### <a name="enterprise-agreement-customers"></a>Enterprise 合約客戶

如果原始購買是使用帳戶, 則會將款項新增至交換和退款的承諾用量金額。 自原始購買後的任何超額發票都會重新開啟並 rerated, 以確保使用承諾用量金額。 如果使用保留期的承諾用量金額已不再有效, 則會將點數新增至您目前的 enterprise 合約承諾用量期限。

如果原始購買是以超額部分進行, Microsoft 會發出信用備忘錄。

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>隨用隨付發票付款和 CSP 計畫

原始保留購買發票會取消, 然後為退款建立新發票。 針對交換, 新發票會顯示退款和新購買。 退款金額會針對購買進行調整。 如果您只退款了保留, 則按比例計算的金額會與 Microsoft 保持一致, 並會針對未來購買的保留進行調整。

### <a name="pay-as-you-go-credit-card-customers"></a>隨用隨付信用卡客戶

原始發票會取消, 並建立新的發票。 Money 會退款到用於原始購買的信用卡。 如果您已變更您的卡片, 請[聯絡支援](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)人員。

## <a name="cancel-exchange-and-refund-policies"></a>取消、交換和退款原則

Azure 具有用於取消、交換和退款的下列原則。

**Exchange 原則**

- 您可以傳回多個現有的保留, 以購買相同類型的新保留。 您無法交換另一個類型的保留。 例如, 您無法傳回 VM 保留以購買 SQL 保留區。
- 只有保留擁有者可以處理交換。 [瞭解如何新增或變更可以管理保留的使用者](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。
- 交換會以退款和重新購買來處理–針對取消和新的購買建立不同的交易。 針對您所進行的保留, 會以按比例計算的保留量。 您會完全收取新購買的費用。 按比例計算的保留數量是所傳回保留的每日按比例剩餘值。
- 即使用來購買保留的 enterprise 合約已到期, 並已更新為新合約, 您也可以交換或退款保留。
- 您可以使用交換來變更任何保留屬性, 例如大小、區域、數量和詞彙。
- 新的購買總計應該等於或大於傳回的金額。
- 隨著 exchange 一起購買的新保留, 從 exchange 的時間開始就有了新的詞彙。
- 交換沒有任何損失或年度限制。

**退款原則**
- 如果您取消保留, 可能會有 12% 的提前終止費用。
- 您在取消時收到的退款是餘額減去 12% 提前終止費用的餘額。 若要取消, 請移至 Azure 入口網站中的保留區, 然後選取 [**退款**]。
- 您的總退款金額在12個月的輪流時段內不得超過 $50000 美元。
- 退款是根據您購買價格或保留的目前價格的最低價格來計算。
- 只有保留擁有者可以處理退款。 [瞭解如何新增或變更可以管理保留的使用者](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。
- Microsoft 保留對任何退貨收取 12% 負面影響的權利。 目前不會對損失收費, 但會在未來收取費用。

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Premium 儲存體的 Exchange 非 premium 儲存體

針對不支援高階儲存體的 VM 大小, 您可以將所購買的保留交換至執行的對應 VM 大小。 例如, _F1s_的_F1_ 。 若要進行交換, 請移至 [保留詳細資料], 然後按一下 [ **exchange**]。 交換並不會重設保留實例的期限, 也不會建立新的交易。

## <a name="need-help-contact-us"></a>需要協助嗎? 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

- 若要了解如何管理保留項目，請參閱[管理 Azure 保留項目](billing-manage-reserved-vm-instance.md)。
- 若要深入了解 Azure 保留項目，請參閱下列文章：
    - [什麼是 Azure 保留項目？](billing-save-compute-costs-reservations.md)
    - [管理 Azure 中的保留](billing-manage-reserved-vm-instance.md)
    - [了解保留項目折扣的套用方式](billing-understand-vm-reservation-charges.md)
    - [了解隨用隨付訂用帳戶的保留使用量](billing-understand-reserved-instance-usage.md)
    - [了解 Enterprise 註冊的保留項目使用量](billing-understand-reserved-instance-usage-ea.md)
    - [Windows 軟體的成本不包括在保留項目內](billing-reserved-instance-windows-software-costs.md)
    - [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](/partner-center/azure-reservations)
