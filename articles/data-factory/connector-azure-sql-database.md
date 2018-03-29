---
title: 使用 Data Factory 將資料複製到 Azure SQL Database 或從該處複製資料 | Microsoft Docs
description: 了解如何使用 Data Factory 將資料從支援的來源資料存放區複製到 Azure SQL Database，或從 SQL Database 複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: jingwang
ms.openlocfilehash: 12f673a8d3ca9c0bb03b9cd2d8c33ae866039289
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure SQL Database 或從該處複製資料
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版 - 正式推出](v1/data-factory-azure-sql-connector.md)
> * [第 2 版 - 預覽](connector-azure-sql-database.md)

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Azure SQL Database 複製資料及將資料複製到該處。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (也就是正式推出版 (GA))，請參閱 [V1 中的 Azure SQL Database 連接器](v1/data-factory-azure-sql-connector.md)。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 Azure SQL Database 複製到任何支援的接收資料存放區，或將資料從任何支援的來源資料存放區複製到 Azure SQL Database。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Azure SQL Database 連接器支援：

- 使用 **SQL 驗證**和 **Azure Active Directory 應用程式權杖驗證**與服務主體或受控服務識別 (MSI) 來複製資料。
- 作為來源時，使用 SQL 查詢或預存程序來擷取資料。
- 在複製期間作為接收器時，使用自訂邏輯將資料附加到目的地資料表或叫用預存程序。

