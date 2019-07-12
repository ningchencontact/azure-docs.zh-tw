---
title: 使用 Azure Data Factory 從 SAP 資料表複製資料 |Microsoft Docs
description: 了解如何將資料從 SAP 資料表複製到支援的接收資料存放區，藉由使用 Azure Data Factory 管線中的 「 複製活動 」。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: jingwang
ms.openlocfilehash: 9216f5c00cbdac273b562736abdd1c812d172237
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827760"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 SAP 資料表複製資料

本文概述如何使用 Azure Data Factory 中的 「 複製活動，從 SAP 資料表複製資料。 如需詳細資訊，請參閱 <<c0> [ 複製活動概觀](copy-activity-overview.md)。

## <a name="supported-capabilities"></a>支援的功能

您可以從 SAP 資料表將資料複製到任何支援的接收資料存放區中。 如需複製活動所支援作為來源或接收器的資料存放區的清單，請參閱 <<c0> [ 支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)資料表。

具體來說，此 SAP 資料表 」 連接器支援：

- 將資料從 SAP 資料表中的複製：

  - SAP ERP 中央元件 (SAP ECC) 7.01 或更新版本 （最新 SAP 支援封裝堆疊在 2015年之後釋出） 的版本。
  - SAP Business Warehouse (SAP BW) 7.01 或更新版本。
  - SAP S/4HANA。
  - 在 SAP Business Suite 7.01 或更新版本中的其他產品。

- 從 SAP 透明資料表、 集區的資料表、 叢集的資料表和檢視表複製資料。
- 如果 SNC 設定，請使用基本驗證或安全網路通訊 (SNC)，複製資料。
- 連接到 SAP 應用程式伺服器或 SAP 訊息伺服器。

## <a name="prerequisites"></a>必要條件

若要使用這個 SAP 資料表連接器，您需要：

- 設定自我裝載的整合執行階段 （3.17 或更新版本）。 如需詳細資訊，請參閱 <<c0> [ 建立和設定自我裝載的整合執行階段](create-self-hosted-integration-runtime.md)。

