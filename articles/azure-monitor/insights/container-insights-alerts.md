---
title: 使用適用於容器的 Azure 監視器建立效能警示 | Microsoft Docs
description: 本文描述您可以如何根據來自適用於容器的 Azure 監視器之記憶體和 CPU 使用率，建立記錄查詢。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: magoedte
ms.openlocfilehash: c275d50ab927895eca3aa018b71ab6989a4dde5a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238835"
---
# <a name="how-to-set-up-alerts-in-azure-monitor-for-container-performance-problems"></a>在 Azure 監視器中針對容器效能問題設定警示
適用於容器的 Azure 監視器會監視部署至 Azure Container Instances 或 Azure Kubernetes Service (AKS) 上所裝載受控 Kubernetes 叢集的容器工作負載效能。 

本文描述如何啟用警示，當叢集節點的處理器和記憶體使用率超過您所定義的閾值時，便會發出警示。

## <a name="create-alert-on-cluster-cpu-or-memory-utilization"></a>建立叢集 CPU 或記憶體使用率的警示
建立以提供的記錄查詢為基礎的工制度量單位，以在叢集 CPU 或記憶體使用率變高時發出警示。 該查詢使用 `now` 運算子來比較日期時間和目前時間，並往前推一小時。 Azure 監視器針對容器儲存的所有日期都為 UTC 格式。  

在開始之前，如果您不熟悉 Azure 監視器中的警示，請參閱 [Microsoft Azure 中的警示概觀](../platform/alerts-overview.md)。 若要深入了解使用記錄查詢的警示，請參閱 [Azure 監視器中的記錄警示](../platform/alerts-unified-log.md)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 Azure 入口網站中，從左側窗格選取 [監視器]。 在 [Insights] 區段下方，選取 [容器]。    
3. 從 [受監視的叢集] 索引標籤中，選取按一下清單中的叢集名稱來選取叢集。
4. 在左側窗格中的 [監視] 區段下，選取 [記錄] 以開啟 Azure 監視器記錄頁面，此頁面可用來撰寫及執行 Azure Log Analytics 查詢。
5. 在 [記錄] 頁面上，按一下 [+ 新增警示規則]。
6. 在 [條件] 區段下，按一下預先定義的自訂記錄條件 [每當自訂記錄搜尋]**<logic undefined>**。 系統會自動為我們選取 [自訂記錄搜尋] 訊號類型，因為我們直接從 Azure 監視器記錄頁面起始了建立警示規則。  
7. 將以下其中一個查詢貼上到 [搜尋查詢] 欄位。 下列查詢會計算成員節點每分鐘的平均 CPU 使用率，以作為平均 CPU 使用率。

    ```
    let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let capacityCounterName = 'cpuCapacityNanoCores';
    let usageCounterName = 'cpuUsageNanoCores';
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    // cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
    | distinct ClusterName, Computer
    | join hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime
       | where TimeGenerated >= startDateTime
       | where ObjectName == 'K8SNode'
       | where CounterName == capacityCounterName
       | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
       | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
    ) on Computer
    | join kind=inner hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime + trendBinSize
       | where TimeGenerated >= startDateTime - trendBinSize
       | where ObjectName == 'K8SNode'
       | where CounterName == usageCounterName
       | project Computer, UsageValue = CounterValue, TimeGenerated
    ) on Computer
    | where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
    | project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
    | summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
    ```

    下列查詢會計算成員節點每分鐘的平均記憶體使用率，以作為平均記憶體使用率。

    ```
    let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let capacityCounterName = 'memoryCapacityBytes';
    let usageCounterName = 'memoryRssBytes';
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    // cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
    | distinct ClusterName, Computer
    | join hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime
       | where TimeGenerated >= startDateTime
       | where ObjectName == 'K8SNode'
       | where CounterName == capacityCounterName
       | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
       | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
    ) on Computer
    | join kind=inner hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime + trendBinSize
       | where TimeGenerated >= startDateTime - trendBinSize
       | where ObjectName == 'K8SNode'
       | where CounterName == usageCounterName
       | project Computer, UsageValue = CounterValue, TimeGenerated
    ) on Computer
    | where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
    | project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
    | summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
    ```

8. 使用下列資訊設定警示：

    a. 從 [依據] 下拉式清單中，選取 [計量測量]。 計量測量會針對查詢中其值超過指定閾值的每個物件建立警示。  
    b. 針對 [條件] 選取 [大於]，並輸入 **75** 作為初始基準 [閾值]，或輸入符合您準則的值。  
    c. 在 [觸發程序警示依據] 區段下方，選取 [連續違規]，然後從下拉式清單中選取 [大於] 並輸入 **2** 作為值。  
    d. 在 [評估依據] 下方，將 [期限] 值修改為 60 分鐘。 此規則會每隔五分鐘執行一次，並傳回在目前時間之前的一小時內建立的記錄。 將時間週期設定為較大的時間範圍，可因應資料延遲的可能性，並確保查詢會傳回資料，以避免發生永遠不會引發警示的漏判。 

9. 按一下 [完成] 完成警示規則。
10. 在 [警示規則名稱] 欄位中提供警示的名稱。 指定警示特定的詳細 [描述]，並從提供的選項中選取適當的嚴重性。
11. 若要在完成建立後立即啟動警示規則，請接受 [在建立時啟用規則] 的預設值。
12. 對於最後一個步驟，選取現有的或建立新的 [動作群組]，這樣可確保每次觸發警示時會執行相同動作，且警示可用於您定義的每個規則。 根據您的 IT 或 DevOps 作業管理事件的方式來設定。 
13. 按一下 [建立警示規則] 以完成警示規則。 此警示規則會立即開始執行。

## <a name="next-steps"></a>後續步驟

* 檢閱一些[記錄查詢範例](container-insights-analyze.md#search-logs-to-analyze-data)，以了解預先定義查詢或是評估及使用或自訂其他警示案例的範例。 
* 若要繼續了解如何使用 Azure 監視器並監視您 AKS 叢集的其他層面，請參閱[檢視 Azure Kubernetes 服務健康情況](container-insights-analyze.md)
