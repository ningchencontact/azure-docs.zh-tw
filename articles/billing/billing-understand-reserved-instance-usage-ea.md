---
title: 了解 Azure 保留使用量的 Enterprise 合約
description: 學習如何看懂使用量，以了解 Enterprise 註冊的 Azure 保留套用情形。
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/07/2019
ms.author: banders
ms.openlocfilehash: b2452580eaecc0ab922f8e7db48676f70831a8ca
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66126853"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>取得 Enterprise 合約的保留項目成本與使用量

保留項目成本和使用方式資料可供 Enterprise 合約客戶在 Azure 入口網站和 REST Api。 這篇文章可協助您：

- 取得保留購買資料
- 取得保留項目不足的使用量資料
- 降低保留成本
- 保留使用量的計費
- 計算保留節省量

Marketplace 費用將會合併使用情況資料。 您檢視第一次的合作對象使用、 marketplace 使用方式和從單一資料來源的購買費用。

## <a name="reservation-charges-in-azure-usage-data"></a>在 Azure 的使用量資料的保留項目費用

資料會分成兩個不同的資料集：_實際成本_並_分攤成本_。 這兩個資料集有何不同：

**實際成本**-提供資料給核對與每月帳單。 此資料已保留購買成本。 它具有零 EffectivePrice 收到保留折扣的使用方式。

**分攤成本**-資源取得保留折扣的 EffectiveCost 就是保留的執行個體的按比例計算的成本。 資料集也有未使用的保留項目成本。 未使用的保留區與保留成本的總和會提供的保留每日的分攤的成本。

比較兩個資料集：

| 資料 | 實際的成本資料集 | 分攤的成本資料集 |
| --- | --- | --- |
| 保留購買 | 在此檢視中可用。<br><br>  若要取得此資料篩選 ChargeType =&quot;採購&quot;。 <br><br> 請參閱 ReservationID 或 ReservationName 知道需要付費的是哪一個保留項目。  | 此檢視不適用。 <br><br> 分攤的資料中，不提供採購成本。 |
| EffectivePrice | 取得保留折扣的使用量為零的值。 | 這個值是每小時按比例成本有保留折扣的使用方式的保留項目。 |
| 未使用的保留項目 （提供的保留項目並未用於一天的小時數和浪費的貨幣值） | 在此檢視中不適用。 | 在此檢視中可用。<br><br> 若要取得這項資料，篩選 ChargeType = &quot;UnusedReservation&quot;。<br><br>  請參閱 ReservationID 或 ReservationName 知道哪一個保留項目使用量過低。 這是保留項目中有多少浪費在一天。  |
| UnitPrice （來自價位表的資源的價格） | 可用 | 可用 |

其他可用 Azure 使用量資料中的資訊已變更：

- 產品和計量資訊-Azure 不會取代原本取用的計量使用 ReservationId 和 ReservationName，像先前一樣。
- ReservationId 和 ReservationName-它們是他們自己的資料中的欄位。 先前，它以往是僅適用於 AdditionalInfo。
- ProductOrderId-保留訂單識別碼中，新增為自己的欄位。
- ProductOrderName-購買的保留的產品名稱。
- 詞彙-12 個月或 36 個月。
- RINormalizationRatio-AdditionalInfo 底下可使用。 這是比例保留項目套用至使用情況記錄的位置。 如果您保留項目會啟用執行個體大小的彈性，它可以套用至其他大小。 值會顯示此保留項目已套用至使用量記錄的比率。

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>取得使用 API 的 Azure 耗用量和保留使用量資料

您可以使用 API 取得資料，或從 Azure 入口網站下載。

