---
title: 使用 Azure Data Factory 從 PostgreSQL 複製資料 | Microsoft Docs
description: 了解如何使用 Azure Data Factory 管線中的複製活動，將資料從 PostgreSQL 複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: 8515b3f357d77ea4f3d98101f8dd058f13b69206
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405744"
---
# <a name="copy-data-from-postgresql-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 PostgreSQL 複製資料
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版](v1/data-factory-onprem-postgresql-connector.md)
> * [目前的版本](connector-postgresql.md)

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 PostgreSQL 資料庫複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 PostgreSQL 資料庫複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 PostgreSQL 連接器支援 PostgreSQL **7.4 版和更新版本**。

## <a name="prerequisites"></a>必要條件

如果您的 PostgreSQL 資料庫無法公開存取，您需要設定自我裝載 Integration Runtime。 若要了解自我裝載整合執行階段，請參閱[自我裝載整合執行階段](create-self-hosted-integration-runtime.md)一文。 Integration Runtime 從版本 3.7 開始提供內建的 PostgreSQL 驅動程式，因此您不需要手動安裝任何驅動程式。

針對版本 3.7 以前的自我裝載 IR 版本，您必須在 Integration Runtime 電腦上安裝 [PostgreSQL 的 Ngpsql 資料提供者](https://go.microsoft.com/fwlink/?linkid=282716)，版本為 2.0.12 和 3.1.9 之間的版本。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 PostgreSQL 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 PostgreSQL 已連結服務支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為：**PostgreSql** | 是 |
| connectionString | ODBC 連接字串，用於連線到適用於 PostgreSQL 的 Azure 資料庫。 <br/>將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中。 您也可以將密碼放在 Azure Key Vault 中，並從連接字串中提取 `password` 組態。 請參閱下列範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文中的更多詳細資料。 | 是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 您可以使用「自我裝載 Integration Runtime」或 Azure Integration Runtime (如果您的資料存放區是可公開存取的)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

一般的連接字串為 `Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`。 您可以根據您的案例設定更多屬性：

| 屬性 | 描述 | 選項 | 必要項 |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| 驅動程式用來加密在驅動程式和資料庫伺服器之間傳送之資料的方法。 例如 `ValidateServerCertificate=<0/1/6>;`| 0 (無加密) **(預設)** / 1 (SSL) / 6 (RequestSSL) | 否 |
| ValidateServerCertificate (VSC) | 決定啟用 SSL 加密時，驅動程式是否驗證由資料庫伺服器所傳送的憑證 (加密方法 = 1)。 例如 `ValidateServerCertificate=<0/1>;`| 0 (停用) **(預設)** / 1 (啟用) | 否 |

**範例：**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
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
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;"
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

如果您使用具有以下承載的 PostgreSQL 連結服務，它仍然如同現狀受支援，但是建議您使用新版本。

**先前的承載：**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
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

如需可用來定義資料集的區段和屬性完整清單，請參閱資料集文章。 本節提供 PostgreSQL 資料集所支援的屬性清單。

若要從 PostgreSQL 複製資料，請將資料集的類型屬性設定為 **RelationalTable**。 以下是支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為：**RelationalTable** | 是 |
| tableName | PostgreSQL 資料庫中的資料表名稱。 | 否 (如果已指定活動來源中的「查詢」) |

**範例**

```json
{
    "name": "PostgreSQLDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<PostgreSQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 PostgreSQL 來源所支援的屬性清單。

### <a name="postgresql-as-source"></a>PostgreSQL 作為來源

若要從 PostgreSQL 複製資料，請將複製活動中的來源類型設定為 **RelationalSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為：**RelationalSource** | 是 |
| query | 使用自訂 SQL 查詢來讀取資料。 例如： `"query": "SELECT * FROM \"MySchema\".\"MyTable\""` 。 | 否 (如果已指定資料集中的 "tableName") |

> [!NOTE]
> 結構描述和資料表名稱會區分大小寫。 在查詢中以 `""` (雙引號) 括住它們。

**範例：**

```json
"activities":[
    {
        "name": "CopyFromPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<PostgreSQL input dataset name>",
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
                "query": "SELECT * FROM \"MySchema\".\"MyTable\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。
