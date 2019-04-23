---
title: 建立效能警示 for containers 中使用 Azure 監視器 |Microsoft Docs
description: 本文說明如何使用適用於容器的 Azure 監視器來建立自訂記錄檔查詢的記憶體和 CPU 使用率為基礎的警示。
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
ms.date: 04/01/2019
ms.author: magoedte
ms.openlocfilehash: ebe2c2b488e3d71597dd24f5504a14dd7ce6671e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791536"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>如何設定 Azure 監視器中適用於容器的效能問題的警示
適用於容器的 azure 監視會監視部署至 Azure 容器執行個體，或受管理 Kubernetes 叢集，裝載在 Azure Kubernetes Service (AKS) 的容器工作負載的效能。

這篇文章說明如何啟用警示，在下列情況下：

* 當叢集節點上的 CPU 或記憶體使用率超過定義的臨界值
* 當控制器內的任何容器上的 CPU 或記憶體使用率超過定義的閾值，相較於對應的資源設定的限制
* *NotReady*狀態 節點計數
*  *失敗*，*暫止*，*未知*，*執行*，或*Succeeded* pod 階段計數

若要高 CPU 或記憶體使用量，在叢集節點上的警示，使用提供給建立度量警示或計量測量警示的查詢。 計量警示具有較低的延遲，比記錄警示。 但是，記錄警示提供進階的查詢和更新版本的複雜度。 記錄查詢使用比較目前的日期時間的警示*現在*運算子，並移回一小時。 （適用於容器的 azure 監視器會儲存所有日期格式為 Coordinated Universal Time (UTC)）。

如果您不熟悉 Azure 監視器會發出警示，請參閱[在 Microsoft Azure 中的警示概觀](../platform/alerts-overview.md)開始之前。 若要深入了解使用記錄檔查詢的警示，請參閱[Azure 監視器中的記錄警示](../platform/alerts-unified-log.md)。 如需有關計量警示的詳細資訊，請參閱[在 Azure 監視器計量警示](../platform/alerts-metric-overview.md)。

## <a name="resource-utilization-log-search-queries"></a>資源使用率記錄檔搜尋查詢
在本節中的查詢支援每個警示的狀況。 它們會用於步驟 7[建立警示](#create-an-alert-rule)一節。

下列查詢會計算平均 CPU 使用率的平均值成員節點的 CPU 使用率每隔一分鐘。  

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

下列查詢會計算成員節點的記憶體使用率每隔一分鐘的平均值的平均記憶體的使用率。

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
>下列查詢會使用預留位置值\<您的叢集名稱 > 和\<您的控制器名稱 > 來代表您的叢集和控制站。 它們與您環境特有的值時取代您設定警示。

下列查詢會計算的平均 CPU 使用率在控制器中的所有容器的 CPU 使用率在控制器中的每個容器執行個體的每分鐘的平均值。 度量單位為容器設定限制的百分比。

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

下列查詢會計算在控制器中的所有容器的平均記憶體使用量的記憶體使用率在控制器中的每個容器執行個體的每分鐘的平均值。 度量單位為容器設定限制的百分比。

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

下列查詢會傳回所有的節點和狀態的計數*就緒*並*NotReady*。

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
下列查詢會傳回 pod 階段計數為基礎的所有階段：*失敗*，*暫止*，*未知*，*執行*，或*成功*。  

```kusto
let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let clusterName = '<your-cluster-name>';
    KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                 by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>這類特定 pod 階段中，發出警示*暫止*， *Failed*，或*未知*，修改查詢的最後一行。 例如，若警示要*由於*使用： <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

## <a name="create-an-alert-rule"></a>建立警示規則
請遵循下列步驟來建立在 Azure 監視器中的記錄警示，使用其中一種稍早所提供的記錄檔搜尋規則。  

>[!NOTE]
>下列程序來建立警示規則的容器資源使用量會要求您切換到新的記錄檔，系統會通知 API 中所述[切換 API 喜好設定的記錄警示](../platform/alerts-log-api-switch.md)。
>

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 **監視器**在左手邊的窗格。 底下**Insights**，選取**容器**。
3. 在 **監視叢集**索引標籤上，從清單中選取叢集。
4. 在左側窗格中**監視**，選取**記錄**以開啟 [Azure 監視器的記錄] 頁面。 您可以使用此頁面來撰寫及執行 Azure Log Analytics 查詢。
5. 在 **記錄檔**頁面上，選取 **+ 新增警示規則**。
6. 在 **條件**區段中，選取**每當自訂記錄檔搜尋\<未定義邏輯 >** 預先定義的自訂記錄檔條件。 **自訂記錄檔搜尋**因為我們要直接從 Azure 監視器的 [記錄] 頁面來建立警示規則，會自動選取訊號類型。  
7. 貼上其中一個[查詢](#resource-utilization-log-search-queries)稍早提供**搜尋查詢**欄位。
8. 請設定警示，如下所示：

    1. 從 [依據] 下拉式清單中，選取 [計量測量]。 計量測量我們指定的臨界值以上的值的查詢中建立每個物件的警示。
    1. 針對**條件**，選取**大於**，然後輸入**75**為初始的基準**臨界值**。 或輸入不同的值符合您的準則。
    1. 在 **觸發程序警示依據**區段中，選取**連續違規**。 從下拉式清單中，選取**Greater than**，然後輸入**2**。
    1. 若要在設定容器 CPU 或記憶體使用率的警示**彙總依據**，選取**ContainerName**。 
    1. 在**為基礎的已評估**區段中，將**期間**值**60 分鐘**。 此規則會每隔 5 分鐘執行一次，並傳回前一個小時，從目前的時間內所建立的記錄。 將時間期間設為範圍的帳戶可能會發生的資料延遲。 它也可確保查詢會傳回資料，以避免 (false negative) 中永遠不會引發警示。

9. 選取 **完成**完成警示規則。
10. 輸入中的名稱**警示規則名稱**欄位。 指定**描述**，提供有關警示的詳細資料。 然後從所提供的選項中選取適當的嚴重性層級。
11. 若要立即啟用警示規則，接受預設值**在建立時啟用規則**。
12. 選取現有**動作群組**或建立新的群組。 這個步驟可確保每次時，便會觸發警示之後，會採取相同的動作。 設定根據您的 it 人員或 DevOps 作業團隊負責管理事件。
13. 選取 **建立警示規則**完成警示規則。 此警示規則會立即開始執行。

## <a name="next-steps"></a>後續步驟

* 檢視[記錄查詢範例](container-insights-analyze.md#search-logs-to-analyze-data)若要了解預先定義的查詢和範例，以評估或自訂的其他警示的案例。
* 若要深入了解 Azure 監視器，以及如何監視您的 AKS 叢集的其他層面，請參閱[檢視 Azure Kubernetes 服務健全狀況](container-insights-analyze.md)。
