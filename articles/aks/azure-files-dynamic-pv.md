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
ms.openlocfilehash: d468944883cca80946001724c38dd5ec9ba0d94f
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="persistent-volumes-with-azure-files---dynamic-provisioning"></a>永續性磁碟區與 Azure 檔案-動態佈建

永續性磁碟區表示一段已佈建 Kubernetes 叢集中使用的儲存體。 永續性磁碟區可供一個或多個 pod，且可以動態或靜態佈建。 本文件詳述動態佈建的 Azure 檔案共用為 Kubernetes 永續性磁碟區 AKS 叢集中。 

如需有關 Kubernetes 永續性磁碟區的詳細資訊，請參閱[Kubernetes 永續性磁碟區][kubernetes-volumes]。

## <a name="prerequisites"></a>必要條件

動態佈建時為 Kubernetes 磁碟區的 Azure 檔案共用，就可以使用任何儲存體帳戶，只要它包含在與 AKS 叢集相同的資源群組。 如有需要建立儲存體帳戶與 AKS 叢集相同的資源群組中。 

若要識別適當的資源群組，請使用[az 群組清單][ az-group-list]命令。

```azurecli-interactive
az group list --output table
```

下列範例輸出顯示的資源群組，同時與 AKS 叢集相關聯。 資源群組的名稱類似*MC_myAKSCluster_myAKSCluster_eastus*包含 AKS 叢集資源，而且是必須建立儲存體帳戶的位置。 

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

一旦已經識別的資源群組，建立儲存體帳戶與[az 儲存體帳戶建立][ az-storage-account-create]命令。

```azurecli-interactive
az storage account create --resource-group  MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>建立儲存類別

儲存類別用來定義動態建立的永續性磁碟區的設定方式。 項目，例如 Azure 儲存體帳戶名稱、 SKU 和區域中的儲存類別物件定義。 如需有關 Kubernetes 儲存類別的詳細資訊，請參閱[Kubernetes 儲存類別][kubernetes-storage-classes]。

下列範例會指定 SKU 的任何儲存體帳戶輸入`Standard_LRS`中`eastus`要求儲存體時，就可以使用區域。 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  skuName: Standard_LRS
```

若要使用特定的儲存體帳戶，`storageAccount`可用參數。

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

永續性磁碟區宣告會儲存類別物件使用動態佈建的儲存體。 使用 Azure 檔案時，選取或指定儲存類別物件中的儲存體帳戶中建立的 Azure 檔案共用。

>  [!NOTE]
>   請確定適當的儲存體帳戶預先建立為 AKS 叢集相同的資源群組中。 永續性磁碟區宣告將無法佈建 Azure 檔案共用如果無法使用儲存體帳戶。 

下列資訊清單可以用來建立持續性磁碟區宣告`5GB`大小的`ReadWriteOnce`存取。 如需有關 PVC 存取模式的詳細資訊，請參閱[存取模式][access-modes]。

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

## <a name="using-the-persistent-volume"></a>使用持續性磁碟區

一旦已建立的永續性磁碟區的宣告，並已成功佈建的儲存體，可以存取磁碟區建立為 pod。 下列資訊清單會建立使用永續性磁碟區宣告 pod`azurefile`裝載在 Azure 的檔案共用`/var/www/html`路徑。 

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

Kubernetes 版本之間的預設 fileMode 和 dirMode 值不同下, 表中所述。 

| version | value |
| ---- | ---- |
| v1.6.x、 v1.7.x | 0777 |
| v1.8.0 v1.8.5 | 0700 |
| v1.8.6 或更新版本 | 0755 |
| v1.9.0 | 0700 |
| v1.9.1 或更新版本 | 0755 |

如果使用叢集的版本 1.8.5 或更多，掛接的存放裝置類別物件，您可以指定選項。 下列範例會設定`0777`。 

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

如果使用叢集的版本 1.8.0-1.8.4，您可以使用指定的安全性內容`runAsUser`值設定為`0`。 如需 Pod 的安全性內容的詳細資訊，請參閱[設定安全性內容][kubernetes-security-context]。

## <a name="next-steps"></a>後續步驟

深入了解使用 Azure 檔案 Kubernetes 永續性磁碟區。

> [!div class="nextstepaction"]
> [Azure 檔案的 Kubernetes 外掛程式][kubernetes-files]

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