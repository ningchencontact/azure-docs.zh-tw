---
title: "記錄警示的 Azure 監視的警示 （預覽） |Microsoft 文件"
description: "Azure 警示 （預覽） 符合您指定的複雜查詢條件時，觸發程序的電子郵件，通知，便會呼叫網站 Url (webhook) 或自動化。"
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: vinagara
ms.openlocfilehash: 99d222102ab0245c7c4dc8603eaedcfc88ae7a66
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2017
---
# <a name="log-alerts-in-azure-monitor---alerts-preview"></a>記錄警示的 Azure 監視的警示 （預覽）
本文章提供如何警示的規則，在 Azure 警示 （預覽） 中的分析查詢工作的詳細資料，並說明不同類型的記錄檔的警示規則之間的差異。
目前 Azure 警示 （預覽），只支援記錄的查詢上的警示[Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md)以撰寫[新的記錄分析查詢語言](../log-analytics/log-analytics-log-search-upgrade.md)

> [!WARNING]
> Azure 警示 (Preview)-記錄檔的警示，目前並不支援跨工作區中或跨應用程式的查詢。 

## <a name="log-alert-rules"></a>記錄檔的警示規則

會自動定期執行的記錄檔查詢的 Azure 警示 （預覽） 建立警示。  如果記錄檔查詢的結果符合特定準則，則會建立警示的記錄。 規則可以再自動執行主動通知您警示，或叫用另一個處理序執行中的 runbook，使用類似的一或多個動作[動作群組](monitoring-action-groups.md)。  不同類型的警示規則會使用不同邏輯來執行這項分析。

警示規則會由下列詳細資料定義：

- **記錄查詢**。  每次引發警示規則都會執行的查詢。  此查詢所傳回的記錄用來判斷是否要建立警示。
- **時間範圍**。  指定查詢的時間範圍。  查詢只會傳回在此目前時間範圍內建立的記錄。  時間間隔可以是 5 分鐘和 1440 分鐘或 24 小時之間的任何值。 例如，如果時間範圍設定為 60 分鐘，則查詢會在下午 1:15 執行，只會傳回在下午 12:15 與下午 1:15 之間建立的記錄。
- **頻率**。  指定應執行查詢的頻率。 可以是介於 5 分鐘與 24 小時之間的任何值。 應等於或小於此時間範圍。  如果值大於時間範圍，則您可能有遺漏記錄的風險。<br>例如，請考慮 30 分鐘的時間範圍，以及 60 分鐘的頻率。  如果在 1:00 執行查詢，它會傳回 12:30 到下午 1:00 之間的記錄。  下一次執行查詢就是 2:00 時，它會傳回 1:30 至 2:00 之間的記錄。  1:00 和 1:30 之間建立的任何記錄一律不會評估。
- **閾值**。  系統會評估記錄搜尋的結果，以判斷是否應該建立警示。  不同類型的警示規則會有不同的閾值。

Log Analytics 中的各個警示規則是兩種類型其中之一。  下列各節會詳細說明這兩個類型。

