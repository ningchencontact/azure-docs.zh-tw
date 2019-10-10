---
title: 如何搭配 Node.js 使用 Azure 服務匯流排主題和訂用帳戶 | Microsoft Docs
description: 了解如何從 Node.js 應用程式，在 Azure 中使用服務匯流排主題和訂用帳戶。
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: f927274e1e866a9cba72330280316cc5ee7d8047
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178064"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>如何使用服務匯流排主題和訂用帳戶搭配 node.js 和 azure/服務匯流排套件
> [!div class="op_multi_selector" title1="程式設計語言" title2="Node.js 套件"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

在本教學課程中，您將瞭解如何撰寫 node.js 程式，以將訊息傳送至服務匯流排主題，並使用新的[@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)封裝從服務匯流排的訂用帳戶接收訊息。 此套件使用更快速的[AMQP 1.0 通訊協定](service-bus-amqp-overview.md)，而舊的[azure sb](https://www.npmjs.com/package/azure-sb)套件則用[服務匯流排 REST 執行時間 api](/rest/api/servicebus/service-bus-runtime-rest)。 這些範例是以 JavaScript 撰寫。

## <a name="prerequisites"></a>必要條件
- Azure 訂用帳戶。 若要完成此教學課程，您需要 Azure 帳戶。 您可以啟用[MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 如果您沒有要使用的主題和訂用帳戶，請遵循[使用 Azure 入口網站建立服務匯流排主題和訂閱一](service-bus-quickstart-topics-subscriptions-portal.md)文中的步驟來建立它們。 記下您服務匯流排實例的連接字串，以及您所建立之主題和訂用帳戶的名稱。 我們會在範例中使用這些值。

> [!NOTE]
> - 本教學課程適用于您可以使用[Nodejs](https://nodejs.org/)來複製和執行的範例。 如需如何建立 node.js 應用程式的指示，請參閱[使用 Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)[建立 node.js 應用程式並將其部署到 Azure 網站](../app-service/app-service-web-get-started-nodejs.md)或 node.js 雲端服務。
> - 新的[@no__t 1](https://www.npmjs.com/package/@azure/service-bus)封裝尚不支援建立 topcis 和訂閱。 如果您想要以程式設計方式建立，請使用[@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus)套件。

### <a name="use-node-package-manager-npm-to-install-the-package"></a>使用 Node Package Manager (NPM) 安裝封裝
若要安裝服務匯流排的 npm 套件，請開啟在其路徑中有 `npm` 的命令提示字元，將目錄變更為您想要擁有範例的資料夾，然後執行此命令。

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>傳送訊息至主題
與服務匯流排主題互動一開始會先將[ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient)類別具現化，並使用它來具現化[TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient)類別。 擁有用戶端之後，您就可以建立傳送者，並在其上使用[send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-)或[sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---)方法來傳送訊息。

1. 開啟您最愛的編輯器，例如[Visual Studio Code](https://code.visualstudio.com/)
2. 建立名為 `send.js` 的檔案，並將下列程式碼貼入其中。 此程式碼會將10則訊息傳送至您的主題。

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const topicClient = sbClient.createTopicClient(topicName);
      const sender = topicClient.createSender();
      
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

            await topicClient.close();
          } finally {
            await sbClient.close();
          }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 在上述程式碼中，輸入您的主題的連接字串和名稱。
4. 然後在命令提示字元中執行命令 `node send.js`，以執行此檔案。 

恭喜您！ 您剛剛將訊息傳送至服務匯流排的佇列。

訊息具有一些標準屬性，例如 `label`，以及您可以在傳送時設定的 `messageId`。 如果您想要設定任何自訂屬性，請使用 `userProperties`，這是可保存自訂資料之索引鍵/值組的 json 物件。

服務匯流排主題支援的訊息大小上限：在[標準層](service-bus-premium-messaging.md)中為 256 KB 以及在[進階層](service-bus-premium-messaging.md)中為 1 MB。 主題中所保存的訊息數目沒有限制，但主題所保存的訊息大小總計有限制。 此主題大小會在建立時定義，上限是 5 GB。 如需有關配額的詳細資訊，請參閱[服務匯流排配額](service-bus-quotas.md)。

## <a name="receive-messages-from-a-subscription"></a>自訂用帳戶接收訊息
與服務匯流排訂用帳戶的互動一開始會先具現化[ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient)類別，並使用它來將[SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient)類別具現化。 擁有訂用帳戶用戶端之後，您就可以建立接收者，並在其上使用[receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-)或[registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-)方法來接收訊息。

1. 開啟您最愛的編輯器，例如[Visual Studio Code](https://code.visualstudio.com/)
2. 建立名為 `recieve.js` 的檔案，並將下列程式碼貼入其中。 此程式碼會嘗試從您的訂用帳戶接收10則訊息。 您收到的實際計數取決於訂用帳戶中的訊息數目和網路延遲。

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.receiveAndDelete);
      
      try {
        const messages = await receiver.receiveMessages(10);
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
        
        await subscriptionClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 在上述程式碼中，輸入您的主題和訂用帳戶的連接字串和名稱。
4. 然後在命令提示字元中執行命令 `node receiveMessages.js`，以執行此檔案。

恭喜您！ 您剛收到來自服務匯流排訂用帳戶的訊息。

[CreateReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-)方法會採用 `ReceiveMode`，這是具有[ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations)和[PeekLock](message-transfers-locks-settlement.md#settling-receive-operations)值的列舉。 如果您在訊息上使用任何 `complete()`、`abandon()`、`defer()` 或 @no__t 5 方法，請記得使用 `PeekLock` 模式來[結算您的訊息](message-transfers-locks-settlement.md#settling-receive-operations)。

## <a name="subscription-filters-and-actions"></a>訂用帳戶篩選和動作
服務匯流排支援[訂閱上的篩選和動作](topic-filters.md)，這可讓您篩選傳入的訊息，並編輯其屬性。

當您擁有 @no__t 0 的實例時，您可以在其上使用下列方法來取得、新增和移除訂用帳戶的規則，以控制篩選和動作。

- getRules
- addRule
- removeRule

每個訂用帳戶都有一個預設規則，它會使用 true 篩選準則來允許所有傳入訊息。 當您新增規則時，請記得移除預設篩選，讓新規則中的篩選準則能夠正常執行。 如果訂用帳戶沒有任何規則，則它不會收到任何訊息。

> [!NOTE]
> 您可以使用[服務匯流排總管](https://github.com/paolosalvatori/ServiceBusExplorer/)來管理服務匯流排資源。 服務匯流排總管可讓使用者連線到服務匯流排命名空間，並以簡便的方式管理傳訊實體。 此工具提供進階的功能 (例如匯入/匯出功能) 或測試主題、佇列、訂用帳戶、轉送服務、通知中樞和事件中樞的能力。 

## <a name="next-steps"></a>後續步驟
若要深入瞭解，請參閱下列資源。

- [佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
- 簽出[GitHub 上服務匯流排的其他 Nodejs 範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js 開發人員中心](https://azure.microsoft.com/develop/nodejs/)


