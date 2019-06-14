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
ms.date: 06/13/2019
ms.author: jingwang
ms.openlocfilehash: e68b522d5a0fe7c359d83fc436aa7a1fd2159198
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048585"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure SQL Database 受控執行個體及從該處複製資料

本文概述如何使用 Azure Data Factory 中的複製活動，將資料複製到 Azure SQL Database 受控執行個體及從該處複製資料。 本文是以[複製活動概觀](copy-activity-overview.md)一文作為基礎，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 Azure SQL Database 受控執行個體複製到任何支援的接收資料存放區。 您也可以從任何支援的來源資料存放區將資料複製到受控執行個體。 如需複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Azure SQL Database 受控執行個體連接器支援：

- 使用 SQL 驗證來複製資料。
- 作為來源時，使用 SQL 查詢或預存程序來擷取資料。
- 在複製期間作為接收器時，使用自訂邏輯將資料附加到目的地資料表或叫用預存程序。

目前不支援 SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)。 

## <a name="prerequisites"></a>必要條件

若要存取 Azure SQL Database 受控執行個體 **[公用端點](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)** ，您可以使用受管理的 ADF Azure ir。 請確定您不只啟用公用端點，但也允許網路安全性群組，讓 ADF 必須能夠連線到您的資料庫中，依照下列中的 公用端點的流量[本指南](../sql-database/sql-database-managed-instance-public-endpoint-configure.md)。

若要存取 Azure SQL Database 受控執行個體**私用端點**、 設定[自我裝載整合執行階段](create-self-hosted-integration-runtime.md)可存取資料庫。 如果您佈建為受管理的執行個體相同的虛擬網路中的自我裝載的整合執行階段，請確定您的整合執行階段電腦會在您的受控執行個體的不同子網路。 如果您將自我裝載整合執行階段佈建在與受控執行個體不同的虛擬網路中，您可以使用虛擬網路對等互連或虛擬網路對虛擬網路的連線。 如需詳細資訊，請參閱[將您的應用程式連線到 Azure SQL Database 受控執行個體](../sql-database/sql-database-managed-instance-connect-app.md)。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Azure SQL Database 受控執行個體連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Azure SQL Database 受控執行個體連結服務支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為 **SqlServer**。 | 是。 |
| connectionString |此屬性會指定使用 SQL 驗證連線到受管理的執行個體所需的 connectionString 資訊。 如需詳細資訊，請參閱下列範例。 <br/>將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中。 您也可以將密碼放在 Azure Key Vault 中，而且，如果這是 SQL 驗證，則會從連接字串中提取 `password` 組態。 請參閱表格下方的 JSON 範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文深入了解詳細資料。 |是。 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用自我裝載 Integration Runtime 或 Azure Integration Runtime （如果您的受控執行個體具有公用端點，而且允許存取的 ADF）。 如果未指定，就會使用預設的 Azure Integration Runtime。 |是。 |

**範例 1：使用 SQL 驗證**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername:port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
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

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱資料集文章。 本節提供 Azure SQL Database 受控執行個體資料集所支援的屬性清單。

若要與 Azure SQL Database 受控執行個體，請複製資料，支援下列屬性：

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
        "schema": [ < physical schema, optional, retrievable during authoring > ],
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

> [!TIP]
> 深入了解支援的寫入行為、 組態和最佳做法是從[將資料載入 Azure SQL Database 受控執行個體的最佳做法](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)。

