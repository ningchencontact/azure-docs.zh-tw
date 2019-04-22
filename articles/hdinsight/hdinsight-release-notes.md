---
title: Azure HDInsight 的版本資訊
description: Azure HDInsight 的最新版本資訊。 取得 Hadoop、 Spark、 R Server、 Hive 和更多開發秘訣和詳細資料。
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 5769f90ef69a82497194ff6de01b378acc84deec
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678377"
---
# <a name="release-notes-for-azure-hdinsight"></a>Azure HDInsight 的版本資訊

本文提供有關**最新** Azure HDInsight 版本更新的資訊。 如需有關較早版本的詳細資訊，請參閱 [HDInsight 版本資訊封存](hdinsight-release-notes-archive.md)。

> [!IMPORTANT]  
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [HDInsight 版本控制文件](hdinsight-component-versioning.md)。

## <a name="summary"></a>總結

Azure HDInsight 是最受企業客戶歡迎的其中一項服務，可供 Azure 上的開放原始碼 Apache Hadoop 與 Apache Spark 分析使用。

## <a name="new-features"></a>新功能

如需有關 HDInsight 4.0 的重大變更。，請參閱[HDI 4.0 中最新消息？](../hdinsight/hdinsight-version-release.md)。

## <a name="component-versions"></a>元件版本

下面提供的官方 Apache 新版 HDInsight 4.0 的所有元件 列出的元件是提供的最新穩定版本的版本。

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1
- Apache Mahout 0.9.0+
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Apache Ranger 0.7.0
- Apache Slider 0.92.0
- Apache Spark 2.3.1
- Apache Sqoop 1.4.7
- Apache TEZ 0.9.1
- Apache Zeppelin 0.8.0
- Apache ZooKeeper 3.4.6

除了上面所列版本的 HDP 發佈中有時組合的 Apache 元件的更新版本。 在此情況下，這些更新版本會列在「技術預覽」資料表中，且不應該在生產環境中取代上述清單中的 Apache 元件版本。

## <a name="apache-patch-information"></a>Apache 修補程式資訊

如需有關 HDInsight 4.0 中可用的修補程式的詳細資訊，請參閱每項產品，在下表中列出的修補程式。

| 產品名稱 | 修補程式資訊 |
|---|---|
| Ambari | [Ambari 修補程式資訊](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Hadoop 修補程式資訊](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| hbase | [HBase 修補程式資訊](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | 此版本提供 Hive 3.1.0 沒有其他 Apache 修補程式。  |
| Kafka | 此版本提供 Kafka 1.1.1 沒有其他 Apache 修補程式。 |
| Oozie | [Oozie 修補程式資訊](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Phoenix 修補程式資訊](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Pig 修補程式資訊](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Ranger 修補程式資訊](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Spark 修補程式資訊](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | 此版本提供 Sqoop 1.4.7 沒有其他 Apache 修補程式。 |
| Tez | 此版本提供 Tez 0.9.1 沒有其他 Apache 修補程式。 |
| Zeppelin | 此版本會提供 Zeppelin 0.8.0 沒有其他 Apache 修補程式。 |
| Zookeeper | [Zookeeper 修補程式資訊](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>修正常見弱點與漏洞

如需有關安全性問題已解決在此版本，請參閱 Hortonworks[固定的常見弱點與揭露的 HDP 3.0.1](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html)。

## <a name="known-issues"></a>已知問題

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>複寫是安全的 HBase 的預設安裝中斷

對於 HDInsight 4.0 中，執行下列步驟：

1. 啟用叢集間通訊。
1. 登入作用中前端節點。
1. 下載指令碼來啟用複寫使用下列命令：

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. 輸入命令`sudo kinit <domainuser>`。
1. 輸入下列命令來執行指令碼：

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
針對 HDInsight 3.6，執行下列作業：

1. 登入作用中的 HMaster ZK。
1. 下載指令碼來啟用複寫使用下列命令：
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. 輸入命令`sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`。
1. 輸入以下命令：

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>Phoenix Sqlline 移轉之後會停止運作到 HDInsight 4.0 的 HBase 叢集

請執行下列步驟：

1. 卸除下列 Phoenix 資料表：
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. 如果您無法刪除的任何資料表，重新啟動 HBase 來清除資料表的任何連線。
1. 再次執行 `sqlline.py`。 Phoenix 會重新建立所有已刪除在步驟 1 中的資料表。
1. 重新產生 Phoenix 資料表和檢視表，為您的 HBase 資料。

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>Phoenix Sqlline 後，從 HDInsight 3.6 的 HBase Phoenix 中繼資料複寫至 4.0 停止運作

請執行下列步驟：

1. 進行複寫，請移至目的地 4.0 叢集前後執行`sqlline.py`。 此命令會產生 Phoenix 資料表，如同`SYSTEM.MUTEX`和`SYSTEM.LOG`只存在於 4.0。
1. 卸除下列資料表：
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. 啟動 HBase 複寫

## <a name="deprecation"></a>淘汰

Apache Storm 和 ML 服務無法在 HDInsight 4.0。
