---
title: 設定容器代理程式資料集合的 Azure 監視器 |Microsoft Docs
description: 本文說明如何設定容器代理程式的 Azure 監視器，以控制 stdout/stderr 和環境變數記錄檔收集。
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
ms.date: 10/08/2019
ms.author: magoedte
ms.openlocfilehash: dfa823955cccba4ac7ec6859894a4562f0810d76
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248763"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>為容器的 Azure 監視器設定代理程式資料收集

Azure 監視器容器會從容器化的代理程式，收集從部署至裝載于 Azure Kubernetes Service （AKS）之受控 Kubernetes 叢集的 stdout、stderr 和環境變數。 此代理程式也可以使用容器化代理程式，從 Prometheus 收集時間序列資料（也稱為計量），而不需要設定及管理 Prometheus 伺服器和資料庫。 您可以藉由建立自訂的 Kubernetes ConfigMaps 來控制此體驗，以設定代理程式資料收集設定。 

本文示範如何根據您的需求建立 ConfigMap 和設定資料收集。

>[!NOTE]
>目前的 Prometheus 支援是公開預覽中的一項功能。
>

## <a name="configmap-file-settings-overview"></a>ConfigMap 檔案設定總覽

提供範本 ConfigMap 檔，可讓您使用自訂輕鬆編輯它，而不需要從頭建立。 開始之前，您應該先參閱有關[ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)的 Kubernetes 檔，並熟悉如何建立、設定及部署 ConfigMaps。 這可讓您篩選每個命名空間或整個叢集中的 stderr 和 stdout，以及在叢集中所有 pod/節點上執行之任何容器的環境變數。

>[!IMPORTANT]
>從容器工作負載收集 stdout、stderr 和環境變數所支援的最低代理程式版本是 ciprod06142019 或更新版本。 抓取 Prometheus 計量支援的最低代理程式版本為 ciprod07092019 或更新版本。 若要驗證您的代理程式版本，請在 [**節點**] 索引標籤中選取節點，然後在 [屬性] 窗格的 [**代理程式映射**標籤] 屬性值。  

### <a name="data-collection-settings"></a>資料收集設定

以下是可設定以控制資料收集的設定。

|Key |資料類型 |值 |描述 |
|----|----------|------|------------|
|`schema-version` |字串（區分大小寫） |v1 |這是在剖析此 ConfigMap 時，代理程式所使用的架構版本。 目前支援的架構版本為 v1。 不支援修改此值，而且在評估 ConfigMap 時將會遭到拒絕。|
|`config-version` |字串 | | 支援在您的原始檔控制系統/存放庫中追蹤此設定檔版本的功能。 允許的字元數上限為10，而所有其他字元則會被截斷。 |
|`[log_collection_settings.stdout] enabled =` |Boolean | True 或 False | 這會控制是否啟用 stdout 容器記錄檔收集。 設定為時 `true`，而且不會針對 stdout 記錄收集（以下的 `log_collection_settings.stdout.exclude_namespaces` 設定）排除任何命名空間，將會從叢集中所有 pod/節點的所有容器收集 stdout 記錄檔。 如果在 ConfigMaps 中未指定，則預設值為 `enabled = true`。 |
|`[log_collection_settings.stdout] exclude_namespaces =`|字串 | 以逗號分隔的陣列 |將不會收集 stdout 記錄的 Kubernetes 命名空間陣列。 只有當 `log_collection_settings.stdout.enabled` 設定為 `true` 時，此設定才會生效。 如果在 ConfigMap 中未指定，則預設值為 `exclude_namespaces = ["kube-system"]`。|
|`[log_collection_settings.stderr] enabled =` |Boolean | True 或 False |這會控制是否啟用 stderr 容器記錄檔收集。 當設定為 `true`，而且不會針對 stdout 記錄收集（`log_collection_settings.stderr.exclude_namespaces` 設定）排除任何命名空間時，會從叢集中所有 pod/節點的所有容器中收集 stderr 記錄。 如果在 ConfigMaps 中未指定，則預設值為 `enabled = true`。 |
|`[log_collection_settings.stderr] exclude_namespaces =` |字串 |以逗號分隔的陣列 |將不會收集 stderr 記錄的 Kubernetes 命名空間陣列。 只有當 `log_collection_settings.stdout.enabled` 設定為 `true` 時，此設定才會生效。 如果在 ConfigMap 中未指定，則預設值為 `exclude_namespaces = ["kube-system"]`。 |
| `[log_collection_settings.env_var] enabled =` |Boolean | True 或 False | 此設定可控制叢集中所有 pod/節點間的環境變數集合，並在 ConfigMaps 中未指定時預設為 `enabled = true`。 如果環境變數的集合是全域啟用的，您可以將環境變數 `AZMON_COLLECT_ENV` 設定為**False** ，或使用 Dockerfile 設定或在[Pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) **的設定檔中，將其停用於特定容器env：** 區段。 如果環境變數的集合已全域停用，則您無法啟用特定容器的集合（也就是可在容器層級套用的唯一覆寫，就是停用已全域啟用的集合）。 |

