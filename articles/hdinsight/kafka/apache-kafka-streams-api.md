---
title: 使用 Apache Kafka 串流 API - Azure HDInsight | Microsoft Docs
description: 了解如何搭配 HDInsight 上的 Kafka 使用 Apache Kafka 串流 API。 此 API 可讓您在 Kafka 主題之間執行串流處理。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: 36d67cdb99871f3948db1f6497b1a4638df4f3f1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="apache-kafka-streams-api"></a>Apache Kafka 串流 API

了解如何建立應用程式，該應用程式會使用 Kafka 串流 AP，並且與 HDInsight 上的 Kafka 搭配使用。

當使用 Apache Kafka 時，串流處理通常會使用 Apache Spark 或 Storm 來完成。 Kafka 0.10.0 版 (在 HDInsight 3.5 和 3.6 中) 導入了 Kafka 串流 API。 此 API 可讓您使用在 Kafka 上執行的應用程式，轉換輸入和輸出主題之間的資料流。 在某些情況下，這可能是建立 Spark 或 Storm 串流解決方案的替代方案。 如需有關 Kafka 串流的詳細資訊，請參閱 Apache.org 上的[串流簡介](https://kafka.apache.org/10/documentation/streams/)文件。

## <a name="set-up-your-development-environment"></a>設定開發環境

您必須在開發環境中安裝下列元件：

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 或同等功能版本，例如 OpenJDK。

* [Apache Maven](http://maven.apache.org/)

* SSH 用戶端和 `scp` 命令。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) 文件。

## <a name="set-up-your-deployment-environment"></a>設定您的部署環境

這個範例需要 HDInsight 3.6 上的 Kafka。 若要深入了解如何在 HDInsight 叢集上建立 Kafka，請參閱[開始使用 HDInsight 上的 Kafka](apache-kafka-get-started.md) 文件。

## <a name="build-and-deploy-the-example"></a>建置並部署範例

使用下列步驟以建置專案，並將其部署至 HDInsight 叢集上的 Kafka。

1. 從 [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) 下載範例。

2. 將目錄變更為 `Streaming` 目錄，然後使用下列命令以建立 jar 套件：

    ```bash
    mvn clean package
    ```

    此命令會建立名為 `target` 的目錄，其中包含名為 `kafka-streaming-1.0-SNAPSHOT.jar` 的檔案。

3. 使用下列命令將 `kafka-streaming-1.0-SNAPSHOT.jar` 檔案複製到 HDInsight 叢集：
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    以叢集的 SSH 使用者取代 **USERNAME**，並以叢集的名稱取代 **CLUSTERNAME**。 出現提示時，請輸入 SSH 使用者帳戶的密碼。 如需搭配 HDInsight 使用 `scp` 的詳細資訊，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="run-the-example"></a>執行範例

1. 若要開啟與叢集的 SSH 連線，使用下列命令：

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    以叢集的 SSH 使用者取代 **USERNAME**，並以叢集的名稱取代 **CLUSTERNAME**。 出現提示時，請輸入 SSH 使用者帳戶的密碼。 如需搭配 HDInsight 使用 `scp` 的詳細資訊，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。

4. 若要建立本範例所使用的 Kafka 主題，請使用下列命令：

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

    以您 HDInsight 叢集的名稱取代 __您的叢集名稱__。 出現提示時，輸入 HDInsight 叢集登入帳戶的密碼。

    > [!NOTE]
    > 如果您的叢集登入與預設值 `admin` 不同，以您的叢集登入名稱取代上一個命令中的 `admin` 值。

5. 若要執行此範例，您必須執行三件事：

    * 啟動 `kafka-streaming.jar` 中包含的串流解決方案。
    * 啟動會寫入 `test` 主題的產生者。
    * 啟動取用者，讓您可以查看寫入至 `wordcounts` 主題的輸出

    > [!NOTE]
    > 您需要確認 Kafka 訊息代理程式設定檔中的 `auto.create.topics.enable` 屬性已設定為 `true`。 您可以使用 Ambari Web UI 在進階 Kafka 訊息代理程式設定檔中檢視和修改這個屬性。 否則，您需要在使用下列命令執行此範例之前，先手動建立中繼主題 `RekeyedIntermediateTopic`：
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic  --zookeeper $KAFKAZKHOSTS
    ```
    
    您可以藉由開啟三個 SSH 工作階段來完成這些作業。 接著，您必須設定每個項目的 `$KAFKABROKERS` 和 `$KAFKAZKHOSTS`，方法是在每個 SSH 工作階段中執行此區段的步驟 4。 較簡單的解決方案是使用 `tmux` 公用程式，它可以將目前的 SSH 顯示分割成多個區段。 若要使用 `tmux` 來啟動串流、產生者和取用者，請使用下列命令：

    ```bash
    tmux new-session '/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer' \; split-window -h 'java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS' \; split-window -v '/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test' \; attach
    ```

    此命令會將 SSH 顯示分割成三個區段：

    * 左側區段會執行主控台取用者，它會從 `wordcounts` 主題讀取訊息：`/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer`

        > [!NOTE]
        > `--property` 參數會告知主控台取用者列印索引鍵 (字組) 和計數 (值)。 這個參數也會設定從 Kafka 讀取這些值時要使用的還原序列化。

    * 右上方區段會執行串流 API 解決方案：`java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS`

    * 右下方區段會執行主控台產生者，並等候您輸入訊息以傳送到 `test` 主題：`/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test`
 
6. `tmux` 命令分割顯示之後，您的游標會在右下方區段。 開始輸入句子。 在每個句子後面，左窗格會更新以顯示唯一字組的計數。 輸出大致如下：
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    > [!NOTE]
    > 每次遇到一個單字，計數就會遞增。

7. 使用 __Ctrl + C__ 來結束產生者。 繼續使用 __Ctrl + C__ 來結束應用程式和取用者。

## <a name="next-steps"></a>後續步驟

在本文件中，您會了解如何搭配 HDInsight 上的 Kafka 使用 Kafka 串流 API。 使用下列各項來深入了解 Kafka 的使用方式︰

* [分析 Kafka 日誌](apache-kafka-log-analytics-operations-management.md)
* [在Kafka 叢集之間複寫資料](apache-kafka-mirroring.md)
* [採用 HDInsight 的 Kafka 產生者和取用者 API](apache-kafka-producer-consumer-api.md)
* [搭配 HDInsight 上的 Kafka 使用 Apacha Spark 串流 (DStream)](../hdinsight-apache-spark-with-kafka.md)
* [搭配 HDInsight 上的 Kafka 使用 Spark 結構化串流](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [使用 Apache Spark 結構化串流將資料從 HDInsight 上的 Kafka 移至 Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [使用 Apache Storm 搭配 HDInsight 上的 Kafka](../hdinsight-apache-storm-with-kafka.md)
* [透過 Azure 虛擬網路連線到 Kafka](apache-kafka-connect-vpn-gateway.md)
