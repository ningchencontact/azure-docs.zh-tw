---
title: 複製活動中的結構描述對應 | Microsoft Docs
description: 深入了解 Azure Data Factory 中的複製活動在複製資料時，如何將來源資料中的結構描述和資料類型對應到接收資料。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 99798b35419ec9574c99aaba42803fbeeb1555f1
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59267118"
---
# <a name="schema-mapping-in-copy-activity"></a>複製活動中的結構描述對應
本文說明 Azure Data Factory 複製活動在執行資料複製時，如何將來源資料中的結構描述和資料類型對應到接收資料。

## <a name="column-mapping"></a>資料行對應

在表格形狀的資料之間複製資料時，適用資料行對應。 根據預設，除非已設定[明確的資料行對應](#explicit-column-mapping)，否則複製活動**會依資料行名稱將來源資料對應到接收**。 更具體來說，複製活動會：

1. 從來源讀取資料，並判斷來源結構描述

    * 對於含有資料存放區/檔案格式之預先定義結構描述的資料來源 (例如含中繼資料 (Avro/ORC/Parquet/含標頭的文字) 的資料庫/檔案)，會從查詢結果或檔案中繼資料擷取來源結構描述。
    * 對於具有彈性結構描述的資料來源 (例如 Azure 資料表/Cosmos DB)，會從查詢結果推斷來源結構描述。 您可以藉由在資料集中設定「結構」以將它覆寫。
    * 對於沒有標頭的文字檔案，會以 Prop_0、Prop_1 的模式產生預設資料行名稱，您可以藉由在資料集中設定「結構」以將它覆寫。
    * 對於動態來源，您必須在資料集「結構」區段中提供結構描述資訊。

2. 如果指定，請套用明確的資料行對應。

3. 撰寫要接收的資料

    * 對具有預先定義結構描述的資料存放區，資料會寫入至具有相同名稱的資料行。
    * 對於沒有固定結構描述的資料存放區及檔案格式，將會根據來源結構描述產生資料行名稱/中繼資料。

### <a name="explicit-column-mapping"></a>明確的資料行對應

您可以在「複製活動」的 **typeProperties**區段中指定 **columnMappings**，以進行明確的資料行對應。 在此案例中，輸入和輸出資料集需要「結構」區段。 資料行對應支援將來源資料集「結構」中的所有或一部分資料行，對應至接收資料集「結構」中的所有資料行。 以下是會導致發生例外狀況的錯誤狀況：

* 來源資料存放區查詢結果在輸入資料集的「結構」區段中並未指定資料行名稱。
* 接收資料存放區 (如果含有預先定義的結構描述) 在輸出資料集的「結構」區段中並未指定資料行名稱。
* 接收資料集「結構」中的資料行數量多於或少於對應中所指定的數量。
* 重複的對應。

#### <a name="explicit-column-mapping-example"></a>明確的資料行對應範例

在此範例中，輸入資料表有一個結構，且指向內部部署 SQL 資料庫中的資料表。

```json
{
    "name": "SqlServerInput",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "SqlServerLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

在此範例中，輸入資料表有一個結構，且指向 Azure SQL 資料庫中的資料表。

```json
{
    "name": "AzureSqlOutput",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

下列 JSON 定義了管線中的複製活動。 來自來源的資料行是使用**轉譯程式**屬性來對應至接收中的資料行 (**columnMappings**)。

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "SqlServerInput",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "AzureSqlOutput",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "SqlSource" },
        "sink": { "type": "SqlSink" },
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

**資料行對應流程：**

![資料行對應流程](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="schema-mapping"></a>結構描述對應

在階層形狀的資料與表格形狀的資料之間複製資料時 (例如，從 MongoDB/REST 複製到文字檔案，以及從 SQL 複製到 Azure Cosmos DB 的 MongoDB 版 API 時)，適用結構描述對應。 複製活動的 `translator` 區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動轉譯程式的類型屬性必須設定為：**TabularTranslator** | 是 |
| schemaMapping | 索引鍵 / 值組的集合表示的對應關聯性**從來源到接收端的側邊**。<br/>- **索引鍵：** 代表來源。 針對**表格式來源**，指定資料行名稱，因為定義資料集結構中; 如**階層式的來源**，指定每個欄位擷取，並將對應的 JSON 路徑運算式。<br/>- **值：** 代表接收。 針對**表格式接收**，指定資料行名稱，因為定義資料集結構中; 如**階層式接收**，指定每個欄位擷取，並將對應的 JSON 路徑運算式。 <br/> JSON 路徑開頭根 $; 若為階層式資料，根物件下的欄位所選陣列內的欄位`collectionReference`屬性中，JSON 路徑的開頭的陣列項目。  | 是 |
| collectionReference | 如果您想要逐一查看**陣列欄位內**相同模式的物件並擷取資料，然後轉換為每個物件一個資料列，則請指定該陣列的 JSON 路徑，以執行交叉套用。 只有在階層式資料是來源時，才支援這個屬性。 | 否 |

**範例： 從 MongoDB 複製到 SQL:**

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

您想要壓平合併陣列內的資料 (order_pd 和 order_price)，將內容複製到下列格式的 Azure SQL 資料表，並與一般根資訊 (編號、日期和城市) 交叉聯結︰

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

如下列複製活動 JSON 範例所示，設定結構描述對應規則：

```json
{
    "name": "CopyFromMongoDBToSqlAzure",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "SqlSink"
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

Data Factory 支援下列過渡資料類型：在[資料集結構](concepts-datasets-linked-services.md#dataset-structure)組態中設定類型資訊時，您可以指定下列值：

* Byte[]
* BOOLEAN
* DateTime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* 字串
* Timespan

### <a name="explicit-data-type-conversion"></a>明確的資料類型轉換

將資料複製到含有固定結構描述 (例如 SQL Server/Oracle) 的資料存放區時，若同一個資料行上的來源和接收類型不同，則應在來源端宣告明確的類型轉換：

* 對於檔案來源 (如 CSV/Avro)，應以完整的資料行清單 (來源端資料行名稱和接收端類型) 透過來源結構宣告類型轉換
* 針對關聯式來源 (例如，SQL/Oracle)，應在查詢陳述式中透過明確的類型轉換來達成類型轉換。

## <a name="when-to-specify-dataset-structure"></a>指定資料集「結構」的時機

在以下情況下，資料集中的「結構」是必要的：

* 複製期間針對檔案來源套用[明確的資料類型轉換](#explicit-data-type-conversion) (輸入資料集)
* 複製期間套用[明確的資料行對應](#explicit-column-mapping) (輸入和輸出資料集)
* 從 Dynamics 365/CRM 來源複製 (輸入資料集)
* 當來源不是 JSON 檔案時，作為巢狀物件複製到 Cosmos DB (輸出資料集)

在以下情況下，建議在資料集中使用「結構」：

* 從沒有標頭的文字檔案複製 (輸入資料集)。 您可以指定與對應接收資料行對齊之文字檔案的資料行名稱，就不用設定明確的資料行對應。
* 從具有彈性結構描述 (例如 Azure 資料表/Cosmos DB (輸入資料集)) 的資料存放區複製，以確保每個活動執行期間複製的是預期的資料 (資料行)，而不是讓複製活動根據最上層的資料列推論結構描述。


## <a name="next-steps"></a>後續步驟
請參閱其他複製活動文章：

- [複製活動概觀](copy-activity-overview.md)
- [複製活動容錯](copy-activity-fault-tolerance.md)
- [複製活動的效能](copy-activity-performance.md)
