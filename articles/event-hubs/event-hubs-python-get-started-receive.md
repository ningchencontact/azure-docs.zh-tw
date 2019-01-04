---
title: 使用 Python 來接收事件 - Azure 事件中樞 | Microsoft Docs
description: 本文將逐步解說如何建立一個會從「Azure 事件中樞」接收事件的 Python 應用程式。
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 11/26/2018
ms.author: shvija
ms.openlocfilehash: bc1cf07c5a74bc4d7182eea5281e75525fd04247
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103179"
---
# <a name="receive-events-from-event-hubs-using-python"></a>使用 Python 從事件中樞接收事件

Azure 事件中樞是可高度擴充的事件管理系統，每秒可以處理數以百萬計的事件，讓應用程式能處理及分析已連線裝置和其他系統所產生的大量資料。 收集到事件中樞之後，您就可以使用處理序內處理常式來接收及處理事件，或將事件轉送到期他分析系統。

若要深入了解事件中樞，請參閱[事件中樞概觀][Event Hubs overview]。

本教學課程說明如何透過以 Python 撰寫的應用程式，從事件中樞接收事件。 若要傳送事件，請參閱[相對應的傳送文章](event-hubs-python-get-started-send.md)。

此教學課程中的程式碼取自[這些 GitHub 範例](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) \(英文\)， 您可以檢查它們以查看完整的可運作應用程式，包括重要陳述式與變數宣告。 相同的 GitHub 資料夾中有其他範例可供使用。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要下列必要條件：

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。
- Python 3.4 或更新版本。
- 現有的事件中樞命名空間和事件中樞。 請依照[此文章](event-hubs-create.md)中的指示來建立這些實體。 

## <a name="install-python-package"></a>安裝 Python 套件

若要為事件中樞安裝 Python 套件，請開啟在其路徑中有 Python 的命令提示字元，並執行下列命令︰ 

```bash
pip install azure-eventhub
```

## <a name="create-a-python-script-to-receive-events"></a>建立用來接收事件的 Python 指令碼

接下來，建立可從事件中樞接收事件的 Python 應用程式：

1. 開啟您慣用的 Python 編輯器，例如 [Visual Studio 程式碼][Visual Studio Code]。
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

## <a name="receive-events"></a>接收事件

若要執行指令碼，請開啟在其路徑中有 Python 的命令提示字元，並執行下列命令︰

```bash
start python recv.py
```
 
## <a name="next-steps"></a>後續步驟
在此快速入門中，您已建立可從事件中樞接收訊息的 Python 應用程式。 若要了解如何使用 Python 將事件傳送到事件中樞，請參閱[從事件中樞傳送事件 - Python](event-hubs-python-get-started-send.md)。

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
