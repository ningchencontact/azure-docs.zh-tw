---
title: "管理 Azure Stack 中的 VM 磁碟 | Microsoft Docs"
description: "佈建Azure Stack 的虛擬機器連結。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/14/2017
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 0c36e2eaaf2d266842b2b7de0b0c8dc0ed1e0145
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2017
---
# <a name="virtual-machine-disk-storage-for-azure-stack"></a>Azure Stack 的虛擬機器連結儲存體

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

Azure Stack 支援在虛擬機器中使用[非受控磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks)作為作業系統 (OS) 磁碟和資料磁碟。 若要使用非受控磁碟，您可建立[儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)，然後將磁碟儲存為儲存體帳戶內容器中的分頁 blob。 這些磁碟之後稱為 VM 磁碟。

若要改善效能並降低 Azure Stack 系統管理成本，建議您將每個 VM 磁碟放在不同的容器中。 容器應保存 OS 磁碟或資料磁碟，但不能同時保存兩者。 不過，不會限制無法將兩者放在相同的容器中。

如果您將一或多個資料磁碟新增到 VM，可計劃使用其他容器作為保存這些磁碟的位置。 如同資料磁碟，其他 VM 的 OS 磁碟也應該位於自己的個別容器中。

當您建立多個 VM 時，您可以對每個新 VM 重複使用相同的儲存體帳戶。 只有您建立的容器應該是唯一的。  

若要將磁碟新增到 VM，請使用使用者入口網站或 PowerShell。

