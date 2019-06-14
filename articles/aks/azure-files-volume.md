---
title: 在 Azure Kubernetes Service (AKS) 中建立適用於多個 Pod 的靜態磁碟區
description: 了解如何透過 Azure 檔案服務手動建立磁碟區，以搭配 Azure Kubernetes Service (AKS) 中的多個平行 Pod 使用
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: 5f3c1331e2b005b136a015c537d0fc18406ca9d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072063"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中手動建立和使用 Azure 檔案共用的磁碟區

容器型應用程式常常需要存取和保存外部資料磁碟區中的資料。 如果有多個 Pod 需要並行存取相同的儲存體磁碟區，您可以透過[伺服器訊息區 (SMB) 通訊協定][smb-overview]來使用 Azure 檔案服務進行連線。 本文會示範如何手動建立 Azure 檔案共用，並將其連結到 AKS 中的 Pod。

如需有關 Kubernetes 磁碟區的詳細資訊，請參閱 < [AKS 中的應用程式的儲存體選項][concepts-storage]。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您也需要 Azure CLI 2.0.59 版或更新版本安裝並設定。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用

在使用 Azure 檔案服務作為 Kubernetes 磁碟區之前，您必須建立 Azure 儲存體帳戶與檔案共用。 下列命令會建立名為的資源群組*myAKSShare*，儲存體帳戶和名為的檔案共用*aksshare*:

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

請記下指令碼輸出末端所示的儲存體帳戶名稱和金鑰。 當您在下列其中一個步驟內建立 Kubernetes 磁碟區時，會需要這些值。

## <a name="create-a-kubernetes-secret"></a>建立 Kubernetes 祕密

Kubernetes 必須要有認證才能存取前面步驟中建立的檔案共用。 這些認證會儲存在 [Kubernetes 祕密][kubernetes-secret]，並在建立 Kubernetes Pod 時加以參考。

使用 `kubectl create secret` 命令建立秘密。 下列範例會建立名為 azure-secret  的共用，並填入前面步驟中的 azurestorageaccountname  和 azurestorageaccountkey  。 若要使用現有的 Azure 儲存體帳戶，請提供帳戶名稱和金鑰。

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>將檔案共用掛接為磁碟區

若要將 Azure 檔案共用掛接至您的 Pod，請在容器規格中設定磁碟區。建立一個名為 `azure-files-pod.yaml` 且含有下列內容的新檔案。 如果您變更了檔案共用或祕密的名稱，請更新 shareName  和 secretName  。 若有需要，請更新 `mountPath`，這是 Pod 中檔案共用掛接所在的路徑。 適用於 Windows Server 容器 （目前處於預覽 AKS 中） 指定*mountPath*使用 Windows 路徑慣例，例如 *'d ':* 。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

使用 `kubectl` 命令建立 Pod。

```console
kubectl apply -f azure-files-pod.yaml
```

您現在已有一個 Azure 檔案共用掛接在 /mnt/azure  的執行中 Pod。 您可以使用 `kubectl describe pod mypod` 來驗證是否已成功掛接共用。 下列扼要範例輸出顯示容器中掛接的磁碟區：

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="mount-options"></a>掛接選項

Kubernetes 版本之間的預設 fileMode  和 dirMode  值不同，如下表中所述。

| version | value |
| ---- | ---- |
| v1.6.x、v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| v1.8.6 或以上版本 | 0755 |
| v1.9.0 | 0700 |
| v1.9.1 或以上版本 | 0755 |

如果使用 1.8.5 版或更新版本的叢集，並以靜態方式建立永續性磁碟區物件，則必須在 PersistentVolume  物件上指定裝載選項。

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

如果您是使用 1.8.0-1.8.4 版的叢集，可將 runAsUser  值設定為 0  來指定資訊安全內容。 如需關於 Pod 資訊安全內容的詳細資訊，請參閱[設定資訊安全內容][kubernetes-security-context]。

## <a name="next-steps"></a>後續步驟

如需相關聯的最佳作法，請參閱[儲存體和 AKS 中的備份的最佳做法][operator-best-practices-storage]。

如需有關 AKS 叢集與 Azure 檔案服務互動的詳細資訊，請參閱[適用於 Azure 檔案服務的 Kubernetes 外掛程式][kubernetes-files]。

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
