---
title: 使用 Azure Data Factory 從 Teradata 有利複製資料 |Microsoft Docs
description: Data Factory 服務的 Teradata 連接器可讓您將 Teradata 有利的資料複製到 Data Factory 所支援的資料存放區作為接收。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: jingwang
ms.openlocfilehash: b91ec46d47814418de21e9afe3e6e5534473c921
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2019
ms.locfileid: "71008973"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 Teradata 有利複製資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
>
> * [第 1 版](v1/data-factory-onprem-teradata-connector.md)
> * [目前的版本](connector-teradata.md)

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Teradata 有利複製資料。 它是以[複製活動總覽](copy-activity-overview.md)為基礎。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Teradata 連接器：

- [複製活動](copy-activity-overview.md)與[支援的來源矩陣](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 Teradata 有利複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Teradata 連接器支援：

- Teradata**版本14.10、15.0、15.10、16.0、16.10 和 16.20**。
- 使用**Basic**或**Windows**驗證來複製資料。
- 從 Teradata 來源進行平行複製。 如需詳細資訊，請參閱[從 Teradata 的平行複製](#parallel-copy-from-teradata)一節。

> [!NOTE]
>
> 在自我裝載整合執行時間 v 3.18 發行之後，Azure Data Factory 升級 Teradata 連接器。 仍然支援任何使用舊版 Teradata 連接器的現有工作負載。 不過，對於新的工作負載而言，使用新的就是個不錯的主意。 請注意，新的路徑需要一組不同的連結服務、資料集和複製來源。 如需設定詳細資料，請參閱下列各節。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

整合執行時間提供內建的 Teradata 驅動程式，從3.18 版開始。 您不需要手動安裝任何驅動程式。 驅動程式在自我裝載C++整合執行時間電腦上需要「Visual 可轉散發2012更新4」。 如果您尚未安裝，請從[這裡](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)下載。

針對早于3.18 的任何自我裝載整合執行時間版本，請在 integration runtime 電腦上安裝適用于 Teradata 14 或更新版本的[.net Data Provider](https://go.microsoft.com/fwlink/?LinkId=278886)。 

## <a name="getting-started"></a>使用者入門

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Teradata 連接器特定的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

Teradata 連結服務支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | Type 屬性必須設定為**Teradata**。 | 是 |
| connectionString | 指定連接到 Teradata 實例所需的資訊。 請參考下列範例。<br/>您也可以將密碼放在 Azure Key Vault 中，並從`password`連接字串中提取設定。 如需詳細資訊，請參閱[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)。 | 是 |
| username | 指定要連接到 Teradata 的使用者名稱。 適用于使用 Windows 驗證時。 | 否 |
| password | 針對您為使用者名稱指定的使用者帳戶指定密碼。 您也可以選擇[參考儲存在 Azure Key Vault 中的秘密](store-credentials-in-key-vault.md)。 <br>適用于當您使用 Windows 驗證，或參考 Key Vault 中的密碼進行基本驗證時。 | 否 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 深入瞭解[必要條件](#prerequisites)一節。 如果未指定，就會使用預設的 Azure Integration Runtime。 |是 |

**使用基本驗證的範例**

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

**使用 Windows 驗證的範例**

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
> 仍支援下列裝載。 不過，您應該使用新的應用層。

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

本節提供 Teradata 資料集所支援的屬性清單。 如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)。

若要從 Teradata 複製資料，支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | 資料集的類型屬性必須設定為`TeradataTable`。 | 是 |
| database | Teradata 實例的名稱。 | 否 (如果已指定活動來源中的"query") |
| table | Teradata 實例中的資料表名稱。 | 否 (如果已指定活動來源中的"query") |

**範例:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable`仍然支援類型資料集。 不過，我們建議您使用新的資料集。

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

本節提供 Teradata 來源所支援的屬性清單。 如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)。 

### <a name="teradata-as-source"></a>Teradata 作為來源

>[!TIP]
>若要使用資料分割有效率地從 Teradata 載入資料，請從[Teradata 的平行複製](#parallel-copy-from-teradata)一節深入瞭解。

若要從 Teradata 複製資料，複製活動的 [**來源**] 區段中支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | 複製活動來源的類型屬性必須設定為`TeradataSource`。 | 是 |
| query | 使用自訂 SQL 查詢來讀取資料。 例如 `"SELECT * FROM MyTable"`。<br>當您啟用資料分割載入時，您必須在查詢中攔截任何對應的內建資料分割參數。 如需範例，請參閱[從 Teradata 平行複製](#parallel-copy-from-teradata)一節。 | 否（如果已指定資料集中的資料表） |
| partitionOptions | 指定用來從 Teradata 載入資料的資料分割選項。 <br>允許值為：**無**（預設值）、 **Hash**和**DynamicRange**。<br>當分割區選項已啟用（也就是不`None`是）時，從 Teradata 並行載入資料的平行處理原則程度，是[`parallelCopies`](copy-activity-performance.md#parallel-copy)由複製活動上的設定所控制。 | 否 |
| partitionSettings | 指定資料分割的設定群組。 <br>當資料分割選項不`None`適用時套用。 | 否 |
| partitionColumnName | 針對 [平行複製的範圍分割或雜湊分割]，指定將使用的來源資料行名稱。 如果未指定，則會自動偵測資料表的主要索引，並使用它做為資料分割資料行。 <br>當資料分割選項為`Hash`或`DynamicRange`時套用。 如果您使用查詢來抓取來源資料，請`?AdfHashPartitionCondition`攔截或`?AdfRangePartitionColumnName`在 WHERE 子句中。 請參閱[Parallel copy From Teradata](#parallel-copy-from-teradata)一節中的範例。 | 否 |
| partitionUpperBound | 用來複製資料的分割區資料行的最大值。 <br>當資料分割選項為`DynamicRange`時套用。 如果您使用 query 來抓取來源資料，請`?AdfRangePartitionUpbound`在 WHERE 子句中掛上。 如需範例，請參閱[Parallel copy From Teradata](#parallel-copy-from-teradata)一節。 | 否 |
| partitionLowerBound | 用來複製資料的分割區資料行的最小值。 <br>適用于資料分割選項為`DynamicRange`時。 如果您使用查詢來抓取來源資料，請在 WHERE `?AdfRangePartitionLowbound`子句中掛上。 如需範例，請參閱[Parallel copy From Teradata](#parallel-copy-from-teradata)一節。 | 否 |

> [!NOTE]
>
> `RelationalSource`仍然支援類型複製來源，但它不支援 Teradata 的新內建平行載入（資料分割選項）。 不過，我們建議您使用新的資料集。

**範例：使用沒有分割區的基本查詢來複製資料**

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

## <a name="parallel-copy-from-teradata"></a>從 Teradata 進行平行複製

Data Factory Teradata 連接器會提供內建的資料分割，以平行方式從 Teradata 複製資料。 您可以在複製活動的**源**資料表上找到資料分割選項。

![資料分割選項的螢幕擷取畫面](./media/connector-teradata/connector-teradata-partition-options.png)

當您啟用資料分割複本時，Data Factory 會針對您的 Teradata 來源執行平行查詢，以依據分割區來載入資料。 平行程度是由複製活動上[`parallelCopies`](copy-activity-performance.md#parallel-copy)的設定所控制。 例如，如果您將設定`parallelCopies`為四，Data Factory 會同時產生並根據您指定的分割區選項和設定執行四個查詢，而且每個查詢都會從您的 Teradata 中抓取部分資料。

建議您啟用具有資料分割的平行複製，特別是當您從 Teradata 載入大量資料時。 以下是適用于不同案例的建議設定。 將資料複製到以檔案為基礎的資料存放區時，會建議寫入資料夾做為多個檔案（僅指定資料夾名稱），在此情況下，效能會比寫入單一檔案更好。

| 狀況                                                     | 建議的設定                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 來自大型資料表的完整載入。                                   | 資料**分割選項**：散列. <br><br/>在執行期間，Data Factory 會自動偵測 PK 資料行、對其套用雜湊，並依資料分割複製資料。 |
| 使用自訂查詢載入大量資料。                 | 資料**分割選項**：散列.<br>**查詢**： `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`。<br>資料**分割資料行**：指定用於套用雜湊資料分割的資料行。 如果未指定，Data Factory 會自動偵測您在 Teradata 資料集中指定之資料表的 PK 資料行。<br><br>在執行期間，Data Factory `?AdfHashPartitionCondition`會以雜湊磁碟分割邏輯取代，並將傳送至 Teradata。 |
| 使用自訂查詢載入大量資料，並具有以平均分佈值進行範圍分割的整數資料行。 | 資料**分割選項**：動態範圍分割。<br>**查詢**： `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`。<br>資料**分割資料行**：指定用來分割資料的資料行。 您可以針對具有整數資料類型的資料行進行分割。<br>**分割區上限**和**分割區下限**：如果您想要針對資料分割資料行進行篩選，以便只在範圍下限和上限之間取得資料，請指定。<br><br>在執行期間，Data Factory `?AdfRangePartitionColumnName`會`?AdfRangePartitionUpbound`將、 `?AdfRangePartitionLowbound`和取代為實際的資料行名稱和每個資料分割的值範圍，並傳送至 Teradata。 <br>例如，如果您將資料分割資料行 "ID" 設定為下限為1，而上限為80，且 parallel copy 設為4，則 Data Factory 會依4個分割區來抓取資料。 其識別碼分別介於 [1，20]、[21，40]、[41、60] 和 [61，80] 之間。 |

**範例：使用雜湊資料分割進行查詢**

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

**範例：使用動態範圍分割進行查詢**

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

當您從 Teradata 複製資料時，會套用下列對應。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

| Teradata 資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| 字元 |String |
| Clob |String |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| 圖形 |不支援。 在來源查詢中套用明確轉換。 |
| Integer |Int32 |
| Interval Day |不支援。 在來源查詢中套用明確轉換。 |
| Interval Day To Hour |不支援。 在來源查詢中套用明確轉換。 |
| Interval Day To Minute |不支援。 在來源查詢中套用明確轉換。 |
| Interval Day To Second |不支援。 在來源查詢中套用明確轉換。 |
| Interval Hour |不支援。 在來源查詢中套用明確轉換。 |
| Interval Hour To Minute |不支援。 在來源查詢中套用明確轉換。 |
| Interval Hour To Second |不支援。 在來源查詢中套用明確轉換。 |
| Interval Minute |不支援。 在來源查詢中套用明確轉換。 |
| Interval Minute To Second |不支援。 在來源查詢中套用明確轉換。 |
| 間隔月 |不支援。 在來源查詢中套用明確轉換。 |
| Interval Second |不支援。 在來源查詢中套用明確轉換。 |
| 間隔年 |不支援。 在來源查詢中套用明確轉換。 |
| 間隔年至月 |不支援。 在來源查詢中套用明確轉換。 |
| Number |Double |
| 期間（日期） |不支援。 在來源查詢中套用明確轉換。 |
| 期間（時間） |不支援。 在來源查詢中套用明確轉換。 |
| 期間（使用時區的時間） |不支援。 在來源查詢中套用明確轉換。 |
| 期間（時間戳記） |不支援。 在來源查詢中套用明確轉換。 |
| Period （含時區的時間戳記） |不支援。 在來源查詢中套用明確轉換。 |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |不支援。 在來源查詢中套用明確轉換。 |
| Xml |不支援。 在來源查詢中套用明確轉換。 |


## <a name="lookup-activity-properties"></a>查閱活動屬性

若要瞭解屬性的詳細資料，請檢查[查閱活動](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>後續步驟
如需 Data Factory 中的複製活動所支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
