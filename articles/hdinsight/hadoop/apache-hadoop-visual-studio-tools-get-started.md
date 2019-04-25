---
title: 使用 Data Lake Tools for Visual Studio 來連線至 Apache Hadoop - Azure HDInsight
description: 了解如何安裝和使用 Data Lake Tools for Visual Studio 來連線到 Azure HDInsight 中的 Apache Hadoop 叢集，然後執行 Hive 查詢。
keywords: hadoop 工具,hive 查詢,visual studio,visual studio hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: cf392bb254e38b2e07a92e87927b12e144b26f16
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60343841"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>使用 Data Lake Tools for Visual Studio 連線至 Azure HDInsight 及執行 Apache Hive 查詢

了解如何使用[用于 Visual Studio 的 Microsoft Azure Data Lake 和流分析工具](https://www.microsoft.com/download/details.aspx?id=49504)（也称为 Data Lake 工具）连接到 [Azure HDInsight](../hdinsight-hadoop-introduction.md) 中的 Apache Hadoop 群集并提交 Hive 查询。  

如需使用 HDInsight 的詳細資訊，請參閱 [HDInsight 簡介](../hdinsight-hadoop-introduction.md)和[開始使用 HDInsight](apache-hadoop-linux-tutorial-get-started.md)。  

如需連線到 Apache Storm 叢集的詳細資訊，請參閱[使用 Visual Studio 開發 HDInsight 上 Apache Storm 的 C# 拓撲](../storm/apache-storm-develop-csharp-visual-studio-topology.md)。

您可以使用 Data Lake Tools for Visual Studio 來存取 Azure Data Lake Analytics 和 HDInsight。 如需 Data Lake Tools 的相關資訊，請參閱[使用 Data Lake Tools for Visual Studio 開發 U-SQL 指令碼](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md)。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程並使用 Data Lake Tools for Visual Studio，您需要下列項目：

* Azure HDInsight 叢集。 若要建立 HDInsight 叢集，請參閱[在 Azure HDInsight 中開始使用 Apache Hadoop](apache-hadoop-linux-tutorial-get-started.md)。 若要執行互動式 Apache Hive 查詢，您需要 [HDInsight 互動式查詢](../interactive-query/apache-interactive-query-get-started.md)叢集。  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/)（2013 或更高版本）。  [Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/) 是免费的。  另请参阅[安装 Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio)。

  > [!IMPORTANT]  
  > Visual Studio 2013 不再支持 Data Lake 工具。 

## <a name="install-data-lake-tools-for-visual-studio"></a>安裝 Data Lake Tools for Visual Studio  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017  
  在安装期间，请确保至少包含工作负荷“Azure 开发”或“数据存储和处理”。  

  对于现有安装，请在菜单栏中导航到“工具” > “获取工具和功能...”打开 Visual Studio 安装程序。  然后至少选择工作负荷“Azure 开发”或“数据存储和处理”。

  ![Visual Studio 安装程序的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/vs2017_installation.png)

* Visual Studio 2013 和 2015  
  [下载 Data Lake 工具](https://www.microsoft.com/download/details.aspx?id=49504)。 選擇與您的 Visual Studio 版本相符的 Data Lake Tools 版本。  

> [!NOTE]  
> Data Lake Tools for Visual Studio 目前只有英文版。

## <a name="update-data-lake-tools-for-visual-studio"></a>更新用于 Visual Studio 的 Data Lake 工具  

1. 開啟 Visual Studio。

2. 在菜单栏中，导航到“工具” > “扩展和更新...”。

3. 在“扩展和更新”窗口中，展开左侧的“更新”。

4. 如果有可用的更新，“Azure Data Lake 和流分析工具”会显示在主窗口中。  選取 [更新]。

> [!NOTE]  
> 您只可以使用 Data Lake Tools 2.3.0.0 版或更新版本，連線到互動式查詢叢集及執行互動式 Hive 查詢。

## <a name="connect-to-azure-subscriptions"></a>連線到 Azure 訂用帳戶
您可以使用 Data Lake Tools for Visual Studio 連線到您的 HDInsight 叢集、執行一些基本管理作業，以及執行 Hive 查詢。

> [!NOTE]  
> 如需連線到一般 Hadoop 叢集的相關資訊，請參閱[使用 Visual Studio 撰寫和提交 Hive 查詢](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx)。

若要連線到您的 Azure 訂用帳戶：

1. 開啟 Visual Studio。

2. 在菜单栏中，导航到“视图” > “服务器资源管理器”。

3. 在服务器资源管理器中右键单击“Azure”并选择“连接到 Microsoft Azure 订阅...”，然后完成登录过程。

4. 在服务器资源管理器中，会显示现有 HDInsight 群集的列表。 如果您沒有任何叢集，可以使用 Azure 入口網站、Azure PowerShell 或 HDInsight SDK 來建立一個。 如需詳細資訊，請參閱[建立 HDInsight 叢集](../hdinsight-hadoop-provision-linux-clusters.md)。

   ![伺服器總管中的 Data Lake Tools for Visual Studio 叢集清單的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png "伺服器總管中的 Data Lake Tools for Visual Studio 叢集清單")

5. 展開某個 HDInsight 叢集。 **Hive 資料庫**、預設儲存體帳戶、連結的儲存體帳戶，以及 **Hadoop 服務記錄**隨即出現。 您可以進一步展開這些實體。

连接到 Azure 订阅后，可执行以下任务。

若要從 Visual Studio 連線到 Azure 入口網站：

1. 在服务器资源管理器中，导航到“Azure” > “HDInsight”并选择你的群集。

2. 以滑鼠右鍵按一下 HDInsight 叢集，然後選取**在 Azure 入口網站中管理叢集**。

通过 Visual Studio 提问和/或提供反馈：

1. 在服务器资源管理器中，导航到“Azure” > “HDInsight”。

2. 右键单击“HDInsight”，并选择“MSDN 论坛”以提问，或选择“提供反馈”以提供反馈。

## <a name="explore-linked-resources"></a>瀏覽連結的資源
從 [伺服器總管] 中，您可以看到預設的儲存體帳戶，以及任何連結的儲存體帳戶。 如果您展開預設儲存體帳戶，您可以看到儲存體帳戶上的容器。 預設儲存體帳戶和預設容器皆已標示。 在任何容器上按一下滑鼠右鍵以檢視容器內容。

![伺服器總管中 Data Lake Tools for Visual Studio 清單連結資源的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png "清單連結資源")

開啟容器之後，您可以使用下列按鈕來上傳、刪除及下載 Blob：

![伺服器總管中 Data Lake Tools for Visual Studio Blob 作業的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png "在伺服器總管中更新、刪除和下載 Blob")

## <a name="run-interactive-apache-hive-queries"></a>執行互動式 Apache Hive 查詢
[Apache Hive](https://hive.apache.org) 是以 Hadoop 為基礎的資料倉儲基礎結構。 Hive 用於進行資料彙整、查詢及分析。 您可以使用 Data Lake Tools for Visual Studio 從 Visual Studio 執行 Hive 查詢。 如需有關 Hive 的詳細資訊，請參閱[將 Apache Hive 與 HDInsight 搭配使用](hdinsight-use-hive.md)。

[互動式查詢](../interactive-query/apache-interactive-query-get-started.md)在 Apache Hive 2.1 中使用 [LLAP 上的 Hive](https://cwiki.apache.org/confluence/display/Hive/LLAP)。 互動式查詢可將互動功能整合到已儲存的大型資料集上的複雜資料倉儲樣式查詢。 相較於傳統的 Hive 批次作業，在互動式查詢上執行 Hive 查詢比較快速。 如需詳細資訊，請參閱執行 Apache Hive 批次作業。

> [!NOTE]  
> 只有在您連線到 [HDInsight 互動式查詢](../interactive-query/apache-interactive-query-get-started.md)叢集時，您才可以執行互動式 Hive 查詢。

您也可以使用 Data Lake Tools for Visual Studio 來查看 Hive 作業的內容。 Data Lake Tools for Visual Studio 會收集和呈現特定 Hive 作業的 Yarn 記錄。

在服务器资源管理器中，导航到“Azure” > “HDInsight”并选择你的群集。  后续部分所述的操作将在服务器资源管理器中的此位置着手。

### <a name="view-hivesampletable"></a>查看 hivesampletable
所有 HDInsight 群集都有一个名为 `hivesampletable` 的默认示例 Hive 表。  

在群集中，导航到“Hive 数据库” > “默认” > “hivesampletable”。

* 查看 `hivesampletable` 架构：  
展开“hivesampletable”。

* 查看 `hivesampletable` 数据：  
右键单击“hivesampletable”并选择“查看前 100 行”。  這相當於使用 Hive ODBC 驅動程式來執行下列 Hive 查詢：

   `SELECT * FROM hivesampletable LIMIT 100`

  您可以自訂資料列計數。

  ![HDInsight Hive Visual Studio 結構描述查詢的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png "Hive 查詢結果")

### <a name="create-hive-tables"></a>建立 Hive 資料表
若要建立 Hive 資料表，您可以使用 GUI 或使用 Hive 查詢。 如需使用 Hive 查詢的相關資訊，請參閱[執行 Apache Hive 查詢](#run.queries)。

1. 在群集中，导航到“Hive 数据库” > “默认”。

2. 右键单击“默认”并选择“创建表”。

3. 根据需要配置表。  

4. 選取 [建立資料表] 以提交作業來建立新的 Hive 資料表。

    ![HDInsight Visual Studio Tools 建立資料表視窗的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png "建立 Hive 資料表")

### <a name="run.queries"></a>创建并运行 Hive 查询
您有兩個選項可建立和執行 Hive 查詢：

* 建立特定查詢
* 建立 Hive 應用程式

若要建立，並執行臨機操作查詢：

1. 右键单击要运行查询的群集，然后选择“编写 Hive 查询”。  

2. 輸入 Hive 查詢。  

    Hive 編輯器支援 Intellisense。 Data Lake Tools for Visual Studio 支援在編輯 Hive 指令碼時載入遠端中繼資料。 例如，如果键入 `SELECT * FROM`，则 IntelliSense 会列出所有建议的表名称。 若已指定資料表名稱，IntelliSense 會列出資料行名稱。 此工具支援大部分的 Hive DML 陳述式、子查詢及內建 UDF。

    ![HDInsight Visual Studio Tools IntelliSense 範例 1 的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png "U-SQL IntelliSense")

    ![HDInsight Visual Studio Tools IntelliSense 範例 2 的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png "U-SQL IntelliSense")

   > [!NOTE]  
   > IntelliSense 只建議 HDInsight 工具列中已選取的叢集中繼資料。

3. 选择执行模式：

    * **互動式**  

      确保“交互式”已选中，然后选择“执行”。

      ![查询和“执行”的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/execute.png)  

    * **Batch**  

      确保“批处理”已选中，然后选择“提交”。  如果选择高级提交选项，请为脚本配置“作业名称”、“参数”、“其他配置”和“状态目录”。

      ![查询和“批处理”的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/batch.png)  

      ![HDInsight Hadoop Hive 查詢的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "提交查詢")

      > [!NOTE]  
      > 无法将批提交到交互式查询群集。  必须使用交互模式。

若要建立和執行 Hive 解決方案：

1. 在菜单栏中，导航到“文件” > “新建” > “项目...”。

2. 在左窗格中，导航到“已安装” > “Azure Data Lake” > “HIVE (HDInsight)”。  

3. 在中間窗格中，選取 [Hive 應用程式]。 輸入屬性，然後選取 [確定]。

    ![HDInsight Visual Studio Tools 工具新 Hive 專案的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "從 Visual Studio 建立 Hive 應用程式")

4. 在 [方案總管] 中，按兩下 **Script.hql** 來開啟指令碼。

### <a name="view-job-summary-and-output"></a>查看作业摘要和输出

作业摘要根据选择的是“批处理”还是“交互式”模式而略有不同。

![作业摘要](./media/apache-hadoop-visual-studio-tools-get-started/jobSummary.png "Hive 作业摘要")

使用“刷新”按钮更新状态，直到作业状态更改为“已完成”。  

* 如需“批处理”模式下的作业详细信息，请选择底部的链接以查看“作业查询”、“作业输出”、“作业日志”或“Yarn 日志”。

* 如需“交互式”模式下的作业详细信息，请查看“输出”和“HiveServer2 输出”选项卡。

  ![作业详细信息](./media/apache-hadoop-visual-studio-tools-get-started/tabs.png "Hive 作业详细信息")

### <a name="view-job-graph"></a>查看作业图

目前，仅显示使用 Tez 作为执行引擎的 Hive 作业的作业图。  如需啟用 Tez 的相關資訊，請參閱[在 HDInsight 中使用 Apache Hive](hdinsight-use-hive.md)。  另请参阅[使用 Apache Tez 而不是 Map Reduce](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce)。  

若要檢視頂點內的所有運算子，可按兩下作業圖表的頂點。 您也可以指向特定運算子，以查看有關運算子的更多詳細資料。

即使已将 Tez 指定为执行引擎，但如果未启动 Tez 应用程序，则也可能不会显示作业图。  可能的原因是作业不包含 DML 语句，或者在未启动 Tez 应用程序的情况下 DML 语句可以返回。 例如，`SELECT * FROM table1` 不会启动 Tez 应用程序。

![作业图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png "Hive 作业摘要")


### <a name="task-execution-detail"></a>任务执行详细信息

在作业图中，可以选择“任务执行详细信息”获取 Hive 作业的结构化和可视化信息。 您也可以取得更多作業詳細資料。 如果發生效能問題，您可以使用此檢視來取得有關問題的更多詳細資料。 例如，您可以取得每個工作的運作方式資訊，以及每個工作的詳細資訊 (資料讀取/寫入、排程/開始/結束時間等)。 使用此資訊，根據視覺化資訊來微調作業組態或系統架構。

![Data Lake Visual Studio Tools 工作執行檢視視窗的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png "工作執行檢視")


### <a name="view-hive-jobs"></a>檢視 Hive 工作
您可以檢視 Hive 工作的工作查詢、工作輸出、工作記錄和 Yarn 記錄。

在工具的最新版本中，您可以藉由收集和呈現 Yarn 記錄來查看 Hive 作業的內容。 Yarn 記錄可協助您調查效能問題。 如需 HDInsight 如何收集 Yarn 記錄的詳細資訊，請參閱[以程式設計方式存取 HDInsight 應用程式記錄](../hdinsight-hadoop-access-yarn-app-logs-linux.md)。

若要檢視 Hive 作業：

1. 右键单击某个 HDInsight 群集，并选择“查看作业”。 在該叢集上執行的 Hive 作業清單隨即出現。  

2. 選取一個工作。 在 [Hive 作業摘要] 視窗中，選取下列其中一項：
   - **作業查詢**
   - **工作輸出**
   - **作業記錄**  
   - **Yarn 記錄**

     ![HDInsight Visual Studio Tools 檢視 Hive 作業視窗的螢幕擷取畫面](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png "檢視 Hive 作業")


## <a name="run-apache-pig-scripts"></a>執行 Apache Pig 指令碼

1. 在菜单栏中，导航到“文件” > “新建” > “项目...”。

2. 在左窗格中，导航到“已安装” > “Azure Data Lake” > “Pig (HDInsight)”。  

3. 在中间窗格中选择“Pig 应用程序”。 輸入屬性，然後選取 [確定]。

4. 在“解决方案资源管理器”中，双击“Script.pig”打开脚本。

## <a name="feedback-and-known-issues"></a>意見反應和已知問題
* 尚未修正以下問題：未顯示以 null 值開頭的結果。 如果您因為此問題而遭到封鎖，請連絡支援小組。
* Visual Studio 所建立的 HQL 指令碼是根據使用者的所在區域設定進行編碼。 如果您將指令碼當作二進位檔案上傳到叢集，則指令碼不會正確執行。

## <a name="next-steps"></a>後續步驟
在本文中，您已了解如何使用 Data Lake Tools for Visual Studio 套件從 Visual Studio 連線到 HDInsight 叢集。 您也了解如何執行 Hive 查詢。 如需詳細資訊，請參閱這些文章：

* [使用用于 Visual Studio 的 Data Lake 工具运行 Apache Hive 查询](apache-hadoop-use-hive-visual-studio.md)
* [在 HDInsight 中使用 Hadoop Hive](hdinsight-use-hive.md)
* [開始使用 HDInsight 中的 Apache Hadoop](apache-hadoop-linux-tutorial-get-started.md)
* [在 HDInsight 中提交 Apache Hadoop 作業](submit-apache-hadoop-jobs-programmatically.md)
* [在 HDInsight 中使用 Apache Hadoop 分析 Twitter 資料](../hdinsight-analyze-twitter-data-linux.md)

