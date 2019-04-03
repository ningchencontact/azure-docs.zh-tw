---
title: 在 Azure Service Fabric 叢集中使用 EventStore API 查詢叢集事件 | Microsoft Docs
description: 了解如何使用 Azure Service Fabric EventStore API 來查詢平台事件
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: facbcd6def7451ca83bdf00fe9b7c7cac2c74945
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879942"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>針對叢集事件查詢 EventStore API

本文內容涵蓋如何查詢於 Service Fabric 6.2 版和更新版本中提供的 EventStore API。如果您想要深入了解 EventStore 服務，請參閱 [EventStore 服務概觀](service-fabric-diagnostics-eventstore.md)。 目前，EventStore 服務只能存取過去 7 天 (這是以您叢集的診斷資料保留原則為基礎) 的資料。

>[!NOTE]
>EventStore API 從 Service Fabric 6.4 版起正式推出，僅適用於在 Azure 上執行的 Windows 叢集。

EventStore API 可以直接透過 REST 端點存取，或以程式設計方式存取。 視查詢而定，需要有幾個參數才能收集到正確資料。 這些參數通常包括：
* `api-version`： 您正在使用的 EventStore api 版本
* `StartTimeUtc`： 定義您有興趣查看期開始
* `EndTimeUtc`： 結束的時間週期

除了這些參數之外，還有幾個選擇性參數可用，例如：
* `timeout`： 覆寫預設值 60 秒逾時執行要求作業
* `eventstypesfilter`： 這可讓您篩選特定事件類型的選項
* `ExcludeAnalysisEvents`： 不會傳回 [分析] 事件。 根據預設，EventStore 查詢會在可能的情況下傳回「分析」事件。 分析事件為資訊較豐富的操作通道事件，其中包含比一般 Service Fabric 事件更廣泛的其他內容或資訊，並提供更深入的資訊。
* `SkipCorrelationLookup`： 執行不會尋找潛在的相互關聯事件，在叢集中。 根據預設，EventStore 將會嘗試在跨叢集事件之間建立關聯，並盡可能將您的事件連結在一起。 

您可以對叢集中的每個實體查詢事件。 您也可以針對某一類型的所有實體查詢事件。 例如，您可以查詢特定節點的事件，或叢集中所有節點的事件。 目前您可以查詢事件的實體集合為 (包含查詢的結構方式)：
* 叢集: `/EventsStore/Cluster/Events`
* 節點： `/EventsStore/Nodes/Events`
* 節點： `/EventsStore/Nodes/<NodeName>/$/Events`
* 應用程式： `/EventsStore/Applications/Events`
* 應用程式: `/EventsStore/Applications/<AppName>/$/Events`
* 服務： `/EventsStore/Services/Events`
* 服務： `/EventsStore/Services/<ServiceName>/$/Events`
* 分割區: `/EventsStore/Partitions/Events`
* 磁碟分割： `/EventsStore/Partitions/<PartitionID>/$/Events`
* 複本： `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* 複本： `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>參考應用程式或服務名稱時，查詢不需要包括 "fabric:/" 前置詞。 此外，如果您的應用程式或服務名稱包含 "/"，請將它變更為 "~"，查詢才能運作。 例如，如果您的應用程式顯示為 "fabric:/App1/FrontendApp"，應用程式特定查詢的結構就會是 `/EventsStore/Applications/App1~FrontendApp/$/Events`。
>此外，現今的服務健康情況報表會顯示在相對應的應用程式底下，因此您可以查詢正確應用程式實體的 `DeployedServiceHealthReportCreated` 事件。 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>透過 REST API 端點查詢 EventStore

您可以將 `GET` 要求設為 `<your cluster address>/EventsStore/<entity>/Events/`，以直接透過 REST 端點查詢 EventStore。

例如，若要查詢 `2018-04-03T18:00:00Z` 和 `2018-04-04T18:00:00Z` 之間的所有叢集事件，您的要求看起來會像這樣：

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

這可能不會傳回任何事件，或以 json 格式傳回事件清單：

```json
Response: 200
Body:
[
  {
    "Kind": "ClusterUpgradeStart",
    "CurrentClusterVersion": "0.0.0.0:",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeType": "Rolling",
    "RollingUpgradeMode": "UnmonitoredAuto",
    "FailureAction": "Manual",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:18:59.4313064Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(0 1 2)",
    "UpgradeDomainElapsedTimeInMs": "78.5288",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:19:59.5729953Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(3 4)",
    "UpgradeDomainElapsedTimeInMs": "0",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.6271949Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeComplete",
    "TargetClusterVersion": "6.2:1.0",
    "OverallUpgradeElapsedTimeInMs": "120196.5212",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.8134457Z",
    "HasCorrelatedEvents": false
  }
]
```

