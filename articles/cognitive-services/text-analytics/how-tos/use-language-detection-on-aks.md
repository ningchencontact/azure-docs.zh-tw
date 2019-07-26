---
title: 執行 Kubernetes 服務
titleSuffix: Text Analytics - Azure Cognitive Services
description: 將語言偵測容器以及執行範例部署到 Azure Kubernetes Service，並在網頁瀏覽器中進行測試。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: ba6fde66b6173bdbff8e9acc08b16f47c5bf7ea4
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377107"
---
# <a name="deploy-the-language-detection-container-to-azure-kubernetes-service"></a>將語言偵測容器部署到 Azure Kubernetes Service

了解如何部署語言偵測容器。 此程式說明如何建立本機 Docker 容器、將容器推送至您自己的私人容器登錄、在 Kubernetes 叢集中執行容器, 以及在網頁瀏覽器中進行測試。 使用容器可以將您的注意力轉移到管理基礎結構之外, 改為專注于應用程式開發。

## <a name="prerequisites"></a>必要條件

此程序需要必須安裝並在本機執行的多個工具。 請勿使用 Azure Cloud Shell。 您需要下列項目：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。
* 適用于您作業系統的[Git](https://git-scm.com/downloads) , 讓您可以複製此程式中所使用的[範例](https://github.com/Azure-Samples/cognitive-services-containers-samples)。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* [Docker 引擎](https://www.docker.com/products/docker-engine), 並驗證 docker CLI 可在主控台視窗中運作。
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe)。
* 具有正確定價層的 Azure 資源。 並非所有的定價層都會使用這個容器︰
    * 只有 F0 或標準定價層的**Azure 文字分析**資源。
    * 具有 S0 定價層的**Azure 認知服務**資源。

## <a name="run-the-sample"></a>執行範例

此程式會載入並執行認知服務的容器範例, 以進行語言偵測。 此範例有兩個容器，一個用於用戶端應用程式，一個用於認知服務容器。 您必須將這兩個映射推送到您自己的 Azure Container Registry 實例。 在您自己的登錄之後, 請建立 Azure Kubernetes Service 的實例 (AKS), 以存取這些映射並執行容器。 當容器正在執行時, 請使用**kubectl** CLI 來監看容器的效能。 使用 HTTP 要求存取用戶端應用程式，並查看結果。

