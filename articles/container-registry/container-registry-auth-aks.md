---
title: 從 Azure Kubernetes Service 對 Azure Container Registry 進行驗證
description: 了解如何使用 Azure Active Directory 服務主體，提供從 Azure Kubernetes Service 對您私人容器登錄中的映像的存取。
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: nepeters
ms.openlocfilehash: 0888afbb9087251e2c9219e2eb32fbf0d5600304
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
ms.locfileid: "33767881"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>從 Azure Kubernetes Service 對 Azure Container Registry 進行驗證

當您搭配使用 Azure Container Service (AKS) 與 Azure Kubernetes Registry (ACR) 時，必須建立驗證機制。 這份文件詳細說明在這兩個 Azure 服務之間進行驗證所建議的組態。

## <a name="grant-aks-access-to-acr"></a>將 AKS 存取權授與 ACR

建立 AKS 叢集時，也會建立服務主體來管理 Azure 資源的叢集可操作性。 此服務主體也可用於向 ACR 登錄進行驗證。 若要這樣做，需要建立角色指派，以將服務主體讀取存取權授與 ACR 資源。

可使用下列範例來完成此作業。

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>使用 Kubernetes 祕密進行存取

在某些情況下，AKS 使用的服務主體無法侷限在 ACR 登錄的範圍內。 對於這種情況，您可以建立唯一的服務主體，並將範圍侷限於 ACR 登錄。

下列指令碼可以用來建立服務主體。

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create a contributor role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principle client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

服務主體認證現在可以儲存在 Kubernetes[映像提取秘密][image-pull-secret]中，而且在 AKS 叢集中執行容器時可以參考。

下列命令會建立 Kubernetes 秘密。 將伺服器名稱改為 ACR 登入伺服器將使用者名稱取代為服務主體識別碼，將密碼改為服務主體密碼。

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Kubernetes 秘密可用於使用 `ImagePullSecrets` 參數的 Pod 部署。

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets
