---
title: "使用 Azure Data Factory 的登錄區中的資料複製 |Microsoft 文件"
description: "了解如何將資料從登錄區複製到支援的接收資料存放區，在 Azure Data Factory 管線中使用複製活動。"
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
ms.openlocfilehash: af9050d41502f55b0426b858654b8af6985b93ca
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>使用 Azure Data Factory 的登錄區中的資料複製 

本文概述如何使用 Azure Data Factory 中的複製活動來將資料複製登錄區。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務，也就是正式推出 (GA) 的版本，請參閱[第 1 版的複製活動](v1/data-factory-data-movement-activities.md)。

## <a name="supported-capabilities"></a>支援的功能

您可以從 Hive 資料複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

Azure Data Factory 提供的內建驅動程式可啟用連線，因此使用此連接器您不需要手動安裝任何驅動程式。

## <a name="getting-started"></a>開始使用

您可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure Resource Manager 範本來建立具有複製活動的管線。 如需建立內含複製活動之管線的逐步指示，請參閱[複製活動教學課程](quickstart-create-data-factory-dot-net.md)。

下列各節提供有關用來定義特定的 Data Factory 實體 Hive 連接器屬性詳細資料。

## <a name="linked-service-properties"></a>連結服務屬性

登錄區連結服務支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為： **hive 控制檔** | 是 |
| host | IP 位址或主機名稱的登錄區伺服器，由 「; 」 分隔多個主機 （只有當 serviceDiscoveryMode 為啟用）。  | 是 |
| 連接埠 | 登錄區伺服器用來接聽用戶端連線之 TCP 通訊埠。  | 否 |
| 伺服器類型 | 登錄區伺服器類型。 <br/>允許的值為： **HiveServer1**， **HiveServer2**， **HiveThriftServer** | 否 |
| thriftTransportProtocol | Thrift 層中使用的傳輸通訊協定。 <br/>允許的值為：**二進位**， **SASL**，* * HTTP * * | 否 |
| authenticationType | 用來存取登錄區伺服器驗證方法。 <br/>允許的值為：**匿名**， **Username**， **UsernameAndPassword**， **WindowsAzureHDInsightService** | 是 |
| serviceDiscoveryMode | true 表示不 false 使用動物園管理員服務。  | 否 |
| zooKeeperNameSpace | 加入節點下的登錄區伺服器 2 動物園管理員在命名空間。  | 否 |
| useNativeQuery | 指定驅動程式會使用原生 HiveQL 查詢，或將它們轉換為 HiveQL 中對等格式。  | 否 |
| username | 您用來存取登錄區伺服器使用者名稱。  | 否 |
| password | 對應至您在 [使用者名稱] 欄位中提供的使用者名稱密碼可以選擇將以 securestring 的形式將它存放在 ADF，安全地或儲存在 Azure 金鑰保存庫中的密碼，並讓複製活動提取從該處時執行資料複製-這個欄位標記 lea從多個 rn[將認證儲存在金鑰保存庫](store-credentials-in-key-vault.md)。 | 否 |
| httpPath | 對應至登錄區伺服器部分的 URL。  | 否 |
| enableSsl | 指定伺服器的連接是否使用 SSL 進行加密。 預設值為 False。  | 否 |
| trustedCertPath | 這個.pem 檔案，包含受信任的 CA 憑證，透過 SSL 連線時，驗證伺服器的完整路徑。 這個屬性可以只在自我裝載的紅外線上使用 SSL 時設定 預設值是隨紅外線 cacerts.pem 檔案  | 否 |
| useSystemTrustStore | 指定是否要使用的 CA 憑證，從系統信任存放區，或從指定的 PEM 檔案。 預設值為 False。  | 否 |
| allowHostNameCNMismatch | 指定是否需要符合伺服器的主機名稱，透過 SSL 連線時的 CA 所核發的 SSL 憑證名稱。 預設值為 False。  | 否 |
| allowSelfSignedServerCert | 指定是否要允許從伺服器的自我簽署的憑證。 預設值為 False。  | 否 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 您可以使用「自我裝載 Integration Runtime」或 Azure Integration Runtime (如果您的資料存放區是可公開存取的)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

**範例：**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "httpPath" : "gateway/sandbox/spark"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供登錄區的資料集所支援的屬性的清單。

若要從登錄區複製資料，設定要的資料集的類型屬性**HiveObject**。 沒有任何額外的特定類型的屬性，在這種類型的資料集。

**範例**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供一份 Hive 來源所支援的屬性。

### <a name="hivesource-as-source"></a>做為來源 HiveSource

若要從登錄區複製資料，將來源類型複製活動中**HiveSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為： **HiveSource** | 是 |
| query | 使用自訂 SQL 查詢來讀取資料。 例如：`"SELECT * FROM MyTable"`。 | 是 |

**範例：**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
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
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
