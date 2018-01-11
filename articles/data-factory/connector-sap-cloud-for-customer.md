---
title: "使用 Azure Data Factory 的客戶的資料來源/目的地 SAP 雲端複製 |Microsoft 文件"
description: "了解如何將資料複製從 SAP 雲端支援的接收資料存放區的客戶 （或） 從支援的來源資料存放區到 SAP 雲端客戶使用 Data Factory。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: ad3bc7ba38f5ea20586031bdcc3ae44f03f9da0b
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>將資料從 SAP 客戶 (C4C) 的雲端，使用 Azure Data Factory 複製

本文概述如何使用 Azure Data Factory 中的複製活動的客戶 (C4C) 複製自/至 SAP 雲端的資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務，也就是正式推出 (GA) 的版本，請參閱[第 1 版的複製活動](v1/data-factory-data-movement-activities.md)。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 SAP 雲端的資料客戶複製到任何支援的接收資料存放區中，或將資料從任何支援的來源資料存放區複製到 SAP 雲端，客戶。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，此連接器可讓 Azure Data Factory 複製自/至 SAP 雲端的資料包括 SAP 雲端銷售額、 SAP 雲端服務，以及 SAP 雲端社交 Engagement 解決方案的客戶。

## <a name="getting-started"></a>開始使用

您可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure Resource Manager 範本來建立具有複製活動的管線。 如需建立內含複製活動之管線的逐步指示，請參閱[複製活動教學課程](quickstart-create-data-factory-dot-net.md)。

下列各節提供有關用來定義 SAP 雲端客戶連接器的 Data Factory 實體特定屬性的詳細資料。

## <a name="linked-service-properties"></a>連結服務屬性

連結的客戶服務支援 SAP 雲端下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為： **SapCloudForCustomer**。 | 是 |
| url | SAP C4C OData 服務的 URL。 | 是 |
| username | 指定要連接到 SAP C4C 的使用者名稱。 | 是 |
| password | 指定您所指定的使用者名稱的使用者帳戶的密碼。 請將此欄位標示為 SecureString。 | 是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 如果未指定，就會使用預設的 Azure Integration Runtime。 | 否 (來源)；是 (接收) |

>[!IMPORTANT]
>若要將資料複製到雲端 SAP 客戶，明確[建立 Azure IR](create-azure-integration-runtime.md#create-azure-ir)與 SAP 客戶及連結服務中的關聯雲端附近的位置，如下列範例：

**範例：**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供一份 SAP 雲端支援的客戶資料集的屬性。

若要從 SAP 雲端複製資料，客戶，設定要的資料集的類型屬性**SapCloudForCustomerResource**。 以下是支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為： **SapCloudForCustomerResource** |是 |
| path | 指定 SAP C4C OData 實體的路徑。 |是 |

**範例：**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typePoperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供一份客戶來源 SAP 雲端支援的屬性。

### <a name="sap-c4c-as-source"></a>做為來源的 SAP C4C

若要從 SAP 雲端複製資料，客戶，複製活動中將 來源類型**SapCloudForCustomerSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為： **SapCloudForCustomerSource**  | 是 |
| query | 指定自訂的 OData 查詢，以讀取資料。 | 否 |

範例查詢，以取得特定一天的資料：`"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**範例：**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SAPC4CSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>做為接收器的 SAP C4C

若要將客戶的資料複製到 SAP 雲端，複製活動中設定 接收器類型**SapCloudForCustomerSink**。 複製活動的 **sink** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為： **SapCloudForCustomerSink**  | 是 |
| writeBehavior | 作業的寫入行為。 可能是 「 插入 」，「 更新 」。 | 編號 預設 「 插入 」。 |
| writeBatchSize | 寫入作業的批次大小。 要取得最佳效能的批次大小可能不同的伺服器或不同的資料表。 | 編號 預設值 10。 |

**範例：**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "cloudDataMovementUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>SAP 雲端客戶的資料類型對應

複製資料時從 SAP 雲端的客戶，下列的對應會用於從 SAP 雲端客戶的資料類型以 Azure Data Factory 過渡期的資料類型。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收器。

| SAP C4C OData 資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | Datetime |
| Edm.Decimal | 十進位 |
| Edm.Double | 兩倍 |
| Edm.Single | 單一 |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | 字串 |
| Edm.Time | 時間範圍 |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
