---
title: 在 Azure Kubernetes Service 中使用標準 SKU 負載平衡器（AKS）
description: 瞭解如何使用具有標準 SKU 的負載平衡器，以 Azure Kubernetes Service （AKS）公開您的服務。
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/05/2019
ms.author: zarhoads
ms.openlocfilehash: 9cfced0860b206e41b3e9f82f1ed2b92867e6b39
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914829"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service 中使用標準 SKU 負載平衡器（AKS）

若要在 Azure Kubernetes Service （AKS）中提供應用程式的存取權，您可以建立和使用 Azure Load Balancer。 在 AKS 上執行的負載平衡器可做為內部或外部負載平衡器使用。 內部負載平衡器可讓 Kubernetes 服務僅供在與 AKS 叢集相同的虛擬網路中執行的應用程式存取。 外部負載平衡器會接收一或多個公用 Ip 進行輸入，並使用公用 Ip 讓 Kubernetes 服務可從外部進行存取。

Azure Load Balancer 有兩種 SKU -「基本」和「標準」。 根據預設，當服務資訊清單用來在 AKS 上建立負載平衡器時，會使用*基本*SKU。 使用*標準*SKU 負載平衡器提供額外的特性和功能，例如較大的後端集區大小和可用性區域。 請務必先瞭解*標準*和*基本*負載平衡器之間的差異，再選擇要使用哪一個。 建立 AKS 叢集之後，您就無法變更該叢集的負載平衡器 SKU。 如需*基本*和*標準*sku 的詳細資訊，請參閱[Azure 負載平衡器 sku 比較][azure-lb-comparison]。

本文說明如何建立和使用具有 Azure Kubernetes Service （AKS）之*標準*SKU 的 Azure Load Balancer。

本文假設您對 Kubernetes 和 Azure Load Balancer 概念有基本瞭解。 如需詳細資訊，請參閱[Kubernetes 的核心概念 Azure Kubernetes Service （AKS）][kubernetes-concepts]和[Azure Load Balancer 的內容？][azure-lb]。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 版2.0.59 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

## <a name="before-you-begin"></a>開始之前

如果您使用現有的子網或資源群組，則 AKS 叢集服務主體需要管理網路資源的許可權。 一般來說，請將「*網路參與者*」角色指派給委派資源上的服務主體。 如需許可權的詳細資訊，請參閱[將 AKS 存取權委派給其他 Azure 資源][aks-sp]。

您必須建立 AKS 叢集，將負載平衡器的 SKU 設定為 [*標準*]，而不是預設的 [*基本*]。

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 擴充功能

若要使用 Azure 負載平衡器標準 SKU，您需要*aks-preview* CLI 擴充功能版本0.4.12 或更高版本。 使用[az extension add][az-extension-add]命令來安裝*aks-preview* Azure CLI 擴充功能，然後使用[az extension update][az-extension-update]命令檢查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>限制

當您建立和管理支援具有*標準*SKU 之負載平衡器的 AKS 叢集時，適用下列限制：

* 必須至少有一個公用 IP 或 IP 首碼，才能允許來自 AKS 叢集的輸出流量。 此外，也需要公用 IP 或 IP 首碼，以維護控制平面和代理程式節點之間的連線，以及維護與舊版 AKS 的相容性。 您有下列選項可使用*標準*SKU 負載平衡器來指定公用 IP 或 IP 首碼：
    * 提供您自己的公用 Ip。
    * 提供您自己的公用 IP 首碼。
    * 指定最多100的數位，以允許 AKS 叢集在建立為 AKS 叢集的相同資源群組中建立許多*標準*SKU 公用 ip，這通常會在一開始就使用*MC_* 來命名。 AKS 會將公用 IP 指派給*標準*SKU 負載平衡器。 根據預設，如果未指定公用 IP、公用 IP 首碼或 Ip 數目，則會在與 AKS 叢集相同的資源群組中自動建立一個公用 IP。 您也必須允許公用位址，並避免建立 ban IP 建立的任何 Azure 原則。
