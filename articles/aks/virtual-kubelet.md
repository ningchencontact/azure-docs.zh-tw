---
title: 在 Azure Kubernetes Service (AKS) 叢集中執行 Virtual Kubelet
description: 使用 Virtual Kubelet 在 Azure 容器執行個體上執行 Kubernetes 容器。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/12/2018
ms.author: iainfou
ms.openlocfilehash: 04fdb1620dc6e7147ed10ae6eeeaeb3eeae14b62
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097354"
---
# <a name="virtual-kubelet-with-aks"></a>採用 AKS 的 Virtual Kubelet

Azure 容器執行個體 (ACI) 可提供託管環境，以便在 Azure 中執行容器。 使用 ACI 時，不需要管理基礎計算基礎結構，Azure 會為您處理此管理工作。 在 ACI 中執行時容器時，系統會針對每個執行中的容器以秒計費。

使用 Azure 容器執行個體的 Virtual Kubelet 提供者時，Linux 和 Windows 容器都可以在容器執行個體上排程，宛如標準 Kubernetes 節點一樣。 此組態可讓您善用 Kubernetes 功能，以及容器執行個體的管理價值和成本效益。

> [!NOTE]
> Virtual Kubelet 是實驗性開放原始碼專案，應該如此使用。 若要參與、提報問題，以及深入了解 Virtual Kubelet，請參閱[Virtual Kubelet GitHub 專案][vk-github]。

本文件詳細說明如何在 AKS 上設定容器執行個體的 Virtual Kubelet。

## <a name="prerequisite"></a>必要條件

本文件假設您有 AKS 叢集。 如果您需要 AKS 叢集，請參閱 [Azure Kubernetes Service (AKS) 快速入門][aks-quick-start]。

您也必須需要 Azure CLI 版本 **2.0.33** 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

此外，也需要 [Helm](https://docs.helm.sh/using_helm/#installing-helm) 才能安裝 Virtual Kubelet。

## <a name="installation"></a>安裝

使用 [az aks install-connector][aks-install-connector] 命令來安裝 Virtual Kubelet。 下列範例可部署 Linux 與 Windows 連接器。

```azurecli-interactive
az aks install-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet --os-type Both
```

`aks install-connector` 命令可使用下列引數。

| 引數： | 說明 | 必要 |
|---|---|:---:|
| `--connector-name` | ACI 連接器的名稱。| yes |
| `--name` `-n` | 受控叢集的名稱。 | yes |
| `--resource-group` `-g` | 資源群組的名稱。 | yes |
| `--os-type` | 容器執行個體的作業系統類型。 允許的值為：兩者、Linux、Windows。 預設值：Linux。 | 否 |
| `--aci-resource-group` | 要在其中建立 ACI 容器群組的資源群組。 | 否 |
| `--location` `-l` | 要建立 ACI 容器群組的位置。 | 否 |
| `--service-principal` | 用於向 Azure API 驗證的服務主體。 | 否 |
| `--client-secret` | 與服務主體相關聯的祕密。 | 否 |
| `--chart-url` | 安裝 ACI 連接器的 Helm 圖表 URL。 | 否 |
| `--image-tag` | Virtual Kubelet 容器映像的映像標記。 | 否 |

## <a name="validate-virtual-kubelet"></a>驗證 Virtual Kubelet

若要驗證已安裝的 Virtual Kubelet，請使用 [kubectl get nodes][kubectl-get] 命令來傳回 Kubernetes 節點清單。

```console
$ kubectl get nodes

NAME                                    STATUS    ROLES     AGE       VERSION
aks-nodepool1-23443254-0                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-1                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-2                Ready     agent     16d       v1.9.6
virtual-kubelet-virtual-kubelet-linux   Ready     agent     4m        v1.8.3
virtual-kubelet-virtual-kubelet-win     Ready     agent     4m        v1.8.3
```

## <a name="run-linux-container"></a>執行 Linux 容器

建立名為 `virtual-kubelet-linux.yaml` 的檔案，然後將下列 YAML 複製進來。 使用 Linux Virtual Kubelet 節點名稱取代 `kubernetes.io/hostname` 值。 請注意 [nodeSelector][node-selector] 和 [toleration][toleration] 用於在節點上排定容器。

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
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
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-linux
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

使用 [kubectl create][kubectl-create] 命令來執行應用程式。

```azurecli-interactive
kubectl create -f virtual-kubelet-linux.yaml
```

使用 [kubectl get pods][kubectl-get] 命令搭配 `-o wide` 引數來輸出包含排定節點的 Pod 清單。 請注意，`aci-helloworld` pod 已排定在 `virtual-kubelet-virtual-kubelet-linux` 節點上。

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
aci-helloworld-2559879000-8vmjw     1/1       Running   0          39s       52.179.3.180   virtual-kubelet-virtual-kubelet-linux
```

## <a name="run-windows-container"></a>執行 Windows 容器

建立名為 `virtual-kubelet-windows.yaml` 的檔案，然後將下列 YAML 複製進來。 使用 Windows Virtual Kubelet 節點名稱取代 `kubernetes.io/hostname` 值。 請注意 [nodeSelector][node-selector] 和 [toleration][toleration] 用於在節點上排定容器。

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: nanoserver/iis
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-win
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

使用 [kubectl create][kubectl-create] 命令來執行應用程式。

```azurecli-interactive
kubectl create -f virtual-kubelet-windows.yaml
```

使用 [kubectl get pods][kubectl-get] 命令搭配 `-o wide` 引數來輸出包含排定節點的 Pod 清單。 請注意，`nanoserver-iis` pod 已排定在 `virtual-kubelet-virtual-kubelet-win` 節點上。

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
nanoserver-iis-868bc8d489-tq4st     1/1       Running   8         21m       138.91.121.91   virtual-kubelet-virtual-kubelet-win
```

## <a name="remove-virtual-kubelet"></a>移除 Virtual Kubelet

使用 [az aks remove-connector][aks-remove-connector] 命令來移除 Virtual Kubelet。 以連接器、AKS 叢集和 AKS 叢集資源群組的名稱取代引數值。

```azurecli-interactive
az aks remove-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet
```

## <a name="next-steps"></a>後續步驟

在 [Virtual Kubelet Github 專案][vk-github]中深入了解 Virtual Kubelet。

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az_aks_list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
