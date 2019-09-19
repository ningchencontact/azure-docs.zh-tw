---
title: 使用 Azure Data Factory 從 Azure SQL 資料倉儲來回複製資料 | Microsoft Docs
description: 了解如何使用 Data Factory 將資料從支援的來源存放區複製到「Azure SQL 資料倉儲」，或從「SQL 資料倉儲」複製到支援的接收存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: jingwang
ms.openlocfilehash: 78b74c1db5f331e7b74a730148d52b1ff7694ec0
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71058988"
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure SQL 資料倉儲或從該處複製資料 
> [!div class="op_single_selector" title1="選取您要使用的 Data Factory 服務版本："]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [目前的版本](connector-azure-sql-data-warehouse.md)

本文概述如何在 Azure SQL 資料倉儲之間複製資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Azure SQL 資料倉儲連接器：

- [複製活動](copy-activity-overview.md)與[支援的來源/接收矩陣](copy-activity-overview.md)資料表
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)

具體而言，這個「Azure SQL 資料倉儲」連接器支援下列功能：

- 使用 SQL 驗證和 Azure Active Directory (Azure AD) 應用程式權杖驗證搭配服務主體或 Azure 資源的受控識別來複製資料。
- 作為來源時，使用 SQL 查詢或預存程序來擷取資料。
- 做為接收時，使用 PolyBase 或大量插入來載入資料。 建議您使用 PolyBase 以獲得較佳的複製效能。

> [!IMPORTANT]
> 如果您使用Azure Data Factory Integration Runtime 複製資料，請設定 [Azure SQL 伺服器防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)，讓 Azure 服務可存取伺服器。
> 如果您使用自我裝載整合執行階段來複製資料，請將 Azure SQL 伺服器防火牆設定為允許適當的 IP 範圍。 此範圍包括機器的 IP，用來連線到 Azure SQL Database。

## <a name="get-started"></a>開始使用

