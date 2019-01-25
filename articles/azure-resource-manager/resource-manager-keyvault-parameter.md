---
title: 金鑰保存庫密碼與 Azure Resource Manager 範本 | Microsoft Docs
description: 示範如何在部署期間從金鑰保存庫中傳遞密碼做為參數。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: a885fda23bb76091705ebe388f40a6eae7b56416
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2019
ms.locfileid: "54351504"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>在部署期間使用 Azure Key Vault 以傳遞安全的參數值

當您需要在部署期間，傳送安全值 (例如密碼) 做為參數時，您可以從 [Azure Key Vault](../key-vault/key-vault-whatis.md) 擷取值。 您可以藉由參考金鑰保存庫和參數檔案中的密碼來擷取值。 您只參考其金鑰保存庫識別碼，因此該值絕不會公開。 金鑰保存庫可以存在於與您部署的資源群組的不同訂用帳戶中。

## <a name="deploy-a-key-vault-and-secret"></a>部署金鑰保存庫和密碼

若要建立金鑰保存庫與密碼，請使用 Azure CLI 或 PowerShell。 `enabledForTemplateDeployment` 是金鑰保存庫屬性。 若要從 Resource Manager 部署中存取此 Key Vault 內的秘密，`enabledForTemplateDeployment` 必須是 `true`。 

下列範例 Azure PowerShell 和 Azure CLI 指令碼將示範如何建立 Key Vault 和秘密。

對於 Azure CLI，請使用：

```azurecli-interactive
keyVaultName='{your-unique-vault-name}'
resourceGroupName='{your-resource-group-name}'
location='centralus'
userPrincipalName='{your-email-address-associated-with-your-subscription}'

# Create a resource group
az group create --name $resourceGroupName --location $location

# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

# Create a secret with the name, vmAdminPassword
password=$(openssl rand -base64 32)
echo $password
az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
```

對於 PowerShell，請使用：

```azurepowershell-interactive
$keyVaultName = "{your-unique-vault-name}"
$resourceGroupName="{your-resource-group-name}"
$location='Central US'
$userPrincipalName='{your-email-address-associated-with-your-subscription}'

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

New-AzureRmKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list

$password = openssl rand -base64 32
echo $password
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretvalue
```

如果要在 Cloud Shell 以外執行 PowerShell 指令碼，請改為使用下列命令來產生密碼：

```powershell
Add-Type -AssemblyName System.Web
[System.Web.Security.Membership]::GeneratePassword(16,3)
```

如需使用 Resource Manager 範本：請參閱[教學課程：在 Resource Manager 範本部署中整合 Azure Key Vault](./resource-manager-tutorial-use-key-vault.md#prepare-the-key-vault)。

> [!NOTE]
> 每個 Azure 服務都有特定的密碼需求。 例如，Azure 虛擬機器的需求可在[建立 VM 時的密碼需求為何？](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)上找到。

## <a name="enable-access-to-the-secret"></a>啟用密碼的存取權

除了將 `enabledForTemplateDeployment` 設定為 `true` 以外，部署範本的使用者還必須對包含 Key Vault 的範圍 (包括資源群組和 Key Vault 本身) 具備 `Microsoft.KeyVault/vaults/deploy/action` 權限。 [擁有者](../role-based-access-control/built-in-roles.md#owner)和[參與者](../role-based-access-control/built-in-roles.md#contributor)角色皆可授與此權限。 如果您建立了 Key Vault，您就是擁有者，因此就會有此權限。 如果 Key Vault 屬於不同的訂用帳戶，則必須由 Key Vault 的擁有者授與存取權。

下列程序說明如何建立具有最低權限的角色，以及如何指派使用者
1. 建立自訂角色定義 JSON 檔案：

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    請將 "00000000-0000-0000-0000-000000000000" 取代為需要部署範本的使用者所具備的訂用帳戶識別碼。

2. 使用 JSON 檔案建立新的角色：

    ```azurepowershell
    $resourceGroupName= "<Resource Group Name>" # the resource group which contains the Key Vault
    $userPrincipalName = "<Email Address of the deployment operator>"
    New-AzureRmRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzureRmRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    `New-AzureRmRoleAssignment` 範例會在資源群組層級上將自訂角色指派給使用者。  

將金鑰保存庫與[受控應用程式](../managed-applications/overview.md)的範本搭配使用時，您必須授與**設備資源提供者**服務主體的存取權。 如需詳細資訊，請參閱[在部署 Azure 受控應用程式時存取金鑰保存庫密碼](../managed-applications/key-vault-access.md) (英文)。

## <a name="reference-a-secret-with-static-id"></a>使用靜態識別碼參考密碼

接收金鑰保存庫密碼的範本與其他任何範本都很像。 這是因為**您參考了參數檔案中的金鑰保存庫，而非範本。** 下圖顯示參數檔案如何參考祕密，並將該值傳遞至範本。

![靜態識別碼](./media/resource-manager-keyvault-parameter/statickeyvault.png)

例如，[下列範本](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json)部署了包含系統管理員密碼的 SQL 資料庫。 密碼參數會設定為安全字串。 但是，範本並未指定該值來自何處。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

現在，請為前述範本建立參數檔案。 在參數檔案中，指定符合範本中參數名稱的參數。 針對參數值，參考來自金鑰保存庫的密碼。 您可以藉由傳遞金鑰保存庫的資源識別碼和密碼的名稱來參考密碼。 在[下列參數檔案](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.parameters.json)中，金鑰保存庫祕密必須已經存在，而且您要針對其資源識別碼提供靜態值。 在本機複製這個檔案，並設定訂用帳戶識別碼、保存庫名稱及 SQL 伺服器名稱。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "examplesecret"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

如果需要使用目前版本以外的祕密版本，請使用 `secretVersion` 屬性。

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

現在可部署範本，並在參數檔案中傳遞。 您可以使用 GitHub 中的範例範本，但您必須使用其值設定為您的環境的本機參數檔案。

對於 Azure CLI，請使用：

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

對於 PowerShell，請使用：

```powershell-interactive
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>使用動態識別碼參考密碼

上一節已說明如何從參數傳遞金鑰保存庫秘密的靜態資源識別碼。 不過，在某些情況下，您需要參考會隨目前的部署而變化的金鑰保存庫密碼。 或者，您可能想要將參數值直接傳至範本，而不要在參數檔案中建立參考參數。 在任一情況下，您都可以使用連結的範本，以動態方式產生金鑰保存庫秘密的資源識別碼。

由於參數檔中不允許使用範本運算式，因此您無法在參數檔中以動態方式產生資源識別碼。 

在父代範本中，您必須新增連結的範本，並傳入包含動態產生的資源識別碼的參數。 下圖顯示連結的範本中的參數如何參考祕密。

![動態識別碼](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

[下列範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id)會動態建立金鑰保存庫識別碼，並將它當作參數傳遞。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "The location where the resources will be deployed."
            }
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "The name of the keyvault that contains the secret."
            }
        },
        "secretName": {
            "type": "string",
            "metadata": {
                "description": "The name of the secret."
            }
        },
        "vaultResourceGroupName": {
            "type": "string",
            "metadata": {
                "description": "The name of the resource group that contains the keyvault."
            }
        },
        "vaultSubscription": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "The name of the subscription that contains the keyvault."
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located. When the template is deployed using the accompanying scripts, a private location in the subscription will be used and this value will be automatically generated."
            },
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/"
        },
        "_artifactsLocationSasToken": {
            "type": "securestring",
            "metadata": {
                "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated."
            },
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "[uri(parameters('_artifactsLocation'), concat('./nested/sqlserver.json', parameters('_artifactsLocationSasToken')))]"
                },
                "parameters": {
                    "location": {
                        "value": "[parameters('location')]"
                    },
                    "adminLogin": {
                        "value": "ghuser"
                    },
                    "adminPassword": {
                        "reference": {
                            "keyVault": {
                                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                            },
                            "secretName": "[parameters('secretName')]"
                        }
                    }
                }
            }
        }
    ],
    "outputs": {
        "sqlFQDN": {
            "type": "string",
            "value": "[reference('dynamicSecret').outputs.sqlFQDN.value]"
        }
    }
}
```

請部署上述範本，並提供參數值。 您可以使用 GitHub 中的範例範本，但您必須為您的環境提供參數值。

對於 Azure CLI，請使用：

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=<your-vault> vaultResourceGroupName=examplegroup secretName=examplesecret
```

對於 PowerShell，請使用：

```powershell
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName <your-vault> -vaultResourceGroupName examplegroup -secretName examplesecret
```

## <a name="next-steps"></a>後續步驟
* 如需金鑰保存庫的一般資訊，請參閱[開始使用 Azure 金鑰保存庫](../key-vault/key-vault-get-started.md)。
* 如需參考金鑰密碼的完整範例，請參閱 [金鑰保存庫範例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)。