![執行範例容器的概念](../media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>範例容器

此範例有兩個容器映射。 一個適用于前端網站。 第二個影像是語言偵測容器, 它會傳回所偵測到的文字語言 (文化特性)。 當您完成時, 可以從外部 IP 存取這兩個容器。

### <a name="the-language-front-end-container"></a>語言前端容器

此網站相當於您本身提出語言偵測端點要求的用戶端應用程式。 程序完成時，您可以使用 `http://<external-IP>/<text-to-analyze>` 存取瀏覽器中的網站容器，收到所偵測到的字元字串語言。 此 URL 的範例是 `http://132.12.23.255/helloworld!`。 瀏覽器中的結果是 `English`。

### <a name="the-language-container"></a>語言容器

在此特定程式中, 任何外部要求都可以存取語言偵測容器。 容器不會以任何方式變更, 因此可使用標準認知服務容器特定的語言偵測 API。

對於此容器，該 API 是語言偵測的 POST 要求。 對於所有認知服務容器，您可以從其託管的 Swagger 資訊 `http://<external-IP>:5000/swagger/index.html` 深入了解容器。

連接埠 5000 是與認知服務容器搭配使用的預設連接埠。

## <a name="create-an-azure-container-registry-service"></a>建立 Azure Container Registry 服務

若要將容器部署至 Azure Kubernetes Service, 必須可存取容器映射。 建立您自己的 Azure Container Registry 服務來裝載映像。

1. 登入 Azure CLI。

    ```azurecli
    az login
    ```

1. 建立名為 `cogserv-container-rg` 的資源群組來保存在此程序中建立的每個資源。

    ```azurecli
    az group create --name cogserv-container-rg --location westus
    ```

1. 建立您自己的 Azure Container Registry 實例, 其格式為您的名稱, `registry`後面接著。 例如 `pattyregistry`。 請勿在名稱中使用破折號或底線字元。

    ```azurecli
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    將結果儲存以取得 **loginServer** 屬性。 此屬性是裝載容器位址的一部分, 稍後會在檔案中`language.yml`使用。

    ```console
    > az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    {
        "adminUserEnabled": false,
        "creationDate": "2019-01-02T23:49:53.783549+00:00",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry",
        "location": "westus",
        "loginServer": "pattyregistry.azurecr.io",
        "name": "pattyregistry",
        "provisioningState": "Succeeded",
        "resourceGroup": "cogserv-container-rg",
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

1. 登入您的容器登錄。 您必須先登入, 才能將映射推送至您的登錄。

    ```azurecli
    az acr login --name pattyregistry
    ```

## <a name="get-the-website-docker-image"></a>取得網站 Docker 映射

1. 此程序中使用的範例程式碼是在認知服務容器範例儲存機制中。 複製存放庫來取得範例的本機副本。

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    在存放庫位於您的本機電腦上之後, 請在[\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService)目錄中尋找網站。 此網站會作為用戶端應用程式, 以呼叫語言偵測容器中裝載的語言偵測 API。 

1. 建置此網站的 Docker 映像。 當您執行下列命令時, 請確定主控台位於 Docker 檔案所在的[\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService)目錄中:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    若要追蹤容器登錄庫的版本，請將標記加入版本格式，例如 `v1`。 

1. 將映像推送至容器登錄。 此步驟可能需要幾分鐘的時間。 

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    如果您收到`unauthorized: authentication required`錯誤, 請`az acr login --name <your-container-registry-name>`使用命令登入。 

    當處理常式完成時, 結果會類似于:

    ```console
    > docker push pattyregistry.azurecr.io/language-frontend:v1
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-the-language-detection-docker-image"></a>取得語言偵測 Docker 映射 

1. 將最新版 Docker 映像提取到本機電腦。 此步驟可能需要幾分鐘的時間。 如果此容器有較新的版本, 請將值從`1.1.006770001-amd64-preview`變更為較新的版本。 

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. 使用您的容器登錄標記映射。 尋找最新版本, 如果您有較`1.1.006770001-amd64-preview`新的版本, 請取代版本。 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. 將映像推送至容器登錄。 此步驟可能需要幾分鐘的時間。 

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>取得容器登錄認證

若要取得所需的資訊, 以便將您的容器登錄與您稍後在此程式中建立 Azure Kubernetes Service 的實例進行連線, 您需要執行下列步驟。

1. 建立服務主體。

    ```azurecli
    az ad sp create-for-rbac --skip-assignment
    ```

    對於步驟 3 中的受託人參數 `<appId>` 儲存結果 `appId` 值。 針對下一節的用戶端密碼參數`<client-secret>`儲存密碼。

    ```console
    > az ad sp create-for-rbac --skip-assignment
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. 取得容器登錄 ID。

    ```azurecli
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    在下一個步驟中儲存範圍參數值的輸出 `<acrId>`。 如下所示：

    ```console
    > az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    對於這個區段的步驟 3 儲存完整值。 

1. 若要授予 AKS 叢集所需的正確存取權，以使用容器登錄中儲存的映像，請建立角色指派。 使用在前兩個步驟中所蒐集的值，取代 `<appId>` 和 `<acrId>`。

    ```azurecli
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-the-azure-kubernetes-service-cluster"></a>建立 Azure Kubernetes Service 叢集

1. 建立 Kubernetes 叢集。 除了 name 參數之外的所有參數值均來自先前區段。 選擇表示建立者及其用途的名稱，例如 `patty-kube`。 

    ```azurecli
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    此步驟可能需要幾分鐘的時間。 結果如下： 

    ```console
    > az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    {
      "aadProfile": null,
      "addonProfiles": null,
      "agentPoolProfiles": [
        {
          "count": 2,
          "dnsPrefix": null,
          "fqdn": null,
          "maxPods": 110,
          "name": "nodepool1",
          "osDiskSizeGb": 30,
          "osType": "Linux",
          "ports": null,
          "storageProfile": "ManagedDisks",
          "vmSize": "Standard_DS1_v2",
          "vnetSubnetId": null
        }
      ],
      "dnsPrefix": "patty-kube--65a101",
      "enableRbac": true,
      "fqdn": "patty-kube--65a101-341f1f54.hcp.westus.azmk8s.io",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/cogserv-container-rg/providers/Microsoft.ContainerService/managedClusters/patty-kube",
      "kubernetesVersion": "1.9.11",
      "linuxProfile": {
        "adminUsername": "azureuser",
        "ssh": {
          "publicKeys": [
            {
              "keyData": "ssh-rsa AAAAB3NzaC...ohR2d81mFC
            }
          ]
        }
      },
      "location": "westus",
      "name": "patty-kube",
      "networkProfile": {
        "dnsServiceIp": "10.0.0.10",
        "dockerBridgeCidr": "172.17.0.1/16",
        "networkPlugin": "kubenet",
        "networkPolicy": null,
        "podCidr": "10.244.0.0/16",
        "serviceCidr": "10.0.0.0/16"
      },
      "nodeResourceGroup": "MC_patty_westus",
      "provisioningState": "Succeeded",
      "resourceGroup": "cogserv-container-rg",
      "servicePrincipalProfile": {
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "keyVaultSecretRef": null,
        "secret": null
      },
      "tags": null,
      "type": "Microsoft.ContainerService/ManagedClusters"
    }
    ```

    此服務已建立, 但尚未擁有網站容器或語言偵測容器。 

1. 取得 Kubernetes 叢集的認證。 

    ```azurecli
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>將協調流程定義載入到您的 Kubernetes 服務

本節會使用**kubectl** CLI 來與您的 Azure Kubernetes Service 實例溝通。 

1. 在您載入協調流程定義之前, 請檢查以確定**kubectl**可存取節點。

    ```console
    kubectl get nodes
    ```

    回應如下所示：

    ```console
    > kubectl get nodes
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. 複製下列檔案, 並將它`language.yml`命名為。 檔案有 `service` 區段和 `deployment` 區段，分別用於兩種容器類型，而且有 `language-frontend` 網站容器和 `language` 偵測容器。 

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. 根據下表變更語言的`language.yml`前端部署行, 以新增您自己的容器登錄映射名稱、用戶端密碼和文字分析設定。

    語言前端部署設定|用途|
    |--|--|
    |行 32<br> `image` 屬性|容器登錄中前端映射的映射位置<br>`<container-registry-name>.azurecr.io/language-frontend:v1`.|
    |行 44<br> `name` 屬性|映射的容器登錄密碼, `<client-secret>`在上一節中稱為。|

1. `language.yml`根據下表變更的語言部署行, 以新增您自己的容器登錄映射名稱、用戶端密碼和文字分析設定。

    |語言部署設定|用途|
    |--|--|
    |行 78<br> `image` 屬性|容器登錄中的語言映射的映射位置<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`.|
    |行 95<br> `name` 屬性|映射的容器登錄密碼, `<client-secret>`在上一節中稱為。|
    |行 91<br> `apiKey` 屬性|您的文字分析資源金鑰。|
    |行 92<br> `billing` 屬性|文字分析資源的計費端點。<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`.|

    由於**apiKey**和**計費端點**屬性會設定為 Kubernetes 協調流程定義的一部分, 因此網站容器不需要知道這些專案, 或在要求中傳遞它們。 網站容器是指其協調器名稱為 `language` 的語言偵測容器。 

1. 從您建立和儲存 `language.yml` 的資料夾中，載入此範例的協調流程定義檔。 

    ```console
    kubectl apply -f language.yml
    ```

    回應如下：

    ```console
    > kubectl apply -f language.yml
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>取得容器的外部 IP

針對這兩個容器, 確認`language-frontend`和`language`服務正在執行, 並取得外部 IP 位址。 

```console
kubectl get all
```

```console
> kubectl get all
NAME                                     READY     STATUS    RESTARTS   AGE
pod/language-586849d8dc-7zvz5            1/1       Running   0          13h
pod/language-frontend-68b9969969-bz9bg   1/1       Running   1          13h

NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
service/kubernetes          ClusterIP      10.0.0.1      <none>          443/TCP          14h
service/language            LoadBalancer   10.0.39.169   104.42.172.68   5000:30161/TCP   13h
service/language-frontend   LoadBalancer   10.0.42.136   104.42.37.219   80:30943/TCP     13h

NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/language            1         1         1            1           13h
deployment.extensions/language-frontend   1         1         1            1           13h

NAME                                                 DESIRED   CURRENT   READY     AGE
replicaset.extensions/language-586849d8dc            1         1         1         13h
replicaset.extensions/language-frontend-68b9969969   1         1         1         13h

NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/language            1         1         1            1           13h
deployment.apps/language-frontend   1         1         1            1           13h

NAME                                           DESIRED   CURRENT   READY     AGE
replicaset.apps/language-586849d8dc            1         1         1         13h
replicaset.apps/language-frontend-68b9969969   1         1         1         13h
```

如果服務`EXTERNAL-IP`的顯示為 [擱置], 請重新執行命令, 直到顯示 IP 位址, 再移至下一個步驟。 

## <a name="test-the-language-detection-container"></a>測試語言偵測容器

開啟瀏覽器, 並移至上一節中`language`容器的外部 IP:。 `http://<external-ip>:5000/swagger/index.html` 使用 API 的功能來測試語言偵測端點。 `Try it` 

![查看容器的 Swagger 檔](../media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>測試用戶端應用程式容器

使用下列格式, 將瀏覽器中的 URL 變更為`language-frontend`容器的外部 IP:。 `http://<external-ip>/helloworld` `helloworld` 的英文文化特性文字預測為 `English`。

## <a name="clean-up-resources"></a>清除資源

當您完成叢集時, 請刪除 Azure 資源群組。 

```azure-cli
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>相關資訊

* [適用于 Docker 使用者的 kubectl](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>後續步驟 

* 使用更多[認知服務容器](../../cognitive-services-container-support.md)。
* 使用[文字分析已連線的服務](../vs-text-connected-service.md)。


<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->