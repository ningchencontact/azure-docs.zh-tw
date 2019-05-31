---
title: 轉換 Azure 受控磁碟儲存體從標準，為進階或標準的進階 |Microsoft Docs
description: 如何轉換 Azure 受控磁碟儲存體從標準為進階或標準的進階使用 Azure CLI。
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: bc42bcbf7149f88eb895317a411c7acd5913d63d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417688"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>轉換 Azure 受控磁碟儲存體從標準，為進階或標準的進階

有四個磁碟類型的 Azure 受控磁碟：Azure 的強力 Ssd （預覽）、 進階 SSD、 標準的 SSD 和 HDD 標準。 您可以切換的三種 GA 磁碟類型 (進階 SSD、 標準的 SSD 和 HDD 標準) 根據效能需求。 您尚不能夠切換或強力的 SSD，您必須部署新的資源。

這項功能不支援非受控磁碟的功能。 但您可以輕鬆地[將非受控的磁碟轉換為受控磁碟](convert-unmanaged-to-managed-disks.md)能夠切換磁碟類型。

這篇文章會示範如何將受控的磁碟從標準轉換至進階或標準的 Premium，使用 Azure CLI。 若要安裝或升級工具，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="before-you-begin"></a>開始之前

* 磁碟轉換需要重新啟動虛擬機器 (VM)，所以排定移轉的磁碟儲存體在預先存在的維護期間。
* 針對非受控磁碟，第一個[轉換為受控磁碟](convert-unmanaged-to-managed-disks.md)讓您可以切換儲存體選項。


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>切換所有受控的磁碟的進階和標準之間的 VM

此範例示範如何將轉換的所有 VM 的磁碟從標準到進階儲存體或從 Premium 升級到標準儲存體。 若要使用進階受控磁碟，VM 必須使用可支援進階儲存體的 [VM 大小](sizes.md)。 此範例也會切換為支援進階儲存體的大小。

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>切換 Standard 和 Premium 之間個別的受控的磁碟

您的開發/測試工作負載，您可以混合使用標準和進階磁碟，以降低成本。 您可以選擇升級那些需要更佳的效能的磁碟。 此範例示範如何將轉換的單一 VM 磁碟從標準到進階儲存體或從 Premium 升級到標準儲存體。 若要使用進階受控磁碟，VM 必須使用可支援進階儲存體的 [VM 大小](sizes.md)。 此範例也會切換為支援進階儲存體的大小。

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>切換之間標準 HDD 和 SSD 標準受控的磁碟

此範例示範如何將轉換的單一 VM 磁碟從標準的 HDD 變成標準的 SSD 或標準 SSD 至標準的 HDD。

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>切換 Standard 和 Premium 之間在 Azure 入口網站中的受控的磁碟

請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從清單中選取 VM**虛擬機器**。
3. 如果未停止 VM，請選取**停止**頂端的 [VM**概觀**] 窗格中，並停止 VM 的等候。
4. 在 [vm] 窗格中選取**磁碟**從功能表。
5. 選取您想要轉換的磁碟。
6. 選取 **組態**從功能表。
7. 變更**帳戶類型**從**標準 HDD**來**進階 SSD**進出**進階 SSD**到**標準 HDD**.
8. 選取 **儲存**，並關閉 磁碟 窗格。

磁碟類型的更新是在瞬間完成。 您可以在轉換之後，重新啟動您的 VM。

## <a name="next-steps"></a>後續步驟

使用時，進行 VM 的唯讀副本[快照集](snapshot-copy-managed-disk.md)。
