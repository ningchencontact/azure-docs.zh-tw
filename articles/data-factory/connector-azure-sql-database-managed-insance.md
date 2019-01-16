---
title: 使用 Azure Data Factory 將資料複製到 Azure SQL Database 受控執行個體或從該處複製資料 | Microsoft Docs
description: 了解如何使用 Azure Data Factory 從 Azure SQL Database 受控執行個體來回移動資料。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: jingwang
ms.openlocfilehash: df8d337e7950400a86dcab14de4484f4811f43e2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025074"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure SQL Database 受控執行個體及從該處複製資料

本文概述如何使用 Azure Data Factory 中的複製活動，從 Azure SQL Database 受控執行個體複製資料及將資料複製到該處。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 Azure SQL Database 受控執行個體複製到任何支援的接收資料存放區，或將資料從任何支援的來源資料存放區複製到受控執行個體。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Azure SQL Database 受控執行個體連接器支援：

- 使用 **SQL** 或 **Windows** 驗證來複製資料。
- 作為來源時，使用 SQL 查詢或預存程序來擷取資料。
- 在複製期間作為接收器時，使用自訂邏輯將資料附加到目的地資料表或叫用預存程序。

## <a name="prerequisites"></a>必要條件

若要使用來自位於 VNET 之 Azure SQL Database 受控執行個體的複製資料，您需要在可存取資料庫的相同 VNET 中設定自我裝載整合執行階段。 如需詳細資料，請參閱[自我裝載 Integration Runtime](create-self-hosted-integration-runtime.md) 一文。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Azure SQL Database 受控執行個體連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Azure SQL Database 受控執行個體連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為：**SqlServer** | 是 |
| connectionString |指定使用 SQL 驗證或 Windows 驗證來連線到受控執行個體所需的 connectionString 資訊。 請參考下列範例。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |是 |
| userName |如果您使用「Windows 驗證」，請指定使用者名稱。 範例︰**domainname\\username**。 |否 |
| password |指定您為 userName 指定之使用者帳戶的密碼。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |否 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 在與您受控執行個體相同的 VNET 中，佈建自我裝載整合執行階段。 |是 |

>[!TIP]
>如果您遇到錯誤，其錯誤碼為 "UserErrorFailedToConnectToSqlServer"，以及「資料庫的工作階段限制為 XXX 並已達到。」訊息，請將 `Pooling=false` 新增至您的連接字串並再試一次。

**範例 1：使用 SQL 驗證**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例 2：使用 Windows 驗證**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
             "userName": "<domain\\username>",
             "password": {
                "type": "SecureString",
                "value": "<password>"
             }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
     }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱資料集文章。 本節提供 Azure SQL Database 受控執行個體資料集所支援的屬性清單。

若要從 Azure SQL Database 受控執行個體複製資料或將資料複製到該處，請將資料集的類型屬性設定為 **SqlServerTable**。 以下是支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為：**SqlServerTable** | 是 |
| tableName |資料庫執行個體中連結服務所參考的資料表或檢視的名稱。 | 否 (來源)；是 (接收) |

**範例**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Azure SQL Database 受控執行個體來源和接收所支援的屬性清單。

### <a name="azure-sql-database-managed-instance-as-source"></a>作為來源的 Azure SQL Database 受控執行個體

若要從 Azure SQL Database 受控執行個體複製資料，請將複製活動中的來源類型設定為 **SqlSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為：**SqlSource** | 是 |
| SqlReaderQuery |使用自訂 SQL 查詢來讀取資料。 範例： `select * from MyTable`. |否 |
| sqlReaderStoredProcedureName |從來源資料表讀取資料的預存程序名稱。 最後一個 SQL 陳述式必須是預存程序中的 SELECT 陳述式。 |否 |
| storedProcedureParameters |預存程序的參數。<br/>允許的值為：名稱/值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 |否 |

**注意事項**

- 如果為 SqlSource 指定 **sqlReaderQuery**，複製活動就會針對受控執行個體來源執行此查詢來取得資料。 或者，您可以藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** (如果預存程序接受參數) 來指定預存程序。
- 如果您未指定 "sqlReaderQuery" 或 "sqlReaderStoredProcedureName" 屬性，就會使用資料集 JSON 的 "structure" 區段中定義的資料行，來建構要針對受控執行個體執行的查詢 (`select column1, column2 from mytable`)。 如果資料集定義沒有 "structure"，則會從資料表中選取所有資料行。

**範例：使用 SQL 查詢**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**範例：使用預存程序**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**預存程序定義**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-database-managed-instance-as-sink"></a>作為接收的 Azure SQL Database 受控執行個體

