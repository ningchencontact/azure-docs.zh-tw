---
title: 串流至適用於 Kafka 生態系統的 Azure 事件中樞 | Microsoft Docs
description: 使用 Kafka 通訊協定和 API 串流至事件中樞。
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2018
ms.author: bahariri
ms.openlocfilehash: cabbb7ed6157a6c68530ab6b5f405aa67b31a1b2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="stream-into-event-hubs-for-kafka-ecosystem"></a>串流至適用於 Kafka 生態系統的事件中樞

> [!NOTE]
> 您可在 [GitHub](https://github.com/Azure/azure-event-hubs) 上取得此範例

本快速入門說明如何串流至已啟用 Kafka 的事件中樞，而不需要變更通訊協定用戶端或執行您自己的叢集。 您將會了解如何僅在應用程式中變更設定，就能讓生產者和取用者與已啟用 Kafka 的事件中樞交談。 Kafka 生態系統的 Azure 事件中樞支援 [Apache Kafka 1.0 版。](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>先決條件

若要完成本快速入門，請確定您具備︰

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)。
* [下載](http://maven.apache.org/download.cgi)及[安裝](http://maven.apache.org/install.html) Maven 二進位封存檔。
* [Git](https://www.git-scm.com/)
* [已啟用 Kafka 的事件中樞命名空間](event-hubs-create.md)

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>使用事件中樞內的 Kafka 傳送及接收訊息

1. 複製 [Azure 事件中樞存放庫](https://github.com/Azure/azure-event-hubs)。

2. 瀏覽至 `azure-event-hubs/samples/kafka/quickstart/producer`。

3. 在 src/main/resources/producer.config 中更新生產者的設定詳細資料，如下所示。

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
4. 執行生產者程式碼，並串流至已啟用 Kafka 的事件中樞。
   
    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
5. 瀏覽至 azure-event-hubs/samples/kafka/quickstart/consumer。

6. 在 src/main/resources/consumer.config 中更新取用者的設定詳細資料，如下所示。
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. 執行取用者程式碼，並使用 Kafka 用戶端從已啟用 Kafka 的事件中樞進行處理。

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

如果事件中樞 Kafka 叢集包含從生產者排入佇列的事件，您現在應該會開始從取用者接收到這些事件。

## <a name="next-steps"></a>後續步驟

* [了解適用於 Kafka 生態系統的事件中樞](event-hubs-for-kafka-ecosystem-overview.md)
* [了解事件中樞](event-hubs-what-is-event-hubs.md)
* 使用 [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) 從內部部署 Kafka 將事件串流至雲端上已啟用 Kafka 的事件中樞。](event-hubs-kafka-mirror-maker-tutorial.md)
