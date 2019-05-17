---
title: 如何搭配 Python 使用 Azure 服務匯流排佇列 | Microsoft Docs
description: 了解如何從 Python 使用 Azure 服務匯流排佇列。
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: f2605ee5688a86de0a8e7d036aa63edd604c6538
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787190"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>如何將服務匯流排佇列搭配 Python 使用

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

在本教學課程中，您將了解如何建立 Python 應用程式，來傳送和接收來自服務匯流排佇列的訊息。 

## <a name="prerequisites"></a>必要條件
1. Azure 訂用帳戶。 若要完成此教學課程，您需要 Azure 帳戶。 您可以啟用您[MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或是註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
2. 中的後續步驟[使用 Azure 入口網站來建立服務匯流排佇列](service-bus-quickstart-portal.md)文章。
    1. 閱讀快速**概觀**的服務匯流排**佇列**。 
    2. 建立服務匯流排**命名空間**。 
    3. 取得**連接字串**。 

        > [!NOTE]
        > 您將建立**佇列**在本教學課程中使用 Python 的服務匯流排命名空間中。 
1. 安裝 Python 或[Python Azure 服務匯流排套件][Python Azure Service Bus package]，請參閱[Python 安裝指南](../python-how-to-install.md)。 請參閱完整的文件的服務匯流排 Python SDK[此處](/python/api/overview/azure/servicebus?view=azure-python)。

## <a name="create-a-queue"></a>建立佇列
可以通过 **ServiceBusClient** 对象处理队列。 將下列程式碼新增至您想要在其中以程式設計方式存取服務匯流排之任何 Python 檔案內的頂端附近：

```python
from azure.servicebus import ServiceBusClient
```

以下代码创建 **ServiceBusClient** 对象。 請使用您的命名空間、共用存取簽章 (SAS) 金鑰名稱和值來取代 `mynamespace`、`sharedaccesskeyname` 和 `sharedaccesskey`。

```python
sb_client = ServiceBusClient.from_connection_string('<CONNECTION STRING>')
```

SAS 金鑰名稱和值的值可以在 [Azure 入口網站][Azure portal]連線資訊中找到，或在 Visual Studio 的 [伺服器總管] 中選取「服務匯流排」命名空間時於 [屬性] 窗格中找到 (如上一節所示)。

```python
sb_client.create_queue("taskqueue")
```

`create_queue` 方法也支援其他選項，而可讓您覆寫訊息存留時間 (TTL) 或佇列大小上限等預設佇列設定。 下列範例會將佇列大小上限設為 5GB，並將 TTL 時間設為 1 分鐘：

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120, default_message_time_to_live=datetime.timedelta(minutes=1))
```

如需詳細資訊，請參閱 < [Azure 服務匯流排 Python 文件](/python/api/overview/azure/servicebus?view=azure-python)。

## <a name="send-messages-to-a-queue"></a>傳送訊息至佇列
要将消息发送到服务总线队列，应用程序需对 `ServiceBusClient` 对象调用 `send` 方法。

下列範例示範如何使用 `send_queue_message` 將測試訊息傳送至名為 `taskqueue` 的佇列：

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient 
queue_client = QueueClient.from_connection_string("<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

服務匯流排佇列支援的訊息大小上限：在[標準層](service-bus-premium-messaging.md)中為 256 KB 以及在[進階層](service-bus-premium-messaging.md)中為 1 MB。 標頭 (包含標準和自訂應用程式屬性) 可以容納 64 KB 的大小上限。 佇列中所保存的訊息數目沒有限制，但佇列所保存的訊息大小總計會有最高限制。 此佇列大小會在建立時定義，上限是 5 GB。 如需有關配額的詳細資訊，請參閱[服務匯流排配額][Service Bus quotas]。

如需詳細資訊，請參閱 < [Azure 服務匯流排 Python 文件](/python/api/overview/azure/servicebus?view=azure-python)。

## <a name="receive-messages-from-a-queue"></a>從佇列接收訊息
对 `ServiceBusService` 对象使用 `get_receiver` 方法可从队列接收消息：

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient 
queue_client = QueueClient.from_connection_string("<CONNECTION STRING>", "<QUEUE NAME>")

## Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

如需詳細資訊，請參閱 < [Azure 服務匯流排 Python 文件](/python/api/overview/azure/servicebus?view=azure-python)。


如果將 `peek_lock` 參數設為 **False**，則當讀取訊息後，訊息便會從佇列中刪除。 您可以將參數 `peek_lock` 設為 **True**，來讀取 (查看) 並鎖定訊息，避免系統從佇列刪除訊息。

隨著接收作業讀取及刪除訊息之行為是最簡單的模型，且最適合可容許在發生失敗時不處理訊息的應用程式案例。 若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。 因為服務匯流排會將訊息標示為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。

如果您將 `peek_lock` 參數設為 **True**，接收會變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。 當服務匯流排收到要求時，它會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回應用程式。 在應用程式完成訊息處理 (或可靠地儲存此訊息以供未來處理) 之後，它會在 **Message** 物件上呼叫 **delete** 方法，以完成接收程序的第二個階段。 **delete** 方法會將訊息標示為已取用，並將其從佇列中移除。

```python
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>如何處理應用程式當機與無法讀取的訊息
服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。 如果接收者應用程式因為某些原因無法處理訊息，它可以在 **Message** 物件上呼叫 **unlock** 方法。 這將導致服務匯流排將佇列中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與在佇列內鎖定之訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

如果應用程式在處理訊息之後，尚未呼叫 **delete** 方法時當機，則會在應用程式重新啟動時將訊息重新傳遞給該應用程式。 這通常稱為**至少一次處理**，也就是每個訊息會被處理一次，但在某些情況下可能會重新傳遞相同訊息。 如果案例無法容許重複處理，則應用程式開發人員應在其應用程式中加入其他邏輯，以處理重複的訊息傳遞。 通常您可使用訊息的 **MessageId** 屬性來達到此目的，該屬性將在各個傳遞嘗試中會保持不變。

## <a name="next-steps"></a>後續步驟
既然您已經了解「服務匯流排」佇列的基本概念，您現在可以閱讀下列文章來深入了解。

* [佇列、主題和訂用帳戶][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

