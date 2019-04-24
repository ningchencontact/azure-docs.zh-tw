---
title: 透過 Istio 在 Azure Kubernetes Service (AKS) 中使用智慧型路由和 Canary 版本
description: 了解如何使用 Istio 在 Azure Kubernetes Service (AKS) 叢集中提供智慧型路由及部署 Canary 版本
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 12/03/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 0a4e5e7e310a9949ee59291c2032eafda46955a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60465827"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>透過 Istio 在 Azure Kubernetes Service (AKS) 中使用智慧型路由和 Canary 版本

[Istio][istio-github] 是一種開放原始碼服務網格，可在 Kubernetes 叢集中提供跨微服務的主要功能集。 這些功能包括流量管理、服務識別和安全性、原則強制執行和可檢視性。 如需 Istio 的詳細資訊，請參閱官方文件[什麼是 Istio？][istio-docs-concepts]。

本文將說明如何使用 Istio 的流量管理功能。 我們將使用範例 AKS 投票應用程式來探索智慧型路由和 Canary 版本。

在本文中，您將了解：

> [!div class="checklist"]
> * 部署應用程式
> * 更新應用程式
> * 推出應用程式的 Canary 版本
> * 完成推出

## <a name="before-you-begin"></a>開始之前

本文中詳述的步驟假設您已建立 AKS 叢集 (已啟用 RBAC 的 Kubernetes 1.10 和更新版本)，並已建立與叢集的 `kubectl` 連線。 您也必須在叢集中安裝 Istio。

如果您需要前述任何方面的協助，請參閱 [AKS 快速入門][aks-quickstart]和[在 AKS 中安裝 Istio][istio-install]。

## <a name="about-this-application-scenario"></a>關於此應用程式的案例

範例 AKS 投票應用程式為使用者提供兩種投票選項 (「貓」或「狗」)。 有一項儲存體元件會保存各個選項的投票數。 此外，還有一項分析元件會提供與每個選項的投票有關的詳細資料。

在本文中，您首先將部署 *1.0* 版的投票應用程式和 *1.0* 版的分析元件。 此分析元件會提供簡單的投票計數。 投票應用程式和分析元件會與採用 Redis 的 *1.0* 版儲存體元件互動。

接著您會將此分析元件升級至 *1.1* 版，此版本不僅提供計數，也提供總計和百分比。

一部分的使用者會透過 Canary 版本測試 *2.0* 版的應用程式。 這個新版本會使用由 MySQL 資料庫支援的儲存體元件。

在您確認 *2.0* 版可供該部分的使用者正常運作後，您即會為使用者推出 *2.0* 版。

## <a name="deploy-the-application"></a>部署應用程式

首先我們將在您的 Azure Kubernetes Service (AKS) 叢集中部署應用程式。 下圖顯示本節的結尾所執行的項目 - 所有元件的 *1.0* 版，且透過 Istio 輸入閘道回應輸入要求：

![AKS 投票應用程式元件和路由。](media/istio/components-and-routing-01.png)

您依照本文指示操作時所需的成品，可從 [Azure-Samples/aks-voting-app][github-azure-sample] GitHub 存放庫取得。 您可以下載成品或複製存放庫，如下所示：

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

在下載/複製的存放庫中切換至下列資料夾，並從這個資料夾執行所有後續步驟：

```console
cd scenarios/intelligent-routing-with-istio
```

首先，在您的 AKS 叢集中為範例 AKS 投票應用程式建立名為「投票」的命名空間，如下所示：

```console
kubectl create namespace voting
```

為此命名空間加上 `istio-injection=enabled` 的標籤。 此標籤會指示 istio 在此命名空間內的所有 Pod 中自動插入 istio-proxy 作為側車。

```console
kubectl label namespace voting istio-injection=enabled
```

現在，我們將建立 AKS 投票應用程式的元件。 在上一個步驟所建立的「投票」命名空間中建立這些元件。

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

下列範例輸出顯示已成功建立的資源：

