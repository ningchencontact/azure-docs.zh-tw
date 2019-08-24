---
title: Azure Data Factory 中的複製活動效能和擴充性指南 |Microsoft Docs
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
ms.date: 08/16/2019
ms.author: jingwang
ms.openlocfilehash: 05ecfdc4f082aaa44fe54e6b807a1c5faf84eb8d
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996451"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>複製活動效能和擴充性指南
> [!div class="op_single_selector" title1="選取您要使用的 Azure Data Factory 版本:"]
> * [第 1 版](v1/data-factory-copy-activity-performance.md)
> * [目前的版本](copy-activity-performance.md)

無論您想要從 data lake 或企業資料倉儲 (EDW) 執行大規模資料移轉至 Azure, 或想要將大規模資料從不同來源內嵌到 Azure 以進行 big data 分析, 請務必達到最佳效能, 並延展性.  Azure Data Factory 提供高效能、彈性且符合成本效益的機制來內嵌資料, 讓資料工程師更適合想要建立高效能且可調整規模的資料內嵌管線。

閱讀本文後，您將能夠回答下列問題：

- 我可以針對資料移轉和資料內嵌案例使用 ADF 複製活動, 達到何種層級的效能和擴充性？

- 為了微調 ADF 複製活動的效能, 我應該採取哪些步驟？
- 我可以使用哪些 ADF 效能優化旋鈕, 將單一複製活動執行的效能優化？
- 在優化複製效能時, ADF 以外的其他因素會被考慮嗎？

> [!NOTE]
> 如果您一般不熟悉複製活動, 請參閱[複製活動總覽](copy-activity-overview.md), 再閱讀本文。

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>使用 ADF 可達到複製效能和擴充性

ADF 提供無伺服器架構, 允許不同層級的平行處理原則, 讓開發人員能夠建立管線來充分利用您的網路頻寬, 以及儲存 IOPS 和頻寬, 以最大化您環境的資料移動輸送量。  這表示您可以藉由測量來來源資料存放區、目的地資料存放區, 以及來源與目的地之間的網路頻寬所提供的最小輸送量來估計您可達到的輸送量。  下表根據您環境的資料大小和頻寬限制來計算複製持續時間。 

| 資料大小/ <br/> 頻寬 | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2.7 分鐘    | 1.4 分鐘   | 0.3 分鐘   | 0.1 分鐘  | 0.03 分鐘 | 0.01 分鐘 | 0.0 分鐘   |
| **10 GB**                   | 27.3 分鐘   | 13.7 分鐘  | 2.7 分鐘   | 1.3 分鐘  | 0.3 分鐘  | 0.1 分鐘  | 0.03 分鐘  |
| **100 GB**                  | 4.6 小時    | 2.3 小時   | 0.5 小時   | 0.2 小時  | 0.05 小時 | 0.02 小時 | 0.0 小時   |
| **1 TB**                    | 46.6 小時   | 23.3 小時  | 4.7 小時   | 2.3 小時  | 0.5 小時  | 0.2 小時  | 0.05 小時  |
| **10 TB**                   | 19.4 天  | 9.7 天  | 1.9 天  | 0.9 天 | 0.2 天 | 0.1 天 | 0.02 天 |
| **100 TB**                  | 194.2 天 | 97.1 天 | 19.4 天 | 9.7 天 | 1.9 天 | 1天   | 0.2 天  |
| **1 PB**                    | 64.7 月    | 32.4 月   | 6.5 月    | 3.2 月   | 0.6 月   | 0.3 月   | 0.06 月   |
| **10 PB**                   | 647.3 月   | 323.6 月  | 64.7 月   | 31.6 月  | 6.5 月   | 3.2 月   | 0.6 月    |

ADF 複製可在不同層級進行調整:

![ADF 複製的縮放方式](media/copy-activity-performance/adf-copy-scalability.png)

