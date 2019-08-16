---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: db8147717e825d9cc48b7f0704dc5eea0be223a9
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510346"
---
# <a name="using-azure-ultra-disks"></a>使用 Azure ultra 磁片

Azure ultra 磁片提供高輸送量、高 IOPS 以及一致的低延遲磁片儲存體, 適用于 Azure IaaS 虛擬機器 (Vm)。 這個新的供應項目可提供絕佳的效能，同時保有我們現有磁碟供應項目的相同可用性層級。 Ultra 磁片的一個主要優點是能夠以動態方式變更 SSD 的效能和您的工作負載, 而不需要重新開機您的 Vm。 Ultra 磁片適用于資料密集的工作負載, 例如 SAP Hana、最上層資料庫, 以及高交易量的工作負載。

## <a name="check-if-your-subscription-has-access"></a>檢查您的訂用帳戶是否有存取權

如果您已註冊 ultra 磁片, 而您想要檢查是否已為 ultra 磁片啟用您的訂用帳戶, 請使用下列其中一個命令: 

CLI：`az feature show --namespace Microsoft.Compute --name UltraSSD`

PowerShell：`Get-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName UltraSSD`

如果您的訂用帳戶已啟用, 則您的輸出應該看起來像這樣:

```bash
{
  "id": "/subscriptions/<yoursubID>/providers/Microsoft.Features/providers/Microsoft.Compute/features/UltraSSD",
  "name": "Microsoft.Compute/UltraSSD",
  "properties": {
    "state": "Registered"
  },
  "type": "Microsoft.Features/providers/features"
}
```

## <a name="determine-your-availability-zone"></a>判斷您的可用性區域

一經核准, 您就必須判斷您所在的可用性區域, 才能使用 ultra 磁片。 執行下列其中一個命令, 以判斷要將您的 ultra 磁片部署到哪個區域, 請務必先取代**region**、 **vmSize**和**訂**用帳戶值:

CLI：

```bash
$subscription = "<yourSubID>"
$region = "<yourLocation>, example value is southeastasia"
$vmSize = "<yourVMSize>, example value is Standard_E64s_v3"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

PowerShell：

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

回應會類似下圖, 其中 X 是要用於在您選擇的區域中部署的區域。 X 可能是 1、2 或 3。 目前只有三個區域支援 ultra 磁片, 它們是:美國東部2、東南亞和北歐。

保留 [**區域**] 值, 它代表您的可用性區域, 而您將需要它來部署 Ultra 磁片。

|ResourceType  |Name  |Location  |區域  |限制  |功能  |值  |
|---------|---------|---------|---------|---------|---------|---------|
|磁碟     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> 如果命令沒有回應, 則您對功能的註冊可能仍在擱置中, 或者您使用的是舊版的 CLI 或 PowerShell。

既然您已經知道要部署的區域, 請遵循本文中的部署步驟, 部署已連接 ultra 磁片的 VM, 或將 ultra 磁片連結至現有的 VM。

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>使用 Azure Resource Manager 部署 ultra 磁片

首先, 決定要部署的 VM 大小。 目前, 只有 DsV3 和 EsV3 VM 系列支援 ultra 磁片。 如需有關這些 VM 大小的其他詳細資訊，請參閱此[部落格](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) \(英文\) 的第二個表格。

如果您想要建立具有多個 ultra 磁片的 VM, 請參閱[使用多個 ultra 磁片建立 vm](https://aka.ms/UltraSSDTemplate)的範例。

如果您想要使用自己的範本, 請確定`Microsoft.Compute/virtualMachines`和`Microsoft.Compute/Disks`的**apiVersion**設定為`2018-06-01` (或更新版本)。

將磁片 sku 設定為**UltraSSD_LRS**, 然後設定磁片容量、IOPS、可用性區域和輸送量 (以 MBps 為單位), 以建立 ultra 磁片。

一旦佈建 VM，您就可以將資料磁碟分割和格式化，並針對你的工作負載設定它們。

## <a name="deploy-an-ultra-disk-using-cli"></a>使用 CLI 部署 ultra 磁片

首先, 決定要部署的 VM 大小。 目前, 只有 DsV3 和 EsV3 VM 系列支援 ultra 磁片。 如需有關這些 VM 大小的其他詳細資訊，請參閱此[部落格](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) \(英文\) 的第二個表格。

您必須建立能夠使用 ultra 磁片的 VM, 才能連接 ultra 磁片。

以您自己的值取代或設定 **$vmname**、 **$rgname**、 **$diskname**、 **$location**、 **$password**、 **$user**變數。 將 **$zone**設定為您從本文[開頭](#determine-your-availability-zone)處獲得的可用性區域值。 然後執行下列 CLI 命令, 以建立已啟用 ultra 的 VM:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-disk-using-cli"></a>使用 CLI 建立 ultra 磁片

現在您已有能夠連接 ultra 磁片的 VM, 您可以建立 ultra 磁片並將其連結至該虛擬機器。

```azurecli-interactive
$location="eastus2"
$subscription="xxx"
$rgname="ultraRG"
$diskname="ssd1"
$vmname="ultravm1"
$zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>使用 CLI 將 ultra 磁片連結至 VM

或者, 如果您現有的 VM 位於能夠使用 ultra 磁片的區域/可用性區域中, 您可以使用 ultra 磁片, 而不需要建立新的 VM。

```bash
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>使用 CLI 調整 ultra 磁片的效能

Ultra 磁片提供獨特的功能, 可讓您調整其效能, 下列命令會描述如何使用這項功能:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>使用 PowerShell 部署 ultra 磁片

首先, 決定要部署的 VM 大小。 目前, 只有 DsV3 和 EsV3 VM 系列支援 ultra 磁片。 如需有關這些 VM 大小的其他詳細資訊，請參閱此[部落格](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) \(英文\) 的第二個表格。

若要使用 ultra 磁片, 您必須建立能夠使用 ultra 磁片的 VM。 以您自己的值取代或設定 **$resourcegroup**和 **$vmName**變數。 將 **$zone**設定為您從本文[開頭](#determine-your-availability-zone)處獲得的可用性區域值。 然後執行下列[update-azvm](/powershell/module/az.compute/new-azvm)命令, 以建立已啟用 ULTRA 的 VM:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-an-ultra-disk-using-powershell"></a>使用 PowerShell 建立 ultra 磁片

現在您有一個能夠使用 ultra 磁片的 VM, 您可以建立一個 ultra 磁片並將其連接到它:

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>使用 PowerShell 將 ultra 磁片連結至 VM

或者, 如果您現有的 VM 位於能夠使用 ultra 磁片的區域/可用性區域中, 您可以使用 ultra 磁片, 而不需要建立新的 VM。

```powershell
# add disk to VM
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Login-AzureRMAccount -SubscriptionId $subscription
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>使用 PowerShell 調整 ultra 磁片的效能

Ultra 磁片具有獨特的功能, 可讓您調整其效能, 下列命令是在不需要卸離磁片的情況下調整效能的範例:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>後續步驟

如果您想要嘗試新的磁片類型, 請[使用這份問卷要求存取權](https://aka.ms/UltraDiskSignup)。