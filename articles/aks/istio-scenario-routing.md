---
title: 透過 Istio 在 Azure Kubernetes Service (AKS) 中使用智慧型路由和 Canary 版本
description: 了解如何使用 Istio 在 Azure Kubernetes Service (AKS) 叢集中提供智慧型路由及部署 Canary 版本
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: bd660a2b6ffb96478c3170cc7013ff22518b758f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64702215"
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

> [!NOTE]
> 此案例中，已經過 Istio 版本`1.1.3`。

這篇文章中詳述的步驟假設您已建立 AKS 叢集 (Kubernetes`1.11`和更新版本，使用 RBAC 啟用)，並已建立`kubectl`與叢集的連線。 您也需要安裝在您的叢集中的 Istio。

如果您需要協助進行任何這些項目，則會看到[AKS 快速入門][ aks-quickstart]並[安裝在 AKS 的 Istio] [ istio-install]指引。

## <a name="about-this-application-scenario"></a>關於此應用程式的案例

範例 AKS 投票應用程式提供兩個投票選項 (**貓**或是**Dogs**) 給使用者。 有一項儲存體元件會保存各個選項的投票數。 此外，還有一項分析元件會提供與每個選項的投票有關的詳細資料。

在此應用程式案例中，您先部署版本`1.0`投票應用程式和版本`1.0`的 analytics 元件。 此分析元件會提供簡單的投票計數。 投票應用程式和分析元件互動版本`1.0`的儲存體元件，而由 Redis。

分析元件升級至新版`1.1`，它提供計數，並立即總計和百分比。

使用者測試版本的子集`2.0`透過 canary 版本的應用程式。 這個新版本會使用由 MySQL 資料庫支援的儲存體元件。

一旦您確信該版本`2.0`轉出版本的使用者子集上正常運作，`2.0`給您的使用者。

## <a name="deploy-the-application"></a>部署應用程式

首先我們將在您的 Azure Kubernetes Service (AKS) 叢集中部署應用程式。 下圖顯示執行結束時的這一節的什麼版本`1.0`透過 Istio 輸入閘道服務的輸入要求的所有元件：

![AKS 投票應用程式元件和路由。](media/istio/components-and-routing-01.png)

您依照本文指示操作時所需的成品，可從 [Azure-Samples/aks-voting-app][github-azure-sample] GitHub 存放庫取得。 您可以下載成品或複製存放庫，如下所示：

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

在下載/複製的存放庫中切換至下列資料夾，並從這個資料夾執行所有後續步驟：

```console
cd scenarios/intelligent-routing-with-istio
```

首先，建立範例 AKS 投票應用程式名為 AKS 叢集中的 命名空間`voting`，如下所示：

```azurecli
kubectl create namespace voting
```

為此命名空間加上 `istio-injection=enabled` 的標籤。 此標籤會指示 istio 在此命名空間內的所有 Pod 中自動插入 istio-proxy 作為側車。

```azurecli
kubectl label namespace voting istio-injection=enabled
```

現在，我們將建立 AKS 投票應用程式的元件。 建立這些元件在`voting`在上一個步驟中建立的命名空間。

```azurecli
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

下列範例輸出會顯示正在建立的資源：

```console
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

```azurecli
kubectl get pods -n voting
```

下列範例輸出顯示有三個執行個體`voting-app`pod 和這兩者的單一執行個體`voting-analytics`和`voting-storage`pod。 每個 Pod 有兩個容器。 其中一個容器是元件，而另一個是`istio-proxy`:

```console
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s
```

若要查看 Pod 的相關資訊，請使用 [kubectl describe pod][kubectl-describe]。 請將 Pod 名稱取代為前述輸出中您個人 AKS 叢集內的 Pod 名稱：

```azurecli
kubectl describe pod voting-app-1-0-956756fd-d5w7z --namespace voting
```

`istio-proxy`容器自動已經由插入 Istio 來管理網路流量進出您的元件，如下列範例輸出所示：

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.1.3
[...]
```

在建立 Istio [閘道][istio-reference-gateway]和[虛擬服務][istio-reference-virtualservice]前，您無法連線至投票應用程式。 這些 Istio 資源會將來自預設 Istio 輸入閘道的流量路由至我們的應用程式。

> [!NOTE]
> **閘道**是位於服務網格邊緣的元件，會接收輸入或輸出的 HTTP 和 TCP 流量。
> 
> **虛擬服務**會為一或多個目的地服務定義一組路由規則。

使用`kubectl apply`命令來部署閘道和虛擬服務 yaml。 請記得要指定這些資源會部署到的命名空間。

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

下列範例輸出顯示新的閘道和所建立的虛擬服務：

```console
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

