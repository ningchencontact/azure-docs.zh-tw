---
title: 使用 Data Factory 將資料複製到 Azure Cosmos DB (SQL API) 或從該處複製資料 | Microsoft Docs
description: 了解如何使用 Data Factory 將資料從支援的來源資料存放區複製到 Azure Cosmos DB (SQL API)，或從 Azure Cosmos DB (SQL API) 複製到支援的接收存放區。
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 6dd7707c489bbbad7a97a0ec0a76e7c631bd1465
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359250"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure Cosmos DB (SQL API) 或從該處複製資料

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [第 1 版](v1/data-factory-azure-documentdb-connector.md)
> * [目前的版本](connector-azure-cosmos-db.md)

本文概述如何使用 Azure Data Factory 中的複製活動，從 Azure Cosmos DB (SQL API) 複製資料以及將資料複製到其中。 本文是以 [Azure Data Factory 中的複製活動](copy-activity-overview.md)為基礎，該文提供複製活動的一般概觀。

>[!NOTE]
>此連接器只支援將資料複製到 Cosmos DB SQL API 或從該處複製資料。 針對 MongoDB，請參閱[適用於 MongoDB 的 Azure Cosmos DB API 連接器](connector-azure-cosmos-db-mongodb-api.md)。 目前不支援其他 API 類型。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 Azure Cosmos DB (SQL API) 複製到任何支援的接收資料存放區，或將資料從任何支援的來源資料存放區複製到 Azure Cosmos DB (SQL API)。 如需複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

您可以使用 Azure Cosmos DB (SQL API) 連接器來：

