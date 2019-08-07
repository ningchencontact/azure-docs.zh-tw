---
title: 使用 Python 來傳送及接收訊息 - Azure 事件中樞 | Microsoft Docs
description: 了解如何使用 Python 透過「事件中樞」來傳送事件、接收事件及擷取事件資料流。
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2018
ms.author: shvija
ms.openlocfilehash: 0960862da9e65cff4d957e97a7488dc7a245651a
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742128"
---
# <a name="how-to-use-azure-event-hubs-from-a-python-application"></a>如何從 Python 應用程式使用 Azure 事件中樞
Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 如需詳細資訊，請參閱[事件中樞簡介](event-hubs-what-is-event-hubs.md)。 

本文提供文件連結，說明如何從以 **Python** 撰寫的應用程式執行下列工作：

- [將事件傳送到事件中樞](#send-events-to-event-hubs)
- [從事件中樞接收事件](#receive-events-from-event-hubs)
- 從 Azure 儲存體讀取已擷取的事件資料。 

## <a name="prerequisites"></a>先決條件
- 依照下列其中一個快速入門來建立事件中樞：[Azure 入口網站](event-hubs-create.md)、[Azure CLI](event-hubs-quickstart-cli.md)、[Azure PowerShell](event-hubs-quickstart-powershell.md)、[Azure Resource Manager 範本](event-hubs-resource-manager-namespace-event-hub.md)。 
- 在機器上安裝 Python 3.4 或更新版本。

## <a name="install-python-package"></a>安裝 Python 套件

若要為事件中樞安裝 Python 套件，請開啟在其路徑中有 Python 的命令提示字元，並執行下列命令︰ 

```bash
pip install azure-eventhub
```

## <a name="send-events-to-event-hubs"></a>將事件傳送至事件中樞
下列程式碼示範如何從 Python 應用程式將事件傳送到事件中樞： 

1. 建立變數以保存事件中樞的 URL、金鑰名稱及金鑰值。 

    ```python
    # Import classes from Event Hubs python package
    from azure.eventhub import EventHubClient, Receiver, Offset
    
    # Address can be in either of these formats:
    # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
    # "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
    # For example:
    ADDRESS = "amqps://<MyEventHubNamspaceName>.servicebus.windows.net/<MyEventHubName>"
    
    # SAS policy and key are not required if they are encoded in the URL
    USER = "<Name of the access key. Default name: RootManageSharedAccessKey>"
    KEY = "<The access key>"
    ```

2. 建立「事件中樞」用戶端、新增傳送者、執行用戶端、使用傳送者來傳送事件，然後在您完成時停止用戶端。 

    ```python
    # Create an Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    
    # Add a sender to the client
    sender = client.add_sender(partition="0")
    
    # Run the Event Hub client
    client.run()
    
    # Send event to the event hub
    sender.send(EventData("<MyEventData>"))
    
    # Stop the Event Hubs client
    client.stop()
    
    ```

如需有關如何從以 Python 撰寫的應用程式將事件傳送給事件中樞的完整教學課程，請參閱[這篇文章](event-hubs-python-get-started-send.md)。

## <a name="receive-events-from-event-hubs"></a>從事件中樞接收事件
下列程式碼示範如何從 Python 應用程式自事件中樞接收事件： 

```python

# Create an Event Hubs client
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)

# Add a receiver to the client
receiver = client.add_receiver(
    CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)

# Run the Event Hubs client
client.run()

# Receive event data from the event hub
for event_data in receiver.receive(timeout=100):
    last_offset = event_data.offset
    last_sn = event_data.sequence_number
    print("Received: {}, {}".format(last_offset, last_sn))

# Stop the Event Hubs client
client.stop()
```

如需有關如何從以 Python 撰寫的應用程式自事件中樞接收事件的完整教學課程，請參閱[這篇文章](event-hubs-python-get-started-receive.md)

## <a name="read-capture-event-data-from-azure-storage"></a>從 Azure 儲存體讀取已擷取的事件資料
下列程式碼示範如何從 Python 應用程式讀取儲存在 **Azure Blob 儲存體**中的已擷取事件資料：請依照來自以下的指示，為事件中樞啟用 [擷取] 功能：[使用 Azure 入口網站來啟用事件中樞擷取功能](event-hubs-capture-enable-through-portal.md)。 然後，先將一些事件傳送給事件中樞，再測試程式碼。 

```python
import os
import string
import json
import avro.schema
from avro.datafile import DataFileReader, DataFileWriter
from avro.io import DatumReader, DatumWriter
from azure.storage.blob import BlockBlobService

def processBlob(filename):
    reader = DataFileReader(open(filename, 'rb'), DatumReader())
    dict = {}
    for reading in reader:
        parsed_json = json.loads(reading["Body"])
        if not 'id' in parsed_json:
            return
        if not dict.has_key(parsed_json['id']):
            list = []
            dict[parsed_json['id']] = list
        else:
            list = dict[parsed_json['id']]
            list.append(parsed_json)
    reader.close()
    for device in dict.keys():
        deviceFile = open(device + '.csv', "a")
        for r in dict[device]:
            deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')

def startProcessing(accountName, key, container):
    print 'Processor started using path: ' + os.getcwd()
    block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
    generator = block_blob_service.list_blobs(container)
    for blob in generator:
        #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
        if blob.properties.content_length > 508:
            print('Downloaded a non empty blob: ' + blob.name)
            cleanName = string.replace(blob.name, '/', '_')
            block_blob_service.get_blob_to_path(container, blob.name, cleanName)
            processBlob(cleanName)
            os.remove(cleanName)
        block_blob_service.delete_blob(container, blob.name)
startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')
```

如需有關如何從以 Python 撰寫的應用程式讀取 Azure Blob 儲存體中已擷取之「事件中樞」資料的完整教學課程，請參閱[這篇文章](event-hubs-capture-python.md)

## <a name="github-samples"></a>GitHub 範例
您可以在 [azure-event-hubs-python Git 存放庫](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs)中找到更多 Python 範例。

## <a name="next-steps"></a>後續步驟
閱讀＜概念＞一節中的所有文章 (從[事件中樞功能概觀](event-hubs-features.md)開始)。
