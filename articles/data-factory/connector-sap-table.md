---
title: 從 SAP 資料表使用 Azure Data Factory 複製資料 |Microsoft Docs
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
ms.date: 05/24/2018
ms.author: jingwang
ms.openlocfilehash: 4dee0e994c9e7be9677a8f1051481850990998e9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247165"
---
# <a name="copy-data-from-sap-table-using-azure-data-factory"></a>從 SAP 資料表使用 Azure Data Factory 複製資料

本文概述如何使用 Azure Data Factory 中的 「 複製活動，從 SAP 資料表複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

您可以從 SAP 資料表將資料複製到任何支援的接收資料存放區中。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，此 SAP 資料表 」 連接器支援：

- 將資料從 SAP 資料表中複製**7.01 或更高版本的 SAP Business Suite** （在新 SAP 支援封裝堆疊在 2015 年之後釋出） 或**S/4HANA**。
- 將資料複製兩個**SAP 透明資料表**並**檢視**。
- 複製 使用資料**基本驗證**或是**SNC** （安全網路通訊） 如果設定 SNC。
- 連接到**應用程式伺服器**或是**訊息伺服器**。

## <a name="prerequisites"></a>必要條件

若要使用這個 SAP 資料表連接器，您需要：

- 設定自我裝載整合執行階段版本 3.17 或更新版本。 如需詳細資料，請參閱[自我裝載 Integration Runtime](create-self-hosted-integration-runtime.md) 一文。

