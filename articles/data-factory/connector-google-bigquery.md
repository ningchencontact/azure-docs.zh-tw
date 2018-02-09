---
title: "使用 Azure Data Factory 從 Google BigQuery 複製資料 (搶鮮版 (Beta)) | Microsoft Docs"
description: "了解如何使用資料處理站管線中的複製活動，將資料從 Google BigQuery 複製到支援的接收資料存放區。"
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
ms.openlocfilehash: 3b559e64f38727b1e390160515b7614ad1dfaa97
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2018
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory-beta"></a>使用 Azure Data Factory 從 Google BigQuery 複製資料 (搶鮮版 (Beta))

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Google BigQuery 複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用已正式運作的第 1 版 Data Factory 服務，請參閱[第 1 版中的複製活動](v1/data-factory-data-movement-activities.md)。

> [!IMPORTANT]
> 此連接器目前為搶鮮版 (Beta)。 您可以親身體驗並提供意見反應。 請勿在生產環境中使用它。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 Google BigQuery 複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源或接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

 Data Factory 會提供內建的驅動程式來啟用連線。 因此，您不需要為了使用此連接器而需手動安裝驅動程式。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Google BigQuery 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Google BigQuery 連結服務所支援的屬性。

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | type 屬性必須設定為 **GoogleBigQuery**。 | yes |
| project | 要據以查詢之預設 BigQuery 專案的專案識別碼。  | yes |
| additionalProjects | 要存取之公開 BigQuery 專案的專案識別碼清單 (以逗號分隔)。  | 否 |
| requestGoogleDriveScope | 是否要求存取 Google 雲端硬碟。 允許 Google 雲端硬碟存取能夠支援同盟資料表，其中結合了 BigQuery 資料與來自 Google 雲端硬碟的資料。 預設值為 **false**。  | 否 |
| authenticationType | 用於驗證的 OAuth 2.0 驗證機制。 ServiceAuthentication 只能在自我裝載 Integration Runtime 上使用。 <br/>允許的值為 **ServiceAuthentication** 和 **UserAuthentication**。 | yes |
| refreshToken | 從 Google 取得的重新整理權杖，用來針對 UserAuthentication 授權存取 BigQuery。 您可以將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中。 您也可以將密碼儲存在 Azure Key Vault，當您執行資料複製時，讓複製活動從該處提取。 若要深入了解，請參閱[在 Key Vault 中儲存認證](store-credentials-in-key-vault.md)。 | 否 |
| 電子郵件 | 用於 ServiceAuthentication 的服務帳戶電子郵件識別碼。 它只能在自我裝載 Integration Runtime 上使用。  | 否 |
| keyFilePath | .p12 金鑰檔的完整路徑，用來驗證服務帳戶電子郵件地址。 它只能在自我裝載 Integration Runtime 上使用。  | 否 |
| trustedCertPath | .pem 檔案的完整路徑，其中包含在透過 SSL 連線時，用來驗證伺服器的受信任 CA 憑證。 只有當您在自我裝載 Integration Runtime 使用 SSL 時，才能設定這個屬性。 預設值為隨整合執行階段安裝的 cacerts.pem 檔案。  | 否 |
| useSystemTrustStore | 指定是否使用來自系統信任存放區或來自指定 .pem 檔案的 CA 憑證。 預設值為 **false**。  | 否 |

**範例：**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refresh token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Google BigQuery 資料集所支援的屬性清單。

若要從 Google BigQuery 複製資料，請將資料集的 type 屬性設定為 **GoogleBigQueryObject**。 在此類型的資料集中，沒有任何其他類型特定的屬性。

**範例**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Google BigQuery 來源類型所支援的屬性清單。

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource 作為來源類型

若要從 Google BigQuery 複製資料，請將複製活動中的來源類型設定為 **GoogleBigQuerySource**。 複製活動的 [來源] 區段支援下列屬性。

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 type 屬性必須設定為 **GoogleBigQuerySource**。 | yes |
| query | 使用自訂 SQL 查詢來讀取資料。 例如 `"SELECT * FROM MyTable"`。 | yes |

**範例：**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
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
