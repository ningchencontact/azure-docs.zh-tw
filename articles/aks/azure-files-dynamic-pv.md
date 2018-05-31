---
title: 搭配 AKS 使用 Azure 檔案
description: 搭配 AKS 使用 Azure 磁碟
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/17/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 991db1fc32ae89ab04ca040cfb6e8d59ffe5262f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356438"
---
# <a name="persistent-volumes-with-azure-files"></a>包含 Azure 檔案的永續性磁碟區

永續性磁碟區是一塊已建立而可在 Kubernetes 叢集中使用的儲存體。 永續性磁碟區可供一個或多個 Pod 使用，且可以動態或靜態方式建立。 本文件詳述如何**以動態方式建立**作為永續性磁碟區的 Azure 檔案共用。

如需有關 Kubernetes 永續性磁碟區的詳細資訊 (包括靜態建立)，請參閱 [Kubernetes 永續性磁碟區][kubernetes-volumes]。

## <a name="create-storage-account"></a>建立儲存體帳戶

在以動態方式將 Azure 檔案共用建立為 Kubernetes 磁碟區時，您可以使用任何儲存體帳戶，只要該帳戶位於與 AKS 叢集相同的資源群組中即可。 如有需要，請在與 AKS 叢集相同的資源群組中建立儲存體帳戶。

若要找出適當的資源群組，請使用 [az group list][az-group-list] 命令。

```azurecli-interactive
az group list --output table
```

尋找其名稱類似 `MC_clustername_clustername_locaton` 的資源群組。

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

使用 [kubectl apply][kubectl-apply] 命令來建立儲存體類別。

```azurecli-interactive
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>建立永續性磁碟區宣告

永續性磁碟區宣告 (PVC) 會使用儲存體類別物件，以動態方式佈建 Azure 檔案共用。

下列 YAML 可用來建立具有 `ReadWriteOnce` 存取權，且大小為 `5GB` 的永續性磁碟區宣告。 如需有關存取模式的詳細資訊，請參閱 [Kubernetes 永續性磁碟區][access-modes]文件。

建立名為 `azure-file-pvc.yaml` 的檔案，然後將下列 YAML 複製進來。 請確定 `storageClassName` 與最後一個步驟中建立的儲存體類別相符。

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

使用 [kubectl apply][kubectl-apply] 命令來建立永續性磁碟區宣告。

```azurecli-interactive
kubectl apply -f azure-file-pvc.yaml
```

完成之後，便會建立檔案共用。 此外，也會建立 Kubernetes 祕密，其中包含連線資訊和認證。

## <a name="using-the-persistent-volume"></a>使用永續性磁碟區

下列 YAML 所建立的 Pod，會使用永續性磁碟區宣告 `azurefile`，將 Azure 檔案共用裝載在 `/mnt/azure` 路徑。

建立名為 `azure-pvc-files.yaml` 的檔案，然後將下列 YAML 複製進來。 請確定 `claimName` 與最後一個步驟中建立的 PVC 相符。

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

使用 [kubectl apply][kubectl-apply] 命令來建立 Pod。

```azurecli-interactive
kubectl apply -f azure-pvc-files.yaml
```

您現在已有一個 Azure 磁碟掛接在 `/mnt/azure` 目錄中的執行中 Pod。 當您透過 `kubectl describe pod mypod` 檢查 Pod 時，可以看到這項設定。

## <a name="mount-options"></a>掛接選項

Kubernetes 版本之間的預設 fileMode 和 dirMode 值不同，如下表中所述。

| version | value |
| ---- | ---- |
| v1.6.x、v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| v1.8.6 或以上版本 | 0755 |
| v1.9.0 | 0700 |
| v1.9.1 或以上版本 | 0755 |

如果使用 1.8.5 版或更新版本的叢集，並透過儲存體類別以動態方式建立永續性磁碟區，則可以在儲存體類別物件上指定裝載選項。 下列範例會設定 `0777`。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

如果使用 1.8.5 版或更新版本的叢集，並以靜態方式建立永續性磁碟區物件，則必須在 `PersistentVolume` 物件上指定裝載選項。 如需有關以靜態方式建立永續性磁碟區的詳細資訊，請參閱[靜態永續性磁碟區][pv-static]。

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: azurefile
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  ```

如果您是使用 1.8.0-1.8.4 版的叢集，可將 `runAsUser` 值設定為 `0` 來指定資訊安全內容。 如需關於 Pod 資訊安全內容的詳細資訊，請參閱[設定資訊安全內容][kubernetes-security-context]。

## <a name="next-steps"></a>後續步驟

使用「Azure 檔案」來深入了解 Kubernetes 永續性磁碟區。

> [!div class="nextstepaction"]
> [適用於 Azure 檔案的 Kubernetes 外掛程式][kubernetes-files] \(英文\)

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
