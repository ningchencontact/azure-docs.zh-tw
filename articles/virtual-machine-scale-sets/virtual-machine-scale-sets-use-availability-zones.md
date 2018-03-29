---
title: 建立使用可用性區域的 Azure 擴展集 (預覽) | Microsoft Docs
description: 了解如何建立使用可用性區域的 Azure 虛擬機器擴展集，以提升系統中斷時的備援能力
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: iainfou
ms.openlocfilehash: 8b497af8bc7e3060e184dd6a029b23ccb2d2bbfb
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>建立使用可用性區域的虛擬機器擴展集 (預覽)
如要保護虛擬機器擴展集，使其免於資料中心層級的失敗，您可以建立跨可用性區域的擴展集。 支援可用性區域的 Azure 區域至少會有三個不同的區域，每個區域都有自己的獨立電源、網路和冷卻系統。 如需詳細資訊，請參閱[可用性區域概觀](../availability-zones/az-overview.md)。

[!INCLUDE [availability-zones-preview-statement.md](../../includes/availability-zones-preview-statement.md)]


## <a name="single-zone-and-zone-redundant-scale-sets"></a>單一區域和區域備援擴展集
在部署虛擬機器擴展集時，您可以選擇使用一個區域中的單一可用性區域，或是使用多個區域。

當您在單一區域中建立擴展集時，您可以控制要在哪個區域執行所有 VM 執行個體，且擴展集只會在該區域內進行管理並自動調整。 區域備援擴展集可讓您建立跨多個區域的單一擴展集。 當您建立 VM 執行個體時，系統預設會將它們平均分散到各個區域。 若其中一個區域發生中斷，擴展集不會自動相應放大來增加容量。 最佳做法是設定以 CPU 或記憶體使用量為基礎的自動調整規則。 自動調整規則可讓擴展集在該區域發生 VM 執行個體遺失時，於其餘運作中的區域相應放大新的執行個體，來作為回應。

