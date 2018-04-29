---
title: 了解 Azure Log Analytics 中的警示 | Microsoft Docs
description: Log Analytics 中的警示會識別您的 OMS 儲存機制中的重要資訊，並可主動通知您相關問題或叫用動作以嘗試更正問題。  本文說明不同種類的警示規則和其定義方式。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2018
ms.author: bwren
ms.openlocfilehash: 5e01ea901f1ba07c0ee5a99720c00c5f03574365
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2018
---
# <a name="understanding-alerts-in-log-analytics"></a>了解 Log Analytics 中的警示

> [!NOTE]
> Log Analytics 中的警示[擴充至 Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md)。 本文中的資訊仍可用來定義使用 Log Analytics 搜尋的 Azure 入口網站中的警示詳細資料。

Log Analytics 中的警示會識別您的 Log Analytics 儲存機制中的重要資訊。 本文將討論一些設計決策，必須以要查詢資料的收集頻率、透過可能因網路延遲或處理容量所造成資料擷取的隨機延遲，及認可 Log Analytics 工作區內的資料為基礎來制定這些決策。 還會提供警示規則在 Log Analytics 中的運作方式，並說明不同類型的警示規則之間的差異。

如需建立警示規則的程序，請參閱下列文章：

- 使用 [Azure 入口網站](log-analytics-alerts-creating.md)建立警示規則
- 使用 [Resource Manager 範本](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)建立叢集規則
- 使用 [REST API](log-analytics-api-alerts.md) 建立警示規則

## <a name="considerations"></a>考量

