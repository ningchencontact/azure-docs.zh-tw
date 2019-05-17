---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 495326c172f900dc8bcff78b0df38f2cb64ed27e
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546528"
---
# <a name="enable-and-deploy-azure-ultra-ssds-preview"></a>啟用並部署 Azure 的強力 Ssd （預覽）

Azure 的強力索引固態硬碟 (SSD) （預覽） 供應項目高輸送量、 高 IOPS，以及一致的低延遲磁碟儲存體的 Azure IaaS 虛擬機器 (Vm)。 這個新的供應項目可提供絕佳的效能，同時保有我們現有磁碟供應項目的相同可用性層級。 強力的 Ssd 的一個主要優點是能夠以動態方式變更您的工作負載，而不需要重新啟動您的 Vm 以及 SSD 的效能。 強力的 Ssd 適用於資料密集的工作負載，例如 SAP HANA、 最上層資料庫和交易為主的工作負載。

目前，強力的 Ssd 處於預覽階段，您必須[註冊](https://aka.ms/UltraSSDPreviewSignUp)預覽版才能存取它們。

## <a name="determine-your-availability-zone"></a>判斷您的可用性區域

一旦核准之後，您需要判斷哪一個可用性區域，您會在中，才能使用強力的 Ssd。 執行下列命令，以判斷哪一個區域，在美國東部 2 來部署您強力的磁碟，以其中一種方法：

PowerShell：`Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

CLI：`az vm list-skus --resource-type disks --query “[?name==UltraSSD_LRS]”`

回應將類似下面的表單，其中 X 是要用於部署在美國東部 2 區域。 X 可能是 1、2 或 3。

保留**區域**值，它代表您的可用性區域，且您需要它才能部署強力的 SSD。

|ResourceType  |名稱  |位置  |區域  |限制  |功能  |Value  |
|---------|---------|---------|---------|---------|---------|---------|
|磁碟     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

如果從命令中，沒有回應，則您的註冊功能仍為暫止，或核准尚未。

既然您已經知道要部署至哪一個區域，請依照此文章中的部署步驟，使用 Ultra SSD 部署您的第一部 VM。

## <a name="deploy-an-ultra-ssd-using-azure-resource-manager"></a>部署使用 Azure Resource Manager 的強力 SSD

首先，判斷要部署的 VM 大小。 在此預覽版本中，只支援 DsV3 與 EsV3 VM 系列。 如需有關這些 VM 大小的其他詳細資訊，請參閱此[部落格](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) \(英文\) 的第二個表格。

如果您想要建立具有多個超級 Ssd 的 VM，請參閱範例[建立具有多個超級 SSD 的 VM](https://aka.ms/UltraSSDTemplate)。

如果您想要使用您自己的範本，請確定**apiVersion** for`Microsoft.Compute/virtualMachines`並`Microsoft.Compute/Disks`已設為`2018-06-01`（或更新版本）。

設定磁碟 sku **UltraSSD_LRS**，然後設定以 mbps 為單位建立超級磁碟的磁碟容量、 IOPS、 可用性區域與輸送量。

一旦佈建 VM，您就可以將資料磁碟分割和格式化，並針對你的工作負載設定它們。

## <a name="deploy-an-ultra-ssd-using-cli"></a>部署使用 CLI 的強力 SSD

首先，判斷要部署的 VM 大小。 在此預覽版本中，只支援 DsV3 與 EsV3 VM 系列。 如需有關這些 VM 大小的其他詳細資訊，請參閱此[部落格](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) \(英文\) 的第二個表格。

若要使用強力的 Ssd，您必須建立能夠使用強力的 Ssd 的 VM。

取代或設定 **$vmname**， **$rgname**， **$diskname**， **$location**， **$password**， **$user**變數，以您自己的值。 設定 **$zone**您所得的可用性區域的值[這篇文章的啟動](#determine-your-availability-zone)。 然後執行下列 CLI 命令建立的強力的啟用的 VM:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled --zone $zone --authentication-type password --admin-password $password --admin-username $user --attach-data-disks $diskname --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-ssd-using-cli"></a>建立使用 CLI 的強力 SSD

您現在已能夠使用強力的 Ssd 的 VM，您可以建立並附加至這個強力的 SSD。

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an Ultra SSD disk
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

### <a name="adjust-the-performance-of-an-ultra-ssd-using-cli"></a>調整使用 CLI 的強力 SSD 的效能

強力的 Ssd 提供獨特的功能，可讓您調整其效能，下列命令說明如何使用這項功能：

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-ssd-using-powershell"></a>部署使用 PowerShell 的超級 SSD

首先，判斷要部署的 VM 大小。 在此預覽版本中，只支援 DsV3 與 EsV3 VM 系列。 如需有關這些 VM 大小的其他詳細資訊，請參閱此[部落格](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) \(英文\) 的第二個表格。

若要使用強力的 Ssd，您必須建立能夠使用強力的 Ssd 的 VM。 取代或設定 **$resourcegroup**並 **$vmName**變數，以您自己的值。 設定 **$zone**您所得的可用性區域的值[這篇文章的啟動](#determine-your-availability-zone)。 然後執行下列[New-azvm](/powershell/module/az.compute/new-azvm)命令來建立超級啟用 VM:

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

### <a name="create-an-ultra-ssd-using-powershell"></a>建立使用 PowerShell 的超級 SSD

您現在已能夠使用強力的 Ssd 的 VM，您可以建立並附加至這個強力的 SSD:

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

### <a name="adjust-the-performance-of-an-ultra-ssd-using-powershell"></a>調整使用 PowerShell 的超級 SSD 的效能

強力的 Ssd 具有獨特的功能，可讓您調整其效能，下列命令是調整效能，而不需要中斷連接磁碟的範例：

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>後續步驟

如果您想要嘗試新的磁碟類型[要求的存取權與這份問卷預覽](https://aka.ms/UltraSSDPreviewSignUp)。