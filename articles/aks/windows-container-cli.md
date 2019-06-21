---
title: 預覽-在 Azure Kubernetes Service (AKS) 叢集上建立 Windows Server 容器
description: 了解如何快速建立 Kubernetes 叢集、 部署 Windows Server 容器 Azure Kubernetes Service (AKS) 中使用 Azure CLI 中的應用程式。
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 06/17/2019
ms.author: twhitney
ms.openlocfilehash: a9887e923358b5658a365b5cfc88759eca2501e0
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303561"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>預覽-使用 Azure CLI 在 Azure Kubernetes Service (AKS) 叢集上建立 Windows Server 容器

Azure Kubernetes Service (AKS) 是受控 Kubernetes 服務，可讓您快速部署及管理叢集。 在本文中，您可以部署 AKS 叢集使用 Azure CLI。 您也會部署到叢集的 ASP.NET 範例應用程式在 Windows Server 容器中。

此功能目前為預覽狀態。

![瀏覽至 ASP.NET 範例應用程式的映像](media/windows-container/asp-net-sample-app.png)

本文假設基本了解 Kubernetes 概念。 如需詳細資訊，請參閱 < [Kubernetes 核心概念的 Azure Kubernetes Service (AKS)][kubernetes-concepts]。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇要安裝在本機使用 CLI，本文會要求您執行 Azure CLI 2.0.61 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="before-you-begin"></a>開始之前

您可以執行 Windows Server 容器的叢集建立之後，您必須新增額外的節點集區。 新增額外的節點集區負責在稍後的步驟，但您必須先啟用一些的預覽功能。

