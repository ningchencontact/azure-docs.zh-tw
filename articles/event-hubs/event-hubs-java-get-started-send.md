---
title: 使用 Java 將事件傳送至 Azure 事件中樞 | Microsoft Docs
description: 開始使用 Java 傳送事件至事件中樞
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 05/30/2018
ms.author: shvija
ms.openlocfilehash: 6217f507b83325acb9a5062aada150fa6f8bc5d2
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2018
ms.locfileid: "40007102"
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>使用 Java 將事件傳送至 Azure 事件中樞

事件中樞是高度可擴充的擷取系統，每秒可擷取數百萬個事件，讓應用程式能處理並分析已連線裝置與應用程式產生的大量資料。 資料收集到事件中樞後，您可以使用任何即時分析提供者或儲存體叢集來轉換與儲存資料。

如需詳細資訊，請參閱 [事件中樞概觀][Event Hubs overview]。

本教學課程也會示範如何使用以 Java 撰寫的主控台應用程式，將事件傳送到事件中樞。 若要使用 Java Event Processor Host 程式庫接收事件，請參閱[此文章](event-hubs-java-get-started-receive-eph.md)，或按一下左側目錄中適當的接收語言。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要下列必要條件：

* Java 開發環境。 本教學課程使用 [Eclipse](https://www.eclipse.org/)。
* 使用中的 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶][]。

本教學課程中的程式碼是根據 [SimpleSend GitHub 範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend)，您可以檢查該範例以查看可完整運作的應用程式。

## <a name="send-events-to-event-hubs"></a>將事件傳送至事件中樞

[Maven 中央儲存機制](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)中的 Maven 專案可使用事件中樞的 Java 用戶端程式庫。 您可以在 Maven 專案檔中用下列相依性宣告來參照此程式庫。 目前版本為 1.0.1：    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.1</version>
</dependency>
```

對於不同類型的組建環境，您可以明確從 [Maven 中央儲存機制](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)取得最新發佈的 JAR 檔案。  

如果是簡單事件發行者，請針對事件中樞用戶端類別匯入 *com.microsoft.azure.eventhubs* 套件，以及針對公用程式類別匯入 *com.microsoft.azure.servicebus* 套件，例如，與 Azure 服務匯流排訊息用戶端共用的常見例外狀況。 

### <a name="declare-the-send-class"></a>宣告 Send 類別

針對下列範例，在您最喜愛的 Java 開發環境中，先為主控台/殼層應用程式建立新的 Maven 專案。 將類別 `SimpleSend` 命名為：     

```java
package com.microsoft.azure.eventhubs.samples.send;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
```

### <a name="construct-connection-string"></a>建構連接字串

使用 ConnectionStringBuilder 類別以建構要傳遞至事件中樞用戶端執行個體的連接字串值。 將預留位置取代為您在建立命名空間和事件中樞時所取得的值：

```java
final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
        .setNamespaceName("Your Event Hubs namespace name")
        .setEventHubName("Your event hub")
        .setSasKeyName("Your policy name")
        .setSasKey("Your primary SAS key");
```

### <a name="send-events"></a>傳送事件

將字串轉換為 UTF-8 位元組編碼，藉以建立單一事件。 然後從連接字串建立新的事件中樞用戶端執行個體，並傳送訊息：   

```java 
String payload = "Message " + Integer.toString(i);
byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
EventData sendEvent = EventData.create(payloadBytes);

final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
ehClient.sendSync(sendEvent);
    
// close the client at the end of your program
ehClient.closeSync();

``` 

## <a name="next-steps"></a>後續步驟

您可以造訪下列連結以深入了解事件中樞︰

* [使用 EventProcessorHost 接收事件](event-hubs-java-get-started-receive-eph.md)
* [事件中樞概觀][Event Hubs overview]
* [建立事件中樞](event-hubs-create.md)
* [事件中樞常見問題集](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[免費帳戶]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

