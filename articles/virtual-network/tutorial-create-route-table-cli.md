---
title: 路由網路流量 - Azure CLI | Microsoft Docs
description: 了解如何使用 Azure CLI 以路由表路由網路流量。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/02/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 3c16c774fa1c8a5c8bf50b4f4f9d0bfb283318e3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>使用 Azure CLI 以路由表路由網路流量

Azure 依預設會自動路由虛擬網路內所有子網路之間的流量。 您可以建立您自己的路由，以覆寫 Azure 的預設路由。 舉例來說，如果您想要通過防火牆路由子網路之間的流量，則建立自訂路由的能力很有幫助。 在本文中，您將了解如何：

> [!div class="checklist"]
> * 建立路由表
> * 建立路由
> * 將路由表與虛擬網路子網路產生關聯
> * 測試路由
> * 對路由進行疑難排解

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本快速入門會要求您執行 Azure CLI 2.0.28 版或更新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

## <a name="create-a-route-table"></a>建立路由表

根據預設，Azure 會路由虛擬網路內所有子網路之間的流量。 若要深入了解 Azure 的預設路由，請參閱[系統路由](virtual-networks-udr-overview.md)。 若要覆寫 Azure 的預設路由，您必須建立包含路由的路由表，並且將路由表關聯至虛擬網路子網路。

您必須先使用 [az group create](/cli/azure/group#az_group_create) 為本文中建立的所有資源建立資源群組，才能建立路由表。 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

使用 [az network route-table create](/cli/azure/network/route#az_network_route_table_create) 建立路由表。 下列範例會建立名為 *myRouteTablePublic* 的路由表。 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>建立路由

路由表包含零個或多個路由。 使用 [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create) 建立路由表中的路由。 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

路由會透過具有 IP 位址 10.0.2.4 的網路虛擬設備將所有流量導向至 10.0.1.0/24 位址首碼。 在稍後步驟中，會建立具有指定位址首碼的網路虛擬設備和子網路。 路由會覆寫直接在子網路之間路由流量的 Azure 預設路由。 每個路由都會指定下一個躍點類型。 下一個躍點類型會指示 Azure 如何路由流量。 在此範例中，下一個躍點類型是 VirtualAppliance。 若要深入了解 Azure 中所有可用的下一個躍點類型，及其使用時機，請參閱[下一個躍點類型](virtual-networks-udr-overview.md#custom-routes)。

## <a name="associate-a-route-table-to-a-subnet"></a>建立路由表與子網路的關聯

您必須先建立虛擬網路和子網路，才能將路由表關聯至子網路： 使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 建立具有一個子網路的虛擬網路。

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) 建立另外兩個子網路。

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

您可以將路由表關聯至零個或多個子網路。 子網路可以有零個或一個與其相關聯的路由表。 來自子網路的輸出流量，會根據 Azure 的預設路由以及您在與子網路相關聯的路由表中新增的任何自訂路由，來進行路由。 使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) 將 myRouteTablePublic 路由表關聯至公用子網路。

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

在您部署用於生產環境的路由表之前，建議您徹底瞭解 [Azure 中的路由](virtual-networks-udr-overview.md)和 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="test-routing"></a>測試路由

若要測試路由，您必須建立作為網路虛擬設備的虛擬機器，讓您在上一個步驟中建立的路由通過。 建立網路虛擬設備之後，您要將虛擬機器部署到「公用」和「私人」子網路中。 然後，透過網路虛擬設備將「公用」子網路的流量路由至「私人」子網路。

### <a name="create-a-network-virtual-appliance"></a>建立網路虛擬設備

在上一個步驟中，您建立了路由，將網路虛擬設備指定為下一個躍點類型。 執行網路應用程式的虛擬機器通常稱為網路虛擬設備。 在生產環境中，您部署的網路虛擬設備通常是預先設定的虛擬機器。 有數個網路虛擬設備可從 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1) 取得。 在本文中會建立基本虛擬機器。 

