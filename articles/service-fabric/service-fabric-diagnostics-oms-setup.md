---
title: "Azure Service Fabric - 使用 OMS Log Analytics 設定監視 | Microsoft Docs"
description: "了解如何設定 OMS 對事件進行視覺化及分析，以供監視 Azure Service Fabric 叢集。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2017
ms.author: dekapur
ms.openlocfilehash: 53b06c5a1395f34c96d4011366835a920d5c670b
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2018
---
# <a name="set-up-oms-log-analytics-for-a-cluster"></a>為叢集設定 OMS Log Analytics

您可以透過 Azure Resource Manager、PowerShell 或 Azure Marketplace 設定 OMS 工作區。 如果要維護您的部署的 Resource Manager 更新範本，以供未來使用，請使用相同的範本來設定您的 OMS 環境。 如果已經在啟用診斷的情況下部署叢集，則透過 Marketplace 部署會更輕鬆。 如果要部署 OMS 的帳戶中沒有訂用帳戶層級的存取權，請使用 PowerShell，或透過 Resource Manager 範本進行部署。

> [!NOTE]
> 您需要先為叢集啟用診斷，以檢視叢集/平台層級事件，以便能夠設定 OMS 以成功監視叢集。

## <a name="deploying-oms-using-azure-marketplace"></a>使用 Azure Marketplace 部署 OMS

如果您想要在部署叢集之後新增 OMS 工作區，請前往 Azure Marketplace (在入口網站中)，並尋找「Service Fabric 分析」。

1. 在左側導覽功能表中按一下 [新增]。 

2. 搜尋 *Service Fabric 分析*。 按一下顯示的資源。

