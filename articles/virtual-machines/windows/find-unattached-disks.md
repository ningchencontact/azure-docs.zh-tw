---
title: "尋找及刪除未連接的 Azure 受控和非受控磁碟 | Microsoft Docs"
description: "如何使用 Azure PowerShell 尋找及刪除未連接的 Azure 受控和非受控 (VHD/分頁 Blob) 磁碟。"
services: virtual-machines-windows
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: a846d3578d40b19762f185381c92bdf8e225b185
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>尋找及刪除未連接的 Azure 受控和非受控磁碟
當您在 Azure 中刪除虛擬機器時，預設不會刪除連接至它的磁碟。 它會防止由於不小心刪除虛擬機器而造成的資料遺失，但是您會繼續支付不必要的未連接磁碟。 使用本文以尋找及刪除所有未連接的磁碟並節省成本。 


## <a name="find-and-delete-unattached-managed-disks"></a>尋找及刪除未連接的受控磁碟 

下列指令碼會示範如何使用 *ManagedBy* 屬性來尋找未連接的[受控磁碟](managed-disks-overview.md)。 它會針對訂用帳戶中的所有受控磁碟迴圈執行，並且檢查 *ManagedBy* 屬性是否為 Null 以尋找未連接的受控磁碟。 *ManagedBy* 屬性會將虛擬機器的資源 ID 儲存至已連接的受控磁碟。

我們強烈建議您第一次執行指令碼時將 *deleteUnattachedDisks* 變數設為 0，以檢視所有未連接的磁碟。 在檢閱未連接的磁碟之後，將 *deleteUnattachedDisks* 設為 1 來執行指令碼，以刪除所有未連接的磁碟。

```azurepowershell-interactive

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0

$managedDisks = Get-AzureRmDisk

foreach ($md in $managedDisks) {
    
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){

        if($deleteUnattachedDisks -eq 1){
            
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"

            $md | Remove-AzureRmDisk -Force

            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "

        }else{

            $md.Id

        }
           
    }
     
 } 
```
## <a name="find-and-delete-unattached-unmanaged-disks"></a>尋找及刪除未連接的非受控磁碟 

非受控磁碟是在 [Azure 儲存體帳戶](../../storage/common/storage-create-storage-account.md)中儲存為 [分頁 Blob] (/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) 的 VHD 檔。 下列指令碼會示範如何使用 *LeaseStatus* 屬性來尋找未連接的非受控磁碟 (分頁 Blob)。 它會針對訂用帳戶之所有儲存體帳戶中的所有非受控磁碟迴圈執行，並且檢查 *LeaseStatus* 屬性是否解除鎖定，以尋找未連接的非受控磁碟。 如果非受控磁碟連接至虛擬機器，則 *LeaseStatus* 屬性會設為鎖定。

我們強烈建議您第一次執行指令碼時將 *deleteUnattachedVHDs* 變數設為 0，以檢視所有未連接的磁碟。 在檢閱未連接的磁碟之後，將 *deleteUnattachedVHDs* 設為 1 來執行指令碼，以刪除所有未連接的磁碟。


```azurepowershell-interactive
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=1

$storageAccounts = Get-AzureRmStorageAccount

foreach($storageAccount in $storageAccounts){

    $storageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value

    $context = New-AzureStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey

    $containers = Get-AzureStorageContainer -Context $context

    foreach($container in $containers){

        $blobs = Get-AzureStorageBlob -Container $container.Name -Context $context

        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
        
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
              
                  if($deleteUnattachedVHDs -eq 1){

                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"

                        $_ | Remove-AzureStorageBlob -Force

                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                  }
                  else{

                        $_.ICloudBlob.Uri.AbsoluteUri

                  }

            }
        
        }

    }

}

```

## <a name="next-steps"></a>後續步驟

[刪除儲存體帳戶](../../storage/common/storage-create-storage-account.md)




