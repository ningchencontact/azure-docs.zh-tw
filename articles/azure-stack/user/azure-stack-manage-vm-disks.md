---
title: 管理 Azure Stack 中的 VM 磁碟 | Microsoft Docs
description: 在 Azure Stack 中建立虛擬機器的磁碟。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/18/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.openlocfilehash: df67c92d4a8856292e1d429eb9d69bea69a82640
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54478258"
---
# <a name="create-virtual-machine-disk-storage-in-azure-stack"></a>在 Azure Stack 中建立虛擬機器磁碟儲存體

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

本文說明如何使用 Azure Stack 入口網站或使用 PowerShell 來建立虛擬機器磁碟儲存體。

## <a name="overview"></a>概觀

從 1808 版開始，Azure Stack 支援在虛擬機器上使用受控磁碟和非受控磁碟作為作業系統 (OS) 和資料磁碟。 在 1808 版之前，只支援非受控磁碟。 

**[受控磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#managed-disks)** 會管理與 VM 磁碟相關的儲存體帳戶，從而簡化 Azure IaaS VM 的磁碟管理。 您只需要指定您需要的磁碟大小，Azure Stack 就會替您建立和管理磁碟。

**[非受控磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks)** 需要建立[儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)來儲存磁碟。 您建立的磁碟就是 VM 磁碟，並且會儲存在儲存體帳戶的容器中。

### <a name="best-practice-guidelines"></a>最佳做法指南

為了改善效能並降低總成本，建議您將每個 VM 磁碟放在不同的容器中。 容器應保存 OS 磁碟或資料磁碟，但不能同時保存兩者。 (不過，沒有東西能防止您將這兩種磁碟放在相同容器中。)

如果您將一或多個資料磁碟新增到 VM，可使用其他容器作為保存這些磁碟的位置。 其他 VM 的 OS 磁碟也應該位於自己的容器中。

當您建立 VM 時，您可以對每個新的虛擬機器重複使用相同的儲存體帳戶。 只有您建立的容器應該是唯一的。

### <a name="adding-new-disks"></a>新增磁碟

下表摘要說明如何使用入口網站以及使用 PowerShell 新增磁碟。

| 方法 | 選項
|-|-|
|[使用者入口網站](#use-the-portal-to-add-additional-disks-to-a-vm)|- 將新的資料磁碟新增至現有的 VM。 Azure Stack 會建立新的磁碟。 </br> </br>- 將現有磁碟 (.vhd) 檔案新增至先前建立的 VM。 若要這樣做，您必須先準備 .vhd 檔案並將它上傳至 Azure Stack。 |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | - 建立具有 OS 磁碟的新 VM，並同時將一或多個資料磁碟新增到該 VM。 |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>使用入口網站將磁碟新增到 VM

根據預設，當您使用入口網站為大多數 Marketplace 項目建立 VM 時，只會建立一個 OS 磁碟。

建立 VM 之後，您可以使用入口網站：
* 建立新的資料磁碟，並將它連結至 VM。
* 上傳現有資料磁碟，並將它連結至 VM。

您所新增的每個非授控磁碟都應該放在不同的容器中。

>[!NOTE]  
>Azure 所建立並管理的磁碟稱為[受控磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)。

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>使用入口網站建立並連結新的資料磁碟

1.  在入口網站中，選擇 [所有服務] > [虛擬機器]。    
    ![範例：VM 儀表板](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  選取先前建立的虛擬機器。   
    ![範例：在儀表板中選取 VM](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  為虛擬機器選取 [磁碟] > [新增資料磁碟]。       
    ![範例：將新磁碟連結至 VM](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  針對資料磁碟：
    -  輸入 **LUN**。 LUN 必須是有效的數字。
    -  選取 [建立磁碟]。
    ![範例：將新磁碟連結至 VM](media/azure-stack-manage-vm-disks/add-a-data-disk-create-disk.png)

5.  在 [建立受控磁碟] 刀鋒視窗中：
    -  輸入磁碟的 [名稱]。
    -  選取現有的資源群組或建立新的 [資源群組]。
    -  選取 [位置]。 根據預設，位置會設定為保存 OS 磁碟的同一個容器。
    -  選取 [帳戶類型]。 
        ![範例：將新磁碟連結至 VM](media/azure-stack-manage-vm-disks/create-manage-disk.png)

        **進階 SSD**  
        進階磁碟 (SSD) 採用固態硬碟，可提供一致、低延遲的效能。 進階磁碟可讓您在價格和效能之間取得最佳的平衡，並且適用於 I/O 密集型應用程式和生產工作負載。
       
        **標準 HDD**  
        標準磁碟 (HDD) 採用磁碟機，適用於不常存取資料的應用程式。 區域備援磁碟採用區域備援儲存體 (ZRS)，此儲存體可跨多個區域複寫資料，且即使在單一區域停止運作時也可供使用。 

    -  選取 [來源類型]。

       從另一個磁碟的快照集、儲存體帳戶中的 Blob 建立磁碟，或建立空的磁碟。

        **快照集**  
        選取快照集 (如果有的話)。 快照集必須位於 VM 的訂用帳戶和位置中。

        **儲存體 Blob**  
        - 新增包含磁碟映像的儲存體 Blob 的 URI。  
        - 選取 [瀏覽] 以開啟 [儲存體帳戶] 刀鋒視窗。 如需指示，請參閱[從儲存體帳戶新增資料磁碟](#add-a-data-disk-from-a-storage-account)。
        - 選取映像的 OS 類型，可以是**Windows**、**Linux** 或**無 (資料磁碟)**。

        **無 (空磁碟)**

    -  選取 [大小 (GiB)]。

       標準磁碟的成本會隨著磁碟的大小而增加。 進階磁碟的成本和效能會隨著磁碟的大小而提高。 如需詳細資訊，請參閱[受控磁碟定價](https://go.microsoft.com/fwlink/?linkid=843142)。

    -  選取 [建立] 。 Azure Stack 會建立並驗證受控磁碟。

5.  在 Azure Stack 建立磁碟並將它連結至虛擬機器之後，新的磁碟會列在虛擬機器之磁碟設定的 [資料磁碟] 底下。   

    ![範例：檢視磁碟](media/azure-stack-manage-vm-disks/view-data-disk.png)

### <a name="add-a-data-disk-from-a-storage-account"></a>從儲存體帳戶新增資料磁碟

如需關於在 Azure Stack 中使用儲存體帳戶的詳細資訊，請參閱 [Azure Stack 儲存體簡介](azure-stack-storage-overview.md)。

1. 選取要使用的 [儲存體帳戶]。
2. 選取要放置資料磁碟的 [容器]。 在 [容器] 刀鋒視窗中，您可以視需要建立新的容器。 您可以接著將新磁碟的位置變更為其本身的容器。 當您對每個磁碟使用不同的容器時，您可分散資料磁碟的放置位置以改善效能。
3. 選擇 [選取] 儲存選取項目。

    ![範例：選取容器](media/azure-stack-manage-vm-disks/select-container.png)

## <a name="attach-an-existing-data-disk-to-a-vm"></a>將現有的資料磁碟附加至 VM

1.  [準備.vhd 檔案](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd)以作為 VM 的資料磁碟。 將該 .vhd 檔案上傳至儲存體帳戶，而您會使用該儲存體帳戶搭配 .vhd 檔案所用連結的 VM。

    計劃使用與保存 OS 磁碟的容器不同的容器來保存 .vhd 檔案。   
    ![範例：上傳 VHD 檔案](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  上傳 .Vhd 檔案之後，您即可將 VHD 連結至 VM。 在左邊的功能表上，選取 [虛擬機器]。  
 ![範例：在儀表板中選取 VM](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  然後從清單中選擇虛擬機器。

    ![範例：在儀表板中選取 VM](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  在虛擬機器頁面上，選取 [磁碟] > [連結現有的]。   

    ![範例：連接現有磁碟](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  在 [連結現有的磁碟] 頁面中，選取 [VHD 檔案]。 [儲存體帳戶] 頁面隨即開啟。    

    ![範例：選取 VHD 檔案](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  在 [儲存體帳戶] 下，選取要使用的帳戶，然後選擇保存您先前上傳之 .vhd 檔案的容器。 選取 .vhd 檔案，然後選擇 [選取] 儲存選取項目。    

    ![範例：選取容器](media/azure-stack-manage-vm-disks/select-container2.png)

7.  在 [連結現有磁碟] 下，您選取的檔案會列在 [VHD 檔案] 底下。 更新磁碟的 [主機快取] 設定，然後選取 [確定] 以儲存 VM 的新磁碟設定。    

    ![範例：連結 VHD 檔案](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  在 Azure Stack 建立磁碟並將它連結至虛擬機器之後，新的磁碟會列在虛擬機器之磁碟設定的 [資料磁碟] 下。   

    ![範例：完成磁碟連結](media/azure-stack-manage-vm-disks/complete-disk-attach.png)

## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>使用 PowerShell 將多個非受控磁碟新增到 VM

您可以使用 PowerShell 來佈建 VM 及新增資料磁碟，或連結既存的 **.vhd** 檔案作為資料磁碟。

**Add-AzureRmVMDataDisk** Cmdlet 會將資料磁碟新增至虛擬機器。 您可以在建立虛擬機器時新增資料磁碟，也可以將資料磁碟新增到現有的虛擬機器。 指定 **VhdUri** 參數，將磁碟分散至不同的容器。

### <a name="add-data-disks-to-a-new-virtual-machine"></a>將資料磁碟新增到新的虛擬機器
下列範例使用 PowerShell 命令來建立具有三個資料磁碟的 VM，且每個放在不同的容器中。

第一個命令會建立虛擬機器物件，然後將它儲存在 $VirtualMachine 變數中。 此命令會指派虛擬機器的名稱和大小。

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                    -VMSize "Standard_A2"
```

接下來三個命令會將三個資料磁碟的路徑指派給 $DataDiskVhdUri01、$DataDiskVhdUri02 和 $DataDiskVhdUri03 變數。 在 URL 中定義不同的路徑名稱，將磁碟分散至不同的容器。

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

最後三個命令會將資料磁碟新增到 $VirtualMachine 中儲存的虛擬機器。 每個命令可指定磁碟的名稱、位置和其他屬性。 每個磁碟的 URI 會儲存在 $DataDiskVhdUri01、$DataDiskVhdUri02 和 $DataDiskVhdUri03 中。

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                -VhdUri $DataDiskVhdUri01 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                -VhdUri $DataDiskVhdUri02 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                -VhdUri $DataDiskVhdUri03 -CreateOption Empty
```

使用下列 PowerShell 命令將 OS 磁碟和網路組態新增至 VM，然後啟動新的 VM。

```powershell
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

# Create a network security group configuration
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

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                -Name "VirtualMachine"
```
接下來三個命令會將三個資料磁碟的路徑指派給 $DataDiskVhdUri01、 $DataDiskVhdUri02 和 $DataDiskVhdUri03 變數。  vhduri 中不同的路徑名稱表示放置磁碟的不同容器。
```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```
```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```
```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```


接下來三個命令會將資料磁碟新增到 $VirtualMachine 變數中儲存的虛擬機器。 每個命令可指定磁碟的名稱、位置和其他屬性。 每個磁碟的 URI 會儲存在 $DataDiskVhdUri01、$DataDiskVhdUri02 和 $DataDiskVhdUri03 中。

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                      -VhdUri $DataDiskVhdUri01 -LUN 0 `
                      -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                      -VhdUri $DataDiskVhdUri02 -LUN 1 `
                      -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                      -VhdUri $DataDiskVhdUri03 -LUN 2 `
                      -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
```

最後一個命令會更新 -ResourceGroupName 的 $VirtualMachine 中儲存之虛擬機器的狀態。

```powershell
Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Stack 虛擬機器，請參閱 [Azure Stack 中虛擬機器的考量](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations)。
