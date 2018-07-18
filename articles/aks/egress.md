---
title: 將來自 Azure Kubernetes Service (AKS) 叢集的輸出流量列入白名單
description: 將來自 Azure Kubernetes Service (AKS) 叢集的輸出流量列入白名單
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/23/2018
ms.author: iainfou
ms.openlocfilehash: 2394b61fb84a2f22af036f35819b87074a1dbd2d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100140"
---
# <a name="azure-kubernetes-service-aks-egress"></a>Azure Kubernetes Service (AKS) 輸出

根據預設，來自 Azure Kubernetes Service (AKS) 的輸出位址會隨機指派。 當需要識別 IP 位址以存取外部服務時，這個組態並不理想。 這份文件詳細說明如何建立及維護 AKS 叢集內，以靜態方式指派的輸出 IP 位址。

## <a name="egress-overview"></a>輸出概觀

來自 AKS 叢集的輸出流量會遵循 Azure Load Balancer 慣例，記載在[這裡][outbound-connections]。 在建立 `LoadBalancer` 類型的第一個 Kubernetes 服務之前，代理程式節點不是任何 Azure Load Balancer 集區的一部分。 在此組態中，節點沒有執行個體層級的公用 IP 位址。 Azure 會將輸出流量轉譯為公用來源 IP 位址，這不是可設定或確定性的位址。

一旦建立 `LoadBalancer` 類型的 Kubernetes 服務，代理程式節點就會新增至 Azure Load Balancer 集區。 Azure 會將輸出流量轉譯為負載平衡器上設定的第一個公用 IP 位址。

## <a name="create-a-static-public-ip"></a>建立靜態公用 IP

若要防止使用隨機的 IP 位址，請建立靜態 IP 位址，並確保負載平衡器會使用這個位址。 IP 位址必須在 AKS **節點**資源群組中建立。

使用 [az resource show][az-resource-show] 命令取得資源群組名稱。 請更新資源群組名稱和叢集名稱，以符合您的環境。

```
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

接下來，使用 [az network public-ip create][public-ip-create] 命令來建立靜態公用 IP 位址。 請更新資源群組名稱，以符合在上一個步驟中取得的名稱。

```console
$ az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static --query publicIp.ipAddress -o table

Result
-------------
23.101.128.81
```

## <a name="create-a-service-with-the-static-ip"></a>建立具有靜態 IP 的服務

現在您已經有一個 IP 位址，請建立具有 `LoadBalancer` 類型的 Kubernetes 服務，並將 IP 位址指派給服務。

建立名為 `egress-service.yaml` 的檔案，然後將下列 YAML 複製進來。 請更新 IP 位址以符合您的環境。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-egress
spec:
  loadBalancerIP: 23.101.128.81
  type: LoadBalancer
  ports:
  - port: 8080
```

使用 `kubectl apply` 命令建立服務和部署。

```console
$ kubectl apply -f egress-service.yaml

service "aks-egress" created
```

建立此服務會在 Azure Load Balancer 上設定新的前端 IP。 如果您未設定任何其他 IP，則**所有**輸出流量現在都應該會使用此位址。 在 Azure Load Balancer 上設定多個位址時，輸出會使用該負載平衡器上的第一個 IP。

## <a name="verify-egress-address"></a>確認輸出位址

若要確認所使用的公用 IP 位址，請使用例如 `checkip.dyndns.org` 的服務。

啟動並連結至 Pod：

```console
$ kubectl run -it --rm aks-ip --image=debian
```

如有需要，請在容器中安裝 cURL：

```console
$ apt-get update && apt-get install curl -y
```

cURL `checkip.dyndns.org`，會傳回輸出 IP 位址：

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 23.101.128.81</body></html>
```

您應該會看到 IP 位址與連結到 Azure 負載平衡器的靜態 IP 位址相符。

## <a name="ingress-controller"></a>輸入控制器

若要避免維護 Azure Load Balancer 上的多個公用 IP 位址，請考慮使用輸入控制器。 輸入控制器提供例如負載平衡、SSL/TLS 終止、支援 URI 重寫及上游 SSL/TLS 加密等優點。 如需 AKS 中輸入控制器的詳細資訊，請參閱[在 AKS 叢集中設定 NGINX 輸入控制器][ingress-aks-cluster]指南。

## <a name="next-steps"></a>後續步驟

深入了解本文所示範的軟體。

- [Helm CLI][helm-cli-install]
- [NGINX 輸入控制器][nginx-ingress]
- [Azure Load Balancer 輸出連線][outbound-connections]

<!-- LINKS - internal -->
[az-resource-show]: /cli/azure/resource#az-resource-show
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[create-aks-cluster]: ./kubernetes-walkthrough.md
[helm-cli-install]: ./kubernetes-helm.md#install-helm-cli
[ingress-aks-cluster]: ./ingress.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create

<!-- LINKS - external -->
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
