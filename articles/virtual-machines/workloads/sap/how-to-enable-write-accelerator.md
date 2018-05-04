---
title: 適用於 SAP 部署的 Azure 寫入加速器 | Microsoft Docs
description: 部署在 Azure 虛擬機器上之 SAP HANA 系統的操作指南。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 89a4216a3893892eedd6c216c7e0e5d51cf64749
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2018
---
# <a name="azure-write-accelerator-for-sap-deployments"></a>適用於 SAP 部署的 Azure 寫入加速器
Azure 寫入加速器是專門為 M 系列 VM 推出的功能。 除了 M 系列，Azure 寫入加速器不適用於 Azure 中的任何其他 VM 系列。 如同名稱所示，這個功能的目的是改善針對 Azure 進階儲存體之寫入的 I/O 延遲。 

在以下地區可取得 M 系列部署的 Azure 寫入加速器公開預覽功能：

- 美國西部 2
- 美國東部 2
- 西歐
- 東南亞

## <a name="planning-for-using-azure-write-accelerator"></a>規劃使用 Azure 寫入加速器
Azure 寫入加速器應該用於包含 DBMS 之交易記錄或重做記錄的磁碟區。 不建議將 Azure 寫入加速器用在 DBMS 的資料磁碟區。 有此限制的原因是，Azure 寫入加速器會要求裝載 Azure 進階儲存體 VHD，但不進行進階儲存體可使用的額外讀取快取。 此類型快取的優勢可在傳統資料庫中更加明顯地觀察到。 由於寫入加速器只會影響寫入活動，但不會加快讀取速度，所以針對 SAP 所支援的設計是將寫入加速器用於 SAP 所支援資料庫的交易記錄或重做記錄磁碟機。 

Azure 寫入加速器只能與 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)搭配運作。 因此，您必須依據此前提進行規劃。 

>[!NOTE]
> 由於 Azure 寫入加速器的功能仍處於公開預覽狀態，因此目前還沒有可支援其功能的生產案例部署。

Azure 寫入加速器可以支援之每個虛擬機器的 Azure 進階儲存體 VHD 有其上限。 目前的上限是：


| VM SKU | 寫入加速器磁碟的數目 | 每個 VM 的寫入加速器 IOPS |
| --- | --- | --- |
| M128ms | 16 | 8000 |
| M128s | 16 | 8000 |
| M64ms | 8 | 4000 |
| M64s | 8 | 4000 | 



> [!IMPORTANT]
> 如果您想要為現有磁碟區啟用或停用 Azure 寫入加速器，而此磁碟區是使用 Windows 磁碟或磁碟區管理員、Windows 儲存空間、Windows 相應放大檔案伺服器 (SOFS)、Linux LVM 或 MDADM 從多個 Azure 進階儲存體磁碟建置並等量，則建置了磁碟區的所有磁碟必須分開使用不同的步驟啟用或停用寫入加速器。 **啟用或停用此種設定的寫入加速器之前，請先關閉 Azure VM**。 


> [!IMPORTANT]
> 若要啟用現有 Azure 磁碟的 Azure 寫入加速器，且此磁碟「不」屬於使用 Windows 磁碟或磁碟區管理員、Windows 儲存空間、Windows 相應放大檔案伺服器 (SOFS)、Linux LVM 或 MDADM 從多個磁碟建置的磁碟區，則必須關閉存取此 Azure 磁碟的工作負載。 「必須」關閉使用 Azure 磁碟的資料庫應用程式。

> [!IMPORTANT]
> 為 VM 的 Azure VM 作業系統磁碟啟用寫入加速器時，會重新啟動此 VM。 

針對 SAP 相關的 VM 設定，應該沒有必要為作業磁碟啟用 Azure 寫入加速器。

### <a name="restrictions-when-using-azure-write-accelerator"></a>使用 Azure 寫入加速器時的限制
針對 Azure 磁碟/VHD 使用 Azure 寫入加速器時適用以下限制：

- 必須將進階磁碟快取設定為「無」或「唯讀」。 不支援所有其他的快取模式。
- 尚不支援已啟用寫入加速器之磁碟的快照集。 這項限制會封鎖「Azure 備份服務」對虛擬機器的所有磁碟執行應用程式一致快照的能力。
- 只有較小的 I/O 大小會採用加速路徑。 在大量載入資料或在不同 DBMS 的交易記錄緩衝區保存到儲存體之前就達到較大填滿程度的工作負載情況下，寫入磁碟的 I/O 可能不會採用加速路徑。


## <a name="enabling-write-accelerator-on-a-specific-disk"></a>在特定磁碟上啟用寫入加速器
接下來的幾節將說明如何在 Azure 進階儲存體 VHD 上啟用 Azure 寫入加速器。

現階段，透過 Azure Rest API 和 PowerShell 是啟用寫入加速器的唯一方法。 其他可在 Azure 入口網站中支援的方法，會在接下來幾週按預定時程推出。

### <a name="prerequisites"></a>先決條件
以下必要條件適用於本文撰寫時的 Azure 寫入加速器使用：

- 您用來部署 VM 和 VM 儲存體的訂用帳戶識別碼必須列入白名單。 請連絡您的 Microsoft CSA、GBB 或客戶經理，將您的訂用帳戶識別碼列入白名單。 
- 您想要套用 Azure 寫入加速器的磁碟必須為 [Azure 受控磁碟](https://azure.microsoft.com/services/managed-disks/)。

### <a name="enabling-through-power-shell"></a>透過 PowerShell 啟用
5.5.0 版之後的 Azure PowerShell 模組皆已引進相關 Cmdlet 的變更，可以為特定 Azure 進階儲存體磁碟啟用或停用 Azure 寫入加速器。
為了要啟用或部署寫入加速器所支援的磁碟，下列 PowerShell 命令已經過變更，並擴大為接受寫入加速器的參數。

下列 Cmdlet 加入了新的開關參數 "WriteAccelerator"： 

- Set-AzureRmVMOsDisk
- Add-AzureRmVMDataDisk
- Set-AzureRmVMDataDisk
- Add-AzureRmVmssDataDisk

未提供參數等於將屬性設為 false，而且會部署沒有 Azure 寫入加速器支援的磁碟。

下列 Cmdlet 加入了新的開關參數 "OsDiskWriteAccelerator"： 

- Set-AzureRmVmssStorageProfile

未提供參數等於將屬性設為 false，而且會提供不利用 Azure 寫入加速器的磁碟。

下列 Cmdlet 加入了新的選擇性布林值 (不可使用 null 值) 參數 "OsDiskWriteAccelerator"： 

- Update-AzureRmVM
- Update-AzureRmVmss

指定 $true 或 $false 來控制磁碟的 Azure 寫入加速器支援。

命令範例如下︰

```

New-AzureRmVMConfig | Set-AzureRmVMOsDisk | Add-AzureRmVMDataDisk -Name "datadisk1" | Add-AzureRmVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVM

Get-AzureRmVM | Update-AzureRmVM -OsDiskWriteAccelerator $true

New-AzureRmVmssConfig | Set-AzureRmVmssStorageProfile -OsDiskWriteAccelerator | Add-AzureRmVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzureRmVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVmss

Get-AzureRmVmss | Update-AzureRmVmss -OsDiskWriteAccelerator:$false 

```

後面幾節有兩個主要案例的指令碼。

#### <a name="adding--new-disk-supported-by-azure-write-accelerator"></a>新增 Azure 寫入加速器所支援的新磁碟
您可以使用這個指令碼將新的磁碟新增至您的 VM。 使用此指令碼所建立的磁碟將會使用 Azure 寫入加速器。

```

# Specify your VM Name
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzureRmVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```
您需要根據您特有部署的磁碟調整 VM、磁碟、資源群組的名稱、磁碟大小和 LunID。


#### <a name="enabling-azure-write-accelerator-on-an-existing-azure-disk"></a>在現有 Azure 磁碟上啟用 Azure 寫入加速器
如果您需要在現有磁碟上啟用寫入加速器，可以使用以下指令碼來執行這個工作：

```

#Specify your VM Name
$vmName="mysapVM"
#Specify your Resource Group
$rgName = "mysap"
#data disk name
$datadiskname = "testsap-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```

您需要調整 VM、磁碟和資源群組的名稱。 在上述指令碼中，$newstatus 的值設定為 '$true' 會將寫入加速器新增至現有磁碟。 若使用 '$false' 值，則會停用指定磁碟的寫入加速器。

> [!Note]
> 執行上述指令碼會將指定的磁碟中斷連結，對磁碟啟用寫入加速器，然後再重新連結磁碟




### <a name="enabling-through-rest-apis"></a>透過 Rest API 啟用
若要透過 Azure Rest API 部署，您需要安裝 Azure ARMClient

#### <a name="install-armclient"></a>安裝 ARMClient

若要執行 ARMClient，您需要透過 Chocolatey 安裝它。 您可以使用 cmd.exe 或 PowerShell 來安裝它。 以提高的權限使用這些命令 (「以系統管理員身分執行」)。

使用 cmd.exe 執行下列命令：

```
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

若使用 PowerShell，則必須使用：

```
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

現在您可以使用下列命令，以 cmd.exe 或 PowerShell 安裝 ARMClient

```
choco install armclient
```

#### <a name="getting-your-current-vm-configuration"></a>取得目前的 VM 設定
為了變更磁碟設定的屬性，您首先要取得 JSON 檔案中的目前設定。 執行下列命令可以取得目前的設定：

```
armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>
```
將 '<<   >>' 括住的字詞取代為您的資料，包括 JSON 檔案應有的檔案名稱。

輸出看起來像這樣：

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

下一個步驟是更新 JSON 檔案，並在名為 'log1' 的磁碟上啟用寫入加速器。 將這個屬性新增至 JSON 檔案中磁碟的快取項目之後，即可完成此步驟。 

```
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

然後使用此命令更新現有的部署：

```
armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>

```

輸出看起來應該會像下面這樣。 您可以看到有一個磁碟已啟用寫入加速器。

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

從改變的觀點來說，寫入加速器應該要支援磁碟機。

 