> [!IMPORTANT]
> 如果您使用 Azure 整合執行階段複製資料，請將 [Azure SQL Server 防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)設定為[允許 Azure 服務存取伺服器](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)。 如果您使用自我裝載整合執行階段複製資料，請將 Azure SQL Server 防火牆設定為允許適當的 IP 範圍，包括用來連線到 Azure SQL Database 之機器的 IP。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Azure SQL Database 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Azure SQL Database 已連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | type 屬性必須設為： **AzureSqlDatabase** | yes |
| connectionString |針對 connectionString 屬性指定連接到 Azure SQL Database 執行個體所需的資訊。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |yes |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 搭配服務主體使用 AAD 驗證時為是。 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 搭配服務主體使用 AAD 驗證時為是。 |
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 將滑鼠游標暫留在 Azure 入口網站右上角，即可擷取它。 | 搭配服務主體使用 AAD 驗證時為是。 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或「自我裝載 Integration Runtime」(如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

針對不同的驗證類型，請分別參閱下列有關先決條件和 JSON 範例的章節：

- [使用 SQL 驗證](#using-sql-authentication)
- [使用 AAD 應用程式權杖驗證 - 服務主體](#using-service-principal-authentication)
- [使用 AAD 應用程式權杖驗證 - 受控服務識別](#using-managed-service-identity-authentication)

### <a name="using-sql-authentication"></a>使用 SQL 驗證

**使用 SQL 驗證的連結服務範例：**

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

### <a name="using-service-principal-authentication"></a>使用服務主體驗證

若要使用以服務主體為基礎的 AAD 應用程式權杖驗證，請遵循下列步驟：

1. **[從 Azure 入口網站建立 Azure Active Directory 應用程式](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)。**  請記下應用程式名稱，以及下列可供用來定義連結服務的值：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 如果您尚未這麼做，請在 Azure 入口網站上針對您的 Azure SQL Server **[佈建 Azure Active Directory 系統管理員](../sql-database/sql-database-aad-authentication-configure.md#create-an-azure-ad-administrator-for-azure-sql-server)**。 AAD 系統管理員必須是 AAD 使用者或 AAD 群組，但不得為服務主體。 此步驟必須完成，如此您才可以在後續步驟中使用 AAD 身分識別來為服務主體建立自主資料庫使用者。

3. **針對服務主體建立自主資料庫使用者**，方法是以至少具有 ALTER ANY USER 權限的 AAD 身分識別，使用 SSMS 之類的工具連線至您想要將資料複製到其中或從中複製資料的資料庫，然後執行下列 T-SQL。 從[這裡](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)深入了解自主資料庫使用者。
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 像您一般對 SQL 使用者所做的一樣，**授與服務主體所需的權限**，例如藉由執行以下動作：

    ```sql
    EXEC sp_addrolemember '[your application name]', 'readonlyuser';
    ```

5. 在 ADF 中，設定 Azure SQL Database 連結服務。


**使用服務主體驗證的連結服務範例：**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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

### <a name="using-managed-service-identity-authentication"></a>使用受控服務識別驗證

資料處理站可以與[受控服務識別 (MSI)](data-factory-service-identity.md) 相關聯，用後者來表示此特定資料處理站。 您可以針對 Azure SQL Database 驗證使用此服務識別，讓這個指定的處理站可以存取及複製資料到您的資料庫，以及從中存取、複製。

若要使用以 MSI 為基礎的 AAD 應用程式權杖驗證，請遵循下列步驟：

1. **在 Azure AD 中建立群組，並讓處理站 MSI 成為此群組的成員**。

    a. 從 Azure 入口網站尋找資料處理站服務識別。 移至您的資料處理站 -> [屬性]-> 複製 [服務識別識別碼]。

    b. 安裝 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) 模組，使用 `Connect-AzureAD` 命令登入，然後執行下列命令以建立群組，並且新增資料處理站 MSI 作為成員。
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. 如果您尚未這麼做，請在 Azure 入口網站上針對您的 Azure SQL Server **[佈建 Azure Active Directory 系統管理員](../sql-database/sql-database-aad-authentication-configure.md#create-an-azure-ad-administrator-for-azure-sql-server)**。 AAD 系統管理員可以是 AAD 使用者或 AAD 群組。 如果您將系統管理員角色授與具有 MSI 的群組，請略過下面的步驟 3 和 4，因為系統管理員具有資料庫的完整存取權。

3. **針對 AAD 群組建立自主資料庫使用者**，方法是以至少具有 ALTER ANY USER 權限的 AAD 身分識別，使用 SSMS 之類的工具連線至您想要將資料複製到其中或從中複製資料的資料庫，然後執行下列 T-SQL。 從[這裡](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)深入了解自主資料庫使用者。
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. 像您一般對 SQL 使用者所做的一樣，**授與 AAD 群組所需的權限**，例如藉由執行以下動作：

    ```sql
    EXEC sp_addrolemember '[your AAD group name]', 'readonlyuser';
    ```

5. 在 ADF 中，設定 Azure SQL Database 連結服務。

**使用 MSI 驗證的連結服務範例：**

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

如需可用來定義資料集的區段和屬性完整清單，請參閱資料集文章。 本節提供 Azure SQL Database 資料集所支援的屬性清單。

若要從 Azure SQL Database 複製資料或將資料複製到該處，請將資料集的類型屬性設定為 **AzureSqlTable**。 以下是支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為：**AzureSqlTable** | yes |
| tableName |Azure SQL Database 執行個體中連結服務所參考的資料表或檢視的名稱。 | yes |

**範例：**

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

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Azure SQL Database 來源和接收器所支援的屬性清單。

### <a name="azure-sql-database-as-source"></a>Azure SQL Database 作為來源

若要從 Azure SQL Database 複製資料，請將複製活動中的來源類型設定為 **SqlSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為：**SqlSource** | yes |
| SqlReaderQuery |使用自訂 SQL 查詢來讀取資料。 範例： `select * from MyTable`. |否 |
| sqlReaderStoredProcedureName |從來源資料表讀取資料的預存程序名稱。 最後一個 SQL 陳述式必須是預存程序中的 SELECT 陳述式。 |否 |
| storedProcedureParameters |預存程序的參數。<br/>允許的值為：名稱/值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 |否 |

**注意事項：**

- 如果已為 SqlSource 指定 **sqlReaderQuery** ，複製活動會針對 Azure SQL Database 來源執行這項查詢以取得資料。 或者，您可以藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** (如果預存程序接受參數) 來指定預存程序。
- 如果您未指定 "sqlReaderQuery" 或 "sqlReaderStoredProcedureName"，就會使用資料集 JSON 的 "structure" 區段中定義的資料行，來建構要針對 Azure SQL Database 執行的查詢 (`select column1, column2 from mytable`)。 如果資料集定義沒有 "structure"，則會從資料表中選取所有資料行。
- 當您使用 **sqlReaderStoredProcedureName** 時，仍然必須在資料集 JSON 中指定一個虛設的 **tableName**。

**範例：使用 SQL 查詢**

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

**範例：使用預存程序**

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

### <a name="azure-sql-database-as-sink"></a>Azure SQL Database 作為接收器

若要將資料複製到 Azure SQL Database，請將複製活動中的接收器類型設定為 **SqlSink**。 複製活動的 **sink** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器的 type 屬性必須設定為：**SqlSink** | yes |
| writeBatchSize |當緩衝區大小達到 writeBatchSize 時，將資料插入 SQL 資料表中<br/>允許的值為：整數 (資料列數目)。 |否 (預設值為 10000) |
| writeBatchTimeout |在逾時前等待批次插入作業完成的時間。<br/>允許的值為：時間範圍。 範例：“00:30:00” (30 分鐘)。 |否 |
| preCopyScript |指定一個 SQL 查詢，供「複製活動」在將資料寫入 Azure SQL Database 前執行。 每一複製回合只會叫用此查詢一次。 您可以使用此屬性來清除預先載入的資料。 |否 |
| sqlWriterStoredProcedureName |預存程序的名稱，此預存程序定義如何將來源資料套用至目標資料表，例如使用您自己的商務邏輯來執行更新插入或轉換。 <br/><br/>請注意，將會**依批次叫用**此預存程序。 如果您想要進行只執行一次且與來源資料無關的作業 (例如刪除/截斷)，請使用 `preCopyScript` 屬性。 |否 |
| storedProcedureParameters |預存程序的參數。<br/>允許的值為：名稱/值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 |否 |
| sqlWriterTableType |指定要在預存程序中使用的資料表類型名稱。 複製活動可讓正在移動的資料可用於此資料表類型的暫存資料表。 然後，預存程序程式碼可以合併正在複製的資料與現有的資料。 |否 |

> [!TIP]
> 將資料複製到 Azure SQL Database 時，複製活動預設會將資料附加至接收資料表。 若要執行 UPSERT 或其他商務邏輯，請在 SqlSink 中使用該預存程序。 若要了解更多詳細資料，請參閱[叫用 SQL 接收器的預存程序](#invoking-stored-procedure-for-sql-sink)。

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

**範例 2：在複製期間叫用預存程序來進行更新插入**

若要了解更多詳細資料，請參閱[叫用 SQL 接收器的預存程序](#invoking-stored-procedure-for-sql-sink)。

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

本節提供的範例將示範，如何把資料從沒有身分識別資料行的來源資料表，複製到具有身分識別資料行的目的地資料表。

**來源資料表：**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**目的地資料表：**

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

**目的地資料集 JSON 定義**

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

請注意，您的來源資料表與目標資料表的結構描述不同 (目標資料表有一個具有身分識別的額外資料行)。 在此案例中，您必須在目標資料集定義中指定 **structure** 屬性，這不包含身分識別資料行。

## <a name="invoke-stored-procedure-from-sql-sink"></a>從 SQL 接收器叫用預存程序

將資料複製到 Azure SQL Database 時，可以設定使用者指定的預存程序，並搭配其他參數來叫用此程序。

當內建的複製機制無法滿足需求時，可以使用預存程序。 通常是在最後一次將來源資料插入到目的地資料表之前，如果必須執行更新插入 (插入 + 更新) 或額外的處理 (合併資料行、查閱其他值、插入到多個資料表等) 時，會使用此程序。

下列範例示範如何使用預存程序，對 Azure SQL Database 中的資料表執行更新插入。 假設輸入資料和接收器 "Marketing" 資料表各有三個資料行：ProfileID、State 及 Category。 根據 “ProfileID” 資料行執行更新插入，然後僅針對特定的類別套用。

**輸出資料集**

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

依下列方式定義複製活動中的 "SqlSink" 區段。

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

在資料庫中，使用與 "SqlWriterStoredProcedureName" 相同的名稱來定義預存程序。 它會處理來自指定來源的輸入資料，並合併至輸出資料表。 請注意，預存程序的參數名稱應該與資料集中定義的 "tableName" 相同。

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

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL Database 的資料類型對應

從 Azure SQL Database 複製資料或將資料複製到該處時，會使用下列從 Azure SQL Database 資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收器。

| Azure SQL Database 資料類型 | Data Factory 過渡期資料類型 |
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
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String、Char[] |
| xml |xml |

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。