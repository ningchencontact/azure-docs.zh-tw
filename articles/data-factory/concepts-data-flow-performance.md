---
title: 對應 Azure Data Factory 中的資料流程效能和微調指南 |Microsoft Docs
description: 瞭解當您使用對應資料流程時，會影響 Azure Data Factory 中資料流程效能的關鍵因素。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 09/22/2019
ms.openlocfilehash: e4b3e08c0cc7fc1ead2aed551c228c6a1165c3b6
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180852"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>對應資料流程效能和微調指南

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory 對應資料流程可提供無程式碼的瀏覽器介面，以大規模設計、部署和協調資料轉換。

> [!NOTE]
> 如果您不熟悉 ADF 對應資料流程的一般情況，請參閱[資料流程總覽](concepts-data-flow-overview.md)，再閱讀本文。
>

> [!NOTE]
> 當您從 ADF UI 設計和測試資料流程時，請務必開啟 Debug 參數，讓您可以即時執行資料流程，而不需要等候叢集準備。
>

![調試 按鈕](media/data-flow/debugb1.png "Debug")

## <a name="monitor-data-flow-performance"></a>監視資料流程效能

在瀏覽器中設計對應資料流程時，您可以在每個轉換的底部設定窗格中，按一下 [資料預覽] 索引標籤，以對每個個別轉換進行單元測試。 您應採取的下一個步驟是在「管線設計師」中端對端測試您的資料流程。 加入「執行資料流程」活動，並使用 [偵錯工具] 按鈕來測試資料流程的效能。 在 [管線] 視窗的下方窗格中，您會在 [動作] 底下看到 eyeglass 圖示：

![資料流程監視](media/data-flow/mon002.png "資料流程監視器 2")

按一下該圖示，就會顯示資料流程的執行計畫和後續的效能設定檔。 您可以使用這項資訊，針對不同大小的資料來源來估計資料流程的效能。 請注意，您可以在整體效能計算中假設1分鐘的叢集作業執行設定時間，如果您使用的是預設 Azure Integration Runtime，可能也需要加入5分鐘的叢集微調時間。

![資料流程監視](media/data-flow/mon003.png "資料流程監視器 3")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Azure SQL Database 和 Azure SQL 資料倉儲的優化

![來源部分](media/data-flow/sourcepart3.png "來源部分")

### <a name="partition-your-source-data"></a>分割您的來源資料

* 移至 [優化]，然後選取 [來源]。 請在查詢中設定特定的資料表資料行或類型。
* 如果您選擇 [資料行]，則挑選資料分割資料行。
* 此外，請設定 Azure SQL DB 的最大連線數目。 您可以嘗試較高的設定，以取得與資料庫的平行連接。 不過，某些情況下可能會因為連線數目有限而導致效能更快。
* 您的源資料庫資料表不需要進行資料分割。
* 在您的來源轉換中設定符合資料庫資料表之資料分割配置的查詢，可讓源資料庫引擎利用分割區刪除。
* 如果您的來源尚未分割，ADF 仍然會根據您在 [來源] 轉換中選取的金鑰，使用 Spark 轉換環境中的資料分割。

### <a name="set-batch-size-and-query-on-source"></a>設定來源上的批次大小和查詢

![來源](media/data-flow/source4.png "來源")

* 設定批次大小會指示 ADF 將資料儲存在記憶體中的集合中，而不是逐列。 這是選擇性的設定，如果計算節點上的資源大小不正確，您可能會用完它們。
* 設定查詢可以讓您直接在來源篩選資料列，然後再抵達資料流程以進行處理，這可以讓初始資料的取得速度更快。
* 如果您使用查詢，您可以為 Azure SQL DB 新增選擇性的查詢提示，亦即讀取未認可

### <a name="set-isolation-level-on-source-transformation-settings-for-sql-datasets"></a>設定 SQL 資料集之來源轉換設定的隔離等級

* 讀取未認可的會在來源轉換時提供更快速的查詢結果

![隔離等級](media/data-flow/isolationlevel.png "隔離等級")

### <a name="set-sink-batch-size"></a>設定接收批次大小