```
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio 在 Pod 和服務方面會有某些特定需求。 如需詳細資訊，請參閱 [Istio 的 Pod 和服務需求文件][istio-requirements-pods-and-services]。

若要查看已建立的 Pod，請使用 [kubectl get pods][kubectl-get] 命令，如下所示：

```console
kubectl get pods -n voting
```

下列範例輸出顯示 *voting-app* Pod 有三個執行個體，而 *voting-analytics* 和 *voting-storage* Pod 各有一個執行個體。 每個 Pod 有兩個容器。 其中一個容器是元件，另一個則是 *istio-proxy*：

```
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-669f99dcc8-lzh7k   2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-bdmld         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-gcrng         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-strzc         2/2       Running   0          1m
voting-storage-1-0-7954799d96-5fv9r     2/2       Running   0          1m
```

若要查看 Pod 的相關資訊，請使用 [kubectl describe pod][kubectl-describe]。 請將 Pod 名稱取代為前述輸出中您個人 AKS 叢集內的 Pod 名稱：

```console
kubectl describe pod voting-app-1-0-6c65c4bdd4-bdmld --namespace voting
```

Istio 已在 *Istio-proxy* 容器中自動插入，以管理出入於元件的網路流量，如下列範例輸出所示：

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.0.4
[...]
```

在建立 Istio [閘道][istio-reference-gateway]和[虛擬服務][istio-reference-virtualservice]前，您無法連線至投票應用程式。 這些 Istio 資源會將來自預設 Istio 輸入閘道的流量路由至我們的應用程式。

> [!NOTE]
> *閘道*是位於服務網格邊緣的元件，會接收輸入或輸出的 HTTP 和 TCP 流量。
>
> *虛擬服務*會為一或多個目的地服務定義一組路由規則。

使用 `istioctl` 用戶端二進位檔部署閘道和虛擬服務 yaml。 如同 `kubectl apply` 命令，請記得指定這些資源要部署到的命名空間。

```console
istioctl create -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

使用下列命令取得 Istio 輸入閘道的 IP 位址：

```console
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

下列範例輸出顯示輸入閘道的 IP 位址：

```
52.187.250.239
```

開啟瀏覽器並貼上 IP 位址。 範例 AKS 投票應用程式隨即顯示。

![在已啟用 Istio 的 AKS 叢集中執行的 AKS 投票應用程式。](media/istio/deploy-app-01.png)

畫面底部的資訊顯示應用程式使用 *1.0* 版的 *voting-app* 和 *1.0* 版 (Redis) 作為儲存體選項。

## <a name="update-the-application"></a>更新應用程式

我們將部署新版本的分析元件。 *1.1* 這個新版本除了會顯示每個類別的計數，也會顯示總計和百分比。

下圖顯示本節的結尾所執行的項目 - 只有 *1.1* 版的 *voting-analytics* 元件有路由自 *voting-app* 元件的流量。 即使 *1.0* 版的 *voting-analytics* 元件繼續執行，並且由 *voting-analytics* 服務所參考，Istio Proxy 仍不允許其輸入和輸出流量。

![AKS 投票應用程式元件和路由。](media/istio/components-and-routing-02.png)

我們將部署 *1.1* 版的 *voting-analytics* 元件。 請在「投票」命名空間中建立此元件：

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

使用在上一個步驟中取得的 Istio 輸入閘道 IP 位址，再次在瀏覽器中開啟範例 AKS 投票應用程式。

您的瀏覽器會在兩個檢視之間交替顯示，如下所示。 由於您對 *voting-analytics* 元件使用的 Kubernetes [服務][kubernetes-service]只有單一標籤選取器 (`app: voting-analytics`)，因此 Kubernetes 會在符合該選取器的 Pod 之間使用循環配置資源的預設行為。 在此案例中，這是 *voting-analytics* Pod 的 *1.0* 和 *1.1* 版。

![在 AKS 投票應用程式中執行的 1.0 版分析元件。](media/istio/deploy-app-01.png)

