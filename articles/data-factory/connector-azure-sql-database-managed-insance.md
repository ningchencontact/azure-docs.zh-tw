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
ms.date: 04/08/2019
ms.author: jingwang
ms.openlocfilehash: 9cb3c028c14e6c47d47eafcf6279a918c0917442
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61093942"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure SQL Database 受控執行個體及從該處複製資料

本文概述如何使用 Azure Data Factory 中的複製活動，將資料複製到 Azure SQL Database 受控執行個體及從該處複製資料。 本文是以[複製活動概觀](copy-activity-overview.md)一文作為基礎，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 Azure SQL Database 受控執行個體複製到任何支援的接收資料存放區。 您也可以從任何支援的來源資料存放區將資料複製到受控執行個體。 如需複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Azure SQL Database 受控執行個體連接器支援：

- 使用 SQL 或 Windows 驗證來複製資料。
- 作為來源時，使用 SQL 查詢或預存程序來擷取資料。
- 在複製期間作為接收器時，使用自訂邏輯將資料附加到目的地資料表或叫用預存程序。

目前不支援 SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)。 

## <a name="prerequisites"></a>必要條件

若要從虛擬網路中的 Azure SQL Database 受控執行個體複製資料，請設定可存取資料庫的自我裝載整合執行階段。 如需詳細資訊，請參閱[自我裝載整合執行階段](create-self-hosted-integration-runtime.md)。

如果您將自我裝載整合執行階段佈建在受控執行個體所在的虛擬網路中，請確定您的整合執行階段機器位在與受控執行個體不同的子網路中。 如果您將自我裝載整合執行階段佈建在與受控執行個體不同的虛擬網路中，您可以使用虛擬網路對等互連或虛擬網路對虛擬網路的連線。 如需詳細資訊，請參閱[將您的應用程式連線到 Azure SQL Database 受控執行個體](../sql-database/sql-database-managed-instance-connect-app.md)。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Azure SQL Database 受控執行個體連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Azure SQL Database 受控執行個體連結服務支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為 **SqlServer**。 | 是。 |
| connectionString |此屬性會指定使用 SQL 驗證或 Windows 驗證來連線到受控執行個體所需的 connectionString 資訊。 如需詳細資訊，請參閱下列範例。 <br/>將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中。 您也可以將密碼放在 Azure Key Vault 中，而且，如果這是 SQL 驗證，則會從連接字串中提取 `password` 組態。 請參閱表格下方的 JSON 範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文深入了解詳細資料。 |是。 |
| userName |如果您使用 Windows 驗證，此屬性會指定使用者名稱。 範例為 **domainname\\username**。 |沒有。 |
| password |此屬性會針對您為使用者名稱指定的使用者帳戶指定密碼。 選取 **SecureString**，將 connectionString 資訊安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |沒有。 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 在與您受控執行個體相同的虛擬網路中，佈建自我裝載整合執行階段。 |是。 |

>[!TIP]
>您可能會看到 "UserErrorFailedToConnectToSqlServer" 錯誤碼，並且包含類似「已達到資料庫工作階段限制 XXX」的訊息。 如果發生此錯誤，請將 `Pooling=false` 新增至您的連接字串，然後再試一次。

**範例 1：使用 SQL 驗證**