- ADF 控制流程可以平行啟動多個複製活動, 例如,[針對每個迴圈](control-flow-for-each-activity.md)使用。
- 單一複製活動可以利用可調整的計算資源: 使用 Azure Integration Runtime 時, 您可以用無伺服器的方式為每個複製活動指定[最多 256 diu](#data-integration-units) ;使用自我裝載的 Integration Runtime 時, 您可以手動相應增加機器或相應放大至多部電腦 ([最多4個節點](create-self-hosted-integration-runtime.md#high-availability-and-scalability)), 而單一複製活動會在所有節點上分割其檔案集。
- 單一複製活動會[以平行方式](#parallel-copy)使用多個執行緒讀取和寫入資料存放區。

## <a name="performance-tuning-steps"></a>效能微調步驟

採取下列步驟, 使用複製活動來微調 Azure Data Factory 服務的效能。

1. **挑選測試資料集並建立基準。** 在開發階段, 使用複製活動針對代表性資料範例來測試您的管線。 您選擇的資料集應該代表您的一般資料模式 (資料夾結構、檔案模式、資料結構描述等), 而且夠大, 足以評估複製的效能, 例如, 需要10分鐘或更久的時間才能完成複製活動。 在[複製活動監視](copy-activity-overview.md#monitoring)之後收集執行詳細資料和效能特性。

2. **如何將單一複製活動的效能最大化**:

   一開始, 我們建議您先使用單一複製活動來最大化效能。

   **如果要在 Azure Integration Runtime 上執行複製活動:**

   從[[資料整合單位 (DIU)](#data-integration-units) ] 和 [[平行複製](#parallel-copy)設定] 的預設值開始。  執行效能測試回合, 並記下所達到的效能, 以及用於 Diu 和平行複製的實際值。  請參閱[複製活動監視](copy-activity-overview.md#monitoring), 以瞭解如何收集所使用的執行結果和效能設定。

   現在會執行額外的效能測試回合, 每次加倍 DIU 設定的值。  或者, 如果您認為使用預設設定所達到的效能低於預期, 您可以在後續的測試回合中更大幅地增加 DIU 設定。

   當您增加 DIU 設定時, 複製活動應該會以最線性的方式進行調整。  如果 DIU 設定加倍, 您就不會看到輸送量加倍, 因此可能會發生兩件事:

   - 您所執行的特定複製模式不會因新增更多 Diu 而受益。  即使您已指定較大的 DIU 值, 實際使用的 DIU 還是會維持不變, 因此您會取得與之前相同的輸送量。  如果是這種情況, 您可以同時執行多個複本, 同時參考步驟 3, 以最大化匯總輸送量。
   - 藉由新增更多 Diu (更有動力), 進而驅動資料提取、傳輸和載入的速率偏高, 來源資料存放區、其間的網路或目的地資料存放區已達到其瓶頸, 而且可能正在進行節流。  如果是這種情況, 請嘗試聯絡您的資料存放區系統管理員或網路系統管理員以提高上限, 或減少 DIU 設定, 直到節流停止發生為止。

   **如果要在自我裝載的 Integration Runtime 上執行複製活動:**

   我們建議您將專用的獨立電腦與裝載資料存放區的伺服器搭配使用, 以裝載整合執行時間。

   從[平行複製](#parallel-copy)設定的預設值開始, 並使用自我裝載 IR 的單一節點。  執行效能測試回合, 並記下所達到的效能。

   如果您想要達到更高的輸送量, 您可以擴充或相應放大自我裝載的 IR:

   - 如果自我裝載 IR 節點上的 CPU 和可用記憶體未完全使用, 但並行作業的執行達到限制, 您應該增加可在節點上執行的並行作業數目來相應增加。  如需相關指示, 請參閱[這裡](create-self-hosted-integration-runtime.md#scale-up)。
   - 另一方面, 如果自我裝載 IR 節點上的 CPU 很高, 或可用記憶體不足, 您可以加入新的節點, 以協助相應放大多個節點上的負載。  如需相關指示, 請參閱[這裡](create-self-hosted-integration-runtime.md#high-availability-and-scalability)。

   當您相應增加或相應放大自我裝載 IR 的容量時, 請重複執行效能測試, 以查看是否有更好的輸送量。  如果輸送量停止改善, 很可能是來源資料存放區、網路介於其間, 或目的地資料存放區已達到其瓶頸, 因而開始受到節流。 如果是這種情況, 請嘗試聯絡您的資料存放區系統管理員或網路系統管理員以提高上限, 或回到您先前的自我裝載 IR 調整設定。 

3. **如何藉由同時執行多個複本, 將匯總輸送量最大化:**

   現在您已將單一複製活動的效能最大化, 如果您尚未達到環境的輸送量上限 (網路、來源資料存放區和目的地資料存放區), 您可以使用 ADF 平行執行多個複製活動控制流程結構, 例如[For each 迴圈](control-flow-for-each-activity.md)。

4. **效能微調秘訣和優化功能。** 在某些情況下, 當您在 Azure Data Factory 中執行複製活動時, 您會在[複製活動監視](copy-activity-overview.md#monitor-visually)之上看到「效能調整秘訣」訊息, 如下列範例所示。 訊息會告訴您針對指定的複本執行所識別的瓶頸。 它也會引導您進行要變更的內容, 以提高複製輸送量。 效能微調秘訣目前提供的建議如下:

   - 當您將資料複製到 Azure SQL 資料倉儲時, 請使用 PolyBase。
   - 當資料存放區端的資源為瓶頸時, 增加 Azure Cosmos DB 要求單位或 Azure SQL Database Dtu (資料庫輸送量單位)。
   - 移除不必要的分段複製。

   效能調整規則也會逐漸變豐富。

   **範例：使用效能微調秘訣複製到 Azure SQL Database**

   在此範例中, 在複製執行期間, Azure Data Factory 通知接收器 Azure SQL Database 到達高 DTU 使用率, 這會使寫入作業變慢。 建議是以更多 Dtu 來增加 Azure SQL Database 層。 

   ![複製監視和效能微調祕訣](media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

   此外, 以下是一些您應該注意的效能優化功能:

   - [平行複製](#parallel-copy)
   - [資料整合單位](#data-integration-units)
   - [分段複製](#staged-copy)
   - [自我裝載整合執行時間的擴充性](concepts-integration-runtime.md#self-hosted-integration-runtime)

5. **將設定擴充到整個資料集。** 當您對執行結果及效能感到滿意時, 可以展開定義和管線來涵蓋整個資料集。

## <a name="copy-performance-optimization-features"></a>複製效能優化功能

Azure Data Factory 提供下列效能優化功能:

- [平行複製](#parallel-copy)
- [資料整合單位](#data-integration-units)
- [分段複製](#staged-copy)

### <a name="data-integration-units"></a>資料整合單位

資料整合單位是一種量值, 代表 Azure Data Factory 中單一單位的能力 (CPU、記憶體和網路資源分配的組合)。 資料整合單位僅適用于[Azure 整合運行](concepts-integration-runtime.md#azure-integration-runtime)時間, 但不會套用至[自我裝載整合運行](concepts-integration-runtime.md#self-hosted-integration-runtime)時間。

您將以 **# 個已使用的\* diu 複製\*持續時間單位價格/DIU 小時**計費。 請在[這裡](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)查看目前的價格。 每個訂用帳戶類型可能會套用當地貨幣和個別折扣。

允許複製活動執行的 Diu, 是**介於2到256之間**。 如果未指定, 或您在 UI 上選擇 [自動], Data Factory 根據您的來源接收組和資料模式, 動態套用最佳 DIU 設定。 下表列出不同複製案例中使用的預設 Diu:

| 複製案例 | 服務決定的預設 DIU |
|:--- |:--- |
| 在以檔案為基礎的存放區之間複製資料 | 根據檔案的數目和大小, 介於4到32之間 |
| 將資料複製到 Azure SQL Database 或 Azure Cosmos DB |依據接收器 Azure SQL Database 或 Cosmos DB 的層 (Dtu 數/ru 數目), 介於4到16之間 |
| 所有其他複製案例 | 4 |

若要覆寫此預設值，請如下所示指定 **dataIntegrationUnits** 屬性的值。 根據您的資料模式，複製作業會在執行階段使用的 *實際 DIU 數目* 等於或小於所設定的值。

當您監視活動執行時, 您可以在複製活動輸出中查看每個複製執行所使用的 Diu。 如需詳細資訊, 請參閱[複製活動監視](copy-activity-overview.md#monitoring)。

> [!NOTE]
> 目前只有當您將多個檔案從 Azure 儲存體、Azure Data Lake Storage、Amazon S3、Google Cloud Storage、雲端 FTP 或雲端 SFTP 複製到任何其他雲端資料存放區時, 才會套用超過四個 Diu 的設定。

**範例:**

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

### <a name="parallel-copy"></a>平行複製

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

**注意事項：**

- 當您在以檔案為基礎的存放區之間複製資料時, **parallelCopies**會決定檔案層級的平行處理原則。 單一檔案內的區塊化會自動且透明地出現在下方。 其設計目的是針對指定的來源資料存放區類型, 使用最適合的區塊大小, 以平行方式載入資料, 並與**parallelCopies**。 資料移動服務在執行階段用於複製作業的實際平行複製數目不會超過您擁有的檔案數目。 如果複製行為是**mergeFile**, 複製活動就無法利用檔案層級的平行處理原則。
- 當您從不是以檔案為基礎的存放區複製資料時 ( [Oracle](connector-oracle.md#oracle-as-source)、 [Teradata](connector-teradata.md#teradata-as-source)、 [Sap 資料表](connector-sap-table.md#sap-table-as-source)和[sap 開放式中樞](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)連接器除外, 做為已啟用資料分割的來源), 會儲存至以檔案為基礎之資料移動服務的存放區。忽略**parallelCopies**屬性。 即使已指定平行處理原則，也不會套用於此案例。
- **ParallelCopies**屬性正交于**dataIntegrationUnits**。 前者會跨所有資料整合單位計算。
- 當您指定**parallelCopies**屬性的值時, 請考慮來源和接收資料存放區的負載增加。 也請考慮負載增加至自我裝載整合執行時間 (如果複製活動是由其進行授權), 例如針對混合式複製。 當您對相同的資料存放區執行相同活動的多個活動或並存執行時, 就會發生這種負載增加的情況。 如果您注意到資料存放區或自我裝載整合執行時間已負擔負載, 請減少**parallelCopies**值以減輕負載。

**範例:**

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

### <a name="staged-copy"></a>分段複製

從來源資料存放區將資料複製到接收資料存放區時，您可以選擇使用 Blob 儲存體做為過渡暫存存放區。 暫存在下列情況下特別有用︰

- **您想要透過 PolyBase 將資料從各種資料存放區內嵌到 SQL 資料倉儲。** SQL 資料倉儲使用 PolyBase 做為高輸送量機制，將大量資料載入 SQL 資料倉儲。 來源資料必須位於 Blob 儲存體或 Azure Data Lake 存放區中, 而且必須符合其他準則。 當您從 Blob 儲存體或 Azure Data Lake Store 以外的資料存放區載入資料時，您可以啟用透過過渡暫存 Blob 儲存體的資料複製。 在此情況下, Azure Data Factory 會執行必要的資料轉換, 以確保它符合 PolyBase 的需求。 然後，它會使用 PolyBase 將資料有效率地載入到 SQL 資料倉儲。 如需詳細資訊，請參閱[使用 PolyBase 將資料載入 Azure SQL 資料倉儲](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。
- **有時候, 執行混合式資料移動 (也就是從內部部署資料存放區複製到雲端資料存放區) 時, 會需要一些時間才能透過低速網路連線。** 若要改善效能, 您可以使用分段複製來壓縮內部部署資料, 以便將資料移至雲端中的暫存資料存放區所需的時間較短。 接著, 您可以在載入至目的地資料存放區之前, 先解壓縮臨時存儲區中的資料。
- **您不想要在防火牆中開啟埠80和埠443以外的通訊埠, 因為公司的 IT 原則。** 例如，從內部部署資料存放區將資料複製到 Azure SQL Database 接收或 Azure SQL 資料倉儲接收時，您必須針對 Windows 防火牆和公司防火牆啟用連接埠 1433 上的輸出 TCP 通訊。 在此案例中, 分段複製可以利用自我裝載整合執行時間, 先透過 HTTP 或 HTTPS 在埠443上將資料複製到 Blob 儲存體暫存實例。 然後, 它可以將資料從 Blob 儲存體暫存載入 SQL Database 或 SQL 資料倉儲。 在此流程中，您不需要啟用連接埠 1433。

#### <a name="how-staged-copy-works"></a>分段複製的運作方式

當您啟用暫存功能時，會先從來源資料存放區複製資料到暫存 Blob 儲存體 (自備)。 接著再從暫存資料存放區複製資料到接收資料存放區。 Azure Data Factory 會自動為您管理兩階段流程。 在資料移動完成後, Azure Data Factory 也會清除暫存儲存體中的臨時資料。

![分段複製](media/copy-activity-performance/staged-copy.png)

當您使用暫存存放區啟用資料移動時, 可以指定是否要在將資料從來源資料存放區移至過渡或暫存資料存放區之前先壓縮資料, 然後再從過渡或暫存的 dat 移動資料之前進行解壓縮。接收資料存放區的存放區。

目前, 您無法在透過不同自我裝載的 IRs 連線的兩個數據存放區之間複製資料, 不論是否有分段複製也不會。 針對這種情況, 您可以設定兩個明確連結的複製活動, 從來源複製到預備環境, 然後從暫存到接收。

#### <a name="configuration"></a>組態

設定 [複製] 活動中的 [ **enableStaging** ] 設定, 指定在將資料載入目的地資料存放區之前, 是否要在 Blob 儲存體中暫存資料。 當您將**enableStaging**設定`TRUE`為時, 請指定下表所列的其他屬性。 您也需要建立 Azure 儲存體或儲存體共用存取簽章連結服務, 以供暫存 (如果您沒有的話)。

| 屬性 | 說明 | 預設值 | 必要項 |
| --- | --- | --- | --- |
| enableStaging |指定您是否要透過過渡暫存存放區複製資料。 |False |否 |
| linkedServiceName |指定 [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) 連結服務的名稱，以代表您用來做為過渡暫存存放區的儲存體執行個體。 <br/><br/> 您無法使用具有共用存取簽章的儲存體, 透過 PolyBase 將資料載入 SQL 資料倉儲。 您可以將它用於其他所有案例。 |N/A |是，當 **enableStaging** 設為 TRUE |
| path |指定要包含分段資料的 Blob 儲存體路徑。 如果您未提供路徑, 服務會建立容器來儲存暫存資料。 <br/><br/> 只有在使用具有共用存取簽章的儲存體時，或需要讓暫存資料位於特定位置時，才指定路徑。 |N/A |否 |
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

#### <a name="staged-copy-billing-impact"></a>分段複製的計費影響

您會根據兩個步驟向您收費: 複製持續時間和複製類型。

* 當您在雲端複製期間使用暫存時, 如果將資料從雲端資料存放區複製到另一個雲端資料存放區 (這兩個階段都是由 Azure integration runtime 所授權), 您就會向您收取 [步驟1和步驟2的複製持續時間總和] x [雲端複製單位價格]。
* 當您在混合式複製期間使用暫存時, 會將資料從內部部署資料存放區複製到雲端資料存放區, 由自我裝載整合執行時間所授權的一個階段, 您需支付 [混合式複製持續時間] x [混合式複製單位價格] + [雲端複製持續時間]x [雲端複製單位價格]。

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
- [使用 Azure Data Factory 將資料從您的 data lake 或資料倉儲遷移至 Azure](data-migration-guidance-overview.md)
- [將資料從 Amazon S3 遷移至 Azure 儲存體](data-migration-guidance-s3-azure-storage.md)
