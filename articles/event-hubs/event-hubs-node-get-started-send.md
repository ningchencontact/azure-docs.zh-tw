---
title: 使用 Node.js 將事件傳送到 Azure 事件中樞 | Microsoft Docs
description: 開始使用 Node.js 將事件傳送到事件中樞。
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 09/18/2018
ms.author: shvija
ms.openlocfilehash: 413f36a12dee135cc1a7dc99a34d8b7b2be6c46f
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801058"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>使用 Node.js 將事件傳送到 Azure 事件中樞

Azure 事件中樞是可高度擴充的事件管理系統，每秒可以處理數以百萬計的事件，讓應用程式能處理及分析已連線裝置和其他系統所產生的大量資料。 收集到事件中樞之後，您就可以使用處理序內處理常式來接收及處理事件，或將事件轉送到期他分析系統。

若要深入了解事件中樞，請參閱[事件中樞概觀](event-hubs-about.md)。

此教學課程說明如何將事件從以 Node.js 撰寫的應用程式傳送到事件中樞。 若要使用 Node.js 事件處理器主機套件來接收事件，請參閱[對應的接收文章](event-hubs-node-get-started-receive.md)。

適用於此快速入門的程式碼可在 [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client) 上取得。 

## <a name="prerequisites"></a>先決條件

若要完成此教學課程，您需要下列先決條件：

- Node.js 8.x 版和更新版本。 從 [https://nodejs.org](https://nodejs.org) 下載最新的 LTS 版本。
- 使用中的 Azure 帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶][] 。
- Visual Studio Code (建議採用) 或任何其他的 IDE

## <a name="create-a-namespace-and-event-hub"></a>建立命名空間和事件中樞
第一個步驟是使用 Azure 入口網站來建立事件中樞命名空間和事件中樞。 如果您沒有現有的帳戶，您可以依照[使用 Azure 入口網站來建立事件中樞命名空間和事件中樞](event-hubs-create.md)中的指示來建立這些實體。

## <a name="clone-the-sample-git-repository"></a>複製範例 Git 存放庫
在您的電腦上，從 [Github](https://github.com/Azure/azure-event-hubs-node) 複製範例 Git 存放庫。 

## <a name="install-nodejs-package"></a>安裝 Node.js 套件
在您的電腦上，安裝適用於 Azure 事件中樞的 Node.js 套件。 

```nodejs
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>複製 Git 存放庫
從 GitHub 下載或複製[範例](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples)。 

## <a name="send-events"></a>傳送事件
您已複製的 SDK 包含多個範例，其會示範如何使用 Node.js 將事件傳送到事件中樞。 在此快速入門中，您會使用 **simpleSender.js** 範例。 若要觀察所接收的事件，請開啟另一個終端機，並使用[接收範例](event-hubs-node-get-started-receive.md)來接收事件。

1. 在 Visual Studio Code 上開啟專案。 
2. 在 [用戶端] 資料夾下方建立名為 **.env** 的檔案。 從根資料夾的 **sample.env**，複製並貼上範例環境變數。
3. 設定您的事件中樞連接字串、事件中樞名稱和儲存體端點。 您可以在 Azure 入口網站的 [事件中樞] 頁面上，從 **RootManageSharedAccessKey** 下方的 **Connection string-primary** 索引鍵中，複製事件中樞的連接字串。 如需詳細步驟，請參閱[取得連接字串](event-hubs-quickstart-portal.md#create-an-event-hubs-namespace)。
4. 在 Azure CLI 上，瀏覽至 [用戶端] 資料夾路徑。 執行下列命令來安裝節點套件並建置專案：

    ```nodejs
    npm i
    npm run build
    ```
5. 執行下列命令以開始傳送事件： 

    ```nodejs
    node dist/examples/simpleSender.js
    ```


## <a name="review-the-sample-code"></a>檢閱範例程式碼 
以下是使用 Node.js 將事件傳送到事件中樞的範例程式碼。 您可以手動建立 sampleSender.js 檔案，並執行它以將事件傳送到事件中樞。 


```nodejs
const { EventHubClient, EventPosition } = require('@azure/event-hubs');

const client = EventHubClient.createFromConnectionString(process.env["EVENTHUB_CONNECTION_STRING"], process.env["EVENTHUB_NAME"]);

async function main() {
    // NOTE: For receiving events from Azure Stream Analytics, please send Events to an EventHub where the body is a JSON object/array.
    // const eventData = { body: { "message": "Hello World" } };
    const data = { body: "Hello World 1" };
    const delivery = await client.send(data);
    console.log("message sent successfully.");
}

main().catch((err) => {
    console.log(err);
});

```

請記得先設定您的環境變數，然後再執行指令碼。 您可以在命令列中設定此項 (如下列範例所示)，或使用 [dotenv 套件](https://www.npmjs.com/package/dotenv#dotenv) \(英文\)。 

```
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="next-steps"></a>後續步驟

若要深入了解事件中樞，請參閱下列文章：

* [使用 Node.js 接收事件](event-hubs-node-get-started-receive.md)
* [GitHub 上的範例](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/)
* [事件中樞常見問題集](event-hubs-faq.md)

<!-- Links -->
[免費帳戶]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
