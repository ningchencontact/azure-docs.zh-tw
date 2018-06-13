---
title: 建立具有多個子網路的 Azure 虛擬網路 - Azure CLI | Microsoft Docs
description: 了解如何使用 Azure CLI 建立具有多個子網路的虛擬網路。
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 0b0bfae02147910d98231d7c93f5ab260f26364f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880528"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-cli"></a>使用 Azure CLI 建立具有多個子網路的虛擬網路

虛擬網路可讓數種類型的 Azure 資源與網際網路通訊，以及讓資源彼此私密通訊。 在虛擬網路中建立多個子網路可讓您分割網路，以便能夠篩選或控制子網路之間流量的流動。 在本文中，您將了解如何：

> [!div class="checklist"]
> * 建立虛擬網路
> * 建立子網路
> * 測試虛擬機器之間的網路通訊

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本快速入門會要求您執行 Azure CLI 2.0.4 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli.md)。 

## <a name="create-a-virtual-network"></a>建立虛擬網路

使用 [az group create](/cli/azure/group#az_group_create) 來建立資源群組。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 建立虛擬網路。 下列範例會建立名為 myVirtualNetwork 的虛擬網路，位址首碼為 10.0.0.0/16。 此命令會建立一個名為「公用」的子網路，其位址首碼為 10.0.0.0/24。

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

因為前一個命令中未指定位置，所以 Azure 會在 myResourceGroup 資源群組所在的相同位置建立虛擬網路。 **address-prefixes** 和 **subnet-prefix** 需以 CIDR 標記法指定。 指定的位址首碼包括 IP 位址 10.0.0.0-10.0.255.254。 針對子網路指定的首碼必須位於針對虛擬網路定義的位址首碼內。 Azure DHCP 會將子網路位址首碼中的 IP 位址指派給子網路中所部署的資源。 Azure 只會將位址 10.0.0.4-10.0.0.254 指派給**公用**子網路內所部署的資源，因為 Azure 會保留每個子網路中的前四個位址 (在此範例中為子網路的 10.0.0.0-10.0.0.3) 和最後一個位址 (在此範例中為子網路的 10.0.0.255)。

## <a name="create-a-subnet"></a>建立子網路

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) 建立子網路。 下列範例會在 myVirtualNetwork 虛擬網路中建立名為「私人」的子網路，位址首碼為 10.0.1.0/24。 位址首碼必須在針對虛擬網路定義的位址首碼內，而且不能與虛擬網路中任何其他子網路的位址首碼重疊。

```azurecli-interactive 
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24
```

