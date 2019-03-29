---
title: Apache Kafka 的 HDInsight 叢集的效能最佳化
description: 最佳化 Azure HDInsight 上的 Apache Kafka 工作負載提供技術的概觀。
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: 3f15f45e0543c582d70463fb9ddc7ac569ff57bc
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576753"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Apache Kafka 的 HDInsight 叢集的效能最佳化

本文提供將 HDInsight 中 Apache Kafka 的工作負載的效能最佳化一些的建議。 重點在於調整產生者和訊息代理程式設定。 有不同的方法可以測量效能，以及您所套用的最佳化作業將取決於您的業務需求。

## <a name="architecture-overview"></a>架構概觀

Kafka 主題用來組織資料錄。 記錄是由產生者，所產生，並由取用者取用。 產生者會將記錄傳送到 Kafka 訊息代理程式，然後將資料儲存。 HDInsight 叢集中的每個背景工作節點都是 Kafka 訊息代理程式。

主題會將記錄分割至各個訊息代理程式。 取用記錄時，您可以針對每個分割區最多使用一個取用者，以達到資料平行處理。

複寫用來重複節點之間的資料分割。 這可防止節點 (broker) 中斷。 複本的群組之間的單一資料分割指定為資料分割的領導者。 使用 ZooKeeper 所管理的狀態，可將生產者流量路由傳送至每個節點的前端項目。

## <a name="identify-your-scenario"></a>識別您的案例

Apache Kafka 的效能會有兩個主要部分 – 輸送量和延遲。 輸送量是可以處理資料的最大速率。 更高的輸送量通常會比較好。 延遲是要儲存或擷取資料所花費的時間。 較低的延遲通常會比較好。 尋找適當的平衡之間的輸送量、 延遲及應用程式的基礎結構的成本頗具挑戰性。 您的效能需求可能會比對下列三種常見的情況，根據您是否需要高輸送量、 低延遲，或兩者的其中一個：

* 高輸送量、 低延遲。 此案例需要高輸送量和低延遲 （~ 100 毫秒為單位）。 這種類型的應用程式的範例是服務可用性監視。
* 高輸送量、 高延遲。 此案例需要高輸送量 (~1.5 GBps)，但可承受較高的延遲 （< 250 毫秒）。 這種類型的應用程式的範例是用於接近即時的處理程序，例如安全性和入侵偵測應用程式的遙測資料擷取。
* 低輸送量、 低延遲。 這種情況下進行即時處理，需要低延遲 （< 10 毫秒），但可承受較低的輸送量。 這種類型的應用程式的範例為線上的拼字和文法檢查。

## <a name="producer-configurations"></a>產生者組態

