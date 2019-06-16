---
title: 傳送，並使用 Node.js-Azure 事件中樞接收事件 |Microsoft Docs
description: 本文將逐步解說如何建立一個會從「Azure 事件中樞」傳送事件的 Node.js 應用程式。
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: e67be59e0ed78b2080986acb73a33fc87599c9d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65539337"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>傳送事件，或從使用 Node.js 的 Azure 事件中樞接收事件

Azure 事件中樞是巨量資料串流平台和事件擷取服務，每秒可接收和處理數百萬個事件。 事件中樞可以處理及儲存分散式軟體和裝置所產生的事件、資料或遙測。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 如需事件中樞的詳細概觀，請參閱[事件中樞概觀](event-hubs-about.md)和[事件中樞功能](event-hubs-features.md)。

本教學課程說明如何建立 Node.js 應用程式傳送事件至或接收來自事件中樞的事件。

> [!NOTE]
> 您可以從 [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client) 下載此快速入門來作為範例，並以您事件中樞的值取代 `EventHubConnectionString` 和 `EventHubName` 字串，然後執行。 或者，您可以遵循本教學課程中的步驟，來建立自己的解決方案。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要下列必要條件：

- 使用中的 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 。
- Node.js 8.x 版和更新版本。 從 [https://nodejs.org](https://nodejs.org) 下載最新的 LTS 版本。
- Visual Studio Code (建議採用) 或任何其他的 IDE
- **建立事件中樞命名空間和事件中樞**。 第一個步驟是使用 [Azure 入口網站](https://portal.azure.com)來建立「事件中樞」類型的命名空間，然後取得您應用程式與「事件中樞」進行通訊所需的管理認證。 若要建立命名空間和「事件中樞」，請依照[這篇文章](event-hubs-create.md)中的程序操作，然後繼續進行此教學課程中的下列步驟。 然後，取得事件中樞命名空間指示從文件中的連接字串：[取得連接字串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 您稍後會在本教學課程中使用連接字串。


### <a name="install-npm-package"></a>安裝 npm 套件
若要安裝[事件中樞的 npm 套件](https://www.npmjs.com/package/@azure/event-hubs)，開啟命令提示字元具有`npm`在其路徑中，將目錄變更至您要有您的範例，然後再執行此命令的資料夾

```shell
npm install @azure/event-hubs
```

若要安裝[Event Processor Host 的 npm 套件](https://www.npmjs.com/package/@azure/event-processor-host)，請執行下列命令改為

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>傳送事件

本節將說明如何建立 Node.js 應用程式將事件傳送到事件中樞。 

1. 開啟您最愛的編輯器，例如[Visual Studio Code](https://code.visualstudio.com)。 
2. 建立檔案，稱為`send.js`並貼上下列程式碼到其中。
    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

    // Define connection string and the name of the Event Hub
    const connectionString = "";
    const eventHubsName = "";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. 在上述程式碼中輸入的連接字串和事件中樞的名稱
4. 然後執行命令`node send.js`在命令提示字元執行此檔案中。 這會將 100 個事件傳送至事件中樞

恭喜！ 您現在已傳送事件至事件中樞。


## <a name="receive-events"></a>接收事件

本節將說明如何建立 Node.js 應用程式接收來自事件中樞內的預設取用者群組的單一資料分割的事件。 

1. 開啟您最愛的編輯器，例如[Visual Studio Code](https://code.visualstudio.com)。 
2. 建立檔案，稱為`receive.js`並貼上下列程式碼到其中。
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

    // Define connection string and related Event Hubs entity name here
    const connectionString = "";
    const eventHubsName = "";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. 在上述程式碼中，輸入連接字串和事件中樞的名稱。
4. 然後執行命令`node receive.js`在命令提示字元執行此檔案中。 這會從事件中樞內的預設取用者群組的資料分割的其中一個接收事件

恭喜！ 您現在已從事件中樞收到事件。

## <a name="receive-events-using-event-processor-host"></a>使用事件處理器主機接收事件

本節說明如何從事件中樞接收事件，藉由使用 Azure [EventProcessorHost](event-hubs-event-processor-host.md) Node.js 應用程式中。 EventProcessorHost (EPH) 可藉由在事件中樞取用者群組中的所有分割區上建立接收者，協助您有效率地從事件中樞接收事件。 它會在 Azure 儲存體 Blob 中，定期針對所接收訊息的中繼資料進行檢查點檢查。 此方法可在稍後輕鬆地從您離開的地方繼續接收訊息。

1. 開啟您最愛的編輯器，例如[Visual Studio Code](https://code.visualstudio.com)。 
2. 建立檔案，稱為`receiveAll.js`並貼上下列程式碼到其中。
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Define connection string and related Event Hubs entity name here
    const eventHubConnectionString = "";
    const eventHubName = "";
    const storageConnectionString = "";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. 在上述的程式碼，以及連接字串中輸入的連接字串和事件中樞的名稱，Azure Blob 儲存體
4. 然後執行命令`node receiveAll.js`在命令提示字元執行此檔案中。

恭喜！ 您現在已使用事件處理器主機從事件中樞收到事件。 這會接收來自事件中樞內的預設取用者群組的所有資料分割的事件

## <a name="next-steps"></a>後續步驟
請閱讀下列文章：

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [功能與 Azure 事件中樞中的術語](event-hubs-features.md)
- [事件中樞常見問題集](event-hubs-faq.md)
- 請參閱適用於其他 Node.js 範例[事件中樞](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples)並[Event Processor Host](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) GitHub 上
