---
title: 搭配 AKS 使用 Azure 檔案
description: 搭配 AKS 使用 Azure 磁碟
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a5126bc4c5e7c9cd9832f33fc908e6c8b9e02b91
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/16/2018
---
# <a name="persistent-volumes-with-azure-files"></a>包含 Azure 檔案的永續性磁碟區

永續性磁碟區代表一塊已佈建而可在 Kubernetes 叢集中使用的儲存體。 永續性磁碟區可供一個或多個 Pod 使用，且可以動態或靜態方式佈建。 本文件詳述在 AKS 叢集中作為 Kubernetes 永續性磁碟區的 Azure 檔案共用動態佈建。 

如需有關 Kubernetes 永續性磁碟區的詳細資訊，請參閱 [Kubernetes 永續性磁碟區][kubernetes-volumes]。

## <a name="create-storage-account"></a>建立儲存體帳戶

在以動態方式將 Azure 檔案共用佈建為 Kubernetes 磁碟區時，您可以使用任何儲存體帳戶，只要該帳戶包含在與 AKS 叢集相同的資源群組中即可。 如有需要，請在與 AKS 叢集相同的資源群組中建立儲存體帳戶。 

若要找出適當的資源群組，請使用 [az group list][az-group-list] 命令。

```azurecli-interactive
az group list --output table
```

您正在尋找名稱類似 `MC_clustername_clustername_locaton` 的資源群組，其中 clustername 是您的 AKS 叢集名稱，而 location 是已佈建叢集的 Azure 區域。

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

請使用 [az storage account create][az-storage-account-create] 命令來建立儲存體帳戶。 

使用此範例時，請將 `--resource-group` 更新成資源群組的名稱，並將 `--name` 更新成您選擇的名稱。

```azurecli-interactive
az storage account create --resource-group MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>建立儲存體類別

儲存體類別可用來定義 Azure 檔案共用的建立方式。 您可以在類別中指定特定的儲存體帳戶。 如果未指定儲存體帳戶，就必須指定 `skuName` 和 `location`，而系統將會評估相關資源群組中的所有儲存體帳戶以尋找相符項目。

如需有關適用於 Azure 檔案之 Kubernetes 儲存體類別的詳細資訊，請參閱 [Kubernetes 儲存體類別][kubernetes-storage-classes]。

建立名為 `azure-file-sc.yaml` 的檔案，然後將下列資訊清單複製進來。 將 `storageAccount` 更新成您目標儲存體帳戶的名稱。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: mystorageaccount
```

使用 [kubectl create][kubectl-create] 命令來建立儲存體類別。

```azurecli-interactive
kubectl create -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>建立永續性磁碟區宣告

永續性磁碟區宣告 (PVC) 會使用儲存體類別物件，以動態方式佈建 Azure 檔案共用。 

下列資訊清單可用來建立具有 `ReadWriteOnce` 存取權，且大小為 `5GB` 的永續性磁碟區宣告。

建立名為 `azure-file-pvc.yaml` 的檔案，然後將下列資訊清單複製進來。 請確定 `storageClassName` 與最後一個步驟中建立的儲存體類別相符。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

使用 [kubectl create][kubectl-create] 命令來建立永續性磁碟區宣告。

```azurecli-interactive
kubectl create -f azure-file-pvc.yaml
```

完成之後，便會建立檔案共用。 此外，也會建立 Kubernetes 祕密，其中包含連線資訊和認證。

## <a name="using-the-persistent-volume"></a>使用永續性磁碟區

下列資訊清單所建立的 Pod，會使用永續性磁碟區宣告 `azurefile`，將 Azure 檔案共用掛接在 `/mnt/azure` 路徑。

建立名為 `azure-pvc-files.yaml` 的檔案，然後將下列資訊清單複製進來。 請確定 `claimName` 與最後一個步驟中建立的 PVC 相符。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

使用 [kubectl create][kubectl-create] 命令來建立 Pod。

```azurecli-interactive
kubectl create -f azure-pvc-files.yaml
```

您現在已有一個 Azure 磁碟掛接在 `/mnt/azure` 目錄中的執行中 Pod。 您可以在透過 `kubectl describe pod mypod`檢查 Pod 時，查看磁碟區掛接。

## <a name="next-steps"></a>後續步驟

使用「Azure 檔案」來深入了解 Kubernetes 永續性磁碟區。

> [!div class="nextstepaction"]
> [適用於 Azure 檔案的 Kubernetes 外掛程式][kubernetes-files] \(英文\)

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
