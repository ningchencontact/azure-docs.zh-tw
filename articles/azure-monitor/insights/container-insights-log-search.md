---
title: 如何從容器的 Azure 監視器查詢記錄 |Microsoft Docs
description: 適用于容器的 Azure 監視器會收集計量和記錄資料，而本文會描述記錄並包含範例查詢。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/12/2019
ms.openlocfilehash: c3a034776b32db57f70ddee960c1cd5fc96b170b
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555421"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>如何從容器的 Azure 監視器查詢記錄

容器的 Azure 監視器會從容器主機和容器收集效能計量、清查資料和健全狀況狀態資訊，並將其轉送至 Azure 監視器中的 Log Analytics 工作區。 每隔三分鐘會收集一次資料。 這項資料可用於 Azure 監視器中的[查詢](../../azure-monitor/log-query/log-query-overview.md)。 您可以將此資料套用至各種案例，包括移轉規劃、容量分析、探索和隨選效能疑難排解。

## <a name="container-records"></a>容器資料列

下表顯示適用於容器的 Azure 監視器所收集的記錄範例，以及記錄搜尋結果中所顯示的資料類型：

| Data type | 記錄檔搜尋中的資料類型 | 欄位 |
| --- | --- | --- |
| 主機和容器的效能 | `Perf` | Computer、ObjectName、CounterName &#40;%Processor Time、Disk Reads MB、Disk Writes MB、Memory Usage MB、Network Receive Bytes、Network Send Bytes、Processor Usage sec、Network&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem |
| 容器清查 | `ContainerInventory` | TimeGenerated、Computer、container name、ContainerHostname、Image、ImageTag、ContainerState、ExitCode、EnvironmentVar、Command、CreatedTime、StartedTime、FinishedTime、SourceSystem、ContainerID、ImageID |
| 容器記錄檔 | `ContainerLog` | TimeGenerated、Computer、image ID、container name、LogEntrySource、LogEntry、SourceSystem、ContainerID |
| 容器節點清查 | `ContainerNodeInventory`| TimeGenerated、Computer、ClassName_s、DockerVersion_s、OperatingSystem_s、Volume_s、Network_s、NodeRole_s、OrchestratorType_s、InstanceID_g、SourceSystem|
| 清查 Kubernetes 叢集中的 Pod | `KubePodInventory` | TimeGenerated、Computer、ClusterId、ContainerCreationTimeStamp、PodUid、PodCreationTimeStamp、ContainerRestartCount、PodRestartCount、PodStartTime、ContainerStartTime、ServiceName、ControllerKind、ControllerName、ContainerStatus、 ContainerStatusReason、ContainerID、容器名稱、PodLabel、Namespace、PodStatus、ClusterName、PodIp、及 sourcesystem |
| 清查 Kubernetes 叢集中的節點部分 | `KubeNodeInventory` | TimeGenerated、Computer、ClusterName、ClusterId、LastTransitionTimeReady、Labels、Status、KubeletVersion、KubeProxyVersion、CreationTimeStamp、SourceSystem | 
| Kubernetes 事件 | `KubeEvents` | TimeGenerated、Computer、ClusterId_s、FirstSeen_t、LastSeen_t、Count_d、ObjectKind_s、Namespace_s、Name_s、Reason_s、Type_s、TimeGenerated_s、SourceComponent_s、ClusterName_s、Message、SourceSystem | 
| Kubernetes 叢集中的服務 | `KubeServices` | TimeGenerated、ServiceName_s、Namespace_s、SelectorLabels_s、ClusterId_s、ClusterName_s、ClusterIP_s、ServiceType_s、SourceSystem | 
| Kubernetes 叢集節點部分的效能計量 | Perf &#124; where ObjectName == “K8SNode” | Computer、ObjectName、CounterName &#40;CpuAllocatableBytes、MemoryAllocatableBytes、CpuCapacityNanoCores、MemoryCapacityBytes、MemoryRssBytes、CpuUsageNanoCores、MemoryWorkingsetBytes、restartTimeEpoch&#41;、CounterValue、TimeGenerated、CounterPath、及 sourcesystem | 
| Kubernetes 叢集容器部分的效能計量 | Perf &#124; where ObjectName == “K8SContainer” | CounterName &#40; CpuRequestNanoCores、MemoryRequestBytes、CpuLimitNanoCores、MemoryWorkingSetBytes、RestartTimeEpoch、CpuUsageNanoCores、memoryRssBytes&#41;、CounterValue、TimeGenerated、CounterPath、及 sourcesystem | 
| 自訂計量 |`InsightsMetrics` | Computer、Name、Namespace、原點、及 sourcesystem、Tags<sup>1</sup>、TimeGenerated、Type、Va、_ResourceId | 

