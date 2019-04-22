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
ms.openlocfilehash: f03bfde8f7ea37989756ad47678369e94b831438
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59677896"
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
- 複製品[範例的 GitHub 存放庫](https://github.com/Azure/azure-event-hubs-node)您的電腦上。 


## <a name="send-events"></a>傳送事件
本節將說明如何建立 Node.js 應用程式將事件傳送到事件中樞。 

### <a name="install-nodejs-package"></a>安裝 Node.js 套件
在您的電腦上，安裝適用於 Azure 事件中樞的 Node.js 套件。 

```shell
npm install @azure/event-hubs
```

如果您還沒有複製 Git 儲存機制，如必要條件中所述，下載[範例](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples)從 GitHub。 

您已複製的 SDK 包含多個範例，其會示範如何使用 Node.js 將事件傳送到事件中樞。 在此快速入門中，您會使用 **simpleSender.js** 範例。 若要觀察所接收的事件，請開啟另一個終端機，並使用[接收範例](event-hubs-node-get-started-receive.md)來接收事件。

1. 在 Visual Studio Code 上開啟專案。 
2. 在 [用戶端] 資料夾下方建立名為 **.env** 的檔案。 從根資料夾的 **sample.env**，複製並貼上範例環境變數。
3. 設定您的事件中樞連接字串、事件中樞名稱和儲存體端點。 如需有關取得事件中樞之連接字串的指示，請參閱[取得連接字串](event-hubs-create.md#create-an-event-hubs-namespace)。
4. 在 Azure CLI 上，瀏覽至 [用戶端] 資料夾路徑。 執行下列命令來安裝節點套件並建置專案：

    ```shell
    npm i
    npm run build
    ```
5. 執行下列命令以開始傳送事件： 

    ```shell
    node dist/examples/simpleSender.js
    ```


### <a name="review-the-sample-code"></a>檢閱範例程式碼 
檢閱 simpleSender.js 檔案中的範例程式碼，以傳送事件到事件中樞。

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
const lib_1 = require("../lib");
const dotenv = require("dotenv");
dotenv.config();
const connectionString = "EVENTHUB_CONNECTION_STRING";
const entityPath = "EVENTHUB_NAME";
const str = process.env[connectionString] || "";
const path = process.env[entityPath] || "";

async function main() {
    const client = lib_1.EventHubClient.createFromConnectionString(str, path);
    const data = {
        body: "Hello World!!"
    };
    const delivery = await client.send(data);
    console.log(">>> Sent the message successfully: ", delivery.tag.toString());
    console.log(delivery);
    console.log("Calling rhea-promise sender close directly. This should result in sender getting reconnected.");
    await Object.values(client._context.senders)[0]._sender.close();
    // await client.close();
}

main().catch((err) => {
    console.log("error: ", err);
});

```

請記得先設定您的環境變數，然後再執行指令碼。 您可以在命令列中設定它們 (如下列範例所示)，或使用 [dotenv 套件](https://www.npmjs.com/package/dotenv#dotenv) \(英文\)。 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="receive-events"></a>接收事件
此教學課程示範如何在 Node.js 應用程式中，使用 Azure [EventProcessorHost](event-hubs-event-processor-host.md) 從事件中樞接收事件。 EventProcessorHost (EPH) 可藉由在事件中樞取用者群組中的所有分割區上建立接收者，協助您有效率地從事件中樞接收事件。 它會在 Azure 儲存體 Blob 中，定期針對所接收訊息的中繼資料進行檢查點檢查。 此方法可在稍後輕鬆地從您離開的地方繼續接收訊息。

適用於此快速入門的程式碼可在 [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor) 上取得。

### <a name="clone-the-git-repository"></a>複製 Git 存放庫
從 GitHub 下載或複製[範例](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/)。 

### <a name="install-the-eventprocessorhost"></a>安裝 EventProcessorHost
安裝適用於事件中樞模組的 EventProcessorHost。 

```shell
npm install @azure/event-processor-host
```

### <a name="receive-events-using-eventprocessorhost"></a>使用 EventProcessorHost 接收事件
您已複製的 SDK 包含多個範例，其會示範如何使用 Node.js 從事件中樞接收事件。 在此快速入門中，您會使用 **singleEPH.js** 範例。 若要觀察所接收的事件，請開啟另一個終端機，並使用[傳送範例](event-hubs-node-get-started-send.md)來傳送事件。

1. 在 Visual Studio Code 上開啟專案。 
2. 在 [處理器] 資料夾下方建立名為 **.env** 的檔案。 從根資料夾的 **sample.env**，複製並貼上範例環境變數。
3. 設定您的事件中樞連接字串、事件中樞名稱和儲存體端點。 您可以在 Azure 入口網站的 [事件中樞] 頁面上，從 **RootManageSharedAccessKey** 下方的 **Connection string-primary** 索引鍵中，複製事件中樞的連接字串。 如需詳細步驟，請參閱[取得連接字串](event-hubs-create.md#create-an-event-hubs-namespace)。
4. 在 Azure CLI 上，瀏覽至 [處理器] 資料夾路徑。 執行下列命令來安裝節點套件並建置專案：

    ```shell
    npm i
    npm run build
    ```
5. 藉由執行下列命令，使用您的事件處理器主機來接收事件：

    ```shell
    node dist/examples/singleEph.js
    ```

### <a name="review-the-sample-code"></a>檢閱範例程式碼 
以下是使用 Node.js 從事件中樞接收事件的範例程式碼。 您可以手動建立 sampleEph.js 檔案，並執行它以從事件中樞接收事件。 

  ```javascript
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processor Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path,
        onEphError: (error) => {
          console.log("This handler will notify you of any internal errors that happen " +
          "during partition and lease management: %O", error);
        }
      }
    );
    let count = 0;
    // Message event handler
    const onMessage = async (context/*PartitionContext*/, data /*EventData*/) => {
      console.log(">>>>> Rx message from '%s': '%s'", context.partitionId, data.body);
      count++;
      // let us checkpoint every 100th message that is received across all the partitions.
      if (count % 100 === 0) {
        return await context.checkpoint();
      }
    };
    // Error event handler
    const onError = (error) => {
      console.log(">>>>> Received Error: %O", error);
    };
    // start the EPH
    await eph.start(onMessage, onError);
    // After some time let' say 2 minutes
    await delay(120000);
    // This will stop the EPH.
    await eph.stop();
  }
  
  main().catch((err) => {
    console.log(err);
  });
      
  ```

請記得先設定您的環境變數，然後再執行指令碼。 您可以在命令列中設定此項 (如下列範例所示)，或使用 [dotenv 套件](https://www.npmjs.com/package/dotenv#dotenv) \(英文\)。 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

您可以在[這裡](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples)找到更多範例。


## <a name="next-steps"></a>後續步驟
請閱讀下列文章：

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [功能與 Azure 事件中樞中的術語](event-hubs-features.md)
- [事件中樞常見問題集](event-hubs-faq.md)
- 在 [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/) 上查看事件中樞的其他 Node.js 範例。