![在 AKS 投票應用程式中執行的 1.1 版分析元件。](media/istio/update-app-01.png)

您可以視覺化方式顯示兩個 *voting-analytics* 元件版本之間的切換，如下所示。 請務必使用您個人 Istio 輸入閘道的 IP 位址。

```console
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

下列範例輸出顯示在不同版本間切換網站時，所傳回網站的對應部分：

```
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>鎖定對 1.1 版應用程式的流量

現在，我們將鎖定對 *1.1* 版 *voting-analytics* 元件和 *1.0* 版 *voting-storage* 元件的流量。 接著，您會定義所有其他元件的路由規則。

> * *虛擬服務*會為一或多個目的地服務定義一組路由規則。
> * *目的地規則*會定義流量原則和版本特定原則。
> * *原則*會定義可用於工作負載的驗證方法。

您將使用 `istioctl` 用戶端二進位檔來取代 *voting-app* 的虛擬服務定義，並且為其他元件新增[目的地規則][istio-reference-destinationrule]和[虛擬服務][istio-reference-virtualservice]。

您也會將[原則][istio-reference-policy]新增至*投票*命名空間，以確定服務之間的所有通訊都會透過相互 TLS 和用戶端憑證受到保護。

由於您所取代的 *voting-app* 有現有的虛擬服務定義，請使用 `istioctl replace` 命令，如下所示：

```console
istioctl replace -f istio/step-2a-update-voting-app-virtualservice.yaml --namespace voting
```

下列範例輸出顯示 Istio 虛擬服務已成功更新：

```
Updated config virtual-service/voting/voting-app to revision 141902
```

接下來，請使用 `istioctl create` 命令新增新的原則，以及所有其他元件的新目的地規則和虛擬服務。

* 此原則會將 `peers.mtls.mode` 設為 `STRICT`，以確定您在*投票*命名空間內的服務之間會強制執行相互 TLS。
* 您也會在所有的目的地規則中將 `trafficPolicy.tls.mode` 設為 `ISTIO_MUTUAL`。 Istio 可提供具有增強式身分識別的服務，並使用 Istio 可明確管理的相互 TLS 和用戶端憑證保護服務之間的通訊。

```console
istioctl create -f istio/step-2b-add-routing-for-all-components.yaml --namespace voting
```

下列範例輸出顯示新的原則、目的地規則和虛擬服務已成功建立：

```
Created config policy/voting/default to revision 142118
Created config destination-rule/voting/voting-app at revision 142119
Created config destination-rule/voting/voting-analytics at revision 142120
Created config virtual-service/voting/voting-analytics at revision 142121
Created config destination-rule/voting/voting-storage at revision 142122
Created config virtual-service/voting/voting-storage at revision 142123
```

如果您再次在瀏覽器中開啟 AKS 投票應用程式，將只有 *voting-app* 元件會使用新版本 *1.1* 的 *voting-analytics* 元件。

![在 AKS 投票應用程式中執行的 1.1 版分析元件。](media/istio/update-app-01.png)

您可以更輕鬆地呈現出我們目前僅路由至 *1.1* 版的 *voting-analytics* 元件，如下所示。 請務必使用您的 Istio 輸入閘道的 IP 位址。

您可以呈現出您目前僅路由至 *1.1* 版的 *voting-analytics* 元件，如下所示。 請務必使用您個人 Istio 輸入閘道的 IP 位址：

```azurecli
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

下列範例輸出顯示所傳回網站的對應部分：

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

請確認 Istio 使用相互 TLS 來保護每個服務之間的通訊。 下列命令會檢查每個 *voting-app* 服務的 TLS 設定：

```console
istioctl authn tls-check voting-app.voting.svc.cluster.local
istioctl authn tls-check voting-analytics.voting.svc.cluster.local
istioctl authn tls-check voting-storage.voting.svc.cluster.local
```

下列範例輸出顯示透過原則和目的地規則為每個服務強制執行相互 TLS 的情形：

```
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>推出應用程式的 Canary 版本

