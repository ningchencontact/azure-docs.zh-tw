---
title: 建立 Azure Kubernetes Service (AKS) 內部負載平衡器
description: 搭配 Azure Kubernetes Service (AKS) 使用內部負載平衡器。
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 3/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a1a5d6d455086ee34767e92b277936840717bcd6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>搭配 Azure Kubernetes Service (AKS) 使用內部負載平衡器

內部負載平衡讓 Kubernetes 服務可供在與 Kubernetes 叢集相同之虛擬網路中執行的應用程式存取。 本文詳述如何使用 Azure Kubernetes Service (AKS) 建立內部負載平衡器。

## <a name="create-internal-load-balancer"></a>建立內部負載平衡器

若要建立內部負載平衡器，使用服務類型 `LoadBalancer` 和 `azure-load-balancer-internal` 註釋來建置服務資訊清單，如下列範例所示。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

一旦部署之後， Azure 負載平衡器即會建立，並且可在與 AKS 叢集相同的虛擬網路上使用。

![AKS 內部負載平衡器的影像](media/internal-lb/internal-lb.png)

擷取服務詳細資料時，`EXTERNAL-IP` 欄中的 IP 位址是內部負載平衡器的 IP 位址。

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>指定 IP 位址

如果您想要搭配內部負載平衡器使用特定 IP 位址，請將 `loadBalancerIP` 屬性新增至負載平衡器規格中。指定的 IP 位址必須位於與 AKS 叢集相同的子網路，而且必須尚未指派給資源。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

擷取服務詳細資料時，`EXTERNAL-IP` 上的 IP 位址應該反映指定的 IP 位址。

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="delete-the-load-balancer"></a>刪除負載平衡器

將使用內部負載平衡器的所有服務都被刪除之後，負載平衡器本身也會被刪除。

## <a name="next-steps"></a>後續步驟

在 [Kubernetes 服務文件][kubernetes-services]中深入了解 Kubernetes 服務。

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/