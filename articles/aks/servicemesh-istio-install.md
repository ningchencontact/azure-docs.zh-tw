---
title: 在 Azure Kubernetes Service (AKS) 中安裝 Istio
description: 了解如何在 Azure Kubernetes Service (AKS) 叢集中安裝和使用 Istio 以建立服務網格
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 9c9dcd567b8632626bf4b1f0bf2ef6b5e69b8a9d
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530442"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中安裝和使用 Istio

[Istio][istio-github]是一種開放原始碼服務網格，可在 Kubernetes 叢集中跨微服務提供一組重要的功能。 這些功能包括流量管理、服務識別和安全性、原則強制執行和可檢視性。 如需有關 Istio 的詳細資訊，請參閱官方[什麼是 Istio？][istio-docs-concepts]檔。

本文說明如何安裝 Istio。 Istio `istioctl` 用戶端二進位檔會安裝到您的用戶端電腦上，而 Istio 元件則會安裝到 AKS 上的 Kubernetes 叢集中。

> [!NOTE]
> 這些指示會參考 Istio 版本 `1.3.2`，並使用至少 Helm 版本 `2.14.2`。
>
> Istio 團隊已針對 Kubernetes 版本 `1.13`、`1.14` `1.15`，測試了 Istio `1.3.x` 版本。 您可以在[GitHub Istio 版本][istio-github-releases]（英文）中找到其他 Istio 版本、 [Istio 新聞][istio-release-notes]中每個版本的相關資訊，以及[Istio 一般常見問題][istio-faq]中的支援 Kubernetes 版本。

在本文中，您將了解：

> [!div class="checklist"]
> * 下載並安裝 Istio istioctl 用戶端二進位檔
> * 在 AKS 上安裝 Istio
> * 驗證 Istio 安裝
> * 存取附加元件
> * 從 AKS 卸載 Istio

## <a name="before-you-begin"></a>開始之前

本文中詳述的步驟假設您已建立 AKS 叢集（已啟用 RBAC 的 Kubernetes `1.13` 和更新版本），並已建立與叢集的 `kubectl` 連接。 如果您需要這些專案的協助，請參閱[AKS 快速入門][aks-quickstart]。

您將需要[Helm][helm] ，才能遵循這些指示並安裝 Istio。 建議您在叢集中正確安裝和設定最新的穩定版本。 如果您需要安裝 Helm 的協助，請參閱[AKS Helm 安裝指引][helm-install]。 所有 Istio pod 也必須排程在 Linux 節點上執行。

請確定您已閱讀[Istio 效能和擴充性](https://istio.io/docs/concepts/performance-and-scalability/)檔，以瞭解在 AKS 叢集中執行 Istio 的其他資源需求。 核心和記憶體需求會根據您的特定工作負載而有所不同。 選擇適當數目的節點和 VM 大小，以滿足您的設定。

本文將 Istio 安裝指引分成數個獨立的步驟。 最終結果在結構上與官方 Istio 安裝[指導][istio-install-helm]方針相同。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="add-the-istio-helm-chart-repository"></a>新增 Istio Helm 圖表存放庫

新增 Istio 版本的 Istio Helm 圖表存放庫。 請確定您執行 `helm repo update` 來更新圖表存放庫的本機資訊。

```azurecli
helm repo add istio.io https://storage.googleapis.com/istio-release/releases/$ISTIO_VERSION/charts/
helm repo update
```

## <a name="install-the-istio-crds-on-aks"></a>在 AKS 上安裝 Istio CRDs

Istio 會使用[自訂資源定義（CRDs）][kubernetes-crd]來管理其執行時間設定。 我們必須先安裝 Istio CRDs，因為 Istio 元件有相依性。 使用 Helm 和 `istio-init` 圖表，將 Istio CRDs 安裝到 AKS 叢集中的 `istio-system` 命名空間：

```azurecli
helm install istio.io/istio-init --name istio-init --namespace istio-system
```

[作業][kubernetes-jobs]會部署為 `istio-init` Helm 圖表的一部分，以安裝 CRDs。 根據您的叢集環境而定，這些作業的完成時間應該少於 .20s。 您可以驗證作業是否已順利完成，如下所示：

```azurecli
kubectl get jobs -n istio-system
```

下列範例輸出顯示已成功完成的作業。

```console
NAME                      COMPLETIONS   DURATION   AGE
istio-init-crd-10-1.3.2   1/1           14s        14s
istio-init-crd-11-1.3.2   1/1           12s        14s
istio-init-crd-12-1.3.2   1/1           14s        14s
```

既然我們已確認成功完成作業，讓我們來確認是否已安裝正確的 Istio CRDs 數目。 您可以執行下列命令來確認已安裝所有 23 Istio CRDs。 此命令應該會傳回 `23` 的數目。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - check CRD count](includes/servicemesh/istio/install-check-crd-count-powershell.md)]

::: zone-end

如果您已經開始，這表示您已成功安裝 Istio CRDs。 現在請移至下一節，以[在 AKS 上安裝 Istio 元件](#install-the-istio-components-on-aks)。

## <a name="install-the-istio-components-on-aks"></a>在 AKS 上安裝 Istio 元件

我們將在 Istio 安裝過程中安裝[Grafana][grafana]和[Kiali][kiali] 。 Grafana 提供分析和監視儀表板，而 Kiali 則提供服務網格可檢視性儀表板。 在我們的設定中，每個元件都需要認證，必須以[秘密][kubernetes-secrets]的形式提供。

在我們可以安裝 Istio 元件之前，必須先為 Grafana 和 Kiali 建立秘密。 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>安裝 Istio 元件

既然我們已成功建立 AKS 叢集中的 Grafana 和 Kiali 秘密，就可以安裝 Istio 元件了。 使用 Helm 和 `istio` 圖表，將 Istio 元件安裝到 AKS 叢集中的 `istio-system` 命名空間。 

> [!NOTE]
> **安裝選項**
> 
> 我們會在安裝過程中使用下列選項：
> - 已啟用控制平面的 `global.controlPlaneSecurityEnabled=true` 相互 TLS
> - `global.mtls.enabled=true`-要求所有服務對服務通訊都具有 mtls
> - `grafana.enabled=true`-啟用分析和監視儀表板的 Grafana 部署
> - `grafana.security.enabled=true`-啟用 Grafana 的驗證
> - `tracing.enabled=true`-啟用追蹤的 Jaeger 部署
> - `kiali.enabled=true`-啟用服務網格可檢視性儀表板的 Kiali 部署
>
> **節點選取器**
>
> Istio 目前必須排程在 Linux 節點上執行。 如果您的叢集中有 Windows Server 節點，您必須確定 Istio pod 只排程在 Linux 節點上執行。 我們將使用[節點選取器][kubernetes-node-selectors]來確定 pod 已排程至正確的節點。

> [!CAUTION]
> [SDS （秘密探索服務）][istio-feature-sds]和[Istio CNI][istio-feature-cni] Istio 功能目前是以[Alpha][istio-feature-stages]提供，因此請先考慮一下，再啟用這些功能。 此外，目前的 AKS 版本不會啟用[服務帳戶權杖磁片區投射][kubernetes-feature-sa-projected-volume]Kubernetes 功能（SDS 的需求）。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/istio/install-components-powershell.md)]

::: zone-end

@No__t_0 Helm 圖會部署大量物件。 您可以從上述 `helm install` 命令的輸出查看清單。 根據您的叢集環境，部署 Istio 元件應需要2分鐘的時間才能完成。

此時，您已將 Istio 部署至 AKS 叢集。 為了確保我們已成功部署 Istio，讓我們繼續進行下一節，以[驗證 Istio 安裝](#validate-the-istio-installation)。

## <a name="validate-the-istio-installation"></a>驗證 Istio 安裝

先確認是否已建立預期的服務。 使用[kubectl get svc][kubectl-get]命令來查看執行中的服務。 查詢 `istio-system` 命名空間，其中 Istio 和附加元件是由 `istio` Helm 圖表所安裝：

```console
kubectl get svc --namespace istio-system --output wide
```

下列範例輸出會顯示現在應該正在執行的服務：

- `istio-*` 服務
- `jaeger-*`、`tracing` 和 `zipkin` 附加元件追蹤服務
- `prometheus` 附加元件計量服務
- `grafana` 附加元件分析和監視儀表板服務
- `kiali` 附加元件服務網格儀表板服務

如果 `istio-ingressgateway` 顯示外部 IP 是 `<pending>`，請等候幾分鐘，直到 Azure 網路指派了 IP 位址。

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.164.244   <none>           3000/TCP                                                                                                                                     53s   app=grafana
istio-citadel            ClusterIP      10.0.49.16     <none>           8060/TCP,15014/TCP                                                                                                                           53s   istio=citadel
istio-galley             ClusterIP      10.0.175.173   <none>           443/TCP,15014/TCP,9901/TCP                                                                                                                   53s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.226.151   20.188.221.111   15020:31128/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:30817/TCP,15030:30436/TCP,15031:32485/TCP,15032:30980/TCP,15443:30124/TCP   53s   app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.102.158   <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       53s   istio=pilot
istio-policy             ClusterIP      10.0.234.53    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                                 53s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.216.8     <none>           443/TCP,15014/TCP                                                                                                                            53s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.154.215   <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       53s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                                   52s   app=jaeger
jaeger-collector         ClusterIP      10.0.26.109    <none>           14267/TCP,14268/TCP                                                                                                                          52s   app=jaeger
jaeger-query             ClusterIP      10.0.70.55     <none>           16686/TCP                                                                                                                                    52s   app=jaeger
kiali                    ClusterIP      10.0.36.206    <none>           20001/TCP                                                                                                                                    53s   app=kiali
prometheus               ClusterIP      10.0.236.99    <none>           9090/TCP                                                                                                                                     53s   app=prometheus
tracing                  ClusterIP      10.0.83.152    <none>           80/TCP                                                                                                                                       52s   app=jaeger
zipkin                   ClusterIP      10.0.25.86     <none>           9411/TCP                                                                                                                                     52s   app=jaeger
```

接著，確認是否已建立所需的 Pod。 使用[kubectl get][kubectl-get] pod 命令，然後重新查詢 `istio-system` 命名空間：

```console
kubectl get pods --namespace istio-system
```

下列範例輸出會顯示正在執行的 Pod：

- `istio-*` pod
- `prometheus-*` 附加元件計量 pod
- `grafana-*` 附加元件分析和監視儀表板 pod
- `kiali` 附加元件服務網格儀表板 pod

```console
NAME                                     READY   STATUS      RESTARTS   AGE
grafana-7c48555456-msl7b                 1/1     Running     0          88s
istio-citadel-566fc66db7-m8wgl           1/1     Running     0          87s
istio-galley-5746db8d56-pl5gg            1/1     Running     0          88s
istio-ingressgateway-6c94f7c9bf-f5lt5    1/1     Running     0          88s
istio-init-crd-10-1.3.2-xw9g2            0/1     Completed   0          92m
istio-init-crd-11-1.3.2-54rz8            0/1     Completed   0          92m
istio-init-crd-12-1.3.2-789qj            0/1     Completed   0          92m
istio-pilot-6748968b6d-rvdfx             2/2     Running     0          87s
istio-policy-7576bbbcf7-2stft            2/2     Running     0          87s
istio-sidecar-injector-76d79d494-7jk9n   1/1     Running     0          87s
istio-telemetry-74b7bf676d-tfrcl         2/2     Running     0          88s
istio-tracing-655d9588bc-d2htg           1/1     Running     0          86s
kiali-65d55bcfb8-tqrfk                   1/1     Running     0          88s
prometheus-846f9849bd-br8kp              1/1     Running     0          87s
```

應該會有三個具有 `Completed` 狀態的 `istio-init-crd-*` pod。 這些 pod 負責執行在先前步驟中建立 CRDs 的工作。 所有其他 pod 都應該顯示 `Running` 的狀態。 如果您的 Pod 沒有這些狀態，請等候一兩分鐘直到其成為該狀態。 如果有任何 pod 回報問題，請使用[kubectl 描述 pod][kubectl-describe]命令來檢查其輸出和狀態。

## <a name="accessing-the-add-ons"></a>存取附加元件

在上述的安裝程式中，Istio 會安裝一些附加元件，以提供額外的功能。 附加元件的 web 應用程式**不**會透過外部 ip 位址公開公開。 

若要存取附加元件使用者介面，請使用 `istioctl dashboard` 命令。 此命令會利用[kubectl 的埠轉送][kubectl-port-forward]和隨機埠，在您的用戶端電腦和 AKS 叢集中的相關 pod 之間建立安全連線。 然後，它會在您的預設瀏覽器中自動開啟附加元件 web 應用程式。

我們為 Grafana 和 Kiali 新增了一層額外的安全性，方法是在本文稍早的指定認證。

### <a name="grafana"></a>Grafana

Istio 的分析和監視儀表板是由[Grafana][grafana]提供。 請記得在出現提示時，使用您稍早透過 Grafana 密碼所建立的認證。 以安全的方式開啟 Grafana 儀表板，如下所示：

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

[Prometheus][prometheus]會提供 Istio 的計量。 以安全的方式開啟 Prometheus 儀表板，如下所示：

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Istio 內的追蹤是由[Jaeger][jaeger]提供。 以安全的方式開啟 Jaeger 儀表板，如下所示：

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

服務網格可檢視性儀表板是由[Kiali][kiali]提供。 請記得在出現提示時，使用您稍早透過 Kiali 密碼所建立的認證。 以安全的方式開啟 Kiali 儀表板，如下所示：

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

提供[Envoy][envoy] proxy 的簡單介面。 它會為在指定 pod 中執行的 Envoy proxy 提供設定資訊和計量。 安全地開啟 Envoy 介面，如下所示：

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>從 AKS 卸載 Istio

> [!WARNING]
> 從執行中的系統刪除 Istio 可能會導致您的服務之間發生流量相關的問題。 請確定您已在不 Istio 的情況下，將您的系統布建為正常運作，再繼續進行。

### <a name="remove-istio-components-and-namespace"></a>移除 Istio 元件和命名空間

若要從您的 AKS 叢集中移除 Istio，請使用下列命令。 @No__t_0 命令會移除 `istio` 和 `istio-init` 的圖表，而 `kubectl delete namespace` 命令會移除 `istio-system` 命名空間。

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete namespace istio-system
```

### <a name="remove-istio-crds-and-secrets"></a>移除 Istio CRDs 和秘密

上述命令會刪除所有 Istio 元件和命名空間，但我們仍會留下 Istio CRDs 和秘密。 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>後續步驟

下列檔說明如何使用 Istio 來提供智慧型路由，以推出未排放的版本：

> [!div class="nextstepaction"]
> [AKS Istio 智慧型路由案例][istio-scenario-routing]

若要深入探索 Istio 的安裝和組態選項，請參閱下列官方 Istio 文章：

- [Istio-Helm 安裝指南][istio-install-helm]
- [Istio-Helm 安裝選項][istio-install-helm-options]

您也可以使用來遵循其他案例：

- [Istio Bookinfo 應用程式範例][istio-bookinfo-example]

若要瞭解如何使用 Application Insights 和 Istio 監視您的 AKS 應用程式，請參閱下列 Azure 監視器檔：

- [Kubernetes 託管應用程式的零檢測應用程式監視][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: https://docs.microsoft.com/en-us/azure/aks/concepts-clusters-workloads#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./servicemesh-istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
