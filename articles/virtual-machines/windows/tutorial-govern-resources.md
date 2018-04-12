---
title: 使用 Azure PowerShell 控管 Azure 虛擬機器 | Microsoft Docs
description: 教學課程 - 藉由使用 Azure PowerShell 來套用 RBAC、原則、鎖定和標籤以管理 Azure 虛擬機器
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: 30f5fe83c46f2dbe1933e8347242be7fbb30a3e3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="virtual-machine-governance-with-azure-powershell"></a>使用 Azure PowerShell 控管虛擬機器

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

如果您選擇在本機安裝和使用 PowerShell，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Login-AzureRmAccount` 以建立與 Azure 的連線。 針對本機安裝，您還必須[下載 Azure AD PowerShell 模組](https://www.powershellgallery.com/packages/AzureAD/)以建立新的 Azure Active Directory 群組。

## <a name="understand-scope"></a>了解範圍

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

在本教學課程中，您會將所有的管理設定套用到資源群，讓您可以在完成後輕易地移除這些設定。

先來建立該資源群組。

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

目前資源群組是空的。

## <a name="role-based-access-control"></a>角色型存取控制

您想要確定組織中的使用者具有這些資源的正確存取權等級。 您不想要授與不受限制的存取權給使用者，但是您又必須確定他們可以執行其工作。 [角色型存取控制](../../active-directory/role-based-access-control-what-is.md)可讓您管理哪些使用者具有權限可以在一個範圍內完成特定動作。

若要建立和移除角色指派，使用者必須具有 `Microsoft.Authorization/roleAssignments/*` 存取權。 此存取權是透過擁有者或使用者存取系統管理員角色來授與。

為了管理虛擬機器解決方案，有三個資源專屬角色可提供您經常需要的存取權：

* [虛擬機器參與者](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)
* [網路參與者](../../active-directory/role-based-access-built-in-roles.md#network-contributor)
* [儲存體帳戶參與者](../../active-directory/role-based-access-built-in-roles.md#storage-account-contributor)

相較於將角色指派給個別使用者，為需要執行類似動作的使用者[建立 Azure Active Directory 群組](../../active-directory/active-directory-groups-create-azure-portal.md)通常會更加容易。 然後，將該群組指派給適當的角色。 一言以蔽之，您要建立沒有成員的 Azure Active Directory 群組。 您仍然可以指派此群組給某個範圍內的角色。 

下列範例會建立名為 VMDemoContributors、郵件暱稱為 vmDemoGroup 的 Azure Active Directory 群組。 郵件暱稱可作為群組的別名。

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
```

在命令提示字元傳回後，群組需要一些時間才會傳播到整個 Azure Active Directory。 等候 20 或 30 秒之後，使用 [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) 命令將新的 Azure Active Directory 群組指派給資源群組的虛擬機器參與者角色。  如果您在群組傳播完成之前執行下列命令，您會收到錯誤，指出：**原則 <guid> 不存在於目錄中**。 請嘗試再次執行命令。

```azurepowershell-interactive
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

通常您需要針對網路參與者和儲存體帳戶參與者重複進行此程序，以確保已指派使用者來管理已部署的資源。 在本文中，您可以略過這些步驟。

## <a name="azure-policies"></a>Azure 原則

[!INCLUDE [Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>套用原則

您的訂用帳戶已經有數個原則定義。 若要查看可用的原則定義，請使用 [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) 命令：

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

您會看到現有的原則定義。 原則類型不是**內建**就是**自訂**。 查看定義，尋找符合您要指派之條件的定義。 在本文中，您指派的原則要：

* 限制所有資源的位置。
* 限制虛擬機器的 SKU。
* 稽核未使用受控磁碟的虛擬機器。

在下列範例中，您會根據顯示名稱擷取三個原則定義。 您使用 [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) 命令將這些定義指派給資源群組。 針對某些原則，您要提供參數值來指定允許的值。

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzureRmResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
New-AzureRMPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>部署虛擬機器

您已指派角色和原則，現在可以開始部署您的解決方案。 預設大小是 Standard_DS1_v2，也就是所允許之 SKU 的其中一個。 執行此步驟時，系統會提示您輸入認證。 您輸入的值會設定為虛擬機器的使用者名稱和密碼。

```azurepowershell-interactive
New-AzureRmVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

部署完成之後，您可以套用更多的管理設定至解決方案。

## <a name="lock-resources"></a>鎖定資源

[資源鎖定](../../azure-resource-manager/resource-group-lock-resources.md)可避免組織中的使用者不小心刪除或修改重要資源。 不同於角色型存取控制，資源鎖定會對所有使用者和角色套用限制。 您可以將鎖定層級設定為 *CanNotDelete* 或 *ReadOnly*。

若要鎖定虛擬機器和網路安全性群組，請使用 [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock) 命令：

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

若要測試鎖定，請嘗試執行下列命令：

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup
```

您會看到錯誤指出因為鎖定所以無法執行刪除作業。 只有當您明確移除鎖定後，才能刪除資源群組。 [清除資源](#clean-up-resources)中會說明該步驟。

## <a name="tag-resources"></a>標記資源

您可將[標籤](../../azure-resource-manager/resource-group-using-tags.md)套用至 Azure 資源，以便以邏輯方式依照類別組織這些資源。 每個標記都是由一個名稱和一個值所組成。 例如，您可以將「環境」名稱和「生產」值套用至生產環境中的所有資源。

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

若要將標籤套用至虛擬機器，請使用 [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) 命令：

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>依標籤尋找資源

若要尋找具有某標籤名稱和值的資源，請使用 [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource) 命令：

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

傳回的值可用於管理工作，例如停止具有某標籤值的所有虛擬機器。

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>依標籤值檢視成本

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>清除資源

您無法刪除已鎖定的網路安全性群組，除非移除鎖定。 若要移除鎖定，請使用 [Remove-AzureRmResourceLock](/powershell/module/azurerm.resources/remove-azurermresourcelock) 命令：

```azurepowershell-interactive
Remove-AzureRmResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzureRmResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

若不再需要，您可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令來移除資源群組、VM 和所有相關資源。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

您在本教學課程中建立了自訂 VM 映像。 您已了解如何︰

> [!div class="checklist"]
> * 將使用者指派給角色
> * 套用會強制執行標準的原則
> * 使用鎖定保護重要資源
> * 標記資源以進行計費和管理

請前進到下一個教學課程，以了解如何使虛擬機器具備高可用性。

> [!div class="nextstepaction"]
> [監視虛擬機器](tutorial-monitoring.md)

