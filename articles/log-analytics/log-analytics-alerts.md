---
title: "了解 Azure Log Analytics 中的警示 | Microsoft Docs"
description: "Log Analytics 中的警示會識別您的 OMS 儲存機制中的重要資訊，並可主動通知您相關問題或叫用動作以嘗試更正問題。  本文說明不同種類的警示規則和其定義方式。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/05/2018
ms.author: bwren
ms.openlocfilehash: 07e8312d5e113eeb9016dcc832b1cf66f8001c5f
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2018
---
# <a name="understanding-alerts-in-log-analytics"></a>了解 Log Analytics 中的警示

Log Analytics 中的警示會識別您的 Log Analytics 儲存機制中的重要資訊。  本文將討論某些設計決策，必須變更為基礎的查詢、 隨機延遲，可能是因為網路延遲或處理容量，並認可資料至記錄檔擷取的資料與資料收集頻率分析儲存機制。  它也提供中記錄分析工作如何警示規則的詳細資料，並說明不同類型的警示規則之間的差異。

如需建立警示規則的程序，請參閱下列文章：

- 使用 [Azure 入口網站](log-analytics-alerts-creating.md)建立警示規則
- 使用 [Resource Manager 範本](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)建立叢集規則
- 使用 [REST API](log-analytics-api-alerts.md) 建立警示規則

## <a name="considerations"></a>注意事項

