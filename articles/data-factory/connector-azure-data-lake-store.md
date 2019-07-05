---
title: 複製資料，或從 Azure Data Lake 儲存體 Gen1 使用 Data Factory |Microsoft Docs
description: 了解如何使用 Data Factory 將資料從支援的來源資料存放區複製到 Azure Data Lake Store，或從 Data Lake Store 複製到支援的接收存放區。
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: df88c3e2e07165182c917eaf30a5f37451fbd073
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509573"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Azure Data Lake 儲存體 Gen1 來回複製資料使用 Azure Data Factory
> [!div class="op_single_selector" title1="選取您要使用的 Azure Data Factory 的版本："]
> * [第 1 版](v1/data-factory-azure-datalake-connector.md)
> * [目前的版本](connector-azure-data-lake-store.md)

本文概述如何從 Azure Data Lake 儲存體 Gen1 來回複製資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

此 Azure Data Lake 儲存體 Gen1 連接器支援下列活動：

- [複製活動](copy-activity-overview.md)與[支援來源或接收器的矩陣](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)

具體而言，使用此連接器您可以：

- 使用下列的驗證方法的其中一個複製檔案： 服務主體或受管理的身分識別，適用於 Azure 資源。
- 將檔案或剖析或產生的檔案複製[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs.md)。

> [!IMPORTANT]
> 如果您使用自我裝載的整合執行階段複製資料，設定公司防火牆以允許輸出流量`<ADLS account name>.azuredatalakestore.net`和`login.microsoftonline.com/<tenant>/oauth2/token`連接埠 443 上。 後者是 Azure 安全性權杖服務，整合執行階段需要與之通訊才能取得權杖。

## <a name="get-started"></a>開始使用

