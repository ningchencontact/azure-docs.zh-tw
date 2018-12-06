---
title: 'Azure Toolkit for IntelliJ：建立適用於 HDInsight 叢集的 Spark 應用程式 '
description: 使用適用於 IntelliJ 的 Azure 工具組來開發以 Scala 撰寫的 Spark 應用程式，並將它們提交到 HDInsight Spark 叢集。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: maxluk
ms.openlocfilehash: b2bf79d90c741e09c683e4520b05b31ba2fee1da
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582762"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>使用 Azure Toolkit for IntelliJ 為 HDInsight 叢集建立 Apache Spark 應用程式

使用 Azure Toolkit for IntelliJ 外掛程式來開發以 [Scala](https://www.scala-lang.org/) 撰寫的 [Apache Spark](https://spark.apache.org/) 應用程式，然後直接從 IntelliJ 整合式開發環境 (IDE) 將它們提交到 HDInsight Spark 叢集。 您可以利用數個方式來使用此外掛程式：

* 在 HDInsight Spark 叢集上開發並提交 Scala Spark 應用程式。
* 存取您的 Azure HDInsight Spark 叢集資源。
* 在本機開發並執行 Scala Spark 應用程式。

若要建立您的專案，請觀看 [Create Apache Spark Applications with the Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (使用 Azure Toolkit for IntelliJ 建立 Apache Spark 應用程式) 影片。

> [!IMPORTANT]
> 您可以使用此外掛程式僅針對 Linux 上的 HDInsight Spark 叢集建立並提交應用程式。
> 

## <a name="prerequisites"></a>必要條件

- HDInsight Linux 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。
- Oracle Java Development Kit。 您可以從 [Oracle 網站](https://aka.ms/azure-jdks)下載。
- IntelliJ IDEA。 本文章使用 2017.1 版。 您可以從 [JetBrains 網站](https://www.jetbrains.com/idea/download/)下載。

## <a name="install-azure-toolkit-for-intellij"></a>安裝適用於 IntelliJ 的 Azure 工具組
如需安裝指示，請參閱[安裝適用於 IntelliJ 的 Azure 工具組](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation)。

## <a name="get-started"></a>開始使用
使用者可以[登入 Azure 訂用帳戶](#sign-in-to-your-azure-subscription)，或是使用 Ambari 使用者名稱/密碼或加入網域的認證開始[連結 HDInsight 叢集](#link-a-cluster)。


## <a name="sign-in-to-your-azure-subscription"></a>登入您的 Azure 訂用帳戶：

1. 啟動 IntelliJ IDE，然後開啟 [Azure Explorer]。 在 [檢視] 功能表上，選取 [工具視窗]，然後選取 [Azure Explorer]。
       
   ![[Azure Explorer] 連結](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

1. 以滑鼠右鍵按一下 [Azure] 節點，然後選取 [登入]。

1. 在 [Azure 登入] 對話方塊中，選取 [登入]，然後輸入您的 Azure 認證。

    ![[Azure 登入] 對話方塊](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

1. 登入之後，[選取訂用帳戶] 對話方塊會列出與認證建立關聯的所有 Azure 訂用帳戶。 選取 [選取] 按鈕。

    ![[選取訂用帳戶] 對話方塊](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

1. 在 [Azure Explorer] 索引標籤中展開 [HDInsight]，可檢視您訂用帳戶中的 HDInsight Spark 叢集。
   
    ![Azure Explorer 中的 HDInsight Spark 叢集](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

1. 若要檢視與叢集建立關聯的資源 (例如儲存體帳戶)，您可以進一步展開叢集名稱節點。
   
    ![展開的叢集名稱節點](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>連結叢集
您可以使用 Apache Ambari 受控使用者名稱來連結一般的 HDInsight 叢集。 同樣地，對於已加入網域的 HDInsight 叢集，您可以使用網域和使用者名稱進行連結，例如 user1@contoso.com。 您也可以連結 Livy 服務叢集。

1. 從 [Azure 總管] 中選取 [連結叢集]。

   ![連結叢集操作功能表](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

2. 您有兩個選項可連結叢集。 

   * 如需連結 HDInsight 叢集，請在 [叢集資訊] 欄位中選擇 [HDInsight 叢集]，輸入 [叢集名稱/URL]、[使用者名稱] 及 [密碼]。

      ![連結 hdinsight 叢集對話方塊](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

   * 如需連結 Livy 服務叢集，請在 [叢集資訊] 欄位中選擇 [Livy 服務]，輸入 [Livy 端點]、[叢集名稱]。 [Yarn 端點] 是選擇性的。 [驗證] 欄位會提供兩個選項。 它們 [基本驗證] 和 [不需要驗證]。 當您選取 [基本驗證] 時，應提供 [使用者名稱] 和 [密碼]。 如果驗證失敗，您需要檢查使用者名稱和密碼。
      
      ![連結 livy 叢集對話方塊](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)
   
3. 如果輸入資訊正確無誤，您可以在 [HDInsight] 節點中看見連結的叢集。 您現在可以將應用程式提交至此連結的叢集。

   ![連結的叢集](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

4. 您也可以從 [Azure 總管] 取消連結叢集。
   
   ![取消連結的叢集](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="create-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>在 HDInsight Spark 叢集上建立 Spark Scala 應用程式

1. 啟動 IntelliJ IDEA，然後建立專案。 在 [新增專案] 對話方塊中，遵循下列步驟： 

   a. 選取 [HDInsight] > [HDInsight 上的 Spark (Scala)]。

   b. 在 [建置工具] 清單中，根據您的需求選取下列任一項目：

      * **Maven**，以支援 Scala 專案建立精靈
      * **SBT**，以管理相依性並建置 Scala 專案

    ![[新增專案] 對話方塊](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

1. 選取 [下一步] 。

1. Scala 專案建立精靈會自動偵測您是否已安裝 Scala 外掛程式。 選取 [安裝]。

   ![Scala 外掛程式檢查](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

1. 若要下載 Scala 外掛程式，請選取 [確定]。 依指示重新啟動 IntelliJ。 

   ![Scala 外掛程式安裝對話方塊](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

1. 在 [新增專案] 視窗中，執行下列動作：  

    ![選取 Spark SDK](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. 輸入專案名稱和位置。

   b. 在 [專案 SDK] 下拉式清單中，選取 [Java 1.8] \(適用於 Spark 2.x 叢集)，或選取 [Java 1.7] \(適用於 Spark 1.x 叢集)。

   c. 在 [Spark 版本] 下拉式清單中，Scala 專案建立精靈會為 Spark SDK 和 Scala SDK 整合正確的版本。 如果 Spark 叢集是 2.0 以前的版本，請選取 [Spark 1.x]。 否則，請選取 [Spark2.x]。 此範例使用 **Spark 2.0.2 (Scala 2.11.8)**。

1. 選取 [完成]。

1. Spark 專案會自動為您建立構件。 若要檢視構件，請執行下列動作：

   a. 在 [檔案] 功能表上，選取 [專案結構]。

   b. 在 [專案結構] 對話方塊中，選取 [構件]，以檢視建立的預設構件。 您也可以建立自己的構件，方法是選取加號 (**+**)。

      ![對話方塊中的構件資訊](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
1. 若要新增應用程式的原始程式碼，請執行下列動作：

   a. 在專案總管中，以滑鼠右鍵按一下 [src]、指向 [新增]，然後選取 [Scala 類別]。
      
      ![從 [專案總管] 中建立 Scala 類別的命令](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. 在 [建立新的 Scala 類別] 對話方塊中，提供一個名稱，並在 [種類] 方塊中選取 [物件]，然後選取 [確定]。
      
      ![建立新的 Scala 類別對話方塊](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. 在 **MyClusterApp.scala** 檔案中，貼入下列程式碼。 此程式碼會從 HVAC.csv (所有 HDInsight Spark 叢集上均提供) 讀取資料、擷取在 CSV 檔案的第七個資料行中只有一個位數的資料列，並將輸出寫入叢集預設儲存體容器下的 **/HVACOut** 。

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object MyClusterApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACOut")
            }
    
        }

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>在 HDInsight Spark 叢集上執行 Spark Scala 應用程式
建立 Scala 應用程式之後，您可以將它提交到叢集。

1. 在 [專案總管] 中，找出 Java 或 Scala 檔案，然後在滑鼠右鍵功能表中選取 [將 Spark 應用程式提交給 HDInsight]。
    
      ![[將 Spark 應用程式提交給 HDInsight] 命令](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. 在 [設定] 對話方塊視窗中，請提供下列值，然後按一下 **SparkJobRun**。

      ![[提交 Spark] 對話方塊](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
      
    * 針對 [Spark clusters (Linux only) (Spark 叢集 (僅限 Linux))] ，選取您要在其上執行應用程式的 HDInsight Spark 叢集。

    * 從 IntelliJ 專案中選取構件，或從硬碟中選取一個。

    * **主要類別名稱**欄位：預設值是所選檔案中的主要類別。 選取省略符號 (**...**) 並選擇另一個類別，即可變更類別。   

    * **作業組態**欄位：預設值的設定如上圖所示。 您可以為您的作業提交作業，變更此值或新增索引鍵/值。 如需詳細資訊：[Apache Livy REST API](http://livy.incubator.apache.org./docs/latest/rest-api.html)

      ![[提交 Spark] 對話方塊的作業設定說明](./media/apache-spark-intellij-tool-plugin/submit-job-configurations.png)

    * **命令列引數**欄位：如有需要，您可以為主要類別輸入以空格分割的引數值。

    * **參考 Jar** 和**參考檔案**欄位：您可以輸入所參考 Jar 和檔案 (如果有的話) 的路徑。 如需詳細資訊：[Apache Spark Configuration](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) (Apache Spark 設定) 

      ![[提交 Spark] 對話方塊的 jar 檔案說明](./media/apache-spark-intellij-tool-plugin/jar-files-meaning.png)

       > [!NOTE]
       > 若要上傳參考的 JAR 和參考的檔案，請參閱：[如何將資源上傳至叢集](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer)
                         
    * **上傳路徑**：您可以指定 Jar 或 Scala 專案資源提交的儲存位置。 支援的儲存體類型有三種：**Azure Blob**、[使用 Spark 互動式工作階段來上傳成品] 和 [使用叢集預設儲存體帳戶]，以及 [ADLS Gen1]。 以下螢幕擷取畫面是 Azure Blob 的範例。

        ![[提交 Spark] 對話方塊](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-types.png)

        ![[提交 Spark] 對話方塊](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-blob.png)

3. 按一下 **SparkJobRun**，將您的專案提交至所選的叢集。 [叢集中的遠端 Spark 作業] 索引標籤會在底部顯示作業執行進度。 按一下紅色按鈕，即可停止應用程式。 若要了解如何存取作業輸出，請參閱本文稍後的＜使用適用於 IntelliJ 的 Azure 工具組來存取和管理 HDInsight Spark 叢集＞一節。
      
     ![[提交 Spark] 視窗](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)


## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>本機或遠端偵錯 HDInsight 叢集上的 Apache Spark 應用程式 
我們也建議另一種將 Spark 應用程式提交至叢集的方式。 做法是在 [執行/偵錯設定] IDE 中設定參數。 如需詳細資訊，請參閱[使用 Azure Toolkit for IntelliJ 透過 SSH 本機或遠端偵錯 HDInsight 叢集上的 Apache Spark 應用程式](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh)。



## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>使用適用於 IntelliJ 的 Azure 工具組來存取和管理 HDInsight Spark 叢集
您可以使用適用於 IntelliJ 的 Azure 工具組來執行各種作業。

### <a name="access-the-job-view"></a>存取作業檢視
1. 在 [Azure Explorer] 中，依序展開 [HDInsight] 和 Spark 叢集名稱，然後選取 [作業]。  

    ![作業檢視節點](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

1. 在右窗格中，[Spark 作業檢視]  索引標籤會顯示已在叢集上執行的所有應用程式。 選取您想要查看更多詳細資料的應用程式名稱。

    ![應用程式詳細資料](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

1. 若要顯示基本的執行作業資訊，請將滑鼠停留在作業圖形上。 若要檢視每項作業產生的階段圖形和資訊，請選取作業圖形上的節點。

    ![作業階段詳細資料](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

1. 若要檢視經常使用的記錄 (例如「驅動程式 Stderr」、「驅動程式 Stdout」和「目錄資訊」)，請選取 [記錄] 索引標籤。

    ![記錄詳細資料](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

1. 您也可以選取視窗頂端的連結，在應用程式層級檢視 Spark 歷程記錄 UI 和 YARN UI。

### <a name="access-the-spark-history-server"></a>存取 Spark 歷程記錄伺服器
1. 在 [Azure Explorer] 中，展開 [HDInsight]、以滑鼠右鍵按一下您的 Spark 叢集名稱，然後選取 [開啟 Spark 歷程記錄 UI]。 

1. 出現提示時，輸入叢集的系統管理員認證，也就是您在設定叢集時所指定的認證。

1. 在 [Spark 歷程記錄伺服器] 儀表板上，您可以使用應用程式名稱，尋找您剛完成執行的應用程式。 在上述程式碼中，您可以使用 `val conf = new SparkConf().setAppName("MyClusterApp")`來設定應用程式名稱。 因此，Spark 應用程式名稱為 **MyClusterApp**。

### <a name="start-the-ambari-portal"></a>啟動 Ambari 入口網站
1. 在 [Azure Explorer] 中，展開 [HDInsight]、以滑鼠右鍵按一下您的 Spark 叢集名稱，然後選取 [開啟叢集管理入口網站 (Ambari)]。 

1. 出現提示時，輸入叢集的系統管理員認證。 您在叢集設定程序期間已指定這些認證。

### <a name="manage-azure-subscriptions"></a>管理 Azure 訂用帳戶
根據預設，適用於 IntelliJ 的 Azure 工具組會列出您所有 Azure 訂用帳戶中的 Spark 叢集。 如有必要，您可以指定要存取的訂用帳戶。 

1. 在 [Azure Explorer] 中，以滑鼠右鍵按一下 [Azure] 根節點，然後選取 [管理訂用帳戶]。 

1. 在對話方塊中，清除您不想要存取之訂用帳戶旁的核取方塊，然後選取 [關閉]。 如果您想要登出 Azure 訂用帳戶，也可以選取 [登出]。

## <a name="spark-console"></a>Spark 主控台
您可以執行 Spark 本機主控台 (Scala)，或執行 Spark Livy 互動式工作階段主控台 (Scala)。

### <a name="spark-local-consolescala"></a>Spark 本機主控台 (Scala)
1. 如果您之前沒有任何組態，請加以設定。 在 [執行/偵錯組態] 視窗中，按一下 **+**->[Azure HDInsight Spark]，選取 [在本機執行] 和 [從遠端在叢集中執行]，選擇主要類別，然後按一下 [確定]。

    ![本機主控台設定組態](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)
 
2. 開啟對應的主要類別檔案，並以滑鼠右鍵按一下 [Spark 主控台]，然後按一下 [執行 Spark 本機主控台 (Scala)]。 或移至 [工具] 功能表->[Spark 主控台]->[執行 Spark 本機主控台 (Scala)] 以啟動主控台。 接著會顯示兩個對話方塊，詢問您是否要自動修正相依性。 只要按一下 [自動修正] 按鈕即可。

    ![Spark 自動修正 1](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Spark 自動修正 2](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

    ![Spark 本機進入點](./media/apache-spark-intellij-tool-plugin/spark-console-local-entry-script.png)

3. 在成功啟動本機主控台之後。 它看起來如下所示。 您可以執行您想要的動作。 例如，輸入 **sc.appName**，按 ctrl+Enter，然後會顯示結果。 按一下紅色按鈕，即可終止本機主控台。

    ![本機主控台結果](./media/apache-spark-intellij-tool-plugin/local-console-result.png)


### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy 互動式工作階段主控台 (Scala)
僅在 IntelliJ 2018.2 和 2018.3 上提供支援。

1. 如果您之前沒有任何組態，請加以設定。 在 [執行/偵錯組態] 視窗中，按一下**+**->[Azure HDInsight Spark]，選取 [從遠端在叢集中執行]，選擇叢集名稱和主要類別，然後按一下 [確定]。

    ![互動式主控台的新增組態項目](./media/apache-spark-intellij-tool-plugin/interactive-console-add-config-entry.png)

    ![互動式主控台的設定組態](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

2. 開啟主要類別的對應檔案，並以滑鼠右鍵按一下 [Spark 主控台]，然後按一下 [執行 Spark Livy 互動式工作階段主控台 (Scala)]。 或移至 [工具] 功能表，按一下 [Spark 主控台]，然後按一下 [執行 Spark Livy 互動式工作階段主控台 (Scala)] 以啟動主控台。

3. 在成功啟動主控台之後，您可以執行您想要的動作。 例如，輸入 **sc.appName**，按 ctrl+Enter，然後會顯示結果。

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

1. 儲存變更。 您的應用程式現在應該可與適用於 IntelliJ 的 Azure 工具組相容。 您可以滑鼠右鍵按一下 [專案總管] 中的專案名稱來進行測試。 現在，快顯功能表提供 [將 Spark 應用程式提交給 HDInsight] 的選項。

## <a name="troubleshooting"></a>疑難排解

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>本機執行的錯誤：「請使用較大的堆積大小」
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
* [概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="demo"></a>示範
* 建立 Scala 專案 (影片)：[Create Apache Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (建立 Apache Spark Scala 應用程式)
* 遠端偵錯 (影片)：[Use Azure Toolkit for IntelliJ to debug Apache Spark applications remotely on HDInsight Cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (使用 Azure Toolkit for IntelliJ 遠端偵錯 HDInsight 叢集上的 Apache Spark 應用程式)

### <a name="scenarios"></a>案例
* [Apache Spark 和 BI：在 HDInsight 中搭配 BI 工具使用 Spark 執行互動式資料分析](apache-spark-use-bi-tools.md)
* [Apache Spark 和機器學習服務：在 HDInsight 中利用 HVAC 資料使用 Spark 分析建築物溫度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 和機器學習服務：在 HDInsight 中使用 Spark 預測食品檢查結果](apache-spark-machine-learning-mllib-ipython.md)
* [在 HDInsight 中使用 Apache Spark 進行網站記錄分析](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>建立和執行應用程式
* [使用 Scala 建立獨立應用程式](apache-spark-create-standalone-application.md)
* [使用 Livy 遠端執行 Apache Spark 叢集上的作業](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和擴充功能
* [使用 Azure Toolkit for IntelliJ 透過 VPN 遠端偵錯 Apache Spark 應用程式](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用 Azure Toolkit for IntelliJ 透過 SSH 遠端偵錯 Apache Spark 應用程式](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [透過 Hortonworks 沙箱使用 HDInsight Tools for IntelliJ](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具建立 Apache Spark 應用程式](apache-spark-eclipse-tool-plugin.md)
* [在 HDInsight 上搭配 Apache Spark 叢集使用 Apache Zeppelin Notebook](apache-spark-zeppelin-notebook.md)
* [HDInsight Apache Spark 叢集中 Jupyter Notebook 的可用核心](apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>管理資源
* [在 Azure HDInsight 中管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](apache-spark-job-debugging.md)
