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
ms.openlocfilehash: 9fc18b11b24791c1e154d89d757408da4ab20539
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64709560"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>在 Azure HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集

HDInsight Spark 叢集包含可用來執行 [Apache Spark](https://spark.apache.org/) 作業的 [Apache Zeppelin](https://zeppelin.apache.org/) Notebook。 在本文中，您將學習如何在 HDInsight 叢集上使用 Zeppelin Notebook。

**必要條件：**

* Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。
* 群集主存储的 URI 方案。 对于 Azure Blob 存储，此值为 `wasb://`；对于Azure Data Lake Storage Gen2，此值为 `abfs://`；对于 Azure Data Lake Storage Gen1，此值为 `adl://`。 如果为 Blob 存储或 Data Lake Storage Gen2 启用了安全传输，则 URI 分别是 `wasbs://` 或 `abfss://`。  另请参阅[在 Azure 存储中要求安全传输](../../storage/common/storage-require-secure-transfer.md)了解详细信息。

## <a name="launch-an-apache-zeppelin-notebook"></a>啟動 Apache Zeppelin Notebook

1. 在 Spark 群集的“概述”中，从**群集仪表板**选择“Zeppelin 笔记本”。 输入群集的管理员凭据。  

   > [!NOTE]  
   > 您也可以在瀏覽器中開啟下列 URL，來連接到您叢集的 Zeppelin Notebook。 使用您叢集的名稱取代 **CLUSTERNAME** ：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. 建立新的 Notebook。 在标题窗格中，导航到“笔记本” > “创建新笔记”。

    ![建立新的 Zeppelin Notebook](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "建立新的 Zeppelin Notebook")

    输入笔记本的名称，然后选择“创建笔记”。

3. 确保笔记本标题显示“已连接”状态。 右上角的綠點即表示此狀態。

    ![Zeppelin 笔记本状态](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin 笔记本状态")

4. 將範例資料載入暫存資料表。 在 HDInsight 中创建 Spark 群集时，系统会将示例数据文件 `hvac.csv` 复制到 `\HdiSamples\SensorSampleData\hvac` 下的关联存储帐户。

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

    您也可以為每個段落提供標題。 在段落的右侧一角，选择“设置”图标（齿轮图标），然后选择“显示标题”。  

    > [!NOTE]  
    > 所有 HDInsight 版本的 Zeppelin Notebook 中都不支援 %spark2 解譯器，而 HDInsight 4.0 以後的版本不支援 %sh 解譯器。

5. 现在可以针对 `hvac` 表运行 Spark SQL 语句。 將以下查詢貼入新段落。 此查詢會擷取建築物識別碼，以及在指定日期當天每棟建築物之目標溫度與實際溫度間的差異。 按下 **SHIFT + ENTER**。

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
    ```  

    開頭的 **%Sql** 陳述式會告訴 Notebook 使用 Livy Scala 解譯器。

6. 选择“条形图”图标以更改显示内容。  使用选择“条形图”后显示的“设置”可以选择“键”和“值”。  以下螢幕擷取畫面顯示輸出。

    ![使用笔记本运行 Spark SQL 语句](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "使用笔记本运行 Spark SQL 语句")

7. 您也可以在查詢中使用變數來執行 Spark SQL 陳述式。 以下代码片段演示如何在查询中使用可以用来查询的值定义 `Temp` 变量。 當您第一次執行查詢時，下拉式清單會自動填入您指定的變數值。

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    將此程式碼片段貼到新的段落中，然後按下 **SHIFT + ENTER**。 然后，从“温度”下拉列表中选择“65”。 

8. 选择“条形图”图标以更改显示内容。  然后选择“设置”并进行以下更改：

   * **组：** 添加 **targettemp**。  
   * **值：** 1. 删除 **date**。  2. 添加 **temp_diff**。  3.  将聚合器从“SUM”更改为“AVG”。  

     以下螢幕擷取畫面顯示輸出。

     ![使用笔记本运行 Spark SQL 语句](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "使用笔记本运行 Spark SQL 语句")

9. 重新啟動 Livy 解譯器以結束應用程式。 为此，请选择右上角的登录用户名打开解释器设置，然后选择“解释器”。  

    ![啟動解譯器](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 輸出")

10. 滚动到“livy”，然后选择“重启”。  出现提示时，请选择“确定”。

    ![重新啟動 Livy 解譯器](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "重新啟動 Zeppelin 解譯器")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>如何搭配 Notebook 使用外部套件？
可在 HDInsight 上的 Apache Spark 群集中配置 Zeppelin 笔记本，以使用未现成包含在群集中的、由社区贡献的外部包。 您可以搜尋 [Maven 儲存機制](https://search.maven.org/) 來取得可用套件的完整清單。 您也可以從其他來源取得可用套件清單。 例如，從 [Spark 套件](https://spark-packages.org/)可以取得社群提供套件的完整清單。

在本文中，您將了解如何搭配 Jupyter Notebook 使用 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 套件。

1. 開啟解譯器設定。 选择右上角的登录用户名，然后选择“解释器”。

    ![啟動解譯器](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 輸出")

2. 滚动到“livy”，然后选择“编辑”。

    ![變更解譯器設定](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "變更解譯器設定")

3. 添加名为 `livy.spark.jars.packages` 的新键，并以 `group:id:version` 格式设置其值。 因此，如果您想要使用 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) 套件，您必須將金鑰值設為 `com.databricks:spark-csv_2.10:1.4.0`。

    ![變更解譯器設定](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "變更解譯器設定")

    选择“保存”，然后重启 Livy 解释器。

4. 如果您想要了解如何得出上面所輸入的金鑰值，其方法如下。
   
    a. 在「Maven 儲存機制」中找出套件。 針對本教學課程，我們使用 [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)。
   
    b. 從儲存機制收集 [GroupId]、[ArtifactId] 及 [版本] 的值。
   
    ![搭配 Jupyter Notebook 使用外部封裝](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "搭配 Jupyter Notebook 使用外部封裝")
   
    c. 串連三個值，其中以冒號分隔 (**:**)。
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Zeppelin Notebook 儲存在哪裡？
Zeppelin Notebook 會儲存到叢集前端節點。 因此，如果您刪除叢集，Notebook 會一併刪除。 如果您想要保留 Notebook 以供稍後用於其他叢集上，您必須在作業執行完成後將 Notebook 匯出。 若要导出笔记本，请选择下图所示的“导出”图标。

![下載 Notebook](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "下載 Notebook")

這會將 Notebook 以 JSON 檔案的形式儲存在下載位置中。

## <a name="livy-session-management"></a>Livy 工作階段管理
當您在 Zeppelin Notebook 中執行第一個程式碼段落時，HDInsight Spark 叢集中便會建立新的 Livy 工作階段。 此工作階段可供您後續建立的所有 Zeppelin Notebook 共用。 如果 Livy 工作階段因為某些原因而遭到刪除 (叢集重新開機等)，您就無法從 Zeppelin Notebook 執行作業。

在這種情況下，您必須先執行下列步驟，然後才能開始從 Zeppelin Notebook 執行作業。  

1. 從 Zeppelin Notebook 重新啟動 Livy 解譯器。 为此，请选择右上角的登录用户名打开解释器设置，然后选择“解释器”。

    ![啟動解譯器](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive 輸出")

2. 滚动到“livy”，然后选择“重启”。

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
