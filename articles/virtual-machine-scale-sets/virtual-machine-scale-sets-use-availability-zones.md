---
title: "建立 Azure 延展集，使用可用性區域 （預覽） |Microsoft 文件"
description: "了解如何建立可用性區域用於免於中斷提升備援的 Azure 虛擬機器規模集"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: iainfou
ms.openlocfilehash: 310fdc68d3eb662906053d2bc0c45e6cfa18d4da
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>建立使用可用性區域 （預覽） 的虛擬機器規模集
若要保護您的虛擬機器擴展集從資料中心層級的失敗，您可以建立在擴展集可用性區域中。 支援可用性區域的 azure 區域至少有三個不同的區域，每個都有自己獨立的來源、 網路和冷卻的電源。 如需詳細資訊，請參閱[可用性區域概觀](../availability-zones/az-overview.md)。

若要使用可用性區域，必須建立規模集在[支援 Azure 地區](../availability-zones/az-overview.md#regions-that-support-availability-zones)。 您可以建立下列方法之一使用可用性區域的小數位數組：

- [Azure 入口網站](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Azure 資源管理員範本](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>使用 Azure 入口網站
會建立使用可用性區域的小數位數集的程序中所述相同[使用者入門文件](virtual-machine-scale-sets-create-portal.md)。 當您選取支援的 Azure 區域時，您可以建立其中一個可用的區域，設定小數位數，如下列範例所示：

![建立在單一可用性區域中設定的標尺](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

該規模調整集合和支援資源，例如 Azure 負載平衡器和公用 IP 位址，會建立在您指定的單一區域。


## <a name="use-the-azure-cli-20"></a>使用 Azure CLI 2.0
會建立使用可用性區域的小數位數集的程序中所述相同[使用者入門文件](virtual-machine-scale-sets-create-cli.md)。 若要使用可用性區域，您必須建立您在支援的 Azure 地區設定的標尺。 新增`--zones`參數[az vmss 建立](/cli/azure/vmss#az_vmss_create)命令，並指定要使用哪一個區域 (例如區域*1*， *2*，或*3*)。 下列範例會建立名為標尺*myScaleSet*區域*1*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

花幾分鐘的時間來建立及設定所有標尺資源和 Vm 設定在您指定的區域。


## <a name="use-azure-powershell"></a>使用 Azure PowerShell
會建立使用可用性區域的小數位數集的程序中所述相同[使用者入門文件](virtual-machine-scale-sets-create-powershell.md)。 若要使用可用性區域，您必須建立您在支援的 Azure 地區設定的標尺。 新增`-Zone`參數[新增 AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig)命令，並指定要使用哪一個區域 (例如區域*1*， *2*，或*3*). 下列範例會建立名為小數位數組設定*vmssConfig*中*美國東部 2*區域*1*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

若要建立實際的小數點位數組，請依照下列額外的步驟中所述[使用者入門文件](virtual-machine-scale-sets-create-powershell.md)。


## <a name="use-azure-resource-manager-templates"></a>使用 Azure 資源管理員範本
使用可用性區域的小數位數組的建立程序是為取得已啟動的發行項中所述相同[Linux](virtual-machine-scale-sets-create-template-linux.md)或[Windows](virtual-machine-scale-sets-create-template-windows.md)。 若要使用可用性區域，您必須建立您在支援的 Azure 地區設定的標尺。 新增`zones`屬性*Microsoft.Compute/virtualMachineScaleSets*資源輸入您的範本，並指定要使用哪一個區域 (例如區域*1*， *2*，或*3*)。 下列範例會建立名為 Linux 標尺*myScaleSet*中*美國東部 2*區域*1*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
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

若要建立實際的小數點位數集，請遵循如取得已啟動的發行項中所述的其他步驟[Linux](virtual-machine-scale-sets-create-template-linux.md)或[Windows](virtual-machine-scale-sets-create-template-windows.md)


## <a name="next-steps"></a>後續步驟
既然您已經建立可用性區域中設定的比例，您可以了解如何[部署虛擬機器上的應用程式的擴充組](virtual-machine-scale-sets-deploy-app.md)或[搭配虛擬機器擴展集自動調整規模](virtual-machine-scale-sets-autoscale-overview.md)。
