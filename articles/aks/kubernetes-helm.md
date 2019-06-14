---
title: 在 Azure 上的 Kubernetes 中運用 Helm 部署容器
description: 了解如何部署 Azure Kubernetes Service (AKS) 叢集中的容器使用 Helm 封裝工具
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 05/23/2019
ms.author: zarhoads
ms.openlocfilehash: 76a5391cbe142851d9b1f60ea9346af2e7a35d6a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66392147"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中使用 Helm 安裝應用程式

[Helm][helm] 是開放原始碼的封裝工具，可協助您安裝和管理 Kubernetes 應用程式的生命週期。 Helm 類似於 *APT* 和 *Yum* 等 Linux 套件管理工具，可用於管理 Kubernetes 圖表 (即預先設定的 Kubernetes 資源套件)。

本文示範如何在 AKS 上的 Kubernetes 叢集中設定及使用 Helm。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您也需要 Helm CLI 安裝，也就是在您的開發系統執行的用戶端。 它可讓您啟動、 停止及管理應用程式，使用 Helm。 如果您使用 Azure Cloud Shell，則已安裝 Helm CLI。 如需本機平台的安裝指示，請參閱[安裝 Helm][helm-install]。

> [!IMPORTANT]
> Helm 被要在 Linux 節點上執行。 如果您將 Windows 伺服器節點，在您的叢集時，您必須確定 Helm pod 只會排程在 Linux 節點上執行。 您也需要確保您安裝任何 Helm 圖表也會排程在正確的節點上執行。 在此命令本文使用[節點選取器][ k8s-node-selector]確保 pod 會排定至正確的節點，但並非所有的 Helm 圖表可能會公開 （expose） 的節點選取器。 您也可以考慮使用您的叢集上的其他選項，例如[taints][taints]。

## <a name="create-a-service-account"></a>建立服務帳戶

在已啟用 RBAC 的 AKS 叢集中部署 Helm 之前，您需要適用於 Tiller 服務的服務帳戶與角色繫結。 如需在已啟用 RBAC 的叢集中保護 Helm / Tiller 的詳細資訊，請參閱 [Tiller、命名空間和 RBAC][tiller-rbac]。 如果 AKS 叢集未啟用 RBAC，請略過此步驟。

建立名為 `helm-rbac.yaml` 的檔案，然後將下列 YAML 複製進來：

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

使用 `kubectl apply` 命令來建立服務帳戶和角色繫結：

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="secure-tiller-and-helm"></a>保護 Tiller 和 Helm

Helm 用戶端和 Tiller 服務會使用 TLS/SSL 互相驗證及通訊。 這個驗證方法有助於保護 Kubernetes 叢集，以及驗證可以部署哪些服務。 為了提高安全性，您可以產生自己的簽署憑證。 每個 Helm 使用者會收到自己的用戶端憑證，而 Tiller 會在 Kubernetes 叢集中初始化，並且套用憑證。 如需詳細資訊，請參閱[在 Helm 與 Tiller 之間使用 TLS/SSL][helm-ssl]。

使用了已啟用 RBAC 的 Kubernetes 叢集，您就可以控制 Tiller 對於叢集的存取權層級。 您可以定義 Kubernetes 命名空間 (Tiller 部署於其中)，並且限制 Tiller 後續可以在其中部署資源的命名空間。 這個方法可讓您在不同的命名空間中建立 Tiller 執行個體並且限制部署界限，以及將 Helm 用戶端使用者的範圍限制為特定命名空間。 如需詳細資訊，請參閱 [Helm 角色型存取控制][helm-rbac]。

## <a name="configure-helm"></a>設定 Helm

若要將基本 Tiller 部署到 AKS 叢集，請使用 [helm init][helm-init] 命令。 如果您的叢集並未啟用 RBAC，請移除 `--service-account` 引數和值。 如果您已為 Tiller 和 Helm 設定 TLS/SSL，請略過這個基本初始化步驟，改為提供必要的 `--tiller-tls-`，如接下來的範例所示。

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

如果您已在 Helm 與 Tiller 之間設定 TLS/SSL，請提供 `--tiller-tls-*` 參數和您自己的憑證名稱，如下列範例所示：

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --service-account tiller \
    --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="find-helm-charts"></a>尋找 Helm 圖表

使用 Helm 圖表將應用程式部署到 Kubernetes 叢集中。 若要搜尋預先建立的 Helm 圖表，請使用 [helm search][helm-search] 命令：

```console
helm search
```

下列扼要範例輸出顯示一些可供使用的 Helm 圖表：

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

若要更新圖表清單，請使用 [helm repo update][helm-repo-update] 命令。 以下範例顯示成功存放庫更新的情況：

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>執行 Helm 圖表

若要使用 Helm 來安裝圖表，請使用 [helm install][helm-install] 命令並且指定要安裝的圖表名稱。 若要查看作用中安裝 Helm 圖表，讓我們來安裝基本的 nginx 部署使用 Helm 圖表。 如果您已設定 TLS/SSL，請新增 `--tls` 參數以使用 Helm 用戶端憑證。

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

下列扼要範例輸出顯示 Helm 圖表所建立的 Kubernetes 資源部署狀態：

```
$ helm install stable/nginx-ingress --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME:   flailing-alpaca
LAST DEPLOYED: Thu May 23 12:55:21 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                      DATA  AGE
flailing-alpaca-nginx-ingress-controller  1     0s

==> v1/Pod(related)
NAME                                                            READY  STATUS             RESTARTS  AGE
flailing-alpaca-nginx-ingress-controller-56666dfd9f-bq4cl       0/1    ContainerCreating  0         0s
flailing-alpaca-nginx-ingress-default-backend-66bc89dc44-m87bp  0/1    ContainerCreating  0         0s

==> v1/Service
NAME                                           TYPE          CLUSTER-IP  EXTERNAL-IP  PORT(S)                     AGE
flailing-alpaca-nginx-ingress-controller       LoadBalancer  10.0.109.7  <pending>    80:31219/TCP,443:32421/TCP  0s
flailing-alpaca-nginx-ingress-default-backend  ClusterIP     10.0.44.97  <none>       80/TCP                      0s
...
```

它需要幾分鐘的兩個*EXTERNAL-IP*會填入，並可讓您存取的網頁瀏覽器的 nginx 輸入控制器服務的位址。

## <a name="list-helm-releases"></a>列出 Helm 版本

若要查看安裝於叢集上的版本清單，請使用 [helm list][helm-list] 命令。 下列範例會顯示在上一個步驟中部署 nginx 輸入版本。 如果您已設定 TLS/SSL，請新增 `--tls` 參數以使用 Helm 用戶端憑證。

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

## <a name="clean-up-resources"></a>清除資源

部署 Helm 圖表時會建立一些 Kubernetes 資源。 這些資源包含 Pod、部署和服務。 若要清除這些資源，請使用 `helm delete` 命令，並指定在先前 `helm list` 命令中找到的版本名稱。 下列範例會刪除名為 release *flailing alpaca*:

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>後續步驟

如需使用 Helm 管理 Kubernetes 應用程式部署的詳細資訊，請參閱 Helm 文件。

> [!div class="nextstepaction"]
> [Helm 文件][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
[helm-ssl]: https://docs.helm.sh/using_helm/#using-ssl-between-helm-and-tiller

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
[taints]: operator-best-practices-advanced-scheduler.md