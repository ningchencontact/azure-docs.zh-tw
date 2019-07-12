---
title: Azure Data Factory 中的對應資料流程的效能和微調指南 |Microsoft Docs
description: 深入了解 Azure Data Factory 中的資料流程效能的影響，當您使用對應的資料流時的關鍵因素。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 1ee266d7d9846a357dce613817affdb0cde5bfdc
ms.sourcegitcommit: e6cb7ca206a125c05acfd431b5a64391a8dcc6b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67569034"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>對應資料的流程效能及微調指南

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory 對應資料流程提供設計、 部署和協調大規模的資料轉換的無程式碼的瀏覽器介面。

> [!NOTE]
> 如果您不熟悉如何使用 ADF 對應資料流動一般情況下，請參閱[資料的流程概觀](concepts-data-flow-overview.md)之前先閱讀這篇文章。
>

> [!NOTE]
> 當您設計和測試資料流，從 ADF UI 時，請務必開啟偵錯參數讓您可以執行您的資料流中即時而不需等待叢集準備項目。
>

![偵錯 按鈕](media/data-flow/debugb1.png "偵錯")

## <a name="monitor-data-flow-performance"></a>監視資料流程效能

設計您的對應資料流動瀏覽器中，您可以單元測試每個個別的轉換在底部設定 窗格，針對每個轉換的資料預覽 索引標籤，即可。 您應該採取的下一個步驟是測試您的資料流程端對端管線設計師 」 中。 新增執行資料流活動，並使用偵錯 按鈕來測試在資料流程的效能。 在 [管線] 視窗的下方窗格中，您會看到 eyeglass 圖示，在 [動作] 下：

![監視資料流程](media/data-flow/mon002.png "資料流程監視器 2")

按一下該圖示會顯示執行計畫，在資料流程的後續的效能設定檔。 您可以使用這項資訊來預估您針對不同大小的資料來源的資料流程的效能。 請注意，您可以假設 1 分鐘，叢集作業執行設定時間的整體效能計算的而且如果您使用預設的 Azure Integration Runtime，您可能需要新增叢集啟動時間的 5 分鐘。

![資料流監視](media/data-flow/mon003.png "資料流程監視 3")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>最佳化 Azure SQL Database 和 Azure SQL 資料倉儲

![來源組件](media/data-flow/sourcepart3.png "來源組件")

### <a name="partition-your-source-data"></a>將來源資料分割

* 請移至 「 最佳化 」，然後選取 「 來源 」。 在查詢中設定特定的資料表資料行或類型。
* 如果您選擇 [資料行]，然後挑選資料分割資料行。
* 此外，設您的 Azure SQL DB 的連線數目上限。 您可以嘗試較高的設定，以取得平行連線到您的資料庫。 不過，某些情況下可能會導致更快的效能，數量有限的連線。
* 來源資料庫資料表不需要進行資料分割。
* 設定您的來源轉換符合您的資料庫資料表的資料分割配置中的查詢，可讓來源資料庫引擎，以利用分割區刪除。
* 如果您的來源不已資料分割，ADF 會仍然會使用 Spark 轉換環境，根據您選取 來源轉換中的索引鍵中的資料分割。

### <a name="set-batch-size-and-query-on-source"></a>設定批次大小與來源上的查詢

![來源](media/data-flow/source4.png "來源")

* 設定批次大小，會指示 ADF 將資料儲存在記憶體中而不是由資料列的資料列集。 它是選擇性的設定，您可能執行資源不足的計算節點上若不正確大小。
* 設定查詢，可以讓您篩選來源端的資料列右側，即使到達適用於資料流程進行處理，可以進行更快速的初始資料擷取之前。
* 如果您使用查詢時，您就可以為您的 Azure SQL DB，也就是 READ UNCOMMITTED 新增選擇性的查詢提示

### <a name="set-isolation-level-on-source-transformation-settings-for-sql-datasets"></a>來源轉換設定為 SQL 資料集上設定隔離等級

* 讀取未認可將來源轉換提供更快的查詢結果

![隔離等級](media/data-flow/isolationlevel.png "隔離等級")

### <a name="set-sink-batch-size"></a>設定接收批次大小

![接收](media/data-flow/sink4.png "接收")

* 為了避免逐列處理的資料流，請在 接收設定，適用於 Azure SQL DB 中設定 「 批次大小 」。 這會告知 ADF 來處理資料庫寫入提供的大小為基礎的批次。

### <a name="set-partitioning-options-on-your-sink"></a>設定資料分割的接收器上的選項

* 即使您沒有在目的地 Azure SQL DB 資料表分割資料，請移至 [最佳化] 索引標籤，並設定資料分割。
* 通常，只告訴 ADF 使用執行中的 Spark 叢集上的分割區更快的資料載入不會強迫單一節點/資料分割中的所有連線會導致循環配置資源。

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>增加您 Azure 整合執行階段中的計算引擎的大小

![新的 IR](media/data-flow/ir-new.png "新 IR")

* 增加將增加節點數目，並提供您更多的處理能力來查詢及寫入您的 Azure SQL DB 的核心數目。
* 請嘗試將套用到計算節點的更多資源的 「 計算最佳化 」 和 「 記憶體最佳化 」 選項。

### <a name="unit-test-and-performance-test-with-debug"></a>單元測試和偵錯與效能測試

