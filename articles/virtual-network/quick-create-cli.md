---
title: "建立虛擬網路 - Azure CLI | Microsoft Docs"
description: "快速了解如何使用 Azure CLI 來建立虛擬網路。 虛擬網路可讓許多類型的 Azure 資源互相進行私密通訊。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 792b92731f89f3d0bab4f23221223e469ddf9550
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>使用 Azure CLI 建立虛擬網路

在本文中，您將了解如何建立虛擬網路。 建立虛擬網路之後，您需將兩部虛擬機器部署到虛擬網路中，以在兩者間測試私人網路通訊。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0.4 版或更新版本。 若要尋找已安裝的版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#az_group_create) 命令來建立資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。 所有 Azure 資源都會建立在 Azure 位置 (或區域) 內。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-network"></a>建立虛擬網路

請使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 命令來建立虛擬網路。 以下範例會建立名為 *myVirtualNetwork* 的預設虛擬網路，其中含有一個名為 *default* 的子網路。 由於未指定位置，因此 Azure 會在與資源群組相同的位置中建立虛擬網路。

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

建立虛擬網路之後，傳回的一部分資訊如下。

```azurecli
"newVNet": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/16"
```

所有虛擬網路都有一或多個指派的位址首碼。 由於建立虛擬網路時並未指定位址首碼，因此 Azure 根據預設定義了 10.0.0.0/16 位址空間。 位址空間需以 CIDR 標記法指定。 位址空間 10.0.0.0/16 包含 10.0.0.0-10.0.255.254。

傳回的另一部分資訊是命令中所指定 *default* 子網路的 **addressPrefix** *10.0.0.0/24*。 虛擬網路會包含零個或多個子網路。 此命令建立了一個名為 *default* 的單一子網路，但並沒有為此子網路指定任何位址首碼。 沒有為虛擬網路或子網路指定位址首碼時，Azure 預設會定義 10.0.0.0/24 作為第一個子網路的位址首碼。 因此，子網路會包含 10.0.0.0-10.0.0.254，但只有 10.0.0.4-10.0.0.254 可供使用，因為 Azure 會保留每個子網路中的前四個位址 (0-3) 和最後一個位址。

## <a name="test-network-communication"></a>測試網路通訊

虛擬網路可讓數種類型的 Azure 資源互相進行私密通訊。 其中一種您可以部署到虛擬網路中的資源類型是虛擬機器。 在虛擬網路中建立兩部虛擬機器，以便您可以在稍後步驟中驗證它們之間的私密通訊。

### <a name="create-virtual-machines"></a>建立虛擬機器

使用 [az vm create](/cli/azure/vm#az_vm_create) 命令建立虛擬機器。 以下範例會建立名為 *myVm1* 的虛擬機器。 如果預設金鑰位置中還沒有 SSH 金鑰，此命令將會建立這些金鑰。 若要使用一組特定金鑰，請使用 `--ssh-key-value` 選項。 `--no-wait` 選項會在背景建立虛擬機器，因此您可以繼續進行下一步。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

Azure 會自動在 *myVirtualNetwork* 虛擬網路的 *default* 子網路中建立虛擬機器，因為虛擬網路存在於資源群組中，而命令中並未指定任何虛擬網路或子網路。 建立虛擬機器時，Azure DHCP 自動將 10.0.0.4 指派給了虛擬機器，因為這是 *default* 子網路中第一個可用的位址。 虛擬機器的建立位置必須與虛擬網路所的位置相同。 虛擬機器不一定要與虛擬網路屬於相同資源群組，雖然在本文中兩者屬於相同的資源群組。

建立第二部虛擬機器。 根據預設，Azure 也會在 *default* 子網路中建立此虛擬機器。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

建立虛擬機器需要幾分鐘的時間。 建立虛擬機器之後，Azure CLI 會傳回類似以下範例的輸出： 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm1",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

在此範例中，您會看到 **privateIpAddress** 是 *10.0.0.5*。 Azure DHCP 自動將 *10.0.0.5* 指派給了虛擬機器，因為這是 *default* 子網路中下一個可用的位址。 請記下 **publicIpAddress**。 這個位址可在稍後的步驟中，用來從網際網路存取虛擬機器。 公用 IP 位址不是從虛擬網路或子網路位址首碼內指派的。 將會從[指派給每個 Azure 區域的位址集區](https://www.microsoft.com/download/details.aspx?id=41653) \(英文\) 指派公用 IP 位址。 雖然 Azure 知道哪個公用 IP 位址已指派給虛擬機器，但在虛擬機器中執行的作業系統並不會察覺指派給它的任何公用 IP 位址。

### <a name="connect-to-a-virtual-machine"></a>連接到虛擬機器

請使用下列命令來建立與 *myVm2* 虛擬機器的 SSH 工作階段。 以虛擬機器的公用 IP 位址取代 `<publicIpAddress>` 位址。 在上面的範例中，IP 位址是 *40.68.254.142*。

```bash 
ssh <publicIpAddress>
```

### <a name="validate-communication"></a>驗證通訊

請使用下列命令來確認從 *myVm2* 與 *myVm1* 進行的通訊：

```bash
ping myVm1 -c 4
```

您會從 *10.0.0.4* 收到四個回覆。 您可以從 *myVm2* 與 *myVm1* 進行通訊，因為兩部虛擬機器都已從 *default* 子網路獲指派私人 IP 位址。 您能夠依據主機名稱執行 Ping，因為 Azure 會自動為虛擬網路內的所有主機提供 DNS 名稱解析。

請使用下列命令來確認對網際網路的輸出通訊：

```bash
ping bing.com -c 4
```

您會從 bing.com 收到四個回覆。根據預設，虛擬網路中的所有虛擬機器都能對網際網路進行輸出通訊。

結束虛擬機器的 SSH 工作階段。

## <a name="clean-up-resources"></a>清除資源

不再需要資源群組時，您可以使用 [az group delete](/cli/azure/group#az_group_delete) 命令來移除資源群組及其包含的所有資源：

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>後續步驟

在本文中，您已部署含有一個子網路的預設虛擬網路。 若要了解如何建立含有多個子網路的自訂虛擬網路，請繼續進行建立自訂虛擬網路的教學課程。

> [!div class="nextstepaction"]
> [建立自訂虛擬網路](virtual-networks-create-vnet-arm-pportal.md#azure-cli)
