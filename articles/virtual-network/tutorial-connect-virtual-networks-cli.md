---
title: 使用虛擬網路對等互連連線虛擬網路 - Azure CLI | Microsoft Docs
description: 深入了解如何使用虛擬網路對等互連連線虛擬網路。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: df56f2e3e13f80e7ce2c2b6c9cffeac3d03776e5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>使用 Azure CLI 以虛擬網路對等互連連線虛擬網路

您可以使用虛擬網路對等互連，讓虛擬網路彼此連線。 一旦虛擬網路對等互連，兩個虛擬網路中的資源就可以彼此通訊，且通訊時會有相同的延遲和頻寬，彷彿這些資源是位於相同的虛擬網路中。 本文涵蓋建立和對等互連兩個虛擬網路。 您會了解如何：

> [!div class="checklist"]
> * 建立兩個虛擬網路
> * 建立虛擬網路之間的對等互連
> * 測試對等互連

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本快速入門會要求您執行 Azure CLI 2.0.4 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

## <a name="create-virtual-networks"></a>建立虛擬網路

建立虛擬網路之前，您必須為虛擬網路以及在本文中建立的所有其他資源，建立資源群組。 使用 [az group create](/cli/azure/group#az_group_create) 來建立資源群組。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 建立虛擬網路。 下列範例會建立名為 myVirtualNetwork1 的虛擬網路，其位址首碼為 10.0.0.0/16。

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

建立名為 myVirtualNetwork2 的虛擬網路，其位址首碼為 10.1.0.0/16。 此位址前置詞不會與 myVirtualNetwork1 虛擬網路的位址首碼重疊。 您無法對等互連具有重疊位址首碼的虛擬網路。

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>對等互連虛擬網路

對等互連是建立於虛擬網路識別碼之間，所以您必須先使用 [az network vnet show](/cli/azure/network/vnet#az_network_vnet_show) 取得每個虛擬網路的識別碼，並且將識別碼儲存在變數中。

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

使用 [az network vnet peering create](/cli/azure/network/vnet/peering#az_network_vnet_peering_create) 建立從 myVirtualNetwork1 到 myVirtualNetwork2 的對等互連。 如果未指定 `--allow-vnet-access` 參數，雖然會建立對等互連，但是沒有通訊可以通過它。

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet-id $vNet2Id \
  --allow-vnet-access
```

在執行上一個命令之後所傳回的輸出中，您會看到 **peeringState** 是「已啟動」。 對等互連會持續處於「已啟動」狀態，直到您建立從 myVirtualNetwork2 到 myVirtualNetwork1 的對等互連為止。 建立從 myVirtualNetwork2 到 myVirtualNetwork1 的對等互連。 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet-id $vNet1Id \
  --allow-vnet-access
```

在執行上一個命令之後所傳回的輸出中，您會看到 **peeringState** 是「已連線」。 Azure 也會將 myVirtualNetwork1-myVirtualNetwork2 對等互連的對等互連狀態變更為「已連線」。 使用 [az network vnet peering show](/cli/azure/network/vnet/peering#az_network_vnet_peering_show) 確認 myVirtualNetwork1-myVirtualNetwork2 對等互連的對等互連狀態變更為「已連線」。

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

兩個虛擬網路之對等互連的 **PeeringState** 都是「已連線」之前，其中一個虛擬網路中的資源無法與另一個虛擬網路中的資源通訊。 

對等互連介於兩個虛擬網路之間，但不可轉移。 所以舉例來說，如果您也想要將 myVirtualNetwork2 對等互連至 myVirtualNetwork3，您必須在虛擬網路 myVirtualNetwork2 與 myVirtualNetwork3 之間建立額外的對等互連。 雖然 myVirtualNetwork1 已與 myVirtualNetwork2 對等互連，但是如果 myVirtualNetwork1 也與 myVirtualNetwork3 對等互連，則 myVirtualNetwork1 內的資源只能存取 myVirtualNetwork3 中的資源。 

在對等互連生產環境虛擬網路之前，建議您徹底熟悉[對等互連概觀](virtual-network-peering-overview.md)、[管理對等互連](virtual-network-manage-peering.md)以及[虛擬網路限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 雖然本文說明相同訂用帳戶和位置中兩個虛擬網路之間的對等互連，您也可以對等互連[不同區域](#register)和[不同 Azure 訂用帳戶](create-peering-different-subscriptions.md#cli)中的虛擬網路。 您也可以使用對等互連來建立[中樞和輪輻網路設計](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)。

## <a name="test-peering"></a>測試對等互連

若要測試不同虛擬網路中虛擬機器之間透過對等互連的網路通訊，請將虛擬機器部署到每個子網路，然後在虛擬機器之間進行通訊。 

### <a name="create-virtual-machines"></a>建立虛擬機器

使用 [az vm create](/cli/azure/vm#az_vm_create) 建立虛擬機器。 下列範例會在 myVirtualNetwork1 虛擬網路中建立名為 myVm1 的虛擬機器。 如果預設金鑰位置中還沒有 SSH 金鑰，此命令將會建立這些金鑰。 若要使用一組特定金鑰，請使用 `--ssh-key-value` 選項。 `--no-wait` 選項會在背景建立虛擬機器，因此您可以繼續進行下一步。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

Azure 會自動將 10.0.0.4 指派為虛擬機器的私人 IP 位址，因為 10.0.0.4 是 myVirtualNetwork1 的 Subnet1 中第一個可用的 IP 位址。 

在 myVirtualNetwork2 虛擬網路中建立虛擬機器。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

建立虛擬機器需要幾分鐘的時間。 建立虛擬機器之後，Azure CLI 會顯示類似以下範例的資訊： 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

在輸出範例中，您會看到 **privateIpAddress** 是 10.1.0.4。 Azure DHCP 會自動將 10.1.0.4 指派給虛擬機器，因為這是 myVirtualNetwork2 的 Subnet1 中第一個可用的位址。 請記下 **publicIpAddress**。 這個位址可在稍後的步驟中，用來從網際網路存取虛擬機器。

### <a name="test-virtual-machine-communication"></a>測試虛擬機器通訊

請使用下列命令來建立與 *myVm2* 虛擬機器的 SSH 工作階段。 以虛擬機器的公用 IP 位址取代 `<publicIpAddress>` 位址。 在上述範例中，公用 IP 位址是 13.90.242.231。

```bash 
ssh <publicIpAddress>
```

Ping myVirtualNetwork1 中的虛擬機器。

```bash 
ping 10.0.0.4 -c 4
```

您會收到四個回覆。 如果您依據虛擬機器的名稱 (myVm1) 而不是其 IP 位址來進行 ping，則 ping 會失敗，因為 myVm1 是未知的主機名稱。 Azure 的預設名稱解析可以在相同虛擬網路中的虛擬機器之間運作，但是無法在不同虛擬網路中的虛擬機器之間運作。 若要跨虛擬網路間解析名稱，您必須[部署自己的 DNS 伺服器](virtual-networks-name-resolution-for-vms-and-role-instances.md)或使用 [Azure DNS 私人網域](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

關閉 myVm2 虛擬機器的 SSH 工作階段。 

## <a name="clean-up-resources"></a>清除資源

請使用 [az group delete](/cli/azure/group#az_group_delete) 來移除不再需要的資源群組以及其所包含的所有資源。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

**<a name="register"></a>註冊可獲得虛擬網路對等互連全球預覽版**

在一般情況下，可以為相同地區中的虛擬網路建立對等互連。 讓不同區域中的虛擬網路進行對等互連的功能目前為預覽版。 如需了解有哪些可用的區域，請參閱[虛擬網路更新](https://azure.microsoft.com/updates/?product=virtual-network)。 若要使各區域的虛擬網路對等互連，您必須完成下列步驟 (在您想要對等互連之虛擬網路適用的訂用帳戶內)，先註冊預覽版：

1. 輸入下列命令來註冊預覽版︰

  ```azurecli-interactive
  az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
  az provider register --name Microsoft.Network
  ```

2. 藉由輸入下列命令，確認您已註冊預覽版︰

  ```azurecli-interactive
  az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
  ```

  如果您在針對兩個訂用帳戶輸入上一個命令之後收到的 **RegistrationState** 輸出是「已註冊」之前，嘗試對等互連不同區域中的虛擬網路，對等互連會失敗。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何使用虛擬網路對等互連來連線兩個網路。 您可以透過 VPN [將自己的電腦連線到虛擬網路](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，並且與虛擬網路中或已對等互連虛擬網路中的資源進行互動。

繼續進行可重複使用指令碼的指令碼範例，以完成虛擬網路文章中涵蓋的許多工作。

> [!div class="nextstepaction"]
> [虛擬網路指令碼範例](../networking/cli-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