```json
{
    "name": "AzureSqlMILinkedService",
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

**範例 2：使用 SQL 驗證搭配 Azure Key Vault 中的密碼**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例 3：使用 Windows 驗證**

```json
{
    "name": "AzureSqlMILinkedService",
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

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為 **SqlServerTable**。 | 是。 |
| tableName |此屬性是資料庫執行個體中連結服務所參考的資料表或檢視名稱。 | 不是來源的必要項目。 是接收的必要項目。 |

**範例**

```json
{
    "name": "AzureSqlMIDataset",
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

### <a name="azure-sql-database-managed-instance-as-a-source"></a>作為來源的 Azure SQL Database 受控執行個體

若要從 Azure SQL Database 受控執行個體複製資料，請將複製活動中的來源類型設定為 **SqlSource**。 複製活動的 [來源] 區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動來源的 type屬性必須設定為 **SqlSource**。 | 是。 |
| sqlReaderQuery |此屬性使用自訂 SQL 查詢來讀取資料。 例如 `select * from MyTable`。 |沒有。 |
| sqlReaderStoredProcedureName |此屬性是從來源資料表讀取資料的預存程序名稱。 最後一個 SQL 陳述式必須是預存程序中的 SELECT 陳述式。 |沒有。 |
| storedProcedureParameters |這些是預存程序的參數。<br/>允許的值為名稱或值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 |沒有。 |

請注意下列幾點：

- 如果為 **SqlSource** 指定 **sqlReaderQuery**，複製活動就會針對受控執行個體來源執行此查詢來取得資料。 如果預存程序接受參數，您也可以藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** 來指定預存程序。
- 如果您未指定 **sqlReaderQuery** 或 **sqlReaderStoredProcedureName** 屬性，資料集 JSON "structure" 區段中定義的資料行就會用來建構查詢。 `select column1, column2 from mytable` 查詢會根據受控執行個體來執行。 如果資料集定義沒有 "structure"，則會從資料表中選取所有資料行。

**範例：使用 SQL 查詢**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
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
        "name": "CopyFromAzureSqlMI",
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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>作為接收的 Azure SQL Database 受控執行個體

若要將資料複製到 Azure SQL Database 受控執行個體，請將複製活動中的接收類型設定為 **SqlSink**。 複製活動的 [接收] 區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動接收的 type 屬性必須設定為 **SqlSink**。 | 是。 |
| writeBatchSize |要插入至 SQL 資料表的資料列的數目**每個批次**。<br/>允許的值為整數的資料列數目。 |否 (預設值：10,000)。 |
| writeBatchTimeout |此屬性會指定在逾時前等待批次插入作業完成的時間。<br/>允許的值為時間範圍。 範例是 “00:30:00”，也就是 30 分鐘。 |沒有。 |
| preCopyScript |此屬性會針對複製活動指定一個 SQL 查詢，在將資料寫入到受控執行個體之前執行。 每一複製回合只會叫用此查詢一次。 您可以使用此屬性來清除預先載入的資料。 |沒有。 |
| sqlWriterStoredProcedureName |這是用來定義如何將來源資料套用到目標資料表的預存程序名稱。 程序的範例是使用您自己的商務邏輯來進行 upsert 或轉換。 <br/><br/>此預存程序將會*依批次叫用*。 若要進行只執行一次且與來源資料無關的作業 (例如刪除或截斷)，請使用 `preCopyScript` 屬性。 |沒有。 |
| storedProcedureParameters |這些是用於預存程序的參數。<br/>允許的值為名稱或值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 |沒有。 |
| sqlWriterTableType |此參數會指定要在預存程序中使用的資料表類型名稱。 複製活動可讓正在移動的資料可用於此資料表類型的暫存資料表。 然後，預存程序程式碼可以合併正在複製的資料與現有的資料。 |沒有。 |

> [!TIP]
> 將資料複製到 Azure SQL Database 受控執行個體時，複製活動預設會將資料附加至接收資料表。 若要執行 upsert 或其他商務邏輯，請在 SqlSink 中使用該預存程序。 如需詳細資訊，請參閱[從 SQL 接收叫用預存程序](#invoke-a-stored-procedure-from-a-sql-sink)。

**範例 1：附加資料**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
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

**範例 2：在複製期間叫用預存程序來進行 upsert**

若要了解更多詳細資料，請參閱[叫用 SQL 接收中的預存程序](#invoke-a-stored-procedure-from-a-sql-sink)。

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
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

下列範例會將資料從沒有身分識別資料行的來源資料表，複製到具有身分識別資料行的目的地資料表。

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

請注意，您的來源和目標資料表有不同的結構描述。 目標資料表具有識別欄位。 此案例在不包含識別欄位的目標資料集定義中指定 "structure" 屬性。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> 從 SQL 接收叫用預存程序

將資料複製到 Azure SQL Database 受控執行個體時，您可以使用所指定的其他參數來設定及叫用預存程序。

當內建的複製機制無法滿足需求時，您可以使用預存程序。 在最後將來源資料插入目的地資料表之前，必須完成 upsert (更新並插入) 或額外處理時，通常會使用此程序。 額外處理可能包含合併資料行、查閱其他值和插入多個資料表等等的工作。

下列範例示範如何使用預存程序，對 SQL Server 資料庫中的資料表執行更新插入。 假設輸入資料和接收器 **Marketing** 資料表各有三個資料行：**ProfileID**、**State** 和 **Category**。 根據 **ProfileID** 資料行執行 upsert，然後僅針對特定的類別套用。

**輸出資料集：** "tableName"應該是您的預存程序 （請參閱下列預存程序的指令碼） 相同的資料表型別參數名稱。

```json
{
    "name": "AzureSqlMIDataset",
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

定義**SQL 接收器**一節中複製活動，如下所示。

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

在資料庫中，使用與 **SqlWriterStoredProcedureName** 相同的名稱來定義預存程序。 它會處理來自指定來源的輸入資料，並合併至輸出資料表。 預存程序中資料表類型的參數名稱應該與資料集中定義的 **tableName** 相同。

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
      VALUES (source.ProfileID, source.State, source.Category);
END
```

在資料庫中，使用與 sqlWriterTableType 相同的名稱來定義資料表類型。 資料表類型的結構描述會與輸入資料所傳回的結構描述相同。

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

預存程序功能使用[資料表值參數](https://msdn.microsoft.com/library/bb675163.aspx)。

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Azure SQL Database 受控執行個體的資料類型對應

從 Azure SQL Database 受控執行個體複製資料或將資料複製到該處時，會使用下列從 Azure SQL Database 受控執行個體資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

| Azure SQL Database 受控執行個體資料類型 | Azure Data Factory 過渡期資料類型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> 針對對應至 Decimal 過渡期類型的資料類型，Azure Data Factory 目前支援最多 28 個有效位數。 如果您的資料需要大於 28 個有效位數，請考慮轉換成 SQL 查詢中的字串。

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。
