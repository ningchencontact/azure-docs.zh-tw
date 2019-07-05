---
title: 使用 Azure Data Factory 從 Teradata 複製資料 | Microsoft Docs
description: 了解 Data Factory 服務的「Teradata 連接器」，此連接器可讓您將資料從 Teradata 資料庫複製到 Data Factory 所支援作為接收器的資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 63f28c8b6eaceed12e1f76e9c0c5984e3b63b500
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561442"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>使用 Azure Data Factory 從 Teradata 複製資料
> [!div class="op_single_selector" title1="選取您正在使用的 Data Factory 服務的版本："]
>
> * [第 1 版](v1/data-factory-onprem-teradata-connector.md)
> * [目前的版本](connector-teradata.md)

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Teradata 資料庫複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 Teradata 資料庫複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Teradata 連接器支援：

- Teradata **14.10、 15.0、 15.10、 16.0、 16.10，和 16.20**。
- 使用 **Basic** (基本) 或 **Windows** 驗證來複製資料。
- 從 Teradata 來源的平行複製。 請參閱[平行從 Teradata 複製](#parallel-copy-from-teradata)詳細資料 區段。

> [!NOTE]
>
> Azure Data Factory 會自其中式的內建的 ODBC 驅動程式，並提供彈性的連接選項，以及立即可用平行複製，大幅提升效能的自我裝載 Integration Runtime v3.18 升級 Teradata 連接器。 由.NET 資料提供者所 Teradata 仍支援做為授權的任何現有的工作負載，使用先前的 Teradata 連接器-是，但建議您接下來一。 請注意新的路徑需要一組不同的連結服務/資料集/複製來源。 在設定的詳細資訊，請參閱個別的區段。

## <a name="prerequisites"></a>必要條件

如果您的 Teradata 不是可公開存取，您需要設定自我裝載整合執行階段。 如需整合執行階段的詳細資訊，請參閱[自我裝載 Integration Runtime](create-self-hosted-integration-runtime.md)。 Integration Runtime 提供內建的 Teradata 驅動程式，從 3.18 版開始，因此您不需要手動安裝任何驅動程式。 驅動程式需要 「 VisualC++可轉散發套件 2012 Update 4 「 自我裝載 IR 上從下載[以下](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)如果您還沒有安裝它。

如需自我裝載 IR 版本低於 3.18，必須先安裝[.NET Data Provider for Teradata](https://go.microsoft.com/fwlink/?LinkId=278886)版本 14 或以上 Integration Runtime 電腦上。 

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Teradata 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Teradata 已連結服務支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為：**Teradata** | 是 |
| connectionString | 指定連接到 Teradata 資料庫執行個體所需的資訊。 請參考下列範例。<br/>您也可以將密碼放在 Azure Key Vault 中，並從連接字串中提取 `password` 組態。 請參閱[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)有更多詳細資料的發行項。 | 是 |
| username | 指定連線到 Teradata 資料庫時所要使用的使用者名稱。 適用於使用 Windows 驗證時。 | 否 |
| password | 指定您為使用者名稱所指定之使用者帳戶的密碼。 您也可以選擇[參考儲存在 Azure Key Vault 祕密](store-credentials-in-key-vault.md)。 <br>適用於使用 Windows 驗證時，或參考金鑰保存庫中的密碼進行基本驗證。 | 否 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 如[必要條件](#prerequisites)所述，必須要有一個「自我裝載 Integration Runtime」。 |是 |

**範例： 使用基本驗證**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例：使用 Windows 驗證**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> 如果您使用的 Teradata 連結的服務獲得授權的.NET Data Provider for Teradata 具有下列的承載，它仍然支援做為是，但建議您接下來一。

**先前的承載：**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
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

如需可用來定義資料集的區段和屬性完整清單，請參閱資料集文章。 本節提供 Teradata 資料集所支援的屬性清單。

若要從 Teradata 複製資料，支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為：**TeradataTable** | 是 |
| database | Teradata 資料庫的名稱。 | 否 (如果已指定活動來源中的「查詢」) |
| table | Teradata 資料庫中的資料表名稱。 | 否 (如果已指定活動來源中的「查詢」) |

**範例：**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

> [!NOTE]
>
> 如果您已使用"RelationalTable"類型資料集，如下所示，它仍然支援做為是，但建議您接下來一。

**先前的承載：**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Teradata 來源所支援的屬性清單。

### <a name="teradata-as-source"></a>Teradata 作為來源

> [!TIP]
>
> 進一步了解[平行從 Teradata 複製](#parallel-copy-from-teradata)如何將資料從 Teradata 有效率地使用 資料分割上的一節。

若要從 Teradata 複製資料，將複製活動中支援下列屬性**來源**區段：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為：**TeradataSource** | 是 |
| query | 使用自訂 SQL 查詢來讀取資料。 例如： `"SELECT * FROM MyTable"` 。<br>當您啟用資料分割的負載時，您需要在查詢中攔截相對應的內建的資料分割參數。 請參閱中的範例[平行從 Teradata 複製](#parallel-copy-from-teradata)一節。 | 否 （如果已指定資料集中的資料表） |
| partitionOptions | 指定資料分割選項用來將資料從 Teradata 的資料。 <br>允許的值為：**無**（預設值），**雜湊**並**DynamicRange**。<br>啟用資料分割選項時 (沒有 ' None')，請也設定 **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** 設定複製活動，例如 4 中，以決定從 Teradata，同時將資料的平行程度資料庫。 | 否 |
| partitionSettings | 指定的設定資料分割的群組。 <br>不是資料分割選項時，適用`None`。 | 否 |
| partitionColumnName | 指定的來源資料行名稱**中的整數型別**，將使用的定界分割的平行複製。 如果未指定，則資料表的主索引鍵將會自動偵測到並做為分割區資料行。 <br>適用於當資料分割選項`Hash`或`DynamicRange`。 如果您使用查詢來擷取來源資料時，攔截`?AdfHashPartitionCondition`或`?AdfRangePartitionColumnName`WHERE 子句中。 中的範例，請參閱[平行從 Teradata 複製](#parallel-copy-from-teradata)一節。 | 否 |
| partitionUpperBound | 將資料複製的資料分割資料行的最大值。 <br>套用資料分割選項時`DynamicRange`。 如果您使用查詢來擷取來源資料時，攔截`?AdfRangePartitionUpbound`WHERE 子句中。 中的範例，請參閱[平行從 Teradata 複製](#parallel-copy-from-teradata)一節。 | 否 |
| PartitionLowerBound | 將資料複製的資料分割資料行的最小值。 <br>套用資料分割選項時`DynamicRange`。 如果您使用查詢來擷取來源資料時，攔截`?AdfRangePartitionLowbound`WHERE 子句中。 中的範例，請參閱[平行從 Teradata 複製](#parallel-copy-from-teradata)一節。 | 否 |

> [!NOTE]
>
> 如果您已使用"RelationalSource"類型複製來源，它仍可作為-，但不支援從 Teradata （資料分割選項） 的新內建平行處理負載。 建議您在使用這個接下來的新指令碼。

**使用基本的查詢，而不需要資料分割的範例： 複製資料**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>從 Teradata 的平行複製

資料處理站的 Teradata 連接器提供內建的資料分割來從 Teradata 複製資料，以平行方式有絕佳的效能。 您可以找到在複製活動的資料分割選項]-> [Teradata 來源：

![資料分割選項](./media/connector-teradata/connector-teradata-partition-options.png)

當您啟用資料分割的複本時，data factory 會執行平行查詢，針對 Teradata 來源將資料載入資料分割。 設定和控制透過平行程度 **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** 複製活動上設定。 例如，如果您設定`parallelCopies`為四個，同時會產生資料處理站，並執行四個查詢根據您指定的資料分割選項和設定，將資料從 Teradata 資料庫中每個擷取的一部分。

建議您啟用平行複製的資料分割，尤其是當您載入大量資料從 Teradata 資料庫的資料。 以下是不同案例的建議的設定：

| 案例                                                     | 建議的設定                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 從大型資料表的完整載入                                   | **資料分割選項**:雜湊。 <br><br/>在執行期間，資料處理站自動偵測 PK 資料行，套用對它的雜湊並將資料複製的資料分割。 |
| 載入大量的資料使用自訂查詢                 | **資料分割選項**:雜湊。<br>**查詢**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`。<br>**資料分割資料行**:指定用於套用雜湊分割區的資料行。 如果未指定，ADF 會自動偵測您在 Teradata 資料集指定資料表的 PK 資料行。<br><br>在執行期間，資料處理站取代`?AdfHashPartitionCondition`雜湊資料分割邏輯與傳送到 Teradata。 |
| 載入大量使用自訂查詢，具有整數資料行的定界分割平均分佈值的資料 | **資料分割選項**:動態定界分割區。<br>**查詢**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`。<br>**資料分割資料行**:指定用來分割資料的資料行。 您可以針對資料行分割具有整數資料類型。<br>**資料分割上限**並**分割區下限**:指定是否您想要針對資料分割資料行，只擷取下限和上限範圍之間的資料篩選。<br><br>在執行期間，資料處理站 replace `?AdfRangePartitionColumnName`， `?AdfRangePartitionUpbound`，和`?AdfRangePartitionLowbound`使用實際的資料行的名稱和值範圍，每個資料分割，並將傳送至 Teradata。 <br>比方說，如果您的磁碟分割資料行的 「 識別碼 」 設定下限為 1 到上限為 80，平行複製設定為 4，ADF 會擷取資料，由 4 個磁碟分割識別碼之間 [1,20]，[21，40，] [41，60] 和 [61，80]。 |

**範例： 查詢具有雜湊資料分割**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**範例： 查詢具有動態範圍資料分割**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Teradata 的資料類型對應

從 Teradata 複製資料時，會使用下列從 Teradata 資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收器。

| Teradata 資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |String |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| 圖形 |不支援。 適用於來源查詢中的明確轉換。 |
| Integer |Int32 |
| Interval Day |不支援。 適用於來源查詢中的明確轉換。 |
| Interval Day To Hour |不支援。 適用於來源查詢中的明確轉換。 |
| Interval Day To Minute |不支援。 適用於來源查詢中的明確轉換。 |
| Interval Day To Second |不支援。 適用於來源查詢中的明確轉換。 |
| Interval Hour |不支援。 適用於來源查詢中的明確轉換。 |
| Interval Hour To Minute |不支援。 適用於來源查詢中的明確轉換。 |
| Interval Hour To Second |不支援。 適用於來源查詢中的明確轉換。 |
| Interval Minute |不支援。 適用於來源查詢中的明確轉換。 |
| Interval Minute To Second |不支援。 適用於來源查詢中的明確轉換。 |
| 間隔月 |不支援。 適用於來源查詢中的明確轉換。 |
| Interval Second |不支援。 適用於來源查詢中的明確轉換。 |
| 間隔年 |不支援。 適用於來源查詢中的明確轉換。 |
| 間隔年至月 |不支援。 適用於來源查詢中的明確轉換。 |
| Number |Double |
| 期間 （日期） |不支援。 適用於來源查詢中的明確轉換。 |
| 期間 （時間） |不支援。 適用於來源查詢中的明確轉換。 |
| 期間 （時間與時區） |不支援。 適用於來源查詢中的明確轉換。 |
| 期間 （時間戳記） |不支援。 適用於來源查詢中的明確轉換。 |
| 期間 （時區的時間戳記） |不支援。 適用於來源查詢中的明確轉換。 |
| SmallInt |Int16 |
| Time |TimeSpan |
| 時區的時間 |TimeSpan |
| Timestamp |DateTime |
| Timestamp With Time Zone |Datetime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |不支援。 適用於來源查詢中的明確轉換。 |
| Xml |不支援。 適用於來源查詢中的明確轉換。 |


## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
