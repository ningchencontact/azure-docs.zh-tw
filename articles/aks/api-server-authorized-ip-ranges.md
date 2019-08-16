---
title: Azure Kubernetes Service (AKS) 中的 API 伺服器授權 IP 範圍
description: 瞭解如何使用 IP 位址範圍來保護您的叢集, 以存取 Azure Kubernetes Service 中的 API 伺服器 (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: fe0c9d7e870b56bf83b70845af9159ea0703c4ab
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533621"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>預覽-在 Azure Kubernetes Service (AKS) 中使用授權的 IP 位址範圍來保護 API 伺服器的存取

在 Kubernetes 中, API 伺服器會接收在叢集中執行動作的要求, 例如建立資源或調整節點數目。 API 伺服器是與叢集互動和管理叢集的主要方式。 為了改善叢集安全性並將攻擊降至最低, 您只能從一組有限的 IP 位址範圍存取 API 伺服器。

本文說明如何使用 API 伺服器授權的 IP 位址範圍來限制控制平面的要求。 此功能目前為預覽狀態。

> [!IMPORTANT]
> AKS 預覽功能是自助加入宣告。 預覽會以「原樣」和「可用」的方式提供, 並從服務等級協定中排除, 並享有有限擔保。 AKS 預覽的部分是由客戶支援, 以最大的方式來涵蓋。 因此, 這些功能並不適用于生產環境使用。 如需其他資訊, 請參閱下列支援文章:
>
> * [AKS 支援原則][aks-support-policies]
> * [Azure 支援常見問題集][aks-faq]

## <a name="before-you-begin"></a>開始之前

API 伺服器授權的 IP 範圍僅適用于您所建立的新 AKS 叢集。 本文說明如何使用 Azure CLI 建立 AKS 叢集。

您需要安裝並設定 Azure CLI 版本2.0.61 或更新版本。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級, 請參閱 [安裝 Azure CLI][install-azure-cli]。

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 擴充功能

若要設定 API 伺服器授權的 IP 範圍, 您需要*aks-preview* CLI 擴充功能版本0.4.1 或更高版本。 使用[az extension add][az-extension-add]命令來安裝*aks-preview* Azure CLI 擴充功能, 然後使用[az extension update][az-extension-update]命令檢查是否有任何可用的更新:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>註冊訂用帳戶的功能旗標

若要使用 API 伺服器授權的 IP 範圍, 請先在您的訂用帳戶上啟用功能旗標。 若要註冊*APIServerSecurityPreview*功能旗標, 請使用[az feature register][az-feature-register]命令, 如下列範例所示:

> [!CAUTION]
> 當您在訂用帳戶上註冊功能時, 目前無法取消註冊該功能。 啟用一些預覽功能之後, 預設值可能會用於在訂用帳戶中建立的所有 AKS 叢集。 請勿在生產訂用帳戶上啟用預覽功能。 使用個別的訂用帳戶來測試預覽功能並收集意見反應。

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

狀態需要幾分鐘的時間才會顯示「已註冊」。 您可以使用[az feature list][az-feature-list]命令來檢查註冊狀態:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

準備好時, 請使用[az provider register][az-provider-register]命令重新整理*microsoft.containerservice*資源提供者的註冊:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>限制

當您設定 API 伺服器授權的 IP 範圍時, 適用下列限制:

* 您目前無法使用 Azure Dev Spaces, 因為與 API 伺服器的通訊也會遭到封鎖。

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API 伺服器授權的 IP 範圍總覽

Kubernetes API 伺服器是基礎 Kubernetes Api 的公開方式。 此元件可提供管理工具的互動，例如 `kubectl` 或 Kubernetes 儀表板。 AKS 提供具有專用 API 伺服器的單一租使用者叢集主機。 根據預設, 會將公用 IP 位址指派給 API 伺服器, 而且您應該使用角色型存取控制 (RBAC) 來控制存取權。

若要安全存取其他可公開存取的 AKS 控制平面/API 伺服器, 您可以啟用並使用已授權的 IP 範圍。 這些授權的 IP 範圍只允許已定義的 IP 位址範圍與 API 伺服器通訊。 從不屬於這些授權 IP 範圍的 IP 位址對 API 伺服器提出的要求會遭到封鎖。 您應該繼續使用 RBAC 來授權使用者和他們所要求的動作。

如需有關 API 伺服器和其他叢集元件的詳細資訊, 請參閱[AKS 的 Kubernetes 核心概念][concepts-clusters-workloads]。

## <a name="create-an-aks-cluster"></a>建立 AKS 叢集

API 伺服器授權的 IP 範圍僅適用于新的 AKS 叢集。 您無法在叢集建立作業中啟用授權的 IP 範圍。 如果您嘗試在叢集建立程式中啟用授權的 IP 範圍, 叢集節點將無法在部署期間存取 API 伺服器, 因為輸出 IP 位址未在該時間點定義。

首先, 使用[az aks create][az-aks-create]命令來建立叢集。 下列範例會在名為*myResourceGroup*的資源群組中建立名為*myAKSCluster*的單一節點叢集。

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-outbound-gateway-for-firewall-rules"></a>建立防火牆規則的輸出閘道

若要確保當您在下一節中啟用授權的 IP 範圍時, 叢集中的節點可以可靠地與 API 伺服器通訊, 請建立 Azure 防火牆作為輸出閘道使用。 接著, 在下一節中, Azure 防火牆的 IP 位址會新增至已授權的 API 伺服器 IP 位址清單。

> [!WARNING]
> 使用 Azure 防火牆可能會在每月計費週期產生顯著的成本。 只有在此初始預覽期間才需要使用 Azure 防火牆的需求。 如需詳細資訊和成本規劃, 請參閱[Azure 防火牆定價][azure-firewall-costs]。
>
> 或者, 如果您的叢集使用[標準 SKU 負載平衡器][standard-sku-lb], 您就不需要將 Azure 防火牆設定為輸出閘道。 使用[az network public ip list][az-network-public-ip-list]並指定 AKS 叢集的資源群組, 這通常會以*MC_* 開頭。 這會顯示您叢集的公用 IP, 您可以將其列入允許清單。 例如:
>
> ```azurecli-interactive
> RG=$(az aks show --resource-group myResourceGroup --name myAKSClusterSLB --query nodeResourceGroup -o tsv)
> SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
> az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSClusterSLB
> ```

首先, 取得 AKS 叢集和虛擬網路的*MC_* 資源組名。 然後, 使用[az network vnet subnet create][az-network-vnet-subnet-create]命令來建立子網。 下列範例會建立名為*AzureFirewallSubnet*且 CIDR 範圍為*10.200.0.0/16*的子網:

```azurecli-interactive
# Get the name of the MC_ cluster resource group
MC_RESOURCE_GROUP=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query nodeResourceGroup -o tsv)

# Get the name of the virtual network used by the cluster
VNET_NAME=$(az network vnet list \
    --resource-group $MC_RESOURCE_GROUP \
    --query [0].name -o tsv)

# Create a subnet in the virtual network for Azure Firewall
az network vnet subnet create \
    --resource-group $MC_RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name AzureFirewallSubnet \
    --address-prefixes 10.200.0.0/16
```

若要建立 Azure 防火牆, 請使用[az extension add][az-extension-add]命令來安裝*azure 防火牆*CLI 擴充功能。 然後, 使用[az network firewall create][az-network-firewall-create]命令來建立防火牆。 下列範例會建立名為*myAzureFirewall*的 Azure 防火牆:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

系統會為 Azure 防火牆指派輸出流量流經的公用 IP 位址。 使用[az network public-ip create][az-network-public-ip-create]命令建立公用位址, 然後使用適用于公用 ip 的[az network firewall ip-config create][az-network-firewall-ip-config-create]在防火牆上建立 ip 設定:

```azurecli-interactive
# Create a public IP address for the firewall
FIREWALL_PUBLIC_IP=$(az network public-ip create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallPublicIP \
    --sku Standard \
    --query publicIp.ipAddress -o tsv)

# Associated the firewall with virtual network
az network firewall ip-config create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallIPConfig \
    --vnet-name $VNET_NAME \
    --firewall-name myAzureFirewall \
    --public-ip-address myAzureFirewallPublicIP
```

現在, 使用[az network firewall network-rule create][az-network-firewall-network-rule-create]命令來建立 Azure 防火牆網路規則, 以*允許*所有*TCP*流量。 下列範例會針對具有任何來源或目的地位址的流量, 建立名為*AllowTCPOutbound*的網路規則:

```azurecli-interactive
az network firewall network-rule create \
    --resource-group $MC_RESOURCE_GROUP \
    --firewall-name myAzureFirewall \
    --name AllowTCPOutbound \
    --collection-name myAzureFirewallCollection \
    --priority 200 \
    --action Allow \
    --protocols TCP \
    --source-addresses '*' \
    --destination-addresses '*' \
    --destination-ports '*'
```

若要將 Azure 防火牆與網路路由建立關聯, 請取得現有的路由表資訊、Azure 防火牆的內部 IP 位址, 以及 API 伺服器的 IP 位址。 下一節會指定這些 IP 位址, 以控制流量應如何路由進行叢集通訊。

```azurecli-interactive
# Get the AKS cluster route table
ROUTE_TABLE=$(az network route-table list \
    --resource-group $MC_RESOURCE_GROUP \
    --query "[?contains(name,'agentpool')].name" -o tsv)

# Get internal IP address of the firewall
FIREWALL_INTERNAL_IP=$(az network firewall show \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewall \
    --query ipConfigurations[0].privateIpAddress -o tsv)

# Get the IP address of API server endpoint
K8S_ENDPOINT_IP=$(kubectl get endpoints -o=jsonpath='{.items[?(@.metadata.name == "kubernetes")].subsets[].addresses[].ip}')
```

最後, 使用[az network route-table create][az-network-route-table-route-create]命令, 在現有的 AKS 網路路由表中建立路由, 讓流量可以使用 Azure 防火牆設備進行 API 伺服器通訊。

```azurecli-interactive
az network route-table route create \
    --resource-group $MC_RESOURCE_GROUP \
    --route-table-name $ROUTE_TABLE \
    --name AzureFirewallAPIServer \
    --address-prefix $K8S_ENDPOINT_IP/32 \
    --next-hop-ip-address $FIREWALL_INTERNAL_IP \
    --next-hop-type VirtualAppliance

echo "Public IP address for the Azure Firewall instance that should be added to the list of API server authorized addresses is:" $FIREWALL_PUBLIC_IP
```

記下 Azure 防火牆設備的公用 IP 位址。 在下一節中, 此位址會新增至 API 伺服器授權的 IP 範圍清單。

## <a name="enable-authorized-ip-ranges"></a>啟用授權的 IP 範圍

若要啟用 API 伺服器授權的 IP 範圍, 請提供授權的 IP 位址範圍清單。 當您指定 CIDR 範圍時, 請從範圍中的第一個 IP 位址開始。 例如, *137.117.106.90/29*是有效的範圍, 但請務必指定範圍中的第一個 IP 位址, 例如*137.117.106.88/29*。

使用[az aks update][az-aks-update]命令, 並指定要允許的 *--api 伺服器授權 ip 範圍*。 這些 IP 位址範圍通常是內部部署網路所使用的位址範圍。 新增您自己在上一個步驟中取得的 Azure 防火牆公用 IP 位址, 例如*20.42.25.196/32*。

下列範例會在名為*myResourceGroup*的資源群組中, 于名為*myAKSCluster*的叢集上啟用 API 伺服器授權的 IP 範圍。 要授權的 IP 位址範圍是*20.42.25.196/32* (Azure 防火牆公用 IP 位址), 然後是*172.0.0.0/16*和*168.10.0.0/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.0/16,168.10.0.0/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>更新或停用授權的 IP 範圍

若要更新或停用已授權的 IP 範圍, 請再次使用[az aks update][az-aks-update]命令。 指定您想要允許的已更新 CIDR 範圍, 或指定空的範圍來停用 API 伺服器授權的 IP 範圍, 如下列範例所示:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>後續步驟

在本文中, 您已啟用 API 伺服器授權的 IP 範圍。 此方法是您可以如何執行安全 AKS 叢集的其中一個部分。

如需詳細資訊, 請參閱[AKS 中應用程式和叢集的安全性概念][concepts-security]和[AKS 中叢集安全性和升級的最佳做法][operator-best-practices-cluster-security]。

<!-- LINKS - external -->
[azure-firewall-costs]: https://azure.microsoft.com/pricing/details/azure-firewall/

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[create-aks-sp]: kubernetes-service-principal.md#manually-create-a-service-principal
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[standard-sku-lb]: load-balancer-standard.md
