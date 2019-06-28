---
title: 授權 Azure Kubernetes Service (AKS) 中的 IP 範圍的 API 伺服器
description: 了解如何保護您的叢集 IP 位址範圍用於 Azure Kubernetes Service (AKS) 中的 API 伺服器的存取權
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: 9ec48c8ed924293a5ffea903fe03a9830dcd1184
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329422"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>預覽-對 API 伺服器中使用的安全存取授權的 Azure Kubernetes Service (AKS) 中的 IP 位址範圍

在 Kubernetes 中，API 伺服器會接收要求，例如叢集中執行動作，來建立資源，或調整規模的節點數目。 API 伺服器是集中的方式進行互動，並且管理叢集。 若要改善叢集安全性並減少攻擊，應該只能存取一組有限的 IP 位址範圍 API 伺服器。

本文說明如何使用 API 伺服器授權的 IP 位址範圍限制至控制平面的要求。 此功能目前為預覽狀態。

> [!IMPORTANT]
> AKS 預覽功能包括自助、 選擇加入。 它們可供收集從我們的社群的意見及 bug。 在預覽中，這些功能不適用於實際執行環境。 在公開預覽功能底下 '盡力' 支援。 AKS 技術支援小組的協助時可使用營業時間太平洋 」 (PST) 僅限 timezone。 如需詳細資訊，請參閱下列支援文章：
>
> * [AKS 支援原則][aks-support-policies]
> * [Azure 支援常見問題集][aks-faq]

## <a name="before-you-begin"></a>開始之前

API 伺服器授權的 IP 範圍只適用於新建立的 AKS 叢集。 這篇文章會示範如何建立 AKS 叢集使用 Azure CLI。

您需要 Azure CLI 2.0.61 版或更新版本安裝並設定。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 擴充功能

若要設定 API 授權伺服器的 IP 範圍，您需要*aks 預覽*CLI 擴充功能版本 0.4.1 或更高版本。 安裝*aks 預覽*Azure CLI 擴充功能使用[az 延伸模組加入][az-extension-add]command, then check for any available updates using the [az extension update][az-extension-update]命令：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>註冊您的訂用帳戶的功能旗標

若要使用 API 伺服器授權 IP 範圍，請先啟用您的訂用帳戶的功能旗標。 若要註冊*APIServerSecurityPreview*功能旗標，請使用[az 功能註冊][az-feature-register]命令，在下列範例所示：

> [!CAUTION]
> 當您註冊訂用帳戶上的功能時，您目前無法取消註冊該功能。 啟用某些預覽功能之後，可能會使用預設值，然後在 訂用帳戶中建立的所有 AKS 叢集。 請勿啟用生產訂用帳戶上的預覽功能。 若要測試預覽功能，並收集意見反應中使用不同的訂用帳戶。

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

狀態需要幾分鐘的時間才會顯示「已註冊」  。 您可以檢查註冊狀態 using [az 功能清單][az-feature-list]命令：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

準備好時，重新整理的註冊*Microsoft.ContainerService*使用的資源提供者[az provider register][az-provider-register]命令：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>限制

當您設定 API 授權伺服器的 IP 範圍時，就會套用下列限制：

* 為使用 API 伺服器的通訊也已封鎖，您目前無法使用 Azure 開發人員的空格。

## <a name="overview-of-api-server-authorized-ip-ranges"></a>授權 IP 範圍的 API 伺服器的概觀

Kubernetes API 伺服器為基礎的 Kubernetes Api 公開的方式。 此元件可提供管理工具的互動，例如 `kubectl` 或 Kubernetes 儀表板。 AKS 提供單一租用戶叢集主機，使用專用的 API 伺服器。 根據預設，API 伺服器指派公用 IP 位址，而且您應該控制使用角色型存取控制 (RBAC) 的存取。

否則可公開存取的 AKS 控制平面的安全存取 / API 伺服器，您可以啟用和使用授權的 IP 範圍。 這些授權的 IP 範圍只允許已定義的 IP 位址範圍，以使用 API 伺服器進行通訊。 對 API 伺服器不屬於這些授權的 IP 範圍的 IP 位址的要求會遭到封鎖。 您應該繼續使用 RBAC，然後授權給使用者和它們所要求的動作。

如需 API 伺服器和其他叢集元件的詳細資訊，請參閱[Kubernetes AKS 的核心概念][concepts-clusters-workloads]。

## <a name="create-an-aks-cluster"></a>建立 AKS 叢集

API 授權伺服器的 IP 範圍僅適用於新的 AKS 叢集。 隸屬於叢集建立作業，您無法啟用已授權的 IP 範圍。 如果您嘗試啟用已授權的 IP 範圍，為叢集的一部分建立處理程序，叢集節點都無法存取在部署期間的 API 伺服器的輸出 IP 位址不在該點定義。

