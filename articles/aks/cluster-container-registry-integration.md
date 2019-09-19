---
title: 將 Azure Container Registry 與 Azure Kubernetes Service 整合
description: 瞭解如何整合 Azure Kubernetes Service （AKS）與 Azure Container Registry （ACR）
services: container-service
author: mlearned
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 09/17/2018
ms.author: mlearned
ms.openlocfilehash: ab744efd205d826cb7ae2c3eda7bba28f4a9bee0
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097811"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>從 Azure Kubernetes Service 對 Azure Container Registry 進行驗證

當您搭配使用 Azure Container Service (AKS) 與 Azure Kubernetes Registry (ACR) 時，必須建立驗證機制。 本文詳細說明在這兩個 Azure 服務之間進行驗證所建議的組態。

您可以使用 Azure CLI 的幾個簡單命令來設定 ACR 整合的 AKS。

## <a name="before-you-begin"></a>開始之前

您必須滿足以下條件：

* **Azure 訂**用帳戶的**擁有**者或**azure 帳戶管理員**角色
* 您也需要 Azure CLI 版2.0.73 或更新版本
* 您需要在用戶端上[安裝 docker](https://docs.docker.com/install/) ，而且需要[docker hub](https://hub.docker.com/)的存取權

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>使用 ACR 整合建立新的 AKS 叢集

您可以在初始建立 AKS 叢集期間，設定 AKS 和 ACR 整合。  為了讓 AKS 叢集與 ACR 互動，會使用 Azure Active Directory**服務主體**。 下列 CLI 命令可讓您授權訂用帳戶中現有的 ACR，並為服務主體設定適當的**ACRPull**角色。 請為您的參數提供有效的值。  方括弧中的參數是選擇性的。
```azurecli
az login
az acr create -n myContainerRegistry -g myContainerRegistryResourceGroup --sku basic [in case you do not have an existing ACR]
az aks create -n myAKSCluster -g myResourceGroup --attach-acr <acr-name-or-resource-id>
```
**ACR 資源識別碼的格式如下：** 

/subscriptions/< 訂用帳戶-d >/resourceGroups/< 資源群組-名稱 >/providers/Microsoft.ContainerRegistry/registries/{name} 
  
此步驟可能需要幾分鐘的時間才能完成。

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>為現有的 AKS 叢集設定 ACR 整合

提供**acr 名稱**或**acr 資源識別碼**的有效值，以整合現有的 ACR 與現有的 AKS 叢集，如下所示。

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

您也可以使用下列程式來移除 ACR 與 AKS 叢集之間的整合
```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```


## <a name="log-in-to-your-acr"></a>登入您的 ACR

使用下列命令登入您的 ACR。  將<acrname>參數取代為您的 ACR 名稱。  例如，預設值為**aks < 您的資源群組 > acr**。

```azurecli
az acr login -n <acrName>
```

## <a name="pull-an-image-from-docker-hub-and-push-to-your-acr"></a>從 docker hub 提取映射並推送至您的 ACR

從 docker hub 提取映射、標記映射，然後推送至您的 ACR。

```console
acrloginservername=$(az acr show -n <acrname> -g <myResourceGroup> --query loginServer -o tsv)
docker pull nginx
```

```
$ docker tag nginx $acrloginservername/nginx:v1
$ docker push $acrloginservername/nginx:v1

The push refers to repository [someacr1.azurecr.io/nginx]
fe6a7a3b3f27: Pushed
d0673244f7d4: Pushed
d8a33133e477: Pushed
v1: digest: sha256:dc85890ba9763fe38b178b337d4ccc802874afe3c02e6c98c304f65b08af958f size: 948
```

## <a name="update-the-state-and-verify-pods"></a>更新狀態並確認 pod

請執行下列步驟來驗證您的部署。

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

查看 yaml 檔案，並以您的 ACR 登入伺服器、映射和標籤取代值來編輯 image 屬性。

```
$ cat acr-nginx.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <replace this image property with you acr login server, image and tag>
        ports:
        - containerPort: 80

$ kubectl apply -f acr-nginx.yaml
$ kubectl get pods

You should have two running pods.

NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
