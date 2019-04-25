---
title: 自訂 CoreDNS 的 Azure Kubernetes Service (AKS)
description: 了解如何自訂 CoreDNS 新增子網域，或擴充 Azure Kubernetes Service (AKS) 使用的自訂 DNS 端點
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 03/15/2019
ms.author: jnoller
ms.openlocfilehash: 9186c5ff7c6fbc68487a1ccff0fc1d2d1478df79
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60466439"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>自訂 CoreDNS 搭配 Azure Kubernetes Service

Azure Kubernetes Service (AKS) 使用[CoreDNS] [ coredns]叢集 DNS 管理與使用所有解析專案*1.12.x*和更高版本的叢集。 先前已使用 kubernetes dns 專案。 這個 kube dns 專案現在已被取代。 如需有關 CoreDNS 自訂和 Kubernetes 的詳細資訊，請參閱[上游的正式文件][corednsk8s]。

AKS 為受管理的服務，您無法修改 CoreDNS 的主要組態 ( *CoreFile*)。 相反地，您可以使用 Kubernetes *configmap 掛接*覆寫預設設定。 若要查看預設 AKS CoreDNS ConfigMaps，使用`kubectl get configmaps coredns -o yaml`命令。

本文說明如何使用 ConfigMaps CoreDNS AKS 中的基本自訂選項。

> [!NOTE]
> `kube-dns` 提供不同[自訂選項][ kubednsblog]透過 Kubernetes 組態對應。 是 CoreDNS**不**kube dns 回溯相容。 您先前使用的任何自訂項目必須更新與 CoreDNS 搭配使用。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱 [使用 Azure CLI] AKS 快速入門 [aks-快速入門-cli] [使用 Azure 入口網站] 或 [aks 快速入門-入口網站]。

## <a name="change-the-dns-ttl"></a>變更 DNS TTL。

您可能想要在 CoreDNS 中設定的其中一個案例是降低或提高時間 (TTL) 設定快取的 DNS 名稱。 在此範例中，讓我們改變的 TTL 值。 根據預設，此值為 30 秒。 如需有關 DNS 快取選項的詳細資訊，請參閱 <<c0> [ 官方 CoreDNS 文件][dnscache]。

在下列範例 configmap 掛接，請注意`name`值。 根據預設，CoreDNS 不支援這種類型的自訂當您修改 CoreFile 本身。 使用 AKS *coredns 自訂*configmap 掛接，以納入自己的組態和主要 CoreFile 之後載入。

下列範例會告知 CoreDNS，所有網域 (所示`.`中`.:53`)，在連接埠 53 （預設 DNS 連接埠），將快取 TTL 設定為 15 (`cache 15`)。 建立名為`coredns-custom.json`並貼上下列範例組態：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    .:53 {
        cache 15  # this is our new cache value
    }
```

建立使用 ConfigMap [kubectl 套用 configmap 掛接][ kubectl-apply]命令並指定您的 YAML 資訊清單的名稱：

```console
kubectl apply configmap coredns-custom.json
```

若要確認已套用自訂項目，請使用[kubectl get configmaps] [ kubectl-get]並指定您*coredns 自訂*configmap 掛接：

```
kubectl get configmaps coredns-custom -o yaml
```

現在會強制重新載入 configmap 掛接 CoreDNS。 [Kubectl 刪除 pod] [ kubectl delete]命令不具破壞性，並不會造成停機時間。 `kube-dns` Pod 會被刪除，和 Kubernetes 排程器再重新建立它們。 這些新的 pod 包含 TTL 值的變更。

```console
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="rewrite-dns"></a>請重寫 DNS

您有一個案例是執行在即時 DNS 名稱重寫。 在下列範例中，取代`<domain to be written>`以您自己的完整的網域名稱。 建立名為`coredns-custom.json`並貼上下列範例組態：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        proxy .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

如同先前的範例中，建立使用 ConfigMap [kubectl 套用 configmap 掛接][ kubectl-apply]命令並指定您的 YAML 資訊清單的名稱。 然後，強制重新載入 configmap 掛接使用 CoreDNS [kubectl 刪除 pod] [ kubectl delete] Kubernetes 排程器重新加以建立：

```console
kubectl apply configmap coredns-custom.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="custom-proxy-server"></a>自訂的 proxy 伺服器

如果您需要指定的 proxy 伺服器，您的網路流量，您可以建立自訂 DNS ConfigMap。 在下列範例中，更新`proxy`姓名和地址與您自己環境的值。 建立名為`coredns-custom.json`並貼上下列範例組態：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    .:53 {
        proxy foo.com 1.1.1.1
    }
```

如同先前的範例中，建立使用 ConfigMap [kubectl 套用 configmap 掛接][ kubectl-apply]命令並指定您的 YAML 資訊清單的名稱。 然後，強制重新載入 configmap 掛接使用 CoreDNS [kubectl 刪除 pod] [ kubectl delete] Kubernetes 排程器重新加以建立：

```console
kubectl apply configmap coredns-custom.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>使用自訂網域

若要設定自訂網域只能在內部解析。 例如，您可能想要解析自訂網域*puglife.local*，這不是有效的最上層網域。 沒有自訂網域 configmap 掛接，AKS 叢集無法解析的位址。

在下列範例中，更新的自訂網域和 IP 位址將流量導向您自己的環境的值。 建立名為`coredns-custom.json`並貼上下列範例組態：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: |
    puglife.local:53 {
        errors
        cache 30
        proxy . 192.11.0.1  # this is my test/dev DNS server
    }
```

如同先前的範例中，建立使用 ConfigMap [kubectl 套用 configmap 掛接][ kubectl-apply]命令並指定您的 YAML 資訊清單的名稱。 然後，強制重新載入 configmap 掛接使用 CoreDNS [kubectl 刪除 pod] [ kubectl delete] Kubernetes 排程器重新加以建立：

```console
kubectl apply configmap coredns-custom.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="stub-domains"></a>虛設常式網域

CoreDNS 也可用來設定虛設常式網域。 在下列範例中，更新的自訂網域和 IP 位址取代為您自己的環境的值。 建立名為`coredns-custom.json`並貼上下列範例組態：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
    abc.com:53 {
        errors
        cache 30
        proxy . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        proxy . 2.3.4.5
    }
```

如同先前的範例中，建立使用 ConfigMap [kubectl 套用 configmap 掛接][ kubectl-apply]命令並指定您的 YAML 資訊清單的名稱。 然後，強制重新載入 configmap 掛接使用 CoreDNS [kubectl 刪除 pod] [ kubectl delete] Kubernetes 排程器重新加以建立：

```console
kubectl apply configmap coredns-custom.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="next-steps"></a>後續步驟

本文說明 CoreDNS 自訂範例的案例。 CoreDNS 專案上的資訊，請參閱[上游專案頁面中 CoreDNS][coredns]。

若要深入了解核心網路概念，請參閱[網路 AKS 中的應用程式的概念][concepts-network]。

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete

<!-- LINKS - external -->
[concepts-network]: concepts-network.md