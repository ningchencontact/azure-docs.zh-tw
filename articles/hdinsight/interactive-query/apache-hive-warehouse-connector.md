---
title: 將 Apache Spark 和 Apache Hive 與 Hive 倉儲連接器整合
description: 瞭解如何在 Azure HDInsight 上將 Apache Spark 和 Apache Hive 與 Hive 倉儲連接器整合。
ms.service: hdinsight
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 98b97abf9fe0d8e71f93022dcbfeff0a5339d5a1
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69983072"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>將 Apache Spark 和 Apache Hive 與 Hive 倉儲連接器整合

Apache Hive 倉儲連接器 (HWC) 是一種程式庫, 可讓您更輕鬆地使用 Apache Spark 和 Apache Hive, 方法是支援在 Spark 資料框架和 Hive 資料表之間移動資料, 以及將 Spark 串流資料導向 Hive 資料表的工作。 Hive 倉儲連接器的運作方式類似于 Spark 和 Hive 之間的橋樑。 它支援 Scala、JAVA 和 Python 以進行開發。

Hive 倉儲連接器可讓您利用 Hive 和 Spark 的獨特功能, 來建立功能強大的大型資料應用程式。 Apache Hive 提供不可部分完成、一致、隔離且持久 (ACID) 的資料庫交易支援。 如需 Hive 中 ACID 和交易的詳細資訊, 請參閱[Hive 交易](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions)。 Hive 也會透過 Apache Ranger 提供詳細的安全性控制, 且 Apache Spark 不提供低延遲分析處理。

Apache Spark, 具有結構化串流 API, 提供 Apache Hive 中無法使用的串流功能。 從 HDInsight 4.0 開始, Apache Spark 2.3.1 和 Apache Hive 3.1.0 有個別的中繼存放區, 這可能會使互通性變得很棘手。 Hive 倉儲連接器可讓您更輕鬆地搭配使用 Spark 和 Hive。 HWC 程式庫會以平行方式將 LLAP 守護程式中的資料載入 Spark 執行程式, 使其更有效率且更具擴充性, 而不是使用從 Spark 到 Hive 的標準 JDBC 連接。

