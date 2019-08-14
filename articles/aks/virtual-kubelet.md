---
title: 在 Azure Kubernetes Service (AKS) 叢集中執行 Virtual Kubelet
description: 了解如何將 Virtual Kubelet 與 Azure Kubernetes Service (AKS) 搭配使用，以在 Azure Container 執行個體上執行 Linux 和 Windows 容器。
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: f18992be353d2d6cc739412d98ccd97d5e78d4c7
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2019
ms.locfileid: "67613865"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>將 Virtual Kubelet 與 Azure Kubernetes Service (AKS) 搭配使用

Azure 容器執行個體 (ACI) 可提供託管環境，以便在 Azure 中執行容器。 使用 ACI 時，不需要管理基礎計算基礎結構，Azure 會為您處理此管理工作。 在 ACI 中執行時容器時，系統會針對每個執行中的容器以秒計費。

使用 Azure 容器執行個體的 Virtual Kubelet 提供者時，Linux 和 Windows 容器都可以在容器執行個體上排程，宛如標準 Kubernetes 節點一樣。 此組態可讓您善用 Kubernetes 功能，以及容器執行個體的管理價值和成本效益。

> [!NOTE]
> AKS 現已內建支援排程 ACI (稱為*虛擬節點*) 上的容器。 這些虛擬節點目前支援 Linux 容器執行個體。 如果您需要排程 Windows 容器執行個體，您可以繼續使用 Virtual Kubelet。 否則，您應該使用虛擬節點，而不是如本文所述的手動 Virtual Kubelet 指示。 您可以使用[Azure CLI][virtual-nodes-cli]或[Azure 入口網站][virtual-nodes-portal]開始使用虛擬節點。
>
> Virtual Kubelet 是實驗性開放原始碼專案，應該如此使用。 若要參與、提出問題, 以及深入瞭解 virtual kubelet, 請參閱[Virtual Kubelet GitHub 專案][vk-github]。

## <a name="before-you-begin"></a>開始之前

本文件假設您有 AKS 叢集。 如果您需要 AKS 叢集, 請參閱[Azure Kubernetes Service (AKS) 快速入門][aks-quick-start]。

您也需要 Azure CLI 版**2.0.65**或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

