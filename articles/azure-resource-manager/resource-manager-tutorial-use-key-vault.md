---
title: 在 Resource Manager 範本部署中整合 Azure Key Vault | Microsoft Docs
description: 了解如何使用 Azure Key Vault 在 Resource Manager 範本部署期間傳遞安全的參數值
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 0d78e6eaca708073c3a216507b320fe8783a25b6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239248"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>教學課程：在 Resource Manager 範本部署中整合 Azure Key Vault

了解如何從 Azure Key Vault 擷取祕密，並且在 Resource Manager 部署期間傳遞祕密作為參數。 您只參考其金鑰保存庫識別碼，因此該值絕不會公開。 如需詳細資訊，請參閱[在部署期間使用 Azure Key Vault 以傳遞安全的參數值](./resource-manager-keyvault-parameter.md)。

在[設定資源部署順序](./resource-manager-tutorial-create-templates-with-dependent-resources.md)教學課程中，您建立了虛擬機器。 您需要提供虛擬機器系統管理員的使用者名稱和密碼。 您可以不提供密碼，而是將密碼預先儲存在 Azure Key Vault，然後自訂範本以在部署期間從金鑰保存庫擷取密碼。

![Resource Manager 範本 Key Vault 整合圖](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 準備金鑰保存庫
> * 開啟快速入門範本
> * 編輯參數檔案
> * 部署範本
> * 驗證部署
> * 清除資源

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必要條件

若要完成本文，您需要：

* [Visual Studio Code](https://code.visualstudio.com/) 搭配 [Resource Manager Tools 擴充功能](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)。
* 為了提高安全性，請使用為虛擬機器系統管理員帳戶產生的密碼。 以下是用於產生密碼的範例：

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    請確認所產生的密碼符合虛擬機器的密碼需求。 每個 Azure 服務都有特定的密碼需求。 如需 VM 密碼需求，請參閱[建立 VM 時的密碼需求為何？](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。

## <a name="prepare-a-key-vault"></a>準備金鑰保存庫

在本節中，您會建立金鑰保存庫，並在金鑰保存庫中新增祕密，以便可以在部署範本時擷取祕密。 有許多方式可以建立金鑰保存庫。 在本教學課程中，您會使用 Azure PowerShell 來部署 [Resource Manager 範本](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json)。 此範本會：

* 在啟用 `enabledForTemplateDeployment` 屬性的情況下建立金鑰保存庫。 此屬性必須為 true，範本部署程序才能存取此金鑰保存庫中定義的祕密。
* 將秘密新增至金鑰保存庫。  此祕密會儲存虛擬機器的系統管理員密碼。

> [!NOTE]
> 如果您 (作為要部署虛擬機器範本的使用者) 不是金鑰保存庫的擁有者或參與者，則金鑰保存庫的擁有者或參與者必須准許您存取金鑰保存庫的 Microsoft.KeyVault/vaults/deploy/action 權限。 如需詳細資訊，請參閱[在部署期間使用 Azure Key Vault 以傳遞安全的參數值](./resource-manager-keyvault-parameter.md)。

若要執行下列 PowerShell 指令碼，請選取 [試試看]  來開啟 Cloud Shell。 若要貼上指令碼，請以滑鼠右鍵按一下 Shell 窗格，然後選取 [貼上]  。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue
```

幾項重要資訊：

* 資源群組名稱是附加 **rg** 的專案名稱。 若要能夠更加容易地[清除本教學課程所建立的資源](#clean-up-resources)，請在[部署下一個範本](#deploy-the-template)時使用相同的專案名稱和資源群組名稱。
* 祕密名稱的預設名稱是 **vmAdminPassword**。 其會硬式編碼在範本中。
* 若要讓範本能夠擷取祕密，您必須為金鑰保存庫啟用稱為**為範本部署啟用對 Azure Resource Manager 的存取**的存取原則。 範本中會啟用此原則。 如需此存取原則的詳細資訊，請參閱[部署金鑰保存庫和祕密](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets)。

範本有一個稱為 **keyVaultId** 的輸出值。 請記下這個值。 當您部署虛擬機器時，您會需要此識別碼。 資源識別碼格式為：

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

當您複製並貼上識別碼時，識別碼可能會分成多行。 您必須將這幾行合併，並移除多餘的空格。

若要驗證部署，請在相同的 Shell 窗格中執行下列 PowerShell 命令，來以純文字擷取祕密。 此命令會使用先前的 PowerShell 指令碼中定義的 $keyVaultName 變數，因此只能在同一個殼層工作階段中起作用。

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

現在，您已準備好金鑰保存庫和祕密，下列各節會顯示如何自訂現有範本以在部署期間擷取祕密。

## <a name="open-a-quickstart-template"></a>開啟快速入門範本

Azure 快速入門範本是 Resource Manager 範本的存放庫。 您可以尋找範例範本並加以自訂，而不要從頭建立範本。 本教學課程中使用的範本名為[部署簡單的 Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)。

1. 在 Visual Studio Code 中，選取 [檔案]  >[開啟檔案]  。
2. 在 [檔案名稱]  中，貼上下列 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. 選取 [開啟]  以開啟檔案。 這與[教學課程：建立具有相依資源的 Azure Resource Manager 範本](./resource-manager-tutorial-create-templates-with-dependent-resources.md)中使用的案例相同。
4. 範本中定義了五項資源：

   * `Microsoft.Storage/storageAccounts` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)。
   * `Microsoft.Network/publicIPAddresses` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)。
   * `Microsoft.Network/virtualNetworks` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)。
   * `Microsoft.Network/networkInterfaces` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)。
   * `Microsoft.Compute/virtualMachines` 。 請參閱[範本參考](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)。

     自訂範本之前，最好能初步了解範本。
5. 選取 [檔案]  >[另存新檔]  ，以名稱 **azuredeploy.json** 將檔案的複本儲存至您的本機電腦。
6. 重複步驟 1-4 以開啟下列 URL，然後將檔案儲存為 **azuredeploy.parameters.json**。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>編輯參數檔案

您完全不必對範本檔案進行任何變更。

1. 在 Visual Studio Code 中開啟 **azuredeploy.parameters.json** (如果尚未開啟)。
2. 將 **adminPassword** 參數更新為：

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > 使用在上一個程序所建立金鑰保存庫的資源識別碼取代 **id** 值。

    ![整合金鑰保存庫與 Resource Manager 範本虛擬機器部署參數檔案](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. 提供下列值：

    * **adminUsername**：虛擬機器系統管理員帳戶的名稱。
    * **dnsLabelPrefix**：dnsLabelPrefix 的名稱。

    請參閱上一個螢幕擷取畫面中的範例。

4. 儲存變更。

## <a name="deploy-the-template"></a>部署範本

請依照[部署範本](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)中的指示來部署範本。 您必須將 **azuredeploy.json** 與 **azuredeploy.parameters.json** 上傳到 Cloud Shell，然後使用下列 PowerShell 指令碼來部署範本：

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

當您部署範本時，請使用相同的資源群組作為金鑰保存庫。 這可讓您更為輕鬆地清除資源。 您只需要刪除一個資源群組，而非兩個資源群組。

## <a name="validate-the-deployment"></a>驗證部署

在您成功部署虛擬機器之後，請使用在金鑰保存庫中儲存的密碼來測試登入。

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [資源群組識別碼]  /**YourResourceGroupName >** /**simpleWinVM**
3. 選取頂端的 [連線]  。
4. 選取 [下載 RDP 檔案]  ，然後依照指示，使用金鑰保存庫中儲存的密碼來登入虛擬機器。

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您從 Azure Key Vault 擷取了祕密，並且在範本部署中使用此祕密。  若要了解如何建立連結的範本，請參閱：

> [!div class="nextstepaction"]
> [建立連結的範本](./resource-manager-tutorial-create-linked-templates.md)
