---
title: 從 Python 應用程式讀取 Azure 事件中樞捕獲的資料 |Microsoft Docs
description: 本文說明如何撰寫 Python 程式碼來捕獲傳送至事件中樞的資料，並從 Azure 儲存體讀取已捕獲的事件資料。
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2019
ms.author: spelluru
ms.openlocfilehash: 43223f7cb9ed254340c99d235d494d1e93583c7f
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293533"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-using-python"></a>在 Azure 儲存體中捕獲事件中樞資料，並使用 Python 加以讀取 
您可以使用 [設定事件中樞]，以便在 Azure 儲存體或 Azure Data Lake Storage 中捕捉傳送至事件中樞的資料。 本文說明如何使用寫入 Python 程式碼，將事件傳送至事件中樞，以及從 Azure blob 儲存體讀取已捕獲的資料。 如需這項功能的詳細資訊，請參閱[事件中樞 Capture 功能總覽](event-hubs-capture-overview.md)。

此範例使用 [Azure Python SDK](https://azure.microsoft.com/develop/python/) 來示範「擷取」功能。 sender.py 程式會以 JSON 格式將模擬的環境遙測傳送至事件中樞。 事件中樞已設定為使用擷取功能，將此資料批次寫入至 Blob 儲存體。 capturereader.py 應用程式會讀取這些 Blob，並為每個裝置建立附加檔案。 此應用程式接著會將資料寫入至 .csv 檔案。

> [!IMPORTANT]
> 本快速入門使用第5版的 Azure 事件中樞 Python SDK。 如需使用舊版 Python SDK 的快速入門，請參閱[這篇文章](event-hubs-capture-python.md)。 如果您使用第1版的 SDK，建議您將程式碼遷移至最新版本。 如需詳細資訊，請參閱[遷移指南](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)。

在本快速入門中，您將： 

> [!div class="checklist"]
> * 在 Azure 入口網站中建立 Azure Blob 儲存體帳戶和容器。
> * 使用 Azure 入口網站建立事件中樞命名空間。
> * 建立已啟用 Capture 功能的事件中樞，並將它連線到您的儲存體帳戶。
> * 使用 Python 指令碼，將資料傳送至您的事件中樞。
> * 使用另一個 Python 指令碼，讀取和處理事件中樞「擷取」的檔案。

## <a name="prerequisites"></a>必要條件

- Python 2.7 和3.5 或更新版本，並已安裝並更新 `pip`。
- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。
- [在命名空間中建立事件中樞命名空間和事件中樞](event-hubs-create.md)。 記下事件中樞命名空間的名稱、事件中樞的名稱，以及命名空間的主要存取金鑰。 遵循下列文章中的指示取得存取金鑰：[取得連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 預設的索引鍵名稱為： **RootManageSharedAccessKey**。 您不需要教學課程的連接字串。 您只需要主要金鑰。 
- 請遵循下列步驟來建立**Azure 儲存體帳戶**和**blob 容器**：
    1. [建立 Azure 儲存體帳戶](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)。
    2. [在儲存體中建立 blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)。 
    3. [取得儲存體帳戶的連接字串](../storage/common/storage-configure-connection-string.md#view-and-copy-a-connection-string)。

        記下**連接字串**和**容器名稱**。 您稍後會在程式碼中使用它們。 
- 依照下列的指示，啟用事件中樞的**Capture**功能：[使用 Azure 入口網站啟用事件中樞 Capture](event-hubs-capture-enable-through-portal.md)。 選取您在上一個步驟中建立的儲存體帳戶和 blob 容器。 您也可以在建立事件中樞時啟用此功能。 

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>建立 Python 指令碼以將事件傳送到事件中樞
在本節中，您會建立 Python 腳本，以將200事件（10個裝置 * 20 個事件）傳送到事件中樞。 這些事件是以 JSON 格式傳送的範例環境閱讀。 

1. 開啟您慣用的 Python 編輯器，例如 [Visual Studio 程式碼][Visual Studio Code]。
2. 建立稱為 **sender.py**的指令碼。 
3. 將下列程式碼貼入 sender.py。 如需詳細資訊，請參閱程式碼批註。 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # this scripts simulates production of events for 10 devices
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # create a producer client to produce/publish events to the event hub
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # for each device, produce 20 events 
        event_data_batch = producer.create_batch() # create a batch. you will add events to the batch later. 
        for dev in devices:
            # create a dummy reading
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # convert reading into a JSON string
            event_data_batch.add(EventData(s)) # add event data to the batch
        producer.send_batch(event_data_batch) # send the batch of events to the event hub
    
    # close the producer    
    producer.close()
    ```
4. 取代腳本中的下列值：
    1. 將 `EVENT HUBS NAMESPACE CONNECTION STRING` 取代為事件中樞命名空間的連接字串。
    2. 將 `EVENT HUB NAME` 取代為事件中樞的名稱。 
5. 執行腳本以將事件傳送至事件中樞。 
6. 在 Azure 入口網站中，您可以確認事件中樞已收到訊息。 切換至 [**計量**] 區段中的 [**訊息**]。 重新整理頁面以更新圖表。 可能需要幾秒鐘的時間，才會顯示已收到訊息。 

    [![確認事件中樞已收到訊息](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>建立 Python 指令碼來讀取擷取檔案
在此範例中，已捕捉的資料會儲存在 Azure Blob 儲存體中。 本節中的腳本會從您的 Azure 儲存體讀取 capture 資料檔，並產生 CSV 檔案，讓您輕鬆地開啟和查看內容。 您會在應用程式目前的工作目錄中看到10個檔案。 這些檔案將包含10部裝置的環境讀數。 

1. 在您的 Python 編輯器中，建立名為**capturereader.py**的腳本。 此指令碼會讀取擷取檔案，並為每個裝置建立檔案以便只寫入該裝置的資料。
2. 將下列程式碼貼入 capturereader.py。 如需詳細資訊，請參閱程式碼批註。 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # create a blob container client
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # list all the blobs in the container
        for blob in blob_list:
            #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # create a blob client for the blob
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # construct a file name based on the blob name
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # open the file to write. create if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # write blob contents into the file
                processBlob2(cleanName) # convert the file into a CSV file
                os.remove(cleanName) # remove the original downloaded file
                # delete the blob from the container after it's read
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
4. 將 `<AZURE STORAGE CONNECTION STRING>` 取代為您 Azure 儲存體帳戶的連接字串。 您在本教學課程中建立的容器名稱為： **capture**。 如果您使用不同的容器名稱，請將 `capture` 取代為儲存體帳戶中的容器名稱。 

## <a name="run-the-scripts"></a>執行指令碼
1. 開啟在其路徑中具有 Python 的命令提示字元，並執行下列命令來安裝 Python 必要條件封裝︰
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. 將目錄變更為儲存了 sender.py 和 capturereader.py 的任何位置，並執行此命令︰
   
   ```
   python sender.py
   ```
   
   此命令會啟動新的 Python 程序來執行傳送器。
3. 等候擷取執行幾分鐘的時間。 然後在原始命令視窗中輸入下列命令︰
   
   ```
   python capturereader.py
   ```

   此擷取處理器會使用本機目錄從儲存體帳戶/容器下載所有 Blob。 它會處理任何非空白的 Blob，並將結果以 .csv 檔案的形式寫入到本機目錄。

## <a name="next-steps"></a>後續步驟
請參閱[這裡](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)的 GitHub 上的 Python 範例。 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
