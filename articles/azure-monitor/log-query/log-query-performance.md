---
title: 在 Azure 監視器中撰寫有效率的記錄查詢 | Microsoft Docs
description: 用來了解如何在 Log Analytics 中撰寫查詢的資源參考。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2019
ms.openlocfilehash: a5ee03f6c42f076549856161a6ebe0b1888fe4aa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894122"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>在 Azure 監視器中撰寫有效率的記錄查詢
本文將建議您如何在 Azure 監視器中撰寫有效率的記錄查詢。 您可以使用這些策略，確保查詢能以最低額外負荷來快速執行。

## <a name="scope-your-query"></a>為您的查詢設定範圍
讓查詢處理超過實際需求的資料量，可能會導致查詢執行時間過長，而且通常會造成結果中有太多資料，以至於無法進行有效的分析。 在一些極端情況下，查詢還可能會逾時並且失敗。

### <a name="specify-your-data-source"></a>指定資料來源
若要撰寫有效率的查詢，第一步是將其範圍限制在所需的資料來源。 指定資料表總是優於執行廣泛的文字搜尋，例如 `search *`。 若要查詢特定資料表，請以資料表名稱開始您的查詢，如下所示：

``` Kusto
requests | ...
```

您可以使用如下所示的查詢，使用 [search](/azure/kusto/query/searchoperator) 在特定資料表中的多個資料行之間搜尋值：

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

使用 [union](/azure/kusto/query/unionoperator) 來查詢多個資料表，如下所示：

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>指定時間範圍
您也應該將查詢限制在所需的資料時間範圍。 根據預設，您的查詢會包含過去 24 小時內所收集的資料。 您可以在[時間範圍選取器](get-started-portal.md#select-a-time-range)中變更該選項，或明確地將時間加入您的查詢。 請在資料表名稱之後立即加上時間篩選條件，如此一來，剩下的查詢就只會處理該範圍內的資料：

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>僅取得最新記錄

若要只傳回最新記錄，請使用下列查詢中所示的 *top* 運算子，此查詢會傳回 *traces* 資料表中最新的 10 筆記錄：

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>篩選記錄
若您只要檢閱符合指定條件的記錄，請使用下列查詢中所示的 *where* 運算子，此查詢只會傳回 _severityLevel_ 值大於 0 的記錄：

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>字串比較
[評估字串](/azure/kusto/query/datatypes-string-operators)時，通常應該使用 `has` (而不是 `contains`) 來尋找完整權杖。 `has` 的效率比較高，因為不需要查詢子字串。

## <a name="returned-columns"></a>傳回的資料行

使用 [project](/azure/kusto/query/projectoperator) 將要處理的一組資料行縮小為您需要的資料行：

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

雖然您可以使用 [extend](/azure/kusto/query/extendoperator) 來計算值，並建立您自己的資料行，但篩選資料表的資料行通常會更有效率。

例如，比起下列建立新 _subscription_ 資料行並在其中進行篩選的第二個查詢，下列第一個篩選 _operation\_Name_ 的查詢會更有效率：

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>使用聯結
使用 [join](/azure/kusto/query/joinoperator) 運算子時，請選擇資料列較少的資料表放在查詢的左側。


## <a name="next-steps"></a>後續步驟
若要深入了解查詢的最佳做法，請參閱[查詢最佳做法](/azure/kusto/query/best-practices)。