若要將資料複製到 Azure SQL Database 受控執行個體，請將複製活動中的接收類型設定為 **SqlSink**。 複製活動的 **sink** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器的類型屬性必須設定為：**SqlSink** | 是 |
| writeBatchSize |當緩衝區大小達到 writeBatchSize 時，將資料插入 SQL 資料表中<br/>允許的值為：整數 (資料列數目)。 |無 (預設值：10000) |
| writeBatchTimeout |在逾時前等待批次插入作業完成的時間。<br/>允許的值為：時間範圍。 範例：“00:30:00” (30 分鐘)。 |否 |
| preCopyScript |針對複製活動指定一個 SQL 查詢，在將資料寫入到受控執行個體之前執行。 每一複製回合只會叫用此查詢一次。 您可以使用此屬性來清除預先載入的資料。 |否 |
| sqlWriterStoredProcedureName |預存程序的名稱，此預存程序定義如何將來源資料套用至目標資料表，例如使用您自己的商務邏輯來執行更新插入或轉換。 <br/><br/>請注意，將會**依批次叫用**此預存程序。 如果您想要進行只執行一次且與來源資料無關的作業 (例如刪除/截斷)，請使用 `preCopyScript` 屬性。 |否 |
| storedProcedureParameters |預存程序的參數。<br/>允許的值為：名稱/值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 |否 |
| sqlWriterTableType |指定要在預存程序中使用的資料表類型名稱。 複製活動可讓正在移動的資料可用於此資料表類型的暫存資料表。 然後，預存程序程式碼可以合併正在複製的資料與現有的資料。 |否 |

> [!TIP]
> 將資料複製到 Azure SQL Database 受控執行個體時，複製活動預設會將資料附加至接收資料表。 若要執行 UPSERT 或其他商務邏輯，請在 SqlSink 中使用該預存程序。 若要了解更多詳細資料，請參閱[叫用 SQL 接收器的預存程序](#invoking-stored-procedure-for-sql-sink)。

**範例 1：附加資料**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**範例 2：在複製期間叫用預存程序進行更新插入**

若要了解更多詳細資料，請參閱[叫用 SQL 接收器的預存程序](#invoking-stored-procedure-for-sql-sink)。

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>目標資料庫中的身分識別資料行

本節提供一個範例，此範例會將資料從沒有身分識別資料行的來源資料表，複製到具有身分識別資料行的目的地資料表。

**來源資料表**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**目的地資料表**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

請注意，目標資料表具有身分識別資料行。

**來源資料集 JSON 定義**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**目的地資料集 JSON 定義**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

請注意，您的來源資料表與目標資料表的結構描述不同 (目標資料表有一個具有身分識別的額外資料行)。 在此案例中，您必須在目標資料集定義中指定 **structure** 屬性，這不包含身分識別資料行。

## <a name="invoking-stored-procedure-for-sql-sink"></a> 從 SQL 接收器叫用預存程序

將資料複製到 Azure SQL Database 受控執行個體時，可以設定使用者指定的預存程序，並搭配其他參數來叫用此預存程序。

當內建的複製機制無法滿足需求時，可以使用預存程序。 通常是在最後一次將來源資料插入到目的地資料表之前，如果必須執行更新插入 (插入 + 更新) 或額外的處理 (合併資料行、查閱其他值、插入到多個資料表等) 時，會使用此程序。

下列範例示範如何使用預存程序，對受控執行個體中的資料表執行更新插入。 假設輸入資料和接收器 "Marketing" 資料表各有三個資料行：ProfileID、State 與 Category。 根據 “ProfileID” 資料行執行更新插入，然後僅針對特定的類別套用。

**輸出資料集**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

依下列方式定義複製活動中的 SqlSink 區段。

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

在資料庫中，使用與 SqlWriterStoredProcedureName 相同的名稱定義預存程序。 它會處理來自指定來源的輸入資料，並合併至輸出資料表。 預存程序中資料表類型的參數名稱應該與資料集中定義的 "tableName" 相同。

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
AS
BEGIN
  MERGE [dbo].[Marketing] AS target
  USING @Marketing AS source
  ON (target.ProfileID = source.ProfileID and target.Category = @category)
  WHEN MATCHED THEN
      UPDATE SET State = source.State
  WHEN NOT MATCHED THEN
      INSERT (ProfileID, State, Category)
      VALUES (source.ProfileID, source.State, source.Category)
END
```

在資料庫中，使用與 sqlWriterTableType 相同的名稱來定義資料表類型。 請注意，資料表類型的結構描述應該與輸入資料所傳回的結構描述相同。

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

預存程序功能使用 [資料表值參數](https://msdn.microsoft.com/library/bb675163.aspx)。

>[!NOTE]
>如果透過叫用預存程序來寫入 Money/Smallmoney 資料類型，值可能會四捨五入。 在 TVP 中將對應的資料類型指定為十進位而不是 Money/Smallmoney，以降低風險。 

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Azure SQL Database 受控執行個體的資料類型對應

從 Azure SQL Database 受控執行個體複製資料或將資料複製到該處時，會使用下列從受控執行個體資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收器。

| Azure SQL Database 受控執行個體資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |BOOLEAN |
| char |String、Char[] |
| 日期 |Datetime |
| DateTime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| 十進位 |十進位 |
| FILESTREAM 屬性 (varbinary(max)) |Byte[] |
| Float |兩倍 |
| 映像 |Byte[] |
| int |Int32 |
| money |十進位 |
| nchar |String、Char[] |
| ntext |String、Char[] |
| numeric |十進位 |
| nvarchar |String、Char[] |
| real |單一 |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |十進位 |
| sql_variant |物件 * |
| text |String、Char[] |
| 分析 |時間範圍 |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String、Char[] |
| xml |xml |

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。
