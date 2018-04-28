---
title: 使用 Draft 搭配 AKS 和 Azure Container Registry
description: 使用 Draft 搭配 AKS 和 Azure Container Registry
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: b91d446f4c43a4ecae40ef49e5e7f930f25e6ad2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="use-draft-with-azure-container-service-aks"></a>使用 Draft 搭配 Azure Container Service (AKS)

Draft 是開放原始碼工具，可協助在 Kubernetes 叢集中包含和部署容器，讓您自由地專注於開發週期--集中開發的「內部迴圈」。 Draft 會在程式碼開發期間，但在認可至版本控制之前運作。 當程式碼變更時，您可以使用 Draft 將應用程式快速地重新部署到 Kubernetes。 如需有關 Draft 的詳細資訊，請參閱 [Github 上的 Draft 文件][draft-documentation]。

本文件會詳細說明如何在 AKS 上使用 Draft 搭配 Kubernetes 叢集。

## <a name="prerequisites"></a>先決條件

本文件中詳述的步驟假設您已建立 AKS 叢集，並建立與叢集的 kubectl 連線。 如果您需要這些項目，請參閱 [AKS 快速入門][aks-quickstart]。

您在 Azure Container Registry (ACR) 中也需要私人 Docker 登錄。 如需有關部署 ACR 執行個體的指示，請參閱 [Azure Container Registry 快速入門][acr-quickstart]。

Helm 也必須安裝在 AKS 叢集中。 如需如何安裝 Helm 的詳細資訊，請參閱[使用 Helm 搭配 Azure Container Service (AKS)][aks-helm]。

