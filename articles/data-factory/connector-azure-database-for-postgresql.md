---
title: 使用 Azure Data Factory 從適用於 PostgreSQL 的 Azure 資料庫複製資料 | Microsoft Docs
description: 了解如何使用 Azure Data Factory 管線中的複製活動，將資料從適用於 PostgreSQL 的 Azure 資料庫複製到支援的接收資料存放區。
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
ms.date: 04/28/2018
ms.author: jingwang
ms.openlocfilehash: 365775f840f85efe1792f376880145c7e7db1312
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2018
ms.locfileid: "33205324"
---
# <a name="copy-data-from-azure-database-for-postgresql-using-azure-data-factory"></a>使用 Azure Data Factory 從適用於 PostgreSQL 的 Azure 資料庫複製資料 

本文概述如何使用 Azure Data Factory 中的「複製活動」，從適用於 PostgreSQL 的 Azure 資料庫複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務，也就是正式推出 (GA) 的版本，請參閱[第 1 版的複製活動](v1/data-factory-data-movement-activities.md)。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從適用於 PostgreSQL 的 Azure 資料庫複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

Azure Data Factory 提供的內建驅動程式可啟用連線，因此使用此連接器您不需要手動安裝任何驅動程式。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義適用於 PostgreSQL 的 Azure 資料庫連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對適用於 PostgreSQL 的 Azure 資料庫已連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為：**AzurePostgreSql** | yes |
| connectionString | ODBC 連接字串，用於連線到適用於 PostgreSQL 的 Azure 資料庫。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | yes |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或「自我裝載 Integration Runtime」(如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

一般的連接字串為 `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>@admstest;Password=<Password>`。 您可以根據您的案例設定更多屬性：

| 屬性 | 說明 | 選項 | 必要 |
|:--- |:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| 驅動程式用來加密在驅動程式和資料庫伺服器之間傳送之資料的方法。 例如 `ValidateServerCertificate=<0/1/6>;`| 0 (無加密) **(預設)** / 1 (SSL) / 6 (RequestSSL) | 否 |
| ValidateServerCertificate (VSC) | 決定啟用 SSL 加密時，驅動程式是否驗證由資料庫伺服器所傳送的憑證 (加密方法 = 1)。 例如 `ValidateServerCertificate=<0/1>;`| 0 (停用) **(預設)** / 1 (啟用) | 否 |

**範例：**

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>@admstest;Password=<Password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供適用於 PostgreSQL 的 Azure 資料庫資料集所支援的屬性清單。

若要從適用於 PostgreSQL 的 Azure 資料庫複製資料，將資料集的類型屬性設定為 **AzurePostgreSqlTable**。 在此類型的資料集中，沒有任何其他類型特定的屬性。

**範例**

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供適用於 PostgreSQL 的 Azure 資料庫來源所支援的屬性清單。

### <a name="azurepostgresqlsource-as-source"></a>AzurePostgreSqlSource as source

若要從適用於 PostgreSQL 的 Azure 資料庫複製資料，將複製活動中的來源類型設定為 **AzurePostgreSqlSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為：**AzurePostgreSqlSource** | yes |
| query | 使用自訂 SQL 查詢來讀取資料。 例如：`"SELECT * FROM MyTable"`。 | yes |

**範例：**

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