「解決方案」概觀一文的[資料收集詳細資料](log-analytics-add-solutions.md#data-collection-details)中，提供各種解決方案的資料收集頻率和資料類型的相關詳細資料。 如本文所述，收集頻率最低可能會是每七天一次到無通知。 設定警示前，請務必了解及考量資料收集頻率。 

- 收集頻率會決定 OMS 代理程式將資料傳送至 Log Analytics 的頻率。 例如，如果收集頻率為 10 分鐘，且系統中沒有其他延遲，則傳輸資料的時間戳記在新增至存放庫並可在 Log Analytics 進行搜尋之前，可能會是介於 0 到 10 分鐘以前的。

- 在您可以觸發警示之前，必須將資料寫入存放庫中，以便在其接受查詢時可供使用。 由於上述的延遲，收集頻率與資料可供查詢的時間不相同。 比方說，儘管系統可能會精確地每隔 10 分鐘收集資料，但資料在資料存放庫中會以不規則的間隔提供使用。 在假設情況下，在 0、10 和 20 分鐘的時間間隔收集的資料，可供搜尋的時間可能分別是 25、28 和 35 分鐘，或是受擷取延遲影響的一些其他不規則的間隔。 這些延遲最壞的情況會記載在 [Log Analytics 的 SLA](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1) 中，其中不包含收集頻率所造成的延遲，或是電腦與 Log Analytics 服務之間的網路延遲。


## <a name="alert-rules"></a>警示規則

警示會由自動定期執行記錄搜尋的警示規則所建立。 如果記錄搜尋的結果符合特定準則，則會建立警示的記錄。 此規則接著可以自動執行一或多個動作，以主動通知警示或叫用另一個處理序。 不同類型的警示規則會使用不同邏輯來執行這項分析。

![Log Analytics 警示](media/log-analytics-alerts/overview.png)

記錄資料的擷取會發生預期的延遲，因此無法預期檢索資料與資料可供搜尋的時間之間的絕對時間。 定義警示規則時，應該將以接近即時可用性所收集的資料列入考量。

警示可靠性和警示的回應之間應有所取捨。 您可以選擇設定警示參數來降低警示誤判和遺漏警示，或是可以選擇警示參數以快速回應要受到監視，但偶爾會產生誤判或遺漏警示的情況。

警示規則會由下列詳細資料定義：

- **記錄搜尋**。 每次引發警示規則都會執行的查詢。 此查詢所傳回的記錄將是用來判斷是否要建立警示。
- **時間範圍**。 指定查詢的時間範圍。 查詢只會傳回在此目前時間範圍內建立的記錄。 這可能是介於 5 分鐘與 24 小時之間的任何值。 範圍寬度必須足以容納擷取中的合理延遲。 時間間隔必須是您需要能夠處理的最長延遲時間的兩倍。<br> 例如，如果您需要對 30 分鐘延遲的可靠警示，範圍就必須是一小時。

    如果時間範圍太小，您可能會遭遇兩個徵兆。

    - **遺漏警示**. 假設擷取延遲在某些情況下為 60 分鐘，但大部分的情況下是 15 分鐘。 如果時間間隔設定為 30 分鐘，當延遲是 60 分鐘時就會遺失警示，因為當執行警示查詢時，資料無法提供搜尋。 
   
        >[!NOTE]
        >嘗試診斷警示不可能遺漏的原因。 例如，在上述情況中，資料會在執行警示查詢的 60 分鐘後寫入存放庫。 如果是在遺漏警示的隔天、以及查詢在正確時間間隔後執行的隔天收到通知，記錄搜尋條件就會符合結果。 系統可能會出現警示應該已觸發。 事實上，因為當警示查詢執行時資料尚無法使用，因此不會觸發警示。 
        >
 
    - **誤判警示**。 有時會設計警示查詢來識別事件不存在。 其中一個範例是藉由搜尋遺漏的活動訊號，來偵測虛擬機器離線的時間。 如上方所述，如果無法在警示時間間隔內使用活動訊號，則會因活動訊號資料尚無法搜尋而產生警示，因此會不存在。 這會與 VM 已合法離線且未產生活動訊號資料的結果相同。 在正確時間間隔後隔天執行查詢會顯示有活動訊號且警示失敗。 事實上，活動訊號尚無法供搜尋，因為警示時間間隔設為太小。

- **頻率**。  指定查詢應執行、以及可用來使警示在一般情況下更有效回應的頻率。 值可介於 5 分鐘到 24 小時之間，且應該等於或小於警示時間間隔。  如果值大於時間範圍，則您可能有遺漏記錄的風險。<br>如果目標是讓最多 30 分鐘的延遲保持可靠，且標準延遲為 10 分鐘，時間間隔應該是一小時，且頻率值應為 10 分鐘。 這會在警示資料產生時的 10 到 20 分鐘之間，透過具有 10 分鐘擷取延遲的資料觸發警示。<br>若要避免由於時間間隔太寬而針對相同資料建立多個警示，可以使用 [抑制警示](log-analytics-tutorial-response.md#create-alerts) 選項來將警示抑制為至少與時間間隔一樣長。
  
- **閾值**。 系統會評估記錄搜尋的結果，以判斷是否應該建立警示。 不同類型的警示規則會有不同的閾值。

Log Analytics 中的各個警示規則是兩種類型其中之一。 下列各節會詳細說明這兩個類型。

- **[結果數目](#number-of-results-alert-rules)**。 當記錄搜尋所傳回的數目記錄超過指定數目時，系統會建立單一警示。
- **[計量測量](#metric-measurement-alert-rules)**。 針對記錄搜尋結果中的每個物件，若其值超過指定的閾值，系統就會為其建立警示。

警示規則類型之間的差異如下所示。

- **結果數目**警示規則一律會建立單一警示，而**計量測量**警示規則會為每個超過閾值的物件建立警示。
- **結果數目**警示規則會在閾值超過一次時建立警示。 **計量測量**警示規則會在閾值於特定時間間隔內超過一定次數時建立警示。

## <a name="number-of-results-alert-rules"></a>結果數目警示規則
**結果數目**警示規則會在搜尋查詢所傳回的記錄數目超過指定閾值時建立單一警示。

### <a name="threshold"></a>閾值
**結果數目**警示規則的閾值會大於或小於特定值。 如果記錄搜尋所傳回的記錄數目符合此準則，則會建立警示。

### <a name="scenarios"></a>案例

#### <a name="events"></a>活動
這種類型的警示規則適用於處理例如 Windows 事件記錄、Syslog 和自訂記錄的事件。 您可能希望在建立特定的錯誤事件時，或是在特定時間範圍內建立多個錯誤事件時建立警示。

若要對單一事件發出警示，請將結果數目設為大於 0 並將頻率與時間範圍設為 5 分鐘。 如此一來，將會每隔 5 分鐘執行一次查詢，並檢查在上次執行查詢後是否發生所建立的單一事件。 較長的頻率可能會延遲收集事件和建立警示的時間。

有些應用程式可能會記錄不一定會產生警示的偶發錯誤。 例如，應用程式可能會重試造成錯誤事件的處理序，而下一次就會成功。 在此情況下，除非在特定時間範圍內建立多個事件，否則您不會想建立警示。

在某些情況下，您可能想在某個事件不存在時建立警示。 例如，處理序可能會記錄一般事件，表示運作正常。 如果它不在特定的時間範圍內記錄一個事件，則應建立警示。 在此情況下，您會將臨界值設定為**小於 1**。

#### <a name="performance-alerts"></a>效能警示
[效能資料](log-analytics-data-sources-performance-counters.md) 會儲存為 OMS 儲存機制中類似事件的記錄。 如果您要在效能計數器超過特定臨界值時發出警示，則該臨界值應包含在查詢中。

例如，如果您想要在處理器執行超過 90% 時發出警示，您會使用如下的查詢，且警示規則的臨界值**大於 0**。

    Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" and CounterValue>90

如果您想要針對特定的時間範圍在處理器平均超過 90% 時發出警示，您會使用如下的 `measure` 命令來使用查詢，且警示規則的臨界值**大於 0**。

    Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" | summarize avg(CounterValue) by Computer | where CounterValue>90



## <a name="metric-measurement-alert-rules"></a>公制度量單位的警示規則

>[!NOTE]
> 計量測量警示規則目前處於公開預覽狀態。

**計量測量**警示規則會針對查詢中其值超過指定閾值的每個物件建立警示。 這些警示規則與**結果數目**警示規則具有下列明顯差異。

#### <a name="log-search"></a>記錄搜尋
您可以對**結果數目**警示規則使用任何查詢，但是對計量測量警示規則使用查詢時則有特定需求。  它必須包含 `measure` 命令以將結果群組在特定欄位上。 此命令必須包含下列元素。


- **彙總函式**。  決定要執行的計算，並可能決定要彙總的數值欄位。 例如，**count()** 會在查詢中傳回記錄數目，**avg(CounterValue)** 則會傳回 CounterValue 欄位在一段間隔內的平均值。
- **群組欄位**。  系統會為這個欄位中的每個執行個體建立帶有彙總值的記錄，而且每個執行個體都可產生警示。 例如，如果您想要為每部電腦產生警示，您可以使用**依電腦**。   
- **間隔**。  定義用來彙總資料的時間間隔。  例如，如果您指定 **5 分鐘**，則系統會為群組欄位的每個執行個體建立記錄，而這些執行個體是在針對警示所指定的時間範圍內，以每 5 分鐘為間隔進行彙總的。

#### <a name="threshold"></a>閾值
計量測量警示規則的閾值是由彙總值與若干違規所定義。 如果記錄搜尋中的任何資料點超過此值，系統就會將其視為違規。 如果結果中任何物件的違規數目超過指定值，系統舊會為該物件建立警示。

#### <a name="example"></a>範例
假設您想要在任何電腦於過去 30 分鐘內發生三次處理器使用率超過 90% 時收到警示。 您可以建立詳細資料如下的警示規則。

**查詢：**Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer Interval 5minute<br>
**時間範圍︰**30 分鐘<br>
**警示頻率︰**5 分鐘<br>
**彙總值︰**大於 90<br>
**警示觸發依據︰**違規總數大於 5<br>

查詢會以 5 分鐘為間隔為每部電腦建立平均值。 此查詢會每隔 5 分鐘針對在先前 30 分鐘內所收集的資料來執行。 三部電腦的資料範例如下所示。

![查詢結果範例](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

在此範例中，系統會為 srv02 和 srv03 建立個別警示，因為它們在時間範圍內違反 90% 閾值 3 次。 如果**警示觸發依據︰**變更為**連續**，則系統只會為 srv03 建立警示，因為它違反了 3 個連續取樣的閾值。

## <a name="alert-records"></a>警示記錄
在 Log Analytics 中由警示規則建立的警示記錄，**Type** 為 **Alert**，**SourceSystem** 為 **OMS**。 其屬性如下表所示。

| 屬性 | 說明 |
|:--- |:--- |
| 類型 |*警示* |
| SourceSystem |*OMS* |
| *Object*  | [計量測量警示](#metric-measurement-alert-rules)具有群組欄位的屬性。 例如，如果記錄搜尋針對「電腦」進行群組，則警示記錄會有電腦欄位，並以電腦名稱作為值。
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
