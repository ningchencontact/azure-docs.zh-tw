---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: sdwheeler
ms.service: virtual-machines
ms.topic: include
ms.date: 04/18/2018
ms.author: kirpas;iainfou;sewhee
ms.custom: include file
ms.openlocfilehash: ab4d5326fc06cc8676ea93ed529315cc47651e26
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2018
ms.locfileid: "36943448"
---
## <a name="overview"></a>概觀
當您在資源群組中，透過從 [Azure Marketplace](https://azure.microsoft.com/marketplace/) 部署映像來建立新的虛擬機器 (VM) 時，預設的作業系統磁碟機為 127 GB (根據預設，有些映像的作業系統磁碟大小比較小)。 即使可以將資料磁碟加入到 VM (數量取決於您所選擇的 SKU)，而且建議將應用程式和需要大量 CPU 的工作負載安裝在這些附加的磁碟上，但客戶有時候還是必須擴充作業系統磁碟機以支援特定的案例，例如︰

1. 支援將元件安裝在作業系統磁碟機上的舊型應用程式。
2. 從具有較大作業系統磁碟機的內部部署移轉實體電腦或虛擬機器。

> [!IMPORTANT]
> Azure 建立和處理資源的部署模型有二種：資源管理員和傳統。 本文涵蓋內容包括如何使用資源管理員模型。 Microsoft 建議讓大部分的新部署使用 Resource Manager 模式。
> 
> 
> [!WARNING]
> 調整 Azure 虛擬機器的作業系統磁碟大小將致使它重新啟動。
>

## <a name="resize-the-os-drive"></a>調整作業系統磁碟機的大小
在本文中，我們將使用 [Azure Powershell](/powershell/azureps-cmdlets-docs)的資源管理員模組，完成調整作業系統磁碟機大小的工作。 我們會說明如何調整非受控和受控磁碟的作業系統磁碟機大小，因為這兩種磁碟類型的磁碟大小調整方法不同。

### <a name="for-resizing-unmanaged-disks"></a>如需調整非受控磁碟的大小：

在系統管理模式下開啟您的 Powershell ISE 或 Powershell 視窗，並依照下列步驟進行︰

1. 在資源管理模式下登入您的 Microsoft Azure 帳戶，並選取您的訂用帳戶，如下所示︰
   
   ```Powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. 設定資源群組名稱和 VM 名稱，如下所示：
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. 取得您 VM 的參考，如下所示︰
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. 在調整磁碟大小之前停止 VM，如下所示︰
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. 接著就是我們期待已久的時刻了！ 將非受控作業系統磁碟的大小設定為所需的值並更新 VM，如下所示︰
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > 新的大小應該大於現有的磁碟大小。 作業系統磁碟允許的上限為 2048 GB。 (可以將 VHD blob 展開超過這個大小，但作業系統只能夠搭配第一個 2048 GB 的空間運作。)
   > 
   > 
6. 更新 VM 可能需要幾秒鐘的時間。 命令執行完畢之後，重新啟動 VM，如下所示︰
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

### <a name="for-resizing-managed-disks"></a>如需調整受控磁碟的大小：

在系統管理模式下開啟您的 Powershell ISE 或 Powershell 視窗，並依照下列步驟進行︰

1. 在資源管理模式下登入您的 Microsoft Azure 帳戶，並選取您的訂用帳戶，如下所示︰
   
   ```Powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. 設定資源群組名稱和 VM 名稱，如下所示：
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. 取得您 VM 的參考，如下所示︰
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. 在調整磁碟大小之前停止 VM，如下所示︰
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. 取得受控作業系統磁碟的參考。 將受控作業系統磁碟的大小設定為所需的值並更新磁碟，如下所示︰
   
   ```Powershell
   $disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > 新的大小應該大於現有的磁碟大小。 作業系統磁碟允許的上限為 2048 GB。 (可以將 VHD blob 展開超過這個大小，但作業系統只能夠搭配第一個 2048 GB 的空間運作。)
   > 
   > 
6. 更新 VM 可能需要幾秒鐘的時間。 命令執行完畢之後，重新啟動 VM，如下所示︰
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

這樣就大功告成了！ 現在 RDP 到 VM，開啟 [電腦管理] \(或 [磁碟管理])，然後使用剛配置的空間擴充磁碟機。

## <a name="summary"></a>總結
在本文中，我們使用 Powershell 的 Azure Resource Manager 模組擴充 IaaS 虛擬機器的作業系統磁碟機。 以下重現非受控和受控磁碟的完整指令碼，供您參考：

非受控磁碟：

```Powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
受控磁碟：

```Powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRMVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="for-resizing-data-disks"></a>針對調整資料磁碟大小
在本文中，我們主要著重於擴充虛擬機器的非受控/受控作業系統磁碟，但是已開發的指令碼也可用於擴充連結至虛擬機器的資料磁碟。 例如，若要擴充連接至 VM 的第一個資料磁碟，請將 ```StorageProfile``` 的 ```OSDisk``` 物件取代成 ```DataDisks``` 陣列，並使用數值索引取得第一個連接的資料磁碟的參考，如下所示︰

非受控磁碟：
```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
受控磁碟：
```Powershell
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

同樣地，您可以使用如上所示的索引，或如下所示的磁碟 ```Name``` 屬性，參考連接至 VM 的其他資料磁碟︰

非受控磁碟：
```Powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```
受控磁碟：
```Powershell
(Get-AzureRmDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

如果您想要了解如何將磁碟連接至 Azure Resource Manager VM，請參閱這篇[文章](../articles/virtual-machines/windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