<sup>1</sup> *Tags*屬性代表對應度量的[多個維度](../platform/data-platform-metrics.md#multi-dimensional-metrics)。 如需 `InsightsMetrics` 資料表中所收集和儲存之計量的詳細資訊，以及記錄屬性的描述，請參閱[InsightsMetrics 總覽](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md)。

>[!NOTE]
>目前的 Prometheus 支援是公開預覽中的一項功能。
>

## <a name="search-logs-to-analyze-data"></a>搜尋記錄來分析資料

Azure 監視器記錄可協助您尋找趨勢、診斷瓶頸、預測或相互關聯資料，協助您判斷目前的叢集設定是否以最佳方式執行。 已為您提供可立即開始使用，或自訂以您想要的方式傳回資訊的預先定義記錄搜尋。

您可以在預覽窗格中選取 [檢視 Kubernetes 事件記錄] 或 [檢視容器記錄] 選項，以在工作區中對資料執行互動式分析。 [記錄搜尋] 頁面會出現在您所在 Azure 入口網站頁面的右邊。

![在 Log Analytics 中分析資料](./media/container-insights-analyze/container-health-log-search-example.png)   

轉送至您工作區的容器記錄輸出為 STDOUT 和 STDERR。 因為 Azure 監視器會監視 Azure 受控 Kubernetes (AKS)，所以，由於所產生的資料量很大，因此今天不會收集 Kube 系統。 

### <a name="example-log-search-queries"></a>範例記錄檔搜尋查詢

從一或兩個範例開始建置查詢，然後加以修改以滿足您的需求，通常很實用。 若要取得建置更進階查詢的協助，您可以試驗下列範例查詢：

| 查詢 | 描述 | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | 列出所有容器的生命週期資訊| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Kubernetes 事件|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | 映像清查 | 
| **選取折線圖顯示選項**：<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器 CPU | 
| **選取折線圖顯示選項**：<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器記憶體 |
| InsightsMetrics<br> &#124;其中 Name = = "requests_count"<br> &#124;摘要 Val = any （Val） by TimeGenerated = bin （TimeGenerated，1m）<br> &#124;排序依據 TimeGenerated asc<br> &#124;專案 RequestsPerMinute = Val-上一個（Val），TimeGenerated <br> &#124;轉譯 barchart  | 使用自訂計量的每分鐘要求數 |

下列範例是 Prometheus 計量查詢。 收集的計量會計算，而為了判斷在特定時段內發生多少錯誤，我們必須從計數中減去。 資料集會依*partitionKey*分割，這表示每一組唯一的*名稱*、*主機名稱*和*OperationType*，我們都會在該集合上執行子查詢，藉由*TimeGenerated*排序記錄，讓它能夠找出先前的*TimeGenerated*和記錄的時間計數，以判斷速率。

```
let data = InsightsMetrics 
| where Namespace contains 'prometheus' 
| where Name == 'kubelet_docker_operations' or Name == 'kubelet_docker_operations_errors'    
| extend Tags = todynamic(Tags) 
| extend OperationType = tostring(Tags['operation_type']), HostName = tostring(Tags.hostName) 
| extend partitionKey = strcat(HostName, '/' , Name, '/', OperationType) 
| partition by partitionKey ( 
    order by TimeGenerated asc 
    | extend PrevVal = prev(Val, 1), PrevTimeGenerated = prev(TimeGenerated, 1) 
    | extend Rate = iif(TimeGenerated == PrevTimeGenerated, 0.0, Val - PrevVal) 
    | where isnull(Rate) == false 
) 
| project TimeGenerated, Name, HostName, OperationType, Rate; 
let operationData = data 
| where Name == 'kubelet_docker_operations' 
| project-rename OperationCount = Rate; 
let errorData = data 
| where Name == 'kubelet_docker_operations_errors' 
| project-rename ErrorCount = Rate; 
operationData 
| join kind = inner ( errorData ) on TimeGenerated, HostName, OperationType 
| project-away TimeGenerated1, Name1, HostName1, OperationType1 
| extend SuccessPercentage = iif(OperationCount == 0, 1.0, 1 - (ErrorCount / OperationCount))
```

輸出會顯示類似下面的結果：

![資料內嵌磁片區的記錄查詢結果](./media/container-insights-log-search/log-query-example-prometheus-metrics.png)

## <a name="next-steps"></a>後續步驟

容器的 Azure 監視器不包含預先定義的一組警示。 請參閱[使用適用于容器的 Azure 監視器建立效能警示](container-insights-alerts.md)，以瞭解如何建立建議的高 CPU 和記憶體使用率警示，以支援您的 DevOps 或作業程式和程式。 
