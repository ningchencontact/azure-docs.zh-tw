---
title: 如何使用 AMQP 1.0 與 Java JMS 服務匯流排 API 搭配 |Microsoft Docs
description: 如何搭配 Azure 服務匯流排和 Advanced Message Queuing Protodol (AMQP) 1.0 使用 Java Message Service (JMS)。
services: service-bus-messaging
documentationcenter: java
author: axisc
manager: timlt
editor: spelluru
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 03/05/2019
ms.author: aschhab
ms.openlocfilehash: a7e4282a176794fe885049173ba56ce2461cd6fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60310956"
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>如何搭配使用 Java 訊息服務 (JMS) API 與服務匯流排和 AMQP 1.0
進階訊息佇列通訊協定 (AMQP) 1.0 是一個有效率且可靠的有線等級訊息通訊協定，可以用來建置強大的跨平台訊息應用程式。

在服務匯流排中支援 AMQP 1.0 代表您現在能夠從一組平台中使用有效率的二進位通訊協定，來運用佇列與發佈/訂閱代理傳訊功能。 此外，您還可以建置使用混合語言、架構及作業系統所建置之元件所組成的應用程式。

本文說明如何利用常用的 Java 訊息服務 (JMS) API 標準，從 Java 應用程式使用服務匯流排傳訊功能 (佇列和發佈/訂閱主題)。 這是說明如何使用服務匯流排 .NET API 達到相同效用的[附屬文章](service-bus-amqp-dotnet.md)。 您可以同時使用這兩個指南了解使用 AMQP 1.0 的跨平台訊息。

