---
title: 如何搭配 Java 使用 Azure 服務匯流排主題 | Microsoft Docs
description: 使用 Azure 中的服務匯流排主題和訂用帳戶。
services: service-bus-messaging
documentationcenter: java
author: spelluru
manager: timlt
editor: ''
ms.assetid: 63d6c8bd-8a22-4292-befc-545ffb52e8eb
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 09/17/2018
ms.author: spelluru
ms.openlocfilehash: 501d15ebbb373c100dd735e97bebf2f085a9579e
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241305"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-java"></a>如何透過 Java 使用服務匯流排主題和訂用帳戶

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

在本快速入門中，您會執行下列步驟： 

- 使用 Azure 入口網站建立主題
- 使用 Azure 入口網站為主題建立三個訂用帳戶
- 撰寫 Java 程式碼以將訊息傳送到主題
- 撰寫 Java 程式碼以從訂用帳戶接收訊息

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free)。
- [Azure SDK for Java][Azure SDK for Java]。 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>什麼是服務匯流排主題和訂用帳戶？
服務匯流排主題和訂用帳戶支援「發佈/訂閱」  訊息通訊模型。 使用主題和訂用帳戶時，分散式應用程式的元件彼此不直接通訊，相反的，他們會透過扮演中繼角色的主題來交換訊息。

