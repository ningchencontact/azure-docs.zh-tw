---
title: 將資料複製到或從 Azure Data Lake Storage Gen1
description: 了解如何使用 Data Factory 將資料從支援的來源資料存放區複製到 Azure Data Lake Store，或從 Data Lake Store 複製到支援的接收存放區。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/24/2019
ms.openlocfilehash: 17f7c62600bcc2aa21fbcea8ecd96810be412a26
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930521"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>使用 Azure Data Factory 從 Azure Data Lake Storage Gen1 複製資料

> [!div class="op_single_selector" title1="選取您要使用的 Azure Data Factory 版本："]
> * [第 1 版](v1/data-factory-azure-datalake-connector.md)
> * [目前的版本](connector-azure-data-lake-store.md)

本文概述如何在 Azure Data Lake Storage Gen1 之間複製資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Azure Data Lake Storage Gen1 連接器：

- [複製活動](copy-activity-overview.md)與[支援的來源/接收矩陣](copy-activity-overview.md) 
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)

具體而言，使用此連接器，您可以：

- 使用下列其中一種驗證方法來複製檔案：服務主體或 Azure 資源的受控識別。
- 複製檔案，或使用[支援的檔案格式和壓縮編解碼器](supported-file-formats-and-compression-codecs.md)來剖析或產生檔案。

> [!IMPORTANT]
> 如果您使用自我裝載整合執行時間來複製資料，請設定公司防火牆，以允許連到埠443上 `<ADLS account name>.azuredatalakestore.net` 和 `login.microsoftonline.com/<tenant>/oauth2/token` 的輸出流量。 後者是 Azure 安全性權杖服務，整合執行階段需要與之通訊才能取得權杖。

## <a name="get-started"></a>開始使用

> [!TIP]
> 如需如何使用 Azure Data Lake Store 連接器的逐步解說，請參閱將[資料載入 Azure Data Lake 存放區](load-azure-data-lake-store.md)。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關資訊，這些屬性是用來定義 Azure Data Lake 存放區特定的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Azure Data Lake Store 已連結服務支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| 類型 | `type` 屬性必須設為 **AzureDataLakeStore**。 | 是 |
| dataLakeStoreUri | Azure Data Lake Store 帳戶相關資訊。 此資訊會採用下列其中一種格式：`https://[accountname].azuredatalakestore.net/webhdfs/v1` 或 `adl://[accountname].azuredatalakestore.net/`。 | 是 |
| subscriptionId | Data Lake Store 帳戶所屬的 Azure 訂用帳戶識別碼。 | 接收 (Sink) 的必要項目 |
| resourceGroupName | Data Lake Store 帳戶所屬的 Azure 資源群組名稱。 | 接收 (Sink) 的必要項目 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果您的資料存放區位於私人網路中，您可以使用 Azure integration runtime 或自我裝載整合執行時間。 如果未指定此屬性，則會使用預設的 Azure integration runtime。 |否 |

### <a name="use-service-principal-authentication"></a>使用服務主體驗證

若要使用服務主體驗證，請遵循下列步驟。