* 當單元測試的資料流，設定 「 資料流程偵錯 」 的按鈕設為 ON。
* 在資料流程設計師中，使用 [資料預覽] 索引標籤上轉換檢視轉換邏輯的結果。
* 單元測試您的資料將資料流程的活動放置在管線設計方向為由管線設計工具畫布，然後使用 [偵錯] 按鈕來測試。
* 在偵錯模式下測試將會處理已準備的即時叢集環境，而不需要等待在即時叢集啟動。

### <a name="disable-indexes-on-write"></a>寫入停用索引
* 使用 ADF 管線預存程序活動之前您資料流程的活動，以停用您會接收從正在寫入的目標資料表上的索引。
* 之後您資料流程的活動，將啟用這些索引的另一個預存程序活動。

### <a name="increase-the-size-of-your-azure-sql-db"></a>增加您的 Azure SQL DB 大小
* 排程調整大小，您的來源和接收 Azure SQL DB，您的管線，以提高輸送量及降低 Azure 節流，一旦達到 DTU 限制您執行之前。
* 您的管線執行完成之後，您可以調整您的資料庫回到其正常的執行率。

## <a name="optimizing-for-azure-sql-data-warehouse"></a>最佳化 Azure SQL 資料倉儲

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>使用暫存來透過 Polybase 的大量資料載入

* 為了避免逐列處理的資料流，以便 ADF 可以利用以避免逐列插入至 DW 的 Polybase，接收設定中設定 「 預備 」 選項。 這會指示要使用 Polybase，因此可以將資料載入大量的 ADF。
* 當您執行來自您資料的流程活動上開啟的管線，使用接移，您必須選取的暫存大量載入資料的 Blob 存放區位置。

### <a name="increase-the-size-of-your-azure-sql-dw"></a>增加您的 Azure SQL DW 的大小

* 排程調整大小，您的來源和接收 Azure SQL DW，然後再執行您的管線，以提高輸送量及降低 Azure 節流，一旦達到 DWU 限制。

* 您的管線執行完成之後，您可以調整您的資料庫回到其正常的執行率。

## <a name="optimize-for-files"></a>最佳化的檔案

* 您可以控制多少個分割區將使用 ADF。 在每個來源與接收的轉換，以及每個個別的轉換，您可以設定資料分割配置。 對於較小的檔案，您可能會發現越順暢且快速要求 Spark 來分割您的小型檔案選取 「 單一分割區 」 可以有時運作。
* 如果您的來源資料的相關沒有足夠的資訊，您可以選擇 「 循環配置資源 」 資料分割，然後設定資料分割數目。
* 如果您瀏覽資料，並尋找您有可以是很好的雜湊索引鍵的資料行，使用雜湊資料分割選項。

### <a name="file-naming-options"></a>檔案命名選項

* 將已轉換的資料寫入 ADF 對應資料流程中的預設本質是寫入 Blob 或 ADLS 連結服務所擁有的資料集。 您應該將該資料集會指向資料夾或容器，不是具名的檔案。
* 明確地選擇資料的流程使用 Azure Databricks Spark 來執行，這表示您的輸出會分成多個根據檔案可能是預設的 Spark 分割或配置您的資料分割。
* ADF 資料流程中的常見作業是選擇 [輸出至單一檔案]，讓所有的輸出組件檔案一起合併成單一的輸出檔案。
* 不過，這項作業需要輸出減少到單一叢集節點上的單一資料分割。
* 選擇這個常用的選項時，請記住這點。 如果您要結合至單一輸出檔案資料分割的許多大型的原始程式檔，您可以執行叢集節點的資源不足。
* 若要避免耗盡計算節點的資源，您可以在 ADF，會針對效能進行最佳化，保留預設值或明確的資料分割配置，然後再新增 會合併所有組件的管線中後續的複製活動檔案從輸出資料夾複製到新的單一檔案。 基本上，這項技術會分隔檔案合併 」 轉換的動作，並達到相同結果與設為 「 輸出至單一檔案 」。

### <a name="looping-through-file-lists"></a>循環使用檔案清單

在大部分情況下，在 ADF 中的資料流程會執行管線，以允許資料流量的來源轉換來反覆查看多個檔案從更佳。 換句話說，最好使用萬用字元，或在您的來源資料中的檔案清單流程，逐一查看在管線中，呼叫每個反覆運算上的 執行資料流程中使用 ForEach 檔案的大型清單。 資料流程的程序會更快速執行，藉由使用內部資料流進行迴圈。

比方說，如果我有一份資料檔案從 2019 年 7 月，我想要在 Blob 儲存體中的資料夾中處理時，它會是更好的效能從您的管線呼叫一次執行資料流程的活動，並使用萬用字元，在您的來源，就像這樣:

```DateFiles/*_201907*.txt```

這會優於查閱，以對 Blob 存放區，然後會跨所有相符的檔案執行資料流程的活動內搭配使用 ForEach 逐一查看管線中執行。

## <a name="next-steps"></a>後續步驟

請參閱其他資料流程的文章與效能相關：

- [資料流程最佳化 索引標籤](concepts-data-flow-optimize-tab.md)
- [Data Flow 活動](control-flow-execute-data-flow-activity.md)
- [監視資料流程效能](concepts-data-flow-monitoring.md)