使用下列命令取得 Istio 輸入閘道的 IP 位址：

```azurecli
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

下列範例輸出顯示輸入閘道的 IP 位址：

```
20.188.211.19
```

開啟瀏覽器並貼上 IP 位址。 範例 AKS 投票應用程式隨即顯示。

![在已啟用 Istio 的 AKS 叢集中執行的 AKS 投票應用程式。](media/istio/deploy-app-01.png)

應用程式使用的版本會顯示在畫面底部的資訊`1.0`的`voting-app`和 版本`1.0`的`voting-storage`(Redis)。

## <a name="update-the-application"></a>更新應用程式

我們將部署新版本的分析元件。 這個新版本`1.1`顯示總計，以及除了每個分類計數的百分比。

下圖顯示哪些會執行這一節只有版本結尾`1.1`的我們`voting-analytics`元件的流量路由傳送從`voting-app`元件。 即使版本`1.0`的我們`voting-analytics`元件會繼續執行，而且由參考`voting-analytics`服務、 Istio proxy 不允許流量進出它。

![AKS 投票應用程式元件和路由。](media/istio/components-and-routing-02.png)

讓我們來部署版本`1.1`的`voting-analytics`元件。 建立此元件在`voting`命名空間：

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

下列範例輸出會顯示正在建立的資源：

```console
deployment.apps/voting-analytics-1-1 created
```

使用在上一個步驟中取得的 Istio 輸入閘道 IP 位址，再次在瀏覽器中開啟範例 AKS 投票應用程式。

您的瀏覽器會在兩個檢視之間交替顯示，如下所示。 因為您要使用 Kubernetes[服務][ kubernetes-service] for`voting-analytics`元件只是單一標籤選取器 (`app: voting-analytics`)，Kubernetes 會使用循環之間的預設行為符合該選取器的 pod。 在此情況下，它是兩個版本`1.0`並`1.1`的您`voting-analytics`pod。

![在 AKS 投票應用程式中執行的 1.0 版分析元件。](media/istio/deploy-app-01.png)

![在 AKS 投票應用程式中執行的 1.1 版分析元件。](media/istio/update-app-01.png)

您可以視覺化方式顯示兩個版本之間切換`voting-analytics`元件，如下所示。 請務必使用您個人 Istio 輸入閘道的 IP 位址。

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

Powershell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
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

現在讓我們鎖定只有版本流量`1.1`的`voting-analytics`元件和版本`1.0`的`voting-storage`元件。 接著，您會定義所有其他元件的路由規則。

> * **虛擬服務**會為一或多個目的地服務定義一組路由規則。
> * **目的地規則**會定義流量原則和版本特定原則。
> * **原則**會定義可用於工作負載的驗證方法。

使用`kubectl apply`命令，以取代虛擬服務定義，在您`voting-app`並新增[目的地規則][ istio-reference-destinationrule]並[虛擬服務][istio-reference-virtualservice]其他元件。 您將會加入[原則][ istio-reference-policy]到`voting`使用相互 TLS 和用戶端憑證保護，確保所有通訊服務之間的命名空間。

* 原則含有`peers.mtls.mode`設定為`STRICT`以確保相互 TLS 會強制執行您的服務內之間`voting`命名空間。
* 我們也會設定`trafficPolicy.tls.mode`至`ISTIO_MUTUAL`我們所有的目的地規則。 Istio 可提供具有增強式身分識別的服務，並使用 Istio 可明確管理的相互 TLS 和用戶端憑證保護服務之間的通訊。

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

下列範例輸出顯示新的原則、 目的地規則，以及正在更新/建立的虛擬服務：

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

如果您的 AKS 投票應用程式瀏覽器中開啟同樣地，只有新的版本才`1.1`的`voting-analytics`元件由`voting-app`元件。

![在 AKS 投票應用程式中執行的 1.1 版分析元件。](media/istio/update-app-01.png)

您可以視覺化方式顯示，您現在只有路由傳送至版本`1.1`的程式`voting-analytics`元件，如下所示。 請務必使用您個人 Istio 輸入閘道的 IP 位址：

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

Powershell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

下列範例輸出顯示所傳回網站的對應部分：

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

讓我們現在先確認 Istio 使用相互 TLS 保護每個服務之間的通訊。 我們將使用這[authn tls 檢查][ istioctl-authn-tls-check]命令`istioctl`用戶端二進位檔，其使用下列格式。

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

這組命令指定的服務，提供存取權的相關資訊，從所有 pod 的命名空間中，符合一組的標籤：

Bash

```bash
# mTLS configuration between each of the istio ingress pods and the voting-app service
kubectl get pod -n istio-system -l app=istio-ingressgateway | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.istio-system voting-app.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-analytics service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-analytics.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-storage service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
kubectl get pod -n voting -l app=voting-analytics,version=1.1 | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local
```

Powershell

```powershell
# mTLS configuration between each of the istio ingress pods and the voting-app service
(kubectl get pod -n istio-system -l app=istio-ingressgateway | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".istio-system") voting-app.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-analytics service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-analytics.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-analytics,version=1.1 | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }
```

此下列的範例輸出顯示相互 TLS 會強制執行每個我們上面的查詢。 輸出也會顯示原則並強制執行相互 TLS 的目的地規則：

```console
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>推出應用程式的 Canary 版本

