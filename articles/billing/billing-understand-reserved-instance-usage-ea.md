---
title: 瞭解 Enterprise 合約的 Azure 保留使用量
description: 學習如何看懂使用量，以了解 Enterprise 註冊的 Azure 保留套用情形。
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 507ad62a917120689bee3f1e293e23c9ab8b0f66
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598091"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>取得 Enterprise 合約保留成本和使用量

保留成本和使用量資料可供 Azure 入口網站和 REST Api 中的 Enterprise 合約客戶使用。 本文可協助您:

- 取得保留購買資料
- 瞭解使用保留的訂用帳戶、資源群組或資源
- 保留使用量的計費
- 計算保留費用
- 取得保留使用量資料
- 攤銷保留成本

Marketplace 費用會合並在使用量資料中。 您可以從單一資料來源查看第一方使用量、marketplace 使用量和購買費用。

## <a name="reservation-charges-in-azure-usage-data"></a>Azure 使用量資料中的保留費用

資料分成兩個不同的資料集:_實際成本_和_分攤成本_。 這兩個資料集有何不同:

**實際成本**-提供要與您的每月帳單協調的資料。 此資料具有保留購買成本和保留應用程式詳細資料。 透過這項資料, 您可以知道哪一個訂用帳戶或資源群組或資源在特定一天內收到保留折扣。 接收保留折扣之使用量的 EffectivePrice 為零。

**分攤成本**-此資料集與實際成本資料集相似, 不同之處在于, 取得保留折扣的使用量 EffectivePrice 是保留的按比例計算成本 (而不是零)。 這可協助您瞭解訂用帳戶、資源群組或資源的保留耗用量貨幣值, 並可協助您在內部收取保留使用量的費用。 資料集也有未使用的保留時數。 資料集沒有保留的採購記錄。 

兩個資料集的比較:

| Data | 實際成本資料集 | 分攤成本資料集 |
| --- | --- | --- |
| 保留購買 | 可在此視圖中取得。<br><br>  若要在 ChargeType = &quot;購買&quot;時取得此資料篩選準則。 <br><br> 請參閱 ReservationID 或 ReservationName, 以瞭解費用的保留專案。  | 不適用於此視圖。 <br><br> 未在分攤的資料中提供購買費用。 |
| EffectivePrice | 對於取得保留折扣的使用量, 此值為零。 | 此值為具有保留折扣之使用量保留的每小時費用。 |
| 未使用的保留 (提供保留未在一天內使用的時數和浪費的貨幣值) | 不適用於此視圖。 | 可在此視圖中取得。<br><br> 若要取得此資料, 請篩選 ChargeType &quot;=&quot;UnusedReservation。<br><br>  請參閱 ReservationID 或 ReservationName, 以瞭解哪些保留使用量過低。 這是一天中浪費多少保留空間。  |
| 單價 (價位表中的資源價格) | 可用 | 可用 |

Azure 使用量資料中的其他可用資訊已變更:

- 產品和計量資訊-Azure 不會以先前所執行的 ReservationId 和 ReservationName 取代原先使用的計量。
- ReservationId 和 ReservationName-它們是自己在資料中的欄位。 之前, 它只能在 AdditionalInfo 下使用。
- ProductOrderId-保留訂單識別碼, 新增為其本身的欄位。
- ProductOrderName-所購買保留的產品名稱。
- 期限-12 個月或36個月。
- RINormalizationRatio-可在 AdditionalInfo 下使用。 這是將保留套用至使用量記錄的比率。 如果已針對您的保留啟用實例大小彈性, 則它可以套用至其他大小。 值會顯示針對使用量記錄套用保留的比例。

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>使用 API 取得 Azure 耗用量和保留使用量資料

您可以使用 API 來取得資料, 或從 Azure 入口網站下載。

