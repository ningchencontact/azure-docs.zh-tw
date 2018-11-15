---
title: 使用 Azure Resource Manager 範本建立服務匯流排傳訊命名空間 | Microsoft Docs
description: 使用 Azure Resource Manager 範本來建立服務匯流排傳訊命名空間
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/06/2018
ms.author: spelluru
ms.openlocfilehash: c616ad86e6846800d214feeaf100f63e311f78b0
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282725"
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本來建立服務匯流排命名空間
在本快速入門中，您會建立一個 Azure Resource Manager 範本，此範本會建立一個**訊息**類型且具有**標準** SKU 的服務匯流排命名空間。 本文也會定義針對部署執行而指定的參數。 您可以直接在自己的部署中使用此範本，或自訂此範本以符合您的需求。 如需關於建立範本的詳細資訊，請參閱[編寫 Azure Resource Manager 範本][Authoring Azure Resource Manager templates]。 如需完整的範本，請參閱 GitHub 上的 [服務匯流排命名空間範本][Service Bus namespace template]。

> [!NOTE]
> 下列 Azure Resource Manager 範本可供下載和部署。 
> 
> * [建立服務匯流排命名空間與佇列](service-bus-resource-manager-namespace-queue.md)
> * [建立服務匯流排命名空間與主題和訂用帳戶](service-bus-resource-manager-namespace-topic.md)
> * [建立服務匯流排命名空間與佇列和授權規則](service-bus-resource-manager-namespace-auth-rule.md)
> * [建立服務匯流排命名空間與主題、訂用帳戶和規則](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> 若要檢查最新的範本，請造訪 [Azure 快速入門範本][Azure Quickstart Templates]資源庫並搜尋「服務匯流排」。

## <a name="quick-deployment"></a>快速部署
若要在不撰寫任何 JSON 及執行 PowerShell/CLI 命令的情況下執行範例，請選取下列按鈕：

[![部署至 Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

若要手動建立及部署範本，請瀏覽本文中的下列各節。

## <a name="prerequisites"></a>必要條件
若要完成本快速入門，您需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。

如果您想要使用 **Azure PowerShell** 來部署 Resource Manager 範本，請[安裝 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0)。

如果您想要使用 **Azure CLI** 來部署 Resource Manager 範本，請[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-the-resource-manager-template-json"></a>建立 Resource Manager 範本 JSON 
使用以下內容來建立名為 **MyServiceBusNamespace.json** 的 JSON 檔案： 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusSku": {
            "type": "string",
            "allowedValues": [
                "Basic",
                "Standard",
                "Premium"
            ],
            "defaultValue": "Standard",
            "metadata": {
                "description": "The messaging tier for service Bus namespace"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('serviceBusSku')]"
            }
        }
    ]
}
```

此範本會建立標準「服務匯流排」命名空間。

## <a name="create-the-parameters-json"></a>建立參數 JSON
您在上一個步驟中建立的範本包含一個名為 `Parameters` 的區段。 您需針對會依據所要部署之專案或依據目標環境而變更的值，定義參數。 此範本會定義下列參數：**serviceBusNamespaceName**、**serviceBusSku** 及 **location**。 若要深入了解「服務匯流排」的 SKU，請參閱 [服務匯流排 SKU](https://azure.microsoft.com/pricing/details/service-bus/) 以進行建立作業。

使用以下內容來建立名為 **MyServiceBusNamespace-Parameters.json** 的 JSON 檔案： 

> [!NOTE] 
> 為您的「服務匯流排」命名空間指定名稱。 


```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "value": "<Specify a name for the Service Bus namespace>"
    },
    "serviceBusSku": {
      "value": "Standard"
    },
    "location": {
        "value": "East US"
    }
  }
}
```


## <a name="use-azure-powershell-to-deploy-the-template"></a>使用 Azure PowerShell 來部署範本

### <a name="sign-in-to-azure"></a>登入 Azure
1. 啟動 Azure PowerShell

2. 執行下列命令以登入 Azure：

   ```azurepowershell
   Login-AzureRmAccount
   ```
3. 發出下列命令以設定目前的訂用帳戶內容：

   ```azurepowershell
   Select-AzureRmSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="deploy-resources"></a>部署資源
若要使用 Azure PowerShell 來部署資源，請切換至您儲存 JSON 檔案的資料夾，然後執行下列命令：

> [!IMPORTANT]
> 執行命令之前，請指定 Azure 資源群組的名稱作為 $resourceGroupName 的值。 

1. 為資源群組名稱宣告一個變數，並為它指定一個值。 

    ```azurepowershell
    $resourceGroupName = "<Specify a name for the Azure resource group>"
    ```
2. 建立 Azure 資源群組。

    ```azurepowershell
    New-AzureRmResourceGroup $resourceGroupName -location 'East US'
    ```
3. 部署 Resource Manager 範本。 指定部署本身、資源群組、範本之 JSON 檔案、參數之 JSON 檔案的名稱

    ```azurepowershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyServiceBusNamespace.json -TemplateParameterFile MyServiceBusNamespace-Parameters.json
    ```

## <a name="use-azure-cli-to-deploy-the-template"></a>使用 Azure CLI 來部署範本

### <a name="sign-in-to-azure"></a>登入 Azure

1. 執行下列命令以登入 Azure：

    ```azurecli
    az login
    ```
2. 設定目前的訂用帳戶環境。 以您要使用的 Azure 訂用帳戶名稱取代 `MyAzureSub`：

    ```azurecli
    az account set --subscription <Name of your Azure subscription>
    ``` 

### <a name="deploy-resources"></a>部署資源
若要使用 Azure CLI 來部署資源，請切換到含有 JSON 檔案的資料夾，然後執行下列命令：

> [!IMPORTANT]
> 在 az group create 命令中指定 Azure 資源群組的名稱。 .

1. 建立 Azure 資源群組。 
    ```azurecli
    az group create --name <YourResourceGroupName> --location eastus
    ```

2. 部署 Resource Manager 範本。 指定資源群組、部署、範本之 JSON 檔案、參數之 JSON 檔案的名稱。

    ```azurecli
    az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyServiceBusNamespace.json --parameters @MyServiceBusNamespace-Parameters.json
    ```

## <a name="next-steps"></a>後續步驟
在本文中，您已建立「服務匯流排」命名空間。 請參閱其他快速入門以了解如何建立佇列、主題/訂用帳戶，以及如何使用它們： 

- [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
- [開始使用服務匯流排主題](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: service-bus-pricing-billing.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
