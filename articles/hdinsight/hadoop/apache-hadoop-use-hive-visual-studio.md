---
title: 搭配使用 Apache Hive 與 Data Lake (Apache Hadoop) Tools for Visual Studio - Azure HDInsight
description: 了解如何使用 Data Lake Tools for Visual Studio 在 Azure HDInsight 上搭配 Apache Hadoop 執行 Apache Hive 查詢。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: 7480dafe435e555bfba81ebd9242bb5724c0bf3f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65861603"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>使用 Data Lake Tools for Visual Studio 執行 Apache Hive 查詢

了解如何使用 Data Lake Tools for Visual Studio 查詢 Apache Hive。 Data Lake Tools 可讓您在 Azure HDInsight 上輕鬆地建立、提交和監視對 Apache Hadoop 的 Hive 查詢。

## <a id="prereq"></a>必要條件

* HDInsight 上的 Apache Hadoop 叢集。 請參閱[開始在 Linux 上使用 HDInsight](./apache-hadoop-linux-tutorial-get-started.md)。

* Visual Studio (下列其中一個版本)：

    * Visual Studio 2015、 2017 （任何版本）
    * Visual Studio 2013 Community/Professional/Premium/Ultimate，含 Update 4

* HDInsight tools for Visual Studio 或 Azure Data Lake tools for Visual Studio。 如需有關安裝和設定工具的資訊，請參閱 [開始使用 Visual Studio Hadoop Tools for HDInsight](apache-hadoop-visual-studio-tools-get-started.md) 。

## <a id="run"></a> 使用 Visual Studio 執行 Apache Hive 查詢

您有兩個選項可建立和執行 Hive 查詢：

* 建立特定查詢
* 建立 Hive 應用程式

### <a name="ad-hoc"></a>臨機操作

臨機操作查詢可以在執行**批次**或是**Interactive**模式。

1. 開啟**Visual Studio**。

2. 從**伺服器總管**，瀏覽至**Azure** > **HDInsight**。

3. 依序展開**HDInsight**，以滑鼠右鍵按一下您想要用來執行查詢，然後選取叢集**撰寫 Hive 查詢**。

