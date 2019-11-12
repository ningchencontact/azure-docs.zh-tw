---
title: 使用 Azure Resource Manager 範本建立工作區
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Resource Manager 範本來建立新的 Azure Machine Learning 工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 11/04/2019
ms.custom: seoapril2019
ms.openlocfilehash: 8bcfb80d42e7cd1fad6ff4c04415bd8627a3293e
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932150"
---
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>使用 Azure Resource Manager 範本來建立 Azure Machine Learning 的工作區

在本文中，您將瞭解使用 Azure Resource Manager 範本建立 Azure Machine Learning 工作區的數種方式。 Resource Manager 範本可讓您輕鬆地以單一、協調的作業建立資源。 範本是 JSON 文件，其定義部署所需的資源。 它也可以指定部署參數。 參數用來在使用範本時提供輸入值。

如需詳細資訊，請參閱 [使用 Azure 資源管理員範本部署應用程式](../../azure-resource-manager/resource-group-template-deploy.md)。

## <a name="prerequisites"></a>先決條件

* **Azure 訂用帳戶**。 如果您沒有帳戶，請試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* 若要從 CLI 使用範本，您需要 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) 或 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="resource-manager-template"></a>Resource Manager 範本

下列 Resource Manager 範本可以用來建立 Azure Machine Learning 工作區和相關聯的 Azure 資源：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus",
      "allowedValues": [
        "eastus",
        "eastus2",
        "southcentralus",
        "southeastasia",
        "westcentralus",
        "westeurope",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku":{
      "type": "string",
      "defaultValue": "basic",
        "allowedValues": [
          "basic",
          "enterprise"
        ],
        "metadata": {
          "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
        }
    }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
    "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2015-05-01",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2017-10-01",
      "name": "[variables('containerRegistryName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "adminUserEnabled": true
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]",
        "[resourceId('Microsoft.ContainerRegistry/registries', variables('containerRegistryName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
        "tier": "[parameters('sku')]",
        "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
      }
    }
  ]
}
```

此範本會建立下列 Azure 服務：

* Azure 資源群組
* Azure 儲存體帳戶
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning 工作區

資源群組是保存服務的容器。 各種服務是 Azure Machine Learning 工作區的必要項。

範例範本有兩個參數：

* **位置**是將建立資源群組和服務的位置。

    範本將會針對大部分的資源使用您選取的位置。 例外狀況是 Application Insights 服務，該服務在其他服務可用的所有位置都無法使用。 如果您選取的位置無法使用，服務會建立在美國中南部位置。

* **工作區名稱**是 Azure Machine Learning 工作區的易記名稱。

    其他服務的名稱會隨機產生。

> [!TIP]
> 雖然與此檔相關聯的範本會建立新的 Azure Container Registry，但您也可以建立新的工作區，而不需要建立容器登錄。 如果工作區中有容器登錄，則當您執行需要容器登錄的作業時，將會建立一個。 例如，定型或部署模型。
>
> 您也可以在 [Azure Resource Manager] 範本中參考現有的容器登錄或儲存體帳戶，而不是建立一個新的。

如需範本的詳細資訊，請參閱下列文章：

* [編寫 Azure 資源管理員範本](../../azure-resource-manager/resource-group-authoring-templates.md)
* [使用 Azure Resource Manager 範本部署應用程式](../../azure-resource-manager/resource-group-template-deploy.md)
* [Microsoft.MachineLearningServices resource 類型](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

1. 遵循[從自訂範本部署資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template)的步驟。 當您看見 [編輯範本] 畫面時，貼入此文件的範本。
1. 選取 [儲存] 以使用範本。 提供下列資訊，並同意列出的條款及條件：

   * 訂用帳戶：選取要用於這些資源的 Azure 訂用帳戶。
   * 資源群組：選取或建立包含服務的資源群組。
   * 工作區名稱：要用於將建立之 Azure Machine Learning 工作區的名稱。 工作區名稱必須介於 3 到 33 個字元之間。 只能包含英數字元和 '-'。
   * 位置：選取將建立資源的位置。

如需詳細資訊，請參閱[從自訂範本部署資源](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)。

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

這個範例假設您已將範本儲存到目前目錄中名為 `azuredeploy.json` 的檔案：

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

如需詳細資訊，請參閱[使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../../azure-resource-manager/resource-group-template-deploy.md)和[使用 SAS 權杖和 Azure PowerShell 部署私用 Resource Manager 範本](../../azure-resource-manager/resource-manager-powershell-sas-token.md)。

## <a name="use-azure-cli"></a>使用 Azure CLI

這個範例假設您已將範本儲存到目前目錄中名為 `azuredeploy.json` 的檔案：

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus
```

如需詳細資訊，請參閱[使用 Resource Manager 範本與 Azure CLI 來部署資源](../../azure-resource-manager/resource-group-template-deploy-cli.md)和[使用 SAS 權杖和 Azure CLI 部署私用 Resource Manager 範本](../../azure-resource-manager/resource-manager-cli-sas-token.md)。

## <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault 存取原則和 Azure Resource Manager 範本

當您使用 Azure Resource Manager 範本來建立工作區和相關聯的資源（包括 Azure Key Vault）時，會多次。 例如，使用範本多次，並以相同的參數作為持續整合和部署管線的一部分。

大部分透過範本的資源建立作業都是等冪的，但 Key Vault 會在每次使用範本時清除存取原則。 清除存取原則會中斷對使用它之任何現有工作區的 Key Vault 存取。 例如，Azure Notebooks VM 的停止/建立功能可能會失敗。  

若要避免這個問題，建議您採用下列其中一種方法：

* 不要針對相同的參數多次部署範本。 或刪除現有的資源，然後再使用此範本重新建立它們。
  
* 檢查 Key Vault 的存取原則，然後使用這些原則來設定範本的 accessPolicies 屬性。
* 檢查 Key Vault 資源是否已存在。 如果有，請不要透過範本重新建立它。 例如，新增可讓您停用建立 Key Vault 資源的參數（如果已經存在）。

## <a name="next-steps"></a>後續步驟

* [使用 Resource Manager 範本和 Resource Manager REST API 部署資源](../../azure-resource-manager/resource-group-template-deploy-rest.md)。
* [透過 Visual Studio 建立與部署 Azure 資源群組](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。
