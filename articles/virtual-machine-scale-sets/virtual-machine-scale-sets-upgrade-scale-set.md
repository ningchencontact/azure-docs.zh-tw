---
title: "修改 Azure 虛擬機器擴展集 | Microsoft Docs"
description: "修改 Azure 虛擬機器擴展集"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: negat
ms.openlocfilehash: cdd1015f63e80b7ec51565c18f3440ce1828fb03
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>修改虛擬機器擴展集
本文說明如何修改現有的擴展集。 這包括如何變更擴展集的設定、如何變更在擴展集上執行之應用程式的設定、如何管理可用性等。

## <a name="fundamental-concepts"></a>基本概念

### <a name="the-scale-set-model"></a>擴展集模型

擴展集具有「擴展集模型」，可擷取擴展集整體的「預期」狀態。 若要查詢擴展集的模型，您可以使用：

REST API：`GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` (如需詳細資訊，請參閱 [REST API 文件](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/get) \(英文\))

Powershell：`Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (如需詳細資訊，請參閱 [Powershell 文件](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) \(英文\))

CLI：`az vmss show -g {resourceGroupName} -n {vmSaleSetName}` (如需詳細資訊，請參閱 [CLI 文件](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show) \(英文\))

您也可以使用 [resources.azure.com](https://resources.azure.com) 或 [Azure SDK](https://azure.microsoft.com/downloads/) 來查詢擴展集的模型。

確切的輸出呈現內容取決於您提供給命令的選項，但以下有一些來自 CLI 的範例輸出：

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
  .
  .
  .
}
```

如您所見，這些屬性會套用至擴展集整體。



### <a name="the-scale-set-instance-view"></a>擴展集執行個體檢視

擴展集也具有「擴展集執行個體檢視」，可擷取擴展集整體的目前「執行階段」狀態。 若要查詢擴展集的執行個體檢視，您可以使用：

REST API：`GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version={apiVersion}` (如需詳細資訊，請參閱 [REST API 文件](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview) \(英文\))

Powershell：`Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceView` (如需詳細資訊，請參閱 [Powershell 文件](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) \(英文\))

CLI：`az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName}` (如需詳細資訊，請參閱 [CLI 文件](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view) \(英文\))