若要將資料複製到 Azure SQL Database 受控執行個體，請將複製活動中的接收類型設定為 **SqlSink**。 複製活動的 [接收] 區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動接收的 type 屬性必須設定為 **SqlSink**。 | 是。 |
| writeBatchSize |要插入至 SQL 資料表的資料列的數目**每個批次**。<br/>允許的值為整數的資料列數目。 依預設，Data Factory 以動態方式決定適當的批次大小為基礎的資料列大小。  |否 |
| writeBatchTimeout |此屬性會指定在逾時前等待批次插入作業完成的時間。<br/>允許的值為時間範圍。 範例是 “00:30:00”，也就是 30 分鐘。 |沒有。 |
| preCopyScript |此屬性會針對複製活動指定一個 SQL 查詢，在將資料寫入到受控執行個體之前執行。 每一複製回合只會叫用此查詢一次。 您可以使用此屬性來清除預先載入的資料。 |沒有。 |
| sqlWriterStoredProcedureName |這是用來定義如何將來源資料套用到目標資料表的預存程序名稱。 <br/>此預存程序將會*依批次叫用*。 若要進行只執行一次且與來源資料無關的作業 (例如刪除或截斷)，請使用 `preCopyScript` 屬性。 |沒有。 |
| storedProcedureParameters |這些是用於預存程序的參數。<br/>允許的值為名稱或值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 |沒有。 |
| sqlWriterTableType |此參數會指定要在預存程序中使用的資料表類型名稱。 複製活動可讓正在移動的資料可用於此資料表類型的暫存資料表。 然後，預存程序程式碼可以合併正在複製的資料與現有的資料。 |沒有。 |

**範例 1： 附加資料**

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

**範例 2： 在複製期間呼叫的預存的程序**

若要了解更多詳細資料，請參閱[叫用 SQL 接收中的預存程序](#invoking-stored-procedure-for-sql-sink)。

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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>資料載入 Azure SQL Database 受控執行個體的最佳作法

當您將資料複製到 Azure SQL Database 受控執行個體時，您可能需要不同的寫入行為：

- **[附加](#append-data)** ： 我的來源資料只會有新的記錄;
- **[Upsert](#upsert-data)** ： 我的來源資料有插入和更新;
- **[覆寫](#overwrite-entire-table)** :我想要重新載入整個維度資料表每次;
- **[寫入使用自訂邏輯](#write-data-with-custom-logic)** :我需要在最後一個插入至目的地資料表之前的額外處理。

請參閱分別章節，到如何在 ADF 和最佳作法設定。

### <a name="append-data"></a>附加資料

這是預設行為，此 Azure SQL Database 受控執行個體的接收連接器，和 ADF 嗎**bulk insert**有效率地寫入至您的資料表。 您只可以設定來源，並據以接收複製活動中。

### <a name="upsert-data"></a>更新插入資料

**選項我**（特別是當您有要複製的大型資料建議）：**大部分的高效能方法**執行 upsert 如下： 

- 首先，利用[暫存資料表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables)來大量載入，使用複製活動的所有記錄。 因為對暫存資料表的作業不會記錄，您可以載入數百萬筆記錄，以秒為單位。
- 在要套用的 ADF 執行預存程序活動[合併](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current)（或插入/更新） 陳述式，並使用 temp 資料表來源，以執行所有更新或插入做為單一交易中，減少往返量，並記錄作業。 在預存程序活動結束時，就可能被截斷暫存資料表以做好下次 upsert 循環。 

例如，，您也可以在 Azure Data Factory 中建立管線，其中含有**複製活動**與鏈結**預存程序活動**成功。 先前的資料複製從來源儲存到暫存資料表時，假設 「 **##UpsertTempTable**"做為資料集中的資料表名稱，然後第二個會叫用來從暫存資料表的來源資料合併到目標資料表，並清除預存程序暫存資料表。

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

## <a name="invoking-stored-procedure-for-sql-sink"></a> 從 SQL 接收叫用預存程序

當您將資料複製到 Azure SQL Database 受控執行個體時，您也可以設定，並叫用使用者自訂預存程序搭配其他參數。

> [!TIP]
> 叫用預存程序會處理資料列逐列，而非大量作業，不建議針對大規模複製。 進一步了解[將資料載入 Azure SQL Database 受控執行個體的最佳做法](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)。

您可以使用預存程序，當內建的複製機制不提供的用途，例如，適用於來源資料的最後一個插入至目的地資料表之前的額外處理。 一些額外處理的範例包括合併資料行、查閱其他的值，以及插入多個資料表中。

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
