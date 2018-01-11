---
title: "使用加速網路來建立 Azure 虛擬機器 | Microsoft Docs"
description: "了解如何以加速網路建立 Linux 虛擬機器。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/02/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 908d81c363a556917d211e0bcc92188f849fb690
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>建立 Linux 虛擬機器使用加速網路功能

> [!IMPORTANT] 
> 必須具有啟用加速網路建立虛擬機器。 無法在現有的虛擬機器上啟用此功能。 您可以依照下列步驟來啟用加速網路：
>   1. 刪除虛擬機器。
>   2. 重新建立虛擬機器使用加速網路功能啟用。
>

在本教學課程中，您會學習如何以加速網路建立 Linux 虛擬機器 (VM)。 加速網路可以對 VM 啟用 Single Root I/O Virtualization (SR-IOV)，大幅提升其網路效能。 這個高效能的路徑會略過主應用程式資料路徑，從減少延遲、 抖動和支援的 VM 類型的最嚴苛網路工作負載搭配使用的 CPU 使用率。 下圖顯示兩個 Vm 時或無加速網路之間的通訊：

![比較](./media/create-vm-accelerated-networking/accelerated-networking.png)

如果沒有加速網路，進出 VM 的所有網路流量都必須周遊主機和虛擬交換器。 虛擬交換器對網路流量提供所有原則強制執行，例如網路安全性群組、存取控制清單、隔離性以及其他網路虛擬化服務。 若要深入了解虛擬交換器，請閱讀 [Hyper-V Network Virtualization and Virtual Switch (Hyper-V 網路虛擬化和虛擬交換器)](https://technet.microsoft.com/library/jj945275.aspx) 文章。

如果使用加速網路，網路流量就會先送達 VM 的網路介面 (NIC)，然後轉送到 VM。 所有適用於虛擬交換器的網路原則現在會卸載，而套用的硬體。 在硬體中套用原則會讓 NIC 略過主機和虛擬交換器，同時在主機中維護套用的所有原則，直接將網路流量轉送到 VM。

加速網路的優點只適用於已啟用此功能的 VM。 為了獲得最佳結果，最好在至少兩部連線到相同 Azure 虛擬網路 (VNet) 的 VM 上啟用此功能。 當透過 VNet 通訊或連線內部部署時，此功能對整體延遲的影響可以降到最低。

## <a name="benefits"></a>優點
* **較低的延遲 / 較高的每秒封包數目 (pps)：** 從資料路徑移除虛擬交換器會減少主機中封包在處理原則時所花的時間，並增加 VM 內可處理的封包數目。
* **減少抖動︰** 虛擬交換器處理視需要套用的原則數量和正在進行處理的 CPU 工作負載而定。 將原則強制執行卸載到硬體透過將封包直接傳遞到 VM、移除主機到 VM 的通訊，以及所有軟體插斷和環境切換，而減少變化。
* **降低 CPU 使用率︰** 略過主機中的虛擬交換器可減少處理網路流量的 CPU 使用率。

## <a name="supported-operating-systems"></a>受支援的作業系統
* **Ubuntu 16.04**: 4.11.0-1013 或更高的核心版本
* **SLES SP3**: 4.4.92-6.18 或更高的核心版本
* **RHEL**: 7.4.2017120423 或更高的核心版本
* **CentOS**: 7.4.20171206 或更高的核心版本

## <a name="supported-vm-instances"></a>支援的 VM 執行個體
加速網路功能支援大多數的一般用途和 4 或更多 Vcpu 計算最佳化的執行個體大小。 執行個體上例如 D/DSv3 或 E/ESv3 支援超執行緒，加速網路都支援具有 8 個以上 Vcpu 的 VM 執行個體。  支援的數列： D/DSv2、 D/DSv3、 E/ESv3、 F/Fs/Fsv2 和 Ms/Mms。 

如需有關 VM 執行個體的詳細資訊，請參閱[Linux VM 大小](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="regions"></a>區域
適用於東亞除外的所有公用 Azure 區域。   尚未支援 azure 政府雲端。

## <a name="limitations"></a>限制
使用這項功能時，有下列限制︰

* **網路介面建立︰**您只能對新的 NIC 啟用加速網路。 無法對現有 NIC 來啟用。
* **VM 建立：**啟用加速網路的 NIC 只能在 VM 建立之後附加至 VM。 NIC 無法附加至現有的 VM。 如果將 VM 加入至現有的可用性設定，在可用性設定組的所有 Vm 必須也有都加速網路啟用。
* **部署只透過 Azure 資源管理員：**無法部署虛擬機器 （傳統），以加速網路。

## <a name="create-a-virtual-network"></a>建立虛擬網路

請安裝最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) 並使用 [az login](/cli/azure/#login) 來登入 Azure 帳戶。 在下列範例中，請以您自己的值取代範例參數名稱。 所包含的範例參數名稱*myResourceGroup*， *myNic*，和*myVm*。

使用 [az group create](/cli/azure/group#create) 來建立資源群組。 下列範例會建立名為的資源群組*myResourceGroup*中*centralus*位置：

```azurecli
az group create --name myResourceGroup --location centralus
```

您必須選取支援的 Linux 地區中所列[Linux 加速網路](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview)。

使用 [az network vnet create](/cli/azure/network/vnet#create) 建立虛擬網路。 下列範例會建立虛擬網路，名為*myVnet*具有一個子網路：

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-network-security-group"></a>建立網路安全性群組
使用 [az network nsg create](/cli/azure/network/nsg#create) 建立網路安全性群組。 下列範例建立名為 myNetworkSecurityGroup 的網路安全性群組：

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

網路安全性群組包含數個預設規則，其中會停用所有從網際網路存取。 開啟連接埠以允許虛擬機器的 SSH 存取[az 網路 nsg 規則建立](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>建立含加速網路功能的網路介面

使用 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 建立公用 IP 位址。 如果您不打算從網際網路存取虛擬機器，但若要完成這篇文章中的步驟，不需要公用 IP 位址，它是必要。

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

建立的網路介面[az 網路 nic 建立](/cli/azure/network/nic#create)加速網路啟用與。 下列範例會建立名為的網路介面*myNic*中*mySubnet*子網路的*myVnet*虛擬網路並將相關聯*myNetworkSecurityGroup*給網路介面的網路安全性群組：

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nic"></a>建立 VM，並且附加 NIC
當您建立指定 NIC 的 VM，您建立的`--nics`。 您必須選取大小，並發佈列入[Linux 加速網路](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview)。 

使用 [az vm create](/cli/azure/vm#create) 建立 VM。 下列範例會建立名為的 VM *myVM* UbuntuLTS 映像和加速網路所支援的大小 (*Standard_DS4_v2*):

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

如需所有 VM 大小和特性的清單，請參閱[Linux VM 大小](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

一旦建立 VM 時，就會傳回類似下列的範例輸出的輸出。 請注意**publicIpAddress**。 此位址用來存取後續步驟中的 VM。

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="confirm-that-accelerated-networking-is-enabled"></a>確認已啟用加速網路功能

使用下列命令來對 VM 建立 SSH 工作階段。 取代`<your-public-ip-address>`公用 IP 位址指派給虛擬機器所建立，並取代*azureuser*如果您使用不同的值`--admin-username`當您在建立 VM。

```bash
ssh azureuser@<your-public-ip-address>
```

從 Bash 殼層中，輸入`uname -r`，並確認核心版本是其中一個的下列版本，或更新版本：

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


確認 Mellanox VF 裝置公開給具有 VM`lspci`命令。 傳回的輸出會類似於下列輸出：

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

檢查 VF （虛擬函式） 上的活動與`ethtool -S eth0 | grep vf_`命令。 如果您收到的輸出類似下列的範例輸出，加速的網路啟用狀態和使用。

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
現在已啟用您 VM 的加速網路。