![接收](media/data-flow/sink4.png "接收")

* 為了避免對資料流程進行逐列處理，請在 Azure SQL DB 的接收設定中設定「批次大小」。 這會告訴 ADF 根據所提供的大小，以批次方式處理資料庫寫入。

### <a name="set-partitioning-options-on-your-sink"></a>在您的接收上設定資料分割選項

* 即使您的資料未分割在目的地 Azure SQL DB 資料表中，請移至 [優化] 索引標籤並設定分割。
* 通常，只是告訴 ADF 在 Spark 執行叢集上使用迴圈配置資源分割，會產生更快的資料載入，而不會強制執行來自單一節點/資料分割的所有連接。

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>在 Azure Integration Runtime 中增加計算引擎的大小

![新增 IR](media/data-flow/ir-new.png "新增 IR")

* 增加核心數目，這會增加節點數目，並提供您更多的處理能力來查詢和寫入您的 Azure SQL DB。
* 嘗試「計算優化」和「記憶體優化」選項，將更多資源套用至您的計算節點。

### <a name="unit-test-and-performance-test-with-debug"></a>使用 debug 進行單元測試和效能測試

* 當單元測試資料流程時，請將 [資料流程調試] 按鈕設定為 [開啟]。
* 在資料流程設計師中，使用 [轉換] 上的 [資料預覽] 索引標籤來查看轉換邏輯的結果。
* 對管線設計工具中的資料流程進行單元測試：將資料流程活動放在管線設計畫布上，並使用 [Debug] 按鈕進行測試。
* 在 [偵錯工具] 模式中測試會針對即時準備就緒叢集環境運作，而不需要等待即時叢集啟動。
* 在資料流程設計師體驗內的資料預覽期間，您可以從資料流程設計師 UI 上的 [Debug 設定] 連結設定資料列限制，以限制每個來源測試的資料量。 請注意，您必須先開啟 [Debug] 模式。

![Debug 設定](media/data-flow/debug-settings.png "Debug 設定")

* 從管線的「偵測執行」測試資料流程時，您可以在每個來源上設定取樣大小，以限制用於測試的資料列數目。 請務必在定期運作排程時停用取樣。

資料![列取樣]資料(media/data-flow/source1.png "列取樣")

### <a name="disable-indexes-on-write"></a>在寫入時停用索引
* 在您的資料流程活動之前使用 ADF 管線預存程式活動，以停用要從接收器寫入的目標資料表索引。
* 在資料流程活動之後，新增另一個已啟用這些索引的預存程式活動。

### <a name="increase-the-size-of-your-azure-sql-db"></a>增加您的 Azure SQL DB 大小
* 在您執行管線以增加輸送量，並在達到 DTU 限制後將 Azure 節流降到最低之後，請排程來源的調整大小並接收 Azure SQL DB。
* 當您的管線執行完成之後，您可以將資料庫調整回其正常執行速率。

## <a name="optimizing-for-azure-sql-data-warehouse"></a>Azure SQL 資料倉儲的優化

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>使用預備環境透過 Polybase 大量載入資料

* 若要避免資料流程的逐列處理，請在接收設定中設定「暫存」選項，讓 ADF 可以利用 Polybase 來避免將逐列插入 DW 中。 這會指示 ADF 使用 Polybase，讓資料可以大量載入。
* 當您從管線執行「資料流程」活動時，若已開啟暫存，您將需要選取暫存資料的 Blob 存放區位置，以便進行大量載入。

### <a name="increase-the-size-of-your-azure-sql-dw"></a>增加您的 Azure SQL DW 大小

* 在您執行管線以增加輸送量，並在達到 DWU 限制之後，將 Azure 節流降到最低，以排程來源和接收 Azure SQL DW 的調整大小。

* 當您的管線執行完成之後，您可以將資料庫調整回其正常執行速率。

## <a name="optimize-for-files"></a>檔案優化

