---
title: 預覽-使用 Azure Kubernetes Service (AKS) 中的標準 SKU 負載平衡器
description: 了解如何使用與標準 SKU 負載平衡器公開您的服務與 Azure Kubernetes Service (AKS)。
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/25/2019
ms.author: zarhoads
ms.openlocfilehash: a9cf3db3a15fab5a2f067a146950e02923a20379
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476816"
---
# <a name="preview---use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>預覽-使用 Azure Kubernetes Service (AKS) 中的標準 SKU 負載平衡器

若要提供存取您的應用程式在 Azure Kubernetes Service (AKS) 中，您可以建立並使用 Azure Load Balancer。 在 AKS 上執行的負載平衡器可用來當做內部或外部負載平衡器。 內部負載平衡讓 Kubernetes 服務僅供在與 AKS 叢集相同的虛擬網路中執行的應用程式存取。 外部負載平衡器會接收輸入的一或多個公用 Ip，並讓 Kubernetes 服務可存取的外部使用的公用 Ip。

Azure Load Balancer 有兩種 SKU -「基本」  和「標準」  。 根據預設，*基本*SKU 可在服務資訊清單用來在 AKS 上建立負載平衡器。 使用*標準*SKU 負載平衡器提供額外特性和功能，例如較大的後端集區大小和可用性區域。 您必須了解之間的差異*標準*並*基本*選擇要使用之前的負載平衡器。 一旦您建立 AKS 叢集時，就無法變更該叢集的負載平衡器 SKU。 如需詳細資訊*基本*並*標準*Sku，請參閱[Azure 負載平衡器 SKU 的比較][azure-lb-comparison]。

這篇文章會示範如何建立及使用具有 Azure Load Balancer*標準*SKU 與 Azure Kubernetes Service (AKS)。

本文假設基本了解 Kubernetes 和 Azure 負載平衡器的概念。 如需詳細資訊，請參閱 < [Kubernetes 核心概念的 Azure Kubernetes Service (AKS)][kubernetes-concepts] and [What is Azure Load Balancer?][azure-lb]。

此功能目前為預覽狀態。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇要安裝在本機使用 CLI，本文會要求您執行 Azure CLI 2.0.59 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

## <a name="before-you-begin"></a>開始之前

AKS 叢集服務主體需要管理網路資源，如果您使用現有的子網路或資源群組的權限。 一般情況下，指派*網路參與者*給服務主體的委派資源的角色。 如需有關權限的詳細資訊，請參閱[存取其他 Azure 資源的委派 AKS][aks-sp]。

您必須建立的 AKS 叢集，設定負載平衡器 SKU*標準*而非預設*基本*。 在稍後步驟中，負責建立 AKS 叢集，但您必須先啟用一些的預覽功能。

