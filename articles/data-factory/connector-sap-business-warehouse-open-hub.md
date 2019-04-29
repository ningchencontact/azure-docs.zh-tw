---
title: 使用 Azure Data Factory 透過 Open Hub 從 SAP Business Warehouse 複製資料 | Microsoft Docs
description: 了解如何使用 Azure Data Factory 管線中的複製活動，透過 Open Hub 將資料從 SAP Business Warehouse (BW) 複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 03/08/2019
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: c64842dc89c9519c738701558f510940f4cc148d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848861"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>使用 Azure Data Factory 透過 Open Hub 從 SAP Business Warehouse 複製資料

本文概述如何使用 Azure Data Factory 中的「複製活動」，透過 Open Hub 從 SAP Business Warehouse (BW) 複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

您可以透過 Open Hub 將資料從 SAP Business Warehouse 複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，此 SAP Business Warehouse Open Hub 連接器支援：

- SAP Business Warehouse **7.01 或 （堆疊中較高層最近 SAP 支援封裝之後 2015 年發行） 版本**。
- 透過 Open Hub Destination 本機資料表複製資料，其下可能是 DSO、InfoCube、MultiProvider、DataSource 等等。
- 使用基本驗證來複製資料。
- 連線至應用程式伺服器。

## <a name="sap-bw-open-hub-integration"></a>SAP BW Open Hub 整合 

[SAP BW Open Hub Service](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) 可讓您有效地從 SAP BW 中擷取資料。 下圖顯示客戶在其 SAP 系統中的常見流程之一，其資料流程為 SAP ECC -> PSA -> DSO -> Cube。

SAP BW Open Hub Destination (OHD) 會定義轉送 SAP 資料的目標。 支援的 SAP 資料傳輸程序 (DTP) 的任何物件可用來當做開啟中樞的資料來源，比方說，DSO、 InfoCube、 資料來源等。Open Hub Destination 類型是轉送的資料儲存所在之處，可以是資料庫資料表 (本機或遠端) 和一般檔案。 此 SAP BW Open Hub 連接器支援從 BW 中的 OHD 本機資料表複製資料。 如果您使用其他類型，您可以使用其他連接器直接連線到資料庫或檔案系統。

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>差異擷取流程

ADF SAP BW 開啟中樞連接器提供兩個選擇性屬性：`excludeLastRequest`和`baseRequestId`可用來處理差異負載，或從開啟的中樞。 

- **excludeLastRequestId**:是否要排除最後一個要求的記錄。 預設值為 true。 
- **baseRequestId**:差異載入的要求識別碼。 一旦設定之後，將會擷取 requestid 大於這個屬性的值的資料。 

整體來說，擷取 SAP InfoProviders 從 Azure Data Factory (ADF) 包含 2 個步驟： 

1. **SAP BW 資料傳輸程序 (DTP)** 此步驟中將資料從 SAP BW InfoProvider 複製到 SAP BW Open Hub 資料表 

1. **ADF 資料複製**在此步驟中，由 ADF 連接器讀取 Open Hub 資料表 

![差異擷取流程](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

在第一個步驟中，會執行 DTP。 每次執行會建立新的 SAP 要求識別碼。 要求識別碼會儲存在中樞開啟資料表，然後由 ADF 連接器用來識別差異。 以非同步方式執行的兩個步驟： DTP 由 SAP，觸發，並透過 ADF 觸發 ADF 複製資料。 

根據預設，ADF 器不會讀取最新的 delta 從 Open Hub 資料表 （「 排除的最後一個要求 」 的選項為 true）。 謹此，在 ADF 中的資料不是 100%開放中樞資料表 （最後一個差異是缺少） 中的資料保持最新狀態。 此程序可確保沒有任何資料列會遺失非同步擷取所造成。 沒問題即使 ADF 會讀取 Open Hub 資料表，而 DTP 仍是寫入至相同的資料表。 

您通常會儲存 adf 暫存資料存放區 （例如 Azure Blob 中圖表上方) 中最後一個執行中的最大的複製的要求識別碼。 因此，相同的要求是讀取第二次 adf 後續執行中。 同時，請注意從 Open Hub 資料表不會自動刪除資料。

適當的差異處理它不是允許將要求從不同的 DTPs Id 在相同資料表中，開啟的中樞。 因此，您必須建立一個以上的 DTP 的每個開啟中樞目的地 」 (OHD)。 當需要從同一個 InfoProvider 完整和差異的擷取，您應該建立兩個 OHDs 相同 InfoProvider。 

