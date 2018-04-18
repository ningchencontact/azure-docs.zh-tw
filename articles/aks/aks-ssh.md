---
title: 透過 SSH 連線至 Azure Container Service (AKS) 叢集
description: 建立 Azure Container Service (AKS) 叢集節點的 SSH 連線
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 085a2976443db8ece7a36dbfc133b173432ce4c8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="ssh-into-azure-container-service-aks-cluster-nodes"></a>透過 SSH 連線至 Azure Container Service (AKS) 叢集

有時候，您可能需要存取 Azure Container Service (AKS) 節點以進行維護、記錄收集或其他疑難排解作業。 Azure Container Service (AKS) 節點不會公開至網際網路。 請使用本文詳述的步驟建立 AKS 節點的 SSH 連線。

## <a name="get-aks-node-address"></a>取得 AKS 節點位址

使用 `az vm list-ip-addresses` 命令取得 AKS 叢集節點的 IP 位址。 使用您 AKS 資源群組的名稱取代資源群組名稱。

```console
$ az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table

VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-42032720-0  10.240.0.6
aks-nodepool1-42032720-1  10.240.0.5
aks-nodepool1-42032720-2  10.240.0.4
```

## <a name="create-ssh-connection"></a>建立 SSH 連線

執行 `debian` 容器映像，並將終端機工作階段與它連結。 接著，您可以使用容器搭配 AKS 叢集中的任何節點來建立 SSH 工作階段。

```console
kubectl run -it --rm aks-ssh --image=debian
```

在容器中安裝 SSH 用戶端。

```console
apt-get update && apt-get install openssh-client -y
```

開啟第二個終端機，並列出所有 Pod 以取得新建立的 Pod 名稱。

```console
$ kubectl get pods

NAME                       READY     STATUS    RESTARTS   AGE
aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
```

將 SSH 金鑰複製到 Pod，然後將 Pod 名稱取代為適當的值。

```console
kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
```

更新 `id_rsa` 檔案，讓它成為使用者唯讀狀態。

```console
chmod 0600 id_rsa
```

現在建立對 AKS 節點的 SSH 連線。 AKS 叢集的預設使用者名稱是 `azureuser`。 如果此帳戶在叢集建立時有所變更，請取代為適當的管理員使用者名稱。

```console
$ ssh -i id_rsa azureuser@10.240.0.6

Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

48 packages can be updated.
0 updates are security updates.


*** System restart required ***
Last login: Tue Feb 27 20:10:38 2018 from 167.220.99.8
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@aks-nodepool1-42032720-0:~$
```

## <a name="remove-ssh-access"></a>移除 SSH 存取

完成時，結束 SSH 工作階段，然後結束互動式容器工作階段。 這個動作會刪除用於從 AKS 叢集進行 SSH 存取的 Pod。