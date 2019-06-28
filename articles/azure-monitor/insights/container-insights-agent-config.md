---
title: 設定 Azure 監視器容器代理程式資料收集 |Microsoft Docs
description: 本文說明如何您也可以設定 Azure 監視器容器代理程式，來控制 stdout/stderr 和環境變數的記錄檔集合。
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
ms.date: 06/22/2019
ms.author: magoedte
ms.openlocfilehash: 1e7506e311c38d87371dd1b65440b6fb41a7ce78
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341652"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>設定 Azure 監視器 」 的容器中的代理程式資料收集

適用於容器的 azure 監視器會收集 stdout、 stderr 以及環境變數從容器工作負載部署到受管理的容器化的代理程式從裝載在 Azure Kubernetes Service (AKS) 中的 Kubernetes 叢集。 您可以建立自訂來控制這項體驗的 Kubernetes ConfigMaps 設定代理程式資料收集設定。 這篇文章會示範如何建立 configmap 掛接並設定您的需求為基礎的資料收集。

## <a name="configure-your-cluster-with-custom-data-collection-settings"></a>使用自訂資料集合設定來設定您的叢集

提供可讓您輕鬆地加以編輯您的自訂設定而不必從頭開始建立範本 configmap 掛接檔案。 開始之前，您應該檢閱 Kubernetes 文件的相關[ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)並熟悉如何建立、 設定和部署 ConfigMaps。 這可讓您篩選 stderr 和 stdout，每個命名空間，或跨整個叢集，並跨所有 pod/節點叢集中執行任何容器的環境變數。

>[!IMPORTANT]
>這項功能支援的代理程式最低版本是 microsoft / oms:ciprod06142019 或更新版本。 

### <a name="overview-of-configurable-data-collection-settings"></a>可設定的資料集合設定概觀

以下是可以控制資料收集設定的設定。

|Key |数据类型 |Value |描述 |
|----|----------|------|------------|
|`schema-version` |字串 （區分大小寫） |v1 |這是剖析此 configmap 掛接時，代理程式所使用的結構描述版本。 目前支援的結構描述版本是 v1。 修改這個值不支援，以及評估 configmap 掛接時將會遭到拒絕。|
|`config-version` |字串 | | 能夠追蹤的來源控制系統/存放庫中的這個組態檔的版本。 允許的字元數上限為 10，和所有其他字元會被截斷。 |
|`[log_collection_settings.stdout] enabled =` |Boolean | True 或 False | 這會控制如果已啟用 stdout 容器記錄檔收集。 當設定為`true`以及任何命名空間會排除 stdout 記錄檔集合 (`log_collection_settings.stdout.exclude_namespaces`以下的設定)，將 pod/叢集中所有節點會跨所有容器收集 stdout 記錄檔。 如果 ConfigMaps 中未指定，預設值是`enabled = true`。 |
|`[log_collection_settings.stdout] exclude_namespaces =`|字串 | 以逗號分隔的陣列 |Kubernetes 命名空間的 stdout 記錄檔將不會收集陣列。 此設定才會生效才`log_collection_settings.stdout.enabled`設為`true`。 如果 configmap 掛接中未指定，預設值是`exclude_namespaces = ["kube-system"]`。|
|`[log_collection_settings.stderr] enabled =` |Boolean | True 或 False |這會控制如果已啟用 stderr 容器記錄檔收集。 當設定為`true`和 stdout 記錄檔集合會排除任何命名空間 (`log_collection_settings.stderr.exclude_namespaces`設定)，所有容器會跨 pod/叢集中所有節點收集 stderr 記錄檔。 如果 ConfigMaps 中未指定，預設值是`enabled = true`。 |
|`[log_collection_settings.stderr] exclude_namespaces =` |字串 |以逗號分隔的陣列 |Kubernetes 命名空間的無 stderr 記錄檔將不會收集陣列。 此設定才會生效才`log_collection_settings.stdout.enabled`設為`true`。 如果 configmap 掛接中未指定，預設值是`exclude_namespaces = ["kube-system"]`。 |
| `[log_collection_settings.env_var] enabled =` |Boolean | True 或 False | 這會控制如果已啟用環境變數集合。 當設定為`false`，跨所有 pod/節點叢集中執行任何容器將會收集任何環境變數。 如果 configmap 掛接中未指定，預設值是`enabled = true`。 |

