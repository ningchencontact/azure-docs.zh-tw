---
title: 如何在 node.js 中使用 azure/服務匯流排佇列
description: 瞭解如何使用 azure/服務匯流排套件，從 node.js 應用程式在 Azure 中服務匯流排佇列。
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 10/22/2019
ms.author: aschhab
ms.openlocfilehash: 58049855cc27d51134b9f76a773f32f49c6381b6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790318"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>如何搭配使用服務匯流排佇列與 node.js 和 azure/服務匯流排套件
> [!div class="op_multi_selector" title1="程式設計語言" title2="Node.js 套件"]
> - [（Node.js | azure-sb）](service-bus-nodejs-how-to-use-queues.md)
> - [（Node.js | @azure/service-bus）](service-bus-nodejs-how-to-use-queues-new-package.md)

在本教學課程中，您將瞭解如何撰寫 Nodejs 程式，以使用新的[@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)套件，在服務匯流排佇列中傳送和接收訊息。 此套件使用更快速的[AMQP 1.0 通訊協定](service-bus-amqp-overview.md)，而舊的[azure sb](https://www.npmjs.com/package/azure-sb)套件則用[服務匯流排 REST 執行時間 api](/rest/api/servicebus/service-bus-runtime-rest)。 這些範例是以 JavaScript 撰寫。

## <a name="prerequisites"></a>必要條件
- Azure 訂用帳戶。 若要完成此教學課程，您需要 Azure 帳戶。 您可以啟用[MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 如果您沒有要使用的佇列，請遵循[使用 Azure 入口網站建立服務匯流排佇列一](service-bus-quickstart-portal.md)文中的步驟來建立佇列。 記下您服務匯流排實例的連接字串，以及您所建立之佇列的名稱。 我們會在範例中使用這些值。

> [!NOTE]
> - 本教學課程適用于您可以使用[Nodejs](https://nodejs.org/)來複製和執行的範例。 如需如何建立 node.js 應用程式的指示，請參閱[使用 Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)[建立 node.js 應用程式並將其部署到 Azure 網站](../app-service/app-service-web-get-started-nodejs.md)或 node.js 雲端服務。
> - 新的[@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)封裝尚不支援建立佇列。 如果您想要以程式設計方式建立[@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus)封裝，請使用此套件。

### <a name="use-node-package-manager-npm-to-install-the-package"></a>使用 Node Package Manager (NPM) 安裝封裝
若要安裝服務匯流排的 npm 套件，請開啟在其路徑中有 `npm` 的命令提示字元，將目錄變更為您想要取得範例的資料夾，然後執行此命令。

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>傳送訊息至佇列
與服務匯流排的佇列互動一開始會先具現化[ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient)類別，並使用它來將[QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient)類別具現化。 擁有佇列用戶端之後，您可以建立傳送者，並在其上使用[send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-)或[sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---)方法來傳送訊息。

1. 開啟您最愛的編輯器，例如[Visual Studio Code](https://code.visualstudio.com/)
2. 建立名為 `send.js` 的檔案，並在其中貼上下列程式碼。 此程式碼會將10個訊息傳送至您的佇列。

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const sender = queueClient.createSender();
      
      try {
        for (let i = 0; i < 10; i++) {
          const message= {
            body: `Hello world! ${i}`,
            label: `test`,
            userProperties: {
                myCustomPropertyName: `my custom property value ${i}`
           }
          };
          console.log(`Sending message: ${message.body}`);
          await sender.send(message);
        }
        
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 在上述程式碼中，輸入您的佇列的連接字串和名稱。
4. 然後在命令提示字元中執行命令 `node send.js`，以執行此檔案。

恭喜！ 您剛剛將訊息傳送至服務匯流排的佇列。

訊息具有一些標準屬性，例如您可以在傳送時設定的 `label` 和 `messageId`。 如果您想要設定任何自訂屬性，請使用 `userProperties`，這是可保存自訂資料之索引鍵/值組的 json 物件。

服務匯流排佇列支援的訊息大小上限：在[標準層](service-bus-premium-messaging.md)中為 256 KB 以及在[進階層](service-bus-premium-messaging.md)中為 1 MB。 佇列中保留的訊息數目沒有限制，但佇列所持有的訊息大小總計有上限。 此佇列大小會在建立時定義，上限是 5 GB。 如需有關配額的詳細資訊，請參閱[服務匯流排配額](service-bus-quotas.md)。

## <a name="receive-messages-from-a-queue"></a>從佇列接收訊息
與服務匯流排的佇列互動一開始會先具現化[ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient)類別，並使用它來將[QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient)類別具現化。 擁有佇列用戶端之後，您就可以建立接收者，並在其上使用[receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-)或[registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-)方法來接收訊息。

1. 開啟您最愛的編輯器，例如[Visual Studio Code](https://code.visualstudio.com/)
2. 建立名為 `recieve.js` 的檔案，並在其中貼上下列程式碼。 此程式碼會嘗試從您的佇列接收10個訊息。 您收到的實際計數取決於佇列中的訊息數目和網路延遲。

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.receiveAndDelete);
      try {
        const messages = await receiver.receiveMessages(10)
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
         
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 在上述程式碼中，輸入您的佇列的連接字串和名稱。
4. 然後在命令提示字元中執行命令 `node receiveMessages.js`，以執行此檔案。

恭喜！ 您剛收到來自服務匯流排佇列的訊息。

[CreateReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-)方法會採用 `ReceiveMode`，這是具有[ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations)和[PeekLock](message-transfers-locks-settlement.md#settling-receive-operations)值的列舉。 如果您在訊息上使用任何 `complete()`、`abandon()`、`defer()`或 `deadletter()` 方法，請記得使用 `PeekLock` 模式來[結算您的訊息](message-transfers-locks-settlement.md#settling-receive-operations)。

> [!NOTE]
> 您可以使用[服務匯流排總管](https://github.com/paolosalvatori/ServiceBusExplorer/)來管理服務匯流排資源。 服務匯流排總管可讓使用者連線到服務匯流排命名空間，並以簡便的方式管理傳訊實體。 此工具提供進階的功能 (例如匯入/匯出功能) 或測試主題、佇列、訂用帳戶、轉送服務、通知中樞和事件中樞的能力。 

## <a name="next-steps"></a>後續步驟
若要深入瞭解，請參閱下列資源。
- [佇列、主題和訂閱](service-bus-queues-topics-subscriptions.md)
- 簽出[GitHub 上服務匯流排的其他 Nodejs 範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js 開發人員中心](https://azure.microsoft.com/develop/nodejs/)

