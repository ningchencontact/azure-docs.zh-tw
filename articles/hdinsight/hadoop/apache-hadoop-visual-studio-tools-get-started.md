---
title: Apache Hadoop & Visual Studio Data Lake 工具-Azure HDInsight
description: 了解如何安裝和使用 Data Lake Tools for Visual Studio 來連線到 Azure HDInsight 中的 Apache Hadoop 叢集，然後執行 Hive 查詢。
keywords: hadoop 工具,hive 查詢,visual studio,visual studio hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 887630eb0f75b45c231ec1cd69af925b853b1086
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73098400"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>使用 Data Lake Tools for Visual Studio 連線至 Azure HDInsight 及執行 Apache Hive 查詢

瞭解如何使用[Microsoft Azure Data Lake 和串流分析 Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) （也稱為 Data Lake 工具）的工具，在[Azure HDInsight](../hdinsight-hadoop-introduction.md)中連線到 Apache Hadoop 叢集並提交 Hive 查詢。  

如需使用 HDInsight 的詳細資訊，請參閱 [HDInsight 簡介](../hdinsight-hadoop-introduction.md)和[開始使用 HDInsight](apache-hadoop-linux-tutorial-get-started.md)。  

如需連線到 Apache Storm 叢集的詳細資訊，請參閱[使用 Visual Studio 開發 HDInsight 上 Apache Storm 的 C# 拓撲](../storm/apache-storm-develop-csharp-visual-studio-topology.md)。

您可以使用 Data Lake Tools for Visual Studio 來存取 Azure Data Lake Analytics 和 HDInsight。 如需 Data Lake Tools 的相關資訊，請參閱[使用 Data Lake Tools for Visual Studio 開發 U-SQL 指令碼](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md)。

## <a name="prerequisites"></a>必要條件

若要完成本文並使用適用于 Visual Studio 的 Data Lake 工具，您需要下列專案：

* Azure HDInsight 叢集。 若要建立 HDInsight 叢集，請參閱[在 Azure HDInsight 中開始使用 Apache Hadoop](apache-hadoop-linux-tutorial-get-started.md)。 若要執行互動式 Apache Hive 查詢，您需要 [HDInsight 互動式查詢](../interactive-query/apache-interactive-query-get-started.md)叢集。  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) （2013或更新版本）。  [Visual Studio Community 版本](https://visualstudio.microsoft.com/vs/community/)是免費的。  另請參閱[安裝 Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio)和[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。 Visual Studio 2019 有些許介面變化。

  > [!IMPORTANT]  
  > Visual Studio 2013 不再支援 Data Lake 工具。

## <a name="install-data-lake-tools-for-visual-studio"></a>安裝 Data Lake Tools for Visual Studio

<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017 或 Visual Studio 2019  
  在安裝期間，請確定您至少包含工作負載**Azure 開發**或**資料儲存和處理**。  

  針對現有的安裝，請從功能表列流覽至 **工具**， > **取得工具和功能 ...**  開啟 Visual Studio 安裝程式。  然後選取至少工作負載**Azure 開發**或**資料儲存和處理**。

  ![Visual Studio 安裝程式的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/vs-2017-installation.png)

* Visual Studio 2013 和2015  
  [下載 Data Lake 工具](https://www.microsoft.com/download/details.aspx?id=49504)。 選擇與您的 Visual Studio 版本相符的 Data Lake Tools 版本。  

> [!NOTE]  
> Data Lake Tools for Visual Studio 目前只有英文版。

## <a name="update-data-lake-tools-for-visual-studio"></a>更新 Visual Studio 的 Data Lake 工具  

1. 開啟 Visual Studio。

2. 從功能表列中，流覽至 [**擴充**功能] > [**管理延伸**模組]。

3. 在 [**管理擴充**功能] 視窗中，展開左側的 [**更新**]。

4. 如果有可用的更新， **Azure Data Lake 和串流分析工具**就會出現在主視窗中。  選取 [更新]。

> [!NOTE]  
> 您只可以使用 Data Lake Tools 2.3.0.0 版或更新版本，連線到互動式查詢叢集及執行互動式 Hive 查詢。

## <a name="connect-to-azure-subscriptions"></a>連線到 Azure 訂用帳戶

您可以使用 Data Lake Tools for Visual Studio 連線到您的 HDInsight 叢集、執行一些基本管理作業，以及執行 Hive 查詢。

> [!NOTE]  
> 如需連線到一般 Hadoop 叢集的相關資訊，請參閱[使用 Visual Studio 撰寫和提交 Hive 查詢](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx)。

若要連線到您的 Azure 訂用帳戶：

1. 開啟 Visual Studio。

2. 從功能表列中，流覽至 [ **View** > **伺服器總管**]。

3. 在伺服器總管中，以滑鼠右鍵按一下 [ **Azure**]，選取 **[連線到 Microsoft Azure 訂用帳戶 ...]** ，然後完成登入程式。

4. 從伺服器總管，會顯示現有 HDInsight 叢集的清單。 如果您沒有任何叢集，可以使用 Azure 入口網站、Azure PowerShell 或 HDInsight SDK 來建立一個。 如需詳細資訊，請參閱[建立 HDInsight 叢集](../hdinsight-hadoop-provision-linux-clusters.md)。

   ![伺服器總管中 Visual Studio 叢集清單的 Data Lake 工具](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png "伺服器總管中 Visual Studio 叢集清單的 Data Lake 工具")

5. 展開某個 HDInsight 叢集。 **Hive 資料庫**、預設儲存體帳戶、連結的儲存體帳戶，以及**Hadoop 服務記錄**檔隨即出現。 您可以進一步展開這些實體。

連接到您的 Azure 訂用帳戶之後，您可以執行下列工作。

若要從 Visual Studio 連線到 Azure 入口網站：

1. 從伺服器總管，流覽至**Azure** > **HDInsight** ，然後選取您的叢集。

2. 在 HDInsight 叢集上按一下滑鼠右鍵，然後選取 [**在 Azure 入口網站中管理**叢集]。

若要提出問題及/或提供 Visual Studio 的意見反應：

1. 從伺服器總管流覽至**Azure** > **HDInsight**。

2. 以滑鼠右鍵按一下 [ **HDInsight** ]，然後選取 [ **MSDN 論壇**] 來提出問題，或**提供意見**反應以提供意見反應。

## <a name="link-a-cluster"></a>連結叢集

以滑鼠右鍵按一下 [ **hdinsight** ]，然後選取 [**連結 HDInsight**叢集]，即可連結叢集。 輸入連線**Url**、**使用者名稱**和**密碼**，按 **[下一步**]，然後按一下 [完成]，叢集應該會列在 [HDInsight 節點成功 **]** 底下。

![Visual Studio 連結叢集的 Data Lake 工具 對話方塊的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

以滑鼠右鍵按一下連結的叢集，選取 [**編輯**]，使用者可以更新叢集資訊。 新增 HDInsight 叢集目前僅支援 Hive。

![Visual Studio 連結叢集更新 Data Lake 工具的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>瀏覽連結的資源

從 [伺服器總管] 中，您可以看到預設的儲存體帳戶，以及任何連結的儲存體帳戶。 如果您展開預設儲存體帳戶，您可以看到儲存體帳戶上的容器。 預設儲存體帳戶和預設容器皆已標示。 在任何容器上按一下滑鼠右鍵以檢視容器內容。

![在伺服器總管中 Visual Studio 連結資源的 Data Lake 工具](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png "列出連結的資源")

開啟容器之後，您可以使用下列按鈕來上傳、刪除及下載 Blob：

![在伺服器總管中 Visual Studio blob 作業的 Data Lake 工具](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png "上傳、刪除及下載 blob 伺服器總管")

## <a name="run-interactive-apache-hive-queries"></a>執行互動式 Apache Hive 查詢

[Apache Hive](https://hive.apache.org) 是以 Hadoop 為基礎的資料倉儲基礎結構。 Hive 用於進行資料彙整、查詢及分析。 您可以使用 Data Lake Tools for Visual Studio 從 Visual Studio 執行 Hive 查詢。 如需有關 Hive 的詳細資訊，請參閱[將 Apache Hive 與 HDInsight 搭配使用](hdinsight-use-hive.md)。

[互動式查詢](../interactive-query/apache-interactive-query-get-started.md)在 Apache Hive 2.1 中使用 [LLAP 上的 Hive](https://cwiki.apache.org/confluence/display/Hive/LLAP)。 互動式查詢可將互動功能整合到已儲存的大型資料集上的複雜資料倉儲樣式查詢。 相較於傳統的 Hive 批次作業，在互動式查詢上執行 Hive 查詢比較快速。 

> [!NOTE]  
> 只有在您連線到 [HDInsight 互動式查詢](../interactive-query/apache-interactive-query-get-started.md)叢集時，您才可以執行互動式 Hive 查詢。

您也可以使用 Data Lake Tools for Visual Studio 來查看 Hive 作業的內容。 Data Lake Tools for Visual Studio 會收集和呈現特定 Hive 作業的 Yarn 記錄。

從伺服器總管，流覽至**Azure** > **HDInsight** ，然後選取您的叢集。  這會是伺服器總管中要遵循之區段的起始點。

### <a name="view-hivesampletable"></a>View hivesampletable

所有 HDInsight 叢集都有一個稱為 `hivesampletable`的預設範例 Hive 資料表。  

從您的叢集，流覽至  **Hive 資料庫** > **預設值** > **hivesampletable**。

* 若要查看 `hivesampletable` 的架構：  
展開 [ **hivesampletable**]。

* 若要查看 `hivesampletable` 的資料：  
以滑鼠右鍵按一下 [ **hivesampletable**]，然後選取 [**查看前 100**個數據列]。  這相當於使用 Hive ODBC 驅動程式來執行下列 Hive 查詢：

   `SELECT * FROM hivesampletable LIMIT 100`

  您可以自訂資料列計數。

  ![HDInsight Hive Visual Studio 架構查詢的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-hive-schema.png "Hive 查詢結果")

### <a name="create-hive-tables"></a>建立 Hive 資料表

若要建立 Hive 資料表，您可以使用 GUI 或使用 Hive 查詢。 如需使用 Hive 查詢的相關資訊，請參閱[執行 Apache Hive 查詢](#run.queries)。

1. 從您的叢集中，流覽至 [ **Hive 資料庫**] > **預設值**。

2. 以滑鼠右鍵按一下 [**預設**]，然後選取 [**建立資料表**]。

3. 視需要設定資料表。  

4. 選取 [建立資料表] 以提交作業來建立新的 Hive 資料表。

    ![HDInsight Visual Studio Tools [建立資料表] 視窗的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png "建立 Hive 資料表")

### <a name="run.queries"></a>建立和執行 Hive 查詢

您有兩個選項可建立和執行 Hive 查詢：

* 建立特定查詢
* 建立 Hive 應用程式

若要建立和執行特定查詢：

1. 在您要執行查詢的叢集上按一下滑鼠右鍵，然後選取 [**撰寫 Hive 查詢**]。  

2. 輸入下列 Hive 查詢：

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

    Hive 編輯器支援 Intellisense。 Data Lake Tools for Visual Studio 支援在編輯 Hive 指令碼時載入遠端中繼資料。 例如，如果您輸入 `SELECT * FROM`，IntelliSense 會列出所有建議的資料表名稱。 若已指定資料表名稱，IntelliSense 會列出資料行名稱。 此工具支援大部分的 Hive DML 陳述式、子查詢及內建 UDF。

    ![HDInsight Visual Studio Tools IntelliSense 範例1的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png "U-SQL IntelliSense")

    ![HDInsight Visual Studio Tools IntelliSense 範例2的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png "U-SQL IntelliSense")

   > [!NOTE]  
   > IntelliSense 只建議 HDInsight 工具列中已選取的叢集中繼資料。

3. 選擇執行模式：

    * **互動式**  

      確定已選取 [**互動式**]，然後選取 [**執行**]。

      ![查詢和執行的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

      確定已選取 [**批次**]，然後選取 [**提交**]。  如果您選取 [advanced submit] 選項，請設定腳本的 [**作業名稱**]、[**引數**]、[**其他**設定] 和 [**狀態目錄**]。

      ![Visual Studio 查詢和批次選項](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)  

      ![HDInsight Hadoop Hive 查詢的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png "提交查詢")

      > [!NOTE]  
      > 您不能將批次提交至互動式查詢叢集。  您必須使用互動模式。

若要建立和執行 Hive 解決方案：

1. 從功能表列中，**流覽至** 檔案 > **新增** > **專案**...。

2. 在左窗格中，流覽至 **已安裝**的 >  **Azure Data Lake** > **HIVE （HDInsight）** 。  

3. 在中間窗格中，選取 [Hive 應用程式]。 輸入屬性，然後選取 [確定]。

    ![HDInsight Visual Studio Tools 新 Hive 專案的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png "從 Visual Studio 建立 Hive 應用程式")

4. 在 [方案總管] 中，按兩下 **Script.hql** 來開啟指令碼。

### <a name="view-job-summary-and-output"></a>查看作業摘要和輸出

工作摘要在**批次**和**互動**模式之間有些許差異。

![[Apache Hive 作業摘要] 索引標籤顯示](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png "Hive 作業摘要")

使用 [重新整理 **] 按鈕來**更新狀態，直到作業狀態變更為 [**已完成**] 為止。  

* 如需**批次**模式中的作業詳細資料，請選取底部的連結以查看 [**作業查詢**]、[**作業輸出**]、[**作業記錄**] 或 [ **Yarn 記錄**]。

* 如需**互動**模式的作業詳細資料，請參閱定位點**輸出**和**HiveServer2 輸出**。

  ![Visual Studio Apache Hive 作業詳細資料](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png "Hive 作業詳細資料")

### <a name="view-job-graph"></a>查看作業圖形

目前，只會針對使用 Tez 做為執行引擎的 Hive 作業顯示作業圖形。  如需啟用 Tez 的相關資訊，請參閱[在 HDInsight 中使用 Apache Hive](hdinsight-use-hive.md)。  另請參閱[使用 Apache Tez，而非 Map 的縮減](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce)。  

若要檢視頂點內的所有運算子，可按兩下作業圖表的頂點。 您也可以指向特定運算子，以查看有關運算子的更多詳細資料。

即使未啟動任何 Tez 應用程式，也不會出現作業圖形，即使 Tez 已指定為執行引擎亦然。  這可能是因為作業未包含 DML 語句，或 DML 語句可以在不啟動 Tez 應用程式的情況下傳回。 例如，`SELECT * FROM table1` 不會啟動 Tez 應用程式。

![Visual Studio Apache Hive 作業圖形](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png "Hive 作業摘要")

### <a name="task-execution-detail"></a>工作執行詳細資料

從作業圖形中，您可以選取 [工作**執行詳細資料**]，以取得 Hive 作業的結構化和視覺化資訊。 您也可以取得更多作業詳細資料。 如果發生效能問題，您可以使用此檢視來取得有關問題的更多詳細資料。 例如，您可以取得每個工作的運作方式資訊，以及每個工作的詳細資訊 (資料讀取/寫入、排程/開始/結束時間等)。 使用此資訊，根據視覺化資訊來微調作業組態或系統架構。

![Data Lake Visual Studio Tools 工作執行視圖視窗](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png "工作執行視圖")

### <a name="view-hive-jobs"></a>檢視 Hive 工作

您可以檢視 Hive 工作的工作查詢、工作輸出、工作記錄和 Yarn 記錄。

在工具的最新版本中，您可以藉由收集和呈現 Yarn 記錄來查看 Hive 作業的內容。 Yarn 記錄可協助您調查效能問題。 如需 HDInsight 如何收集 Yarn 記錄的詳細資訊，請參閱[以程式設計方式存取 HDInsight 應用程式記錄](../hdinsight-hadoop-access-yarn-app-logs.md)。

若要檢視 Hive 作業：

1. 以滑鼠右鍵按一下 HDInsight 叢集，然後選取 [**查看作業**]。 在該叢集上執行的 Hive 作業清單隨即出現。  

2. 選取一個工作。 在 [Hive 作業摘要] 視窗中，選取下列其中一項：
    * **作業查詢**
    * **工作輸出**
    * **作業記錄**  
    * **Yarn 記錄**

    ![HDInsight Visual Studio Tools View Hive 作業 視窗的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png "檢視 Hive 工作")

## <a name="run-apache-pig-scripts"></a>執行 Apache Pig 指令碼

1. 從功能表列中，**流覽至** 檔案 > **新增** > **專案**...。

2. 在左窗格中，流覽至 **已安裝**的 >  **Azure Data Lake** > **Pig （HDInsight）** 。  

3. 在中間窗格中，選取 [ **Pig 應用程式**]。 輸入屬性，然後選取 [確定]。

4. 在**方案總管**中，按兩下 [ **pig** ] 以開啟腳本。

## <a name="feedback-and-known-issues"></a>意見反應和已知問題

* 尚未修正以下問題：未顯示以 null 值開頭的結果。 如果您因為此問題而遭到封鎖，請連絡支援小組。
* Visual Studio 所建立的 HQL 指令碼是根據使用者的所在區域設定進行編碼。 如果您將指令碼當作二進位檔案上傳到叢集，則指令碼不會正確執行。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何使用 Data Lake Tools for Visual Studio 套件從 Visual Studio 連線到 HDInsight 叢集。 您也了解如何執行 Hive 查詢。 如需詳細資訊，請參閱這些文章：

* [使用 Data Lake Tools for Visual Studio 執行 Apache Hive 查詢](apache-hadoop-use-hive-visual-studio.md)
* [在 HDInsight 中使用 Hadoop Hive](hdinsight-use-hive.md)
* [開始使用 HDInsight 中的 Apache Hadoop](apache-hadoop-linux-tutorial-get-started.md)
* [在 HDInsight 中提交 Apache Hadoop 作業](submit-apache-hadoop-jobs-programmatically.md)
* [在 HDInsight 中使用 Apache Hadoop 分析 Twitter 資料](../hdinsight-analyze-twitter-data.md)