### <a name="prometheus-scraping-settings"></a>Prometheus 抓取設定

![適用于 Prometheus 的容器監視架構](./media/container-insights-agent-config/monitoring-kubernetes-architecture.png)

適用于容器的 Azure 監視器提供順暢的體驗，讓多個抓取透過下列各項機制來收集 Prometheus 計量，如下表所示。 系統會透過單一 ConfigMap 檔案中指定的一組設定來收集計量，這是用來設定從容器工作負載收集 stdout、stderr 和環境變數的相同檔案。 

來自 Prometheus 之計量的作用中抓取是從兩個觀點的其中一個來執行：

* 整個叢集的 HTTP URL，並從服務的已列出端點探索目標、k8s 服務（例如 kube）、kube 狀態度量，以及應用程式特定的 pod 附注。 在此內容中收集的計量會定義于 ConfigMap 區段 *[Prometheus data_collection_settings]* 中。
* 整個節點-HTTP URL，並從服務的列出端點探索目標。 在此內容中收集的計量會定義于 ConfigMap 區段 *[Prometheus_data_collection_settings]* 中。

| 端點 | `Scope` | 範例 |
|----------|-------|---------|
| Pod 注釋 | 全叢集 | 備註 <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes 服務 | 全叢集 | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| url/端點 | 每個節點和/或全叢集的叢集 | `http://myurl:9101/metrics` |

指定 URL 時，容器的 Azure 監視器只會抓取端點。 當指定 Kubernetes 服務時，會使用叢集 DNS 伺服器解析服務名稱以取得 IP 位址，然後剪輯解析的服務。

|`Scope` | Key | 資料類型 | 值 | 描述 |
|------|-----|-----------|-------|-------------|
| 全叢集 | | | | 指定下列三種方法中的任何一種，以抓取度量的端點。 |
| | `urls` | 字串 | 以逗號分隔的陣列 | HTTP 端點（IP 位址或指定的有效 URL 路徑）。 例如： `urls=[$NODE_IP/metrics]` 。 （$NODE _IP 是容器參數的特定 Azure 監視器，可以用來取代節點 IP 位址。 必須全部大寫）。 |
| | `kubernetes_services` | 字串 | 以逗號分隔的陣列 | Kubernetes 服務的陣列，可從 kube 狀態計量抓取計量。 例如，`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`。|
| | `monitor_kubernetes_pods` | Boolean | True 或 False | 當設定為 [全叢集] 設定中的 [`true`] 時，容器代理程式的 Azure 監視器會針對下列 Prometheus 注釋，在整個叢集中抓取 Kubernetes pod：<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolean | True 或 False | 啟用 pod 的抓取。 `monitor_kubernetes_pods` 必須設為 `true`。 |
| | `prometheus.io/scheme` | 字串 | http 或 https | 預設為透過 HTTP 的 scrapping。 如有必要，請將設定為 `https`。 | 
| | `prometheus.io/path` | 字串 | 以逗號分隔的陣列 | 從中提取計量的來源 HTTP 資源路徑。 如果計量路徑不 `/metrics`，請使用此注釋加以定義。 |
| | `prometheus.io/port` | 字串 | 9102 | 指定要抓取的埠。 如果未設定埠，則會預設為9102。 |
| 全節點 | `urls` | 字串 | 以逗號分隔的陣列 | HTTP 端點（IP 位址或指定的有效 URL 路徑）。 例如： `urls=[$NODE_IP/metrics]` 。 （$NODE _IP 是容器參數的特定 Azure 監視器，可以用來取代節點 IP 位址。 必須全部大寫）。 |
| 全節點或全叢集 | `interval` | 字串 | 60s | 收集間隔的預設值為一分鐘（60秒）。 您可以將 *[prometheus_data_collection_settings]* 和/或 *[prometheus_data_collection_settings]* 的集合修改為時間單位，例如 s、m、h。 |
| 全節點或全叢集 | `fieldpass`<br> `fielddrop`| 字串 | 以逗號分隔的陣列 | 您可以藉由設定 [允許（`fieldpass`）] 和 [不允許] （`fielddrop`）清單，指定要收集的特定計量。 您必須先設定允許清單。 |

