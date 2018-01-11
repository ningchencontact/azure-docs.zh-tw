---
title: "將資料從使用 Azure Data Factory (Beta) 的正方形複製 |Microsoft 文件"
description: "了解如何將資料從方複製到支援的接收資料存放區，在 Azure Data Factory 管線中使用複製活動。"
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
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: 08b931c7c172578191cbc2628d8fabca0984a6e0
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-square-using-azure-data-factory-beta"></a>從使用 Azure Data Factory (Beta) 的正方形複製資料

本文將概述如何使用 Azure Data Factory 中的複製活動，從方複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務，也就是正式推出 (GA) 的版本，請參閱[第 1 版的複製活動](v1/data-factory-data-movement-activities.md)。

> [!IMPORTANT]
> 此連接器目前為 beta 版。 您可以現在就試試看，並提供意見反應。 請勿使用它在生產環境中。

## <a name="supported-capabilities"></a>支援的功能

您可以從方資料複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

Azure Data Factory 提供的內建驅動程式可啟用連線，因此使用此連接器您不需要手動安裝任何驅動程式。

## <a name="getting-started"></a>開始使用

您可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure Resource Manager 範本來建立具有複製活動的管線。 如需建立內含複製活動之管線的逐步指示，請參閱[複製活動教學課程](quickstart-create-data-factory-dot-net.md)。

下列各節提供有關用來定義特定的 Data Factory 實體至正方形連接器屬性詳細資料。

## <a name="linked-service-properties"></a>連結服務屬性

正方形連結的服務支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為：**正方形** | 是 |
| host | 正方形的執行個體的 URL。 (也就是 mystore.mysquare.com)  | 是 |
| clientId | 正方形應用程式相關聯的用戶端識別碼。  | 是 |
| clientSecret | 正方形應用程式相關聯的用戶端密碼。 您可以選擇將這個欄位標記以 securestring 的形式將它安全地儲存在 ADF，或將密碼儲存在 Azure 金鑰保存庫，而且可讓複製活動時執行資料複製，從中提取-進一步了解從[將認證儲存在金鑰保存庫](store-credentials-in-key-vault.md)。 | 是 |
| redirectUri | 重新導向 URL 指派方形的應用程式儀表板中。 (也就是 http://localhost:2500)  | 是 |
| useEncryptedEndpoints | 指定資料來源端點是否使用 HTTPS 進行加密。 預設值為 true。  | 否 |
| useHostVerification | 指定是否需要透過 SSL 連線時，符合伺服器的主機名稱的伺服器憑證中的主機名稱。 預設值為 true。  | 否 |
| usePeerVerification | 指定是否要透過 SSL 連線時，請確認伺服器身分識別。 預設值為 true。  | 否 |

**範例：**

```json
{
    "name": "SquareLinkedService",
    "properties": {
        "type": "Square",
        "typeProperties": {
            "host" : "mystore.mysquare.com",
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "redirectUri" : "http://localhost:2500"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供一份方形的資料集所支援的屬性。

若要從方複製資料，設定要的資料集的類型屬性**SquareObject**。 沒有任何額外的特定類型的屬性，在這種類型的資料集。

**範例**

```json
{
    "name": "SquareDataset",
    "properties": {
        "type": "SquareObject",
        "linkedServiceName": {
            "referenceName": "<Square linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供一份正方形來源所支援的屬性。

### <a name="squaresource-as-source"></a>做為來源 SquareSource

若要從方複製資料，將來源類型複製活動中**SquareSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為： **SquareSource** | 是 |
| query | 使用自訂 SQL 查詢來讀取資料。 例如：`"SELECT * FROM Business"`。 | 是 |

**範例：**

```json
"activities":[
    {
        "name": "CopyFromSquare",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Square input dataset name>",
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
                "type": "SquareSource",
                "query": "SELECT * FROM Business"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
