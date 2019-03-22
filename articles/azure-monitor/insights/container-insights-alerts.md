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
ms.date: 02/26/2019
ms.author: magoedte
ms.openlocfilehash: e6fdb0d57a44578647c1f16dc76c557296f20ddb
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886756"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>如何設定 Azure 監視器中適用於容器的效能問題的警示
適用於容器的 Azure 監視器會監視部署至 Azure Container Instances 或 Azure Kubernetes Service (AKS) 上所裝載受控 Kubernetes 叢集的容器工作負載效能。 

本文說明如何啟用警示用於下列情況：

* 當叢集節點上的 CPU 和記憶體使用量或超過您定義的臨界值。
* 當任何控制器內的容器上的 CPU 或記憶體使用率超過您定義的閾值，相較於對應的資源上設定的限制。

若要 CPU 或記憶體使用率過高的叢集或控制站時，便會發出警示，您可以建立以所提供的記錄檔查詢為基礎的計量測量警示規則。 查詢會比較使用現在運算子存在的日期時間，並會回到一小時。 Azure 監視器針對容器儲存的所有日期都為 UTC 格式。

在開始之前，如果您不熟悉 Azure 監視器中的警示，請參閱 [Microsoft Azure 中的警示概觀](../platform/alerts-overview.md)。 若要深入了解使用記錄查詢的警示，請參閱 [Azure 監視器中的記錄警示](../platform/alerts-unified-log.md)

## <a name="resource-utilization-log-search-queries"></a>資源使用率記錄檔搜尋查詢
在本節中的查詢被提供來支援每個警示的狀況。 查詢所需的步驟 7 之下[建立警示](#create-alert-rule)下一節。  

下列查詢會計算成員節點每分鐘的平均 CPU 使用率，以作為平均 CPU 使用率。  

```kusto
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

```kusto
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
>[!IMPORTANT]
>下列查詢會包含預留位置字串值，您的叢集和控制站名稱-< 您的叢集名稱 > 和 < 您的控制器名稱 >。 設定警示之前，您環境特有的值取代預留位置。 


下列查詢會計算的平均 CPU 使用率在控制器中的所有容器的 CPU 使用率的控制器中的每個容器執行個體的容器設定的限制百分比每隔一分鐘的平均值。

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

下列查詢會計算在控制器中的所有容器的平均記憶體使用量的記憶體使用量在控制器中的每個容器執行個體的容器設定的限制百分比每隔一分鐘的平均值。

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

## <a name="create-alert-rule"></a>建立警示規則
執行下列步驟來使用其中一種稍早提供的記錄檔搜尋規則的 Azure 監視器中建立記錄警示。  

>[!NOTE]
>下列程序會要求您切換到新的記錄警示 API 中所述[切換 API 喜好設定的記錄警示](../platform/alerts-log-api-switch.md)如果您要建立容器資源使用率的警示規則。 
>

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 Azure 入口網站中，從左側窗格選取 [監視器]。 在 [Insights] 區段下方，選取 [容器]。    
3. 從 [受監視的叢集] 索引標籤中，選取按一下清單中的叢集名稱來選取叢集。
4. 在左側窗格中的 [監視] 區段下，選取 [記錄] 以開啟 Azure 監視器記錄頁面，此頁面可用來撰寫及執行 Azure Log Analytics 查詢。
5. 在 [記錄] 頁面上，按一下 [+ 新增警示規則]。
6. 在 [條件] 區段下，按一下預先定義的自訂記錄條件 [每當自訂記錄搜尋]**<logic undefined>**。 系統會自動為我們選取 [自訂記錄搜尋] 訊號類型，因為我們直接從 Azure 監視器記錄頁面起始了建立警示規則。  
7. 貼上其中一個[查詢](#resource-utilization-log-search-queries)稍早提供**搜尋查詢**欄位。 

8. 使用下列資訊設定警示：

    a. 從 [依據] 下拉式清單中，選取 [計量測量]。 計量測量會針對查詢中其值超過指定閾值的每個物件建立警示。  
    b. 針對 [條件] 選取 [大於]，並輸入 **75** 作為初始基準 [閾值]，或輸入符合您準則的值。  
    c. 在 [觸發程序警示依據] 區段下方，選取 [連續違規]，然後從下拉式清單中選取 [大於] 並輸入 **2** 作為值。  
    d. 如果在設定容器 CPU 或記憶體使用率的警示**彙總依據**選取**ContainerName**從下拉式清單。  
    e. 在 [評估依據] 下方，將 [期限] 值修改為 60 分鐘。 此規則會每隔五分鐘執行一次，並傳回在目前時間之前的一小時內建立的記錄。 將時間週期設定為較大的時間範圍，可因應資料延遲的可能性，並確保查詢會傳回資料，以避免發生永遠不會引發警示的漏判。 

9. 按一下 [完成] 完成警示規則。
10. 在 [警示規則名稱] 欄位中提供警示的名稱。 指定警示特定的詳細 [描述]，並從提供的選項中選取適當的嚴重性。
11. 若要在完成建立後立即啟動警示規則，請接受 [在建立時啟用規則] 的預設值。
12. 對於最後一個步驟，選取現有的或建立新的 [動作群組]，這樣可確保每次觸發警示時會執行相同動作，且警示可用於您定義的每個規則。 根據您的 IT 或 DevOps 作業管理事件的方式來設定。 
13. 按一下 [建立警示規則] 以完成警示規則。 此警示規則會立即開始執行。

## <a name="next-steps"></a>後續步驟

* 檢閱一些[記錄查詢範例](container-insights-analyze.md#search-logs-to-analyze-data)，以了解預先定義查詢或是評估及使用或自訂其他警示案例的範例。 
* 若要繼續了解如何使用 Azure 監視器並監視您 AKS 叢集的其他層面，請參閱[檢視 Azure Kubernetes 服務健康情況](container-insights-analyze.md)
