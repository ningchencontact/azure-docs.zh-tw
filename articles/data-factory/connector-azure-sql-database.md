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
ms.date: 04/16/2019
ms.author: jingwang
ms.openlocfilehash: 749b5690f5814bb2f63f9f4451bba85990166acd
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683863"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure SQL Database 或從該處複製資料
> [!div class="op_single_selector" title1="Select the version of Data Factory service you use:"]
> * [第 1 版](v1/data-factory-azure-sql-connector.md)
> * [目前的版本](connector-azure-sql-database.md)

本文說明如何使用 Azure Data Factory 中的「複製活動」，從 Azure SQL Database 來回複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 Azure SQL Database 來回複製到任何支援的接收資料存放區。 您也可以將資料從任何支援的來源資料存放區複製到 Azure SQL Database。 如需複製活動所支援作為來源或接收的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Azure SQL Database 連接器支援下列功能：

- 使用 SQL 驗證和 Azure Active Directory (Azure AD) 應用程式權杖驗證搭配服務主體或 Azure 資源的受控識別來複製資料。
- 作為來源時，使用 SQL 查詢或預存程序來擷取資料。
- 在複製期間作為接收時，請使用自訂邏輯將資料附加到目的地資料表或叫用預存程序。

目前不支援 Azure SQL Database [一律加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)。 

