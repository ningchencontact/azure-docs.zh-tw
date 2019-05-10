---
title: 使用 Data Factory 從 Azure Data Lake Storage Gen2 來回複製資料 | Microsoft Docs
description: 了解如何使用 Azure Data Factory 將資料複製到 Azure Data Lake Storage Gen2 或從該處複製資料。
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 3fcedc74cde9e26ea53d2475f0e9805788787f2d
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228624"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>使用 Azure Data Factory 從 Azure Data Lake Storage Gen2 來回複製資料

Azure Data Lake 儲存體 Gen2 (ADLS Gen2) 是一份專用於巨量資料分析、 內建的功能[Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)。 此功能可讓您使用檔案系統和物件儲存範例連接您的資料。

本文概述如何從 Azure Data Lake 儲存體 Gen2 來回複製資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

此 Azure Data Lake 儲存體 Gen2 連接器支援下列活動：

- [複製活動](copy-activity-overview.md)與[支援來源/接收器矩陣](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)

具體而言，此連接器支援：

- 使用帳戶金鑰、服務主體或 Azure 資源的受控識別驗證來複製資料。
- 依原樣複製檔案，或使用[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs.md)來剖析或產生檔案。

>[!TIP]
>如果您啟用階層命名空間，Blob 和 ADLS Gen2 API 之間目前並沒有作業的互通性。 如果發生 "ErrorCode=FilesystemNotFound" 的錯誤 (詳細訊息為「指定的檔案系統不存在。」)，這是因為指定的接收檔案系統是透過 Blob API 而建立的，而不是 ADLS Gen2 API。 若要修正此問題，請使用目前未作為 Blob 容器名稱的名稱來指定新的檔案系統，ADF 即會在資料複製期間自動建立該檔案系統。

>[!NOTE]
>如果您啟用 Azure 儲存體防火牆設定的 [允許信任的 Microsoft 服務存取此儲存體帳戶] 選項，則使用 Azure Integration Runtime 連線到 Data Lake Storage Gen2 會失敗並出現禁止錯誤，因為 ADF 並未被視為信任的 Microsoft 服務。 請改用自我裝載 Integration Runtime 作為連線方式。

## <a name="get-started"></a>開始使用

