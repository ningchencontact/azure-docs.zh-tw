---
title: 使用 Python 傳送和接收事件-Azure 事件中樞
description: 本逐步解說會示範如何建立及執行 Python 腳本，以將事件傳送至 Azure 事件中樞或從中接收事件。
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/11/2019
ms.author: shvija
ms.openlocfilehash: 330a7f5dc325c707b5be7ce9f9b3242a1d4c9547
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428893"
---
# <a name="send-and-receive-events-with-event-hubs-using-python"></a>使用 Python 以事件中樞傳送和接收事件

Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理和儲存來自分散式軟體和裝置的事件、資料或遙測。 傳送至事件中樞的資料，可以透過任何即時分析提供者或批次/儲存體配接器加以轉換及儲存。 如需事件中樞的詳細資訊，請參閱 Azure 事件中樞中的[Azure 事件中樞](event-hubs-about.md)和[功能與術語](event-hubs-features.md)。

本快速入門示範如何建立 Python 應用程式，以將事件傳送至事件中樞並從中接收事件。 

> [!NOTE]
> 您可以從 GitHub 下載並執行[範例應用程式](https://github.com/Azure/azure-event-hubs-python/tree/master/examples)，而不是使用快速入門。 使用您的事件中樞值來取代 `EventHubConnectionString` 和 @no__t 1 字串。 

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您必須符合下列必要條件：

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。
- 作用中的事件中樞命名空間和事件中樞，遵循[快速入門：使用 Azure 入口網站建立事件中樞](event-hubs-create.md)中的指示建立。 記下命名空間和事件中樞名稱，以便稍後在本逐步解說中使用。 
- 事件中樞命名空間的共用存取金鑰名稱和主要金鑰值。 遵循[取得連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)中的指示，取得存取金鑰名稱和值。 預設的存取金鑰名稱是**RootManageSharedAccessKey**。 複製 [金鑰名稱] 和 [主要金鑰] 值，以供稍後在本逐步解說中使用。 
- 已安裝並更新 @no__t 0 的 Python 3.4 或更新版本。
- 事件中樞的 Python 套件。 若要安裝套件，請在其路徑中有 Python 的命令提示字元中執行此命令： 
  
  ```cmd
  pip install azure-eventhub
  ```
  
  > [!NOTE]
  > 本快速入門中的程式碼會使用事件中樞 SDK 的目前穩定版本1.3.1。 如需使用 SDK 預覽版本的範例程式碼，請參閱[https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples)。

## <a name="send-events"></a>傳送事件

若要建立可將事件傳送至事件中樞的 Python 應用程式：

1. 開啟您最愛的 Python 編輯器，例如[Visual Studio Code](https://code.visualstudio.com/)
2. 建立名為*send.py*的新檔案。 此指令碼會將 100 個事件傳送到事件中樞。
3. 將下列程式碼貼入*send.py*，以您的值取代事件中樞 @no__t 1namespace >、\<eventhub >、@no__t 3AccessKeyName > 和 @no__t 4primary 金鑰值 >： 
   
   ```python
   import sys
   import logging
   import datetime
   import time
   import os
   
   from azure.eventhub import EventHubClient, Sender, EventData
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<namespace>.servicebus.windows.net/eventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   try:
       if not ADDRESS:
           raise ValueError("No EventHubs URL supplied.")
   
       # Create Event Hubs client
       client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
       sender = client.add_sender(partition="0")
       client.run()
       try:
           start_time = time.time()
           for i in range(100):
               print("Sending message: {}".format(i))
               message = "Message {}".format(i)
               sender.send(EventData(message))
       except:
           raise
       finally:
           end_time = time.time()
           client.stop()
           run_time = end_time - start_time
           logger.info("Runtime: {} seconds".format(run_time))
   
   except KeyboardInterrupt:
       pass
   ```
   
4. 儲存檔案。 

若要執行腳本，請從您儲存*send.py*的目錄執行此命令：

```cmd
start python send.py
```

恭喜！ 您現在已將傳送訊息到事件中樞。

## <a name="receive-events"></a>接收事件

若要建立從事件中樞接收事件的 Python 應用程式：

1. 在您的 Python 編輯器中，建立名為*recv.py*的檔案。
2. 將下列程式碼貼入*recv.py*，以您的值取代事件中樞 @no__t 1namespace >、\<eventhub >、@no__t 3AccessKeyName > 和 @no__t 4primary 金鑰值 >： 
   
   ```python
   import os
   import sys
   import logging
   import time
   from azure.eventhub import EventHubClient, Receiver, Offset
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   
   CONSUMER_GROUP = "$default"
   OFFSET = Offset("-1")
   PARTITION = "0"
   
   total = 0
   last_sn = -1
   last_offset = "-1"
   client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
   try:
       receiver = client.add_receiver(
           CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
       client.run()
       start_time = time.time()
       for event_data in receiver.receive(timeout=100):
           print("Received: {}".format(event_data.body_as_str(encoding='UTF-8')))
           total += 1
   
       end_time = time.time()
       client.stop()
       run_time = end_time - start_time
       print("Received {} messages in {} seconds".format(total, run_time))
   
   except KeyboardInterrupt:
       pass
   finally:
       client.stop()
   ```
   
4. 儲存檔案。

若要執行腳本，請從您儲存*recv.py*的目錄執行此命令：

```cmd
start python recv.py
```

## <a name="next-steps"></a>後續步驟
如需事件中樞的詳細資訊，請參閱下列文章：

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure 事件中樞的功能與術語](event-hubs-features.md)
- [事件中樞常見問題集](event-hubs-faq.md)

