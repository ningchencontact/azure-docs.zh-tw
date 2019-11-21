---
title: Automate resource deployment for a function app in Azure Functions
description: 了解如何建置能部署函數應用程式的 Azure Resource Manager 範本。
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 9c222937831c0e8017a390b16ef192783e9e564a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230515"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Azure Functions 中函數應用程式的自動化資源部署

您可以使用 Azure Resource Manager 範本來部署函數應用程式。 本文概述執行這項作業所需的資源和參數。 您可能需要部署額外的資源，視函數應用程式中的[觸發程序和繫結](functions-triggers-bindings.md)而定。

如需關於建立範本的詳細資訊，請參閱 [編寫 Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)。

如需範例範本，請參閱：
- [採用取用方案的函數應用程式]
- [採用 Azure App Service 方案的函數應用程式]

## <a name="required-resources"></a>所需資源

An Azure Functions deployment typically consists of these resources:

| 資源                                                                           | 需求 | Syntax and properties reference                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| 函數應用程式                                                                     | 必要項    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |   |
| [Azure 儲存體](../storage/index.yml)帳戶                                   | 必要項    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| An [Application Insights](../azure-monitor/app/app-insights-overview.md) component | 選用    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |   |
| A [hosting plan](./functions-scale.md)                                             | Optional<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup>A hosting plan is only required when you choose to run your function app on a [Premium plan](./functions-premium-plan.md) (in preview) or on an [App Service plan](../app-service/overview-hosting-plans.md).

> [!TIP]
> While not required, it is strongly recommended that you configure Application Insights for your app.

<a name="storage"></a>
### <a name="storage-account"></a>儲存體帳戶