### <a name="configure-and-deploy-configmaps"></a>設定及部署 ConfigMaps

執行下列步驟來設定，並將 configmap 掛接組態檔部署到您的叢集。

1. [下載](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml)範本 ConfigMap yaml 檔案，並將它儲存為容器 azm-ms agentconfig.yaml。  
1. 編輯 ConfigMap yaml 檔案，以您的自訂。 

    - 若要排除特定的命名空間，stdout 記錄檔集合，您可以設定使用下列範例的索引鍵/值： `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`。
    - 若要停用特定容器的環境變數集合，請設定 索引鍵/值`[log_collection_settings.env_var] enabled = true`全域，啟用變數的集合，然後遵循步驟來[這裡](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container)以完成設定特定的容器。
    - 若要停用 stderr 記錄檔收集整個叢集，您可以設定使用下列範例的索引鍵/值： `[log_collection_settings.stderr] enabled = false`。

1. 執行下列的 kubectl 命令以建立 configmap 掛接： `kubectl apply -f <configmap_yaml_file.yaml>`。
    
    範例： `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    組態變更可能需要幾分鐘才能完成才會生效，並在叢集中的所有 oms 代理程式 pod 會重新啟動。 重新啟動所有的 oms 代理程式 pod 輪流重新啟動後，不是全部同時重新啟動。 在重新啟動完成時，會顯示訊息，如下所示，而且包含結果： `configmap "container-azm-ms-agentconfig" created`。

若要確認已成功套用設定，請使用下列命令以檢閱代理程式 pod 的記錄檔： `kubectl logs omsagent-fdf58 -n=kube-system`。 如果有組態錯誤從 osmagent pod，輸出會顯示錯誤如下所示：

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

錯誤會導致無法剖析該檔案，使其重新啟動和使用預設組態的 oms 代理程式。 修正在 configmap 掛接錯誤之後，儲存 yaml 檔案，然後執行命令來套用更新的 ConfigMaps: `kubectl apply -f <configmap_yaml_file.yaml`。

## <a name="applying-updated-configmap"></a>套用更新 configmap 掛接

如果您已部署 configmap 掛接為叢集，而且您想要更新的較新的組態，您只需編輯您先前使用過，然後套用 同樣地，使用相同的命令 configmap 掛接檔案`kubectl apply -f <configmap_yaml_file.yaml`。

組態變更可能需要幾分鐘才能完成才會生效，並在叢集中的所有 oms 代理程式 pod 會重新啟動。 重新啟動所有的 oms 代理程式 pod 輪流重新啟動後，不是全部同時重新啟動。 在重新啟動完成時，會顯示訊息，如下所示，而且包含結果： `configmap "container-azm-ms-agentconfig" updated`。

## <a name="verifying-schema-version"></a>正在驗證結構描述版本

支援的組態結構描述版本可為 omsagent pod 上的 pod 註解 （結構描述版本）。 您可以看到它們使用下列的 kubectl 命令： `kubectl describe pod omsagent-fdf58 -n=kube-system`

輸出會顯示類似下列的註釋結構描述版本：

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

## <a name="next-steps"></a>後續步驟

- 若要繼續了解如何使用 Azure 監視器並監視您 AKS 叢集的其他層面，請參閱[檢視 Azure Kubernetes 服務健康情況](container-insights-analyze.md)。
- 檢視[記錄查詢範例](container-insights-log-search.md#search-logs-to-analyze-data)以查看預先定義的查詢和範例，以評估或自訂警示、 視覺化，或分析您的叢集。