---
title: Azure 上的 Kubernetes 教學課程 - 更新叢集
description: Azure 上的 Kubernetes 教學課程 - 更新叢集
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 04/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0886d13b62b6b8ad1c0dcd430ce48bcc51d6d465
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>教學課程：在 Azure Kubernetes Service (AKS) 中升級 Kubernetes

使用 Azure CLI 可以升級 Azure Kubernetes Service (AKS) 叢集。 在升級過程中，會仔細地[隔離並清空][kubernetes-drain] Kubernetes 節點，將執行應用程式的中斷情況降到最低。

在本教學課程 (八個章節的第八部分) 中升級了 Kubernetes 叢集。 您完成的工作包括：

> [!div class="checklist"]
> * 識別目前和可用的 Kubernetes 版本
> * 升級 Kubernetes 節點
> * 驗證升級成功

## <a name="before-you-begin"></a>開始之前

在先前的教學課程中，已將應用程式封裝成容器映像、將此映像上傳至 Azure Container Registry，並已建立 Kubernetes 叢集。 該應用程式接著便在 Kubernetes 叢集上執行。

如果您尚未完成這些步驟，而想要跟著做，請回到[教學課程 1 – 建立容器映像][aks-tutorial-prepare-app]。


## <a name="get-cluster-versions"></a>取得叢集版本

升級叢集之前，請使用 `az aks get-upgrades` 命令檢查哪些 Kubernetes 版本可進行升級。

```azurecli
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

在此範例中，目前的節點版本是 `1.7.9`及 upgrades 資料行下可用的升級版本。

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------------------
default  myResourceGroup  1.7.9            1.7.9              1.7.12, 1.8.1, 1.8.2, 1.8.6, 1.8.7
```

## <a name="upgrade-cluster"></a>升級叢集

使用 `az aks upgrade` 命令升級叢集節點。 下列範例將叢集更新為版本 `1.8.2`。

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
```

輸出：

```json
{
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "location": "eastus",
  "name": "myAKSCluster",
  "properties": {
    "accessProfiles": {
      "clusterAdmin": {
        "kubeConfig": "..."
      },
      "clusterUser": {
        "kubeConfig": "..."
      }
    },
    "agentPoolProfiles": [
      {
        "count": 1,
        "dnsPrefix": null,
        "fqdn": null,
        "name": "myAKSCluster",
        "osDiskSizeGb": null,
        "osType": "Linux",
        "ports": null,
        "storageProfile": "ManagedDisks",
        "vmSize": "Standard_D2_v2",
        "vnetSubnetId": null
      }
    ],
    "dnsPrefix": "myK8sClust-myResourceGroup-4f48ee",
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.eastus.azmk8s.io",
    "kubernetesVersion": "1.8.2",
    "linuxProfile": {
      "adminUsername": "azureuser",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "..."
          }
        ]
      }
    },
    "provisioningState": "Succeeded",
    "servicePrincipalProfile": {
      "clientId": "e70c1c1c-0ca4-4e0a-be5e-aea5225af017",
      "keyVaultSecretRef": null,
      "secret": null
    }
  },
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-upgrade"></a>驗證升級

使用 `az aks show` 命令，確認升級是否成功。

```azurecli
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

輸出：

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已在 AKS 叢集中升級 Kubernetes。 已完成下列工作：

> [!div class="checklist"]
> * 識別目前和可用的 Kubernetes 版本
> * 升級 Kubernetes 節點
> * 驗證升級成功

跟著此連結以深入了解 AKS。

> [!div class="nextstepaction"]
> [AKS 概觀][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md