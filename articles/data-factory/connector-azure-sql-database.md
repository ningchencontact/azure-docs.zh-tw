---
title: 使用 Data Factory 從 Azure SQL Database 來回複製資料 | Microsoft Docs
description: 了解如何使用 Data Factory 將資料從支援的來源資料存放區複製到 Azure SQL Database，或從 SQL Database 複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: jingwang
ms.openlocfilehash: 5dbd739070b1f66fe5ff04f319a3818269d0bdaa
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449599"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure SQL Database 或從該處複製資料
> [!div class="op_single_selector" title1="選取您要使用的 Azure Data Factory 的版本："]
> * [第 1 版](v1/data-factory-azure-sql-connector.md)
> * [目前的版本](connector-azure-sql-database.md)

本文概述如何從 Azure SQL Database 來回複製資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

此 Azure SQL Database 連接器支援下列活動：

- [複製活動](copy-activity-overview.md)具有[支援來源/接收器矩陣](copy-activity-overview.md)資料表
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)

具體而言，這個 Azure SQL Database 連接器支援下列功能：

- 適用於 Azure 資源與服務主體或受控身分識別使用 SQL 驗證和 Azure Active Directory (Azure AD) 應用程式權杖驗證來複製資料。
- 做為來源，請使用 SQL 查詢或預存程序中擷取資料。
- 作為接收器時，將資料附加到目的地資料表或叫用自訂邏輯的預存程序在複製期間。

>[!NOTE]
>Azure SQL Database [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current)現在不支援此連接器。 若要解決，您可以使用[一般的 ODBC 連接器](connector-odbc.md)以及 SQL Server ODBC 驅動程式透過自我裝載的整合執行階段。 請遵循[本指南](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current)具有 ODBC 驅動程式下載及連接字串組態。

> [!IMPORTANT]
> 如果您使用 Azure Data Factory 整合執行階段複製資料，請設定[Azure SQL Server 防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)，讓 Azure 服務可以存取伺服器。
> 如果您使用自我裝載的整合執行階段複製資料，Azure SQL Server 防火牆設定為允許適當的 IP 範圍。 此範圍包括用來連接到 Azure SQL Database 之機器的 IP。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供有關用來定義 Azure SQL Database 連接器專屬的 Azure Data Factory 實體的屬性的詳細資料。

## <a name="linked-service-properties"></a>連結服務屬性

以下是支援 Azure SQL Database 已連結服務的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | **type** 屬性必須設為 **AzureSqlDatabase**。 | 是 |
| connectionString | 針對 **connectionString** 屬性指定連線到 Azure SQL Database 執行個體所需的資訊。 <br/>將做為這個欄位標記**SecureString**若要將它安全地儲存在 Azure Data Factory。 您也可以放在 Azure Key Vault 密碼或服務主體金鑰。 如果是 SQL 驗證，提取`password`設定連接字串。 如需詳細資訊，請參閱表格後的 JSON 範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)。 | 是 |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 是，當您使用 Azure AD 驗證與服務主體 |
| servicePrincipalKey | 指定應用程式的金鑰。 將做為這個欄位標記**SecureString**若要將它安全地儲存在 Azure Data Factory 或[參考儲存在 Azure Key Vault 祕密](store-credentials-in-key-vault.md)。 | 是，當您使用 Azure AD 驗證與服務主體 |
| tenant | 指定租用戶資訊，例如網域名稱或租用戶的識別碼，您的應用程式所在。 游標停留在 Azure 入口網站右上角，擷取它。 | 是，當您使用 Azure AD 驗證與服務主體 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果您的資料存放區位於私人網路中，您可以使用 Azure integration runtime 或自我裝載的整合執行階段。 如果未指定，則會使用預設的 Azure 整合執行階段。 | 否 |

針對不同的驗證類型，請分別參閱下列有關先決條件和 JSON 範例的章節：

