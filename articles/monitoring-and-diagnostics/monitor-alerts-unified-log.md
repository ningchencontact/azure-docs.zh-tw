---
title: "Azure 監視器中的記錄警示 - 警示 (預覽) | Microsoft Docs"
description: "當您指定的複雜查詢條件符合 Azure 警示 (預覽) 時，觸發電子郵件、通知、呼叫網站 URL (Webhook) 或自動化。"
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
ms.date: 02/02/2018
ms.author: vinagara
ms.openlocfilehash: 438776e7f0885dbdb0d66ccdd18d854e14beb299
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2018
---
# <a name="log-alerts-in-azure-monitor---alerts-preview"></a>Azure 監視器中的記錄警示 - 警示 (預覽)
本文詳述在 Azure 警示 (預覽) 中，警示規則如何在 Analytics 中運作，並說明不同類型的記錄警示規則之間的差異。

Azure 警示 (預覽) 目前支援來自 [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 和 [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events) 查詢上的記錄警示。

> [!WARNING]

> Azure 警示 (預覽) 中的記錄警示目前 不支援跨工作區中或跨應用程式的查詢。

此外，使用者可在 Azure 中選擇的 Analytics平台中完成理想的查詢後，*藉著儲存查詢來匯入它們以便在警示 (預覽) 中使用*。 要遵循的步驟如下：
- 針對 Application Insights：前往 Analytics 入口網站，驗證查詢及其結果。 然後使用唯一名稱儲存到 [共用的查詢]中。
- 針對 Log Analytics：前往記錄搜尋，驗證查詢及其結果。 然後使用唯一名稱儲存到任何類別中。

然後，[在警示 (預覽) 中建立記錄警示時](monitor-alerts-unified-usage.md)，您會看到儲存的查詢列在 [記錄 (已儲存的查詢)] 訊號類型；如下列範例所示：![已儲存的查詢匯入至警示](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> 使用 [記錄 (已儲存的查詢)] 會造成匯入到警示。 因此，在 Analytics 中完成後的任何變更不會反映到已儲存的警示規則中，反之亦然。

## <a name="log-alert-rules"></a>記錄警示規則

警示會由自動定期執行記錄查詢的 Azure 警示 (預覽) 所建立。  如果記錄查詢的結果符合特定準則，則會建立警示的記錄。 此規則接著可以使用[動作群組](monitoring-action-groups.md)自動執行一或多個動作，以主動通知警示或叫用另一個處理序，例如使用 [json based webhook](monitor-alerts-unified-log-webhook.md) 傳送資料到外部應用程式。 不同類型的警示規則會使用不同邏輯來執行這項分析。

警示規則會由下列詳細資料定義：

- **記錄查詢**。  每次引發警示規則都會執行的查詢。  此查詢所傳回的記錄將是用來判斷是否要建立警示。
- **時間範圍**。  指定查詢的時間範圍。  查詢只會傳回在此目前時間範圍內建立的記錄。  時間範圍可以是 5 分鐘至 1440 分鐘 (或 24 小時) 之間的任何值。 例如，如果時間範圍設定為 60 分鐘，則查詢會在下午 1:15 執行，只會傳回在下午 12:15 與下午 1:15 之間建立的記錄。
- **頻率**。  指定應執行查詢的頻率。 可以是介於 5 分鐘與 24 小時之間的任何值。 應等於或小於此時間範圍。  如果值大於時間範圍，則您可能有遺漏記錄的風險。<br>例如，請考慮 30 分鐘的時間範圍，以及 60 分鐘的頻率。  如果在 1:00 執行查詢，它會傳回 12:30 到下午 1:00 之間的記錄。  下一次執行查詢就是 2:00 時，它會傳回 1:30 至 2:00 之間的記錄。  1:00 和 1:30 之間建立的任何記錄一律不會評估。
- **閾值**。  系統會評估記錄搜尋的結果，以判斷是否應該建立警示。  不同類型的警示規則會有不同的閾值。

Log Analytics 中的各個警示規則是兩種類型其中之一。  下列各節會詳細說明這兩個類型。

- **[結果數目](#number-of-results-alert-rules)**。 當記錄搜尋所傳回的數目記錄超過指定數目時，系統會建立單一警示。
- **[計量測量](#metric-measurement-alert-rules)**。  針對記錄搜尋結果中的每個物件，若其值超過指定的閾值，系統就會為其建立警示。

警示規則類型之間的差異如下所示。

- **結果數目警示規則一律會建立單一警示，而**計量測量**警示規則會為每個超過閾值的物件建立警示。
- **結果數目**警示規則會在閾值超過一次時建立警示。 **計量測量**警示規則會在閾值於特定時間間隔內超過一定次數時建立警示。

## <a name="number-of-results-alert-rules"></a>結果數目警示規則
**結果數目**警示規則會在搜尋查詢所傳回的記錄數目超過指定閾值時建立單一警示。 這種類型的警示規則適用於處理例如 Windows 事件記錄、Syslog、WebApp 回應和自訂記錄的事件。  您可能希望在建立特定的錯誤事件時，或是在特定時間範圍內建立多個錯誤事件時建立警示。

**閾值**：**結果數目警示規則的閾值會大於或小於特定值。  如果記錄搜尋所傳回的記錄數目符合此準則，則會建立警示。

若要針對單一事件警示，請將結果數目設定為大於 0，並檢查在上次執行查詢後是否發生所建立的單一事件。 有些應用程式可能會記錄不一定會產生警示的偶發錯誤。  例如，應用程式可能會重試造成錯誤事件的處理序，而下一次就會成功。  在此情況下，除非在特定時間範圍內建立多個事件，否則您不會想建立警示。  

在某些情況下，您可能想在某個事件不存在時建立警示。  例如，處理序可能會記錄一般事件，表示運作正常。  如果它不在特定的時間範圍內記錄一個事件，則應建立警示。  在此情況下，您會將閾值設定為**小於 1**。

### <a name="example"></a>範例
請考慮一個情境，您想要知道您的 Web 架構應用程式何時向使用者回應程式碼 500 (亦即) 內部伺服器錯誤。 您可以建立詳細資料如下的警示規則：  
**查詢：**要求 | 其中 resultCode =="500"<br>
**時間範圍︰**30 分鐘<br>
**警示頻率：** 5 分鐘<br>
**臨界值︰**大於 0<br>

然後警示會以 30 分鐘的資料，每隔 5 分鐘執行一次查詢，以尋找結果碼為 500 的任何記錄。 如果找到一筆這樣的記錄，它就會引發警示並觸發設定的動作。

## <a name="metric-measurement-alert-rules"></a>公制度量單位的警示規則

**計量測量**警示規則會針對查詢中其值超過指定閾值的每個物件建立警示。  這些警示規則與**結果數目**警示規則具有下列明顯差異。

**彙總函式**：決定要執行的計算，並可能決定要彙總的數值欄位。  例如，**count()** 會在查詢中傳回記錄數目，**avg(CounterValue)** 則會傳回 CounterValue 欄位在一段間隔內的平均值。

> [!NOTE]

> 查詢中的彙總函式必須是名為/稱為：AggregatedValue，並提供數值。 


**群組欄位**：系統會為這個欄位中的每個執行個體建立帶有彙總值的記錄，而且每個執行個體都可產生警示。  例如，如果您想要為每部電腦產生警示，您可以使用**依電腦**   

> [!NOTE]

> 對於以 Application Insights 為基礎的計量測量警示規則，您可以指定用來分組資料的欄位。 若要這樣做，請使用規則定義中的 [彙總依據] 選項。   

**間隔**：定義用來彙總資料的時間間隔。  例如，如果您指定 **5 分鐘**，則系統會為群組欄位的每個執行個體建立記錄，而這些執行個體是在針對警示所指定的時間範圍內以每 5 分鐘為間隔進行彙總的。
> [!NOTE]
> 查詢中必須使用 Bin 函式。 此外，如果使用 Bin 函式產生的時間間隙是不相等的時間間隔 - 警示會改用 bin_at 函式以確保沒有固定的點

**閾值**：計量測量警示規則的閾值是由彙總值與若干違規所定義。  如果記錄搜尋中的任何資料點超過此值，系統就會將其視為違規。  如果結果中任何物件的違規數目超過指定值，系統舊會為該物件建立警示。

#### <a name="example"></a>範例
假設您想要在任何電腦於過去 30 分鐘內發生三次處理器使用率超過 90% 時收到警示。  您可以建立詳細資料如下的警示規則：  

**查詢：** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5 m), Computer<br>
**時間範圍：**30 分鐘<br>
**警示頻率：** 5 分鐘<br>
**彙總值︰**大於 90<br>
**警示觸發依據︰**違規總數大於 5<br>

查詢會以 5 分鐘為間隔為每部電腦建立平均值。  此查詢會每隔 5 分鐘針對在先前 30 分鐘內所收集的資料來執行。  三部電腦的資料範例如下所示。

![查詢結果範例](../log-analytics/media/log-analytics-alerts/metrics-measurement-sample-graph.png)

在此範例中，系統會為 srv02 和 srv03 建立個別警示，因為它們在時間範圍內違反 90% 閾值 3 次。  如果**警示觸發依據︰**變更為**連續**，則系統只會為 srv03 建立警示，因為它違反了 3 個連續取樣的閾值。


## <a name="next-steps"></a>後續步驟
* 了解 [Webhook 動作記錄警示](monitor-alerts-unified-log-webhook.md)
* [取得 Azure 警示 (預覽) 概觀](monitoring-overview-unified-alerts.md)
* 了解[使用 Azure 警示 (預覽)](monitor-alerts-unified-usage.md)
* 深入了解 [Application Insights](../application-insights/app-insights-analytics.md)
* 深入了解 [Log Analytics](../log-analytics/log-analytics-overview.md)。    
