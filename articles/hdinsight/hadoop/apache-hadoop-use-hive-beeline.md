---
title: 使用 Apache Beeline 搭配 Apache Hive - Azure HDInsight
description: 了解如何使用 Beeline 用戶端以 Hadoop on HDInsight 執行 Hive 查詢。 Beeline 是透過 JDBC 與 HiveServer2 搭配作業的公用程式。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: hrasheed
ms.openlocfilehash: 89303e5c827fc24540d345a9a2b9a0743e453a4d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257122"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>使用 Apache Beeline 用戶端搭配 Apache Hive

了解如何使用 [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) 在 HDInsight 上執行 Apache Hive 查詢。

Beeline 是 Hive 用戶端，隨附於您的 HDInsight 叢集的前端節點。 Beeline 會使用 JDBC 連線至 HiveServer2，它是裝載在 HDInsight 叢集上的服務。 您也可以使用 Beeline 透過網際網路從遠端存取 HDInsight 上的 Hive。 下列範例中提供用來從 Beeline 連接到 HDInsight 最常見的連接字串：

## <a name="types-of-connections"></a>連線類型

### <a name="from-an-ssh-session"></a>從 SSH 工作階段

當從 SSH 工作階段連線到叢集前端節點，您就可以連線到`headnodehost`連接埠上的地址`10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>透過 Azure 虛擬網路

透過 Azure 虛擬網路，從用戶端連線至 HDInsight 時，您必須提供叢集前端節點的完整的網域名稱 (FQDN)。 由於此連線是直接連線到叢集節點，因此會使用連接埠 `10001` 進行連線：

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

取代`<headnode-FQDN>`與叢集前端節點的完整的網域名稱。 若要找出前端節點的完整網域名稱，請利用[使用 Apache Ambari REST API 管理 HDInsight](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) 文件中的資訊。

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster"></a>HDInsight 企業安全性套件 」 (ESP) 叢集

當從用戶端連線至企業安全性套件 」 (ESP) 叢集加入至 Azure Active Directory (AAD) 時，您也必須指定網域名稱`<AAD-Domain>`來存取叢集的權限網域使用者帳戶名稱和`<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

以網域上具備叢集存取權限的帳戶名稱取代 `<username>`。 取代`<AAD-DOMAIN>`具有名稱的 Azure Active Directory (AAD) 加入叢集。 使用大寫字串`<AAD-DOMAIN>`值，否則認證找不到。 檢查`/etc/krb5.conf`的領域名稱，如有需要。

---

### <a name="over-public-internet"></a>透過公用網際網路

透過公用網際網路連線時，您必須提供叢集登入帳戶名稱 (預設為 `admin`) 和密碼。 例如，使用 Beeline 從用戶端系統連線到 `<clustername>.azurehdinsight.net` 位址。 此連線是透過連接埠 `443`，並使用 SSL 加密：

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
```

將 `clustername` 替換為 HDInsight 叢集的名稱。 將 `admin` 取代為叢集的叢集登入帳戶。 將 `password` 取代為叢集登入帳戶的密碼。

---

### <a id="sparksql"></a>使用 Beeline 搭配 Apache Spark

Apache Spark 提供自己的 HiveServer2 (有時稱為 Spark Thrift 伺服器) 實作。 此服務會使用 Spark SQL 來解析查詢而不是 Hive，並可能提供更佳的效能 (視您的查詢而定)。

#### <a name="over-public-internet-with-apache-spark"></a>透過公用網際網路和 Apache Spark

透過網際網路連接時所使用的連接字串會稍微不同。 不是包含`httpPath=/hive2`很`httpPath/sparkhive2`:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>從叢集前端或深入探討 Azure 虛擬網路使用 Apache Spark

直接從叢集前端節點，或是從 Azure 虛擬網路 (與 HDInsight 叢集相同) 內的資源進行連線時，應對 Spark Thrift 伺服器使用連接埠 `10002`，而非 `10001`。 下列範例示範如何直接連接到前端節點：

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>必要條件

* 在 HDInsight 上 Hadoop 叢集。 请参阅 [Linux 上的 HDInsight 入门](./apache-hadoop-linux-tutorial-get-started.md)。

* 請注意[URI 配置](../hdinsight-hadoop-linux-information.md#URI-and-scheme)您叢集的主要儲存體。 比方說， `wasb://` Azure 儲存體而言`abfs://`針對 Azure Data Lake 儲存體 Gen2 或`adl://`針對 Azure Data Lake 儲存體 Gen1。 Azure 儲存體或 Data Lake 儲存體 Gen2 啟用安全傳輸，URI 是否`wasbs://`或`abfss://`分別。 如需詳細資訊，請參閱 <<c0> [ 安全傳輸](../../storage/common/storage-require-secure-transfer.md)。


* 選項 1：SSH 用戶端。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。 本文件中的大部分步驟都假設您從連往叢集的 SSH 工作階段使用 Beeline。

* 選項 2：本機 Beeline 用戶端。


## <a id="beeline"></a>執行 Hive 查詢

這個範例根據使用 Beeline 用戶端，從 SSH 連線。

