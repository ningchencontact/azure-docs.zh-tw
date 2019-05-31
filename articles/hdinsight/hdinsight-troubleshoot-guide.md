---
title: Azure HDInsight 疑難排解指南
description: 使用 Azure HDInsight 進行 Apache Hadoop 工作負載的疑難排解。 逐步文件會顯示如何使用 HDInsight 解決 Apache Hive、Apache Spark、Apache YARN、Apache HBase、HDFS 和 Apache Storm 的常見問題。
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: d39aaf458a1c67330ee1023ec2598bfb979fa48f
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393707"
---
# <a name="troubleshoot-by-using-azure-hdinsight"></a>使用 Azure HDInsight 進行疑難排解

| Apache 工作負載 | 最常見問題 |
|---|---|
|![HBase](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[Apache HBase 疑難排解](hbase/apache-troubleshoot-hbase.md)|<br>[如何執行回報多個未指派區域的 hbck 命令？](hbase/apache-troubleshoot-hbase.md#how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions)<br><br>[如何修正使用 hbck 命令進行區域指派時發生的逾時問題？](hbase/apache-troubleshoot-hbase.md#how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments)<br><br>[如何在叢集中強制停用 HDFS 安全模式？](hbase/apache-troubleshoot-hbase.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)<br><br>[如何修正與 Apache Phoenix 的 JDBC 或 SQLLine 連線問題？](hbase/apache-troubleshoot-hbase.md#how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix)<br><br>[什麼情況導致主要伺服器無法啟動？](hbase/apache-troubleshoot-hbase.md#what-causes-a-master-server-to-fail-to-start)<br><br>[什麼情況導致區域伺服器上的重新啟動失敗？](hbase/apache-troubleshoot-hbase.md#what-causes-a-restart-failure-on-a-region-server)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[Apache Hadoop HDFS 疑難排解](hdinsight-troubleshoot-hdfs.md)|<br>[如何從叢集內部存取本機 HDFS？](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[如何在叢集中強制停用 HDFS 安全模式？](hdinsight-troubleshoot-hdfs.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)|
|![Hive](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[Apache HBase 疑難排解](hdinsight-troubleshoot-hive.md)|<br>[如何匯出 Hive 中繼存放區並匯入另一個叢集？](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[如何在叢集上尋找 Apache Hive 記錄？](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[如何在叢集上以特定設定啟動 Apache Hive Shell？](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[如何分析叢集關鍵路徑上的 Apache Tez DAG 資料？](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[如何從叢集下載 Apache Tez DAG 資料？](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![Spark](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Apache Spark 疑難排解](hdinsight-troubleshoot-SPARK.md)|<br>[如何使用 Apache Ambari 在叢集上設定 Apache Spark 應用程式？](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters)<br><br>[如何使用 Jupyter Notebook 在叢集上設定 Apache Spark 應用程式？](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[如何使用 Apache Livy 在叢集上設定 Apache Spark 應用程式？](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters)<br><br>[如何使用 spark-submit 在叢集上設定 Apache Spark 應用程式？](spark/apache-troubleshoot-spark.md#how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters)<br><br>[如何使用 IntelliJ 來設定 Apache Spark 應用程式？](spark/apache-spark-intellij-tool-plugin.md)<br><br>[如何使用 Eclipse 來設定 Apache Spark 應用程式？](spark/apache-spark-eclipse-tool-plugin.md)<br><br>[如何使用 VSCode 來設定 Apache Spark 應用程式？](hdinsight-for-vscode.md)<br><br>[造成 Apache Spark 應用程式 OutOfMemoryError 例外狀況的原因為何？](spark/apache-troubleshoot-spark.md#what-causes-an-apache-spark-application-outofmemoryerror-exception)|
|![Storm](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[Apache Storm 疑難排解](hdinsight-troubleshoot-STORM.md)|<br>[如何存取叢集上的 Apache Storm UI？](storm/apache-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[如何將 Apache Storm 事件中樞 Spout 檢查點資訊從一個拓撲傳輸到另一個拓撲？](storm/apache-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[如何找出叢集上的 Storm 二進位檔？](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[如何判斷 Storm 叢集的部署拓撲？](storm/apache-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[如何找出用於開發的 Apache Storm 事件中樞 Spout 二進位檔？](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)|
|![YARN](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[Apache Hadoop YARN 疑難排解](hdinsight-troubleshoot-YARN.md)|<br>[如何在叢集上建立新的 Apache Hadoop YARN 佇列？](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[如何從叢集下載 Apache Hadoop YARN 記錄？](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>HDInsight 疑難排解資源

| 如需詳細資訊 | 請參閱以下文章 |
| --- | --- |
| Linux 上的 HDInsight 與最佳化 | - [在 Linux 上使用 HDInsight 的相關資訊](hdinsight-hadoop-linux-information.md)<br>- [Apache Hadoop 記憶體和效能疑難排解](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Apache Hive 查詢效能](https://web.archive.org/web/20190217214250/ https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| 記錄和傾印 | - [存取 Linux 上的 Apache Hadoop YARN 應用程式記錄](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [為 Linux 上的 Apache Hadoop 服務啟用堆積傾印](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>- [分析 HDInsight 記錄](hdinsight-debug-jobs.md)|
| Errors | - [了解並解決 WebHCat 錯誤](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [修正 OutofMemory 錯誤的 Apache Hive 設定](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| 工具 | - [將 Apache Hive 查詢最佳化](hdinsight-hadoop-optimize-hive-query.md)<br>- [HDInsight IntelliJ 工具](./spark/apache-spark-intellij-tool-plugin.md)<br>- [HDInsight Eclipse 工具](./spark/apache-spark-eclipse-tool-plugin.md)<br>- [HDInsight VSCode 工具](hdinsight-for-vscode.md)<br>- [HDInsight Visual Studio 工具](./hadoop/apache-hadoop-visual-studio-tools-get-started.md) |