若要安裝虛擬 Kubelet, 請在您的 AKS 叢集中安裝及設定[Helm][aks-helm] 。 請確定您的 Tiller 已[設定為與 KUBERNETES RBAC 搭配使用](#for-rbac-enabled-clusters)(如有需要)。

### <a name="register-container-instances-feature-provider"></a>註冊容器實例功能提供者

如果您先前未使用 Azure 容器實例 (ACI) 服務, 請向您的訂用帳戶註冊服務提供者。 您可以使用[az provider list][az-provider-list]命令來檢查 ACI 提供者註冊的狀態, 如下列範例所示:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance* 提供者應該回報為 *Registered*，如以下範例輸出所示：

```console
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

如果提供者顯示為*NotRegistered*, 請使用[az provider register][az-provider-register]註冊提供者, 如下列範例所示:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>對於已啟用 RBAC 的叢集

如果已啟用 RBAC 的 AKS 叢集，您必須建立服務帳戶和角色繫結，以與 Tiller 搭配使用。 如需詳細資訊, 請參閱[Helm 以角色為基礎的存取控制][helm-rbac]。 若要建立服務帳戶和角色繫結，請建立名為 *rbac-virtual-kubelet.yaml* 的檔案，並貼上下列定義：

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

使用[kubectl apply][kubectl-apply]套用服務帳戶和系結, 並指定您的*rbac-kubelet yaml*檔案, 如下列範例所示:

```console
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

設定 Helm 以使用 tiller 服務帳戶：

```console
helm init --service-account tiller
```

您現在可以繼續將 Virtual Kubelet 安裝至 AKS 叢集。

## <a name="installation"></a>安裝

使用[az aks install-connector][aks-install-connector]命令來安裝虛擬 Kubelet。 下列範例可部署 Linux 與 Windows 連接器。

```azurecli-interactive
az aks install-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

這些引數適用于[az aks install-connector][aks-install-connector]命令。

| 引數： | 描述 | 必要項 |
|---|---|:---:|
| `--connector-name` | ACI 連接器的名稱。| 是 |
| `--name` `-n` | 受控叢集的名稱。 | 是 |
| `--resource-group` `-g` | 資源群組的名稱。 | 是 |
| `--os-type` | 容器執行個體的作業系統類型。 允許的值：兩者、Linux、Windows。 預設值：Linux。 | 否 |
| `--aci-resource-group` | 要在其中建立 ACI 容器群組的資源群組。 | 否 |
| `--location` `-l` | 要建立 ACI 容器群組的位置。 | 否 |
| `--service-principal` | 用於向 Azure API 驗證的服務主體。 | 否 |
| `--client-secret` | 與服務主體相關聯的祕密。 | 否 |
| `--chart-url` | 安裝 ACI 連接器的 Helm 圖表 URL。 | 否 |
| `--image-tag` | Virtual Kubelet 容器映像的映像標記。 | 否 |

## <a name="validate-virtual-kubelet"></a>驗證 Virtual Kubelet

若要驗證是否已安裝虛擬 Kubelet, 請使用[kubectl get 節點][kubectl-get]命令傳回 Kubernetes 節點清單:

```console
$ kubectl get nodes

NAME                                             STATUS   ROLES   AGE   VERSION
aks-nodepool1-56577038-0                         Ready    agent   11m   v1.12.8
virtual-kubelet-virtual-kubelet-linux-eastus     Ready    agent   39s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
virtual-kubelet-virtual-kubelet-windows-eastus   Ready    agent   37s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
```

## <a name="run-linux-container"></a>執行 Linux 容器

建立名為 `virtual-kubelet-linux.yaml` 的檔案，然後將下列 YAML 複製進來。 請注意, [nodeSelector][node-selector]和[toleration][toleration]正用來排程節點上的容器。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: linux
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

使用[kubectl create][kubectl-create]命令來執行應用程式。

```console
kubectl create -f virtual-kubelet-linux.yaml
```

使用[kubectl get][kubectl-get] pod 命令`-o wide`搭配引數, 以輸出具有已排程節點的 pod 清單。 請注意，`aci-helloworld` pod 已排定在 `virtual-kubelet-virtual-kubelet-linux` 節點上。

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
aci-helloworld-7b9ffbf946-rx87g   1/1     Running   0          22s     52.224.147.210   virtual-kubelet-virtual-kubelet-linux-eastus
```

## <a name="run-windows-container"></a>執行 Windows 容器

建立名為 `virtual-kubelet-windows.yaml` 的檔案，然後將下列 YAML 複製進來。 請注意, [nodeSelector][node-selector]和[toleration][toleration]正用來排程節點上的容器。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nanoserver-iis
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: microsoft/iis:nanoserver
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: windows
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

使用[kubectl create][kubectl-create]命令來執行應用程式。

```console
kubectl create -f virtual-kubelet-windows.yaml
```

使用[kubectl get][kubectl-get] pod 命令`-o wide`搭配引數, 以輸出具有已排程節點的 pod 清單。 請注意，`nanoserver-iis` pod 已排定在 `virtual-kubelet-virtual-kubelet-windows` 節點上。

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
nanoserver-iis-5d999b87d7-6h8s9   1/1     Running   0          47s     52.224.143.39    virtual-kubelet-virtual-kubelet-windows-eastus
```

## <a name="remove-virtual-kubelet"></a>移除 Virtual Kubelet

使用[az aks remove-connector][aks-remove-connector]命令來移除虛擬 Kubelet。 以連接器、AKS 叢集和 AKS 叢集資源群組的名稱取代引數值。

```azurecli-interactive
az aks remove-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

> [!NOTE]
> 如果您在移除兩個 OS 連接器時遇到錯誤，或者只想要移除 Windows 或 Linux OS 連接器，可以手動指定 OS 類型。 將 `--os-type` 參數新增至先前的 `az aks remove-connector` 命令，然後指定 `Windows` 或 `Linux`。

## <a name="next-steps"></a>後續步驟

如需虛擬 Kubelet 的可能問題, 請參閱[已知][vk-troubleshooting]的錯誤和因應措施。 若要報告虛擬 Kubelet 的問題, 請[開啟 GitHub 問題][vk-issues]。

如需虛擬 Kubelet 的詳細資訊, 請參閱[Virtual Kubelet GitHub 專案][vk-github]。

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector
[virtual-nodes-cli]: virtual-nodes-cli.md
[virtual-nodes-portal]: virtual-nodes-portal.md
[aks-helm]: kubernetes-helm.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[vk-troubleshooting]: https://github.com/virtual-kubelet/virtual-kubelet#known-quirks-and-workarounds
[vk-issues]: https://github.com/virtual-kubelet/virtual-kubelet/issues
