---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 3e9885466d422a0428311ed3013e2ab34341cd25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66391427"
---
暫時的 OS 磁碟是本機的虛擬機器 (VM) 儲存體上建立，並不會保存到遠端的 Azure 儲存體。 暫時的 OS 磁碟適用於無狀態工作負載，其中的應用程式可容忍的個別 VM 失敗，但關心更大規模的部署所花費的時間或時間來重新安裝映像個別的 VM 執行個體。 它也適合用於部署的應用程式，使用傳統部署模型中，移至 Resource Manager 部署模型。 使用暫時性 OS 磁碟時，您會發現 OS 磁碟的讀寫延遲較低，而重新安裝 VM 映像的速度較快。 此外，暫時的作業系統磁碟是免費，您需要支付在 OS 磁碟沒有儲存體費用。 
 
暫時磁碟的主要功能如下： 
- 它們可以搭配 Marketplace 映像和自訂映像。
- 您可以部署 VM 映像，但不超過 VM 快取的大小。
- 能夠快速重設，或其為原始的開機狀態的 Vm 重新安裝映像。  
- 較低的執行階段延遲類似於暫存磁碟。 
- 針對 OS 磁碟的任何成本。 
 
 
持續性和暫時性 OS 磁碟之間的主要差異：

|                             | 永續性的 OS 磁碟                          | 暫時性 OS 磁碟                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| OS 磁碟的大小限制      | 2 TiB                                                                                        | 快取大小的 VM 大小或 2TiB，取其較小的- [DS](../articles/virtual-machines/linux/sizes-general.md)， [ES](../articles/virtual-machines/linux/sizes-memory.md)， [M](../articles/virtual-machines/linux/sizes-memory.md)， [FS](../articles/virtual-machines/linux/sizes-compute.md)，和[GS](../articles/virtual-machines/linux/sizes-memory.md)              |
| 支援的 VM 大小          | 全部                                                                                          | DSv1、 DSv2、 DSv3、 Esv3、 Fs、 FsV2、 GS、 M                                               |
| 磁碟型別支援           | 受控和非受控 OS 磁碟                                                                | 僅限受控的 OS 磁碟                                                               |
| 區域支援              | 所有區域                                                                                  | 所有區域                              |
| 資料永續性            | OS 磁碟的資料轉遞到作業系統磁碟會儲存在 Azure 儲存體                                  | OS 磁碟寫入的資料會儲存到本機的 VM 儲存體，而不會保存到 Azure 儲存體。 |
| 停止並解除配置的狀態      | Vm 和擴展集執行個體可以停止並解除配置並從的停止取消配置狀態重新啟動 | Vm 和擴展集執行個體不能停止並解除配置                                  |
| 特製化的 OS 磁碟支援 | 是                                                                                          | 否                                                                                 |
| OS 磁碟大小調整              | 支援的 VM 建立期間和之後停止並解除配置 VM                                | 只有在建立 VM 時支援                                                  |
| 新的 VM 大小調整大小   | OS 磁碟的資料會保留                                                                    | OS 磁碟上的資料會遭到刪除，OS 會重新佈建                                      |

## <a name="scale-set-deployment"></a>擴展集部署  
建立會使用暫時性 OS 磁碟的擴展集的程序是以新增`diffDiskSettings`屬性設`Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile`範本中的資源類型。 此外，快取原則必須設為`ReadOnly`的暫時性 OS 磁碟。 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-deployment"></a>VM 部署 
您可以使用暫時性 OS 磁碟使用範本來部署 VM。 建立使用暫時性 OS 磁碟的 VM 的程序是新增`diffDiskSettings`範本中的 microsoft.compute/virtualmachines 資源類型的屬性。 此外，快取原則必須設為`ReadOnly`的暫時性 OS 磁碟。 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>使用 REST 將 VM 重新安裝映像
目前，若要重新安裝映像使用暫時性 OS 磁碟的虛擬機器執行個體的唯一方法是透過使用 REST API。 擴展集，重新安裝映像已可透過 Powershell、 CLI 和入口網站。

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>常見問題集

**问：本機 OS 磁碟大小為何？**

答：預覽版本中，我們將支援的平台和/或 VM 的快取大小，會本機虛擬機器相同的節點上的所有讀取/寫入的 OS 磁碟的映像。 

**问：暫時性 OS 磁碟所能調整？**

答：否，一旦暫時性 OS 磁碟已佈建，則無法調整大小的 OS 磁碟。 

**问：我是否可以將受控的磁碟附加至暫時的 VM？**

答：是，您也可以使用暫時性 OS 磁碟的 VM，以附加受控的資料磁碟。 

**问：將所有 VM 大小都支援暫時性 OS 磁碟嗎？**

答：否，所有 Premium 儲存體 VM 大小都所支援 （DS、 ES、 FS、 GS 及 M） 除了 B 系列中，N 系列和 H 系列大小。  
 
**问：暫時性 OS 磁碟可套用至現有的 Vm 和擴展集？**

答：否，暫時的 OS 磁碟只會用於 VM 及調整集建立。 

**问：您可以混合使用擴展集內的暫時和一般 OS 磁碟嗎？**

答：否，您不能有混合的暫時性和持續性 OS 磁碟執行個體相同的擴展集內。 

**问：可以暫時性 OS 磁碟建立使用 Powershell 或 CLI？**

答：是，您可以使用暫時性 OS 磁碟，使用 REST、 範本、 PowerShell 和 CLI 來建立 Vm。

**问：使用暫時性 OS 磁碟不支援哪些功能？**

答：暫時的磁碟不支援：
- 擷取 VM 映像
- 磁碟快照集 
- Azure 磁碟加密 
- Azure 備份
- Azure Site Recovery  
- OS 磁碟交換 
