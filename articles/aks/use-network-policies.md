---
title: 在 Azure Kubernetes Service (AKS) 中使用網路原則來保護 Pod
description: 了解如何在 Azure Kubernetes Service (AKS) 中使用 Kubernetes 網路原則，保護流入及流出的流量
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/12/2019
ms.author: iainfou
ms.openlocfilehash: ade5a39273aa807f6c69f76342a0f715c7a96309
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232169"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中使用網路原則來保護 Pod 之間的流量

當您在 Kubernetes 中執行新式微服務架構的應用程式時，您通常想要控制哪些元件可以彼此通訊。 最低權限原則應該套用至流量可以在 AKS 叢集中的 Pod 之間流動的方式。 例如，您可能想要封鎖直接流向後端應用程式的流量。 在 Kubernetes 中，「網路原則」功能可讓您針對叢集中 Pod 之間的輸入和輸出流量定義規則。

本文示範如何使用網路原則來控制 AKS 中 Pod 之間的流量流程。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定][terms-of-use]，即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="before-you-begin"></a>開始之前

您必須安裝並設定 Azure CLI 版本 2.0.56 或更新版本。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="overview-of-network-policy"></a>網路原則概觀

根據預設，AKS 叢集中的所有 Pod 都可以無限制地傳送及接收流量。 為了提升安全性，您可以定義可控制流量流程的規則。 例如，後端應用程式通常只會對必要的前端服務公開，或是資料庫元件僅供與其連線的應用程式層存取。

網路原則是一種 Kubernetes 資源，可讓您控制 Pod 之間的流量流程。 您可以根據指派的標籤、命名空間或流量連接埠等設定，選擇允許或拒絕流量。 網路原則會定義為 YAML 資訊清單，而且可納入更廣泛的資訊清單中，而該清單也會建立部署或服務。

若要查看運作中的網路原則，讓我們建立而後展開可定義流量流程的原則，如下所示：

* 拒絕流向 Pod 的所有流量。
* 允許以 Pod 標籤為基礎的流量。
* 允許以命名空間為基礎的流量。

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>建立 AKS 叢集並啟用網路原則

只有在建立叢集時，才可以啟用網路原則。 您無法在現有的 AKS 叢集上啟用網路原則。 若要使用網路原則建立 AKS，請先對您的訂用帳戶啟用功能旗標。 若要註冊 EnableNetworkPolicy 功能旗標，請使用 [az feature register][az-feature-register] 命令，如下列範例所示：

```azurecli-interactive
az feature register --name EnableNetworkPolicy --namespace Microsoft.ContainerService
```

