---
title: 針對 Azure HDInsight 上的 Apache HBase 效能問題進行疑難排解
description: 取得 Azure HDInsight 最佳效能的各種 Apache HBase 效能微調指導方針和秘訣。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: c67f21a6ed8a7697977bb7737f0e46348efb2530
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "71266649"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>針對 Azure HDInsight 上的 Apache HBase 效能問題進行疑難排解

本檔說明各種 Apache HBase 效能微調指導方針，以及在 Azure HDInsight 上取得最佳效能的秘訣。 其中有許多秘訣取決於特定的工作負載和讀取/寫入/掃描模式。 在生產環境上套用之前，請先徹底測試設定變更。

## <a name="hdinsight-hbase-performance-insights"></a>HDInsight HBase 效能深入解析

大部分 HBase 工作負載中最重要的瓶頸是「先行寫入記錄」（WAL）。 它會嚴重影響寫入效能。 HDInsight HBase 具有個別的儲存體計算模型–也就是，資料會儲存在 Azure 儲存體，但區域伺服器裝載在 Vm 上。 到目前為止，寫入記錄檔也會寫入 Azure 儲存體，因此在 HDInsight 的情況下深化此瓶頸。 [加速寫入](./apache-hbase-accelerated-writes.md)功能的設計是為了解決這個問題，其方式是將寫入記錄寫入至 AZURE premium SSD 受控磁片。 這項優點可大幅提升效能，並協助許多大量寫入工作負載所面臨的問題。

請使用[進階區塊 Blob 儲存體帳戶](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/)作為遠端儲存體，以大幅改善讀取作業。 只有在已啟用「預寫記錄檔」功能時，才能使用此選項。

## <a name="compaction"></a>壓縮

「壓縮」是在「社區」中基本上同意的另一個潛在瓶頸。  根據預設，HDInsight HBase 叢集上會停用主要壓縮。 這是因為假設它是需要大量資源的程式，我們想要讓客戶能夠根據其工作負載特性（也就是在離峰時段）以完整的方式排定其排程。 此外，假設我們的儲存體是遠端（Azure 儲存體的支援），而不是本機 HDFS，這在大部分的內部內部部署實例上很常見，資料位置並不重要，這是主要壓縮的主要目標之一。

假設客戶會根據自己的便利性，負責排定主要壓縮。 如果未進行這項維護，壓縮會大幅影響長期執行的讀取效能。

特別是針對掃描工作，平均延遲大於100毫秒的時間，應該是問題的原因。 檢查是否已正確排程主要壓縮。

## <a name="know-your-apache-phoenix-workload"></a>瞭解您的 Apache Phoenix 工作負載

回答下列問題可協助您更瞭解 Apache Phoenix 的工作負載：

* 您的所有「讀取」轉譯為掃描嗎？
    * 若是如此，這些掃描的特性有哪些？
    * 您是否已將這些掃描的 phoenix 資料表架構優化，包括適當的編制索引？
* 您已使用 `EXPLAIN` 語句來瞭解「讀取」產生的查詢計劃。
* 您的寫入是否「upsert」？
    * 若是如此，它們也會進行掃描。 掃描的預期延遲順序為平均100毫秒，而不是在 HBase 中取得點的10毫秒。  

## <a name="test-methodology-and-metrics-monitoring"></a>測試方法和計量監視

如果您使用 YCSB、JMeter 或 Pherf 等基準測試和微調效能，請確定下列事項：

1. 用戶端機器不會成為瓶頸（檢查用戶端電腦上的 CPU 使用量）。

1. 用戶端的一種方式（例如執行緒數目等等）會適當地調整，以使用戶端頻寬飽和。

1. 測試結果會正確且有系統地記錄。

如果您的查詢突然開始比之前更糟，請檢查應用程式代碼中是否有潛在的錯誤–它會突然產生大量不正確資料，因而自然增加讀取延遲？

## <a name="migration-issues"></a>遷移問題

如果要遷移到 Azure HDInsight，請確定您的遷移是以有系統的方式正確地完成，最好是透過自動化進行。 避免手動遷移。 請確定下列事項：

1. 資料表屬性（例如壓縮、bloom 篩選等）應正確遷移。

