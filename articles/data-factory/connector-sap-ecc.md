---
title: 使用 Azure Data Factory 從 SAP ECC 複製資料 |Microsoft Docs
description: 了解如何使用 Azure Data Factory 管線中的複製活動，將資料從 SAP ECC 複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 7ccd2e7a804c6495f6caf5e264b1f7c2a36cb02e
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827776"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 SAP ECC 複製資料

本文概述如何使用 Azure Data Factory 中的 「 複製活動，將資料從 SAP Enterprise Central Component (ECC) 的複製。 如需詳細資訊，請參閱 <<c0> [ 複製活動概觀](copy-activity-overview.md)。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 SAP ECC 複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源或接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，此 SAP ECC 連接器支援：

- 從 SAP NetWeaver 7.0 和更新版本上的 SAP ECC 複製資料。
- 將資料從 SAP ECC OData 服務所公開的這類的任何物件的複製：

  - SAP 資料表或檢視表。
  - 商務應用程式開發介面 [BAPI] 物件。
  - 資料擷取器。
  - 資料或中繼文件 (Idoc) 傳送至 SAP 程序整合 (PI) 可透過相對配接器接收作為 OData。

- 使用基本驗證來複製資料。

>[!TIP]
>若要從 SAP ECC 複製資料，透過 SAP 資料表或檢視，使用[SAP 資料表](connector-sap-table.md)連接器，更快且更具擴充性。

## <a name="prerequisites"></a>先決條件

一般而言，SAP ECC 會透過 OData 服務經由 SAP 閘道公開實體。 若要使用此 SAP ECC 連接器，您必須：

- **設定 SAP 閘道**。 針對具有晚於 7.4 的 SAP NetWeaver 版本的伺服器，已安裝 SAP 閘道。 舊版本中，您必須內嵌的 SAP 閘道或安裝 SAP 閘道中樞系統之前公開 SAP ECC 資料透過 OData 服務。 若要設定 SAP 閘道，請參閱[安裝指南](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm)。

- **啟用及設定 SAP OData 服務**。 您可以啟用透過 TCODE SICF OData 服務，以秒為單位。 您也可以設定哪些物件需要公開。 如需詳細資訊，請參閱 <<c0> [ 逐步指引](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/)。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供有關用來定義 SAP ECC 連接器專屬的 Data Factory 實體的屬性的詳細資料。

## <a name="linked-service-properties"></a>連結服務屬性

SAP ECC 連結服務支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| `type` | `type`屬性必須設為`SapEcc`。 | 是 |
| `url` | SAP ECC OData 服務的 URL。 | 是 |
| `username` | 用來連線至 SAP ECC 的使用者名稱。 | 否 |
| `password` | 用來連線至 SAP ECC 的純文字密碼。 | 否 |
| `connectVia` | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用自我裝載的整合執行階段或 Azure 整合執行階段 （如果您的資料存放區是可公開存取）。 如果您未指定執行階段`connectVia`會使用預設的 Azure 整合執行階段。 | 否 |

### <a name="example"></a>範例

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需的區段和屬性可用來定義資料集的完整清單，請參閱 <<c0> [ 資料集](concepts-datasets-linked-services.md)。 下一節會提供 SAP ECC 資料集所支援的屬性清單。

若要從 SAP ECC 複製資料，設定`type`若要將資料集屬性`SapEccResource`。

以下是支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| `path` | SAP ECC OData 實體的路徑。 | 是 |

### <a name="example"></a>範例

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需的區段和屬性可用來定義活動的完整清單，請參閱 <<c0> [ 管線](concepts-pipelines-activities.md)。 下一節會提供 SAP ECC 來源所支援的屬性清單。

### <a name="sap-ecc-as-a-source"></a>SAP ECC 作為來源

若要從 SAP ECC 複製資料，設定`type`中的屬性`source`之複製活動的區段`SapEccSource`。

以下支援的屬性在複製活動的`source`區段：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| `type` | `type`複製活動的屬性`source`部份必須設定為`SapEccSource`。 | 是 |
| `query` | 要篩選資料的 OData 查詢選項。 例如:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>SAP ECC 連接器會將資料複製從合併的 URL:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>如需詳細資訊，請參閱 [OData URL 元件](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)。 | 否 |

### <a name="example"></a>範例

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>SAP ECC 的資料類型對應

當您要從 SAP ECC 複製資料時，下列對應會用於 OData 資料類型從 SAP ECC 資料到 Azure Data Factory 過渡期資料類型。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

| OData 資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> 目前不支援複雜資料型別。

## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中的複製活動所支援作為來源和接收資料存放區的清單，請參閱 <<c0> [ 支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
