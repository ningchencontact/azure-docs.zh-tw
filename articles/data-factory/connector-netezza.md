---
title: 使用 Azure Data Factory 從 Netezza 複製資料 | Microsoft Docs
description: 了解如何使用 Azure Data Factory 管線中的複製活動，從 Netezza 將資料複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: jingwang
ms.openlocfilehash: 1b7499990a049f276bf1af9e31b639ea4944d8f7
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167559"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 Netezza 複製資料 

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Netezza 複製資料。 本文是以 [Azure Data Factory 中的複製活動](copy-activity-overview.md)為基礎，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 Netezza 複製到任何支援的接收資料存放區。 如需複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

Azure Data Factory 會提供內建的驅動程式來啟用連線。 您不需要為了使用此連接器而需手動安裝驅動程式。

## <a name="get-started"></a>開始使用

您可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure Resource Manager 範本來建立使用複製活動的管線。 如需逐步指示來了解如何建立內含複製活動的管線，請參閱[複製活動教學課程](quickstart-create-data-factory-dot-net.md)。

下列各節提供屬性的相關詳細資料，您可使用這些屬性來定義 Netezza 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Netezza 連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | **type** 屬性必須設為 **Netezza**。 | 是 |
| connectionString | 連線到 Netezza 的 ODBC 連接字串。 將此欄位標記為 **SecureString** 類型，將它安全地儲存在 Data Factory 中。 您也可以[參考 Azure Key Vault 中儲存的認證](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以選擇自我裝載整合執行階段或 Azure Integration Runtime (如果您的資料存放區可公開存取)。 如果未指定，則會使用預設的 Azure Integration Runtime。 |否 |

一般的連接字串為 `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`。 下表描述更多您可設定的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| SecurityLevel | 驅動程式用來連線到資料存放區的安全性 (SSL/TLS) 層級。 範例： `SecurityLevel=preferredSecured`. 支援的值包括：<br/>- **僅限未受保護連線** (**onlyUnSecured**)：驅動程式不會使用 SSL。<br/>- **偏好未受保護連線 (preferredUnSecured) (預設值)**：如果伺服器提供選擇，則驅動程式不使用 SSL。 <br/>- **偏好受保護連線 (preferredSecured)**：如果伺服器提供選擇，則驅動程式會使用 SSL。 <br/>- **僅限受保護連線 (onlySecured)**：除非有 SSL 連線，否則驅動程式不會連線。 | 否 |
| CaCertFile | 伺服器所用 SSL 憑證的完整路徑。 範例： `CaCertFile=<cert path>;`| 是，如果已啟用 SSL |

**範例**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
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

本節提供 Netezza 資料集所支援的屬性清單。

如需定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)。 

若要從 Netezza 複製資料，請將資料集的 **type** 屬性設定為 **NetezzaTable**。 在此類型的資料集中，沒有任何其他類型特定的屬性。

**範例**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

本節提供 Netezza 來源所支援的屬性清單。

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)。 

### <a name="netezza-as-source"></a>Netezza 作為來源

若要從 Netezza 複製資料，請將複製活動中的**來源**類型設定為 **NetezzaSource**。 複製活動的 [來源] 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 **type** 屬性必須設定為 **NetezzaSource**。 | 是 |
| query | 使用自訂 SQL 查詢來讀取資料。 範例： `"SELECT * FROM MyTable"` | 是 |

**範例：**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
