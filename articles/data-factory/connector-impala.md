---
title: 使用 Azure Data Factory 從 Impala 複製資料 (預覽) | Microsoft Docs
description: 了解如何使用資料處理站管線中的複製活動，將資料從 Impala 複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 01712508b15be947f4c3d7df401ad431d9f25455
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70276388"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory-preview"></a>使用 Azure Data Factory 從 Impala 複製資料 (預覽)

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Impala 複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

> [!IMPORTANT]
> 此連接器目前為預覽版。 您可以親身體驗並提供意見反應。 如果您需要依賴解決方案中的預覽連接器，請連絡 [Azure 支援](https://azure.microsoft.com/support/)。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 Impala 複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源或接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

Data Factory 會提供內建的驅動程式來啟用連線。 因此，您不需要為了使用此連接器而需手動安裝驅動程式。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Impala 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Impala 連結服務所支援的屬性。

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | type 屬性必須設定為 **Impala**。 | 是 |
| host | Impala 伺服器的 IP 位址或主機名稱 (也就是 192.168.222.160)。  | 是 |
| port | Impala 伺服器用來接聽用戶端連線的 TCP 連接埠。 預設值為 21050。  | 否 |
| authenticationType | 要使用的驗證類型。 <br/>允許的值為 **Anonymous**、**SASLUsername** 和 **UsernameAndPassword**。 | 是 |
| username | 用來存取 Impala 伺服器的使用者名稱。 使用 SASLUsername 時，預設值為 anonymous。  | 否 |
| password | 使用 UsernameAndPassword 時，值為對應到使用者名稱的密碼。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 否 |
| enableSsl | 指定是否使用 SSL 來加密與伺服器的連線。 預設值為 **false**。  | 否 |
| trustedCertPath | .pem 檔案的完整路徑，其中包含在透過 SSL 連線時，用來驗證伺服器的受信任 CA 憑證。 只有當您在自我裝載 Integration Runtime 使用 SSL 時，才能設定這個屬性。 預設值為隨整合執行階段安裝的 cacerts.pem 檔案。  | 否 |
| useSystemTrustStore | 指定是否使用來自系統信任存放區或來自指定 PEM 檔案的 CA 憑證。 預設值為 **false**。  | 否 |
| allowHostNameCNMismatch | 指定在透過 SSL 連線時，是否要求 CA 所核發的 SSL 憑證名稱符合伺服器的主機名稱。 預設值為 **false**。  | 否 |
| allowSelfSignedServerCert | 指定是否允許來自伺服器的自我簽署憑證。 預設值為 **false**。  | 否 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 深入瞭解[必要條件](#prerequisites)一節。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

**範例:**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Impala 資料集所支援的屬性清單。

若要從 Impala 複製資料，請將資料集的類型屬性設定為 **ImpalaObject**。 以下是支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | 資料集的類型屬性必須設定為：**ImpalaObject** | 是 |
| schema | 架構的名稱。 |否 (如果已指定活動來源中的"query")  |
| table | 資料表的名稱。 |否 (如果已指定活動來源中的"query")  |
| tableName | 具有架構之資料表的名稱。 此屬性支援回溯相容性。 針對`schema`新`table`的工作負載使用和。 | 否 (如果已指定活動來源中的"query") |

**範例**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[Pipelines](concepts-pipelines-activities.md)一文。 本節提供 Impala 來源類型所支援的屬性清單。

### <a name="impala-as-a-source-type"></a>Impala 作為來源類型

若要從 Impala 複製資料，請將複製活動中的來源類型設定為 **ImpalaSource**。 複製活動的 [來源] 區段支援下列屬性。

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | 複製活動來源的 type 屬性必須設定為 **ImpalaSource**。 | 是 |
| query | 使用自訂 SQL 查詢來讀取資料。 例如 `"SELECT * FROM MyTable"`。 | 否 (如果已指定資料集中的 "tableName") |

**範例:**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
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
如需 Data Factory 中的複製活動所支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
