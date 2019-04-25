---
title: 使用 Spark 以讀取及寫入 HBase 資料 - Azure HDInsight
description: 使用 Spark HBase Connector 將資料從 Spark 叢集讀取及寫入至 HBase 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 200691f7af16e82d554d0e1e019b6a4e5c75949f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60484933"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>使用 Apache Spark 來讀取和寫入 Apache HBase 資料

Apache HBase 通常會使用其低階 API (scan、get、put) 或者使用 Apache Phoenix 以 SQL 語法來查詢。 Apache 也提供 Apache Spark HBase Connector，這是查詢及修改 HBase 儲存之資料的方便且高效能替代方式。

## <a name="prerequisites"></a>必要條件

* 两个独立的 HDInsight 群集、一个HBase、一个至少装有 Spark 2.1 (HDInsight 3.6) 的 Spark。
* Spark 叢集必須以最少的延遲直接與 HBase 叢集通訊，因此建議的設定是在相同的虛擬網路中同時部署兩個叢集。 如需詳細資訊，請參閱[使用 Azure 入口網站在 HDInsight 中建立以 Linux 為基礎的叢集](hdinsight-hadoop-create-linux-clusters-portal.md)。
* SSH 用戶端。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。
* 群集主存储的 [URI 方案](hdinsight-hadoop-linux-information.md#URI-and-scheme)。 這會是 wasb: / / Azure Blob 儲存體，abfs: / / Azure Data Lake 儲存體 Gen2 或 adl: / / Azure Data Lake 儲存體 Gen1。 如果为 Blob 存储或 Data Lake Storage Gen2 启用了安全传输，则 URI 分别是 wasbs:// 或 abfss://。另请参阅[安全传输](../storage/common/storage-require-secure-transfer.md)。


## <a name="overall-process"></a>整體程序

啟用您的 Spark 叢集以查詢 HDInsight 叢集的高階程序如下：

1. 在 HBase 中準備一些範例資料。
2. 從您的 HBase 叢集設定資料夾 (/etc/hbase/conf) 取得 hbase-site.xml 檔案。
3. 將 hbase-site.xml 的複本放置在您的 Spark 2 設定資料夾 (/etc/spark2/conf)。
4. 根據 `packages` 選項中的 Maven 座標，執行參照 Spark HBase Connector 的 `spark-shell`。
5. 定義目錄，該目錄將結構描述從 Spark 對應至 HBase。
6. 使用 RDD 或 DataFrame API 與 HBase 資料進行互動。

## <a name="prepare-sample-data-in-apache-hbase"></a>在 Apache HBase 中準備範例資料

在此步驟中，您會在 Apache HBase 中建立並填入簡單的資料表，然後您可以使用 Spark 查詢。

1. 使用 SSH 連線到 HBase 叢集的前端節點。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)。  编辑以下命令，将 `HBASECLUSTER` 替换为 HBase 群集的名称，将 `sshuser` 替换为 SSH 用户帐户名，然后输入该命令。

    ```
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. 输入以下命令，以启动 HBase shell：

        hbase shell

3. 输入以下命令，以创建包含列系列 `Personal` 和 `Office` 的 `Contacts` 表：

        create 'Contacts', 'Personal', 'Office'

4. 输入以下命令，以加载几行示例数据：

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
        put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
        put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'

5. 输入以下命令，以退出 HBase shell：

        exit 

## <a name="copy-hbase-sitexml-to-spark-cluster"></a>将 hbase-site.xml 复制到 Spark 群集
将 hbase-site.xml 从本地存储复制到 Spark 群集默认存储所在的根目录。  编辑以下命令以反映配置。  然后，在与 HBase 群集建立的 SSH 会话中输入该命令：

| 语法值 | 新增值|
|---|---|
|[URI 配置](hdinsight-hadoop-linux-information.md#URI-and-scheme) | 修改此值以反映存储。  以下语法适用于启用了安全传输的 Blob 存储。|
|`SPARK_STORAGE_CONTAINER`|替换为 Spark 群集使用的默认存储容器名称。|
|`SPARK_STORAGE_ACCOUNT`|替换为 Spark 群集使用的默认存储帐户名称。|

```
hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
```

## <a name="put-hbase-sitexml-on-your-spark-cluster"></a>將 hbase-site.xml 放置在您的 Spark 叢集

1. 使用 SSH 連線到 Spark 叢集的前端節點。

2. 输入以下命令，将 `hbase-site.xml` 从 Spark 群集的默认存储复制到群集本地存储上的 Spark 2 配置文件夹中：

        sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>執行參照 Spark HBase Connector 的 Spark Shell

1. 在与 Spark 群集建立的 SSH 会话中，输入以下命令以启动 Spark shell：

    ```
    spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/
    ```  

2. 讓此 Spark Shell 執行個體保持開啟，並且繼續進行下一個步驟。

## <a name="define-a-catalog-and-query"></a>定義目錄和查詢

在這個步驟中，您會定義目錄物件，該目錄物件將結構描述從 Apache Spark 對應至 Apache HBase。  

1. 在打开的 Spark Shell 中，输入以下 `import` 语句：

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

2. 输入以下命令，以定义在 HBase 中创建的 Contacts 表的目录：

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    该代码执行以下操作：  

     a. 針對名為 `Contacts` 的 HBase 資料表定義目錄結構描述。  
     b. 將 rowkey 識別為 `key`，並且將 Spark 中使用的資料行名稱對應至 HBase 中使用的資料行系列、資料行名稱以及資料行類型。  
     c. rowkey 也必須詳細定義為具名資料行 (`rowkey`)，其具有 `rowkey` 的特定資料行系列 `cf`。  

3. 输入以下命令，以定义一个在 HBase 中提供围绕 `Contacts` 表的 DataFrame 的方法：

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

4. 建立 DataFrame 的執行個體：

    ```scala
    val df = withCatalog(catalog)
    ```  

5. 查詢 DataFrame：

    ```scala
    df.show()
    ```

6. 您應該會看到兩個資料列的資料：

        +------+--------------------+--------------+-------------+--------------+
        |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
        +------+--------------------+--------------+-------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
        |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+-------------+--------------+

7. 註冊暫存資料表，以便使用 Spark SQL 查詢 HBase 資料表：

    ```scala
    df.createTempView("contacts")
    ```

8. 根據 `contacts` 資料表發出 SQL 查詢：

    ```scala
    val query = spark.sqlContext.sql("select personalName, officeAddress from contacts")
    query.show()
    ```

9. 您應該會看到如下的結果：

        +-------------+--------------------+
        | personalName|       officeAddress|
        +-------------+--------------------+
        |    John Dole|1111 San Gabriel Dr.|
        |  Calvin Raji|5415 San Gabriel Dr.|
        +-------------+--------------------+

## <a name="insert-new-data"></a>插入新資料

1. 若要插入新的連絡人記錄，請定義 `ContactRecord` 類別：

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

2. 建立 `ContactRecord` 的執行個體並將其放置在陣列中：

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

3. 將新資料的陣列儲存至 HBase：

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

4. 檢查結果︰

    ```scala  
    df.show()
    ```

5. 您應該會看到如下的輸出：

        +------+--------------------+--------------+------------+--------------+
        |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
        +------+--------------------+--------------+------------+--------------+
        |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
        | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
        |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
        +------+--------------------+--------------+------------+--------------+

## <a name="next-steps"></a>後續步驟

* [Apache Spark HBase Connector](https://github.com/hortonworks-spark/shc)