- 從 SAP 的網站下載 **64 位元[ SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)** ，並將其安裝在自我裝載的 IR 機器上。 當安裝，請在 [選用設定步驟] 視窗中，請確定您選取**組件安裝到 GAC**選項。 

    ![安裝 SAP .NET 連接器](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- 使用 Data Factory SAP 資料表連接器中的 SAP 使用者必須具有下列權限： 

    - RFC 的授權。 
    - 授權物件 」 S_SDSAUTH 」 的 「 執行 」 活動的權限。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供有關用來定義 SAP 資料表連接器專屬的 Data Factory 實體的屬性的詳細資料。

## <a name="linked-service-properties"></a>連結服務屬性

以下是 SAP Business Warehouse Open Hub 連結服務支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為：**SapTable** | 是 |
| 伺服器 | SAP 執行個體所在伺服器的名稱。<br/>如果您想要連接到適用於**SAP 應用程式伺服器**。 | 否 |
| systemNumber | SAP 系統的系統編號。<br/>如果您想要連接到適用於**SAP 應用程式伺服器**。<br/>允許的值：以字串表示的二位數十進位數字。 | 否 |
| messageServer | SAP 訊息伺服器的主機名稱。<br/>如果您想要連接到適用於**SAP 訊息伺服器**。 | 否 |
| messageServerService | 服務名稱或連接埠的訊息伺服器數目。<br/>如果您想要連接到適用於**SAP 訊息伺服器**。 | 否 |
| systemId | SystemID SAP 系統資料表所在的位置。<br/>如果您想要連接到適用於**SAP 訊息伺服器**。 | 否 |
| logonGroup | SAP 系統的登入群組。<br/>如果您想要連接到適用於**SAP 訊息伺服器**。 | 否 |
| clientId | SAP 系統中的用戶端的用戶端識別碼。<br/>允許的值：以字串表示的三位數十進位數字。 | 是 |
| 語言 | SAP 系統使用的語言。 | 否 (預設值為 **EN**)|
| userName | 能夠存取 SAP 伺服器的使用者名稱。 | 是 |
| password | 使用者的密碼。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是 |
| sncMode | SNC 啟用標記，來存取 SAP 伺服器之資料表所在的位置。<br/>如果您想要連接到 SAP 伺服器使用 SNC，適用這個選項。<br/>允許的值包括：**0** (off，預設值) 或**1** （開啟）。 | 否 |
| sncMyName | 存取 SAP 伺服器的資料表所在的 SNC 起始端的名稱。<br/>適用於`sncMode`上。 | 否 |
| sncPartnerName | 若要存取之資料表所在的 SAP 伺服器通訊的夥伴 SNC 名稱。<br/>適用於`sncMode`上。 | 否 |
| sncLibraryPath | 外部安全性產品的程式庫來存取 SAP 伺服器之資料表所在的位置。<br/>適用於`sncMode`上。 | 否 |
| sncQop | SNC 品質的保護。<br/>適用於`sncMode`上。 <br/>允許的值包括：**1** （驗證）、 **2** （完整性）， **3** （隱私權） **8** （預設）、 **9** （最大值）。 | 否 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 如[必要條件](#prerequisites)所述，必須要有一個「自我裝載 Integration Runtime」。 |是 |

**範例 1： 連接到 SAP 應用程式伺服器**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

**範例 2： 連接到 SAP 訊息伺服器**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system id>",
            "logonGroup": "<logon group>",
            "clientId": "<client id>",
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

**範例 3： 連接使用 SNC**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "snc myname",
            "sncPartnerName": "snc partner name",
            "sncLibraryPath": "snc library path",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 SAP 資料表的資料集所支援的屬性清單。

SAP BW Open Hub 來回，請複製資料，支援下列屬性。

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為**SapTableResource**。 | 是 |
| tableName | 若要複製資料從 SAP 資料表的名稱。 | 是 |

**範例：**

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP Table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 SAP 資料表來源所支援的屬性清單。

### <a name="sap-table-as-source"></a>SAP 資料表作為來源

若要從 SAP 資料表複製資料，支援下列屬性。

| 屬性                         | 描述                                                  | 必要項 |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| type                             | Type 屬性必須設定為**SapTableSource**。       | 是      |
| rowCount                         | 要擷取的資料列數目。                              | 否       |
| rfcTableFields                   | 若要從 SAP 資料表複製的欄位。 例如： `column0, column1`。 | 否       |
| rfcTableOptions                  | SAP 資料表中的資料列篩選選項。 例如： `COLUMN0 EQ 'SOMEVALUE'`。 | 否       |
| customRfcReadTableFunctionModule | 自訂 RFC 函式的模組，可用來從 SAP 資料表讀取資料。 | 否       |
| partitionOption                  | 若要從 SAP 資料表讀取資料分割機制。 支援的選項包括： <br/>- **None**<br/>- **PartitionOnInt** （一般整數或整數值，以零填補左方 0000012345 等）<br/>- **PartitionOnCalendarYear** (4 digits in format "YYYY")<br/>- **PartitionOnCalendarMonth** （格式為"YYYYMM 」 的 6 位數）<br/>- **PartitionOnCalendarDate** （格式為"YYYYMMDD"的 8 位數） | 否       |
| partitionColumnName              | 要分割資料的資料行名稱。 | 否       |
| partitionUpperBound              | 在指定的資料行的最大值`partitionColumnName`，將會用於進行資料分割。 | 否       |
| partitionLowerBound              | 在指定的資料行的最小值`partitionColumnName`，將會用於進行資料分割。 | 否       |
| maxPartitionsNumber              | 若要將資料分割成的資料分割的數目上限。 | 否       |

>[!TIP]
>- 如果您的 SAP 資料表有大量的資料，例如數個數十億個資料列，使用`partitionOption`和`partitionSetting`將資料分割成小型資料分割，在此情況下讀取的資料分割且每個資料分割的資料會從您透過一個單一的 SAP 伺服器RFC 呼叫。<br/>
>- 採取`partitionOption`作為`partitionOnInt`做為範例，每個分割中的資料列數目計算方式 (之間的資料列總數*partitionUpperBound*並*partitionLowerBound*) /*maxPartitionsNumber*。<br/>
>- 如果您想要進一步分割區平行執行以加快複製，強烈建議進行`maxPartitionsNumber`做為值的倍數`parallelCopies`(進一步了解[平行複製](copy-activity-performance.md#parallel-copy))。

**範例：**

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP Table input dataset name>",
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

## <a name="data-type-mapping-for-sap-table"></a>SAP 資料表的資料類型對應

當從 SAP 資料表複製資料，請使用下列對應會從 SAP 資料表資料類型到 Azure Data Factory 過渡期資料類型。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收器。

| SAP ABAP 類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| C (字串) | String |
| 我 （整數） | Int32 |
| F (浮點數) | Double |
| D (日期) | 字串 |
| T (時間) | 字串 |
| P (BCD 封裝、貨幣、小數、數量) | Decimal |
| N （數值） | String |
| X (二進位和原始) | 字串 |

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
