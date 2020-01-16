---
title: 使用 Node.js 來傳送和接收事件 - Azure 事件中樞
description: 本文將逐步解說如何建立一個會從「Azure 事件中樞」傳送事件的 Node.js 應用程式。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/09/2020
ms.author: spelluru
ms.openlocfilehash: d4810c325acc42d5aa665002654cb01154cdc6bb
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981623"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>使用 Node.js 將事件傳送至 Azure 事件中樞或從中接收事件

Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體及裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料，可以透過任何即時分析提供者或批次/儲存體配接器加以轉換及儲存。 如需事件中樞的詳細概觀，請參閱[事件中樞概觀](event-hubs-about.md)和[事件中樞功能](event-hubs-features.md)。

本教學課程說明如何建立 Node.js 應用程式，以將事件傳送至事件中樞或從中接收事件。

> [!IMPORTANT]
> 本快速入門使用第5版的 Azure 事件中樞 JAVA 腳本 SDK。 如需使用舊版 JAVA 腳本 SDK 的快速入門，請參閱[這篇文章](event-hubs-node-get-started-send.md)。 如果您使用第2版的 SDK，建議您將程式碼遷移至最新版本。 如需詳細資訊，請參閱[遷移指南](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要下列必要條件：

- 使用中的 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Node.js 8.x 版和更新版本。 從 [https://nodejs.org](https://nodejs.org) 下載最新的 LTS 版本。
- Visual Studio Code (建議採用) 或任何其他的 IDE
- **建立事件中樞命名空間和事件中樞**。 第一個步驟是使用 [Azure 入口網站](https://portal.azure.com)來建立「事件中樞」類型的命名空間，然後取得您應用程式與「事件中樞」進行通訊所需的管理認證。 若要建立命名空間和「事件中樞」，請依照[這篇文章](event-hubs-create.md)中的程序操作，然後繼續進行此教學課程中的下列步驟。 然後，依照下列文章中的指示，取得事件中樞命名空間的連接字串：[取得連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 您稍後會在本教學課程中使用連接字串。


### <a name="install-npm-packages"></a>安裝 npm 套件
若要安裝[事件中樞的 npm 套件](https://www.npmjs.com/package/@azure/event-hubs)，請開啟在路徑中有 `npm` 的命令提示字元，並將目錄切換至您要用來存放範例的資料夾，然後執行下列命令

```shell
npm install @azure/event-hubs
```

對於接收端，您必須安裝兩個以上的套件。 在本快速入門中，您將使用 Azure Blob 儲存體保存檢查點，讓程式不會讀取已讀取的事件。 它會在 blob 中定期檢查已接收訊息的中繼資料。 此方法可在稍後輕鬆地從您離開的地方繼續接收訊息。

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>傳送事件

本節說明如何建立可將事件傳送至事件中樞的 Node.js 應用程式。

1. 開啟您慣用的編輯器，例如 [Visual Studio Code](https://code.visualstudio.com)。
2. 建立名為 `send.js` 的檔案，並在其中貼上下列程式碼。

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // create a producer client to send messages to the event hub
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // prepare a batch of three events
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // send the batch to the event hub
      await producer.sendBatch(batch);

      // close the producer client
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 別忘了取代**連接字串**，以及程式碼中的**事件中樞名稱**值。
5. 執行命令 `node send.js` 以執行此檔案。 這會將三個事件的批次傳送至您的事件中樞
6. 在 Azure 入口網站中，您可以確認事件中樞已收到訊息。 切換至 [**計量**] 區段中的 [**訊息**]。 重新整理頁面以更新圖表。 可能需要幾秒鐘的時間，才會顯示已收到訊息。

    [![確認事件中樞已收到訊息](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > 如需完整的原始程式碼，並提供更多資訊批註，請參閱[GitHub 上的這個](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js)檔案

恭喜！ 您現在已將事件傳送至事件中樞。


## <a name="receive-events"></a>接收事件
本節說明如何使用 node.js 應用程式中的 Azure Blob 檢查點存放區，從事件中樞接收事件。 它會在 Azure 儲存體 Blob 中，定期針對所接收訊息的中繼資料進行檢查點檢查。 此方法可在稍後輕鬆地從您離開的地方繼續接收訊息。

### <a name="create-an-azure-storage-and-a-blob-container"></a>建立 Azure 儲存體和 blob 容器
請遵循下列步驟來建立 Azure 儲存體帳戶中的 blob 容器。

1. [建立 Azure 儲存體帳戶](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [建立 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [取得儲存體帳戶的連接字串](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    記下連接字串和容器名稱。 您會在接收程式碼中使用它們。

### <a name="write-code-to-receive-events"></a>撰寫程式碼以接收事件

1. 開啟您慣用的編輯器，例如 [Visual Studio Code](https://code.visualstudio.com)。
2. 建立名為 `receive.js` 的檔案，並在其中貼上下列程式碼。 如需詳細資訊，請參閱程式碼批註。
    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // create a blob container client and a blob checkpoint store using the client
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // create a consumer client for the event hub by specifying the checkpoint store
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // subscribe for the events and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // update the checkpoint
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // after 30 seconds, stop processing
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
3. 別忘了在程式碼中指定**下列值**：
    - 事件中樞命名空間的連接字串
    - 事件中樞的名稱
    - Azure 儲存體帳戶的連接字串
    - Blob 容器的名稱
5. 然後，在命令提示字元中執行命令 `node receive.js`，以執行此檔案。 您應該會在視窗中看到 [收到的事件] 的相關訊息。

    > [!NOTE]
    > 如需完整的原始程式碼，並提供更多資訊批註，請參閱[GitHub 上的這個](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js)檔案。

恭喜！ 您現在已從事件中樞接收事件。 接收者程式會從事件中樞內的預設取用者群組的所有分割區接收事件

## <a name="next-steps"></a>後續步驟
請參閱 GitHub 上的這些範例：

- [JavaScript 範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript) \(英文\)
- [TypeScript 範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
