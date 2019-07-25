---
title: 在 Azure Kubernetes Service (AKS) 中以動態方式建立適用於多個 Pod 的檔案磁碟區
description: 了解如何透過 Azure 檔案服務以動態方式建立永續性磁碟區，以搭配 Azure Kubernetes Service (AKS) 中的多個平行 Pod 使用
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/08/2019
ms.author: mlearned
ms.openlocfilehash: 580363973afd918351931edfb187a1a8d38d6985
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2019
ms.locfileid: "67665977"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中以動態方式建立和使用 Azure 檔案服務的永續性磁碟區

永續性磁碟區代表一塊已佈建來與 Kubernetes Pod 搭配使用的儲存體。 永續性磁碟區可供一個或多個 Pod 使用，且可以動態或靜態方式佈建。 如果多個 pod 需要平行存取相同的存放磁片區, 您可以使用 Azure 檔案儲存體, 以使用[伺服器訊息區 (SMB) 通訊協定][smb-overview]進行連接。 本文會示範如何在 Azure Kubernetes Service (AKS) 叢集中以動態方式建立 Azure 檔案共用，以供多個 Pod 使用。

如需有關 Kubernetes 磁片區的詳細資訊, 請參閱[AKS 中應用程式的儲存體選項][concepts-storage]。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集, 請參閱[使用 Azure CLI][aks-quickstart-cli]或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您也需要安裝並設定 Azure CLI 版本2.0.59 或更新版本。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級, 請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="create-a-storage-class"></a>建立儲存體類別

儲存體類別可用來定義 Azure 檔案共用的建立方式。 系統會自動在[節點資源群組][node-resource-group]中建立儲存體帳戶, 以搭配儲存類別來存放 Azure 檔案共用。 選擇下列適用于*skuName*的[Azure 儲存體冗余][storage-skus]:

* *Standard_LRS* - 標準本地備援儲存體 (LRS)
* *Standard_GRS* - 標準異地備援儲存體 (GRS)
* *Standard_RAGRS* - 標準讀取權限異地備援儲存體 (RA-GRS)

> [!NOTE]
> Azure 檔案儲存體在執行 Kubernetes 1.13 或更高版本的 AKS 叢集中支援 premium 儲存體。

如需 Azure 檔案儲存體的 Kubernetes 儲存體類別的詳細資訊, 請參閱[Kubernetes 儲存體類別][kubernetes-storage-classes]。

建立名為 `azure-file-sc.yaml` 的檔案，然後將下列資訊清單範例複製進來。 如需*mountOptions*的詳細資訊, 請參閱[掛接選項][mount-options]一節。

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

使用[kubectl apply][kubectl-apply]命令來建立儲存體類別:

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-cluster-role-and-binding"></a>建立叢集角色和繫結

AKS 叢集使用 Kubernetes 角色型存取控制 (RBAC) 來限制可以執行的動作。 「角色」會定義要授與的權限，而「繫結」會將角色套用至需要的使用者。 這些指派可以套用至指定的命名空間或在整個叢集中套用。 如需詳細資訊, 請參閱[使用 RBAC 授權][kubernetes-rbac]。

若要允許 Azure 平台建立必要的儲存體資源，請建立 *ClusterRole* 和 *ClusterRoleBinding*。 建立名為 `azure-pvc-roles.yaml` 的檔案，然後將下列 YAML 複製進來：

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: system:azure-cloud-provider
rules:
- apiGroups: ['']
  resources: ['secrets']
  verbs:     ['get','create']
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: system:azure-cloud-provider
roleRef:
  kind: ClusterRole
  apiGroup: rbac.authorization.k8s.io
  name: system:azure-cloud-provider
subjects:
- kind: ServiceAccount
  name: persistent-volume-binder
  namespace: kube-system
```

使用[kubectl apply][kubectl-apply]命令指派許可權:

```console
kubectl apply -f azure-pvc-roles.yaml
```

## <a name="create-a-persistent-volume-claim"></a>建立永續性磁碟區宣告

永續性磁碟區宣告 (PVC) 會使用儲存體類別物件，以動態方式佈建 Azure 檔案共用。 下列 YAML 可用來建立具有 ReadWriteMany 存取權，且大小為 5GB 的永續性磁碟區宣告。 如需有關存取模式的詳細資訊, 請參閱[Kubernetes 持續性磁片][access-modes]區檔。

現在，建立名為 `azure-file-pvc.yaml` 的檔案，然後將下列 YAML 複製進來。 請確定 storageClassName 與最後一個步驟中建立的儲存體類別相符。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

使用[kubectl apply][kubectl-apply]命令來建立持續性磁片區宣告:

```console
kubectl apply -f azure-file-pvc.yaml
```

完成之後，便會建立檔案共用。 此外，也會建立 Kubernetes 祕密，其中包含連線資訊和認證。 您可以使用[kubectl get][kubectl-get]命令來查看 PVC 的狀態:

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>使用永續性磁碟區

下列 YAML 所建立的 Pod，會使用永續性磁碟區宣告 azurefile，將 Azure 檔案共用裝載在 /mnt/azure 路徑。 若是 Windows Server 容器 (目前在 AKS 中處於預覽狀態), 請使用 Windows 路徑慣例指定*mountPath* , 例如「 *d:* 」。

建立名為 `azure-pvc-files.yaml` 的檔案，然後將下列 YAML 複製進來。 請確定claimName 與最後一個步驟中建立的 PVC 相符。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

使用[kubectl apply][kubectl-apply]命令建立 pod。

```console
kubectl apply -f azure-pvc-files.yaml
```

您現在已有一個 Azure 檔案共用掛接在 /mnt/azure 目錄中的執行中 Pod。 當您透過 `kubectl describe pod mypod` 檢查 Pod 時，可以看到這項設定。 下列扼要範例輸出顯示容器中掛接的磁碟區：

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>掛接選項

Kubernetes 版本之間的預設 fileMode 和 dirMode 值不同，如下表中所述。

| 版本 | value |
| ---- | ---- |
| v1.6.x、v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| v1.8.6 或以上版本 | 0755 |
| v1.9.0 | 0700 |
| v1.9.1 或以上版本 | 0755 |

如果使用 1.8.5 版或更新版本的叢集，並透過儲存體類別以動態方式建立永續性磁碟區，則可以在儲存體類別物件上指定裝載選項。 下列範例會設定 0777：

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

如果您是使用 1.8.0-1.8.4 版的叢集，可將 runAsUser 值設定為 0 來指定資訊安全內容。 如需 Pod 安全性內容的詳細資訊, 請參閱[設定安全性內容][kubernetes-security-context]。

## <a name="next-steps"></a>後續步驟

如需相關的最佳作法, 請參閱[AKS 中儲存和備份的最佳作法][operator-best-practices-storage]。

使用「Azure 檔案」來深入了解 Kubernetes 永續性磁碟區。

> [!div class="nextstepaction"]
> [適用於 Azure 檔案的 Kubernetes 外掛程式][kubernetes-files] \(英文\)

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks