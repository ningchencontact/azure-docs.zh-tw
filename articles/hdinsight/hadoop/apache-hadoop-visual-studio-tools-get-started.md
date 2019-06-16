---
title: 使用 Data Lake Tools for Visual Studio 來連線至 Apache Hadoop - Azure HDInsight
description: 了解如何安裝和使用 Data Lake Tools for Visual Studio 來連線到 Azure HDInsight 中的 Apache Hadoop 叢集，然後執行 Hive 查詢。
keywords: hadoop 工具,hive 查詢,visual studio,visual studio hadoop
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 42ef03d604caacf5ba18773b88e892237b5f0eae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66688539"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>使用 Data Lake Tools for Visual Studio 連線至 Azure HDInsight 及執行 Apache Hive 查詢

了解如何使用[Microsoft Azure Data Lake 和 Stream Analytics Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) (也稱為的 Data Lake Tools) 來連線到 Apache Hadoop 叢集[Azure HDInsight](../hdinsight-hadoop-introduction.md)及提交 Hive 查詢。  

如需使用 HDInsight 的詳細資訊，請參閱 [HDInsight 簡介](../hdinsight-hadoop-introduction.md)和[開始使用 HDInsight](apache-hadoop-linux-tutorial-get-started.md)。  

如需連線到 Apache Storm 叢集的詳細資訊，請參閱[使用 Visual Studio 開發 HDInsight 上 Apache Storm 的 C# 拓撲](../storm/apache-storm-develop-csharp-visual-studio-topology.md)。

您可以使用 Data Lake Tools for Visual Studio 來存取 Azure Data Lake Analytics 和 HDInsight。 如需 Data Lake Tools 的相關資訊，請參閱[使用 Data Lake Tools for Visual Studio 開發 U-SQL 指令碼](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md)。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程並使用 Data Lake Tools for Visual Studio，您需要下列項目：

* Azure HDInsight 叢集。 若要建立 HDInsight 叢集，請參閱[在 Azure HDInsight 中開始使用 Apache Hadoop](apache-hadoop-linux-tutorial-get-started.md)。 若要執行互動式 Apache Hive 查詢，您需要 [HDInsight 互動式查詢](../interactive-query/apache-interactive-query-get-started.md)叢集。  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) （2013年或更新版本）。  [Visual Studio Community edition](https://visualstudio.microsoft.com/vs/community/)是免費的。  此外，請參閱 <<c0> [ 安裝 Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio)並[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。 有輕微的介面與 Visual Studio 2019 的變化。

  > [!IMPORTANT]  
  > 不再支援 data Lake Tools for Visual Studio 2013。

## <a name="install-data-lake-tools-for-visual-studio"></a>安裝 Data Lake Tools for Visual Studio  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017 或 Visual Studio 2019  
  在安裝期間，請確定您至少包含工作負載**Azure 開發**或是**資料儲存和處理**。  

  針對現有的安裝中，從功能表列中，瀏覽至**工具** > **取得工具與功能...** 來開啟 Visual Studio 安裝程式。  然後選取 最小的工作負載**Azure 開發**或是**資料儲存和處理**。

  ![Visual Studio 安裝程式的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/vs2017_installation.png)

* Visual Studio 2013 和 2015  
  [下載 Data Lake Tools](https://www.microsoft.com/download/details.aspx?id=49504)。 選擇與您的 Visual Studio 版本相符的 Data Lake Tools 版本。  

> [!NOTE]  
> Data Lake Tools for Visual Studio 目前只有英文版。

## <a name="update-data-lake-tools-for-visual-studio"></a>更新 Data Lake Tools for Visual Studio  

1. 開啟 Visual Studio。

2. 從功能表列中，瀏覽至**工具** > **擴充功能和更新...** .

3. 從**擴充功能和更新** 視窗中，展開**更新**左側。

4. 如果有可用的更新**Azure Data Lake and Stream 分析工具**會出現在主視窗。  選取 [更新]  。

> [!NOTE]  
> 您只可以使用 Data Lake Tools 2.3.0.0 版或更新版本，連線到互動式查詢叢集及執行互動式 Hive 查詢。

## <a name="connect-to-azure-subscriptions"></a>連線到 Azure 訂用帳戶
您可以使用 Data Lake Tools for Visual Studio 連線到您的 HDInsight 叢集、執行一些基本管理作業，以及執行 Hive 查詢。

> [!NOTE]  
> 如需連線到一般 Hadoop 叢集的相關資訊，請參閱[使用 Visual Studio 撰寫和提交 Hive 查詢](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx)。

若要連線到您的 Azure 訂用帳戶：

1. 開啟 Visual Studio。

2. 從功能表列中，瀏覽至**檢視** > **伺服器總管**。

3. 從伺服器總管 中，以滑鼠右鍵按一下**Azure**，選取**連接到 Microsoft Azure 訂用帳戶...** ，並完成登入程序。

4. 伺服器總管 中，從現有的 HDInsight 叢集的清單隨即出現。 如果您沒有任何叢集，可以使用 Azure 入口網站、Azure PowerShell 或 HDInsight SDK 來建立一個。 如需詳細資訊，請參閱[建立 HDInsight 叢集](../hdinsight-hadoop-provision-linux-clusters.md)。

   ![伺服器總管中的 Data Lake Tools for Visual Studio 叢集清單的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png "伺服器總管中的 Data Lake Tools for Visual Studio 叢集清單")

5. 展開某個 HDInsight 叢集。 **Hive 資料庫**，預設儲存體帳戶、 連結的儲存體帳戶，並**Hadoop 服務記錄**出現。 您可以進一步展開這些實體。

您已連接到您的 Azure 訂用帳戶之後，您可以執行下列工作。

若要從 Visual Studio 連線到 Azure 入口網站：

1. 從伺服器總管 中，瀏覽至**Azure** > **HDInsight** ，然後選取您的叢集。

2. 以滑鼠右鍵按一下 HDInsight 叢集，然後選取**管理的叢集，在 Azure 入口網站中 [sic]** 。

若要提出問題及/或從 Visual Studio 提供意見反應：

1. 從伺服器總管 中，瀏覽至**Azure** > **HDInsight**。

2. 以滑鼠右鍵按一下**HDInsight** ，然後選取**MSDN 論壇**提出問題、 或是**提供意見反應**提供意見反應。

## <a name="link-a-cluster"></a>連結叢集
您可以連結在叢集上按一下滑鼠右鍵**HDInsight**然後選取**HDInsight 叢集連結**。 輸入**連線 Url**，**使用者名稱**並**密碼**，按一下 [**下一步]** 然後**完成**，叢集應該會列出成功的 HDInsight 節點下。

![螢幕擷取畫面的 Data Lake Tools for Visual Studio 連結叢集對話方塊](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

以滑鼠右鍵按一下連結叢集，請選取**編輯**，使用者可以更新叢集資訊。 新增 HDInsight 叢集現在僅支援 Hive。

![螢幕擷取畫面的 Data Lake Tools for Visual Studio 連結叢集更新](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>瀏覽連結的資源
從 [伺服器總管] 中，您可以看到預設的儲存體帳戶，以及任何連結的儲存體帳戶。 如果您展開預設儲存體帳戶，您可以看到儲存體帳戶上的容器。 預設儲存體帳戶和預設容器皆已標示。 在任何容器上按一下滑鼠右鍵以檢視容器內容。

![伺服器總管中 Data Lake Tools for Visual Studio 清單連結資源的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png "清單連結資源")

開啟容器之後，您可以使用下列按鈕來上傳、刪除及下載 Blob：

![伺服器總管中 Data Lake Tools for Visual Studio Blob 作業的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png "在伺服器總管中更新、刪除和下載 Blob")

## <a name="run-interactive-apache-hive-queries"></a>執行互動式 Apache Hive 查詢
[Apache Hive](https://hive.apache.org) 是以 Hadoop 為基礎的資料倉儲基礎結構。 Hive 用於進行資料彙整、查詢及分析。 您可以使用 Data Lake Tools for Visual Studio 從 Visual Studio 執行 Hive 查詢。 如需有關 Hive 的詳細資訊，請參閱[將 Apache Hive 與 HDInsight 搭配使用](hdinsight-use-hive.md)。

[互動式查詢](../interactive-query/apache-interactive-query-get-started.md)在 Apache Hive 2.1 中使用 [LLAP 上的 Hive](https://cwiki.apache.org/confluence/display/Hive/LLAP)。 互動式查詢可將互動功能整合到已儲存的大型資料集上的複雜資料倉儲樣式查詢。 相較於傳統的 Hive 批次作業，在互動式查詢上執行 Hive 查詢比較快速。 

> [!NOTE]  
> 只有在您連線到 [HDInsight 互動式查詢](../interactive-query/apache-interactive-query-get-started.md)叢集時，您才可以執行互動式 Hive 查詢。

您也可以使用 Data Lake Tools for Visual Studio 來查看 Hive 作業的內容。 Data Lake Tools for Visual Studio 會收集和呈現特定 Hive 作業的 Yarn 記錄。

從伺服器總管 中，瀏覽至**Azure** > **HDInsight** ，然後選取您的叢集。  這會是區段進行的伺服器總管 中的起始點。

### <a name="view-hivesampletable"></a>檢視 hivesampletable
所有 HDInsight 叢集都有一個稱為的預設範例 Hive 資料表`hivesampletable`。  

從您的叢集，瀏覽至**Hive 資料庫** > **預設** > **hivesampletable**。

* 若要檢視`hivesampletable`結構描述：  
依序展開**hivesampletable**。

* 若要檢視`hivesampletable`資料：  
以滑鼠右鍵按一下**hivesampletable**，然後選取**檢視前 100 個資料列**。  這相當於使用 Hive ODBC 驅動程式來執行下列 Hive 查詢：

   `SELECT * FROM hivesampletable LIMIT 100`

  您可以自訂資料列計數。

  ![HDInsight Hive Visual Studio 結構描述查詢的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-hive-schema.png "Hive 查詢結果")

### <a name="create-hive-tables"></a>建立 Hive 資料表
若要建立 Hive 資料表，您可以使用 GUI 或使用 Hive 查詢。 如需使用 Hive 查詢的相關資訊，請參閱[執行 Apache Hive 查詢](#run.queries)。

1. 從您的叢集，瀏覽至**Hive 資料庫** > **預設**。

2. 以滑鼠右鍵按一下**預設**，然後選取**Create Table**。

3. 視需要設定資料表。  

4. 選取 [建立資料表]  以提交作業來建立新的 Hive 資料表。

    ![HDInsight Visual Studio Tools 建立資料表視窗的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png "建立 Hive 資料表")

### <a name="run.queries"></a>建立和執行 Hive 查詢
您有兩個選項可建立和執行 Hive 查詢：

* 建立特定查詢
* 建立 Hive 應用程式

若要建立，並執行臨機操作查詢：

1. 以滑鼠右鍵按一下您要執行查詢，然後選取的叢集**撰寫 Hive 查詢**。  

2. 輸入下列 Hive 查詢：

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

    Hive 編輯器支援 Intellisense。 Data Lake Tools for Visual Studio 支援在編輯 Hive 指令碼時載入遠端中繼資料。 例如，如果您輸入`SELECT * FROM`，IntelliSense 會列出所有建議的資料表名稱。 若已指定資料表名稱，IntelliSense 會列出資料行名稱。 此工具支援大部分的 Hive DML 陳述式、子查詢及內建 UDF。

    ![HDInsight Visual Studio Tools IntelliSense 範例 1 的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png "U-SQL IntelliSense")

    ![HDInsight Visual Studio Tools IntelliSense 範例 2 的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png "U-SQL IntelliSense")

   > [!NOTE]  
   > IntelliSense 只建議 HDInsight 工具列中已選取的叢集中繼資料。

3. 選擇 執行模式：

    * **互動式**  

      請確定**Interactive**已選取，然後選取**Execute**。

      ![查詢的螢幕擷取畫面，然後執行](./media/apache-hadoop-visual-studio-tools-get-started/execute.png)  

    * **批次**  

      請確定**批次**已選取，然後選取**送出**。  如果您選取進階的提交選項，設定**作業名稱**，**引數**，**其他組態**，和**狀態目錄**指令碼。

      ![查詢和批次的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/batch.png)  

      ![HDInsight Hadoop Hive 查詢的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png "提交查詢")

      > [!NOTE]  
      > 您可以提交批次到互動式查詢叢集。  您必須使用互動模式。

若要建立和執行 Hive 解決方案：

1. 從功能表列中，瀏覽至**檔案** > **新增** > **專案...** .

2. 在左窗格中，瀏覽至**已安裝** > **Azure Data Lake** > **HIVE (HDInsight)** 。  

3. 在中間窗格中，選取 [Hive 應用程式]  。 輸入屬性，然後選取 [確定]  。

    ![HDInsight Visual Studio Tools 工具新 Hive 專案的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png "從 Visual Studio 建立 Hive 應用程式")

4. 在 [方案總管]  中，按兩下 **Script.hql** 來開啟指令碼。

### <a name="view-job-summary-and-output"></a>檢視作業摘要和輸出

作業摘要之間稍有變化**批次**並**Interactive**模式。

![工作摘要](./media/apache-hadoop-visual-studio-tools-get-started/jobSummary.png "Hive 作業摘要")

使用**重新整理**按鈕以更新的狀態，直到工作狀態變為**已完成**。  

* 工作詳細資料，從**批次**模式中，選取連結以查看底部**作業查詢**，**作業輸出**，**作業記錄**，或**Yarn 記錄**。

* 工作詳細資料，從**Interactive**模式，請參閱索引標籤**輸出**並**HiveServer2 輸出**。

  ![作業詳細資料](./media/apache-hadoop-visual-studio-tools-get-started/tabs.png "Hive 作業詳細資料")

### <a name="view-job-graph"></a>檢視作業圖形

目前，使用 Tez 作為執行引擎的 Hive 工作只會顯示工作圖形。  如需啟用 Tez 的相關資訊，請參閱[在 HDInsight 中使用 Apache Hive](hdinsight-use-hive.md)。  此外，請參閱 <<c0> [ 使用 Apache Tez 而非 Mapreduce](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce)。  

若要檢視頂點內的所有運算子，可按兩下作業圖表的頂點。 您也可以指向特定運算子，以查看有關運算子的更多詳細資料。

即使 Tez 指定做為執行引擎，如果沒有 Tez 應用程式啟動時，可能不會顯示作業圖形。  這可能是因為作業不會包含 DML 陳述式，或是可以傳回的 DML 陳述式，而不啟動 Tez 應用程式。 比方說，`SELECT * FROM table1`將不會啟動 Tez 應用程式。

![作業圖形](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png "Hive 作業摘要")


### <a name="task-execution-detail"></a>工作執行詳細資料

您可以從作業圖形中，選取**工作執行詳細資料**取得結構化和視覺化 Hive 工作的資訊。 您也可以取得更多作業詳細資料。 如果發生效能問題，您可以使用此檢視來取得有關問題的更多詳細資料。 例如，您可以取得每個工作的運作方式資訊，以及每個工作的詳細資訊 (資料讀取/寫入、排程/開始/結束時間等)。 使用此資訊，根據視覺化資訊來微調作業組態或系統架構。

![Data Lake Visual Studio Tools 工作執行檢視視窗的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png "工作執行檢視")


### <a name="view-hive-jobs"></a>檢視 Hive 工作
您可以檢視 Hive 工作的工作查詢、工作輸出、工作記錄和 Yarn 記錄。

在工具的最新版本中，您可以藉由收集和呈現 Yarn 記錄來查看 Hive 作業的內容。 Yarn 記錄可協助您調查效能問題。 如需 HDInsight 如何收集 Yarn 記錄的詳細資訊，請參閱[以程式設計方式存取 HDInsight 應用程式記錄](../hdinsight-hadoop-access-yarn-app-logs.md)。

若要檢視 Hive 作業：

1. 以滑鼠右鍵按一下 HDInsight 叢集，然後選取**檢視作業**。 在該叢集上執行的 Hive 作業清單隨即出現。  

2. 選取一個工作。 在 [Hive 作業摘要]  視窗中，選取下列其中一項：
    - **作業查詢**
    - **工作輸出**
    - **作業記錄**  
    - **Yarn 記錄**

    ![HDInsight Visual Studio Tools 檢視 Hive 作業視窗的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png "檢視 Hive 作業")


## <a name="run-apache-pig-scripts"></a>執行 Apache Pig 指令碼

1. 從功能表列中，瀏覽至**檔案** > **新增** > **專案...** .

2. 在左窗格中，瀏覽至**已安裝** > **Azure Data Lake** > **Pig (HDInsight)** 。  

3. 在中間窗格中，選取**Pig 應用程式**。 輸入屬性，然後選取 [確定]  。

4. 在 **方案總管**，按兩下**Script.pig**開啟指令碼。

## <a name="feedback-and-known-issues"></a>意見反應和已知問題
* 尚未修正以下問題：未顯示以 null 值開頭的結果。 如果您因為此問題而遭到封鎖，請連絡支援小組。
* Visual Studio 所建立的 HQL 指令碼是根據使用者的所在區域設定進行編碼。 如果您將指令碼當作二進位檔案上傳到叢集，則指令碼不會正確執行。

## <a name="next-steps"></a>後續步驟
在本文中，您已了解如何使用 Data Lake Tools for Visual Studio 套件從 Visual Studio 連線到 HDInsight 叢集。 您也了解如何執行 Hive 查詢。 如需詳細資訊，請參閱這些文章：

* [執行使用 Data Lake tools for Visual Studio 的 Apache Hive 查詢](apache-hadoop-use-hive-visual-studio.md)
* [在 HDInsight 中使用 Hadoop Hive](hdinsight-use-hive.md)
* [開始使用 HDInsight 中的 Apache Hadoop](apache-hadoop-linux-tutorial-get-started.md)
* [在 HDInsight 中提交 Apache Hadoop 作業](submit-apache-hadoop-jobs-programmatically.md)
* [在 HDInsight 中使用 Apache Hadoop 分析 Twitter 資料](../hdinsight-analyze-twitter-data.md)

