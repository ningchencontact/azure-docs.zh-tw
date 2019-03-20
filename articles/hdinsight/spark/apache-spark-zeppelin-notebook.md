---
title: 在 Azure HDInsight 上搭配使用 Zeppelin Notebook 和 Apache Spark 叢集
description: 如何在 Azure HDInsight 上搭配使用 Zeppelin Notebook 和 Apache Spark 叢集的逐步指示。
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/04/2019
ms.openlocfilehash: 18660b41f4413d3ae1cd820cd645ddb540397906
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58095889"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>在 Azure HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集

HDInsight Spark 叢集包含可用來執行 [Apache Spark](https://spark.apache.org/) 作業的 [Apache Zeppelin](https://zeppelin.apache.org/) Notebook。 在本文中，您將學習如何在 HDInsight 叢集上使用 Zeppelin Notebook。

**必要條件：**

* Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。
* 您叢集的主要儲存體 URI 配置。 這會是`wasb://`Azure Blob 儲存體而言`abfs://`針對 Azure Data Lake 儲存體 Gen2 或`adl://`針對 Azure Data Lake 儲存體 Gen1。 如果 Blob 儲存體或 Data Lake 儲存體 Gen2 啟用安全傳輸，則 URI 會是`wasbs://`或`abfss://`分別。  此外，請參閱 <<c0> [ 需要 Azure 儲存體中的安全傳輸](../../storage/common/storage-require-secure-transfer.md)如需詳細資訊。

## <a name="launch-an-apache-zeppelin-notebook"></a>啟動 Apache Zeppelin Notebook

1. 從 Spark 叢集**概觀**，選取**Zeppelin notebook**從**叢集儀表板**。 輸入叢集系統管理員認證。  

   > [!NOTE]  
   > 您也可以在瀏覽器中開啟下列 URL，來連接到您叢集的 Zeppelin Notebook。 使用您叢集的名稱取代 **CLUSTERNAME** ：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. 建立新的 Notebook。 從標頭 窗格中，瀏覽至**Notebook** > **建立新的記事**。

    ![建立新的 Zeppelin Notebook](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "建立新的 Zeppelin Notebook")

    輸入 notebook 的名稱，然後選取**建立的附註**。

3. 請確定 notebook 標頭會顯示連線的狀態。 右上角的綠點即表示此狀態。

    ![Zeppelin 笔记本状态](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin 笔记本状态")

4. 將範例資料載入暫存資料表。 當您建立 HDInsight，將範例資料檔案中的 Spark 叢集`hvac.csv`，複製到相關聯的儲存體帳戶`\HdiSamples\SensorSampleData\hvac`。

    將以下程式碼片段貼入新 Notebook 中預設建立的空白段落。

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
   
    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0), 
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    按下 **SHIFT + ENTER**，或是按一下 [播放] 按鈕來讓段落執行程式碼片段。 段落右上角的狀態應該會從「準備就緒」逐一轉變成「擱置」、「執行中」及「已完成」。 輸出會顯示在同一個段落的底部。 螢幕擷取畫面如下所示：

    ![從未經處理資料建立暫存資料表](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "從未經處理資料建立暫存資料表")

    您也可以為每個段落提供標題。 段落右上角，選取**設定**圖示 (sprocket)，，然後選取**顯示標題**。  

    > [!NOTE]  
    > 所有 HDInsight 版本的 Zeppelin Notebook 中都不支援 %spark2 解譯器，而 HDInsight 4.0 以後的版本不支援 %sh 解譯器。

5. 您現在可以在執行 Spark SQL 陳述式`hvac`資料表。 將以下查詢貼入新段落。 此查詢會擷取建築物識別碼，以及在指定日期當天每棟建築物之目標溫度與實際溫度間的差異。 按下 **SHIFT + ENTER**。

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
    ```  

    開頭的 **%Sql** 陳述式會告訴 Notebook 使用 Livy Scala 解譯器。

6. 選取 **橫條圖**來變更顯示的圖示。  **設定**，選取之後，會出現**橫條圖**，可讓您選擇**金鑰**，和**值**。  以下螢幕擷取畫面顯示輸出。

    ![使用笔记本运行 Spark SQL 语句](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "使用笔记本运行 Spark SQL 语句")

7. 您也可以在查詢中使用變數來執行 Spark SQL 陳述式。 下一步 的程式碼片段示範如何定義一個變數， `Temp`，在您想要使用查詢的查詢可能的值。 當您第一次執行查詢時，下拉式清單會自動填入您指定的變數值。

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    將此程式碼片段貼到新的段落中，然後按下 **SHIFT + ENTER**。 然後選取**65**從**Temp**下拉式 ist。 

8. 選取 **橫條圖**來變更顯示的圖示。  然後選取**設定**並進行下列變更：

   * **群組：** 新增**targettemp**。  
   * **值：** 1. 移除**日期**。  2. 新增**temp_diff**。  3.  變更彙總工具，從**總和**要**AVG**。  

     以下螢幕擷取畫面顯示輸出。

     ![使用笔记本运行 Spark SQL 语句](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "使用笔记本运行 Spark SQL 语句")

9. 重新啟動 Livy 解譯器以結束應用程式。 若要這樣做，請開啟解譯器設定在 使用者名稱，從右上角，選取 登入，然後選取**解譯器**。  

    ![啟動解譯器](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 輸出")

10. 若要捲動**livy**，然後選取**重新啟動**。  選取 **確定**在提示字元。

    ![重新啟動 Livy 解譯器](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "重新啟動 Zeppelin 解譯器")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>如何搭配 Notebook 使用外部套件？
您可以使用外部、 社群提供的套件不包含--蜪鎏叢集中的 HDInsight 上的 Apache Spark 叢集中設定 Zeppelin notebook。 您可以搜尋 [Maven 儲存機制](https://search.maven.org/) 來取得可用套件的完整清單。 您也可以從其他來源取得可用套件清單。 例如，從 [Spark 套件](https://spark-packages.org/)可以取得社群提供套件的完整清單。

在本文中，您將了解如何搭配 Jupyter Notebook 使用 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 套件。

1. 開啟解譯器設定。 從右上角，選取 使用者名稱中的 登入，然後選取**解譯器**。

    ![啟動解譯器](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 輸出")

2. 若要捲動**livy**，然後選取**編輯**。

    ![變更解譯器設定](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "變更解譯器設定")

3. 加入新的金鑰，稱為`livy.spark.jars.packages`，並設定其值格式`group:id:version`。 因此，如果您想要使用 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 套件，您必須將金鑰值設為 `com.databricks:spark-csv_2.10:1.4.0`。

    ![變更解譯器設定](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "變更解譯器設定")

    選取 **儲存**然後重新啟動 Livy 解譯器。

4. 如果您想要了解如何得出上面所輸入的金鑰值，其方法如下。
   
    a. 在「Maven 儲存機制」中找出套件。 針對本教學課程，我們使用 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)。
   
    b. 從儲存機制收集 [GroupId]、[ArtifactId] 及 [版本] 的值。
   
    ![搭配 Jupyter Notebook 使用外部封裝](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "搭配 Jupyter Notebook 使用外部封裝")
   
    c. 串連三個值，其中以冒號分隔 (**:**)。
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Zeppelin Notebook 儲存在哪裡？
Zeppelin Notebook 會儲存到叢集前端節點。 因此，如果您刪除叢集，Notebook 會一併刪除。 如果您想要保留 Notebook 以供稍後用於其他叢集上，您必須在作業執行完成後將 Notebook 匯出。 若要匯出 notebook，請選取**匯出**圖示，如下圖所示。

![下載 Notebook](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "下載 Notebook")

這會將 Notebook 以 JSON 檔案的形式儲存在下載位置中。

## <a name="livy-session-management"></a>Livy 工作階段管理
當您在 Zeppelin Notebook 中執行第一個程式碼段落時，HDInsight Spark 叢集中便會建立新的 Livy 工作階段。 此工作階段可供您後續建立的所有 Zeppelin Notebook 共用。 如果 Livy 工作階段因為某些原因而遭到刪除 (叢集重新開機等)，您就無法從 Zeppelin Notebook 執行作業。

在這種情況下，您必須先執行下列步驟，然後才能開始從 Zeppelin Notebook 執行作業。  

1. 從 Zeppelin Notebook 重新啟動 Livy 解譯器。 若要這樣做，請開啟解譯器設定在 使用者名稱，從右上角，選取 登入，然後選取**解譯器**。

    ![啟動解譯器](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 輸出")

2. 若要捲動**livy**，然後選取**重新啟動**。

    ![重新啟動 Livy 解譯器](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "重新啟動 Zeppelin 解譯器")

3. 在现有的 Zeppelin 笔记本中运行代码单元。 這會在 HDInsight 叢集中建立新的 Livy 工作階段。

## <a name="seealso"></a>另請參閱
* [概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>案例
* [Apache Spark 和 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](apache-spark-use-bi-tools.md)
* [Apache Spark 和 Machine Learning：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 和 Machine Learning：使用 HDInsight 中的 Spark 來預測食品檢查結果](apache-spark-machine-learning-mllib-ipython.md)
* [在 HDInsight 中使用 Apache Spark 進行網站記錄分析](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>建立及執行應用程式
* [使用 Scala 建立獨立應用程式](apache-spark-create-standalone-application.md)
* [利用 Apache Livy 在 Apache Spark 叢集上遠端執行作業](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和擴充功能
* [使用適用於 IntelliJ IDEA 的 Azure 工具組建立適用於 HDInsight 叢集的 Spark 應用程式](apache-spark-intellij-tool-plugin.md)
* [使用適用於 IntelliJ IDEA 的 HDInsight 工具外掛程式遠端偵錯 Apache Spark 應用程式](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight Apache Spark 叢集中 Jupyter Notebook 的可用核心](apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理資源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 
