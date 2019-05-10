---
title: Azure 監視器-監視 Kubernetes 託管的應用程式零檢測應用程式 |Microsoft Docs
description: 監視裝載的 Kubernetes 應用程式零檢測應用程式是監視解決方案，可讓您收集有關在 Kubernetes 叢集中所執行的 pod 往返傳入和傳出要求的 Application Insights 遙測資料使用服務網格技術稱為 Istio。
services: application-insights
author: tokaplan
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: alkaplan
ms.openlocfilehash: 42b81ec0fa01841791a5b2651d1c1189db5e27ff
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408217"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Kubernetes 零檢測應用程式監視裝載的應用程式

> [!IMPORTANT]
> 這項功能目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 監視器現在會利用服務網格技術上您的 Kubernetes 叢集，以提供從 方塊中的任何裝載的 Kubernetes 應用程式的應用程式監視。 Application Insights 功能，例如預設值[應用程式對應](../../azure-monitor/app/app-map.md)來建立您的相依性的模型[即時的計量 Stream](../../azure-monitor/app/live-stream.md)即時監視，功能強大的視覺效果，而[預設儀表板](../../azure-monitor/app/overview-dashboard.md)，[計量瀏覽器](../../azure-monitor/platform/metrics-getting-started.md)，以及[活頁簿](../../azure-monitor/app/usage-workbooks.md)。 這項功能可協助使用者找出效能瓶頸和失敗的作用點，所有選取的 Kubernetes 命名空間內其 Kubernetes 工作負載。 Istio 等技術將現有服務網格投資中獲益，Azure 監視器可讓自動檢測的應用程式監視不需要任何修改您的應用程式程式碼。

> [!NOTE]
> 這是要執行的 Kubernetes 上的應用程式監視的眾多方法之一。 您也可以進行任何應用程式所使用裝載的 Kubernetes [Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.md)而不需要服務網格。 若要監視 Kubernetes 不檢測的應用程式使用 SDK，您可以使用下列方法。

## <a name="prerequisites"></a>必要條件

- A [Kubernetes 叢集](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads)。
- 主控台執行叢集的存取權*kubectl*。
- [Application Insight 資源](create-new-resource.md)
- 需要服務網格。 如果您的叢集沒有 Istio 部署，您可以了解如何[安裝，並在 Azure Kubernetes 服務中使用 Istio](https://docs.microsoft.com/azure/aks/istio-install)。

## <a name="capabilities"></a>功能

藉由使用零個 kubernetes 監視裝載的應用程式的檢測應用程式，您可以使用：

- [應用程式對應](../../azure-monitor/app/app-map.md)
- [即時 Stream 計量](../../azure-monitor/app/live-stream.md)
- [儀表板](../../azure-monitor/app/overview-dashboard.md)
- [Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md)
- [分散式追蹤](../../azure-monitor/app/distributed-tracing.md)
- [端對端交易監視](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>安裝步驟

若要啟用的解決方案，我們會執行下列步驟：
- （如果尚未部署），請部署應用程式。
- 確定應用程式服務網格的一部分。
- 觀察所收集的遙測。

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>設定您的應用程式，才能使用服務網格

Istio 支援兩種[檢測 pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/)。
在大部分情況下，是最簡單的方式將標記包含您的應用程式的 Kubernetes 命名空間*istio 插入*標籤：

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> 服務網格訂位資料從網路，因為我們無法攔截加密的流量。 不會離開叢集的流量，請使用未加密的通訊協定 (例如，HTTP)。 對於外部流量必須加密，請考慮[設定 SSL 終止](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls)在輸入控制器。

不會影響服務網格外部執行的應用程式。

### <a name="deploy-your-application"></a>部署應用程式

- 應用程式部署至*我的應用程式-命名空間*命名空間。 如果已部署應用程式，而且您已遵循上述的自動側車資料隱碼攻擊方法，您需要重新建立 pod，以確保 Istio 插入其側車;可能是起始輪流更新或刪除個別的 pod 並等候排程來重新建立。
- 請確定您的應用程式遵守[Istio 需求](https://istio.io/docs/setup/kubernetes/prepare/requirements/)。

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>部署託管的應用程式監視 Kubernetes 零檢測應用程式

1. 下載並解壓縮[ *Application Insights 配接器*release](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/)。
2. 瀏覽至 */srckubernetes/* 發行資料夾內。
3. 編輯*application-insights-istio-mixer-adapter-deployment.yaml*
    - 編輯的值*ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY*環境變數，以包含在 Azure 入口網站，以包含遙測的 Application Insights 資源的檢測金鑰。
    - 如有必要，編輯的值*ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES*環境變數，以包含您要啟用監視的命名空間的逗號分隔的清單。 保留空白，以便監視所有命名空間。
4. 套用*每個*YAML 檔案下找到*src/kubernetes/* 藉由執行下列命令 (您仍然必須在內 */srckubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>驗證部署

- 確定已部署 Application Insights 配接器：

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> 在某些情況下，微調微調是必要的。 若要包含或排除個別的 pod 的遙測收集，請使用*appinsights/monitoring.enabled*標籤上的 pod。 這將會優先於所有命名空間為基礎的組態。 設定*appinsights/monitoring.enabled*要 *，則為 true*包含 pod，以及*false*以將其排除。

### <a name="view-application-insights-telemetry"></a>檢視 Application Insights 遙測資料

- 產生的範例要求，針對您的應用程式，以確認該監視正常運作。
- 在 3 到 5 分鐘內，您應該會開始看到遙測會出現在 Azure 入口網站。 請務必查看*應用程式對應*入口網站中的 Application Insights 資源一節。

## <a name="troubleshooting"></a>疑難排解

遙測資料不會出現在 Azure 入口網站時所要使用的疑難排解流程應該如下。

1. 請確定應用程式未達負載並已傳送/接收中一般 HTTP 要求。 遙測會消除離線，因為不支援加密的流量。 如果沒有任何傳入或傳出的要求，會有任何遙測可能。
2. 確保在提供正確的檢測金鑰*ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY*中的環境變數*application-insights-istio-mixer-adapter-deployment.yaml*。 檢測金鑰位於*概觀* 索引標籤，在 Azure 入口網站中的 Application Insights 資源。
3. 請確定正確的 Kubernetes 命名空間中提供*ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES*中的環境變數*application-insights-istio-mixer-adapter-deployment.yaml*。 保留空白，以便監視所有命名空間。
4. 請確定您的應用程式 pod 已由 Istio 插入側車。 請確認 Istio 的側車存在於每個 pod。

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   確認有一個名為容器*istio proxy*在 pod 上執行。

5. 檢視 Application Insights 配接器的追蹤。

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   接收到的遙測項目計數會更新一次一分鐘的時間。 如果它不會成長分鐘超過分鐘數-不會被傳送遙測至配接器 Istio。
   尋找記錄檔中的任何錯誤。
6. 若已建立的*Kubernetes 的 Application Insight*配接器不正在傳送遙測資料，請檢查 Istio 的 Mixer 記錄檔，以找出為什麼它不將資料傳送至配接器：

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   尋找任何錯誤，特別屬於與通訊*applicationinsightsadapter*配接器。

## <a name="faq"></a>常見問題集

取得這個專案進度的最新資訊，請瀏覽[Istio Mixer 專案的 GitHub 的 Application Insights 配接器](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq)。

## <a name="uninstall"></a>解除安裝

若要解除安裝的產品*每隔*YAML 檔案下找到*src/kubernetes/* 執行：

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 監視器和容器的合作方式，請造訪[容器概觀相關的 Azure 監視](../../azure-monitor/insights/container-insights-overview.md)