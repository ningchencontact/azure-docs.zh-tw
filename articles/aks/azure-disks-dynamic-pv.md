---
title: "搭配 AKS 使用 Azure 磁碟"
description: "搭配 AKS 使用 Azure 磁碟"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/25/2018
ms.author: nepeters
ms.openlocfilehash: aa89cf9fe4e2cd5b63017558e89401de86effdc9
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2018
---
# <a name="persistent-volumes-with-azure-disks"></a>包含 Azure 磁碟的永續性磁碟區

永續性磁碟區代表一塊已佈建而可在 Kubernetes 叢集中使用的儲存體。 永續性磁碟區可供一個或多個 Pod 使用，且可以動態或靜態方式佈建。 本文件詳述如何以動態方式將 Azure 磁碟佈建為 AKS 叢集中的 Kubernetes 永續性磁碟區。 

如需有關 Kubernetes 永續性磁碟區的詳細資訊，請參閱 [Kubernetes 永續性磁碟區][kubernetes-volumes]。

## <a name="built-in-storage-classes"></a>內建儲存體類別

儲存體類別可用來定義動態建立之永續性磁碟區的設定方式。 如需有關 Kubernetes 儲存體類別的詳細資訊，請參閱 [Kubernetes 儲存體類別][kubernetes-storage-classes]。

每個 AKS 叢集都包含兩個預先建立的儲存體類別，而且這兩個類別都設定為使用 Azure 磁碟。 請使用 `kubectl get storageclass` 命令查看這兩個類別。

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

如果這兩個儲存體類別適合您的需要，您就不必另外建立新的類別。

## <a name="create-storage-class"></a>建立儲存體類別

如果您想要建立新的儲存體類別，並設定為用於 Azure 磁碟，您可以使用下列資訊清單範例來進行此作業。 

`Standard_LRS` 的 `storageaccounttype` 值表示所建立的是標準磁碟。 此值可變更為 `Premium_LRS` 以便建立[進階磁碟][premium-storage]。 若要使用進階磁碟，AKS 節點虛擬機器必須有與進階磁碟相容的大小。 請參閱[這份文件][premium-storage]，以取得相容大小清單。

```yaml
apiVersion: storage.k8s.io/v1beta1
kind: StorageClass
metadata:
  name: azure-managed-disk
provisioner: kubernetes.io/azure-disk
parameters:
  kind: Managed
  storageaccounttype: Standard_LRS
```

## <a name="create-persistent-volume-claim"></a>建立永續性磁碟區宣告

永續性磁碟區宣告會使用儲存體類別物件，以動態方式佈建一塊儲存體。 使用 Azure 磁碟時，磁碟會建立在與 AKS 資源相同的資源群組中。

此資訊清單範例會使用 `azure-managed-disk` 儲存體類別建立永續性磁碟區宣告，以便建立大小為 `5GB` 且具有 `ReadWriteOnce` 存取權的磁碟。 如需有關 PVC 存取模式的詳細資訊，請參閱[存取模式][access-modes]。

> [!NOTE]
> 您只能使用存取模式類型 ReadWriteOnce 來掛接 Azure 磁碟，以讓它僅供單一 AKS 節點使用。 如果您需要在多個節點之間共用永續性磁碟區，請考慮使用 [Azure 檔案][azure-files-pvc]。 

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

## <a name="using-the-persistent-volume"></a>使用永續性磁碟區

在建立永續性磁碟區宣告，並成功佈建磁碟之後，就能建立可存取磁碟的 Pod。 下列資訊清單所建立的 Pod，會使用永續性磁碟區宣告 `azure-managed-disk`，將 Azure 磁碟掛接在 `/var/www/html` 路徑。 

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
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>後續步驟

深入了解使用 Azure 磁碟的 Kubernetes 永續性磁碟區。

> [!div class="nextstepaction"]
> [Azure 磁碟的 Kubernetes 外掛程式][kubernetes-disk] (英文)

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md