1. 在 Azure Active Directory 中註冊應用程式實體，並授與它 Data Lake Store 的存取權。 如需詳細的步驟，請參閱[服務對服務驗證](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)。 請記下以下的值，您可以使用這些值來定義連結服務：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 授與服務主體適當的許可權。 如需如何在[Azure Data Lake Storage Gen1 的存取控制](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)Data Lake Storage Gen1 中，請參閱許可權運作方式的範例。

    - **作為來源**：在 **資料 Explorer**  > **存取** 中，至少授與所有上游資料夾（包括根）的 **執行** 許可權，以及要複製之檔案的 **讀取** 許可權。 您可以選擇新增到 [此資料夾及所有子系] 以供遞迴，並新增成為**存取權限和預設權限項目**。 不需要帳戶層級的存取控制（IAM）。
    - **作為接收**：在**資料管理器** > **存取**中，請至少授與所有上游資料夾（包括根）的 [**執行**] 許可權，以及 [接收] 資料夾的 [**寫入**] 許可權。 您可以選擇新增到 [此資料夾及所有子系] 以供遞迴，並新增成為**存取權限和預設權限項目**。 如果您使用 Azure 整合執行時間來複製（來源和接收都在雲端），在 IAM 中，請至少授與**讀取**者角色，以便讓 Data Factory 偵測 Data Lake Store 的區域。 如果您想要避免此 IAM 角色，請以 Data Lake Store 的位置明確地[建立 Azure 整合執行階段](create-azure-integration-runtime.md#create-azure-ir)。 例如，如果您的 Data Lake Store 在西歐，請建立 Azure 整合執行時間，並將 [位置] 設定為 [西歐]。 在 Data Lake Store 連結服務中建立關聯，如下列範例所示。

以下是支援的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 是 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為 `SecureString`，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是 |
| tenant | 指定您的應用程式所在的租使用者資訊，例如功能變數名稱或租使用者識別碼。 將滑鼠游標暫留在 Azure 入口網站右上角，即可擷取它。 | 是 |

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

資料處理站可與 [Azure 資源的受控識別](data-factory-service-identity.md)相關聯，後者表示特定的資料處理站。 您可以直接將此受控識別用於 Data Lake Store 驗證，類似于使用您自己的服務主體。 這可以讓這個指定的處理站存取及複製資料至 Data Lake Store，或從 Data Lake Store 存取及複製資料。

若要使用 Azure 資源的受控識別驗證，請遵循下列步驟。

1. 藉由複製與您的處理站一起產生的「服務識別應用程式識別碼」值，來抓取[data factory 的受控識別資訊](data-factory-service-identity.md#retrieve-managed-identity)。

2. 將存取權授與 Data Lake Store 的受控識別。 如需如何在[Azure Data Lake Storage Gen1 的存取控制](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)Data Lake Storage Gen1 中，請參閱許可權運作方式的範例。

    - **作為來源**：在 **資料 Explorer**  > **存取** 中，至少授與所有上游資料夾（包括根）的 **執行** 許可權，以及要複製之檔案的 **讀取** 許可權。 您可以選擇新增到 [此資料夾及所有子系] 以供遞迴，並新增成為**存取權限和預設權限項目**。 不需要帳戶層級的存取控制（IAM）。
    - **作為接收**：在**資料管理器** > **存取**中，請至少授與所有上游資料夾（包括根）的 [**執行**] 許可權，以及 [接收] 資料夾的 [**寫入**] 許可權。 您可以選擇新增到 [此資料夾及所有子系] 以供遞迴，並新增成為**存取權限和預設權限項目**。 如果您使用 Azure 整合執行時間來複製（來源和接收都在雲端），在 IAM 中，請至少授與**讀取**者角色，以便讓 Data Factory 偵測 Data Lake Store 的區域。 如果您想要避免此 IAM 角色，請以 Data Lake Store 的位置明確地[建立 Azure 整合執行階段](create-azure-integration-runtime.md#create-azure-ir)。 在 Data Lake Store 連結服務中建立關聯，如下列範例所示。

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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

下列屬性支援以格式為基礎之資料集內 `location` 設定下的 Azure Data Lake 存放區 Gen1：

| 屬性   | 描述                                                  | 必要項 |
| ---------- | ------------------------------------------------------------ | -------- |
| 類型       | Dataset 中 `location` 下的 type 屬性必須設定為**AzureDataLakeStoreLocation**。 | 是      |
| folderPath | 資料夾的路徑。 如果您想要使用萬用字元來篩選資料夾，請略過此設定，並在 [活動來源設定] 中指定它。 | 否       |
| fileName   | 指定 folderPath 下的檔案名。 如果您想要使用萬用字元來篩選檔案，請略過此設定，並在 [活動來源設定] 中指定它。 | 否       |

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

### <a name="legacy-dataset-model"></a>舊版資料集模型

>[!NOTE]
>下列資料集模型仍受到支援，以供回溯相容性之用。 建議您使用上一節中所提及的新模型，然後 ADF 撰寫 UI 已切換為產生新的模型。

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| 類型 | 資料集的類型屬性必須設定為**AzureDataLakeStoreFile**。 |是 |
| folderPath | Data Lake Store 中的資料夾路徑。 若未指定，它會指向根。 <br/><br/>支援萬用字元篩選準則。 允許的萬用字元 `*` （比對零或多個字元）和 `?` （符合零或單一字元）。 如果您的實際資料夾名稱包含萬用字元或內的此 escape 字元，請使用 `^` 來進行 escape。 <br/><br/>例如： rootfolder/子資料夾/。 如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 |否 |
| fileName | 指定 "folderPath" 下之檔案的名稱或萬用字元篩選。 若未指定此屬性的值，資料集就會指向資料夾中的所有檔案。 <br/><br/>對於篩選，允許的萬用字元是 `*` （符合零或多個字元）和 `?` （符合零或單一字元）。<br/>- 範例 1：`"fileName": "*.csv"`<br/>- 範例 2：`"fileName": "???20180427.txt"`<br/>如果您的實際檔案名包含萬用字元或內的此 escape 字元，請使用 `^` 來進行換用。<br/><br/>當未指定輸出資料集的 fileName，且在活動接收中未指定**preserveHierarchy**時，複製活動會自動以下列模式產生檔案名： "*Data. [活動執行識別碼 GUID]。[如果 FlattenHierarchy，則為 GUID]。[格式化（若已設定）]。[已設定壓縮]* ，例如 "Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a .txt. gz"。 如果您使用資料表名稱（而非查詢）從表格式來源複製，則名稱模式會是 " *[資料表名稱]. [格式]。[已設定壓縮]* ，例如 "MyTable. csv"。 |否 |
| modifiedDatetimeStart | 檔案會根據上次修改的屬性進行篩選。 如果上次修改時間是在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd`之間的時間範圍內，則會選取檔案。 時間會以 "2018-12-01T05：00： 00Z" 的格式套用至 UTC 時區。 <br/><br/> 當您想要以大量檔案執行檔案篩選時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 屬性可以是 Null，這表示不會將檔案屬性篩選套用至資料集。 當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 Null 時，表示已選取上次修改屬性大於或等於日期時間值的檔案。 當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 Null 時，表示已選取上次修改屬性小於日期時間值的檔案。| 否 |
| modifiedDatetimeEnd | 檔案會根據上次修改的屬性進行篩選。 如果上次修改時間是在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd`之間的時間範圍內，則會選取檔案。 時間會以 "2018-12-01T05：00： 00Z" 的格式套用至 UTC 時區。 <br/><br/> 當您想要以大量檔案執行檔案篩選時，啟用這項設定會影響資料移動的整體效能。 <br/><br/> 屬性可以是 Null，這表示不會將檔案屬性篩選套用至資料集。 當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 Null 時，表示已選取上次修改屬性大於或等於日期時間值的檔案。 當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 Null 時，表示已選取上次修改屬性小於日期時間值的檔案。| 否 |
| format | 如果您想要在以檔案為基礎的存放區之間依檔案複製檔案（二進位複本），請略過輸入和輸出資料集定義中的 format 區段。<br/><br/>如果您想要以特定格式來剖析或產生檔案，以下是支援的檔案格式類型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 將 [format] 下的 [type] 屬性設定為下列其中一個值。 如需詳細資訊，請參閱[文字格式](supported-file-formats-and-compression-codecs.md#text-format)、[JSON 格式](supported-file-formats-and-compression-codecs.md#json-format)、[Avro 格式](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc 格式](supported-file-formats-and-compression-codecs.md#orc-format)和 [Parquet 格式](supported-file-formats-and-compression-codecs.md#parquet-format)小節。 |否 (僅適用於二進位複製案例) |
| compression | 指定此資料的壓縮類型和層級。 如需詳細資訊，請參閱[支援的檔案格式和壓縮轉碼器](supported-file-formats-and-compression-codecs.md#compression-support)。<br/>支援的類型為：GZip、Deflate、BZip2 及 ZipDeflate。<br/>支援的層級為 **Optimal** 和 **Fastest**。 |否 |

>[!TIP]
>若要複製資料夾下的所有檔案，請只指定 **folderPath**。<br>若要複製具有特定名稱的單一檔案，請以資料夾部分指定**folderPath** ，**並以檔案名命名**。<br>若要複製資料夾下的檔案子集，請以資料夾部分指定**folderPath** ，並以萬用字元篩選來指定**檔案名**。 

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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

下列屬性支援以格式為基礎之複製來源的 `storeSettings` 設定底下的 Azure Data Lake 存放區 Gen1：

| 屬性                 | 描述                                                  | 必要項                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| 類型                     | `storeSettings` 下的 type 屬性必須設定為**AzureDataLakeStoreReadSetting**。 | 是                                           |
| 遞迴                | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 當遞迴設定為 true 且接收是以檔案為基礎的存放區時，不會在接收時複製或建立空的資料夾或子資料夾。 允許的值為 **true** (預設值) 和 **false**。 | 否                                            |
| wildcardFolderPath       | 包含用來篩選源資料夾之萬用字元的資料夾路徑。 <br>允許的萬用字元 `*` （比對零或多個字元）和 `?` （符合零或單一字元）。 如果您的實際資料夾名稱包含萬用字元或內的此 escape 字元，請使用 `^` 來進行 escape。 <br>如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 否                                            |
| wildcardFileName         | 在指定的 folderPath/wildcardFolderPath 下具有萬用字元的檔案名，用以篩選原始程式檔。 <br>允許的萬用字元 `*` （比對零或多個字元）和 `?` （符合零或單一字元）。 如果您的實際資料夾名稱包含萬用字元或內的此 escape 字元，請使用 `^` 來進行 escape。 如需更多範例，請參閱[資料夾和檔案篩選範例](#folder-and-file-filter-examples)。 | 如果未在 dataset 中指定 `fileName` 則為 [是] |
| modifiedDatetimeStart    | 檔案會根據上次修改的屬性進行篩選。 如果上次修改時間是在 `modifiedDatetimeStart` 和 `modifiedDatetimeEnd`之間的時間範圍內，則會選取檔案。 時間會以 "2018-12-01T05：00： 00Z" 的格式套用至 UTC 時區。 <br> 屬性可以是 Null，這表示不會將檔案屬性篩選套用至資料集。 當 `modifiedDatetimeStart` 具有日期時間值，但 `modifiedDatetimeEnd` 為 Null 時，表示已選取上次修改屬性大於或等於日期時間值的檔案。 當 `modifiedDatetimeEnd` 具有日期時間值，但 `modifiedDatetimeStart` 為 Null 時，表示已選取上次修改屬性小於日期時間值的檔案。 | 否                                            |
| modifiedDatetimeEnd      | 同上。                                               | 否                                            |
| maxConcurrentConnections | 同時連接到儲存體存放區的連線數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | 否                                            |

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

#### <a name="legacy-source-model"></a>舊版來源模型

>[!NOTE]
>下列複製來源模型仍受到支援，以提供回溯相容性。 我們建議您先使用上述的新模型，然後 ADF 撰寫 UI 已切換為產生新的模型。

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| 類型 | 複製活動來源的 `type` 屬性必須設定為**AzureDataLakeStoreSource**。 |是 |
| 遞迴 | 指出是否從子資料夾、或只有從指定的資料夾，以遞迴方式讀取資料。 當 `recursive` 設定為 true 且接收是以檔案為基礎的存放區時，不會在接收時複製或建立空的資料夾或子資料夾。 允許的值為 **true** (預設值) 和 **false**。 | 否 |
| maxConcurrentConnections | 同時連接到資料存放區的連接數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | 否 |

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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

下列屬性支援以格式為基礎之複製接收中 `storeSettings` 設定下的 Azure Data Lake 存放區 Gen1：

| 屬性                 | 描述                                                  | 必要項 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| 類型                     | `storeSettings` 下的 type 屬性必須設定為**AzureDataLakeStoreWriteSetting**。 | 是      |
| copyBehavior             | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設值)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來源資料夾的所有檔案合併成一個檔案。 若已指定檔案名稱，合併檔案的名稱會是指定的名稱。 否則，就會是自動產生的檔案名稱。 | 否       |
| maxConcurrentConnections | 同時連接到資料存放區的連接數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | 否       |

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

#### <a name="legacy-sink-model"></a>舊版接收模型

>[!NOTE]
>下列複製接收模型仍受到支援，以供回溯相容性之用。 我們建議您先使用上述的新模型，然後 ADF 撰寫 UI 已切換為產生新的模型。

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| 類型 | 複製活動接收的 `type` 屬性必須設定為**AzureDataLakeStoreSink**。 |是 |
| copyBehavior | 當來源是來自檔案型資料存放區的檔案時，會定義複製行為。<br/><br/>允許的值包括：<br/><b>- PreserveHierarchy (預設值)</b>：保留目標資料夾中的檔案階層。 來源檔案到來源資料夾的相對路徑，與目標檔案到目標資料夾的相對路徑相同。<br/><b>- FlattenHierarchy</b>：來自來源資料夾的所有檔案都在目標資料夾的第一層中。 目標檔案會有自動產生的名稱。 <br/><b>- MergeFiles</b>：將來源資料夾的所有檔案合併成一個檔案。 若已指定檔案名稱，合併檔案的名稱會是指定的名稱。 否則，會自動產生檔案名稱。 | 否 |
| maxConcurrentConnections | 同時連接到資料存放區的連接數目。 只有當您想要限制與資料存放區的並行連接時，才指定。 | 否 |

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

| folderPath | fileName | 遞迴 | 來源資料夾結構和篩選結果 (會擷取以**粗體**顯示的檔案)|
|:--- |:--- |:--- |:--- |
| `Folder*` | （空白，使用預設值） | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | （空白，使用預設值） | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>複製作業的行為範例

本節說明 `recursive` 和 `copyBehavior` 值在不同組合的情況下，複製作業所產生的行為。

| 遞迴 | copyBehavior | 來源資料夾結構 | 產生的目標 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以與來源相同的結構，建立目標 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File3 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File4 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File5 自動產生的名稱 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 內容會合並成一個檔案，並具有自動產生的檔案名。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>不會挑選具有 File3、File4 和 File5 的含有。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File1 自動產生的名稱<br/>&nbsp;&nbsp;&nbsp;&nbsp;針對 File2 自動產生的名稱<br/><br/>不會挑選具有 File3、File4 和 File5 的含有。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | 會以下列結構建立目標資料夾 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 內容會合並成一個檔案，其中包含自動產生的檔案名。 針對 File1 自動產生的名稱<br/><br/>不會挑選具有 File3、File4 和 File5 的含有。 |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>保留 Acl 以 Data Lake Storage Gen2

如果您想要在從 Data Lake Storage Gen1 升級至 Data Lake Storage Gen2 時，將存取控制清單（Acl）和資料檔案進行複寫，請參閱[從 Data Lake Storage Gen1 保留 acl](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1)。

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

深入瞭解「對應資料流程」功能中的「[來源轉換](data-flow-source.md)」和「[接收」轉換](data-flow-sink.md)。

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要瞭解屬性的詳細資料，請檢查[查閱活動](control-flow-lookup-activity.md)。

## <a name="getmetadata-activity-properties"></a>GetMetadata 活動屬性

若要瞭解有關屬性的詳細資料，請檢查[GetMetadata 活動](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>刪除活動屬性

若要瞭解屬性的詳細資料，請檢查[刪除活動](delete-activity.md)

## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md##supported-data-stores-and-formats)。
