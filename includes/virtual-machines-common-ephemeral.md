---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a57335eccfce1e81fe0cc85ae6fa7b12aa27e1c3
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805879"
---
暫時的 OS 磁碟是本機的虛擬機器 (VM) 儲存體上建立，並不會儲存到遠端的 Azure 儲存體。 暫時的 OS 磁碟搭配無狀態工作負載，其中應用程式都願意容忍個別的 VM 失敗，但會更受到 VM 部署時間，或重新安裝映像個別的 VM 執行個體。 使用暫時性 OS 磁碟時，您可以取得較低的讀取/寫入延遲，OS 磁碟並更快的 VM 重新安裝映像。 
 
暫時磁碟的主要功能如下： 
- 適用於無狀態應用程式。
- 它們可以搭配 Marketplace 和自訂映像。
- 能夠快速重設或重新安裝映像的 Vm 和擴展集執行個體的原始開機狀態。  
- 較低的延遲，類似於暫存磁碟。 
- 暫時的 OS 磁碟免費，您需要支付在 OS 磁碟沒有儲存體費用。
- 它們可用於所有 Azure 區域。 
- 藉由支援暫時性 OS 磁碟[共用映像庫](/azure/virtual-machines/linux/shared-image-galleries)。 
 

 
持續性和暫時性 OS 磁碟之間的主要差異：