函數應用程式需要 Azure 儲存體帳戶。 您需要支援 Blob、資料表、佇列和檔案的一般用途帳戶。 如需詳細資訊，請參閱 [Azure Functions 儲存體帳戶需求](functions-create-function-app-portal.md#storage-account-requirements)。

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-04-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
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

Application Insights is recommended for monitoring your function apps. The Application Insights resource is defined with the type **Microsoft.Insights/components** and the kind **web**:

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

In addition, the instrumentation key needs to be provided to the function app using the `APPINSIGHTS_INSTRUMENTATIONKEY` application setting. This property is specified in the `appSettings` collection in the `siteConfig` object:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>主控方案

The definition of the hosting plan varies, and can be one of the following:
* [Consumption plan](#consumption) (default)
* [Premium plan](#premium) (in preview)
* [App Service 計劃](#app-service-plan)

### <a name="function-app"></a>函式應用程式

The function app resource is defined by using a resource of type **Microsoft.Web/sites** and kind **functionapp**:

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
> If you are explicitly defining a hosting plan, an additional item would be needed in the dependsOn array: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

A function app must include these application settings：

| 設定名稱                 | 描述                                                                               | 值的範例                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | A connection string to a storage account that the Functions runtime for internal queueing | See [Storage account](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | The version of the Azure Functions runtime                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | The language stack to be used for functions in this app                                   | `dotnet`, `node`, `java`, or `python` |
| WEBSITE_NODE_DEFAULT_VERSION | Only needed if using the `node` language stack, specifies the version to use              | `10.14.1`                             |

These properties are specified in the `appSettings` collection in the `siteConfig` property:

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

## <a name="deploy-on-consumption-plan"></a>Deploy on Consumption plan

取用方案會在您的程式碼執行時自動配置計算能力、視需要相應放大來處理負載，然後在程式碼未執行時相應減少。 You don't have to pay for idle VMs, and you don't have to reserve capacity in advance. 若要深入了解，請參閱 [Azure Functions 規模調整和主控](functions-scale.md#consumption-plan)。

如需範例 Azure Resource Manager 範本，請參閱[採用取用方案的函數應用程式]。

### <a name="create-a-consumption-plan"></a>建立取用方案

A Consumption plan does not need to be defined. One will automatically be created or selected on a per-region basis when you create the function app resource itself.

The Consumption plan is a special type of "serverfarm" resource. For Windows, you can specify it by using the `Dynamic` value for the `computeMode` and `sku` properties:

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> The Consumption plan cannot be explicitly defined for Linux. It will be created automatically.

If you do explicitly define your consumption plan, you will need to set the `serverFarmId` property on the app so that it points to the resource ID of the plan. You should ensure that the function app has a `dependsOn` setting for the plan as well.

### <a name="create-a-function-app"></a>建立函數應用程式

#### <a name="windows"></a>Windows

On Windows, a Consumption plan requires two additional settings in the site configuration: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` and `WEBSITE_CONTENTSHARE`. 這些屬性能設定儲存函數應用程式程式碼和組態的儲存體帳戶和檔案路徑。

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

On Linux, the function app must have its `kind` set to `functionapp,linux`, and it must have the `reserved` property set to `true`:

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

## <a name="deploy-on-premium-plan"></a>Deploy on Premium plan

The Premium plan offers the same scaling as the consumption plan but includes dedicated resources and additional capabilities. To learn more, see [Azure Functions Premium Plan](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>建立進階方案

A Premium plan is a special type of "serverfarm" resource. You can specify it by using either `EP1`, `EP2`, or `EP3` for the `sku` property value.

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

A function app on a Premium plan must have the `serverFarmId` property set to the resource ID of the plan created earlier. In addition, a Premium plan requires two additional settings in the site configuration: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` and `WEBSITE_CONTENTSHARE`. 這些屬性能設定儲存函數應用程式程式碼和組態的儲存體帳戶和檔案路徑。

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

## <a name="deploy-on-app-service-plan"></a>Deploy on App Service plan

在 App Service 方案中，您的函數應用程式是依據基本、標準或進階 SKU 在專用的 VM 上執行，就像 Web 應用程式一樣。 如需 App Service 方案運作方式的詳細資訊，請參閱 [Azure App Service 方案深入概觀](../app-service/overview-hosting-plans.md)。

如需範例 Azure Resource Manager 範本，請參閱[採用 Azure App Service 方案的函數應用程式]。

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

An App Service plan is defined by a "serverfarm" resource.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

To run your app on Linux, you must also set the `kind` to `Linux`:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>建立函數應用程式 

A function app on an App Service plan must have the `serverFarmId` property set to the resource ID of the plan created earlier.

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

Linux apps should also include a `linuxFxVersion` property under `siteConfig`. If you are just deploying code, the value for this is determined by your desired runtime stack:

| Stack            | 範例值                                         |
|------------------|-------------------------------------------------------|
| Python           | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
| Javascript       | `DOCKER|microsoft/azure-functions-node8:2.0`          |
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

If you are [deploying a custom container image](./functions-create-function-linux-custom-image.md), you must specify it with `linuxFxVersion` and include configuration that allows your image to be pulled, as in [Web App for Containers](/azure/app-service/containers). Also, set `WEBSITES_ENABLE_APP_SERVICE_STORAGE` to `false`, since your app content is provided in the container itself:

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

## <a name="customizing-a-deployment"></a>Customizing a deployment

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
* [Azure 入口網站](../azure-resource-manager/resource-group-template-deploy-portal.md)
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

### <a name="deploy-using-powershell"></a>使用 PowerShell 進行部署

The following PowerShell commands create a resource group and deploy a template that create a function app with its required resources. To run locally, you must have [Azure PowerShell](/powershell/azure/install-az-ps) installed. Run [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) to sign in.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

To test out this deployment, you can use a [template like this one](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) that creates a function app on Windows in a Consumption plan. Replace `<function-app-name>` with a unique name for your function app.

## <a name="next-steps"></a>後續步驟

深入了解如何開發並設定 Azure Functions。

* [Azure Functions 開發人員參考](functions-reference.md)
* [如何設定 Azure Functions 應用程式設定](functions-how-to-use-azure-function-app-settings.md)
* [建立您的第一個 Azure 函式](functions-create-first-azure-function.md)

<!-- LINKS -->

[採用取用方案的函數應用程式]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[採用 Azure App Service 方案的函數應用程式]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
