---
title: 複製活動中的結構描述對應 | Microsoft Docs
description: 深入了解 Azure Data Factory 中的複製活動在複製資料時，如何將來源資料中的結構描述和資料類型對應到接收資料。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 9108f83e854b51720c64c5a74a828543cc5e7688
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64875808"
---
# <a name="schema-mapping-in-copy-activity"></a>複製活動中的結構描述對應

本文說明 Azure Data Factory 複製活動在執行資料複製時，如何將來源資料中的結構描述和資料類型對應到接收資料。

## <a name="schema-mapping"></a>結構描述對應

從來源到接收複製資料時，適用於資料行對應。 根據預設，複製活動**對應到接收的資料行名稱的來源資料**。 您可以指定[明確對應](#explicit-mapping)來自訂您的需求為基礎的資料行對應。 更具體來說，複製活動會：

1. 從來源讀取資料，並判斷來源結構描述
2. 使用預設資料行對應資料行對應名稱，或如果指定，將明確的資料行對應。
3. 撰寫要接收的資料

### <a name="explicit-mapping"></a>明確對應

您可以指定要在複製活動中對應的資料行]-> [ `translator`  ->  `mappings`屬性。 下列範例會定義複製活動的管線，以將資料從分隔的文字複製到 Azure SQL Database 中。

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [{
        "referenceName": "DelimitedTextInput",
        "type": "DatasetReference"
    }],
    "outputs": [{
        "referenceName": "AzureSqlOutput",
        "type": "DatasetReference"
    }],
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": {
                        "name": "UserId",
                        "type": "Guid"
                    },
                    "sink": {
                        "name": "MyUserId"
                    }
                }, 
                {
                    "source": {
                        "name": "Name",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyName"
                    }
                }, 
                {
                    "source": {
                        "name": "Group",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyGroup"
                    }
                }
            ]
        }
    }
}
```

以下支援的屬性底下`translator`  ->  `mappings` ]-> [物件`source`和`sink`:

| 屬性 | 描述                                                  | 必要項 |
| -------- | ------------------------------------------------------------ | -------- |
| name     | 來源或接收器的資料行名稱。                           | 是      |
| ordinal  | 資料行索引。 從 1 開始。 <br>套用並使用分隔標頭行沒有文字時所需。 | 否       |
| path     | 每個欄位來擷取，或對應的 JSON 路徑運算式。 適用於階層式資料例如 MongoDB/REST。<br>根物件下的欄位，JSON 路徑的開頭根 $;所選陣列內的欄位`collectionReference`屬性中，JSON 路徑的開頭的陣列項目。 | 否       |
| type     | Data Factory 過渡期資料類型的來源或接收器的資料行。 | 否       |
| culture  | 來源或接收器的資料行的文化特性。 <br>類型是時，適用`Datetime`或`Datetimeoffset`。 預設為 `en-us`。 | 否       |
| format   | 格式字串類型時，使用`Datetime`或`Datetimeoffset`。 有關如何格式化日期時間的資訊，請參閱[自訂日期和時間格式字串](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 | 否       |

以下支援的屬性底下`translator`  ->  `mappings`除了物件`source`和`sink`:

| 屬性            | 描述                                                  | 必要項 |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | 支援階層式資料，例如 MongoDB/REST 時才來源。<br>如果您想要逐一查看**陣列欄位內**相同模式的物件並擷取資料，然後轉換為每個物件一個資料列，則請指定該陣列的 JSON 路徑，以執行交叉套用。 | 否       |

### <a name="alternative-column-mapping"></a>替代的資料行對應

您可以指定複製活動-> `translator`  ->  `columnMappings`表格式圖形化資料之間的對應。 在此情況下，為必要的輸入和輸出資料集 「 結構 」 區段。 資料行對應支援將來源資料集「結構」中的所有或一部分資料行，對應至接收資料集「結構」中的所有資料行  。 以下是會導致發生例外狀況的錯誤狀況：

* 來源資料存放區查詢結果在輸入資料集的「結構」區段中並未指定資料行名稱。
* 接收資料存放區 (如果含有預先定義的結構描述) 在輸出資料集的「結構」區段中並未指定資料行名稱。
* 接收資料集「結構」中的資料行數量多於或少於對應中所指定的數量。
* 重複的對應。

在下列範例中，輸入資料集有一個結構，並指向內部部署 Oracle 資料庫中的資料表。

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

在此範例中，輸出資料集都有一個結構，並指向 Salesfoce 中的資料表。

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

下列 JSON 定義了管線中的複製活動。 使用對應至接收器中的資料行的來源資料行**translator** -> **columnMappings**屬性。

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

如果您正在使用 `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` 的語法指定資料行對應，則仍會依原狀支援該對應。

### <a name="alternative-schema-mapping"></a>替代的結構描述對應

您可以指定複製活動-> `translator`  ->  `schemaMapping`對應階層式圖形化的資料與表格式圖形化的資料，例如複製 MongoDB/REST 到文字檔案和從 Oracle 複製到 Azure Cosmos DB 的 API for MongoDB。 複製活動的 `translator` 區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動轉譯程式的類型屬性必須設定為：**TabularTranslator** | 是 |
| schemaMapping | 索引鍵 / 值組的集合表示的對應關聯性**從來源到接收端的側邊**。<br/>- **索引鍵：** 代表來源。 針對**表格式來源**，指定資料行名稱，因為定義資料集結構中; 如**階層式的來源**，指定每個欄位擷取，並將對應的 JSON 路徑運算式。<br>- **值：** 代表接收。 針對**表格式接收**，指定資料行名稱，因為定義資料集結構中; 如**階層式接收**，指定每個欄位擷取，並將對應的 JSON 路徑運算式。 <br>JSON 路徑開頭根 $; 若為階層式資料，根物件下的欄位所選陣列內的欄位`collectionReference`屬性中，JSON 路徑的開頭的陣列項目。  | 是 |
| collectionReference | 如果您想要逐一查看**陣列欄位內**相同模式的物件並擷取資料，然後轉換為每個物件一個資料列，則請指定該陣列的 JSON 路徑，以執行交叉套用。 只有在階層式資料是來源時，才支援這個屬性。 | 否 |

**範例： 從 MongoDB 複製到 Oracle:**

例如，如果您有具備下列內容的 MongoDB 文件：

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

您想要壓平合併陣列內的資料 (order_pd 和 order_price)  ，將內容複製到下列格式的 Azure SQL 資料表，並與一般根資訊 (編號、日期和城市)  交叉聯結︰

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

如下列複製活動 JSON 範例所示，設定結構描述對應規則：

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "orderNumber": "$.number",
                "orderDate": "$.date",
                "order_pd": "prod",
                "order_price": "price",
                "city": " $.city[0].name"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>資料類型對應

複製活動會使用下列 2 個步驟的方法，將來源類型對應至接收類型：

1. 從原生來源類型轉換成 Azure Data Factory 過渡資料類型
2. 從 Azure Data Factory 過渡資料類型轉換成原生接收類型

您可以在每個連接器主題的＜資料類型對應＞一節中找到原生類型和過渡類型之間的對應。

### <a name="supported-data-types"></a>支援的資料類型

Data Factory 支援下列過渡資料類型：在[資料集結構](concepts-datasets-linked-services.md#dataset-structure-or-schema)組態中設定類型資訊時，您可以指定下列值：

* Byte[]
* Boolean
* Datetime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* String
* Timespan

## <a name="next-steps"></a>後續步驟
請參閱其他複製活動文章：

- [複製活動概觀](copy-activity-overview.md)
