---
title: 使用 Execute SSIS 套件活動執行 SSIS 套件 - Azure | Microsoft Docs
description: 本文描述如何使用 Execute SSIS 套件活動，在 Azure Data Factory 管線執行 SQL Server Integration Services (SSIS) 套件。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/18/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 8e01ac4efa3c310b17e88351383861cbdccb68e6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58171103"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>在 Azure Data Factory 中使用 Execute SSIS 套件活動執行 SSIS 套件
本文描述如何使用 Execute SSIS 套件活動，在 Azure Data Factory (ADF) 管線執行 SSIS 套件。 

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果您未依照以下教學課程中的逐步指示進行，請建立 Azure SSIS 整合執行階段 (IR)：[教學課程：將 SSIS 套件部署至 Azure](tutorial-create-azure-ssis-runtime-portal.md)。

## <a name="run-a-package-in-the-azure-portal"></a>在 Azure 入口網站中執行套件
在本節中，您會使用 ADF 使用者介面 (UI)/應用程式來建立 ADF 管線，其中具有要執行 SSIS 套件的執行 SSIS 套件活動。

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>使用 Execute SSIS 套件活動建立管線
在此步驟中，您可以使用 ADF UI/應用程式建立管線。 您要將 Execute SSIS 套件活動新增至管線，並設定它來執行 SSIS 套件。 