首先，使用下列方法建立叢集[az aks 建立][az-aks-create]命令。 下列範例會建立名為單一節點叢集*myAKSCluster*資源群組中名為*myResourceGroup*。

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

若要確保叢集中的節點可以可靠地通訊與 API 伺服器時啟用授權的 IP 範圍，在下一節中，建立 Azure 防火牆作為輸出的閘道。 Azure 防火牆的 IP 位址再加入下一節中授權的 API 伺服器 IP 位址的清單。

> [!WARNING]
> 使用 Azure 的防火牆會造成顯著的成本，每月計費週期。 使用 Azure 防火牆需求應該只需要在這個初始預覽期間內。 如需詳細資訊和規劃成本，請參閱[Azure 防火牆定價][azure-firewall-costs]。

首先，取得*MC_* AKS 叢集與虛擬網路的資源群組名稱。 接著，建立 子網路，使用[az vnet 子網路建立][az-network-vnet-subnet-create]命令。 下列範例會建立名為的子網路*AzureFirewallSubnet*使用的 CIDR 範圍*10.200.0.0/16*:

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

若要建立 Azure 防火牆，安裝*azure 防火牆*CLI 擴充功能使用[az 延伸模組加入][az-extension-add]command. Then, create a firewall using the [az network firewall create][az-network-firewall-create]命令。 下列範例會建立名為 Azure 防火牆*myAzureFirewall*:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Azure 的防火牆會指派輸出流量流經的公用 IP 位址。 公用位址，使用下列方法建立[az 網路公用 ip 建立][az-network-public-ip-create] command, then create an IP configuration on the firewall using the [az network firewall ip-config create][az-network-firewall-ip-config-create] ，適用於公用 IP:

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

現在，建立 Azure 防火牆網路規則，以*允許*所有*TCP*流量使用[az 網路防火牆網路規則建立][az-network-firewall-network-rule-create]命令。 下列範例會建立名為的網路規則*AllowTCPOutbound*任何來源或目的地位址的流量：

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

若要將 Azure 的防火牆與網路路由產生關聯，取得現有的路由資料表資訊、 內部 IP 位址的 Azure 防火牆，然後 API 伺服器的 IP 位址。 若要控制如何流量應該會路由傳送的叢集通訊下一步 區段中指定這些 IP 位址。

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

最後，建立在現有 AKS 網路路由表中使用的路由[az 網路路由表路由建立][az-network-route-table-route-create]命令，以允許使用 API 伺服器通訊的 Azure 防火牆設備的流量。

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

記下您的 Azure 防火牆應用裝置的公用 IP 位址。 此位址會加入下一節中的 API 授權伺服器的 IP 範圍的清單。

## <a name="enable-authorized-ip-ranges"></a>啟用授權的 IP 範圍

若要啟用 API 授權伺服器的 IP 範圍，您可以提供一份已獲授權的 IP 位址範圍。 當您指定的 CIDR 範圍時，開始在範圍內的第一個 IP 位址。 例如， *137.117.106.90/29*是有效的範圍，但請確定您指定第一個 IP 位址在範圍內，例如*137.117.106.88/29*。

使用[更新 az aks][az-aks-update]命令，並指定 *-api-server-授權-ip 範圍*允許。 這些 IP 位址範圍通常是使用您的內部部署網路的位址範圍。 新增公用 IP 位址的取得，是在上一個步驟中，例如您的 Azure 防火牆*20.42.25.196/32*。

下列範例會啟用名為在叢集上的 API 伺服器授權 IP 範圍*myAKSCluster*資源群組中名為*myResourceGroup*。 若要授權的 IP 位址範圍是*20.42.25.196/32* （Azure 防火牆公用 IP 位址），然後*172.0.0.10/16*並*168.10.0.10/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.10/16,168.10.0.10/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>更新或停用已獲授權的 IP 範圍

若要更新或停用已獲授權的 IP 範圍，您一次使用[az aks 更新][az-aks-update]命令。 指定更新的 CIDR 範圍，您想要允許或指定要停用 API 伺服器的空白範圍授權 IP 範圍，如下列範例所示：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>後續步驟

在本文中，您可以啟用 API 授權伺服器的 IP 範圍。 這種方法是執行安全的 AKS 叢集之方式的其中一部分。

如需詳細資訊，請參閱 <<c0> [ 的應用程式和 AKS 叢集的安全性概念][concepts-security] and [Best practices for cluster security and upgrades in AKS][operator-best-practices-cluster-security]。

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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-list]: /cli/azure/extension#az-extension-list
[az-extension-update]: /cli/azure/extension#az-extension-update
