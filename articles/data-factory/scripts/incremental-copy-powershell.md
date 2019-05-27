---
title: PowerShell 指令碼：使用 Azure Data Factory 以累加方式載入資料 | Microsoft Docs
description: 這個 PowerShell 指令碼示範如何使用 Azure Data Factory，以累加方式將資料從 Azure SQL Database 複製到「Azure Blob 儲存體」。
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: 5ae6d6ed06aa5734dc601e6e72ba55ec8ddf7bcf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160628"
---
# <a name="powershell-script---incrementally-load-data-by-using-azure-data-factory"></a>PowerShell 指令碼 - 使用 Azure Data Factory 以累加方式載入資料
這個範例 PowerShell 指令碼會在初次將資料從來源完整複製到接收器之後，只將來源資料存放區中新的或已更新的記錄，載入到接收資料存放區。  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

如需了解執行此範例的必要條件，請參閱[教學課程：累加複製](../tutorial-incremental-copy-powershell.md#prerequisites)。 

## <a name="sample-script"></a>範例指令碼

> [!IMPORTANT]
> 這個指令碼會建立 JSON 檔案，該檔案定義硬碟上 c:\ 資料夾中的 Data Factory 實體 (已連結的服務、資料集和管線)。

```powershell
# Set variables with your own values
$resourceGroupName = "<azure resource group name>" # group will be created if it does not exist already
$dataFactoryName = "<data factory name>" # must be globally unique
$dataFactoryRegion = "East US" 
$storageAccountName = "<Az.Storage account name>"
$storageAccountKey = "<Az.Storage account key>"
$azureSqlServer = "<azure sql server name>"
$azureSqlDatabase = "<azure sql database name>"
$azureSqlUser = "<azure sql server - user name>"
$azureSqlPassword = "<azure sql server - user password>"
$outputBlobFolderPath = "<azure blob container>/<folder>" # output folder where the records are copied. 

$azureStorageLinkedServiceName = "AzureStorageLinkedService"
$azureSqlDatabaseLinkedServiceName = "AzureSQLDatabaseLinkedService"
$sourceDatasetName = "SourceDataset"
$sinkDatasetName = "SinkDataset"
$sourceSqlTableName = "datasource"
$watermarkDatasetName = "WatermarkDataset"
$pipelineName = "IncrementalCopyPipeline"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $dataFactoryRegion


# Create a data factory
$df = Set-AzDataFactory -ResourceGroupName $resourceGroupName -Location $dataFactoryRegion -Name $dataFactoryName 

# Create an Az.Storage linked service in the data factory

## JSON definition of the linked service. 
$storageLinkedServiceDefinition = @"
{
    "name": "$azureStorageLinkedServiceName",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey",
                "type": "SecureString"
            }
        }
    }
}
"@

## IMPORTANT: stores the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$storageLinkedServiceDefinition | Out-File c:\$azureStorageLinkedServiceName.json

## Creates a linked service in the data factory
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$azureStorageLinkedServiceName" -File c:\$azureStorageLinkedServiceName.json

## JSON definition of the linked service. 
$azureSQLDatabaseLinkedServiceDefinition = @"
{
    "name": "$azureSqlDatabaseLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "value": "Server = tcp:$azureSqlServer.database.windows.net,1433;Initial Catalog=$azureSqlDatabase; Persist Security Info=False; User ID=$azureSqlUser; Password=$azureSqlPassword; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                "type": "SecureString"
            }
        }
    }
}
"@

## IMPORTANT: stores the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$azureSQLDatabaseLinkedServiceDefinition | Out-File c:\$azureSqlDatabaseLinkedServiceName.json

## Creates a linked service in the data factory
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$azureSqlDatabaseLinkedServiceName" -File c:\$azureSqlDatabaseLinkedServiceName.json

# Create an Azure SQL dataset in the data factory

## JSON definition of the dataset
$sourceDataset = @"
{
    "name": "$sourceDatasetName",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties": {
            "tableName": "$sourceSqlTableName"
        },
        "linkedServiceName": {
            "referenceName": "$azureSqlDatabaseLinkedServiceName",
            "type": "LinkedServiceReference"
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$sourceDataset | Out-File c:\$sourceDatasetName.json

## Create a dataset in the data factory
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$sourceDatasetName" -File "c:\$sourceDatasetName.json"


# Create an Azure Blob dataset in the data factory

## JSON definition of the dataset
$sinkDataset = @"
{
    "name": "$sinkDatasetName",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "$outputBlobFolderPath",
            "format": {
                "type": "TextFormat"
            }
        },
        "linkedServiceName": {
            "referenceName": "$azureStorageLinkedServiceName",
            "type": "LinkedServiceReference"
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$sinkDataset | Out-File c:\$sinkDatasetName.json

## Create a dataset in the data factory
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$sinkDatasetName" -File "c:\$sinkDatasetName.json"

## JSON definition of the dataset
$watermarkDataset = @"
{
    "name": "$watermarkDatasetName",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties": {
            "tableName": "watermarktable"
        },
        "linkedServiceName": {
            "referenceName": "$azureSqlDatabaseLinkedServiceName",
            "type": "LinkedServiceReference"
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$watermarkDataset | Out-File c:\$watermarkDatasetName.json

## Create a dataset in the data factory
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$watermarkDatasetName" -File "c:\$watermarkDatasetName.json"

# Create a pipeline in the data factory

## JSON definition of the pipeline
$pipelineDefinition = @"
{
    "name": "$pipelineName",
    "properties": {
        "activities": [
            {
                "name": "LookupWaterMarkActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                    "type": "SqlSource",
                    "sqlReaderQuery": "select * from watermarktable"
                    },

                    "dataset": {
                    "referenceName": "$watermarkDatasetName",
                    "type": "DatasetReference"
                    }
                }
            },
            {
                "name": "LookupMaxValuefromSourceActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "SELECT MAX(LastModifytime) as NewWatermarkvalue FROM dbo.datasource"
                    },

                    "dataset": {
                    "referenceName": "$sourceDatasetName",
                    "type": "DatasetReference"
                    }
                }
            },

            {
                "name": "IncrementalCopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from dbo.datasource where LastModifytime > '@{activity('LookupWaterMarkActivity').output.WatermarkValue}' and LastModifytime <= '@{activity('LookupMaxValuefromSourceActivity').output.NewWatermarkvalue}'"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "dependsOn": [
                    {
                        "activity": "LookupMaxValuefromSourceActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    },
                    {
                        "activity": "LookupWaterMarkActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],

                "inputs": [
                    {
                        "referenceName": "$sourceDatasetName",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "$sinkDatasetName",
                        "type": "DatasetReference"
                    }
                ]
            },

            {
                "name": "StoredProceduretoWriteWatermarkActivity",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {

                    "storedProcedureName": "sp_write_watermark",
                    "storedProcedureParameters": {
                        "LastModifiedtime": {"value": "@{activity('LookupMaxValuefromSourceActivity').output.NewWatermarkvalue}", "type": "datetime" },
                        "TableName":  { "value":"@{activity('LookupWaterMarkActivity').output.TableName}", "type":"String"}
                    }
                },

                "linkedServiceName": {
                    "referenceName": "$azureSqlDatabaseLinkedServiceName",
                    "type": "LinkedServiceReference"
                },

                "dependsOn": [
                    {
                        "activity": "IncrementalCopyActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ]
            }
        ],

    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryPipeline command. 
$pipelineDefinition | Out-File c:\$pipelineName.json

## Create a pipeline in the data factory
Set-AzDataFactoryPipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$pipelineName" -File "c:\$pipelineName.json"


$RunId = Invoke-AzDataFactoryPipeline -PipelineName "$pipelineName" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName

# Check the pipeline run status until it finishes the copy operation
Start-Sleep -Seconds 30
while ($True) {
    $result = Get-AzDataFactoryActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        Start-Sleep -Seconds 30
    }
    else {
        Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
}


$result = Get-AzDataFactoryActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName `
    -PipelineRunId $runId `
    -RunStartedAfter (Get-Date).AddMinutes(-10) `
    -RunStartedBefore (Get-Date).AddMinutes(10) `
    -ErrorAction Stop

$result

if ($result.Status -eq "Succeeded") {`
    $result.Output -join "`r`n"`
}`
else {`
    $result.Error -join "`r`n"`
}

# INSERT INTO datasource
# VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

# INSERT INTO datasource
# VALUES (7, 'newdata','9/7/2017 9:01:00 AM')

# Inovke the pipeline and see that only new records are copied to the destination. 


# To remove the data factory from the resource gorup
# Remove-AzDataFactory -Name $dataFactoryName -ResourceGroupName $resourceGroupName
# 
# To remove the whole resource group
# Remove-AzResourceGroup  -Name $resourceGroupName
```

## <a name="clean-up-deployment"></a>清除部署

執行範例指令碼之後，您可以使用下列命令以移除資源群組及與其相關聯的所有資源：

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
若要從資源群組移除資料處理站，請執行下列命令： 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| 命令 | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | 建立資料處理站。 |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | 在資料處理站中建立已連結的服務。 已連結的服務會將資料存放區或計算連結到資料處理站。 |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | 在資料處理站中建立資料集。 資料集代表管線中活動的輸入/輸出。 | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | 在資料處理站中建立管線。 管線包含一或多個執行特定作業的活動。 在此管線中，複製活動會將資料從 Azure Blob 儲存體中的一個位置複製到其他位置。 |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | 建立管線的執行。 也就是說，執行管線。 |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | 在管線中取得活動的執行 (活動執行) 的相關詳細資料。 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在 [Azure Data Factory PowerShell 指令碼](../samples-powershell.md)中，找到其他 Azure Data Factory PowerShell 指令碼範例。
