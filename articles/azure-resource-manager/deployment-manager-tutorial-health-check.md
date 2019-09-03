---
title: 搭配使用 Azure 部署管理員與 Resource Manager 範本 | Microsoft Docs
description: 使用 Resource Manager 範本與 Azure 部署管理員來部署 Azure 資源。
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.date: 05/31/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 17e27fcbd0e31c8602869be3d884888fe4fe7db0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095821"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>教學課程：在 Azure 部署管理員 (公開預覽版) 中使用健康情況檢查

了解如何在 [Azure 部署管理員](./deployment-manager-overview.md)中整合健康情況檢查。 本教學課程是以[使用 Azure 部署管理員搭配 Resource Manager 範本](./deployment-manager-tutorial.md)教學課程為基礎。 您必須先完成該教學課程，再繼續本教學課程。

在[使用 Azure 部署管理員搭配 Resource Manager 範本](./deployment-manager-tutorial.md)中使用的首度發行範本中，您使用了等候步驟。 在本教學課程中，您會以健康情況檢查步驟取代等候步驟。

> [!IMPORTANT]
> 如果您的訂用帳戶標示針對 Canary 測試新的 Azure 功能，您只能使用 Azure 部署管理員部署到 Canary 區域。 

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 建立健康情況檢查服務模擬器
> * 建立首度發行範本
> * 部署拓撲
> * 在不良狀態下部署首度發行
> * 驗證首度發行部署
> * 在良好狀態下部署首度發行
> * 驗證首度發行部署
> * 清除資源

其他資源：