1. 下列程式碼中開啟叢集的 SSH 連線。 將 `sshuser` 取代為叢集的 SSH 使用者，並將 `CLUSTERNAME` 取代為叢集的名稱。 出現提示時，輸入 SSH 使用者帳戶的密碼。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 從連線到 HiveServer2 與您的 Beeline 用戶端開啟 SSH 工作階段中輸入下列命令：

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline 命令以 `!` 字元開頭，例如 `!help` 顯示說明。 不過，一些命令可以省略 `!`。 例如，`help` 也能運作。

    會有一個 `!sql`，用來執行 HiveQL 陳述式。 但是，由于 HiveQL 非常流行，因此可以省略前面的 `!sql`。 下列兩個陳述式是相等的：

    ```hiveql
    !sql show tables;
    show tables;
    ```

    新的叢集上只會列出一個資料表：**hivesampletable**。

4. 使用下列命令來顯示 hivesampletable 的結構描述：

    ```hiveql
    describe hivesampletable;
    ```

    此命令會傳回下列資訊：

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    此資訊描述資料表中的資料行。

5. 輸入下列陳述式，以使用 HDInsight 叢集隨附的範例資料來建立名為 **log4jLogs** 的資料表：(視需要修訂將會根據您[URI 配置](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。)

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
        GROUP BY t4;
    ```

    這些陳述式會執行下列動作：

    * `DROP TABLE` -如果資料表存在，則會將它刪除。

    * `CREATE EXTERNAL TABLE` -建立**外部**在 Hive 中的資料表。 外部資料表只會將資料表定義儲存在 Hive 中。 資料會留在原來的位置。

    * `ROW FORMAT` -如何格式化資料。 在此情況下，每個記錄中的欄位會以空格隔開。

    * `STORED AS TEXTFILE LOCATION` -當資料儲存和以何種檔案格式。

    * `SELECT` -選取的所有資料列計數，資料行**t4**含有值 **[ERROR]**。 此查詢會傳回值 **3** ，因為有 3 個資料列包含此值。

    * `INPUT__FILE__NAME LIKE '%.log'` -Hive 嘗試將結構描述套用至目錄中的所有檔案。 在此情況下，目錄包含不符合結構描述的檔案。 若要防止結果中出現亂碼資料，此陳述式會告訴 Hive 只應該從檔名以 .log 結尾的檔案傳回資料。

   > [!NOTE]  
   > 當您預期會由外部來源來更新基礎資料時，請使用外部資料表。 例如，自動化的資料上傳程序，或透過其他 MapReduce 作業。
   >
   > 删除外部表**不会**删除数据，只会删除表定义。

    此命令的輸出類似下列文字：

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

6. 若要結束 Beeline，請使用 `!exit`。

## <a id="file"></a>執行 HiveQL 檔案

這是從先前的範例的接續。 使用下列步驟建立檔案，然後利用執行該檔案。

1. 使用以下命令，建立名為 **query.hql** 的檔案：

    ```bash
    nano query.hql
    ```

2. 使用下列文字做為檔案的內容。 此查詢將建立名為 **errorLogs** 的新「內部」資料表：

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    這些陳述式會執行下列動作：

   * **CREATE TABLE IF NOT EXISTS** - 如果資料表尚不存在，則會建立它。 因為未使用 **EXTERNAL** 關鍵字，這個陳述式會建立內部資料表。 內部資料表儲存在 Hive 資料倉儲中，並完全由 Hive 管理。
   * **STORED AS ORC**：以优化行纵栏表 (ORC) 格式存储数据。 ORC 格式是高度最佳化且有效率的 Hive 資料儲存格式。
   * **INSERT OVERWRITE ...SELECT**- 從包含 **[ERROR]** 的 **log4jLogs** 資料表選取資料列，然後將資料插入 **errorLogs** 資料表。

    > [!NOTE]  
    > 與外部資料表不同之處在於，捨棄內部資料表也會刪除基礎資料。

3. 若要儲存檔案，請使用 **Ctrl**+**_X**，然後輸入 **Y**，最後按 **Enter**。

4. 使用下列命令，以使用 Beeline 來執行檔案：

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > `-i` 參數會啟動 Beeline 並執行 `query.hql` 檔案中的陳述式。 當查詢完成時，您會看到 `jdbc:hive2://headnodehost:10001/>` 提示字元。 您也可以使用 `-f` 參數執行檔案，它會在查詢完成後結束 Beeline。

5. 若要验证是否已创建 **errorLogs** 表，请使用以下语句从 **errorLogs** 返回所有行：

    ```hiveql
    SELECT * from errorLogs;
    ```

    應該傳回三個資料列，且在資料行 t4 中全部包含 **[ERROR]** ：

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)




## <a id="summary"></a><a id="nextsteps"></a>後續步驟

如需有關 HDInsight 中 Hive 的更多一般資訊，請參閱下列文件：

* [使用 Apache Hive 與 HDInsight 上的 Apache Hadoop](hdinsight-use-hive.md)

如需您可以使用 HDInsight 上的 Hadoop 之其他方式的詳細資訊，請參閱下列文件：

* [在 HDInsight 上搭配 Apache Hadoop 使用 Apache Pig](hdinsight-use-pig.md)
* [搭配使用 MapReduce 與 HDInsight 上的 Apache Hadoop](hdinsight-use-mapreduce.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
