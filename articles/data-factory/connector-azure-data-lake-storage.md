---
title: 複製資料，或從 Azure Data Lake 儲存體 Gen2 使用 Data Factory |Microsoft Docs
description: 了解如何使用 Azure Data Factory 從 Azure Data Lake 儲存體 Gen2 來回複製資料。
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: jingwang
ms.openlocfilehash: 6425fdfe89ca2f4c47aaf0e5ffd1dac7767b5020
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057928"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>使用 Azure Data Factory 從 Azure Data Lake Storage Gen2 來回複製資料

Azure Data Lake 儲存體 Gen2 是一份專用於內建的巨量資料分析的功能[Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)。 您可以使用它來與您的資料使用這兩個檔案系統 」 和 「 物件儲存體的範例。

本文概述如何從 Azure Data Lake 儲存體 Gen2 來回複製資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

此 Azure Data Lake 儲存體 Gen2 連接器支援下列活動：

- [複製活動](copy-activity-overview.md)與[支援來源或接收器的矩陣](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)

具體而言，使用此連接器您可以：

- 使用將資料複製帳戶金鑰、 服務主體或受管理的身分識別的 Azure 資源驗證。
- 將檔案或剖析或產生的檔案複製[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs.md)。

>[!TIP]
>如果您啟用階層式命名空間時，目前沒有任何互通性的 Blob 與 Data Lake 儲存體 Gen2 Api 之間的作業。 如果您遇到錯誤 「 ErrorCode = FilesystemNotFound 」 與 「 指定的檔案系統並不存在 」 的訊息，造成其他位置建立透過 Blob API，而不是 Data Lake 儲存體 Gen2 API 指定的接收檔案系統。 若要修正此問題，指定新的檔案系統做為 Blob 容器的名稱不存在的名稱。 然後 Data Factory 會自動會建立該檔案系統資料複製期間。

>[!NOTE]
>如果您啟用**允許信任的 Microsoft 服務存取此儲存體帳戶**選項在 Azure 儲存體防火牆設定中，Azure 整合執行階段未連線到 Data Lake 儲存體 Gen2 並顯示禁止的錯誤。 因為 Data Factory 不被視為受信任的 Microsoft 服務，就會出現錯誤訊息。 您可以使用自我裝載的整合執行階段，而是連線。

## <a name="get-started"></a>開始使用

