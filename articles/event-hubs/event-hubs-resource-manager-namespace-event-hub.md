---
title: 使用範本來建立 Azure 事件中樞命名空間和取用者群組 | Microsoft Docs
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
ms.openlocfilehash: 48d41ef4df986f959dfabe04e07552e287fdfcd0
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457045"
---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本，以事件中樞和取用者群組來建立事件中樞命名空間
Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 如需事件中樞的詳細概觀，請參閱[事件中樞概觀](event-hubs-about.md)與[事件中樞功能](event-hubs-features.md)。

在此快速入門中，您會使用 Azure Resource Manager 範本建立事件中樞。 您可以使用 Azure Resource Manager 範本來建立一個類型為[事件中樞](event-hubs-what-is-event-hubs.md)、含有一個事件中樞和一個取用者群組的的命名空間。 本文說明如何定義要部署哪些資源，以及如何定義執行部署時所指定的參數。 您可以直接在自己的部署中使用此範本，或自訂此範本以符合您的需求。 如需關於建立範本的資訊，請參閱[編寫 Azure Resource Manager 範本][Authoring Azure Resource Manager templates]。

如需完整的範本，請參閱 GitHub 上的[事件中樞和取用者群組範本][Event Hub and consumer group template]。

> [!NOTE]
> 若要檢查最新的範本，請造訪 [Azure 快速入門範本][Azure Quickstart Templates] 資源庫並搜尋事件中樞。

## <a name="prerequisites"></a>必要條件
若要完成本快速入門，您需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶][]。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

若您使用 **Azure PowerShell** 在本機部署 Resource Manager 範本，您必須執行最新版本的 PowerShell 以完成此快速入門。 如果您需要安裝或升級，請參閱[安裝和設定 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0)。

如果您選擇在本機安裝 **Azure CLI** 並使用它來部署 Resource Manager 範本，此教學課程會要求您執行 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 來檢查您的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="what-will-you-deploy"></a>您將部署什麼？

藉由此範本，您會部署含有事件中樞和取用者群組的「事件中樞」命名空間。

若要自動執行部署，請選取下列按鈕：

[![部署至 Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="define-parameters"></a>定義參數
透過 Azure 資源管理員，您可以定義在部署範本時想要指定之值的參數。 此範本有一個 `Parameters` 區段，內含所有參數值。 針對會隨著所要部署專案或部署環境而變化的值，您應該為這些值定義參數。 請不要為永遠保持不變的值定義參數。 範本中的每個參數值會定義所部署的資源。

範本會定義下列參數：

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

要建立的事件中樞命名空間名稱。

```json
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName

在「事件中樞」命名空間中建立的事件中樞名稱。

```json
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName

為事件中樞建立的取用者群組名稱。

```json
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion

範本的 API 版本。

```json
"apiVersion": {
"type": "string"
}
```

## <a name="define-resources-to-deploy"></a>定義要部署的資源

建立類型為 **EventHubs**、含有事件中樞和取用者群組的命名空間：

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="azure-powershell"></a>Azure PowerShell
若要使用 Azure PowerShell 部署資源，請執行下列命令：

```azurepowershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI
若要使用 Azure PowerShell 部署資源，請執行下列命令：

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

恭喜！ 您已使用 Azure Resource Manager 範本建立事件中樞命名空間與該命名空間內的事件中樞。

## <a name="next-steps"></a>後續步驟

在此文章中，您已建立事件中樞命名空間，並使用範例應用程式來傳送事件到事件中樞，或從事件中樞接收事件。 如需將事件傳送至事件中樞或從事件樞接收事件的逐步指示，請參閱下列教學課程： 

- **將事件傳送至事件中樞**：[.NET Standard](event-hubs-dotnet-standard-getstarted-send.md)、[.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)、[Java](event-hubs-java-get-started-send.md)、[Python](event-hubs-python-get-started-send.md)、[Node.js](event-hubs-node-get-started-send.md)、[Go](event-hubs-go-get-started-send.md)、[C](event-hubs-c-getstarted-send.md)
- **從事件中樞接收事件**：[.NET Standard](event-hubs-dotnet-standard-getstarted-receive-eph.md)、[.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md)、[Java](event-hubs-java-get-started-receive-eph.md)、[Python](event-hubs-python-get-started-receive.md)、[Node.js](event-hubs-node-get-started-receive.md)、[Go](event-hubs-go-get-started-receive-eph.md)、[Apache Storm](event-hubs-storm-getstarted-receive.md)

[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