> [!TIP]
> 如需如何使用 Azure Data Lake Store 連接器的逐步解說，請參閱 <<c0> [ 將資料載入 Azure Data Lake Store](load-azure-data-lake-store.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性用來定義 Azure Data Lake Store 專屬的 Data Factory 實體的相關資訊。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Azure Data Lake Store 已連結服務支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | `type` 屬性必須設為 **AzureDataLakeStore**。 | 是 |
| dataLakeStoreUri | Azure Data Lake Store 帳戶相關資訊。 此資訊會採用下列其中一種格式：`https://[accountname].azuredatalakestore.net/webhdfs/v1` 或 `adl://[accountname].azuredatalakestore.net/`。 | 是 |
| subscriptionId | Data Lake Store 帳戶所屬的 Azure 訂用帳戶識別碼。 | 接收 (Sink) 的必要項目 |
| resourceGroupName | Data Lake Store 帳戶所屬的 Azure 資源群組名稱。 | 接收 (Sink) 的必要項目 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果您的資料存放區位於私人網路中，您可以使用 Azure integration runtime 或自我裝載的整合執行階段。 如果未指定此屬性，則會使用預設的 Azure 整合執行階段。 |否 |

### <a name="use-service-principal-authentication"></a>使用服務主體驗證

若要使用服務主體驗證，請在 Azure Active Directory 中註冊應用程式實體，然後授與它 Data Lake Store 存取權。 如需詳細的步驟，請參閱[服務對服務驗證](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)。 請記下以下的值，您可以使用這些值來定義連結服務：

- 應用程式識別碼
- 應用程式金鑰
- 租用戶識別碼

>[!IMPORTANT]
> 服務主體適當權限授與 Data Lake Store 中：
>- **作為來源**：在 [資料總管]   > [存取]  中，請至少授與列出和複製資料夾和子資料夾中的檔案時所需的**讀取 + 執行**權限。 或者也可以授與複製單一檔案時所需的**讀取**權限。 您可以選擇新增到 [此資料夾及所有子系]  以供遞迴，並新增成為**存取權限和預設權限項目**。 沒有在帳戶層級存取控制 (IAM) 上的需求。
>- **作為接收**：在 [資料總管]   > [存取]  中，請至少授與在資料夾中建立子項目所需的**寫入 + 執行**權限。 您可以選擇新增到 [此資料夾及所有子系]  以供遞迴，並新增成為**存取權限和預設權限項目**。 如果您使用 Azure 整合執行階段來複製 （來源和接收器都在雲端），在 IAM，授與至少**讀取器**角色，以便讓 Data Factory 偵測 Data Lake Store 的區域。 如果您想要避免此 IAM 角色，請以 Data Lake Store 的位置明確地[建立 Azure 整合執行階段](create-azure-integration-runtime.md#create-azure-ir)。 例如，如果您的 Data Lake Store，在西歐區域建立 Azure 整合執行階段使用位置設定為"「 西歐 」。 請將它們關聯中的 Data Lake Store 連結服務，如下列範例所示。

>[!NOTE]
>若要從根層級開始列出資料夾，則必須將所要授與的服務主體權限設為**在根層級具有「執行」的權限**。 您使用下列項目時，這種情況即會成立：
>- **複製資料工具**以作者複製管線。
>- 以 **Data Factory UI** 在製作期間測試連線和瀏覽資料夾。
>如果您有顧慮授與根層級的權限，在撰寫期間，略過測試連線，以及授與權限的 paraent 路徑然後選擇瀏覽 從指定路徑的輸入。 只要服務主體授與具有適當的權限在檔案複製，請將複製活動的運作方式。

以下是支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 是 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為 `SecureString`，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定租用戶資訊，例如網域名稱或租用戶的識別碼，您的應用程式所在。 將滑鼠游標暫留在 Azure 入口網站右上角，即可擷取它。 | 是 |

**範例：**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a>使用 Azure 資源的受控識別驗證

資料處理站可與 [Azure 資源的受控識別](data-factory-service-identity.md)相關聯，後者表示特定的資料處理站。 您直接可以使用這個受管理的身分識別用於 Data Lake Store 驗證，類似於使用您自己的服務主體。 這可以讓這個指定的處理站存取及複製資料至 Data Lake Store，或從 Data Lake Store 存取及複製資料。

使用 Azure 資源的受控識別驗證：

1. [擷取的 data factory 受控身分識別資訊](data-factory-service-identity.md#retrieve-managed-identity)藉由複製"Service Identity Application ID"站一起產生的值。
2. 受控身分識別存取權授與 Data Lake Store，相同的服務主體，請遵循這些資訊的方式。

>[!IMPORTANT]
> 請確定您 data factory 受控身分識別適當權限授與 Data Lake Store 中：
>- **作為來源**：在 [資料總管]   > [存取]  中，請至少授與列出和複製資料夾和子資料夾中的檔案時所需的**讀取 + 執行**權限。 或者也可以授與複製單一檔案時所需的**讀取**權限。 您可以選擇新增到 [此資料夾及所有子系]  以供遞迴，並新增成為**存取權限和預設權限項目**。 沒有在帳戶層級存取控制 (IAM) 上的需求。
>- **作為接收**：在 [資料總管]   > [存取]  中，請至少授與在資料夾中建立子項目所需的**寫入 + 執行**權限。 您可以選擇新增到 [此資料夾及所有子系]  以供遞迴，並新增成為**存取權限和預設權限項目**。 如果您使用 Azure 整合執行階段來複製 （來源和接收器都在雲端），在 IAM，授與至少**讀取器**角色，以便讓 Data Factory 偵測 Data Lake Store 的區域。 如果您想要避免此 IAM 角色，請以 Data Lake Store 的位置明確地[建立 Azure 整合執行階段](create-azure-integration-runtime.md#create-azure-ir)。 請將它們關聯中的 Data Lake Store 連結服務，如下列範例所示。

>[!NOTE]
>清單資料夾從根目錄開始，您必須設定受管理的身分識別授與的權限**與 「 執行 」 權限的根層級**。 您使用下列項目時，這種情況即會成立：
>- **複製資料工具**以作者複製管線。
>- 以 **Data Factory UI** 在製作期間測試連線和瀏覽資料夾。
>如果您有顧慮授與根層級的權限，在撰寫期間，略過測試連線，以及授與權限的父路徑 [瀏覽] 從指定路徑的輸入。 只要服務主體授與具有適當的權限在檔案複製，請將複製活動的運作方式。

在 Azure Data Factory 中，除了 Data Lake Store 的一般資訊，您不需要在連結服務中指定任何屬性。

**範例：**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
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

- Parquet 和分隔的文字格式，請參閱[Parquet 和分隔的文字格式的資料集](#parquet-and-delimited-text-format-dataset)一節。
- 針對像是 ORC、 Avro、 JSON 或二進位格式的其他格式，請參閱[其他格式的資料集](#other-format-dataset)一節。

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet 和分隔的文字格式的資料集

若要複製資料進出 Azure 資料湖存放區 Gen1 parquet 或分隔的文字格式，請參閱[Parquet 格式](format-parquet.md)並[分隔文字格式](format-delimited-text.md)文章格式為基礎的資料集和支援的設定。 下列屬性可在 Azure 資料湖存放區 Gen1`location`格式為基礎的資料集內的設定：

| 屬性   | 描述                                                  | 必要項 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | [類型] 屬性底下`location`資料集內必須設定為**AzureDataLakeStoreLocation**。 | 是      |
| folderPath | 資料夾的路徑。 如果您想要使用萬用字元來篩選資料夾，就會略過這項設定，並指定活動來源設定中。 | 否       |
| fileName   | 指定 folderPath 檔案名稱。 如果您想要使用萬用字元來篩選檔案，就會略過這項設定，並指定活動來源設定中。 | 否       |

> [!NOTE]
>
> **AzureDataLakeStoreFile**原狀複製、 查閱和回溯相容性的 GetMetadata 活動仍然支援使用下列一節所述的 parquet 或文字格式的類型資料集。 但它並不適用於對應資料的資料流程功能。 我們建議您使用接下來的這個新模型。 編寫 UI 的資料處理站產生這些新的型別。

**範例：**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
                "folderPath": "root/folder/subfolder"
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

若要複製資料，進出 Azure 資料湖存放區 Gen1 中 ORC、 Avro、 JSON 或二進位格式，支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 資料集的 type 屬性必須設定為**AzureDataLakeStoreFile**。 |是 |
| folderPath | Data Lake Store 中的資料夾路徑。 若未指定，它會指向根。 <br/><br/>支援萬用字元篩選。 允許使用萬用字元`*`（比對零或多個字元） 和`?`(比對零或單一字元)。 使用`^`來逸出，如果您的實際資料夾名稱有萬用字元或內此逸出字元。 <br/><br/>例如： 根資料夾/子資料夾 /。 如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 |否 |
| fileName | 指定的"folderPath"下的檔案名稱或萬用字元篩選條件。 若未指定此屬性的值，資料集就會指向資料夾中的所有檔案。 <br/><br/>篩選中，會允許使用萬用字元`*`（比對零或多個字元） 和`?`(比對零或單一字元)。<br/>- 範例 1：`"fileName": "*.csv"`<br/>- 範例 2：`"fileName": "???20180427.txt"`<br/>使用`^`來逸出，如果您的實際檔案名稱萬用字元或內此逸出字元。<br/><br/>沒有為輸出資料集指定 fileName 且活動接收器中未指定 **preserveHierarchy** 時，複製活動會自動以下列模式產生檔案名稱：「*資料。[活動執行識別碼 GUID。][GUID 如果 FlattenHierarchy]。[格式設定]。[壓縮設定]* "，例如"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"。 如果您使用的資料表名稱，而不是查詢複製從表格式的來源，名稱模式為" *[資料表名稱]。 [格式]。[壓縮設定]* "，例如"MyTable.csv 」。 |否 |
| modifiedDatetimeStart | 上次修改的屬性為基礎的檔案篩選條件。 如果其上次修改的時間之間的時間範圍內，會選取的檔案`modifiedDatetimeStart`和`modifiedDatetimeEnd`。 時間會套用至 UTC 時區，格式為"2018年-12-01T05:00:00Z"。 <br/><br/> 啟用此設定，當您想要進行大量的檔案與檔案篩選器時，會影響資料移動的整體效能。 <br/><br/> 屬性可以是 NULL，表示檔案屬性未套用任何篩選至資料集。 當`modifiedDatetimeStart`具有日期時間值，但`modifiedDatetimeEnd`是 NULL，這表示其最後一個已修改的屬性大於檔案，或者選取的日期時間值等於。 當`modifiedDatetimeEnd`具有日期時間值，但`modifiedDatetimeStart`是 NULL，這表示會選取其最後一個已修改的屬性是日期時間值小於檔案。| 否 |
| modifiedDatetimeEnd | 上次修改的屬性為基礎的檔案篩選條件。 如果其上次修改的時間之間的時間範圍內，會選取的檔案`modifiedDatetimeStart`和`modifiedDatetimeEnd`。 時間會套用至 UTC 時區，格式為"2018年-12-01T05:00:00Z"。 <br/><br/> 啟用此設定，當您想要進行大量的檔案與檔案篩選器時，會影響資料移動的整體效能。 <br/><br/> 屬性可以是 NULL，表示檔案屬性未套用任何篩選至資料集。 當`modifiedDatetimeStart`具有日期時間值，但`modifiedDatetimeEnd`是 NULL，這表示其最後一個已修改的屬性大於檔案，或者選取的日期時間值等於。 當`modifiedDatetimeEnd`具有日期時間值，但`modifiedDatetimeStart`是 NULL，這表示會選取其最後一個已修改的屬性是日期時間值小於檔案。| 否 |
| format | 如果您想要複製檔案，檔案型存放區 （二進位複本） 之間依原樣，略過這兩個輸入和輸出資料集定義中的 [格式] 區段。<br/><br/>如果您想要剖析或產生特定格式的檔案，以下是支援的檔案格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 將 [format]  下的 [type]  屬性設定為下列其中一個值。 如需詳細資訊，請參閱[文字格式](supported-file-formats-and-compression-codecs.md#text-format)、[JSON 格式](supported-file-formats-and-compression-codecs.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs.md#parquet-format)小節。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs.md#compression-support)。<br/>支援的類型為：GZip  、Deflate  、BZip2  及 ZipDeflate  。<br/>支援的層級為 **Optimal** 和 **Fastest**。 |否 |


>[!TIP]
>若要複製資料夾下的所有檔案，請只指定 **folderPath**。<br>若要複製單一檔案具有特定名稱，指定**folderPath**與資料夾組件和**fileName**檔案名稱。<br>若要複製的資料夾下的檔案子集，請指定**folderPath**與資料夾組件並**fileName**與萬用字元篩選條件。 

**範例：**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
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

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)。 本節提供 Azure Data Lake Store 來源和接收器所支援的屬性清單。

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store 作為來源

- 若要複製 parquet 或分隔的文字格式，請參閱[Parquet 和分隔的文字格式來源](#parquet-and-delimited-text-format-source)一節。
- 若要複製其他格式，例如 ORC、 Avro、 JSON 或二進位格式，請參閱[其他格式來源](#other-format-source)一節。

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet 和分隔的文字格式的來源

若要將資料從 Azure 資料湖存放區 Gen1 複製 parquet 或分隔的文字格式，請參閱[Parquet 格式](format-parquet.md)並[分隔文字格式](format-delimited-text.md)格式為基礎的複製活動來源和支援的設定上的文章。 下列屬性可在 Azure 資料湖存放區 Gen1`storeSettings`格式為基礎的複製來源中的設定：

| 屬性                 | 描述                                                  | 必要項                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | [類型] 屬性底下`storeSettings`必須設為**AzureDataLakeStoreReadSetting**。 | 是                                           |
| recursive                | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 當遞迴設定為 true 且接收是檔案型存放區，空的資料夾或子資料夾不是複製或建立在接收上。 允許的值為 **true** (預設值) 和 **false**。 | 否                                            |
| wildcardFolderPath       | 使用萬用字元來篩選來源資料夾的資料夾路徑。 <br>允許使用萬用字元`*`（比對零或多個字元） 和`?`(比對零或單一字元)。 使用`^`來逸出，如果您的實際資料夾名稱有萬用字元或內此逸出字元。 <br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 否                                            |
| wildcardFileName         | 在給定篩選來源檔案 folderPath/wildcardFolderPath 萬用字元在檔名。 <br>允許使用萬用字元`*`（比對零或多個字元） 和`?`(比對零或單一字元)。 使用`^`來逸出，如果您的實際資料夾名稱有萬用字元或內此逸出字元。 如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | [是] 如果`fileName`未指定資料集中 |
| modifiedDatetimeStart    | 上次修改的屬性為基礎的檔案篩選條件。 如果其上次修改的時間之間的時間範圍內，會選取的檔案`modifiedDatetimeStart`和`modifiedDatetimeEnd`。 時間會套用至 UTC 時區，格式為"2018年-12-01T05:00:00Z"。 <br> 屬性可以是 NULL，表示檔案屬性未套用任何篩選至資料集。 當`modifiedDatetimeStart`具有日期時間值，但`modifiedDatetimeEnd`是 NULL，這表示其最後一個已修改的屬性大於檔案，或者選取的日期時間值等於。 當`modifiedDatetimeEnd`具有日期時間值，但`modifiedDatetimeStart`是 NULL，這表示會選取其最後一個已修改的屬性是日期時間值小於檔案。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 同時連接到儲存體存放區的連線數目。 只有在您想要限制資料存放區的並行連接時，才指定。 | 否                                            |

> [!NOTE]
> Parquet 或分隔的文字格式**AzureDataLakeStoreSource**為是為了回溯相容性仍支援類型複製活動來源中下一節所述。 我們建議您使用接下來的這個新模型。 編寫 UI 的資料處理站產生這些新的型別。

**範例：**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
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
                    "type": "AzureDataLakeStoreReadSetting",
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

若要從 Azure 資料湖存放區 Gen1 ORC、 Avro、 JSON 或二進位格式中複製資料，請將複製活動中支援下列屬性**來源**區段：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | `type`複製活動來源的屬性必須設為**AzureDataLakeStoreSource**。 |是 |
| recursive | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 當`recursive`設為 true 且接收是檔案型存放區，空的資料夾或子資料夾不是複製或建立在接收上。 允許的值為 **true** (預設值) 和 **false**。 | 否 |
| maxConcurrentConnections | 同時連接到資料存放區的連線數目。 只有在您想要限制資料存放區的並行連接時，才指定。 | 否 |

**範例：**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store 作為接收器

- 要複製至 parquet 和分隔的文字格式，請參閱[Parquet 和分隔的文字格式接收](#parquet-and-delimited-text-format-sink)一節。
- 若要複製到其他格式，例如 ORC、 Avro、 JSON 或二進位格式，請參閱[其他格式接收](#other-format-sink)一節。

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet 和分隔的文字格式接收

若要將資料複製到 Azure 資料湖存放區 Gen1 中，以 parquet 或分隔的文字格式，請參閱[Parquet 格式](format-parquet.md)並[分隔文字格式](format-delimited-text.md)格式為基礎的複製活動接收器和支援的設定上的文章。 下列屬性可在 Azure 資料湖存放區 Gen1`storeSettings`中格式為基礎的複製接收設定：

| 屬性                 | 描述                                                  | 必要項 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | [類型] 屬性底下`storeSettings`必須設為**AzureDataLakeStoreWriteSetting**。 | 是      |
| copyBehavior             | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都會在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來源資料夾的所有檔案合併成一個檔案。 若已指定檔案名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否       |
| maxConcurrentConnections | 同時連接到資料存放區的連線數目。 只有在您想要限制資料存放區的並行連接時，才指定。 | 否       |

> [!NOTE]
> Parquet 或分隔的文字格式**AzureDataLakeStoreSink**為是為了回溯相容性仍支援類型複製活動接收器中下一節所述。 我們建議您使用接下來的這個新模型。 編寫 UI 的資料處理站產生這些新的型別。

**範例：**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
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
                    "type": "AzureDataLakeStoreWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>其他格式接收

若要將資料複製到 Azure 資料湖存放區 Gen1 中 ORC、 Avro、 JSON 或二進位格式，支援下列屬性**接收**區段：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | `type`複製活動接收器的屬性必須設定為**AzureDataLakeStoreSink**。 |是 |
| copyBehavior | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都會在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來源資料夾的所有檔案合併成一個檔案。 若已指定檔案名稱，合併檔案的名稱會是指定的名稱。 否則，會自動產生檔案名稱。 | 否 |
| maxConcurrentConnections | 同時連接到資料存放區的連線數目。 只有在您想要限制資料存放區的並行連接時，才指定。 | 否 |

**範例：**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
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

### <a name="examples-of-behavior-of-the-copy-operation"></a>複製作業的行為範例

本節說明 `recursive` 和 `copyBehavior` 值在不同組合的情況下，複製作業所產生的行為。

| recursive | copyBehavior | 來源資料夾結構 | 產生的目標 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以與來源相同的結構，建立目標 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File3 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File4 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File5 自動產生的名稱 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 內容會合併成一個檔案，並有自動產生檔案名稱。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>File3、 File4，與 File5 的 Subfolder1 未挑選。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/><br/>File3、 File4，與 File5 的 Subfolder1 未挑選。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的內容會合併成一個檔案，並自動產生的檔案名稱。 針對 File1 自動產生的名稱<br/><br/>File3、 File4，與 File5 的 Subfolder1 未挑選。 |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>保留至 Data Lake 儲存體 Gen2 的 Acl

如果您想要複寫的存取控制會列出與資料檔案 (Acl) 至 Data Lake 儲存體 Gen2 從 Data Lake 儲存體 Gen1 升級時，請參閱[保留的 Acl，從 Data Lake 儲存體 Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1)。

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

深入了解[來源轉換](data-flow-source.md)並[接收轉換](data-flow-sink.md)中對應資料的資料流程功能。

## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。