- [Azure 部署管理員 REST API 參考](https://docs.microsoft.com/rest/api/deploymentmanager/)。
- [Azure 部署管理員範例](https://github.com/Azure-Samples/adm-quickstart)。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要完成本文，您需要：

* 完成[使用 Azure 部署管理員搭配 Resource Manager 範本](./deployment-manager-tutorial.md)。
* 下載本教學課程所使用的[範本和成品](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip)。

## <a name="create-a-health-check-service-simulator"></a>建立健康情況檢查服務模擬器

在生產環境中，您通常會使用一或多個監視提供者。 為了讓健康情況整合盡量簡化，Microsoft 持續與一些最頂尖的服務健康情況監視公司合作，為您提供簡單的複製/貼上解決方案，以整合健康情況檢查與您的部署。 如需這些公司的清單，請參閱[健康情況監視提供者](./deployment-manager-health-check.md#health-monitoring-providers)。 就此教學課程的目的，您會建立 [Azure 函式](/azure/azure-functions/)來模擬健康情況監視服務。 此函式會接受狀態碼，並傳回相同的代碼。 Azure 部署管理員範本會使用狀態碼來判斷如何繼續進行部署。

下列兩個檔案用於部署 Azure 函式。 您不需要下載這些檔案，即可進行本教學課程。

* Resource Manager 範本位於 [https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json](https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json)。 您會部署此範本來建立 Azure 函式。
* Azure 函式原始程式碼的 zip 檔 [https://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip ](https://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip)。 Resource Manager 範本會呼叫此 zip 檔。

若要部署 Azure 函式，請選取 [試試看]  以開啟 Azure Cloud Shell，然後將下列指令碼貼到 Shell 視窗。  若要貼上程式碼，請以滑鼠右鍵按一下 Shell 視窗，然後選取 [貼上]  。

> [!IMPORTANT]
> PowerShell 指令碼中的 **projectName** 用來為本教學課程中部署的 Azure 服務產生名稱。 不同的 Azure 服務有不同的名稱需求。 若要確保成功部署，請選擇小於 12 個字元且僅有小寫字母和數字的名稱。
> 儲存專案名稱的複本。 您會在整個教學課程中使用相同的 projectName。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json" -projectName $projectName
```

若要驗證及測試 Azure 函式：

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
1. 開啟資源群組。  預設名稱是附加 **rg** 的專案名稱。
1. 從資源群組中選取 App Service。  App Service 的預設名稱是是附加 **webapp** 的專案名稱。
1. 展開 [函式]  ，然後選取 [HttpTrigger1]  。

    ![Azure 部署管理員檢查 Azure 函式的健康情況](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. 選取 &lt;/>[取得函式 URL]  。
1. 選取 [複製]  將 URL 複製到剪貼簿。  URL 如下所示：

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    以狀態碼取代 URL 中的 `{healthStatus}`。 在本教學課程中，使用 **unhealthy** 來測試狀況不良的案例，以及使用 **healthy** 或 **warning** 來測試狀況良好的案例。 建立兩個 URL，一個為不良狀態，另一個則為良好狀態。 例如：

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    您需要這兩個 URL 才能完成本教學課程。

1. 若要測試健康情況監視模擬器，請開啟您在最後一個步驟中建立的 URL。  不良狀態的結果應如下所示：

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>建立首度發行範本

這一節的目的是要說明如何在首度發行範本中包含健康情況檢查步驟。 您不必建立自己的 CreateADMRollout.json 檔案，即可完成本教學課程。 後續各節使用的儲存體帳戶會共用已修訂的首度發行範本。

1. 開啟 **CreateADMRollout.json**。 此 JSON 檔案是下載的一部分。  請參閱[必要條件](#prerequisites)。
1. 新增額外兩個參數：

    ```json
    "healthCheckUrl": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check URL."
        }
    },
    "healthCheckAuthAPIKey": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check Azure Function function authorization key."
        }
    }
    ```

1. 以健康情況檢查步驟資源定義取代等候步驟資源定義：

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

    根據定義，如果健康情況狀態是 *healthy* 或 *warning*，首度發行則會繼續執行。

1. 更新首度發行定義的 **dependsON**，以納入新定義的健康情況檢查步驟：

    ```json
    "dependsOn": [
        "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
        "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. 更新 **stepGroups** 以納入健康情況檢查步驟。 **healthCheckStep** 會在 **stepGroup2** 的 **postDeploymentSteps** 中呼叫。 只有健康狀態為 *healthy* 或 *warning* 時，才會部署 **stepGroup3** 和 **stepGroup4**。

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

    如果您比較修訂前後的 **stepGroup3** 區段，此區段現在會相依於 **stepGroup2**。  當 **stepGroup3** 和後續步驟群組相依於健康情況監視結果時，則為必要動作。

    下列螢幕擷取畫面說明已修改的區域，以及如何使用健康情況檢查步驟：

    ![Azure 部署管理員健康情況檢查範本](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>部署拓撲

為了簡化本教學課程，拓撲範本和成品會在下列位置共用，因此您不需要準備您自己的複本。 如果您想使用自己的複本，請依照指示操作：[教學課程：使用 Azure 部署管理員搭配 Resource Manager 範本](./deployment-manager-tutorial.md)。

* 拓撲範本：https:\//armtutorials.blob.core.windows.net/admtutorial/ADMTemplates/CreateADMServiceTopology.json
* 成品存放區：https:\//armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

若要部署拓撲，請選取 [試試看]  以開啟 Cloud Shell，然後貼上 PowerShell 指令碼。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMServiceTopology.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation
```

使用 Azure 入口網站確認服務拓撲和基礎資源均已成功建立：

![Azure 部署管理員教學課程已部署的服務拓撲資源](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

必須選取 [顯示隱藏的類型]  才能檢視資源。

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>在不良狀態下部署首度發行

為了簡化本教學課程，已修訂的首度發行範本會在下列位置共用，因此您不需要準備您自己的複本。 如果您想使用自己的複本，請依照指示操作：[教學課程：使用 Azure 部署管理員搭配 Resource Manager 範本](./deployment-manager-tutorial.md)。

* 拓撲範本：https:\//armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json
* 成品存放區：https:\//armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

使用您在[建立健康情況檢查服務模擬器](#create-a-health-check-service-simulator)中建立的不良狀態 URL。 如需 **managedIdentityID**，請參閱[輸入使用者指派的受控識別](./deployment-manager-tutorial.md#create-the-user-assigned-managed-identity)。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$managedIdentityID = Read-Host -Prompt "Enter a user-assigned managed identity"
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation `
    -managedIdentityID $managedIdentityID `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment` 是非同步呼叫。 成功訊息只表示部署已成功開始。 若要驗證部署，請使用 `Get-AZDeploymentManagerRollout`。  請參閱下一個程序。

若要使用下列 PowerShell 指令碼查看首度發行進度：

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$resourceGroupName = "${projectName}rg"
$rolloutName = "${projectName}Rollout"

# Get the rollout status
Get-AzDeploymentManagerRollout `
    -ResourceGroupName $resourceGroupName `
    -Name $rolloutName `
    -Verbose
```

下列範例輸出顯示不良狀態所導致的部署失敗：

```output
Service: myhc0417ServiceWUSrg
    TargetLocation: WestUS
    TargetSubscriptionId: <Subscription ID>

    ServiceUnit: myhc0417ServiceWUSWeb
        TargetResourceGroup: myhc0417ServiceWUSrg

        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
Get-AzDeploymentManagerRollout :
Service: myhc0417ServiceWUSrg
    ServiceUnit: myhc0417ServiceWUSWeb
        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
At line:1 char:1
+ Get-AzDeploymentManagerRollout `
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : NotSpecified: (:) [Get-AzDeploymentManagerRollout], Exception
+ FullyQualifiedErrorId : RolloutFailed,Microsoft.Azure.Commands.DeploymentManager.Commands.GetRollout


ResourceGroupName       : myhc0417rg
BuildVersion            : 1.0.0.0
ArtifactSourceId        : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/artifactSources/myhc0417ArtifactSourceRollout
TargetServiceTopologyId : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/serviceTopologies/myhc0417ServiceTopology
Status                  : Failed
TotalRetryAttempts      : 0
Identity                : Microsoft.Azure.Commands.DeploymentManager.Models.PSIdentity
OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
Services                : {myhc0417ServiceWUS, myhc0417ServiceWUSrg}
Name                    : myhc0417Rollout
Type                    : Microsoft.DeploymentManager/rollouts
Location                : centralus
Id                      : /subscriptions/<Subscription ID>/resourcegroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/rollouts/myhc0417Rollout
Tags                    :
```

首度發行完成之後，您應會看到針對美國西部建立的一個額外資源群組。

## <a name="deploy-the-rollout-with-the-healthy-status"></a>在良好狀態下部署首度發行

重複這一節以使用良好狀態 URL 重新部署首度發行。  首度發行完成之後，您應會看到針對美國東部建立的一個額外資源群組。

## <a name="verify-the-deployment"></a>驗證部署

1. 開啟 [Azure 入口網站](https://portal.azure.com)。
2. 在首度發行部署所建立的新資源群組下，瀏覽至新建立的 Web 應用程式。
3. 在網頁瀏覽器中開啟 Web 應用程式。 確認 index.html 檔案的位置和版本。

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 使用 [依名稱篩選]  欄位，縮減在本教學課程中建立的資源群組數目。 應該會有 3-4 個：

    * **&lt;namePrefix>rg**：包含部署管理員資源。
    * **&lt;namePrefix>ServiceWUSrg**：包含 ServiceWUS 所定義的資源。
    * **&lt;namePrefix>ServiceEUSrg**：包含 ServiceEUS 所定義的資源。
    * 使用者定義受控識別的資源群組。
3. 選取資源群組名稱。
4. 從頂端功能表中選取 [刪除資源群組]  。
5. 重複最後兩個步驟，刪除本教學課程所建立的其他資源群組。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用 Azure 部署管理員的健康情況檢查功能。 若要深入了解，請參閱 [Azure Resource Manager 文件](/azure/azure-resource-manager/)。
