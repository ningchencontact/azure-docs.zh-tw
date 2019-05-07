---
title: Azure Data Factory 中的分隔的文字格式 |Microsoft Docs
description: 本主題描述如何處理 Azure Data Factory 中的分隔的文字格式。
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 407b8ba2fda35d3acbf1b425bb15fe20778613d7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146010"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Azure Data Factory 中的分隔的文字格式

當您想要遵循這篇文章**剖析符號分隔的文字檔案，或將資料寫入分隔的文字格式**。 

分隔的文字格式支援下列連接器：[Amazon S3](connector-amazon-simple-storage-service.md)， [Azure Blob](connector-azure-blob-storage.md)， [Azure Data Lake 儲存體 Gen1](connector-azure-data-lake-store.md)， [Azure Data Lake 儲存體 Gen2](connector-azure-data-lake-storage.md)， [Azure 檔案儲存體](connector-azure-file-storage.md)，[檔案系統](connector-file-system.md)， [FTP](connector-ftp.md)， [Google 雲端儲存體](connector-google-cloud-storage.md)， [HDFS](connector-hdfs.md)， [HTTP](connector-http.md)，與[SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供一份分隔的文字資料集所支援的屬性。

| 屬性         | 描述                                                  | 必要項 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的 type 屬性必須設定為**DelimitedText**。 | 是      |
| location         | 對檔案的位置設定。 每個檔案為基礎的連接器有它自己的位置類型和支援的屬性底下`location`。 **請參閱文章中的詳細資料連接器]-> [資料集屬性區段**。 | 是      |
| columnDelimiter  | 用來分隔檔案中的資料行的字元。 目前，多重字元分隔符號只支援對應的資料流，但不是複製活動。 <br>預設值是**逗號`,`** ，當資料行分隔符號定義為空字串，表示沒有分隔符號，整行會被視為單一資料行。 | 否       |
| rowDelimiter     | 單一字元或者"\r\n"用來分隔檔案中的資料列。<br>預設值是下列值之一**讀取: ["\r\n"、"\r"、"\n"]**，並 **"\n"或"\r\n"寫入**對應資料的流程，並分別複製活動。 <br>當`rowDelimiter`設定為 沒有分隔符號 （空字串）、`columnDelimiter`必須設定為沒有分隔符號 （空字串） 做為表示也視為單一值的整個內容。 | 否       |
| quoteChar        | 要引用的資料行值，如果它包含資料行分隔符號的單一字元。 <br>預設值是**雙引號括住** `"`。 <br>適用於對應資料流程，`quoteChar`不得為空字串。 <br>對於複製活動，當`quoteChar`定義為空字串，表示沒有任何引號字元，而且資料行值不加上引號，和`escapeChar`用來逸出的資料行分隔符號和本身。 | 否       |
| escapeChar       | 要逸出引號括住值內的單一字元。<br>預設值是**反斜線`\`** 。 <br>適用於對應資料流程，`escapeChar`不得為空字串。 <br/>對於複製活動，當`escapeChar`定義為空字串，`quoteChar`必須設定為空的字串，在此情況下確定所有資料行值不包含分隔符號。 | 否       |
| firstRowAsHeader | 指定是否要將/品牌的第一個資料列作為標頭行的資料行名稱。<br>允許的值為**真**並**false** （預設值）。 | 否       |
| nullValue        | 指定 null 值的字串表示。 <br>預設值是**空字串**。 | 否       |
| encodingName     | 用來讀取/寫入測試檔案的編碼類型。 <br>允許的值如下所示："UTF-8"、"UTF-16"、"UTF-16BE"、"UTF-32"、"UTF-32BE"，"US-ASCII"、"UTF-7、UTF"、"BIG5 」、"EUC-JP 」、"EUC-KR 」、"ENCODING="GB2312"、"GB18030"、"JOHAB 」、"SHIFT-JIS 」、"CP875 」、"CP866 」、"IBM00858 」、"IBM037 」、"IBM273"、"IBM437 」、"IBM500"、"IBM737"、"IBM775 」、"IBM850 」，「IBM852"，"IBM855"、"IBM857 」、"IBM860 」、"IBM861 」、"IBM863 」、"IBM864 」、"IBM865 」、"IBM869 」、"IBM870 」、"IBM01140 」、"IBM01141 」、"IBM01142"、"IBM01143 」、"IBM01144 」、"IBM01145 」、"IBM01146"、"IBM01147 」、"IBM01148 」、"IBM01149 」、"ISO-2022年-JP"、"ISO-2022年-韓國"，"ISO-8859-1"、"ISO-8859-2"、"ISO-8859-3"、"ISO-8859-4"、"ISO-8859-5"、"ISO-8859-6"、"ISO-8859-7"、"ISO-8859-8"、"ISO-8859-9"、"ISO-8859-13"、"ISO-8859-15"、"WINDOWS 874"、"WINDOWS-1250"、"WINDOWS 1251"、"WINDOWS-1252"、"WINDOWS-1253"，"WINDOWS 1254"、"WINDOWS-1255"、"WINDOWS-1256"、"WINDOWS-1257 」、 「 WINDOWS 1258"。<br>請注意對應的資料流不支援 utf-7 編碼。 | 否       |
| compressionCodec | 用來讀取/寫入文字檔案的壓縮轉碼器。 <br>允許的值為**bzip2**， **gzip**， **deflate**， **ZipDeflate**， **snappy**，或  **lz4**。 若要儲存檔案時，會使用它。 <br>請注意目前複製活動不支援 「 snappy"&"lz4 」，而且對應的資料流不支援 「 ZipDeflate"。 | 否       |
| compressionLevel | 壓縮比率。 <br>允許的值為**最佳**或是**Fastest**。<br>- **最快：** 即使未以最佳方式壓縮所產生的檔案，壓縮作業也應儘速完成。<br>- **最佳**:即使作業耗費較長的時間才能完成，壓縮作業也應以最佳方式壓縮。 如需詳細資訊，請參閱 [壓縮層級](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 主題。 | 否       |

以下是 Azure Blob 儲存體上的符號分隔的文字資料集的範例：

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供一份分隔的文字來源和接收器所支援的屬性。

### <a name="delimited-text-as-source"></a>分隔的文字，做為來源 

以下支援的屬性將複製活動中***\*來源\**** 一節。

| 屬性       | 描述                                                  | 必要項 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 複製活動來源的 type 屬性必須設定為**DelimitedTextSource**。 | 是      |
| formatSettings | 一組屬性。 請參閱**分隔的文字讀取設定**下表。 | 否       |
| storeSettings  | 一組有關如何從資料存放區讀取資料的屬性。 每個檔案為基礎的連接器有自己支援的讀取的設定下`storeSettings`。 **請參閱文章中的詳細資料連接器-> 複製活動屬性 區段**。 | 否       |

支援**分隔的文字檔讀取設定**下方`formatSettings`:

| 屬性      | 描述                                                  | 必要項 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | FormatSettings 的類型必須設為**DelimitedTextReadSetting**。 | 是      |
| skipLineCount | 指出從輸入檔讀取資料時，要略過的**非空白**資料列數。 <br>如果指定 skipLineCount 和 firstRowAsHeader，則會先略過程式碼行，再從輸入檔讀取標頭資訊。 | 否       |

### <a name="delimited-text-as-sink"></a>分隔的文字，作為接收器

以下支援的屬性將複製活動中***\*接收器\**** 一節。

| 屬性       | 描述                                                  | 必要項 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 複製活動來源的 type 屬性必須設定為**DelimitedTextSink**。 | 是      |
| formatSettings | 一組屬性。 請參閱**分隔的文字寫入設定**下表。 |          |
| storeSettings  | 如何將資料寫入至資料存放區上的屬性群組。 每個檔案為基礎的連接器有自己支援的寫入設定下`storeSettings`。 **請參閱文章中的詳細資料連接器-> 複製活動屬性 區段**。 | 否       |

支援**分隔的文字寫入設定**下方`formatSettings`:

| 屬性      | 描述                                                  | 必要項                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | FormatSettings 的類型必須設為**DelimitedTextWriteSetting**。 | 是                                                   |
| fileExtension | 用來命名輸出檔案，例如副檔名`.csv`， `.txt`。 它必須是指定何時`fileName`輸出中未指定 DelimitedText 資料集。 | 是檔案名稱未指定輸出資料集中時 |

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

了解詳細資料，從[來源轉換](data-flow-source.md)並[接收轉換](data-flow-sink.md)中對應的資料流。

## <a name="next-steps"></a>後續步驟

- [複製活動概觀](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)