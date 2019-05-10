---
title: 自動加入 Azure DevTest Labs 中的實驗室使用者 |Microsoft Docs
description: 了解如何自動化在 Azure DevTest Labs 中對實驗室新增實驗室使用者。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 0eed874d405fcf99241a702292f8ceadae6c5a07
ms.sourcegitcommit: 1d257ad14ab837dd13145a6908bc0ed7af7f50a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65502038"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>自動加入 Azure DevTest Labs 中實驗室的實驗室使用者
Azure DevTest Labs 可讓您快速地建立使用 Azure 入口網站的 自助服務的開發 / 測試環境。 不過，如果您有數個小組和數個 DevTest Labs 執行個體，自動在建立程序可以節省時間。 [Azure Resource Manager 範本](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)可讓您建立實驗室、 實驗室 Vm、 自訂映像、 公式，並以自動化方式新增使用者。 本文特別著重於將使用者新增至 DevTest Labs 執行個體。

若要將使用者新增至實驗室，您將使用者新增至**DevTest Labs 使用者**實驗室的角色。 這篇文章會說明如何自動執行 新增使用者至實驗室，使用下列方法之一：

- Azure 資源管理員範本
- Azure PowerShell Cmdlet 
- Azure CLI。

## <a name="use-azure-resource-manager-templates"></a>使用 Azure 資源管理員範本
下列的範例 Resource Manager 範本會指定要加入至使用者**DevTest Labs 使用者**實驗室的角色。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

如果您要指派相同的範本建立實驗室中的角色，請記得將角色指派資源和實驗室之間的相依性。 如需詳細資訊，請參閱 < [Azure Resource Manager 範本中定義相依性](/azure-resource-manager/resource-group-define-dependencies.md)文章。

### <a name="role-assignment-resource-information"></a>角色指派資源資訊
角色指派資源，就必須指定的型別和名稱。

要注意的第一件事是資源的類型不是`Microsoft.Authorization/roleAssignments`就像在資源群組。  相反地，資源類型會遵循模式`{provider-namespace}/{resource-type}/providers/roleAssignments`。 資源類型將會在此情況下， `Microsoft.DevTestLab/labs/providers/roleAssignments`。

角色指派名稱本身必須是全域唯一的。  指派的名稱會使用模式`{labName}/Microsoft.Authorization/{newGuid}`。 `newGuid`是範本的參數值。 它可確保角色指派名稱是唯一的。 因為沒有範本函式來建立 Guid，您需要使用 GUID 產生器的任何工具自行產生的 GUID。  

在範本中，所定義的角色指派名稱`fullDevTestLabUserRoleName`變數。 從範本的哪一行是：

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>角色指派資源屬性
角色指派本身會定義三個屬性。 它需要`roleDefinitionId`， `principalId`，和`scope`。

### <a name="role-definition"></a>角色定義
角色定義識別碼是現有的角色定義的字串識別碼。 識別碼是在表單中的角色`/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`。 

訂用帳戶識別碼使用取得的`subscription().subscriptionId`樣板函式。  

