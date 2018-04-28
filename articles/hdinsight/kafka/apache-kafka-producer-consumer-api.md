---
title: 使用 Apache Kafka Producer 和 Consumer API - Azure HDInsight | Microsoft Docs
description: 了解如何搭配 HDInsight 上的 Kafka 使用 Apache Kafka Producer 和 Consumer API。 這些 API 可讓您開發寫入和讀取 Apache Kafka 的應用程式。
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
ms.openlocfilehash: 01592401c4c88adeed49b11df4e7963e27b1bcee
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="apache-kafka-producer-and-consumer-apis"></a>Apache Kafka Producer 和 Consumer API

了解如何建立應用程式，該應用程式會搭配 HDInsight 上的 Kafka 使用 Kafka Producer 和 Consumer API。

關於 API 的文件，請參閱 [Producer API](https://kafka.apache.org/documentation/#producerapi) 和 [Consumer API](https://kafka.apache.org/documentation/#consumerapi)。

## <a name="set-up-your-development-environment"></a>設定開發環境

您必須在開發環境中安裝下列元件：

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 或同等功能版本，例如 OpenJDK。

* [Apache Maven](http://maven.apache.org/)

* SSH 用戶端和 `scp` 命令。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) 文件。

## <a name="set-up-your-deployment-environment"></a>設定您的部署環境

這個範例需要 HDInsight 3.6 上的 Kafka。 若要深入了解如何在 HDInsight 叢集上建立 Kafka，請參閱[開始使用 HDInsight 上的 Kafka](apache-kafka-get-started.md) 文件。

## <a name="build-and-deploy-the-example"></a>建置並部署範例

1. 從 [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) 下載範例。

2. 將目錄變更為 `Producer-Consumer` 目錄的位置並使用下列命令︰

    ```
    mvn clean package
    ```

    此命令會建立名為 `target` 的目錄，其中包含名為 `kafka-producer-consumer-1.0-SNAPSHOT.jar` 的檔案。

3. 使用下列命令將 `kafka-producer-consumer-1.0-SNAPSHOT.jar` 檔案複製到 HDInsight 叢集：
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    以叢集的 SSH 使用者取代 **USERNAME**，並以叢集的名稱取代 **CLUSTERNAME**。 出現提示時，請輸入 SSH 使用者的密碼。

## <a id="run"></a> 執行範例

1. 若要開啟與叢集的 SSH 連線，使用下列命令：

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    以叢集的 SSH 使用者取代 **USERNAME**，並以叢集的名稱取代 **CLUSTERNAME**。 出現提示時，請輸入 SSH 使用者帳戶的密碼。 如需搭配 HDInsight 使用 `scp` 的詳細資訊，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 若要建立本範例所使用的 Kafka 主題，請使用下列命令：

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    以您 HDInsight 叢集的名稱取代 __您的叢集名稱__。 出現提示時，輸入 HDInsight 叢集登入帳戶的密碼。

    > [!NOTE]
    > 如果您的叢集登入與預設值 `admin` 不同，請以您的叢集登入名稱取代上一個命令中的 `admin` 值。

3. 若要執行產生器，並且將資料寫入至主題，請使用下列命令：

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

4. 產生器完成後，請使用下列命令從主題讀取︰
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    已讀取的記錄以及記錄計數隨即顯示。

5. 使用 __Ctrl + C__ 來結束取用者。

### <a name="multiple-consumers"></a>多個取用者

Kafka 取用者會在讀取記錄時使用取用者群組。 多個取用者使用相同群組會導致從主題讀取負載平衡。 群組中的每個取用者都會收到一部分的記錄。

取用者應用程式接受作為群組識別碼的參數。 例如，下列命令會使用 `mygroup` 的群組識別碼啟動取用者：
   
```bash
java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
```

若要查看此程序的運作情況，請使用下列步驟︰

1. 重複[執行範例](#run)一節中的步驟 1 和 2，開啟新的 SSH 工作階段。

2. 在這兩個 SSH 工作階段中，輸入下列命令：

    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```
    
    > [!IMPORTANT]
    > 輸入這兩個命令，以便這兩個命令同時執行。

    請注意，訊息讀取計數與上一節不同，在上一節只有一個取用者。 反之，訊息讀取會在執行個體之間分割。

透過主題的資料分割處理相同群組內的用戶端取用。 稍早建立的 `test` 主題有 8 個資料分割。 如果您開啟 8 個 SSH 工作階段並在所有工作階段中啟動某個取用者，則每個取用者都會從主題的單一資料分割讀取記錄。

> [!IMPORTANT]
> 一個取用者群組中的取用者執行個體不得超過資料分割。 在此範例中，一個取用者群組可以包含最多 8 個取用者，因為這是主題中的資料分割數目。 或者，您可以有多個取用者群組，其各有不超過 8 個取用者。

Kafka 中儲存的記錄會依照其在資料分割內接收的順序儲存。 若要達到依序傳遞「資料分割內」的記錄，請建立取用者群組，其中的取用者執行個體數目與資料分割數目相符。 若要達到依序傳遞「主題內」的記錄，請建立只有一個取用者執行個體的取用者群組。

## <a name="next-steps"></a>後續步驟

在本文件中，您會了解如何搭配 HDInsight 上的 Kafka 使用 Kafka Producer and Consumer API。 使用下列各項來深入了解 Kafka 的使用方式︰

* [分析 Kafka 日誌](apache-kafka-log-analytics-operations-management.md)
* [在Kafka 叢集之間複寫資料](apache-kafka-mirroring.md)
* [採用 HDInsight 的 Kafka Streams API](apache-kafka-streams-api.md)
* [搭配 HDInsight 上的 Kafka 使用 Apacha Spark 串流 (DStream)](../hdinsight-apache-spark-with-kafka.md)
* [搭配 HDInsight 上的 Kafka 使用 Spark 結構化串流](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [使用 Apache Spark 結構化串流將資料從 HDInsight 上的 Kafka 移至 Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [使用 Apache Storm 搭配 HDInsight 上的 Kafka](../hdinsight-apache-storm-with-kafka.md)
* [透過 Azure 虛擬網路連線到 Kafka](apache-kafka-connect-vpn-gateway.md)