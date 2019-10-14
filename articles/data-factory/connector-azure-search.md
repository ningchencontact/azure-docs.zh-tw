---
title: 使用 Azure Data Factory 將資料複製到搜尋服務索引 | Microsoft Docs
description: 了解如何使用 Azure Data Factory 管線中的「複製活動」，將資料推送或複製到 Azure 搜尋服務索引。
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
ms.openlocfilehash: 703dcb5fee0c31328eee91c9673e62653d7aa698
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286335"
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure 搜尋服務索引

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-azure-search-connector.md)
> * [目前的版本](connector-azure-search.md)

本文概述如何使用 Azure Data Factory 中的「複製活動」，將資料複製到「Azure 搜尋服務」索引。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從任何支援的來源資料存放區複製到「Azure 搜尋服務」索引。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

## <a name="getting-started"></a>使用者入門

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義「Azure 搜尋服務」連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對「Azure 搜尋服務」已連結服務支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| 型別 | 類型屬性必須設定為：**AzureSearch** | 是 |
| url | Azure 搜尋服務的 URL。 | 是 |
| key | Azure 搜尋服務的系統管理金鑰。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或「自我裝載 Integration Runtime」(如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

> [!IMPORTANT]
> 將資料從雲端資料存放區複製到 Azure 搜尋服務索引時，在 Azure 搜尋服務連結服務中，您需要在 connactVia 中參考明確區域的 Azure Integration Runtime。 請將區域設定為您「Azure 搜尋服務」所在的區域。 請參閱 [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime) 以深入了解。

**範例:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供「Azure 搜尋服務」資料集所支援的屬性清單。

若要將資料複製到 Azure 搜尋服務，支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| 型別 | 資料集的類型屬性必須設定為：**AzureSearchIndex** | 是 |
| IndexName | Azure 搜尋服務索引的名稱。 Data Factory 不會建立索引。 索引必須存在於 Azure 搜尋服務中。 | 是 |

**範例:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "typeProperties" : {
            "indexName": "products"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[Pipelines](concepts-pipelines-activities.md)一文。 本節提供「Azure 搜尋服務」來源所支援的屬性清單。

### <a name="azure-search-as-sink"></a>Azure 搜尋服務作為接收器

若要將資料複製到「Azure 搜尋服務」，請將複製活動中的來源類型設定為 **AzureSearchIndexSink**。 複製活動的 **sink** 區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| 型別 | 複製活動來源的類型屬性必須設定為：**AzureSearchIndexSink** | 是 |
| writeBehavior | 指定若文件已經存在於索引中，是否要合併或取代。 請參閱 [WriteBehavior 屬性](#writebehavior-property)。<br/><br/>允許的值包括：**Merge** (預設值) 和 **Upload**。 | 否 |
| writeBatchSize | 當緩衝區大小達到 writeBatchSize 時，將資料上傳至 Azure 搜尋服務中。 如需詳細資訊，請參閱 [WriteBatchSize 屬性](#writebatchsize-property)。<br/><br/>允許的值為：整數 1 到 1,000；預設值為 1000。 | 否 |

### <a name="writebehavior-property"></a>WriteBehavior 屬性

AzureSearchSink 會在寫入資料時更新插入。 換句話說，在撰寫文件時，如果文件索引鍵已經存在於 Azure 搜尋服務索引中，Azure 搜尋服務就會更新現有的文件，而不是擲回衝突例外狀況。

AzureSearchSink (藉由使用 AzureSearch SDK) 提供下列兩種更新插入行為：

- **合併**︰將新文件中的所有資料行與現有的文件相結合。 對於新文件中含有 null 值的資料行，則會保留現有文件中的值。
- **上傳**：新的文件會取代現有的文件。 針對新文件中未指定的資料行，不論現有的文件中是否具有非 null 的值，都會將值設為 null。

預設行為是**合併**。

### <a name="writebatchsize-property"></a>WriteBatchSize 屬性

Azure 搜尋服務支援批次寫入文件。 一個批次可包含 1 到 1,000 個動作。 一個動作可指示一份文件來執行上傳/合併作業。

**範例:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

## <a name="data-type-support"></a>資料類型支援

下表指出是否支援 Azure 搜尋服務資料類型。

| Azure 搜尋服務資料類型 | 在 Azure 搜尋服務接收器中受到支援 |
| ---------------------- | ------------------------------ |
| String | Y |
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| Boolean | Y |
| DataTimeOffset | Y |
| String Array | N |
| GeographyPoint | N |

目前不支援其他資料類型（例如 ComplexType）。 如需 Azure 搜尋服務支援之資料類型的完整清單，請參閱[支援的資料類型（Azure 搜尋服務）](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)。

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。