您也可以使用 [resources.azure.com](https://resources.azure.com) 或 [Azure SDK](https://azure.microsoft.com/downloads/) 來查詢擴展集的執行個體檢視。

確切的輸出呈現內容取決於您提供給命令的選項，但以下有來自 CLI 的範例輸出：

```
$ az vmss get-instance-view -g {resourceGroupName} -n {virtualMachineScaleSetName}
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
  .
  .
  .
}
```

如您所見，這些屬性會提供擴展集內 VM 目前執行階段狀態的摘要，包括套用至擴展集之延伸模組的狀態 (為簡潔起見已省略)。



### <a name="the-scale-set-vm-model-view"></a>擴展集 VM 模型檢視

就像擴展集有模型檢視一樣，擴展集內的每個 VM 也有自己的模型檢視。 若要查詢擴展集的模型檢視，您可以使用：

REST API：`GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}?api-version={apiVersion}` (如需詳細資訊，請參閱 [REST API 文件](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/get) \(英文\))

Powershell：`Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` (如需詳細資訊，請參閱 [Powershell 文件](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm) \(英文\))

CLI：`az vmss show -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` (如需詳細資訊，請參閱 [CLI 文件](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show) \(英文\))

您也可以使用 [resources.azure.com](https://resources.azure.com) 或 [Azure SDK](https://azure.microsoft.com/downloads/) 來查詢擴展集內 VM 的模型。

確切的輸出呈現內容取決於您提供給命令的選項，但以下有一些來自 CLI 的範例輸出：

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
  .
  .
  .
}
```

如您所見，這些屬性會描述 VM 本身的設定，而非擴展集整體的設定。 例如，擴展集模型有 `overprovision` 作為屬性，而擴展集內 VM 的模型則沒有。 之所以會有這個差異，是因為過度佈建是適用於擴展集整體的屬性，而不適用於擴展集內個別的 VM (如需有關過度佈建的詳細資訊，請參閱[這份文件](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning))。



### <a name="the-scale-set-vm-instance-view"></a>擴展集 VM 執行個體檢視

就像擴展集有執行個體檢視一樣，擴展集內的每個 VM 也有自己的執行個體檢視。 若要查詢擴展集的執行個體檢視，您可以使用：

REST API：`GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/instanceView?api-version={apiVersion}` (如需詳細資訊，請參閱 [REST API 文件](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/getinstanceview) \(英文\))

Powershell：`Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -InstanceView` (如需詳細資訊，請參閱 [Powershell 文件](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm) \(英文\))

CLI：`az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` (如需詳細資訊，請參閱 [CLI 文件](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view) \(英文\))

您也可以使用 [resources.azure.com](https://resources.azure.com) 或 [Azure SDK](https://azure.microsoft.com/downloads/) 來查詢擴展集內 VM 的執行個體檢視。

確切的輸出呈現內容取決於您提供給命令的選項，但以下有一些來自 CLI 的範例輸出：

```
$ az vmss get-instance-view -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
  .
  .
  .
}
```

如您所見，這些屬性會描述 VM 本身目前執行階段狀態的摘要，包括套用至擴展集的所有延伸模組 (為簡潔起見已省略)。




## <a name="how-to-update-global-scale-set-properties"></a>如何更新全域擴展集屬性

若要更新全域擴展集屬性，您必須更新擴展集模型中的屬性。 您可以透過下列方式執行此操作：

REST API：`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` (如需詳細資訊，請參閱 [REST API 文件](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate) \(英文\))

Resource Manager 範本：您也可以選擇使用來自 REST API 的屬性來部署 Resource Manager 範本，以更新全域擴展集屬性。

Powershell：`Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -VirtualMachineScaleSet {scaleSetConfigPowershellObject}` (如需詳細資訊，請參閱 [Powershell 文件](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) \(英文\))

CLI。 修改屬性：`az vmss update --set {propertyPath}={value}`。 將物件新增至擴展集內的清單屬性：`az vmss update --add {propertyPath} {JSONObjectToAdd}`。 從擴展集內的清單屬性移除物件：`az vmss update --remove {propertyPath} {indexToRemove}`。 (如需詳細資訊，請參閱 [CLI 文件](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update) \(英文\))。 或者，如果您先前已使用 `az vmss create` 命令來部署擴展集，則可以再次執行 `az vmss create` 命令來更新擴展集。 若要這樣做，您必須確保 `az vmss create` 命令中的所有屬性都與之前相同，除了您想要修改的屬性之外。



您也可以使用 [resources.azure.com](https://resources.azure.com) 或 [Azure SDK](https://azure.microsoft.com/downloads/) 來更新擴展集模型。

更新擴展集模型之後，新的設定就會套用至擴展集內新建立的所有 VM。 不過，擴展集內現有 VM 的模型仍然必須藉由最新的整體擴展集模型來更新至最新狀態。 每個 VM 的模型中都有一個名為 `latestModelApplied` 的布林值屬性，此屬性會指出 VM 是否已藉由最新的整體擴展集模型更新至最新狀態 (`true` 表示 VM 已藉由最新的模型更新至最新狀態)。




## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>如何藉由最新的擴展集模型將 VM 更新至最新狀態

擴展集具有「升級原則」，可決定藉由最新擴展集模型將 VM 更新至最新狀態的方式。 升級原則的三個模式為：

- 自動：在此模式下，擴展集不保證 VM 的關閉順序。 擴展集可能同時關閉所有 VM。 
- 輪流：在此模式下，擴展集會分批進行更新，批次之間會有選擇性的暫停時間。
- 手動：在此模式下，當您更新擴展集模型時，現有的 VM 將不受影響。 若要更新現有的 VM，您必須「手動升級」每個現有的 VM。 您可以透過下列方式執行這個手動升級：

REST API：`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/manualupgrade?api-version={apiVersion}` (如需詳細資訊，請參閱 [REST API 文件](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/updateinstances) \(英文\))

Powershell：`Update-AzureRmVmssInstance -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` (如需詳細資訊，請參閱 [Powershell 文件](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance) \(英文\))

CLI：`az vmss update-instances -g {resourceGroupName} -n {vmScaleSetName} --instance-ids {instanceIds}` (如需詳細資訊，請參閱 [CLI 文件](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update_instances) \(英文\))。

您也可以使用 [Azure SDK](https://azure.microsoft.com/downloads/) 在擴展集內的 VM 上進行手動升級。

>[!NOTE]
> Service Fabric 叢集只能使用「自動」模式，但處理更新的方式不同。 如需有關 Service Fabric 更新的詳細資訊，請參閱 [Service Fabric 文件](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade)。

>[!NOTE]
> 有一種全域擴展集屬性修改並不遵守升級原則。 這些就是對擴展集「OS 設定檔」(例如系統管理員使用者名稱和密碼) 所做的變更。 這些屬性只能在 API 版本 2017-12-01 或更新版本中變更。 這些變更只會套用至變更擴展集模型之後建立的 VM。 若要將現有的 VM 更新至最新狀態，您必須為每個現有的 VM 執行「重新安裝映像」作業。 您可以透過下列方式執行這個重新安裝映像作業：

REST API：`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (如需詳細資訊，請參閱 [REST API 文件](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage) \(英文\))

Powershell：`Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (如需詳細資訊，請參閱 [Powershell 文件](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm) \(英文\))

CLI：`az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (如需詳細資訊，請參閱 [CLI 文件](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage) \(英文\))。

