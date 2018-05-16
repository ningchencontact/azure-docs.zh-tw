---
title: 搭配 AKS 使用 Azure 磁碟
description: 搭配 AKS 使用 Azure 磁碟
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: b790213e19b9f2aaef74a3f670c89246f54fd6d7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="volumes-with-azure-disks"></a>包含 Azure 磁碟的磁碟區

容器型應用程式常常需要存取和保存外部資料磁碟區中的資料。 Azure 磁碟可用來作為這個外部資料存放區。 本文將詳細說明如何在 Azure Kubernetes Service (AKS) 叢集中使用 Azure 磁碟作為 Kubernetes 磁碟區。

如需有關 Kubernetes 磁碟區的詳細資訊，請參閱 [Kubernetes 磁碟區][kubernetes-volumes]。

## <a name="create-an-azure-disk"></a>建立 Azure 磁碟

在掛接 Azure 受控磁碟作為 Kubernetes 磁碟區之前，磁碟必須存在於與 AKS 叢集資源相同的資源群組中。 若要尋找此資源群組，請使用 [az group list][az-group-list] 命令。

```azurecli-interactive
az group list --output table
```

您正在尋找名稱類似 `MC_clustername_clustername_locaton` 的資源群組，其中 clustername 是您的 AKS 叢集名稱，而 location 是已佈建叢集的 Azure 區域。

```console
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

使用 [az disk create][az-disk-create] 命令來建立 Azure 磁碟。

使用此範例時，請將 `--resource-group` 更新成資源群組的名稱，並將 `--name` 更新成您選擇的名稱。

```azurecli-interactive
az disk create \
  --resource-group MC_myAKSCluster_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

一旦建立磁碟，您應該會看到類似下列的輸出。 這個值是磁碟識別碼，將磁碟掛接至 Kubernetes Pod 時會使用此識別碼。

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>將磁碟掛接為磁碟區

藉由在容器規格中設定磁碟區，將 Azure 磁碟掛接至您的 Pod。

建立一個名為 `azure-disk-pod.yaml` 且含有下列內容的新檔案。 使用新建立的磁碟名稱更新 `diskName`，以及使用磁碟識別碼更新 `diskURI`。 此外，請記下 `mountPath`，這是 Pod 中 Azure 磁碟掛接所在的路徑。

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-disk-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

使用 kubectl 來建立 Pod。

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

您現在已有一個 Azure 磁碟掛接在 `/mnt/azure` 的執行中 Pod。

## <a name="next-steps"></a>後續步驟

深入了解使用 Azure 磁碟的 Kubernetes 磁碟區。

> [!div class="nextstepaction"]
> [Azure 磁碟的 Kubernetes 外掛程式][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
