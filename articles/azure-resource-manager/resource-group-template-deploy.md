---
title: 使用 PowerShell 與範本部署資源 | Microsoft Docs
description: 使用 Azure Resource Manager 和 Azure PowerShell，將資源部署至 Azure。 資源會定義在 Resource Manager 範本中。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2019
ms.author: tomfitz
ms.openlocfilehash: 3b9a35cb5a37aa17a2f7803f3e996d51bede81e0
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518553"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>使用 Resource Manager 範本與 Azure PowerShell 來部署資源

了解如何使用 Azure PowerShell 與 Resource Manager 範本，將您的資源部署至 Azure。 如需 Azure 解決方案部署和管理概念的詳細資訊，請參閱 [Azure Resource Manager 概觀](resource-group-overview.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment-scope"></a>部署範圍

您可以針對您的 Azure 訂用帳戶或訂用帳戶內的資源群組的部署。 在大部分情況下，您會針對部署至資源群組。 使用訂用帳戶部署來套用原則和角色指派，在訂用帳戶。 您也可以使用訂用帳戶部署來建立資源群組，並將資源部署到它。 根據部署的範圍，您可以使用不同的命令。

若要部署到**資源群組**，使用[新增 AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

若要部署到**訂用帳戶**，使用[新增 AzDeployment](/powershell/module/az.resources/new-azdeployment):

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

這篇文章中的範例會使用資源群組部署。 如需有關訂用帳戶部署的詳細資訊，請參閱[建立資源群組和資源的訂用帳戶層級](deploy-to-subscription.md)。

## <a name="prerequisites"></a>必要條件

您需要部署範本。 如果您還沒有其中一個，下載並儲存[範例範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)從 Azure 快速入門範本存放庫。 本文所使用的本機檔案名稱是 **c:\MyTemplates\azuredeploy.json**。

除非您使用 Azure Cloud shell 來部署範本時，您需要安裝 Azure PowerShell 並連線至 Azure:

- **在本機電腦上安裝 Azure PowerShell Cmdlet。** 如需詳細資訊，請參閱[開始使用 Azure PowerShell](/powershell/azure/get-started-azureps)。
- **使用 [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)** 來連線至 Azure。 如果您有多個 Azure 訂用帳戶，則可能還需要執行 [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext)。 如需詳細資訊，請參閱[使用多個 Azure 訂用帳戶](/powershell/azure/manage-subscriptions-azureps)。

## <a name="deploy-local-template"></a>部署本機範本

下列範例會建立資源群組，並會從本機電腦的範本部署。 資源群組的名稱只能包含英數字元、句點 (.)、底線、連字號及括弧。 最多可有 90 個字元。 不能以句點結束。

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

部署需要幾分鐘的時間才能完成。

## <a name="deploy-remote-template"></a>部署遠端的範本

您可能希望將 Resource Manager 範本儲存在外部位置，而不是儲存在您的本機電腦。 您可以將範本儲存在原始檔控制存放庫 (例如 GitHub) 中。 或者，您可以將它們儲存在 Azure 儲存體帳戶中，讓組織共用存取。

若要部署外部模板，请使用 TemplateUri 参数。 在範例中使用 URI 以部署來自 GitHub 的範例範本。

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

上述範例針對範本需要可公開存取 URI，這適用於大部分的案例，因為您的範本不應該包含機密資料。 如果您需要指定機密資料 (例如系統管理員密碼)，請將該值以安全參數傳遞。 不過，如果不希望將範本公開存取，您可以將它儲存在私人儲存體容器中加以保護。 如需部署需要共用存取簽章 (SAS) 權杖之範本的相關資訊，請參閱[使用 SAS 權杖部署私人範本](resource-manager-powershell-sas-token.md)。 若要進行教學課程，請參閱[教學課程：在 Resource Manager 範本部署中整合 Azure Key Vault](./resource-manager-tutorial-use-key-vault.md)。

## <a name="deploy-from-azure-cloud-shell"></a>從 Azure Cloud shell 部署

您可以使用 [Azure Cloud Shell](https://shell.azure.com) 來部署範本。 若要部署外部範本，請提供範本的 URI。 若要部署本機範本，您必須先將範本載入 Cloud Shell 的儲存體帳戶。 若要將檔案上傳到殼層，請從殼層視窗選取 [上傳/下載檔案] 功能表圖示。

若要開啟 Cloud Shell，請瀏覽至 [https://shell.azure.com](https://shell.azure.com)，或從下列程式碼區段選取 [試試看]：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

若要將程式碼貼到殼層中，請在殼層內按一下滑鼠右鍵，然後選取 [貼上]。

## <a name="redeploy-when-deployment-fails"></a>部署失敗時重新部署

當部署失敗時，您可以從部署記錄自動重新部署先前成功的部署。 若要指定重新部署，請在部署命令中使用 `-RollbackToLastDeployment` 或 `-RollBackDeploymentName` 參數。

若要使用這個選項，您的部署必須有唯一的名稱，以便在歷程記錄中進行識別。 如果您沒有唯一的名稱，則目前失敗的部署可能會覆寫歷程記錄中先前成功的部署。 您只可以使用此選項搭配根層級部署。 從巢狀範本部署不適用於重新部署。

若要重新部署最後一個成功的部署，請新增 `-RollbackToLastDeployment` 參數作為旗標。

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

若要重新部署特定部署，請使用 `-RollBackDeploymentName` 參數，並提供部署的名稱。

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

指定的部署必須成功。

## <a name="pass-parameter-values"></a>傳遞參數值

若要傳遞參數值，您可以使用內嵌參數或參數檔案。 本文中的前述範例顯示的是內嵌參數。

### <a name="inline-parameters"></a>內嵌參數

若要傳遞內嵌參數，請使用 `New-AzResourceGroupDeployment` 命令提供參數名稱。 例如，若要將字串和陣列傳遞至範本，請使用：

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

您也可以取得檔案內容，並將該內容當作內嵌參數提供。

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

當您需要提供組態值時，從檔案取得參數值會很有幫助。 例如，您可以提供 [Linux 虛擬機器的 cloud-init 值](../virtual-machines/linux/using-cloud-init.md)。

### <a name="parameter-files"></a>參數檔案

相對於在您的指令碼中將參數做為內嵌值傳遞，使用包含該參數值的 JSON 檔案可能較為容易。 參數檔案可以是本機檔案或具有可存取 URI 的外部檔案。

參數檔必須是下列格式︰

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

請注意，參數區段包含符合於範本中所定義之參數 (storageAccountType) 的參數名稱。 參數檔案包含參數的值。 這個值會在部署期間自動傳遞至範本。 您可以建立多個參數檔案，然後視情況傳入適當的參數檔案。

複製上述的範例，然後將它另存為名叫 `storage.parameters.json` 的檔案。

若要傳遞本機參數檔案，請使用 **TemplateParameterFile** 參數︰

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

若要傳遞外部參數檔案，請使用 **TemplateParameterUri** 參數︰

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

### <a name="parameter-precedence"></a>參數優先順序

您可以在相同的部署作業中使用內嵌參數和本機參數檔案。 例如，您可以在部署期間指定本機參數檔案中的某些值，並新增其他內嵌值。 如果您同時為本機檔案中和內嵌的參數提供值，內嵌值的優先順序較高。

不過，當使用外部參數檔案時，您無法傳遞內嵌或本機檔案的其他值。 當您指定 **TemplateParameterUri** 參數中的參數檔案時，所有內嵌參數都會被忽略。 請提供外部檔案中的所有參數值。 如果您的範本包含不能包含在參數檔案中的機密值，請將該值新增至金鑰保存庫，或以動態方式提供所有內嵌參數值。

### <a name="parameter-name-conflicts"></a>參數名稱衝突

如果您範本所含的參數名稱與 PowerShell 命令中的其中一個參數一樣，PowerShell 會以加上後置 **FromTemplate** 的方式呈現您範本中的參數。 例如，範本中名為 **ResourceGroupName** 的參數會與 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) Cmdlet 中的 **ResourceGroupName** 參數發生衝突。 系統會提示您為 **ResourceGroupNameFromTemplate** 提供值。 一般而言，在為參數命名時，請勿使用與部署作業所用參數相同的名稱，以避免發生這種混淆的情形。

## <a name="test-template-deployments"></a>測試範本部署

若要測試您的範本和參數值而不實際部署任何資源，請使用 [Test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment)。 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

如果未偵測到錯誤，命令即完成，不會有回應。 如果偵測到錯誤，命令會傳回錯誤訊息。 例如，針對儲存體帳戶 SKU 傳遞不正確的值，則會傳回下列錯誤：

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

如果您的範本有語法錯誤，命令會傳回錯誤，指出無法剖析範本。 訊息會指出剖析錯誤的行號和位置。

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>後續步驟

- 若要安全地在多個區域推出您的服務，請參閱 [Azure 部署管理員](deployment-manager-overview.md)。
- 若要指定如何處理存在於資源群組中、但尚未定義於範本中的資源，請參閱 [Azure Resource Manager 部署模式](deployment-modes.md)。
- 若要了解如何在您的範本中定義參數，請參閱[了解 Azure Resource Manager 範本的結構和語法](resource-group-authoring-templates.md)。
- 如需部署需要 SAS 權杖之範本的詳細資訊，請參閱[使用 SAS 權杖部署私人範本](resource-manager-powershell-sas-token.md)。
