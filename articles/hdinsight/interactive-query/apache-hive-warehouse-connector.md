---
title: 整合 Apache Spark 和 Apache Hive 與 Hive 倉儲連接器
description: 了解如何整合 Hive Warehouse 連接器，在 Azure HDInsight 上的 Apache Spark 和 Apache Hive。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/18/2019
ms.openlocfilehash: f5c4b07326bfd469720ab07b522aefb9ed074cbb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60484724"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>整合 Apache Spark 和 Apache Hive 與 Hive 倉儲連接器

Apache Hive 倉儲連接器 (HWC) 是可讓您能夠更輕鬆地使用 Apache Spark 和 Apache Hive 所支援的工作，例如 Spark 資料框架和 Hive 資料表之間移動資料，以及也將 Spark 串流至 Hive 資料表的資料導向的程式庫。 Hive Warehouse 連接器的運作方式類似 Spark 和 Hive 之間的橋樑。 它支援 Scala、 Java 和 Python 進行開發。

Hive 倉儲連接器可讓您充分利用的 Hive 和 Spark 來建置功能強大的巨量資料應用程式的獨特功能。 Apache Hive 提供是不可部分完成、 一致、 隔離及持久 (ACID) 的資料庫交易的支援。 如需 ACID 的詳細資訊和在 Hive 中的交易，請參閱[Hive 交易](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions)。 Hive 也提供詳細的安全性控制項，透過 Apache Ranger 和低延遲分析處理 Apache Spark 中無法使用。

Apache Spark 有結構化串流 API，可讓資料流中的功能無法使用 Apache Hive。 從 Hortonworks Data Platform (HDP) 3.0 與，Apache Spark 和 Apache Hive 可以個別中繼存放區，它只能進行困難的互通性。 Hive 倉儲 」 連接器可讓您同時使用 Spark 和 Hive 的工作變得更容易。 HWC 程式庫載入到 Spark 執行程式以平行方式，讓它更有效率且可調整比使用標準 JDBC 連接，從 Spark 到 Hive LLAP 精靈資料。

