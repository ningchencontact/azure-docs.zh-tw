---
title: 搭配 AKS 使用 Azure 磁碟
description: 搭配 AKS 使用 Azure 磁碟
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.openlocfilehash: 36e25d7e5f1e5c6e1cf72442b73ac081810d216a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="persistent-volumes-with-azure-disks"></a>包含 Azure 磁碟的永續性磁碟區

永續性磁碟區代表一塊已佈建來與 Kubernetes Pod 搭配使用的儲存體。 永續性磁碟區可供一個或多個 Pod 使用，且可以動態或靜態方式佈建。 如需有關 Kubernetes 永續性磁碟區的詳細資訊，請參閱 [Kubernetes 永續性磁碟區][kubernetes-volumes]。

本文件詳述如何在 Azure Container Service (AKS) 叢集內搭配 Azure 磁碟使用永續性磁碟區。

## <a name="built-in-storage-classes"></a>內建儲存體類別

儲存體類別可用來定義如何搭配永續性磁碟區動態建立儲存體單位。 如需有關 Kubernetes 儲存體類別的詳細資訊，請參閱 [Kubernetes 儲存體類別][kubernetes-storage-classes]。

每個 AKS 叢集都包含兩個預先建立的儲存體類別，而且這兩個類別都設定為使用 Azure 磁碟。 `default` 儲存體類別會佈建標準 Azure 磁碟。 `managed-premium` 儲存體類別會佈建進階 Azure 磁碟。 如果您叢集內的 AKS 節點使用進階儲存體，請選取 `managed-premium` 類別。

使用 [kubectl get sc][kubectl-get] 命令來查看預先建立的儲存體類別。

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

## <a name="create-persistent-volume-claim"></a>建立永續性磁碟區宣告

永續性磁碟區宣告 (PVC) 可用來根據儲存體類別，動態佈建儲存體。 在此情況下，PVC 可以使用其中一個預先建立的儲存體類別，來建立標準或進階 Azure 受控磁碟。

建立名為 `azure-premimum.yaml` 的檔案，然後將下列資訊清單複製進來。

請注意，註釋中已指定 `managed-premium` 儲存體類別，而宣告所要求的是一個大小為 `5GB`、具有 `ReadWriteOnce` 存取權的磁碟。 

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: managed-premium
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

使用 [kubectl create][kubectl-create] 命令來建立永續性磁碟區宣告。

```azurecli-interactive
kubectl create -f azure-premimum.yaml
```

> [!NOTE]
> 您只能使用存取模式類型 ReadWriteOnce 來掛接 Azure 磁碟，以讓它僅供單一 AKS 節點使用。 如果您需要在多個節點之間共用永續性磁碟區，請考慮使用 [Azure 檔案][azure-files-pvc]。

## <a name="using-the-persistent-volume"></a>使用永續性磁碟區

在建立永續性磁碟區宣告，並成功佈建磁碟之後，就能建立可存取磁碟的 Pod。 下列資訊清單所建立的 Pod，會使用永續性磁碟區宣告 `azure-managed-disk`，將 Azure 磁碟掛接在 `/mnt/azure` 路徑。 

建立名為 `azure-pvc-disk.yaml` 的檔案，然後將下列資訊清單複製進來。

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
        claimName: azure-managed-disk
```

使用 [kubectl create][kubectl-create] 命令來建立 Pod。

```azurecli-interactive
kubectl create -f azure-pvc-disk.yaml
```

您現在已有一個 Azure 磁碟掛接在 `/mnt/azure` 目錄中的執行中 Pod。 您可以在透過 `kubectl describe pod mypod`檢查 Pod 時，查看磁碟區掛接。

## <a name="next-steps"></a>後續步驟

深入了解使用 Azure 磁碟的 Kubernetes 永續性磁碟區。

> [!div class="nextstepaction"]
> [Azure 磁碟的 Kubernetes 外掛程式][kubernetes-disk] (英文)

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md