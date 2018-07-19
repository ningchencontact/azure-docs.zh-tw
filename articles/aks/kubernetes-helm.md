---
title: 在 Azure 上的 Kubernetes 中運用 Helm 部署容器
description: 使用 Helm 套件工具，將容器部署在 AKS 的 Kubernetes 叢集中。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 531e6d9368b2bf91c48fd41b1e9330879b0df49a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101347"
---
# <a name="use-helm-with-azure-kubernetes-service-aks"></a>使用 Helm 搭配 Azure Kubernetes Service (AKS)

[Helm][helm] 是開放原始碼的封裝工具，可協助您安裝和管理 Kubernetes 應用程式的生命週期。 Helm 類似於 *APT* 和 *Yum* 等 Linux 套件管理工具，可用於管理 Kubernetes 圖表 (即預先設定的 Kubernetes 資源套件)。

本文將逐步引導您在 AKS 的 Kubernetes 叢集中設定及使用 Helm。

## <a name="before-you-begin"></a>開始之前

本文件中詳述的步驟假設您已建立 AKS 叢集，並建立與叢集的 kubectl 連線。 如果您需要這些項目，請參閱 [AKS 快速入門][aks-quickstart]。

## <a name="install-helm-cli"></a>安裝 Helm CLI

Helm CLI 是在開發系統上執行的用戶端，可讓您透過 Helm 啟動、停止和管理應用程式。

如果您是使用 Azure CloudShell，即已安裝 Helm CLI。 若要在 Mac 上安裝 Helm CLI，請使用 `brew`。 如需其他安裝選項，請參閱[安裝 Helm][helm-install-options]。

```console
brew install kubernetes-helm
```

輸出：

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="create-service-account"></a>建立服務帳戶

在已啟用 RBAC 的叢集中設定 Helm 之前，您需要適用於 Tiller 服務的服務帳戶與角色繫結。 如需在已啟用 RBAC 的叢集中保護 Helm / Tiller 的詳細資訊，請參閱 [Tiller、命名空間和 RBAC][tiller-rbac]。 請注意，如果您的叢集未啟用 RBAC，請略過此步驟。

建立名為 `helm-rbac.yaml` 的檔案，然後將下列 YAML 複製進來。

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
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

使用 `kubectl create` 命令建立服務帳戶和角色繫結。

```
kubectl create -f helm-rbac.yaml
```

使用已啟用 RBAC 的叢集時，您可選擇 Tiller 所具有的叢集存取層級。 如需設定選項的詳細資訊，請參閱 [Helm：角色型存取控制][helm-rbac]。

## <a name="configure-helm"></a>設定 Helm

現在使用 [helm init][helm-init] 命令安裝 Tiller。 如果您的叢集並未啟用 RBAC，請移除 `--service-account` 引數和值。

```
helm init --service-account tiller
```

## <a name="find-helm-charts"></a>尋找 Helm 圖表

使用 Helm 圖表將應用程式部署到 Kubernetes 叢集中。 若要搜尋預先建立的 Helm 圖表，請使用 [helm search][helm-search] 命令。

```azurecli-interactive
helm search
```

輸出看起來與下列內容類似，不過多了許多圖表。

```
NAME                            VERSION DESCRIPTION
stable/acs-engine-autoscaler    2.0.0   Scales worker nodes within agent pools
stable/artifactory              6.1.0   Universal Repository Manager supporting all maj...
stable/aws-cluster-autoscaler   0.3.1   Scales worker nodes within autoscaling groups.
stable/buildkite                0.2.0   Agent for Buildkite
stable/centrifugo               2.0.0   Centrifugo is a real-time messaging server.
stable/chaoskube                0.5.0   Chaoskube periodically kills random pods in you...
stable/chronograf               0.3.0   Open-source web application written in Go and R...
stable/cluster-autoscaler       0.2.0   Scales worker nodes within autoscaling groups.
stable/cockroachdb              0.5.0   CockroachDB is a scalable, survivable, strongly...
stable/concourse                0.7.0   Concourse is a simple and scalable CI system.
stable/consul                   0.4.1   Highly available and distributed service discov...
stable/coredns                  0.5.0   CoreDNS is a DNS server that chains middleware ...
stable/coscale                  0.2.0   CoScale Agent
stable/dask-distributed         2.0.0   Distributed computation in Python
stable/datadog                  0.8.0   DataDog Agent
...
```

若要更新圖表清單，請使用 [helm repo update][helm-repo-update] 命令。

```azurecli-interactive
helm repo update
```

輸出：

```
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>執行 Helm 圖表

若要使用 Helm 圖表部署 Wordpress，請使用 [helm install][helm-install] 命令。

```azurecli-interactive
helm install stable/wordpress
```

輸出看起來類似下列內容，但包含如何使用 Kubernetes 部署指示等的其他資訊。

```
NAME:   bilging-ibex
LAST DEPLOYED: Tue Jun  5 14:31:49 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                     READY  STATUS   RESTARTS  AGE
bilging-ibex-mariadb-7557b5474-dmdxn     0/1    Pending  0         1s
bilging-ibex-wordpress-7494c545fb-tskhz  0/1    Pending  0         1s

==> v1/Secret
NAME                    TYPE    DATA  AGE
bilging-ibex-mariadb    Opaque  2     1s
bilging-ibex-wordpress  Opaque  2     1s

==> v1/ConfigMap
NAME                        DATA  AGE
bilging-ibex-mariadb        1     1s
bilging-ibex-mariadb-tests  1     1s

==> v1/PersistentVolumeClaim
NAME                    STATUS   VOLUME   CAPACITY  ACCESS MODES  STORAGECLASS  AGE
bilging-ibex-mariadb    Pending  default  1s
bilging-ibex-wordpress  Pending  default  1s

==> v1/Service
NAME                    TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)                     AGE
bilging-ibex-mariadb    ClusterIP     10.0.76.164   <none>       3306/TCP                    1s
bilging-ibex-wordpress  LoadBalancer  10.0.215.250  <pending>    80:30934/TCP,443:31134/TCP  1s

==> v1beta1/Deployment
NAME                    DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
bilging-ibex-mariadb    1        1        1           0          1s
bilging-ibex-wordpress  1        1        1           0          1s
...
```

## <a name="list-helm-releases"></a>列出 Helm 版本

若要查看安裝於叢集上的版本清單，請使用 [helm list][helm-list] 命令。

```azurecli-interactive
helm list
```

輸出：

```
NAME            REVISION    UPDATED                     STATUS      CHART           NAMESPACE
bilging-ibex    1           Tue Jun  5 14:31:49 2018    DEPLOYED    wordpress-1.0.9 default
```

## <a name="next-steps"></a>後續步驟

如需管理 Kubernetes 圖表的詳細資訊，請參閱 Helm 文件。

> [!div class="nextstepaction"]
> [Helm 文件][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/helm/#helm-install
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
