---
title: 使用 Data Factory 在 Azure Data Lake Storage Gen2 複製資料Microsoft Docs
description: 瞭解如何使用 Azure Data Factory，在 Azure Data Lake Storage Gen2 中複製資料。
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 8f190f6b933c61072df9af954c8db01497e35e82
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010221"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>使用 Azure Data Factory 從 Azure Data Lake Storage Gen2 來回複製資料

Azure Data Lake Storage Gen2 （ADLS Gen2）是內建于[Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)中的大型資料分析專用的一組功能。 您可以使用檔案系統和物件儲存範例，將它與您的資料互動。

本文概述如何在 Azure Data Lake Storage Gen2 之間複製資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Azure Data Lake Storage Gen2 連接器：

- [複製活動](copy-activity-overview.md)與[支援的來源/接收矩陣](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)

具體而言，使用此連接器，您可以：

- 使用帳戶金鑰、服務主體或 Azure 資源的受控識別驗證來複製資料。
- 複製檔案，或使用[支援的檔案格式和壓縮編解碼器](supported-file-formats-and-compression-codecs.md)來剖析或產生檔案。

>[!TIP]
>如果您啟用階層式命名空間，則 Blob 與 Data Lake Storage Gen2 Api 之間的作業目前沒有互通性。 如果您遇到「ErrorCode = FilesystemNotFound」錯誤，並顯示「指定的檔案系統不存在」訊息，則是由 Blob API 建立的指定接收檔案系統所造成，而不是在別處 Data Lake Storage Gen2 API。 若要修正此問題，請使用不存在於 Blob 容器名稱的名稱來指定新的檔案系統。 然後 Data Factory 在資料複製期間自動建立該檔案系統。

>[!NOTE]
>如果您在 Azure 儲存體防火牆設定 中啟用 **允許受信任的 Microsoft 服務存取此儲存體帳戶** 選項，Azure 整合執行時間就不會連線到 Data Lake Storage Gen2 並顯示禁止的錯誤。 因為不會將 Data Factory 視為受信任的 Microsoft 服務，所以會顯示錯誤訊息。 請改用自我裝載整合執行時間來連接。

## <a name="get-started"></a>開始使用

