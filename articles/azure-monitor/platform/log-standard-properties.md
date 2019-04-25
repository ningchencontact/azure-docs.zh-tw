---
title: Azure 監視器記錄中的標準屬性 | Microsoft Docs
description: 說明「Azure 監視器」記錄中多種資料類型通用的屬性。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/20/2019
ms.author: bwren
ms.openlocfilehash: c01cdb967fd7f9516b4403aa4f0c76f2577d5050
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60394518"
---
# <a name="standard-properties-in-azure-monitor-log-records"></a>Azure 監視器記錄中的標準屬性
「Azure 監視器」中的記錄資料會[儲存為一組記錄](../log-query/log-query-overview.md)，每個記錄分別屬於具有一組唯一屬性的特定資料類型。 有許多資料類型都具有多種類型之間通用的標準屬性。 本文將說明這些屬性，並提供在查詢中加以使用的範例。

其中有部分屬性尚在實作程序中，因此您可能會在某些資料類型中看到這些屬性，但在其他類型中卻沒看到。

## <a name="timegenerated"></a>TimeGenerated
**TimeGenerated** 屬性包含建立記錄的日期與時間。 其提供以時間進行篩選或彙總時所用的共通屬性。 當您在 Azure 入口網站中針對檢視或儀表板選取時間範圍時，就是使用 TimeGenerated 來篩選結果。

### <a name="examples"></a>範例

下列查詢會傳回前一週中每天建立的錯誤事件數目。

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

## <a name="type"></a>類型
**類型**屬性會保留資料表名稱，而從中擷取的記錄也可視為記錄類型。 在結合多份資料表中記錄的查詢中 (例如使用 `search` 運算子的那些查詢)，此屬性十分適合用來區分不同類型的記錄。 **$table** 可用來取代某些位置中的**類型**。

### <a name="examples"></a>範例
下列查詢會依據類型傳回過去一小時所收集的記錄計數。

```Kusto
search * 
| where TimeGenerated > ago(1h) 
| summarize count() by Type 
```

## <a name="resourceid"></a>\_ResourceId
**\_ResourceId** 屬性會保存與記錄相關聯的資源唯一識別碼。 這可讓您有標準屬性可用來將查詢範圍限定於來自特定資源的記錄，或跨多個資料表聯結相關的資料。

就 Azure 資源而言，**_ResourceId** 的值為 [Azure 資源識別碼 URL](../../azure-resource-manager/resource-group-template-functions-resource.md)。 此屬性目前僅適用於 Azure 資源，但未來將擴充至 Azure 以外的資源，例如內部部署電腦。

> [!NOTE]
> 某些資料類型的欄位已包含 Azure 資源識別碼，或是至少屬於其一部份的訂用帳戶識別碼等。 雖然這些欄位會保留回溯相容性，但還是建議您使用 _ResourceId 執行交叉相互關聯，這樣將會更一致。

### <a name="examples"></a>範例
下列是將每一部電腦的效能和事件資料相聯結的查詢。 其中顯示識別碼為 _101_ 且處理器使用率超過 50% 的所有事件。

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

下列是將 _AzureActivity_ 記錄與 _SecurityEvent_ 記錄相聯結的查詢。 其中顯示已登入這些機器的使用者所產生的所有活動作業。

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

下列查詢會剖析 **_ResourceId**和彙總計費資料磁碟區，每個 Azure 訂用帳戶。

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

請謹慎使用這些 `union withsource = tt *` 查詢，因為執行跨資料類型掃描的費用相當高昂。

## <a name="isbillable"></a>\_IsBillable
**\_IsBillable** 屬性會指定擷取的資料是否可計費。 **\_IsBillable** 等於 _false_ 的資料會免費收集，且費用不會計入您的 Azure 帳戶。

### <a name="examples"></a>範例
若要取得傳送計費資料類型的電腦清單，請使用下列查詢：

> [!NOTE]
> 請謹慎使用這些查詢搭配 `union withsource = tt *`，因為執行跨資料類型掃描相當昂貴。 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

加以延伸，即可傳回每小時傳送計費資料類型的電腦計數：

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="billedsize"></a>\_BilledSize
如果 **\_IsBillable** 為 true，**\_BilledSize** 屬性可指定將計入 Azure 帳戶的資料大小 (以位元組為單位)。

### <a name="examples"></a>範例
若要查看每部電腦擷取的可計費事件的大小，請使用大小以位元組計算的 `_BilledSize` 屬性：

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

若要查看每部電腦所擷取的事件計數，請使用下列查詢：

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

若要查看每部電腦所擷取的可計費事件計數，請使用下列查詢： 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

如果您想要查看有哪些可計費資料類型的計數正在傳送資料到特定的電腦，請使用下列查詢：

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```


## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 監視器記錄資料的儲存方式](../log-query/log-query-overview.md)。
- 參與[撰寫記錄查詢](../../azure-monitor/log-query/get-started-queries.md)的課程。
- 參與[在記錄查詢中聯結資料表](../../azure-monitor/log-query/joins.md)的課程。
