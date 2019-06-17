---
title: Azure Data Factory 中的持續整合和傳遞 | Microsoft Docs
description: 了解如何使用持續整合和傳遞將一個環境 (開發、測試、生產) 中的 Data Factory 管線移至另一個環境。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2019
author: gauravmalhot
ms.author: gamal
ms.reviewer: maghan
manager: craigg
ms.openlocfilehash: 76962975705ff53a292f41a0a54e42c5f2991a2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002642"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Azure Data Factory 中的持續整合和傳遞 (CI/CD)

持續整合是指進行相關實作，以自動並及早測試對您的程式碼基底所做的每項變更。 在持續整合期間執行測試，並將變更推送至暫存或生產系統後，就會進行持續傳遞。

就 Azure Data Factory 而言，持續整合和傳遞是指將一個環境 (開發、測試、生產) 中的 Data Factory 管線移至另一個環境。 若要進行持續整合和傳遞，您可以使用 Data Factory UI 整合與 Azure Resource Manager 範本。 當您選取 [ARM 範本]  選項時，Data Factory UI 可產生 Resource Manager 範本。 當您選取 [匯出 ARM 範本]  時，入口網站將會產生資料處理站的 Resource Manager 範本，以及包含您所有連接字串和其他參數的組態檔。 然後，您必須為每個環境 (開發、測試、生產) 建立一個組態檔。 所有環境的主要 Resource Manager 範本檔案會保持相同。

如需此功能的 9 分鐘簡介與示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-manager-template-for-each-environment"></a>為每個環境建立 Resource Manager 範本
選取 [匯出 ARM 範本]  ，在開發環境中匯出資料處理站的 Resource Manager 範本。

![](media/continuous-integration-deployment/continuous-integration-image1.png)

然後，移至您的測試資料處理站和生產資料處理站，並選取 [匯入 ARM 範本]  。

![](media/continuous-integration-deployment/continuous-integration-image2.png)

此動作會將您移至 Azure 入口網站，您可以在此處匯入先前匯出的範本。 依序選取 [在編輯器中建置您自己的範本]  、[載入檔案]  ，和產生的 Resource Manager 範本。 提供設定，將資料處理站和整個管線匯入生產環境中。

![](media/continuous-integration-deployment/continuous-integration-image3.png)

![](media/continuous-integration-deployment/continuous-integration-image4.png)

選取 [載入檔案]  以選取匯出的 Resource Manager 範本，並提供所有的組態值 (例如，連結服務)。

![](media/continuous-integration-deployment/continuous-integration-image5.png)

**連接字串**。 您可以在有關個別連接器的文章中找到建立連接字串所需的資訊。 例如，針對 Azure SQL Database，請參閱[使用 Azure Data Factory 將資料複製到 Azure SQL Database 或從該處複製資料](connector-azure-sql-database.md)。 若要確認正確的連接字串 (例如，針對連結的服務)，您也可以在 Data Factory UI 中開啟資源的程式碼檢視。 不過，在程式碼檢視中，連接字串的密碼或帳戶金鑰部分已遭移除。 若要開啟程式碼檢視，選取下列螢幕擷取畫面中醒目提示的圖示。

