---
title: Azure HDInsight 增強式寫入 Apache hbase （預覽）
description: 提供 Azure HDInsight 的增強式寫入功能，以改善效能的 Apache HBase 寫入繼續記錄檔中使用進階受控磁碟的概觀。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 3/27/2019
ms.openlocfilehash: b0c71d0f101ea262b6ce56c845ef9f375a7de85e
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804202"
---
# <a name="azure-hdinsight-enhanced-writes-for-apache-hbase-preview"></a>Azure HDInsight 增強式寫入 Apache hbase （預覽）

這篇文章提供背景**增強式寫入**Azure HDInsight 中的 Apache HBase 的功能及如何它可有效改善寫入效能。 **增強式寫入**會使用[Azure 進階 SSD 受控磁碟](../../virtual-machines/linux/disks-types.md#premium-ssd)來改善效能的 Apache HBase 寫入繼續記錄 (WAL)。 若要深入了解 Apache HBase，請參閱[什麼是 HDInsight 中的 Apache HBase](apache-hbase-overview.md)。

## <a name="overview-of-hbase-architecture"></a>HBase 架構的概觀

在 HBase 中，**資料列**包含一個或多個**資料行**並由識別**資料列索引鍵**。 多個資料列組成**資料表**。 資料行包含**儲存格**，這是時間戳記的版本中該資料行的值。 資料行可分為**資料行系列**，和所有資料行的資料行系列會一起儲存在儲存體的檔案，呼叫**HFiles**。

**區域**在 HBase 中用來平衡的資料處理負載。 HBase 第一次儲存在單一區域中的 資料表資料列，然後將跨多個區域的資料列分散的少量資料，在此資料表會增加。 **區域伺服器**可以處理多個區域的要求。

## <a name="write-ahead-log-for-apache-hbase"></a>預寫記錄檔對 Apache HBase

在 HBase 中的資料更新會先寫入呼叫寫入繼續記錄 (WAL) 認可記錄的類型。 更新會儲存在 WAL 之後，它會寫入至記憶體中**MemStore**。 當記憶體中的資料達到其最大容量時，它已寫入磁碟**HFile**。

如果**RegionServer**損毀或無法寫入繼續記錄檔的 MemStore 排清之前，可用來重新執行更新。 沒有 WAL，如果**RegionServer**排清的更新之前損毀**HFile**，所有這些遺失的更新。

## <a name="enhanced-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>增強型的 Azure HDInsight 的 Apache HBase 中的寫入功能

「 增強式寫入 」 功能可解決較高寫-延遲使用預寫記錄檔中的雲端儲存體所造成的問題。  HDInsight 的 Apache HBase 叢集的增強式寫入功能，將進階 SSD 將受控磁碟到每個 RegionServer （背景工作節點）。 撰寫繼續記錄檔接著會寫入至 Hadoop 檔案系統 (HDFS) 已掛上這些進階受控磁碟，而非雲端儲存體。  進階受控的磁碟使用固態硬碟 (Ssd)，並具有容錯功能提供了絕佳的 I/O 效能。  不同於非受控磁碟，一個儲存體單位會關閉，如果它不會影響在相同的可用性設定組中的其他儲存體單位。  如此一來，受控的磁碟可提供低寫入延遲和更佳的恢復功能，您的應用程式。 若要深入了解 Azure 受控磁碟，請參閱[Introduction to Azure 受控磁碟](../../virtual-machines/windows/managed-disks-overview.md)。 

## <a name="how-to-enable-enhanced-writes-for-hbase-in-hdinsight"></a>如何啟用增強式寫入在 HDInsight 中的 hbase

若要建立新的 HBase 叢集使用增強式寫入功能，請遵循[在 HDInsight 中設定叢集](../hdinsight-hadoop-provision-linux-clusters.md)直到您到達**步驟 3、 儲存體**。 底下**中繼存放區設定**，按一下旁邊的核取方塊**啟用增強式寫入 （預覽）**。 然後，繼續進行其餘步驟來建立叢集。

![啟用增強式的寫入選項適用於 HDInsight 的 Apache HBase](./media/apache-hbase-enhanced-writes/enhanced-writes-cluster-creation.jpg)

## <a name="other-considerations"></a>其他考量

若要保留的資料持續性，請使用至少三個背景工作角色節點建立叢集。 建立之後，您不能相應減少到少於三個背景工作節點叢集。 

排清或之前刪除叢集，停用 HBase 資料表，使您不會遺失預先寫入記錄檔資料。

```
flush 'mytable'
```

```
disable 'mytable'
```

## <a name="next-steps"></a>後續步驟

* 正式的 Apache HBase 文件上[預先寫入記錄檔功能](https://hbase.apache.org/book.html#wal)
* 若要升級您的 HDInsight 的 Apache HBase 叢集，以使用增強式寫入，請參閱[移轉至新版本的 Apache HBase 叢集](apache-hbase-migrate-new-version.md)。
