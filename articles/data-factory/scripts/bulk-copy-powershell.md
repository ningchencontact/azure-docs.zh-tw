---
title: PowerShell 指令碼：使用 Azure Data Factory 大量複製資料 | Microsoft Docs
description: 這個 PowerShell 指令碼示範如何使用 Azure Data Factory，將資料從來源資料存放區大量複製到目的地資料存放區。
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
ms.openlocfilehash: d2db5bced78a00c8acabc150752fe65e9515dff1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480633"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>PowerShell 指令碼 - 使用 Azure Data Factory 來大量複製多個資料表

這個範例 PowerShell 指令碼會將資料從 Azure SQL 資料庫中的多個資料表複製到 Azure SQL 資料倉儲。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

如需了解執行此範例的必要條件，請參閱[教學課程：大量複製](../tutorial-bulk-copy.md#prerequisites)。

## <a name="sample-script"></a>範例指令碼

> [!IMPORTANT]
> 這個指令碼會建立 JSON 檔案，該檔案定義硬碟上 c:\ 資料夾中的 Data Factory 實體 (已連結的服務、資料集和管線)。

```powershell
# Set variables with your own values
$resourceGroupName = "<resource group name>"
$dataFactoryName = "<data factory name>" # Name of the data factory must be globally unique
$dataFactoryNameLocation = "China East"

$azureSqlServer = "<azure sql server name>"
$azureSqlServerUser = "<azure sql server user>"
$azureSqlServerUserPassword = "<azure sql server password>"
$azureSqlDatabase = "<source azure sql database name>"
$azureSqlDataWarehouse = "<sink azure sql data warehouse name>"

$azureStorageAccount = "<Az.Storage accoutn name>"
$azureStorageAccountKey = "<Az.Storage account key>"

# No need to change values for these variables
$azureSqlDatabaseLinkedService = "AzureSqlDatabaseLinkedService"
$azureSqlDataWarehouseLinkedService = "AzureSqlDataWarehouseLinkedService"
$azureStorageLinkedService = "AzureStorageLinkedService"
$azureSqlDatabaseDataset = "AzureSqlDatabaseDataset"
$azureSqlDataWarehouseDataset = "AzureSqlDataWarehouseDataset"
$IterateAndCopySQLTablesPipeline = "IterateAndCopySQLTables"
$pipelineGetTableListAndTriggerCopyData = "GetTableListAndTriggerCopyData"


# create a resource gorup
New-AzResourceGroup -Name $resourceGroupName -Location $dataFactoryNameLocation

# create a data factory
$df = Set-AzDataFactory -ResourceGroupName $resourceGroupName -Location $dataFactoryNameLocation -Name $dataFactoryName

# create a linked service for Azure SQL Database (source)
$azureSQLDatabaseLinkedServiceDefinition = @"
{
    "name": "$azureSqlDatabaseLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:$azureSqlServer.database.windows.net,1433;Database=$azureSqlDatabase;User ID=$azureSqlServerUser;Password=$azureSqlServerUserPassword;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$azureSQLDatabaseLinkedServiceDefinition | Out-File c:\$azureSqlDatabaseLinkedService.json

## Creates an Az.Storage linked service
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $azureSqlDatabaseLinkedService -File c:\$azureSqlDatabaseLinkedService.json


# create a linked service for Azure SQL Datawarehouse (sink)
$azureSQLDataWarehouseLinkedServiceDefinition = @"
{
    "name": "$azureSqlDataWarehouseLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:$azureSqlServer.database.windows.net,1433;Database=$azureSqlDataWarehouse;User ID=$azureSqlServerUser;Password=$azureSqlServerUserPassword;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$azureSQLDataWarehouseLinkedServiceDefinition | Out-File c:\$azureSqlDataWarehouseLinkedService.json

## Creates an linked service for Az.Storage Account. Interim storage to enable PolyBase
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $azureSqlDataWarehouseLinkedService -File c:\$azureSqlDataWarehouseLinkedService.json

$storageLinkedServiceDefinition = @"
{
    "name": "$azureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=$azureStorageAccount;AccountKey=$azureStorageAccountKey",
                "type": "SecureString"
            }
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$storageLinkedServiceDefinition | Out-File c:\$azureStorageLinkedService.json

## Creates an Az.Storage linked service
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $azureStorageLinkedService -File c:\$azureStorageLinkedService.json


# create the input dataset (Azure SQL Database)
$azureSqlDatabaseDatasetDefiniton = @"
{
    "name": "$azureSqlDatabaseDataset",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "$azureSqlDatabaseLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "dummy"
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$azureSqlDatabaseDatasetDefiniton | Out-File c:\$azureSqlDatabaseDataset.json

## Create a dataset in the data factory
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $azureSqlDatabaseDataset -File "c:\$azureSqlDatabaseDataset.json"


# create the output dataset (Azure SQL Data Warehouse)
$azureSqlDataWarehouseDatasetDefiniton = @"
{
    "name": "$azureSqlDataWarehouseDataset",
    "properties": {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "$azureSqlDataWarehouseLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": {
                "value": "@{dataset().DWTableName}",
                "type": "Expression"
            }
        },
        "parameters":{
            "DWTableName":{
                "type":"String"
            }
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$azureSqlDataWarehouseDatasetDefiniton | Out-File c:\$azureSqlDataWarehouseDataset.json

## Create a dataset in the data factory
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $azureSqlDataWarehouseDataset -File "c:\$azureSqlDataWarehouseDataset.json"

# Create a pipeline in the data factory that copies data from source SQL Database to sink SQL Data Warehouse
$pipelineDefinition = @"
{
    "name": "$IterateAndCopySQLTablesPipeline",
    "properties": {
        "activities": [
            {
                "name": "IterateSQLTables",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "false",
                    "items": {
                        "value": "@pipeline().parameters.tableList",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "CopyData",
                            "description": "Copy data from SQL database to SQL DW",
                            "type": "Copy",
                            "inputs": [
                                {
                                    "referenceName": "$azureSqlDatabaseDataset",
                                    "type": "DatasetReference"
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "$azureSqlDataWarehouseDataset",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "DWTableName": "[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                    }
                                }
                            ],
                            "typeProperties": {
                                "source": {
                                    "type": "SqlSource",
                                    "sqlReaderQuery": "SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                },
                                "sink": {
                                    "type": "SqlDWSink",
                                    "preCopyScript": "TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]",
                                    "allowPolyBase": true
                                },
                                "enableStaging": true,
                                "stagingSettings": {
                                    "linkedServiceName": {
                                        "referenceName": "$azureStorageLinkedService",
                                        "type": "LinkedServiceReference"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "tableList": {
                "type": "Object"
            }
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryPipeline command. 
$pipelineDefinition | Out-File c:\$IterateAndCopySQLTablesPipeline.json

## Create a pipeline in the data factory
Set-AzDataFactoryPipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $IterateAndCopySQLTablesPipeline -File "c:\$IterateAndCopySQLTablesPipeline.json"


# Create a pipeline in the data factory that retrieves a list of tables and invokes the above pipeline for each table to be copied
$pipeline2Definition = @"
{
    "name":"$pipelineGetTableListAndTriggerCopyData",
    "properties":{
        "activities":[
            { 
                "name": "LookupTableList",
                "description": "Retrieve the table list from Azure SQL dataabse",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'"
                    },
                    "dataset": {
                        "referenceName": "$azureSqlDatabaseDataset",
                        "type": "DatasetReference"
                    },
                    "firstRowOnly": false
                }
            },
            {
                "name": "TriggerCopy",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {
                        "tableList": {
                            "value": "@activity('LookupTableList').output.value",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "$IterateAndCopySQLTablesPipeline",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                },
                "dependsOn": [
                    {
                        "activity": "LookupTableList",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ]
            }
        ]
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryPipeline command. 
$pipeline2Definition | Out-File c:\$pipelineGetTableListAndTriggerCopyData.json

## Create a pipeline in the data factory
Set-AzDataFactoryPipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineGetTableListAndTriggerCopyData -File "c:\$pipelineGetTableListAndTriggerCopyData.json"



# Create a pipeline run 

## JSON definition for dummy pipeline parameters
$pipelineParameters = @"
{
    "dummy":  "b"
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Invoke-AzDataFactoryPipeline command. 
$pipelineParameters | Out-File c:\PipelineParameters.json

# Create a pipeline run by using parameters
$runId = Invoke-AzDataFactoryPipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineGetTableListAndTriggerCopyData -ParameterFile c:\PipelineParameters.json

# Check the pipeline run status until it finishes the copy operation
Start-Sleep -Seconds 30
while ($True) {
    $result = Get-AzDataFactoryActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        Start-Sleep -Seconds 30
    }
    else {
        Write-Host "Pipeline '$pipelineGetTableListAndTriggerCopyData' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
}

# Get the activity run details 
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
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-azdatafactoryv2) | 建立資料處理站。 |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-azdatafactoryv2linkedservice) | 在資料處理站中建立已連結的服務。 已連結的服務會將資料存放區或計算連結到資料處理站。 |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/set-azdatafactoryv2dataset) | 在資料處理站中建立資料集。 資料集代表管線中活動的輸入/輸出。 | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-azdatafactoryv2pipeline) | 在資料處理站中建立管線。 管線包含一或多個執行特定作業的活動。 在此管線中，複製活動會將資料從 Azure Blob 儲存體中的一個位置複製到其他位置。 |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-azdatafactoryv2pipeline) | 建立管線的執行。 也就是說，執行管線。 |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-azdatafactoryv2activityrun) | 在管線中取得活動的執行 (活動執行) 的相關詳細資料。 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在 [Azure Data Factory PowerShell 指令碼](../samples-powershell.md)中，找到其他 Azure Data Factory PowerShell 指令碼範例。
