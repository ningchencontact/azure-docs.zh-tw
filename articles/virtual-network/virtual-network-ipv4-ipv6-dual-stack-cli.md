---
title: 部署 Azure 虛擬網路-CLI 中的 IPv6 雙重堆疊應用程式
titlesuffix: Azure Virtual Network
description: 這篇文章示範如何部署 IPv6 雙重堆疊應用程式使用 Azure CLI 的 Azure 虛擬網路中。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 12fbf2ae5387ac0a9350cc203f4a6f2587c8dafe
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130869"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli-preview"></a>部署 Azure 虛擬網路-CLI （預覽） 中的 IPv6 雙重堆疊應用程式

本文說明如何部署雙重堆疊 （IPv4 + IPv6） 中的應用程式包含具有雙重堆疊子網路，使用雙 （IPv4 + IPv6） 前端組態，具有有雙重 IP 組態的 Nic 的 Vm 負載平衡器的雙重堆疊虛擬網路的 Azure雙重的網路安全性群組規則和 「 雙重的公用 Ip。

> [!Important]
> Azure 虛擬網路的 IPv6 雙重堆疊目前處於公開預覽狀態。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果您沒有 Azure 訂用帳戶，請立即建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您決定安裝，而改為在本機使用 Azure CLI，本快速入門需要您使用 Azure CLI 2.0.49 版或更新版本。 若要尋找您安裝的版本，請執行 `az --version`。 如需安裝或升級的資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="prerequisites"></a>必要條件
若要使用 IPv6 的 Azure 虛擬網路功能，您必須設定您的訂用帳戶使用 Azure PowerShell，如下所示：

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
需要 30 分鐘才能完成功能註冊。 您可以執行下列 Azure CLI 命令來檢查您的註冊狀態：

```azurelci
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
註冊完成之後，請執行下列命令：

```azurelci
az provider register --namespace Microsoft.Network
```
## <a name="create-a-resource-group"></a>建立資源群組

您可以建立雙重堆疊虛擬網路之前，您必須建立的資源群組[az 群組建立](/cli/azure/group)。 下列範例會建立名為的資源群組*myRGDualStack*中*eastus*位置：

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>建立 IPv4 和 IPv6 公用 IP 位址的負載平衡器
若要存取您在網際網路上的 IPv4 和 IPv6 端點，您會需要 IPv4 和 IPv6 公用 IP 位址的負載平衡器。 使用 [az network public-ip create](/cli/azure/network/public-ip) 建立公用 IP 位址。 下列範例會建立 IPv4 和 IPv6 公用 IP 位址名為*dsPublicIP_v4*並*dsPublicIP_v6*中*myRGDualStack*資源群組：

```azurecli
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>為 Vm 建立公用 IP 位址

若要從遠端存取您在網際網路上的 Vm，您會需要 IPv4 公用 IP 位址的 vm。 使用 [az network public-ip create](/cli/azure/network/public-ip) 建立公用 IP 位址。

```azurecli
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>建立基本負載平衡器

在本節中，您會設定 （IPv4 和 IPv6） 的雙重的前端 IP 與負載平衡器的後端位址集區，然後再建立基本負載平衡器。

### <a name="create-load-balancer"></a>建立負載平衡器

建立基本 Load Balancer 與[建立 az 網路 lb](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest)名為**dsLB** ，其中包含名為前端集區**dsLbFrontEnd_v4**，名為後端集區**dsLbBackEndPool_v4** IPv4 公用 IP 位址與相關聯**dsPublicIP_v4**您在上一個步驟中建立。 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>建立 IPv6 前端

建立具有 IPV6 前端 IP [az 網路 lb 前端 ip 建立](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create)。 下列範例會建立名為的前端 IP 組態*dsLbFrontEnd_v6* ，並將附加*dsPublicIP_v6*位址：

```azurepowershell-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>設定 IPv6 後端位址集區