1. 在 Azure 入口網站中的 ADF 概觀/首頁上，按一下 [撰寫與監視] 圖格，以在個別索引標籤中啟動 ADF UI/應用程式。 

   ![Data Factory 首頁](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   在 [讓我們開始吧] 頁面上，按一下 [建立管線]： 

   ![開始使用頁面](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. 在 [活動] 工具箱中展開 [一般]，然後將 [執行 SSIS 套件] 活動拖放至管線設計工具介面。 

   ![將執行 SSIS 套件活動拖曳至設計工具介面](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. 在 Execute SSIS 套件活動的 [一般] 索引標籤上，提供活動的名稱和描述。 設定選用的逾時和重試值。

   ![在 [一般] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. 在 Execute SSIS 套件活動的 [設定] 索引標籤上，選取與 SSISDB 資料庫 (套件部署所在) 相關聯的 Azure-SSIS IR。 如果您的套件會使用 Windows 驗證來存取資料存放區，例如 SQL 伺服器/檔案共用內部部署，Azure 檔案等檢查**Windows 驗證**核取方塊，然後輸入網域/使用者名稱/密碼為您的封裝執行。 如果您的套件需要 32 位元執行階段以便執行，請核取 [32 位元執行階段] 核取方塊。 針對 [記錄層級]，針對您的套件執行選取預先定義的記錄範圍。 如果您想要改為輸入您的自訂記錄名稱，請核取 [自訂] 核取方塊。 執行您的 Azure-SSIS IR 且 [手動項目] 核取方塊未核取時，您可以瀏覽並從 SSISDB 選取現有資料夾/專案/套件/環境。 按一下 [重新整理] 按鈕，擷取您剛剛從 SSISDB 新增的資料夾/專案/套件/環境，讓它們可供瀏覽和選取。 

   ![在 [設定] 索引標籤上設定屬性 - 自動化](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   未執行您的 Azure SSIS IR 時或**手動的項目**核取方塊已核取，您可以從 SSISDB 輸入您的封裝和環境路徑，直接以下列格式：`<folder name>/<project name>/<package name>.dtsx`和`<folder name>/<environment name>`。

   ![在 [設定] 索引標籤上設定屬性 - 手動](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

5. 在執行 SSIS 套件活動的 [SSIS 參數] 索引標籤上，當您的 Azure-SSIS IR 執行中且未核取 [設定] 索引標籤上的 [手動項目] 核取方塊時，會為您顯示從 SSISDB 選取的專案/套件的現有 SSIS 參數，讓您為它們指派值。 否則，您可以逐一輸入以手動將值指派給它們，請確定它們存在且已正確輸入，您的套件執行才會成功。 您可以將動態內容新增至它們使用運算式、 函式、 ADF 系統變數和 ADF 管線的參數/變數的值。 或者，您可以使用儲存在您的 Azure Key Vault (AKV) 做為其值的祕密。 若要這樣做，請按一下**AZURE KEY VAULT**相關的參數，旁邊的核取方塊選取/編輯現有連結的 AKV 服務或建立新的程式，然後選取祕密參數值的名稱/版本。  當您建立/編輯連結的 AKV 服務時，您可以選取/編輯您現有的 AKV 或建立新的連線，但請 ADF 受控身分識別存取權授與您 AKV 如果您還沒做這樣。 您也可以直接以下列格式輸入您的祕密： `<AKV linked service name>/<secret name>/<secret version>`。

   ![在 [SSIS 參數] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. 上**連接管理員**索引標籤上執行 SSIS 套件活動，在執行您的 Azure SSIS IR 時，**手動項目**上的核取方塊**設定** 索引標籤上未選取，在您所選專案/套件從 SSISDB 的現有連接管理員會顯示，以將值指派給它們的屬性。 否則，您可以輸入它們其中一個用來以手動方式將值指派給它們的屬性，請確定它們存在，並且為您的封裝執行成功輸入正確的。 您可以將動態內容新增至它們使用運算式、 函式、 ADF 系統變數和 ADF 管線的參數/變數的屬性值。 或者，您可以使用儲存在您的 Azure Key Vault (AKV) 做為其屬性值的祕密。 若要這樣做，請按一下**AZURE KEY VAULT**相關屬性旁的核取方塊選取/編輯現有連結的 AKV 服務或建立新的程式，然後選取祕密名稱/版本的屬性值。  當您建立/編輯連結的 AKV 服務時，您可以選取/編輯您現有的 AKV 或建立新的連線，但請 ADF 受控身分識別存取權授與您 AKV 如果您還沒做這樣。 您也可以直接以下列格式輸入您的祕密： `<AKV linked service name>/<secret name>/<secret version>`。

   ![在 [連線管理員] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. 在執行 SSIS 套件活動的 [屬性覆寫] 索引標籤上，您可以逐一輸入從 SSISDB 選取的套件中現有屬性的路徑，手動為它們指派值，請確定它們存在且已正確輸入，您的套件執行才會成功，例如，若要覆寫您使用者變數的值，請使用下列格式輸入其路徑：`\Package.Variables[User::YourVariableName].Value`。 您也可以使用運算式、函式、ADF 系統變數及 ADF 管線參數/變數，將動態內容新增至其值。

   ![在 [屬性覆寫] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

8. 若要驗證管線設定，按一下工具列上的 [驗證]。 若要關閉 [管線驗證報告]，按一下 **>>**。

9. 藉由按一下 [全部發佈] 按鈕，將管線發佈到 ADF。 

### <a name="run-the-pipeline"></a>執行管道
在此步驟中，您會觸發管線執行。 

1. 若要觸發管線執行，按一下工具列上的 [觸發程序]，然後按一下 [立即觸發]。 

   ![立即觸發](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. 在 [管線執行] 視窗中，選取 [完成]。 

### <a name="monitor-the-pipeline"></a>監視管線

1. 切換至左側的 [監視] 索引標籤。 您會看到管線執行、其狀態，以及其他資訊 (例如執行開始時間)。 若要重新整理檢視，按一下 [重新整理]。

   ![管線執行](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. 按一下 [動作]資料行中的 [檢視活動執行] 連結。 您只會看到一個活動執行，因為該管線只有一個活動 (Execute SSIS 套件活動)。

   ![活動執行](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. 您可以依據 Azure SQL Server 中的 SSISDB 資料庫執行下列**查詢**，來確認套件已執行。 

   ```sql
   select * from catalog.executions
   ```

   ![確認封裝執行](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. 您也可以從管線活動執行的輸出取得 SSISDB 執行識別碼，並使用識別碼來檢查更完整的執行記錄和 SSMS 中的錯誤訊息。

   ![取得執行識別碼。](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>使用觸發程序排程管線

您也可以建立管線的排程觸發程序，以便管線依排程執行 (每小時、每日等等)。 如需範例，請參閱[建立資料處理站 - 資料處理站 UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)。

## <a name="run-a-package-with-powershell"></a>使用 PowerShell 執行套件
在本節中，您會使用 Azure PowerShell 來建立 ADF 管線，其中具有要執行 SSIS 套件的執行 SSIS 套件活動。 

依照[如何安裝和設定 Azure PowerShell](/powershell/azure/install-az-ps)中的逐步指示，安裝最新的 Azure PowerShell 模組。

### <a name="create-an-adf-with-azure-ssis-ir"></a>建立具有 Azure-SSIS IR 的 ADF
您可以遵循下列教學課程中的逐步指示，使用已佈建 Azure-SSIS IR 的現有 ADF，或建立具有 Azure-SSIS IR 的新 ADF：[教學課程：透過 PowerShell 將 SSIS 套件部署至 Azure](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)。

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>使用 Execute SSIS 套件活動建立管線 
在此步驟中，您要使用執行 SSIS 套件活動建立管線。 此活動會執行您的 SSIS 套件。 

1. 使用類似下列範例的內容，在 **C:\ADF\RunSSISPackage** 資料夾中建立名為 **RunSSISPackagePipeline.json** 的 JSON 檔案：

   > [!IMPORTANT]
   > 儲存檔案之前，請先取代物件名稱、描述和路徑、屬性和參數值、密碼及其他變數值。 

   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "mySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "myAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyDomain",
                       "userName": "MyUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "**********"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "FolderName/ProjectName/PackageName.dtsx"
                   },
                   "environmentPath": "FolderName/EnvironmentName",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyPipelineParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MySecret"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "userName": {
                               "value": "sa"
                           },
                           "passWord": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "abc"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "userName": {
                               "value": {
                                   "value": "@pipeline().parameters.MyUsername",
                                   "type": "Expression"
                               }
                           },
                           "passWord": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyPassword",
                                   "secretVersion": "3a1b74e361bf4ef4a00e47053b872149"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

2. 在 Azure PowerShell 中，切換至 `C:\ADF\RunSSISPackage` 資料夾。

3. 若要建立管線**RunSSISPackagePipeline**，請執行**組 AzDataFactoryV2Pipeline** cmdlet。

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   以下是範例輸出：

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>執行管道
使用**Invoke AzDataFactoryV2Pipeline** cmdlet 執行管線。 Cmdlet 會傳回管線執行識別碼，方便後續監視。

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>監視管線

執行下列 PowerShell 程式碼以持續檢查管線執行狀態，直到完成複製資料為止。 在 PowerShell 視窗中複製/貼上下列指令碼，然後按 ENTER。 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

您也可以使用 Azure 入口網站監視管線。 如需逐步指示，請參閱[監視管線](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)：

### <a name="schedule-the-pipeline-with-a-trigger"></a>使用觸發程序排程管線
在上一個步驟中，您已依需求執行管線。 您也可以建立排程觸發程序，依排程執行管線 (每小時、每天等)。

1. 使用下列內容，在 **C:\ADF\RunSSISPackage** 資料夾中建立名為 **MyTrigger.json** 的 JSON 檔案： 

   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
2. 在 **Azure PowerShell** 中，切換至 **C:\ADF\RunSSISPackage** 資料夾。
3. 執行**組 AzDataFactoryV2Trigger** cmdlet，以建立觸發程序。 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. 觸發程序預設處於已停止狀態。 執行來啟動觸發程序**開始 AzDataFactoryV2Trigger** cmdlet。 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. 確認 執行啟動觸發程序**Get AzDataFactoryV2Trigger** cmdlet。 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. 在下一個小時後，執行下列命令。 例如，如果目前的時間是 UTC 下午 3:25，請在 UTC 下午 4:00 執行命令。 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   您可以依據 Azure SQL Server 中的 SSISDB 資料庫執行下列查詢，確認已執行套件。 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>後續步驟
請參閱下列部落格文章：
-   [使用 ADF 管線中的 SSIS 活動來現代化及擴充您的 ETL/ELT 工作流程](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