狀態需要幾分鐘的時間才會顯示「已註冊」。 您可以使用 [az feature list][az-feature-list] 命令檢查註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableNetworkPolicy')].{Name:name,State:properties.state}"
```

準備就緒時，使用 [az provider register][az-provider-register] 命令重新整理 *Microsoft.ContainerService* 資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

若要使用網路原則搭配 AKS 叢集，您必須使用 [Azure CNI 外掛程式][azure-cni]並定義自己的虛擬網路和子網路。 如需有關如何規劃出必要子網路範圍的詳細資訊，請參閱[設定進階網路][use-advanced-networking]。 下列範例指令碼：

* 建立虛擬網路和子網路。
* 建立 Azure Active Directory (AD) 服務主體，以便搭配 AKS 叢集使用。
* 針對虛擬網路上的 AKS 叢集服務主體指派「參與者」權限。
* 在定義的虛擬網路中建立 AKS 叢集，然後啟用網路原則。

提供您自己的安全 *SP_PASSWORD*。 如有需要，取代 *RESOURCE_GROUP_NAME* 和 *CLUSTER_NAME* 變數：

```azurecli-interactive
SP_PASSWORD=mySecurePassword
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
read SP_ID <<< $(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --kubernetes-version 1.12.4 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy calico
```

建立叢集需要幾分鐘的時間。 完成後，請使用 [az aks get-credentials][az-aks-get-credentials] 命令，設定 `kubectl` 以連線到 Kubernetes 叢集。 此命令會下載憑證並設定 Kubernetes CLI 以供使用：

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>拒絕流向 Pod 的所有輸入流量

在您定義規則以允許特定網路流量之前，先建立網路原則以拒絕所有流量。 此原則讓您能夠開始僅只將想要的流量列入白名單。 您可以也清楚地看到套用網路原則時會捨棄該流量。

針對我們的範例應用程式環境和流量規則，讓我們先建立名為 *development* 的命名空間以執行我們的範例 Pod：

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

現在建立可執行 NGINX 的範例後端 Pod。 這個後端 Pod 可用於模擬範例後端 Web 應用程式。 在 *development* 命名空間中建立這個 Pod，然後開啟連接埠 *80* 來處理 Web 流量。 替 Pod 加上 *app=webapp,role=backend* 標籤，以便我們在下一節中使用網路原則以其作為目標：

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

若要測試您可以成功觸達預設 NGINX 網頁，請建立另一個 Pod，然後連結終端機工作階段：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

在殼層提示字元，使用 `wget` 來確認您可以存取預設 NGINX 網頁：

```console
wget -qO- http://backend
```

下列範例輸出會顯示傳回的預設 NGINX 網頁：

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

結束連結的終端機工作階段。 測試 Pod 會自動刪除：

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>建立並套用網路原則

既然已確認您可以在範例後端 Pod 上存取基本 NGINX 網頁，請建立網路原則以拒絕所有流量。 建立名為 `backend-policy.yaml` 的檔案，並貼上下列 YAML 資訊清單。 此資訊清單會使用 *podSelector* 將原則連結至具有 *app:webapp,role:backend* 標籤的 Pod，例如您的範例 NGINX Pod。 *ingress* 之下未定義任何規則，因此會拒絕流向 Pod 的所有輸入流量：

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

使用 [kubectl apply][kubectl-apply] 命令來套用網路原則，並指定 YAML 資訊清單的名稱：

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>測試網路原則

讓我們看看您是否可以在後端 Pod 上再次存取 NGINX 網頁。 建立另一個測試 Pod 並連結終端機工作階段：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

在殼層提示字元，使用 `wget` 來查看您是否可以存取預設 NGINX 網頁。 此時，將逾時值設定為 2 秒。 網路原則現在會封鎖所有輸入流量，因此無法載入此頁面，如下列範例所示：

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

結束連結的終端機工作階段。 測試 Pod 會自動刪除：

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>允許以 Pod 標籤為基礎的輸入流量

在上一節中，已排程後端 NGINX Pod，並建立了網路原則以拒絕所有流量。 現在讓我們建立前端 Pod 並更新網路原則，以允許來自前端 Pod 的流量。

更新網路原則，以允許來自任何命名空間中具有 *app:webapp,role:frontend* 標籤的 Pod 流量。 編輯先前的 *backend-policy.yaml* 檔案，然後新增 *matchLabels* 輸入規則，讓您的資訊清單看起來如下列範例所示：

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

使用 [kubectl apply][kubectl-apply] 命令來套用已更新的網路原則，並指定 YAML 資訊清單的名稱：

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

現在排程標籤為 *app=webapp,role=frontend* 的 Pod 並連結終端機工作階段：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

在殼層提示字元，使用 `wget` 來查看您是否可以存取預設 NGINX 網頁：

```console
wget -qO- http://backend
```

因為輸入規則允許具有 *app: webapp,role: frontend* 標籤的 Pod 流量，所以允許來自前端 Pod 的流量。 下列範例輸出會顯示傳回的預設 NGINX 網頁：

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

結束連結的終端機工作階段。 Pod 會自動刪除：

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>測試沒有相符標籤的 Pod

網路原則允許來自標籤為 *app: webapp,role: frontend* 的 Pod 流量，但應該拒絕所有其他流量。 讓我們測試另一個沒有這些標籤的 Pod 是否無法存取後端 NGINX Pod。 建立另一個測試 Pod 並連結終端機工作階段：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

在殼層提示字元，使用 `wget` 來查看您是否可以存取預設 NGINX 網頁。 網路原則會封鎖輸入流量，因此無法載入此頁面，如下列範例所示：

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

結束連結的終端機工作階段。 測試 Pod 會自動刪除：

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>只允許來自已定義命名空間的流量

在上述範例中，您建立了可拒絕所有流量的網路原則，然後更新了此原則，以允許來自具有特定標籤的 Pod 流量。 另一個常見需求是讓流量僅限於指定的命名空間內。 如果先前的範例是針對 *development* 命名空間中的流量，您可以接著建立網路原則，以防止來自另一個命名空間 (例如 *production*) 的流量觸達 Pod。

首先，建立新的命名空間，以模擬生產命名空間：

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

在標籤為 *app=webapp,role=frontend* 的 *production* 命名空間中排程測試 Pod。 連結終端機工作階段：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

在殼層提示字元，使用 `wget` 來確認您可以存取預設 NGINX 網頁：

```console
wget -qO- http://backend.development
```

因為 Pod 的標籤符合網路原則目前許可的內容，所以允許流量。 網路原則不會查看命名空間，只會查看 Pod 標籤。 下列範例輸出會顯示傳回的預設 NGINX 網頁：

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

結束連結的終端機工作階段。 測試 Pod 會自動刪除：

```console
exit
```

### <a name="update-the-network-policy"></a>更新網路原則

現在讓我們更新輸入規則 *namespaceSelector* 區段，只允許來自 *development* 命名空間的流量。 編輯 *backend-policy.yaml* 資訊清單檔，如下列範例所示：

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

在更複雜的範例中，您可以定義多個輸入規則，例如依序使用 *namespaceSelector* 和 *podSelector*。

使用 [kubectl apply][kubectl-apply] 命令來套用已更新的網路原則，並指定 YAML 資訊清單的名稱：

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>測試已更新的網路原則

現在在 *production* 命名空間中排程另一個 Pod，然後連結終端機工作階段：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

在殼層提示字元，使用 `wget` 來查看網路原則現在會拒絕流量：

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

結束測試 Pod：

```console
exit
```

拒絕了來自 *production* 命名空間的流量，現在回到 *development* 命名空間中排程測試 Pod，然後連結終端機工作階段：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

在殼層提示字元，使用 `wget` 來查看網路原則允許流量：

```console
wget -qO- http://backend
```

因為 Pod 已排定在符合網路原則許可內容的命名空間中，所以允許流量。 下列範例輸出會顯示傳回的預設 NGINX 網頁：

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

結束連結的終端機工作階段。 測試 Pod 會自動刪除：

```console
exit
```

## <a name="clean-up-resources"></a>清除資源

在本文中，我們會建立兩個命名空間並套用網路原則。 若要清除這些資源，請使用 [kubectl delete][kubectl-delete] 命令並指定資源名稱，如下所示：

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>後續步驟

如需網路資源的詳細資訊，請參閱 [Azure Kubernetes Service (AKS) 中應用程式的網路概念][concepts-network]。

若要深入了解如何使用原則，請參閱 [Kubernetes 網路原則][kubernetes-network-policies]。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