若要使用可用性區域，您必須在[支援的 Azure 區域](../availability-zones/az-overview.md#regions-that-support-availability-zones)中建立擴展集。 您還必須[註冊可用性區域預覽](http://aka.ms/azenroll)。 您可以使用下列其中一個方法，來建立使用可用性區域的擴展集：

- [Azure 入口網站](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Azure 資源管理員範本](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>使用 Azure 入口網站
建立使用可用性區域之擴展集的流程，與[使用者入門文章](quick-create-portal.md)中所述的流程相同。 請確定您已[註冊可用性區域預覽](http://aka.ms/azenroll)。 當您選取支援的 Azure 區域時，您就可以在其中一個可用區域中建立擴展集，如下列範例所示：

![在單一可用性區域中建立擴展集](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

擴展集和支援資源 (例如，Azure 負載平衡器和公用 IP 位址) 都會建立在您指定的單一區域中。


## <a name="use-the-azure-cli-20"></a>使用 Azure CLI 2.0
建立使用可用性區域之擴展集的流程，與[使用者入門文章](quick-create-cli.md)中所述的流程相同。 若要使用可用性區域，您必須在支援的 Azure 區域中建立擴展集，並且已[註冊可用性區域預覽](http://aka.ms/azenroll)。

將 `--zones` 參數新增至 [az vmss create](/cli/azure/vmss#az_vmss_create) 命令，並指定要使用的區域 (例如區域 1、2 或 3)。 下列範例會在區域 1 中建立名為 myScaleSet 的單一區域擴展集：

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
如需單一區域擴展集和網路資源的完整範例，請參閱[這個 CLI 指令碼範例](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh.)

### <a name="zone-redundant-scale-set"></a>區域備援擴展集
若要建立區域備援擴展集，請使用標準 SKU 公用 IP 位址和負載平衡器。 為了增強備援能力，標準 SKU 會建立區域備援網路資源。 如需詳細資訊，請參閱 [Azure Load Balancer Standard 概觀](../load-balancer/load-balancer-standard-overview.md)。 第一次建立區域備援擴展集或負載平衡器時，您必須完成下列步驟來為帳戶註冊這些預覽功能。

1. 使用 [az feature register](/cli/azure/feature#az_feature_register) 為帳戶註冊區域備援擴展集和網路功能，如下所示：

    ```azurecli
    az feature register --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```
    
2. 註冊功能可能需要幾分鐘的時間。 您可以使用 [az feature show](/cli/azure/feature#az_feature_show) 來檢查作業狀態：

    ```azurecli
    az feature show --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature show --name AllowLBPreview --namespace Microsoft.Network
    ```

    下列範例顯示所需的功能狀態為「已註冊」：
    
    ```json
    "properties": {
          "state": "Registered"
       },
    ```

3. 一旦區域備援擴展集和網路資源都回報為「已註冊」後，請使用 [az provider register](/cli/azure/provider#az_provider_register) 重新註冊「計算」和「網路」提供者，如下所示：

    ```azurecli
    az provider register --namespace Microsoft.Compute
    az provider register --namespace Microsoft.Network
    ```

若要建立區域備援擴展集，請使用 `--zones` 參數來指定多個區域。 下列範例會跨區域 1、2、3 建立名為 myScaleSet 的區域備援擴展集：

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones {1,2,3}
```

在您指定的區域中建立及設定所有擴展集資源和 VM 需要幾分鐘的時間。 如需區域備援擴展集和網路資源的完整範例，請參閱[這個 CLI 指令碼範例](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)


## <a name="use-azure-powershell"></a>使用 Azure PowerShell
建立使用可用性區域之擴展集的流程，與[使用者入門文章](quick-create-powershell.md)中所述的流程相同。 若要使用可用性區域，您必須在支援的 Azure 區域中建立擴展集，並且已[註冊可用性區域預覽](http://aka.ms/azenroll)。 將 `-Zone` 參數新增至 [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) 命令，並指定要使用的區域 (例如區域 1、2 或 3)。 

下列範例會在「美國東部 2」區域 1 中，建立名為 vmssConfig 的單一區域擴展集設定：

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

如需單一區域擴展集和網路資源的完整範例，請參閱[這個 PowerShell 指令碼範例](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1)

### <a name="zone-redundant-scale-set"></a>區域備援擴展集
若要建立區域備援擴展集，請使用標準 SKU 公用 IP 位址和負載平衡器。 為了增強備援能力，標準 SKU 會建立區域備援網路資源。 如需詳細資訊，請參閱 [Azure Load Balancer Standard 概觀](../load-balancer/load-balancer-standard-overview.md)。 第一次建立區域備援擴展集或負載平衡器時，您必須完成下列步驟來為帳戶註冊這些預覽功能。

1. 使用 [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) 為帳戶註冊區域備援擴展集和網路功能，如下所示：

    ```powershell
    Register-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```
    
2. 註冊功能可能需要幾分鐘的時間。 您可以使用 [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) 來檢查作業狀態：

    ```powershell
    Get-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute 
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

    下列範例顯示所需的功能狀態為「已註冊」：
    
    ```powershell
    RegistrationState
    -----------------
    Registered
    ```

3. 一旦區域備援擴展集和網路資源都回報為「已註冊」後，請使用 [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) 重新註冊「計算」和「網路」提供者，如下所示：

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```

若要建立區域備援擴展集，請使用 `-Zone` 參數來指定多個區域。 下列範例會跨「美國東部 2」區域 1、2、3 建立名為 myScaleSet 的區域備援擴展集設定：

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1", "2", "3"
```

如果您使用 [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) 來建立公用 IP 位址，或使用 [New-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/New-AzureRmLoadBalancer) 來建立負載平衡器，請指定「-SKU "Standard"」來建立區域備援網路資源。 您還必須建立網路安全性群組和規則，以便允許所有流量。 如需詳細資訊，請參閱 [Azure Load Balancer Standard 概觀](../load-balancer/load-balancer-standard-overview.md)。

如需區域備援擴展集和網路資源的完整範例，請參閱[這個 PowerShell 指令碼範例](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1)


## <a name="use-azure-resource-manager-templates"></a>使用 Azure 資源管理員範本
建立使用可用性區域之擴展集的流程，與 [Linux](quick-create-template-linux.md) 或 [Windows](quick-create-template-windows.md) 的使用者入門文章中所述的流程相同。 若要使用可用性區域，您必須在支援的 Azure 區域中建立擴展集，並且已[註冊可用性區域預覽](http://aka.ms/azenroll)。 將 `zones` 屬性新增至範本中的 Microsoft.Compute/virtualMachineScaleSets 資源類型，並指定要使用的區域 (例如區域 1、2 或 3)。

下列範例會在「美國東部 2」區域 1 中建立名為 myScaleSet 的 Linux 單一區域擴展集：

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

如需單一區域擴展集和網路資源的完整範例，請參閱[這個 Resource Manager 範本範例](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>區域備援擴展集
若要建立區域備援擴展集，請在 Microsoft.Compute/virtualMachineScaleSets 資源類型的 `zones` 屬性中指定多個值。 下列範例會跨「美國東部 2」區域 1、2、3 建立名為 myScaleSet 的區域備援擴展集：

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

如果您建立的是公用 IP 位址或負載平衡器，請指定「"sku": { "name": "Standard" }"」屬性，以建立區域備援網路資源。 您還必須建立網路安全性群組和規則，以便允許所有流量。 如需詳細資訊，請參閱 [Azure Load Balancer Standard 概觀](../load-balancer/load-balancer-standard-overview.md)。

如需區域備援擴展集和網路資源的完整範例，請參閱[這個 Resource Manager 範本範例](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)


## <a name="next-steps"></a>後續步驟
現在您已在可用性區域中建立擴展集，接下來您可以了解如何[在虛擬機器擴展集上部署應用程式](virtual-machine-scale-sets-deploy-app.md)或[搭配虛擬機器擴展集使用自動調整](virtual-machine-scale-sets-autoscale-overview.md)。