>[!TIP]
>如需使用 Data Lake Storage Gen2 連接器的逐步解說，請參閱[將資料載入 Data Lake Storage Gen2 中](load-azure-data-lake-storage-gen2.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下各節將針對用來定義 Data Lake Storage Gen2 專屬 Data Factory 實體的屬性提供相關詳細資料。

## <a name="linked-service-properties"></a>連結服務屬性

Azure Data Lake Storage Gen2 連接器支援下列驗證類型，請參閱詳細資料的對應章節：

- [帳戶金鑰驗證](#account-key-authentication)
- [服務主體驗證](#service-principal-authentication)
- [Azure 資源的受控識別驗證](#managed-identity)

### <a name="account-key-authentication"></a>帳戶金鑰驗證

若要使用儲存體帳戶金鑰驗證，以下是支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| 類型 | 類型屬性必須設為 **AzureBlobFS**。 |有 |
| url | 具有 `https://<accountname>.dfs.core.windows.net` 模式的 Data Lake Storage Gen2 所適用的端點。 | 有 |
| accountKey | Data Lake Storage Gen2 服務的帳戶金鑰。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載 Integration Runtime (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

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

若要使用服務主體驗證，請遵循下列步驟：

1. 遵循[使用 Azure AD 租用戶註冊應用程式](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)，以在 Azure Active Directory (Azure AD) 中註冊應用程式實體。 請記下以下的值，您可以使用這些值來定義連結服務：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 授與服務主體適當權限。

    - **做為來源**，在儲存體總管中，至少授與**讀取 + 執行**來列出和複製資料夾和子資料夾中的檔案，或授與的權限**讀取**複製單一檔案的權限。 或者，在存取控制 (IAM)，請至少授與**儲存體 Blob 資料讀者**角色。
    - **作為接收器**，在儲存體總管中，至少授與**寫入 + 執行**權限建立子資料夾中的項目。 或者，在存取控制 (IAM)，請至少授與**儲存體 Blob 資料參與者**角色。

>[!NOTE]
>清單資料夾啟動從帳戶層級，或若要測試連線，您需要設定服務主體授與的權限**儲存體帳戶中 IAM 的 「 執行 」 權限**。 您使用下列項目時，這種情況即會成立：
>- 以 **複製資料工具**製作複製管線。
>- 以 **Data Factory UI** 在製作期間測試連線和瀏覽資料夾。 
>如果您有在帳戶層級的權限授與需要考量，您可以略過測試連接，並輸入的路徑以手動方式在撰寫期間。 複製活動仍然可以使用，只要服務主體授與具有適當的權限在檔案複製。

以下是連結服務支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| 類型 | 類型屬性必須設為 **AzureBlobFS**。 |有 |
| url | 具有 `https://<accountname>.dfs.core.windows.net` 模式的 Data Lake Storage Gen2 所適用的端點。 | 有 |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 是 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為 **SecureString**，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 將滑鼠游標暫留在 Azure 入口網站右上角，即可擷取它。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載 Integration Runtime (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

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

資料處理站可與 [Azure 資源的受控識別](data-factory-service-identity.md)相關聯，後者表示特定的資料處理站。 您可以直接使用這個受管理的身分識別，ADLS Gen2 驗證類似於使用您自己的服務主體。 它可讓這個指定的處理站可以存取及複製資料，或將您的 ADLS Gen2。

若要使用 Azure 資源的受控識別驗證，請遵循下列步驟：

1. [擷取的資料處理站的受管理身分識別資訊](data-factory-service-identity.md#retrieve-managed-identity)藉由複製"SERVICE IDENTITY APPLICATION ID"站一起產生的值。

2. 授與受管理的身分識別適當的權限。 

    - **做為來源**，在儲存體總管中，至少授與**讀取 + 執行**來列出和複製資料夾和子資料夾中的檔案，或授與的權限**讀取**複製單一檔案的權限。 或者，在存取控制 (IAM)，請至少授與**儲存體 Blob 資料讀者**角色。
    - **作為接收器**，在儲存體總管中，至少授與**寫入 + 執行**權限建立子資料夾中的項目。 或者，在存取控制 (IAM)，請至少授與**儲存體 Blob 資料參與者**角色。

>[!NOTE]
>清單資料夾啟動從帳戶層級，或若要測試連線，您必須設定受管理的身分識別授與的權限**儲存體帳戶中 IAM 的 「 執行 」 權限**。 您使用下列項目時，這種情況即會成立：
>- 以 **複製資料工具**製作複製管線。
>- 以 **Data Factory UI** 在製作期間測試連線和瀏覽資料夾。 
>如果您有在帳戶層級的權限授與需要考量，您可以略過測試連接，並輸入的路徑以手動方式在撰寫期間。 複製活動仍然可以使用，只要受控身分識別時授與適當的權限在檔案複製。

以下是連結服務支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| 類型 | 類型屬性必須設為 **AzureBlobFS**。 |有 |
| url | 具有 `https://<accountname>.dfs.core.windows.net` 模式的 Data Lake Storage Gen2 所適用的端點。 | 有 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載 Integration Runtime (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 

- 針對**Parquet 和分隔的文字格式**，請參閱[Parquet 和分隔的文字格式的資料集](#parquet-and-delimited-text-format-dataset)一節。
- 為其他格式，例如**ORC/Avro/JSON/二進位格式**，請參閱[其他格式的資料集](#other-format-dataset)一節。

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet 和分隔的文字格式的資料集

若要從 ADLS Gen2 中來回複製資料**Parquet 或分隔的文字格式**，請參閱[Parquet 格式](format-parquet.md)和[分隔文字格式](format-delimited-text.md)格式為基礎的資料集上的文章和支援的設定。 支援下列屬性下的 ADLS Gen2`location`格式為基礎的資料集內的設定：

| 屬性   | 描述                                                  | 必要項 |
| ---------- | ------------------------------------------------------------ | -------- |
| 類型       | [類型] 屬性底下`location`資料集內必須設定為**AzureBlobFSLocation**。 | 有      |
| fileSystem | ADLS Gen2 檔案系統名稱。                              | 無       |
| folderPath | 在指定的檔案系統資料夾的路徑。 如果您想要使用萬用字元來篩選的資料夾，略過這項設定，並在 活動來源設定中指定。 | 否       |
| fileName   | 在指定的檔案系統 + folderPath 檔案名稱。 如果您想要使用萬用字元來篩選檔案，略過這項設定，並在 活動來源設定中指定。 | 無       |

> [!NOTE]
> **AzureBlobFSFile**下一節中所述的 Parquet] / [文字格式的類型資料集仍可作為-用於複製/查閱/GetMetadata 活動進行回溯相容性，但它不適用於對應資料流程。 若要使用這個新的模型，從現在開始，建議您，並撰寫 UI 的 ADF 已切換為產生這些新的類型。

**範例：**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen2 linked service name>",
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

若要從 ADLS Gen2 中來回複製資料**ORC/Avro/JSON/二進位格式**，支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| 類型 | 資料集的類型屬性必須設為 **AzureBlobFSFile**。 |有 |
| folderPath | Data Lake Storage Gen2 資料夾的路徑。 若未指定，它會指向根。 <br/><br/>支援萬用字元篩選，允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。 <br/><br/>範例： 檔案系統/資料夾/，請參閱中的更多範例[資料夾和檔案篩選條件範例](#folder-and-file-filter-examples)。 |無 |
| fileName | 在指定 "folderPath" 之下檔案的**名稱或萬用字元篩選**。 若未指定此屬性的值，資料集就會指向資料夾中的所有檔案。 <br/><br/>針對篩選，允許的萬用字元為：`*` (符合零或多個字元) 和 `?` (符合零或單一字元)。<br/>- 範例 1：`"fileName": "*.csv"`<br/>- 範例 2：`"fileName": "???20180427.txt"`<br/>如果實際檔案名稱內有萬用字元或逸出字元 `^`，請使用此逸出字元來逸出。<br/><br/>沒有為輸出資料集指定 fileName 且活動接收器中未指定 **preserveHierarchy** 時，複製活動會自動以下列模式產生檔案名稱：「*資料。[活動執行識別碼 GUID。][GUID 如果 FlattenHierarchy]。[格式設定]。[壓縮設定]*"，例如"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"；如果您使用資料表名稱而非查詢，從表格式來源進行複製，則名稱模式會是 "*[資料表名稱].[格式].[壓縮 (如果已設定)]*"，例如"MyTable.csv"。 |否 |
| modifiedDatetimeStart | 檔案篩選會根據以下屬性：上次修改時間。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意會影響資料移動的整體效能，藉由啟用此設定，當您想要進行大量檔案從檔案篩選器。 <br/><br/> 屬性可以是 NULL，表示任何檔案的屬性篩選條件會套用至資料集。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。| 否 |
| modifiedDatetimeEnd | 檔案篩選會根據以下屬性：上次修改時間。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br/><br/> 請注意會影響資料移動的整體效能，藉由啟用此設定，當您想要進行大量檔案從檔案篩選器。 <br/><br/> 屬性可以是 NULL，表示任何檔案的屬性篩選條件會套用至資料集。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。| 否 |
| format | 如果您想要在檔案型存放區之間依原樣複製檔案 (二進位複本)，請在輸入和輸出資料集定義中略過格式區段。<br/><br/>如果您想要剖析或產生特定格式的檔案，以下是支援的檔案格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 將 [format] 下的 [type] 屬性設定為下列其中一個值。 如需詳細資訊，請參閱[文字格式](supported-file-formats-and-compression-codecs.md#text-format)、[JSON 格式](supported-file-formats-and-compression-codecs.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs.md#parquet-format)小節。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs.md#compression-support)。<br/>支援的類型為：GZip、Deflate、BZip2 及 ZipDeflate。<br/>支援的層級為 **Optimal** 和 **Fastest**。 |無 |

>[!TIP]
>若要複製資料夾下的所有檔案，請只指定 **folderPath**。<br>若要複製指定名稱的單一檔案，請以資料夾部分指定 **folderPath**，並以檔案名稱指定 **fileName**。<br>若要複製資料夾下的檔案子集，請以資料夾部分指定 **folderPath**，並以萬用字元篩選指定 **fileName**。 

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

如需可用來定義活動的區段和屬性完整清單，請參閱[複製活動組態](copy-activity-overview.md#configuration)和[管線和活動](concepts-pipelines-activities.md)一文。 本節提供 Data Lake Storage Gen2 來源和接收端所支援的屬性清單。

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>以 Azure Data Lake Storage Gen2 作為來源類型

- 從複製**Parquet 和分隔的文字格式**，請參閱[Parquet 和分隔的文字格式來源](#parquet-and-delimited-text-format-source)一節。
- 為其他格式的副本**ORC/Avro/JSON/二進位格式**，請參閱[其他格式來源](#other-format-source)一節。

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet 和分隔的文字格式的來源

若要將資料從 ADLS Gen2 中複製**Parquet 或分隔的文字格式**，請參閱[Parquet 格式](format-parquet.md)並[分隔文字格式](format-delimited-text.md)上格式為基礎的複製活動來源的文件和支援的設定。 支援下列屬性下的 ADLS Gen2`storeSettings`格式為基礎的複製來源中的設定：

| 屬性                 | 描述                                                  | 必要項                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| 類型                     | [類型] 屬性底下`storeSettings`必須設為**AzureBlobFSReadSetting**。 | 有                                           |
| recursive                | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當遞迴設定為 true 且接收是檔案型存放區時，就不會在接收上複製或建立空的資料夾或子資料夾。 允許的值為 **true** (預設值) 和 **false**。 | 否                                            |
| wildcardFolderPath       | 使用萬用字元，在 設定在資料集篩選器的來源資料夾中指定的檔案系統下資料夾路徑。 <br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。 <br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 否                                            |
| wildcardFileName         | 在指定的檔案系統 + folderPath/wildcardFolderPath 篩選來源檔案的萬用字元在檔名。 <br>允許的萬用字元為：`*` (比對零或多個字元) 和 `?` (比對零或單一字元)；如果您的實際資料夾名稱包含萬用字元或此逸出字元，請使用 `^` 來逸出。  如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | [是] 如果`fileName`未指定資料集中 |
| modifiedDatetimeStart    | 檔案篩選會根據以下屬性：上次修改時間。 如果檔案的上次修改時間在 `modifiedDatetimeStart` 與 `modifiedDatetimeEnd` 之間的時間範圍內，系統就會選取該檔案。 此時間會以 "2018-12-01T05:00:00Z" 格式套用至 UTC 時區。 <br> 屬性可以是 NULL，這意謂著不會在資料集套用任何檔案屬性篩選。  當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 NULL 時，意謂著系統將會選取上次更新時間屬性大於或等於此日期時間值的檔案。  當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 NULL 時，則意謂著系統將會選取上次更新時間屬性小於此日期時間值的檔案。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 同時連接到儲存體存放區的連線數目。 只有在您想要限制資料存放區的並行連接時，才指定。 | 無                                            |

> [!NOTE]
> Parquet/分隔的文字格式，如**AzureBlobFSSource**類型下一節中所述的複製活動來源仍可作為-是為了回溯相容性。 若要使用這個新的模型，從現在開始，建議您，並撰寫 UI 的 ADF 已切換為產生這些新的類型。

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

若要將資料複製在 ADLS Gen2 **ORC/Avro/JSON/二進位格式**，以下支援的屬性將複製活動中**來源**區段：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| 類型 | 複製活動來源的類型屬性必須設為 **AzureBlobFSSource**。 |有 |
| recursive | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 請注意，當遞迴設定為 true 且接收是檔案型存放區時，就不會在接收上複製或建立空的資料夾或子資料夾。<br/>允許的值為 **true** (預設值) 和 **false**。 | 否 |
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

- 若要複製**Parquet 和分隔的文字格式**，請參閱[Parquet 和分隔的文字格式接收](#parquet-and-delimited-text-format-sink)區段。
- 複製為其他格式，例如**ORC/Avro/JSON/二進位格式**，請參閱[其他格式接收](#other-format-sink)一節。

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet 和分隔的文字格式接收

若要將資料複製到在 ADLS Gen2 **Parquet 或分隔的文字格式**，請參閱[Parquet 格式](format-parquet.md)和[分隔文字格式](format-delimited-text.md)格式為基礎的複製活動接收器上的文章和支援的設定。 支援下列屬性下的 ADLS Gen2`storeSettings`中格式為基礎的複製接收設定：

| 屬性                 | 描述                                                  | 必要項 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| 類型                     | [類型] 屬性底下`storeSettings`必須設為**AzureBlobFSWriteSetting**。 | 有      |
| copyBehavior             | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都會在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來源資料夾的所有檔案合併成一個檔案。 若已指定檔案名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否       |
| maxConcurrentConnections | 同時連接到資料存放區的連線數目。 只有在您想要限制資料存放區的並行連接時，才指定。 | 無       |

> [!NOTE]
> Parquet/分隔的文字格式，如**AzureBlobFSSink**為仍然支援類型下一節中所述的複製活動接收器-是為了回溯相容性。 若要使用這個新的模型，從現在開始，建議您，並撰寫 UI 的 ADF 已切換為產生這些新的類型。

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

若要將資料複製到在 ADLS Gen2 **ORC/Avro/JSON/二進位格式**中, 支援下列屬性**接收**區段：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| 類型 | 複製活動接收的類型屬性必須設為 **AzureBlobFSSink**。 |有 |
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
| `Folder*` | (空白，使用預設值) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (空白，使用預設值) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>一些遞迴和 copyBehavior 範例

本節說明遞迴和 copyBehavior 值在不同組合的情況下，複製作業所產生的行為。

| recursive | copyBehavior | 來源資料夾結構 | 產生的目標 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以與來源相同的結構，建立目標資料夾 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File3 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File4 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File5 自動產生的名稱 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 的內容會合併成一個檔案，並具有自動產生的檔案名稱。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>系統不會挑選含有 File3、File4、File5 的 Subfolder1。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/><br/>系統不會挑選含有 File3、File4、File5 的 Subfolder1。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的內容會合併成一個檔案，並具有自動產生的檔案名稱。 針對 File1 自動產生的名稱<br/><br/>系統不會挑選含有 File3、File4、File5 的 Subfolder1。 |

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

了解詳細資料，從[來源轉換](data-flow-source.md)並[接收轉換](data-flow-sink.md)中對應的資料流。

## <a name="next-steps"></a>後續步驟

如需 Data Factory 中的複製活動所支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。