* 使用負載平衡器的*標準*SKU 時，您必須使用 Kubernetes 1.13 或更高版本。
* 只有當您建立 AKS 叢集時，才可以定義負載平衡器 SKU。 建立 AKS 叢集之後，您就無法變更負載平衡器 SKU。
* 您只能在單一叢集中使用一個負載平衡器 SKU。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯群組。 建立資源群組時，系統會要求您指定位置。 此位置是儲存資源群組中繼資料的位置，如果您未在資源建立期間指定另一個區域，此位置也會是您在 Azure 中執行資源的位置。 使用 [az group create][az-group-create] 命令來建立資源群組。

下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

下列範例輸出顯示已成功建立的資源群組：

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-aks-cluster"></a>建立 AKS 叢集
若要執行支援具有*標準*SKU 之負載平衡器的 AKS 叢集，您的叢集必須將*負載平衡器-SKU*參數設定為 [*標準*]。 此參數會在您的叢集建立時，使用*標準*SKU 建立負載平衡器。 當您在叢集上執行*LoadBalancer*服務時，系統會使用服務的設定來更新*標準*SKU 負載平衡器的設定。 使用[az aks create][az-aks-create]命令來建立名為*myAKSCluster*的 aks 叢集。

> [!NOTE]
> 只有在建立叢集時，才可以使用*負載平衡器-sku*屬性。 建立 AKS 叢集之後，您就無法變更負載平衡器 SKU。 此外，您只能在單一叢集中使用一種類型的負載平衡器 SKU。
> 
> 如果您想要使用您自己的公用 Ip，請使用*負載平衡器-輸出*ip 或*負載平衡器-輸出 ip 首碼*參數。 [更新](#optional---provide-your-own-public-ips-or-prefixes-for-egress)叢集時，也可以使用這兩個參數。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

在幾分鐘之後，此命令就會完成，並以 JSON 格式傳回叢集的相關資訊。

## <a name="connect-to-the-cluster"></a>連接到叢集

若要管理 Kubernetes 叢集，請使用 Kubernetes 命令列用戶端：[kubectl][kubectl]。 如果您使用 Azure Cloud Shell，則 `kubectl` 已安裝。 若要在本機安裝 `kubectl`，請使用 [az aks install-cli][az-aks-install-cli] 命令：

```azurecli
az aks install-cli
```

若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令會下載憑證並設定 Kubernetes CLI 以供使用。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

若要驗證叢集的連線，請使用 [kubectl get][kubectl-get] 命令來傳回叢集節點的清單。

```azurecli-interactive
kubectl get nodes
```

下列輸出範例會顯示上一個步驟中建立的單一節點。 請確定節點的狀態為 *Ready*：

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.13.10
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>確認您的叢集使用*標準*SKU

使用[az aks show][az-aks-show]顯示叢集的設定。

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster

{
  "aadProfile": null,
  "addonProfiles": null,
   ...
   "networkProfile": {
    "dnsServiceIp": "10.0.0.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "loadBalancerSku": "standard",
    ...
```

確認 [ *loadBalancerSku* ] 屬性顯示為 [*標準*]。

## <a name="use-the-load-balancer"></a>使用負載平衡器

若要在您的叢集上使用負載平衡器，請使用服務類型*LoadBalancer*建立服務資訊清單。 若要顯示負載平衡器運作，請建立另一個資訊清單，其中包含要在叢集上執行的範例應用程式。 這個範例應用程式會透過負載平衡器公開，並可透過瀏覽器來查看。

建立名`sample.yaml`為的資訊清單，如下列範例所示：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
```

上述資訊清單會設定兩個部署： *azure-投票-front*和*azure 投票*。 若要使用負載平衡器來設定要公開的*azure 投票-front*部署，請建立名`standard-lb.yaml`為的資訊清單，如下列範例所示：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

服務*azure 投票*會使用*LoadBalancer*類型，在您的 AKS 叢集上設定負載平衡器，以連線到*azure 投票-front*部署。

使用[kubectl apply][kubectl-apply]部署範例應用程式和負載平衡器，並指定 YAML 資訊清單的名稱：

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

*標準*SKU 負載平衡器現在已設定為公開範例應用程式。 使用[kubectl get][kubectl-get]查看*azure 投票*的服務詳細資料，以查看負載平衡器的公用 IP。 負載平衡器的公用 IP 位址會顯示在 [*外部 IP* ] 資料行中。 IP 位址可能需要一或兩分鐘的時間，才會從 *\<暫\>止狀態*變更為實際的外部 IP 位址，如下列範例所示：

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

在瀏覽器中流覽至公用 IP，並確認您看到範例應用程式。 在上述範例中，公用 IP 是`52.179.23.131`。

![瀏覽至 Azure 投票的影像](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> 您也可以將負載平衡器設定為內部，而不公開公用 IP。 若要將負載平衡器設定為內部`service.beta.kubernetes.io/azure-load-balancer-internal: "true"` ，請新增作為「 *LoadBalancer* 」服務的注釋。 您可以在[這裡][internal-lb-yaml]查看範例 yaml 資訊清單，以及有關內部負載平衡器的詳細資料。

## <a name="optional---scale-the-number-of-managed-public-ips"></a>選擇性-調整受控公用 Ip 的數目

使用*標準*SKU 負載平衡器搭配預設建立的受控輸出公用 ip 時，您可以使用*負載平衡器管理的 ip 計數*參數來調整受控輸出公用 ip 數目。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

上述範例會將*myResourceGroup*中*myAKSCluster*叢集的受控輸出公用 ip 數目設定為*2* 。 建立叢集時，您也可以使用*負載平衡器管理的 ip 計數*參數來設定初始受控輸出公用 ip 數目。 受控輸出公用 Ip 的預設數目為1。

## <a name="optional---provide-your-own-public-ips-or-prefixes-for-egress"></a>選擇性-提供您自己的公用 Ip 或輸出的首碼

使用*標準*sku 負載平衡器時，AKS 叢集會自動在針對 AKS 叢集所建立的相同資源群組中建立公用 ip，並將公用 ip 指派給*標準*SKU 負載平衡器。 或者，您也可以指派自己的公用 IP。

> [!IMPORTANT]
> 您必須使用*標準*Sku 公用 ip，搭配您的*標準*sku 與您的負載平衡器。 您可以使用[az network public-ip show][az-network-public-ip-show]命令來驗證公用 IP 的 SKU：
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

使用[az network public-ip show][az-network-public-ip-show]命令來列出公用 Ip 的識別碼。

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

上述命令會顯示*myResourceGroup*資源群組中*myPublicIP*公用 IP 的識別碼。

使用*az aks update*命令搭配*負載平衡器-輸出 ip*參數，將您的叢集更新為您的公用 ip。

下列範例會使用*負載平衡器-輸出 ip*參數與上一個命令中的識別碼。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

您也可以使用公用 IP 首碼，搭配*標準*SKU 負載平衡器進行輸出。 下列範例會使用[az network public-ip prefix show][az-network-public-ip-prefix-show]命令來列出公用 ip 首碼的識別碼：

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

上述命令會顯示*myResourceGroup*資源群組中*myPublicIPPrefix*公用 IP 首碼的識別碼。

使用*az aks update*命令搭配*負載平衡器-輸出 ip-* 前置詞參數與上一個命令的識別碼。

下列範例會使用*負載平衡器-輸出 ip-* 前置詞參數與上一個命令的識別碼。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> 公用 Ip 和 IP 首碼必須位於與您的 AKS 叢集相同的區域中，且屬於相同的訂用帳戶。

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>清除標準 SKU 負載平衡器設定

若要移除範例應用程式和負載平衡器設定，請使用[kubectl delete][kubectl-delete]：

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Kubernetes services，請[參閱 Kubernetes services 檔][kubernetes-services]。

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