![主題概念](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

有別於服務匯流排佇列，服務匯流排佇列中的每個訊息只會由單一取用者處理，主題和訂用帳戶採用發佈/訂閱模式，提供一對多的通訊形式。 一個主題可以登錄多個訂用帳戶。 當訊息傳送至主題時，每個訂用帳戶都可取得訊息來個別處理。 主題的訂用帳戶類似於虛擬佇列，同樣可接收已傳送到主題的訊息複本。 您可以選擇為個別訂用帳戶登錄主題的篩選規則，以篩選或限制主題的哪些訊息由哪些主題訂用帳戶接收。

服務匯流排主題和訂用帳戶可讓您擴大處理非常多使用者和應用程式上大量的訊息。

## <a name="create-a-service-bus-namespace"></a>建立服務匯流排命名空間

服務匯流排傳訊命名空間提供唯一的範圍容器 (依其[完整網域名稱](https://wikipedia.org/wiki/Fully_qualified_domain_name)來參考)，您可以在其中建立一或多個佇列、主題和訂用帳戶。 下列範例會在新的或現有的[資源群組](/azure/azure-resource-manager/resource-group-portal)中建立服務匯流排傳訊命名空間：

1. 在入口網站的左方瀏覽窗格中，依序按一下 [+ 建立資源]、[企業整合] 及 [服務匯流排]。
2. 在 [建立命名空間]  對話方塊中，輸入命名空間名稱。 系統會立即檢查此名稱是否可用。
3. 確定命名空間名稱可用之後，請選擇定價層 ([標準] 或 [進階])。
4. 在 [訂用帳戶]  欄位中，選擇要在其中建立命名空間的 Azure 訂用帳戶。
5. 在 [資源群組] 欄位中，選擇要存留命名空間的現有資源群組，或是建立新的資源群組。      
6. 在 [位置] 中，選擇應裝載您命名空間的國家或地區。
7. 按一下頁面底部的 [新增] 。 此時系統會建立並啟用命名空間。 系統為帳戶提供資源時，您可能需要等幾分鐘。

  ![namespace](./media/service-bus-tutorial-topics-subscriptions-portal/create-namespace.png)

### <a name="obtain-the-management-credentials"></a>取得管理認證

建立新命名空間會自動產生初始共用存取簽章 (SAS) 規則，其利用相關聯的主要和次要金鑰組，分別授與命名空間的所有層面的完全控制權。 若要複製初始規則，請遵循下列步驟：

1. 按一下 [所有資源]，然後按一下新建立的命名空間名稱。
2. 在命名空間視窗中，按一下 [共用存取原則]。
3. 在 [共用存取原則] 畫面中，按一下 **RootManageSharedAccessKey**。
4. 在 [原則: RootManageSharedAccessKey] 視窗中，按一下 [主要連接字串] 旁邊的 [複製] 按鈕，將連接字串複製到剪貼簿以供後續使用。 將此值貼到記事本或一些其他暫存位置。

    ![connection-string](./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png)
5. 重複前一個步驟，複製 [主要金鑰] 的值並貼到暫存位置以供後續使用。

## <a name="create-a-topic"></a>建立主題 
若要建立服務匯流排主題，請指定您要用來建立主題的命名空間。 下列範例說明如何在入口網站上建立主題：

1. 在入口網站的左方瀏覽窗格中，按一下 [服務匯流排] \(如果您未看見 [服務匯流排]，請按一下 [所有服務])。
2. 按一下要在其中建立主題的命名空間。
3. 在命名空間視窗中按一下 [主題]，然後在 [主題] 視窗中按一下 [+ 主題]。
4. 輸入 **BasicTopic** 作為主題 [名稱]，並且讓其他值保留其預設值。
5. 在視窗底部按一下 [建立]。


## <a name="create-subscriptions-for-the-topic"></a>為主題建立訂用帳戶
1. 選取您建立的**主題**。
2. 按一下 [+ 訂用帳戶]，輸入訂用帳戶名稱 **Subscription1**，並將所有其他值保留為預設值。
3. 再重複兩次前述步驟，建立名為 **Subscription2** 和 **Subscription3** 的訂用帳戶。


## <a name="configure-your-application-to-use-service-bus"></a>設定應用程式以使用服務匯流排
在建置此範例之前，請先確定您已安裝 [Azure SDK for Java][Azure SDK for Java]。 如果您使用的是 Eclipse，則可以安裝包含 Azure SDK for Java 的[適用於 Eclipse 的 Azure 工具組][Azure Toolkit for Eclipse]。 然後您可以將 **Microsoft Azure Libraries for Java** 新增至您的專案：

![Eclipse 組建路徑中的程式庫](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

您也需要將下列 JAR 新增至 Java 組建路徑：

- gson-2.6.2.jar
- commons-cli-1.4.jar
- proton-j-0.21.0.jar

使用 **Main** 方法新增類別，然後在 Java 檔案頂端新增下列 `import` 陳述式：

```java
import com.google.gson.reflect.TypeToken;
import com.microsoft.azure.servicebus.*;
import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import com.google.gson.Gson;
import static java.nio.charset.StandardCharsets.*;
import java.time.Duration;
import java.util.*;
import java.util.concurrent.*;
import java.util.function.Function;
import org.apache.commons.cli.*;
import org.apache.commons.cli.DefaultParser;
```

## <a name="send-messages-to-a-topic"></a>傳送訊息至主題
更新 **main** 方法以建立 **TopicClient** 物件，並叫用 helper 方法，以非同步方式將範例訊息傳送至服務匯流排主題。

> [!NOTE] 
> - 以您的服務匯流排命名空間名稱取代 `<NameOfServiceBusNamespace>`。 
> - 以您命名空間的存取金鑰取代 `<AccessKey>`。

```java
public class MyServiceBusTopicClient {

    static final Gson GSON = new Gson();
    
    public static void main(String[] args) throws Exception, ServiceBusException {
        // TODO Auto-generated method stub

        TopicClient sendClient;
        String connectionString = "Endpoint=sb://<NameOfServiceBusNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<AccessKey>";
        sendClient = new TopicClient(new ConnectionStringBuilder(connectionString, "BasicTopic"));       
        sendMessagesAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());
    }

    static CompletableFuture<Void> sendMessagesAsync(TopicClient sendClient) {
        List<HashMap<String, String>> data =
                GSON.fromJson(
                        "[" +
                                "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                                "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                                "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                                "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                                "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                                "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                                "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                                "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                                "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                                "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                                "]",
                        new TypeToken<List<HashMap<String, String>>>() {
                        }.getType());

        List<CompletableFuture> tasks = new ArrayList<>();
        for (int i = 0; i < data.size(); i++) {
            final String messageId = Integer.toString(i);
            Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
            message.setContentType("application/json");
            message.setLabel("Scientist");
            message.setMessageId(messageId);
            message.setTimeToLive(Duration.ofMinutes(2));
            System.out.printf("Message sending: Id = %s\n", message.getMessageId());
            tasks.add(
                    sendClient.sendAsync(message).thenRunAsync(() -> {
                        System.out.printf("\tMessage acknowledged: Id = %s\n", message.getMessageId());
                    }));
        }
        return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
    }
}
```

服務匯流排主題支援的訊息大小上限：在[標準層](service-bus-premium-messaging.md)中為 256 KB 以及在[進階層](service-bus-premium-messaging.md)中為 1 MB。 標頭 (包含標準和自訂應用程式屬性) 可以容納 64 KB 的大小上限。 主題中所保存的訊息數目沒有限制，但主題所保存的訊息大小總計會有限制。 此主題大小會在建立時定義，上限是 5 GB。

## <a name="how-to-receive-messages-from-a-subscription"></a>如何自訂用帳戶接收訊息
更新 **main** 方法來為三個訂用帳戶建立三個 **SubscriptionClient** 物件，並叫用 helper 方法，以非同步方式接收來自服務匯流排主題的訊息。 範例程式碼會假設您已建立名為 **BasicTopic** 的主題，以及三個名為 **Subscription1**、**Subscription2**和 **Subscription3** 的訂用帳戶。 如果您對這些項目使用不同名稱，請先更新程式碼，然後再進行測試。 

```java
public class MyServiceBusTopicClient {

    static final Gson GSON = new Gson();
    
    public static void main(String[] args) throws Exception, ServiceBusException {
        SubscriptionClient subscription1Client = new SubscriptionClient(new ConnectionStringBuilder(connectionString, "BasicTopic/subscriptions/Subscription1"), ReceiveMode.PEEKLOCK);
        SubscriptionClient subscription2Client = new SubscriptionClient(new ConnectionStringBuilder(connectionString, "BasicTopic/subscriptions/Subscription2"), ReceiveMode.PEEKLOCK);
        SubscriptionClient subscription3Client = new SubscriptionClient(new ConnectionStringBuilder(connectionString, "BasicTopic/subscriptions/Subscription3"), ReceiveMode.PEEKLOCK);        

        registerMessageHandlerOnClient(subscription1Client);
        registerMessageHandlerOnClient(subscription2Client);
        registerMessageHandlerOnClient(subscription3Client);
    }
    
    static void registerMessageHandlerOnClient(SubscriptionClient receiveClient) throws Exception {

        // register the RegisterMessageHandler callback
        IMessageHandler messageHandler = new IMessageHandler() {
            // callback invoked when the message handler loop has obtained a message
            public CompletableFuture<Void> onMessageAsync(IMessage message) {
                // receives message is passed to callback
                if (message.getLabel() != null &&
                        message.getContentType() != null &&
                        message.getLabel().contentEquals("Scientist") &&
                        message.getContentType().contentEquals("application/json")) {

                    byte[] body = message.getBody();
                    Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                    System.out.printf(
                            "\n\t\t\t\t%s Message received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                                    "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                            receiveClient.getEntityPath(),
                            message.getMessageId(),
                            message.getSequenceNumber(),
                            message.getEnqueuedTimeUtc(),
                            message.getExpiresAtUtc(),
                            message.getContentType(),
                            scientist != null ? scientist.get("firstName") : "",
                            scientist != null ? scientist.get("name") : "");
                }
                return receiveClient.completeAsync(message.getLockToken());
            }
            
            public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                System.out.printf(exceptionPhase + "-" + throwable.getMessage());
            }
        };

 
        receiveClient.registerMessageHandler(
                    messageHandler,
                    // callback invoked when the message handler has an exception to report
                // 1 concurrent call, messages are auto-completed, auto-renew duration
                new MessageHandlerOptions(1, false, Duration.ofMinutes(1)));

    }
}
```

## <a name="run-the-program"></a>執行程式
執行程式後應該會看到類似下列的輸出：

```java
Message sending: Id = 0
Message sending: Id = 1
Message sending: Id = 2
Message sending: Id = 3
Message sending: Id = 4
Message sending: Id = 5
Message sending: Id = 6
Message sending: Id = 7
Message sending: Id = 8
Message sending: Id = 9
    Message acknowledged: Id = 0
    Message acknowledged: Id = 9
    Message acknowledged: Id = 7
    Message acknowledged: Id = 8
    Message acknowledged: Id = 5
    Message acknowledged: Id = 6
    Message acknowledged: Id = 3
    Message acknowledged: Id = 2
    Message acknowledged: Id = 4
    Message acknowledged: Id = 1

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 0, 
                        SequenceNumber = 11, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.442Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.442Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Albert, name = Einstein ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 0, 
                        SequenceNumber = 11, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.442Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.442Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Albert, name = Einstein ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 9, 
                        SequenceNumber = 12, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Nikolaus, name = Kopernikus ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 8, 
                        SequenceNumber = 13, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Johannes, name = Kepler ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 0, 
                        SequenceNumber = 11, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.442Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.442Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Albert, name = Einstein ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 9, 
                        SequenceNumber = 12, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Nikolaus, name = Kopernikus ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 7, 
                        SequenceNumber = 14, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Galileo, name = Galilei ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 9, 
                        SequenceNumber = 12, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Nikolaus, name = Kopernikus ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 8, 
                        SequenceNumber = 13, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Johannes, name = Kepler ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 6, 
                        SequenceNumber = 15, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Michael, name = Faraday ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 8, 
                        SequenceNumber = 13, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Johannes, name = Kepler ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 7, 
                        SequenceNumber = 14, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Galileo, name = Galilei ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 5, 
                        SequenceNumber = 16, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Niels, name = Bohr ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 7, 
                        SequenceNumber = 14, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Galileo, name = Galilei ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 6, 
                        SequenceNumber = 15, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Michael, name = Faraday ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 4, 
                        SequenceNumber = 17, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Isaac, name = Newton ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 6, 
                        SequenceNumber = 15, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Michael, name = Faraday ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 5, 
                        SequenceNumber = 16, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Niels, name = Bohr ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 3, 
                        SequenceNumber = 18, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Steven, name = Hawking ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 5, 
                        SequenceNumber = 16, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Niels, name = Bohr ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 4, 
                        SequenceNumber = 17, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Isaac, name = Newton ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 2, 
                        SequenceNumber = 19, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Marie, name = Curie ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 4, 
                        SequenceNumber = 17, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Isaac, name = Newton ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 3, 
                        SequenceNumber = 18, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Steven, name = Hawking ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 1, 
                        SequenceNumber = 20, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Werner, name = Heisenberg ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 2, 
                        SequenceNumber = 19, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Marie, name = Curie ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 3, 
                        SequenceNumber = 18, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Steven, name = Hawking ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 2, 
                        SequenceNumber = 19, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Marie, name = Curie ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 1, 
                        SequenceNumber = 20, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Werner, name = Heisenberg ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 1, 
                        SequenceNumber = 20, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Werner, name = Heisenberg ]
```


## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱[服務匯流排佇列、主題和訂用帳戶][Service Bus queues, topics, and subscriptions]。

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse.md
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage

[0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
[2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
[3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png