- 從 Azure Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction) 複製資料或將資料複製到其中。
- 寫入 Azure Cosmos DB 作為 **insert** 或 **upsert**。
- 依原樣匯入和匯出 JSON 文件，或從表格式資料集複製資料或將資料複製到其中。 範例包括 SQL 資料庫和 CSV 檔案。 若要依原樣將文件複製到 JSON 檔案或另一個 Azure Cosmos DB 集合或複製其中的文件，請參閱[匯入或匯出 JSON 文件](#importexport-json-documents)。

Data Factory 可與 [Azure Cosmos DB 大量執行程式庫](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)整合，以在寫入至 Azure Cosmos DB 時提供最理想的效能。

> [!TIP]
> [資料移轉影片](https://youtu.be/5-SRNiC_qOU)會引導您完成將資料從 Azure Blob 儲存體複製到 Azure Cosmos DB 的步驟。 此影片也會說明在一般情況下將資料擷取到 Azure Cosmos DB 的效能調整考量。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，您可使用這些屬性來定義 Azure Cosmos DB (SQL API) 專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Azure Cosmos DB (SQL API) 連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | **type** 屬性必須設定為 **CosmosDb**.。 | 是 |
| connectionString |指定連線到 Azure Cosmos DB 資料庫所需的資訊。<br /><br />**注意**：您必須在連接字串中指定資料庫資訊，如接下來的範例所示。 將此欄位標記為 **SecureString** 類型，將它安全地儲存在 Data Factory 中。 您也可以[參考 Azure Key Vault 中儲存的認證](store-credentials-in-key-vault.md)。 |是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載整合執行階段 (如果您的資料存放區位於私人網路中)。 如果未指定此屬性，則會使用預設的 Azure Integration Runtime。 |否 |

**範例**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
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

本節提供 Azure Cosmos DB (SQL API) 資料集所支援的屬性清單。 

如需定義資料集的區段和屬性完整清單，請參閱[資料集和連結服務](concepts-datasets-linked-services.md)。 

若要從 Azure Cosmos DB (SQL API) 複製資料或將資料複製到其中，請將資料集的 **type** 屬性設定為 **DocumentDbCollection**。 以下是支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的 **type** 屬性必須設定為 **DocumentDbCollection**。 |是 |
| collectionName |Azure Cosmos DB 文件集合的名稱。 |是 |

**範例**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Data factory 的結構描述

針對無結構描述的資料存放區 (例如 Azure Cosmos DB)，複製活動會以下列清單中描述的其中一種方式推斷結構描述。 除非您想要[依原樣匯入或匯出 JSON 文件](#import-or-export-json-documents)，否則最佳做法是在 **structure** 區段中指定資料的結構。

* 如果您是使用資料集定義中的 **structure** 屬性來定義結構，Data Factory 會將此結構接受為結構描述。 

    如果資料列的資料行沒有值，系統就會為資料行值提供 null 值。
* 如果您未使用資料集定義中的 **structure** 屬性來指定結構，Data Factory 服務會使用資料的第一列來推斷結構描述。 

    如果第一個資料列不包含完整的結構描述，某些資料行會因複製作業而遺失。

## <a name="copy-activity-properties"></a>複製活動屬性

本節提供 Azure Cosmos DB (SQL API) 來源和接收端所支援的屬性清單。

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)。

### <a name="azure-cosmos-db-sql-api-as-source"></a>將 Azure Cosmos DB (SQL API) 當作來源

若要從 Azure Cosmos DB (SQL API) 複製資料，請將複製活動中的**來源**類型設定為 **DocumentDbCollectionSource**。 

複製活動的 [來源] 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 **type** 屬性必須設定為 **DocumentDbCollectionSource**。 |是 |
| query |指定 Azure Cosmos DB 查詢來讀取資料。<br/><br/>範例：<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |否 <br/><br/>如果未指定，則會執行此 SQL 陳述式：`select <columns defined in structure> from mycollection` |
| nestingSeparator |用來指出文件為巢狀文件及如何將結果集壓平合併的特殊字元。<br/><br/>例如，當 **nestedSeparator** 值是 **.** (點) 時，如果 Cosmos DB 查詢傳回巢狀結果 `"Name": {"First": "John"}`，則複製活動會將資料行名稱識別為 `Name.First` 且值為 "John" 。 |否<br />(預設值為 **.** (點)) |

**範例**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-sql-api-as-sink"></a>將 Azure Cosmos DB (SQL API) 當作接收端

若要將資料複製到 Azure Cosmos DB (SQL API)，請將複製活動中的**接收**類型設定為 **DocumentDbCollectionSink**。 

複製活動的 [來源] 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收的 **type** 屬性必須設定為 **DocumentDbCollectionSink**。 |是 |
| writeBehavior |描述如何將資料寫入至 Azure Cosmos DB。 允許的值：**insert** 和 **upsert**。<br/><br/>如果存在具有相同識別碼的文件，**upsert** 的行為會用來取代文件；否則會插入文字。<br /><br />**注意**：如果未在原始文件中或藉由資料行對應來指定識別碼，則 Data Factory 會自動為文件產生識別碼。 這表示您必須確定，為了讓 **upsert** 如預期般運作，您的文件具有識別碼。 |否<br />(預設值為 **insert**) |
| writeBatchSize | Data Factory 會使用 [Azure Cosmos DB 大量執行程式庫](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)將資料寫入 Cosmos DB 中。 **writeBatchSize** 可控制 ADF 提供給程式庫的文件大小。 您可以嘗試增加 **writeBatchSize** 值來改善效能，而如果您的文件大小很大，請嘗試增加此值 - 請參閱以下祕訣。 |否<br />(預設值為 **10,000**) |
| nestingSeparator |**source** 資料行名稱中用來表示需要巢狀文件的特殊字元。 <br/><br/>例如，當 **nestedSeparator** 是 **.** (點) 時，輸出資料集結構中的 `Name.First` 會在 Azure Cosmos DB 文件中產生下列 JSON 結構： `"Name": {"First": "[value maps to this column from source]"}`  |否<br />(預設值為 **.** (點)) |

>[!TIP]
>Cosmos DB 會將單一要求的大小限制為 2MB。 公式為要求大小 = 單一文件大小 * 寫入批次大小。 如果您遇到指出「要求大小太大。」的錯誤，請在複製接收組態中**縮小 `writeBatchSize` 值**。

**範例**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-or-export-json-documents"></a>匯入或匯出 JSON 文件

您可以使用 Azure Cosmos DB (SQL API) 連接器輕鬆地：

* 將 JSON 文件從各種來源匯入到 Azure Cosmos DB，包括 Azure Blob 儲存體、Azure Data Lake Store 及 Azure Data Factory 支援的其他檔案型存放區。
* 將 JSON 文件從 Azure Cosmos DB 集合匯出至各種檔案型存放區。
* 在兩個 Azure Cosmos DB 集合之間依原樣複製文件。

若要達成無從驗證結構描述的複製：

* 當您使用複製資料工具時，請選取 [依原樣匯出到 JSON 檔案或 Cosmos DB 集合] 選項。
* 當您使用活動撰寫功能時，請勿在 Azure Cosmos DB 資料集中指定 **structure** (也稱為 schema) 區段。 此外，請勿在複製活動的 Azure Cosmos DB 來源或接收端中指定 **nestingSeparator** 屬性。 當您從 JSON 檔案匯入或匯出到這些檔案時，請在對應的檔案存放區資料集中，將 **format** 類型指定為 **JsonFormat** 以及設定 **filePattern** (如 [JSON 格式](supported-file-formats-and-compression-codecs.md#json-format)一節所述)。 然後，請勿指定 **structure** 區段並略過其餘的格式設定。

## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。
