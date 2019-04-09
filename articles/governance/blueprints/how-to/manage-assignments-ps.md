---
title: 如何管理使用 PowerShell 指派
description: 了解如何管理使用官方 Azure 藍圖 PowerShell 模組，Az.Blueprint 藍圖指派。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d8eacffe4b792eda5d81051f6aa65caa3292c896
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256867"
---
# <a name="how-to-manage-assignments-with-powershell"></a>如何管理使用 PowerShell 指派

可以使用管理藍圖指派**Az.Blueprint** Azure PowerShell 模組。 此模組支援擷取、 建立、 更新和移除指派。 此模組也可以在現有的藍圖定義擷取詳細資料。 這篇文章說明如何安裝模組，並開始使用它。

## <a name="add-the-azblueprint-module"></a>新增 Az.Blueprint 模組

若要啟用 Azure PowerShell 來管理藍圖指派，必須新增的模組。 此模組適用於在本機安裝的 PowerShell、[Azure Cloud Shell](https://shell.azure.com) 或 [Azure PowerShell Docker 映像](https://hub.docker.com/r/azuresdk/azure-powershell/)。

### <a name="base-requirements"></a>基底需求

Azure 藍圖模組需要下列軟體：

- Azure PowerShell 1.5.0 或更高版本。 如果尚未安裝，請依照[這些指示](/powershell/azure/install-az-ps)操作。
- PowerShellGet 2.0.1 或更新版本。 如果未安裝或更新，請依照[這些指示](/powershell/gallery/installing-psget)操作。

### <a name="install-the-module"></a>安裝模組

適用於 PowerShell 的藍圖模組**Az.Blueprint**。

1. 從**系統管理** PowerShell 提示字元中，執行下列命令：

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > 如果**Az.Accounts**是已安裝，可能需要使用`-AllowClobber`強制安裝。

1. 驗證已匯入模組，而且是正確的版本 (0.1.0)：

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>取得藍圖定義

使用 指派的第一個步驟通常取得 blueprint （藍圖） 定義的參考。
`Get-AzBlueprint` Cmdlet 會取得一或多個藍圖定義。 此指令程式可以從管理群組取得藍圖定義`-ManagementGroupId {mgId}`的訂用帳戶或`-SubscriptionId {subId}`。 **名稱**參數取得 blueprint （藍圖） 定義，但必須配合**ManagementGroupId**或是**SubscriptionId**。 **版本**可以搭配**名稱**更明確了哪些 blueprint （藍圖） 會傳回定義。 而不是**版本**，切換`-LatestPublished`grabs 最近發行的版本。

下列範例會使用`Get-AzBlueprint`以取得所有版本的藍圖定義名為 '101-藍圖-定義-訂用帳戶' 從特定的訂用帳戶以表示`{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

藍圖定義多個版本的範例輸出看起來像這樣：

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

[藍圖參數](../concepts/parameters.md#blueprint-parameters)藍圖上定義可以擴充以提供更多的資訊。

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

如果藍圖指派已經存在，您可以取得它的參考`Get-AzBlueprintAssignment`cmdlet。 此 cmdlet 會採用**SubscriptionId**並**名稱**做為選擇性參數。 如果**SubscriptionId**未指定，會使用目前的訂用帳戶內容。

下列範例會使用`Get-AzBlueprintAssignment`若要取得具名 '指派-鎖定-資源-群組' 從特定的訂用帳戶以單一的藍圖指派`{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

藍圖指派的範例輸出看起來像這樣：

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

如果還不存在藍圖指派，您可以建立它與`New-AzBlueprintAssignment`cmdlet。 此 cmdlet 會使用下列參數：

- **名稱**[必要]
  - 指定名稱的藍圖指派
  - 必須是唯一而且不存在於**SubscriptionId**
- **藍圖**[必要]
  - 指定要指派藍圖定義
  - 使用`Get-AzBlueprint`來取得參考的物件
- **位置**[必要]
  - 指定系統指派給受控身分識別和訂用帳戶部署物件中建立的區域
- **訂用帳戶**（選擇性）
  - 指定指派部署至訂用帳戶
  - 如果未提供，預設為目前的訂用帳戶內容
- **鎖定**（選擇性）
  - 定義[藍圖資源鎖定](../concepts/resource-locking.md)来用於已部署的資源
  - 支援的選項：_無_， _AllResourcesReadOnly_， _AllResourcesDoNotDelete_
  - 如果未提供，預設值為_None_
- **SystemAssignedIdentity** （選擇性）
  - 選取要部署資源和建立由系統指派給受控身分識別的指派
  - 預設值為"identity"參數集的
  - 不能搭配**UserAssignedIdentity**
- **UserAssignedIdentity** （選擇性）
  - 指定的使用者指派給受控身分識別用來指派，以及將資源部署
  - "Identity"參數集的一部分
  - 不能搭配**SystemAssignedIdentity**
- **參數**（選擇性）
  - A[雜湊表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)設定的索引鍵/值組[動態參數](../concepts/parameters.md#dynamic-parameters)藍圖指派
  - 預設值為動態參數**defaultValue**定義中
  - 如果未提供參數，且沒有**defaultValue**，參數不是選擇性參數

    > [!NOTE]
    > **參數**不支援 securestrings 做。

- **ResourceGroupParameter** （選擇性）
  - A[雜湊表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)的資源群組成品
  - 每個資源群組成品預留位置將會有動態設定索引鍵/值配對**名稱**及/或**位置**於該資源群組的成品
  - 如果未提供資源群組參數，且沒有**defaultValue**，資源群組參數不是選擇性參數

下列範例會建立新的指派，擷取與 'my 藍圖' 藍圖定義 '1.1' 版的`Get-AzBlueprint`，以 'westus2' 受管理的 身分識別與指派物件位置，鎖定的資源_AllResourcesReadOnly_，並同時設定雜湊表**參數**並**ResourceGroupParameter**表示為特定的訂用帳戶上`{subId}`:

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

建立藍圖指派的範例輸出看起來像這樣：

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

有時候就必須更新已建立的藍圖指派。 `Set-AzBlueprintAssignment`指令程式會處理此動作。 指令程式會使用大部分相同的參數， `New-AzBlueprintAssignment` cmdlet 已存在，但允許設定指派至更新的任何項目。 此例外狀況_名稱_，_藍圖_，並_SubscriptionId_。 會更新所提供的值。

若要了解更新藍圖指派時，會發生什麼情況，請參閱[規則正在更新指派](./update-existing-assignments.md#rules-for-updating-assignments)。

- **名稱**[必要]
  - 指定要更新的藍圖指派的名稱
  - 用來尋找更新，而不變更指派的指派
- **藍圖**[必要]
  - 指定藍圖指派藍圖的定義
  - 使用`Get-AzBlueprint`來取得參考的物件
  - 用來尋找更新，而不變更指派的指派
- **位置**（選擇性）
  - 指定系統指派給受控身分識別和訂用帳戶部署物件中建立的區域
- **訂用帳戶**（選擇性）
  - 指定指派部署至訂用帳戶
  - 如果未提供，預設為目前的訂用帳戶內容
  - 用來尋找更新，而不變更指派的指派
- **鎖定**（選擇性）
  - 定義[藍圖資源鎖定](../concepts/resource-locking.md)来用於已部署的資源
  - 支援的選項：_無_， _AllResourcesReadOnly_， _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** （選擇性）
  - 選取要部署資源和建立由系統指派給受控身分識別的指派
  - 預設值為"identity"參數集的
  - 不能搭配**UserAssignedIdentity**
- **UserAssignedIdentity** （選擇性）
  - 指定的使用者指派給受控身分識別用來指派，以及將資源部署
  - "Identity"參數集的一部分
  - 不能搭配**SystemAssignedIdentity**
- **參數**（選擇性）
  - A[雜湊表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)設定的索引鍵/值組[動態參數](../concepts/parameters.md#dynamic-parameters)藍圖指派
  - 預設值為動態參數**defaultValue**定義中
  - 如果未提供參數，且沒有**defaultValue**，參數不是選擇性參數

    > [!NOTE]
    > **參數**不支援 securestrings 做。

- **ResourceGroupParameter** （選擇性）
  - A[雜湊表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)的資源群組成品
  - 每個資源群組成品預留位置將會有動態設定索引鍵/值配對**名稱**及/或**位置**於該資源群組的成品
  - 如果未提供資源群組參數，且沒有**defaultValue**，資源群組參數不是選擇性參數

下列範例會更新與擷取的 'my 藍圖' 藍圖定義 '1.1' 版的指派`Get-AzBlueprint`藉由變更的鎖定模式：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

建立藍圖指派的範例輸出看起來像這樣：

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

要移除的藍圖指派的時候`Remove-AzBlueprintAssignment`指令程式會處理此動作。 此 cmdlet 會接受**名稱**或是**InputObject**以指定的藍圖指派，以移除。 **SubscriptionId**已_必要_且必須在所有情況下提供。

下列範例會擷取與現有的藍圖指派`Get-AzBlueprintAssignment`，然後移除該特定訂用帳戶，並表示為從`{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>端對端程式碼範例

將所有的步驟結合在一起，下列範例取得藍圖定義，然後建立、 更新，並表示為特定訂用帳戶中移除的藍圖指派`{subId}`:

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