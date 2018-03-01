---
title: "搭配 AKS 使用 Azure 檔案"
description: "搭配 AKS 使用 Azure 磁碟"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/04/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ce37cfdd70f95822a912f6ea71b9e4a3f9a30a14
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2018
---
# <a name="persistent-volumes-with-azure-files"></a>包含 Azure 檔案的永續性磁碟區

永續性磁碟區代表一塊已佈建而可在 Kubernetes 叢集中使用的儲存體。 永續性磁碟區可供一個或多個 Pod 使用，且可以動態或靜態方式佈建。 本文件詳述在 AKS 叢集中作為 Kubernetes 永續性磁碟區的 Azure 檔案共用動態佈建。 

如需有關 Kubernetes 永續性磁碟區的詳細資訊，請參閱 [Kubernetes 永續性磁碟區][kubernetes-volumes]。

## <a name="prerequisites"></a>先決條件

在以動態方式將 Azure 檔案共用佈建為 Kubernetes 磁碟區時，您可以使用任何儲存體帳戶，只要該帳戶包含在與 AKS 叢集相同的資源群組中即可。 如有需要，請在與 AKS 叢集相同的資源群組中建立儲存體帳戶。 

若要找出適當的資源群組，請使用 [az group list][az-group-list] 命令。

```azurecli-interactive
az group list --output table
```

下列範例輸出所顯示的資源群組都與 AKS 叢集相關聯。 擁有 MC_myAKSCluster_myAKSCluster_eastus 之類名稱的資源群組便包含 AKS 叢集資源，而您必須將儲存體帳戶建立在這樣的資源群組中。 

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

找出資源群組後，請使用 [az storage account create][az-storage-account-create] 命令建立儲存體帳戶。

```azurecli-interactive
az storage account create --resource-group  MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>建立儲存體類別

儲存體類別可用來定義動態建立之永續性磁碟區的設定方式。 諸如 Azure 儲存體帳戶名稱、SKU 和區域等項目，都會定義在儲存體類別物件中。 如需有關 Kubernetes 儲存體類別的詳細資訊，請參閱 [Kubernetes 儲存體類別][kubernetes-storage-classes]。

下列範例會指定在要求儲存體時，可以使用 `eastus` 區域中屬於 SKU 類型 `Standard_LRS` 的任何儲存體帳戶。 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  skuName: Standard_LRS
```

若要使用特定的儲存體帳戶，您可以使用 `storageAccount` 參數。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: azure_storage_account_name
```

## <a name="create-persistent-volume-claim"></a>建立永續性磁碟區宣告

永續性磁碟區宣告會使用儲存體類別物件，以動態方式佈建一塊儲存體。 在使用 Azure 檔案時，Azure 檔案共用會建立在儲存體類別物件中選取或指定的儲存體帳戶內。

>  [!NOTE]
>   請確定您已在與 AKS 叢集資源相同的資源群組中，預先建立適當的儲存體帳戶。 此資源群組擁有 MC_myAKSCluster_myAKSCluster_eastus 之類的名稱。 如果無法使用儲存體帳戶，永續性磁碟區宣告就無法佈建 Azure 檔案共用。 

下列資訊清單可用來建立具有 `ReadWriteOnce` 存取權，且大小為 `5GB` 的永續性磁碟區宣告。 如需有關 PVC 存取模式的詳細資訊，請參閱[存取模式][access-modes]。

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

## <a name="using-the-persistent-volume"></a>使用永續性磁碟區

在建立永續性磁碟區宣告，並成功佈建儲存體之後，就能建立可存取磁碟區的 Pod。 下列資訊清單所建立的 Pod，會使用永續性磁碟區宣告 `azurefile`，將 Azure 檔案共用掛接在 `/var/www/html` 路徑。 

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
      - mountPath: "/var/www/html"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

## <a name="mount-options"></a>掛接選項

Kubernetes 版本之間的預設 fileMode 和 dirMode 值不同，如下表中所述。 

| version | value |
| ---- | ---- |
| v1.6.x、v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| v1.8.6 或以上版本 | 0755 |
| v1.9.0 | 0700 |
| v1.9.1 或以上版本 | 0755 |

如果您是使用 1.8.5 版或更高版本的叢集，則可以在儲存體類別物件上指定掛接選項。 下列範例會設定 `0777`。 

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

如果您是使用 1.8.0-1.8.4 版的叢集，可將 `runAsUser` 值設定為 `0` 來指定資訊安全內容。 如需關於 Pod 資訊安全內容的詳細資訊，請參閱[設定資訊安全內容][kubernetes-security-context]。

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
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create