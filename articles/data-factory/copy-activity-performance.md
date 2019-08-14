---
title: Azure Data Factory 中的複製活動效能和微調指南 |Microsoft Docs
description: 瞭解當您使用複製活動時, 會影響 Azure Data Factory 中資料移動效能的關鍵因素。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: d8ce0a4f6bacdd1c8c858d474e6f3957a23c6357
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967368"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>複製活動效能和微調指南
> [!div class="op_single_selector" title1="選取您要使用的 Azure Data Factory 版本:"]
> * [第 1 版](v1/data-factory-copy-activity-performance.md)
> * [目前的版本](copy-activity-performance.md)


Azure Data Factory 複製活動提供頂級的安全、可靠且高效能的資料載入解決方案。 您可以使用它在各種不同的雲端和內部部署資料存放區中每天複製數十 tb 的資料。 快速資料載入效能是確保您可以專注于核心 big data 問題的關鍵: 建立先進的分析解決方案, 並取得所有資料的深入見解。

Azure 提供一組企業級的資料儲存體和資料倉儲解決方案。 複製活動提供高度優化的資料載入體驗, 可讓您輕鬆設定和設定。 使用單一複製活動, 您可以將資料載入:

* Azure SQL 資料倉儲 1.2 GBps。
* Azure Blob 儲存體 (1.0 GBps)。
* Azure Data Lake 存放區以 1.0 GBps 為限。

本文章說明：

