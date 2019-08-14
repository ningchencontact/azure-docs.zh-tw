---
title: Azure HDInsight 的版本資訊
description: Azure HDInsight 的最新版本資訊。 取得 Hadoop、Spark、R Server、Hive 等的開發秘訣和詳細資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 7c31520efd881e8e0b5ed309f62d273bac59c0e3
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945021"
---
# <a name="release-notes"></a>版本資訊

本文提供有關**最新** Azure HDInsight 版本更新的資訊。 如需有關較早版本的詳細資訊，請參閱 [HDInsight 版本資訊封存](hdinsight-release-notes-archive.md)。

> [!IMPORTANT]  
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [HDInsight 版本控制文件](hdinsight-component-versioning.md)。

## <a name="summary"></a>總結

Azure HDInsight 是最受企業客戶歡迎的其中一項服務，可供 Azure 上的開放原始碼 Apache Hadoop 與 Apache Spark 分析使用。

## <a name="new-features"></a>新功能

如需有關 HDInsight 4.0 之重要變更的詳細資訊, 請參閱[HDI 4.0 的新功能](../hdinsight/hdinsight-version-release.md)。

## <a name="component-versions"></a>元件版本

以下提供所有 HDInsight 4.0 元件的官方 Apache 版本。 列出的元件是可用的最新穩定版本的版本。

- Apache Ambari 2.7.1
- Apache Hadoop 3.1。1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1、2.1。0
- Apache Mahout 0.9.0+
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Apache Ranger 0.7.0
- Apache Slider 0.92.0
- Apache Spark 2.3.1、2.4。0
- Apache Sqoop 1.4.7
- Apache TEZ 0.9.1
- Apache Zeppelin 0.8.0 版
- Apache ZooKeeper 3.4.6

較新版本的 Apache 元件有時會在 HDP 散發套件中, 除了上列的版本外。 在此情況下，這些更新版本會列在「技術預覽」資料表中，且不應該在生產環境中取代上述清單中的 Apache 元件版本。

## <a name="apache-patch-information"></a>Apache 修補程式資訊

如需 HDInsight 4.0 中可用修補程式的詳細資訊, 請參閱下表中每個產品的修補程式清單。

| 產品名稱 | 修補程式資訊 |
|---|---|
| Ambari | [Ambari 修補程式資訊](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Hadoop 修補程式資訊](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| HBase | [HBase 修補程式資訊](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | 此版本提供 Hive 3.1.0, 沒有其他 Apache 修補程式。  |
| Kafka | 此版本提供 Kafka 1.1.1, 沒有其他 Apache 修補程式。 |
| Oozie | [Oozie 修補程式資訊](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Phoenix 修補程式資訊](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Pig 修補程式資訊](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Ranger 修補程式資訊](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Spark 修補程式資訊](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | 此版本提供 Sqoop 1.4.7, 沒有其他 Apache 修補程式。 |
| Tez | 此版本提供 Tez 0.9.1, 沒有其他 Apache 修補程式。 |
| Zeppelin | 此版本提供 Zeppelin 0.8.0 版, 沒有其他 Apache 修補程式。 |
| Zookeeper | [Zookeeper 修補程式資訊](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>修正常見弱點與漏洞

如需此版本中已解決之安全性問題的詳細資訊, 請參閱 Hortonworks ' 已[修正 HDP 3.0.1 的常見弱點和曝光](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html)。

## <a name="known-issues"></a>已知問題

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>使用預設安裝的安全 HBase 的複寫中斷

針對 HDInsight 4.0, 請執行下列步驟:

1. 啟用叢集間通訊。
1. 登入作用中的前端節點。
1. 下載腳本, 以使用下列命令來啟用複寫:

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. 輸入命令`sudo kinit <domainuser>`。
1. 輸入下列命令來執行腳本:

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
針對 HDInsight 3.6, 請執行下列動作:

1. 登入 active HMaster ZK。
1. 下載腳本, 以使用下列命令來啟用複寫:
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. 輸入命令`sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`。
1. 輸入以下命令：

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>將 HBase 叢集遷移至 HDInsight 4.0 之後, Phoenix Sqlline 會停止運作

請執行下列步驟：

1. 捨棄下列 Phoenix 資料表:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. 如果您無法刪除任何資料表, 請重新開機 HBase 以清除資料表的任何連接。
1. 再次執行 `sqlline.py`。 Phoenix 將會重新建立在步驟1中刪除的所有資料表。
1. 重新產生 HBase 資料的 Phoenix 資料表和 views。

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>從 HDInsight 3.6 將 HBase Phoenix 中繼資料複寫到4.0 之後, Phoenix Sqlline 會停止運作

請執行下列步驟：

1. 執行複寫之前, 請移至目的地4.0 叢集並執行`sqlline.py`。 此命令將會產生 Phoenix 資料表`SYSTEM.MUTEX` , `SYSTEM.LOG`例如, 且只存在於4.0。
1. 捨棄下列資料表:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. 啟動 HBase 複寫

## <a name="deprecation"></a>淘汰

HDInsight 4.0 不提供 Apache Storm 和 ML 服務。
