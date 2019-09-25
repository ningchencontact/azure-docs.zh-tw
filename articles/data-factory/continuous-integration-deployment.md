---
title: Azure Data Factory 中的持續整合和傳遞 | Microsoft Docs
description: 了解如何使用持續整合和傳遞將一個環境 (開發、測試、生產) 中的 Data Factory 管線移至另一個環境。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 4386a7adba17eefe3c373697597abdb7d69c476a
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265974"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Azure Data Factory 中的持續整合和傳遞 (CI/CD)

## <a name="overview"></a>總覽

持續整合是指進行相關實作，以自動並及早測試對您的程式碼基底所做的每項變更。 在持續整合期間執行測試，並將變更推送至暫存或生產系統後，就會進行持續傳遞。

在 Azure Data Factory 中，持續整合 & 傳遞表示將 Data Factory 管線從一個環境（開發、測試、生產）移至另一個環境。 若要進行持續整合 & 傳遞，您可以使用與 Azure Resource Manager 範本 Data Factory UX 整合。 Data Factory UX 可以從**ARM 範本**下拉式清單產生 Resource Manager 範本。 當您選取 [匯出 ARM 範本] 時，入口網站將會產生資料處理站的 Resource Manager 範本，以及包含您所有連接字串和其他參數的組態檔。 接著，您必須為每個環境（開發、測試、生產）建立一個設定檔。 所有環境的主要 Resource Manager 範本檔案會保持相同。

如需此功能的 9 分鐘簡介與示範，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="continuous-integration-lifecycle"></a>持續整合生命週期

以下範例概述使用 Azure Repos Git 設定的 Azure Data factory 中的持續整合和傳遞生命週期。 如需如何設定 Git 儲存機制的詳細資訊，請參閱[Azure Data Factory 中的原始檔控制](source-control.md)。

1.  開發資料處理站會使用 Azure Repos Git 建立和設定，其中所有開發人員都有權撰寫 Data Factory 的資源，例如管線和資料集。

1.  當開發人員在其功能分支中進行變更時，他們會以最新的變更來進行管線執行的調試。 如需如何對管線執行進行偵錯工具的詳細資訊，請參閱[使用 Azure Data Factory 的反復式開發和調試](iterative-development-debugging.md)。

1.  一旦開發人員對其變更感到滿意之後，就會從其功能分支建立提取要求至主要或共同作業分支，以取得對等的已審核變更。

1.  在提取要求經過核准並在主要分支中合併變更之後，即可將其發行至開發 factory。

1.  當小組準備好要將變更部署到測試處理站，然後再部署到生產工廠時，他們會從 master 分支匯出 Resource Manager 範本。

1.  匯出的 Resource Manager 範本會以不同的參數檔案部署到測試處理站和生產工廠。

## <a name="create-a-resource-manager-template-for-each-environment"></a>為每個環境建立 Resource Manager 範本

在 [ **ARM 範本**] 下拉式清單中，選取 [**匯出 ARM 範本**]，以在開發環境中匯出 data factory 的 Resource Manager 範本。

![](media/continuous-integration-deployment/continuous-integration-image1.png)

在您的測試和生產資料處理站中，選取 [匯**入 ARM 範本**]。 此動作會將您移至 Azure 入口網站，您可以在此處匯入先前匯出的範本。 **在編輯器中選取 [建立您自己的範本**]，以開啟 [Resource Manager 範本編輯器]。

![](media/continuous-integration-deployment/continuous-integration-image3.png) 

按一下 [**載入**檔案]，然後選取產生的 Resource Manager 範本。

![](media/continuous-integration-deployment/continuous-integration-image4.png)

在 [設定] 窗格中，輸入 [連結服務認證] 之類的設定值。 完成後，請按一下 [**購買**] 以部署 Resource Manager 範本。