![架構](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Hive 倉儲連接器所支援的作業包括：

* 描述資料表
* 建立 ORC 格式的資料的資料表
* 選取 Hive 資料和擷取的資料框架
* 資料框架寫入批次中的 Hive
* 執行 Hive update 陳述式
* 讀取資料表資料從 Hive、 轉換在 Spark 中，以及寫入至新的 Hive 資料表
* 寫入使用 HiveStreaming Hive 中的資料框架或 Spark 串流

## <a name="hive-warehouse-connector-setup"></a>Hive Warehouse 連接器安裝程式

請遵循下列步驟來設定 Hive 倉儲之間的連接器在 Azure HDInsight Spark 和互動式查詢叢集：

1. 建立 HDInsight Spark 4.0 叢集與儲存體帳戶和自訂的 Azure 虛擬網路中使用 Azure 入口網站。 如需在 Azure 虛擬網路中建立叢集的詳細資訊，請參閱[新增至現有的虛擬網路的 HDInsight](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md#existingvnet)。
1. 建立 HDInsight 互動式查詢 (LLAP) 4.0 叢集使用 Azure 入口網站的相同儲存體帳戶和 Azure 虛擬網路與 Spark 叢集。
1. 複製的內容`/etc/hosts`檔案上的互動式查詢叢集的 headnode0`/etc/hosts`檔案，您的 Spark 叢集的 headnode0。 此步驟可讓您的 Spark 叢集，若要解決的互動式查詢叢集中節點的 IP 位址。 檢視與更新之檔案的內容`cat /etc/hosts`。 輸出應該看起來像下列螢幕擷取畫面中所示。

    ![檢視主機檔案](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. 設定 Spark 叢集設定執行下列步驟： 
    1. 前往 Azure 入口網站選取 HDInsight 叢集，然後按一下您的叢集名稱。
    1. 在右側，底下**叢集儀表板**，選取**Ambari 首頁**。
    1. 在 Ambari web UI 中，按一下**SPARK2** > **CONFIGS** > **自訂 spark2-defaults**。

        ![Spark2 Ambari 組態](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. 設定`spark.hadoop.hive.llap.daemon.service.hosts`相同的值當做屬性**LLAP 應用程式名稱**之下**進階 hive 互動式 env**。 例如， `@llap0`

    1. 設定`spark.sql.hive.hiveserver2.jdbc.url`JDBC 連接字串中，它會連線到 Hiveserver2 互動式查詢叢集上。 您的叢集的連接字串看起來類似下列 URI。 `CLUSTERNAME` 您的 Spark 叢集的名稱和`user`和`password`參數會設定為正確的值，為您的叢集。

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        >[!Note] 
        > JDBC URL 應包含認證連線到 Hiveserver2 包括使用者名稱和密碼。

    1. 設定`spark.datasource.hive.warehouse.load.staging.dir`到適合的 HDFS 相容暫存目錄。 如果您有兩個不同的叢集時，暫存目錄應該是 LLAP 叢集的儲存體帳戶的暫存目錄中的資料夾，使 HiveServer2 具有其存取權。 比方說，`wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`何處`STORAGE_ACCOUNT_NAME`是由叢集使用的儲存體帳戶名稱和`STORAGE_CONTAINER_NAME`是儲存體容器的名稱。

    1. 設定`spark.datasource.hive.warehouse.metastoreUri`中繼存放區的互動式查詢叢集的 URI 值。 若要尋找 metastoreUri LLAP 叢集，尋求**hive.metastore.uris**屬性，在 Ambari UI 中用於您 LLAP 叢集底下**Hive** > **進階**  > **一般**。 值看起來會像下列 URI:

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. 設定`spark.security.credentials.hiveserver2.enabled`至`false`YARN 用戶端的部署模式。
    1. 設定`spark.hadoop.hive.zookeeper.quorum`LLAP 叢集的 Zookeeper 仲裁。 若要尋找 LLAP 叢集的 Zookeeper 仲裁，尋找**hive.zookeeper.quorum**屬性在 Ambari UI 中用於您 LLAP 叢集底下**Hive** > **進階** > **進階 hive 網站**。 值看起來會像下列字串：

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

若要測試您的 Hive 的倉儲連接器的組態，請遵循一節中的步驟[連線且正在執行的查詢](#connecting-and-running-queries)。

## <a name="using-the-hive-warehouse-connector"></a>使用 Hive 倉儲連接器

### <a name="connecting-and-running-queries"></a>連線並執行查詢

您可以選擇幾個不同的方法，來連線到互動式查詢叢集及執行使用 Hive 倉儲連接器的查詢。 支援的方法包括下列工具：

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

這篇文章中所提供的所有範例將會透過 spark shell 都執行。

若要啟動 spark 殼層工作階段，請執行下列步驟：

1. 透過 ssh 連線到您的叢集前端節點。 如需有關如何連接到您的叢集使用 SSH 的詳細資訊，請參閱 <<c0> [ 連接到 HDInsight (Apache Hadoop) 使用 SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。
1. 變更為正確的目錄中，輸入`cd /usr/hdp/current/hive_warehouse_connector`或提供 spark shell 命令中當做參數使用的所有 jar 檔案的完整路徑。
1. 輸入下列命令以啟動 spark 殼層：

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. 您會看到歡迎訊息和`scala>`您可以在其中輸入命令提示字元。

1. 啟動 spark 殼層之後, 的 Hive Warehouse 連接器執行個體可以開始使用下列命令：

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>連線並在企業安全性套件 」 (ESP) 叢集上執行查詢

企業安全性套件 (ESP) 提供企業級功能，例如 Active Directory 為基礎的驗證、 多使用者支援和 Azure HDInsight 中的 Apache Hadoop 叢集的角色型存取控制。 如需有關 ESP 的詳細資訊，請參閱[使用企業安全性套件的 Apache Hadoop 安全性簡介](../domain-joined/apache-domain-joined-introduction.md)。

1. 請遵循的初始步驟 1 和 2 下的[連線且正在執行的查詢](#connecting-and-running-queries)。
1. 型別`kinit`並使用網域使用者登入。
1. 啟動 spark 殼層，如下所示的組態參數的完整清單。 所有以角括弧之間的所有大寫字母的值必須根據您的叢集指定。 如果您需要了解的任何下列參數輸入值，請參閱區段[Hive Warehouse 連接器安裝程式](#hive-warehouse-connector-setup)。:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>從 Hive 查詢中建立 Spark 資料框架

使用 HWC 程式庫的所有查詢的結果會傳回資料框架的方式。 下列範例示範如何建立基本查詢。

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

查詢的結果是 Spark 資料框架，可以搭配 Spark 程式庫，例如 MLIB 和 SparkSQL。

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>寫出 Spark 資料框架至 Hive 資料表

Spark 原生不支援寫入至 Hive 的受管理的 ACID 資料表。 使用 HWC，不過，您可以寫出到 Hive 資料表的任何資料框架。 您可以看到這項功能在下列範例中的工作：

1. 建立資料表，稱為`sampletable_colorado`並指定其資料行，使用下列命令：

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

2. 篩選資料表`hivesampletable`其中的資料行`state`等於`Colorado`。 此 Hive 資料表的查詢會傳回 Spark 資料框架的方式。 然後 Hive 資料表中儲存之資料框架`sampletable_colorado`使用`write`函式。
    
    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

您可以看到下面的螢幕擷取畫面中產生的資料表。

![顯示產生的資料表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>結構化串流寫入

使用 Hive 倉儲連接器，您可以使用 Spark 串流將資料寫入至 Hive 資料表。

請遵循下列步驟來建立內嵌資料從 Spark 串流至 Hive 資料表的 localhost 連接埠 9999 上的 Hive Warehouse 連接器範例。

1. 開啟您的 Spark 叢集上的終端機。
1. 首先，spark 串流，使用下列命令：

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. 產生資料的 Spark 串流，您建立，請執行下列步驟：
    1. 開啟另一個相同的 Spark 叢集上的終端機。
    1. 在命令提示字元中，輸入 `nc -lk 9999`。 此命令會使用 netcat 公用程式，將從命令列的資料傳送至指定的連接埠。
    1. 輸入您想要擷取，Spark 串流的單字，後面接著歸位字元。
        ![spark 串流的輸入的資料](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)
1. 建立新的 Hive 資料表來保存資料流處理的資料。 在 spark shell 中，輸入下列命令：

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. 資料流資料寫入新建立的資料表，使用下列命令：

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > `metastoreUri`和`database`選項必須目前手動設定由於 Apache Spark 的已知問題。 如需有關此問題的詳細資訊，請參閱 < [SPARK 25460](https://issues.apache.org/jira/browse/SPARK-25460)。

1. 您可以檢視資料插入資料表中，使用下列命令：

    ```scala
    hive.table("stream_table").show()
    ```

### <a name="securing-data-on-spark-esp-clusters"></a>ESP Spark 叢集上的保護資料

1. 建立資料表`demo`的一些範例資料中輸入下列命令：

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. 檢視資料表的內容，使用下列命令。 套用原則之前,`demo`資料表會顯示完整的資料行。

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![示範資料表之後才套用 ranger 原則](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. 適用於遮罩原則，只會顯示四個字元的資料行的資料行。  
    1. 移至 Ranger 系統管理 UI，在`https://CLUSTERNAME.azurehdinsight.net/ranger/`。
    1. 按一下您的叢集的 Hive 服務**Hive**。
        ![示範資料表之後才套用 ranger 原則](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. 按一下 **遮罩**索引標籤，然後**新增新的原則**![原則清單](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)
    1. 提供所需的原則名稱。 選取資料庫：**預設值**，Hive 資料表：**示範**，Hive 資料行：**名稱**，使用者： **rsadmin2**，存取類型：**選取**，及**部分遮罩： 顯示最後 4**從**選取遮罩選項**功能表。 按一下 [新增] 。
                ![原則清單](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. 再次檢視資料表的內容。 Ranger 原則的套用之後, 我們可以看到資料行的最後四個字元。

    ![示範資料表之後套用 ranger 原則](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>後續步驟

* [搭配 HDInsight 使用互動式查詢](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [使用 Zeppelin，Livy，Hive 倉儲連接器與互動的範例提交 spark 和 pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