> [!IMPORTANT]
> 如果您使用 Azure Data Factory Integration Runtime 複製資料，請設定 [Azure SQL 伺服器防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)，讓 Azure 服務可存取伺服器。
> 如果您使用自我裝載整合執行階段來複製資料，請將 Azure SQL 伺服器防火牆設定為允許適當的 IP 範圍。 此範圍包括用來連線到 Azure SQL Database 的機器 IP。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Azure SQL Database 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是支援 Azure SQL Database 已連結服務的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | **type** 屬性必須設為 **AzureSqlDatabase**。 | 是 |
| connectionString | 針對 **connectionString** 屬性指定連線到 Azure SQL Database 執行個體所需的資訊。 <br/>將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中。 您也可以將密碼/服務主體金鑰放在 Azure Key Vault 中，而且，如果這是 SQL 驗證，則會從連接字串中提取 `password` 組態。 請參閱表格下方的 JSON 範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文深入了解詳細資料。 | 是 |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 當您搭配服務主體使用 Azure AD 驗證時為是。 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為 **SecureString**，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 當您搭配服務主體使用 Azure AD 驗證時為是。 |
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 將滑鼠游標暫留在 Azure 入口網站右上角，即可擷取它。 | 當您搭配服務主體使用 Azure AD 驗證時為是。 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載整合執行階段 (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 | 否 |

針對不同的驗證類型，請分別參閱下列有關先決條件和 JSON 範例的章節：

- [SQL 驗證](#sql-authentication)
- [Azure AD 應用程式權杖驗證：服務主體](#service-principal-authentication)
- [Azure AD 應用程式權杖驗證：適用於 Azure 資源的受控識別](#managed-identity)

>[!TIP]
>如果您遇到錯誤，其錯誤碼為 "UserErrorFailedToConnectToSqlServer"，以及「資料庫的工作階段限制為 XXX 並已達到。」訊息，請將 `Pooling=false` 新增至您的連接字串並再試一次。

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

**Azure Key Vault 中的密碼：** 

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

1. **[從 Azure 入口網站建立 Azure Active Directory 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**。 請記下應用程式名稱，以及下列可定義連結服務的值：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 如果您尚未這麼做，請在 Azure 入口網站上針對您的 Azure SQL 伺服器**[佈建 Azure Active Directory 系統管理員](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**。 Azure AD 系統管理員必須是 Azure AD 使用者或 Azure AD 群組，但不能是服務主體。 此步驟必須完成，如此您才可以在下一個步驟中使用 Azure AD 身分識別，為服務主體建立自主資料庫使用者。

3. 為服務主體**[建立自主資料庫使用者](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**。 以至少具有 ALTER ANY USER 權限的 Azure AD 身分識別，使用 SSMS 這類工具連線至您想要從中來回複製資料的資料庫。 執行下列 T-SQL： 
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 如同您一般對 SQL 使用者或其他人所做的一樣，**將所需的權限授與服務主體**。 執行下列程式碼，或更多的選項是指[此處](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)。

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. 在 Azure Data Factory 中，**設定 Azure SQL Database 連結服務**。


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

若要使用受控身分識別驗證，請遵循下列步驟：

1. 如果您尚未這麼做，請在 Azure 入口網站上針對您的 Azure SQL 伺服器**[佈建 Azure Active Directory 系統管理員](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**。 Azure AD 系統管理員可以是 Azure AD 使用者或 Azure AD 群組。 如果您授與管理的身分識別系統管理員角色的群組，請略過步驟 3 和 4。 系統管理員將擁有完整的資料庫存取權。

2. **[建立自主的資料庫使用者](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** 的 Data Factory 受控身分識別。 以至少具有 ALTER ANY USER 權限的 Azure AD 身分識別，使用 SSMS 這類工具連線至您想要從中來回複製資料的資料庫。 執行下列 T-SQL： 
    
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **授與 Data Factory 受控身分識別所需的權限**像您一般的 SQL 使用者和其他項目。 執行下列程式碼，或更多的選項是指[此處](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)。

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. 在 Azure Data Factory 中，**設定 Azure SQL Database 連結服務**。

**範例：**

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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)一文。 本節提供 Azure SQL Database 資料集所支援的屬性清單。

若要從 Azure SQL Database 來回複製資料，請將資料集的 **type** 屬性設定為 **AzureSqlTable**。 以下是支援的屬性：

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
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Azure SQL Database 來源和接收所支援屬性的清單。

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database 作為來源

若要從 Azure SQL Database 複製資料，請將複製活動來源中的 **type** 屬性設定為 **SqlSource**。 複製活動的 [來源] 區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動來源的 **type**屬性必須設定為 **SqlSource**。 | 是 |
| SqlReaderQuery | 使用自訂 SQL 查詢來讀取資料。 範例： `select * from MyTable`. | 否 |
| sqlReaderStoredProcedureName | 從來源資料表讀取資料的預存程序名稱。 最後一個 SQL 陳述式必須是預存程序中的 SELECT 陳述式。 | 否 |
| storedProcedureParameters | 預存程序的參數。<br/>允許的值為名稱或值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 | 否 |

### <a name="points-to-note"></a>注意事項

- 如果已為 **SqlSource** 指定 **sqlReaderQuery**，複製活動會針對 Azure SQL Database 來源執行這項查詢以取得資料。 您也可以指定預存程序。 指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** (如果預存程序接受參數)。
- 如果您未指定 **sqlReaderQuery** 或 **sqlReaderStoredProcedureName**，就會使用資料集 JSON **structure** 區段中定義的資料行來建構查詢。 `select column1, column2 from mytable` 會針對 Azure SQL Database 執行。 如果資料集定義沒有 **structure**，則會從資料表中選取所有資料行。

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

若要將資料複製到 Azure SQL Database，請將複製活動接收中的 **type** 屬性設定為 **SqlSink**。 複製活動的 [接收] 區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動接收的 **type** 屬性必須設定為：**SqlSink**。 | 是 |
| writeBatchSize | 要插入至 SQL 資料表的資料列的數目**每個批次**。<br/> 允許的值為**整數** (資料列數目)。 | 沒有。 預設值為 10000。 |
| writeBatchTimeout | 在逾時前等待批次插入作業完成的時間。<br/> 允許的值為**時間範圍**。 範例：“00:30:00” (30 分鐘)。 | 否 |
| preCopyScript | 指定一個 SQL 查詢，供「複製活動」在將資料寫入 Azure SQL Database 之前執行。 每一複製回合只會叫用此查詢一次。 使用此屬性來清除預先載入的資料。 | 否 |
| sqlWriterStoredProcedureName | 定義如何將來源資料套用到目標資料表的預存程序名稱。 例如，使用您自己的商務邏輯來進行 upsert 或轉換。 <br/><br/>此預存程序將會**依批次叫用**。 針對僅執行一次、且與來源資料無關的作業，請使用 `preCopyScript` 屬性。 範例作業為刪除和截斷。 | 否 |
| storedProcedureParameters |預存程序的參數。<br/>允許的值為：名稱和值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 | 否 |
| sqlWriterTableType | 指定要在預存程序中使用的資料表類型名稱。 複製活動會讓正在移動的資料可用於此資料表類型的暫存資料表。 然後，預存程序程式碼可以合併正在複製的資料與現有的資料。 | 否 |

> [!TIP]
> 將資料複製到 Azure SQL Database 時，複製活動預設會將資料附加至接收資料表。 若要執行 upsert 或其他商務邏輯，請在 **SqlSink** 中使用該預存程序。 若要了解更多詳細資料，請參閱[叫用 SQL 接收中的預存程序](#invoking-stored-procedure-for-sql-sink)。

#### <a name="append-data-example"></a>附加資料範例

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

#### <a name="invoke-a-stored-procedure-during-copy-for-upsert-example"></a>在複製期間叫用預存程序來進行 upsert 的範例

若要了解更多詳細資料，請參閱[叫用 SQL 接收中的預存程序](#invoking-stored-procedure-for-sql-sink)。

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

## <a name="identity-columns-in-the-target-database"></a>目標資料庫中的身分識別資料行

本節將示範如何將資料從沒有識別欄位的來源資料表，複製到具有識別欄位的目的地資料表。

#### <a name="source-table"></a>來源資料表

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

#### <a name="destination-table"></a>目的地資料表

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

> [!NOTE]
> 目標資料表具有識別欄位。

#### <a name="source-dataset-json-definition"></a>來源資料集 JSON 定義

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " AzureSqlTable",
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

#### <a name="destination-dataset-json-definition"></a>目的地資料集 JSON 定義

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
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

> [!NOTE]
> 您的來源和目標資料表有不同的結構描述。 

目標的其他資料行具有身分識別。 在此案例中，您必須在目標資料集定義中指定 **structure** 屬性，這不包含識別欄位。

## <a name="invoking-stored-procedure-for-sql-sink"></a> 從 SQL 接收器叫用預存程序

將資料複製到 Azure SQL Database 時，您也可以使用其他參數來設定及叫用使用者指定的預存程序。

當內建的複製機制無法滿足需求時，您可以使用預存程序。 在最後將來源資料插入目的地資料表之前，必須完成 upsert、插入並更新或額外處理時，通常會使用它們。 一些額外處理的範例包括合併資料行、查閱其他的值，以及插入多個資料表中。

下列範例示範如何使用預存程序，對 Azure SQL Database 中的資料表執行 upsert。 假設輸入資料和接收器 **Marketing** 資料表各有三個資料行：**ProfileID**、**State** 和 **Category**。 根據 **ProfileID** 資料行執行 upsert，然後僅針對特定的類別套用。

**輸出資料集：** "tableName"應該是您的預存程序 （請參閱下列預存程序的指令碼） 相同的資料表型別參數名稱。

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

在資料庫中，使用與 **sqlWriterTableType** 相同的名稱來定義資料表類型。 資料表類型的結構描述應該與輸入資料所傳回的結構描述相同。

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL
)
```

預存程序功能使用 [資料表值參數](https://msdn.microsoft.com/library/bb675163.aspx)。

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL Database 的資料類型對應

從 Azure SQL Database 複製資料或將資料複製到該處時，會使用下列從 Azure SQL Database 資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收。

| Azure SQL Database 資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |BOOLEAN |
| char |String、Char[] |
| 日期 |DateTime |
| datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM 屬性 (varbinary(max)) |Byte[] |
| Float |Double |
| 映像 |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String、Char[] |
| ntext |String、Char[] |
| numeric |Decimal |
| nvarchar |String、Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String、Char[] |
| 分析 |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String、Char[] |
| xml |xml |

>[!NOTE]
> 針對對應至 Decimal 過渡期類型的資料類型，目前 ADF 支援的有效位數最多可達 28。 如果您有有效位數超過 28 的資料，請考慮在 SQL 查詢中將其轉換成字串。

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收端的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md##supported-data-stores-and-formats)。
