---
title: "將資料從使用 Azure Data Factory (Beta) 的 Impala 複製 |Microsoft 文件"
description: "了解如何將資料從 Impala 複製到支援的接收資料存放區，在 Azure Data Factory 管線中使用複製活動。"
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
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: ff6d970b8d4bb5328eb958acc652ba05e1c8be5f
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="copy-data-from-impala-using-azure-data-factory-beta"></a>從使用 Azure Data Factory (Beta) 的 Impala 複製資料

本文將概述如何使用 Azure Data Factory 中的複製活動，從 Impala 複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務，也就是正式推出 (GA) 的版本，請參閱[第 1 版的複製活動](v1/data-factory-data-movement-activities.md)。

> [!IMPORTANT]
> 此連接器目前為 beta 版。 您可以現在就試試看，並提供意見反應。 請勿使用它在生產環境中。

## <a name="supported-capabilities"></a>支援的功能

您可以從 Impala 資料複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

Azure Data Factory 提供的內建驅動程式可啟用連線，因此使用此連接器您不需要手動安裝任何驅動程式。

## <a name="getting-started"></a>開始使用

您可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure Resource Manager 範本來建立具有複製活動的管線。 如需建立內含複製活動之管線的逐步指示，請參閱[複製活動教學課程](quickstart-create-data-factory-dot-net.md)。

下列各節提供有關用來定義特定的 Data Factory 實體至 Impala 連接器屬性詳細資料。

## <a name="linked-service-properties"></a>連結服務屬性

Impala 連結服務支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為： **Impala** | 是 |
| host | Impala 伺服器 IP 位址或主機名稱。 （這是 192.168.222.160）  | 是 |
| 連接埠 | Impala 伺服器用來接聽用戶端連線之 TCP 通訊埠。 預設值是 21050。  | 否 |
| authenticationType | 要使用的驗證類型。 <br/>允許的值為：**匿名**， **SASLUsername**， **UsernameAndPassword** | 是 |
| username | 用來存取 Impala 伺服器使用者名稱。 使用 SASLUsername 時，預設值是匿名的。  | 否 |
| password | 使用 UsernameAndPassword 時，對應到使用者名稱密碼。 您可以選擇將這個欄位標記以 securestring 的形式將它安全地儲存在 ADF，或將密碼儲存在 Azure 金鑰保存庫，而且可讓複製活動時執行資料複製，從中提取-進一步了解從[將認證儲存在金鑰保存庫](store-credentials-in-key-vault.md)。 | 否 |
| enableSsl | 指定伺服器的連接是否使用 SSL 進行加密。 預設值為 False。  | 否 |
| trustedCertPath | 這個.pem 檔案，包含受信任的 CA 憑證，透過 SSL 連線時，驗證伺服器的完整路徑。 這個屬性可以只在自我裝載的紅外線上使用 SSL 時設定 預設值是隨紅外線 cacerts.pem 檔案  | 否 |
| useSystemTrustStore | 指定是否要使用的 CA 憑證，從系統信任存放區，或從指定的 PEM 檔案。 預設值為 False。  | 否 |
| allowHostNameCNMismatch | 指定是否需要符合伺服器的主機名稱，透過 SSL 連線時的 CA 所核發的 SSL 憑證名稱。 預設值為 False。  | 否 |
| allowSelfSignedServerCert | 指定是否要允許從伺服器的自我簽署的憑證。 預設值為 False。  | 否 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 您可以使用「自我裝載 Integration Runtime」或 Azure Integration Runtime (如果您的資料存放區是可公開存取的)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

**範例：**

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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供一份 Impala 資料集所支援的屬性。

若要從 Impala 複製資料，設定要的資料集的類型屬性**ImpalaObject**。 沒有任何額外的特定類型的屬性，在這種類型的資料集。

**範例**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供一份 Impala 來源所支援的屬性。

### <a name="impala-as-source"></a>Impala 做為來源

若要從 Impala 複製資料，請將來源類型複製活動中**ImpalaSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為： **ImpalaSource** | 是 |
| query | 使用自訂 SQL 查詢來讀取資料。 例如：`"SELECT * FROM MyTable"`。 | 是 |

**範例：**

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
對於一份資料會儲存在 Azure Data Factory 中支援的資料存放區，請參閱[支援資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
