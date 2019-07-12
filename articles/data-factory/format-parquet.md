---
title: Azure Data Factory 中的 parquet 格式 |Microsoft Docs
description: 本主題描述如何處理 Azure Data Factory 中的 Parquet 格式。
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 360b794f0d8ba9c145a92f015f264eb624fbb0f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65144885"
---
# <a name="parquet-format-in-azure-data-factory"></a>Azure Data Factory 中的 parquet 格式

當您想要遵循這篇文章**剖析 Parquet 檔案，或將資料寫入 Parquet 格式**。 

Parquet 格式支援下列連接器：[Amazon S3](connector-amazon-simple-storage-service.md)， [Azure Blob](connector-azure-blob-storage.md)， [Azure Data Lake 儲存體 Gen1](connector-azure-data-lake-store.md)， [Azure Data Lake 儲存體 Gen2](connector-azure-data-lake-storage.md)， [Azure 檔案儲存體](connector-azure-file-storage.md)，[檔案系統](connector-file-system.md)， [FTP](connector-ftp.md)， [Google 雲端儲存體](connector-google-cloud-storage.md)， [HDFS](connector-hdfs.md)， [HTTP](connector-http.md)，與[SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Parquet 資料集所支援的屬性的清單。

| 屬性         | 描述                                                  | 必要項 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的 type 屬性必須設定為**Parquet**。 | 是      |
| location         | 對檔案的位置設定。 每個檔案為基礎的連接器有它自己的位置類型和支援的屬性底下`location`。 **請參閱文章中的詳細資料連接器]-> [資料集屬性區段**。 | 是      |
| compressionCodec | 壓縮轉碼器來寫入 Parquet 檔案時使用。 從 Parquet 檔案讀取時，Data Factory 會自動決定根據檔案的中繼資料的壓縮轉碼器。<br>支援的類型為"**無**"，"**gzip**"，"**snappy**"（預設值） 和 「**lzo**"。 請注意，目前複製活動不支援 LZO。 | 否       |

> [!NOTE]
> Parquet 檔案不支援資料行名稱中的泛空白字元。

以下是在 Azure Blob 儲存體的 Parquet 資料集的範例：

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
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
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供一份 Parquet 來源和接收器所支援的屬性。

### <a name="parquet-as-source"></a>Parquet 作為來源

以下支援的屬性將複製活動中***\*來源\**** 一節。

| 屬性      | 描述                                                  | 必要項 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的 type 屬性必須設定為**ParquetSource**。 | 是      |
| storeSettings | 一組有關如何從資料存放區讀取資料的屬性。 每個檔案為基礎的連接器有自己支援的讀取的設定下`storeSettings`。 **請參閱文章中的詳細資料連接器-> 複製活動屬性 區段**。 | 否       |

### <a name="parquet-as-sink"></a>Parquet，接收

以下支援的屬性將複製活動中***\*接收器\**** 一節。

| 屬性      | 描述                                                  | 必要項 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的 type 屬性必須設定為**ParquetSink**。 | 是      |
| storeSettings | 如何將資料寫入至資料存放區上的屬性群組。 每個檔案為基礎的連接器有自己支援的寫入設定下`storeSettings`。 **請參閱文章中的詳細資料連接器-> 複製活動屬性 區段**。 | 否       |

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

了解詳細資料，從[來源轉換](data-flow-source.md)並[接收轉換](data-flow-sink.md)中對應的資料流。

## <a name="data-type-support"></a>資料類型支援

Parquet 複雜資料型別是目前不支援 （例如 MAP、 LIST、 結構）。

## <a name="using-self-hosted-integration-runtime"></a>使用自我裝載的整合執行階段

> [!IMPORTANT]
> 針對由自我裝載 Integration Runtime 所授權的複製 (例如，在內部部署與雲端資料存放區之間)，如果您不會**依原樣**複製 Parquet 檔案，就需要在 IR 機器上安裝 **64 位元的 JRE 8 (Java Runtime Environment) 或 OpenJDK**。 如需更多詳細資料，請參閱接下來的段落。

針對在自我裝載 IR 上搭配 Parquet 檔案序列化/還原序列化來執行的複製，ADF 會找出 Java 執行階段，方法是先檢查登錄 *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* 是否有 JRE，如果找不到，就接著檢查系統變數 *`JAVA_HOME`* 是否有 OpenJDK。

- **使用 JRE**：64 位元 IR 需要 64 位元 JRE。 您可以從[這裡](https://go.microsoft.com/fwlink/?LinkId=808605)找到該程式。
- **使用 OpenJDK**：自 IR 3.13 版開始便可支援。 請將 jvm.dll 與所有其他必要的 OpenJDK 組件一起封裝至自我裝載 IR 機器，然後相應地設定 JAVA_HOME 系統環境變數。

> [!TIP]
> 如果您使用自我裝載 Integration Runtime 將資料複製到 Parquet 格式 (或從該格式複製資料)，而且遇到錯誤顯示 [叫用 Java 時發生錯誤。訊息: **java.lang.OutOfMemoryError:Java heap space**]，您可以在裝載自我裝載 IR 的機器中新增環境變數 `_JAVA_OPTIONS`，以調整 JVM 的堆積大小下限/上限，使系統能執行這樣的複製，然後重新執行管線。

![在自我裝載 IR 上設定 JVM 堆積大小](C:/AzureContent/azure-docs-pr/articles/data-factory/media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

範例：將變數 `_JAVA_OPTIONS` 的值設定為 `-Xms256m -Xmx16g`。 旗標 `Xms` 指定 JAVA 虛擬機器 (JVM) 的初始記憶體配置集區，而 `Xmx` 指定記憶體配置集區的最大值。 這表示 JVM 啟動時有 `Xms` 數量的記憶體，且最多可以使用 `Xmx` 數量的記憶體。 根據預設，ADF 使用最小為 64 MB，最大為 1 G。

## <a name="next-steps"></a>後續步驟

- [複製活動概觀](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)