---
title: "尋找及刪除未連結的 Azure 受控和非受控磁碟 | Microsoft Docs"
description: "如何使用 Azure CLI 尋找及刪除未連結的 Azure 受控和非受控 (VHD/分頁 Blob) 磁碟"
services: virtual-machines-linux
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: 9ada768cd4128b9dd6949b5a96c557496c6bb11c
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>尋找及刪除未連結的 Azure 受控和非受控磁碟
當您在 Azure 中刪除虛擬機器時，預設不會刪除其連結的磁碟。 這會防止由於不小心刪除虛擬機器而造成的資料遺失，但是您會繼續為不必要的未連結磁碟支付費用。 使用本文以尋找及刪除所有未連結的磁碟並節省成本。 


## <a name="find-and-delete-unattached-managed-disks"></a>尋找及刪除未連結的受控磁碟 

下列指令碼會示範如何使用 ManagedBy 屬性來尋找未連結的受控磁碟。  它會針對訂用帳戶中的所有受控磁碟重複執行，並且檢查 ManagedBy 屬性是否為 Null 以尋找未連結的受控磁碟。 ManagedBy 屬性會儲存虛擬機器 (已連結受控磁碟) 的資源 ID。 

我們強烈建議您第一次執行指令碼時，將 deleteUnattachedDisks 變數設為 0，以檢視所有未連結的磁碟。 在檢閱未連結的磁碟之後，將 deleteUnattachedDisks 設為 1 來執行指令碼，以刪除所有未連結的磁碟。

 ```azurecli

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0

unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done

```
## <a name="find-and-delete-unattached-unmanaged-disks"></a>尋找及刪除未連結的非受控磁碟 

非受控磁碟是 VHD 檔案，會以[分頁 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) 的形式儲存在 [Azure 儲存體帳戶](../../storage/common/storage-create-storage-account.md)中。 下列指令碼會示範如何使用 LeaseStatus 屬性來尋找未連結的非受控磁碟 (分頁 Blob)。 它會針對訂用帳戶中所有儲存體帳戶的所有非受控磁碟重複執行，並且檢查 LeaseStatus 屬性是否解除鎖定，以尋找未連結的非受控磁碟。 如果非受控磁碟連結至虛擬機器，則 LeaseStatus 屬性會設為鎖定。 

我們強烈建議您第一次執行指令碼時，將 deleteUnattachedVHDs 變數設為 0，以檢視所有未連結的磁碟。 在檢閱未連結的磁碟之後，將 deleteUnattachedVHDs 設為 1 來執行指令碼，以刪除所有未連結的磁碟。


 ```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=1

storageAccountIds=$(az storage account list --query [].[id] -o tsv)

for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 

```

## <a name="next-steps"></a>後續步驟

[刪除儲存體帳戶](../../storage/common/storage-create-storage-account.md)