| 方法 | 選項
|-|-|
|[使用者入口網站](#use-the-portal-to-add-additional-disks-to-a-vm)|- 將新資料磁碟新增至先前佈建的 VM。 Azure Stack 會建立新的磁碟。 </br> </br>- 將現有的 .vhd 檔案當作磁碟新增至先前佈建的 VM。 若要這樣做，您必須先準備 .vhd 檔案並將它上傳至 Azure Stack。 |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | - 建立具有 OS 磁碟的新 VM，並同時將一或多個資料磁碟新增到該 VM。 |


## <a name="use-the-portal-to-add-additional-disks-to-a-vm"></a>使用入口網站將其他磁碟新增到 VM
根據預設，當您使用入口網站來建立大多數 Marketplace 項目的 VM 時，只會建立一個 OS 磁碟。 Azure 所建立的磁碟稱為受控磁碟。

在您佈建 VM 之後，可以使用入口網站，將新的資料磁碟或現有的資料磁碟新增到該 VM。 每個額外磁碟都應該放在不同的容器中。 您新增至 VM 的磁碟稱為非受控磁碟。

### <a name="use-the-portal-to-attach-a-new-data-disk-to-a-vm"></a>使用入口網站將新的資料磁碟連結至 VM

1.  在入口網站中，按一下 [虛擬機器]。    
    ![範例：VM 儀表板](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  選取先前已佈建的虛擬機器。   
    ![範例：在儀表板中選取 VM](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  針對虛擬機器，按一下 [磁碟] > [連結新的]。       
    ![範例：將新磁碟連結至 VM](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  在 [連結新磁碟] 窗格中，按一下 [位置]。 根據預設，[位置] 會設定為保存 OS 磁碟的同一個容器。      
    ![範例：設定磁碟位置](media/azure-stack-manage-vm-disks/disk-location.png)

5.  選取要使用的 [儲存體帳戶]。 接下來，選取您要放置資料磁碟的 [容器]。 在 [容器] 頁面中，您可以視需要建立新的容器。 您可以接著將新磁碟的位置變更為其本身的容器。 當您對每個磁碟使用不同的容器時，您可分散資料磁碟的放置位置以改善效能。 按一下 [選取] 儲存選取項目。     
    ![範例：選取容器](media/azure-stack-manage-vm-disks/select-container.png)

6.  在 [連結新磁碟] 頁面中，更新磁碟的 [名稱]、[類型]、[大小] 和 [主機快取] 設定。 然後按一下 [確定] 以儲存 VM 的新磁碟組態。  
    ![範例：完成磁碟連結](media/azure-stack-manage-vm-disks/complete-disk-attach.png)  

7.  在 Azure Stack 建立磁碟並將它連結至虛擬機器之後，新的磁碟會列在虛擬機器之磁碟設定的 [資料磁碟] 底下。   
    ![範例：檢視磁碟](media/azure-stack-manage-vm-disks/view-data-disk.png)


### <a name="attach-an-existing-data-disk-to-a-vm"></a>將現有的資料磁碟附加至 VM
1.  [準備.vhd 檔案](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd)以作為 VM 的資料磁碟。 將該 .vhd 檔案上傳至儲存體帳戶，而您會使用該儲存體帳戶搭配 .vhd 檔案所用連結的 VM。

  計劃使用與保存 OS 磁碟的容器不同的容器來保存 .vhd 檔案。   
  ![範例：上傳 VHD 檔案](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  上傳 .Vhd 檔案之後，您即可將 VHD 連結至 VM。 在左側功能表中，按一下 [虛擬機器]。  
 ![範例：在儀表板中選取 VM](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  然後從清單中選取虛擬機器。    
  ![範例：在儀表板中選取 VM](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  在虛擬機器頁面上，按一下 [磁碟] > [連結現有的]。   
  ![範例：連結現有的磁碟](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  在 [連結現有的磁碟] 頁面中，按一下 [VHD 檔案]。 [儲存體帳戶] 頁面隨即開啟。    
  ![範例：選取 VHD 檔案](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  在 [儲存體帳戶] 下，選取要使用的帳戶，然後選取保存您先前上傳之 .vhd 檔案的容器。 按一下 .vhd 檔案，然後按一下 [選取] 儲存選取項目。    
  ![範例：選取容器](media/azure-stack-manage-vm-disks/select-container2.png)

7.  在 [連結現有磁碟] 下，您選取的檔案會列在 [VHD 檔案] 底下。 更新磁碟的 [主機快取] 設定，然後按一下 [確定] 以儲存 VM 的新磁碟組態。    
  ![範例：連結 VHD 檔案](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  在 Azure Stack 建立磁碟並將它連結至虛擬機器之後，新的磁碟會列在虛擬機器之磁碟設定的 [資料磁碟] 下。   
  ![範例：完成磁碟連結](media/azure-stack-manage-vm-disks/complete-disk-attach.png)


## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>使用 PowerShell 將多個非受控磁碟新增到 VM
您可以使用 PowerShell 來佈建 VM 及新增資料磁碟，或連結既存的 **.vhd** 檔案作為資料磁碟。

**Add-AzureRmVMDataDisk** Cmdlet 會將資料磁碟新增至虛擬機器。 您可以在建立虛擬機器時新增資料磁碟，也可以將資料磁碟新增到現有的虛擬機器。 指定 **VhdUri** 參數，將磁碟分散至不同的容器。

### <a name="add-data-disks-to-a-new-virtual-machine"></a>將資料磁碟新增到新的虛擬機器
下列範例使用 PowerShell 命令來建立具有三個資料磁碟的 VM，且每個放在不同的容器中。

第一個命令會建立虛擬機器物件，然後將它儲存在 $VirtualMachine 變數中。 此命令會指派虛擬機器的名稱和大小。
  ```
  $VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
  ```

接下來三個命令會將三個資料磁碟的路徑指派給 $DataDiskVhdUri01、$DataDiskVhdUri02 和 $DataDiskVhdUri03 變數。 在 URL 中定義不同的路徑名稱，將磁碟分散至不同的容器。     
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```

  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```

  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```

最後三個命令會將資料磁碟新增到 $VirtualMachine 中儲存的虛擬機器。 每個命令可指定磁碟的名稱、位置和其他屬性。 每個磁碟的 URI 會儲存在 $DataDiskVhdUri01、$DataDiskVhdUri02 和 $DataDiskVhdUri03 中。
  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                  -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                  -VhdUri $DataDiskVhdUri01 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                 -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                 -VhdUri $DataDiskVhdUri02 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                  -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                  -VhdUri $DataDiskVhdUri03 -CreateOption Empty
  ```

使用下列 PowerShell 命令將 OS 磁碟和網路組態新增至 VM，然後啟動新的 VM。
  ```
  #set variables
  $rgName = "myResourceGroup"
  $location = "local"
  #Set OS Disk
  $osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
  $osDiskName = "osDisk"

  $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
      -CreateOption FromImage -Windows

  # Create a subnet configuration
  $subnetName = "mySubNet"
  $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

  # Create a vnet configuration
  $vnetName = "myVnetName"
  $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
      -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

  # Create a public IP
  $ipName = "myIP"
  $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
      -AllocationMethod Dynamic

  # Create a network security group cnfiguration
  $nsgName = "myNsg"
  $rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
      -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
      -SourceAddressPrefix Internet -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
      -Name $nsgName -SecurityRules $rdpRule

  # Create a NIC configuration
  $nicName = "myNicName"
  $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
  -Location $location -SubnetId $vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

  #Create the new VM
  $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine | `
      Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
      -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
  New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
  ```



### <a name="add-data-disks-to-an-existing-virtual-machine"></a>將資料磁碟新增到現有的虛擬機器
下列範例使用 PowerShell 命令將三個資料磁碟新增到現有的 VM。
第一個命令會使用 **Get-AzureRmVM** Cmdlet 來取得名為 VirtualMachine 的虛擬機器。 此命令會將虛擬機器儲存在 *$VirtualMachine* 變數中。
  ```
  $VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                  -Name "VirtualMachine"
  ```
接下來三個命令會將三個資料磁碟的路徑指派給 $DataDiskVhdUri01、 $DataDiskVhdUri02 和 $DataDiskVhdUri03 變數。  vhduri 中不同的路徑名稱表示放置磁碟的不同容器。
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```
  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```
  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```


  接下來三個命令會將資料磁碟新增到 $VirtualMachine 變數中儲存的虛擬機器。 每個命令可指定磁碟的名稱、位置和其他屬性。 每個磁碟的 URI 會儲存在 $DataDiskVhdUri01、$DataDiskVhdUri02 和 $DataDiskVhdUri03 中。
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                        -VhdUri $DataDiskVhdUri01 -LUN 0 `
                        -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                        -VhdUri $DataDiskVhdUri02 -LUN 1 `
                        -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                        -VhdUri $DataDiskVhdUri03 -LUN 2 `
                        -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
  ```


  最後一個命令會更新 -ResourceGroupName 的 $VirtualMachine 中儲存之虛擬機器的狀態。
  ```
  Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
  ```
<!-- Pending scripts  

## Distribute the data disks of an existing VM
If you have a VM with more than one disk in the same container, the service operator of the Azure Stack deployment might ask you to redistribute the disks into individual containers.

To do so, use the scripts from the following location in GitHub. These scripts can be used to move the data disks to different containers.
-->

## <a name="next-steps"></a>後續步驟
若要深入了解 Azure Stack 虛擬機器，請參閱 [Azure Stack 中虛擬機器的考量](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations)。