ConfigMaps 是全域清單，而且只能有一個 ConfigMap 套用至代理程式。 您不能有另一個 ConfigMaps overruling 集合。

## <a name="configure-and-deploy-configmaps"></a>設定和部署 ConfigMaps

請執行下列步驟來設定您的 ConfigMap 設定檔，並將其部署到您的叢集。

1. [下載](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml)範本 ConfigMap yaml 檔，並將它儲存為 azm-ms-agentconfig. yaml。  

2. 編輯 ConfigMap yaml 檔與您的自訂專案，以收集 stdout、stderr 和/或環境變數。

    - 若要排除 stdout 記錄檔收集的特定命名空間，您可以使用下列範例來設定索引鍵/值： `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`。
    
    - 若要停用特定容器的環境變數集合，請將索引鍵/值設定 `[log_collection_settings.env_var] enabled = true`，以全域啟用變數集合，然後依照[這裡](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container)的步驟完成特定容器的設定。
    
    - 若要停用 stderr 記錄收集整個叢集，請使用下列範例來設定索引鍵/值： `[log_collection_settings.stderr] enabled = false`。
    
3. 若要設定整個叢集的 Kubernetes 服務集合，請使用下列範例來設定 ConfigMap 檔案。

    ```
    prometheus-data-collection-settings: |- 
    # Custom Prometheus metrics data collection settings
    [prometheus_data_collection_settings.cluster] 
    interval = "1m"  ## Valid time units are s, m, h.
    fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
    fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
    kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
    ```

4. 若要設定從整個叢集的特定 URL 抓取 Prometheus 計量，請使用下列範例來設定 ConfigMap 檔案。

    ```
    prometheus-data-collection-settings: |- 
    # Custom Prometheus metrics data collection settings
    [prometheus_data_collection_settings.cluster] 
    interval = "1m"  ## Valid time units are s, m, h.
    fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
    fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
    urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
    ```

5. 若要從代理程式的 DaemonSet 針對叢集中的每個個別節點設定 Prometheus 計量的抓取，請在 ConfigMap 中設定下列各項：
    
    ```
    prometheus-data-collection-settings: |- 
    # Custom Prometheus metrics data collection settings 
    [prometheus_data_collection_settings.node] 
    interval = "1m"  ## Valid time units are s, m, h. 
    urls = ["http://$NODE_IP:9103/metrics"] 
    fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
    fielddrop = ["metric_to_drop"] 
    ```

    >[!NOTE]
    >$NODE _IP 是容器參數的特定 Azure 監視器，可以用來取代節點 IP 位址。 必須全部大寫。 

6. 若要藉由指定 pod 注釋來設定 Prometheus 計量的抓取，請執行下列步驟：

    1. 在 ConfigMap 中，指定下列各項：

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings
         [prometheus_data_collection_settings.cluster] 
         interval = "1m"  ## Valid time units are s, m, h
         monitor_kubernetes_pods = true 
        ```

    2. 指定 pod 注釋的下列設定：

        ```
         - prometheus.io/scrape:"true" #Enable scraping for this pod 
         - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
         - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
         - prometheus.io/port:"8000" #If port is not 9102 use this annotation
        ```

7. 執行下列 kubectl 命令來建立 ConfigMap： `kubectl apply -f <configmap_yaml_file.yaml>`。
    
    範例： `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    設定變更可能需要幾分鐘的時間才會生效，且叢集中的所有 omsagent pod 都會重新開機。 重新開機是所有 omsagent pod 的輪流重新開機，不會同時全部重新開機。 當重新開機完成時，會顯示與下列類似的訊息，並包含結果： `configmap "container-azm-ms-agentconfig" created`。

