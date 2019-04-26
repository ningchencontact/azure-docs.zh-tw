---
title: 如何查詢記錄從 Azure 監視器的容器 |Microsoft Docs
description: 適用於容器的 azure 監視器所收集的計量和記錄資料，而這篇文章描述的記錄，並包含範例查詢。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: 66fc55d8c3dbb8487d1e796d5f30b08a94f717f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60494760"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>如何查詢 Azure 監視器中適用於容器的記錄檔
適用於容器的 azure 監視器會從容器主機和容器，收集效能計量、 清查資料，以及健全狀況狀態資訊，並將它轉送至 Log Analytics 工作區，在 Azure 監視器中。 每隔三分鐘會收集一次資料。 這項資料可供[查詢](../../azure-monitor/log-query/log-query-overview.md)Azure 監視器中。 您可以將此資料套用至各種案例，包括移轉規劃、容量分析、探索和隨選效能疑難排解。

## <a name="container-records"></a>容器資料列

下表顯示適用於容器的 Azure 監視器所收集的記錄範例，以及記錄搜尋結果中所顯示的資料類型：

| 資料類型 | 記錄檔搜尋中的資料類型 | 欄位 |
| --- | --- | --- |
| 主機和容器的效能 | `Perf` | Computer、ObjectName、CounterName &#40;%Processor Time、Disk Reads MB、Disk Writes MB、Memory Usage MB、Network Receive Bytes、Network Send Bytes、Processor Usage sec、Network&#41;、CounterValue、TimeGenerated、CounterPath、SourceSystem |
| 容器清查 | `ContainerInventory` | TimeGenerated、Computer、container name、ContainerHostname、Image、ImageTag、ContainerState、ExitCode、EnvironmentVar、Command、CreatedTime、StartedTime、FinishedTime、SourceSystem、ContainerID、ImageID |
| 容器記錄檔 | `ContainerLog` | TimeGenerated、Computer、image ID、container name、LogEntrySource、LogEntry、SourceSystem、ContainerID |
| 容器節點清查 | `ContainerNodeInventory`| TimeGenerated、Computer、ClassName_s、DockerVersion_s、OperatingSystem_s、Volume_s、Network_s、NodeRole_s、OrchestratorType_s、InstanceID_g、SourceSystem|
| 清查 Kubernetes 叢集中的 Pod | `KubePodInventory` | TimeGenerated、 Computer、 ClusterId、 ContainerCreationTimeStamp、 PodUid、 PodCreationTimeStamp、 ContainerRestartCount、 PodRestartCount、 PodStartTime、 ContainerStartTime、 ServiceName、 ControllerKind、 ControllerName、 ContainerStatus， ContainerStatusReason、 ContainerID、 容器名稱、 名稱、 PodLabel、 命名空間、 PodStatus、 ClusterName、 PodIp、 SourceSystem |
| 清查 Kubernetes 叢集中的節點部分 | `KubeNodeInventory` | TimeGenerated、Computer、ClusterName、ClusterId、LastTransitionTimeReady、Labels、Status、KubeletVersion、KubeProxyVersion、CreationTimeStamp、SourceSystem | 
| Kubernetes 事件 | `KubeEvents` | TimeGenerated、Computer、ClusterId_s、FirstSeen_t、LastSeen_t、Count_d、ObjectKind_s、Namespace_s、Name_s、Reason_s、Type_s、TimeGenerated_s、SourceComponent_s、ClusterName_s、Message、SourceSystem | 
| Kubernetes 叢集中的服務 | `KubeServices` | TimeGenerated、ServiceName_s、Namespace_s、SelectorLabels_s、ClusterId_s、ClusterName_s、ClusterIP_s、ServiceType_s、SourceSystem | 
| Kubernetes 叢集節點部分的效能計量 | Perf &#124; where ObjectName == “K8SNode” | 電腦、 ObjectName、 CounterName &#40;cpuAllocatableBytes，memoryAllocatableBytes，cpuCapacityNanoCores，memoryCapacityBytes，memoryRssBytes，cpuUsageNanoCores，memoryWorkingsetBytes，restartTimeEpoch&#41;，CounterValue，TimeGenerated、 CounterPath、 SourceSystem | 
| Kubernetes 叢集容器部分的效能計量 | Perf &#124; where ObjectName == “K8SContainer” | CounterName &#40; cpuRequestNanoCores，memoryRequestBytes，cpuLimitNanoCores，memoryWorkingSetBytes，restartTimeEpoch，cpuUsageNanoCores，memoryRssBytes&#41;，CounterValue、 TimeGenerated、 CounterPath、 SourceSystem | 
| InsightsMetrics | 電腦名稱、 命名空間、 來源、 SourceSystem、 標記、 TimeGenerated、 型別，值 |

## <a name="search-logs-to-analyze-data"></a>搜尋記錄來分析資料
Azure 監視器記錄檔可協助您尋找的趨勢、 診斷瓶頸，預測，或相互關聯的資料，可協助您判斷目前的叢集設定是否以最佳方式執行。 已為您提供可立即開始使用，或自訂以您想要的方式傳回資訊的預先定義記錄搜尋。 

您可以在預覽窗格中選取 [檢視 Kubernetes 事件記錄] 或 [檢視容器記錄] 選項，以在工作區中對資料執行互動式分析。 [記錄搜尋] 頁面會出現在您所在 Azure 入口網站頁面的右邊。

![在 Log Analytics 中分析資料](./media/container-insights-analyze/container-health-log-search-example.png)   

容器記錄檔輸出轉送至您的工作區是 STDOUT 和 STDERR。 因為 Azure 監視器會監視 Azure 受控 Kubernetes (AKS)，所以，由於所產生的資料量很大，因此今天不會收集 Kube 系統。 

### <a name="example-log-search-queries"></a>範例記錄檔搜尋查詢
從一或兩個範例開始建置查詢，然後加以修改以滿足您的需求，通常很實用。 若要取得建置更進階查詢的協助，您可以試驗下列範例查詢：

| 查詢 | 描述 | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | 列出所有容器的生命週期資訊| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Kubernetes 事件|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | 映像清查 | 
| **選取折線圖顯示選項**：<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器 CPU | 
| **選取折線圖顯示選項**：<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器記憶體 |

## <a name="next-steps"></a>後續步驟
適用於容器的 azure 監視器不包含一組預先定義的警示。 檢閱[建立容器與 Azure 監視器的效能警示](container-insights-alerts.md)以了解如何建立建議的警示，以支援您的 DevOps 或作業程序和程序的高 CPU 和記憶體使用率。 