現在，我們將部署新版本 *2.0* 的 *voting-app*、*voting-analytics* 和 *voting-storage* 元件。 新的 *voting-storage* 元件會使用 MySQL 而非 Redis，且 *voting-app* 和 *voting-analytics* 元件會進行更新，以便能夠使用這個新的 *voting-storage* 元件。

*voting-app* 元件現已可支援功能旗標功能。 此功能旗標可讓您針對某部分的使用者測試 Istio 的 Canary 版本功能。

下圖顯示本節的結尾處所將執行的項目。

* *1.0* 版的 *voting-app* 元件、*1.1* 版的 *voting-analytics* 元件和 *1.0* 版的 *voting-storage* 元件能夠互相通訊。
* *2.0* 版的 *voting-app* 元件、*2.0* 版的 *voting-analytics* 元件和 *2.0* 版的 *voting-storage* 元件能夠互相通訊。
* 只有已設定特定功能旗標的使用者能夠存取 *2.0* 版的 *voting-app* 元件。 這項變更可透過 Cookie 使用功能旗標來管理。

![AKS 投票應用程式元件和路由。](media/istio/components-and-routing-03.png)

首先，請更新 Istio 目的地規則和虛擬服務，以因應這些新元件的需求。 這些更新可確保您不會錯誤地將流量路由至新元件，且使用者不會獲得非預期的存取：

```console
istioctl replace -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

下列範例輸出顯示目的地規則和虛擬服務已成功更新：

```
Updated config destination-rule/voting/voting-app to revision 150930
Updated config virtual-service/voting/voting-app to revision 150931
Updated config destination-rule/voting/voting-analytics to revision 150937
Updated config virtual-service/voting/voting-analytics to revision 150939
Updated config destination-rule/voting/voting-storage to revision 150940
Updated config virtual-service/voting/voting-storage to revision 150941
```

接著，我們將為新版本 *2.0* 的元件新增 Kubernetes 物件。 您也會更新 *voting-storage* 服務，以納入適用於 MySQL 的 *3306* 連接埠：

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

下列範例輸出顯示 Kubernetes 物件已成功更新或建立：

```
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

等待所有的 *2.0* 版 Pod 執行完成。 使用 [kubectl get pods][kubectl-get] 命令檢視*投票*命名空間中的所有 Pod：

```azurecli
kubectl get pods --namespace voting
```

您現在應可在 *1.0* 版和 *2.0* 版 (canary) 的投票應用程式之間切換。 畫面底部的功能旗標切換會設定 Cookie。 *voting-app* 虛擬服務會使用此 Cookie 將使用者路由至新版本 *2.0*。

![1.0 版的 AKS 投票應用程式 -「不會」設定功能旗標。](media/istio/canary-release-01.png)

![2.0 版的 AKS 投票應用程式 -「會」設定功能旗標。](media/istio/canary-release-02.png)

兩個應用程式版本的投票計數並不相同。 此差異凸顯出您使用了兩個不同的儲存體後端。

## <a name="finalize-the-rollout"></a>完成推出

在您成功測試 Canary 版本後，請更新 *voting-app* 虛擬服務，以將所有流量路由至 *2.0* 版的 *voting-app* 元件。 其後，無論是否已設定功能旗標，所有使用者都會看到 *2.0* 版的應用程式：

![AKS 投票應用程式元件和路由。](media/istio/components-and-routing-04.png)

更新所有目的地規則，以移除您不想再啟用的元件版本。 接著，更新所有虛擬服務以停止參考這些版本。

由於再也不會有任何流量進入任何較舊版本的元件，您現在可以安全地刪除這些元件的所有部署。

![AKS 投票應用程式元件和路由。](media/istio/components-and-routing-05.png)

您現在已成功推出新版的 AKS 投票應用程式。

## <a name="next-steps"></a>後續步驟

您可以使用 [Istio Bookinfo 應用程式範例][istio-bookinfo-example]探索其他案例。

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/spec-requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#Gateway
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#VirtualService
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#DestinationRule
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md