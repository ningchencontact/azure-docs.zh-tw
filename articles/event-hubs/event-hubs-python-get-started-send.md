---
title: 使用 Python 將事件傳送到 Azure 事件中樞 | Microsoft Docs
description: 開始使用 Python 將事件傳送到事件中樞
services: event-hubs
author: sethmanheim
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/16/2018
ms.author: sethm
ms.openlocfilehash: bb77ed69ae8f2229cbd62afa545cac9f048689e8
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457998"
---
# <a name="send-events-to-event-hubs-using-python"></a>使用 Python 將事件傳送至事件中樞

Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 如需事件中樞的詳細概觀，請參閱[事件中樞概觀](event-hubs-about.md)與[事件中樞功能](event-hubs-features.md)。

本教學課程說明如何將事件從以 Python 撰寫的應用程式傳送到事件中樞。 

> [!NOTE]
> 您可以從 [GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) 下載此快速入門來作為範例，並以您事件中樞的值取代 `EventHubConnectionString` 與 `EventHubName` 字串，然後執行它。 或者，您可以依照此教學課程中的步驟來建立您自己的項目。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要下列必要條件：

- Python 3.4 或更新版本。


## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>建立事件中樞命名空間和事件中樞
第一個步驟是使用 [Azure 入口網站](https://portal.azure.com)來建立「事件中樞」類型的命名空間，然後取得您應用程式與「事件中樞」進行通訊所需的管理認證。 若要建立命名空間與事件中樞，請依照[此文章](event-hubs-create.md)中的程序執行，然後繼續進行此教學課程中的下列步驟。

## <a name="install-python-package"></a>安裝 Python 套件

若要為事件中樞安裝 Python 套件，請開啟在其路徑中有 Python 的命令提示字元，並執行下列命令︰ 

```bash
pip install azure-eventhub
```

## <a name="create-a-python-script-to-send-events"></a>建立用來傳送事件的 Python 指令碼

接下來，建立可將事件傳送到事件中樞的 Python 應用程式：

1. 開啟您慣用的 Python 編輯器，例如 [Visual Studio 程式碼][Visual Studio Code]。
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

## <a name="run-application-to-send-events"></a>執行應用程式以傳送事件

若要執行指令碼，請開啟在其路徑中有 Python 的命令提示字元，並執行下列命令︰

```bash
start python send.py
```

恭喜！ 您現在已將傳送訊息到事件中樞。
 
## <a name="next-steps"></a>後續步驟
在此快速入門中，您已使用 Python 將訊息傳送到事件中樞。 若要了解如何使用 Python 從事件中樞接收事件，請參閱[從事件中樞接收事件 - Python](event-hubs-python-get-started-receive.md)。

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