> [!IMPORTANT]
> AKS 預覽功能包括自助、 選擇加入。 它們可供收集從我們的社群的意見及 bug。 在預覽中，這些功能不適用於實際執行環境。 在公開預覽功能底下 '盡力' 支援。 AKS 技術支援小組的協助時可使用營業時間太平洋 」 (PST) 僅限 timezone。 如需詳細資訊，請參閱下列支援文章：
>
> * [AKS 支援原則][aks-support-policies]
> * [Azure 支援常見問題集][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 擴充功能
    
CLI 命令來建立和管理多個節點的集區共有*aks 預覽*CLI 擴充功能。 安裝*aks 預覽*Azure CLI 擴充功能使用[az 延伸模組新增][az-extension-add]命令，如下列範例所示：

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> 如果您先前已安裝*aks 預覽*延伸模組，安裝任何可用更新使用`az extension update --name aks-preview`命令。

### <a name="register-windows-preview-feature"></a>註冊 Windows 預覽功能

若要建立 AKS 叢集，可以使用多個節點的集區，並執行 Windows Server 容器，請先啟用*WindowsPreview*功能在您的訂用帳戶上的旗標。 *WindowsPreview*多節點的集區的叢集和虛擬機器擴展集來管理部署和設定的 Kubernetes 節點，也會使用功能。 註冊*WindowsPreview*功能旗標使用[az 功能註冊][az-feature-register]命令，在下列範例所示：

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> 任何您已成功註冊之後，您建立的 AKS 叢集*WindowsPreview*功能旗標會使用此預覽叢集體驗。 若要繼續建立一般、 完全支援的叢集，請勿啟用生產訂用帳戶上的預覽功能。 使用個別的測試或開發 Azure 訂用帳戶進行測試預覽功能。

花幾分鐘的時間才能完成註冊。 檢查註冊狀態 using [az 功能清單][az-feature-list]命令：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

註冊狀態時`Registered`，按下 Ctrl + C 可停止監視的狀態。  然後重新整理的註冊*Microsoft.ContainerService*使用的資源提供者[az provider register][az-provider-register]命令：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>限制

當您建立和管理支援多個節點的集區的 AKS 叢集時，就會套用下列限制：

* 多個節點的集區可供您成功註冊之後建立的叢集*WindowsPreview*。 多個節點的集區也會提供，如果您註冊*MultiAgentpoolPreview*並*VMSSPreview*訂用帳戶的功能。 您無法加入或使用現有的 AKS 叢集建立之前已成功註冊這些功能的節點集區。
* 您無法刪除第一個節點集區。

雖然這項功能處於預覽狀態，其他的限制如下：

* AKS 叢集中可以有最多八個節點的集區。
* AKS 叢集中可以有最多 400 個節點的跨這八個節點的集區。
* Windows Server 的節點集區名稱有 6 個字元的限制。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯群組。 建立資源群組時，系統會要求您指定位置。 此位置是儲存資源群組中繼資料的位置，如果您未在資源建立期間指定另一個區域，此位置也會是您在 Azure 中執行資源的位置。 使用 [az group create][az-group-create] 命令來建立資源群組。

下列範例會在 eastus  位置建立名為 myResourceGroup  的資源群組。

> [!NOTE]
> 在本教學課程，本文章會使用 Bash 語法命令。
> 如果您使用 Azure Cloud Shell，請確定在 Cloud Shell 視窗左上方的下拉式清單設為**Bash**。

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

## <a name="create-an-aks-cluster"></a>建立 AKS 叢集

若要執行的 AKS 叢集，適用於 Windows Server 容器支援節點集區，您的叢集需要使用的網路原則，會使用[Azure CNI][azure-cni-about] (advanced) network plugin. For more detailed information to help plan out the required subnet ranges and network considerations, see [configure Azure CNI networking][use-advanced-networking]。 使用[az aks 建立][az aks 建立]命令來建立名為 AKS 叢集中*myAKSCluster*。 如果不存在，此命令會建立必要的網路資源。
  * 設定具有一個節點的叢集
  * *Windows-admin-password*並*windows 管理員使用者名稱*參數會設定要在叢集上建立任何 Windows Server 容器的系統管理員認證。

提供您自己的安全*PASSWORD_WIN* （請記住這篇文章中的命令會輸入到 BASH 殼層）：

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.0 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

> [!Note]
> 如果您收到的密碼驗證錯誤，請嘗試另一個區域中建立您的資源群組。
> 然後再次嘗試使用新的資源群組建立叢集。

在幾分鐘之後，此命令就會完成，並以 JSON 格式傳回叢集的相關資訊。

## <a name="add-a-windows-server-node-pool"></a>新增 Windows Server 的節點集區

根據預設，具有可以執行 Linux 容器的節點集區建立 AKS 叢集。 使用`az aks nodepool add`命令來新增額外的節點集區，可以執行 Windows Server 容器。

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.0
```

上述命令會建立名為新的節點集區*npwin*並將它加入*myAKSCluster*。 在建立執行 Windows Server 容器的節點集區時，預設值*節點 vm 大小*是*Standard_D2s_v3*。 如果您選擇設定*節點 vm 大小*參數，請檢查清單[受限制的 VM 大小][restricted-vm-sizes]。 建議的大小最小值是*Standard_D2s_v3*。 上述命令也會使用預設子網路中建立執行時的預設 vnet `az aks create`。

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
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.0
aksnpwin987654                      Ready    agent   108s   v1.14.0
```

## <a name="run-the-application"></a>執行應用程式

Kubernetes 資訊清單檔會定義所需的叢集狀態，例如要執行哪些容器映像。 在本文中，資訊清單用來建立 Windows Server 容器中執行 ASP.NET 範例應用程式所需的所有物件。 此資訊清單包含[Kubernetes 部署][kubernetes-deployment]for the ASP.NET sample application and an external [Kubernetes service][kubernetes-service]從網際網路存取應用程式。

ASP.NET 範例應用程式提供做為一部分[.NET Framework 範例][dotnet-samples]並在 Windows Server 容器中執行。 AKS 需要 Windows Server 容器為基礎的映像*Windows Server 2019*或更新版本。 也必須定義資訊清單檔案的 Kubernetes[節點選取器][node-selector]告訴您的 AKS 叢集，可以執行 Windows Server 容器的節點上執行 ASP.NET 範例應用程式的 pod。

建立名為 `sample.yaml` 的檔案，然後將下列 YAML 定義複製進來。 如果您使用 Azure Cloud Shell，可以使用 `vi` 或 `nano` 建立這個檔案，猶如使用虛擬或實體系統：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800m
          requests:
            cpu: .1
            memory: 300m
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

部署應用程式使用[kubectl 套用][kubectl-apply]命令並指定您的 YAML 資訊清單的名稱：

```azurecli-interactive
kubectl apply -f sample.yaml
```

下列範例輸出顯示服務已成功建立與部署：

```
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>測試應用程式

執行應用程式時，Kubernetes 服務會向網際網路公開前端應用程式。 此程序需要數分鐘的時間完成。

若要監視進度，請使用 [kubectl get service][kubectl-get] 命令搭配 `--watch` 引數。

```azurecli-interactive
kubectl get service sample --watch
```

最初*EXTERNAL-IP* for*範例*服務會顯示為*暫止*。

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

當 *EXTERNAL-IP* 位址從 *pending* 變成實際的公用 IP 位址時，請使用 `CTRL-C` 停止 `kubectl` 監看式流程。 下列範例輸出會顯示已指派給服務的有效公用 IP 位址：

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

若要查看作用中的範例應用程式，開啟您服務的外部 IP 位址的 web 瀏覽器。

![瀏覽至 ASP.NET 範例應用程式的映像](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>刪除叢集

當不再需要叢集時，使用[az 群組刪除][az-group-delete]命令來移除資源群組、 容器服務和所有相關資源。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> 當您刪除叢集時，不會移除 AKS 叢集所使用的 Azure Active Directory 服務主體。 如需有關如何移除服務主體的步驟，請參閱[AKS 服務主體的考量和刪除][sp-delete]。

## <a name="next-steps"></a>後續步驟

在本文中，您部署了 Kubernetes 叢集，並部署 Windows Server 容器中的 ASP.NET 範例應用程式。 [存取 Kubernetes web 儀表板][kubernetes-dashboard]您剛才建立的叢集。

若要深入了解 AKS，並逐步完成部署範例的完整程式碼，請繼續 Kubernetes 叢集教學課程。

> [!div class="nextstepaction"]
> [AKS 教學課程][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-advanced-networking.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
