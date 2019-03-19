---
title: 適用於 Apache Kafka-Azure HDInsight 的 azure 監視器記錄檔
description: 了解如何使用 Azure 監視器記錄來分析 Azure HDInsight 上的 Apache Kafka 叢集的記錄。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 281b4b8d20957cbbbf0f4ff52166e8c3a78b3e7d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58108132"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>在 HDInsight 上分析 Apache Kafka 的記錄

了解如何使用 Azure 監視器記錄來分析 Apache Kafka on HDInsight 所產生的記錄。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>適用於 Apache Kafka 中啟用 Azure 監視器記錄檔

若要啟用 HDInsight 的 Azure 監視器記錄檔的步驟也適用於所有 HDInsight 叢集。 若要了解如何建立和設定所需的服務，請使用下列連結：

1. 建立 Log Analytics 工作區。 如需詳細資訊，請參閱[開始使用 Log Analytics 工作區](https://docs.microsoft.com/azure/log-analytics)文件。

2. 在 HDInsight 叢集上建立 Kafka。 如需詳細資訊，請參閱[開始使用 HDInsight 上的 Apache Kafka](apache-kafka-get-started.md) 文件。

3. 設定 Kafka 叢集以使用 Azure 監視器記錄檔。 如需詳細資訊，請參閱 <<c0> [ 使用 Azure 監視器記錄來監視 HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md)文件。

    > [!NOTE]  
    > 您也可以設定叢集使用 Azure 監視器記錄檔使用`Enable-AzureRmHDInsightOperationsManagementSuite`cmdlet。 這個 Cmdlet 需要下列資訊︰
    >
    > * HDInsight 叢集名稱。
    > * Azure 監視器記錄工作區識別碼。 您可以在 Log Analytics 工作區中找到工作區識別碼。
    > * Log analytics 連線主索引鍵。 若要尋找主索引鍵，請在 Azure 入口網站中開啟工作區，然後從左側功能表中選取 [進階設定]。 從 [進階設定] 中，選取 [連接的來源]>[Linux 伺服器]。


> [!IMPORTANT]  
> 可能需要大約 20 分鐘的時間才能資料供 Azure 監視器記錄檔。

## <a name="query-logs"></a>查詢記錄

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 Log Analytics 工作區。

2. 選取 [記錄搜尋]。 您可以在這裡搜尋從 Kafka 收集而來的資料。 以下是一些範例搜尋：

   * 磁碟使用量：`Perf | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

   * CPU 使用率：`Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

   * 每秒傳入訊息：`metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)`

   * 每秒傳入位元組：`metrics_kafka_CL | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

   * 每秒傳出位元組：`metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

     > [!IMPORTANT]  
     > 請將查詢值取代為叢集特有的資訊。 例如，`ClusterName_s` 必須設定為您的叢集名稱。 `HostName_s` 必須設定為叢集中背景工作角色節點的網域名稱。

     您也可以輸入 `*` 來搜尋所有記錄的類型。 目前我們提供以下記錄的查詢：

     | 記錄類型 | 描述 |
     | ---- | ---- |
     | log\_kafkaserver\_CL | Kafka broker server.log |
     | log\_kafkacontroller\_CL | Kafka broker controller.log |
     | metrics\_kafka\_CL | Kafka JMX metrics |

     ![CPU 使用率搜尋的影像](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
## <a name="next-steps"></a>後續步驟

如需有關 Azure 監視器的詳細資訊，請參閱 < [Azure 監視器概觀](../../log-analytics/log-analytics-get-started.md)文件。

如需使用 Apache Kafka 的詳細資訊，請參閱下列文件：

 * [在 HDInsight 叢集之間製作 Apache Kafka 的鏡像](apache-kafka-mirroring.md)
 * [在 HDInsight 上增加 Apache Kafka 的延展性](apache-kafka-scalability.md)
 * [搭配 Apache Kafka 使用 Apache Spark 串流 (DStream) ](../hdinsight-apache-spark-with-kafka.md)
 * [將 Apache Spark 結構化串流用於 Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
