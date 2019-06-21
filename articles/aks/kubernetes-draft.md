---
title: 在 Azure Kubernetes Service (AKS) 使用 Draft 上進行開發
description: 使用 Draft 搭配 AKS 和 Azure Container Registry
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: bd099b9d76e17eda36be1650ef5081e5aaa7e53a
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303536"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>快速入門：在 Azure Kubernetes Service (AKS) 使用 Draft 上進行開發

Draft 是開放原始碼工具，可協助封裝，並在 Kubernetes 叢集中執行的應用程式容器。 使用 Draft，您可以快速地重新部署 Kubernetes 應用程式程式碼變更時不必將變更認可至版本控制。 如需有關 Draft 的詳細資訊，請參閱 < [Draft 在 GitHub 上的文件][draft-documentation]。

這篇文章會示範如何使用 Draft 套件，並在 AKS 上執行的應用程式。


## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶](https://azure.microsoft.com/free)。
* [已安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。
* 安裝及設定 docker。 Docker 提供設定 Docker 的套件[Mac][docker-for-mac], [Windows][docker-for-windows]，或[Linux][docker for linux]系統。
* [安裝 helm](https://github.com/helm/helm/blob/master/docs/install.md)。
* [安裝 draft][draft-documentation]。

## <a name="create-an-azure-kubernetes-service-cluster"></a>建立 Azure Kubernetes Service 叢集

建立 AKS 叢集。 下列命令建立名為 MyResourceGroup 和呼叫 MyAKS AKS 叢集中的資源群組。

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>建立 Azure Container Registry
若要使用 Draft 在 AKS 叢集中執行您的應用程式，您需要 Azure Container Registry 來儲存您的容器映像。 下列範例會使用[az acr 建立][az-acr-create]建立名為 ACR *MyDraftACR*中*MyResourceGroup*具有資源群組*基本*SKU。 您應該提供您自己唯一的登錄名稱。 登錄名稱在 Azure 內必須是唯一的，且包含 5-50 個英數字元。 *基本* SKU 對開發用途而言是最符合成本效益的進入點，可在儲存體和輸送量之間取得平衡。

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

輸出類似於下列範例： 請記下的*loginServer*您的 ACR，因為它將用於後續步驟中的值。 在下列範例中， *mydraftacr.azurecr.io*是*loginServer* for *MyDraftACR*。

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyDraftACR",
  "location": "eastus",
  "loginServer": "mydraftacr.azurecr.io",
  "name": "MyDraftACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```


若要使用 ACR 執行個體的 draft，您必須先登入。 使用[az acr 登入][az-acr-login]命令以登入。 下列範例會登入名為 ACR *MyDraftACR*。

```azurecli
az acr login --name MyDraftACR
```

此命令在完成之後會傳回*登入成功*訊息。

## <a name="create-trust-between-aks-cluster-and-acr"></a>在 AKS 叢集與 ACR 之間建立信任

您的 AKS 叢集也需要存取您的 ACR，提取容器映像，並執行它們。 您允許存取 ACR 從 AKS 建立信任。 若要在 AKS 叢集與 ACR 登錄之間建立信任，請授與權限以供 AKS 叢集所使用的 Azure Active Directory 服務主體存取 ACR 登錄。 下列命令授與權限的服務主體*MyAKS*叢集*MyResourceGroup*來*MyDraftACR*中的 ACR *MyResourceGroup*。

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>連接到您的 AKS 叢集

若要從您的本機電腦連線到 Kubernetes 叢集，您使用[kubectl][kubectl]，Kubernetes 命令列用戶端。

如果您使用 Azure Cloud Shell，則 `kubectl` 已安裝。 您也可以使用 [az aks install-cli][] 命令將其安裝於本機：

```azurecli
az aks install-cli
```

若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [az aks get-credentials][] 命令。 下列範例會取得名為 AKS 叢集的認證*MyAKS*中*MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>建立適用於 Helm 的服務帳戶

在已啟用 RBAC 的 AKS 叢集中部署 Helm 之前，您需要適用於 Tiller 服務的服務帳戶與角色繫結。 如需有關保護 Helm / Tiller RBAC 中的啟用的叢集，請參閱 < [Tiller、 命名空間和 RBAC][tiller-rbac]。 如果您的 AKS 叢集未啟用 RBAC，請略過此步驟。

建立名為 `helm-rbac.yaml` 的檔案，然後將下列 YAML 複製進來：

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

使用 `kubectl apply` 命令來建立服務帳戶和角色繫結：

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>設定 Helm
若要部署到 AKS 叢集中的基本 Tiller，使用[helm init][helm-init]命令。 如果您的叢集未啟用 RBAC，移除`--service-account`引數和值。

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>設定 Draft

如果您尚未設定 Draft 在本機電腦上，執行`draft init`:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

您也需要設定 Draft 以使用*loginServer*的 ACR。 下列命令會使用`draft config set`使用`mydraftacr.azurecr.io`為登錄。

```console
draft config set registry mydraftacr.azurecr.io
```

您已設定 Draft 以使用您的 ACR，Draft 可以將容器映像推送到 ACR。 Draft 會執行您的應用程式在 AKS 叢集中，沒有密碼或秘密時才能推送至或從 ACR 登錄提取。 因為您的 AKS 叢集與 ACR 之間建立信任關係，驗證將會在 Azure Resource Manager 層級，使用 Azure Active Directory 中。

## <a name="download-the-sample-application"></a>下載範例應用程式

本快速入門會使用[草稿 GitHub 儲存機制中的範例 java 應用程式][example-java]。 複製的 GitHub 應用程式，並瀏覽至`draft/examples/example-java/`目錄。

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>使用 Draft 執行範例應用程式

使用`draft create`命令，準備應用程式。

```console
draft create
```

此命令會建立在 Kubernetes 叢集中用來執行應用程式的成品。 這些項目包含 Dockerfile、Helm 圖表和 draft.toml  檔案 (Draft 組態檔)。

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

若要在 AKS 叢集中執行應用程式範例，請使用 `draft up` 命令。

```console
draft up
```

此命令會建置 Dockerfile 以建立容器映像、 將映像推送至 ACR，並安裝 Helm 圖表，以在 AKS 中啟動應用程式。 第一次執行此命令時，推送和提取容器映像可能需要一些時間。 在快取基底圖層後，部署應用程式時所花費的時間就會大幅降低。

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>連接到執行範例應用程式從本機電腦

若要測試應用程式，請使用 `draft connect` 命令。

```console
draft connect
```

此命令會使用 Proxy 對 Kubernetes Pod 建立安全的連線。 完成時，就可以在提供的 URL 存取應用程式。

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

瀏覽至應用程式在瀏覽器中使用`localhost`以查看範例應用程式的 url。 在上述範例中，url 是`http://localhost:49804`。 停止連線使用`Ctrl+c`。

## <a name="access-the-application-on-the-internet"></a>在網際網路上存取應用程式

上一個步驟針對 AKS 叢集中的應用程式 Pod 建立了 Proxy 連線。 當您在開發並測試應用程式時，您可以讓應用程式在網際網路上供人使用。 若要公開在網際網路上的應用程式，您可以使用一種建立 Kubernetes 服務[負載平衡器][kubernetes-service-loadbalancer]。

更新`charts/example-java/values.yaml`來建立*LoadBalancer*服務。 值變更*service.type*從*叢集的 Ip*來*LoadBalancer*。

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

儲存變更、 關閉檔案，並執行`draft up`重新執行應用程式。

```console
draft up
```

需要幾分鐘的時間，服務才能傳回公用 IP 位址。 若要監視進度，請使用 `kubectl get service` 命令搭配 watch  參數：

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

瀏覽至您的應用程式，在瀏覽器中使用的負載平衡器*EXTERNAL-IP*以查看範例應用程式。 在上述範例中，IP 是`52.175.224.118`。

## <a name="iterate-on-the-application"></a>針對應用程式反覆執行

您可以逐一查看您的應用程式進行本機變更，然後重新執行`draft up`。

更新傳回的訊息[行 src/main/java/helloworld/Hello.java 第 7][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

執行 `draft up` 命令以重新部署應用程式：

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

若要查看更新的應用程式，請再次瀏覽至您的負載平衡器的 IP 位址，並確認您的變更會出現。

## <a name="delete-the-cluster"></a>刪除叢集

當不再需要叢集時，使用[az 群組刪除][az-group-delete]命令來移除資源群組，AKS 叢集中、 容器登錄、 容器映像儲存於該處，和所有相關資源。

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> 當您刪除叢集時，不會移除 AKS 叢集所使用的 Azure Active Directory 服務主體。 如需有關如何移除服務主體的步驟，請參閱[AKS 服務主體的考量和刪除][sp-delete]。

## <a name="next-steps"></a>後續步驟

如需有關使用 Draft 的詳細資訊，請參閱 GitHub 上的 Draft 文件。

> [!div class="nextstepaction"]
> [Draft 文件][draft-documentation]


[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-login
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[kubernetes-ingress]: ./ingress-basic.md

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[example-java]: https://github.com/Azure/draft/tree/master/examples/example-java
[example-java-hello-l7]: https://github.com/Azure/draft/blob/master/examples/example-java/src/main/java/helloworld/Hello.java#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[helm-init]: https://docs.helm.sh/helm/#helm-init
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