* 支援的來源和接收資料存放區的[效能參考編號](#performance-reference), 可協助您規劃專案。
* 可在不同案例中提高複製輸送量的功能, 包括[資料整合單位](#data-integration-units)(diu)、[平行複製](#parallel-copy)和[分段複製](#staged-copy)。
* [效能微調指導](#performance-tuning-steps)方針, 說明如何微調效能, 以及可能影響複製效能的關鍵因素。

> [!NOTE]
> 如果您一般不熟悉複製活動, 請參閱[複製活動總覽](copy-activity-overview.md), 再閱讀本文。
>

## <a name="performance-reference"></a>效能參考

做為參考, 下表顯示根據內部測試, 在單一複製活動執行中給定來源和接收配對的複製輸送量數位 (以 MBps 為單位)。 為了進行比較, 它也會示範[資料整合單位](#data-integration-units)或自我裝載[整合運行](concepts-integration-runtime.md#self-hosted-integration-runtime)時間擴充性 (多個節點) 的不同設定如何協助複製效能。

![效能矩陣](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> 當複製活動在 Azure 整合執行時間上執行時, 允許的最小資料整合單位 (先前稱為資料移動單位) 為二。 如果未指定, 請參閱[資料整合單位](#data-integration-units)中使用的預設資料整合單位。

**注意事項：**

* 輸送量的計算方式是使用下列公式: [從來源讀取的資料大小]/[複製活動執行持續時間]。
* 資料表中的效能參考數位是使用單一複製活動執行中的[TPC-H](http://www.tpc.org/tpch/)資料集來測量。 以檔案為基礎的存放區的測試檔案是大小為 10 GB 的多個檔案。
* 在 Azure 資料存放區中，來源和接收位於相同 Azure 區域中。
* 針對內部部署與雲端資料存放區之間的混合式複製, 每個自我裝載整合執行時間節點都是在與資料存放區不同的電腦上執行, 並具有下列規格。 當僅執行單一活動，複製作業將只會取用測試電腦一小部分的 CPU、記憶體或網路頻寬。
    <table>
    <tr>
        <td>CPU</td>
        <td>32 核心 2.20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>記憶體</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>網路</td>
        <td>網際網路介面：10 Gbps；內部網路介面：40 Gbps</td>
    </tr>
    </table>


> [!TIP]
> 您可以使用更多 Diu 來達到較高的輸送量。 例如, 您可以使用 100 Diu, 將資料從 Azure Blob 儲存體複製到 Azure Data Lake 存放區 (1.0 GBps)。 如需此功能和支援案例的詳細資訊, 請參閱[資料整合單位](#data-integration-units)一節。 

## <a name="data-integration-units"></a>資料整合單位

資料整合單位是一種量值, 代表 Azure Data Factory 中單一單位的能力 (CPU、記憶體和網路資源分配的組合)。 資料整合單位僅適用于[Azure 整合運行](concepts-integration-runtime.md#azure-integration-runtime)時間, 但不會套用至[自我裝載整合運行](concepts-integration-runtime.md#self-hosted-integration-runtime)時間。

讓複製活動執行的最小 Diu 是兩個。 如果未指定，下表列出用於不同複製案例中的預設 DIU：

| 複製案例 | 服務決定的預設 DIU |
|:--- |:--- |
| 在以檔案為基礎的存放區之間複製資料 | 根據檔案的數目和大小, 介於4到32之間 |
| 將資料複製到 Azure SQL Database 或 Azure Cosmos DB |依據接收器 Azure SQL Database 或 Cosmos DB 的層 (Dtu 數/ru 數目), 介於4到16之間 |
| 所有其他複製案例 | 4 |

若要覆寫此預設值，請如下所示指定 **dataIntegrationUnits** 屬性的值。 **DataIntegrationUnits**屬性的*允許值*最高為256。 根據您的資料模式，複製作業會在執行階段使用的 *實際 DIU 數目* 等於或小於所設定的值。 如需在為特定複製來源和接收設定更多單位時可能獲得之效能增益水準的相關資訊，請參閱 [效能參考](#performance-reference)。

當您監視活動執行時, 您可以在複製活動輸出中查看每個複製執行所使用的 Diu。 如需詳細資訊, 請參閱[複製活動監視](copy-activity-overview.md#monitoring)。

> [!NOTE]
> 目前只有當您將多個檔案從 Azure 儲存體、Azure Data Lake Storage、Amazon S3、Google Cloud Storage、雲端 FTP 或雲端 SFTP 複製到任何其他雲端資料存放區時, 才會套用超過四個 Diu 的設定。
>

**範例**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

### <a name="data-integration-units-billing-impact"></a>資料整合單位計費影響

請記住, 系統會根據複製作業的總時間來向您收費。 您要支付資料移動費用的總持續時間是 Diu 之間的持續時間總和。 若過去某複製作業使用 2 個雲端單位花費 1 小時，現在使用 8 個雲端單位花費 15 分鐘，則兩者的整體費用幾乎相同。

## <a name="parallel-copy"></a>平行複製

您可以使用**parallelCopies**屬性來指出您想要複製活動使用的平行處理原則。 您可以將此屬性視為可以從來源讀取或以平行方式寫入接收資料存放區的複製活動內的最大執行緒數目。

針對每個複製活動執行, Azure Data Factory 會決定要用來將資料從來源資料存放區複製到目的地資料存放區的平行複製數目。 其使用的預設平行複製數目取決於您所使用的來源和接收類型。

| 複製案例 | 由服務決定的預設平行複製計數 |
| --- | --- |
| 在以檔案為基礎的存放區之間複製資料 |取決於檔案大小和用來在兩個雲端資料存放區之間複製資料的 Diu 數, 或自我裝載整合執行時間電腦的實體設定。 |
| 將資料從任何來源存放區複製到 Azure 表格儲存體 |4 |
| 所有其他複製案例 |1 |

> [!TIP]
> 當您在以檔案為基礎的存放區之間複製資料時, 預設行為通常會提供您最佳的輸送量。 系統會根據您的來源檔案模式自動決定預設行為。

若要控制裝載資料存放區之電腦上的負載, 或微調複製效能, 您可以覆寫預設值並指定**parallelCopies**屬性的值。 值必須是大於或等於 1 的整數。 在執行時間, 為了達到最佳效能, 複製活動會使用小於或等於您所設定之值的值。

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

**注意事項：**

* 當您在以檔案為基礎的存放區之間複製資料時, **parallelCopies**會決定檔案層級的平行處理原則。 單一檔案內的區塊化會自動且透明地出現在下方。 其設計目的是針對指定的來源資料存放區類型, 使用最適合的區塊大小, 以平行方式載入資料, 並與**parallelCopies**。 資料移動服務在執行階段用於複製作業的實際平行複製數目不會超過您擁有的檔案數目。 如果複製行為是**mergeFile**, 複製活動就無法利用檔案層級的平行處理原則。
* 當您從不是以檔案為基礎的存放區複製資料時 ( [Oracle](connector-oracle.md#oracle-as-source)、 [Teradata](connector-teradata.md#teradata-as-source)、 [Sap 資料表](connector-sap-table.md#sap-table-as-source)和[sap 開放式中樞](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)連接器除外, 做為已啟用資料分割的來源), 會儲存至以檔案為基礎之資料移動服務的存放區。忽略**parallelCopies**屬性。 即使已指定平行處理原則，也不會套用於此案例。
* **ParallelCopies**屬性正交于**dataIntegrationUnits**。 前者會跨所有資料整合單位計算。
* 當您指定**parallelCopies**屬性的值時, 請考慮來源和接收資料存放區的負載增加。 也請考慮負載增加至自我裝載整合執行時間 (如果複製活動是由其進行授權), 例如針對混合式複製。 當您對相同的資料存放區執行相同活動的多個活動或並存執行時, 就會發生這種負載增加的情況。 如果您注意到資料存放區或自我裝載整合執行時間已負擔負載, 請減少**parallelCopies**值以減輕負載。

## <a name="staged-copy"></a>分段複製

從來源資料存放區將資料複製到接收資料存放區時，您可以選擇使用 Blob 儲存體做為過渡暫存存放區。 暫存在下列情況下特別有用︰

- **您想要透過 PolyBase 將資料從各種資料存放區內嵌到 SQL 資料倉儲。** SQL 資料倉儲使用 PolyBase 做為高輸送量機制，將大量資料載入 SQL 資料倉儲。 來源資料必須位於 Blob 儲存體或 Azure Data Lake 存放區中, 而且必須符合其他準則。 當您從 Blob 儲存體或 Azure Data Lake Store 以外的資料存放區載入資料時，您可以啟用透過過渡暫存 Blob 儲存體的資料複製。 在此情況下, Azure Data Factory 會執行必要的資料轉換, 以確保它符合 PolyBase 的需求。 然後，它會使用 PolyBase 將資料有效率地載入到 SQL 資料倉儲。 如需詳細資訊，請參閱[使用 PolyBase 將資料載入 Azure SQL 資料倉儲](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。
- **有時候, 執行混合式資料移動 (也就是從內部部署資料存放區複製到雲端資料存放區) 時, 會需要一些時間才能透過低速網路連線。** 若要改善效能, 您可以使用分段複製來壓縮內部部署資料, 以便將資料移至雲端中的暫存資料存放區所需的時間較短。 接著, 您可以在載入至目的地資料存放區之前, 先解壓縮臨時存儲區中的資料。
- **您不想要在防火牆中開啟埠80和埠443以外的通訊埠, 因為公司的 IT 原則。** 例如，從內部部署資料存放區將資料複製到 Azure SQL Database 接收或 Azure SQL 資料倉儲接收時，您必須針對 Windows 防火牆和公司防火牆啟用連接埠 1433 上的輸出 TCP 通訊。 在此案例中, 分段複製可以利用自我裝載整合執行時間, 先透過 HTTP 或 HTTPS 在埠443上將資料複製到 Blob 儲存體暫存實例。 然後, 它可以將資料從 Blob 儲存體暫存載入 SQL Database 或 SQL 資料倉儲。 在此流程中，您不需要啟用連接埠 1433。

### <a name="how-staged-copy-works"></a>分段複製的運作方式

當您啟用暫存功能時，會先從來源資料存放區複製資料到暫存 Blob 儲存體 (自備)。 接著再從暫存資料存放區複製資料到接收資料存放區。 Azure Data Factory 會自動為您管理兩階段流程。 在資料移動完成後, Azure Data Factory 也會清除暫存儲存體中的臨時資料。

![分段複製](media/copy-activity-performance/staged-copy.png)

當您使用暫存存放區啟用資料移動時, 可以指定是否要在將資料從來源資料存放區移至過渡或暫存資料存放區之前先壓縮資料, 然後再從過渡或暫存的 dat 移動資料之前進行解壓縮。接收資料存放區的存放區。

目前, 您無法在透過不同自我裝載的 IRs 連線的兩個數據存放區之間複製資料, 不論是否有分段複製也不會。 針對這種情況, 您可以設定兩個明確連結的複製活動, 從來源複製到預備環境, 然後從暫存到接收。

### <a name="configuration"></a>組態

設定 [複製] 活動中的 [ **enableStaging** ] 設定, 指定在將資料載入目的地資料存放區之前, 是否要在 Blob 儲存體中暫存資料。 當您將**enableStaging**設定`TRUE`為時, 請指定下表所列的其他屬性。 您也需要建立 Azure 儲存體或儲存體共用存取簽章連結服務, 以供暫存 (如果您沒有的話)。

| 內容 | 說明 | 預設值 | 必要項 |
| --- | --- | --- | --- |
| enableStaging |指定您是否要透過過渡暫存存放區複製資料。 |False |否 |
| linkedServiceName |指定 [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) 連結服務的名稱，以代表您用來做為過渡暫存存放區的儲存體執行個體。 <br/><br/> 您無法使用具有共用存取簽章的儲存體, 透過 PolyBase 將資料載入 SQL 資料倉儲。 您可以將它用於其他所有案例。 |N/A |是，當 **enableStaging** 設為 TRUE |
| 路徑 |指定要包含分段資料的 Blob 儲存體路徑。 如果您未提供路徑, 服務會建立容器來儲存暫存資料。 <br/><br/> 只有在使用具有共用存取簽章的儲存體時，或需要讓暫存資料位於特定位置時，才指定路徑。 |N/A |否 |
| enableCompression |指定是否應該先壓縮資料, 再將它複製到目的地。 此設定可減少傳輸的資料量。 |False |否 |

>[!NOTE]
> 如果您在啟用壓縮的情況下使用分段複製, 則不支援暫存 blob 連結服務的服務主體或 MSI 驗證。

以下是複製活動的範例定義, 其中包含上表中所述的屬性:

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>分段複製的計費影響

您會根據兩個步驟向您收費: 複製持續時間和複製類型。

* 當您在雲端複製期間使用暫存時, 如果將資料從雲端資料存放區複製到另一個雲端資料存放區 (這兩個階段都是由 Azure integration runtime 所授權), 您就會向您收取 [步驟1和步驟2的複製持續時間總和] x [雲端複製單位價格]。
* 當您在混合式複製期間使用暫存時, 會將資料從內部部署資料存放區複製到雲端資料存放區, 由自我裝載整合執行時間所授權的一個階段, 您需支付 [混合式複製持續時間] x [混合式複製單位價格] + [雲端複製持續時間]x [雲端複製單位價格]。

## <a name="performance-tuning-steps"></a>效能微調步驟

採取下列步驟, 使用複製活動來微調 Azure Data Factory 服務的效能。

1. **建立基準。** 在開發階段, 使用複製活動針對代表性資料範例來測試您的管線。 在[複製活動監視](copy-activity-overview.md#monitoring)之後收集執行詳細資料和效能特性。

2. **診斷並優化效能。** 如果您觀察到的效能不符合您的預期, 請找出效能瓶頸。 然後將效能最佳化，以消除或減少瓶頸的影響。

    在某些情況下, 當您在 Azure Data Factory 中執行複製活動時, 您會在 [[複製活動監視] 頁面](copy-activity-overview.md#monitor-visually)上看到「效能調整秘訣」訊息, 如下列範例所示。 訊息會告訴您針對指定的複本執行所識別的瓶頸。 它也會引導您進行要變更的內容, 以提高複製輸送量。 效能微調秘訣目前提供的建議如下:

    - 當您將資料複製到 Azure SQL 資料倉儲時, 請使用 PolyBase。
    - 當資料存放區端的資源為瓶頸時, 增加 Azure Cosmos DB 要求單位或 Azure SQL Database Dtu (資料庫輸送量單位)。
    - 移除不必要的分段複製。

    效能調整規則也會逐漸變豐富。

    **範例：使用效能微調秘訣複製到 Azure SQL Database**

    在此範例中, 在複製執行期間, Azure Data Factory 通知接收器 Azure SQL Database 到達高 DTU 使用率, 這會使寫入作業變慢。 建議是以更多 Dtu 來增加 Azure SQL Database 層。 

    ![複製監視和效能微調祕訣](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    此外，以下是一些常見的考量。 效能診斷的完整說明不在本文的討論之列。

   * 效能功能︰
     * [平行複製](#parallel-copy)
     * [資料整合單位](#data-integration-units)
     * [分段複製](#staged-copy)
     * [自我裝載整合執行時間的擴充性](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [自我裝載整合執行階段](#considerations-for-self-hosted-integration-runtime)
   * [Source](#considerations-for-the-source)
   * [接收](#considerations-for-the-sink)
   * [序列化和還原序列化](#considerations-for-serialization-and-deserialization)
   * [壓縮](#considerations-for-compression)
   * [資料行對應](#considerations-for-column-mapping)
   * [其他考量](#other-considerations)

3. **將設定擴充到整個資料集。** 當您對執行結果及效能感到滿意時, 可以展開定義和管線來涵蓋整個資料集。

## <a name="considerations-for-self-hosted-integration-runtime"></a>自我裝載整合執行時間的考慮

如果您的複製活動在自我裝載整合執行時間上執行, 請注意下列事項:

**設定**：建議您使用專用的電腦來裝載整合執行時間。 請參閱[使用自我裝載整合執行時間的考慮](concepts-integration-runtime.md)。

**相應放大**：具有一或多個節點的單一邏輯自我裝載整合執行時間, 可同時為多個複製活動執行提供服務。 如果您在混合式資料移動上需要大量的並行複製活動執行, 或有大量資料要複製, 請考慮[相應放大自我裝載整合運行](create-self-hosted-integration-runtime.md#high-availability-and-scalability)時間, 以布建更多資源以提供更好的複製功能。

## <a name="considerations-for-the-source"></a>來源的考量

### <a name="general"></a>一般

請確定基礎資料存放區不是由執行或對其執行的其他工作負載所淹沒。

針對 Microsoft 資料存放區, 請參閱資料存放區特定的[監視和微調主題](#performance-reference)。 這些主題可協助您了解資料存放區的效能特性，以及如何最小化回應時間和最大化輸送量。

* 如果您將資料從 Blob 儲存體複製到 SQL 資料倉儲, 請考慮使用 PolyBase 以提升效能。 如需詳細資訊，請參閱[使用 PolyBase 將資料載入 Azure SQL 資料倉儲](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。
* 如果您將資料從 HDFS 複製到 Azure Blob 儲存體或 Azure Data Lake 存放區, 請考慮使用 DistCp 來提升效能。 如需詳細資訊, 請參閱[使用 DistCp 從 HDFS 複製資料](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs)。
* 如果您將資料從 Redshift 複製到 Azure SQL 資料倉儲、Azure BLob 儲存體或 Azure Data Lake 存放區, 請考慮使用 UNLOAD 來提升效能。 如需詳細資訊, 請參閱[使用 UNLOAD 從 Amazon Redshift 複製資料](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)。

### <a name="file-based-data-stores"></a>以檔案為基礎的資料存放區

* **平均檔案大小和檔案計數**：複製活動會一次傳送一個檔案的資料。 在要移動的資料量相同的前提下，如果資料包含許多個小型檔案而非少數幾個大型檔案，其整體輸送量會較低，因為每個檔案都需要啟動程序階段。 可能的話, 請將小型檔案結合成較大的檔案, 以取得更高的輸送量。
* **檔案格式和壓縮**：如需可改善效能的其他方法，請參閱[序列化和還原序列化的考量](#considerations-for-serialization-and-deserialization)和[壓縮的考量](#considerations-for-compression)小節。

### <a name="relational-data-stores"></a>關聯式資料存放區

* **資料模式**：資料表結構描述對複製輸送量會有影響。 較大的資料列大小提供比較小的資料列大小更佳的效能, 以複製相同數量的資料。 這是因為資料庫可以更有效率地擷取包含較少資料列的較少資料批次。
* **查詢或預存程序**：優化您在複製活動來源中指定的查詢或預存程式邏輯, 以更有效率地提取資料。

## <a name="considerations-for-the-sink"></a>接收的考量

### <a name="general"></a>一般

請確定基礎資料存放區不是由執行或對其執行的其他工作負載所淹沒。

針對 Microsoft 資料存放區, 請參閱資料存放區特定的[監視和微調主題](#performance-reference)。 這些主題可協助您了解資料存放區的效能特性，以及如何最小化回應時間和最大化輸送量。

* 如果您將資料從任何資料存放區複製到 Azure SQL 資料倉儲, 請考慮使用 PolyBase 以提升效能。 如需詳細資訊，請參閱[使用 PolyBase 將資料載入 Azure SQL 資料倉儲](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。
* 如果您將資料從 HDFS 複製到 Azure Blob 儲存體或 Azure Data Lake 存放區, 請考慮使用 DistCp 來提升效能。 如需詳細資訊, 請參閱[使用 DistCp 從 HDFS 複製資料](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs)。
* 如果您將資料從 Redshift 複製到 Azure SQL 資料倉儲、Azure Blob 儲存體或 Azure Data Lake 存放區, 請考慮使用 UNLOAD 來提升效能。 如需詳細資訊, 請參閱[使用 UNLOAD 從 Amazon Redshift 複製資料](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)。

### <a name="file-based-data-stores"></a>以檔案為基礎的資料存放區

* **複製行為**：如果您從其他以檔案為基礎的資料存放區複製資料, 複製活動會透過**copyBehavior**屬性提供三個選項。 它會保留階層、扁平化階層或合併檔案。 保留或扁平化階層幾乎不會造成效能負荷，但合併檔案則會導致效能負荷增加。
* **檔案格式和壓縮**：如需可改善效能的其他方法，請參閱[序列化和還原序列化的考量](#considerations-for-serialization-and-deserialization)和[壓縮的考量](#considerations-for-compression)小節。

### <a name="relational-data-stores"></a>關聯式資料存放區

* **複製行為和效能含意**:有不同的方式可將資料寫入 SQL 接收器。 深入瞭解將[資料載入 Azure SQL Database 中的最佳作法](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database)。

* **資料模式和批次大小**：
  * 資料表結構描述對複製輸送量會有影響。 若要複製相同的資料量，較大的資料列大小會有優於較小資料列大小的效能，因為資料庫可以更有效率地認可較少的資料批次。
  * 複製活動會以一系列批次插入資料。 您可以使用 **writeBatchSize** 屬性來設定批次中包含的資料列數。 如果您的資料具有較小的資料列，您可以將 **writeBatchSize** 屬性設為較高的值，以減少批次的額外負荷，並增加輸送量。 如果您的資料的資料列大小較大，在增加 **writeBatchSize**時請多加留意。 較大的值可能會導致複製因資料庫的超載而失敗。

### <a name="nosql-stores"></a>NoSQL 存放區

* 針對 **表格儲存體**：
  * **資料分割**：將資料寫入至交錯的資料分割會大幅降低效能。 依資料分割索引鍵來排序來源資料, 以便將資料有效率地插入至另一個分割區。 或者, 您可以調整邏輯, 將資料寫入至單一分割區。

## <a name="considerations-for-serialization-and-deserialization"></a>序列化和還原序列化的考量

當您的輸入資料集或輸出資料集是檔案時, 就可能發生序列化和還原序列化。 如需複製活動所支援之檔案格式的詳細資訊, 請參閱支援的檔案[和壓縮格式](supported-file-formats-and-compression-codecs.md)。

**複製行為**：

* 在以檔案為基礎的資料存放區之間複製檔案：
  * 當輸入和輸出資料集都有相同或沒有檔案格式設定時, 資料移動服務會執行*二進位複製*, 而不會進行任何序列化或還原序列化。 此情況的輸送量會高於來源和接收檔案格式設定彼此不同的案例。
  * 當輸入和輸出資料集都是文字格式, 而且只有編碼類型不同時, 資料移動服務只會進行編碼轉換。 而不會執行任何序列化和還原序列化，因此和二進位複製相較之下，會產生一些效能負荷。
  * 當輸入和輸出資料集都有不同的檔案格式或不同的設定 (例如分隔符號) 時, 資料移動服務會將來源資料還原序列化以進行資料流程處理、轉換, 然後將它序列化為您所指定的輸出格式。 此作業會導致遠高於其他案例的效能負荷。
* 例如, 當您將檔案複製到不是以檔案為基礎的資料存放區, 或從檔案型存放區複製到關聯式存放區時, 就需要序列化或還原序列化步驟。 此步驟會導致很高的效能負荷。

**檔案格式**：您選擇的檔案格式可能會影響複製效能。 例如，Avro 是一種壓縮二進位格式，可將中繼資料和資料儲存在一起。 它廣泛支援在 Hadoop 生態系統中進行處理和查詢。 Avro 較昂貴, 用於序列化和還原序列化, 這會導致較低的複製輸送量與文字格式比較。 

在選擇整個處理流程中所使用的檔案格式時，應有整體考量。 開始使用:

- 資料的儲存格式、來源資料存放區, 或從外部系統解壓縮。
- 儲存、分析處理和查詢的最佳格式。
- 將資料匯出至資料超市以供報告和視覺效果工具的格式。

有些時候，在考量整體分析程序時，讀取和寫入效能次佳的檔案格式，可能會是較好的選擇。

## <a name="considerations-for-compression"></a>壓縮的考量

當您的輸入或輸出資料集是檔案時, 您可以將複製活動設定為執行壓縮或解壓縮, 因為它會將資料寫入目的地。 當您選擇壓縮時，您必須在輸入/輸出 (I/O) 與 CPU 之間進行取捨。 壓縮資料須耗用額外的計算資源。 但另一方面卻可降低網路 I/O 和儲存體用量。 根據您的資料而定, 您可能會看到整體複製輸送量的提升。

**轉碼器**：每種壓縮轉碼器各有優點。 例如，bzip2 的複製輸送量最低，但您卻可以在使用 bzip2 時獲得最佳的 Hive 查詢效能，因為可將其劃分來進行處理。 Gzip 是最平衡的選項, 而且最常使用。 請選擇最適合您的端對端案例使用的轉碼器。

**層級**：對於每個壓縮轉碼器，您可以從兩個選項中做選擇：最快速的壓縮和最佳化的壓縮。 最快的壓縮選項會盡可能快速壓縮資料, 即使產生的檔案未以最佳方式壓縮也一樣。 最佳化的壓縮選項會花費較長的壓縮時間，並產生最少量的資料。 您可以測試這兩個選項，以查看何者在您的案例中可提供更好的整體效能。

**考量事項**：若要在內部部署存放區與雲端之間複製大量資料，請考慮使用[分段複製](#staged-copy)，並同時啟用壓縮。 當您的公司網路和 Azure 服務的頻寬是限制因素, 而且您希望輸入資料集和輸出資料集都是未壓縮的形式時, 使用過渡儲存體會很有説明。

## <a name="considerations-for-column-mapping"></a>資料行對應的考量

您可以在複製活動中設定**columnMappings**屬性, 以將所有或部分的輸入資料行對應至輸出資料行。 資料移動服務從來源讀取資料之後，必須先對資料執行資料行對應，再將資料寫入至接收。 這項額外處理會降低複製輸送量。

如果您的來源資料存放區是可查詢的 (例如，如果是 SQL Database 或 SQL Server 之類的關聯式存放區，或如果是表格儲存體或 Azure Cosmos DB 之類的 NoSQL 存放區)，請考慮將資料行篩選和重新排序邏輯推送至 **查詢** 屬性，而不使用資料行對應。 如此一來, 當資料移動服務從來源資料存放區讀取資料時, 就會發生投影, 這會更有效率。

深入瞭解[複製活動架構對應](copy-activity-schema-and-type-mapping.md)。

## <a name="other-considerations"></a>其他考量

如果您想要複製的資料大小很大, 您可以調整您的商務邏輯, 以進一步分割資料。 您可以排程複製活動執行得更頻繁, 以減少每個執行之複製活動的資料大小。

請留意需要 Azure Data Factory 以同時連接到相同資料存放區的資料集和複製活動數目。 許多並行複製作業可能會導致資料存放區出現瓶頸，並導致效能降低，複製作業內部重試，在某些情況下甚至導致執行失敗。

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>範例案例：從內部部署 SQL server 複製到 Blob 儲存體

**案例**：建立管線以將資料從內部部署 SQL server 複製到 CSV 格式的 Blob 儲存體。 為了加快複製作業速度，CSV 檔案應該壓縮為 bzip2 格式。

**測試和分析**：複製活動的輸送量小於 2 MBps, 速度比效能基準測試慢很多。

**效能分析和微調**：為了排解效能問題，我們將查看資料的處理及移動方式。

- **讀取資料**：整合執行時間會開啟連接以 SQL Server 並傳送查詢。 SQL Server 會透過內部網路將資料流程傳送至整合執行時間來進行回應。
- **序列化和壓縮資料**︰整合執行時間會將資料流程序列化為 CSV 格式, 並將資料壓縮為 bzip2 資料流程。
- **寫入資料**：整合執行時間會透過網際網路將 bzip2 資料流程上傳至 Blob 儲存體。

如您所見, 資料會以資料流程順序方式處理和移動:SQL Server > LAN > 整合執行階段 > WAN > Blob 儲存體。 整體效能是透過管線的最小輸送量來控管。

![資料流](./media/copy-activity-performance/case-study-pic-1.png)

下列一或多個因素可能會造成效能瓶頸：

* **來源**：SQL Server 本身的輸送量偏低，因為負載過重。
* **自我裝載整合運行**時間:
  * **LAN**：整合執行階段的位置遠離 SQL Server 電腦，且頻寬連線較低。
  * **整合執行階段**：整合執行階段已達到其負載限制，而無法執行下列作業：
    * **序列化**：將資料流序列化為 CSV 格式時，輸送量偏低。
    * **壓縮**：您選擇緩慢的壓縮編解碼器 (例如 bzip2), 也就是 2.8 MBps (含核心 i7)。
  * **WAN**：公司網路與您的 Azure 服務之間的頻寬偏低, 例如 T1 = 1544 kbps;T2 = 6312 kbps。
* **接收**：Blob 儲存體的輸送量偏低。 此案例不太可能, 因為其服務等級協定 (SLA) 保證最低為 60 MBps。

在此情況下，bzip2 資料壓縮可能會拖慢整個管線。 改用 gzip 壓縮轉碼器可能會緩解此瓶頸。

## <a name="references"></a>參考

以下是幾個支援的資料存放區所適用的效能監視及調整參考：

* Azure 儲存體, 其中包括 Blob 儲存體和資料表儲存體:[Azure 儲存體的擴充性目標](../storage/common/storage-scalability-targets.md), 以及[Azure 儲存體效能和擴充性檢查清單](../storage/common/storage-performance-checklist.md)。
* Azure SQL Database：您可以[監視效能](../sql-database/sql-database-single-database-monitor.md), 並檢查資料庫交易單位 (DTU) 百分比。
* Azure SQL 資料倉儲：其功能是以資料倉儲單位 (Dwu) 來測量。 請參閱[管理 Azure SQL 資料倉儲中的計算能力 (總覽)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)。
* Azure Cosmos DB：[Azure Cosmos DB 中的效能層級](../cosmos-db/performance-levels.md)。
* 內部部署 SQL Server：[效能的監視和微調](https://msdn.microsoft.com/library/ms189081.aspx)。
* 內部部署檔案伺服器：[檔案伺服器的效能微調](https://msdn.microsoft.com/library/dn567661.aspx)。

## <a name="next-steps"></a>後續步驟
請參閱其他複製活動文章:

- [複製活動概觀](copy-activity-overview.md)
- [複製活動架構對應](copy-activity-schema-and-type-mapping.md)
- [複製活動容錯](copy-activity-fault-tolerance.md)
