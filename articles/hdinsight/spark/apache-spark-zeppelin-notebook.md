---
title: 在 Azure HDInsight 上搭配使用 Zeppelin Notebook 和 Apache Spark 叢集
description: 如何在 Azure HDInsight 上搭配使用 Zeppelin Notebook 和 Apache Spark 叢集的逐步指示。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/04/2019
ms.openlocfilehash: 6801f2b3bca1fbfa221ec2eba07f51b76712b4ff
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813982"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>在 Azure HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集

HDInsight Spark 叢集包含可用來執行 [Apache Spark](https://spark.apache.org/) 作業的 [Apache Zeppelin](https://zeppelin.apache.org/) Notebook。 在本文中，您將學習如何在 HDInsight 叢集上使用 Zeppelin Notebook。

**必要條件：**

* Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。
* 叢集主要儲存體的 URI 配置。 這適用于 Azure Blob 儲存體、 `abfs://` Azure Data Lake Storage Gen2 或`adl://` Azure Data Lake Storage Gen1。 `wasb://` 如果已啟用 Blob 儲存體或 Data Lake Storage Gen2 的安全傳輸，則 URI 會分別`wasbs://`是`abfss://`或。  如需詳細資訊，請參閱[Azure 儲存體中的「需要安全傳輸](../../storage/common/storage-require-secure-transfer.md)」。

## <a name="launch-an-apache-zeppelin-notebook"></a>啟動 Apache Zeppelin Notebook

1. 從 Spark 叢集**總覽**中，從 [叢集**儀表板**] 選取 [ **Zeppelin 筆記本**]。 輸入叢集的系統管理員認證。  

   > [!NOTE]  
   > 您也可以在瀏覽器中開啟下列 URL，來連接到您叢集的 Zeppelin Notebook。 使用您叢集的名稱取代 **CLUSTERNAME** ：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. 建立新的 Notebook。 從標頭窗格中，流覽至 [**筆記本** > ] [**建立新便箋**]。

    ![建立新的 Zeppelin Notebook](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "建立新的 Zeppelin Notebook")

    輸入筆記本的名稱，然後選取 [**建立便箋**]。

3. 確定筆記本標頭顯示 [已連線] 狀態。 右上角的綠點即表示此狀態。

    ![Zeppelin Notebook 狀態](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin Notebook 狀態")

4. 將範例資料載入暫存資料表。 當您在 HDInsight 中建立 Spark 叢集時，會將範例資料`hvac.csv`檔案複製到底下的相關聯儲存體`\HdiSamples\SensorSampleData\hvac`帳戶。

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

    您也可以為每個段落提供標題。 從段落的右上角，選取 [**設定**] 圖示（sprocket），然後選取 [**顯示標題**]。  

    > [!NOTE]  
    > 所有 HDInsight 版本的 Zeppelin Notebook 中都不支援 %spark2 解譯器，而 HDInsight 4.0 以後的版本不支援 %sh 解譯器。

5. 您現在可以在`hvac`資料表上執行 Spark SQL 語句。 將以下查詢貼入新段落。 此查詢會擷取建築物識別碼，以及在指定日期當天每棟建築物之目標溫度與實際溫度間的差異。 按下 **SHIFT + ENTER**。

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
    ```  

    開頭的 **%Sql** 陳述式會告訴 Notebook 使用 Livy Scala 解譯器。

6. 選取**橫條圖**圖示以變更顯示。  [**設定**] 會在您選取 [**橫條圖**] 之後出現，可讓您選擇 [索引**鍵**] 和 [**值**]。  以下螢幕擷取畫面顯示輸出。

    ![使用 Notebook1 執行 SPARK SQL 語句](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "使用 Notebook1 執行 SPARK SQL 語句")

7. 您也可以在查詢中使用變數來執行 Spark SQL 陳述式。 下一個程式碼片段示範如何在查詢中， `Temp`使用您想要查詢的可能值來定義變數。 當您第一次執行查詢時，下拉式清單會自動填入您指定的變數值。

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    將此程式碼片段貼到新的段落中，然後按下 **SHIFT + ENTER**。 然後選取 [ **65** ]，從 [ **Temp** ] 下拉式的 [ist]。 

8. 選取**橫條圖**圖示以變更顯示。  然後選取 [**設定**]，並進行下列變更：

   * **部門**新增**targettemp**。  
   * **閾值**1. 移除**日期**。  2. 新增**temp_diff**。  3.  將匯總工具從**SUM**變更為**AVG**。  

     以下螢幕擷取畫面顯示輸出。

     ![使用 Notebook2 執行 SPARK SQL 語句](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "使用 Notebook2 執行 SPARK SQL 語句")

9. 重新啟動 Livy 解譯器以結束應用程式。 若要這麼做，請從右上角選取登入的使用者名稱，然後選取 [**解釋**器]，以開啟解譯器設定。  

    ![啟動解譯器](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 輸出")

10. 流覽至**livy**，然後選取 [**重新開機**]。  在提示中選取 **[確定]** 。

    ![重新啟動 Livy 解譯器](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "重新啟動 Zeppelin 解譯器")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>如何搭配 Notebook 使用外部套件？
您可以在 HDInsight 上的 Apache Spark 叢集中設定 Zeppelin 筆記本，以使用不是叢集中現成可用的外部、提供社區的套件。 您可以搜尋 [Maven 儲存機制](https://search.maven.org/) 來取得可用套件的完整清單。 您也可以從其他來源取得可用套件清單。 例如，從 [Spark 套件](https://spark-packages.org/)可以取得社群提供套件的完整清單。

在本文中，您將了解如何搭配 Jupyter Notebook 使用 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 套件。

1. 開啟解譯器設定。 從右上角選取登入的使用者名稱，然後選取 [**解釋**器]。

    ![啟動解譯器](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 輸出")

2. 流覽至**livy**，然後選取 [**編輯**]。

    ![變更解譯器設定 1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "變更解譯器設定 1")

3. 新增名`livy.spark.jars.packages`為的新金鑰，並以格式`group:id:version`設定其值。 因此，如果您想要使用 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 套件，您必須將金鑰值設為 `com.databricks:spark-csv_2.10:1.4.0`。

    ![變更解譯器 settings2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "變更解譯器 settings2")

    選取 [**儲存**]，然後重新開機 Livy 解譯器。

4. 如果您想要了解如何得出上面所輸入的金鑰值，其方法如下。
   
    a. 在「Maven 儲存機制」中找出套件。 在本文中，我們使用了[spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)。
   
    b. 從儲存機制收集 [GroupId]、[ArtifactId] 及 [版本] 的值。
   
    ![搭配 Jupyter Notebook 使用外部封裝](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "搭配 Jupyter Notebook 使用外部封裝")
   
    c. 串連三個值，其中以冒號分隔 ( **:** )。
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Zeppelin Notebook 儲存在哪裡？
Zeppelin Notebook 會儲存到叢集前端節點。 因此，如果您刪除叢集，Notebook 會一併刪除。 如果您想要保留 Notebook 以供稍後用於其他叢集上，您必須在作業執行完成後將 Notebook 匯出。 若要匯出筆記本，請選取 [**匯出**] 圖示，如下圖所示。

![下載 Notebook](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "下載 Notebook")

這會將 Notebook 以 JSON 檔案的形式儲存在下載位置中。

## <a name="livy-session-management"></a>Livy 工作階段管理
當您在 Zeppelin Notebook 中執行第一個程式碼段落時，HDInsight Spark 叢集中便會建立新的 Livy 工作階段。 此工作階段可供您後續建立的所有 Zeppelin Notebook 共用。 如果 Livy 工作階段因為某些原因而遭到刪除 (叢集重新開機等)，您就無法從 Zeppelin Notebook 執行作業。

在這種情況下，您必須先執行下列步驟，然後才能開始從 Zeppelin Notebook 執行作業。  

1. 從 Zeppelin Notebook 重新啟動 Livy 解譯器。 若要這麼做，請從右上角選取登入的使用者名稱，然後選取 [**解釋**器]，以開啟解譯器設定。

    ![啟動解譯器](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 輸出")

2. 流覽至**livy**，然後選取 [**重新開機**]。

    ![重新啟動 Livy 解譯器](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "重新啟動 Zeppelin 解譯器")

3. 從現有的 Zeppelin Notebook 執行程式碼單元。 這會在 HDInsight 叢集中建立新的 Livy 工作階段。

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
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 