3. 按一下 [建立]

    ![Marketplace 中的 OMS SF 分析](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. 在 Service Fabric 分析建立視窗中，針對 [OMS 工作區] 欄位按一下 [選取工作區]，然後按一下 [建立新的工作區]。 填寫必要項目；這裡唯一的需求是 Service Fabric 的訂用帳戶必須與 OMS 工作區相同。 一旦驗證所有項目，您的 OMS 工作區就會開始部署。 此程序應該只需要幾分鐘的時間。

5. 完成時，再次按一下 Service Fabric 分析建立視窗底部的 [建立]。 請確認新的工作區顯示在 [OMS 工作區] 底下。 這會將解決方案新增至您剛才建立的工作區。

如果使用的是 Windows，請繼續進行下列步驟，以將 OMS 連結到儲存叢集事件所在的儲存體帳戶。 目前尚未在 Linux 叢集上正確實施此體驗，工作尚在進行中。 同時請繼續將 OMS 代理程式新增至您的叢集。  

1. 工作區仍然需要連線到來自叢集的診斷資料。 瀏覽至您建立 Service Fabric 分析解決方案所在的資源群組。 您應該會看到 *ServiceFabric(\<nameOfOMSWorkspace\>)*。 按一下解決方案以瀏覽至其概觀分頁，您可以在其中變更解決方案設定、工作區設定以及瀏覽至 OMS 入口網站。

2. 在左側導覽功能表中，按一下 [工作區資料來源] 底下的 [儲存體帳戶記錄]。

3. 在 [儲存體帳戶記錄] 分頁上，按一下頂端的 [新增] 以將您的叢集記錄新增至工作區。

4. 按一下 [儲存體帳戶] 以新增叢集中建立的適當帳戶。 如果您使用預設名稱，儲存體帳戶名稱為 sfdg\<resourceGroupName\>。 您也可以藉由檢查用來部署叢集的 Azure Resource Manager 範本、檢查針對 `applicationDiagnosticsStorageAccountName` 所使用的值，加以確認。 如果未顯示名稱，您也可能必須向下捲動並按一下 [載入更多]。 按一下正確的儲存體帳戶名稱，加以選取。

5. 接下來，您必須指定 [資料類型]，應設為 **Service Fabric 事件**。

6. 來源應該自動設定為 *WADServiceFabric\*EventTable*。

7. 按一下 [確定] 以將您的工作區連線至您的叢集記錄。

    ![將儲存體帳戶記錄新增至 OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

帳戶現在應該會在工作區資料來源中顯示成為「儲存體帳戶記錄」的一部分。

使用它，您現在就可以在 OMS Log Analytics 工作區中新增 Service Fabric 分析解決方案，該工作區現在正確連線至叢集平台和應用程式記錄資料表。 您可以相同方式將其他來源新增至工作區。


## <a name="deploying-oms-using-a-resource-manager-template"></a>使用 Resource Manager 範本部署 OMS

使用 Resource Manager 範本部署叢集時，該範本會建立新的 OMS 工作區、在其中新增 Service Fabric 解決方案，並將之設定為會從正確的儲存體資料表中讀取資料。

[這裡](https://azure.microsoft.com/resources/templates/service-fabric-oms/)提供一個範例範本，您可以使用並視需求修改。 還有更多範本可提供不同選項來設定 OMS 工作區，可在 [Service Fabric 與 OMS 範本](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS)取得。

主要變更如下：

1. 將 `omsWorkspaceName` 與 `omsRegion` 新增至您的參數。 這表示要將以下程式碼片段新增至 *template.json* 檔案中定義的參數。 可自由將預設值設定為您覺得合適的值。 也請在 *parameters.json* 中新增兩個新的參數，針對資源部署定義其值：
    
    ```json
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "sfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
    ```

    `omsRegion` 值必須符合一組特定的值。 應選擇最接近叢集部署的值。

2. 如果要將任何應用程式記錄檔傳送至 OMS，請確認您的範本中包含作為參數的 `applicationDiagnosticsStorageAccountType` 和 `applicationDiagnosticsStorageAccountName`。 如果尚未納入，請將之新增至如下所示的變數區段，並視需要編輯其值。 您也可以將這些值作為參數加入，按照上面使用的格式即可。

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]"
    ```

3. 將 Service Fabric OMS 解決方案新增至您的範本變數：

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
    ```

4. 將下列命令新增至資源區段的結尾，在宣告 Service Fabric 叢集資源的位置之後。

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', parameters('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "id": "[resourceId('Microsoft.OperationsManagement/solutions/', variables('solution'))]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
    ```
    
    > [!NOTE]
    > 如果您加入 `applicationDiagnosticsStorageAccountName` 作為變數，請務必將其每項參考修改為 `variables('applicationDiagnosticsStorageAccountName')`，而不是 `parameters('applicationDiagnosticsStorageAccountName')`。

5. 以資源管理員升級的方式將範本部署至您的叢集。 這是採取使用 AzureRM PowerShell 模組中的 `New-AzureRmResourceGroupDeployment` API 的方式來完成。 範例命令可能像這樣：

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Azure Resource Manager 將能夠偵測到這是現有資源的更新。 它只會處理驅動現有部署之範本和提供之新範本之間的變更。

## <a name="deploying-oms-using-azure-powershell"></a>使用 Azure PowerShell 部署 OMS

您也可以透過 PowerShell 部署您的 OMS Log Analytics 資源。 使用 `New-AzureRmOperationalInsightsWorkspace` 命令可達成此目的。 若要這麼做，請確定您已安裝 [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1)。 請使用此指令碼建立新的 OMS Log Analytics 工作區，並將 Service Fabric 解決方案新增至該工作區： 

```ps

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<OMS Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

完成之後，如果您的叢集是 Windows 叢集，請遵循上述步驟，將 OMS Log Analytics 連結到適當的儲存體帳戶。

您也可以新增其他解決方案，或使用 PowerShell 對 OMS 工作區進行其他修改。 若要閱讀更多詳細資訊，請參閱[使用 PowerShell 管理 Log Analytics](../log-analytics/log-analytics-powershell-workspace-configuration.md)

## <a name="next-steps"></a>後續步驟
* [部署 OMS 代理程式](service-fabric-diagnostics-oms-agent.md)至您的節點，以收集效能計數器，並收集您容器的 Docker 統計資料和記錄
* 熟悉 Log Analytics 的[記錄搜尋和查詢](../log-analytics/log-analytics-log-searches.md)功能
* [在 Log Analytics 中使用檢視設計工具來建立自訂檢視](../log-analytics/log-analytics-view-designer.md)