現在讓我們來部署新版本`2.0`的`voting-app`， `voting-analytics`，和`voting-storage`元件。 新`voting-storage`元件使用 MySQL，而不是 Redis，而`voting-app`並`voting-analytics`元件會更新為允許它們使用新`voting-storage`元件。

`voting-app`元件現在支援的功能旗標。 此功能旗標可讓您針對某部分的使用者測試 Istio 的 Canary 版本功能。

下圖顯示您必須執行本節的結尾。

* 版本`1.0`的`voting-app`元件、 版本`1.1`的`voting-analytics`元件和版本`1.0`的`voting-storage`元件可以彼此通訊。
* 版本`2.0`的`voting-app`元件、 版本`2.0`的`voting-analytics`元件和版本`2.0`的`voting-storage`元件可以彼此通訊。
* 版本`2.0`的`voting-app`元件只會有一組特定的功能旗標的使用者存取。 這項變更可透過 Cookie 使用功能旗標來管理。

![AKS 投票應用程式元件和路由。](media/istio/components-and-routing-03.png)

首先，請更新 Istio 目的地規則和虛擬服務，以因應這些新元件的需求。 這些更新可確保您不會錯誤地將流量路由至新元件，且使用者不會獲得非預期的存取：

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

下列範例輸出顯示的目的地規則 」 和 「 虛擬服務更新：

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

接下來，讓我們新增新版的 Kubernetes 物件`2.0`元件。 您也更新`voting-storage`服務，以包含`3306`適用於 MySQL 的連接埠：

```azurecli
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

下列範例輸出顯示 Kubernetes 物件已成功更新或建立：

```console
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

等到所有版本`2.0`pod 執行時。 使用[kubectl get pods] [ kubectl-get]命令來檢視中的所有 pod`voting`命名空間：

```azurecli
kubectl get pods --namespace voting
```

您現在應該能夠在版本之間切換`1.0`和 版本`2.0`(canary) 投票應用程式。 畫面底部的功能旗標切換會設定 Cookie。 會使用此 cookie`voting-app`將使用者路由至新版本的虛擬服務`2.0`。

![1\.0 版的 AKS 投票應用程式 -「不會」設定功能旗標。](media/istio/canary-release-01.png)

![2\.0 版的 AKS 投票應用程式 -「會」設定功能旗標。](media/istio/canary-release-02.png)

兩個應用程式版本的投票計數並不相同。 此差異凸顯出您使用了兩個不同的儲存體後端。

## <a name="finalize-the-rollout"></a>完成推出

一旦您已成功通過測試的 canary 版本，更新`voting-app`將所有流量都路由到版本的虛擬服務`2.0`的`voting-app`元件。 所有使用者，然後都查看版本`2.0`應用程式，不論是否設定功能旗標：

![AKS 投票應用程式元件和路由。](media/istio/components-and-routing-04.png)

更新所有目的地規則，以移除您不想再啟用的元件版本。 接著，更新所有虛擬服務以停止參考這些版本。

由於再也不會有任何流量進入任何較舊版本的元件，您現在可以安全地刪除這些元件的所有部署。

![AKS 投票應用程式元件和路由。](media/istio/components-and-routing-05.png)

您現在已成功推出新版的 AKS 投票應用程式。

## <a name="clean-up"></a>清除 

您可以在此案例中移除我們所用的 AKS 投票應用程式，從您的 AKS 叢集，藉由刪除`voting`命名空間，如下所示：

```azurecli
kubectl delete namespace voting
```

下列範例輸出顯示，已從您的 AKS 叢集移除的 AKS 投票應用程式的所有元件。

```console
namespace "voting" deleted
```

## <a name="next-steps"></a>後續步驟

您可以使用 [Istio Bookinfo 應用程式範例][istio-bookinfo-example]探索其他案例。

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md
