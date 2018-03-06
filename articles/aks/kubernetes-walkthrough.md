---
title: "快速入門 - 適用於 Linux 的 Azure Kubernetes 叢集"
description: "快速了解如何在 AKS 中使用 Azure CLI 建立適用於 Linux 容器的 Kubernetes 叢集。"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: quickstart
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: 8e64ab3214633ae2f34234514dca5e7bb7b1896e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>部署 Azure Container Service (AKS) 叢集

本快速入門中會使用 Azure CLI 來部署 AKS 叢集。 接著，在叢集上執行包含 Web 前端和 Redis 執行個體的多容器應用程式。 完成後，即可透過網際網路來存取應用程式。

![瀏覽至 Azure 投票的影像](media/container-service-kubernetes-walkthrough/azure-vote.png)

本快速入門假設讀者已了解 Kubernetes 的基本概念，如需 Kubernetes 的詳細資訊，請參閱 [Kubernetes 文件][kubernetes-documentation]。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本快速入門會要求您執行 Azure CLI 2.0.27 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="enabling-aks-preview-for-your-azure-subscription"></a>啟用您 Azure 訂用帳戶的 AKS 預覽
雖然 AKS 處於預覽狀態，但是建立新叢集需要訂用帳戶的功能旗標。 您可以為您想要使用之任意數量的訂用帳戶要求這項功能。 使用 `az provider register` 命令來註冊 AKS 提供者：

```azurecli-interactive
az provider register -n Microsoft.ContainerService
```

在註冊之後，您現在已準備就緒可以使用 AKS 建立 Kubernetes 叢集。

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create][az-group-create] 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯群組。
在建立資源群組時，系統會要求您指定位置，這是您的資源存在於 Azure 的位置。 雖然 AKS 處於預覽狀態，但只有一些位置選項可用。 這些是 `eastus, westeurope, centralus, canadacentral, canadaeast`。

下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

輸出：

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>建立 AKS 叢集

下列範例會建立名為 myAKSCluster 並包含一個節點的叢集。

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

幾分鐘之後，此命令就會完成，並以 JSON 格式傳回叢集的相關資訊。

## <a name="connect-to-the-cluster"></a>連接到叢集

若要管理 Kubernetes 叢集，請使用 Kubernetes 命令列用戶端：[kubectl][kubectl]。

如果您是使用 Azure Cloud Shell，則已安裝 kubectl。 如果您想要在本機進行安裝，請執行下列命令。


```azurecli
az aks install-cli
```

若要設定 kubectl 來連線到 Kubernetes 叢集，請執行下列命令。 此步驟會下載憑證並設定 Kubernetes CLI 以供使用。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

若要驗證叢集的連線，請使用 [kubectl get][kubectl-get] 命令來傳回叢集節點的清單。 請注意，這可能需要幾分鐘的時間才會出現。

```azurecli-interactive
kubectl get nodes
```

輸出：

```
NAME                          STATUS    ROLES     AGE       VERSION
k8s-myAKSCluster-36346190-0   Ready     agent     2m        v1.7.7
```

## <a name="run-the-application"></a>執行應用程式

Kubernetes 資訊清單檔會定義所需的叢集狀態，包括哪些容器映像應在執行中。 例如，資訊清單可用來建立執行 Azure 投票應用程式所需的所有物件。 提供的映像是一個範例應用程式，但您可以閱讀[建立映像](https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-prepare-app)和[部署至 Azure Container Registry](https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-prepare-acr)來使用自己的映像。

建立名為 `azure-vote.yaml` 的檔案，然後將下列 YAML 程式碼複製到其中。 如果您在 Azure Cloud Shell 中作業，可以使用 vi 或 Nano 建立這個檔案，猶如使用虛擬或實體系統。 如果您在本機作業，您可以執行 `code azure-vote.yaml`，使用 Visual Studio Code 來建立此檔案。

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      containers:
      - name: azure-vote-back
        image: redis
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
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
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

使用 [kubectl create][kubectl-create] 命令來執行應用程式。

```azurecli-interactive
kubectl create -f azure-vote.yaml
```

輸出：

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>測試應用程式

執行應用程式時會建立 [Kubernetes 服務][kubernetes-service]，此服務會向網際網路公開前端應用程式。 此程序需要數分鐘的時間完成。

若要監視進度，請使用 [kubectl get service][kubectl-get] 命令搭配 `--watch` 引數。

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

一開始，*azure-vote-front* 服務的 *EXTERNAL-IP* 會顯示為 *pending*。

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

當 *EXTERNAL-IP* 位址從 *pending* 變成一個 *IP 位址*之後，請使用 `CTRL-C` 來停止 kubectl 監看式流程。

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

您現在可以瀏覽至外部 IP 位址來查看 Azure 投票應用程式。

![瀏覽至 Azure 投票的影像](media/container-service-kubernetes-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>刪除叢集

若不再需要叢集，您可以使用 [az group delete][az-group-delete] 命令來移除資源群組、容器服務和所有相關資源。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>取得程式碼

在本快速入門中，已使用預先建立的容器映像來建立 Kubernetes 部署。 相關的應用程式程式碼、Dockerfile 和 Kubernetes 資訊清單檔案，都可以在 GitHub 上取得。

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已部署 Kubernetes 叢集，並將多容器應用程式部署到此叢集。

若要深入了解 AKS，並逐步完成部署範例的完整程式碼，請繼續 Kubernetes 叢集教學課程。

> [!div class="nextstepaction"]
> [ASK 教學課程][aks-tutorial]：

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az_aks_browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md