- **[結果數目](#number-of-results-alert-rules)**。 當記錄搜尋所傳回的數目記錄超過指定數目時，系統會建立單一警示。
- **[計量測量](#metric-measurement-alert-rules)**。  針對記錄搜尋結果中的每個物件，若其值超過指定的閾值，系統就會為其建立警示。

警示規則類型之間的差異如下所示。

- **結果數目**警示規則一律會建立單一警示時**度量的度量**警示規則建立的每個物件，超出臨界值警示。
- **結果數目**警示規則會在閾值超過一次時建立警示。 **計量測量**警示規則會在閾值於特定時間間隔內超過一定次數時建立警示。

## <a name="number-of-results-alert-rules"></a>結果數目警示規則
**結果數目**警示規則會在搜尋查詢所傳回的記錄數目超過指定閾值時建立單一警示。

**閾值**： 的閾值**的結果數目**警示規則是大於或小於特定值。  如果記錄搜尋所傳回的記錄數目符合此準則，則會建立警示。

### <a name="scenarios"></a>案例

#### <a name="events"></a>活動
這種類型的警示規則適用於處理例如 Windows 事件記錄、Syslog 和自訂記錄的事件。  您可能希望在建立特定的錯誤事件時，或是在特定時間範圍內建立多個錯誤事件時建立警示。

此警示乃針對單一事件，以設定結果的數目大於 0 頻率以及為五分鐘的時間間隔。  每隔五分鐘並檢查上次執行查詢之後所建立的單一事件發生在執行查詢。  較長的頻率可能會延遲收集事件和建立警示的時間。

有些應用程式可能會記錄不一定會產生警示的偶發錯誤。  例如，應用程式可能會重試造成錯誤事件的處理序，而下一次就會成功。  在此情況下，除非在特定時間範圍內建立多個事件，否則您不會想建立警示。  

在某些情況下，您可能想在某個事件不存在時建立警示。  例如，處理序可能會記錄一般事件，表示運作正常。  如果它不在特定的時間範圍內記錄一個事件，則應建立警示。  在此情況下，您會設定臨界值為**小於 1**。

## <a name="metric-measurement-alert-rules"></a>公制度量單位的警示規則

**計量測量**警示規則會針對查詢中其值超過指定閾值的每個物件建立警示。  這些警示規則與**結果數目**警示規則具有下列明顯差異。

**彙總函式**： 執行計算，以及可能數字會決定要彙總欄位。  例如， **count （)**在查詢中，傳回的記錄數目**avg(CounterValue)**傳回 CounterValue 欄位的平均值一段間隔。

**群組欄位**： 彙總的值的記錄會為此欄位中，每個執行個體建立，而且可以針對每個產生警示。  例如，如果您想要針對每一部電腦產生警示，您會使用**電腦**   

**間隔**： 定義時間間隔的彙總的資料。  例如，如果您指定**五分鐘**，會建立一則記錄，每隔 5 分鐘時段彙總指定警示的 [群組] 欄位的每個執行個體。

**閾值**： 度量的度量的警示規則的臨界值由彙總的值和定義的。  如果記錄搜尋中的任何資料點超過此值，系統就會將其視為違規。  如果結果中任何物件的違規數目超過指定值，系統舊會為該物件建立警示。

#### <a name="example"></a>範例
假設您想要在任何電腦於過去 30 分鐘內發生三次處理器使用率超過 90% 時收到警示。  您可以建立警示規則詳細資料如下：  

**查詢：**效能 | 其中 ObjectName = = [處理器] 和 CounterName = ="%Processor Time"| 摘要 AggregatedValue = avg(CounterValue) 的分類收納 (TimeGenerated 5 m)，電腦<br>
**時間間隔：** 30 分鐘<br>
**警示頻率：**五分鐘<br>
**彙總的值：**大於 90<br>
**警示觸發程序為基礎：**總計破壞大於 5<br>

查詢會建立每一部電腦的平均值在 5 分鐘的時間間隔。  此查詢會每隔 5 分鐘針對在先前 30 分鐘內所收集的資料來執行。  三部電腦的資料範例如下所示。

![查詢結果範例](../log-analytics/media/log-analytics-alerts/metrics-measurement-sample-graph.png)

在此範例中，系統會為 srv02 和 srv03 建立個別警示，因為它們在時間範圍內違反 90% 閾值 3 次。  如果**警示觸發程序為基礎：**已變更為**Consecutive**則會僅針對 srv03 建立警示，因為它違反服務等級的三個連續取樣閾值。


## <a name="next-steps"></a>後續步驟
* [取得 Azure 警示 （預覽） 的概觀](monitoring-overview-unified-alerts.md) 
* 深入了解[使用 Azure 警示 （預覽）](monitor-alerts-unified-usage.md)
* 深入了解 [Log Analytics](../log-analytics/log-analytics-overview.md)。    
