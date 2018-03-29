---
title: Azure Data Factory 中的查閱活動 | Microsoft Docs
description: 了解如何使用查閱活動查閱外部來源的值。 此輸出可供後續活動進一步參考。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: f55e85bb424f4f5973fd6d633b6adf9fbca4d0ef
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="lookup-activity-in-azure-data-factory"></a>Azure Data Factory 中的查閱活動
您可使用查閱活動來讀取或查閱任何外部來源的記錄、資料表名稱或值。 此輸出可供後續活動進一步參考。 

當您想從組態檔或資料來源動態擷取檔案、記錄或資料表的清單時，查閱活動就很有用。 活動的輸出可進一步供其他活動使用，以對那些項目執行特定處理。

> [!NOTE]
> 本文適用於第 2 版的 Azure Data Fatory (目前為預覽版)。 如果您使用 Data Factory 服務的 1 版 (正式推出版本 (GA))，請參閱 [Data Factory 第 1 版文件](v1/data-factory-introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

針對查閱，目前支援下列資料來源：
- Azure Blob 儲存體中的 JSON 檔案
- 檔案系統中的 JSON 檔案
- Azure SQL Database (從查詢轉換的 JSON 資料)
- Azure SQL 資料倉儲 (從查詢轉換的 JSON 資料)
- SQL Server (從查詢轉換的 JSON 資料)
- Azure 資料表儲存體 (從查詢轉換的 JSON 資料)

查閱活動能傳回的資料列數目上限是 **5000**，最多為 **10MB** 的大小。

## <a name="syntax"></a>語法

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>類型屬性
Name | 說明 | 類型 | 必要？
---- | ----------- | ---- | --------
資料集 | 提供查閱的資料集參考。 目前支援的資料集類型為：<ul><li>`AzureBlobDataset`，以 [Azure Blob 儲存體](connector-azure-blob-storage.md#dataset-properties)作為來源</li><li>`FileShareDataset`，以[檔案系統](connector-file-system.md#dataset-properties)作為來源</li><li>`AzureSqlTableDataset`，以 [Azure SQL Database](connector-azure-sql-database.md#dataset-properties) 或 [Azure SQL 資料倉儲](connector-azure-sql-data-warehouse.md#dataset-properties)作為來源</li><li>`SqlServerTable`，以 [SQL Server](connector-sql-server.md#dataset-properties) 作為來源</li><li>`AzureTableDataset`，以 [Azure 資料表儲存體](connector-azure-table-storage.md#dataset-properties)作為來源</li> | 索引鍵/值組 | yes
來源 | 包含資料集特定的來源屬性，與複製活動來源相同。 如需詳細資料，請參閱每個對應連接器文章中的＜複製活動屬性＞一節。 | 索引鍵/值組 | yes
firstRowOnly | 指出是否只傳回第一個資料列或傳回所有資料列。 | BOOLEAN | 編號 預設值為 `true`。

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>在後續活動中使用查閱活動結果

查閱結果會在活動執行結果的 `output` 區段中傳回。

* **當 `firstRowOnly` 設為 `true` (預設值)**，輸出格式如下列程式碼所示。 查閱結果就在固定的 `firstRow` 索引鍵底下。 若要在後續活動中使用結果，請使用 `@{activity('MyLookupActivity').output.firstRow.TableName}` 模式。

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **當 `firstRowOnly` 設為 `false`**，輸出格式如下列程式碼所示。 `count` 欄位指出傳回多少筆記錄，詳細的值則顯示在固定的 `value` 陣列底下。 在這種情況下，查閱活動後面通常接著 [Foreach 活動](control-flow-for-each-activity.md)。 您可以使用 `@activity('MyLookupActivity').output.value` 模式，將 `value` 陣列傳遞至 ForEach 活動的 `items` 欄位。 若要存取 `value` 陣列中的元素，請使用下列語法：`@{activity('lookupActivity').output.value[zero based index].propertyname}`。 下列是一個範例：`@{activity('lookupActivity').output.value[0].tablename}`。

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>範例
在此範例中，複製活動會將資料從 Azure SQL Database 執行個體中的 SQL 資料表複製到 Azure Blob 儲存體。 SQL 資料表的名稱會儲存在 Blob 儲存體的 JSON 檔案中。 查閱活動在執行階段會查閱表格名稱。 這個方法可動態修改 JSON，您不必重新部署管線或資料集。 

此範例僅示範查閱第一個資料列。 若要查閱所有資料列並鏈結結果與 ForEach 活動，請參閱[使用 Azure Data Factory 大量複製多個資料表](tutorial-bulk-copy.md)中的範例。

### <a name="pipeline"></a>管線
這個管線包含兩個活動：「查閱」和「複製」。 

- 查閱活動會設定為使用 LookupDataset，它會參考 Azure Blob 儲存體中的位置。 查閱活動會從這個位置的 JSON 檔案讀取 SQL 資料表的名稱。 
- 複製活動會使用查閱活動的輸出 (SQL 資料表名稱)。 來源資料集 (SourceDataset) 中的 tableName 屬性會設定為使用查閱活動的輸出。 複製活動會將資料從 SQL 資料表複製到 Azure Blob 儲存體中由 SinkDataset 屬性指定的位置。 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>查閱資料集
查閱資料集會參照 Azure 儲存體查閱資料夾中的 *sourcetable.json* 檔案 (此檔案是由 AzureStorageLinkedService 類型指定)。 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-the-copy-activity"></a>複製活動的來源資料集
來源資料集會使用查閱活動的輸出，亦即 SQL 資料表的名稱。 複製活動會將資料從這個 SQL 資料表複製到 Azure Blob 儲存體中由接收端資料集指定的位置。 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>複製活動的接收資料集
複製活動會將資料從 SQL 資料表複製到 Azure 儲存體中 *csv* 資料夾內的 *filebylookup.csv* 檔案 (此檔案是由 AzureStorageLinkedService 屬性指定)。 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure 儲存體連結服務
這個儲存體帳戶包含具有 SQL 資料表名稱的 JSON 檔案。 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Azure SQL Database 的連結服務
這個 Azure SQL 資料庫執行個體包含要複製到 Blob 儲存體的資料。 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>物件集合

```json
{
  "Id": "1",
  "tableName": "Table1",
}
{
   "Id": "2",
  "tableName": "Table2",
}
```

#### <a name="array-of-objects"></a>物件的陣列

```json
[ 
    {
        "Id": "1",
          "tableName": "Table1",
    }
    {
        "Id": "2",
        "tableName": "Table2",
    }
]
```

## <a name="next-steps"></a>後續步驟
請參閱 Data Factory 支援的其他控制流程活動： 

- [執行管線活動](control-flow-execute-pipeline-activity.md)
- [ForEach 活動](control-flow-for-each-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
- [Web 活動](control-flow-web-activity.md)
