---
title: 使用 Azure Data Factory 從 SAP HANA 複製資料 | Microsoft Docs
description: 了解如何使用 Azure Data Factory 管線中的複製活動，將資料從 SAP HANA 複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 8174b2e8bc63db8954e596d831eb2f9cad2ba440
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211628"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>使用 Azure Data Factory 從 SAP HANA 複製資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-sap-hana-connector.md)
> * [目前的版本](connector-sap-hana.md)

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 SAP HANA 資料庫複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

>[!TIP]
>若要瞭解 ADF 對於 SAP 資料整合案例的整體支援, 請參閱[使用 Azure Data Factory 白皮書的 SAP 資料整合](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf), 其中包含詳細的簡介、comparsion 和指引。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 SAP HANA 資料庫複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 SAP HANA 連接器支援：

- 從任何版本的 SAP HANA 資料庫複製資料。
- 從**HANA 資訊模型**(例如分析和計算視圖) 和資料**列/資料行資料表**複製資料。
- 使用 **Basic** 或 **Windows** 驗證來複製資料。

> [!TIP]
> 若要將資料複製**到** SAP HANA 資料存放區，請使用一般 ODBC 連接器。 如需詳細資料，請參閱 [SAP HANA 接收器](connector-odbc.md#sap-hana-sink)。 請注意，SAP HANA 連接器和 ODBC 連接器的已連接服務具有不同的類型，因此無法重複使用。

## <a name="prerequisites"></a>必要條件

若要使用這個 SAP HANA 接收器，您必須：

- 設定一個「自我裝載 Integration Runtime」。 如需詳細資料，請參閱[自我裝載 Integration Runtime](create-self-hosted-integration-runtime.md) 一文。
- 在 Integration Runtime 電腦上安裝 SAP HANA ODBC 驅動程式。 您可以從 [SAP 軟體下載中心](https://support.sap.com/swdc)下載 SAP Hana ODBC 驅動程式。 使用關鍵字 **SAP HANA CLIENT for Windows** 搜尋。

## <a name="getting-started"></a>使用者入門

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 SAP HANA 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 SAP HANA 已連結服務支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | 類型屬性必須設定為：**SapHana** | 是 |
| connectionString | 指定使用**基本驗證**或**Windows 驗證**連接到 SAP Hana 所需的資訊。 請參考下列範例。<br>在 [連接字串] 中, 伺服器/埠是強制的 (預設通訊埠是 30015), 而使用者名稱和密碼則是在使用基本驗證時的必要項。 如需其他 advanced 設定, 請參閱[SAP HANA ODBC 連接屬性](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>您也可以將密碼放在 Azure Key Vault 中, 並從連接字串中提取密碼設定。 如需詳細資訊, 請參閱[將認證儲存在 Azure Key Vault](store-credentials-in-key-vault.md)一文。 | 是 |
| userName | 使用 Windows 驗證時, 請指定使用者名稱。 範例： `user@domain.com` | 否 |
| password | 指定使用者帳戶的密碼。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 否 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 如[必要條件](#prerequisites)所述，必須要有一個「自我裝載 Integration Runtime」。 |是 |

**範例: 使用基本驗證**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例: 使用 Windows 驗證**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
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

如果您使用的是具有下列承載的 SAP Hana 連結服務, 它仍會受到支援, 但建議您繼續使用新的服務。

**範例:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

如需可用來定義資料集的區段和屬性完整清單，請參閱資料集文章。 本節提供 SAP HANA 資料集所支援的屬性清單。

若要從 SAP Hana 複製資料, 支援下列屬性:

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | 資料集的類型屬性必須設定為：**SapHanaTable** | 是 |
| schema | SAP Hana 資料庫中的架構名稱。 | 否 (如果已指定活動來源中的"query") |
| table | SAP Hana 資料庫中的資料表名稱。 | 否 (如果已指定活動來源中的"query") |

**範例:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

如果您使用`RelationalTable`的是具類型的資料集, 則仍會受到支援, 但建議您在未來使用新的 dataset。

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[Pipelines](concepts-pipelines-activities.md)一文。 本節提供 SAP HANA 來源所支援的屬性清單。

### <a name="sap-hana-as-source"></a>SAP HANA 作為來源

若要從 SAP Hana 複製資料, 複製活動的 [**來源**] 區段中支援下列屬性:

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | 複製活動來源的類型屬性必須設定為：**SapHanaSource** | 是 |
| query | 指定 SQL 查詢從 SAP HANA 執行個體讀取資料。 | 是 |
| packetSize | 指定將資料分割成多個區塊的網路封包大小 (以 Kb 為單位)。 如果您要複製大量資料, 增加封包大小可能會在大部分情況下從 SAP Hana 增加讀取速度。 調整封包大小時, 建議執行效能測試。 | 資料分割<br>預設值為 2048 (2MB)。 |

**範例:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

如果您使用`RelationalSource`的是具類型的複製來源, 則仍會受到支援, 但建議您在未來使用新的版本。

## <a name="data-type-mapping-for-sap-hana"></a>SAP HANA 的資料類型對應

從 SAP HANA 複製資料時，會使用下列從 SAP HANA 資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收器。

| SAP HANA 資料類型 | Data Factory 過渡期資料類型 |
| ------------------ | ------------------------------ |
| ALPHANUM           | String                         |
| BIGINT             | Int64                          |
| BINARY             | Byte[]                         |
| BINTEXT            | String                         |
| BLOB               | Byte[]                         |
| BOOL               | Byte                           |
| CLOB               | String                         |
| DATE               | DateTime                       |
| Decimal            | Decimal                        |
| DOUBLE             | DOUBLE                         |
| FLOAT              | DOUBLE                         |
| INTEGER            | Int32                          |
| NCLOB              | String                         |
| NVARCHAR           | String                         |
| REAL               | Single                         |
| SECONDDATE         | DateTime                       |
| SHORTTEXT          | String                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | String                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | String                         |
| TIMESTAMP          | DateTime                       |
| VARBINARY          | Byte[]                         |

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