> [!TIP]
> 若要達到最佳效能，請使用 PolyBase 將資料載入 Azure SQL 資料倉儲。 如需詳細資訊，請參閱 [使用 PolyBase 將資料載入 Azure SQL 資料倉儲](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 。 如需使用案例的逐步解說，請參閱[使用 Azure Data Factory 在 15 分鐘內將 1 TB 載入至 Azure SQL 資料倉儲](load-azure-sql-data-warehouse.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性會定義「Azure SQL 資料倉儲」連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是 Azure SQL 資料倉儲連結服務支援的屬性：

| 屬性            | 描述                                                  | 必要項                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 型別                | 類型屬性必須設為 **AzureSqlDW**。             | 是                                                          |
| connectionString    | 針對 **connectionString** 屬性指定連線到 Azure SQL 資料倉儲執行個體所需的資訊。 <br/>將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中。 您也可以將密碼/服務主體金鑰放在 Azure Key Vault 中，而且，如果這是 SQL 驗證，則會從連接字串中提取 `password` 組態。 請參閱表格下方的 JSON 範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文深入了解詳細資料。 | 是                                                          |
| servicePrincipalId  | 指定應用程式的用戶端識別碼。                         | 當您搭配服務主體使用 Azure AD 驗證時為是。 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 當您搭配服務主體使用 Azure AD 驗證時為是。 |
| 租用戶              | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 將滑鼠游標暫留在 Azure 入口網站右上角，即可擷取它。 | 當您搭配服務主體使用 Azure AD 驗證時為是。 |
| connectVia          | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載整合執行階段 (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 | 否                                                           |

針對不同的驗證類型，請分別參閱下列有關先決條件和 JSON 範例的章節：

- [SQL 驗證](#sql-authentication)
- Azure AD 應用程式權杖驗證：[服務主體](#service-principal-authentication)
- Azure AD 應用程式權杖驗證：[適用於 Azure 資源的受控識別](#managed-identity)

>[!TIP]
>如果您遇到錯誤，其錯誤碼為 "UserErrorFailedToConnectToSqlServer"，以及「資料庫的工作階段限制為 XXX 並已達到。」訊息，請將 `Pooling=false` 新增至您的連接字串並再試一次。

### <a name="sql-authentication"></a>SQL 驗證

#### <a name="linked-service-example-that-uses-sql-authentication"></a>使用 SQL 驗證的連結服務範例

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

1. 從 Azure 入口網站 **[建立 Azure Active Directory 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** 。 請記下應用程式名稱，以及下列可定義連結服務的值：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 如果您尚未這麼做，請在 Azure 入口網站上針對您的 Azure SQL 伺服器 **[佈建 Azure Active Directory 系統管理員](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** 。 Azure AD 系統管理員可以是 Azure AD 使用者或 Azure AD 群組。 如果您將系統管理員角色授與受控識別給群組，請略過步驟3和4。 系統管理員將擁有完整的資料庫存取權。

3. 為服務主體 **[建立自主資料庫使用者](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** 。 以至少具有 ALTER ANY USER 權限的 AAD 身分識別，使用 SSMS 這類工具連線至您想要從中來回複製資料的資料倉儲。 執行下列 T-SQL：
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 如同您一般對 SQL 使用者或其他人所做的一樣，**將所需的權限授與服務主體**。 執行下列程式碼，或參閱[這裡](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)的更多選項。 如果您想要使用 PolyBase 來載入資料，請瞭解[必要的資料庫許可權](#required-database-permission)。

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. 在 Azure Data Factory 中，**設定 Azure SQL 資料倉儲連結服務**。


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>使用服務主體驗證的連結服務範例

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

資料處理站可與 [Azure 資源的受控識別](data-factory-service-identity.md)相關聯，後者表示特定的處理站。 您可以使用此受控識別來進行 Azure SQL 資料倉儲驗證。 指定的處理站可以使用此身分識別來存取資料倉儲和從中來回複製資料。

若要使用受控識別驗證，請遵循下列步驟：

1. 如果您尚未這麼做，請在 Azure 入口網站上針對您的 Azure SQL 伺服器 **[佈建 Azure Active Directory 系統管理員](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** 。 Azure AD 系統管理員可以是 Azure AD 使用者或 Azure AD 群組。 如果您將系統管理員角色授與受控識別給群組，請略過步驟3和4。 系統管理員將擁有完整的資料庫存取權。

2. 為 Data Factory 受控識別 **[建立自主資料庫使用者](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** 。 以至少具有 ALTER ANY USER 權限的 AAD 身分識別，使用 SSMS 這類工具連線至您想要從中來回複製資料的資料倉儲。 執行下列 T-SQL。 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **授與 Data Factory 受控識別所需的許可權**，就像您一般對 SQL 使用者和其他人所做的一樣。 執行下列程式碼，或參閱[這裡](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)的更多選項。 如果您想要使用 PolyBase 來載入資料，請瞭解[必要的資料庫許可權](#required-database-permission)。

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. 在 Azure Data Factory 中，**設定 Azure SQL 資料倉儲連結服務**。

**範例:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供「Azure SQL 資料倉儲」資料集所支援的屬性清單。

若要將資料從或複製到 Azure SQL 資料倉儲，支援下列屬性：

| 屬性  | 描述                                                  | 必要項                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| 型別      | 資料集的**type**屬性必須設定為 **AzureSqlDWTable**。 | 是                         |
| schema | 架構的名稱。 |否 (來源)；是 (接收)  |
| table | 資料表/視圖的名稱。 |否 (來源)；是 (接收)  |
| tableName | 具有架構的資料表/視圖名稱。 此屬性支援回溯相容性。 針對新的工作負載`schema` ， `table`請使用和。 | 否 (來源)；是 (接收) |

#### <a name="dataset-properties-example"></a>資料集屬性範例

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[Pipelines](concepts-pipelines-activities.md)一文。 本節提供「Azure SQL 資料倉儲」來源和接收所支援的屬性清單。

### <a name="azure-sql-data-warehouse-as-the-source"></a>Azure SQL 資料倉儲作為來源

若要從「Azure SQL 資料倉儲」複製資料，請將複製活動來源中的**類型**屬性設定為 **SqlDWSource**。 複製活動的 [來源] 區段支援下列屬性：

| 屬性                     | 描述                                                  | 必要項 |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| 型別                         | 複製活動來源的**類型**屬性必須設定為 **SqlDWSource**。 | 是      |
| sqlReaderQuery               | 使用自訂 SQL 查詢來讀取資料。 範例： `select * from MyTable`. | 否       |
| sqlReaderStoredProcedureName | 從來源資料表讀取資料的預存程序名稱。 最後一個 SQL 陳述式必須是預存程序中的 SELECT 陳述式。 | 否       |
| storedProcedureParameters    | 預存程序的參數。<br/>允許的值為名稱或值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 | 否       |

### <a name="points-to-note"></a>注意事項

- 如果已為 **SqlSource** 指定 **sqlReaderQuery**，「複製活動」就會針對「Azure SQL 資料倉儲」來源執行這項查詢以取得資料。 或者，您可以指定預存程序。 指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** (如果預存程序接受參數)。
- 如果您未指定 **sqlReaderQuery** 或 **sqlReaderStoredProcedureName**，就會使用資料集 JSON **structure** 區段中定義的資料行來建構查詢。 `select column1, column2 from mytable` 會針對 Azure SQL 資料倉儲執行。 如果資料集定義沒有 **structure**，則會從資料表中選取所有資料行。

#### <a name="sql-query-example"></a>SQL 查詢範例

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure SQL 資料倉儲作為接收端

若要將資料複製到「Azure SQL 資料倉儲」，請將複製活動中的接收類型設定為 **SqlDWSink**。 複製活動的 [接收] 區段支援下列屬性：

| 屬性          | 描述                                                  | 必要項                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| 型別              | 複製活動接收端的**type**屬性必須設定為 **SqlDWSink**。 | 是                                           |
| allowPolyBase     | 指出是否使用 PolyBase (適用的話) 而不是使用 BULKINSERT 機制。 <br/><br/> 建議使用 PolyBase 將資料載入 SQL 資料倉儲。 請參閱[使用 PolyBase 將資料載入 Azure SQL 資料倉儲](#use-polybase-to-load-data-into-azure-sql-data-warehouse)一節中的條件約束和詳細資料。<br/><br/>允許的值為 **True** 和 **False** (預設值)。 | 否                                            |
| polyBaseSettings  | 可以在 **allowPolybase** 屬性設定為 **true** 時指定的一組屬性。 | 否                                            |
| rejectValue       | 指定在查詢失敗前可以拒絕的資料列數目或百分比。<br/><br/>在 [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx)的＜引數＞一節中，深入了解 PolyBase 的拒絕選項。 <br/><br/>允許的值為 0 (預設值)、1、2 等其他值。 | 否                                            |
| rejectType        | 指定 **rejectValue** 選項為常值或百分比。<br/><br/>允許的值為**值** (預設值) 和**百分比**。 | 否                                            |
| rejectSampleValue | 決定在 PolyBase 重新計算已拒絕的資料列百分比之前，所要擷取的資料列數目。<br/><br/>允許的值為 1、2 等其他值。 | 是，如果 **rejectType** 是**百分比**。 |
| useTypeDefault    | 指定當 PolyBase 從文字檔擷取資料時，如何處理分隔符號文字檔中的遺漏值。<br/><br/>從 [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx) 的＜引數＞一節深入了解這個屬性。<br/><br/>允許的值為 **True** 和 **False** (預設值)。<br><br> | 否                                            |
| writeBatchSize    | 要插入 SQL 資料表中**每個批次**的資料列數目。 只有在未使用 PolyBase 時才適用。<br/><br/>允許的值為**整數** (資料列數目)。 根據預設，Data Factory 會依據資料列大小，以動態方式決定適當的批次大小。 | 否                                            |
| writeBatchTimeout | 在逾時前等待批次插入作業完成的時間。只有在未使用 PolyBase 時才適用。<br/><br/>允許的值為**時間範圍**。 範例：“00:30:00” (30 分鐘)。 | 否                                            |
| preCopyScript     | 指定一個供「複製活動」在每次執行時將資料寫入到「Azure SQL 資料倉儲」前執行的 SQL 查詢。 使用此屬性來清除預先載入的資料。 | 否                                            |
| tableOption | 指定是否要根據來源架構，自動建立接收資料表（如果不存在）。 在複製活動中設定分段複製時，不支援自動建立資料表。 允許的值為`none` ：（預設值`autoCreate`）、。 |否 |
| disableMetricsCollection | Data Factory 會收集計量，例如複製效能優化和建議的 SQL 資料倉儲 Dwu。 如果您擔心此行為，請指定`true`將它關閉。 | 否 (預設值為 `false`) |

#### <a name="sql-data-warehouse-sink-example"></a>SQL 資料倉儲接收範例

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

若要深入了解如何使用 PolyBase 來有效率地載入「SQL 資料倉儲」，請參閱下一節。

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>使用 PolyBase 將資料載入 Azure SQL 資料倉儲

使用 [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) 是以高輸送量將大量資料載入 Azure SQL 資料倉儲的有效方法。 使用 PolyBase 而不是預設的 BULKINSERT 機制，將可看到輸送量大幅提升。 如需使用案例的逐步解說，請參閱[將 1 TB 載入至 Azure SQL 資料倉儲](v1/data-factory-load-sql-data-warehouse.md)。

* 如果您的來源資料是在**Azure Blob、Azure Data Lake Storage Gen1 或 Azure Data Lake Storage Gen2**中，且**格式與 PolyBase 相容**，您可以使用複製活動直接叫用 polybase，讓 Azure SQL 資料倉儲從來源提取資料。 如需詳細資料，請參閱 **[使用 PolyBase 直接複製](#direct-copy-by-using-polybase)** 。
* 如果您的來源資料存放區與格式不受 PolyBase 支援，您可以改用 **[使用 PolyBase 分段複製](#staged-copy-by-using-polybase)** 功能。 分段複製功能也能提供更好的輸送量。 它會自動將資料轉換成與 PolyBase 相容的格式。 並會將資料儲存在 Azure Blob 儲存體中。 然後，它會將資料載入 SQL 資料倉儲。

>[!TIP]
>深入瞭解[使用 PolyBase 的最佳做法](#best-practices-for-using-polybase)。

### <a name="direct-copy-by-using-polybase"></a>使用 PolyBase 直接複製

SQL 資料倉儲 PolyBase 直接支援 Azure Blob、Azure Data Lake Storage Gen1 和 Azure Data Lake Storage Gen2。 如果您的來源資料符合本節所述的準則，請使用 PolyBase 直接從來源資料存放區複製到 Azure SQL 資料倉儲。 否則，請利用[使用 PolyBase 分段複製](#staged-copy-by-using-polybase)。

> [!TIP]
> 若要有效率地將資料複製到 SQL 資料倉儲，請從 Azure Data Factory 深入瞭解，[讓您在搭配 SQL 資料倉儲使用 Data Lake Store 時，更輕鬆且方便地從資料中發掘見解](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)。

如果不符合需求，Azure Data Factory 會檢查設定，並自動切換回適用於資料移動的 BULKINSERT 機制。

1. **來源連結服務**的類型和驗證方法如下：

    | 支援的來源資料存放區類型                             | 支援的來源驗證類型                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | 帳戶金鑰驗證，受控識別驗證 |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | 服務主體驗證                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | 帳戶金鑰驗證，受控識別驗證 |

    >[!IMPORTANT]
    >如果您的 Azure 儲存體設定了 VNet 服務端點，則必須使用受控識別驗證-請參閱[使用 Vnet 服務端點搭配 Azure 儲存體的影響](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)。 請分別從[Azure Blob 管理的身分識別驗證](connector-azure-blob-storage.md#managed-identity)和[Azure Data Lake Storage Gen2 受控識別驗證](connector-azure-data-lake-storage.md#managed-identity)一節中，瞭解 Data Factory 中的必要設定。

2. **源資料格式**為**Parquet**、 **ORC**或分隔的**文字**，具有下列設定：

   1. 資料夾路徑不包含萬用字元篩選準則。
   2. 檔案名是空的，或指向單一檔案。 如果您在複製活動中指定萬用字元檔案名，它只能是`*`或。 `*.*`
   3. `rowDelimiter`是**預設值**、 **\n**、 **\r\n**或 **\r**。
   4. `nullValue`會保留為預設值或設為**空字串**（""）， `treatEmptyAsNull`而且會保留為預設值或設為 true。
   5. `encodingName`會保留為預設值或設定為**utf-8**。
   6. `quoteChar`未`escapeChar`指定、 `skipLineCount`和。 PolyBase 支援略過標頭資料列，可以在`firstRowAsHeader` ADF 中設定為。
   7. `compression` 可以是「無壓縮」、**GZip** 或 **Deflate**。

3. 如果您的來源是資料夾， `recursive`則複製活動中的必須設定為 true。

>[!NOTE]
>如果您的來源是資料夾，請注意 PolyBase 會從資料夾和其所有子資料夾抓取檔案，而且不會從檔案名開頭為底線（_）或句號（.）的檔案中抓取資料，如這裡所記載[的位置引數](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2)。

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSetting",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>使用 PolyBase 分段複製

當來源資料不符合上一節中的準則時，請啟用透過過渡暫存 Azure Blob 儲存體執行個體複製資料。 這不可以是 Azure 進階儲存體。 在此情況下，Azure Data Factory 會自動執行資料轉換，以符合 PolyBase 的資料格式需求。 然後，它會使用 PolyBase 將資料載入 SQL 資料倉儲。 最後，它會清除 Blob 儲存體中的暫存資料。 如需透過暫存 Azure Blob 儲存體執行個體複製資料的詳細資訊，請參閱[分段複製](copy-activity-performance.md#staged-copy)。

若要使用這項功能，請建立一個[Azure Blob 儲存體連結服務](connector-azure-blob-storage.md#linked-service-properties)，其參考具有過渡 Blob 儲存體的 Azure 儲存體帳戶。 然後指定複製`enableStaging`活動`stagingSettings`的和屬性，如下列程式碼所示。

>[!IMPORTANT]
>如果您的預備 Azure 儲存體已設定 VNet 服務端點，您必須使用受控識別驗證-請參閱[使用 Vnet 服務端點搭配 Azure 儲存體的影響](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)。 瞭解[Azure Blob 受控識別驗證](connector-azure-blob-storage.md#managed-identity)Data Factory 中的必要設定。

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

## <a name="best-practices-for-using-polybase"></a>使用 PolyBase 的最佳做法

除了＜[Azure SQL 資料倉儲的最佳做法](../sql-data-warehouse/sql-data-warehouse-best-practices.md)＞中所述的方法外，下列章節還提供其他最佳做法。

### <a name="required-database-permission"></a>必要的資料庫權限

若要使用 PolyBase，將資料載入 SQL 資料倉儲的使用者必須具備目標資料庫的 ["CONTROL" 權限](https://msdn.microsoft.com/library/ms191291.aspx)。 達到此目標的其中一個方法是將該使用者新增為 **db_owner** 角色的成員。 在 [SQL 資料倉儲概觀](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)中了解如何執行此作業。

### <a name="row-size-and-data-type-limits"></a>資料列大小和資料類型限制

PolyBase 負載的限制為小於 1 MB 的資料列。 它不能用來載入至 VARCHR （MAX）、NVARCHAR （MAX）或 VARBINARY （MAX）。 如需詳細資訊，請參閱 [SQL 資料倉儲服務容量限制](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)。

當您來源資料中的資料列大於 1 MB 時，您可能要將來源資料表垂直分割成幾個小的資料表。 務必確認每一列的大小不會超過限制。 然後可以使用 PolyBase 載入較小的資料表，並且在 Azure SQL 資料倉儲中將其合併在一起。

或者，對於具有這類寬資料行的資料，您可以藉由關閉 [允許 PolyBase] 設定，使用非 PolyBase 來載入資料（使用 ADF）。

### <a name="sql-data-warehouse-resource-class"></a>SQL 資料倉儲資源類別

若要達到最佳的可能輸送量，請透過 PolyBase 將較大型資源類別指派給要將資料載入 SQL 資料倉儲的使用者。

### <a name="polybase-troubleshooting"></a>PolyBase 疑難排解

**載入至 Decimal 資料行**

如果您的來源資料為文字格式或其他非 PolyBase 相容存放區（使用分段複製和 PolyBase），而且它包含要載入 SQL 資料倉儲 Decimal 資料行中的空值，您可能會遇到下列錯誤：

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

解決方法是在複製活動接收中取消選取 [**使用類型預設值**] 選項（如 false）-> PolyBase 設定。 "[ USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" 是 polybase 原生設定，指定當 PolyBase 從文字檔抓取資料時，如何處理分隔符號文字檔中遺漏的值。 

**`tableName`在 Azure SQL 資料倉儲**

下表是如何在 JSON 資料集中指定 **tableName** 屬性的範例。 其中會顯示數個結構描述和資料表名稱的組合。

| DB 結構描述 | 資料表名稱 | **tableName** JSON 屬性               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable 或 dbo.MyTable 或 [dbo].[MyTable] |
| dbo1      | MyTable    | dbo1.MyTable 或 [dbo1].[MyTable]          |
| dbo       | My.Table   | [My.Table] 或 [dbo].[My.Table]            |
| dbo1      | My.Table   | [dbo1].[My.Table]                         |

如果您看到下列錯誤，可能是您為 **tableName** 屬性指定的值有問題。 請參閱前面的資料表，以正確的方式指定 **tableName** JSON 屬性的值。

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**具有預設值的資料行**

Data Factory 中的 PolyBase 功能目前只接受與目標資料表中相同的資料行數目。 範例是內含四個資料行的資料表，且其中一個資料行已使用預設值進行定義。 輸入資料仍需要有四個資料行。 3 個資料行的輸入資料集會產生類似下列訊息的錯誤︰

```
All columns of the table must be specified in the INSERT BULK statement.
```

NULL 值是一種特殊形式的預設值。 如果資料欄可以是 Null，Blob 中該欄的輸入資料可能會是空白。 但輸入資料集中不能缺少輸入資料。 PolyBase 會在 Azure SQL 資料倉儲中為遺漏值插入 NULL。

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

深入瞭解對應資料流程中[來源轉換](data-flow-source.md)和[接收轉換](data-flow-sink.md)的詳細資料。

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Azure SQL 資料倉儲的資料類型對應

從「Azure SQL 資料倉儲」複製資料或將資料複製到該處時，會使用下列從「Azure SQL 資料倉儲」資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收。

>[!TIP]
>如需 SQL DW 支援的資料類型和不支援的因應措施，請參閱[Azure SQL 資料倉儲文章中的資料表資料類型](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md)。

| Azure SQL 資料倉儲資料類型    | Data Factory 過渡期資料類型 |
| :------------------------------------ | :----------------------------- |
| Bigint                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | Boolean                        |
| char                                  | String, Char[]                 |
| date                                  | Datetime                       |
| Datetime                              | Datetime                       |
| datetime2                             | Datetime                       |
| Datetimeoffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM attribute (varbinary(max)) | Byte[]                         |
| 浮點數                                 | Double                         |
| image                                 | Byte[]                         |
| ssNoversion                                   | Int32                          |
| money                                 | Decimal                        |
| nchar                                 | String, Char[]                 |
| numeric                               | Decimal                        |
| nvarchar                              | String, Char[]                 |
| real                                  | Single                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | Datetime                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| time                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | Guid                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要瞭解屬性的詳細資料，請檢查[查閱活動](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活動屬性

若要瞭解有關屬性的詳細資料，請檢查[GetMetadata 活動](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收端的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md##supported-data-stores-and-formats)。
