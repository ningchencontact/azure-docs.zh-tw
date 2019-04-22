---
title: Azure Functions 中函數應用程式的自動化資源部署 | Microsoft Docs
description: 了解如何建置能部署函數應用程式的 Azure Resource Manager 範本。
services: Functions
documtationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, 函數, 無伺服器架構, 基礎結構即程式碼, azure resource manager
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.service: azure-functions
ms.server: functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: glenga
ms.openlocfilehash: 5d028768c062ef7df74d48f83ccc4e27a506f1ac
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59270893"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Azure Functions 中函數應用程式的自動化資源部署

您可以使用 Azure Resource Manager 範本來部署函數應用程式。 本文概述執行這項作業所需的資源和參數。 您可能需要部署額外的資源，視函數應用程式中的[觸發程序和繫結](functions-triggers-bindings.md)而定。

如需關於建立範本的詳細資訊，請參閱 [編寫 Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)。

如需範例範本，請參閱：
- [採用取用方案的函數應用程式]
- [採用 Azure App Service 方案的函數應用程式]

> [!NOTE]
> Azure Functions 裝載的進階方案目前為預覽狀態。 如需詳細資訊，請參閱 < [Azure Functions 的進階方案](functions-premium-plan.md)。

## <a name="required-resources"></a>所需資源

Azure Functions 部署通常包含這些資源：

| 資源                                                                           | 需求 | 語法和屬性參考                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| 函數應用程式                                                                     | 必要項    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |   |
| [Azure 儲存體](../storage/index.yml)帳戶                                   | 必要項    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| [Application Insights](../azure-monitor/app/app-insights-overview.md)元件 | 選用    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |   |
| A[主控方案](./functions-scale.md)                                             | Optional<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup>主控方案只是當您選擇執行函式應用程式時，需要[Premium 方案](./functions-premium-plan.md)（處於預覽狀態），或在[App Service 方案](../app-service/overview-hosting-plans.md)。

> [!TIP]
> 雖然並非必要，強烈建議您為您的應用程式設定 Application Insights。

<a name="storage"></a>
### <a name="storage-account"></a>儲存體帳戶

函數應用程式需要 Azure 儲存體帳戶。 您需要支援 Blob、資料表、佇列和檔案的一般用途帳戶。 如需詳細資訊，請參閱 [Azure Functions 儲存體帳戶需求](functions-create-function-app-portal.md#storage-account-requirements)。

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "properties": {
        "accountType": "[parameters('storageAccountType')]"
    }
}
```

此外，您必須在網站設定中將 `AzureWebJobsStorage` 屬性指定為應用程式設定。 若函式應用程式不使用 Application Insights 以進行監視，也應該將 `AzureWebJobsDashboard` 指定為應用程式設定。

Azure Functions 執行階段會使用 `AzureWebJobsStorage` 連接字串來建立內部佇列。  在未啟用 Application Insights 的情況下，執行階段會使用 `AzureWebJobsDashboard` 連接字串來記錄至 Azure 資料表儲存體，並啟動入口網站中的 [監視] 索引標籤。

這些屬性會在 `siteConfig` 物件的 `appSettings`集合中指定：

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

Application Insights 監視您的函式應用程式的建議。 Application Insights 資源定義的型別**microsoft.insights/components**以及種類**web**:

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('functionAppName')]"
            }
        },
```

此外，必須提供函式應用程式使用的檢測金鑰`APPINSIGHTS_INSTRUMENTATIONKEY`應用程式設定。 這個屬性中指定`appSettings`集合中的`siteConfig`物件：

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>主控方案