您需要取得的角色定義`DevTest Labs User`內建角色。 若要取得的 GUID [DevTest Labs 使用者](../role-based-access-control/built-in-roles.md#devtest-labs-user)角色，您可以使用[角色指派的 REST API](/rest/api/authorization/roleassignments)或[Get AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) cmdlet。

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

變數區段中定義的角色識別碼，且名為`devTestLabUserRoleId`。 在範本中，角色識別碼設為：111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>主體識別碼
主體識別碼為 Active Directory 使用者、 群組或您想要為實驗室使用者新增至實驗室的服務主體的物件識別碼。 此範本會使用`ObjectId`做為參數。

您可以使用，以取得 ObjectId [Get-azurermaduser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0)、 [Get-azurermadgroup，或是[Get-azurermadserviceprincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell cmdlet。 這些 cmdlet 會傳回單一或之 Active Directory 物件的 ID 屬性，這是您所需要的物件識別碼的清單。 下列範例會示範如何在這家公司取得單一使用者的物件識別碼。

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

您也可以使用包含 Azure Active Directory PowerShell cmdlet [Get-msoluser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0)， [Get-msolgroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)，並[Get-msolserviceprincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0)。

### <a name="scope"></a>`Scope`
範圍會指定資源或資源群組應該適用於與角色指派。 如需資源，範圍的格式： `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`。 此範本會使用`subscription().subscriptionId`函式，以填滿`subscription-id`組件和`resourceGroup().name`樣板函式，以填滿`resource-group-name`組件。 使用這些函式表示的實驗室，您要將角色指派必須存在於目前的訂用帳戶和範本部署時所做的相同資源群組。 最後一個部分， `resource-name`，是實驗室的名稱。 透過在此範例中的範本參數，就會收到此值。 

角色中的範圍的範本： 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>部署範本
首先，建立參數檔案 (例如： azuredeploy.parameters.json) 中通過的 Resource Manager 範本參數的值。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

然後，使用[New-azurermresourcegroupdeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) PowerShell cmdlet 來部署 Resource Manager 範本。 下列範例命令會將個人、 群組或服務主體指派至實驗室的 DevTest Labs 使用者角色。

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

請務必請注意，必須是唯一的群組部署名稱和角色指派的 GUID。 如果您嘗試部署的資源指派具有非唯一的 GUID，則您會收到`RoleAssignmentUpdateNotPermitted`時發生錯誤。

如果您打算將數個 Active Directory 物件新增至您的實驗室的 DevTest Labs 使用者角色使用的範本數次，請考慮在您的 PowerShell 命令中使用動態物件。 下列範例會使用[New-guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0)指令程式來以動態方式指定資源群組部署名稱和角色指派的 GUID。

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>使用 Azure PowerShell
如簡介中所述，您會建立新的 Azure 角色指派，以將使用者加入**DevTest Labs 使用者**實驗室的角色。 在 PowerShell 中，您可以使用這樣[New-azurermroleassignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) cmdlet。 此 cmdlet 有許多選擇性參數，以允許彈性。 `ObjectId`， `SigninName`，或`ServicePrincipalName`可以指定為要授與其權限的物件。  

以下是將使用者新增至 DevTest Labs 使用者角色指定實驗室中的範例 Azure PowerShell 命令。

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

若要指定要在權限授與可以指定的資源的組合所`ResourceName`， `ResourceType`，`ResourceGroup`或是`scope`參數。 會使用參數的任何組合，提供足夠的資訊至 cmdlet，以唯一識別 Active Directory 物件 （使用者、 群組或服務主體），範圍 （資源群組或資源） 及角色定義。

## <a name="use-azure-command-line-interface-cli"></a>使用 Azure 命令列介面 (CLI)
在 Azure CLI 中，新增至實驗室的實驗室使用者由使用`az role assignment create`命令。 如需有關 Azure CLI cmdlet 的詳細資訊，請參閱 <<c0> [ 管理使用 RBAC 和 Azure CLI 的 Azure 資源的存取權](../role-based-access-control/role-assignments-cli.md)。

您可指定正在授與存取權的物件`objectId`， `signInName`，`spn`參數。 可以藉由識別的物件正在授與存取實驗室`scope`url 或組合`resource-name`， `resource-type`，和`resource-group`參數。

下列 Azure CLI 範例將示範如何將人員新增至 DevTest Labs 使用者角色中，指定的實驗室。  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>後續步驟
請參閱下列文章：

- [建立和管理使用 Azure CLI 的 DevTest Labs 虛擬機器](devtest-lab-vmcli.md)
- [使用 DevTest Labs 使用 Azure PowerShell 建立虛擬機器](devtest-lab-vm-powershell.md)
- [使用命令列工具來啟動和停止 Azure DevTest Labs 虛擬機器](use-command-line-start-stop-virtual-machines.md)