>[!TIP]
>如需如何使用 Data Lake 儲存體 Gen2 連接器的逐步解說，請參閱 <<c0> [ 將資料載入 Azure Data Lake 儲存體 Gen2](load-azure-data-lake-storage-gen2.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性用來定義 Data Lake 儲存體 Gen2 專屬的 Data Factory 實體的相關資訊。

## <a name="linked-service-properties"></a>連結服務屬性

Azure Data Lake 儲存體 Gen2 連接器支援下列驗證類型。 請參閱對應的章節，如需詳細資訊：

- [帳戶金鑰驗證](#account-key-authentication)
- [服務主體驗證](#service-principal-authentication)
- [Azure 資源的受控識別驗證](#managed-identity)

### <a name="account-key-authentication"></a>帳戶金鑰驗證

若要使用儲存體帳戶金鑰驗證，以下是支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為 **AzureBlobFS**。 |是 |
| url | 端點的模式與 Data Lake 儲存體 Gen2 `https://<accountname>.dfs.core.windows.net`。 | 是 |
| accountKey | Data Lake 儲存體 Gen2 的帳戶金鑰。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果您的資料存放區位於私人網路，您可以使用 Azure integration runtime 或自我裝載的整合執行階段。 如果未指定此屬性，則會使用預設的 Azure 整合執行階段。 |否 |

**範例：**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>服務主體驗證

若要使用服務主體驗證，請遵循下列步驟。

1. 中的步驟來註冊 Azure Active Directory (Azure AD) 中的應用程式實體[註冊您的應用程式與 Azure AD 租用戶](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)。 請記下以下的值，您可以使用這些值來定義連結服務：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 授與服務主體適當權限。

    - **作為來源**：在 Azure 儲存體總管中，至少授與**讀取 + 執行**列出和複製資料夾和子資料夾中的檔案權限。 或者也可以授與複製單一檔案時所需的**讀取**權限。 或者，在存取控制 (IAM)，授與至少**儲存體 Blob 資料讀者**角色。
    - **作為接收**：在儲存體總管中，至少授與**寫入 + 執行**權限建立子資料夾中的項目。 或者，在存取控制 (IAM)，授與至少**儲存體 Blob 資料參與者**角色。

>[!NOTE]
>清單資料夾啟動從帳戶層級，或若要測試連線，您需要設定服務主體授與的權限**儲存體帳戶中 IAM 的 「 儲存體 Blob 資料讀者 」 權限**。 您使用下列項目時，這種情況即會成立：
>- **複製資料工具**以作者複製管線。
>- 以 **Data Factory UI** 在製作期間測試連線和瀏覽資料夾。 
>如果您有關於在帳戶層級的權限授與的考量，您可以略過測試連接，並輸入的路徑以手動方式在撰寫期間。 複製活動仍能運作，只要服務主體授與具有適當的權限在檔案複製。

針對已連結服務支援這些屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為 **AzureBlobFS**。 |是 |
| url | 端點的模式與 Data Lake 儲存體 Gen2 `https://<accountname>.dfs.core.windows.net`。 | 是 |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 是 |
| servicePrincipalKey | 指定應用程式的金鑰。 將做為這個欄位標記`SecureString`若要將它安全地儲存在 Data Factory。 或者，您可以[參考儲存在 Azure Key Vault 祕密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 游標停留在 Azure 入口網站右上角，擷取它。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果您的資料存放區位於私人網路，您可以使用 Azure integration runtime 或自我裝載的整合執行階段。 如果未指定，則會使用預設的 Azure 整合執行階段。 |否 |

**範例：**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Azure 資源的受控識別驗證

資料處理站可與 [Azure 資源的受控識別](data-factory-service-identity.md)相關聯，後者表示特定的資料處理站。 您直接可以使用這個受管理的身分識別用於 Data Lake 儲存體 Gen2 驗證，類似於使用您自己的服務主體。 它會將此指定的處理站可以存取及複製資料，或從您的 Data Lake 儲存體 Gen2。

若要使用 Azure 資源驗證的受管理的身分識別，請遵循下列步驟。

1. [擷取的 Data Factory 受控身分識別資訊](data-factory-service-identity.md#retrieve-managed-identity)藉由複製的值**服務識別應用程式識別碼**站一起產生。

2. 授與受管理的身分識別適當的權限。

    - **作為來源**：在儲存體總管中，至少授與**讀取 + 執行**列出和複製資料夾和子資料夾中的檔案權限。 或者也可以授與複製單一檔案時所需的**讀取**權限。 或者，在存取控制 (IAM)，授與至少**儲存體 Blob 資料讀者**角色。
    - **作為接收**：在儲存體總管中，至少授與**寫入 + 執行**權限建立子資料夾中的項目。 或者，在存取控制 (IAM)，授與至少**儲存體 Blob 資料參與者**角色。

>[!NOTE]
>清單資料夾啟動從帳戶層級，或若要測試連線，您必須設定受管理的身分識別授與的權限**儲存體帳戶中 IAM 的 「 儲存體 Blob 資料讀者 」 權限**。 您使用下列項目時，這種情況即會成立：
>- **複製資料工具**以作者複製管線。
>- 以 **Data Factory UI** 在製作期間測試連線和瀏覽資料夾。 
>如果您有關於在帳戶層級的權限授與的考量，您可以略過測試連接，並輸入的路徑以手動方式在撰寫期間。 複製活動仍能運作，只要受控身分識別時授與適當的權限在檔案複製。

>[!IMPORTANT]
>如果您使用 PolyBase 將資料從 Data Lake 儲存體 Gen2 載入 SQL 資料倉儲中，當您使用 Data Lake 儲存體 Gen2 受控身分識別驗證時，請確定您也可以遵循步驟 1 和 2 中的[本指南](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)。 遵循與 Azure Active Directory (Azure AD) 中註冊您的 SQL Database 伺服器的指示。 您也會指派給您的 SQL Database 伺服器的角色型存取控制的儲存體 Blob 資料參與者角色。 Data Factory 會處理其餘部分。 如果您的 Data Lake 儲存體 Gen2 設定使用 Azure 虛擬網路端點，以使用 PolyBase 將資料從它載入時，您必須使用受控身分識別驗證。

針對已連結服務支援這些屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 類型屬性必須設為 **AzureBlobFS**。 |是 |
| url | 端點的模式與 Data Lake 儲存體 Gen2 `https://<accountname>.dfs.core.windows.net`。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果您的資料存放區位於私人網路，您可以使用 Azure integration runtime 或自我裝載的整合執行階段。 如果未指定，則會使用預設的 Azure 整合執行階段。 |否 |

**範例：**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需完整的區段和屬性可用來定義資料集清單，請參閱 <<c0> [ 資料集](concepts-datasets-linked-services.md)。

- Parquet 和分隔的文字格式，請參閱[Parquet 和分隔的文字格式的資料集](#parquet-and-delimited-text-format-dataset)一節。
- 針對像是 ORC、 Avro、 JSON 或二進位格式的其他格式，請參閱[其他格式的資料集](#other-format-dataset)一節。

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet 和分隔的文字格式的資料集

若要以 parquet 或分隔的文字格式從 Data Lake 儲存體 Gen2 來回複製資料，請參閱[Parquet 格式](format-parquet.md)並[分隔文字格式](format-delimited-text.md)文章格式為基礎的資料集和支援的設定。 支援下列屬性下的 Data Lake 儲存體 Gen2`location`格式為基礎的資料集內的設定：

| 屬性   | 描述                                                  | 必要項 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | [類型] 屬性底下`location`資料集內必須設定為**AzureBlobFSLocation**。 | 是      |
| fileSystem | Data Lake 儲存體 Gen2 檔案系統名稱。                              | 否       |
| folderPath | 在指定的檔案系統資料夾的路徑。 如果您想要使用萬用字元來篩選資料夾，就會略過這項設定，並指定活動來源設定中。 | 否       |
| fileName   | 在指定的檔案系統 + folderPath 檔案名稱。 如果您想要使用萬用字元來篩選檔案，就會略過這項設定，並指定活動來源設定中。 | 否       |

> [!NOTE]
> **AzureBlobFSFile**原狀複製、 查閱或回溯相容性的 GetMetadata 活動仍然支援下一節中所述的 parquet 或文字格式的類型資料集。 但它並不適用於對應資料的資料流程功能。 我們建議您使用接下來的這個新模型。 編寫 UI 的資料處理站產生這些新的型別。

**範例：**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>其他格式的資料集

從 Data Lake 儲存體 Gen2 ORC、 Avro、 JSON 或二進位格式中來回，請複製資料，支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設為 **AzureBlobFSFile**。 |是 |
| folderPath | 在 Data Lake 儲存體 Gen2 資料夾路徑。 若未指定，它會指向根。 <br/><br/>支援萬用字元篩選。 允許使用萬用字元`*`（比對零或多個字元） 和`?`(比對零或單一字元)。 使用`^`來逸出，如果您的實際資料夾名稱中有萬用字元或此逸出字元內。 <br/><br/>範例： 檔案系統/資料夾 /。 如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 |否 |
| fileName | 指定的"folderPath"下的檔案名稱或萬用字元篩選條件。 若未指定此屬性的值，資料集就會指向資料夾中的所有檔案。 <br/><br/>篩選中，會允許使用萬用字元`*`（比對零或多個字元） 和`?`(比對零或單一字元)。<br/>- 範例 1：`"fileName": "*.csv"`<br/>- 範例 2：`"fileName": "???20180427.txt"`<br/>使用`^`來逸出，如果您的實際檔案名稱有萬用字元或此逸出字元內。<br/><br/>沒有為輸出資料集指定 fileName 且活動接收器中未指定 **preserveHierarchy** 時，複製活動會自動以下列模式產生檔案名稱：「*資料。[活動執行識別碼 GUID。][GUID 如果 FlattenHierarchy]。[格式設定]。[壓縮設定]* "，例如"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"。 如果您複製從表格式的來源，而不查詢中使用資料表名稱，名稱模式為" *[資料表名稱]。 [格式]。[壓縮設定]* "，例如"MyTable.csv 」。 |否 |
| modifiedDatetimeStart | 上次修改的屬性為基礎的檔案篩選條件。 如果其上次修改的時間之間的時間範圍內，會選取的檔案`modifiedDatetimeStart`和`modifiedDatetimeEnd`。 時間會套用至 UTC 時區，格式為"2018年-12-01T05:00:00Z"。 <br/><br/> 啟用此設定，當您想要進行大量的檔案與檔案篩選器時，會影響資料移動的整體效能。 <br/><br/> 屬性可以是 NULL，表示檔案屬性未套用任何篩選至資料集。 當`modifiedDatetimeStart`具有日期時間值，但`modifiedDatetimeEnd`是 NULL，這表示其最後一個已修改的屬性大於檔案，或者選取的日期時間值等於。 當`modifiedDatetimeEnd`具有日期時間值，但`modifiedDatetimeStart`是 NULL，這表示會選取其最後一個已修改的屬性是日期時間值小於檔案。| 否 |
| modifiedDatetimeEnd | 上次修改的屬性為基礎的檔案篩選條件。 如果其上次修改的時間之間的時間範圍內，會選取的檔案`modifiedDatetimeStart`和`modifiedDatetimeEnd`。 時間會套用至 UTC 時區，格式為"2018年-12-01T05:00:00Z"。 <br/><br/> 啟用此設定，當您想要進行大量的檔案與檔案篩選器時，會影響資料移動的整體效能。 <br/><br/> 屬性可以是 NULL，表示檔案屬性未套用任何篩選至資料集。 當`modifiedDatetimeStart`具有日期時間值，但`modifiedDatetimeEnd`是 NULL，這表示其最後一個已修改的屬性大於檔案，或者選取的日期時間值等於。 當`modifiedDatetimeEnd`具有日期時間值，但`modifiedDatetimeStart`是 NULL，這表示會選取其最後一個已修改的屬性是日期時間值小於檔案。| 否 |
| format | 如果您想要在檔案型存放區之間依原樣複製檔案 (二進位複本)，請在輸入和輸出資料集定義中略過格式區段。<br/><br/>如果您想要剖析或產生特定格式的檔案，以下是支援的檔案格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 將 [format]  下的 [type]  屬性設定為下列其中一個值。 如需詳細資訊，請參閱 <<c0> [ 文字格式](supported-file-formats-and-compression-codecs.md#text-format)， [JSON 格式](supported-file-formats-and-compression-codecs.md#json-format)， [Avro 格式](supported-file-formats-and-compression-codecs.md#avro-format)， [ORC 格式](supported-file-formats-and-compression-codecs.md#orc-format)，和[Parquet 格式](supported-file-formats-and-compression-codecs.md#parquet-format)區段。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs.md#compression-support)。<br/>支援的類型為：GZip  、Deflate  、BZip2  及 ZipDeflate  。<br/>支援的層級為 **Optimal** 和 **Fastest**。 |否 |

>[!TIP]
>若要複製資料夾下的所有檔案，請只指定 **folderPath**。<br>若要複製單一檔案具有指定名稱，指定**folderPath**與資料夾組件和**fileName**檔案名稱。<br>若要複製的資料夾下的檔案子集，請指定**folderPath**與資料夾組件並**fileName**與萬用字元篩選條件。 

**範例：**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

區段和屬性可用來定義活動的完整清單，請參閱[複製活動組態](copy-activity-overview.md#configuration)並[管線和活動](concepts-pipelines-activities.md)。 本節提供 Data Lake Storage Gen2 來源和接收端所支援的屬性清單。

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake 儲存體作為來源類型 Gen2

- 若要複製的 parquet 或分隔的文字格式，請參閱[Parquet 和分隔的文字格式來源](#parquet-and-delimited-text-format-source)一節。
- 若要複製其他格式，例如 ORC、 Avro、 JSON 或二進位格式，請參閱[其他格式來源](#other-format-source)一節。

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet 和分隔的文字格式的來源

若要將資料從 Data Lake 儲存體 Gen2 複製 parquet 或分隔的文字格式，請參閱[Parquet 格式](format-parquet.md)並[分隔文字格式](format-delimited-text.md)格式為基礎的複製活動來源和支援的設定上的文章。 支援下列屬性下的 Data Lake 儲存體 Gen2`storeSettings`格式為基礎的複製來源中的設定：

| 屬性                 | 描述                                                  | 必要項                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | [類型] 屬性底下`storeSettings`必須設為**AzureBlobFSReadSetting**。 | 是                                           |
| recursive                | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 當遞迴設定為 true 且接收是檔案型存放區，空的資料夾或子資料夾不是複製或建立在接收上。 允許的值為 **true** (預設值) 和 **false**。 | 否                                            |
| wildcardFolderPath       | 使用萬用字元，在 設定在資料集篩選器的來源資料夾中指定的檔案系統下資料夾路徑。 <br>允許使用萬用字元`*`（比對零或多個字元） 和`?`(比對零或單一字元)。 使用`^`來逸出，如果您的實際資料夾名稱有萬用字元或內此逸出字元。 <br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 否                                            |
| wildcardFileName         | 在指定的檔案系統 + folderPath/wildcardFolderPath 篩選來源檔案的萬用字元在檔名。 <br>允許使用萬用字元`*`（比對零或多個字元） 和`?`(比對零或單一字元)。 使用`^`來逸出，如果您的實際資料夾名稱有萬用字元或內此逸出字元。 如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | [是] 如果`fileName`未指定資料集中 |
| modifiedDatetimeStart    | 上次修改的屬性為基礎的檔案篩選條件。 如果其上次修改的時間之間的時間範圍內，會選取的檔案`modifiedDatetimeStart`和`modifiedDatetimeEnd`。 時間會套用至 UTC 時區，格式為"2018年-12-01T05:00:00Z"。 <br> 屬性可以是 NULL，這表示，任何檔案的屬性篩選條件會套用至資料集。 當`modifiedDatetimeStart`具有日期時間值，但`modifiedDatetimeEnd`是 NULL，則表示其上次修改屬性的檔案大於或者等於 datetime 值會選取。 當`modifiedDatetimeEnd`具有日期時間值，但`modifiedDatetimeStart`是 NULL，這表示會選取其最後一個已修改的屬性小於日期時間值的檔案。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 同時連接到儲存體存放區的連線數目。 只有在您想要限制資料存放區的並行連接時，才指定。 | 否                                            |

> [!NOTE]
> Parquet 或分隔的文字格式**AzureBlobFSSource**為是為了回溯相容性仍支援類型複製活動來源中下一節所述。 我們建議您使用接下來的這個新模型。 編寫 UI 的資料處理站產生這些新的型別。

**範例：**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>其他格式的來源

若要複製資料從 Data Lake 儲存體 Gen2 ORC、 Avro、 JSON 或二進位格式，將複製活動中支援下列屬性**來源**區段：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設為 **AzureBlobFSSource**。 |是 |
| recursive | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 當遞迴設定為 true 且接收是檔案型存放區，空的資料夾或子資料夾不是複製或建立在接收上。<br/>允許的值為 **true** (預設值) 和 **false**。 | 否 |
| maxConcurrentConnections | 同時連接到資料存放區的連線數目。 只有在您想要限制資料存放區的並行連接時，才指定。 | 否 |

**範例：**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
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
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>以 Azure Data Lake Storage Gen2 作為接收類型

- 若要複製至 parquet 或分隔的文字格式，請參閱[Parquet 和分隔的文字格式接收](#parquet-and-delimited-text-format-sink)一節。
- 若要複製到其他格式，例如 ORC、 Avro、 JSON 或二進位格式，請參閱[其他格式接收](#other-format-sink)一節。

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet 和分隔的文字格式接收

若要將資料複製到 Data Lake 儲存體 Gen2 中，以 parquet 或分隔的文字格式，請參閱[Parquet 格式](format-parquet.md)並[分隔文字格式](format-delimited-text.md)格式為基礎的複製活動接收器和支援的設定上的文章。 支援下列屬性下的 Data Lake 儲存體 Gen2`storeSettings`中格式為基礎的複製接收設定：

| 屬性                 | 描述                                                  | 必要項 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | [類型] 屬性底下`storeSettings`必須設為**AzureBlobFSWriteSetting**。 | 是      |
| copyBehavior             | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都會在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來源資料夾的所有檔案合併成一個檔案。 若已指定檔案名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否       |
| maxConcurrentConnections | 同時連接到資料存放區的連線數目。 只有在您想要限制資料存放區的並行連接時，才指定。 | 否       |

> [!NOTE]
> Parquet 或分隔的文字格式**AzureBlobFSSink**為是為了回溯相容性仍支援類型複製活動接收器中下一節所述。 我們建議您使用接下來的這個新模型。 編寫 UI 的資料處理站產生這些新的型別。

**範例：**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobFSWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>其他格式接收

若要將資料複製到 Data Lake 儲存體 Gen2 ORC、 Avro、 JSON 或二進位格式，支援下列屬性**接收**區段：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動接收的類型屬性必須設為 **AzureBlobFSSink**。 |是 |
| copyBehavior | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都會在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來源資料夾的所有檔案合併成一個檔案。 若已指定檔案名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否 |
| maxConcurrentConnections | 同時連接到資料存放區的連線數目。 只有在您想要限制資料存放區的並行連接時，才指定。 | 否 |

**範例：**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>資料夾和檔案篩選範例

本節描述含有萬用字元篩選之資料夾路徑和檔案名稱所產生的行為。

| folderPath | fileName | recursive | 來源資料夾結構和篩選結果 (會擷取以**粗體**顯示的檔案)|
|:--- |:--- |:--- |:--- |
| `Folder*` | 空的 (使用預設值） | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | 空的 (使用預設值） | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>一些遞迴和 copyBehavior 範例

本章節描述不同的遞迴和 copyBehavior 值組合的複製作業所產生的行為。

| 遞迴 | copyBehavior | 來源資料夾結構 | 產生的目標 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以與來源相同的結構，建立目標 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File3 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File4 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File5 自動產生的名稱 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 的內容會合併成一個檔案，並具有自動產生的檔案名稱。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>File3、 File4，與 File5 的 Subfolder1 不挑選。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/><br/>File3、 File4，與 File5 的 Subfolder1 不挑選。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的內容會合併成一個檔案，並具有自動產生的檔案名稱。 針對 File1 自動產生的名稱<br/><br/>File3、 File4，與 File5 的 Subfolder1 不挑選。 |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>保留從 Data Lake 儲存體 Gen1 的 Acl

>[!TIP]
>若要將資料從 Azure Data Lake 儲存體 Gen1 複製到 Gen2 一般情況下，請參閱[將資料從 Azure Data Lake 儲存體 Gen1 複製至使用 Azure Data Factory 的 Gen2](load-azure-data-lake-storage-gen2-from-gen1.md)如需逐步解說和最佳做法。

當您從 Azure Data Lake 儲存體 Gen1 複製檔案至 Gen2 時，您可以選擇保留的 POSIX 存取控制清單 (Acl) 以及資料。 如需有關存取控制的詳細資訊，請參閱 < [Azure Data Lake 儲存體 Gen1 中的存取控制](../data-lake-store/data-lake-store-access-control.md)並[存取控制，在 Azure Data Lake 儲存體 Gen2](../storage/blobs/data-lake-storage-access-control.md)。

使用 Azure Data Factory 複製活動，可以保留下列類型的 Acl。 您可以選取一或多個類型：

- **ACL**:複製並保留檔案和目錄上的 POSIX 存取控制清單。 它會從來源到接收複製完整現有的 Acl。 
- **擁有者**：複製並保留檔案和目錄的擁有使用者。 需要接收 Data Lake 儲存體 Gen2 超級使用者存取。
- **群組**:複製並保留檔案和目錄的擁有群組。 需要超級使用者存取到接收資料湖儲存體 Gen2 或擁有使用者 （如果擁有使用者也是目標群組的成員）。

如果您指定要複製資料夾中，Data Factory 會複寫該指定的資料夾的檔案和在其下方的目錄的 Acl 如果`recursive`設為 true。 如果您指定要複製單一檔案，則會複製該檔案的 Acl。

>[!IMPORTANT]
>當您選擇保留 Acl 時，請確定您高足夠權限授與 Data Factory 接收器 Data Lake 儲存體 Gen2 帳戶對其運算。 例如，使用帳戶金鑰驗證，或將儲存體 Blob 資料擁有者角色指派給服務主體或受控身分識別。

當您設定來源與 Data Lake 儲存體 Gen1 具有二進位複製 選項或二進位格式和接收器為 Data Lake 儲存體 Gen2 二進位複製 選項或二進位格式時，您可以發現**保留**選項**複製資料工具設定**頁面上，或在**複製活動** > **設定**的活動撰寫功能 索引標籤。

![Data Lake 儲存體 Gen1 為 Gen2 Preserve ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

以下是範例 JSON 設定 (請參閱`preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

深入了解[來源轉換](data-flow-source.md)並[接收轉換](data-flow-sink.md)中對應資料的資料流程功能。

## <a name="next-steps"></a>後續步驟

如需 Data Factory 中的複製活動所支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。