若要確認已成功套用設定，請使用下列命令來檢查代理程式 pod 的記錄： `kubectl logs omsagent-fdf58 -n=kube-system`。 如果 omsagent pod 有設定錯誤，輸出將會顯示類似下列的錯誤：

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

與套用 Prometheus 設定變更相關的錯誤也可供審查。  從代理程式 pod 的記錄，使用相同的 `kubectl logs` 命令，或從即時記錄。 即時記錄會顯示類似下列的錯誤：

```
2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
```

錯誤會使 omsagent 無法剖析檔案，因而導致它重新開機並使用預設設定。 更正 ConfigMap 中的錯誤之後，請儲存 yaml 檔案，並執行下列命令來套用更新的 ConfigMaps： `kubectl apply -f <configmap_yaml_file.yaml`。

## <a name="applying-updated-configmap"></a>套用更新的 ConfigMap

如果您已將 ConfigMap 部署至叢集，而且想要以較新的設定更新它，您可以編輯先前使用的 ConfigMap 檔案，然後使用與之前相同的命令來套用，`kubectl apply -f <configmap_yaml_file.yaml`。

設定變更可能需要幾分鐘的時間才會生效，且叢集中的所有 omsagent pod 都會重新開機。 重新開機是所有 omsagent pod 的輪流重新開機，不會同時全部重新開機。 當重新開機完成時，會顯示與下列類似的訊息，並包含結果： `configmap "container-azm-ms-agentconfig" updated`。

## <a name="verifying-schema-version"></a>正在驗證架構版本

支援的設定架構版本可在 omsagent pod 上以 pod 注釋（架構版本）的形式提供。 您可以使用下列 kubectl 命令來查看它們： `kubectl describe pod omsagent-fdf58 -n=kube-system`

輸出會顯示類似下列的注釋架構版本：

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="query-prometheus-metrics-data"></a>查詢 Prometheus 計量資料

若要依 Azure 監視器來查看剪輯的 prometheus 計量，請指定 "prometheus" 作為命名空間。 以下是從 `default` kubernetes 命名空間中查看 prometheus 計量的範例查詢。

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Prometheus 資料也可以依名稱直接查詢。

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

## <a name="review-prometheus-data-usage"></a>審查 Prometheus 資料使用量

若要識別每日計量大小的內嵌磁片區（GB）以瞭解其是否為高，請提供下列查詢。

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
輸出會顯示類似下面的結果：

![資料內嵌磁片區的記錄查詢結果](./media/container-insights-agent-config/log-query-example-usage-03.png)

若要預估每個計量大小（GB）為一個月，以瞭解工作區中所接收的資料內嵌量是否過高，會提供下列查詢。

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

輸出會顯示類似下面的結果：

![資料內嵌磁片區的記錄查詢結果](./media/container-insights-agent-config/log-query-example-usage-02.png)

如需有關如何監視資料使用量和分析成本的進一步資訊，請前往使用[Azure 監視器記錄來管理使用量和成本](../platform/manage-cost-storage.md)。

## <a name="next-steps"></a>後續步驟

容器的 Azure 監視器不包含預先定義的一組警示。 請參閱[使用適用于容器的 Azure 監視器建立效能警示](container-insights-alerts.md)，以瞭解如何建立建議的高 CPU 和記憶體使用率警示，以支援您的 DevOps 或作業程式和程式。

- 若要繼續了解如何使用 Azure 監視器並監視您 AKS 叢集的其他層面，請參閱[檢視 Azure Kubernetes 服務健康情況](container-insights-analyze.md)。

- 查看[記錄查詢範例](container-insights-log-search.md#search-logs-to-analyze-data)，以查看預先定義的查詢和範例，以評估或自訂警示、視覺化或分析您的叢集。
