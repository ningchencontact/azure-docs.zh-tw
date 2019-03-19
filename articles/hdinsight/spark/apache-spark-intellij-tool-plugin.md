---
title: 'Azure Toolkit for IntelliJ：建立適用於 HDInsight 叢集的 Spark 應用程式 '
description: 使用適用於 IntelliJ 的 Azure 工具組來開發以 Scala 撰寫的 Spark 應用程式，並將它們提交到 HDInsight Spark 叢集。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: maxluk
ms.openlocfilehash: 8fa3cd79011ab31349ec44edf52b8fd9048d0d37
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58077967"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>使用 Azure Toolkit for IntelliJ 為 HDInsight 叢集建立 Apache Spark 應用程式

使用 Azure Toolkit for IntelliJ 外掛程式來開發以 [Scala](https://www.scala-lang.org/) 撰寫的 [Apache Spark](https://spark.apache.org/) 應用程式，然後直接從 IntelliJ 整合式開發環境 (IDE) 將它們提交到 HDInsight Spark 叢集。 您可以利用數個方式來使用此外掛程式：

* 在 HDInsight Spark 叢集上開發並提交 Scala Spark 應用程式。
* 存取您的 Azure HDInsight Spark 叢集資源。
* 在本機開發並執行 Scala Spark 應用程式。

## <a name="prerequisites"></a>必要條件

* HDInsight 上的 Apache Spark 群集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。
* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。  本教學課程使用 Java 8.0.202 版。
* IntelliJ IDEA。 本文使用 [IntelliJ IDEA Community 版本2018.3.4](https://www.jetbrains.com/idea/download/)。
* Azure Toolkit for IntelliJ。  請參閱[安裝 Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable)。
* WINUTILS.EXE。  請參閱[在 Windows 上執行 Hadoop 的問題](https://wiki.apache.org/hadoop/WindowsProblems)。

## <a name="install-scala-plugin-for-intellij-idea"></a>安裝 IntelliJ IDEA 的 Scala 外掛程式
執行下列步驟來安裝 Scala 外掛程式：

1. 開啟 IntelliJ IDEA。

2. 在歡迎使用畫面上，瀏覽至 [設定] > [外掛程式] 以開啟 [外掛程式] 視窗。
   
    ![啟用 Scala 外掛程式](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

3. 針對新視窗中精選的 Scala 外掛程式，選取 [安裝]。  

    ![安裝 Scala 外掛程式](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. 在外掛程式安裝成功後，您必須重新啟動 IDE。


## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>為 HDInsight Spark 叢集建立 Spark Scala 應用程式

1. 啟動 IntelliJ IDEA，然後選取 [建立新專案] 來開啟 [新增專案] 視窗。

2. 選取左窗格中的 [Azure Spark/HDInsight]。

3. 選取主視窗中的 [Spark 專案 (Scala)]。

4. 從 [建置工具] 下拉式清單中，選取下列其中一項：
   * **Maven**：建立 Scala 專案精靈支援。
   * **SBT**：可供管理相依性並建置 Scala 專案。

     ![[新增專案] 對話方塊](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. 選取 [下一步] 。

6. 在 [新增專案] 視窗中，提供下列資訊：  

    |  屬性   | 描述   |  
    | ----- | ----- |  
    |專案名稱| 輸入名稱。  本教學課程會使用 `myApp`。|  
    |專案&nbsp;位置| 輸入所要的位置以儲存您的專案。|
    |專案 SDK| 您第一次使用 IDEA 時，這可能是空白的。  選取 [新增...] 並瀏覽至您的 JDK。|
    |Spark 版本|建立精靈會為 Spark SDK 和 Scala SDK 整合正確的版本。 如果 Spark 叢集是 2.0 以前的版本，請選取 [Spark 1.x]。 否則，請選取 [Spark2.x]。 此範例使用 **Spark 2.3.0 (Scala 2.11.8)**。|

    ![選取 Spark SDK](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

7. 選取 [完成]。  可能需要幾分鐘的時間，專案才會變成可用。

8. Spark 專案會自動為您建立構件。 若要檢視構件，請執行下列動作：

   a. 從功能表中，瀏覽至 [檔案] > [專案結構...]。

   b. 從 [專案結構] 視窗中，選取 [成品]。  

   c. 檢視成品之後，請選取 [取消]。

      ![對話方塊中的構件資訊](./media/apache-spark-intellij-tool-plugin/default-artifact.png)

9. 若要新增應用程式的原始程式碼，請執行下列動作：

    a. 從專案中，瀏覽至 [myApp] > [src] > [main] > [scala]。  

    b. 以滑鼠右鍵按一下 [scala]，然後瀏覽至 [新增] > [Scala 類別]。

   ![從專案中建立 Scala 類別的命令](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. 在 [建立新的 Scala 類別] 對話方塊中，提供一個名稱，並在 [種類] 下拉式清單中選取 [物件]，然後選取 [確定]。

     ![建立新的 Scala 類別對話方塊](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. 接著，**MyApp.scala** 檔案會在主要檢視中開啟。 使用下方列出的程式碼來取代預設程式碼：  

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object myApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("myApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasbs:///HVACOut")
            }
    
        }

    此程式碼會從 HVAC.csv (所有 HDInsight Spark 叢集上均提供) 讀取資料、擷取在 CSV 檔案的第七個資料行中只有一個位數的資料列，並將輸出寫入叢集預設儲存體容器下的 `/HVACOut`。

## <a name="connect-to-your-hdinsight-cluster"></a>連線到 HDInsight 叢集
使用者可以[登入 Azure 訂用帳戶](#sign-in-to-your-azure-subscription)，或是使用 Ambari 使用者名稱/密碼或加入網域的認證來連線到 HDInsight 叢集，以[連結 HDInsight 叢集](#link-a-cluster)。

### <a name="sign-in-to-your-azure-subscription"></a>登入您的 Azure 訂用帳戶：

1. 從功能表列中，瀏覽至 [檢視] > [工具視窗] > [Azure Explorer]。
       
   ![[Azure Explorer] 連結](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. 在 Azure Explorer 中，以滑鼠右鍵按一下 [Azure] 節點，然後選取 [登入]。

3. 在 [Azure 登入] 對話方塊中，選取 [登入]，然後輸入您的 Azure 認證。

    ![[Azure 登入] 對話方塊](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. 登入之後，[選取訂用帳戶] 對話方塊會列出與認證建立關聯的所有 Azure 訂用帳戶。 選取您的訂用帳戶，然後選取 [選取] 按鈕。

    ![[選取訂用帳戶] 對話方塊](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. 在 [Azure Explorer] 中展開 [HDInsight]，可檢視您訂用帳戶中的 HDInsight Spark 叢集。

    ![Azure Explorer 中的 HDInsight Spark 叢集](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. 若要檢視與叢集建立關聯的資源 (例如儲存體帳戶)，您可以進一步展開叢集名稱節點。

    ![展開的叢集名稱節點](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

### <a name="link-a-cluster"></a>連結叢集
您可以使用 Apache Ambari 受控使用者名稱來連結 HDInsight 叢集。 同樣地，對於已加入網域的 HDInsight 叢集，您可以使用網域和使用者名稱進行連結，例如 user1@contoso.com。 您也可以連結 Livy 服務叢集。

1. 從功能表列中，瀏覽至 [檢視] > [工具視窗] > [Azure Explorer]。

2. 在 Azure Explorer 中，以滑鼠右鍵按一下 [HDInsight] 節點，然後選取 [連結叢集]。

   ![連結叢集操作功能表](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

3. [連結叢集] 視窗中的可用選項可能會不同，這取決於您從 [連結資源類型] 下拉式清單中選取的值。  輸入您的值，然後選取 [確定]。

    * **HDInsight 叢集**  
  
        |屬性 |值 |
        |----|----|
        |連結資源類型|從下拉式清單中選取 [HDInsight 叢集]。|
        |叢集名稱/URL| 輸入叢集名稱。|
        |驗證類型| 保留 [基本驗證]|
        |使用者名稱| 輸入叢集使用者名稱，預設值是 admin。|
        |密碼| 輸入使用者名稱的密碼。|
    
        ![連結 hdinsight 叢集對話方塊](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy 服務**  
  
        |屬性 |值 |
        |----|----|
        |連結資源類型|從下拉式清單中選取 [Livy 服務]。|
        |Livy 端點| 輸入 Livy 端點|
        |叢集名稱| 輸入叢集名稱。|
        |Yarn 端點|選用。|
        |驗證類型| 保留 [基本驗證]|
        |使用者名稱| 輸入叢集使用者名稱，預設值是 admin。|
        |密碼| 輸入使用者名稱的密碼。|

        ![連結 livy 叢集對話方塊](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

4. 您可以在 **HDInsight** 節點中看到您已連結的叢集。

   ![連結的叢集](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

5. 您也可以從 [Azure 總管] 取消連結叢集。

   ![取消連結的叢集](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>在 HDInsight Spark 叢集上執行 Spark Scala 應用程式
建立 Scala 應用程式之後，您可以將它提交到叢集。

1. 從專案中，瀏覽至 [myApp] > [src] > [main] > [scala] > [myApp]。  以滑鼠右鍵按一下 [myApp]，然後選取 [提交 Spark 應用程式]\ (可能位於清單底部)。
    
      ![[將 Spark 應用程式提交給 HDInsight] 命令](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. 在 [提交 Spark 應用程式] 對話方塊視窗中，選取 **1.HDInsight 上的 Spark**。

3. 在 [編輯組態] 視窗中，提供下列值，然後選取 [確定]：

    |屬性 |值 |
    |----|----|
    |Spark 叢集 (僅限 Linux)|選取您要在其中執行應用程式的 HDInsight Spark 叢集。|
    |選取要提交的成品|保留預設值。|
    |主類別名稱|預設值是來自所選取檔案的主類別。 選取省略符號 (**...**) 並選擇另一個類別，即可變更類別。|
    |作業設定|您可以變更預設的金鑰和/或值。 如需詳細資訊，請參閱 [Apache Livy REST API](https://livy.incubator.apache.org./docs/latest/rest-api.html)。|
    |命令列引數|如有需要，您可以為主類別輸入以空格隔開的引數。|
    |參考的 Jar 和參考的檔案|您可以輸入參考的 Jar 和檔案的路徑 (如果有的話)。 其他資訊：[Apache Spark 設定](https://spark.apache.org/docs/latest/configuration.html#runtime-environment)。  也請參閱[如何將資源上傳至叢集](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer)。|
    |作業上傳儲存體|展開以顯示其他選項。|
    |儲存體類型|從下拉式清單選取 [使用 Azure Blob 上傳]。|
    |儲存體帳戶|輸入儲存體帳戶。|
    |儲存體金鑰|輸入儲存體金鑰。|
    |儲存體容器|輸入**儲存體帳戶**和**儲存體金鑰**後，從下拉式清單中選取您的儲存體容器。|

    ![[提交 Spark] 對話方塊](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

4. 選取 **SparkJobRun**，將您的專案提交至所選的叢集。 [叢集中的遠端 Spark 作業] 索引標籤會在底部顯示作業執行進度。 按一下紅色按鈕，即可停止應用程式。 若要了解如何存取作業輸出，請參閱本文稍後的＜使用適用於 IntelliJ 的 Azure 工具組來存取和管理 HDInsight Spark 叢集＞一節。  
      
    ![[提交 Spark] 視窗](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>本機或遠端偵錯 HDInsight 叢集上的 Apache Spark 應用程式 
我們也建議另一種將 Spark 應用程式提交至叢集的方式。 做法是在 [執行/偵錯設定] IDE 中設定參數。 如需詳細資訊，請參閱[使用 Azure Toolkit for IntelliJ 透過 SSH 本機或遠端偵錯 HDInsight 叢集上的 Apache Spark 應用程式](apache-spark-intellij-tool-debug-remotely-through-ssh.md)。

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>使用適用於 IntelliJ 的 Azure 工具組來存取和管理 HDInsight Spark 叢集
您可以使用適用於 IntelliJ 的 Azure 工具組來執行各種作業。  大部分的作業都起始於 **Azure Explorer**。  從功能表列中，瀏覽至 [檢視] > [工具視窗] > [Azure Explorer]。

### <a name="access-the-job-view"></a>存取作業檢視

1. 從 [Azure Explorer] 中，瀏覽至 [HDInsight] > \<您的叢集 > > [作業]。

    ![作業檢視節點](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. 在右窗格中，[Spark 作業檢視]  索引標籤會顯示已在叢集上執行的所有應用程式。 選取您想要查看更多詳細資料的應用程式名稱。

    ![應用程式詳細資料](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. 若要顯示基本的執行作業資訊，請將滑鼠停留在作業圖形上。 若要檢視每項作業產生的階段圖形和資訊，請選取作業圖形上的節點。

    ![作業階段詳細資料](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. 若要檢視經常使用的記錄 (例如「驅動程式 Stderr」、「驅動程式 Stdout」和「目錄資訊」)，請選取 [記錄] 索引標籤。

    ![記錄詳細資料](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. 您也可以選取視窗頂端的連結，在應用程式層級檢視 Spark 歷程記錄 UI 和 YARN UI。

### <a name="access-the-spark-history-server"></a>存取 Spark 歷程記錄伺服器

1. 在 [Azure Explorer] 中，展開 [HDInsight]、以滑鼠右鍵按一下您的 Spark 叢集名稱，然後選取 [開啟 Spark 歷程記錄 UI]。  
2. 出現提示時，輸入叢集的系統管理員認證，也就是您在設定叢集時所指定的認證。

3. 在 [Spark 歷程記錄伺服器] 儀表板上，您可以使用應用程式名稱，尋找您剛完成執行的應用程式。 在上述程式碼中，您可以使用 `val conf = new SparkConf().setAppName("myApp")`來設定應用程式名稱。 因此，Spark 應用程式名稱為 **myApp**。

### <a name="start-the-ambari-portal"></a>啟動 Ambari 入口網站

1. 在 [Azure Explorer] 中，展開 [HDInsight]、以滑鼠右鍵按一下您的 Spark 叢集名稱，然後選取 [開啟叢集管理入口網站 (Ambari)]。  

2. 出現提示時，輸入叢集的系統管理員認證。 您在叢集設定程序期間已指定這些認證。

### <a name="manage-azure-subscriptions"></a>管理 Azure 訂用帳戶
根據預設，適用於 IntelliJ 的 Azure 工具組會列出您所有 Azure 訂用帳戶中的 Spark 叢集。 如有必要，您可以指定要存取的訂用帳戶。  

1. 在 [Azure Explorer] 中，以滑鼠右鍵按一下 [Azure] 根節點，然後選取 [選取訂用帳戶]。  

2. 在 [選取訂用帳戶] 視窗中，針對您不想要存取的訂用帳戶，清除其旁邊的核取方塊，然後選取 [關閉]。

## <a name="spark-console"></a>Spark 主控台
您可以執行 Spark 本機主控台 (Scala)，或執行 Spark Livy 互動式工作階段主控台 (Scala)。

### <a name="spark-local-consolescala"></a>Spark 本機主控台 (Scala)
請確定您符合 WINUTILS.EXE 的必要條件。

1. 從功能表列中，瀏覽至 [執行] > [編輯組態...]。

2. 在 [執行/偵錯組態] 視窗中，從左側窗格瀏覽至 [HDInsight 上的 Apache Spark] > [HDInsight 上的 Spark]。

3. 從主視窗中，選取 [在本機執行] 索引標籤。

4. 提供下列值，然後選取 [確定]：

    |屬性 |值 |
    |----|----|
    |作業主類別|預設值是來自所選取檔案的主類別。 選取省略符號 (**...**) 並選擇另一個類別，即可變更類別。|
    |環境變數|請確定 HADOOP_HOME 的值正確無誤。|
    |WINUTILS.exe 位置|請確定路徑正確無誤。|

    ![本機主控台設定組態](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. 從專案中，瀏覽至 [myApp] > [src] > [main] > [scala] > [myApp]。  

6. 從功能表列中，瀏覽至 [工具] > [Spark 主控台] > [執行 Spark 本機主控台 (Scala)]。

7. 接著會顯示兩個對話方塊，詢問您是否要自動修正相依性。 若是如此，請選取 [自動修正]。

    ![Spark 自動修正 1](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Spark 自動修正 2](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

8. 主控台將類似於下圖。 在主控台視窗中，輸入 `sc.appName`，然後按下 ctrl+Enter。  系統將顯示結果。 按一下紅色按鈕，即可終止本機主控台。

    ![本機主控台結果](./media/apache-spark-intellij-tool-plugin/local-console-result.png)


### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy 互動式工作階段主控台 (Scala)
僅在 IntelliJ 2018.2 和 2018.3 上提供支援。

1. 從功能表列中，瀏覽至 [執行] > [編輯組態...]。

2. 在 [執行/偵錯組態] 視窗中，從左側窗格瀏覽至 [HDInsight 上的 Apache Spark] > [HDInsight 上的 Spark]。

3. 從主視窗中，選取 [在叢集中遠端執行] 索引標籤。

4. 提供下列值，然後選取 [確定]：

    |屬性 |值 |
    |----|----|
    |Spark 叢集 (僅限 Linux)|選取您要在其中執行應用程式的 HDInsight Spark 叢集。|
    |主類別名稱|預設值是來自所選取檔案的主類別。 選取省略符號 (**...**) 並選擇另一個類別，即可變更類別。|

    ![互動式主控台的設定組態](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. 從專案中，瀏覽至 [myApp] > [src] > [main] > [scala] > [myApp]。  

6. 從功能表列中，瀏覽至 [工具] > [Spark 主控台] > [執行 Spark Livy 互動式工作階段主控台 (Scala)]。

7. 主控台將類似於下圖。 在主控台視窗中，輸入 `sc.appName`，然後按下 ctrl+Enter。  系統將顯示結果。 按一下紅色按鈕，即可終止本機主控台。

    ![互動式主控台結果](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>將選取項目傳送至 Spark 主控台

將一些程式碼傳送至本機主控台或 Livy 互動式工作階段主控台 (Scala)，方便您預見指令碼結果。 您可以在 Scala 檔案中醒目提示某些程式碼，然後以滑鼠右鍵按一下 [將選取項目傳送至 Spark 主控台]。 所選的程式碼會傳送至主控台並加以執行。 結果會在主控台中顯示於程式碼之後。 主控台會檢查是否有錯誤。  

   ![將選取項目傳送至 Spark 主控台](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>轉換現有的 IntelliJ IDEA 應用程式，來使用適用於 IntelliJ 的 Azure 工具組

您可以將 IntelliJ IDEA 中建立的現有 Spark Scala 應用程式轉換成與適用於 IntelliJ 的 Azure 工具組相容。 然後您可以使用外掛程式，將應用程式提交給 HDInsight Spark 叢集。

1. 對於透過 IntelliJ IDEA 建立的現有 Spark Scala 應用程式，請開啟關聯的 .iml 檔案。

1. 根層級中有 **module** 項目，如下所示：
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   編輯該元素以加入 `UniqueKey="HDInsightTool"` ，使 **module** 元素看起來如下所示：
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. 儲存變更。 您的應用程式現在應該可與適用於 IntelliJ 的 Azure 工具組相容。 您可以滑鼠右鍵按一下專案中的專案名稱來進行測試。 現在，快顯功能表提供 [將 Spark 應用程式提交給 HDInsight] 的選項。

## <a name="troubleshooting"></a>疑難排解

### <a name="error-in-local-run-use-a-larger-heap-size"></a>本機執行的錯誤：「使用較大的堆積大小」
在 Spark 1.6 中，如果您在本機執行期間是使用 32 位元的 Java SDK，可能會遇到下列錯誤︰

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

會發生這些錯誤是因為堆積大小不足，使 Spark 無法執行。 Spark 需要至少 471 MB (如需詳細資訊，請參閱 [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081))。其中一個簡單的解決方案就是使用 64 位元的 Java SDK。 您也可以新增下列選項來變更 IntelliJ 中的 JVM 設定：

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![IntelliJ 中的將選項新增至 [VM 選項] 方塊](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>常見問題集
如果叢集忙碌，您可能會收到以下錯誤。

![Intellij 會在叢集忙碌時收到錯誤](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![Intellij 會在叢集忙碌時收到錯誤](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>意見反應和已知問題
目前，不支援直接檢視 Spark 輸出。

如果您有任何建議或意見反應，或使用此外掛程式時遇到任何問題，請將電子郵件傳送到 hdivstool@microsoft.com。

## <a name="seealso"></a>接續步驟
* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="demo"></a>示範
* 建立 Scala 專案 (影片)：[建立 Apache Spark Scala 應用程式](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) \(英文\)
* 遠端偵錯 (影片)：[使用 Azure Toolkit for IntelliJ 對 HDInsight 叢集上的 Apache Spark 應用程式進行遠端偵錯](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) \(英文\)

### <a name="scenarios"></a>案例
* [Apache Spark 搭配 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](apache-spark-use-bi-tools.md)
* [Apache Spark 搭配機器學習服務：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 搭配機器學習服務：使用 HDInsight 中的 Spark 來預測食品檢查結果](apache-spark-machine-learning-mllib-ipython.md)
* [在 HDInsight 中使用 Apache Spark 進行網站記錄分析](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>建立和執行應用程式
* [使用 Scala 建立獨立應用程式](apache-spark-create-standalone-application.md)
* [利用 Apache Livy 在 Apache Spark 叢集上遠端執行作業](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和擴充功能
* [使用 Azure Toolkit for IntelliJ 透過 VPN 遠端偵錯 Apache Spark 應用程式](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用 Azure Toolkit for IntelliJ 透過 SSH 遠端偵錯 Apache Spark 應用程式](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [透過 Hortonworks 沙箱使用 HDInsight Tools for IntelliJ](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具建立 Apache Spark 應用程式](apache-spark-eclipse-tool-plugin.md)
* [在 HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集](apache-spark-zeppelin-notebook.md)
* [HDInsight Apache Spark 叢集中 Jupyter Notebook 的可用核心](apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>管理資源
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](apache-spark-job-debugging.md)
