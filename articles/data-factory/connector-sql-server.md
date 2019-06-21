---
title: 使用 Azure Data Factory 將資料複製到 SQL Server 或從該處複製資料 | Microsoft Docs
description: 了解如何使用 Azure Data Factory，從內部部署或 Azure VM 中的 SQL Server 資料庫來回移動資料。
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
ms.openlocfilehash: 62845557f33fd9c4f3c2ec4e239213c75101955d
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275973"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 SQL Server 及從該處複製資料
> [!div class="op_single_selector" title1="選取您正在使用的 Data Factory 服務的版本："]
> * [第 1 版](v1/data-factory-sqlserver-connector.md)
> * [目前的版本](connector-sql-server.md)

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 SQL Server 資料庫複製資料及將資料複製到該處。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 SQL Server 資料庫複製到任何支援的接收資料存放區，或將資料從任何支援的來源資料存放區複製到 SQL Server 資料庫。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 SQL Server 連接器支援：

- SQL Server 2016、2014、2012、2008 R2、2008 及 2005 版
- 使用 **SQL** 或 **Windows** 驗證來複製資料。
- 作為來源時，使用 SQL 查詢或預存程序來擷取資料。
- 在複製期間作為接收器時，使用自訂邏輯將資料附加到目的地資料表或叫用預存程序。

>[!NOTE]
>SQL Server **[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)** 目前不支援此連接器。 若要解決，您可以使用[一般的 ODBC 連接器](connector-odbc.md)和 SQL Server ODBC 驅動程式。 請遵循[本指南](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017)具有 ODBC 驅動程式下載及連接字串組態。

## <a name="prerequisites"></a>必要條件

若要從不可公開存取的 SQL Server 資料庫複製資料，您必須設定一個「自我裝載 Integration Runtime」。 如需詳細資料，請參閱[自我裝載 Integration Runtime](create-self-hosted-integration-runtime.md) 一文。 Integration Runtime 提供內建的 SQL Server 資料庫驅動程式，因此從 SQL Server 資料庫複製資料或將資料複製到該處時，您不需要手動安裝任何驅動程式。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 SQL Server 資料庫連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 SQL Server 已連結服務支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為：**SqlServer** | 是 |
| connectionString |指定使用 SQL 驗證或 Windows 驗證來連線至 SQL Server 資料庫時所需的 connectionString 資訊。 請參考下列範例。<br/>將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中。 您也可以將密碼放在 Azure Key Vault 中，而且，如果這是 SQL 驗證，則會從連接字串中提取 `password` 組態。 請參閱表格下方的 JSON 範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文深入了解詳細資料。 |是 |
| userName |如果您使用「Windows 驗證」，請指定使用者名稱。 範例︰**domainname\\username**。 |否 |
| password |指定您為 userName 指定之使用者帳戶的密碼。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |否 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 您可以使用「自我裝載 Integration Runtime」或 Azure Integration Runtime (如果您的資料存放區是可公開存取的)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

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

**範例 2：使用 SQL 驗證搭配 Azure Key Vault 中的密碼**

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

如需可用來定義資料集的區段和屬性完整清單，請參閱資料集文章。 本節提供 SQL Server 資料集所支援的屬性清單。

若要複製資料，或將 SQL Server 資料庫，支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為：**SqlServerTable** | 是 |
| tableName |SQL Server 資料庫執行個體中已連結的服務所參考的資料表或檢視名稱。 | 否 (來源)；是 (接收) |

**範例：**

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

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 SQL Server 來源和接收器所支援的屬性清單。

### <a name="sql-server-as-source"></a>SQL Server 作為來源

若要從 SQL Server 複製資料，請將複製活動中的來源類型設定為 **SqlSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為：**SqlSource** | 是 |
| SqlReaderQuery |使用自訂 SQL 查詢來讀取資料。 範例： `select * from MyTable`. |否 |
| sqlReaderStoredProcedureName |從來源資料表讀取資料的預存程序名稱。 最後一個 SQL 陳述式必須是預存程序中的 SELECT 陳述式。 |否 |
| storedProcedureParameters |預存程序的參數。<br/>允許的值為：名稱/值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 |否 |

**注意事項：**

- 如果已為 SqlSource 指定 **sqlReaderQuery**，「複製活動」就會針對 SQL Server 來源執行此查詢來取得資料。 或者，您可以藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** (如果預存程序接受參數) 來指定預存程序。
- 如果您未指定 "sqlReaderQuery" 或 "sqlReaderStoredProcedureName"，就會使用資料集 JSON 的 "structure" 區段中定義的資料行，來建構要針對 SQL Server 執行的查詢 (`select column1, column2 from mytable`)。 如果資料集定義沒有 "structure"，則會從資料表中選取所有資料行。

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

