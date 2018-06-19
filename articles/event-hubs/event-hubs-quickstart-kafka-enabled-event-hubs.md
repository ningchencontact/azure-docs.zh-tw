---
title: 串流至適用於 Kafka 生態系統的 Azure 事件中樞 | Microsoft Docs
description: 使用 Kafka 通訊協定和 API 串流至事件中樞。
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/08/2018
ms.author: bahariri
ms.openlocfilehash: 8ef6240d19ce1ac1b891c95ce525a8bd211a2900
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297218"
---
# <a name="stream-into-event-hubs-for-the-kafka-ecosystem"></a>串流至 Kafka 生態系統的事件中樞

> [!NOTE]
> 您可在 [GitHub](https://github.com/Azure/azure-event-hubs) 上取得此範例

本快速入門說明如何串流至已啟用 Kafka 的事件中樞，而不需要變更通訊協定用戶端或執行您自己的叢集。 您將了解如何僅在應用程式中變更設定，就能讓產生者和取用者與已啟用 Kafka 的事件中樞交談。 Kafka 生態系統的 Azure 事件中樞支援 [Apache Kafka 1.0 版。](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>先決條件

若要完成本快速入門，請確定您具備下列必要條件︰

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)。
* [下載](http://maven.apache.org/download.cgi)及[安裝](http://maven.apache.org/install.html) Maven 二進位封存檔。
* [Git](https://www.git-scm.com/)
* [已啟用 Kafka 的事件中樞命名空間](event-hubs-create.md)

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>使用事件中樞內的 Kafka 傳送及接收訊息

1. 複製 [Azure 事件中樞存放庫](https://github.com/Azure/azure-event-hubs)。

2. 瀏覽至 `azure-event-hubs/samples/kafka/quickstart/producer`。

3. 在 `src/main/resources/producer.config` 中更新產生者的組態詳細資料，如下所示：

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
4. 執行產生者程式碼，並串流至已啟用 Kafka 的事件中樞：
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
5. 瀏覽至 `azure-event-hubs/samples/kafka/quickstart/consumer`。

6. 在 `src/main/resources/consumer.config` 中更新取用者的組態詳細資料，如下所示：
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. 執行取用者程式碼，並使用 Kafka 用戶端從已啟用 Kafka 的事件中樞進行處理：

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

如果事件中樞 Kafka 叢集包含事件，您現在應該會開始從取用者接收到這些事件。

## <a name="next-steps"></a>後續步驟

* [了解事件中樞](event-hubs-what-is-event-hubs.md)
* [了解適用於 Kafka 生態系統的事件中樞](event-hubs-for-kafka-ecosystem-overview.md)
* 使用 [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)，[將事件從 Kafka 內部部署串流至雲端上已啟用 Kafka 的事件中樞](event-hubs-kafka-mirror-maker-tutorial.md)。
* 了解如何使用 [Apache Flink](event-hubs-kafka-flink-tutorial.md) 或 [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md) 串流至已啟用 Kafka 的事件中樞。
