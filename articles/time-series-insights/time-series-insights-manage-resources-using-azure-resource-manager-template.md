---
title: "如何管理 Azure 時間數列 Insights 環境使用 Azure Resource Manager 範本 |Microsoft 文件"
description: "本文說明如何管理 Azure 時間數列 Insights 環境以程式設計方式使用 Azure 資源管理員。"
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 12/08/2017
ms.openlocfilehash: b09d4a1aea56a4e306f80a1b43d519d313fd73ab
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2017
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>建立使用 Azure Resource Manager 範本的時間序列 Insights 資源

本文說明如何建立及部署使用 Azure Resource Manager 範本、 PowerShell 和時間數列 Insights 資源提供者的時間序列 Insights 資源。

時間序列 Insights 支援的下列資源：
   | 資源 | 說明 |
   | --- | --- |
   | 環境 | 時間序列 Insights 環境是從事件的代理程式讀取、 儲存，並且可供查詢事件的邏輯群組。 如需詳細資訊，請參閱[規劃 Azure 時間數列 Insights 環境](time-series-insights-environment-planning.md) |
   | 事件來源 | 事件來源是時間序列 Insights 讀取並在環境中內嵌事件的事件 broker 的連接。 目前支援的事件來源為 IoT 中樞與事件中心。 |
   | 參考資料集 | 參考資料集提供環境中之事件的相關中繼資料。 輸入期間，將事件聯結參考資料集中的中繼資料。 參考資料集是由事件索引鍵屬性定義為資源。 上傳或透過資料平面 Api 修改實際組成參考資料集的中繼資料。 |
   | 存取原則 | 發出資料的查詢、 操作在環境中，參考資料和共用儲存的查詢和檢視方塊與環境相關聯的權限授與的存取原則。 如需詳細資訊，請參閱[授與使用 Azure 入口網站在時間序列 Insights 環境進行資料存取](time-series-insights-data-access.md) |

資源管理員範本是在資源群組中定義的基礎結構和設定資源的 JSON 檔案。 如需詳細資訊，請參閱下列文件：

- [Azure 資源管理員概觀範本部署](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../azure-resource-manager/resource-group-template-deploy.md)

[201-timeseriesinsights-環境-與-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub)快速入門範本在 GitHub 上發行。 此範本會建立在時間序列 Insights 環境中，才能取用事件從事件中心，並存取授與存取權的環境資料的原則設定為子系的事件來源。 如果未指定現有的事件中心，就會建立與部署。

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>部署在本機上使用 PowerShell 快速入門範本

下列程序描述如何使用 PowerShell 來部署的 Azure 資源管理員範本，建立時間序列 Insights 環境，才能取用事件從事件中心，並存取授與存取權的原則設定為子系的事件來源環境的資料。 如果未指定現有的事件中心，就會建立與部署。

近似的工作流程如下︰

1. 安裝 PowerShell。
1. 建立範本和參數檔案。
1. 在 PowerShell 中登入您的 Azure 帳戶。
1. 如果沒有資源群組，請建立一個新的。
1. 測試部署。
1. 部署範本。

### <a name="install-powershell"></a>安裝 PowerShell

依照下列指示安裝 Azure PowerShell：[開始使用 Azure PowerShell](/powershell/azure/get-started-azureps)。

### <a name="create-a-template"></a>建立範本

再製或複製[201-timeseriesinsights-環境-與-eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json)從 GitHub 的範本。

### <a name="create-a-parameters-file"></a>建立參數檔案

