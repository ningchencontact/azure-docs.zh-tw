---
title: 在 Azure Kubernetes Service 中安裝 Hashicorp Consul (AKS)
description: 瞭解如何安裝和使用 Consul, 以在 Azure Kubernetes Service (AKS) 叢集中建立服務網格
services: container-service
author: dstrebel
ms.service: container-service
ms.topic: article
ms.date: 06/19/2019
ms.author: dastrebe
ms.openlocfilehash: 7acd2533079499091427c7e63741b9c587ee29e5
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2019
ms.locfileid: "70189220"
---
# <a name="install-and-use-consul-connect-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service 中安裝和使用 Consul Connect (AKS)

[Consul][consul-github]是一種開放原始碼服務網格, 可在 Kubernetes 叢集中跨微服務提供一組重要的功能。 這些功能包括服務探索、健康情況檢查、服務分割和可檢視性。 如需有關 Consul 的詳細資訊, 請參閱官方[什麼是 Consul？][consul-docs-concepts]檔。

本文說明如何安裝 Consul。 Consul 元件會安裝在 AKS 上的 Kubernetes 叢集中。

> [!NOTE]
> 這些指示參考 Consul 版本`1.5`。
>
> Hashicorp 小組`1.5.x`已針對 Kubernetes 版本`1.12`( `1.14`、) `1.14`測試 Consul 版本。 您可以在[GitHub Consul 版本][consul-github-releases]中找到其他 Consul 版本, 並在[Consul 版本][consul-release-notes]資訊中找到每個版本的相關資訊。

在本文中，您將了解：

> [!div class="checklist"]
> * 在 AKS 上安裝 Consul 元件
> * 驗證 Consul 安裝
> * 從 AKS 卸載 Consul

## <a name="before-you-begin"></a>開始之前

本文中詳述的步驟假設您已`1.11` `kubectl`建立 AKS 叢集 (已啟用 RBAC 的 Kubernetes 和更新版本), 並已建立與叢集的連線。 如果您需要這些專案的協助, 請參閱[AKS 快速入門][aks-quickstart]。

您將需要[Helm][helm] , 才能遵循這些指示並安裝 Consul。 建議您在叢集中正確安裝`2.12.2`和設定版本或更新版本。 如果您需要安裝 Helm 的協助, 請參閱[AKS Helm 安裝指引][helm-install]。 所有 Consul pod 也必須排程在 Linux 節點上執行。

本文將 Consul 安裝指引分成幾個不同的步驟。 最終結果在結構上與官方 Consul 安裝[指導][consul-install-k8]方針相同。

### <a name="install-the-consul-components-on-aks"></a>在 AKS 上安裝 Consul 元件

我們會先複製 Kubernetes GitHub 存放庫上的官方 HashiCorp Consul。

```bash
git clone https://github.com/hashicorp/consul-helm.git
cd consul-helm
```

接著, 我們必須建立 Consul 安裝的自訂 Helm 值檔案。 在安裝 Consul 之前, 請先建立下列自訂值檔案。

```bash
vim consul-values.yaml
```

然後在 consul yaml 檔案中複製並貼上下列值。

```yaml
# Sets datacenter name and version Consul to use
global:
  datacenter: dc1
  image: "consul:1.5.2"

# Enables proxies to be injected into pods
connectInject:
  enabled: true

# Enables UI on ClusterIP
ui:
  service:
    type: "ClusterIP"

# Enables GRCP that is required for connectInject
client:
  enabled: true
  grpc: true

# Sets replicase to 3 for HA
server:
  replicas: 3
  bootstrapExpect: 1
  disruptionBudget:
    enabled: true
    maxUnavailable: 1

# Syncs Kubernetes service discovery with Consul
syncCatalog:
  enabled: true
```

既然我們已經有自訂值檔案, 我們可以將 Consul 安裝到我們的 AKS 叢集中

Bash

```bash
helm install -f consul-values.yaml --name consul --namespace consul .
```
`Consul` Helm 圖會部署一些物件。 您可以從上述`helm install`命令的輸出查看清單。 Consul 元件的部署可能需要4到5分鐘的時間才能完成, 視您的叢集環境而定。

> [!NOTE]
> 所有 Consul pod 都必須排程在 Linux 節點上執行。 如果您的叢集上有 Linux 節點集區以外的 Windows Server 節點集區, 請確認所有 Consul pod 已排程在 Linux 節點上執行。