您可以使用 api 版本&quot;2019-04-01-preview&quot;來呼叫[使用量詳細資料 API](/rest/api/consumption/usagedetails/list) , 以取得新的資料。 如需術語的詳細資訊, 請參閱[使用方式詞彙](billing-understand-your-usage.md)。 呼叫端應該是使用[EA 入口網站](https://ea.azure.com)之 enterprise 合約的企業系統管理員。 唯讀企業系統管理員也可以取得資料。

資料不適[用於企業客戶的報告 api-使用量詳細](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)資料。

以下是對 API 的範例呼叫:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

如需有關 {enrollmentId} 和 {billingPeriodId} 的詳細資訊, 請參閱[使用量詳細資料–列出](https://docs.microsoft.com/rest/api/consumption/usagedetails/list)API 一文。

下表中關於計量和篩選的資訊可協助解決常見的保留問題。

| **API 資料的類型** | API 呼叫動作 |
| --- | --- |
| **所有費用 (使用量和購買)** | 以 ActualCost 取代 {公制} |
| **獲得保留折扣的使用量** | 以 ActualCost 取代 {公制}<br><br>將 {filter} 取代為: properties/reservationId% 20ne% 20 |
| **未取得保留折扣的使用量** | 以 ActualCost 取代 {公制}<br><br>將 {filter} 取代為: properties/reservationId% 20eq% 20 |
| **分攤費用 (使用量和購買)** | 以 AmortizedCost 取代 {公制} |
| **未使用的保留報表** | 以 AmortizedCost 取代 {公制}<br><br>將 {filter} 取代為: properties/ChargeType% 20eq% 20 ' UnusedReservation ' |
| **保留購買** | 以 ActualCost 取代 {公制}<br><br>將 {filter} 取代為: properties/ChargeType% 20eq% 20 ' Purchase '  |
| **退款** | 以 ActualCost 取代 {公制}<br><br>將 {filter} 取代為: properties/ChargeType% 20eq% 20 ' 退款 ' |

## <a name="download-the-usage-csv-file-with-new-data"></a>以新資料下載使用量 CSV 檔案

如果您是 EA 系統管理員, 您可以從 Azure 入口網站下載包含新使用資料的 CSV 檔案。 此資料無法從[EA 入口網站](https://ea.azure.com)使用。

在 [Azure 入口網站中, 流覽至 [成本管理 + 帳單](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts)]。

1. 選取帳單帳戶。
2. 按一下 [**使用量 + 費用**]。
3. 按一下 [下載]。  
![示範如何在 Azure 入口網站中下載 CSV 使用量資料檔案的範例](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. 在 [**下載使用量 + 費用**] 的 [**使用量詳細資料第2版**] 底下, 選取 **[所有費用 (使用量和購買)** ], 然後按一下 [下載]。 針對**分攤費用重複 (使用量和購買)** 。

您下載的 CSV 檔案包含實際成本和分攤成本。

## <a name="common-cost-and-usage-tasks"></a>一般成本和使用方式工作

下列各節是大部分人用來查看其保留成本和使用量資料的一般工作。

### <a name="get-reservation-purchase-costs"></a>取得保留購買成本

保留購買成本適用于實際成本資料。 篩選_ChargeType = 購買_。 請參閱 ProductOrderID, 以判斷購買的保留順序。

### <a name="get-underutilized-reservation-quantity-and-costs"></a>取得使用量過低的保留數量和成本

取得分攤成本資料, 並篩選_ChargeType_ _= UnusedReservation_。 您會取得每日未使用的保留數量和成本。 您可以分別使用_ReservationId_和_ProductOrderId_欄位篩選保留或保留訂單的資料。 如果保留已使用 100%, 則記錄的數量為0。

### <a name="amortize-reservation-costs"></a>攤銷保留成本

取得分攤的成本資料, 並使用_ProductOrderID_來篩選保留訂單, 以取得保留的每日分攤成本。

### <a name="chargeback-for-a-reservation"></a>保留的退款

您可以依訂用帳戶、資源群組或標籤, 將保留使用計費給其他組織。 分攤成本資料提供下列資料類型的保留使用量貨幣值:

- 資源 (例如 VM)
- 資源群組
- Tags
- 訂閱

### <a name="get-the-blended-rate-for-chargeback"></a>取得計費的混合費率

若要判斷混合速率, 請取得分攤的成本資料, 並匯總總成本。 針對 Vm, 您可以使用來自 AdditionalInfo JSON 資料的 MeterName 或 ServiceType 資訊。 將總成本除以用來取得混合費率的數量。

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>針對實例大小彈性來審核最佳保留使用

具有_RINormalizationRatio_的多個數量, 從 AdditionalInfo。 結果會指出使用方式記錄的保留使用量有多少小時。

### <a name="determine-reservation-savings"></a>決定保留節省

取得分攤的成本資料, 並篩選保留實例的資料。 然後：

1. 取得預估的隨用隨付成本。 將 [_單價_] 值乘以 [_數量_] 值, 以取得預估的隨用隨付成本 (如果保留折扣不適用於使用量)。
2. 取得保留成本。 加總_成本_值, 以取得您為保留實例付費的貨幣值。 其中包含保留的已使用和未使用成本。
3. 從預估的隨用隨付成本減去保留成本, 以取得預估的節省量。

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>成本分析中的保留購買和分攤

保留成本會在[成本分析](https://aka.ms/costanalysis)中提供。 根據預設, 成本分析會顯示**實際成本**, 這就是您帳單上顯示成本的方式。 若要查看保留的購買專案, 並將其與使用權益的資源相關聯, 請切換到**分攤成本**:

![顯示在成本分析中選取分攤成本的範例](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

依收費類型分組, 以查看使用量、購買和退款的細分;或依據保留專案和隨選成本的分解。 請記住, 當您查看實際成本時, 您會看到的唯一保留成本是購買, 但成本會分配給在查看分攤成本時使用 benfit 的個別資源。 查看分攤成本時, 您也會看到新的**UnusedReservation**費用類型。

## <a name="need-help-contact-us"></a>需要協助嗎? 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 保留項目，請參閱下列文章：

- [什麼是 Azure 保留項目？](billing-save-compute-costs-reservations.md)
- [預付具有 Azure 保留 VM 執行個體的虛擬機器](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [以 Azure SQL Database 保留容量預先支付 SQL 資料庫計算資源的費用](../sql-database/sql-database-reserved-capacity.md)
- [管理 Azure 保留項目](billing-manage-reserved-vm-instance.md)
- [了解保留項目折扣的套用方式](billing-understand-vm-reservation-charges.md)
- [了解隨用隨付訂用帳戶的保留使用量](billing-understand-reserved-instance-usage.md)
- [Windows 軟體成本不包含在 Reservations 內](billing-reserved-instance-windows-software-costs.md)