4. 輸入下列 hive 查詢：

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. 選取 [執行]  。 請注意，則預設的執行模式**Interactive**。

    ![執行互動式 Hive 查詢的螢幕擷取畫面](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. 在中執行相同的查詢**批次**模式中，從下拉式清單切換清單**Interactive**來**批次**。 請注意，從 [執行] 按鈕變更**Execute**要**送出**。

    ![提交 hive 查詢的螢幕擷取畫面](./media/apache-hadoop-use-hive-visual-studio/vs-batch-query.png)

    Hive 編輯器支援 Intellisense。 Data Lake Tools for Visual Studio 支援在編輯 Hive 指令碼時載入遠端中繼資料。 例如，如果您輸入`SELECT * FROM`，IntelliSense 會列出所有建議的資料表名稱。 若已指定資料表名稱，IntelliSense 會列出資料行名稱。 此工具支援大部分的 Hive DML 陳述式、子查詢及內建 UDF。 IntelliSense 只建議 HDInsight 工具列中已選取的叢集中繼資料。

    ![HDInsight Visual Studio Tools IntelliSense 範例 1 的螢幕擷取畫面](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![HDInsight Visual Studio Tools IntelliSense 範例 2 的螢幕擷取畫面](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "U-SQL IntelliSense")

7. 選取 [提交]  或 [提交 (進階)]  。

   如果您選取進階提交選項，請設定指令碼的 [作業名稱]  、[引數]  、[其他組態]  和 [狀態目錄]  ：

    ![HDInsight Hadoop Hive 查詢的螢幕擷取畫面](./media/apache-hadoop-use-hive-visual-studio/hdinsight.visual.studio.tools.submit.jobs.advanced.png "提交查詢")

### <a name="hive-application"></a>Hive 應用程式

1. 開啟**Visual Studio**。

2. 從功能表列中，瀏覽至**檔案** > **新增** > **專案**。

3. 從**新的專案** 視窗中，瀏覽至**範本** > **Azure Data Lake** > **HIVE (HDInsight)**  >  **Hive 應用程式**。 

4. 提供此專案的名稱，然後選取**確定**。

5. 開啟使用此專案所建立的 **Script.hql** 檔案，並貼入下列 HiveQL 陳述式中：

    ```hiveql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    這些陳述式會執行下列動作：

   * `DROP TABLE`:如果資料表存在，此陳述式將刪除它。

   * `CREATE EXTERNAL TABLE`:在 Hive 中建立新的「外部」資料表。 外部資料表只會在 Hive 中儲存資料表定義 (資料會保留在原始位置)。

     > [!NOTE]  
     > 當您預期會由外部來源來更新基礎資料時，請使用外部資料表。 例如，MapReduce 工作或 Azure 服務。
     >
     > 捨棄外部資料表並 **不會** 刪除資料，只會刪除資料表定義。

   * `ROW FORMAT`:告訴 Hive 如何設定資料格式。 在此情況下，每個記錄中的欄位會以空格隔開。

   * `STORED AS TEXTFILE LOCATION`:將資料的儲存位置告訴 Hive (example/data 目錄)，且資料儲存為文字。

   * `SELECT`:選擇其資料欄 `t4` 包含值 `[ERROR]` 的所有資料列計數。 這個陳述式會傳回值 `3`，因為有三個資料列包含此值。

   * `INPUT__FILE__NAME LIKE '%.log'` - 告訴 Hive 我們只應該從檔名以 log 結尾的檔案中傳回資料。 這個子句會將搜尋限制為包含資料的 sample.log 檔案。

6. 從工具列中，選取您想要用於此查詢的 **Hdinsight 叢集**。 選取**提交**以 Hive 作業形式執行陳述式。

   ![提交列](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

7. [Hive 工作摘要]  將會出現並顯示執行中工作的相關資訊。 使用 [重新整理]  連結來重新整理工作資訊，直到 [工作狀態]  變更為 [已完成]  為止。

   ![顯示已完成作業的作業摘要](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

8. 使用 [ **工作輸出** ] 連結檢視此工作的輸出。 它會顯示 `[ERROR] 3`，這是此查詢所傳回的值。

### <a name="additional-example"></a>其他範例

此範例依存於`log4jLogs`在先前步驟中建立的資料表。

1. 從**伺服器總管**，以滑鼠右鍵按一下您的叢集，然後選取**撰寫 Hive 查詢**。

2. 輸入下列 hive 查詢：

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    這些陳述式會執行下列動作：

    * `CREATE TABLE IF NOT EXISTS`:建立資料表 (如果不存在)。 因為未使用 `EXTERNAL` 關鍵字，這個陳述式會建立內部資料表。 內部資料表儲存在 Hive 資料倉儲中，並受到 Hive 所管理。
    
    > [!NOTE]  
    > 與 `EXTERNAL` 資料表不同之處在於，捨棄內部資料表也會刪除基礎資料。

    * `STORED AS ORC`:以最佳化資料列單欄式 (Optimized Row Columnar, ORC) 格式儲存資料。 ORC 是高度最佳化且有效率的 Hive 資料儲存格式。
    
    * `INSERT OVERWRITE ... SELECT`:從含有 `[ERROR]`的 `log4jLogs` 資料表選取資料列，然後將資料插入 `errorLogs` 資料表。

3. 執行中的查詢**批次**模式。

4. 若要確認作業已建立資料表，請使用 [伺服器總管]  並展開 [Azure]   > [HDInsight]  > 您的 HDInsight 叢集 > [Hive 資料庫]   > [預設]  。 會列出 **errorLogs** 資料表和 **log4jLogs** 資料表。

## <a id="nextsteps"></a>接續步驟

如您所見，HDInsight tools for Visual Studio 提供簡單的方法，可在 HDInsight 上使用 Hive 查詢。

如需 HDInsight 中 Hive 的一般資訊：

* [在 HDInsight 上搭配 Apache Hadoop 使用 Apache Hive](hdinsight-use-hive.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [在 HDInsight 上搭配 Apache Hadoop 使用 Apache Pig](hdinsight-use-pig.md)

* [搭配 MapReduce 與 HDInsight 上的 Apache Hadoop](hdinsight-use-mapreduce.md)

如需 HDInsight Tools for Visual Studio 的詳細資訊：

* [開始使用 HDInsight Tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)