- [SQL 驗證](#sql-authentication)
- [Azure AD 應用程式權杖驗證：服務主體](#service-principal-authentication)
- [Azure AD 應用程式權杖驗證：適用於 Azure 資源的受控識別](#managed-identity)

>[!TIP]
>如果您遇到錯誤碼"UserErrorFailedToConnectToSqlServer"發生錯誤和訊息 「 資料庫工作階段限制是 XXX，已達到 「，新增`Pooling=false`您的連接字串，並再試一次。

### <a name="sql-authentication"></a>SQL 驗證

#### <a name="linked-service-example-that-uses-sql-authentication"></a>使用 SQL 驗證的連結服務範例

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Azure Key Vault 中的密碼** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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

### <a name="service-principal-authentication"></a>服務主體驗證

若要使用以服務主體為基礎的 Azure AD 應用程式權杖驗證，請遵循下列步驟：

1. [建立 Azure Active Directory 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)從 Azure 入口網站。 請記下應用程式名稱，以及下列可定義連結服務的值：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. [佈建 Azure Active Directory 系統管理員](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)針對您的 Azure SQL 伺服器，如果您還在 Azure 入口網站。 Azure AD 系統管理員必須是 Azure AD 使用者或 Azure AD 群組，但不能是服務主體。 此步驟必須完成，如此您才可以在下一個步驟中使用 Azure AD 身分識別，為服務主體建立自主資料庫使用者。

3. [建立自主的資料庫使用者](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)服務主體。 連接到資料庫，或您要使用至少要有 Azure AD 身分識別的 SQL Server Management Studio 等工具，將資料複製 ALTER ANY USER 權限。 執行下列 T-SQL： 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 授與服務主體所需的權限，像平常針對 SQL 使用者或其他人一樣。 執行下列程式碼。 如需其他選項，請參閱 <<c0> [ 這份文件](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)。

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. 設定 Azure Data Factory 中的 Azure SQL Database 連結服務。


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>使用服務主體驗證的連結服務範例

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Azure 資源的受控識別驗證

資料處理站可與 [Azure 資源的受控識別](data-factory-service-identity.md)相關聯，後者表示特定的資料處理站。 您可以使用這個受管理的身分識別的 Azure SQL Database 驗證。 指定的處理站可以使用此身分識別來存取資料庫，並從中來回複製資料。

若要使用受控身分識別驗證，請遵循下列步驟。

1. [佈建 Azure Active Directory 系統管理員](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)針對您的 Azure SQL 伺服器，如果您還在 Azure 入口網站。 Azure AD 系統管理員可以在 Azure AD 使用者或 Azure AD 群組。 如果您授與管理的身分識別系統管理員角色的群組，請略過步驟 3 和 4。 系統管理員具有完整存取權的資料庫。

2. [建立自主的資料庫使用者](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)針對 Azure Data Factory 受控身分識別。 連接到資料庫，或您要使用至少要有 Azure AD 身分識別的 SQL Server Management Studio 等工具，將資料複製 ALTER ANY USER 權限。 執行下列 T-SQL： 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. 授與 Data Factory 受控身分識別所需權限，為您平常的 SQL 使用者和其他項目。 執行下列程式碼。 如需其他選項，請參閱 <<c0> [ 這份文件](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)。

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. 設定 Azure Data Factory 中的 Azure SQL Database 連結服務。

**範例**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
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

如需完整的區段和屬性可用來定義資料集清單，請參閱 <<c0> [ 資料集](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)。 本節提供 Azure SQL Database 資料集所支援的屬性清單。

若要從或 Azure SQL Database，請複製資料，支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 資料集的 **type** 屬性必須設定為 **AzureSqlTable**。 | 是 |
| tableName | Azure SQL Database 執行個體中連結服務所參考的資料表或檢視的名稱。 | 否 (來源)；是 (接收) |

#### <a name="dataset-properties-example"></a>資料集屬性範例

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)。 本節提供 Azure SQL Database 來源和接收所支援屬性的清單。

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database 作為來源

若要從 Azure SQL Database 複製資料，設定**型別**屬性中的複製活動來源**SqlSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | **型別**複製活動來源的屬性必須設為**SqlSource**。 | 是 |
| sqlReaderQuery | 此屬性使用自訂 SQL 查詢來讀取資料。 例如 `select * from MyTable`。 | 否 |
| sqlReaderStoredProcedureName | 從來源資料表讀取資料的預存程序名稱。 最後一個 SQL 陳述式必須是預存程序中的 SELECT 陳述式。 | 否 |
| storedProcedureParameters | 預存程序的參數。<br/>允許的值為名稱或值組。 名稱和大小寫的參數必須符合的名稱和大小寫的預存程序參數。 | 否 |

**注意事項：**

- 如果**sqlReaderQuery**指定**SqlSource**，複製活動會執行此查詢針對 Azure SQL Database 來源取得的資料。 如果預存程序接受參數，您也可以藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** 來指定預存程序。
- 如果您未指定其中一個**sqlReaderQuery**或是**sqlReaderStoredProcedureName**，資料集 JSON 的"structure"區段中定義的資料行用來建構查詢。 查詢`select column1, column2 from mytable`會針對 Azure SQL Database 執行。 如果資料集定義沒有 "structure"，則會從資料表中選取所有資料行。

#### <a name="sql-query-example"></a>SQL 查詢範例

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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

#### <a name="stored-procedure-example"></a>預存程序範例

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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

### <a name="stored-procedure-definition"></a>預存程序定義

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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL Database 作為接收

> [!TIP]
> 深入了解支援的寫入行為、 組態和最佳作法[將資料載入 Azure SQL Database 的最佳做法](#best-practice-for-loading-data-into-azure-sql-database)。

若要將資料複製到 Azure SQL Database 中，設定**型別**接收器將複製活動中的屬性，這是以**SqlSink**。 複製活動的 **sink** 區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | **型別**複製活動接收器的屬性必須設定為**SqlSink**。 | 是 |
| writeBatchSize | 要插入 SQL 資料表的資料列的數目*每個批次*。<br/> 允許的值為**整數** (資料列數目)。 根據預設，Azure Data Factory 以動態方式決定適當的批次大小為基礎的資料列大小。 | 否 |
| writeBatchTimeout | 在逾時前等待批次插入作業完成的時間。<br/> 允許的值為**時間範圍**。 例如，"00: 30:00"（30 分鐘）。 | 否 |
| preCopyScript | 指定複製活動將資料寫入到 Azure SQL Database 前執行的 SQL 查詢。 每一複製回合只會叫用此查詢一次。 使用此屬性來清除預先載入的資料。 | 否 |
| sqlWriterStoredProcedureName | 定義如何將來源資料套用到目標資料表的預存程序名稱。 <br/>此預存程序將會*依批次叫用*。 對於只執行一次，而來源資料，例如，刪除或截斷的作業，使用`preCopyScript`屬性。 | 否 |
| storedProcedureParameters |預存程序的參數。<br/>允許的值為：名稱和值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 | 否 |
| sqlWriterTableType | 指定要在預存程序中使用的資料表類型名稱。 複製活動可讓您可以使用此資料表類型的暫存資料表中正在移動的資料。 然後，預存程序程式碼可以合併正在複製的資料與現有的資料。 | 否 |

**範例 1：附加資料**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>將資料載入 Azure SQL Database 的最佳作法

當您將資料複製到 Azure SQL Database 時，您可能需要不同的寫入行為：

- [附加](#append-data):我的來源資料會有新的記錄。
- [Upsert](#upsert-data):我的來源資料有插入和更新。
- [覆寫](#overwrite-the-entire-table):我想要重新載入整個維度資料表每一次。
- [使用自訂邏輯撰寫](#write-data-with-custom-logic):我需要在最後一個插入至目的地資料表之前的額外處理。

請參閱有關如何在 Azure Data Factory 和最佳作法中設定個別的區段。

### <a name="append-data"></a>附加資料

將資料附加為此 Azure SQL Database 的接收連接器的預設行為。 Azure Data Factory 會執行大量插入來有效率地寫入至您的資料表。 您可以設定來源，並據以接收將複製活動中。

### <a name="upsert-data"></a>更新插入資料

**選項 1：** 當您有大量複製，請使用下列方法來執行更新插入的資料： 

- 首先，使用[資料庫範圍暫存資料表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database)所有記錄使用複製活動的大量載入。 不記錄針對資料庫範圍暫存資料表的作業，因為您可以載入數百萬筆記錄，以秒為單位。
- 若要套用的 Azure Data Factory 中執行的預存程序活動[合併](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current)或 INSERT/UPDATE 陳述式。 為來源，以執行所有更新或插入做為單一交易，請使用暫存資料表。 如此一來，被減少往返和記錄作業的數目。 在預存程序活動結束時，就可能被截斷暫存資料表以做好下次 upsert 循環。

例如，，您也可以在 Azure Data Factory 中建立管線，其中含有**複製活動**與鏈結**預存程序活動**。 先前的資料複製從您的來源存放區到 Azure SQL Database 的暫存資料表，例如 **##UpsertTempTable**，資料集內資料表的名稱。 然後，後者會叫用來從暫存資料表的來源資料合併到目標資料表，並清除 暫存資料表的預存程序。

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

- 載入至資料庫範圍暫存資料表，並接著叫用預存程序。 
- 在複製期間，叫用預存程序。

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> 從 SQL 接收叫用預存程序

當您將資料複製到 Azure SQL Database 時，您也可以設定，並叫用使用者自訂預存程序搭配其他參數。

> [!TIP]
> 叫用預存程序以處理資料列而不是使用大量作業，我們不建議大規模的複本。 進一步了解[將資料載入 Azure SQL Database 的最佳做法](#best-practice-for-loading-data-into-azure-sql-database)。

當內建的複製機制無法滿足需求時，您可以使用預存程序。 舉例來說，當您想要套用最後一個插入的來源資料至目的地資料表之前的額外處理。 一些額外的處理範例包括當您想要合併資料行、 查閱其他值，以及插入多個資料表。

下列範例示範如何使用預存程序，對 Azure SQL Database 中的資料表執行 upsert。 假設輸入的資料，並接收**行銷**每個資料表有三個資料行：**ProfileID**、**State** 和 **Category**。 根據 **ProfileID** 資料行執行 upsert，然後僅針對特定的類別套用。

**輸出資料集：** "tableName"是在預存程序中，相同的資料表型別參數名稱，如下列的預存程序指令碼所示：

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL
)
```

預存程序功能使用[資料表值參數](https://msdn.microsoft.com/library/bb675163.aspx)。

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

了解詳細資料，從[來源轉換](data-flow-source.md)並[接收轉換](data-flow-sink.md)對應資料流程中。

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL Database 的資料類型對應

Azure SQL Database 來回複製資料時，會使用下列對應從 Azure SQL Database 資料類型到 Azure Data Factory 過渡期資料類型。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

| Azure SQL Database 資料類型 | Azure Data Factory 過渡期資料類型 |
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
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> 針對對應至 Decimal 過渡期類型的資料類型，Azure Data Factory 目前支援最多 28 個有效位數。 如果您有具有有效位數大於 28 的資料，請考慮將轉換成 SQL 查詢中的字串。

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收資料存放區的清單，請參閱 <<c0> [ 支援的資料存放區和格式](copy-activity-overview.md##supported-data-stores-and-formats)。