* 您可以控制 ADF 會使用多少個磁碟分割。 在每個來源 & 接收轉換以及每個個別的轉換中，您可以設定資料分割配置。 對於較小的檔案，您可能會發現選取 [單一分割區] 有時可以比要求 Spark 分割小型檔案更好且快速。
* 如果您的來源資料沒有足夠的資訊，您可以選擇 [迴圈配置資源]，並設定分割區的數目。
* 如果您流覽資料，併發現您有可以是良好雜湊索引鍵的資料行，請使用雜湊資料分割選項。
* 在 [資料預覽] 和 [管線偵錯工具] 中進行調試時，請注意檔案型源資料集的限制和取樣大小只會套用至傳回的資料列數目，而不會套用至讀取的資料列數目。 這一點很重要，因為它可能會影響您的偵錯工具執行效能，而且可能會導致流程失敗。
* 請記住，debug 叢集預設是小型的單一節點叢集，因此請使用暫存的小型檔案進行偵錯工具。 移至 [偵錯工具] [設定]，並使用暫存檔案指向資料的小型子集。

![Debug 設定](media/data-flow/debugsettings3.png "Debug 設定")

### <a name="file-naming-options"></a>檔案命名選項

* 在 ADF 對應資料流程中寫入轉換資料的預設性質是寫入具有 Blob 或 ADLS 連結服務的資料集。 您應該將該資料集設定為指向資料夾或容器，而不是命名檔案。
* 資料流程會使用 Azure Databricks Spark 執行，這表示您的輸出會根據預設 Spark 分割或您明確選擇的資料分割配置，分割成多個檔案。
* ADF 資料流程中的一個很常見的作業，就是選擇「輸出到單一檔案」，讓所有的輸出元件檔案全部合併成一個輸出檔。
* 不過，此作業需要輸出縮減為單一叢集節點上的單一分割區。
* 選擇這個熱門的選項時，請記住這一點。 如果您要將許多大型來源檔案結合成單一輸出檔案分割區，則可以耗盡叢集節點資源。
* 若要避免耗盡計算節點資源，您可以在 ADF 中保留預設或明確的資料分割配置，以優化效能，然後在管線中新增後續的複製活動，以將輸出檔案夾中的所有元件檔案合併到新的單一文字檔. 基本上，這項技術會將轉換的動作與檔案合併分開，而達到與設定「輸出至單一檔案」相同的結果。

### <a name="looping-through-file-lists"></a>迴圈流覽檔案清單

在大部分的情況下，ADF 中的資料流程會從允許資料流程來源轉換反復查看多個檔案的管線中執行得更好。 換句話說，最好在資料流程中的來源內使用萬用字元或檔案清單，而不是使用管線中的 ForEach 逐一查看大型檔案清單，並在每個反復專案上呼叫執行資料流程。 資料流程處理常式會以更快的速度執行，藉由允許迴圈在資料流程內進行。

例如，如果我有2019年7月的資料檔案清單，而我想要在 Blob 儲存體的資料夾中處理，則從您的管線呼叫「執行資料流程」活動一次，並在來源中使用萬用字元（例如:

```DateFiles/*_201907*.txt```

這會比對管線中的 Blob 存放區進行查閱的效果更好，然後使用 ForEach 與內的「執行資料流程」活動逐一查看所有相符的檔案。

### <a name="increase-the-size-of-your-debug-cluster"></a>增加您的 debug 叢集大小

根據預設，開啟 debug 會使用針對每個資料處理站自動建立的預設 Azure 整合執行時間。 此預設 Azure IR 是針對驅動程式節點設定為8個核心、4個，而針對背景工作角色節點設定為4個，使用一般計算屬性。 當您使用較大的資料進行測試時，您可以藉由建立具有較大設定的新 Azure IR 來增加您的偵錯工具大小，並在切換到 [debug] 時選擇這個新的 Azure IR。 這會指示 ADF 使用此 Azure IR 進行資料預覽，以及使用資料流程來進行管線的資料流程處理。

## <a name="next-steps"></a>後續步驟

請參閱其他與效能相關的資料流程文章：

- [資料流程優化索引標籤](concepts-data-flow-optimize-tab.md)
- [資料流程活動](control-flow-execute-data-flow-activity.md)
- [監視資料流程效能](concepts-data-flow-monitoring.md)