![](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>連接字串

您可以在每個連接器的文章中找到如何設定連接字串的資訊。 例如，針對 Azure SQL Database，請參閱[使用 Azure Data Factory 將資料複製到 Azure SQL Database 或從該處複製資料](connector-azure-sql-database.md)。 若要驗證連接字串，您可以在 Data Factory UX 中開啟資源的程式碼視圖。 在程式碼視圖中，會移除連接字串的密碼或帳戶金鑰部分。 若要開啟程式碼檢視，選取下列螢幕擷取畫面中醒目提示的圖示。

![開啟程式碼檢視以查看連接字串](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>透過 Azure Pipelines 發行將持續整合自動化

以下是設定 Azure Pipelines 版本的指南，可將資料處理站的部署自動化到多個環境。

![透過 Azure Pipelines 進行持續整合的圖表](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>需求

-   連結至使用 [Azure Resource Manager 服務端點](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm)Team Foundation Server 或 Azure Repos 的 Azure 訂用帳戶。

-   設定 Azure Repos Git 整合的 Data Factory。

-    [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ，其中包含每個環境的密碼。

### <a name="set-up-an-azure-pipelines-release"></a>設定 Azure Pipelines 發行

1.  在 [ [Azure DevOps 使用者體驗](https://dev.azure.com/)] 中，開啟以您的 Data Factory 設定的專案。

1.  在頁面的左側，按一下 [**管線**]，然後選取 [**發行**]。

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  選取 [**新增管線**]，或如果您有現有的管線、[**新增**] 和 [**新增發行管線**]。

1.  選取 [**空白作業**] 範本。

    ![](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  在 [**階段名稱**] 欄位中，輸入您的環境名稱。

1.  選取 [**新增**成品]，然後選取使用您的 Data Factory 設定的相同存放庫。 選擇 `adf_publish` 作為具有最新預設版本的預設分支。

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  新增 Azure Resource Manager 部署工作：

    a.  在階段視圖中，按一下 [**視圖階段**工作] 連結。

    ![](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  建立新工作。 搜尋 [ **Azure 資源群組部署**]，然後按一下 [**新增**]。

    c.  在部署工作中，選擇目標 Data Factory 的訂用帳戶、資源群組和位置，然後視需要提供認證。

    d.  在 [動作] 下拉式清單中，選取 [**建立或更新資源群組**]。

    e.  選取 **…** (位於 [範本] 欄位中)。 在[為每個環境建立 Resource Manager 範本](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment)中，流覽透過「匯**入 ARM 範本**」步驟建立的 Azure Resource Manager 範本。 在 `adf_publish` 分支的 `<FactoryName>` 資料夾中尋找此檔案。

    f.  選取 **…** 在 [**範本參數] 欄位中。** 選擇參數檔案。 根據您是已建立複本還是使用預設檔案 *ARMTemplateParametersForFactory.json*，選擇正確的檔案。

    g.  選取 **…** (位於 [覆寫範本參數] 欄位旁)，並填入目標 Data Factory 的資訊。 針對來自金鑰保存庫的認證，輸入雙引號之間的密碼名稱。 例如，如果密碼的名稱是`cred1`，請輸入`"$(cred1)"`作為其值。

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    h. 選取 [累加] 部署模式。

    > [!WARNING]
    > 如果您選取 [**完整**部署模式]，可能會刪除現有的資源，包括未在 [Resource Manager] 範本中定義的目標資源群組中的所有資源。

1.  儲存發行管線。

1. 若要觸發發行，請按一下 [**建立發行**]

![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="get-secrets-from-azure-key-vault"></a>從 Azure Key Vault 取得秘密

如果您有要傳入 Azure Resource Manager 範本的密碼，建議您在 Azure Pipelines 版本中使用 Azure Key Vault。

有兩種方式可處理密碼：

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

    -   選取 [工作] 索引標籤、建立新的工作、搜尋 **Azure Key Vault**，並加以新增。

    -   在 Key Vault 工作中，選擇您用來建立金鑰保存庫的訂用帳戶、視需要提供認證，然後選擇金鑰保存庫。

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>為 Azure Pipelines 代理程式授與權限

如果沒有適當的許可權，Azure Key Vault 工作可能會失敗，並出現「拒絕存取」錯誤。 請下載發行的記錄，並使用命令找出 `.ps1` 檔案，為 Azure Pipelines 代理程式指定權限。 您可以直接執行此命令，或是從檔案複製主體識別碼，然後在 Azure 入口網站中手動新增存取原則。 **Get**和**List**是所需的最小許可權。

### <a name="update-active-triggers"></a>更新使用中的觸發程序

如果您嘗試更新使用中的觸發程序，部署可能會失敗。 若要更新使用中的觸發程序，您必須手動將其停止，等部署完成後再加以啟動。 您可以透過 Azure Powershell 工作來執行此動作。

1.  在發行的 [工作] 索引標籤中，新增**Azure Powershell**工作。

1.  選擇 **Azure Resource Manager** 作為連線類型，然後選取您的訂用帳戶。

1.  選擇 [內嵌指令碼] 作為指令碼類型，然後提供您的程式碼。 下列範例會停止觸發程序：

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

您可以遵循類似的`Start-AzDataFactoryV2Trigger`步驟（使用函式），在部署之後重新開機觸發程式。

> [!IMPORTANT]
> 在持續整合和部署案例中，跨不同環境的整合執行階段類型必須是相同的。 例如，如果您在開發環境中有*自我裝載*整合執行階段 (IR)，則相同的 IR 在其他環境 (例如測試和生產環境) 中也必須屬於*自我裝載*類型。 同樣地，如果您要跨多個階段共用整合執行階段，則必須將所有環境中的整合執行階段設定為*連結自我裝載*，例如開發、測試和生產環境。

#### <a name="sample-prepostdeployment-script"></a>範例前置/部署後腳本

以下範例腳本可在部署之前停止觸發程式，並于之後重新開機觸發程式。 此指令碼也包含可將已移除的資源刪除的程式碼。 若要安裝最新版的 Azure PowerShell，請參閱[使用 PowerShellGet 在 Windows 上安裝 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

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

如果您是在 GIT 模式中，您可以覆寫 Resource Manager 範本中的預設屬性，以設定範本中參數化的屬性，以及硬式編碼的屬性。 在這些情況下，您可能會想要覆寫預設參數化範本：

* 您會使用自動化的 CI/CD，而您想要在 Resource Manager 部署期間變更某些屬性，但預設不會將屬性參數化。
* 因為預設 Resource Manager 範本的數目超過允許的最大值（256），所以您的 factory 很大。

在這些情況下，若要覆寫預設參數化範本，請在存放庫的根資料夾 中建立名為 *arm-template-parameters-definition*的檔案。 檔案名必須完全相符。 Data Factory 會嘗試從您目前在 Azure Data Factory 入口網站中的任何分支讀取此檔案，而不只是從共同作業分支。 您可以從私人分支建立或編輯檔案，您可以在其中使用 UI 中的 [**匯出 ARM] 範本**來測試您的變更。 然後，您可以將檔案合併到共同作業分支。 如果找不到任何檔案，則會使用預設範本。


### <a name="syntax-of-a-custom-parameters-file"></a>自訂參數檔案的語法

以下是您在撰寫自訂參數檔案時所要使用的一些指導方針。 檔案是由每個實體類型的區段所組成：觸發程式、管線、連結服務、資料集、整合執行時間等等。
* 在相關實體類型下輸入屬性路徑。
* 當您將屬性名稱設定為 '\*' ' 時，表示您想要將它下的所有屬性（僅向下到第一個層級，而非以遞迴方式）參數化。 您也可以為此提供任何例外狀況。
* 當您將屬性的值設定為字串時，就表示您想要將屬性參數化。 使用格式 `<action>:<name>:<stype>`。
   *  `<action>` 可以是下列其中一個字元：
      * `=` 表示保留目前的值做為參數的預設值。
      * `-` 表示不保留參數的預設值。
      * `|` 這是一個特殊案例，用於連接字串或金鑰 Azure Key Vault 的秘密。
   * `<name>` 這是參數的名稱。 如果是空的，則會接受屬性的名稱。 如果值是以`-`字元開頭，則名稱會縮短。 例如， `AzureStorage1_properties_typeProperties_connectionString`會縮短為`AzureStorage1_connectionString`。
   * `<stype>` 這是參數的類型。 `string`如果 `<stype>`為空白，則 預設類型為。 支援的值`string`： `bool`、 `number`、 `object`、和`securestring`。
* 當您在定義檔中指定陣列時，就表示範本中的相符屬性是陣列。 Data Factory 會使用陣列的 Integration Runtime 物件中指定的定義，逐一查看陣列中的所有物件。 第二個物件 (字串) 會變成屬性的名稱，以作為每個反覆項目參數的名稱。
* 資源實例不可能有特定的定義。 任何定義都會套用至該類型的所有資源。
* 根據預設，系統會將所有安全字串（例如 Key Vault 秘密）和安全字串（例如連接字串、金鑰和權杖）參數化。
 
### <a name="sample-parameterization-template"></a>範例參數化範本

以下是參數化範本可能外觀的範例：

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
以下是如何建立上述範本的說明，並依資源類型細分。

#### <a name="pipelines"></a>管線
    
* Path activity/typeProperties/waitTimeInSeconds 中的任何屬性都已參數化。 管線中具有名為`waitTimeInSeconds`之程式碼層級屬性的任何活動（例如`Wait` ，活動）都會參數化為具有預設名稱的數位。 但它在 Resource Manager 範本中沒有預設值。 在 Resource Manager 部署期間，這會是必要的輸入。
* 同樣地，名`headers`為的屬性（例如， `Web`活動中的）會以類型`object` （JObject）進行參數化。 它具有預設值，其值與來源 factory 中的相同。

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* 路徑`typeProperties`下的所有屬性都會使用其各自的預設值進行參數化。 例如， **IntegrationRuntimes**類型屬性底下有兩個屬性： `computeProperties`和。 `ssisProperties` 這兩個屬性類型都會使用其各自的預設值和類型（物件）來建立。

#### <a name="triggers"></a>觸發程序

* 在`typeProperties`底下，會將兩個屬性參數化。 第一個是`maxConcurrency`，其指定為具有預設值，且的類型`string`為。 它的預設參數名稱`<entityName>_properties_typeProperties_maxConcurrency`是。
* `recurrence`屬性也已參數化。 在其底下，會指定該層級的所有屬性，以預設值和參數名稱參數化為字串。 例外狀況是`interval`屬性，其參數化為數位類型，且參數名稱`<entityName>_properties_typeProperties_recurrence_triggerSuffix`後置字元為。 同樣地， `freq`屬性是字串，而且會參數化為字串。 不過，屬性`freq`在沒有預設值的情況下參數化。 名稱會縮短並加上尾碼。 例如： `<entityName>_freq` 。

#### <a name="linkedservices"></a>LinkedServices

* 連結服務是唯一的。 因為已連結的服務和資料集有範圍廣泛的類型，所以您可以提供特定類型的自訂。 在此範例中，將會套用類型`AzureDataLakeStore`的所有連結服務、特定的範本，並針對所有其他專案\*（透過）套用不同的範本。
* 屬性會參數化`securestring`為值，它不會有預設值，而且會有加`connectionString`上尾碼的簡短參數名稱。 `connectionString`
* 屬性`secretAccessKey`剛好`AmazonS3`是（例如，在連結服務中）。 `AzureKeyVaultSecret` 它會自動參數化為 Azure Key Vault 秘密，並從已設定的金鑰保存庫提取。 您也可以將金鑰保存庫本身參數化。

#### <a name="datasets"></a>資料集

* 雖然特定類型的自訂適用于資料集，但您可以在不明確擁有\*層級設定的情況下提供設定。 在上述範例中，下`typeProperties`的所有資料集屬性都會參數化。

### <a name="default-parameterization-template"></a>預設參數化範本

以下是目前的預設參數化範本。 如果您只需要加入一個或數個參數，則直接編輯此項可能會很有説明，因為您不會遺失現有的參數化結構。

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

以下是如何將單一值新增至預設參數化範本的範例。 我們只想要將 Databricks 連結服務的現有 Databricks 互動式叢集識別碼新增到參數檔案。 請注意，下列檔案與上述檔案相同，但`existingClusterId`包含在的 [屬性] `Microsoft.DataFactory/factories/linkedServices`欄位底下。

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

如果您已為資料處理站設定持續整合和部署（CI/CD），您可能會在您的 factory 變得更大時遇到 Azure Resource Manager 範本限制。 限制的範例是 Resource Manager 範本中的資源數目上限。 為了配合大型工廠，並產生 factory 的完整 Resource Manager 範本，Data Factory 現在會產生連結的 Resource Manager 範本。 透過這項功能，整個 factory 承載會分成數個檔案，因此您不會遇到限制。

如果您已設定 Git，則會產生連結的範本，並將其與`adf_publish`分支中的完整 Resource Manager 範本一起儲存在名`linkedTemplates`為的新資料夾底下。

![連結的 Resource Manager 範本資料夾](media/continuous-integration-deployment/linked-resource-manager-templates.png)

連結的 Resource Manager 範本通常有一個主要範本，以及一組連結到主要範本的子系範本。 父代範本稱為 `ArmTemplate_master.json`，而子系範本的命名模式為 `ArmTemplate_0.json`、`ArmTemplate_1.json`，依此類推。 若要使用連結的範本，而不是完整的 Resource Manager 範本，請將您的`ArmTemplate_master.json` `ArmTemplateForFactory.json` CI/CD 工作更新為指向，而不是（完整 Resource Manager 範本）。 Resource Manager 也會要求您將連結的範本上傳至儲存體帳戶，以便 Azure 在部署期間存取這些範本。 如需詳細資訊，請參閱[透過 VSTS 部署連結的 ARM 範本](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/) (英文)。

請記得在在部署工作之前和之後，於 CI/CD 管線中新增 Data Factory 指令碼。

如果您未設定 Git，則可透過 [匯出 ARM 範本] 手勢存取連結的範本。

## <a name="hot-fix-production-branch"></a>熱修復生產分支

如果您將工廠部署到生產環境，併發現需要立即修正的 bug，但是您無法部署目前的共同作業分支，則可能需要部署熱修復。 這種方法稱為快速修正程式設計或 QFE。 

1.  在 Azure DevOps 中，移至已部署至生產環境的版本，並尋找已部署的最後一個認可。

2.  從認可訊息中，取得共同作業分支的認可識別碼。

3.  從該認可建立新的熱修復分支。

4.  移至 Azure Data Factory UX 並切換至此分支。

5.  使用 Azure Data Factory UX，修正 bug。 測試您的變更。

6.  驗證完成後，請按一下 [**匯出 ARM 範本**]，以取得熱修復 Resource Manager 範本。

7.  將此組建手動簽入 adf_publish 分支。

8.  如果您已將發行管線設定為根據 adf_publish 簽入自動觸發，則會自動啟動新的版本。 否則，請手動將發行排到佇列。

9.  將熱修正版本部署到測試和生產工廠。 此版本包含先前的生產環境承載加上步驟5中所做的修正。

10. 將修正程式中的變更新增至開發分支，讓之後的版本不會遇到相同的錯誤。

## <a name="best-practices-for-cicd"></a>CI/CD 的最佳做法

如果您將 Git 整合與資料處理站搭配使用，而且您具備 CI/CD 管線，可將您的變更從開發轉移到測試，然後再轉移到生產，我們建議您採用以下最佳做法：

-   **Git 整合**。 您只需要使用 Git 整合來設定您的開發資料處理站。 測試和生產環境的變更會透過 CI/CD 部署，而不需要 Git 整合。

-   **Data Factory CI/CD 指令碼**。 在 CI/CD 的 Resource Manager 部署步驟之前，需要執行某些工作，例如停止/啟動觸發程式和清除作業。 我們建議您在部署前後使用 powershell 腳本。 如需詳細資訊，請參閱更新作用中的[觸發](#update-active-triggers)程式。 

-   **整合執行階段和共用**。 整合執行時間不會經常變更，而且在 CI/CD 中的所有階段都很類似。 因此，Data Factory 預期在所有階段的 CI/CD 中，要有相同的名稱和相同類型的整合執行階段。 如果您想要跨所有階段共用整合執行時間，請考慮使用三元處理站，只用于包含共用整合執行時間。 您可以在所有環境中使用此共用 factory 作為連結的整合執行時間類型。

-   **Key Vault**。 當您使用以 Azure Key Vault 為基礎的連結服務時，您可以為不同的環境保留個別的金鑰保存庫，以進一步利用它的優點。 您也可以為每個服務設定不同的權限等級。 例如，您可能不希望小組成員擁有生產密碼的許可權。 如果您遵循此方法，建議您在所有階段保留相同的密碼名稱。 如果您保留相同的名稱，則不需要變更跨 CI/CD 環境的 Resource Manager 範本，因為唯一變更的是金鑰保存庫名稱，也就是其中一個 Resource Manager 範本參數。

## <a name="unsupported-features"></a>不支援的功能

- 根據設計，ADF_不_允許揀選挑選認可或選擇性發佈資源。 發行將包含在 data factory 中進行的**所有**變更

    - Data factory 實體彼此相依，例如，觸發程式取決於管線、管線相依于資料集和其他管線等等。選擇性發佈資源子集_可能_會導致未預期的行為和錯誤
    - 在需要選擇性發佈的罕見情況下，您可以考慮使用熱修復。 如需詳細資訊，請參閱[熱修復生產分支](#hot-fix-production-branch)

-   您無法從私人分支發佈

-   從現在開始，您無法在 Bitbucket 上裝載專案