建立 IPv6 後端位址集區[az network lb ip-config address-pool 建立](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create)。 下列範例會建立名為的後端位址集區*dsLbBackEndPool_v6*來包含具有 IPv6 NIC 組態的 Vm:

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則

負載平衡器規則用來定義如何將流量分散至 VM。 您可定義連入流量的前端 IP 組態及後端 IP 集區來接收流量，以及所需的來源和目的地連接埠。 

使用 [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) 建立負載平衡器規則。 下列範例會建立名為負載平衡器規則*dsLBrule_v4*並*dsLBrule_v6*並在平衡流量*TCP*連接埠*80*至IPv4 和 IPv6 前端 IP 組態：

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \ 
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>建立網路資源
部署一些 Vm 之前，您必須建立支援網路資源的可用性設定組、 網路安全性群組、 虛擬網路與虛擬 Nic。 
### <a name="create-an-availability-set"></a>建立可用性設定組
若要改善您的應用程式的可用性，請將 Vm 放在可用性設定組中。

使用 [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest) 建立可用性設定組。 下列範例會建立可用性設定組*dsAVset*:

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>建立網路安全性群組

建立網路安全性群組的規則，將會管理您的 VNET 中的輸入和輸出通訊。

#### <a name="create-a-network-security-group"></a>建立網路安全性群組

建立網路安全性群組與[az 網路 nsg 建立](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>建立網路安全性群組規則的輸入和輸出連線

建立網路安全性群組規則以允許透過連接埠 3389，網際網路連線的 RDP 連線，透過連接埠 80，以及使用輸出連線[az 網路的 nsg 規則建立](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)。

```azurecli
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>建立虛擬網路

使用 [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create) 建立虛擬網路。 下列範例會建立名為虛擬網路*dsVNET*具有子網路*dsSubNET_v4*並*dsSubNET_v6*:

```azurecli
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>建立 NIC

用於每個 VM 建立虛擬 Nic [az 網路 nic 建立](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)。 下列範例會針對每個 VM 建立虛擬 NIC。 每個 NIC 會有兩個 IP 組態 （1 個 IPv4 組態，1 IPv6 組態）。 建立具有 IPV6 組態[az 網路 nic ip 組態建立](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create)。
 
```azurecli
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>建立虛擬機器

使用 [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) 建立 VM。 下列範例會建立兩個 VM 及必要的虛擬網路元件 (如果尚未存在)。 

建立虛擬機器*dsVM0* ，如下所示：

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest  
```
建立虛擬機器*dsVM1* ，如下所示：

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>在 Azure 入口網站中檢視虛擬網路的 IPv6 雙重堆疊
您可以在 Azure 入口網站中檢視 IPv6 雙重堆疊的虛擬網路，如下所示：
1. 在入口網站的搜尋列中，輸入*dsVnet*。
2. 當搜尋結果中出現 **myVirtualNetwork** 時加以選取。 這會啟動**概觀**名為雙重堆疊虛擬網路頁面*dsVnet*。 雙重堆疊的虛擬網路位於名為雙重堆疊子網路的 IPv4 和 IPv6 組態會顯示兩個 Nic *dsSubnet*。

  ![在 Azure 中 IPv6 雙重堆疊虛擬網路](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Azure 虛擬網路的 IPv6 是在 Azure 入口網站中，您可以使用此預覽版本為唯讀。


## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令來移除資源群組、VM 和所有相關資源。

```azurecli
 az group delete --name DsRG1
```

## <a name="next-steps"></a>後續步驟

在本文中中,，您可以建立基本的 Load Balancer 的雙重的前端 IP 設定 （IPv4 和 IPv6）。 您也會建立包含 Nic 具有雙重 IP 組態 （IPV4 + IPv6） 新增至負載平衡器的後端集區的兩部虛擬機器。 若要深入了解 Azure 虛擬網路中的 IPv6 支援，請參閱[什麼是 Azure 虛擬網路的 IPv6？](ipv6-overview.md)