若要建立參數檔案，複製[201-timeseriesinsights-環境-與-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)檔案。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "eventHubNamespaceName": {
          "value": "GEN-UNIQUE"
      },
      "eventHubName": {
          "value": "GEN-UNIQUE"
      },
      "consumerGroupName": {
          "value": "GEN-UNIQUE"
      },
      "environmentName": {
        "value": "GEN-UNIQUE"
      },
      "eventSourceName": {
        "value": "GEN-UNIQUE"
      }
  }
}
```

#### <a name="required-parameters"></a>必要的參數

   | 參數 | 說明 |
   | --- | --- |
   | eventHubNamespaceName | 來源事件中樞命名空間。 |
   | eventHubName | 來源事件中樞的名稱。 |
   | consumerGroupName | 時間序列 Insights 服務會用來讀取的資料，從事件中樞取用者群組的名稱。 **注意：**為了避免資源爭用，此取用者群組必須專門用於時間序列 Insights 服務並不會與其他的讀取器共用。 |
   | EnvironmentName | 環境的名稱。 名稱不能包含: '<'、' >'，'%'、 '&'、 ': '、'\\'、'？ '、 '/' 與任何控制字元。 允許所有其他字元。|
   | eventSourceName | 事件來源子資源的名稱。 名稱不能包含: '<'、' >'，'%'、 '&'、 ': '、'\\'、'？ '、 '/' 與任何控制字元。 允許所有其他字元。 |

#### <a name="optional-parameters"></a>選擇性參數

   | 參數 | 說明 |
   | --- | --- |
   | existingEventHubResourceId | 現有的事件中心，讓它連接至時間序列 Insights 環境透過事件來源的選擇性資源識別碼。 **注意：**部署範本的使用者必須擁有執行事件中樞 listkey 作業的權限。 如果沒有傳遞任何值，則將範本所建立新的事件中樞。 |
   | environmentDisplayName | 選擇性的易記名稱，以顯示在工具或使用者介面而不是環境名稱。 |
   | environmentSkuName | Sku 的名稱。 如需詳細資訊，請參閱[時間數列 Insights 定價頁面](https://azure.microsoft.com/pricing/details/time-series-insights/)。  |
   | environmentSkuCapacity | Sku 單元容量。 如需詳細資訊，請參閱[時間數列 Insights 定價頁面](https://azure.microsoft.com/pricing/details/time-series-insights/)。|
   | environmentDataRetentionTime | 最小的 timespan 環境的事件可供查詢。 必須指定值，採用 ISO 8601 格式，例如"P30D 「 保留原則的 30 天。 |
   | eventSourceDisplayName | 選擇性的易記名稱，以顯示在工具或使用者介面而不是事件來源名稱。 |
   | eventSourceTimestampPropertyName | 當做事件來源的時間戳記的事件屬性。 如果未指定 timestampPropertyName，其值或指定 null 或空白字串，將會使用事件的建立時間。 |
   | eventSourceKeyName | 共用的存取金鑰的時間序列 Insights 服務將用來連接到事件中樞的名稱。 |
   | accessPolicyReaderObjectIds | 物件的使用者或應用程式應該有讀取器環境的存取權的 Azure AD 中的識別碼清單。 您可以取得服務主體 objectId 呼叫**Get AzureRMADUser**或**Get AzureRMADServicePrincipal** cmdlet。 尚未支援建立 Azure AD 群組的存取原則。 |
   | accessPolicyContributorObjectIds | 物件的使用者或應用程式應該有參與者環境的存取權的 Azure AD 中的識別碼清單。 您可以取得服務主體 objectId 呼叫**Get AzureRMADUser**或**Get AzureRMADServicePrincipal** cmdlet。 尚未支援建立 Azure AD 群組的存取原則。 |

例如，下列參數檔案會用於建立的環境和從現有的事件中心讀取事件的事件來源。 它也會建立兩個參與者環境的存取權授與的存取原則。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventHubNamespaceName": {
            "value": "tsiTemplateTestNamespace"
        },
        "eventHubName": {
            "value": "tsiTemplateTestEventHub"
        },
        "consumerGroupName": {
            "value": "tsiTemplateTestConsumerGroup"
        },
        "environmentName": {
          "value": "tsiTemplateTestEnvironment"
        },
        "eventSourceName": {
          "value": "tsiTemplateTestEventSource"
        },
        "existingEventHubResourceId": {
          "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
        },
        "accessPolicyContributorObjectIds": {
            "value": [
                "AGUID001-0000-0000-0000-000000000000",
                "AGUID002-0000-0000-0000-000000000000"
            ]
        }
    }
  }
```

