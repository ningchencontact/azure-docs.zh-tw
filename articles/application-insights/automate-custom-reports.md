---
title: 將含有 Azure Application Insights 資料的自訂報表自動化
description: 將含有 Azure Application Insights 資料的自訂每天/每週/每月報表自動化
services: application-insights
documentationcenter: ''
author: sdash
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: sdash
ms.openlocfilehash: 804e8c7a43d1ab16d11b6075be44599b33b46a3e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072655"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>將含有 Azure Application Insights 資料的自訂報表自動化

定期的報表有助於讓小組隨時了解商務關鍵服務的執行狀況。 開發人員、DevOps/SRE 小組及其主管可透過自動化報表所提供的可靠見解提升生產力，而不需要要求每個人都登入入口網站。 這類報表也有助於識別逐漸增加的延遲、負載或失敗率，這些項目可能不會觸發任何警示規則。

每個企業都有其獨特的報表需求，例如： 

* 計量的特定百分位數彙總，或自訂報表中的計量。
* 每天、每週和每月的資料彙總有不同報表，適用於不同對象。
* 依自訂的屬性 (例如區域或環境) 分割。 
* 要將某些 AI 資源一起分組在單一報表中，即使它們可能在不同的訂用帳戶或資源群組當中也一樣。
* 將包含敏感性計量的報表分別傳送至特定對象。
* 提供報表給可能沒有入口網站資源存取權限的專案關係人。

> [!NOTE] 
> 每週的 Application Insights 摘要電子郵件不允許任何自訂，且將改為下列自訂選項而中止。 在 2018 年 6 月 11 日將傳送最後的每週摘要電子郵件。 請設定下列其中一個選項，來取的類似的自訂報表 (使用下列建議的查詢)。

## <a name="to-automate-custom-report-emails"></a>將自訂報表電子郵件自動化

您可以[以程式設計方式查詢 Application Insights](https://dev.applicationinsights.io/) 資料，來依排程產生自訂報表。 下列選項可協助您快速入門：

* [透過 Microsoft Flow 進行報表自動化](app-insights-automate-with-flow.md)
* [透過 Logic Apps 進行報表自動化](automate-with-logic-apps.md)
* 在監視案例中使用 "Application Insights 排程摘要" [Azure 函式](https://azure.microsoft.com/services/functions/)範本。 此函式會使用 SendGrid 來傳送電子郵件。 

    ![Azure 函式範本](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>每週摘要電子郵件的範例查詢
下列查詢顯示跨多個資料集聯結，來取得有如報表般的每週摘要電子郵件。 您可以視需要加以自訂，並搭配上面所列的任何選項使用以將每週報表自動化。   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

  
## <a name="next-steps"></a>後續步驟

- 深入了解建立 [Analytics 查詢](app-insights-analytics-using.md)。
- 深入了解[以程式設計方式查詢 Application Insights 資料](https://dev.applicationinsights.io/)
- 深入了解 [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)。
- 深入了解 [Microsoft Flow](https://ms.flow.microsoft.com)。