使用在不同的方案和資料類型的詳細資料收集頻率[資料收集的詳細資料](log-analytics-add-solutions.md#data-collection-details)解決方案概觀文件。 這篇文章所述，收集頻率可以是頻率為一次每七天*通知*。 請務必了解及考慮設定警示前的資料收集頻率。 

- 收集頻率會決定頻率機器上的 OMS 代理程式將資料傳送至記錄分析。 比方說，如果收集頻率為 10 分鐘，且系統中有其他的延遲，然後傳輸資料的時間戳記可能是任何位置介於 0 到 10 分鐘加入至儲存機制之前舊，就可以在記錄分析搜尋。

- 可以觸發警示之前，資料必須寫入至儲存機制，使其可接受查詢時。 由於上述的延遲，收集頻率不相同的資料可供查詢的時間。 比方說，精確地每隔 10 分鐘，可能會收集資料，資料將可使用資料儲存機制中的不規則的間隔。 假設情況下，在零，10，而且 20 分鐘的時間間隔收集的資料可能可供搜尋，25、 28，和 35 分鐘，或在其他異常的間隔擷取延遲的影響。 這些延遲最壞的情況下會記載在[記錄分析的 SLA](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1)，其中不包含記錄分析服務與電腦之間的收集頻率或網路延遲所導入的延遲。


## <a name="alert-rules"></a>警示規則

警示會由自動定期執行記錄搜尋的警示規則所建立。  如果記錄搜尋的結果符合特定準則，則會建立警示的記錄。  此規則接著可以自動執行一或多個動作，以主動通知警示或叫用另一個處理序。  不同類型的警示規則會使用不同邏輯來執行這項分析。

![Log Analytics 警示](media/log-analytics-alerts/overview.png)

因為沒有預期的延遲，以記錄資料的擷取，之間編製索引的資料和何時可供搜尋的絕對時間可能會無法預期。  所收集的資料接近即時可用性應該列入定義警示規則時的考量。    

沒有可靠性的警示和警示的回應之間的取捨。 您可以選擇設定 降到最低，則為 false 的警示和遺失的警示，警示參數，或者您可以選擇警示的參數，以快速回應會受到監視，但偶爾產生 false 或遺失警示的情況。

警示規則會由下列詳細資料定義：

- **記錄搜尋**。  每次引發警示規則都會執行的查詢。  此查詢所傳回的記錄將是用來判斷是否要建立警示。
- **時間範圍**。  指定查詢的時間範圍。  查詢只會傳回在此目前時間範圍內建立的記錄。  這可以是 5 分鐘到 24 小時之間的任何值。 範圍必須是寬度不足以容納擷取合理的延遲。 時間間隔必須是您想要能夠處理的最長延遲的長度兩倍。<br> 比方說，如果您想是可靠的 30 分鐘延遲的警示，然後範圍必須是一小時。  

    有兩個時間範圍為太小，可能獲得的徵兆。

    - **遺失警示**。 假設擷取延遲為 60 分鐘某些情況下，但大部分的時間是 15 分鐘。  如果設定為 30 分鐘的時間間隔就會遺失警示時的延遲是 60 分鐘的時間，因為資料警示的查詢執行時，將無法供搜尋。 
   
        >[!NOTE]
        >嘗試診斷警示，則會遺漏的原因是不可能。 比方說，在上述情況中，資料會寫入至儲存機制警示查詢執行後的 60 分鐘。 如果錯過警示，，和明天正確的時間間隔內執行查詢，它會注意到隔天，記錄檔搜尋條件相符的結果。 它會出現，應該有已觸發警示。 事實上，因為資料無法尚未使用警示的查詢執行時，已不觸發警示。 
        >
 
    - **False 警示**。 有時警示查詢被設計來識別事件不存在。 這其中一個範例偵測時虛擬機器離線藉由搜尋遺失的活動訊號。 做為上方，如果無法使用警示的時間間隔內，搜尋的活動訊號然後警示將會產生活動訊號資料尚未可搜尋，因為，因此不存在。 如同 VM 已合法離線，而且它產生沒有活動訊號資料，這會是相同的結果。 沒有提供的活動訊號，而且警示失敗，則會顯示在正確的時間視窗之上執行查詢隔天。 事實上，活動訊號未還可用於搜尋的警示的時間間隔已設定太小。  

- **頻率**。  指定查詢應執行的頻率，並可用來使警示的一般情況下更能有效回應。 值可介於 5 分鐘到 24 小時，而且應該等於或小於警示的時間間隔。  如果值大於時間範圍，則您可能有遺漏記錄的風險。<br>如果目標是可靠的延遲最多 30 分鐘的時間和標準的延遲為 10 分鐘、 時間間隔應該是一小時和頻率值應為 10 分鐘。 這會觸發警示的 10 分鐘擷取延遲介於 10 到 20 分鐘，產生警示的資料時的資料。<br>若要避免建立多個警示，為相同的資料，由於時間間隔太寬，[抑制警示](log-analytics-tutorial-response.md#create-alerts)選項可用於歸併警示至少只要時間間隔。
  
- **閾值**。  系統會評估記錄搜尋的結果，以判斷是否應該建立警示。  不同類型的警示規則會有不同的閾值。

Log Analytics 中的各個警示規則是兩種類型其中之一。  下列各節會詳細說明這兩個類型。

- **[結果數目](#number-of-results-alert-rules)**。 當記錄搜尋所傳回的數目記錄超過指定數目時，系統會建立單一警示。
- **[計量測量](#metric-measurement-alert-rules)**。  針對記錄搜尋結果中的每個物件，若其值超過指定的閾值，系統就會為其建立警示。

警示規則類型之間的差異如下所示。

- **結果數目**警示規則一律會建立單一警示，而**計量測量**警示規則會為每個超過閾值的物件建立警示。
- **結果數目**警示規則會在閾值超過一次時建立警示。 **計量測量**警示規則會在閾值於特定時間間隔內超過一定次數時建立警示。

## <a name="number-of-results-alert-rules"></a>結果數目警示規則
**結果數目**警示規則會在搜尋查詢所傳回的記錄數目超過指定閾值時建立單一警示。

### <a name="threshold"></a>閾值
**結果數目**警示規則的閾值只會大於或小於特定值。  如果記錄搜尋所傳回的記錄數目符合此準則，則會建立警示。

### <a name="scenarios"></a>案例

#### <a name="events"></a>活動
這種類型的警示規則適用於處理例如 Windows 事件記錄、Syslog 和自訂記錄的事件。  您可能希望在建立特定的錯誤事件時，或是在特定時間範圍內建立多個錯誤事件時建立警示。

若要對單一事件發出警示，請將結果數目設為大於 0 並將頻率與時間範圍設為 5 分鐘。  如此一來，將會每隔 5 分鐘執行一次查詢，並檢查在上次執行查詢後是否發生所建立的單一事件。  較長的頻率可能會延遲收集事件和建立警示的時間。

有些應用程式可能會記錄不一定會產生警示的偶發錯誤。  例如，應用程式可能會重試造成錯誤事件的處理序，而下一次就會成功。  在此情況下，除非在特定時間範圍內建立多個事件，否則您不會想建立警示。  

在某些情況下，您可能想在某個事件不存在時建立警示。  例如，處理序可能會記錄一般事件，表示運作正常。  如果它不在特定的時間範圍內記錄一個事件，則應建立警示。  在此情況下，您會將臨界值設定為**小於 1**。

#### <a name="performance-alerts"></a>效能警示
[效能資料](log-analytics-data-sources-performance-counters.md) 會儲存為 OMS 儲存機制中類似事件的記錄。  如果您要在效能計數器超過特定臨界值時發出警示，則該臨界值應包含在查詢中。

例如，如果您想要在處理器執行超過 90% 時發出警示，您會使用如下的查詢，且警示規則的臨界值**大於 0**。

    Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90

如果您想要針對特定的時間範圍在處理器平均超過 90% 時發出警示，您會利用如下的[測量命令](log-analytics-search-reference.md#commands)來使用查詢，且警示規則的臨界值**大於 0**。

    Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90

>[!NOTE]
> 如果您的工作區已升級為[新的 Log Analytics 查詢語言](log-analytics-log-search-upgrade.md)，則以上查詢會變更如下：`Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" and CounterValue>90`
> `Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" | summarize avg(CounterValue) by Computer | where CounterValue>90`


## <a name="metric-measurement-alert-rules"></a>公制度量單位的警示規則

>[!NOTE]
> 計量測量警示規則目前處於公開預覽狀態。

**計量測量**警示規則會針對查詢中其值超過指定閾值的每個物件建立警示。  這些警示規則與**結果數目**警示規則具有下列明顯差異。

#### <a name="log-search"></a>記錄搜尋
您可以對**結果數目**警示規則使用任何查詢，但是對計量測量警示規則使用查詢時則有特定需求。  它必須包含[測量命令](log-analytics-search-reference.md#commands)以將結果群組在特定欄位上。 此命令必須包含下列元素。

- **彙總函式**。  決定要執行的計算，並可能決定要彙總的數值欄位。  例如，**count()** 會在查詢中傳回記錄數目，**avg(CounterValue)** 則會傳回 CounterValue 欄位在一段間隔內的平均值。
- **群組欄位**。  系統會為這個欄位中的每個執行個體建立帶有彙總值的記錄，而且每個執行個體都可產生警示。  例如，如果您想要為每部電腦產生警示，您可以使用**依電腦**。   
- **間隔**。  定義用來彙總資料的時間間隔。  例如，如果您指定**5 分鐘**，會建立一則記錄，每隔 5 分鐘時段彙總指定警示的 [群組] 欄位的每個執行個體。

#### <a name="threshold"></a>閾值
計量測量警示規則的閾值是由彙總值與若干違規所定義。  如果記錄搜尋中的任何資料點超過此值，系統就會將其視為違規。  如果結果中任何物件的違規數目超過指定值，系統舊會為該物件建立警示。

#### <a name="example"></a>範例
假設您想要在任何電腦於過去 30 分鐘內發生三次處理器使用率超過 90% 時收到警示。  您可以建立詳細資料如下的警示規則。  

**查詢：**Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer Interval 5minute<br>
**時間間隔：** 30 分鐘<br>
**警示頻率：** 5 分鐘<br>
**彙總的值：**大於 90<br>
**警示觸發程序為基礎：**總計破壞大於 5<br>

查詢會以 5 分鐘為間隔為每部電腦建立平均值。  此查詢會每隔 5 分鐘針對在先前 30 分鐘內所收集的資料來執行。  三部電腦的資料範例如下所示。

![查詢結果範例](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

在此範例中，系統會為 srv02 和 srv03 建立個別警示，因為它們在時間範圍內違反 90% 閾值 3 次。  如果**警示觸發依據︰**變更為**連續**，則系統只會為 srv03 建立警示，因為它違反了 3 個連續取樣的閾值。

## <a name="alert-records"></a>警示記錄
在 Log Analytics 中由警示規則建立的警示記錄，**Type** 為 **Alert**，**SourceSystem** 為 **OMS**。  其屬性如下表所示。

| 屬性 | 說明 |
|:--- |:--- |
| 類型 |*警示* |
| SourceSystem |*OMS* |
| *Object*  | [計量測量警示](#metric-measurement-alert-rules)會有群組欄位的屬性。  例如，如果記錄搜尋針對「電腦」進行群組，則警示記錄會有電腦欄位，並以電腦名稱作為值。
| AlertName |警示的名稱。 |
| AlertSeverity |警示的嚴重性層級。 |
| LinkToSearchResults |連結至 Log Analytics 記錄檔搜尋，而該搜尋會從建立警示的查詢傳回記錄。 |
| 查詢 |已執行的查詢文字。 |
| QueryExecutionEndTime |查詢的時間範圍結尾。 |
| QueryExecutionStartTime |查詢的時間範圍開頭。 |
| ThresholdOperator | 警示規則所使用的運算子。 |
| ThresholdValue | 警示規則所使用的值。 |
| TimeGenerated |建立警示的日期和時間。 |

[警示管理解決方案](log-analytics-solution-alert-management.md)和 [Power BI 匯出](log-analytics-powerbi.md)會建立其他種類的警示記錄。  這些記錄的 **Type** 均為 **Alert**，但依其 **SourceSystem** 區分。


## <a name="next-steps"></a>後續步驟
* 安裝[警示管理解決方案](log-analytics-solution-alert-management.md)，以分析在 Log Analytics 中建立的警示以及從 System Center Operations Manager 收集的警示。
* 深入了解可產生警示的 [記錄檔搜尋](log-analytics-log-searches.md) 。
* 完成 [設定 Webook](log-analytics-alerts-webhooks.md) 和警示規則的逐步解說。  
* 了解如何在 [Azure 自動化中撰寫 Runbook](https://azure.microsoft.com/documentation/services/automation) 以補救警示所識別的問題。
