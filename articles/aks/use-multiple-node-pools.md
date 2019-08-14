---
title: 在 Azure Kubernetes Service 中使用多個節點集區 (AKS)
description: 瞭解如何在 Azure Kubernetes Service (AKS) 中建立及管理叢集的多個節點集區
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 08/9/2019
ms.author: mlearned
ms.openlocfilehash: e9b654fc49a953f8fdbc9125c6f12486e0ab7b13
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949496"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>預覽-在 Azure Kubernetes Service (AKS) 中建立及管理叢集的多個節點集區

在 Azure Kubernetes Service (AKS) 中, 相同設定的節點會群組在一起成為*節點*集區。 這些節點集區包含執行應用程式的基礎 Vm。 當您建立 AKS 叢集時, 會定義初始節點數目及其大小 (SKU), 以建立*預設節點集*區。 若要支援具有不同計算或儲存體需求的應用程式, 您可以建立額外的節點集區。 例如, 您可以使用這些額外的節點集區, 為計算密集型應用程式提供 Gpu, 或存取高效能 SSD 儲存體。

> [!NOTE]
> 這項功能可讓您更進一步控制如何建立和管理多個節點集區。 因此, 建立/更新/刪除需要個別的命令。 先前的叢集作業`az aks create`會`az aks update`透過或使用 managedCluster API, 而且是變更您的控制平面和單一節點集區的唯一選項。 這項功能會透過 agentPool API 公開代理程式組件區的個別作業集, 並要求`az aks nodepool`使用命令集來執行個別節點集區上的作業。

本文說明如何在 AKS 叢集中建立和管理多個節點集區。 此功能目前為預覽狀態。

> [!IMPORTANT]
> AKS 預覽功能是自助服務, 可加入宣告。 其提供用來從我們的社區收集意見反應和 bug。 在預覽中, 這些功能不適用於生產環境使用。 公開預覽中的功能落在「最佳」支援之下。 AKS 技術支援小組的協助僅適用于上班時間太平洋時區 (PST)。 如需其他資訊, 請參閱下列支援文章:
>
> * [AKS 支援原則][aks-support-policies]
> * [Azure 支援常見問題集][aks-faq]

## <a name="before-you-begin"></a>開始之前

您需要安裝並設定 Azure CLI 版本2.0.61 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 擴充功能

若要使用多個 nodepools, 您需要*aks-preview* CLI 擴充功能版本0.4.1 或更高版本。 使用[az extension add][az-extension-add]命令來安裝*aks-preview* Azure CLI 擴充功能, 然後使用[az extension update][az-extension-update]命令檢查是否有任何可用的更新::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-multiple-node-pool-feature-provider"></a>註冊多個節點集區功能提供者

若要建立可使用多個節點集區的 AKS 叢集, 請先在您的訂用帳戶上啟用兩個功能旗標。 多節點集區叢集會使用虛擬機器擴展集 (VMSS) 來管理 Kubernetes 節點的部署和設定。 使用[az feature register][az-feature-register]命令來註冊*MultiAgentpoolPreview*和*VMSSPreview*功能旗標, 如下列範例所示:

> [!CAUTION]
> 當您在訂用帳戶上註冊功能時, 目前無法取消註冊該功能。 啟用一些預覽功能之後, 預設值可能會用於在訂用帳戶中建立的所有 AKS 叢集。 請勿在生產訂用帳戶上啟用預覽功能。 使用個別的訂用帳戶來測試預覽功能並收集意見反應。

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> 您在成功註冊*MultiAgentpoolPreview*之後所建立的任何 AKS 叢集都會使用此預覽叢集體驗。 若要繼續建立一般、完全支援的叢集, 請勿在生產訂用帳戶上啟用預覽功能。 使用個別的測試或開發 Azure 訂用帳戶來測試預覽功能。

狀態需要幾分鐘的時間才會顯示「已註冊」。 您可以使用[az feature list][az-feature-list]命令來檢查註冊狀態:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

準備好時, 請使用[az provider register][az-provider-register]命令重新整理*microsoft.containerservice*資源提供者的註冊:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>限制

當您建立和管理支援多個節點集區的 AKS 叢集時, 適用下列限制:

* 只有在您成功註冊訂用帳戶的*MultiAgentpoolPreview*和*VMSSPreview*功能之後, 才會有多個節點集區適用于建立的叢集。 在成功註冊這些功能之前, 您無法新增或管理具有現有 AKS 叢集的節點集區。
* 您無法刪除第一個節點集區。
* 無法使用 HTTP 應用程式路由附加元件。
* 您無法使用現有的 Resource Manager 範本來新增/更新/刪除節點集區, 就像大部分的作業一樣。 相反地, 請[使用個別的 Resource Manager 範本](#manage-node-pools-using-a-resource-manager-template), 對 AKS 叢集中的節點集區進行變更。

雖然這項功能處於預覽狀態, 但仍適用下列其他限制:

* AKS 叢集最多可以有八個節點集區。
* AKS 叢集在這八個節點集區中最多可以有400個節點。
* 所有節點集區都必須位於相同的子網中

## <a name="create-an-aks-cluster"></a>建立 AKS 叢集

若要開始使用, 請建立具有單一節點集區的 AKS 叢集。 下列範例會使用[az group create][az-group-create]命令, 在*eastus*區域中建立名為*myResourceGroup*的資源群組。 然後使用[az AKS create][az-aks-create]命令來建立名為*myAKSCluster*的 AKS 叢集。 *--Kubernetes-* *1.13.5*版本是用來示範如何在下列步驟中更新節點集區。 您可以指定任何[支援的 Kubernetes 版本][supported-versions]。

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --generate-ssh-keys \
    --kubernetes-version 1.13.5
```

建立叢集需要幾分鐘的時間。

當叢集準備就緒時, 請使用[az aks get-認證][az-aks-get-credentials]命令來取得要搭配使用`kubectl`的叢集認證:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>新增節點集區

在上一個步驟中建立的叢集具有單一節點集區。 讓我們使用[az aks nodepool add][az-aks-nodepool-add]命令來新增第二個節點集區。 下列範例會建立名為*mynodepool*的節點集區, 其會執行*3*個節點:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.12.6
```

若要查看節點集區的狀態, 請使用[az aks node pool list][az-aks-nodepool-list]命令, 並指定您的資源群組和叢集名稱:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster -o table
```

下列範例輸出顯示已成功建立節點集區中有三個節點的*mynodepool* 。 在上一個步驟中建立 AKS 叢集時, 會建立一個節點計數為*1*的預設*nodepool1* 。

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

> [!TIP]
> 當您新增節點集區時, 如果未指定*OrchestratorVersion*或*VmSize* , 則會根據 AKS 叢集的預設值來建立節點。 在此範例中, 這是 Kubernetes 的版本*1.13.5*和節點大小*Standard_DS2_v2*。

## <a name="upgrade-a-node-pool"></a>升級節點集區

當您在第一個步驟中建立 AKS 叢集時, `--kubernetes-version`已指定*1.13.5*的。 這會設定控制平面和初始節點集區的 Kubernetes 版本。 有不同的命令可用於升級控制平面和節點集區的 Kubernetes 版本。 命令是用來升級控制平面, `az aks nodepool upgrade`而則是用來升級個別的節點集區。 `az aks upgrade`

讓我們將*mynodepool*升級至 Kubernetes *1.13.7*。 使用[az aks node pool upgrade][az-aks-nodepool-upgrade]命令升級節點集區, 如下列範例所示:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.13.7 \
    --no-wait
```

> [!Tip]
> 若要將控制平面升級至*1.13.7*, `az aks upgrade -k 1.13.7`請執行。

使用[az aks node pool list][az-aks-nodepool-list]命令再次列出節點集區的狀態。 下列範例顯示*mynodepool*處於*1.13.7*的*升級*狀態:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.13.7                 100             Linux     Upgrading            myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

將節點升級為指定的版本需要幾分鐘的時間。

最佳做法是將 AKS 叢集中的所有節點集區升級至相同的 Kubernetes 版本。 升級個別節點集區的功能可讓您執行輪流升級, 並在節點集區之間排程 pod, 以維護上述條件約束內的應用程式執行時間。

> [!NOTE]
> Kubernetes 使用標準的[語義版本](https://semver.org/)控制版本設定配置。 版本號碼會以*x-y*表示, 其中*x*是主要版本, *y*是次要版本, 而*z*是修補程式版本。 例如, 在版本*1.12.6*中, 1 是主要版本, 12 是次要版本, 而6是修補程式版本。 在叢集建立期間, 會設定控制平面和初始節點集區的 Kubernetes 版本。 當所有其他節點集區新增至叢集時, 會設定其 Kubernetes 版本。 節點集區以及節點集區與控制平面之間的 Kubernetes 版本可能不同, 但適用下列限制:
> 
> * 節點集區版本必須與控制平面具有相同的主要版本。
> * 節點集區版本可能是一個小於控制平面版本的次要版本。
> * 節點集區版本可能是任何修補程式版本, 只要遵循其他兩個條件約束即可。
> 
> 若要升級控制平面的 Kubernetes 版本, 請使用`az aks upgrade`。 如果您的叢集只有一個節點集區, `az aks upgrade`此命令也會升級節點集區的 Kubernetes 版本。

## <a name="scale-a-node-pool-manually"></a>手動調整節點集區

當您的應用程式工作負載需求變更時, 您可能需要調整節點集區中的節點數目。 節點數目可以向上或向下調整。

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

若要調整節點集區中的節點數目, 請使用[az aks node pool scale][az-aks-nodepool-scale]命令。 下列範例會將*mynodepool*中的節點數目調整為*5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

使用[az aks node pool list][az-aks-nodepool-list]命令再次列出節點集區的狀態。 下列範例顯示*mynodepool*處於*調整*狀態, 而且有*5*個節點的新計數:

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.13.7                 100             Linux     Scaling              myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

調整作業需要幾分鐘的時間才能完成。

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>藉由啟用叢集自動調整程式, 自動調整特定節點集區

AKS 在預覽中提供不同的功能, 可使用稱為叢集[自動調整程式](cluster-autoscaler.md)的元件自動調整節點集區。 此元件是一個 AKS 的附加元件, 可針對每個節點集區使用唯一的最小和最大調整計數, 為每個節點集區啟用。 瞭解如何[使用每個節點集區的叢集自動調整程式](cluster-autoscaler.md#enable-the-cluster-autoscaler-on-an-existing-node-pool-in-a-cluster-with-multiple-node-pools)。

## <a name="delete-a-node-pool"></a>刪除節點集區

如果您不再需要集區, 您可以將它刪除並移除基礎 VM 節點。 若要刪除節點集區, 請使用[az aks node pool delete][az-aks-nodepool-delete]命令並指定節點集區名稱。 下列範例會刪除在先前步驟中建立的*mynoodepool* :

> [!CAUTION]
> 當您刪除節點集區時, 不會發生資料遺失的復原選項。 如果無法在其他節點集區上排程 pod, 則無法使用這些應用程式。 當使用中的應用程式沒有資料備份或在叢集中的其他節點集區上執行的能力時, 請確定您不會刪除節點集區。

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

下列來自[az aks node pool list][az-aks-nodepool-list]命令的範例輸出顯示*mynodepool*處於*刪除*狀態:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.13.7                 100             Linux     Deleting             myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

刪除節點和節點集區需要幾分鐘的時間。

## <a name="specify-a-vm-size-for-a-node-pool"></a>指定節點集區的 VM 大小

在先前的範例中, 若要建立節點集區, 則會針對在叢集中建立的節點使用預設的 VM 大小。 較常見的案例是使用不同的 VM 大小和功能來建立節點集區。 例如, 您可以建立節點集區, 其中包含具有大量 CPU 或記憶體的節點, 或可提供 GPU 支援的節點集區。 在下一個步驟中, 您會[使用污點和容差](#schedule-pods-using-taints-and-tolerations), 告訴 Kubernetes 排程器如何限制可以在這些節點上執行的 pod 存取。

在下列範例中, 建立以 GPU 為基礎的節點集區, 以使用*Standard_NC6* VM 大小。 這些 Vm 是由 NVIDIA Tesla K80 插卡提供技術支援。 如需可用 VM 大小的詳細資訊, 請參閱[Azure 中 Linux 虛擬機器的大小][vm-sizes]。

再次使用[az aks node pool add][az-aks-nodepool-add]命令來建立節點集區。 這次請指定名稱*gpunodepool*, 並使用`--node-vm-size`參數來指定*Standard_NC6*大小:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

下列來自[az aks node pool list][az-aks-nodepool-list]命令的範例輸出顯示*gpunodepool*正在*建立*具有指定*VmSize*的節點:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name         OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  -----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  1        110        gpunodepool  1.13.5                 100             Linux     Creating             myResourceGroup  Standard_NC6
VirtualMachineScaleSets  1        110        nodepool1    1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

成功建立*gpunodepool*需要幾分鐘的時間。

## <a name="schedule-pods-using-taints-and-tolerations"></a>使用污點和容差排程 pod

您現在的叢集中有兩個節點集區-一開始建立預設節點集區, 以及以 GPU 為基礎的節點集區。 使用 [ [kubectl] [取得節點][kubectl-get]] 命令來查看您叢集中的節點。 下列範例輸出顯示每個節點集區中的一個節點:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.13.5
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.13.5
```

Kubernetes 排程器可以使用污點和容差來限制可以在節點上執行的工作負載。

* **污點**會套用至節點，該節點指示僅可以在其上排程特定的 pod。
* 然後**容差**會套用至容器，允許它們*容許*節點的污點。

如需如何使用 advanced Kubernetes 排程功能的詳細資訊, 請參閱[AKS 中 advanced 排程器功能的最佳做法][taints-tolerations]

在此範例中, 使用[kubectl 污點 node][kubectl-taint]命令, 將污點套用至您的 GPU 型節點。 從上一個`kubectl get nodes`命令的輸出中指定 GPU 型節點的名稱。 污點會當做索引*鍵: 值*和排程選項來套用。 下列範例會使用*sku = gpu*配對, 並定義 pod, 否則會具有*NoSchedule*功能:

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

下列基本範例 YAML 資訊清單會使用 toleration, 以允許 Kubernetes 排程器在 GPU 型節點上執行 NGINX pod。 如需針對 MNIST 資料集執行 Tensorflow 作業的更適當但需要大量時間的範例, 請參閱[在 AKS 上針對計算密集型工作負載使用 gpu][gpu-cluster]。

建立名為 `gpu-toleration.yaml` 的檔案，然後將下列範例 YAML 複製進來：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

使用`kubectl apply -f gpu-toleration.yaml`命令來排程 pod:

```console
kubectl apply -f gpu-toleration.yaml
```

需要幾秒鐘的時間來排程 pod, 並提取 NGINX 的映射。 使用 [ [kubectl 描述 pod][kubectl-describe] ] 命令來查看 pod 狀態。 下列精簡的範例輸出顯示已套用*sku = gpu: NoSchedule* toleration。 在 [事件] 區段中, 排程器已將 pod 指派給*aks-gpunodepool-28993262-vmss000000* GPU 型節點:

```console
$ kubectl describe pod mypod

[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

只有套用此污點的 pod 可以在*gpunodepool*的節點上排程。 任何其他 pod 都會在*nodepool1*節點集區中排程。 如果您建立其他節點集區, 您可以使用其他污點和容差來限制可在這些節點資源上排程的 pod。

## <a name="manage-node-pools-using-a-resource-manager-template"></a>使用 Resource Manager 範本管理節點集區

當您使用 Azure Resource Manager 範本來建立和管理資源時, 您通常可以更新範本中的設定, 並重新部署以更新資源。 在 AKS 中使用節點集區時, 一旦建立 AKS 叢集之後, 就無法更新初始節點集區設定檔。 這個行為表示您無法更新現有的 Resource Manager 範本、對節點集區進行變更, 然後重新部署。 相反地, 您必須建立個別的 Resource Manager 範本, 只更新現有 AKS 叢集的代理程式組件區。

建立範本 (例如) `aks-agentpools.json` , 並貼上下列範例資訊清單。 這個範例範本會設定下列設定:

* 更新名為*myagentpool*的*Linux*代理程式組件區, 以執行三個節點。
* 設定節點集區中的節點, 以執行 Kubernetes 版本*1.13.5*。
* 將節點大小定義為*Standard_DS2_v2*。

視需要編輯這些值, 視需要更新、新增或刪除節點集區:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of your existing AKS cluster."
      }
    },
    "location": {
      "type": "string",
      "metadata": {
        "description": "The location of your existing AKS cluster."
      }
    },
    "agentPoolName": {
      "type": "string",
      "defaultValue": "myagentpool",
      "metadata": {
        "description": "The name of the agent pool to create or update."
      }
    },
    "vnetSubnetId": {
      "type": "string",
      "defaultValue": "",
      "metadata": {
        "description": "The Vnet subnet resource ID for your existing AKS cluster."
      }
    }
  },
  "variables": {
    "apiVersion": {
      "aks": "2019-04-01"
    },
    "agentPoolProfiles": {
      "maxPods": 30,
      "osDiskSizeGB": 0,
      "agentCount": 3,
      "agentVmSize": "Standard_DS2_v2",
      "osType": "Linux",
      "vnetSubnetId": "[parameters('vnetSubnetId')]"
    }
  },
  "resources": [
    {
      "apiVersion": "2019-04-01",
      "type": "Microsoft.ContainerService/managedClusters/agentPools",
      "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
      "location": "[parameters('location')]",
      "properties": {
            "maxPods": "[variables('agentPoolProfiles').maxPods]",
            "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
            "count": "[variables('agentPoolProfiles').agentCount]",
            "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
            "osType": "[variables('agentPoolProfiles').osType]",
            "storageProfile": "ManagedDisks",
      "type": "VirtualMachineScaleSets",
            "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
            "orchestratorVersion": "1.13.5"
      }
    }
  ]
}
```

使用[az group deployment create][az-group-deployment-create]命令來部署此範本, 如下列範例所示。 系統會提示您輸入現有的 AKS 叢集名稱和位置:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

視您在 Resource Manager 範本中定義的節點集區設定和作業而定, 可能需要幾分鐘的時間來更新 AKS 叢集。

## <a name="assign-a-public-ip-per-node-in-a-node-pool"></a>為節點集區中的每個節點指派一個公用 IP

AKS 節點不需要自己的公用 IP 位址進行通訊。 不過, 某些情況下, 節點集區中的節點可能需要有自己的公用 IP 位址。 其中一個範例是遊戲, 其中主控台需要直接連線到雲端虛擬機器, 以將躍點降至最低。 這可以藉由註冊個別的預覽功能 [節點公用 IP (預覽)] 來達成。

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

成功註冊之後, 請遵循[上述](##manage-node-pools-using-a-resource-manager-template)相同指示部署 Azure Resource Manager 範本, 並在 agentPoolProfiles 上新增下列布林值屬性 "enableNodePublicIP"。 將此設`true`為, 預設會將它設定為`false` (如果未指定)。 這是僅限建立時間的屬性, 而且需要最低 API 版本2019-06-01。 這可同時套用至 Linux 和 Windows 節點集區。

```
"agentPoolProfiles":[  
    {  
      "maxPods": 30,
      "osDiskSizeGB": 0,
      "agentCount": 3,
      "agentVmSize": "Standard_DS2_v2",
      "osType": "Linux",
      "vnetSubnetId": "[parameters('vnetSubnetId')]"
      "enableNodePublicIP":true
    }
```

## <a name="clean-up-resources"></a>清除資源

在本文中, 您已建立 AKS 叢集, 其中包含以 GPU 為基礎的節點。 若要減少不必要的成本, 您可能會想要刪除*gpunodepool*或整個 AKS 叢集。

若要刪除以 GPU 為基礎的節點集區, 請使用[az aks nodepool delete][az-aks-nodepool-delete]命令, 如下列範例所示:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

若要刪除叢集本身, 請使用[az group delete][az-group-delete]命令來刪除 AKS 資源群組:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>後續步驟

在本文中, 您已瞭解如何在 AKS 叢集中建立和管理多個節點集區。 如需如何跨節點集區控制 pod 的詳細資訊, 請參閱[AKS 中先進排程器功能的最佳做法][operator-best-practices-advanced-scheduler]。

若要建立及使用 Windows Server 容器節點集區, 請參閱[在 AKS 中建立 Windows server 容器][aks-windows]。

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
