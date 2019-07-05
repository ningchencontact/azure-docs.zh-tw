---
title: Azure Data Factory 中複製活動效能及微調指南 |Microsoft Docs
description: 深入了解 Azure Data Factory 中的資料移動效能的影響，當您使用 「 複製活動的重要因素。
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
ms.openlocfilehash: face3719f32ccb44e7479150e94417496141f90b
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509571"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>複製活動效能及微調指南
> [!div class="op_single_selector" title1="選取您要使用的 Azure Data Factory 的版本："]
> * [第 1 版](v1/data-factory-copy-activity-performance.md)
> * [目前的版本](copy-activity-performance.md)


Azure Data Factory 複製活動會提供載入解決方案的第一級安全、 可靠且高效能資料。 您可以使用它來複製數十 tb 的資料每天跨許多豐富的雲端和內部部署資料存放區。 快速資料載入效能是以確保您可以專注於核心巨量資料問題的關鍵︰ 建置進階的分析解決方案，並從該所有資料獲得深入解析。

Azure 提供一組企業級的資料儲存體和資料倉儲解決方案。 複製活動會提供高度最佳化的資料載入體驗，很容易設定及安裝。 具有單一複製活動，您可以將資料載入：

* Azure SQL 資料倉儲，在 1.2 GBps。
* 在 1.0 GBps 的 azure Blob 儲存體。
* Azure Data Lake Store 中於 1.0 GBps。

本文章說明：

