---
title: 在 HDInsight 中的查詢主控台上使用 Apache Hive - Azure
description: 了解如何使用 Web 型查詢主控台，透過瀏覽器在 HDInsight Hadoop 叢集上執行 Apache Hive 查詢。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 1e638bd348b7a5272dd8bfbe25aa841f38a51b9a
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409695"
---
# <a name="run-apache-hive-queries-using-the-query-console"></a>使用查詢主控台執行 Apache Hive 查詢
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

在本文中，您將學習如何使用 HDInsight 查詢主控台，透過瀏覽器在 HDInsight Hadoop 叢集上執行 Apache Hive 查詢。

> [!IMPORTANT]  
> 只有在 Windows 型 HDInsight 叢集上才能使用 HDInsight 查詢主控台。 Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 淘汰](../hdinsight-component-versioning.md#hdinsight-windows-retirement)。
>
> 針對 HDInsight 3.4 或更新版本，請參閱[在 Ambari Hive 檢視中執行 Apache Hive 查詢](apache-hadoop-use-hive-ambari-view.md)，以取得有關從網頁瀏覽器執行 Hive 查詢的資訊。

## <a id="prereq"></a>必要條件
若要完成本文中的步驟，您需要下列項目。

* Windows 型 HDInsight Hadoop 叢集
* 現代網頁瀏覽器

## <a id="run"></a> 使用查詢主控台執行 Apache Hive 查詢
1. 開啟網頁瀏覽器並瀏覽至 **https://CLUSTERNAME.azurehdinsight.net**，其中 **CLUSTERNAME** 是 HDInsight 叢集的名稱。 出現提示時，輸入您建立叢集時所使用的使用者名稱和密碼。
2. 在頁面頂端的連結中，選取 [ **Hive 編輯器**]。 這會顯示一個表單，而此表單可用來輸入您要在 HDInsight 叢集中執行的 HiveQL 陳述式。

    ![Hive 編輯器](./media/apache-hadoop-use-hive-query-console/queryconsole.png)

    將文字 `Select * from hivesampletable` 取代為下列 HiveQL 陳述式：

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    這些陳述式會執行下列動作：

   * **DROP TABLE**：刪除資料表和資料檔 (如果資料表已經存在)。
   * **CREATE EXTERNAL TABLE**：在 Hive 中建立新的「外部」資料表。 外部資料表只會在 Hive 中儲存資料表定義；資料會保留在原始位置。

     > [!NOTE]  
     > 當您預期以外部來源更新基礎資料 (例如自動化資料上傳程序)，或以其他 MapReduce 作業更新基礎資料，但希望 Hive 查詢一律使用最新資料時，必須使用外部資料表。
     >
     > 捨棄外部資料表並 **不會** 刪除資料，只會刪除資料表定義。
     >
     >
   * **ROW FORMAT**：告訴 Hive 如何設定資料格式。 在此情況下，每個記錄中的欄位會以空格隔開。
   * **STORED AS TEXTFILE LOCATION**：告訴 Hive 資料的儲存位置 (example/data 目錄)，以及資料是儲存為文字
   * **SELECT**：選取 **t4** 資料行包含 **[ERROR]** 值之所有資料列的計數。 這應該會傳回值 **3** ，因為有三個資料列包含此值。
   * **INPUT__FILE__NAME LIKE '%.log'** - 告訴 Hive 我們只應該從檔名以 log 結尾的檔案中傳回資料。 這將限制包含此資料的 sample.log 檔案搜尋，對於不符合我們所定義結構描述的其他範例資料檔案，會防止其傳回資料。
3. 按一下 [提交] 。 頁面底部的 [作業工作階段] 應該會顯示工作的詳細資料。
4. 當 [狀態] 欄位變更為 [已完成] 時，請選取工作的 [檢視詳細資料]。 在詳細資料頁面上，[工作輸出] 包含 `[ERROR]    3`。 您可以使用此欄位下方的 [ **下載** ] 按鈕，下載含有工作輸出的檔案。

## <a id="summary"></a>摘要
如您所見，[查詢主控台] 提供簡單的方法，在 HDInsight 叢集中執行 Hive 查詢、監視工作狀態，以及擷取輸出。

若要深入了解如何使用 Hive 查詢主控台執行 Hive 工作，請選取 [查詢主控台] 頂端的 [ **入門** ]，然後使用提供的範例。 每個範例都會逐步進行使用 Hive 分析資料的程序 (包括範例中所用之 HiveQL 陳述式的說明)。

## <a id="nextsteps"></a>接續步驟
如需 HDInsight 中 Hive 的一般資訊：

* [在 HDInsight 上搭配 Apache Hadoop 使用 Apache Hive](hdinsight-use-hive.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [在 HDInsight 上搭配 Apache Hadoop 使用 Apache Pig](hdinsight-use-pig.md)
* [搭配 HDInsight 上的 Apache Hadoop 使用 MapReduce](hdinsight-use-mapreduce.md)

如果您搭配使用 Tez 和 Hive，請參閱下列文件所提供的偵錯資訊：

* [在以 Windows 為基礎的 HDInsight 上使用 Apache Tez UI](../hdinsight-debug-tez-ui.md)
* [在以 Linux 為基礎的 HDInsight 上使用 Apache Ambari Tez 檢視](../hdinsight-debug-ambari-tez-view.md)

[1]:apache-hadoop-visual-studio-tools-get-started.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
