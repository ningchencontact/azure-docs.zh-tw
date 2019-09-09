---
title: 購買預先或每月付款的 Azure 保留
description: 瞭解如何購買預先或每月付款的 Azure 保留。
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: banders
ms.openlocfilehash: d211334ad2aa760cd63b98c6827fb2512811a1d3
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806944"
---
# <a name="purchase-reservations-with-monthly-payments"></a>購買每月付款的保留期

到目前為止，Azure 保留期必須提前付款。 現在您可以使用每月付款來支付保留費用。 不同于您支付全額的前期購買，每月付款選項會將保留的總成本平均分配給每個月的期限。 預付前和每月保留的總成本是相同的，當您選擇按月支付時，您不需要支付任何額外費用。

您的每月付款金額可能會有所不同，取決於您當地貨幣的當月市場匯率。

每月付款適用于：

- 虛擬機器
- SQL Database
- SQL 資料倉儲
- Cosmos DB
- App Service 戳記費用

[Azure 入口網站](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade)中的購買保留專案。

![顯示保留購買的範例](./media/billing-monthly-payments-reservations/purchase-reservation.png)

在進行保留購買時，您可以查看付款排程。 按一下 [ **View full 付款排程**]。

![顯示保留付款排程的範例](./media/billing-monthly-payments-reservations/prepurchase-schedule.png)

若要在購買後查看付款排程，請選取 [保留]，按一下 [**保留訂單識別碼**]，然後按一下 [**付款**] 索引標籤。

## <a name="view-payments-made"></a>查看所付的款項

您可以使用 Api、使用量資料和成本分析來查看所建立的付款。 針對每月付費的保留期，[頻率] 值會顯示為 [使用方式資料] 和 [保留費用] API 中的**週期性**。 針對前付費的保留，此值會顯示為**onetime**。

成本分析會顯示預設 view 中的每月購買。 針對 [**費用類型**] 和 [**週期性週期**] 套用**採購**篩選 **，以查看**所有購買。 若只要查看保留，請對**保留**套用篩選。

![在成本分析中顯示保留購買成本的範例](./media/billing-monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>在續訂時切換到每月付款

當您更新保留時，您可以將計費頻率變更為每月。

## <a name="exchange-and-refunds"></a>Exchange 和退款

就像其他保留專案一樣，您可以使用每月計費來退款或交換購買的保留。 目前，您可以提交支援要求，以開始交換或退款以每月計費購買的保留。

當您交換每月付費的保留時，新購買的總存留期成本應大於針對傳回的保留所取消的剩餘付款。 交換沒有其他限制或費用。 您可以交換預先付費的保留，以購買每月計費的新保留。 不過，新保留的存留期值應該大於所傳回之保留的按比例值。

如果取消每月付費的保留，Microsoft 可能會將取消費用套用到未來認可的付款。 剩餘的認可付款會累積到 $50000 美元的退款限制。

如需 exchange 和退款的詳細資訊，請參閱[Azure 保留的自助交換和退款](billing-azure-reservations-self-service-exchange-and-refund.md)。

## <a name="faq"></a>常見問題集

問： Azure 是否提供「部分前置保留？」<br>
A. 資料分割 因為保留的預付和每月費用都相同，所以 Microsoft 不支援部分前期付款。

問： Microsoft Cloud 解決方案提供者（CSP）計畫的每月付款是否可用？<br>
A. 是，合作夥伴可以在 Azure 入口網站中購買其 CSP 客戶的保留。 在合作夥伴中心內，無法使用每月計費來購買保留的功能。

問： 我是美國 Azure Government 客戶，是否可以按月支付保留購買費用？<br>
A. 目前不支援。

問： 我何時可以在 Azure 入口網站中交換或退款，而不是建立支援票證？<br>
A. 目前不支援。 使用每月付款的交換要求和退款保留會由 Azure 支援來處理。

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解保留，請參閱[什麼是 Azure 保留專案？](billing-save-compute-costs-reservations.md)
- 若要瞭解購買保留之前應完成的工作，請參閱在[購買之前先決定正確的 VM 大小](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)
