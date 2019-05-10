---
title: 建立 NFS (Network File System) Ubuntu 伺服器以供 pod 的 Azure Kubernetes Service (AKS)
description: 了解如何手動建立的 pod 中 Azure Kubernetes Service (AKS) 使用 NFS Ubuntu Linux 伺服器磁碟區
services: container-service
author: ozboms
ms.service: container-service
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 55eb5b0b98a4097d2f300bacabbfef3b0a32b27b
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468495"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>手動建立，並使用 NFS (Network File System) Linux 伺服器磁碟區與 Azure Kubernetes Service (AKS)
容器之間共用資料，通常是以容器為基礎的服務和應用程式的必要元件。 此外，您通常會有各種需要存取相同的資訊，在外部的永續性磁碟區上的 pod。    
雖然 Azure 檔案的選項，建立 Azure VM 上的 NFS 伺服器是永續性的共用儲存體的另一種。 

本文將說明如何建立 Ubuntu 虛擬機器上的 NFS 伺服器。 並也會將 AKS 容器存取授與此共用的檔案系統。

## <a name="before-you-begin"></a>開始之前
本文假設您有現有的 AKS 叢集。 如果您需要 AKS 叢集，請參閱 AKS 快速入門[使用 Azure CLI] [ aks-quickstart-cli]或是[使用 Azure 入口網站][aks-quickstart-portal]。

您的 AKS 叢集必須位於相同或對等互連虛擬網路，做為 NFS 伺服器。 叢集必須建立在現有的 VNET 中，可能會與您的 VM 相同的 VNET。

文件中說明的步驟，使用現有的 VNET 設定：[現有的 VNET 中建立 AKS 叢集][ aks-virtual-network]和[與 VNET 對等互連連線虛擬網路][peer-virtual-networks]

它也假設您已建立 Ubuntu Linux 虛擬機器 (例如 18.04 LTS)。 設定和大小可以是個人喜好來設定，而且可以透過 Azure 部署。 Linux 快速入門中，請參閱[管理 linux VM][linux-create]。

如果您第一次部署您的 AKS 叢集，Azure 會自動填入 [虛擬網路] 欄位時部署 Ubuntu 電腦，使其相同的 VNET 內的即時。 但是，如果您想要改為使用對等互連的網路，請參閱上述文件。

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>部署到虛擬機器上的 NFS 伺服器
以下是要設定 NFS 伺服器，您的 Ubuntu 虛擬機器內的指令碼：
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
伺服器會重新啟動 （因為指令碼），您可以掛接到 AKS NFS 伺服器

>[!IMPORTANT]  
>請務必取代**AKS_SUBNET**與正確的密碼從您的叢集或"*"會開啟所有連接埠和連接到您 NFS 伺服器。

您已建立您的 VM 之後，請將上述指令碼複製到檔案。 然後，您可以移動它從本機電腦，或每當指令碼時，VM 使用： 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
一旦您的指令碼位於您的 VM，您可以透過 ssh 連線至 VM，並透過命令執行：
```console
sudo ./nfs-server-setup.sh
```
如果執行失敗，因為權限遭拒錯誤，請設定透過命令的執行權限：
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>NFS 伺服器的連線將 AKS 叢集
我們可以連接到我們的叢集的 NFS 伺服器，藉由佈建永續性磁碟區並指定如何存取磁碟區的永續性磁碟區宣告。  
連接這兩項服務在相同或對等互連的虛擬網路中是必要的。 這裡的指示設定相同的 VNET 中的叢集：[現有 VNET 中建立 AKS 叢集][aks-virtual-network]

當它們位於相同的虛擬網路 （或對等互連） 時，您需要佈建永續性磁碟區和永續性磁碟區宣告在 AKS 叢集中。 容器可以再掛接到其本機目錄的 NFS 磁碟機。

以下是定義範例 kubernetes 永續性磁碟區 （此定義會假設您的叢集和 VM 位於相同的 VNET）：

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
取代**NFS_INTERNAL_IP**， **NFS_NAME**並**NFS_EXPORT_FILE_PATH** NFS 伺服器資訊。

您也需要永續性磁碟區宣告檔案。 以下是要包含的項目範例：

>[!IMPORTANT]  
>**「 storageClassName"** 必須保留為空白字串或宣告將無法運作。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>疑難排解
如果您無法連線到伺服器，從叢集中，問題可能是匯出的目錄中或其父代，沒有足夠的權限來存取伺服器。

請檢查您匯出的目錄和其父目錄都具有 777 權限。

您可以執行下列命令來檢查權限，而且目錄應該 *'drwxrwxrwx'* 權限：
```console
ls -l
```

## <a name="more-information"></a>詳細資訊
若要取得完整的逐步解說，或協助您偵錯您的 NFS 伺服器設定，如下的深入教學課程：
  - [NFS 教學課程][nfs-tutorial]

## <a name="next-steps"></a>後續步驟

如需相关的最佳做法，请参阅[在 AKS 中存储和备份的最佳做法][operator-best-practices-storage]。

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: https://docs.microsoft.com/azure/aks/configure-kubenet#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