> [!IMPORTANT]
> AKS 預覽功能包括自助、 選擇加入。 它們可供收集從我們的社群的意見及 bug。 在預覽中，這些功能不適用於實際執行環境。 在公開預覽功能底下 '盡力' 支援。 AKS 技術支援小組的協助時可使用營業時間太平洋 」 (PST) 僅限 timezone。 如需詳細資訊，請參閱下列支援文章：
>
> * [AKS 支援原則][aks-support-policies]
> * [Azure 支援常見問題集][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 擴充功能

若要使用 Azure load balancer 標準 SKU，您需要*aks 預覽*CLI 擴充功能版本 0.4.1 或更高版本。 安裝*aks 預覽*Azure CLI 擴充功能使用[az 延伸模組加入][az-extension-add]command, then check for any available updates using the [az extension update][az-extension-update]命令：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-aksazurestandardloadbalancer-preview-feature"></a>註冊 AKSAzureStandardLoadBalancer 預覽功能

若要建立的 AKS 叢集，可以使用的負載平衡器*標準*SKU，您必須啟用*AKSAzureStandardLoadBalancer*功能在您的訂用帳戶上的旗標。 *AKSAzureStandardLoadBalancer*功能也會使用*VMSSPreview*時使用的虛擬機器擴展集建立叢集。 設定叢集時，這項功能會提供最新一組服務增強功能。 雖然並非必要，但建議您啟用*VMSSPreview*功能旗標。

> [!CAUTION]
> 當您註冊訂用帳戶上的功能時，您目前無法取消註冊該功能。 啟用某些預覽功能之後，可能會使用預設值，然後在 訂用帳戶中建立的所有 AKS 叢集。 請勿啟用生產訂用帳戶上的預覽功能。 若要測試預覽功能，並收集意見反應中使用不同的訂用帳戶。

註冊*VMSSPreview*並*AKSAzureStandardLoadBalancer*功能旗標使用[az 功能註冊][az-feature-register]命令，在下列範例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "VMSSPreview"
az feature register --namespace "Microsoft.ContainerService" --name "AKSAzureStandardLoadBalancer"
```

> [!NOTE]
> 任何您已成功註冊之後，您建立的 AKS 叢集*VMSSPreview*或是*AKSAzureStandardLoadBalancer*功能旗標會使用此預覽叢集體驗。 若要繼續建立一般、 完全支援的叢集，請勿啟用生產訂用帳戶上的預覽功能。 使用個別的測試或開發 Azure 訂用帳戶進行測試預覽功能。

狀態需要幾分鐘的時間才會顯示「已註冊」  。 您可以檢查註冊狀態 using [az 功能清單][az-feature-list]命令：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
```

準備好時，重新整理的註冊*Microsoft.ContainerService*使用的資源提供者[az provider register][az-provider-register]命令：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>限制

當您建立和管理支援的負載平衡器的 AKS 叢集時，適用下列限制*標準*SKU:

* 使用時*標準*負載平衡器的 SKU，您必須允許公用位址，並避免建立禁令 IP 建立任何 Azure 原則。 會自動建立 AKS 叢集*標準*SKU 公用 IP 相同資源群組中的建立的 AKS 叢集，通常名為*MC_* 開頭。 AKS 會指派公用 IP*標準*SKU 負載平衡器。 需要允許輸出流量從 AKS 叢集的公用 IP。 此公用 IP，也需要維護節點之間的連線控制平面與代理程式以及並維持與舊版的 AKS 相容性。
* 使用時*標準*SKU 負載平衡器，您必須使用 Kubernetes 版本 1.13.5 或更新版本。

雖然這項功能處於預覽狀態，其他的限制如下：

* 使用時*標準*AKS 中的負載平衡器的 SKU，您無法設定您自己出口流量的公用 IP 位址的負載平衡器。 您必須使用 AKS 將指派給負載平衡器的 IP 位址。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯群組。 建立資源群組時，系統會要求您指定位置。 此位置是儲存資源群組中繼資料的位置，如果您未在資源建立期間指定另一個區域，此位置也會是您在 Azure 中執行資源的位置。 使用 [az group create][az-group-create] 命令來建立資源群組。

下列範例會在 eastus  位置建立名為 myResourceGroup  的資源群組。

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
若要執行的 AKS 叢集，支援的負載平衡器*標準*若要設定的 SKU，您的叢集需要*負載-平衡器 sku*參數來*標準*。 這個參數會建立具有負載平衡器*標準*SKU 時建立叢集。 當您執行*負載平衡器*服務在您的叢集的組態*標準*SK 負載平衡器會以服務的組態更新。 使用[az aks 建立][az-aks-create]命令來建立名為 AKS 叢集中*myAKSCluster*。

> [!NOTE]
> *負載-平衡器 sku*屬性僅適用於您的叢集建立時。 建立 AKS 叢集之後，您無法變更負載平衡器 SKU。 此外，您只能使用一種類型的單一叢集中的負載平衡器 SKU。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --kubernetes-version 1.14.0 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

在幾分鐘之後，此命令就會完成，並以 JSON 格式傳回叢集的相關資訊。

## <a name="connect-to-the-cluster"></a>連接到叢集

若要管理 Kubernetes 叢集，您使用[kubectl][kubectl]，Kubernetes 命令列用戶端。 如果您使用 Azure Cloud Shell，則 `kubectl` 已安裝。 若要安裝`kubectl`在本機，使用[az aks 安裝 cli][az-aks-install-cli]命令：

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
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.14.0
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>請確認您的叢集使用*標準*SKU

使用[az aks 顯示][az-aks-show]來顯示您的叢集設定。

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

請確認*loadBalancerSku*屬性會顯示為*標準*。

## <a name="use-the-load-balancer"></a>使用負載平衡器

若要在叢集上使用負載平衡器，建立與服務類型的 服務資訊清單*負載平衡器*。 若要顯示使用負載平衡器，請使用您叢集上執行範例應用程式建立另一個資訊清單。 此範例應用程式會公開透過負載平衡器，而且可以透過瀏覽器來檢視。

建立名為資訊清單`sample.yaml`如下列範例所示：

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

上述的資訊清單會設定兩個部署： *azure 投票前*並*azure azure-vote-back*。 若要設定*azure 投票前*部署至可公開使用的負載平衡器中，建立名為資訊清單`standard-lb.yaml`如下列範例所示：

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

服務*azure 投票前*會使用*負載平衡器*連線到您的 AKS 叢集上設定負載平衡器的型別*azure 投票前*部署。

部署範例應用程式和負載平衡器使用[kubectl 套用][kubectl-apply]並指定您的 YAML 資訊清單名稱：

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

*標準*SKU 負載平衡器現在已設定為公開 （expose） 的範例應用程式。 檢視服務詳細資料*azure 投票前*使用[kubectl get][kubectl-get]若要查看負載平衡器的公用 IP。 負載平衡器的公用 IP 位址 所示*EXTERNAL-IP*資料行。 可能需要幾分鐘的時間從變更的 IP 位址 *\<暫止\>* 實際外部 IP 位址，如下列範例所示：

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

瀏覽至瀏覽器中的公用 IP，並確認您看到的範例應用程式。 在上述範例中，公用 IP 是`52.179.23.131`。

![瀏覽至 Azure 投票的影像](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> 您可以也設定為內部負載平衡器，並不會公開公用 IP。 若要設定負載平衡器，為內部，加入`service.beta.kubernetes.io/azure-load-balancer-internal: "true"`成為註釋來*LoadBalancer*服務。 您所見資訊清單也越多的內部負載平衡器的詳細範例 yaml[此處][internal-lb-yaml]。

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>清除標準 SKU 負載平衡器組態

若要移除範例應用程式和負載平衡器組態，請使用[kubectl 刪除][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>後續步驟

深入了解 Kubernetes 服務[Kubernetes 服務文件][kubernetes-services]。

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
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