下列各節將強調一些最重要的組態屬性，來最佳化您的 Kafka 產生者的效能。 所有的組態屬性的詳細說明，請參閱 <<c0> [ 生產者組態上的 Apache Kafka 文件](https://kafka.apache.org/documentation/#producerconfigs)。

### <a name="batch-size"></a>批次大小

Apache Kafka 產生者組合的訊息 （稱為批次） 會當做一個單位傳送至儲存在單一儲存體磁碟分割中的群組。 批次大小表示必須要有該群組傳送之前的位元組數目。 增加`batch.size`參數可以增加輸送量，因為它可以減少額外負荷與網路 IO 要求的處理。 承擔的負載增加的批次大小可能會增加產生者等待批次才能備妥 Kafka 傳送延遲。 負載量大時，建議您增加批次大小，以改善輸送量和延遲。

### <a name="producer-required-acknowledgements"></a>所需的產生者通知

產生者所需`acks`視為寫入要求之前，資料分割指引所需的通知數目會決定設定完成。 此設定會影響資料可靠性和所需的值`0`， `1`，或`-1`。 值`-1`表示，通知必須從接收的所有複本完成寫入之前。 設定`acks = -1`較強的保證，針對資料遺失，但它也提供結果中較高的延遲和較低的輸送量。 如果您的應用程式需求要求更高的輸送量，請嘗試設定`acks = 0`或`acks = 1`。 請記住，未認可所有複本可減少資料可靠性。

### <a name="compression"></a>壓縮

Kafka 產生者可以設定要壓縮訊息然後傳送給訊息代理程式。 `compression.type`設定會指定要使用的壓縮轉碼器。 支援的壓縮轉碼器是"gzip""snappy，"和"lz4。 」 壓縮是有幫助，而且應該被視為磁碟容量限制時。

在兩個常用的壓縮轉碼器之間`gzip`並`snappy`，`gzip`具有較高的壓縮比率，會導致較低的磁碟使用量，但代價是更高的 CPU 負載。 `snappy`轉碼器提供較少的 CPU 負荷較少壓縮。 您可以決定要使用哪些轉碼器會根據 broker 磁碟或生產者 CPU 限制。 `gzip` 可以壓縮資料，費率五次高於`snappy`。

使用資料壓縮會增加可以儲存在磁碟的記錄數目。 它也可以增加 CPU 額外負荷的情況下在其中產生者和訊息代理程式正在使用的壓縮格式之間不相符。 必須傳送前先壓縮資料，並再解壓縮之前處理。

## <a name="broker-settings"></a>代理人設定

下列各節將強調一些最重要的設定，來最佳化您的 Kafka 代理程式的效能。 所有的詳細說明 broker 設定，請參閱[生產者組態上的 Apache Kafka 文件](https://kafka.apache.org/documentation/#producerconfigs)。


### <a name="number-of-disks"></a>磁碟數量

儲存體磁碟有限 （輸入/輸出作業每秒） 的 IOPS 和讀/寫每秒位元組數。 在建立新的分割區時，Kafka 會將每個新的資料分割儲存在具有最少的現有資料分割，以平衡它們的可用磁碟的磁碟上。 儲存策略，處理數百個每個磁碟上的磁碟分割複本時，儘管 Kafka 可以輕鬆地使飽和的可用的磁碟輸送量。 這裡的缺點是輸送量與成本之間。 如果您的應用程式需要更高的輸送量，則在受管理的更多有關建立叢集訊息代理程式每個磁碟。 HDInsight 目前不支援將受控的磁碟新增至執行中的叢集。 如需有關如何設定受管理的磁碟數目的詳細資訊，請參閱 <<c0> [ 設定適用於 HDInsight 上的 Apache Kafka 的儲存體和延展性](apache-kafka-scalability.md)。 了解成本影響的不斷增加的儲存空間，供您的叢集中的節點。

### <a name="number-of-topics-and-partitions"></a>主題和資料分割數目

Kafka 產生者寫入主題。 從主題讀取 Kafka 取用者。 主題是一個記錄檔，也就是在磁碟上的資料結構與相關聯。 Kafka 會將附加 producer(s) 主題記錄檔結尾的記錄。 主題記錄是由許多分散在多個檔案的資料分割所組成。 這些檔案，接著，分散到多個 Kafka 叢集的節點。 取用者從其頻率的 Kafka 主題讀取，並可以挑選它們主題記錄檔中的位置 （位移）。

每個 Kafka 分割區是在系統上，記錄檔，並產生者執行緒可以同時寫入至多個記錄檔。 同樣地，因為每個取用者執行緒會讀取一個資料分割中的訊息，從多個分割區取用以平行方式也會處理。

增加分割區密度 （每個訊息代理程式的資料分割數目） 新增相關的中繼資料作業，並針對每個資料分割要求/回應之間的資料分割領導者和其粉絲有額外負荷。 即使是在通過的資料流不存在，磁碟分割複本仍會向頂尖人士，這會導致傳送額外的處理和透過網路接收要求，擷取資料。

Apache Kafka 叢集 1.1 和上述在 HDInsight，我們建議您擁有最多 1000 個分割區，每個訊息代理程式，包括複本。 增加每個訊息代理程式的資料分割數目會減少輸送量，並可能造成主題無法使用。 如需有關 Kafka 分割區支援的詳細資訊，請參閱[1.1.0 版中支援的資料分割數目的增加上官方的 Apache Kafka 部落格文章](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions)。 如需修改主題的詳細資訊，請參閱[Apache Kafka： 修改主題](https://kafka.apache.org/documentation/#basic_ops_modify_topic)。

### <a name="number-of-replicas"></a>複本數目

更高版本的複寫因素會導致其他要求之間的資料分割指引和粉絲。 因此，較高的複寫因子耗用更多的磁碟和 CPU 處理其他要求，增加寫入延遲及減少輸送量。

我們建議您在 Azure HDInsight 的 kafka 使用至少 3 倍複寫。 大部分的 Azure 區域有三個容錯網域，但在區域只有兩個容錯網域，使用者應該使用 4 倍複寫。

如需有關複寫的詳細資訊，請參閱 < [Apache Kafka： 複寫](https://kafka.apache.org/documentation/#replication)並[Apache Kafka： 增加複寫因子](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor)。

## <a name="next-steps"></a>後續步驟

* [在 Azure 上使用 Apache Kafka 每日處理數兆個事件](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [什麼是 HDInsight 上的 Apache Kafka？](apache-kafka-introduction.md)