如需詳細資訊，請參閱[參數](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)一文。

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>登入 Azure 並設定 Azure 訂用帳戶

從 PowerShell 提示字元中執行下列命令：

```powershell
Login-AzureRmAccount
```

系統會提示您登入您的 Azure 帳戶。 登入之後，執行下列命令以檢視可用的訂用帳戶：

```powershell
Get-AzureRMSubscription
```

這個命令會傳回可用的 Azure 訂用帳戶清單。 執行下列命令為目前的工作階段選擇訂用帳戶。 以您要使用的 Azure 訂用帳戶 GUID 取代 `<YourSubscriptionId>`：

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>設定資源群組

如果您沒有現有的資源群組中，建立新的資源群組，**新增 AzureRmResourceGroup**命令。 提供您要使用的資源群組名稱和位置。 例如︰

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

如果成功，就會顯示新資源群組的摘要。

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>測試部署

執行 `Test-AzureRmResourceGroupDeployment` Cmdlet 驗證部署。 測試部署時，請提供與執行部署時完全一致的參數。

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

### <a name="create-the-deployment"></a>建立部署

若要建立新的部署，請執行 `New-AzureRmResourceGroupDeployment` Cmdlet，並於提示出現時提供必要的參數。 參數會包含部署的名稱、資源群組的名稱，以及範本檔案的路徑或 URL。 如未指定 **Mode** 參數，即會使用預設值 **Incremental**。 如需詳細資訊，請參閱[累加部署與完整部署](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)。

下列命令會提示您輸入 PowerShell 視窗中的五個必要參數：

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

若要改為指定參數檔案，使用下列命令：

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

執行部署 Cmdlet 時，您也可以使用內嵌參數。 命令如下所示︰

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

若要執行[完整](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)部署，請將 **Mode** 參數設為 **Complete**：

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

## <a name="verify-the-deployment"></a>驗證部署

如果資源成功部署，PowerShell 視窗中就會顯示部署的摘要︰

```powershell
DeploymentName          : azuredeploy
ResourceGroupName       : MyDemoRG
ProvisioningState       : Succeeded
Timestamp               : 12/9/2017 01:06:54
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          eventHubNamespaceName  String               <eventHubNamespaceName>
                          eventHubName     String                     <eventHubName>
                          consumerGroupName  String                   <consumerGroupName>
                          existingEventHubResourceId  String
                          environmentName  String                     <environmentName>
                          environmentDisplayName  String
                          environmentSkuName  String                  S1
                          environmentSkuCapacity  Int                 1
                          environmentDataRetentionTime  String        P30D
                          eventSourceName  String                     <eventSourceName>
                          eventSourceDisplayName  String
                          eventSourceTimestampPropertyName  String
                          eventSourceKeyName  String                  manage
                          accessPolicyReaderObjectIds  Array          []
                          accessPolicyContributorObjectIds  Array     [
                            "AGUID001-0000-0000-0000-000000000000",
                            "AGUID002-0000-0000-0000-000000000000"
                          ]

Outputs                 :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          dataAccessFQDN   String
                          <guid>.env.timeseries.azure.com
```

## <a name="deploy-the-quickstart-template-through-the-azure-portal"></a>部署透過 Azure 入口網站快速入門範本

GitHub 上的快速入門範本首頁還包括**部署至 Azure**  按鈕。 按一下 Azure 入口網站中開啟自訂部署 頁面。 您可以從這個頁面上，輸入或選取值的每一個參數從[必要參數](time-series-insights-manage-resources-using-azure-resource-manager-template.md#required-parameters)或[選擇性參數](time-series-insights-manage-resources-using-azure-resource-manager-template.md#optional-parameters)資料表。 填寫設定，按一下後**購買**按鈕會起始部署範本。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank"> <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>後續步驟

- 如需以程式設計方式管理使用 REST Api 的時間序列 Insights 資源資訊，請參閱[時間數列 Insights 管理](https://docs.microsoft.com/rest/api/time-series-insights-management/)。