>[!TIP]
>如需如何使用 Data Lake Storage Gen2 連接器的逐步解說，請參閱將[資料載入 Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關資訊，這些屬性是用來定義 Data Lake Storage Gen2 特有的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

Azure Data Lake Storage Gen2 連接器支援下列驗證類型。 如需詳細資訊，請參閱對應的章節：

- [帳戶金鑰驗證](#account-key-authentication)
- [服務主體驗證](#service-principal-authentication)
- [Azure 資源的受控識別驗證](#managed-identity)

>[!NOTE]
>使用 PolyBase 將資料載入 SQL 資料倉儲時，如果您的來源 Data Lake Storage Gen2 已設定虛擬網路端點，則必須依照 PolyBase 的要求，使用受控識別驗證。 如需更多設定必要條件，請參閱[受控識別驗證](#managed-identity)一節。

### <a name="account-key-authentication"></a>帳戶金鑰驗證

若要使用儲存體帳戶金鑰驗證，以下是支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | 類型屬性必須設為 **AzureBlobFS**。 |是 |
| url | 具有模式之`https://<accountname>.dfs.core.windows.net`Data Lake Storage Gen2 的端點。 | 是 |
| accountKey | Data Lake Storage Gen2 的帳戶金鑰。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果您的資料存放區位於私人網路中，您可以使用 Azure integration runtime 或自我裝載整合執行時間。 如果未指定此屬性，則會使用預設的 Azure integration runtime。 |否 |

>[!NOTE]
>使用帳戶金鑰驗證時，不支援次要 ADLS 檔案系統端點。 您可以使用其他驗證類型。

**範例:**

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

1. 遵循[向 Azure AD 租使用者註冊您的應用程式](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)中的步驟，在 Azure Active Directory （Azure AD）中註冊應用程式實體。 請記下以下的值，您可以使用這些值來定義連結服務：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 授與服務主體適當的許可權。 深入瞭解許可權在檔案[和目錄的存取控制清單](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)Data Lake Storage Gen2 中的運作方式

    - **作為來源**：在儲存體總管中，至少授與從來源檔案系統開始的 [**執行**] 許可權，以及要複製之檔案的 [**讀取**] 許可權。 或者，在 [存取控制（IAM）] 中，至少授與 [**儲存體 Blob 資料讀取**者] 角色。
    - **作為接收**：在儲存體總管中，至少授與從接收檔案系統開始的 [**執行**] 許可權，以及 [接收] 資料夾的 [**寫入**] 許可權。 或者，在 [存取控制（IAM）] 中，至少授與 [**儲存體 Blob 資料參與者**] 角色。

>[!NOTE]
>若要列出從帳戶層級開始或測試連線的資料夾，您必須**使用 IAM 中的「儲存體 Blob 資料讀取器」許可權**，設定授與儲存體帳戶之服務主體的許可權。 您使用下列項目時，這種情況即會成立：
>- **複製資料工具**以撰寫複製管線。
>- 以 **Data Factory UI** 在製作期間測試連線和瀏覽資料夾。 
>如果您有關于在帳戶層級授與許可權的問題，請在撰寫期間略過測試連線，並輸入具有已授與許可權的父路徑，然後選擇從該指定的路徑進行流覽。 只要服務主體被授與要複製之檔案的適當許可權，複製活動就會運作。

連結服務支援這些屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | 類型屬性必須設為 **AzureBlobFS**。 |是 |
| url | 具有模式之`https://<accountname>.dfs.core.windows.net`Data Lake Storage Gen2 的端點。 | 是 |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 是 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位`SecureString`標記為，以安全地將它儲存在 Data Factory 中。 或者，您可以[參考儲存在 Azure Key Vault 中的秘密](store-credentials-in-key-vault.md)。 | 是 |
| 租用戶 | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 將滑鼠游標暫留在 Azure 入口網站的右上角來取出。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果您的資料存放區位於私人網路中，您可以使用 Azure integration runtime 或自我裝載整合執行時間。 如果未指定，則會使用預設的 Azure integration runtime。 |否 |

**範例:**

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

資料處理站可與 [Azure 資源的受控識別](data-factory-service-identity.md)相關聯，後者表示特定的資料處理站。 您可以直接將此受控識別用於 Data Lake Storage Gen2 驗證，類似于使用您自己的服務主體。 它可讓這個指定的處理站在您的 Data Lake Storage Gen2 中存取和複製資料。

若要使用受控識別進行 Azure 資源驗證，請遵循下列步驟。

1. 藉由複製與您的處理站一起產生的**服務身分識別應用程式識別碼**值，來抓取[Data Factory 的受控識別資訊](data-factory-service-identity.md#retrieve-managed-identity)。

2. 授與受控識別適當的許可權。 深入瞭解許可權在檔案[和目錄的存取控制清單](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)Data Lake Storage Gen2 中的運作方式。

    - **作為來源**：在儲存體總管中，至少授與從來源檔案系統開始的 [**執行**] 許可權，以及要複製之檔案的 [**讀取**] 許可權。 或者，在 [存取控制（IAM）] 中，至少授與 [**儲存體 Blob 資料讀取**者] 角色。
    - **作為接收**：在儲存體總管中，至少授與從接收檔案系統開始的 [**執行**] 許可權，以及 [接收] 資料夾的 [**寫入**] 許可權。 或者，在 [存取控制（IAM）] 中，至少授與 [**儲存體 Blob 資料參與者**] 角色。

>[!NOTE]
>若要列出從帳戶層級開始或用來測試連線的資料夾，您必須**使用 IAM 中的「儲存體 Blob 資料讀取器」許可權**，將授與的受控識別許可權設定為儲存體帳戶。 您使用下列項目時，這種情況即會成立：
>- **複製資料工具**以撰寫複製管線。
>- 以 **Data Factory UI** 在製作期間測試連線和瀏覽資料夾。 
>如果您有關于在帳戶層級授與許可權的問題，請在撰寫期間略過測試連線，並輸入具有已授與許可權的父路徑，然後選擇從該指定的路徑進行流覽。 只要服務主體被授與要複製之檔案的適當許可權，複製活動就會運作。

>[!IMPORTANT]
>如果您使用 PolyBase 將資料從 Data Lake Storage Gen2 載入 SQL 資料倉儲，使用 Data Lake Storage Gen2 的受控識別驗證時，請確定您也遵循[本指南](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)中的步驟1和2到1）向註冊您的 SQL Database 伺服器Azure Active Directory （Azure AD）和2）將儲存體 Blob 資料參與者角色指派給您的 SQL Database 伺服器;其餘部分則由 Data Factory 處理。 如果您的 Data Lake Storage Gen2 已設定 Azure 虛擬網路端點，若要使用 PolyBase 來載入資料，您必須使用 PolyBase 所需的受控識別驗證。

連結服務支援這些屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | 類型屬性必須設為 **AzureBlobFS**。 |是 |
| url | 具有模式之`https://<accountname>.dfs.core.windows.net`Data Lake Storage Gen2 的端點。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果您的資料存放區位於私人網路中，您可以使用 Azure integration runtime 或自我裝載整合執行時間。 如果未指定，則會使用預設的 Azure integration runtime。 |否 |

**範例:**

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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)。

- 如需**Parquet、分隔的文字、json、avro 和二進位格式**，請參閱[Parquet、分隔的文字、json、avro 和二進位格式資料集](#format-based-dataset)一節。
- 如需**ORC 格式**之類的其他格式，請參閱[其他格式資料集](#other-format-dataset)一節。

### <a name="format-based-dataset"></a>Parquet、分隔的文字、JSON、Avro 和二進位格式資料集

若要將資料複製到**Parquet、分隔的文字、avro 或二進位格式**，請參閱格式為基礎的資料集上的[Parquet 格式](format-parquet.md)、[分隔的文字格式](format-delimited-text.md)、 [avro 格式](format-avro.md)和[二進位格式](format-binary.md)一文，以及支援的設定。 下列屬性支援以格式為基礎之`location`資料集的 [設定] 下的 Data Lake Storage Gen2：

| 屬性   | 描述                                                  | 必要項 |
| ---------- | ------------------------------------------------------------ | -------- |
| Type       | 資料集`location`內的類型屬性必須設定為**AzureBlobFSLocation**。 | 是      |
| fileSystem | Data Lake Storage Gen2 檔案系統名稱。                              | 否       |
| folderPath | 指定檔案系統下的資料夾路徑。 如果您想要使用萬用字元來篩選資料夾，請略過此設定，並在 [活動來源設定] 中指定它。 | 否       |
| fileName   | 指定檔案系統 + folderPath 的檔案名。 如果您想要使用萬用字元來篩選檔案，請略過此設定，並在 [活動來源設定] 中指定它。 | 否       |

> [!NOTE]
> 下一節中所提及的 parquet 或文字格式的**AzureBlobFSFile**類型資料集，仍受到針對回溯相容性複製、查閱或 GetMetadata 活動的支援。 但它不適用於對應的資料流程功能。 我們建議您繼續使用此新模型。 Data Factory 撰寫 UI 會產生這些新的類型。

**範例:**

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

### <a name="other-format-dataset"></a>其他格式資料集

若要以**ORC 格式**將資料複製到 Data Lake Storage Gen2，並將其複製到其中，則支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | 資料集的類型屬性必須設為 **AzureBlobFSFile**。 |是 |
| folderPath | Data Lake Storage Gen2 中的資料夾路徑。 若未指定，它會指向根。 <br/><br/>支援萬用字元篩選準則。 允許的萬用字元`*`為（符合零或多個字元`?` ）和（符合零或單一字元）。 如果`^`您的實際資料夾名稱包含萬用字元或此 escape 字元位於內部，請使用來進行 escape。 <br/><br/>範例： filesystem/folder/。 如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 |否 |
| fileName | 指定 "folderPath" 下之檔案的名稱或萬用字元篩選。 若未指定此屬性的值，資料集就會指向資料夾中的所有檔案。 <br/><br/>針對篩選，允許`*`的萬用字元為（符合零或多個字元）和`?` （符合零或單一字元）。<br/>- 範例 1：`"fileName": "*.csv"`<br/>- 範例 2：`"fileName": "???20180427.txt"`<br/>如果`^`您的實際檔案名包含萬用字元或此 escape 字元位於內部，請使用來進行 escape。<br/><br/>沒有為輸出資料集指定 fileName 且活動接收器中未指定 **preserveHierarchy** 時，複製活動會自動以下列模式產生檔案名稱：「*資料」。[活動執行識別碼 GUID]。[如果 FlattenHierarchy，則為 GUID]。[格式化（若已設定）]。[已設定壓縮]* ，例如 "Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a .txt. gz"。 如果您使用資料表名稱（而非查詢）從表格式來源複製，則名稱模式會是 " *[資料表名稱]. [格式]。[已設定壓縮]* ，例如 "MyTable. csv"。 |否 |
| modifiedDatetimeStart | 檔案會根據上次修改的屬性進行篩選。 如果上次修改時間是在和`modifiedDatetimeStart` `modifiedDatetimeEnd`之間的時間範圍內，則會選取檔案。 時間會以 "2018-12-01T05：00： 00Z" 的格式套用至 UTC 時區。 <br/><br/> 當您想要以大量檔案執行檔案篩選時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 屬性可以是 Null，這表示不會將檔案屬性篩選套用至資料集。 當`modifiedDatetimeStart`具有日期時間值， `modifiedDatetimeEnd`但為 Null 時，表示已選取上次修改屬性大於或等於日期時間值的檔案。 當`modifiedDatetimeEnd`具有日期時間值， `modifiedDatetimeStart`但為 Null 時，表示已選取上次修改屬性小於日期時間值的檔案。| 否 |
| modifiedDatetimeEnd | 檔案會根據上次修改的屬性進行篩選。 如果上次修改時間是在和`modifiedDatetimeStart` `modifiedDatetimeEnd`之間的時間範圍內，則會選取檔案。 時間會以 "2018-12-01T05：00： 00Z" 的格式套用至 UTC 時區。 <br/><br/> 當您想要以大量檔案執行檔案篩選時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 屬性可以是 Null，這表示不會將檔案屬性篩選套用至資料集。 當`modifiedDatetimeStart`具有日期時間值， `modifiedDatetimeEnd`但為 Null 時，表示已選取上次修改屬性大於或等於日期時間值的檔案。 當`modifiedDatetimeEnd`具有日期時間值， `modifiedDatetimeStart`但為 Null 時，表示已選取上次修改屬性小於日期時間值的檔案。| 否 |
| format | 如果您想要在檔案型存放區之間依原樣複製檔案 (二進位複本)，請在輸入和輸出資料集定義中略過格式區段。<br/><br/>如果您想要剖析或產生特定格式的檔案，以下是支援的檔案格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 將 [format] 下的 [type] 屬性設定為下列其中一個值。 如需詳細資訊，請參閱[文字格式](supported-file-formats-and-compression-codecs.md#text-format)、 [JSON 格式](supported-file-formats-and-compression-codecs.md#json-format)、 [Avro 格式](supported-file-formats-and-compression-codecs.md#avro-format)、 [ORC 格式](supported-file-formats-and-compression-codecs.md#orc-format)和[Parquet 格式](supported-file-formats-and-compression-codecs.md#parquet-format)章節。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs.md#compression-support)。<br/>支援的類型為：GZip、Deflate、BZip2 及 ZipDeflate。<br/>支援的層級為 **Optimal** 和 **Fastest**。 |否 |

>[!TIP]
>若要複製資料夾下的所有檔案，請只指定 **folderPath**。<br>若要複製指定名稱的單一檔案，請以資料夾部分指定**folderPath** ，並以檔案名指定**fileName** 。<br>若要複製資料夾下的檔案子集，請以資料夾部分指定**folderPath** ，並以萬用字元篩選來指定**檔案名**。 

**範例:**

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

如需可用來定義活動的區段和屬性完整清單，請參閱[複製活動](copy-activity-overview.md#configuration)設定和[管線和活動](concepts-pipelines-activities.md)。 本節提供 Data Lake Storage Gen2 來源和接收端所支援的屬性清單。

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 作為來源類型

- 若要從**Parquet、分隔文字、json、avro 和二進位格式**複製，請參閱[Parquet、分隔文字、json、avro 和二進位格式來源](#format-based-source)一節。
- 若要從**ORC 格式**之類的其他格式複製，請參閱[其他格式來源](#other-format-source)一節。

#### <a name="format-based-source"></a>Parquet、分隔的文字、JSON、Avro 和二進位格式來源

若要從**Parquet、分隔文字、JSON、Avro 和二進位格式**複製資料，請參閱格式為基礎之複製活動來源的[Parquet 格式](format-parquet.md)、[分隔文字格式](format-delimited-text.md)、 [Avro 格式](format-avro.md)和[二進位格式](format-binary.md)文章，並加以支援設置。 下列屬性支援以格式為基礎之`storeSettings`複製來源的 [設定] 下的 Data Lake Storage Gen2：

| 屬性                 | 描述                                                  | 必要項                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| Type                     | 底下的 type 屬性`storeSettings`必須設定為**AzureBlobFSReadSetting**。 | 是                                           |
| recursive                | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 當遞迴設定為 true 且接收是以檔案為基礎的存放區時，不會在接收時複製或建立空的資料夾或子資料夾。 允許的值為 **true** (預設值) 和 **false**。 | 否                                            |
| wildcardFolderPath       | 在指定的檔案系統上，設定要用來篩選源資料集的目的檔案夾路徑，其中包含萬用字元。 <br>允許的萬用字元`*`為（符合零或多個字元`?` ）和（符合零或單一字元）。 如果`^`您的實際資料夾名稱包含萬用字元或內的此 escape 字元，請使用來進行 escape。 <br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 否                                            |
| wildcardFileName         | 指定檔案系統 + folderPath/wildcardFolderPath 下具有萬用字元的檔案名，用以篩選來源檔案。 <br>允許的萬用字元`*`為（符合零或多個字元`?` ）和（符合零或單一字元）。 如果`^`您的實際資料夾名稱包含萬用字元或內的此 escape 字元，請使用來進行 escape。 如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 如果未`fileName`在資料集中指定，則為是 |
| modifiedDatetimeStart    | 檔案會根據上次修改的屬性進行篩選。 如果上次修改時間是在和`modifiedDatetimeStart` `modifiedDatetimeEnd`之間的時間範圍內，則會選取檔案。 時間會以 "2018-12-01T05：00： 00Z" 的格式套用至 UTC 時區。 <br> 屬性可以是 Null，這表示不會將檔案屬性篩選套用至資料集。 當`modifiedDatetimeStart`具有日期時間值， `modifiedDatetimeEnd`但為 Null 時，表示已選取其上次修改屬性大於或等於 datetime 值的檔案。 當`modifiedDatetimeEnd`具有日期時間值， `modifiedDatetimeStart`但為 Null 時，表示已選取上次修改屬性小於日期時間值的檔案。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 同時連接到儲存體存放區的連線數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | 否                                            |

> [!NOTE]
> 針對 parquet 或分隔的文字格式，仍然支援下一節中所述的**AzureBlobFSSource**類型複製活動來源，因為這是為了回溯相容性。 我們建議您繼續使用此新模型。 Data Factory 撰寫 UI 會產生這些新的類型。

**範例:**

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

#### <a name="other-format-source"></a>其他格式來源

若要從**ORC 格式**的 Data Lake Storage Gen2 複製資料，複製活動的 [**來源**] 區段中支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | 複製活動來源的類型屬性必須設為 **AzureBlobFSSource**。 |是 |
| recursive | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 當遞迴設定為 true 且接收是以檔案為基礎的存放區時，不會在接收時複製或建立空的資料夾或子資料夾。<br/>允許的值為 **true** (預設值) 和 **false**。 | 否 |
| maxConcurrentConnections | 同時連接到資料存放區的連接數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | 否 |

**範例:**

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

- 若要複製到**Parquet、分隔的文字、json、avro 和二進位格式**，請參閱[Parquet、分隔的文字、json、avro 和二進位格式接收](#format-based-sink)一節。
- 若要複製到其他格式，例如**ORC/JSON 格式**，請參閱[其他格式接收](#other-format-sink)一節。

#### <a name="format-based-sink"></a>Parquet、分隔的文字、JSON、Avro 和二進位格式接收

若要將資料複製到**Parquet、分隔的文字、JSON、Avro 和二進位格式**，請參閱格式為基礎之複製活動接收和支援的[Parquet 格式](format-parquet.md)、[分隔文字格式](format-delimited-text.md)、 [Avro 格式](format-avro.md)和[二進位格式](format-binary.md)一文。設置。 下列屬性支援以格式為基礎之`storeSettings`複製接收的 [設定] 下的 Data Lake Storage Gen2：

| 屬性                 | 描述                                                  | 必要項 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| Type                     | 底下的 type 屬性`storeSettings`必須設定為**AzureBlobFSWriteSetting**。 | 是      |
| copyBehavior             | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都會在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來源資料夾的所有檔案合併成一個檔案。 若已指定檔案名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否       |
| maxConcurrentConnections | 同時連接到資料存放區的連接數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | 否       |

> [!NOTE]
> 針對 parquet 或分隔的文字格式，仍然支援下一節中所述的**AzureBlobFSSink**類型複製活動接收器，因為這是為了回溯相容性。 我們建議您繼續使用此新模型。 Data Factory 撰寫 UI 會產生這些新的類型。

**範例:**

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

#### <a name="other-format-sink"></a>其他格式接收器

若要將資料複製到**ORC 格式**的 Data Lake Storage Gen2， **sink**區段中支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| Type | 複製活動接收的類型屬性必須設為 **AzureBlobFSSink**。 |是 |
| copyBehavior | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都會在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來源資料夾的所有檔案合併成一個檔案。 若已指定檔案名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否 |
| maxConcurrentConnections | 同時連接到資料存放區的連接數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | 否 |

**範例:**

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
| `Folder*` | （空白，使用預設值） | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | （空白，使用預設值） | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>一些遞迴和 copyBehavior 範例

本節說明遞迴和 copyBehavior 值的不同組合之複製作業所產生的行為。

| recursive | copyBehavior | 來源資料夾結構 | 產生的目標 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以與來源相同的結構，建立目標 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File3 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File4 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File5 自動產生的名稱 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 的內容會合併成一個檔案，並具有自動產生的檔案名稱。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>未挑選 File3、File4 和 File5 的含有。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/><br/>未挑選 File3、File4 和 File5 的含有。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的內容會合併成一個檔案，並具有自動產生的檔案名稱。 針對 File1 自動產生的名稱<br/><br/>未挑選 File3、File4 和 File5 的含有。 |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>保留 Data Lake Storage Gen1 的 Acl

>[!TIP]
>若要在一般情況下將資料從 Azure Data Lake Storage Gen1 複製到 Gen2，請參閱[使用 Azure Data Factory 將資料從 Azure Data Lake Storage Gen1 複製到 Gen2](load-azure-data-lake-storage-gen2-from-gen1.md) ，以取得逐步解說和最佳作法。

當您將檔案從 Azure Data Lake Storage Gen1 複製到 Gen2 時，您可以選擇保留 POSIX 存取控制清單（Acl）以及資料。 如需存取控制的詳細資訊，請參閱 Azure Data Lake Storage Gen1 中的[存取控制](../data-lake-store/data-lake-store-access-control.md)和[Azure Data Lake Storage Gen2 中的存取控制](../storage/blobs/data-lake-storage-access-control.md)。

您可以使用 Azure Data Factory 複製活動來保留下列 Acl 類型。 您可以選取一或多個類型：

- **ACL**：複製並保留檔案和目錄上的 POSIX 存取控制清單。 它會將完整的現有 Acl 從來源複製到接收。 
- **擁有者**：複製並保留檔案和目錄的擁有使用者。 需要超級使用者存取接收器 Data Lake Storage Gen2。
- **群組**：複製並保留檔案和目錄的擁有群組。 對於接收 Data Lake Storage Gen2 或擁有使用者的超級使用者存取（如果擁有使用者也是目標群組的成員）是必要的。

如果您指定從資料夾複製，當`recursive`設定為 true 時，Data Factory 會複寫指定資料夾和其下的檔案和目錄的 acl。 如果您指定從單一檔案複製，則會複製該檔案的 Acl。

>[!IMPORTANT]
>當您選擇保留 Acl 時，請確定您已授與足夠的許可權，可讓 Data Factory 對您的接收 Data Lake Storage Gen2 帳戶進行操作。 例如，使用帳戶金鑰驗證，或將儲存體 Blob 資料擁有者角色指派給服務主體或受控識別。

當您使用二進位複製選項或二進位格式來將來源設定為 Data Lake Storage Gen1，並使用二進位複製選項或二進位格式做為 Data Lake Storage Gen2 時，您可以在 [**資料複製工具設定**] 頁面或 [開啟] 上找到 [**保留**] 選項活動撰寫的 [**複製活動** > **設定**] 索引標籤。

![Data Lake Storage Gen1 Gen2 保留 ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

以下是 JSON 設定的範例（請參閱`preserve`）： 

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

深入瞭解「對應資料流程」功能中的「[來源轉換](data-flow-source.md)」和「[接收」轉換](data-flow-sink.md)。

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要瞭解屬性的詳細資料，請檢查[查閱活動](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活動屬性

若要瞭解有關屬性的詳細資料，請檢查[GetMetadata 活動](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>刪除活動屬性

若要瞭解屬性的詳細資料，請檢查[刪除活動](delete-activity.md)
## <a name="next-steps"></a>後續步驟

如需 Data Factory 中的複製活動所支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。