![開啟程式碼檢視以查看連接字串](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="continuous-integration-lifecycle"></a>持續整合生命週期
以下是您在 Data Factory UI 中啟用 Azure Repos Git 整合之後所能使用的持續整合和傳遞的完整生命週期：

1.  使用可讓所有開發人員撰寫 Data Factory 資源 (例如管線、資料集等等) 的 Azure Repos 設定開發資料處理站。

1.  接著，開發人員可以修改管線之類的資源。 在進行修改時，他們可以選取 [偵錯]  以查看管線在經過最新變更之後的執行情況。

1.  如果開發人員滿意其變更，他們可以從其分支建立對主要分支 (或共同作業分支) 的提取要求，讓同事檢閱其變更。

1.  變更送至主要分支後，相關人員可藉由選取 [發行]  將其發行至開發處理站。

1.  當小組準備好要將變更升階至測試處理站和生產處理站時，他們可以從主要分支匯出 Resource Manager 範本，或者，若其主要分支支援即時開發 Data Factory，則可從任何其他分支匯出。

1.  匯出的 Resource Manager 範本可使用不同的參數檔案部署至測試處理站和生產處理站。

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>透過 Azure Pipelines 發行將持續整合自動化

以下是設定 Azure Pipelines 發行以將資料處理站自動部署至多個環境的步驟。

![透過 Azure Pipelines 進行持續整合的圖表](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>需求

-   使用  [*Azure Resource Manager 服務端點*](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm)連結至 Team Foundation Server 或 Azure Repos 的 Azure 訂用帳戶。

-   設定了 Azure Repos Git 整合的 Data Factory。

-   包含密碼的  [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 。

### <a name="set-up-an-azure-pipelines-release"></a>設定 Azure Pipelines 發行

1.  移至您以 Data Factory 設定之相同專案中的 Azure Repos 頁面。

1.  按一下頂層功能表的 [Azure Pipelines]  &gt; [發行]  &gt; [建立發行定義]  。

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  選取 [空白流程]  範本。

1.  輸入您的環境名稱。

1.  新增 Git 成品，並選取以 Data Factory 設定的相同存放庫。 選擇 `adf_publish` 作為具有最新預設版本的預設分支。

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  新增 Azure Resource Manager 部署工作：

    a.  建立新的工作、搜尋 **Azure 資源群組部署**，並加以新增。

    b.  在部署工作中，選擇目標 Data Factory 的訂用帳戶、資源群組和位置，然後視需要提供認證。

    c.  選取**建立或更新資源群組**動作。

    d.  選取 **…** (位於 [範本]  欄位中)。 瀏覽在入口網站中以發行動作建立的 Resource Manager 範本 (*ARMTemplateForFactory.json*)。 在 `adf_publish` 分支的 `<FactoryName>` 資料夾中尋找此檔案。

    e.  對參數檔案執行相同的動作。 根據您是已建立複本還是使用預設檔案 *ARMTemplateParametersForFactory.json*，選擇正確的檔案。

    f.  選取 **…** (位於 [覆寫範本參數]  欄位旁)，並填入目標 Data Factory 的資訊。 對於來自金鑰保存庫的認證，請為密碼使用下列格式的相同名稱：假設密碼的名稱是 `cred1`，請輸入 `"$(cred1)"` (引號之間的內容)。

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    g. 選取 [累加]  部署模式。

    > [!WARNING]
    > 如果您選取 [完整]  部署模式，則可能會刪除現有的資源，包括目標資源群組中所有未定義於 Resource Manager 範本內的資源。

1.  儲存發行管線。

1.  從這個發行管線建立新發行。

    ![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="optional---get-the-secrets-from-azure-key-vault"></a>選用 - 從 Azure Key Vault 取得祕密

如果您要將祕密傳遞至 Azure Resource Manager 範本，建議您搭配使用 Azure Key Vault 與 Azure Pipelines 發行。

有兩種方式可處理密碼︰

1.  將祕密新增至參數檔案。 如需詳細資訊，請參閱[在部署期間使用 Azure Key Vault 以傳遞安全的參數值](../azure-resource-manager/resource-manager-keyvault-parameter.md)。

    -   建立已上傳至發行分支的參數檔案複本，並以下列格式設定您想要從金鑰保存庫取得的參數值：

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    -   當您使用此方法時，系統會自動從金鑰保存庫提取密碼。

    -   參數檔案也必須位於發行分支中。

1.  在上一節中所述的 Azure Resource Manager 部署之前，新增 [Azure Key Vault 工作](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault)：

    -   選取 [工作]  索引標籤、建立新的工作、搜尋 **Azure Key Vault**，並加以新增。

    -   在 Key Vault 工作中，選擇您用來建立金鑰保存庫的訂用帳戶、視需要提供認證，然後選擇金鑰保存庫。

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>為 Azure Pipelines 代理程式授與權限
Azure Key Vault 工作可能會失敗並發生拒絕存取錯誤 fIntegration 整合執行階段。 請下載發行的記錄，並使用命令找出 `.ps1` 檔案，為 Azure Pipelines 代理程式指定權限。 您可以直接執行此命令，或是從檔案複製主體識別碼，然後在 Azure 入口網站中手動新增存取原則。 (*取得*和*列出*是所需的最低權限)。

### <a name="update-active-triggers"></a>更新使用中的觸發程序
如果您嘗試更新使用中的觸發程序，部署可能會失敗。 若要更新使用中的觸發程序，您必須手動將其停止，等部署完成後再加以啟動。 為此，您可以新增 Azure Powershell 工作，如下列範例所示：

1.  在發行的 [工作] 索引標籤中，搜尋 **Azure Powershell** 並加以新增。

1.  選擇 **Azure Resource Manager** 作為連線類型，然後選取您的訂用帳戶。

1.  選擇 [內嵌指令碼]  作為指令碼類型，然後提供您的程式碼。 下列範例會停止觸發程序：

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

在部署之後，您可以依照類似的步驟，並使用 (與 `Start-AzDataFactoryV2Trigger` 函式) 類似的程式碼，來重新啟動觸發程序。

> [!IMPORTANT]
> 在持續整合和部署案例中，跨不同環境的整合執行階段類型必須是相同的。 例如，如果您在開發環境中有*自我裝載*整合執行階段 (IR)，則相同的 IR 在其他環境 (例如測試和生產環境) 中也必須屬於*自我裝載*類型。 同樣地，如果您要跨多個階段共用整合執行階段，則必須將所有環境中的整合執行階段設定為*連結自我裝載*，例如開發、測試和生產環境。

## <a name="sample-deployment-template"></a>範例部署範本

以下是您可以在 Azure Pipelines 中匯入的範例部署範本。

```json
{
    "source": 2,
    "id": 1,
    "revision": 51,
    "name": "Data Factory Prod Deployment",
    "description": null,
    "createdBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "createdOn": "2018-03-01T22:57:25.660Z",
    "modifiedBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "modifiedOn": "2018-03-14T17:58:11.643Z",
    "isDeleted": false,
    "path": "\\",
    "variables": {},
    "variableGroups": [],
    "environments": [{
        "id": 1,
        "name": "Prod",
        "rank": 1,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserprod"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 1
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 2
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 3
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param\n(\n    [parameter(Mandatory = $false)] [String] $rootFolder=\"C:\\Users\\sampleuser\\Downloads\\arm_template\",\n    [parameter(Mandatory = $false)] [String] $armTemplate=\"$rootFolder\\arm_template.json\",\n    [parameter(Mandatory = $false)] [String] $armTemplateParameters=\"$rootFolder\\arm_template_parameters.json\",\n    [parameter(Mandatory = $false)] [String] $domain=\"microsoft.onmicrosoft.com\",\n    [parameter(Mandatory = $false)] [String] $TenantId=\"72f988bf-86f1-41af-91ab-2d7cd011db47\",\n    [parame",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": "5.*"
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "secret1",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/1"
    }, {
        "id": 2,
        "name": "Staging",
        "rank": 2,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserstaging"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 4
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 5
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 6
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "16a37943-8b58-4c2f-a3d6-052d6f032a07",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param(\n$x,\n$y,\n$z)\nwrite-host \"----------\"\nwrite-host $x\nwrite-host $y\nwrite-host $z | ConvertTo-SecureString\nwrite-host \"----------\"",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/2"
    }],
    "artifacts": [{
        "sourceId": "19749ef3-2f42-49b5-9696-f28b49faebcb:a6c88f30-5e1f-4de8-b24d-279bb209d85f",
        "type": "Git",
        "alias": "Dev",
        "definitionReference": {
            "branches": {
                "id": "adf_publish",
                "name": "adf_publish"
            },
            "checkoutSubmodules": {
                "id": "",
                "name": ""
            },
            "defaultVersionSpecific": {
                "id": "",
                "name": ""
            },
            "defaultVersionType": {
                "id": "latestFromBranchType",
                "name": "Latest from default branch"
            },
            "definition": {
                "id": "a6c88f30-5e1f-4de8-b24d-279bb209d85f",
                "name": "Dev"
            },
            "fetchDepth": {
                "id": "",
                "name": ""
            },
            "gitLfsSupport": {
                "id": "",
                "name": ""
            },
            "project": {
                "id": "19749ef3-2f42-49b5-9696-f28b49faebcb",
                "name": "Prod"
            }
        },
        "isPrimary": true
    }],
    "triggers": [{
        "schedule": {
            "jobId": "b5ef09b6-8dfd-4b91-8b48-0709e3e67b2d",
            "timeZoneId": "UTC",
            "startHours": 3,
            "startMinutes": 0,
            "daysToRelease": 31
        },
        "triggerType": 2
    }],
    "releaseNameFormat": "Release-$(rev:r)",
    "url": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1",
    "_links": {
        "self": {
            "href": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1"
        },
        "web": {
            "href": "https://sampleuser.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_release?definitionId=1"
        }
    },
    "tags": [],
    "properties": {
        "DefinitionCreationSource": {
            "$type": "System.String",
            "$value": "ReleaseNew"
        }
    }
}
```

## <a name="sample-script-to-stop-and-restart-triggers-and-clean-up"></a>用以停止並重新啟動觸發程序和清除的範例指令碼

以下是可在部署之前停止觸發程序並在其後重新啟動觸發程序的範例指令碼。 此指令碼也包含可將已移除的資源刪除的程式碼。 若要安裝最新版的 Azure PowerShell，請參閱[使用 PowerShellGet 在 Windows 上安裝 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>搭配使用自訂參數與 Resource Manager 範本

如果您是在 GIT 模式中，您可以在 Resource Manager 範本中的範本和硬式編碼的屬性設定為參數化的屬性來覆寫預設屬性。 您可能想要覆寫預設的參數化範本，在這些情況下：

* 使用自動化的 CI/CD 和您想要在 Resource Manager 部署期間變更某些屬性，但屬性不預設參數化。
* 您的處理站是很大的預設資源管理員範本不正確的因為其中有超過最大允許參數 (256)。

根據這些條件，來覆寫預設參數化的範本，建立名為 *arm 範本參數-definition.json* 存放庫的根資料夾中。 檔案名稱必須完全符合。 Data Factory 會嘗試讀取此檔案，從您目前是在 Azure Data Factory 入口網站中任何分支，而不只是從共同作業分支。 您可以建立或編輯的檔案，從私用分支，其中測試您的變更，以及在使用**匯出 ARM 範本**在 UI 中。 然後，您可以將檔案合併共同作業分支。 如果不找到任何檔案，就會使用預設範本。


### <a name="syntax-of-a-custom-parameters-file"></a>自訂參數檔案的語法

以下是您撰寫自訂的參數檔案時所要使用的一些指導方針。 此檔案包含每個實體類型的區段： 觸發程序、 管線、 連結服務、 資料集、 integrationruntime，等等。
* 輸入下相關的實體類型的屬性路徑。
* 當您設定的屬性名稱為 '\*'，您可以指定您想要參數化其下的所有屬性 （只到第一個層級，不以遞迴方式）。 您也可以提供任何例外狀況。
* 當您將屬性的值設定為字串時，就表示您想要將屬性參數化。 使用格式 `<action>:<name>:<stype>`。
   *  `<action>` 可以是下列字元：
      * `=` 表示會保留目前的值當做參數的預設值。
      * `-` 表示不要保留參數的預設值。
      * `|` 是從 Azure Key Vault 祕密的連接字串或金鑰的特殊案例。
   * `<name>` 為參數的名稱。 如果空白，它會採用屬性的名稱。 如果值的開頭`-`字元，已縮短名稱。 例如，`AzureStorage1_properties_typeProperties_connectionString`會縮短為`AzureStorage1_connectionString`。
   * `<stype>` 為參數的型別。 如果 `<stype>` 是空白，預設的類型是`string`。 支援的值： `string`， `bool`， `number`， `object`，和`securestring`。
* 當您定義檔中指定的陣列時，您可以指定範本中相符的屬性是陣列。 Data Factory 逐一查看陣列中的所有物件使用指定陣列的整合執行階段物件中定義。 第二個物件 (字串) 會變成屬性的名稱，以作為每個反覆項目參數的名稱。
* 您不可以有特定資源執行個體的定義。 任何定義適用於該類型的所有資源。
* 根據預設，所有的安全字串，例如金鑰保存庫密碼和安全字串，例如連接字串、 金鑰和權杖，會進行參數化。
 
## <a name="sample-parameterization-template"></a>範例參數化的範本

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```

### <a name="explanation"></a>說明：

#### <a name="pipelines"></a>管線
    
* 路徑為活動/typeProperties/waitTimeInSeconds 中的任何屬性已參數化。 這表示有一個名為的程式碼層級屬性的管線中的任何活動`waitTimeInSeconds`(例如`Wait`活動) 的數字，以預設名稱參數化。 但是，將不會在 Resource Manager 範本中有預設值。 在 Resource Manager 部署期間會強制性的輸入。
* 同樣地，屬性稱為`headers`(例如，在`Web`活動) 以類型參數化`object`(JObject)。 它有預設值，也就是與來源 factory 相同的值。

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* 只有屬性和所有的內容路徑下`typeProperties`會進行參數化，以其各自的預設值。 例如，截至今天的結構描述中，有兩個屬性底下**IntegrationRuntimes**型別屬性：`computeProperties`和`ssisProperties`。 這兩種屬性類型會建立其各自的預設值和類型 （物件）。

#### <a name="triggers"></a>觸發程序

* 在下`typeProperties`，兩個屬性會進行參數化。 第一個是`maxConcurrency`、 具有預設值，指定和型別會是`string`。 它具有預設的參數名稱的`<entityName>_properties_typeProperties_maxConcurrency`。
* `recurrence`屬性也已參數化。 在其下方該層級的所有屬性會都指定為字串，預設值和參數名稱與參數化。 例外狀況是`interval`屬性，這是參數化為數字 類型，並為參數名稱後面加上`<entityName>_properties_typeProperties_recurrence_triggerSuffix`。 同樣地，`freq`屬性的字串作業，且已參數化做為字串。 不過，`freq`屬性已參數化，沒有預設值。 名稱會縮短，並在後面加上。 例如： `<entityName>_freq`。

#### <a name="linkedservices"></a>LinkedServices

* 連結的服務是唯一的。 因為連結的服務和資料集可能會針對數種，您可以提供特定類型的自訂。 比方說，您可能會說，所有連結的服務型別的`AzureDataLakeStore`，會套用，而所有其他特定的範本 (透過\*) 將會套用不同的範本。
* 在上述範例中，`connectionString`屬性會參數化為`securestring`的值，將不會有預設值，而且會有後置字元的簡短的參數名稱`connectionString`。
* 屬性`secretAccessKey`，不過，剛好`AzureKeyVaultSecret`(比方說，`AmazonS3`連結服務)。 因此，為 Azure Key Vault 祕密已自動參數化，它從已使用來源 factory 中的金鑰保存庫中提取。 您也可參數化的金鑰保存庫本身。

#### <a name="datasets"></a>資料集

* 即使特定型別的自訂適用於資料集，可以提供組態而不需要明確地\*-層級設定。 在上述範例中下的所有資料集屬性`typeProperties`會進行參數化。

可以變更預設參數化的範本，但這是目前的範本。 這會很有用，如果您只需要新增一個額外的屬性做為參數，但也如果您不想失去現有的參數化，而且必須重新建立這些項目。


```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

**範例**：新增 Databricks 互動式叢集識別碼 （從 Databricks 連結服務） 參數檔案：

```
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```


## <a name="linked-resource-manager-templates"></a>連結的 Resource Manager 範本

如果您已為資料處理站設定持續整合和部署 (CI/CD)，則可能會發現，隨著您的處理站成長變大，您會遇到 Resource Manager 範本限制，例如 Resource Manager 範本中的資源數目上限或承載上限。 對於這類案例，除了產生處理站的完整 Resource Manager 範本，Data Factory 現在也會產生連結的 Resource Manager 範本。 因此，您將整個處理站承載分成數個檔案，如此才不會遇到所提的限制。

如果您已設定 Git，則會產生連結的範本並與完整 Resource Manager 範本一起儲存在 `adf_publish` 分支中名為 `linkedTemplates` 的新資料夾中。

![連結的 Resource Manager 範本資料夾](media/continuous-integration-deployment/linked-resource-manager-templates.png)

連結的 Resource Manager 範本通常有一個主要範本，以及一組連結到主要範本的子系範本。 父代範本稱為 `ArmTemplate_master.json`，而子系範本的命名模式為 `ArmTemplate_0.json`、`ArmTemplate_1.json`，依此類推。 若要從使用完整 Resource Manager 範本改為使用連結的範本，請將 CI/CD 工作更新為指向 `ArmTemplate_master.json`，而不是指向 `ArmTemplateForFactory.json` (也就是完整 Resource Manager 範本)。 Resource Manager 也會要求您將連結的範本上傳至儲存體帳戶，以便 Azure 在部署期間存取這些範本。 如需詳細資訊，請參閱[透過 VSTS 部署連結的 ARM 範本](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/) (英文)。

請記得在在部署工作之前和之後，於 CI/CD 管線中新增 Data Factory 指令碼。

如果您未設定 Git，則可透過 [匯出 ARM 範本]  手勢存取連結的範本。

## <a name="best-practices-for-cicd"></a>CI/CD 的最佳做法

如果您將 Git 整合與資料處理站搭配使用，而且您具備 CI/CD 管線，可將您的變更從開發轉移到測試，然後再轉移到生產，我們建議您採用以下最佳做法：

-   **Git 整合**。 您只需要使用 Git 整合設定您的開發資料處理站。 測試與生產的變更已透過 CI/CD 部署，而且不需要具備 Git 整合。

-   **Data Factory CI/CD 指令碼**。 在 CI/CD 中的 Resource Manager 部署步驟之前，您必須處理諸如停止觸發程序和不同類型的中心清理等事項。 建議使用[此指令碼](#sample-script-to-stop-and-restart-triggers-and-clean-up)，因為它會處理所有事項。 在部署之前執行一次，然後使用適當的旗標。

-   **整合執行階段和共用**。 整合執行階段是資料處理站的基礎結構組件之一，它不會經常變更，而且在 CI/CD 中的所有階段皆相似。 因此，Data Factory 預期在所有階段的 CI/CD 中，要有相同的名稱和相同類型的整合執行階段。 如果您希望在所有階段中共用整合執行階段 - 例如，自我裝載整合執行階段 - 這是一種在三元中心主控自我裝載整合執行階段的共用方式，僅適用於包含共用的整合執行階段。 然後您可以在開發/測試/生產中，將其做為連結的整合執行階段類型。

-   **Key Vault**。 當您使用以建議之 Azure Key Vault 為基礎的連結服務時，可以藉由為開發/測試/生產保留單獨的金鑰保存庫，進一步發揮其優勢。您也可以為每個服務設定不同的權限等級。 您可能不希望團隊成員有生產環境密碼的權限。 我們也建議您在所有階段之間保留相同的密碼名稱。 如果保留相同的名稱，不需要跨 CI/CD 變更 Resource Manager 範本，因為唯一需要變更的是金鑰保存庫名稱，這是 Resource Manager 範本參數之一。

## <a name="unsupported-features"></a>不支援的功能

-   因為資料處理站實體彼此相依，您無法發佈個別資源。 例如，觸發程序取決於管線，管線取決於資料集和其他管線等等。追蹤變更的相依性並不容易。 如果可以選擇手動發佈的資源，可以僅選擇整組變更的子集，這將導致發佈後出現未預期的行為。

-   您無法從私人分支發佈。

-   您無法在 Bitbucket 上裝載專案。
