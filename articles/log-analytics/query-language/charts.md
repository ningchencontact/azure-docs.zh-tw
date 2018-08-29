---
title: 從 Azure Log Analytics 查詢建立圖表 | Microsoft Docs
description: 說明 Azure Log Analytics 中能以不同方式顯示您資料的不同視覺效果。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 359e5e671287c4d330deeb2d3573877d9ee5d1c5
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190792"
---
# <a name="creating-charts-and-diagrams-from-log-analytics-queries"></a>從 Log Analytics 查詢建立圖表

> [!NOTE]
> 您應該先完成[Log Analytics 查詢中的進階彙總](advanced-aggregations.md)，再完成此課程。

此文章說明 Azure Log Analytics 中能以不同方式顯示您資料的不同視覺效果。

## <a name="charting-the-results"></a>製作結果圖表
從檢閱過去一小時內每個作業系統類型中有多少部電腦開始：

```OQL
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

根據預設，結果會顯示成圖表︰

![圖表](media/charts/table-display.png)

若要取的更好的檢視，請選取 [圖表]，然後選擇 [圓形圖] 選項以將結果視覺化：

![圓形圖](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>時間表
顯示 1 小時間隔中的處理器時間平均值、第 50 個百分位數與第 95 個百分位數。 查詢會產生多個查詢，而且您可以接著選取要在時間表上顯示哪些數列：

```OQL
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

選取 [折線圖] 圖表顯示選項：

![折線圖](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>參考線

參考線可協助您輕鬆地識別計量是否超過特定閾值。 若要將線條加到圖表中，請以常數欄延伸資料集：

```OQL
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![參考線](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>多維度
`summarize` 之 `by` 子句中的多個運算式會在結果中建立多列，每個值組合都各有一列。

```OQL
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

當您以圖表方式檢視結果時，它會使用來自 `by` 子句的第一欄。 下列範例顯示使用 _EventID_ 顯示堆疊直條圖。 維度類型必須是 `string`，因此此範例中的 _EventID_ 會被轉換為字串。 

![橫條圖 EventID](media/charts/charts-and-diagrams-multiDimension1.png)

您可以選取欄名下拉式清單以進行切換。 

![橫條圖 AccountType](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>後續步驟
請參閱其他課程以了解如何使用 Log Analytics 查詢語言：

- [字串作業](string-operations.md)
- [日期和時間作業](datetime-operations.md)
- [彙總函式](aggregations.md)
- [進階彙總](advanced-aggregations.md)
- [JSON 與資料結構](json-data-structures.md)
- [進階查詢撰寫](advanced-query-writing.md)
- [聯結](joins.md)