* [效能參考數字](#performance-reference)支援來源和接收資料存放區，可協助您規劃您的專案。
* 功能可以大幅提升複製輸送量在不同的情況下，其中包含[資料整合單位](#data-integration-units)(DIUs)，[平行複製](#parallel-copy)，並[分段複製](#staged-copy)。
* [效能微調方針](#performance-tuning-steps)如何微調效能，可能會影響複製效能的關鍵因素。

> [!NOTE]
> 如果您不熟悉複製活動在一般情況下，請參閱[複製活動概觀](copy-activity-overview.md)閱讀這篇文章之前。
>

## <a name="performance-reference"></a>效能參考

做為參考下, 表顯示的複製輸送量數字以 mbps 為單位指定的來源，並根據內部測試中執行單一複製活動的接收器組。 如需比較，它也會示範的不同設定如何[資料整合單位](#data-integration-units)或是[自我裝載整合執行階段延展性](concepts-integration-runtime.md#self-hosted-integration-runtime)（多個節點） 可以協助複製效能。

![效能矩陣](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> 當複製活動在 Azure 整合執行階段上執行時，最小允許的資料整合單位 （之前稱為資料移動單位 」） 是兩個。 如果未指定，請參閱中所使用的預設資料整合單位[資料整合單位](#data-integration-units)。

**注意事項：**

* 使用下列公式會計算輸送量: [從來源讀取資料的大小] / [複製活動執行持續時間]。
* 藉由測量資料表中的效能參考數字[TPC-H](http://www.tpc.org/tpch/)單一複製活動執行裡的資料集。 檔案型存放區的測試檔案的多個具有 10 GB 檔案大小。
* 在 Azure 資料存放區中，來源和接收位於相同 Azure 區域中。
* 在內部部署與雲端之間的混合式複製的資料存放區，使用下列規格的資料存放區分開的機器上執行每個自我裝載的整合執行階段節點。 當僅執行單一活動，複製作業將只會取用測試電腦一小部分的 CPU、記憶體或網路頻寬。
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
> 您可以使用多個 DIUs，以達到更高的輸送量。 例如，使用 100 DIUs，您可以將資料複製從 Azure Blob 儲存體到 Azure Data Lake Store 在 1.0 GBps。 如需有關這項功能和支援的案例的詳細資訊，請參閱 <<c0> [ 資料整合單位](#data-integration-units)一節。 

## <a name="data-integration-units"></a>資料整合單位

資料整合單元是代表 Azure Data Factory 中的單一單位的能力 （CPU、 記憶體和網路資源配置的組合） 的量值。 資料整合單元只適用於[Azure 整合執行階段](concepts-integration-runtime.md#azure-integration-runtime)，而非[自我裝載整合執行階段](concepts-integration-runtime.md#self-hosted-integration-runtime)。

讓複製活動，執行最少的 DIUs 是兩個。 如果未指定，下表列出用於不同複製案例中的預設 DIU：

| 複製案例 | 服務決定的預設 DIU |
|:--- |:--- |
| 在以檔案為基礎的存放區之間複製資料 | 介於 4 到 32 的檔案大小和數量 |
| 將資料複製到 Azure SQL Database 或 Azure Cosmos DB |介於 4 到 16 根據接收 Azure SQL Database 或 Cosmos DB 的階層 （數字的 Dtu/Ru） |
| 其他複製案例 | 4 |

若要覆寫此預設值，請如下所示指定 **dataIntegrationUnits** 屬性的值。 *允許的值*for **dataIntegrationUnits**屬性是最多 256。 根據您的資料模式，複製作業會在執行階段使用的 *實際 DIU 數目* 等於或小於所設定的值。 如需在為特定複製來源和接收設定更多單位時可能獲得之效能增益水準的相關資訊，請參閱 [效能參考](#performance-reference)。

您可以看到用來執行複製活動輸出中，當您監視活動執行每個複本 DIUs。 如需詳細資訊，請參閱 <<c0> [ 複製活動監視](copy-activity-overview.md#monitoring)。

> [!NOTE]
> 設定 DIUs 大於四個目前時才適用 Azure 儲存體、 Azure Data Lake 儲存體、 Amazon S3、 Google Cloud Storage 中複製多個檔案，雲端 FTP 部署或雲端 SFTP 到任何其他雲端資料存放區。
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

請記住，向您收取基礎上，複製作業的總時間。 總持續時間向您收費的資料移動是 DIUs 的持續時間總和。 若過去某複製作業使用 2 個雲端單位花費 1 小時，現在使用 8 個雲端單位花費 15 分鐘，則兩者的整體費用幾乎相同。

## <a name="parallel-copy"></a>平行複製

您可以使用**parallelCopies**屬性，指出您想要使用的複製活動的平行處理原則。 您可以將此屬性視為複製活動內，可從來源讀取或寫入至您的接收資料存放區，以平行方式的執行緒數目上限。

每個複製活動執行，Azure Data Factory 會決定用以將資料從來源資料存放區目的地資料存放區複製的平行複製數目。 預設它會使用的平行複製數目取決於來源與接收您所使用的類型。

| 複製案例 | 由服務決定的預設平行複製計數 |
| --- | --- |
| 在以檔案為基礎的存放區之間複製資料 |檔案和 DIUs 用來複製資料，兩個雲端資料存放區或自我裝載的整合執行階段電腦的實體組態之間的數字的大小而定。 |
| 將資料從任何來源存放區複製到 Azure 資料表儲存體 |4 |
| 所有其他複製案例 |1 |

> [!TIP]
> 當您複製檔案型存放區之間的資料時，預設行為通常用來提供您最佳的輸送量。 預設行為是自動決定根據您的來源檔案模式。

若要控制裝載資料的電腦上的負載存放區，或是調整複製效能，您可以覆寫預設值，並指定的值**parallelCopies**屬性。 值必須是大於或等於 1 的整數。 在執行階段，為了達到最佳效能，「 複製活動會使用值小於或等於您所設定的值，這個值。

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

* 當您將檔案型存放區之間的資料複製**parallelCopies**決定檔案層級的平行處理原則。 單一檔案中進行區塊化底下會自動發生且以透明的方式。 它設計成使用最適合的區塊大小要平行載入資料的指定的來源資料存放區類型和正交於**parallelCopies**。 資料移動服務在執行階段用於複製作業的實際平行複製數目不會超過您擁有的檔案數目。 如果複製行為**mergeFile**，「 複製活動無法利用檔案層級平行處理原則。
* 當您從存放區不是以檔案為基礎 （除了做為啟用資料分割的來源 Oracle 資料庫），會在複製資料時是以檔案為基礎的存放區，資料移動服務會忽略**parallelCopies**屬性。 即使已指定平行處理原則，也不會套用於此案例。
* **ParallelCopies**屬性正交於**dataIntegrationUnits**。 前者會跨所有資料整合單位計算。
* 當您指定的值**parallelCopies**屬性，請考慮在您的來源上的負載增加，並接收資料存放區。 也請考慮負載增加至自我裝載的整合執行階段，當複製活動會由所授權的比方說，針對混合式複製。 此負載增加會特別是當您有多個活動或同時執行的相同對相同的資料存放區執行的活動。 如果您注意到資料存放區或自我裝載的整合執行階段應付負載，減少**parallelCopies**值以減輕負載。

## <a name="staged-copy"></a>分段複製

從來源資料存放區將資料複製到接收資料存放區時，您可以選擇使用 Blob 儲存體做為過渡暫存存放區。 暫存在下列情況下特別有用︰

- **您想要將資料從各種資料存放區內嵌到透過 PolyBase 的 SQL 資料倉儲。** SQL 資料倉儲使用 PolyBase 做為高輸送量機制，將大量資料載入 SQL 資料倉儲。 來源資料必須位於 Blob 儲存體或 Azure Data Lake Store，並符合額外的條件。 當您從 Blob 儲存體或 Azure Data Lake Store 以外的資料存放區載入資料時，您可以啟用透過過渡暫存 Blob 儲存體的資料複製。 在此情況下，Azure Data Factory 會執行所需的資料轉換，以確保它符合 PolyBase 的需求。 然後，它會使用 PolyBase 將資料有效率地載入到 SQL 資料倉儲。 如需詳細資訊，請參閱[使用 PolyBase 將資料載入 Azure SQL 資料倉儲](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。
- **有時候它需要一段時間執行混合式資料移動 （也就是從內部部署資料存放區複製到雲端資料存放區） 透過慢速網路連線。** 若要改善效能，您可以使用分段的複製壓縮內部資料，使其接受較少的時間，將資料移至暫存資料存放區，在雲端中。 然後您可以在載入至目的地資料存放區之前解壓縮暫存存放區中的資料。
- **您不想要在防火牆中開啟連接埠 80 和 443 以外的連接埠，因為公司 IT 原則。** 例如，從內部部署資料存放區將資料複製到 Azure SQL Database 接收或 Azure SQL 資料倉儲接收時，您必須針對 Windows 防火牆和公司防火牆啟用連接埠 1433 上的輸出 TCP 通訊。 在此案例中，分段的複製可利用自我裝載的整合執行階段先將資料複製到 Blob 儲存體暫存執行個體，透過 HTTP 或 HTTPS 連接埠 443 上。 然後它可以將資料載入 SQL Database 或 SQL 資料倉儲從 Blob 儲存體暫存。 在此流程中，您不需要啟用連接埠 1433。

### <a name="how-staged-copy-works"></a>分段複製的運作方式

當您啟用暫存功能時，會先從來源資料存放區複製資料到暫存 Blob 儲存體 (自備)。 接著再從暫存資料存放區複製資料到接收資料存放區。 Azure Data Factory 會自動管理您的兩階段流程。 Azure Data Factory 也會清除暫存儲存體中的暫存資料的資料移動完成之後。

![分段複製](media/copy-activity-performance/staged-copy.png)

當您使用暫存存放區中啟動資料移動時，您可以指定您想要壓縮之前將資料從來源資料移動的資料儲存到過渡或暫存資料存放區，然後解壓縮後才能將資料從過渡或暫存的資料移動要接收資料存放區的儲存區。

目前，您無法複製透過不同的自我裝載 Ir，不使用，也不分段複製連線的兩個資料存放區之間的資料。 這類案例中，您可以設定兩個鏈結明確的複製活動，將複製從來源到預備環境，然後再從暫存到接收。

### <a name="configuration"></a>組態

設定**enableStaging**設定將複製活動中，指定是否要讓暫置至 Blob 儲存體之前載入目的地資料存放區的資料。 當您設定**enableStaging**到`TRUE`，指定下表所列的其他屬性。 您也需要建立 Azure 儲存體或儲存體共用存取簽章連結服務以供預備環境，如果您沒有帳戶。

| 屬性 | 說明 | 預設值 | 必要項 |
| --- | --- | --- | --- |
| enableStaging |指定您是否要透過過渡暫存存放區複製資料。 |False |否 |
| linkedServiceName |指定 [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) 連結服務的名稱，以代表您用來做為過渡暫存存放區的儲存體執行個體。 <br/><br/> 您無法使用共用的存取簽章的儲存體將資料載入 SQL 資料倉儲透過 PolyBase。 您可以將它用於其他所有案例。 |N/A |是，當 **enableStaging** 設為 TRUE |
| path |指定要包含分段資料的 Blob 儲存體路徑。 如果您沒有提供路徑，服務會建立容器來儲存暫存資料。 <br/><br/> 只有在使用具有共用存取簽章的儲存體時，或需要讓暫存資料位於特定位置時，才指定路徑。 |N/A |否 |
| enableCompression |指定複製到目的地之前，是否應該壓縮資料。 此設定可減少傳輸的資料量。 |False |否 |

>[!NOTE]
> 如果您使用分段的複製壓縮已啟用，服務主體，或不支援 MSI 驗證的暫存已連結的 blob 服務。

以下是範例定義的 「 複製活動 」 與上表所述的屬性：

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

向您收取根據兩個步驟： 複製持續時間和複製類型。

* 當您使用暫存雲端複製，正在將資料複製期間從雲端資料存放區，另一個雲端資料存放區，同時由 Azure 整合執行階段所授權的階段，向您收取 [sum 的步驟 1 和步驟 2 的複製持續時間] x [雲端複製單位價格]。
* 當您使用接移期間混合式複製，這從內部部署資料存放區複製資料到雲端資料存放區，由自我裝載的整合執行階段所授權的一個階段，您需支付 [混合式複製持續時間] x [混合式複製單位價格] + [雲端複製持續時間]x [雲端複製單位價格]。

## <a name="performance-tuning-steps"></a>效能微調步驟

執行這些步驟來微調您的 Azure Data Factory 服務，使用複製活動的效能。

1. **建立基準線。** 在開發階段，請使用複製活動，根據代表性資料樣本中測試您的管線。 收集執行詳細資料和之後的效能特性[複製活動監視](copy-activity-overview.md#monitoring)。

2. **診斷及最佳化效能。** 如果您觀察到的效能不符預期，找出效能瓶頸。 然後將效能最佳化，以消除或減少瓶頸的影響。

    在某些情況下，當您執行 「 複製活動 」 在 Azure Data Factory，您看到 「 效能調整秘訣 」 的上方[複製活動監視頁面](copy-activity-overview.md#monitor-visually)，如下列範例所示。 訊息會告訴您所指定的複本執行找出瓶頸。 它也會引導您在將變更為提升複製輸送量。 效能微調秘訣目前提供的建議像是：

    - 當您將資料複製到 Azure SQL 資料倉儲時，請使用 PolyBase。
    - 在資料存放區端上的資源時瓶頸，請增加 Azure Cosmos DB 要求單位或 Azure SQL Database Dtu （資料庫輸送量單位）。
    - 移除不必要的分段的複製。

    效能調整規則也會逐漸變豐富。

    **範例：複製到 Azure SQL Database 效能調整秘訣**

    在此範例中，在執行時，複製 Azure Data Factory 通知的接收 Azure SQL Database 達到高 DTU 使用率，這會減慢 「 寫入 」 操作。 建議是增加更多 Dtu 與 Azure SQL Database 層。 

    ![複製監視和效能微調祕訣](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    此外，以下是一些常見的考量。 效能診斷的完整說明不在本文的討論之列。

   * 效能功能︰
     * [平行複製](#parallel-copy)
     * [資料整合單位](#data-integration-units)
     * [分段複製](#staged-copy)
     * [自我裝載的整合執行階段延展性](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [自我裝載整合執行階段](#considerations-for-self-hosted-integration-runtime)
   * [來源](#considerations-for-the-source)
   * [接收](#considerations-for-the-sink)
   * [序列化和還原序列化](#considerations-for-serialization-and-deserialization)
   * [壓縮](#considerations-for-compression)
   * [資料行對應](#considerations-for-column-mapping)
   * [其他考量](#other-considerations)

3. **展開至整個資料集的 [設定]。** 當您滿意執行結果及效能時，您可以展開定義和管線，以涵蓋整個資料集。

## <a name="considerations-for-self-hosted-integration-runtime"></a>自我裝載的整合執行階段的考量

如果您的複製活動在自我裝載的整合執行階段上執行，請注意下列各項：

**設定**：我們建議您使用專用的電腦來主控件整合執行階段。 請參閱[考量使用自我裝載整合執行階段](concepts-integration-runtime.md)。

**相應放大**：一個或多個節點的單一邏輯自我裝載的整合執行階段可以在同一時間同時提供多個複製活動執行。 如果您對於混合式資料移動大量的並行複製活動執行或的大量複製的資料有很大的需求，請考慮[相應放大自我裝載的整合執行階段](create-self-hosted-integration-runtime.md#high-availability-and-scalability)佈建更多的資源賦予複製能力。

## <a name="considerations-for-the-source"></a>來源的考量

### <a name="general"></a>一般

請確定基礎資料存放區不湧入，或對它執行其他工作負載。

針對 Microsoft 資料存放區，請參閱[監視和微調主題](#performance-reference)特有資料存放區。 這些主題可協助您了解資料存放區的效能特性，以及如何最小化回應時間和最大化輸送量。

* 如果您將資料從 Blob 儲存體複製到 SQL 資料倉儲時，請考慮使用 PolyBase 來提升效能。 如需詳細資訊，請參閱[使用 PolyBase 將資料載入 Azure SQL 資料倉儲](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。
* 如果您將資料從 HDFS 複製到 Azure Blob 儲存體或 Azure Data Lake Store 時，請考慮使用 DistCp，大幅提升效能。 如需詳細資訊，請參閱 <<c0> [ 使用 DistCp 從 HDFS 複製資料](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs)。
* 如果您將資料從 Redshift 複製到 Azure SQL 資料倉儲時，Azure BLob 儲存體或 Azure Data Lake Store，請考慮使用 UNLOAD，大幅提升效能。 如需詳細資訊，請參閱 <<c0> [ 使用 UNLOAD 從 Amazon Redshift 複製資料](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)。

### <a name="file-based-data-stores"></a>以檔案為基礎的資料存放區

* **平均檔案大小和檔案計數**：複製活動會一次傳輸一個檔案的資料。 在要移動的資料量相同的前提下，如果資料包含許多個小型檔案而非少數幾個大型檔案，其整體輸送量會較低，因為每個檔案都需要啟動程序階段。 可能的話，請將小型檔案合併成較大的檔案，以取得更高的輸送量。
* **檔案格式和壓縮**：如需可改善效能的其他方法，請參閱[序列化和還原序列化的考量](#considerations-for-serialization-and-deserialization)和[壓縮的考量](#considerations-for-compression)小節。

### <a name="relational-data-stores"></a>關聯式資料存放區

* **資料模式**：資料表結構描述對複製輸送量會有影響。 較大型的資料列大小可讓您更佳的效能比小型的資料列大小，若要複製相同的資料量。 這是因為資料庫可以更有效率地擷取包含較少資料列的較少資料批次。
* **查詢或預存程序**：最佳化查詢或預存程序的項目，您在複製活動來源，以更有效率地擷取資料中指定的邏輯。

## <a name="considerations-for-the-sink"></a>接收的考量

### <a name="general"></a>一般

請確定基礎資料存放區不湧入，或對它執行其他工作負載。

針對 Microsoft 資料存放區，請參閱[監視和微調主題](#performance-reference)特有資料存放區。 這些主題可協助您了解資料存放區的效能特性，以及如何最小化回應時間和最大化輸送量。

* 如果您將資料從任何資料存放區複製到 Azure SQL 資料倉儲時，請考慮使用 PolyBase 來提升效能。 如需詳細資訊，請參閱[使用 PolyBase 將資料載入 Azure SQL 資料倉儲](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。
* 如果您將資料從 HDFS 複製到 Azure Blob 儲存體或 Azure Data Lake Store 時，請考慮使用 DistCp，大幅提升效能。 如需詳細資訊，請參閱 <<c0> [ 使用 DistCp 從 HDFS 複製資料](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs)。
* 如果您將資料從 Redshift 複製到 Azure SQL 資料倉儲時，Azure Blob 儲存體或 Azure Data Lake Store，請考慮使用 UNLOAD，大幅提升效能。 如需詳細資訊，請參閱 <<c0> [ 使用 UNLOAD 從 Amazon Redshift 複製資料](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)。

### <a name="file-based-data-stores"></a>以檔案為基礎的資料存放區

* **複製行為**：如果您從不同的檔案型資料存放區複製資料，「 複製活動有三個選項，透過**copyBehavior**屬性。 它會保留階層、扁平化階層或合併檔案。 保留或扁平化階層幾乎不會造成效能負荷，但合併檔案則會導致效能負荷增加。
* **檔案格式和壓縮**：如需可改善效能的其他方法，請參閱[序列化和還原序列化的考量](#considerations-for-serialization-and-deserialization)和[壓縮的考量](#considerations-for-compression)小節。

### <a name="relational-data-stores"></a>關聯式資料存放區

* **複製行為與效能，還有以隱含方式**:有不同的方式，將資料寫入 SQL 接收器。 進一步了解[將資料載入 Azure SQL Database 的最佳做法](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database)。

* **資料模式和批次大小**：
  * 資料表結構描述對複製輸送量會有影響。 若要複製相同的資料量，較大的資料列大小會有優於較小資料列大小的效能，因為資料庫可以更有效率地認可較少的資料批次。
  * 複製活動將資料插入一系列的批次。 您可以使用 **writeBatchSize** 屬性來設定批次中包含的資料列數。 如果您的資料具有較小的資料列，您可以將 **writeBatchSize** 屬性設為較高的值，以減少批次的額外負荷，並增加輸送量。 如果您的資料的資料列大小較大，在增加 **writeBatchSize**時請多加留意。 較大的值可能會導致複製因資料庫的超載而失敗。

### <a name="nosql-stores"></a>NoSQL 存放區

* 針對 **表格儲存體**：
  * **資料分割**：將資料寫入至交錯的資料分割會大幅降低效能。 以便將資料插入有效率地為一個磁碟分割之後另一個資料分割索引鍵排序您的來源資料。 或者，您可以調整以將資料寫入至單一資料分割邏輯。

## <a name="considerations-for-serialization-and-deserialization"></a>序列化和還原序列化的考量

您的輸入資料集或輸出資料集是檔案時，可能會發生序列化和還原序列化。 如需有關複製活動所支援的檔案格式的詳細資訊，請參閱[支援的檔案和壓縮格式](supported-file-formats-and-compression-codecs.md)。

**複製行為**：

* 在以檔案為基礎的資料存放區之間複製檔案：
  * 當輸入和輸出資料集這兩個具有相同，或者沒有檔案格式設定，資料移動服務執行*二進位複製*不含任何序列化或還原序列化。 此情況的輸送量會高於來源和接收檔案格式設定彼此不同的案例。
  * 如果輸入和輸出資料集這兩個是以文字格式，只有編碼類型不同，則資料移動服務只會執行編碼轉換。 而不會執行任何序列化和還原序列化，因此和二進位複製相較之下，會產生一些效能負荷。
  * 當輸入和輸出資料集這兩個有不同的檔案格式或不同的組態，例如分隔符號、 資料移動服務會還原序列化資料流，轉換，然後將它序列化為您所指定的輸出格式的來源資料。 此作業會導致遠高於其他案例的效能負荷。
* 不是檔案型，例如，從關聯式存放區中，檔案型存放區的資料存放區來回複製檔案時序列化或還原序列化步驟是必要的。 此步驟會導致很高的效能負荷。

**檔案格式**：您選擇的檔案格式可能會影響複製效能。 例如，Avro 是一種壓縮二進位格式，可將中繼資料和資料儲存在一起。 它廣泛支援在 Hadoop 生態系統中進行處理和查詢。 Avro 是昂貴的序列化和還原序列化時，會導致較低的複製輸送量，相較於文字格式。 

在選擇整個處理流程中所使用的檔案格式時，應有整體考量。 開始：

- 何種形式的資料會儲存來源資料存放區中，或從外部系統擷取。
- 儲存體、 分析的處理和查詢的最佳格式。
- 以何種格式的資料都應該匯出到資料超市中的報告和視覺效果工具。

有些時候，在考量整體分析程序時，讀取和寫入效能次佳的檔案格式，可能會是較好的選擇。

## <a name="considerations-for-compression"></a>壓縮的考量

當您輸入或輸出資料集檔案時，您可以設定執行壓縮或解壓縮，因為它將資料寫入目的地的 「 複製活動。 當您選擇壓縮時，您必須在輸入/輸出 (I/O) 與 CPU 之間進行取捨。 壓縮資料須耗用額外的計算資源。 但另一方面卻可降低網路 I/O 和儲存體用量。 根據您的資料，您可能會看到整體複製輸送量有所提升。

**轉碼器**：每種壓縮轉碼器各有優點。 例如，bzip2 的複製輸送量最低，但您卻可以在使用 bzip2 時獲得最佳的 Hive 查詢效能，因為可將其劃分來進行處理。 Gzip 是最均衡的選項，它具有最常使用的。 請選擇最適合您的端對端案例使用的轉碼器。

**層級**：對於每個壓縮轉碼器，您可以從兩個選項中做選擇：最快速的壓縮和最佳化的壓縮。 儘快，即使未以最佳方式壓縮所產生的檔案時，最快速的壓縮的選項將壓縮的資料。 最佳化的壓縮選項會花費較長的壓縮時間，並產生最少量的資料。 您可以測試這兩個選項，以查看何者在您的案例中可提供更好的整體效能。

**考量事項**：若要在內部部署存放區與雲端之間複製大量資料，請考慮使用[分段複製](#staged-copy)，並同時啟用壓縮。 您的公司網路與 Azure 服務的頻寬是限制因素，而且您想要的輸入資料集和輸出資料集這兩個是未壓縮的形式時，使用過渡儲存體會很有用的。

## <a name="considerations-for-column-mapping"></a>資料行對應的考量

您可以設定**columnMappings**所有對應或輸出資料行之輸入資料行的子集複製活動中的屬性。 資料移動服務從來源讀取資料之後，必須先對資料執行資料行對應，再將資料寫入至接收。 這項額外處理會降低複製輸送量。

如果您的來源資料存放區是可查詢的 (例如，如果是 SQL Database 或 SQL Server 之類的關聯式存放區，或如果是表格儲存體或 Azure Cosmos DB 之類的 NoSQL 存放區)，請考慮將資料行篩選和重新排序邏輯推送至 **查詢** 屬性，而不使用資料行對應。 如此一來，資料移動服務會讀取來源資料存放區會更有效率的資料時，就會發生投射。

進一步了解[複製活動結構描述對應](copy-activity-schema-and-type-mapping.md)。

## <a name="other-considerations"></a>其他考量

如果您想要複製的資料大小很大，您可以調整您的商務邏輯，以進一步分割資料。 您可以排程複製活動 」 以縮減資料大小，每個複製活動執行更頻繁地執行。

留意資料集的數目，並複製需要 Azure Data Factory 同時連線到相同的資料存放區的活動。 許多並行複製作業可能會導致資料存放區出現瓶頸，並導致效能降低，複製作業內部重試，在某些情況下甚至導致執行失敗。

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>範例案例：從內部部署 SQL server 複製到 Blob 儲存體

**案例**：管線是建置來將資料從內部部署 SQL server 複製到 Blob 儲存體，以 CSV 格式。 為了加快複製作業速度，CSV 檔案應該壓縮為 bzip2 格式。

**測試和分析**：將複製活動的輸送量是小於 2 MBps，遠低於效能基準測試。

**效能分析和微調**：為了排解效能問題，我們將查看資料的處理及移動方式。

- **讀取資料**：整合執行階段會開啟 SQL Server 的連接，並傳送查詢。 SQL Server 回應資料流傳送至整合執行階段，透過內部網路。
- **序列化和壓縮資料**︰整合執行階段序列化為 CSV 格式的資料流，並將壓縮為 bzip2 資料流的資料。
- **寫入資料**：整合執行階段會將 bzip2 資料流上傳至 Blob 儲存體，透過網際網路。

如您所見，資料處理和串流序列的方式移動：SQL Server > LAN > 整合執行階段 > WAN > Blob 儲存體。 整體效能受限於最小輸送量管線。

![資料流](./media/copy-activity-performance/case-study-pic-1.png)

下列一或多個因素可能會造成效能瓶頸：

* **來源**：SQL Server 本身的輸送量偏低，因為負載過重。
* **自我裝載整合執行階段**:
  * **LAN**：整合執行階段的位置遠離 SQL Server 電腦，且頻寬連線較低。
  * **整合執行階段**：整合執行階段已達到其負載限制，而無法執行下列作業：
    * **序列化**：將資料流序列化為 CSV 格式時，輸送量偏低。
    * **壓縮**：您選擇了緩慢的壓縮轉碼器，例如 bzip2，這是與 Core i7，速度 2.8 MBps。
  * **WAN**：公司網路與 Azure 服務之間的頻寬不足，例如，T1 = 1,544 kbps;T2 = 6,312 kbps。
* **接收**：Blob 儲存體的輸送量偏低。 此案例中是不太可能的因為其服務等級協定 (SLA) 保證至少有 60 MBps。

在此情況下，bzip2 資料壓縮可能會拖慢整個管線。 改用 gzip 壓縮轉碼器可能會緩解此瓶頸。

## <a name="references"></a>參考

以下是幾個支援的資料存放區所適用的效能監視及調整參考：

* Azure 儲存體，包括 Blob 儲存體和表格儲存體：[Azure 儲存體的延展性目標](../storage/common/storage-scalability-targets.md)並[Azure 儲存體效能與延展性檢查清單](../storage/common/storage-performance-checklist.md)。
* Azure SQL Database：您可以[監視效能](../sql-database/sql-database-single-database-monitor.md)並檢查資料庫交易單位 (DTU) 百分比。
* Azure SQL 資料倉儲：其功能是以資料倉儲單位 (Dwu) 測量。 請參閱[管理的計算能力，在 Azure SQL 資料倉儲 （概觀）](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)。
* Azure Cosmos DB：[Azure Cosmos DB 中的效能層級](../cosmos-db/performance-levels.md)。
* 內部部署 SQL Server：[效能監視與微調](https://msdn.microsoft.com/library/ms189081.aspx)。
* 內部部署檔案伺服器：[檔案伺服器的效能微調](https://msdn.microsoft.com/library/dn567661.aspx)。

## <a name="next-steps"></a>後續步驟
請參閱其他複製活動文章：

- [複製活動概觀](copy-activity-overview.md)
- [複製活動結構描述對應](copy-activity-schema-and-type-mapping.md)
- [複製活動容錯](copy-activity-fault-tolerance.md)
