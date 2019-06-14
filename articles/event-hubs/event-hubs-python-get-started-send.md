---
title: 傳送，或使用 Python-Azure 事件中樞接收事件 |Microsoft Docs
description: 這篇文章會逐步解說建立 Python 應用程式，以將事件傳送至 Azure 事件中樞。
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 6a8f0ddcfe6de904219059c6e761ead4c004732d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60461445"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>傳送事件至或使用 Python 從事件中樞接收事件

Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 如需事件中樞的詳細概觀，請參閱[事件中樞概觀](event-hubs-about.md)和[事件中樞功能](event-hubs-features.md)。

本教學課程說明如何建立 Python 應用程式傳送事件至或接收來自事件中樞的事件。 

> [!NOTE]
> 您可以從 [GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) 下載此快速入門來作為範例，並以您事件中樞的值取代 `EventHubConnectionString` 和 `EventHubName` 字串，然後執行。 或者，您可以遵循本教學課程中的步驟，來建立自己的解決方案。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要下列必要條件：

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。
- Python 3.4 或更新版本。
- 使用[Azure 入口網站](https://portal.azure.com)建立類型為事件中樞的命名空間，並取得您的應用程式與事件中樞進行通訊所需的管理認證。 若要建立命名空間和事件中樞，請依照[這篇文章](event-hubs-create.md)中的程序操作。 然後，依照文件中的指示取得事件中樞的存取金鑰的值：[取得連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 您可以在您於本教學課程稍後撰寫的程式碼中，使用此存取金鑰。 預設的金鑰名稱是：**RootManageSharedAccessKey**。

## <a name="install-python-package"></a>安裝 Python 套件

若要為事件中樞安裝 Python 套件，請開啟在其路徑中有 Python 的命令提示字元，並執行下列命令︰ 

```bash
pip install azure-eventhub
```

## <a name="send-events"></a>傳送事件

### <a name="create-a-python-script-to-send-events"></a>建立用來傳送事件的 Python 指令碼

接下來，建立可將事件傳送到事件中樞的 Python 應用程式：

1. 開啟您慣用的 Python 編輯器，例如[Visual Studio Code](https://code.visualstudio.com/)
2. 建立名為 **send.py** 的指令碼。 此指令碼會將 100 個事件傳送到事件中樞。
3. 將下列程式碼貼到 send.py 中，並將 ADDRESS、USER 和 KEY 等值取代為您在上一節中從 Azure 入口網站取得的值： 

```python
import sys
import logging
import datetime
import time
import os

from azure.eventhub import EventHubClient, Sender, EventData

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"

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
            sender.send(EventData(str(i)))
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

### <a name="run-application-to-send-events"></a>執行應用程式以傳送事件

若要執行指令碼，請開啟在其路徑中有 Python 的命令提示字元，並執行下列命令︰

```bash
start python send.py
```

恭喜！ 您現在已將傳送訊息到事件中樞。

## <a name="receive-events"></a>接收事件

### <a name="create-a-python-script-to-receive-events"></a>建立用來接收事件的 Python 指令碼

接下來，建立可從事件中樞接收事件的 Python 應用程式：

1. 開啟您慣用的 Python 編輯器，例如[Visual Studio Code](https://code.visualstudio.com/)
2. 建立名為 **recv.py** 的指令碼。
3. 將下列程式碼貼到 recv.py 中，並將 ADDRESS、USER 和 KEY 等值取代為您在上一節中從 Azure 入口網站取得的值： 

```python
import os
import sys
import logging
import time
from azure.eventhub import EventHubClient, Receiver, Offset

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"
CONSUMER_GROUP = "$default"
OFFSET = Offset("-1")
PARTITION = "0"

total = 0
last_sn = -1
last_offset = "-1"
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
try:
    receiver = client.add_receiver(CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
    client.run()
    start_time = time.time()
    for event_data in receiver.receive(timeout=100):
        last_offset = event_data.offset
        last_sn = event_data.sequence_number
        print("Received: {}, {}".format(last_offset, last_sn))
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

### <a name="receive-events"></a>接收事件

若要執行指令碼，請開啟在其路徑中有 Python 的命令提示字元，並執行下列命令︰

```bash
start python recv.py
```
 
## <a name="next-steps"></a>後續步驟
請閱讀下列文章：

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [功能與 Azure 事件中樞中的術語](event-hubs-features.md)
- [事件中樞常見問題集](event-hubs-faq.md)

