---
title: Azure Data Factory 中複製活動 |Microsoft Docs
description: 深入瞭解 Azure Data Factory 中的複製活動。 您可以使用它將資料從支援的來源資料存放區複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 8af5673ff0ffef7306a13eceda86f879b5b31413
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2019
ms.locfileid: "70060681"
---
# <a name="copy-activity-in-azure-data-factory"></a>Azure Data Factory 中的複製活動

> [!div class="op_single_selector" title1="選取您要使用的 Data Factory 版本:"]
> * [第 1 版](v1/data-factory-data-movement-activities.md)
> * [目前的版本](copy-activity-overview.md)

在 Azure Data Factory 中, 您可以使用複製活動, 在內部部署和雲端中的資料存放區之間複製資料。 複製資料之後, 您可以使用其他活動進一步轉換和分析它。 您也可以使用「複製活動」來發行商業智慧 (BI) 和應用程式耗用量的轉換和分析結果。

![複製活動的角色](media/copy-activity-overview/copy-activity.png)

複製活動會在[整合運行](concepts-integration-runtime.md)時間上執行。 針對不同的資料複製案例, 您可以使用不同類型的整合執行時間:

* 當您在可從任何 IP 透過網際網路公開存取的兩個數據存放區之間複製資料時, 您可以使用 Azure integration runtime 進行複製活動。 此整合執行時間是安全、可靠、可調整且[可全域使用](concepts-integration-runtime.md#integration-runtime-location)的。
* 當您要將資料複製到位於內部部署或具有存取控制 (例如 Azure 虛擬網路) 的網路中的資料存放區時, 您必須設定自我裝載整合執行時間。

整合執行時間必須與每個來源和接收資料存放區相關聯。 如需複製活動如何判斷要使用哪一個整合執行時間的相關資訊, 請參閱[判斷要使用的 IR](concepts-integration-runtime.md#determining-which-ir-to-use)。

若要將資料從來源複製到接收, 執行複製活動的服務會執行下列步驟:

1. 從來源資料存放區讀取資料。
2. 執行序列化/還原序列化、壓縮/解壓縮、資料行對應等。 它會根據輸入資料集、輸出資料集和複製活動的設定來執行這些作業。
3. 將資料寫入接收/目的地資料存放區。

![複製活動總覽](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>支援的資料存放區和格式

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>支援的檔案格式

您可以使用複製活動, 在兩個以檔案為基礎的資料存放區之間複製檔案。 在此情況下, 會有效率地複製資料, 而不會進行任何序列化或還原序列化。

複製活動也可以讀取和寫入下列格式的檔案:
- 文字
- JSON
- Avro
- ORC
- Parquet

複製活動可以使用下列編解碼器來壓縮和解壓縮檔案: 
- Gzip
- Deflate
- Bzip2
- ZipDeflate

如需詳細資訊, 請參閱支援的檔案[和壓縮格式](supported-file-formats-and-compression-codecs.md)。

例如, 您可以執行下列複製活動:

* 從內部部署 SQL Server 資料庫複製資料, 並以 Parquet 格式將資料寫入 Azure Data Lake Storage Gen2。
* 從內部部署檔案系統複製文字 (CSV) 格式的檔案, 並以 Avro 格式寫入 Azure Blob 儲存體。
* 從內部部署檔案系統複製壓縮檔案, 將它們解壓縮, 並將它們寫入 Azure Data Lake Storage Gen2。
* 從 Azure Blob 儲存體複製 Gzip 壓縮文字 (CSV) 格式的資料, 並將其寫入 Azure SQL Database。
* 許多需要序列化/還原序列化或壓縮/解壓縮的活動。

## <a name="supported-regions"></a>支援區域

啟用複製活動的服務可在[Azure 整合執行時間位置](concepts-integration-runtime.md#integration-runtime-location)所列的區域和地理區域中全域取得。 全域可用的拓撲可確保進行有效率的資料移動，通常可避免發生跨區域躍點的情況。 請參閱[依區域的產品](https://azure.microsoft.com/regions/#services), 以檢查特定區域中 Data Factory 和資料移動的可用性。

## <a name="configuration"></a>組態

若要在 Azure Data Factory 中使用複製活動, 您需要:

1. **建立來源資料存放區和接收資料存放區的連結服務。** 如需設定資訊和支援的屬性, 請參閱連接器文章的「連結服務屬性」一節。 您可以在本文的[支援的資料存放區和格式](#supported-data-stores-and-formats)一節中找到支援的連接器清單。
2. **建立來源和接收的資料集。** 如需設定資訊和支援的屬性, 請參閱來源和接收連接器文章的「資料集屬性」章節。
3. **建立具有複製活動的管線。** 下一節提供範例。

### <a name="syntax"></a>語法

複製活動的下列範本包含完整的支援屬性清單。 請指定適合您案例的屬性。

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>語法詳細資料

| 屬性 | 描述 | 必要項？ |
|:--- |:--- |:--- |
| Type | 若為複製活動, 請將設定為`Copy` | 是 |
| inputs | 指定您所建立的資料集, 以指向來源資料。 複製活動僅支援單一輸入。 | 是 |
| outputs | 指定您所建立的資料集, 以指向接收資料。 複製活動僅支援單一輸出。 | 是 |
| typeProperties | 指定要設定複製活動的屬性。 | 是 |
| source | 指定要用來抓取資料的複製來源類型和對應的屬性。<br/><br/>如需詳細資訊, 請參閱[支援的資料存放區和格式](#supported-data-stores-and-formats)中所列連接器文章中的「複製活動屬性」一節。 | 是 |
| sink | 指定複製接收類型和用於寫入資料的對應屬性。<br/><br/>如需詳細資訊, 請參閱[支援的資料存放區和格式](#supported-data-stores-and-formats)中所列連接器文章中的「複製活動屬性」一節。 | 是 |
| translator | 指定從來源到接收的明確資料行對應。 當預設複製行為不符合您的需求時, 就會套用此屬性。<br/><br/>如需詳細資訊, 請參閱[複製活動中的架構對應](copy-activity-schema-and-type-mapping.md)。 | 否 |
| dataIntegrationUnits | 指定代表[Azure 整合運行](concepts-integration-runtime.md)時間用於資料複製之耗電量的量值。 這些單位先前稱為雲端資料移動單位 (DMU)。 <br/><br/>如需詳細資訊, 請參閱[資料整合單位](copy-activity-performance.md#data-integration-units)。 | 否 |
| parallelCopies | 指定在從來源讀取資料, 並將資料寫入至接收時, 複製活動所要使用的平行處理原則。<br/><br/>如需詳細資訊, 請參閱[平行複製](copy-activity-performance.md#parallel-copy)。 | 否 |
| enableStaging<br/>stagingSettings | 指定是否要將暫時資料暫存在 Blob 儲存體中, 而不是直接將資料從來源複製到接收。<br/><br/>如需實用案例和設定詳細資料的相關資訊, 請參閱[分段複製](copy-activity-performance.md#staged-copy)。 | 否 |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| 選擇當您將資料從來源複製到接收時, 如何處理不相容的資料列。<br/><br/>如需詳細資訊, 請參閱[容錯](copy-activity-fault-tolerance.md)。 | 否 |

## <a name="monitoring"></a>監視

您可以在 Azure Data Factory**作者 & 監視器** UI 中或以程式設計方式監視複製活動執行。

### <a name="monitor-visually"></a>以視覺化方式監視

若要以視覺化方式監視複製活動執行, 請移至您的 data factory, 然後移至 [作者] [ **& 監視器**]。 在 [**監視**] 索引標籤上, 您會在 [**動作**] 資料行中看到一份管線執行清單, 其中包含 [**觀看活動執行**] 按鈕

![監視管線回合](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

選取 [**觀看活動執行**] 以查看管線執行中的活動清單。 在 [**動作**] 資料行中, 您會看到複製活動輸入、輸出、錯誤 (如果複製活動執行失敗) 和詳細資料的連結:

![監視活動回合](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

選取 [**動作**] 資料行中的 [**詳細資料**] 按鈕, 以查看複製活動的執行詳細資料和效能特性。 您會看到像是從來源複製到接收的資料列數量/數目、輸送量、複製活動經歷的步驟、對應的持續時間, 以及用於複製案例的設定等資訊。

>[!TIP]
>在某些情況下, 您也會在 [複製監視] 頁面的頂端看到**效能微調提示**。 這些秘訣會告訴您已發現的瓶頸, 並提供如何變更以提升複製輸送量的相關資訊。 如需範例, 請參閱本文的[效能和微調](#performance-and-tuning)一節。

**範例：從 Amazon S3 複製到 Azure Data Lake 存放**區
 ![監視活動執行詳細資料](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**範例：使用分段複製** 
監視活動執行詳細資料,從AzureSQLDatabase複製到AzureSQL資料倉儲![](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>以程式設計方式監視

複製活動執行詳細資料和效能特性也會在 [**複製活動執行結果** > **輸出**] 區段中傳回。 以下是可能會傳回的完整屬性清單。 您只會看到適用于您的複製案例的屬性。 如需如何監視活動執行的相關資訊, 請參閱[監視管線執行](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run)。

| 屬性名稱  | 描述 | 單位 |
|:--- |:--- |:--- |
| dataRead | 從來源讀取的資料量。 | Int64 值 (以位元組為單位) |
| dataWritten | 寫入接收的資料量。 | Int64 值 (以位元組為單位) |
| filesRead | 從檔案儲存體複製期間複製的檔案數目。 | Int64 值 (未指定單位) |
| filesWritten | 複製到檔案儲存體期間複製的檔案數目。 | Int64 值 (未指定單位) |
| sourcePeakConnections | 在複製活動執行期間, 建立到來源資料存放區的並行連線尖峰數目。 | Int64 值 (未指定單位) |
| sinkPeakConnections | 在複製活動執行期間, 建立至接收資料存放區的並行連線尖峰數目。 | Int64 值 (未指定單位) |
| rowsRead | 從來源讀取的資料列數 (不適用於二進位複製)。 | Int64 值 (未指定單位) |
| rowsCopied | 複製到接收的資料列數 (不適用於二進位複製)。 | Int64 值 (未指定單位) |
| rowsSkipped | 略過的不相容資料列數目。 您可以將設定`enableSkipIncompatibleRow`為 true, 以啟用略過不相容的資料列。 | Int64 值 (未指定單位) |
| copyDuration | 複製執行的持續時間。 | Int32 值 (以秒為單位) |
| throughput | 資料傳輸速率。 | 浮點數字, 以 KBps 為單位 |
| sourcePeakConnections | 在複製活動執行期間, 建立到來源資料存放區的並行連線尖峰數目。 | Int32 值 (沒有單位) |
| sinkPeakConnections| 在複製活動執行期間, 建立至接收資料存放區的並行連線尖峰數目。| Int32 值 (沒有單位) |
| sqlDwPolyBase | 將資料複製到 SQL 資料倉儲時是否使用 PolyBase。 | Boolean |
| redshiftUnload | 從 Redshift 複製資料時, 是否使用 UNLOAD。 | Boolean |
| hdfsDistcp | 從 HDFS 複製資料時是否使用 DistCp。 | Boolean |
| effectiveIntegrationRuntime | 用來為活動執行供電的整合執行時間 (IR) 或執行時間 (格式`<IR name> (<region if it's Azure IR>)`為)。 | 文字 (字串) |
| usedDataIntegrationUnits | 複製期間的有效資料整合單位。 | Int32 值 |
| usedParallelCopies | 複製期間有效的 parallelCopies。 | Int32 值 |
| redirectRowPath | 您在`redirectIncompatibleRowSettings`屬性中設定的 blob 儲存體中, 略過不相容資料列的記錄檔路徑。 請參閱本文稍後的[容錯](#fault-tolerance)。 | 文字 (字串) |
| executionDetails | 複製活動所經歷之階段的詳細資料, 以及對應的步驟、持續時間、設定等等。 我們不建議您剖析此區段, 因為它可能會變更。<br/><br/>Data Factory 也會報告下`detailedDurations`各種階段所花費的詳細持續時間 (以秒為單位)。 這些步驟的持續時間是獨佔的。 只有適用于指定複製活動執行的持續時間才會出現:<br/>**佇列持續時間**(`queuingDuration`):複製活動在整合執行時間上實際開始之前的時間量。 如果您使用自我裝載 IR 且此值很大, 請檢查 IR 容量和使用量, 並根據您的工作負載相應增加或相應放大。 <br/>**複製前腳本持續時間**(`preCopyScriptDuration`):複製活動在 IR 上啟動的時間和複製活動完成執行接收資料存放區中的預先複製腳本之間所經過的時間。 適用于設定預先複製腳本時。 <br/>**第一個位元組時間**(`timeToFirstByte`):上一個步驟結束與紅外線從來源資料存放區接收第一個位元組之間經過的時間。 適用于非以檔案為基礎的來源。 如果這個值很大, 請檢查並優化查詢或伺服器。<br/>**傳輸期間**(`transferDuration`):上一個步驟結束與紅外線將所有資料從來源傳輸到接收之間所經過的時間。 | Array |
| perfRecommendation | 複製效能微調秘訣。 如需詳細資訊, 請參閱[效能和微調](#performance-and-tuning)。 | Array |

```json
"output": {
    "dataRead": 6198358,
    "dataWritten": 19169324,
    "filesRead": 1,
    "sourcePeakConnections": 1,
    "sinkPeakConnections": 2,
    "rowsRead": 39614,
    "rowsCopied": 39614,
    "copyDuration": 1325,
    "throughput": 4.568,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 4,
    "usedParallelCopies": 1,
    "executionDetails": [
        {
            "source": {
                "type": "AzureBlobStorage"
            },
            "sink": {
                "type": "AzureSqlDatabase"
            },
            "status": "Succeeded",
            "start": "2019-08-06T01:01:36.7778286Z",
            "duration": 1325,
            "usedDataIntegrationUnits": 4,
            "usedParallelCopies": 1,
            "detailedDurations": {
                "queuingDuration": 2,
                "preCopyScriptDuration": 12,
                "transferDuration": 1311
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "Sink Azure SQL Database: The DTU utilization was high during the copy activity run. To achieve better performance, you are suggested to scale the database to a higher tier than the current 1600 DTUs.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2043368",
            "RuleName": "AzureDBTierUpgradePerfRecommendRule"
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>結構描述和資料類型對應

如需複製活動如何將您的來源資料對應至接收的相關資訊, 請參閱[架構和資料類型對應](copy-activity-schema-and-type-mapping.md)。

## <a name="fault-tolerance"></a>容錯

根據預設, 當來源資料列與接收資料列不相容時, 複製活動會停止複製資料並傳回失敗。 若要讓複製成功, 您可以將複製活動設定為略過並記錄不相容的資料列, 並只複製相容的資料。 如需詳細資訊, 請參閱[複製活動的容錯](copy-activity-fault-tolerance.md)。

## <a name="performance-and-tuning"></a>效能和微調

[複製活動的效能和擴充性指南](copy-activity-performance.md)會說明透過 Azure Data Factory 中的複製活動, 影響資料移動效能的主要因素。 它也會列出測試期間觀察到的效能值, 並討論如何將複製活動的效能優化。

在某些情況下, 當您在 Data Factory 中執行複製活動時, 您會在 [[複製活動監視] 頁面](#monitor-visually)頂端看到**效能微調提示**, 如下列範例所示。 提示會告訴您指定的複本執行所識別的瓶頸。 它們也會提供要變更哪些內容以提升複製輸送量的相關資訊。 效能微調秘訣目前提供在將資料複製到 Azure SQL 資料倉儲時使用 PolyBase 的建議、在資料存放區端的資源為瓶頸時增加 Azure Cosmos DB ru 或 Azure SQL Database Dtu, 以及移除不需要的分段複本。

**範例：使用效能微調提示覆制到 Azure SQL Database**

在此範例中, 在複製執行期間, Data Factory 會追蹤接收 Azure SQL Database 中的高 DTU 使用率。 這種狀況會使寫入作業變慢。 建議在 Azure SQL Database 層增加 Dtu:

![複製監視和效能微調祕訣](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>增量複製
Data Factory 可讓您以累加方式將差異資料從來源資料存放區複製到接收資料存放區。 如需詳細資訊[, 請參閱教學課程:以累加方式](tutorial-incremental-copy-overview.md)複製資料。

## <a name="next-steps"></a>後續步驟
請參閱下列快速入門、教學課程和範例：

- [在相同的 Azure Blob 儲存體帳戶中, 將資料從一個位置複製到另一個位置](quickstart-create-data-factory-dot-net.md)
- [將資料從 Azure Blob 儲存體複製到 Azure SQL Database](tutorial-copy-data-dot-net.md)
- [將資料從內部部署 SQL Server 資料庫複製到 Azure](tutorial-hybrid-copy-powershell.md)
