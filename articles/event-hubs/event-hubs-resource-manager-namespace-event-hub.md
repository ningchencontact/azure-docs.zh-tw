---
title: 使用消費者群組建立事件中樞 - Azure 事件中樞 | Microsoft Docs
description: 使用 Azure Resource Manager 範本來建立含有事件中樞和取用者群組的事件中樞命名空間
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/16/2018
ms.author: shvija
ms.openlocfilehash: 39c92c870991ce2398b27efd189f1219777afdd7
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2019
ms.locfileid: "54425317"
---
# <a name="quickstart-create-an-event-hub-using-azure-resource-manager-template"></a>快速入門：使用 Azure Resource Manager 範本建立事件中樞
Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 如需事件中樞的詳細概觀，請參閱[事件中樞概觀](event-hubs-about.md)和[事件中樞功能](event-hubs-features.md)。

在此快速入門中，您會使用 Azure Resource Manager 範本建立事件中樞。 您可以使用 Azure Resource Manager 範本來建立一個類型為[事件中樞](event-hubs-what-is-event-hubs.md)、含有一個事件中樞和一個取用者群組的的命名空間。 本文說明如何定義要部署哪些資源，以及如何定義執行部署時所指定的參數。 您可以直接在自己的部署中使用此範本，或自訂此範本以符合您的需求。 如需關於建立範本的資訊，請參閱[編寫 Azure Resource Manager 範本][Authoring Azure Resource Manager templates]。 如需要在範本中使用的 JSON 語法和屬性，請參閱 [Microsoft.EventHub 資源類型](/azure/templates/microsoft.eventhub/allversions)。

> [!NOTE]
> 如需完整的範本，請參閱 GitHub 上的[事件中樞和取用者群組範本][Event Hub and consumer group template]。 除了事件中樞命名空間和事件中樞，此範本還建立了取用者群組。 若要檢查最新的範本，請造訪 [Azure 快速入門範本][Azure Quickstart Templates] 資源庫並搜尋事件中樞。

## <a name="prerequisites"></a>必要條件
若要完成本快速入門，您需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。

如果您想要使用 **Azure PowerShell** 來部署 Resource Manager 範本，請[安裝 Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-5.7.0)。

如果您想要使用 **Azure CLI** 來部署 Resource Manager 範本，請[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-the-resource-manager-template-json"></a>建立 Resource Manager 範本 JSON
使用下列內容建立名為 MyEventHub.json 的 JSON 檔案，並將該檔案儲存到資料夾 (例如：C:\EventHubsQuickstarts\ResourceManagerTemplate)。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventhub-namespace-name": {
            "type": "String"
        },
        "eventhub_name": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.EventHub/namespaces",
            "sku": {
                "name": "Standard",
                "tier": "Standard",
                "capacity": 1
            },
            "name": "[parameters('eventhub-namespace-name')]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "tags": {},
            "scale": null,
            "properties": {
                "isAutoInflateEnabled": false,
                "maximumThroughputUnits": 0
            },
            "dependsOn": []
        },
        {
            "type": "Microsoft.EventHub/namespaces/eventhubs",
            "name": "[concat(parameters('eventhub-namespace-name'), '/', parameters('eventhub_name'))]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "scale": null,
            "properties": {
                "messageRetentionInDays": 7,
                "partitionCount": 1,
                "status": "Active"
            },
            "dependsOn": [
                "[resourceId('Microsoft.EventHub/namespaces', parameters('eventhub-namespace-name'))]"
            ]
        }
    ]
}
```

## <a name="create-the-parameters-json"></a>建立參數 JSON
建立名為 MyEventHub-Parameters.json 的 JSON 檔案，其中包含 Azure Resource Manager 範本的參數。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
        "eventhub-namespace-name": {
            "value": "<specify a name for the event hub namespace>"
        },
        "eventhub_name": {
            "value": "<Specify a name for the event hub in the namespace>"
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

### <a name="provision-resources"></a>佈建資源
若要使用 Azure PowerShell 部署/佈建資源，請切換到 C:\EventHubsQuickStart\ARM\ 資料夾，執行下列命令：

> [!IMPORTANT]
> 執行命令之前，請指定 Azure 資源群組的名稱作為 $resourceGroupName 的值。 

```azurepowershell
$resourceGroupName = "<Specify a name for the Azure resource group>"

# Create an Azure resource group
New-AzureRmResourceGroup $resourceGroupName -location 'East US'

# Deploy the Resource Manager template. Specify the names of deployment itself, resource group, JSON file for the template, JSON file for parameters
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyEventHub.json -TemplateParameterFile MyEventHub-Parameters.json
```

## <a name="use-azure-cli-to-deploy-the-template"></a>使用 Azure CLI 來部署範本

## <a name="sign-in-to-azure"></a>登入 Azure

如果您在 Cloud Shell 中執行命令，則不需要執行下列步驟。 如果您在本機執行 CLI，請執行下列步驟來登入 Azure 並設定您目前的訂用帳戶：

執行下列命令以登入 Azure：

```azurecli
az login
```

設定目前的訂用帳戶環境。 以您要使用的 Azure 訂用帳戶名稱取代 `MyAzureSub`：

```azurecli
az account set --subscription <Name of your Azure subscription>
``` 

### <a name="provision-resources"></a>佈建資源
若要使用 Azure CLI 部署資源，請切換到 C:\EventHubsQuickStart\ARM\ 資料夾，然後執行下列命令：

> [!IMPORTANT]
> 在 az group create 命令中指定 Azure 資源群組的名稱。 上也提供本文中使用的原始碼。

```azurecli
# Create an Azure resource group
az group create --name <YourResourceGroupName> --location eastus

# # Deploy the Resource Manager template. Specify the names of resource group, deployment, JSON file for the template, JSON file for parameters
az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyEventHub.json --parameters @MyEventHub-Parameters.json
```

恭喜！ 您已使用 Azure Resource Manager 範本建立事件中樞命名空間與該命名空間內的事件中樞。

## <a name="next-steps"></a>後續步驟

在此文章中，您已建立事件中樞命名空間，並使用範例應用程式來傳送事件到事件中樞，或從事件中樞接收事件。 如需將事件傳送至事件中樞或從事件樞接收事件的逐步指示，請參閱下列教學課程： 

- **將事件傳送至事件中樞**：[.NET Core](event-hubs-dotnet-standard-getstarted-send.md)、[.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)、[Java](event-hubs-java-get-started-send.md)、[Python](event-hubs-python-get-started-send.md)、[Node.js](event-hubs-node-get-started-send.md)、[Go](event-hubs-go-get-started-send.md)、[C](event-hubs-c-getstarted-send.md)
- **從事件中樞接收事件**：[.NET Core](event-hubs-dotnet-standard-getstarted-receive-eph.md)、[.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md)、[Java](event-hubs-java-get-started-receive-eph.md)、[Python](event-hubs-python-get-started-receive.md)、[Node.js](event-hubs-node-get-started-receive.md)、[Go](event-hubs-go-get-started-receive-eph.md)、[Apache Storm](event-hubs-storm-getstarted-receive.md)

[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
