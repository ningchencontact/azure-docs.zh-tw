---
title: 使用 Node.js 來接收事件 - Azure 事件中樞 | Microsoft Docs
description: 本文將逐步解說如何建立一個會從「Azure 事件中樞」接收事件的 Node.js 應用程式。
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 6986a5c40b51300b631f3fdce6c3a88b43fb5b11
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2019
ms.locfileid: "55730878"
---
# <a name="receive-events-from-azure-event-hubs-using-nodejs"></a>使用 Node.js 從 Azure 事件中樞接收事件

Azure 事件中樞是可高度擴充的事件管理系統，每秒可以處理數以百萬計的事件，讓應用程式能處理及分析已連線裝置和其他系統所產生的大量資料。 收集到事件中樞之後，您就可以使用處理序內處理常式來接收及處理事件，或將事件轉送到期他分析系統。 您也可以先在 Azure 儲存體或 Azure Data Lake Store 中擷取事件資料，然後再處理它。  

若要深入了解事件中樞，請參閱[事件中樞概觀](event-hubs-about.md)。

此教學課程示範如何在 Node.js 應用程式中，使用 Azure [EventProcessorHost](event-hubs-event-processor-host.md) 從事件中樞接收事件。 EventProcessorHost (EPH) 可藉由在事件中樞取用者群組中的所有分割區上建立接收者，協助您有效率地從事件中樞接收事件。 它會在 Azure 儲存體 Blob 中，定期針對所接收訊息的中繼資料進行檢查點檢查。 此方法可在稍後輕鬆地從您離開的地方繼續接收訊息。

適用於此快速入門的程式碼可在 [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor) 上取得。

> [!NOTE]
>  若要使用 Node.js 將事件傳送到事件中樞，請參閱本文：[使用 Node.js 將事件傳送到 Azure 事件中樞](event-hubs-node-get-started-send.md)。 

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要下列必要條件：

- Node.js 8.x 版和更新版本。 從 [https://nodejs.org](https://nodejs.org) 下載最新的 LTS 版本。請勿使用 Node.js 的較舊 LTS 版本。 
- 使用中的 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶][] 。

## <a name="create-a-namespace-and-event-hub"></a>建立命名空間和事件中樞
第一個步驟是使用 Azure 入口網站來建立事件中樞命名空間和事件中樞。 如果您沒有現有的帳戶，您可以依照[使用 Azure 入口網站來建立事件中樞命名空間和事件中樞](event-hubs-create.md)中的指示來建立這些實體。

## <a name="create-a-storage-account-and-container"></a>建立儲存體帳戶和容器
若要使用 EventProcessorHost，您必須擁有 Azure 儲存體帳戶。 分割區上的租用和事件資料流中的檢查點之類的狀態資訊，都會使用 Azure 儲存體容器在接收者之間共用。 您可以依照[這篇文章](../storage/common/storage-quickstart-create-account.md)中的指示來建立 Azure 儲存體帳戶。

## <a name="clone-the-git-repository"></a>複製 Git 存放庫
從 GitHub 下載或複製[範例](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/)。 

## <a name="install-the-eventprocessorhost"></a>安裝 EventProcessorHost
安裝適用於事件中樞模組的 EventProcessorHost。 

```shell
npm install @azure/event-processor-host
```

## <a name="receive-events-using-eventprocessorhost"></a>使用 EventProcessorHost 接收事件
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

## <a name="review-the-sample-code"></a>檢閱範例程式碼 
以下是使用 Node.js 從事件中樞接收事件的範例程式碼。 您可以手動建立 sampleEph.js 檔案，並執行它以從事件中樞接收事件。 

  ```javascript
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processo Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path
      },
      onEphError: (error) => {
        console.log("This handler will notify you of any internal errors that happen " +
        "during partition and lease management: %O", error);
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

在此快速入門中，您已建立可從事件中樞接收訊息的 Node.js 應用程式。 若要了解如何使用 Node.js 將事件傳送到事件中樞，請參閱[從事件中樞傳送事件 - Node.js](event-hubs-node-get-started-send.md)。

<!-- Links -->
[免費帳戶]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