## <a name="prerequisites"></a>必要條件

若要使用此 SAP Business Warehouse Open Hub 連接器，您必須：

- 設定 3.13 版或更新版本的「自我裝載 Integration Runtime。 如需詳細資料，請參閱[自我裝載 Integration Runtime](create-self-hosted-integration-runtime.md) 一文。

- 從 SAP 的網站下載 **64 位元[ SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)**，並將其安裝在自我裝載的 IR 機器上。 安裝時，請在選擇性的設定步驟視窗中確定您已選取 [將組件安裝到 GAC] 選項，如下圖所示。 

    ![安裝 SAP .NET 連接器](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- 在 Data Factory BW 連接器中使用的 SAP 使用者必須具有下列權限： 

    - RFC 和 SAP BW 的授權。 
    - 「執行」授權物件 "S_SDSAUTH" 活動的權限。

- 勾選 [技術金鑰] 選項，將 SAP Open Hub Destination 類型建立為 [資料庫資料表]。  此外也建議您將 [從資料表中刪除資料] 保留為未勾選，但這並非必要動作。 利用 DTP (直接執行或整合到現有的程序鏈結中) 將資料從您所選擇的來源物件 (例如 Cube) 移至 Open Hub Destination 資料表。

## <a name="getting-started"></a>開始使用

> [!TIP]
>
> 使用 SAP BW Open Hub 連接器的逐步解說，請參閱 <<c0> [ 使用 Azure Data Factory 將資料從 SAP Business Warehouse (BW)](load-sap-bw-data.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 SAP Business Warehouse Open Hub 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是 SAP Business Warehouse Open Hub 連結服務支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為：**SapOpenHub** | 是 |
| 伺服器 | SAP BW 執行個體所在之伺服器的名稱。 | 是 |
| systemNumber | SAP BW 系統的系統編號。<br/>允許的值：以字串表示的二位數十進位數字。 | 是 |
| clientId | SAP W 系統中用戶端的用戶端識別碼。<br/>允許的值：以字串表示的三位數十進位數字。 | 是 |
| 語言 | SAP 系統使用的語言。 | 否 (預設值為 **EN**)|
| userName | 能夠存取 SAP 伺服器的使用者名稱。 | 是 |
| password | 使用者的密碼。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 如[必要條件](#prerequisites)所述，必須要有一個「自我裝載 Integration Runtime」。 |是 |

**範例：**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
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

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Salesforce 資料集所支援的屬性清單。

若要從 SAP BW Open Hub 複製資料以及將資料複製到該處，請將資料集的 type 屬性設為 **SapOpenHubTable**。 以下是支援的屬性。

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為 **SapOpenHubTable**。  | 是 |
| openHubDestinationName | 要從中複製資料的 Open Hub Destination 名稱。 | 是 |
| excludeLastRequest | 是否要排除最後一個要求的記錄。 | 否 (預設值為 **true**) |
| baseRequestId | 差異載入的要求識別碼。 設定之後，將只會擷取 requestId **大於**此屬性值的資料。  | 否 |

>[!TIP]
>如果您的 Open Hub 資料表僅包含單一要求識別碼所產生的資料 (例如，您一律執行完整負載並覆寫資料表中的現有資料，或您只在測試時執行 DTP 一次)，請務必取消勾選 "excludeLastRequest" 選項，以複製資料。

**範例：**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 SAP BW Open Hub 來源所支援的屬性清單。

### <a name="sap-bw-open-hub-as-source"></a>以 SAP BW Open Hub 作為來源

若要從 SAP BW Open Hub 複製資料，請將複製活動中的來源類型設為 **SapOpenHubSource**。 複製活動的 **source** 區段中不需要其他類型特定的屬性。

**範例：**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>SAP BW Open Hub 的資料類型對應

從 SAP BW Open Hub 複製資料時，會使用下列從 SAP BW 資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收器。

| SAP ABAP 類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| C (字串) | 字串 |
| I (整數) | Int32 |
| F (浮點數) | Double |
| D (日期) | 字串 |
| T (時間) | 字串 |
| P (BCD 封裝、貨幣、小數、數量) | Decimal |
| N (Numc) | 字串 |
| X (二進位和原始) | 字串 |

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
