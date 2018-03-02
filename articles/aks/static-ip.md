---
title: "搭配 Azure Container Service (AKS) 負載平衡器使用靜態 IP 位址"
description: "搭配 Azure Container Service (AKS) 負載平衡器使用靜態 IP 位址。"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 945132dd5f7e51f05ceda89a9cb16315aabbda8a
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2018
---
# <a name="use-a-static-ip-address-with-the-azure-container-service-aks-load-balancer"></a>搭配 Azure Container Service (AKS) 負載平衡器使用靜態 IP 位址

在某些情況下，例如重新建立 Azure Container Service (AKS) 負載平衡器或重新建立類型為 LoadBalancer 的 Kubernetes 服務時，Kubernetes 服務的公用 IP 位址可能會變更。 本文件將詳細說明如何為您的 Kubernetes 服務設定靜態 IP 位址。

## <a name="create-static-ip-address"></a>建立靜態 IP 位址

為 Kubernetes 服務建立靜態公用 IP 位址。 您必須在於叢集部署期間自動建立的資源群組中建立 IP 位址。 如需有關不同 AKS 資源群組及如何識別自動建立之資源群組的資訊，請參閱 [AKS 常見問題集][aks-faq-resource-group]。

使用 [az network public ip create][az-network-public-ip-create] 命令來建立 IP 位址。

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGRoup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
```

記下 IP 位址。

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    "ipConfiguration": null,
    "ipTags": [],
    "location": "eastus",
    "name": "myAKSPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Static",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "56ec8760-a3b8-4aeb-a89d-42e68d2cbc8c",
    "sku": {
      "name": "Basic"
    },
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses",
    "zones": null
  }
````

 如有需要，可以使用 [az network public-ip list][az-network-public-ip-list] 命令來擷取此位址。

```console
$ az network public-ip list --resource-group MC_myResourceGRoup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-service-with-ip-address"></a>使用 IP 位址來建立服務

佈建靜態 IP 位址之後，即可使用 `loadBalancerIP` 屬性和靜態 IP 位址值來建立 Kubernetes 服務。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="troubleshooting"></a>疑難排解

如果靜態 IP 位址已建立或建立在錯誤的資源群組中，服務建立作業就會失敗。 若要進行疑難排解，請使用 [kubectl describe][kubectl-describe] 命令來傳回服務建立事件。

```console
$ kubectl describe service azure-vote-front

Name:                     azure-vote-front
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-vote-front
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-vote-front: user supplied IP Address 40.121.183.52 was not found
```

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/ 

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list