您呼叫[使用量詳細資料 API](/rest/api/consumption/usagedetails/list) API 版本&quot;2019年-04-01-preview&quot;取得新的資料。 如需術語的詳細資訊，請參閱[使用條款](billing-understand-your-usage.md)。 呼叫端應該是 enterprise 合約使用企業系統管理員[EA 入口網站](https://ea.azure.com)。 唯讀的企業系統管理員也可以取得資料。

資料不適用於[適用於企業客戶-使用方式詳細資料的報告 Api](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)。

以下是範例呼叫 API:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

如需詳細資訊 {enrollmentId} 和 {billingPeriodId}，請參閱[使用方式詳細資料 – 清單](https://docs.microsoft.com/rest/api/consumption/usagedetails/list)API 文件。

下表中計量與篩選相關的資訊可協助解決常見問題的保留項目。

| **API 資料類型** | API 呼叫動作 |
| --- | --- |
| **所有費用 （使用方式和購買項目）** | {計量} 取代為 ActualCost |
| **取得保留折扣的使用方式** | {計量} 取代為 ActualCost<br><br>取代 {filter}: properties/reservationId%20ne%20 |
| **未取得保留折扣的使用方式** | {計量} 取代為 ActualCost<br><br>Replace {filter} with: properties/reservationId%20eq%20 |
| **分攤的費用 （使用方式和購買項目）** | {計量} 取代為 AmortizedCost |
| **未使用的保留項目報表** | {計量} 取代為 AmortizedCost<br><br>取代 {filter}: properties/ChargeType%20eq%20'UnusedReservation' |
| **保留購買** | {計量} 取代為 ActualCost<br><br>取代 {filter}: properties/ChargeType%20eq%20'Purchase'  |
| **退款** | {計量} 取代為 ActualCost<br><br>取代 {filter}: properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>下載使用量 CSV 檔案，以新的資料

如果您是 EA 系統管理員，您可以下載 CSV 檔案包含新的使用方式資料，從 Azure 入口網站。 此資料無法使用從[EA 入口網站](https://ea.azure.com)。

在 Azure 入口網站中，瀏覽至[成本管理 + 計費](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts)。

1. 選取 計費帳戶。
2. 按一下 **使用量 + 費用**。
3. 按一下 [下載] 。  
![示範如何下載 CSV 使用量資料檔案，在 Azure 入口網站中的範例](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. 在**下載使用量 + 費用**下方**使用量詳細資料第 2 版**，選取 **（使用方式和購買項目） 的所有費用**，然後按一下 下載。 針對**分攤費用 （使用方式和購買項目）**。

您所下載的 CSV 檔案包含實際的成本和分攤的成本。

## <a name="common-cost-and-usage-tasks"></a>成本和使用方式的一般工作

下列各節是大多數人用來檢視其保留項目成本與使用量資料的一般工作。

### <a name="get-reservation-purchase-costs"></a>取得保留購買成本

保留購買成本有實際的成本資料。 篩選_ChargeType = 購買_。 請參閱 ProductOrderID 來判斷是購買的保留項目順序。

### <a name="get-underutilized-reservation-quantity-and-costs"></a>取得使用量過低的保留數量和成本

取得分攤的成本資料，並篩選_ChargeType_ _= UnusedReservation_。 您可以取得每日的未使用的保留數量和成本。 您可以篩選的資料保留項目或使用的保留項目順序_ReservationId_並_ProductOrderId_欄位，分別。 如果保留已 100%時，記錄會有數量為 0。

### <a name="amortize-reservation-costs"></a>降低保留成本

取得分攤的成本資料和保留項目順序使用的篩選條件_ProductOrderID_取得保留每日的分攤的成本。

### <a name="chargeback-for-a-reservation"></a>保留的計費

您可以於其他組織的計費保留項目使用的訂用帳戶、 資源群組或標記。 分攤的成本資料提供保留區的使用量，在下列資料類型的貨幣的值：

- 資源 （例如 VM)
- 資源群組
- Tags
- 訂用帳戶

### <a name="get-the-blended-rate-for-chargeback"></a>取得混合的費率計費功能

若要判斷混合的速率，取得分攤的成本資料，並彙總的總成本。 針對 Vm，您可以使用從 AdditionalInfo JSON 資料的計量名稱或 ServiceType 資訊。 將分割的總費用由用來取得混合的率的數量。

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>稽核最佳保留執行個體使用大小的彈性

使用多個數量_RINormalizationRatio_，從 AdditionalInfo。 結果會指出保留項目使用多少小時已套用至使用情況記錄。

### <a name="determine-reservation-savings"></a>決定保留節省量

取得 Amortized 成本資料，並篩選的資料保留的執行個體。 然後：

1. 取得估計的隨用隨付成本。 乘_UnitPrice_值替換_Quantity_值，以取得估計的隨用隨付成本，如果保留折扣不適用於使用方式。
2. 取得保留成本。 總和_成本_以取得您支付保留執行個體的貨幣值的值。 它包含使用和未使用的保留成本。
3. 減去保留成本估計的隨用隨付成本，來取得預估的節約效益。

## <a name="reservation-purchases-and-amortization-in-azure-cost-analysis"></a>保留購買和在 Azure 的成本分析分攤

保留執行個體成本位於[Azure 的成本分析預覽模式](https://preview.portal.azure.com/?feature.canmodifystamps=true&amp;microsoft_azure_costmanagement=stage2&amp;Microsoft_Azure_CostManagement_arm_canary=true&amp;Microsoft_Azure_CostManagement_apiversion=2019-04-01-preview&amp;Microsoft_Azure_CostManagement_amortizedCost=true#blade/Microsoft_Azure_CostManagement/Menu/costanalysis)。 根據預設，成本資料檢視會是實際成本。 您可以切換到 分攤成本。 以下是範例。

![範例，示範如何在 成本分析中選取 分攤的成本](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

套用篩選以查看您的費用依保留項目或費用的型別。 群組保留區名稱，以查看依保留項目細分的成本。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 保留項目，請參閱下列文章：

- [什麼是 Azure 保留項目？](billing-save-compute-costs-reservations.md)
- [預付具有 Azure 保留 VM 執行個體的虛擬機器](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [以 Azure SQL Database 保留容量預先支付 SQL 資料庫計算資源的費用](../sql-database/sql-database-reserved-capacity.md)
- [管理 Azure 保留項目](billing-manage-reserved-vm-instance.md)
- [了解保留項目折扣的套用方式](billing-understand-vm-reservation-charges.md)
- [了解隨用隨付方案訂用帳戶的保留項目使用量](billing-understand-reserved-instance-usage.md)
- [Windows 軟體成本不包含在 Reservations 內](billing-reserved-instance-windows-software-costs.md)
