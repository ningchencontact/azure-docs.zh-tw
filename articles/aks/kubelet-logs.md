---
title: 從 Azure Container Service (AKS) 取得 kubelet 記錄
description: 從 Azure Container Service (AKS) 叢集節點取得 kubelet 記錄
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 56e20a9f9d17eac01e6f85007db41dcc417f83e4
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="get-kubelet-logs-from-azure-container-service-aks-cluster-nodes"></a>從 Azure Container Service (AKS) 叢集節點取得 kubelet 記錄

有時候您可能需要就疑難排解的用途，從 Azure Container Service (AKS) 節點取得 kubelet 記錄。 本文件詳述用來提取這些記錄的其中一個選項。

## <a name="create-an-ssh-connection"></a>建立 SSH 連線

首先，請針對您需要提取 kubelet 記錄的節點建立 SSH 連線。 這項作業會在[透過 SSH 連線至 Azure Container Service (AKS) 叢集節點][aks-ssh]文件中詳細說明。

## <a name="get-kubelet-logs"></a>取得 kubelet 記錄

一旦您已連線到節點後，請執行下列命令以提取 kubelet 記錄。

```azurecli-interactive
docker logs $(docker ps -a | grep "hyperkube kubele" | awk -F ' ' '{print $1}')
```

輸出範例：

```console
2018-03-05 00:04:00.883195 I | proto: duplicate proto type registered: google.protobuf.Any
2018-03-05 00:04:00.883242 I | proto: duplicate proto type registered: google.protobuf.Duration
2018-03-05 00:04:00.883253 I | proto: duplicate proto type registered: google.protobuf.Timestamp
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
Flag --non-masquerade-cidr has been deprecated, will be removed in a future version
I0305 00:04:00.978560    8021 feature_gate.go:144] feature gates: map[Accelerators:true]
I0305 00:04:00.978996    8021 azure.go:174] azure: using client_id+client_secret to retrieve access token
I0305 00:04:00.979041    8021 server.go:439] Successfully initialized cloud provider: "azure" from the config file: "/etc/kubernetes/azure.json"
I0305 00:04:00.979058    8021 server.go:740] cloud provider determined current node name to be aks-nodepool1-42032720-0
```

<!-- LINKS - internal -->
[aks-ssh]: aks-ssh.md