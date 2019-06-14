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
ms.openlocfilehash: 7686014adb989494e6df277de4137b76c3125696
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65992125"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>如何使用 Node.js 和 azure/服務匯流排封裝中使用服務匯流排主題和訂用帳戶
> [!div class="op_multi_selector" title1="程式設計語言" title2="Node.js 套件"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

在本教學課程，了解如何撰寫 Node.js 程式以將訊息傳送至服務匯流排主題，並接收來自服務匯流排訂用帳戶使用新的訊息[ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus)封裝。 此套件會使用速度就越快[AMQP 1.0 通訊協定](service-bus-amqp-overview.md)而較舊[azure sb](https://www.npmjs.com/package/azure-sb)用封裝[服務匯流排 REST 執行階段 Api](/rest/api/servicebus/service-bus-runtime-rest)。 範例是以 JavaScript 撰寫的。

## <a name="prerequisites"></a>必要條件
- Azure 訂用帳戶。 若要完成此教學課程，您需要 Azure 帳戶。 您可以啟用您[MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或是註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 如果您沒有主題和訂用帳戶來處理，後續步驟[使用 Azure 入口網站來建立服務匯流排主題和訂用帳戶](service-bus-quickstart-topics-subscriptions-portal.md)文章，以建立它們。 請記下您的服務匯流排執行個體和名稱的主題和訂用帳戶，您所建立的連接字串。 在範例中，我們將使用這些值。

> [!NOTE]
> - 本教學課程中的運作方式與範例，您可以複製並使用執行[Nodejs](https://nodejs.org/)。 如需有關如何建立 Node.js 應用程式的指示，請參閱 <<c0> [ 建立及部署 Node.js 應用程式至 Azure 網站](../app-service/app-service-web-get-started-nodejs.md)，或[Node.js 雲端服務使用 Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)。
> - 新[ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus)套件不支援尚未建立 topcis 與訂用帳戶。 請改用[ @azure/arm-servicebus ](https://www.npmjs.com/package/@azure/arm-servicebus)封裝，如果您想要以程式設計方式建立它們。

### <a name="use-node-package-manager-npm-to-install-the-package"></a>使用 Node Package Manager (NPM) 安裝封裝
若要安裝 npm 套件適用於服務匯流排，開啟 命令提示字元可`npm`在其路徑中，將目錄變更至您要有您的範例，然後再執行此命令的資料夾。

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>傳送訊息至主題
互動的服務匯流排主題一開始具現化[ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient)類別，並使用它來具現化[TopicClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient)類別。 主題用戶端之後，您可以建立寄件者，並使用任何一種[傳送](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-)或是[sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---)以傳送訊息的方法。

1. 開啟您最愛的編輯器，例如[Visual Studio Code](https://code.visualstudio.com/)
2. 建立檔案，稱為`send.js`並貼上下列程式碼到其中。 此程式碼會將 10 個訊息傳送至主題。

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
3. 在上述程式碼中輸入的連接字串和主題的名稱。
4. 然後執行命令`node send.js`在命令提示字元執行此檔案中。 

恭喜！ 您只會傳送至服務匯流排佇列的訊息。

訊息有一些標準的屬性，例如`label`和`messageId`傳送時，您可以設定。 如果您想要設定的任何自訂屬性，使用`userProperties`，這是可以保留您的自訂資料的索引鍵 / 值組的 json 物件。

服務匯流排主題支援的訊息大小上限：在[標準層](service-bus-premium-messaging.md)中為 256 KB 以及在[進階層](service-bus-premium-messaging.md)中為 1 MB。 在主題中，保存的訊息數目沒有限制，但主題所保存的訊息總大小沒有限制。 此主題大小會在建立時定義，上限是 5 GB。 如需有關配額的詳細資訊，請參閱[服務匯流排配額](service-bus-quotas.md)。

## <a name="receive-messages-from-a-subscription"></a>自訂用帳戶接收訊息
互動的服務匯流排訂用帳戶開始具現化[ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient)類別，並使用它來具現化[SubscriptionClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient)類別。 訂用帳戶用戶端之後，您可以建立接收者，並使用任何一種[receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-)或是[registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-)上接收訊息的方法。

1. 開啟您最愛的編輯器，例如[Visual Studio Code](https://code.visualstudio.com/)
2. 建立檔案，稱為`recieve.js`並貼上下列程式碼到其中。 此程式碼會嘗試從您的訂用帳戶接收 10 則訊息。 您會收到的實際計數，取決於訂用帳戶和網路延遲的訊息數目。

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.ReceiveAndDelete);
      
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
3. 在上述程式碼中，輸入連接字串和您的主題和訂用帳戶的名稱。
4. 然後執行命令`node receiveMessages.js`在命令提示字元執行此檔案中。

恭喜！ 您只會收到來自服務匯流排訂用帳戶的訊息。

[CreateReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-)方法接受`ReceiveMode`這是具有值的列舉[ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations)並[PeekLock](message-transfers-locks-settlement.md#settling-receive-operations)。 請記得[結清您的訊息](message-transfers-locks-settlement.md#settling-receive-operations)如果您使用`PeekLock`模式下使用任一`complete()`， `abandon()`， `defer()`，或`deadletter()`訊息上的方法。

## <a name="subscription-filters-and-actions"></a>訂閱篩選條件和動作
服務匯流排支援[篩選條件和動作，在訂用帳戶上的](topic-filters.md)，可讓您以篩選訂用帳戶的內送訊息，以及編輯其屬性。

一旦您擁有的執行個體`SubscriptionClient`您可以使用下面上，以取得的方法，加上移除訂用帳戶，來控制的篩選條件和動作的規則。

- getRules
- addRule
- removeRule

每個訂用帳戶有一個預設規則可使用，則為 true 的篩選條件來允許所有內送訊息。 當您新增新的規則時，請務必移除預設篩選器篩選條件在您的新規則才能運作。 如果訂用帳戶不有任何規則，它就會收到任何訊息。

> [!NOTE]
> 您可以使用[服務匯流排總管](https://github.com/paolosalvatori/ServiceBusExplorer/)來管理服務匯流排資源。 服務匯流排總管可讓使用者連線到服務匯流排命名空間，並以簡便的方式管理傳訊實體。 此工具提供進階的功能 (例如匯入/匯出功能) 或測試主題、佇列、訂用帳戶、轉送服務、通知中樞和事件中樞的能力。 

## <a name="next-steps"></a>後續步驟
若要進一步了解，請參閱下列資源。

- [佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
- 簽出其他[GitHub 上的服務匯流排的 Nodejs 範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js 開發人員中心](https://azure.microsoft.com/develop/nodejs/)


