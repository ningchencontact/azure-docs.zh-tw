---
title: "透過 SSH 連線至 Azure Container Service (AKS) 叢集"
description: "建立 Azure Container Service (AKS) 叢集節點的 SSH 連線"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/28/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 00affc3d1c02c477826261aeac6e092934037e81
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="ssh-into-azure-container-service-aks-cluster-nodes"></a>透過 SSH 連線至 Azure Container Service (AKS) 叢集

有時候，您可能需要存取 Azure Container Service (AKS) 節點以進行維護、記錄收集或其他疑難排解作業。 Azure Container Service (AKS) 節點不會公開至網際網路。 請使用本文詳述的步驟建立 AKS 節點的 SSH 連線。

## <a name="configure-ssh-access"></a>設定 SSH 存取

 若要透過 SSH 連線至特定節點，必須建立具有 `hostNetwork` 存取的 Pod。 此外也會針對 Pod 存取建立一項服務。 此設定具特殊權限，並應在使用後移除。

建立名為 `aks-ssh.yaml` 的檔案，並複製到此資訊清單中。 以目標 AKS 節點的名稱更新節點名稱。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-ssh
spec:
  selector:
    app: aks-ssh
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 22
    targetPort: 22
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aks-ssh
  labels:
    app: aks-ssh
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-ssh
  template:
    metadata:
      labels:
        app: aks-ssh
    spec:
      containers:
      - name: alpine
        image: alpine:latest
        ports:
        - containerPort: 22
        command: ["/bin/sh", "-c", "--"]
        args: ["while true; do sleep 30; done;"]
      hostNetwork: true
      nodeName: aks-nodepool1-42032720-0
```

執行資訊清單以建立 Pod 和服務。

```azurecli-interactive
$ kubectl apply -f aks-ssh.yaml
```

取得公開服務的外部 IP 位址。 完成 IP 位址設定需要約一分鐘。 

```azurecli-interactive
$ kubectl get service

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
kubernetes         ClusterIP      10.0.0.1      <none>          443/TCP        1d
aks-ssh            LoadBalancer   10.0.51.173   13.92.154.191   22:31898/TCP   17m
```

建立 SSH 連線。 

AKS 叢集的預設使用者名稱是 `azureuser`。 如果此帳戶在叢集建立時有所變更，請取代為適當的管理員使用者名稱。 

如果您的金鑰不在 `~/ssh/id_rsa` 上，請使用 `ssh -i` 引數提供正確的位置。

```azurecli-interactive
$ ssh azureuser@13.92.154.191

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

完成後，請刪除 SSH 存取 Pod 和服務。

```azurecli-interactive
kubectl delete -f aks-ssh.yaml
```