---
title: 使用 Azure Data Factory 從 IBM Informix 來源複製資料 |Microsoft Docs
description: 瞭解如何使用 Azure Data Factory 管線中的複製活動，將資料從 IBM Informix 來源複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: 9048653ff32c4a24268dd7b4a2bbdd3904c43b16
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2019
ms.locfileid: "71008944"
---
# <a name="copy-data-from-and-to-ibm-informix-data-stores-using-azure-data-factory"></a>使用 Azure Data Factory 將資料從和複製到 IBM Informix 資料存放區

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 IBM Informix 資料存放區複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Informix 連接器：

- [複製活動](copy-activity-overview.md)與[支援的來源矩陣](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 Informix 來源複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

## <a name="prerequisites"></a>必要條件

若要使用此 Informix 連接器，您必須：

- 設定一個「自我裝載 Integration Runtime」。 如需詳細資料，請參閱[自我裝載 Integration Runtime](create-self-hosted-integration-runtime.md) 一文。
- 在 Integration Runtime 機上安裝資料存放區的 Informix ODBC 驅動程式。 例如，您可以使用驅動程式「IBM INFORMIX Informix DRIVER （64-bit）」。

## <a name="getting-started"></a>使用者入門

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Informix 連接器特定的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Informix 已連結服務支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | 類型屬性必須設定為：**Informix** | 是 |
| connectionString | 不包括認證部分的 ODBC 連接字串。 您可以指定連接字串，或使用您在 Integration Runtime 機上設定的系統 DSN （資料來源名稱）（您仍然需要在連結服務中指定認證部分）。<br>將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。| 是 |
| authenticationType | 用來連接到 Informix 資料存放區的驗證類型。<br/>允許的值包括：**基本**與**匿名**。 | 是 |
| userName | 如果您要使用 Basic 驗證，請指定使用者名稱。 | 否 |
| password | 指定您為 userName 指定之使用者帳戶的密碼。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 否 |
| credential | 以驅動程式特定「屬性-值」格式指定之連接字串的存取認證部分。 請將此欄位標示為 SecureString。 | 否 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 如[必要條件](#prerequisites)所述，必須要有一個「自我裝載 Integration Runtime」。 |是 |

**範例:**

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Informix",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<Informix connection string or DSN>"
            },
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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Informix 資料集所支援的屬性清單。

若要從 Informix 複製資料，支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | 資料集的類型屬性必須設定為：**InformixTable** | 是 |
| tableName | Informix 中的資料表名稱。 | 就來源而言為非必要 (如果已指定活動來源中的「查詢」)；<br/>就接收器而言為必要 |

**範例**

```json
{
    "name": "InformixDataset",
    "properties": {
        "type": "InformixTable",
        "linkedServiceName": {
            "referenceName": "<Informix linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[Pipelines](concepts-pipelines-activities.md)一文。 本節提供 Informix 來源所支援的屬性清單。

### <a name="informix-as-source"></a>Informix 作為來源

若要從 Informix 複製資料，複製活動的 [**來源**] 區段中支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | 複製活動來源的類型屬性必須設定為：**InformixSource** | 是 |
| query | 使用自訂查詢來讀取資料。 例如： `"SELECT * FROM MyTable"` 。 | 否 (如果已指定資料集中的 "tableName") |

**範例:**

```json
"activities":[
    {
        "name": "CopyFromInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Informix input dataset name>",
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
                "type": "InformixSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要瞭解屬性的詳細資料，請檢查[查閱活動](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。
