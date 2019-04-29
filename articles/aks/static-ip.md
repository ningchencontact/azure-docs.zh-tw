---
title: 搭配 Azure Kubernetes Service (AKS) 負載平衡器使用靜態 IP 位址
description: 了解如何搭配 Azure Kubernetes Service (AKS) 負載平衡器來建立和使用靜態 IP 位址。
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 03/04/2019
ms.date: 04/08/2019
ms.author: v-yeche
ms.openlocfilehash: d2e4314948eeda0c82c004414f894dafc4d4cff6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031638"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>搭配 Azure Kubernetes Service (AKS) 負載平衡器使用靜態公用 IP 位址

根據預設，指派給 AKS 叢集所建立之負載平衡器資源的公用 IP 位址，只有在該資源的生命週期內有效。 如果您刪除 Kubernetes 服務，相關聯的負載平衡器和 IP 位址也會一併刪除。 如果您想要針對已重新部署的 Kubernetes 服務指派特定的 IP 位址或保留 IP 位址，您可以建立並使用靜態公用 IP 位址。

此文章示範如何建立靜態公用 IP 位址，並將它指派給您的 Kubernetes 服務。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

还需安装并配置 Azure CLI 2.0.59 或更高版本。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

目前仅支持基本 IP SKU。 支持标准 IP 资源 SKU 的开发正在进行中。 有关详细信息，请参阅 [Azure 中的 IP 地址类型和分配方法][ip-sku]。

## <a name="create-a-static-ip-address"></a>建立靜態 IP 位址

當您建立靜態公用 IP 位址來與 AKS 搭配使用時，應該在**節點**資源群組中建立 IP 位址資源。 若要分隔资源，请参阅以下部分，以便[在节点资源组外部使用静态 IP 地址](#use-a-static-ip-address-outside-of-the-node-resource-group)。

首先，请使用 [az aks show][az-aks-show] 命令并添加 `--query nodeResourceGroup` 查询参数获取节点资源组名称。 下列範例會在 *myResourceGroup* 資源群組名稱中，取得 AKS 叢集名稱 *myAKSCluster* 的節點資源群組：

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_chinaeast
```

現在使用 [az network public ip create][az-network-public-ip-create] 命令來建立靜態公用 IP 位址。 指定在上一個命令中所取得的節點資源群組名稱，然後指定 IP 位址資源的名稱，例如 *myAKSPublicIP*：

```azurecli
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_chinaeast \
    --name myAKSPublicIP \
    --allocation-method static
```

将显示 IP 地址，如以下精简版示例输出中所示：

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_chinaeast/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [...]
  }
}
```

您稍後可以使用 [az network public-ip list][az-network-public-ip-list] 命令來取得公用 IP 位址。 指定節點資源群組的名稱以及您建立的公用 IP 位址，然後查詢 ipAddress，如下列範例所示：

```azurecli
$ az network public-ip show --resource-group MC_myResourceGroup_myAKSCluster_chinaeast --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>使用靜態 IP 位址建立服務

若要使用靜態公用 IP 位址建立服務，請將 `loadBalancerIP` 屬性和和靜態公用 IP 位址的值新增到 YAML 資訊清單。 建立名為 `load-balancer-service.yaml` 的檔案，然後將下列 YAML 複製進來。 提供您在上一個步驟中所建立的自有公用 IP 位址。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

使用 `kubectl apply` 命令建立服務和部署。

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="use-a-static-ip-address-outside-of-the-node-resource-group"></a>使用節點資源群組以外的靜態 IP 位址

透過 Kubernetes 1.10 或更新版本，您可以使用在節點資源群組外部建立的靜態 IP 位址。 AKS 叢集所使用的服務主體必須具有其他資源群組的委派權限，如下列範例所示：

```azurecli
az role assignment create\
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

若要使用節點資源群組以外的 IP 位址，請在服務定義中加入註解。 下列範例會對名為 myResourceGroup 的資源群組設定註解。 提供您自己的資源群組名稱：

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

## <a name="troubleshoot"></a>疑難排解

如果 Kubernetes 服务清单的 *loadBalancerIP* 属性中定义的静态 IP 地址不存在或尚未在节点资源组中创建，并且尚未配置其他托管，则负载均衡器服务创建将失败。 若要進行疑難排解，請使用 [kubectl describe][kubectl-describe] 命令來檢閱服務建立事件。 提供 YAML 資訊清單中所指定的服務名稱，如下列範例所示：

```console
kubectl describe service azure-load-balancer
```

Kubernetes 服務資源的相關資訊即會顯示。 下列範例輸出結尾的 *Events* 指出「找不到使用者提供的 IP 位址」。 在這些案例中，請確認您已在節點資源群組中建立靜態公用 IP 位址，以及 Kubernetes 服務資訊清單中所指定的 IP 位址正確無誤。

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
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
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>後續步驟

若要進一步控制應用程式的網路流量，您可能想要改為[建立輸入控制器][aks-ingress-basic]。 您也可以[使用靜態公用 IP 位址建立輸入控制器][aks-static-ingress]。

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: https://docs.azure.cn/zh-cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-create
[az-network-public-ip-list]: https://docs.azure.cn/zh-cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-list
[az-aks-show]: https://docs.azure.cn/zh-cn/cli/aks?view=azure-cli-latest#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku