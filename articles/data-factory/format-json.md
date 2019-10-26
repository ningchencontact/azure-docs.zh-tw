---
title: Azure Data Factory 中的 JSON 格式 |Microsoft Docs
description: 本主題描述如何在 Azure Data Factory 中處理 JSON 格式。
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: a01c95cbe9281c915e89572c07fcbc203193b962
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935506"
---
# <a name="json-format-in-azure-data-factory"></a>Azure Data Factory 中的 JSON 格式

當您想要**剖析 json 檔案，或將資料寫入 json 格式**時，請遵循這篇文章。 

下列連接器支援 JSON 格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [Azure 檔案儲存體](connector-azure-file-storage.md)、[檔案系統](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google雲端儲存體](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和[SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 JSON 資料集所支援的屬性清單。

| 屬性         | 描述                                                  | 必要項 |
| ---------------- | ------------------------------------------------------------ | -------- |
| 類型             | 資料集的類型屬性必須設定為**Json**。 | 是      |
| location         | 檔案的位置設定。 每個以檔案為基礎的連接器都有自己的位置類型，且支援的屬性 `location`。 **請參閱連接器文章-> 資料集屬性一節中的詳細資訊**。 | 是      |
| encodingName     | 用來讀取/寫入測試檔案的編碼類型。 <br>允許的值如下： "UTF-8"、"UTF-16"、"UTF-UTF-16BE"、"UTF-32"、"32BE"、"US-ASCII"，"UTF-7"，"BIG5"，"EUC-JP"，"EUC-KR"，"GB2312"，"GB18030"，"JOHAB"，"SHIFT-JIS"，"CP875"，"CP866"，"IBM00858"，"IBM037"，"IBM273"，"IBM437"，"IBM500"，"IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149"，"ISO-2022-JP"，"ISO-2022-KR"，"ISO-8859-1"，"ISO-8859-2"，"ISO-8859-3"，"ISO-8859-4"，"ISO-8859-5"，"ISO-8859-6"，"ISO-8859-7"，"ISO-8859-8"，"ISO-8859-9"，"ISO-8859-13"，"ISO-8859-15"，"WINDOWS-874"，"WINDOWS-1250"，"WINDOWS-1251"，"WINDOWS-1252 "，" WINDOWS-1253 "，" WINDOWS-1254 "，" WINDOWS-1255 "，" WINDOWS-1256 "，" WINDOWS-1257 "，" WINDOWS-1258 "。| 否       |
| compressionCodec | 用來讀取/寫入文字檔的壓縮編解碼器。 <br>允許的值為**bzip2**、 **gzip**、 **deflate**、 **ZipDeflate**、 **snappy**或**lz4**。 表示在儲存檔案時使用。 <br>注意：目前的複製活動不支援 "snappy" & "lz4"。 | 否       |
| compressionLevel | 壓縮比例。 <br>允許的值為**最佳**或**最快**。<br>- **最快：** 即使產生的檔案未以最佳方式壓縮，壓縮作業也應該儘快完成。<br>-  的**最佳**：即使作業需要較長的時間才能完成，壓縮作業也應以最佳方式壓縮。 如需詳細資訊，請參閱 [壓縮層級](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 主題。 | 否       |

以下是 Azure Blob 儲存體上的 JSON 資料集範例：

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 JSON 來源和接收所支援的屬性清單。

### <a name="json-as-source"></a>JSON 做為來源

複製活動***\*來源\**** 區段中支援下列屬性。

| 屬性      | 描述                                                  | 必要項 |
| ------------- | ------------------------------------------------------------ | -------- |
| 類型          | 複製活動來源的類型屬性必須設定為**JSONSource**。 | 是      |
| storeSettings | 一組屬性，說明如何從資料存放區讀取資料。 每個以檔案為基礎的連接器在 `storeSettings` 之下都有自己支援的讀取設定。 **請參閱連接器中的詳細資料文章-> 複製活動屬性一節**。 | 否       |

### <a name="json-as-sink"></a>JSON 做為接收

複製活動***\*接收\**** 區段中支援下列屬性。

| 屬性      | 描述                                                  | 必要項 |
| ------------- | ------------------------------------------------------------ | -------- |
| 類型          | 複製活動來源的類型屬性必須設定為**JSONSink**。 | 是      |
| formatSettings | 屬性的群組。 請參閱下面的**JSON 寫入設定**表格。 | 否       |
| storeSettings | 一組屬性，說明如何將資料寫入資料存放區。 每個以檔案為基礎的連接器在 `storeSettings` 之下都有自己的支援寫入設定。 **請參閱連接器中的詳細資料文章-> 複製活動屬性一節**。 | 否       |

`formatSettings`下支援的**JSON 寫入設定**：

| 屬性      | 描述                                                  | 必要項                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| 類型          | FormatSettings 的類型必須設定為**JsonWriteSetting**。 | 是                                                   |
| filePattern |表示每個 JSON 檔案中儲存的資料模式。 允許的值為︰**setOfObjects** 和 **arrayOfObjects**。 **預設值**為 **setOfObjects**。 關於這些模式的詳細資訊，請參閱 [JSON 檔案模式](#json-file-patterns)一節。 |否 |

### <a name="json-file-patterns"></a>JSON 檔案模式

複製活動可以自動偵測並剖析下列 JSON 檔案模式。 

- **類型 I：setOfObjects**

    每個檔案都會包含單一物件，或以行分隔/串連的多個物件。 
    在複製活動接收中選擇此選項時，複製活動會產生單一 JSON 檔案，每行一個物件（以行分隔）。

    * **單一物件 JSON 範例**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **以行分隔的 JSON 範例**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **串連的 JSON 範例**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **類型 II：arrayOfObjects**

    每個檔案都會包含物件的陣列。

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

深入瞭解對應資料流程中[來源轉換](data-flow-source.md)和[接收轉換](data-flow-sink.md)的詳細資料。

## <a name="next-steps"></a>後續步驟

- [複製活動概觀](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