部署 Azure 虛擬網路和子網路以在生產環境中使用之前，建議您徹底熟悉位址空間[考量](manage-virtual-network.md#create-a-virtual-network)和[虛擬網路限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 一旦資源部署至子網路，某些虛擬網路和子網路變更 (例如變更位址範圍) 可能需要重新部署子網路內已部署的現有 Azure 資源。

## <a name="test-network-communication"></a>測試網路通訊

虛擬網路可讓數種類型的 Azure 資源與網際網路通訊，以及讓資源彼此私密通訊。 其中一種您可以部署到虛擬網路中的資源類型是虛擬機器。 在虛擬網路中建立兩部虛擬機器，以便您可以在稍後步驟中測試它們之間的網路通訊和網際網路。

### <a name="create-virtual-machines"></a>建立虛擬機器

使用 [az vm create](/cli/azure/vm#az_vm_create) 建立虛擬機器。 下列範例會在「公用」子網路中建立名為 myVmWeb 的虛擬機器。 `--no-wait` 參數可以讓 Azure 在背景中執行命令，因此您可以繼續執行下一個命令。 為了簡化此教學課程，我們會使用密碼。 金鑰通常用於生產環境部署中。 如果您使用金鑰，您還必須設定 SSH 代理程式轉送才能完成其餘步驟。 如需 SSH 代理程式轉送的詳細資訊，請參閱 SSH 用戶端的文件。 在下列命令中，將 `<replace-with-your-password>` 取代為您選擇的密碼。

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Azure 會自動將 10.0.0.4 指派為虛擬機器的私人 IP 位址，因為 10.0.0.4 是「公用」子網路中第一個可用的 IP 位址。 

在「私人」子網路中建立虛擬機器。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```
建立虛擬機器需要幾分鐘的時間。 建立虛擬機器之後，Azure CLI 會傳回類似以下範例的輸出： 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmMgmt",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

在輸出範例中，您會看到 **privateIpAddress** 是 10.0.1.4。 Azure 已建立[網路介面](virtual-network-network-interface.md)、將它連結至虛擬機器、對網路介面指派私人 IP 位址，以及指派 **macAddress**。 因為 10.0.1.4 是「私人」子網路中第一個可用的 IP 位址，所以 Azure DHCP 會自動將 10.0.1.4 指派給網路介面。 私人 IP 和 MAC 位址會保持指派給網路介面，直到網路介面刪除為止。 

請記下 **publicIpAddress**。 這個位址可在稍後的步驟中，用來從網際網路存取虛擬機器。 雖然不需要對虛擬機器指派公用 IP 位址，但 Azure 預設會對您建立的每個虛擬機器指派公用 IP 位址。 若要從網際網路對虛擬機器進行通訊，就必須對虛擬機器指派公用 IP 位址。 無論是否有對虛擬機器指派公用 IP 位址，虛擬機器都可以對外與網際網路通訊。 若要深入了解 Azure 中的輸出網際網路連線，請參閱 [Azure 中的輸出連線](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

在本文中所建立的虛擬機器有一個[網路介面](virtual-network-network-interface.md)，其具有一個動態指派給網路介面的 IP 位址。 您部署虛擬機器之後，可以[新增多個公用和私人 IP 位址，也可以將 IP 位址指派方法變更為靜態](virtual-network-network-interface-addresses.md#add-ip-addresses)。 您可以[新增網路介面](virtual-network-network-interface-vm.md#vm-add-nic)，最多可達您在建立虛擬機器時選取之[虛擬機器大小](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)所支援的限制。 您也可以針對虛擬機器[啟用單一根目錄 I/O 虛擬化 (SR-IOV)](create-vm-accelerated-networking-cli.md)，但前提是您所建立的虛擬機器必須具有支援該功能的虛擬機器大小。

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>在虛擬機器之間通訊以及與網際網路通訊

請使用下列命令來建立與 myVmMgmt 虛擬機器的 SSH 工作階段。 以虛擬機器的公用 IP 位址取代 `<publicIpAddress>` 位址。 在上述範例中，公用 IP 位址是 13.90.242.231。 出現輸入密碼的提示時，輸入您在[建立虛擬機器](#create-virtual-machines)中輸入的密碼。

```bash 
ssh azureuser@<publicIpAddress>
```

為確保安全，通常會限制可以在虛擬網路中遠端連線的虛擬機器數目。 在本教學課程中，myVmMgmt 虛擬機器是用來管理虛擬網路中的 myVmWeb 虛擬機器。 使用下列命令，從 myVmMgmt 虛擬機器透過 SSH 連線至 myVmWeb 虛擬機器：

```bash 
ssh azureuser@myVmWeb
```

若要從 myVmWeb 虛擬機器對 myVmMgmt 虛擬機器通訊，請從命令提示字元輸入下列命令：

```
ping -c 4 myvmmgmt
```

您會收到類似下列輸出範例的輸出：
    
```
PING myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net (10.0.1.4) 56(84) bytes of data.
64 bytes from 10.0.1.4: icmp_seq=1 ttl=64 time=1.45 ms
64 bytes from 10.0.1.4: icmp_seq=2 ttl=64 time=0.628 ms
64 bytes from 10.0.1.4: icmp_seq=3 ttl=64 time=0.529 ms
64 bytes from 10.0.1.4: icmp_seq=4 ttl=64 time=0.674 ms

--- myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3029ms
rtt min/avg/max/mdev = 0.529/0.821/1.453/0.368 ms
```
      
您可以看到 myVmMgmt 虛擬機器的位址是 10.0.1.4。 在您於上一個步驟中將 myVmMgmt 虛擬機器部署所在的私人子網路內，10.0.1.4 是該子網路位址範圍中可用的第一個 IP 位址。  您會看到虛擬機器的完整網域名稱是 myvmmgmt.hxehizax3z1udjnrx1r4gr30pg.bx.internal.cloudapp.net。 雖然在您的虛擬機器中，網域名稱的 hxehizax3z1udjnrx1r4gr30pg 部分並不相同，但是網域名稱的其餘部分都相同。 根據預設，Azure 虛擬機器都會使用預設的 Azure DNS 服務。 虛擬網路內的虛擬機器全都可以使用 Azure 的預設 DNS 服務，解析相同虛擬網路中所有其他虛擬機器的名稱。 若不使用 Azure 的預設 DNS 服務，您也可以使用您自己的 DNS 伺服器或 Azure DNS 服務的私人網域功能。 如需詳細資訊，請參閱[使用專屬 DNS 伺服器的名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)或[將 Azure DNS 用於私人網域](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

使用下列命令在 myVmWeb 虛擬機器上安裝 nginx Web 伺服器：

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

nginx 安裝好之後，請關閉 myVmWeb SSH 工作階段，這樣會讓您停留在 myVmMgmt 虛擬機器的提示。 輸入下列命令以從 myVmWeb 虛擬機器擷取 nginx 歡迎畫面。

```bash
curl myVmWeb
```

隨即會傳回 nginx 歡迎畫面。

關閉 myVmMgmt 虛擬機器的 SSH 工作階段。

當 Azure 建立 myVmWeb 虛擬機器時，名為 myVmWebPublicIP 的公用 IP 位址也會建立並指派給虛擬機器。 使用 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) 取得 Azure 指派的公用位址。

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myVmWebPublicIP \
  --query ipAddress
```

從您自己的電腦中輸入下列命令，將 `<publicIpAddress>` 取代為前一個命令傳回的位址：

```bash
curl <publicIpAddress>
```

從您自己的電腦利用 curl 來傳輸 nginx 歡迎畫面的嘗試會失敗。 嘗試會失敗是因為虛擬機器在部署時，Azure 預設是為每個虛擬機器建立網路安全性群組。 

網路安全性群組包含安全性規則，會依連接埠和 IP 位址允許或拒絕傳入和傳出網路流量。 Azure 建立的預設網路安全性群組允許相同虛擬網路中的資源透過所有連接埠彼此通訊。 對於 Linux 虛擬機器，預設網路安全性群組會拒絕從網際網路透過所有連接埠傳入的所有流量，但會接受 TCP 連接埠 22 (SSH)。 因此根據預設，您也可以建立從網際網路直接連線至 myVmWeb 虛擬機器的 SSH 工作階段，即便您並不想要讓連接埠 22 對 Web 伺服器開放。 既然 `curl` 命令是透過連接埠 80 來通訊，因為預設網路安全性群組中沒有任何規則允許流量通過通訊埠 80，所以從網際網路進行通訊會失敗。

## <a name="clean-up-resources"></a>清除資源

請使用 [az group delete](/cli/azure/group#az_group_delete) 來移除不再需要的資源群組以及其所包含的所有資源。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何部署具有多個子網路的虛擬網路。 您也已了解在建立 Linux 虛擬機器時，Azure 會建立網路介面並連結至虛擬機器，而且會建立網路安全性群組以便只允許從網際網路透過連接埠 22 傳輸流量。 請前進到下一個教學課程，以了解如何篩選進入子網路的網路流量，而不是進入個別虛擬機器的網路流量。

> [!div class="nextstepaction"]
> [篩選進入子網路的網路流量](./virtual-networks-create-nsg-arm-cli.md)