此時, 您已將 Consul 部署至 AKS 叢集。 為了確保我們已成功部署 Consul, 讓我們繼續進行下一節, 以驗證 Consul 安裝。

## <a name="validate-the-consul-installation"></a>驗證 Consul 安裝

先確認是否已建立預期的服務。 使用[kubectl get svc][kubectl-get]命令來查看執行中的服務。 查詢命名空間, `consul` Helm 圖表會在其中安裝 Consul 元件: `consul`

```console
kubectl get svc --namespace consul
```

下列範例輸出會顯示現在應該正在執行的服務：

```console
NAME                                 TYPE           CLUSTER-IP     EXTERNAL-IP             PORT(S)                                                                   AGE
consul                               ExternalName   <none>         consul.service.consul   <none>                                                                    38m
consul-consul-connect-injector-svc   ClusterIP      10.0.89.113    <none>                  443/TCP                                                                   40m
consul-consul-dns                    ClusterIP      10.0.166.82    <none>                  53/TCP,53/UDP                                                             40m
consul-consul-server                 ClusterIP      None           <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   40m
consul-consul-ui                     ClusterIP      10.0.117.164   <none>                  80/TCP                                                                    40m
```

接著，確認是否已建立所需的 Pod。 使用[kubectl get][kubectl-get] pod 命令, 然後重新查詢`consul`命名空間:

```console
kubectl get pods --namespace consul
```

下列範例輸出會顯示正在執行的 Pod：

```console
NAME                                                              READY   STATUS    RESTARTS   AGE
consul-consul-7cc9v                                               1/1     Running   0          37m
consul-consul-7klg7                                               1/1     Running   0          37m
consul-consul-connect-injector-webhook-deployment-57f88df8hgmfs   1/1     Running   0          37m
consul-consul-lq8qb                                               1/1     Running   0          37m
consul-consul-server-0                                            1/1     Running   0          37m
consul-consul-server-1                                            1/1     Running   0          36m
consul-consul-server-2                                            1/1     Running   0          36m
consul-consul-sync-catalog-7cf7d5bfff-jjbjv                       1/1     Running   2          37m
```

 所有 pod 都應該顯示狀態`Running`。 如果您的 Pod 沒有這些狀態，請等候一兩分鐘直到其成為該狀態。 如果有任何 pod 回報問題, 請使用[kubectl 描述 pod][kubectl-describe]命令來檢查其輸出和狀態。

## <a name="accessing-the-consul-ui"></a>存取 Consul UI

Consul UI 已安裝在上面的安裝程式中, 提供 Consul 的 UI 型設定。 Consul 的 UI 不會透過外部 ip 位址公開公開。 若要存取附加元件使用者介面, 請使用[kubectl 埠轉送][kubectl-port-forward]命令。 此命令會在您的用戶端電腦與 AKS 叢集中的相關 pod 之間建立安全連線。

```bash
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```
您現在可以開啟瀏覽器並將它`http://localhost:8080/ui`指向, 以開啟 Consul UI。 當您開啟 UI 時, 應該會看到下列內容:

![Consul UI](./media/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>從 AKS 卸載 Consul

> [!WARNING]
> 從執行中的系統刪除 Consul 可能會導致您的服務之間發生流量相關的問題。 請確定您已在不 Consul 的情況下, 將您的系統布建為正常運作, 再繼續進行。

### <a name="remove-consul-components-and-namespace"></a>移除 Consul 元件和命名空間

若要從您的 AKS 叢集中移除 Consul, 請使用下列命令。 這些`helm delete`命令將會`consul`移除圖表, 而`kubectl delete ns`命令會移除`consul`命名空間。

```bash
helm delete --purge consul
kubectl delete ns consul
```

## <a name="next-steps"></a>後續步驟

若要探索 Consul 的更多安裝和設定選項, 請參閱下列官方 Consul 文章:

- [Consul-Helm 安裝指南][consul-install-k8]
- [Consul-Helm 安裝選項][consul-install-helm-options]

您也可以遵循使用[Consul 範例應用程式][consul-app-example]的其他案例。

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://www.consul.io/docs/platform/k8s/run.html
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-app-example]: https://github.com/hashicorp/demo-consul-101/tree/master/k8s
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