|                             | 永續性的 OS 磁碟                          | 暫時性 OS 磁碟                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| OS 磁碟的大小限制      | 2 TiB                                                                                        | 快取大小的 VM 大小或 2TiB，取其較小。 針對**快取大小以 gib 為單位**，請參閱[DS](../articles/virtual-machines/linux/sizes-general.md)， [ES](../articles/virtual-machines/linux/sizes-memory.md)， [M](../articles/virtual-machines/linux/sizes-memory.md)， [FS](../articles/virtual-machines/linux/sizes-compute.md)，以及[GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| 支援的 VM 大小          | 全部                                                                                          | DSv1、 DSv2、 DSv3、 Esv3、 Fs、 FsV2、 GS、 M                                               |
| 磁碟型別支援           | 受控和非受控 OS 磁碟                                                                | 僅限受控的 OS 磁碟                                                               |
| 區域支援              | 所有區域                                                                                  | 所有區域                              |
| 資料永續性            | OS 磁碟的資料轉遞到作業系統磁碟會儲存在 Azure 儲存體                                  | OS 磁碟寫入的資料會儲存到本機的 VM 儲存體，而不會保存到 Azure 儲存體。 |
| 停止並解除配置的狀態      | Vm 和擴展集執行個體可以停止並解除配置並從的停止取消配置狀態重新啟動 | Vm 和擴展集執行個體不能停止並解除配置                                  |
| 特製化的 OS 磁碟支援 | 是                                                                                          | 否                                                                                 |
| OS 磁碟大小調整              | 支援的 VM 建立期間和之後停止並解除配置 VM                                | 只有在建立 VM 時支援                                                  |
| 新的 VM 大小調整大小   | OS 磁碟的資料會保留                                                                    | OS 磁碟上的資料會遭到刪除，OS 會重新佈建                                      |

## <a name="size-requirements"></a>大小需求

您可以部署 VM 和執行個體的映像，但不超過 VM 快取的大小。 例如，標準的 Windows Server 映像市集中的是大約是 127 GiB，也就是說，您必須有大於 127 GiB 的快取的 VM 大小。 在此情況下， [Standard_DS2_v2](/azure/virtual-machines/windows/sizes-general#dsv2-series)的快取大小為 86 GiB，這不是夠大。 Standard_DS2_v2 的快取大小為 172 GiB，這是夠大。 在此情況下，Standard_DS3_v2 是 DSv2 系列可供您使用此映像中的最小大小。 所表示的服務商場和 Windows Server 映像中的基本 Linux 映像`[smallsize]`通常大約 30 GiB，而且可以使用大部分可用的 VM 大小。

暫時的磁碟也會需要 VM 大小支援進階儲存體。 大小通常 （但不是一定） 有`s`名稱，例如 DSv2 和 EsV3 中。 如需詳細資訊，請參閱 < [Azure VM 大小](../articles/virtual-machines/linux/sizes.md)如周圍大小都支援進階儲存體的詳細資訊。

## <a name="powershell"></a>PowerShell

若要使用 PowerShell 的 VM 部署為暫時的磁碟，使用[組 AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) VM 組態中。 設定`-DiffDiskSetting`要`Local`並`-Caching`至`ReadOnly`。     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

擴展集部署，使用[組 AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile)組態中的 cmdlet。 設定`-DiffDiskSetting`要`Local`並`-Caching`至`ReadOnly`。


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

若要使用 CLI 的 VM 部署為暫時的磁碟，設定`--ephemeral-os-disk`中的參數[az vm 建立](/cli/azure/vm#az-vm-create)來`true`並`--os-disk-caching`參數來`ReadOnly`。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

擴展集，您可使用`--ephemeral-os-disk true`參數[az vmss 建立](/cli/azure/vmss#az-vmss-create)並將`--os-disk-caching`參數`ReadOnly`。

## <a name="portal"></a>入口網站   

在 Azure 入口網站中，您可以選擇開啟部署 VM 時，使用暫時的磁碟**進階**一節**磁碟** 索引標籤。針對**使用暫時性 OS 磁碟**選取**是**。

![顯示選擇使用暫時性 OS 磁碟的選項按鈕的螢幕擷取畫面](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

如果使用暫時磁碟的選項會呈現灰色，您可能會選取沒有快取大小大於 OS 映像，或不支援進階儲存體的 VM 大小。 請返回**基本概念**頁面上，然後再次嘗試選擇另一個 VM 大小。

您也可以使用暫時性 OS 磁碟上使用入口網站建立擴展集。 只要確定您選取 VM 大小夠大的快取大小，然後再於**使用暫時性 OS 磁碟**選取**是**。

![顯示選擇使用暫時性 OS 磁碟，擴展集的選項按鈕的螢幕擷取畫面](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>擴展集範本部署  
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

## <a name="vm-template-deployment"></a>VM 範本部署 
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

**問：本機 OS 磁碟大小為何？**

答：我們支援的平台和自訂映像，但不超過 VM 的快取大小，會與虛擬機器在相同節點上本機的所有讀取/寫入的 OS 磁碟。 

**問：暫時性 OS 磁碟所能調整？**

答：否，一旦暫時性 OS 磁碟已佈建，則無法調整大小的 OS 磁碟。 

**問：我是否可以將受控的磁碟附加至暫時的 VM？**

答：是，您也可以使用暫時性 OS 磁碟的 VM，以附加受控的資料磁碟。 

**問：將所有 VM 大小都支援暫時性 OS 磁碟嗎？**

答：否，所有 Premium 儲存體 VM 大小都所支援 （DS、 ES、 FS、 GS 及 M） 除了 B 系列中，N 系列和 H 系列大小。  
 
**問：暫時性 OS 磁碟可套用至現有的 Vm 和擴展集？**

答：否，暫時的 OS 磁碟只會用於 VM 及調整集建立。 

**問：您可以混合使用擴展集內的暫時和一般 OS 磁碟嗎？**

答：否，您不能有混合的暫時性和持續性 OS 磁碟執行個體相同的擴展集內。 

**問：可以暫時性 OS 磁碟建立使用 Powershell 或 CLI？**

答：是，您可以使用暫時性 OS 磁碟，使用 REST、 範本、 PowerShell 和 CLI 來建立 Vm。

**問：使用暫時性 OS 磁碟不支援哪些功能？**

答：暫時的磁碟不支援：
- 擷取 VM 映像
- 磁碟快照集 
- Azure 磁碟加密 
- Azure 備份
- Azure Site Recovery  
- OS 磁碟交換 
