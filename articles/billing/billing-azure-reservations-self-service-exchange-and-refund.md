---
title: 自助服務交換和 Azure 保留的退款 |Microsoft Docs
description: 了解您可以交換或退還 Azure 保留項目。
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 54578746ea8029a760663edc456660f98358abc5
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009305"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>自助服務交換記錄和退費記錄 Azure 保留項目

Azure 保留的項目會提供彈性，幫助您滿足不斷成長的需求。 您可以用保留來交換相同類型的其他保留。 您也可以退還不再需要的保留，每年最多 50,000 美元。

自助式交換和取消功能不適用於美國政府 Enterprise 合約客戶。 支援其他美國政府訂用帳戶類型，包括隨用隨付和 CSP。

您必須擁有者存取權來交換或退款給現有的保留區的保留項目順序。

## <a name="exchange-an-existing-reserved-instance"></a>交換現有的保留執行個體

您可以交換中的三個快速步驟保留[Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)。

1. 選取您想要退款，然後按一下 保留**Exchange**。  
    ![範例影像中顯示傳回的保留項目](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. 選取您要購買的 VM 產品，並輸入數量。 請確定新購買總數是多個傳回的總計。 [判斷正確的大小，在購買](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)。  
    ![顯示 VM 產品購買與 exchange 的範例影像](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. 檢閱並完成交易。  
    ![顯示 VM 產品購買跟 exchange 一樣，完成傳回的範例影像](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

若要退還保留項目，請移至**保留詳細資料**然後按一下**退款**。

## <a name="how-transactions-are-processed"></a>交易的處理方式

首先，Microsoft 會取消現有的保留區，並退款比例數量的保留項目。 交換時，會處理新的採購單。 Microsoft 會處理使用其中一種下列方法，根據您的帳戶類型和付款方法的退費記錄：

### <a name="enterprise-agreement-customers"></a>Enterprise 合約客戶

Money 新增至財務承諾交換和退款如果原始的購買已使用其中一個。 會使用任何超額發票，因為原始的購買項目會重新開啟，而且 rerated 確定承諾用量。 承諾用量一詞，使用購買此保留項目已不再使用中，如果信用額度將加入目前的 enterprise 合約承諾期限。

如果以超額形式進行原始的購買，Microsoft 就會發出進貨折。

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>隨用隨付的發票付款和 CSP 計劃

取消將原始的保留購買發票，然後建立新的發票的退款。 新的發票交換，顯示退費和新的採購單。 退貨量會調整對購買程序。 如果您只會退還保留項目，然後按比例計算的量會與 Microsoft 一起，並調整針對未來的保留購買。

### <a name="pay-as-you-go-credit-card-customers"></a>隨用隨付信用卡客戶

將原始發票，並建立新的發票。 成本被退還已用於原始的購買的信用卡。 如果您已經變更您的卡片[連絡支援人員](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="exchange-policies"></a>Exchange 原則

- 您可以傳回多個現有的保留購買新的保留型別相同。 您無法交換一種類型的另一個保留項的目。 例如，您不能傳回購買 SQL 保留的 VM 保留項目。
- 只有保留項目擁有者可以處理交換。 [了解如何新增或變更的使用者可以管理保留](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance#add-or-change-users-who-can-manage-a-reservation)。
- 退費和重新購買，以處理交換，取消和新的採購單建立不同的交易。 按比例計算的保留項目數量會退還保留項目，您折抵。 您需支付完全新的採購單。 按比例計算的保留項目數量是保留項目所傳回的每日按比例計算殘差的值。
- 您可以交換或退款保留項目即使用來購買保留的 enterprise 合約到期和已更新為新的協議。
- 您可以變更任何保留項目屬性，例如大小、 區域、 數量和交換的詞彙。
- 新購買總數應該等於或大於傳回的數量。
- Exchange 的一部分購買新的保留區已從 exchange 的時間開始的新詞彙。
- 沒有任何負面影響或交換的年度限制。

## <a name="refund-policies"></a>退貨原則

- 您的總退款金額不得超過 50,000 美元的 12 個月的滾動時段中。
- 只有保留項目擁有者可以處理退款。 [了解如何新增或變更的使用者可以管理保留](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。
- Microsoft 保留收取任何傳回 12%負面影響的權限，雖然目前不收費的負面影響。

## <a name="exchange-non-premium-storage-for-premium-storage"></a>交換非進階儲存體，進階儲存體

您可以交換購買不支援進階儲存體以相對應的 VM 大小的 VM 大小的保留項目。 例如， _F1_ for _F1s_。 進行交換，請移至保留詳細資料，並按一下**Exchange**。 Exchange 不重設保留的執行個體的詞彙，或建立新的交易。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

- 若要了解如何管理保留項目，請參閱[管理 Azure 保留項目](billing-manage-reserved-vm-instance.md)。
- 若要深入了解 Azure 保留項目，請參閱下列文章：
    - [什麼是 Azure 保留項目？](billing-save-compute-costs-reservations.md)
    - [管理 Azure 中的保留](billing-manage-reserved-vm-instance.md)
    - [了解保留項目折扣的套用方式](billing-understand-vm-reservation-charges.md)
    - [了解預付型方案訂用帳戶的保留項目使用量](billing-understand-reserved-instance-usage.md)
    - [了解 Enterprise 註冊的保留項目使用量](billing-understand-reserved-instance-usage-ea.md)
    - [Windows 軟體的成本不包括在保留項目內](billing-reserved-instance-windows-software-costs.md)
    - [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](/partner-center/azure-reservations)
