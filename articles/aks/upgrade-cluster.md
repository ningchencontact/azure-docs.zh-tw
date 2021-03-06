---
title: 升級 Azure Kubernetes Service (AKS) 叢集
description: 升級 Azure Kubernetes Service (AKS) 叢集
services: container-service
author: gabrtv
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/18/2018
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 9557311c97ea0fde66790c37b08d1a22d1197405
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144579"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>升級 Azure Kubernetes Service (AKS) 叢集

Azure Kubernetes Service (AKS) 可讓您輕鬆地執行一般管理工作，包括升級 Kubernetes 叢集。

## <a name="upgrade-an-aks-cluster"></a>升級 AKS 叢集

升級叢集之前，請使用 `az aks get-upgrades` 命令檢查哪些 Kubernetes 版本可進行升級。

```azurecli-interactive
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

輸出：

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  -------------------
default  mytestaks007     1.8.10           1.8.10             1.9.1, 1.9.2, 1.9.6
```

我們有三個版本可進行升級：1.9.1、1.9.2 和 1.9.6。 我們可以使用 `az aks upgrade` 命令，升級為最新的可用版本。  在升級過程中，AKS 會將新節點新增至叢集，然後仔細地[隔離並清空][kubernetes-drain]節點 (一次一個)，將中斷執行中應用程式的情況降到最低。

> [!NOTE]
> 升級 AKS 叢集時，無法略過 Kubernetes 次要版本。 例如，允許 1.8.x -> 1.9.x 或 1.9.x -> 1.10.x 之間的升級，但不允許 1.8 -> 1.10 的升級。

```azurecli-interactive
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.9.6
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
    "kubernetesVersion": "1.9.6",
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

使用 `az aks show` 命令，確認升級是否成功。

```azurecli-interactive
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

輸出：

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.9.6                 Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>後續步驟

使用 AKS 教學課程深入了解部署和管理 AKS。

> [!div class="nextstepaction"]
> [AKS 教學課程][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md