---
title: 使用 Azure Data Factory 從 MySQL 複製資料 | Microsoft Docs
description: 了解 Azure Data Factory 中的 MySQL 連接器，此連接器可讓您將資料從 MySQL 資料庫複製到所支援作為接收器的資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: fcf56e8088af25c14c022039bf8862f2dc21c77a
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172558"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>使用 Azure Data Factory 從 MySQL 複製資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-onprem-mysql-connector.md)
> * [目前的版本](connector-mysql.md)

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 MySQL 資料庫複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

>[!NOTE]
>若要將資料從或複製到[適用於 MySQL 的 Azure 資料庫](../mysql/overview.md)服務, 請使用特殊化的[適用於 MySQL 的 Azure 資料庫連接器](connector-azure-database-for-mysql.md)。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 MySQL 資料庫複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 MySQL 連接器支援 MySQL **5.6 和 5.7 版**。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime 從 3.7 版開始提供內建的 MySQL 驅動程式，因此您不需要手動安裝任何驅動程式。

針對 3.7 版以前的自我裝載 IR 版本，您必須在 Integration Runtime 電腦上安裝 [MySQL 連接器/適用於 Microsoft Windows 的 Net](https://dev.mysql.com/downloads/connector/net/)，版本為 6.6.5 與 6.10.7 之間的版本。 這個 32 位元驅動程式可與 64 位元 IR 相容。

## <a name="getting-started"></a>使用者入門

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 MySQL 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 MySQL 已連結服務支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | 類型屬性必須設定為：**MySql** | 是 |
| connectionString | 指定連線到適用於 MySQL 的 Azure 資料庫執行個體所需的資訊。<br/>將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中。 您也可以將密碼放在 Azure Key Vault 中，並從連接字串中提取 `password` 組態。 請參閱下列範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文中的更多詳細資料。 | 是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 深入瞭解[必要條件](#prerequisites)一節。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

一般的連接字串為 `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`。 您可以根據您的案例設定更多屬性：

| 屬性 | 描述 | 選項。 | 必要項 |
|:--- |:--- |:--- |:--- |
| SSLMode | 此選項指定驅動程式在連接到 MySQL 時，是否會使用 SSL 加密及驗證。 例如 `SSLMode=<0/1/2/3/4>`| DISABLED (0) / PREFERRED (1) **(預設)** / REQUIRED (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | 否 |
| UseSystemTrustStore | 此選項指定是否使用來自系統信任存放區或來自指定 PEM 檔案的 CA 憑證。 例如 `UseSystemTrustStore=<0/1>;`| 啟用 (1) / 停用 (0) **(預設)** | 否 |

**範例:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例：在 Azure Key Vault 中儲存密碼**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;"
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

如果您使用具有以下承載的 MySQL 連結服務，它仍然如同現狀受支援，但建議您使用新版本。

**先前的承載：**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "username": "<username>",
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

如需可用來定義資料集的區段和屬性完整清單，請參閱資料集文章。 本節提供 MySQL 資料集所支援的屬性清單。

若要從 MySQL 複製資料，請將資料集的類型屬性設定為 **RelationalTable**。 以下是支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | 資料集的類型屬性必須設定為：**RelationalTable** | 是 |
| tableName | MySQL 資料庫中的資料表名稱。 | 否 (如果已指定活動來源中的"query") |

**範例**

```json
{
    "name": "MySQLDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[Pipelines](concepts-pipelines-activities.md)一文。 本節提供 MySQL 來源所支援的屬性清單。

### <a name="mysql-as-source"></a>MySQL 作為來源

若要從 MySQL 複製資料，請將複製活動中的來源類型設定為 **RelationalSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | 複製活動來源的類型屬性必須設定為：**RelationalSource** | 是 |
| query | 使用自訂 SQL 查詢來讀取資料。 例如： `"SELECT * FROM MyTable"` 。 | 否 (如果已指定資料集中的 "tableName") |

**範例:**

```json
"activities":[
    {
        "name": "CopyFromMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MySQL input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-mysql"></a>MySQL 的資料類型對應

從 MySQL 複製資料時，會使用下列從 MySQL 資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收器。

| MySQL 資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit(1)` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int` |

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
