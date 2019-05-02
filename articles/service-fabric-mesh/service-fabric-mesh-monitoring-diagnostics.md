---
title: Azure Service Fabric Mesh 應用程式的監視和診斷 | Microsoft Docs
description: 了解如何在 Azure 上監視和診斷 Service Fabric Mesh 應用程式。
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 36c9a5d75c4a72365638619ab85d451df647feb3
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939830"
---
# <a name="monitoring-and-diagnostics"></a>監視和診斷
Azure Service Fabric Mesh 是一個受到完整管理的服務，讓開發人員能夠部署微服務應用程式，而不需管理虛擬機器、儲存體或網路功能。 Service Fabric Mesh 的監視和診斷可歸類為三種主要診斷資料類型：

- 應用程式記錄 - 根據您檢測應用程式的方式，這些會定義為來自您的容器化應用程式的記錄 (例如 Docker 記錄)
- 平台事件 - 來自 Mesh 平台並與您的容器作業相關的事件，目前包括容器啟用、停用及終止。
- 容器計量 - 您的容器適用的資源使用率和效能計量 (Docker 統計資料)

本文討論最新預覽版本可用的監視和診斷選項。

## <a name="application-logs"></a>應用程式記錄

您可以從您已部署的容器，依照容器檢視 Docker 記錄。 在 Service Fabric Mesh 應用程式模型中，每個容器都是您應用程式中的程式碼封裝。 若要查看相關聯的記錄與程式碼封裝，請使用下列命令：

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> 您可以使用 "az mesh service-replica" 命令來取得複本名稱。 複本名稱會遞增 0 的整數。

從投票應用程式的 VotingWeb.Code 容器查看記錄時，其外觀如下所示：

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>容器計量 

網狀結構環境會顯示少數幾個度量指出您的容器執行的方式。 下列計量是透過 Azure 入口網站和 Azure 監視 CLI:

| 計量 | 描述 | Units|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu 百分比 | % |
| MemoryUtilization | ActualMem/AllocatedMem 百分比 | % |
| AllocatedCpu | 根據 Azure Resource Manager 範本配置的 Cpu | Millicores |
| AllocatedMemory | 根據 Azure Resource Manager 範本配置的記憶體 | MB |
| ActualCpu | CPU 使用率 | Millicores |
| ActualMemory | 記憶體使用量 | MB |
| ContainerStatus | 0-無效：容器狀態為不明 <br> 1-擱置中：若要啟動已排程的容器 <br> 2-啟動：容器是進行啟動程序 <br> 3-啟動：已成功啟動容器 <br> 4-停止：正在停止容器 <br> 5-停止時間：已成功停止容器 | N/A |
| ApplicationStatus | 0-未知：狀態不是可擷取 <br> 1-準備開始：已成功執行應用程式 <br> 2-將升級：沒有正在進行升級 <br> 3-建立：正在建立應用程式 <br> 4-刪除：正在刪除應用程式 <br> 5-失敗：無法部署應用程式 | N/A |
| ServiceStatus | 0-無效：服務目前沒有健全狀況狀態 <br> 1-[確定]:服務狀況良好  <br> 2-警告：可能有錯誤需要調查 <br> 3-錯誤：發生問題，需要調查 <br> 4-未知：狀態不是可擷取 | N/A |
| ServiceReplicaStatus | 0-無效：複本目前沒有健全狀況狀態 <br> 1-[確定]:服務狀況良好  <br> 2-警告：可能有錯誤需要調查 <br> 3-錯誤：發生問題，需要調查 <br> 4-未知：狀態不是可擷取 | N/A | 
| RestartCount | 容器重新啟動的次數 | N/A |

> [!NOTE]
> ServiceStatus 和 ServiceReplicaStatus 值是相同[HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) Service Fabric 中。 

每個計量是位於不同的維度，以便您可以看到不同層級的彙總。 目前維度的清單如下所示：

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> CodePackageName 維度不是適用於 Linux 的應用程式。 

每個維度對應於不同元件[Service Fabric 應用程式模型](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Azure 監視器 CLI

命令的完整清單位於[Azure 監視器 CLI 文件](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)但我們已包含下列幾個實用範例 

在每個範例中，資源識別碼會遵循此模式

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* CPU 使用率的應用程式中的容器

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* 針對每個服務複本的記憶體使用量
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* 重新啟動在 1 小時 視窗中的每個容器。 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* 在服務之間的平均 CPU 使用率名為"VotingWeb 」 在 1 小時 視窗
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>計量瀏覽器

計量瀏覽器是在入口網站中您可以在此視覺化您的 Mesh 應用程式的所有計量刀鋒視窗。 在入口網站和 Azure 監視器刀鋒視窗中，後者都可用來檢視所有 Azure 資源支援 Azure 監視器計量中的應用程式的頁面存取此刀鋒視窗。 

![計量瀏覽器](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>後續步驟
* 若要深入了解 Service Fabric Mesh，請閱讀 [Service Fabric Mesh 概觀](service-fabric-mesh-overview.md)。
* 若要深入了解 Azure 監視器計量命令，請參閱[Azure 監視器 CLI 文件](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)。
