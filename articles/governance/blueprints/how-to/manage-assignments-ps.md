---
title: 如何使用 PowerShell 管理指派
description: 瞭解如何使用官方 Azure 藍圖 PowerShell 模組 Az. 藍圖來管理藍圖指派。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: beaa3f4c5ab272592e7fae5a95b40a9b586aaf65
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232884"
---
# <a name="how-to-manage-assignments-with-powershell"></a>如何使用 PowerShell 管理指派

您可以使用**Az. 藍圖**Azure PowerShell 模組來管理藍圖指派。 此模組支援提取、建立、更新和移除指派。 此模組也可以提取現有藍圖定義的詳細資料。 本文涵蓋如何安裝模組並開始使用它。

## <a name="add-the-azblueprint-module"></a>新增 Az. 藍圖模組

若要讓 Azure PowerShell 管理藍圖指派, 必須新增模組。 此模組適用於在本機安裝的 PowerShell、[Azure Cloud Shell](https://shell.azure.com) 或 [Azure PowerShell Docker 映像](https://hub.docker.com/r/azuresdk/azure-powershell/)。

### <a name="base-requirements"></a>基底需求

Azure 藍圖模組需要下列軟體:

- Azure PowerShell 1.5.0 或更高版本。 如果尚未安裝，請依照[這些指示](/powershell/azure/install-az-ps)操作。
- PowerShellGet 2.0.1 或更新版本。 如果未安裝或更新，請依照[這些指示](/powershell/gallery/installing-psget)操作。

### <a name="install-the-module"></a>安裝模組

適用于 PowerShell 的藍圖模組是**Az. 藍圖**。

1. 從**系統管理** PowerShell 提示字元中，執行下列命令：

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > 如果已安裝**Az** , 則可能需要使用`-AllowClobber`來強制安裝。

1. 驗證已匯入模組，而且是正確的版本 (0.1.0)：

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>取得藍圖定義

使用指派的第一個步驟通常是取得藍圖定義的參考。
`Get-AzBlueprint` Cmdlet 會取得一或多個藍圖定義。 Cmdlet 可以從管理群組取得藍圖定義, `-ManagementGroupId {mgId}`或使用`-SubscriptionId {subId}`訂用帳戶。 **Name**參數會取得藍圖定義, 但必須與**ManagementGroupId**或**SubscriptionId**搭配使用。 **版本**可以與**名稱**搭配使用, 以更明確地瞭解傳回的藍圖定義。 參數`-LatestPublished`不是**版本**, 而是會抓取最近發行的版本。

下列範例會使用`Get-AzBlueprint` , 從表示為`{subId}`的特定訂用帳戶, 取得名為 ' 101-藍圖-定義-訂用帳戶 ' 的藍圖定義的所有版本:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

具有多個版本之藍圖定義的範例輸出看起來像這樣:

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

藍圖定義上的[藍圖參數](../concepts/parameters.md#blueprint-parameters)可以擴充以提供詳細資訊。

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>取得藍圖指派

如果藍圖指派已存在, 您可以使用`Get-AzBlueprintAssignment` Cmdlet 取得它的參考。 此 Cmdlet 會採用**SubscriptionId**和**Name**做為選擇性參數。 如果未指定**SubscriptionId** , 則會使用目前的訂用帳戶內容。

下列範例會使用`Get-AzBlueprintAssignment` , 從表示為`{subId}`的特定訂用帳戶, 取得名為「指派-鎖定-資源群組」的單一藍圖指派:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

藍圖指派的範例輸出如下所示:

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>建立藍圖指派

如果藍圖指派尚不存在, 您可以使用`New-AzBlueprintAssignment` Cmdlet 加以建立。 此 Cmdlet 會使用下列參數:

- **名稱**具備
  - 指定藍圖指派的名稱
  - 必須是唯一的, 而且不存在於**SubscriptionId**中
- **藍圖**具備
  - 指定要指派的藍圖定義
  - 使用`Get-AzBlueprint`取得參考物件
- **位置**具備
  - 指定要在其中建立系統指派的受控識別和訂用帳戶部署物件的區域
- **訂**用帳戶選擇性
  - 指定要部署指派的訂用帳戶
  - 如果未提供, 則預設為目前的訂用帳戶內容
- **鎖定**選擇性
  - 定義要用於已部署資源的[藍圖資源鎖定](../concepts/resource-locking.md)
  - 支援的選項:_None_、 _AllResourcesReadOnly_、 _AllResourcesDoNotDelete_
  - 如果未提供, 則預設為_None_
- **SystemAssignedIdentity**選擇性
  - 選取即可建立指派給系統指派的受控識別, 並部署資源
  - 已設定 "identity" 參數的預設值
  - 不能與**UserAssignedIdentity**搭配使用
- **UserAssignedIdentity**選擇性
  - 指定要用於指派和部署資源的使用者指派受控識別
  - 「身分識別」參數集的一部分
  - 不能與**SystemAssignedIdentity**搭配使用
- **參數**選擇性
  - 在藍圖指派上設定[動態參數](../concepts/parameters.md#dynamic-parameters)的索引鍵/值組[雜湊表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 動態參數的預設值為定義中的**defaultValue**
  - 如果未提供參數, 而且沒有**defaultValue**, 則參數不是選擇性的

    > [!NOTE]
    > **參數**不支援 secureStrings。

- **ResourceGroupParameter**選擇性
  - 資源群組成品的[雜湊表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 每個資源群組成品預留位置都有一個索引鍵/值組, 可動態設定該資源群組成品上的**名稱**和/或**位置**。
  - 如果未提供資源群組參數, 而且沒有**defaultValue**, 則資源群組參數不是選擇性的

下列範例會建立以所提取`Get-AzBlueprint`之 ' my 藍圖 ' 藍圖定義版本 ' 1.1 ' 的新指派, 並將受控識別和指派物件位置設定為 ' westus2 ', 並使用_AllResourcesReadOnly 鎖定資源_, 和會針對以表示的`{subId}`特定訂用帳戶, 設定**參數**和**ResourceGroupParameter**的雜湊表:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

建立藍圖指派的範例輸出看起來像這樣:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadyOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="update-blueprint-assignments"></a>更新藍圖指派

有時候, 必須更新已建立的藍圖指派。 `Set-AzBlueprintAssignment` Cmdlet 會處理此動作。 此 Cmdlet 會採用`New-AzBlueprintAssignment` Cmdlet 所執行的大部分相同參數, 以允許更新指派上設定的任何專案。 這是_名稱_、_藍圖_和_SubscriptionId_這兩者的例外狀況。 只會更新提供的值。

若要瞭解更新藍圖指派時所發生的情況, 請參閱[更新指派的規則](./update-existing-assignments.md#rules-for-updating-assignments)。

- **名稱**具備
  - 指定要更新之藍圖指派的名稱
  - 用來找出要更新的指派, 而不是變更指派
- **藍圖**具備
  - 指定藍圖指派的藍圖定義
  - 使用`Get-AzBlueprint`取得參考物件
  - 用來找出要更新的指派, 而不是變更指派
- **位置**選擇性
  - 指定要在其中建立系統指派的受控識別和訂用帳戶部署物件的區域
- **訂**用帳戶選擇性
  - 指定要部署指派的訂用帳戶
  - 如果未提供, 則預設為目前的訂用帳戶內容
  - 用來找出要更新的指派, 而不是變更指派
- **鎖定**選擇性
  - 定義要用於已部署資源的[藍圖資源鎖定](../concepts/resource-locking.md)
  - 支援的選項:_None_、 _AllResourcesReadOnly_、 _AllResourcesDoNotDelete_
- **SystemAssignedIdentity**選擇性
  - 選取即可建立指派給系統指派的受控識別, 並部署資源
  - 已設定 "identity" 參數的預設值
  - 不能與**UserAssignedIdentity**搭配使用
- **UserAssignedIdentity**選擇性
  - 指定要用於指派和部署資源的使用者指派受控識別
  - 「身分識別」參數集的一部分
  - 不能與**SystemAssignedIdentity**搭配使用
- **參數**選擇性
  - 在藍圖指派上設定[動態參數](../concepts/parameters.md#dynamic-parameters)的索引鍵/值組[雜湊表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 動態參數的預設值為定義中的**defaultValue**
  - 如果未提供參數, 而且沒有**defaultValue**, 則參數不是選擇性的

    > [!NOTE]
    > **參數**不支援 secureStrings。

- **ResourceGroupParameter**選擇性
  - 資源群組成品的[雜湊表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 每個資源群組成品預留位置都有一個索引鍵/值組, 可動態設定該資源群組成品上的**名稱**和/或**位置**。
  - 如果未提供資源群組參數, 而且沒有**defaultValue**, 則資源群組參數不是選擇性的

下列範例會變更鎖定模式, 以`Get-AzBlueprint`更新提取的「我的藍圖」藍圖定義版本 ' 1.1 ' 的指派:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

建立藍圖指派的範例輸出看起來像這樣:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>移除藍圖指派

當您想要移除藍圖指派時, `Remove-AzBlueprintAssignment`此 Cmdlet 會處理此動作。 Cmdlet 會使用**Name**或**InputObject**來指定要移除哪一個藍圖指派。 **SubscriptionId**是_必要_的, 而且必須在所有情況下提供。

下列範例會使用`Get-AzBlueprintAssignment`來提取現有的藍圖指派, 然後從表示為`{subId}`的特定訂用帳戶中移除它:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>端對端程式碼範例

將所有步驟結合在一起, 下列範例會取得藍圖定義, 然後建立、更新和移除特定訂用帳戶中的藍圖指派, 表示`{subId}`為:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>後續步驟

- 了解[藍圖生命週期](../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../concepts/resource-locking.md)。
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題。