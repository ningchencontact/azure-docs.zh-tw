---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e39f294f7902eabef401d4c8145f4f19a07f267f
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2019
ms.locfileid: "71224569"
---
# <a name="creating-an-incremental-snapshot-preview-for-managed-disks"></a>建立受控磁片的增量快照集（預覽）

增量快照集（預覽）是受控磁片的時間點備份，在這種情況下，只會包含自上一個快照集之後的所有變更。 當您嘗試下載或使用增量快照集時，會使用完整的 VHD。 這項適用于受控磁片快照集的新功能可能會讓它們更符合成本效益，因為您不再需要將整個磁片與每個個別的快照集一起儲存，除非您選擇。 就像一般快照集一樣，您可以使用增量快照集來建立完整的受控磁片，或建立一般的快照集。

增量快照集和一般快照集之間有一些差異。 增量快照集會一律使用標準 Hdd 存放裝置，而不考慮磁片的儲存體類型，而一般快照集可以使用 premium Ssd。 如果您在進階儲存體上使用一般快照集來相應增加 VM 部署，建議您在[共用映射資源庫](../articles/virtual-machines/linux/shared-image-galleries.md)中的標準儲存體上使用自訂映射。 它可協助您以較低的成本達成更大規模的規模。 此外，增量快照集可能會透過[區域冗余儲存體](../articles/storage/common/storage-redundancy-zrs.md)（ZRS）提供更好的可靠性。 如果選取的區域中有可用的 ZRS，則增量快照集會自動使用 ZRS。 如果 ZRS 無法在區域中使用，則快照集會預設為[本機多餘的儲存體](../articles/storage/common/storage-redundancy-lrs.md)（LRS）。 您可以覆寫此行為，並手動選取一個，但我們不建議這麼做。

增量快照集也提供差異功能，這是受控磁片的唯一可用性。 它們可讓您將相同受控磁片的兩個增量快照集之間的變更，減少到區塊層級。 您可以使用這項功能來減少跨區域複製快照集時的資料使用量。

如果您尚未註冊預覽版，而您想要開始使用增量快照集，請傳送電子郵件給我們AzureDisks@microsoft.com ，以取得公開預覽的存取權。

## <a name="restrictions"></a>限制

- 當您變更磁片的大小時，目前無法建立增量快照集。
- 目前無法在訂用帳戶之間移動增量快照集。
- 您目前最多隻能在任何指定的時間產生特定快照集系列的五個快照集的 SAS Uri。
- 您無法為該磁片的訂用帳戶以外的特定磁片建立增量快照集。
- 每個磁片最多可以建立7個增量快照集每五分鐘一次。
- 總共可以針對單一磁片建立總計200的增量快照集。

## <a name="powershell"></a>PowerShell

您可以使用 Azure PowerShell 來建立增量快照集。 您可以在本機上安裝最新版的 PowerShell。 您將需要最新版的 Azure PowerShell，下列命令會安裝它，或將現有的安裝更新為最新版本：

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

一旦安裝之後，請使用`az login`登入您的 PowerShell 會話。

將`<yourDiskNameHere>`、 `<yourResourceGroupNameHere>`和`<yourDesiredSnapShotNameHere>`取代為您的值，然後您可以使用下列腳本來建立增量快照集：

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting:
# 1. Incremental property
# 2. SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 

# You can identify incremental snapshots of the same disk by using the SourceResourceId and SourceUniqueId properties of snapshots. 
# SourceResourceId is the Azure Resource Manager resource ID of the parent disk. 
# SourceUniqueId is the value inherited from the UniqueId property of the disk. If you delete a disk and then create a disk with the same name, the value of the UniqueId property will change. 
# Following script shows how to get all the incremental snapshots in a resource group of same disk
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>Resource Manager 範本

您也可以使用 Azure Resource Manager 範本來建立增量快照集。 您必須確定 apiVersion 設定為**2019-03-01** ，而且累加屬性也設定為 true。 下列程式碼片段是如何使用 Resource Manager 範本建立增量快照集的範例：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="cli"></a>CLI

您可以使用[az snapshot create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create)，以 Azure CLI 建立增量快照集。 範例命令看起來會像下面這樣：

```bash
az snapshot create -g <exampleResourceGroup> \
-n <exampleSnapshotName> \
-l <exampleLocation> \
--source <exampleVMId> \
--incremental
```

您也可以使用`--query` [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-show)上的參數，在 CLI 中識別快照集的增量快照集。 您可以使用該參數直接查詢快照集的**SourceResourceId**和**SourceUniqueId**屬性。 SourceResourceId 是父磁片的 Azure Resource Manager 資源識別碼。 **SourceUniqueId**是從磁片的**UniqueId**屬性繼承而來的值。 如果您刪除磁片，然後使用相同的名稱建立磁片，則**UniqueId**屬性的值將會變更。

其中一個查詢的範例如下所示：

```bash
az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceResourceId] -o tsv

az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceUniqueId] -o tsv
```

## <a name="next-steps"></a>後續步驟

如果您尚未註冊預覽版，而您想要開始使用增量快照集，請傳送電子郵件給我們AzureDisks@microsoft.com ，以取得公開預覽的存取權。