我們可以看到在 `2018-04-03T18:00:00Z` 和 `2018-04-04T18:00:00Z` 之間，此叢集在第一次啟動時便順利完成第一次升級：於 `"OverallUpgradeElapsedTimeInMs": "120196.5212"`的時間內，從 `"CurrentClusterVersion": "0.0.0.0:"` 升級為 `"TargetClusterVersion": "6.2:1.0"`。

## <a name="query-the-eventstore-programmatically"></a>以程式設計方式查詢 EventStore

您也能透過 [Service Fabric 用戶端程式庫](https://docs.microsoft.com/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library)，以程式設計方式查詢 EventStore。

一旦您擁有您服務的網狀架構用戶端設定，您可以藉由存取這類 EventStore 查詢事件：
`sfhttpClient.EventStore.<request>`

以下是一個透過 `GetClusterEventListAsync` 函式，要求 `2018-04-03T18:00:00Z` 和 `2018-04-04T18:00:00Z` 之間所有叢集事件的範例要求。

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

以下是另一個範例，該範例會查詢叢集健康情況以及 2018 年 9 月的所有節點事件，並將其列印出來。

```csharp
  const int timeoutSecs = 60;
  var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
  var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

  var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
  Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());

  
  Console.WriteLine("Querying for node events...");
  var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
      "2018-09-01T00:00:00Z",
      "2018-09-30T23:59:59Z",
      timeoutSecs,
      "NodeDown,NodeUp")
      .GetAwaiter()
      .GetResult()
      .ToList();
  Console.WriteLine("Result Count: {0}", nodesEvents.Count());

  foreach (var nodeEvent in nodesEvents)
  {
      Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
      if (nodeEvent is NodeDownEvent)
      {
          var nodeDownEvent = nodeEvent as NodeDownEvent;
          Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
      }
      else if (nodeEvent is NodeUpEvent)
      {
          var nodeUpEvent = nodeEvent as NodeUpEvent;
          Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
      }
  }
```

## <a name="sample-scenarios-and-queries"></a>範例案例和查詢

以下是幾個範例，說明您可以如何呼叫 Event Store REST API 以了解叢集狀態。

*叢集升級：*

若要查看已成功建立。 您的叢集，或嘗試升級過去一週的最後一個時間，您可以查詢到您的叢集，最近完成的升級 Api 查詢 EventStore"ClusterUpgradeCompleted 」 事件：
`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*叢集升級的問題：*

同樣地，如果最近的叢集升級出現問題，您可以查詢叢集實體的所有事件。 您將會看到各種不同事件，包括升級的起始和升級成功完成的每個 UD。 您也將會看到復原開始時間點的事件和相對應的健康情況事件。 以下是您會使用這個查詢：
`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*節點狀態變更：*

若要查看您的節點狀態變更過去幾天-當節點已增加或減少，或已啟用或停用 （由平台，而 「 混亂 」 服務，或從使用者輸入）-會使用下列查詢：
`https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*應用程式事件：*

您也可以追蹤最近的應用程式部署和升級。 您可以使用下列查詢來查看在叢集中的所有應用程式事件：
`https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*應用程式的健康狀態歷程記錄：*

除了單純查看應用程式生命週期事件之外，您也可以查看特定應用程式之健康情況的歷程記錄資料。 您可以透過指定要收集其資料的應用程式名稱來達到此目的。 使用此查詢來取得所有應用程式健康情況事件：`https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport`. 如果您想要包括可能已經過期 (超出其存留時間 (TTL)) 的健康狀態事件，請將 `,ApplicationHealthReportExpired` 新增至查詢的結尾處，以篩選兩種類型的事件。

*歷程記錄中"myApp"的所有服務的健全狀況：*

目前，服務的健康情況報表事件會在相對應應用程式實體底下顯示為 `DeployedServicePackageNewHealthReport` 事件。 若要查看您的服務方式執行 「 app1"，使用下列查詢：
`https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*分割區重新設定：*

若要查看您叢集中所發生的所有分割區移動，請查詢 `PartitionReconfigured` 事件。 這可以協助您在叢集中診斷問題時，找出有哪些工作負載於哪些特定時間在哪一個節點上執行。 以下是範例查詢是這樣的：
`https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*混亂服務：*

有一個能在混沌服務啟動或停止時，於叢集層級公開的事件。 若要查看您最近使用的混亂服務，請使用下列查詢：
`https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`

