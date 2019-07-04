---
title: 使用 Azure Data Factory 從 SQL Server 來回複製資料 |Microsoft Docs
description: 深入了解如何使用 Azure Data Factory 來移動資料和從內部部署 SQL Server 資料庫或 Azure VM 中。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: jingwang
ms.openlocfilehash: a6767c7c8931898c44fd748dbe4299b8ed23eb9c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443281"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 SQL Server 來回複製資料
> [!div class="op_single_selector" title1="選取您要使用的 Azure Data Factory 的版本："]
> * [第 1 版](v1/data-factory-sqlserver-connector.md)
> * [目前的版本](connector-sql-server.md)

本文概述如何使用 Azure Data Factory 中的 「 複製活動，SQL Server 資料庫來回複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

您可以到任何支援的接收資料存放區的 SQL Server 資料庫來回複製資料。 或者，您也可以將資料從任何支援的來源資料存放區中，複製到 SQL Server 資料庫。 如需複製活動所支援作為來源或接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 SQL Server 連接器支援：

- 2016、 2014年、 2012年、 2008 R2、 2008年及 2005年的 SQL Server 版本。
- 使用 SQL 或 Windows 驗證來複製資料。
- 做為來源，請使用 SQL 查詢或預存程序中擷取資料。
- 在複製期間作為接收器時，使用自訂邏輯將資料附加到目的地資料表或叫用預存程序。

>[!NOTE]
>SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)現在不支援此連接器。 若要解決，您可以使用[一般的 ODBC 連接器](connector-odbc.md)和 SQL Server ODBC 驅動程式。 請遵循[本指南](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017)具有 ODBC 驅動程式下載及連接字串組態。

## <a name="prerequisites"></a>必要條件

若要從不可公開存取的 SQL Server 資料庫複製資料，您需要設定自我裝載的整合執行階段。 如需詳細資訊，請參閱[自我裝載整合執行階段](create-self-hosted-integration-runtime.md)。 Integration runtime 提供內建的 SQL Server 資料庫驅動程式。 您不需要手動安裝任何驅動程式，SQL Server 資料庫來回複製資料時。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供有關用來定義 SQL Server 資料庫連接器專屬的 Data Factory 實體的屬性的詳細資料。

## <a name="linked-service-properties"></a>連結服務屬性

SQL Server 連結服務支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為 **SqlServer**。 | 是 |
| connectionString |指定**connectionString**使用 SQL 驗證] 或 [Windows 驗證連接到 SQL Server 資料庫所需的資訊。 請參考下列範例。<br/>將做為這個欄位標記**SecureString**若要將它安全地儲存在 Azure Data Factory。 您也可以放在 Azure Key Vault 的密碼。 如果是 SQL 驗證，提取`password`設定連接字串。 如需詳細資訊，請參閱表格後的 JSON 範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)。 |是 |
| userName |如果您使用 Windows 驗證，請指定使用者名稱。 範例為 **domainname\\username**。 |否 |
| password |指定您所指定的使用者名稱的使用者帳戶的密碼。 將做為這個欄位標記**SecureString**若要將它安全地儲存在 Azure Data Factory。 或者，您可以[參考儲存在 Azure Key Vault 祕密](store-credentials-in-key-vault.md)。 |否 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果您的資料存放區是可公開存取，您可以使用自我裝載的整合執行階段或 Azure 整合執行階段。 如果未指定，則會使用預設的 Azure 整合執行階段。 |否 |

>[!TIP]
>如果您遇到錯誤碼"UserErrorFailedToConnectToSqlServer"發生錯誤和訊息 「 資料庫工作階段限制是 XXX，已達到 「，新增`Pooling=false`您的連接字串，並再試一次。

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

**範例 2：使用 Azure Key Vault 中密碼的 SQL 驗證**