您也可以使用 [Azure SDK](https://azure.microsoft.com/downloads/) 為擴展集內的 VM 重新安裝映像。




## <a name="properties-with-restrictions-on-modification"></a>具有修改限制的屬性

### <a name="create-time-properties"></a>建立階段屬性

有些屬性只能在初始建立擴展集時設定。 這些屬性包括：

- zones (區域)
- image reference publisher (映像參考發行者)
- image reference offer (映像參考優惠)

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>只能根據目前值變更的屬性

有些屬性可以變更，但除非是根據目前的值。 這些屬性包括：

- singlePlacementGroup：如果 singlePlacementGroup 為 true，則可以修改成 false。 不過，如果 singlePlacementGroup 為 false，則**無法**修改成 true。
- subnet：只要擴展集的原始子網路與新子網路位於相同的虛擬網路中，便可以修改擴展集的子網路。

### <a name="properties-that-require-deallocation-to-change"></a>必須解除配置才能變更的屬性

有些屬性只有在已將擴展集內 VM 解除配置的情況下，才能變更成特定值。 這些屬性包括：

- sku name (SKU 名稱)：如果擴展集目前所在的硬體不支援新的 VM SKU，您必須先將擴展集內的 VM 解除配置，才能修改 SKU 名稱。 如需有關調整 VM 大小的詳細資訊，請參閱[這篇 Azure 部落格文章](https://azure.microsoft.com/blog/resize-virtual-machines/) \(英文\)。


## <a name="vm-specific-updates"></a>VM 特定的更新

某些修改可以套用至特定 VM，而無法套用至全域擴展集屬性。 目前唯一支援的 VM 特定更新是將資料磁碟連結至擴展集內的 VM，或將資料磁碟從那些 VM 中斷連結。 這項功能處於預覽狀態。 如需詳細資訊請，參閱[預覽文件](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk) \(英文\)。

## <a name="scenarios-application-updates-os-updates-etc"></a>案例：應用程式更新、OS 更新等

### <a name="application-updates"></a>應用程式更新

如果透過延伸模組將應用程式部署至擴展集，則更新延伸模組設定時，會造成應用程式根據升級原則進行更新。 例如，如果您有要在自訂指令碼延伸模組中執行的新版指令碼，則可以更新 fileUris 屬性以指向新的指令碼。 不過，在某些情況下，即使延伸模組設定未變更 (例如，您已更新指令碼，但未變更指令碼的 URI)，您也可能會想要強制更新。 在這些情況下，您可以修改 forceUpdateTag 來強制更新。 Azure 平台並不會解譯此屬性，因此變更其值對於延伸模組的執行方式並無任何影響。 修改它只是會強制延伸模組重新執行。 如需有關 forceUpdateTag 的詳細資訊，請參閱[延伸模組的 REST API 文件](https://docs.microsoft.com/rest/api/compute/virtualmachineextensions/createorupdate) \(英文\)。

透過自訂映像來部署應用程式也是常見的做法。 在接下來的＜OS 更新＞一節中將會探討此案例

### <a name="os-updates"></a>OS 更新

如果您使用平台映像，您可以修改 imageReference (如需詳細資訊，請參閱 [REST API 文件](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate) \(英文\)) 來更新映像。

>[!NOTE]
> 使用平台映像時，通常會指定 "latest" 作為映像參考版本。 這意謂著在進行擴展集建立、向外擴展及重新安裝映像時，會使用最新可用版本來建立 VM。 不過，這**並不**意謂著 OS 映像會隨著時間在新映像版本發行時自動更新。 這是個別的功能，目前為預覽版。 如需詳細資訊，請參閱 [自動作業系統升級文件](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)。

如果您使用自訂映像，您可以更新 imageReference 識別碼 (如需詳細資訊，請參閱 [REST API 文件](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate) \(英文\)) 來更新映像。

## <a name="examples"></a>範例

### <a name="updating-the-os-image-for-your-scale-set"></a>更新擴展集的 OS 映像

假設您有執行舊版 Ubuntu LTS 16.04 的擴展集，而您想要更新成較新版的 Ubuntu LTS 16.04 (例如 16.04.201801090 版)。 映像參考版本屬性不是清單的一部分，因此您可以直接使用下列命令來修改這些屬性：

Powershell：`Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -ImageReferenceVersion 16.04.201801090`

CLI：`az vmss update -g {resourceGroupName} -n {vmScaleSetName} --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090`


### <a name="updating-the-load-balancer-for-your-scale-set"></a>更新擴展集的負載平衡器

假設您有一個含有 Azure Load Balancer 的擴展集，而您想要以「Azure 應用程式閘道」取代 Azure Load Balancer。 擴展集的負載平衡器和應用程式閘道屬性是清單的一部分，因此您可以使用命令來移除和新增清單元素，而不需直接修改屬性：

PowerShell：
```
# get the current model of the scale set and store it in a local powershell object named $vmss
> $vmss=Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName}

# create a local powershell object for the new desired IP configuration, which includes the referencerence to the application gateway
> $ipconf = New-AzureRmVmssIPConfig myNic -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name

# replace the existing IP configuration in the local powershell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
> $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf

# Update the model of the scale set with the new configuration in the local powershell object
> Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName} -virtualMachineScaleSet $vmss

```

CLI：
```
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0 # remove the load balancer backend pool from the scale set model
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0 # remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}' # add the application gateway backend pool to the scale set model
```

>[!NOTE]
> 這些命令會假設擴展集上只有一個 IP 設定和負載平衡器。 如果有多個，您可能需要使用清單索引而不是 0。