主控方案的定義會有所差異，並可以是下列其中一項：
* [取用方案](#consumption)（預設值）
* [進階方案](#premium)（處於預覽狀態）
* [App Service 計劃](#app-service-plan)

### <a name="function-app"></a>函式應用程式

使用類型的資源定義的函式應用程式資源**microsoft.web/sites**以及種類**functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
```

> [!IMPORTANT]
> 如果您要明確地定義主控方案，就會需要額外的項目 dependsOn 陣列中： `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

函式應用程式必須包含這些應用程式設定：

| 設定名稱                 | 描述                                                                               | 值的範例                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | 連接字串儲存體帳戶的 Functions 執行階段的內部佇列 | 請參閱[儲存體帳戶](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Azure Functions 執行階段版本                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | 要用於此應用程式中的函式語言堆疊                                   | `dotnet``node`， `java`，或 `python` |
| WEBSITE_NODE_DEFAULT_VERSION | 當使用，才需要`node`語言堆疊上，指定要使用的版本              | `10.14.1`                             |

這些屬性中指定`appSettings`集合中的`siteConfig`屬性：

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>採用取用方案部署

取用方案會在您的程式碼執行時自動配置計算能力、視需要相應放大來處理負載，然後在程式碼未執行時相應減少。 您不必支付閒置的 Vm，您不必預先保留容量。 若要進一步了解，請參閱[Azure Functions 的級別和裝載](functions-scale.md#consumption-plan)。

如需範例 Azure Resource Manager 範本，請參閱[採用取用方案的函數應用程式]。

### <a name="create-a-consumption-plan"></a>建立取用方案

取用方案不需要加以定義。 其中一個會自動建立或選取每個區域為基礎，當您建立的函式應用程式資源本身。

取用方案是一種特殊的 「 伺服器陣列 」 資源。 對於 Windows，您可以指定使用它`Dynamic`值`computeMode`和`sku`屬性：

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

> [!NOTE]
> 取用方案不能明確定義適用於 Linux。 它會自動建立。

如果您明確地定義您的耗用量計劃，您必須設定`serverFarmId`應用程式讓它指向該計劃的資源識別碼上的屬性。 您應該確定函式應用程式具有`dependsOn`也計劃的設定。

### <a name="create-a-function-app"></a>建立函數應用程式

#### <a name="windows"></a>Windows

在 Windows 中，取用方案需要在站台設定中的兩個其他設定：`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`和`WEBSITE_CONTENTSHARE`。 這些屬性能設定儲存函數應用程式程式碼和組態的儲存體帳戶和檔案路徑。

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

#### <a name="linux"></a>Linux

函式應用程式必須具有在 Linux 上，其`kind`設定為`functionapp,linux`，而且必須`reserved`屬性設定為`true`:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        },
        "reserved": true
    }
}
```



<a name="premium"></a>

## <a name="deploy-on-premium-plan"></a>進階方案部署

「 進階 」 方案提供相同的縮放比例為取用方案，但包含專用的資源和其他功能。 若要進一步了解，請參閱[Azure Functions 進階方案 （預覽）](./functions-premium-plan.md)。

### <a name="create-a-premium-plan"></a>建立進階方案

進階方案是一種特殊的 「 伺服器陣列 」 資源。 您可以使用其中一個來指定`EP1`， `EP2`，或`EP3`如`sku`屬性值。

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>建立函數應用程式

進階方案的函數應用程式必須有`serverFarmId`屬性設定為稍早建立的計劃的資源識別碼。 此外，進階方案需要在站台設定中的兩個其他設定：`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`和`WEBSITE_CONTENTSHARE`。 這些屬性能設定儲存函數應用程式程式碼和組態的儲存體帳戶和檔案路徑。

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```


<a name="app-service-plan"></a> 

## <a name="deploy-on-app-service-plan"></a>部署 App Service 方案

在 App Service 方案中，您的函數應用程式是依據基本、標準或進階 SKU 在專用的 VM 上執行，就像 Web 應用程式一樣。 如需 App Service 方案運作方式的詳細資訊，請參閱 [Azure App Service 方案深入概觀](../app-service/overview-hosting-plans.md)。

如需範例 Azure Resource Manager 範本，請參閱[採用 Azure App Service 方案的函數應用程式]。

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

App Service 方案是由 「 伺服器陣列 」 資源定義。

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

若要在 Linux 上執行您的應用程式，您也必須設定`kind`至`Linux`:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="create-a-function-app"></a>建立函數應用程式 

App Service 方案的函數應用程式必須有`serverFarmId`屬性設定為稍早建立的計劃的資源識別碼。

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

Linux 應用程式也應該包括`linuxFxVersion`屬性下的`siteConfig`。 如果您只會部署程式碼，此設定的值取決於您所需的執行階段堆疊：

| 堆疊            | 範例值                                         |
|------------------|-------------------------------------------------------|
| Python (預覽) | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
| JavaScript       | `DOCKER|microsoft/azure-functions-node8:2.0`          |
| .NET             | `DOCKER|microsoft/azure-functions-dotnet-core2.0:2.0` |

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ],
            "linuxFxVersion": "DOCKER|microsoft/azure-functions-node8:2.0"
        }
    }
}
```

您若是[部署的自訂容器映像](./functions-create-function-linux-custom-image.md)，您必須將它與指定`linuxFxVersion`，並包含可讓您的映像提取，如下所示的組態[Web App for Containers](/azure/app-service/containers)。 此外，設定`WEBSITES_ENABLE_APP_SERVICE_STORAGE`至`false`，因為您的應用程式內容提供本身的容器中：

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>自訂部署

函數應用程式有許多子資源可供您用於部署，包括應用程式設定和原始檔控制選項。 您也可以選擇移除 **sourcecontrols** 子資源並改為使用不同的[部署選項](functions-continuous-deployment.md)。

> [!IMPORTANT]
> 若要使用 Azure Resource Manager 成功部署應用程式，請務必了解資源在 Azure 中部署的方式。 在下列範例中，將使用 **siteConfig** 套用高層級組態。 請務必將這些組態設定為高層級，因為它們會將資訊傳遞給 Functions 執行階段和部署引擎。 在套用子 **sourcecontrols/web** 資源之前，需要高層級資訊。 雖然也可以在子層級 **config/appSettings** 資源設定這些設定，在某些案例下，您的函數應用程式需在套用 **config/appSettings**「之前」完成部署。 例如，在搭配使用函數應用程式與 [Logic Apps](../logic-apps/index.yml) 時，您的函數為另一個資源的相依性。

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            },
            {
                "name": "Project",
                "value": "src"
            }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "FUNCTIONS_EXTENSION_VERSION": "~2",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> 此範本使用[專案](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) (英文) 應用程式設定值，它能設定「函數部署引擎」(Kudu) 在其中尋找可部署程式碼的基礎目錄。 在我們的存放庫中，我們的函數是位於 **src** 資料夾的子資料夾中。 因此，在上述範例中，我們將應用程式設定值設定為 `src`。 如果您的函數位於您存放庫的根，或您並非從來源控制項進行部署，您可以移除此應用程式設定值。

## <a name="deploy-your-template"></a>部署您的範本

您可以使用以下任何方式來部署範本：

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Azure 门户](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>部署至 Azure 按鈕

以 GitHub 中 `azuredeploy.json` 檔案的原始路徑 [URL 編碼](https://www.bing.com/search?q=url+encode)版本取代 ```<url-encoded-path-to-azuredeploy-json>```。

以下是使用 Markdown 的範例：

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

以下是使用 HTML 的範例：

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>後續步驟

深入了解如何開發並設定 Azure Functions。

* [Azure Functions 開發人員參考](functions-reference.md)
* [如何設定 Azure Functions 應用程式設定](functions-how-to-use-azure-function-app-settings.md)
* [建立您的第一個 Azure 函式](functions-create-first-azure-function.md)

<!-- LINKS -->

[採用取用方案的函數應用程式]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[採用 Azure App Service 方案的函數應用程式]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