```json
{
    "name": "SqlServerLinkedService",
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

如需可用來定義資料集的區段和屬性完整清單，請參閱資料集文章。 本節提供 SQL Server 資料集所支援的屬性的清單。

若要將資料複製到 SQL Server 資料庫的往返，支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為 **SqlServerTable**。 | 是 |
| tableName |這個屬性是資料表或檢視連結的服務是指 SQL Server 資料庫執行個體的名稱。 | 否 (來源)；是 (接收) |

**範例**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 SQL Server 來源和接收器所支援的屬性的清單。

### <a name="sql-server-as-a-source"></a>SQL Server 作為來源

若要從 SQL Server 複製資料，請將複製活動中的來源類型設定為 **SqlSource**。 複製活動的 [來源] 區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動來源的 type屬性必須設定為 **SqlSource**。 | 是 |
| SqlReaderQuery |使用自訂 SQL 查詢來讀取資料。 例如 `select * from MyTable`。 |否 |
| sqlReaderStoredProcedureName |此屬性是從來源資料表讀取資料的預存程序名稱。 最後一個 SQL 陳述式必須是預存程序中的 SELECT 陳述式。 |否 |
| storedProcedureParameters |這些是預存程序的參數。<br/>允許的值為名稱或值組。 名稱和大小寫的參數必須符合的名稱和大小寫的預存程序參數。 |否 |

**注意事項：**

- 如果**sqlReaderQuery**指定**SqlSource**，「 複製活動會針對 SQL Server 來源取得的資料執行此查詢。 如果預存程序接受參數，您也可以藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** 來指定預存程序。
- 如果您未指定其中一個**sqlReaderQuery**或是**sqlReaderStoredProcedureName**，資料集 JSON 的"structure"區段中定義的資料行用來建構查詢。 查詢`select column1, column2 from mytable`執行對 SQL Server。 如果資料集定義沒有 "structure"，則會從資料表中選取所有資料行。

**範例：使用 SQL 查詢**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "referenceName": "<SQL Server input dataset name>",
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

### <a name="sql-server-as-a-sink"></a>SQL Server 作為接收器

> [!TIP]
> 深入了解支援的寫入行為、 組態和最佳作法[的資料載入 SQL Server 最佳做法](#best-practice-for-loading-data-into-sql-server)。

若要將資料複製到 SQL Server，請將複製活動中的接收器類型設定為 **SqlSink**。 複製活動的 [接收] 區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動接收的 type 屬性必須設定為 **SqlSink**。 | 是 |
| writeBatchSize |要插入 SQL 資料表的資料列的數目*每個批次*。<br/>允許的值為整數的資料列數目。 根據預設，Azure Data Factory 以動態方式決定適當的批次大小為基礎的資料列大小。 |否 |
| writeBatchTimeout |此屬性會指定在逾時前等待批次插入作業完成的時間。<br/>時間範圍是允許的值。 例如，"00: 30:00"30 分鐘。 |否 |
| preCopyScript |此屬性會指定複製活動將資料寫入至 SQL Server 前執行的 SQL 查詢。 每一複製回合只會叫用此查詢一次。 您可以使用此屬性來清除預先載入的資料。 |否 |
| sqlWriterStoredProcedureName |這是用來定義如何將來源資料套用到目標資料表的預存程序名稱。<br/>此預存程序將會*依批次叫用*。 若要進行只執行一次且與來源資料無關的作業 (例如刪除或截斷)，請使用 `preCopyScript` 屬性。 |否 |
| storedProcedureParameters |這些是用於預存程序的參數。<br/>允許的值為名稱或值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 |否 |
| sqlWriterTableType |此參數會指定要在預存程序中使用的資料表類型名稱。 複製活動可讓正在移動的資料可用於此資料表類型的暫存資料表。 然後，預存程序程式碼可以合併正在複製的資料與現有的資料。 |否 |

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
                "referenceName": "<SQL Server output dataset name>",
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

**範例 2：在複製期間叫用預存程序**

若要了解更多詳細資料，請參閱[叫用 SQL 接收中的預存程序](#invoke-a-stored-procedure-from-a-sql-sink)。

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
                "referenceName": "<SQL Server output dataset name>",
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

## <a name="best-practice-for-loading-data-into-sql-server"></a>資料載入 SQL Server 的最佳作法

當您將資料複製到 SQL Server 時，您可能需要不同的寫入行為：

- [附加](#append-data):我的來源資料會有新的記錄。
- [Upsert](#upsert-data):我的來源資料有插入和更新。
- [覆寫](#overwrite-the-entire-table):我想要重新載入整個維度資料表每一次。
- [使用自訂邏輯撰寫](#write-data-with-custom-logic):我需要在最後一個插入至目的地資料表之前的額外處理。

請參閱個別的章節說明如何在 Azure Data Factory 和最佳作法設定。

### <a name="append-data"></a>附加資料

將資料附加為此 SQL Server 接收連接器的預設行為。 Azure Data Factory 會執行大量插入來有效率地寫入至您的資料表。 您可以設定來源，並據以接收將複製活動中。

### <a name="upsert-data"></a>更新插入資料

**選項 1：** 當您有大量複製，請使用下列方法來執行更新插入的資料： 

- 首先，使用[暫存資料表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables)所有記錄使用複製活動的大量載入。 不記錄針對暫存資料表的作業，因為您可以載入數百萬筆記錄，以秒為單位。
- 若要套用的 Azure Data Factory 中執行的預存程序活動[合併](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current)或 INSERT/UPDATE 陳述式。 為來源，以執行所有更新或插入做為單一交易，請使用暫存資料表。 如此一來，被減少往返和記錄作業的數目。 在預存程序活動結束時，就可能被截斷暫存資料表以做好下次 upsert 循環。

例如，，您也可以在 Azure Data Factory 中建立管線，其中含有**複製活動**與鏈結**預存程序活動**。 先前的資料複製從您的來源存放區到資料庫的暫存資料表，例如 **##UpsertTempTable**，資料集內資料表的名稱。 然後，後者會叫用來從暫存資料表的來源資料合併到目標資料表，並清除 暫存資料表的預存程序。

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

在資料庫中，定義合併的邏輯，類似下列的範例中，指向在上一個預存程序活動從預存程序。 假設目標**行銷**具有三個資料行的資料表：**ProfileID**、**State** 和 **Category**。 請勿根據 upsert **ProfileID**資料行。

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**選項 2：** 您也可以選擇[叫用 「 複製活動中的預存程序](#invoke-a-stored-procedure-from-a-sql-sink)。 這種方法會執行每個資料列，而不是在複製活動中，並不適合用於大規模的 upsert 的預設方法為使用大量插入的來源資料表中。

### <a name="overwrite-the-entire-table"></a>覆寫整份資料表

您可以設定**preCopyScript**中複製活動接收器的屬性。 在此情況下，每個複製活動執行，Azure Data Factory 的指令碼會先執行。 然後它會執行插入資料的複本。 比方說，若要覆寫整個資料表，以最新的資料，指定指令碼以先刪除所有記錄，然後您大量載入新的資料來源的資料。

### <a name="write-data-with-custom-logic"></a>寫入使用自訂邏輯的資料

撰寫自訂的邏輯與資料的步驟很類似中所述[Upsert 資料](#upsert-data)一節。 當您需要套用額外處理之前將最後一個插入的來源資料的目的地資料表，大規模的您可以執行下列其中一種： 

- 載入至暫存資料表，並接著叫用預存程序。 
- 在複製期間，叫用預存程序。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> 從 SQL 接收叫用預存程序

當您將資料複製到 SQL Server 資料庫時，您也可以設定，並叫用使用者自訂預存程序搭配其他參數。

> [!TIP]
> 叫用預存程序以處理資料列而不是使用大量作業，我們不建議大規模的複本。 進一步了解[的資料載入 SQL Server 最佳做法](#best-practice-for-loading-data-into-sql-server)。

當內建的複製機制無法滿足需求時，您可以使用預存程序。 舉例來說，當您想要套用最後一個插入的來源資料至目的地資料表之前的額外處理。 一些額外的處理範例包括當您想要合併的資料行、 查閱其他值，並將資料插入多個資料表。

下列範例示範如何使用預存程序，對 SQL Server 資料庫中的資料表執行更新插入。 假設輸入的資料，並接收**行銷**每個資料表有三個資料行：**ProfileID**、**State** 和 **Category**。 根據 **ProfileID** 資料行執行 upsert，然後僅針對特定的類別套用。

**輸出資料集：** "tableName"是在您的預存程序相同的資料表型別參數名稱，如下列的預存程序指令碼所示：

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

定義**SQL 接收器**一節中複製活動，如下所示：

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

在資料庫中，使用與 **SqlWriterStoredProcedureName** 相同的名稱來定義預存程序。 它會處理來自指定來源的輸入資料，並合併至輸出資料表。 預存程序中的資料表類型的參數名稱是相同**tableName**資料集中定義。

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

在資料庫中，定義資料表類型同名**sqlWriterTableType**。 資料表類型的結構描述會與輸入資料所傳回的結構描述相同。

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

預存程序功能使用[資料表值參數](https://msdn.microsoft.com/library/bb675163.aspx)。

## <a name="data-type-mapping-for-sql-server"></a>SQL Server 的資料類型對應

當您在 SQL Server 來回複製資料時，請使用下列對應會從 SQL Server 資料類型到 Azure Data Factory 過渡期資料類型。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

| SQL Server 資料類型 | Azure Data Factory 過渡期資料類型 |
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

## <a name="troubleshoot-connection-issues"></a>針對連線問題進行疑難排解

1. 設定您的 SQL Server 執行個體，以接受遠端連接。 開始**SQL Server Management Studio**，以滑鼠右鍵按一下**伺服器**，然後選取**屬性**。 選取 **連線**從清單中，選取**允許遠端連線到此伺服器**核取方塊。

    ![啟用遠端連線](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    如需詳細步驟，請參閱 <<c0> [ 設定遠端存取伺服器組態選項](https://msdn.microsoft.com/library/ms191464.aspx)。

2. 開始**SQL Server 組態管理員**。 展開您想要之執行個體的 [SQL Server 網路組態]  ，然後選取 [MSSQLSERVER 的通訊協定]  。 通訊協定會出現在右窗格中。 啟用 TCP/IP，以滑鼠右鍵按一下**TCP/IP** ，然後選取**啟用**。

    ![啟用 TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    如需詳細資訊和啟用 TCP/IP 通訊協定的替代方式，請參閱[啟用或停用伺服器網路通訊協定](https://msdn.microsoft.com/library/ms191294.aspx)。

3. 在相同的視窗中，按兩下**TCP/IP**來啟動**TCP/IP 內容**視窗。
4. 切換到 [IP 位址]  索引標籤。向下捲動**IPAll**一節。 請記下**TCP 連接埠**。 預設值是**1433年**。
5. 在電腦上建立 **Windows 防火牆規則** ，來允許透過此連接埠的連入流量。 
6. **驗證連線**：若要使用完整限定的名稱，以連接到 SQL Server，使用 SQL Server Management Studio 從一部電腦。 例如 `"<machine>.<domain>.corp.<company>.com,1433"`。

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。