![架構](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Hive 倉儲連接器所支援的部分作業包括:

* 描述資料表
* 建立 ORC 格式資料的資料表
* 選取 Hive 資料並抓取資料框架
* 在 batch 中將資料框架寫入 Hive
* 執行 Hive update 語句
* 從 Hive 讀取資料表資料、在 Spark 中轉換它, 並將它寫入至新的 Hive 資料表
* 使用 HiveStreaming 將資料框架或 Spark 串流寫入 Hive

## <a name="hive-warehouse-connector-setup"></a>Hive 倉儲連接器設定

請遵循下列步驟, 在 Azure HDInsight 中的 Spark 和互動式查詢叢集之間設定 Hive 倉儲連接器:

1. 使用 Azure 入口網站搭配儲存體帳戶和自訂 Azure 虛擬網路來建立 HDInsight Spark 4.0 叢集。 如需在 Azure 虛擬網路中建立叢集的詳細資訊, 請參閱[將 HDInsight 新增至現有的虛擬網路](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)。
1. 使用 Azure 入口網站搭配與 Spark 叢集相同的儲存體帳戶和 Azure 虛擬網路, 建立 HDInsight 互動式查詢 (LLAP) 4.0 叢集。
1. 將互動式查詢叢集 headnode0 `/etc/hosts`上的檔案內容複寫到 Spark 叢集 headnode0 上`/etc/hosts`的檔案。 此步驟可讓您的 Spark 叢集解析互動式查詢叢集中的節點 IP 位址。 使用`cat /etc/hosts`來查看已更新檔案的內容。 輸出應該會看起來像下面螢幕擷取畫面中所示的內容。

    ![查看 hosts 檔案](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. 執行下列步驟來設定 Spark 叢集設定: 
    1. 移至 Azure 入口網站, 選取 [HDInsight 叢集], 然後按一下您的叢集名稱。
    1. 在右側的 [叢集**儀表板**] 底下, 選取 [ **Ambari home**]。
    1. 在 Ambari web UI 中, 按一下 [ **SPARK2**  >   > ] [SPARK2] [**自訂] [預設值**]。

        ![Spark2 Ambari 設定](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. 將`spark.hadoop.hive.llap.daemon.service.hosts`設定為與位於 * * Advanced hive-interactive-site * * 之下的**llap**屬性相同的值。 例如： `@llap0`

    1. 設定`spark.sql.hive.hiveserver2.jdbc.url`為 JDBC 連接字串, 它會連接到互動式查詢叢集上的 Hiveserver2。 叢集的連接字串看起來會像下面的 URI。 `CLUSTERNAME`是您的 Spark 叢集名稱, `user`而和`password`參數會設定為叢集的正確值。

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        >[!Note] 
        > JDBC URL 應該包含用來連接到 Hiveserver2 的認證, 包括使用者名稱和密碼。

    1. 將`spark.datasource.hive.warehouse.load.staging.dir`設定為適當的 HDFS 相容臨時目錄。 如果您有兩個不同的叢集, 則臨時目錄應該是 LLAP 叢集之儲存體帳戶的預備目錄中的資料夾, 讓 HiveServer2 有其存取權。 例如, `wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`其中`STORAGE_ACCOUNT_NAME`是叢集所使用的儲存體帳戶名稱, 而`STORAGE_CONTAINER_NAME`是儲存體容器的名稱。

    1. 使用`spark.datasource.hive.warehouse.metastoreUri`互動式查詢叢集的中繼存放區 URI 值來設定。 若要尋找 LLAP 叢集的 metastoreUri, 請在 [ **hive**  >  **ADVANCED**  >  **General**] 下的 LLAP 叢集的 Ambari UI 中尋找 [ **hive. 中繼存放區. uri** ] 屬性。 值看起來會像下列 URI:

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. 針對`spark.security.credentials.hiveserver2.enabled` YARN `false`用戶端部署模式, 將設為。
    1. 設定`spark.hadoop.hive.zookeeper.quorum`為 LLAP 叢集的 Zookeeper 仲裁。 若要尋找 LLAP 叢集的 Zookeeper 仲裁, 請在**hive**  >  **advanced**  >  **advanced hive-site**底下的 LLAP 叢集的 Ambari UI 中, 尋找**Zookeeper. 仲裁**屬性。 值看起來會像下列字串:

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

若要測試 Hive 倉儲連接器的設定, 請依照[連接和執行查詢](#connecting-and-running-queries)一節中的步驟進行。

## <a name="using-the-hive-warehouse-connector"></a>使用 Hive 倉儲連接器

### <a name="connecting-and-running-queries"></a>連接和執行查詢

您可以選擇幾種不同的方法來連接到您的互動式查詢叢集, 並使用 Hive 倉儲連接器執行查詢。 支援的方法包括下列工具:

* [spark-shell](../spark/apache-spark-shell.md)
* PySpark
* spark-提交
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

本文中提供的所有範例都是透過 spark shell 來執行。

若要啟動 spark shell 會話, 請執行下列步驟:

1. 透過 SSH 連線到叢集的前端節點。 如需使用 SSH 連線到您的叢集的詳細資訊, 請參閱[使用 ssh 連線到 HDInsight (Apache Hadoop)](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)。
1. 輸入`cd /usr/hdp/current/hive_warehouse_connector`或提供在 spark shell 命令中當做參數使用之所有 jar 檔案的完整路徑, 以變更為正確的目錄。
1. 輸入下列命令以啟動 spark shell:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. 您會看到歡迎使用的訊息, `scala>`以及您可以在其中輸入命令的提示。

1. 啟動 spark shell 之後, 您可以使用下列命令來啟動 Hive 倉儲連接器實例:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>在企業安全性套件 (ESP) 叢集上連接及執行查詢

企業安全性套件 (ESP) 為 Azure HDInsight 中的 Apache Hadoop 叢集, 提供企業級的功能, 例如 Active Directory 型驗證、多使用者支援和角色型存取控制。 如需 ESP 的詳細資訊, 請參閱[在 HDInsight 中使用企業安全性套件](../domain-joined/apache-domain-joined-architecture.md)。

1. 遵循[連接和執行查詢](#connecting-and-running-queries)底下的初始步驟1和2。
1. 輸入`kinit`網域使用者並登入。
1. 使用完整的設定參數清單來啟動 spark-shell, 如下所示。 在角括弧之間的所有大寫字母中的所有值都必須根據您的叢集來指定。 如果您需要找出下列任何參數的輸入值, 請參閱[Hive 倉儲連接器設定](#hive-warehouse-connector-setup)上的一節:

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

### <a name="creating-spark-dataframes-from-hive-queries"></a>從 Hive 查詢建立 Spark 資料框架

所有使用 HWC 程式庫之查詢的結果都會以資料框架的形式傳回。 下列範例示範如何建立基本查詢。

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

查詢的結果是 Spark 資料框架, 可搭配 Spark 程式庫 (例如 MLIB 和 SparkSQL) 使用。

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>將 Spark 資料框架寫出至 Hive 資料表

Spark 原本就不支援寫入 Hive 的受控 ACID 資料表。 不過, 您可以使用 HWC 將任何資料框架寫出至 Hive 資料表。 您可以在下列範例中看到這項功能的運作方式:

1. 建立名`sampletable_colorado`為的資料表, 並使用下列命令來指定其資料行:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

2. 篩選資料表`hivesampletable` , 其中的資料`state`行`Colorado`等於。 此 Hive 資料表的查詢會以 Spark 資料框架的形式傳回。 然後, 資料框架會`sampletable_colorado` `write`使用函數儲存在 Hive 資料表中。
    
    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

您可以在下列螢幕擷取畫面中看到產生的資料表。

![顯示產生的資料表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>結構化串流寫入

使用 Hive 倉儲連接器, 您可以使用 Spark 串流將資料寫入 Hive 資料表。

請遵循下列步驟來建立 Hive 倉儲連接器範例, 以從 localhost 埠9999上的 Spark 資料流程將資料內嵌至 Hive 資料表。

1. 在 Spark 叢集上開啟終端機。
1. 使用下列命令來開始 spark 串流:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. 執行下列步驟, 為您建立的 Spark 資料流程產生資料:
    1. 在相同的 Spark 叢集上開啟另一個終端機。
    1. 在命令提示字元中，輸入 `nc -lk 9999`。 此命令會使用 netcat 公用程式, 從命令列將資料傳送至指定的埠。
    1. 輸入您想要 Spark 串流內嵌的單字, 後面接著 [回車]。
        ![將資料輸入至 spark 資料流程](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)
1. 建立新的 Hive 資料表來保存串流資料。 在 spark shell 中, 輸入下列命令:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. 使用下列命令, 將串流資料寫入至新建立的資料表:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > 因為 Apache Spark `database`中的已知問題,所以目前必須手動設定和選項。`metastoreUri` 如需有關此問題的詳細資訊, 請參閱[SPARK-25460](https://issues.apache.org/jira/browse/SPARK-25460)。

1. 您可以使用下列命令來查看插入資料表的資料:

    ```scala
    hive.table("stream_table").show()
    ```

### <a name="securing-data-on-spark-esp-clusters"></a>保護 Spark ESP 叢集上的資料

1. 輸入下列命令`demo`來建立包含一些範例資料的資料表:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. 使用下列命令來查看資料表的內容。 套用原則之前, `demo`資料表會顯示完整的資料行。

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![套用 ranger 原則之前的示範表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. 套用只顯示資料行最後四個字元的資料行遮罩原則。  
    1. 移至位於`https://CLUSTERNAME.azurehdinsight.net/ranger/`的 Ranger 管理 UI。
    1. 按一下 [ **hive**] 底下叢集的 hive 服務。
        ![套用 ranger 原則之前的示範表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. 按一下 [**遮罩**] 索引標籤, 然後 **新增原則** ![清單](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)
    1. 提供所需的原則名稱。 選取資料庫:**預設值**, hive 資料表:**示範**, hive 資料行:**名稱**, 使用者: **rsadmin2**, 存取類型:**選取**, 以及部分遮罩: 從**選取遮罩選項**功能表**顯示最後4個**。 按一下 [新增]。
                ![原則清單](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. 再次查看資料表的內容。 套用 ranger 原則之後, 我們只會看到資料行的最後四個字元。

    ![套用 ranger 原則後的示範資料表](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>後續步驟

* [搭配 HDInsight 使用互動式查詢](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [使用 Zeppelin、Livy、spark-submit 和 pyspark 與 Hive 倉儲連接器互動的範例](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