最後，您必須安裝 [Docker](https://www.docker.com)。

## <a name="install-draft"></a>安裝 Draft

Draft CLI 是在開發系統上執行的用戶端，可讓您將程式碼快速部署到 Kubernetes 叢集。

> [!NOTE]
> 如果您已安裝 0.12 版之前的 Draft，請先使用 `helm delete --purge draft` 從叢集中刪除 Draft，然後執行 `rm -rf ~/.draft` 以移除本機設定。 如果您在 MacOS 上，請執行 `brew upgrade draft`。

若要在 Mac 上安裝 Draft CLI，請使用 `brew`。 如需其他安裝選項，請參閱 [Draft 安裝指南][install-draft] \(英文\)。

```console
brew tap azure/draft
brew install draft
```

現在，使用 `draft init` 命令初始化 Draft。

```console
draft init
```

## <a name="configure-draft"></a>設定 Draft

Draft 會在本機建置容器映像，然後從本機登錄 (如果是 Minikube) 部署映像，或者，您必須指定要使用的映像登錄。 這個範例使用 Azure Container Registry (ACR)，因此，您必須在 AKS 叢集與 ACR 登錄之間建立信任關係，並設定 Draft 以將容器推送至 ACR。

### <a name="create-trust-between-aks-cluster-and-acr"></a>在 AKS 叢集與 ACR 之間建立信任

若要在 AKS 叢集與 ACR 登錄之間建立信任，您可以修改與 AKS 搭配使用的 Azure Active Directory 服務主體，方法是使用 ACR 存放庫的範圍對它新增參與者角色。 若要這麼做，請執行下列命令，將 _&lt;aks-rg-name&gt;_ 和 _&lt;aks-cluster-name&gt;_ 替換為 AKS 叢集的資源群組與名稱，並將 _&lt;acr-rg-nam&gt;_ 和 _&lt;acr-repo-name&gt;_ 替換為要與其建立信任之 ACR 存放庫的資源群組和存放庫名稱。

```console
export AKS_SP_ID=$(az aks show -g <aks-rg-name> -n <aks-cluster-name> --query "servicePrincipalProfile.clientId" -o tsv)
export ACR_RESOURCE_ID=$(az acr show -g <acr-rg-name> -n <acr-repo-name> --query "id" -o tsv)
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

(這些步驟和其他用來存取 ACR 的驗證機制位於[向 ACR 驗證](../container-registry/container-registry-auth-aks.md)。)

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>設定 Draft 以推送至 ACR 和從 ACR 部署

現在，AKS 和 ACR 之間已有信任關係，下列步驟可讓您從 AKS 叢集使用 ACR。
1. 執行 `draft config set registry <registry name>.azurecr.io` 以設定 Draft 設定 `registry` 值，其中 _&lt;登錄名稱&lt;_ 是 ACR 登錄的名稱。
2. 執行 `az acr login -n <registry name>` 以登入 ACR 登錄。

因為您現在已登入 ACR 本機，並已在 AKS 與 ACR 之間建立信任關係，所以不需要密碼或秘密即可從 ACR 推送/提取至 AKS。 驗證會在 Azure Resource Manager 層級使用 Azure Active Directory 來進行。

## <a name="run-an-application"></a>執行應用程式

Draft 存放庫包含幾個可用來示範 Draft 的應用程式範例。 建立存放庫的複製複本。

```console
git clone https://github.com/Azure/draft
```

變更為 Java 範例目錄。

```console
cd draft/examples/example-java/
```

使用 `draft create` 命令啟動程序。 此命令會建立在 Kubernetes 叢集中用來執行應用程式的成品。 這些項目包含 Dockerfile、Helm 圖表和 `draft.toml` 檔案 (Draft 組態檔)。

```console
draft create
```

輸出：

```console
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

若要在 Kubernetes 叢集上執行應用程式，請使用 `draft up` 命令。 此命令會建置 Dockerfile 以建立容器映像，將映像推送至 ACR，最後再安裝 Helm 圖表以在 AKS 中啟動應用程式。

第一次執行此作業時，推送和提取容器映像可能需要一些時間；在快取基底圖層後，所花費的時間就會大幅降低。

```console
draft up
```

輸出：

```console
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (3.0007s)
example-java: Releasing Application: SUCCESS ⚓  (0.9322s)
example-java: Build ID: 01C9NPDYQQH2CZENDMZW7ESJAM
Inspect the logs with `draft logs 01C9NPDYQQH2CZENDMZW7ESJAM`
```

## <a name="test-the-application"></a>測試應用程式

若要測試應用程式，請使用 `draft connect` 命令。 此命令會將連線 Proxy 到允許安全本機連線的 Kubernetes Pod。 完成時，就可以在提供的 URL 存取應用程式。

在某些情況下，可能需要幾分鐘的時間下載容器映像，並啟動應用程式。 如果存取應用程式時收到錯誤，請重試連線。

```console
draft connect
```

輸出：

```console
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

現在請瀏覽至 http://localhost:46143 以測試應用程式 (在上述範例中，您的連接埠可能會不同)。 測試完應用程式時，請使用 `Control+C` 停止 Proxy 連線。

> [!NOTE]
> 您也可以使用 `draft up --auto-connect` 命令來建置和部署應用程式，並立即連線到第一個執行的容器以加快反覆運算週期。

## <a name="expose-application"></a>公開應用程式

在 Kubernetes 中測試應用程式時，您可以讓應用程式在網際網路上供人使用。 使用 [LoadBalancer][kubernetes-service-loadbalancer] \(英文\) 類型的 Kubernetes 服務或[輸入控制器][kubernetes-ingress] \(英文\)，即可達到此目的。 本文件詳細說明如何使用 Kubernetes 服務。


首先，必須更新 Draft 套件，以指定應建立 `LoadBalancer` 類型的服務。 若要進行，請更新 `values.yaml` 檔案中的服務類型。

```console
vi charts/java/values.yaml
```

找出 `service.type` 屬性並將值從 `ClusterIP` 更新為 `LoadBalancer`。

```yaml
replicaCount: 2
image:
  repository: openjdk
  tag: 8-jdk-alpine
  pullPolicy: IfNotPresent
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
  ```

執行 `draft up` 以重新執行應用程式。

```console
draft up
```

可能需要幾分鐘的時間，服務才能傳回公用 IP 位址。 若要監視進度，請使用 `kubectl get service` 命令搭配監看式。

```console
kubectl get service -w
```

一開始，服務的 *EXTERNAL-IP* 會顯示為 `pending`。

```
example-java-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

當 EXTERNAL-IP 位址從 `pending` 變為 `IP address` 之後，請使用 `Control+C` 來停止 kubectl 監看式流程。

```
example-java-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
```

若要查看應用程式，請瀏覽至外部 IP 位址。

```console
curl 52.175.224.118
```

輸出：

```
Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>針對應用程式反覆執行

現在，Draft 已設定好，且應用程式正在 Kubernetes 中執行，您已準備好反覆執行程式碼。 每當您想要測試更新的程式碼時，請執行 `draft up` 命令，以更新執行中的應用程式。

此範例是更新 Java hello world 應用程式。

```console
vi src/main/java/helloworld/Hello.java
```

更新 Hello World 文字。

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
}
```

執行 `draft up --auto-connect` 命令，在 Pod 已可回應時立即重新部署應用程式。

```console
draft up --auto-connect
```

輸出

```
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (4.0010s)
example-java: Releasing Application: SUCCESS ⚓  (1.1336s)
example-java: Build ID: 01C9NPMJP6YM985GHKDR2J64KC
Inspect the logs with `draft logs 01C9NPMJP6YM985GHKDR2J64KC`
Connect to java:4567 on localhost:39249
Your connection is still active.
Connect to java:4567 on localhost:39249
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567

```

最後，檢視應用程式以查看更新。

```console
curl 52.175.224.118
```

輸出：

```
Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>後續步驟

如需有關使用 Draft 的詳細資訊，請參閱 GitHub 上的 Draft 文件。

> [!div class="nextstepaction"]
> [Draft 文件][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[install-draft]: https://github.com/Azure/draft/blob/master/docs/install.md
[kubernetes-ingress]: ./ingress.md
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[aks-quickstart]: ./kubernetes-walkthrough.md