## <a name="get-started-with-service-bus"></a>開始使用服務匯流排
本指南假設您已經有服務匯流排命名空間，其中包含名稱為 **basicqueue** 的佇列。 如果沒有，您可以使用 [Azure 入口網站](service-bus-create-namespace-portal.md)建立[命名空間和佇列](https://portal.azure.com)。 有关如何创建服务总线命名空间和队列的详细信息，请参阅[服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)。

> [!NOTE]
> 分区队列和主题也支持 AMQP。 如需詳細資訊，請參閱[分割傳訊實體](service-bus-partitioning.md)及[服務匯流排分割佇列和主題的 AMQP 1.0 支援](service-bus-partitioned-queues-and-topics-amqp-overview.md)。
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>下載 AMQP 1.0 JMS 用戶端程式庫
如需可從哪裡下載最新版 Apache Qpid JMS AMQP 1.0 用戶端程式庫的相關資訊，請造訪 [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html)。

您使用服務匯流排建立和執行 JMS 應用程式時，必須從 Apache Qpid JMS AMQP 1.0 散發封裝將下列 4 個 JAR 檔加入 Java CLASSPATH：

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[版本].jar

> [!NOTE]
> JMS JAR 名稱和版本可能已經變更。 如需詳細資料，請參閱 [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10) \(英文\)。

## <a name="coding-java-applications"></a>編寫 Java 應用程式
### <a name="java-naming-and-directory-interface-jndi"></a>Java 命名及目錄介面 (JNDI)
JMS 使用 Java 命名及目錄介面 (JNDI) 建立邏輯名稱與實際名稱之間的區別。 使用 JNDI 可以解析兩種 JMS 物件：ConnectionFactory 和 Destination。 JNDI 使用提供者模型，您可以在其中插入不同的目錄服務處理名稱解析作業。 Apache Qpid JMS AMQP 1.0 程式庫提供使用下列格式的內容檔案設定的簡單內容檔案型 JNDI 提供者：

```
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>設定 JNDI 內容及設定 ConnectionFactory

參考在 [Azure 入口網站](https://portal.azure.com)的 [共用存取原則] 中，於 [主要連接字串] 下可取得的 [連接字串]
```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>設定生產者和取用者目的地佇列
在 Qpid 屬性檔案 JNDI 提供者中用來定義目的地的項目使用下列格式：

建立產生者的目的地佇列 - 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

建立取用者的目的地佇列 - 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>撰寫 JMS 應用程式
對於服務匯流排使用 JMS 時，不需要特別 API 或選項。 不過，後續將說明一些限制。 和任何 JMS 應用程式一樣，首先需要設定 JNDI 環境，才能夠解析 **ConnectionFactory** 和目的地。

#### <a name="configure-the-jndi-initialcontext"></a>設定 JNDI InitialContext
將組態資訊的雜湊表傳遞到 javax.naming.InitialContext 類別的建構函式，將設定 JNDI 環境。 雜湊表中的兩個所需項目是 Initial Context Factory 和 Provider URL 的類別名稱。 下列程式碼顯示如何使用名稱為 **servicebus.properties** 的內容檔案，設定 JNDI 環境使用 Qpid 內容檔案型 JNDI 提供者。

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>使用服務匯流排佇列的簡單 JMS 應用程式
下列範例程式將 JMS TextMessages 傳送到 JNDI 邏輯名稱為 QUEUE 的服務匯流排佇列，並收到傳回的訊息。

您可以從 [Azure Service Bus 範例 JMS 佇列快速入門](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart) \(英文\)，對所有原始程式碼和設定資訊進行所有存取

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS Queue producer into
 * an Azure Service Bus Queue, and receive them with a JMS message consumer.
 * JMS Queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // we create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter
        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // receives message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>執行應用程式
傳遞來自 [共用存取原則] 的 [連接字串]，以執行應用程式。
以下是執行應用程式的表單輸出：

```
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>AMQP 配置和服務匯流排作業對應
以下是 AMQP 配置轉譯為服務匯流排作業的方式：

```
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>JMS 主题与服務匯流排主題
通过 Java 消息服务 (JMS) API 使用 Azure 服务总线主题和订阅可以提供基本的发送和接收功能。 从其他使用 JMS 兼容 API 的消息代理处移植应用程序时，这是一种很方便的选择，即使服务总线主题不同于 JMS 主题且需要一些调整。 

Azure 服务总线主题将消息路由到已命名的、共享的、持久的订阅中，这些订阅通过 Azure 资源管理接口、Azure 命令行工具或 Azure 门户进行管理。 每个订阅允许使用最多 2000 条选择规则，每条规则可能有一个筛选器条件以及一项适用于 SQL 筛选器的元数据转换操作。 每次出现筛选器条件匹配的情况时，系统就会选择将要复制到订阅中的输入消息。  

从订阅接收消息与从队列接收消息是相同的。 每个订阅都有一个关联的死信队列，并且可以将消息自动转发给其他队列或主题。 

JMS 主题允许客户端动态创建非持久的和持久的订阅者，这样就可以选择性地允许通过消息选择器来筛选消息。 服务总线不支持这些非共享的实体。 但是，服务总线的 SQL 筛选器规则语法非常类似于 JMS 支持的消息选择器语法。 

如此示例所示，JMS 主题发布者端兼容服务总线，但动态订阅者则不兼容。 不支持将下述与拓扑相关的 JMS API 与服务总线配合使用。 

## <a name="unsupported-features-and-restrictions"></a>不支援的功能和限制
對於服務匯流排使用 JMS 而不使用 AMQP 1.0 會有下列限制：

* 對於各個**工作階段**僅允許一個 **MessageProducer** 或 **MessageConsumer**。 如果您需要在應用程式中建立多個 **MessageProducers** 或 **MessageConsumers**，請分別建立專用的**工作階段**。
* 目前不支援可變更的主題訂閱。
* **MessageSelectors** 。
* 不支援交易式工作階段和分散式交易。

此外，Azure 服務匯流排會分割控制層與資料層，並因此不支援數個 JMS 的動態拓撲函式：

| 不支援的方法          | 更換為                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | 建立移轉訊息選取器的主題訂用帳戶                                 |
| createDurableConsumer       | 建立移轉訊息選取器的主題訂用帳戶                                 |
| createSharedConsumer        | 服務匯流排主題一律可共用，請參考上面                                       |
| createSharedDurableConsumer | 服務匯流排主題一律可共用，請參考上面                                       |
| createTemporaryTopic        | 透過管理 API/工具/入口網站建立主題，且 *AutoDeleteOnIdle* 設為到期時間 |
| createTopic                 | 透過管理 API/工具/入口網站建立主題                                           |
| 取消訂閱                 | 刪除主題管理 API/工具/入口網站                                             |
| createBrowser               | 不支援。 使用服務匯流排 API 的 Peek() 功能                         |
| createQueue                 | 透過管理 API/工具/入口網站建立佇列                                           | 
| createTemporaryQueue        | 透過管理 API/工具/入口網站建立佇列，且 *AutoDeleteOnIdle* 設為到期時間 |

## <a name="summary"></a>總結
本作法指南說明如何以常用的 JMS API 和 AMQP 1.0 從 Java 使用服務匯流排代理訊息功能 (佇列和發佈/訂閱主題)。

您也可以使用包括 .NET、C、Python 和 PHP 在內的其他語言所撰寫的 Service Bus AMQP 1.0。 使用這些不同的語言撰寫的元件可使用服務匯流排中的 AMQP 1.0 支援確實完整交換訊息。

## <a name="next-steps"></a>後續步驟
* [Azure 服務匯流排中的 AMQP 1.0 支援](service-bus-amqp-overview.md)
* [如何将 AMQP 1.0 与服务总线 .NET API 配合使用](service-bus-dotnet-advanced-message-queuing.md)
* [服務匯流排 AMQP 1.0 開發人員指南](service-bus-amqp-dotnet.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [Java 開發人員中心](https://azure.microsoft.com/develop/java/)