使用 [az vm create](/cli/azure/vm#az_vm_create) 在 DMZ 子網路中建立網路虛擬設備。 在建立虛擬機器時，Azure 依預設會建立公用 IP 位址，並將其指派給虛擬機器。 `--public-ip-address ""` 參數會指示 Azure 不要建立公用 IP 位址並將其指派給虛擬機器，因為並不需要從網際網路連線至虛擬機器。 如果預設金鑰位置中還沒有 SSH 金鑰，此命令將會建立這些金鑰。 若要使用一組特定金鑰，請使用 `--ssh-key-value` 選項。

```azure-cli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

建立虛擬機器需要幾分鐘的時間。 在 Azure 建立虛擬機器並傳回虛擬機器的相關輸出之前，請勿繼續執行後續步驟。 在生產環境中，您部署的網路虛擬設備通常是預先設定的虛擬機器。 有數個網路虛擬設備可從 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1) 取得。

每個連結至虛擬機器並轉送流量的 Azure [網路介面](virtual-network-network-interface.md)，若不是以指派給網路介面本身的 IP 位址作為轉送目標，則必須啟用 IP 轉送。 當您在 DMZ 子網路中建立網路虛擬設備時，Azure 會自動建立名為 myVmNvaVMNic 的網路介面、將網路介面連結至虛擬機器，並將私人 IP 位址 10.0.2.4 指派給網路介面。 使用 [az network nic update](/cli/azure/network/nic#az_network_nic_update) 啟用網路介面的 IP 轉送。

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

在虛擬機器內，作業系統或在虛擬機器內執行的應用程式也必須能夠轉送網路流量。 當您在生產環境中部署網路虛擬設備時，設備通常會在轉送流量之前篩選、記錄或執行一些其他函式。 但在本文中，作業系統只會轉送它接收的所有流量。 請使用 [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) 執行會在作業系統內啟用 IP 轉送的命令，以在虛擬機器的作業系統內啟用 IP 轉送。

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
執行此命令可能需要一分鐘。

### <a name="create-virtual-machines"></a>建立虛擬機器

在虛擬網路中建立兩個虛擬機器，以便在後續步驟中驗證來自「公用」子網路的流量是透過網路虛擬設備路由至「私人」子網路。 

使用 [az vm create](/cli/azure/vm#az_vm_create) 在「公用」子網路中建立虛擬機器。 `--no-wait` 參數可以讓 Azure 在背景中執行命令，因此您可以繼續執行下一個命令。 為了簡化本文，我們會使用密碼。 金鑰通常用於生產環境部署中。 如果您使用金鑰，您還必須設定 SSH 代理程式轉送。 如需詳細資訊，請參閱 SSH 用戶端的文件。 在下列命令中，將 `<replace-with-your-password>` 取代為您選擇的密碼。

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

建立虛擬機器需要幾分鐘的時間。 建立虛擬機器之後，Azure CLI 會顯示類似以下範例的資訊： 

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
請記下 **publicIpAddress**。 這個位址可在稍後的步驟中，用來從網際網路存取虛擬機器。

### <a name="route-traffic-through-a-network-virtual-appliance"></a>透過網路虛擬設備來路由傳送流量

請使用下列命令來建立與 myVmMgmt 虛擬機器的 SSH 工作階段。 以虛擬機器的公用 IP 位址取代 *<publicIpAddress>*。 在上述範例中，IP 位址是 *13.90.242.231*。

```bash 
ssh azureuser@<publicIpAddress>
```

出現密碼的提示時，請輸入您在[建立虛擬機器](#create-virtual-machines)中選取的密碼。

使用下列命令安裝 myVmMgmt 虛擬機器的追蹤路由：

```bash 
sudo apt-get install traceroute
```

使用下列命令，測試從 myVmMgmt 虛擬機器到 myVmWeb 虛擬機器的網路流量路由。

```bash
traceroute myvmweb
```

回應如下列範例所示：

```bash
traceroute to myvmweb (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

您可以看到流量是從 myVmMgmt 虛擬機器直接路由至 myVmWeb 虛擬機器。 Azure 的預設路由會直接路由子網路之間的流量。 

使用下列命令，從 myVmMgmt 虛擬機器透過 SSH 連線至 myVmWeb 虛擬機器：

```bash 
ssh azureuser@myVmWeb
```

使用下列命令安裝 myVmWeb 虛擬機器的追蹤路由：

```bash 
sudo apt-get install traceroute
```

使用下列命令，測試從 myVmWeb 虛擬機器到 myVmMgmt 虛擬機器的網路流量路由。

```bash
traceroute myvmmgmt
```

回應如下列範例所示：

```bash
traceroute to myvmmgmt (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
您可以看到第一個躍點是 10.0.2.4，也就是網路虛擬設備的私人 IP 位址。 第二躍點是 10.0.1.4，也就是 myVmMgmt 虛擬機器的私人 IP 位址。 新增至 myRouteTablePublic 路由表且與「公用」子網路相關聯的路由，會使 Azure 透過 NVA 路由流量，而不是直接路由至「私人」子網路。

將 *myVmWeb* 和 *myVmMgmt* 虛擬機器的 SSH 工作階段都關閉。

## <a name="troubleshoot-routing"></a>對路由進行疑難排解

如同您在先前步驟中所了解的，Azure 會套用預設路由，而您可以選擇性地使用您自己的路由加以覆寫。 有時候，流量可能無法如您預期的進行路由。 請使用 [az network watcher show-next-hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop) 來判斷兩個虛擬機器之間的流量路由方式。 例如，下列命令會測試從 myVmWeb (10.0.0.4) 虛擬機器路由至 myVmMgmt (10.0.1.4) 虛擬機器的流量：

```azurecli-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region.
az network watcher configure --locations eastus --resource-group myResourceGroup --enabled true

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 10.0.1.4 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVmWeb \
  --out table
```
稍待片刻後，會傳回下列輸出：

```azurecli
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

輸出會通知您從 myVmWeb 到 myVmMgmt 之流量的下一個躍點 IP 位址是 10.0.2.4 (myVmNva 虛擬機器)，下一個躍點類型是 VirtualAppliance，造成路由的路由表是 myRouteTablePublic。

每個網路介面的有效路由是 Azure 的預設路由和您定義之任何路由的組合。 請使用 [az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table) 查看虛擬機器中網路介面的所有有效路由。 例如，若要顯示 myVmWeb 虛擬機器中 MyVmWebVMNic 網路介面的有效路由，請輸入下列命令：

```azurecli-interactive
az network nic show-effective-route-table \
  --name MyVmWebVMNic \
  --resource-group myResourceGroup
```

所有預設路由與您在上一個步驟中新增的路由都會傳回。

## <a name="clean-up-resources"></a>清除資源

請使用 [az group delete](/cli/azure/group#az_group_delete) 來移除不再需要的資源群組以及其所包含的所有資源。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>後續步驟

在本文中，您已建立路由表並將其關聯至子網路。 您建立了將來自公用子網路的流量路由至私人子網路的網路虛擬設備。 雖然您可以在虛擬網路內部署許多 Azure 資源，但是某些 Azure PaaS 服務的資源無法部署到虛擬網路中。 您仍然可以將某些 Azure PaaS 服務資源的存取，限定於來自虛擬網路子網路的流量。 請繼續進行下一個教學課程，以了解如何限制對 Azure PaaS 資源的網路存取。

> [!div class="nextstepaction"]
> [限制對 PaaS 資源的網路存取](virtual-network-service-endpoints-configure.md#azure-cli)