1. 若為 Phoenix 資料表，進行 salt 處理設定應適當對應至新的叢集大小。 例如，建議使用 salt 值區數目，做為叢集中的背景工作節點數目的倍數–特定倍數是觀察到的熱找出量的因素。  

## <a name="server-side-config-tunings"></a>伺服器端設定 Tunings

在 HDInsight HBase 中，HFiles 會儲存在遠端存放裝置上，因此當快取遺失時，讀取的成本絕對會高於內部內部部署系統，其具有本機 HDFS 的資料，因為涉及的網路延遲。 在大部分的情況下，智慧型使用 HBase 快取（區塊快取和 bucket 快取）的設計是為了避免這個問題。 不過，在某些情況下，可能會有客戶的問題。 使用 premium 區塊 blob 帳戶已經有説明。 不過，使用 WASB （Windows Azure 儲存體驅動程式） blob 依賴某些屬性（例如 `fs.azure.read.request.size`），根據其判斷為讀取模式（順序與隨機）來提取區塊中的資料時，我們可能會繼續查看具有讀取的延遲較高的實例。 我們已找到將讀取要求區塊大小（`fs.azure.read.request.size`）設定為 512 KB 的經驗實驗，並比對 HBase 資料表的區塊大小，使其達到最佳結果。

適用于大部分大型節點叢集的 HDInsight HBase 會提供 `bucketcache`，做為連結至 VM 的本機 SSD 上的檔案，而該檔案會執行 `regionservers`。 有時候，使用 off 堆積快取可以提供一些改進。 這有使用可用記憶體的限制，而且大小可能小於檔案型快取，因此這不一定是最好的選擇。

我們所調整的一些其他特定參數似乎已協助不同程度的差異，如下所示：

1. 將 `memstore` 的大小從預設的 128 MB 增加到 256 MB –通常建議使用此設定來進行繁重的寫入案例。

1. 增加專用於壓縮的執行緒數目–從預設值1到4。 如果我們觀察到經常的次要 compactions，則此設定是相關的。

1. 避免因為存放區限制而導致封鎖 `memstore` 排清。 `Hbase.hstore.blockingStoreFiles` 可以增加至100，以提供此緩衝區。

1. 若要控制排清，預設值可以如下所示：

    1. `Hbase.regionserver.maxlogs` 可以從 32 upped 為140（因 WAL 限制而避免排清）。

    1. `Hbase.regionserver.global.memstore.lowerLimit` = 0.55。

    1. `Hbase.regionserver.global.memstore.upperLimit` = 0.60。

1. 執行緒集區調整的 Phoenix 特有的程式：

    1. `Phoenix.query.queuesize` 可以增加至10000。

    1. `Phoenix.query.threadpoolsize` 可以增加至512。

1. 其他 phoenix 特定的內容：

    1. 如果我們有大型或多個索引查閱，`Phoenix.rpc.index.handler.count` 可以設定為50。

    1. `Phoenix.stats.updateFrequency` –可以從預設的15分鐘 upped 為1小時。

    1. `Phoenix.coprocessor.maxmetadatacachetimetolivems` –可以從30分鐘 upped 為1小時。

    1. `Phoenix.coprocessor.maxmetadatacachesize` –可以從 20 MB upped 到 50 MB。

1. RPC 超時– HBase rpc 超時、HBase 用戶端掃描器超時和 Phoenix 查詢超時時間可能會增加到3分鐘。 請務必注意，`hbase.client.scanner.caching` 參數會設定為符合伺服器端和用戶端結尾的值。 否則，此設定會導致用戶端上的 `OutOfOrderScannerException` 相關錯誤。 這種設定應該設定為較低的值來進行大型掃描。 我們會將此值設定為100。

## <a name="other-considerations"></a>其他考量

要考慮進行微調的一些其他參數：

1. `Hbase.rs.cacheblocksonwrite` –在 HDI 上預設會將此設定設為 true。

1. 允許稍後延遲次要壓縮的設定。

1. 實驗性設定，例如調整保留給讀取和寫入要求的佇列百分比。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

- 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

- 連接[@AzureSupport](https://twitter.com/azuresupport) -用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社區連接到正確的資源：解答、支援和專家。

- 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