**預存程序定義：**

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

### <a name="sql-server-as-sink"></a>SQL Server 作為接收器

> [!TIP]
> 深入了解支援的寫入行為、 組態和最佳做法是從[的資料載入 SQL Server 最佳做法](#best-practice-for-loading-data-into-sql-server)。

若要將資料複製到 SQL Server，請將複製活動中的接收器類型設定為 **SqlSink**。 複製活動的 **sink** 區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動接收器的類型屬性必須設定為：**SqlSink** | 是 |
| writeBatchSize |要插入至 SQL 資料表的資料列的數目**每個批次**。<br/>允許的值為：整數 (資料列數目)。 依預設，Data Factory 以動態方式決定適當的批次大小為基礎的資料列大小。 |否 |
| writeBatchTimeout |在逾時前等待批次插入作業完成的時間。<br/>允許的值為：時間範圍。 範例：“00:30:00” (30 分鐘)。 |否 |
| preCopyScript |指定一個供「複製活動」在將資料寫入到 SQL Server 前執行的 SQL 查詢。 每一複製回合只會叫用此查詢一次。 您可以使用此屬性來清除預先載入的資料。 |否 |
| sqlWriterStoredProcedureName |定義如何將來源資料套用至目標資料表的預存程序的名稱。<br/>請注意，將會**依批次叫用**此預存程序。 如果您想要進行只執行一次且與來源資料無關的作業 (例如刪除/截斷)，請使用 `preCopyScript` 屬性。 |否 |
| storedProcedureParameters |預存程序的參數。<br/>允許的值為：名稱/值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 |否 |
| sqlWriterTableType |指定要在預存程序中使用的資料表類型名稱。 複製活動可讓正在移動的資料可用於此資料表類型的暫存資料表。 然後，預存程序程式碼可以合併正在複製的資料與現有的資料。 |否 |

**範例 1： 附加資料**

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

**範例 2： 在複製期間呼叫的預存的程序**

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

- **[附加](#append-data)** ： 我的來源資料只會有新的記錄;
- **[Upsert](#upsert-data)** ： 我的來源資料有插入和更新;
- **[覆寫](#overwrite-entire-table)** :我想要重新載入整個維度資料表每次;
- **[寫入使用自訂邏輯](#write-data-with-custom-logic)** :我需要在最後一個插入至目的地資料表之前的額外處理。

請參閱分別章節，到如何在 ADF 和最佳作法設定。

### <a name="append-data"></a>附加資料

這是預設行為，此 SQL Server 接收連接器，和 ADF 嗎**bulk insert**有效率地寫入至您的資料表。 您只可以設定來源，並據以接收複製活動中。

### <a name="upsert-data"></a>更新插入資料

**選項我**（特別是當您有要複製的大型資料建議）：**大部分的高效能方法**執行 upsert 如下： 

- 首先，利用[暫存資料表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables)來大量載入，使用複製活動的所有記錄。 因為對暫存資料表的作業不會記錄，您可以載入數百萬筆記錄，以秒為單位。
- 在要套用的 ADF 執行預存程序活動[合併](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current)（或插入/更新） 陳述式，並使用 temp 資料表來源，以執行所有更新或插入做為單一交易中，減少往返量，並記錄作業。 在預存程序活動結束時，就可能被截斷暫存資料表以做好下次 upsert 循環。 

例如，，您也可以在 Azure Data Factory 中建立管線，其中含有**複製活動**與鏈結**預存程序活動**成功。 先前的資料複製從您的來源存放區到資料庫的暫存資料表，例如" **##UpsertTempTable**"做為資料集中的資料表名稱，然後第二個會叫用來從暫存資料表的來源資料合併到目標資料表的預存程序並清除暫存資料表。

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

在資料庫中，定義與合併邏輯，如下所示，從上述的預存程序活動指的預存程序。 假設目標**行銷**具有三個資料行的資料表：**ProfileID**，**狀態**，以及**類別**，並執行 upsert 根據**ProfileID**資料行。

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

**選項二部分：** 或者，您可以選擇[叫用預存程序中複製活動](#invoking-stored-procedure-for-sql-sink)，同時請注意，這種方法會針對每個資料列中來源資料表，而不是利用大量執行插入成為預設方法在複製活動中，因此它並不適合用於大規模 upsert。

### <a name="overwrite-entire-table"></a>覆寫整份資料表

您可以設定**preCopyScript**接收複製活動中的屬性，在此情況下每個複製活動執行，ADF 指令碼會先執行，然後執行 插入資料的複本。 例如，若要以最新的資料覆寫整個資料表，您可以先指定指令碼以刪除所有記錄，再從來源大量載入新資料。

### <a name="write-data-with-custom-logic"></a>寫入使用自訂邏輯的資料

中所述類似[Upsert 資料](#upsert-data) 區段中，當您需要套用至目的地資料表中，來源資料在最後一次之前的額外處理可以) 針對大規模的載入至暫存資料表，然後叫用預存程序或 b） 在複製期間叫用預存的程序。

## <a name="invoking-stored-procedure-for-sql-sink"></a> 從 SQL 接收器叫用預存程序

將資料複製到 SQL Server 資料庫中，您也可以設定，並叫用使用者自訂預存程序搭配其他參數。

> [!TIP]
> 叫用預存程序會處理資料列逐列，而非大量作業，不建議針對大規模複製。 進一步了解[的資料載入 SQL Server 最佳做法](#best-practice-for-loading-data-into-sql-server)。

您可以使用預存程序，當內建的複製機制不提供的用途，例如，適用於來源資料的最後一個插入至目的地資料表之前的額外處理。 一些額外處理的範例包括合併資料行、查閱其他的值，以及插入多個資料表中。

下列範例示範如何使用預存程序，對 SQL Server 資料庫中的資料表執行更新插入。 假設輸入資料和接收器 **Marketing** 資料表各有三個資料行：**ProfileID**、**State** 和 **Category**。 根據 **ProfileID** 資料行執行 upsert，然後僅針對特定的類別套用。

**輸出資料集：** "tableName"應該是您的預存程序 （請參閱下列預存程序的指令碼） 相同的資料表型別參數名稱。

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

在資料庫中，使用與 sqlWriterTableType 相同的名稱來定義資料表類型。 請注意，資料表類型的結構描述應該與輸入資料所傳回的結構描述相同。

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

預存程序功能使用 [資料表值參數](https://msdn.microsoft.com/library/bb675163.aspx)。

## <a name="data-type-mapping-for-sql-server"></a>SQL Server 的資料類型對應

從 SQL Server 複製資料或將資料複製到該處時，會使用下列從 SQL Server 資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收器。

| SQL Server 資料類型 | Data Factory 過渡期資料類型 |
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
| Xml |xml |

>[!NOTE]
> 針對對應至 Decimal 過渡期類型的資料類型，目前 ADF 支援的有效位數最多可達 28。 如果您有有效位數超過 28 的資料，請考慮在 SQL 查詢中將其轉換成字串。

## <a name="troubleshooting-connection-issues"></a>疑難排解連線問題

1. 將 SQL Server 設定成接受遠端連線。 啟動 [SQL Server Management Studio]  、用滑鼠右鍵按一下 [伺服器]  ，然後按一下 [屬性]  。 選取清單中 [連接]  ，然後核取 [允許此伺服器的遠端連接]  。

    ![啟用遠端連線](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    如需詳細步驟，請參閱 [設定 remote access 伺服器組態選項](https://msdn.microsoft.com/library/ms191464.aspx) 。

2. 啟動 [SQL Server 組態管理員]  。 展開您想要之執行個體的 [SQL Server 網路組態]  ，然後選取 [MSSQLSERVER 的通訊協定]  。 您應該會在右窗格中看到通訊協定。 用滑鼠右鍵按一下 [TCP/IP]  ，然後按一下 [啟用]  來啟用 TCP/IP。

    ![啟用 TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    如需啟用 TCP/IP 通訊協定的詳細資料及替代方式，請參閱 [啟用或停用伺服器網路通訊協定](https://msdn.microsoft.com/library/ms191294.aspx) 。

3. 在相同的視窗中，按兩下 [TCP/IP]  來啟動 [TCP/IP 屬性]  視窗。
4. 切換到 [IP 位址]  索引標籤。向下捲動到 [IPAll]  區段。 記下 [TCP 連接埠]  (預設值是 **1433**)。
5. 在電腦上建立 **Windows 防火牆規則** ，來允許透過此連接埠的連入流量。  
6. **驗證連線**：若要使用完整名稱來連線到 SQL Server，請使用來自不同機器的 SQL Server Management Studio。 例如： `"<machine>.<domain>.corp.<company>.com,1433"` 。

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。