- 下載 64 位元[適用於 Microsoft.NET 3.0 的 SAP 連接器](https://support.sap.com/en/product/connectors/msnet.html)從 SAP 的網站，並將它安裝在自我裝載的整合執行階段電腦上。 在安裝期間，請確定您選取**組件安裝到 GAC**選項**選擇性設定步驟**視窗。

  ![安裝適用於.NET 的 SAP 連接器](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- 用於資料處理站 SAP 資料表連接器的 SAP 使用者必須擁有下列權限：

  - 使用遠端函式呼叫 (RFC) 目的地的授權。
  - 權限可執行活動的 S_SDSAUTH 授權物件。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供有關用來定義 SAP 資料表連接器專屬的 Data Factory 實體的屬性的詳細資料。

## <a name="linked-service-properties"></a>連結服務屬性

SAP BW Open Hub 連結服務支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| `type` | `type`屬性必須設為`SapTable`。 | 是 |
| `server` | SAP 執行個體所在伺服器的名稱。<br/>用來連線到 SAP 應用程式伺服器。 | 否 |
| `systemNumber` | SAP 系統的系統編號。<br/>用來連線到 SAP 應用程式伺服器。<br/>允許的值：表示為字串二位數十進位數字。 | 否 |
| `messageServer` | SAP 訊息伺服器主機名稱。<br/>用來連線到 SAP 訊息伺服器。 | 否 |
| `messageServerService` | 服務名稱或連接埠的訊息伺服器數目。<br/>用來連線到 SAP 訊息伺服器。 | 否 |
| `systemId` | 資料表所在的 SAP 系統識別碼。<br/>用來連線到 SAP 訊息伺服器。 | 否 |
| `logonGroup` | SAP 系統的登入群組。<br/>用來連線到 SAP 訊息伺服器。 | 否 |
| `clientId` | SAP 系統中的用戶端識別碼。<br/>允許的值：以字串表示三位數十進位數字。 | 是 |
| `language` | SAP 系統所使用的語言。<br/>預設值為 `EN`。| 否 |
| `userName` | 具有 SAP 伺服器的存取權之使用者的名稱。 | 是 |
| `password` | 使用者的密碼。 將使用這個欄位標記`SecureString`安全地儲存在 Data Factory 中的型別或[參考儲存在 Azure Key Vault 祕密](store-credentials-in-key-vault.md)。 | 是 |
| `sncMode` | SNC 啟用存取 SAP 伺服器之資料表所在的位置指標。<br/>如果您想要使用 SNC 連接到 SAP 伺服器，使用。<br/>允許的值為`0`（關閉，預設值） 或`1`（開啟）。 | 否 |
| `sncMyName` | 存取 SAP 伺服器的資料表所在的 SNC 起始端的名稱。<br/>適用於當`sncMode`上。 | 否 |
| `sncPartnerName` | 若要存取之資料表所在的 SAP 伺服器通訊的夥伴 SNC 名稱。<br/>適用於當`sncMode`上。 | 否 |
| `sncLibraryPath` | 外部安全性產品的程式庫來存取 SAP 伺服器之資料表所在的位置。<br/>適用於當`sncMode`上。 | 否 |
| `sncQop` | 要套用的保護 SNC 品質層級。<br/>適用於當`sncMode`上。 <br/>允許的值為`1`（驗證）、 `2` （完整性）， `3` （隱私權） `8` （預設）、 `9` （最大值）。 | 否 |
| `connectVia` | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 自我裝載的整合執行階段是必要的如先前所述[必要條件](#prerequisites)。 |是 |

**範例 1：連接到 SAP 應用程式伺服器**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>範例 2：連接到 SAP 訊息伺服器

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>範例 3：使用 SNC 連線

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需的區段和屬性來定義資料集的完整清單，請參閱 <<c0> [ 資料集](concepts-datasets-linked-services.md)。 下一節會提供 SAP 資料表資料集所支援的屬性清單。

若要複製資料，以及連結的 SAP BW Open Hub service，支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| `type` | `type`屬性必須設為`SapTableResource`。 | 是 |
| `tableName` | 若要複製資料從 SAP 資料表的名稱。 | 是 |

### <a name="example"></a>範例

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需的區段和屬性來定義活動的完整清單，請參閱 <<c0> [ 管線](concepts-pipelines-activities.md)。 下一節會提供一份 SAP 資料表來源所支援的屬性。

### <a name="sap-table-as-a-source"></a>SAP 資料表作為來源

若要從 SAP 資料表複製資料，支援下列屬性：

| 屬性                         | 描述                                                  | 必要項 |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | `type`屬性必須設為`SapTableSource`。         | 是      |
| `rowCount`                         | 要擷取的資料列數目。                              | 否       |
| `rfcTableFields`                   | 從 SAP 資料表複製欄位 （資料行）。 例如： `column0, column1` 。 | 否       |
| `rfcTableOptions`                  | 在 SAP 資料表的資料列篩選選項。 例如： `COLUMN0 EQ 'SOMEVALUE'` 。 另請參閱本文稍後的 SAP 查詢運算子的資料表。 | 否       |
| `customRfcReadTableFunctionModule` | 自訂 RFC 函式的模組，可用來從 SAP 資料表讀取資料。<br>您可以使用自訂的 RFC 函式模組來定義您的 SAP 系統從擷取及傳回至 Data Factory 資料的方式。 自訂函數模組必須實作的介面 （匯入、 匯出、 資料表），類似於`/SAPDS/RFC_READ_TABLE2`，這是 Data Factory 所使用的預設介面。 | 否       |
| `partitionOption`                  | 若要從 SAP 資料表讀取資料分割機制。 支援的選項包括： <ul><li>`None`</li><li>`PartitionOnInt` (一般整數或整數值，以零填補左側，例如`0000012345`)</li><li>`PartitionOnCalendarYear` （"YYYY"格式的 4 位數）</li><li>`PartitionOnCalendarMonth` （6 位數格式"YYYYMM"）</li><li>`PartitionOnCalendarDate` （格式為"YYYYMMDD"的 8 位數）</li></ul> | 否       |
| `partitionColumnName`              | 用來分割資料的資料行名稱。                | 否       |
| `partitionUpperBound`              | 在指定的資料行的最大值`partitionColumnName`，將會用來繼續進行資料分割。 | 否       |
| `partitionLowerBound`              | 在指定的資料行的最小值`partitionColumnName`，將會用來繼續進行資料分割。 | 否       |
| `maxPartitionsNumber`              | 若要將資料分割成的資料分割的數目上限。     | 否       |

>[!TIP]
>如果您的 SAP 資料表有大量的資料，例如數個十億筆資料列，使用`partitionOption`和`partitionSetting`將資料分割成較小的資料分割。 在此情況下，每個資料分割，讀取資料，並從您的 SAP 伺服器，透過單一的 RFC 呼叫擷取每個資料分割。<br/>
<br/>
>採取`partitionOption`作為`partitionOnInt`做為範例，每個分割中的資料列數目使用以下公式計算: (之間的資料列總數`partitionUpperBound`並`partitionLowerBound`) /`maxPartitionsNumber`。<br/>
<br/>
>若要執行以平行方式來加快複製的資料分割，我們強烈建議進行`maxPartitionsNumber`值的倍數`parallelCopies`屬性。 如需詳細資訊，請參閱 <<c0> [ 平行複製](copy-activity-performance.md#parallel-copy)。

在  `rfcTableOptions`，您可以使用下列的一般 SAP 查詢運算子來篩選資料列：

| 運算子 | 描述 |
| :------- | :------- |
| `EQ` | 等於 |
| `NE` | 不等於 |
| `LT` | 小於 |
| `LE` | 小於或等於 |
| `GT` | 大於 |
| `GE` | 大於或等於 |
| `LIKE` | 中 `LIKE 'Emma%'` |

### <a name="example"></a>範例

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>SAP 資料表的資料類型對應

當您要從 SAP 資料表複製資料，時請使用下列對應會從 SAP 資料表資料類型到 Azure Data Factory 過渡期資料類型。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

| SAP ABAP 類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| `C` （字串） | `String` |
| `I` （整數） | `Int32` |
| `F` （浮點數） | `Double` |
| `D` （日期） | `String` |
| `T` （時間） | `String` |
| `P` （BCD 封裝，貨幣、 Decimal、 Qty） | `Decimal` |
| `N` （數值） | `String` |
| `X` （二進位和原始） | `String` |

## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中的複製活動所支援作為來源和接收資料存放區的清單，請參閱 <<c0> [ 支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
