---
title: 呼叫或觸發邏輯應用程式使用 Azure Functions 和 Azure 服務匯流排
description: 建立 Azure 函式會呼叫或使用 Azure 服務匯流排觸發邏輯應用程式
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 06/04/2019
ms.openlocfilehash: 3d4f642ae25a179ea2c3241240996da774cd8c23
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494909"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>呼叫或使用 Azure Functions 和 Azure 服務匯流排觸發邏輯應用程式

您可以使用[Azure Functions](../azure-functions/functions-overview.md)觸發邏輯應用程式，當您需要部署長時間執行的接聽程式或工作。 例如，您可以在其中建立 Azure 函式，可在接聽[Azure 服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)排入佇列，並立即引發邏輯應用程式成為推送觸發程序。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* Azure 服務匯流排命名空間中。 如果您沒有命名空間[先建立您的命名空間](../service-bus-messaging/service-bus-create-namespace-portal.md)。

* Azure 函數應用程式，是 Azure functions 的容器。 如果您沒有函式應用程式[函式應用程式，請先建立](../azure-functions/functions-create-first-azure-function.md)，並確定您選取.NET 執行階段堆疊。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

## <a name="create-logic-app"></a>建立邏輯應用程式

此案例中，您必須執行每個邏輯應用程式，您要觸發的函式。 首先，建立邏輯應用程式開頭為 HTTP 要求觸發程序。 每當收到佇列訊息時，函數即會呼叫該端點。  

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後建立空白的邏輯應用程式。

   如果您不熟悉 logic apps，請檢閱[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

1. 在搜尋方塊中，輸入 "http request"。 從觸發程序清單中，選取此觸發程序：**收到 HTTP 请求时**

   ![選取觸發程序](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   要求觸發程序中，您可以選擇性地輸入佇列訊息搭配使用的 JSON 結構描述。 JSON 結構描述可協助您了解輸入資料中，結構，並讓您更輕鬆地在您的工作流程中使用的輸出時，邏輯應用程式設計工具。

1. 若要指定結構描述，請在 [要求本文 JSON 結構描述]  方塊中輸入結構描述，例如：

   ![指定 JSON 結構描述](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   如果您沒有結構描述，但是有 JSON 格式的範例承載，即可從該承載產生 JSON 結構描述。

   1. 在要求觸發程序中，選擇 [使用範例承載來產生結構描述]  。

   1. 在 [輸入或貼上範例 JSON 承載]  之下，輸入您的範例承載，然後選擇 [完成]  。

      ![輸入範例承載](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   此範例承載會產生出現在觸發程序中的這個結構描述：

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. 新增您想要在收到佇列訊息後執行的任何其他動作。

   例如，您可以使用 Office 365 Outlook 連接器傳送電子郵件。

1. 儲存邏輯應用程式，進而為此邏輯應用程式中的觸發程序產生回呼 URL。 稍後，您使用此回撥 URL 程式碼中的 Azure 服務匯流排佇列觸發程序。

   URL 會出現在回呼**HTTP POST URL**屬性。

   ![為觸發程序產生的回呼 URL](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>建立 Azure 函式

接著，建立一個函式，作為觸發程序並接聽佇列。

1. 在 Azure 入口網站中，開啟並展開函式應用程式 (如果尚未開啟)。 

1. 在函式應用程式名稱之下，展開 [Functions]  。 在 [函式]  窗格上，選擇 [新增函式]  。

   ![展開 「 函式 」，並選擇 「 新的函式 」](./media/logic-apps-scenario-function-sb-trigger/create-new-function.png)

1. 選取 根據您是否建立新的函式應用程式，其中您選取.NET 執行階段堆疊中，為此範本，或您正在使用現有的函式應用程式。

   * 針對新的函式應用程式，選取此範本：**服務匯流排佇列觸發程序**

     ![選取新的函式應用程式範本](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * 現有的函式應用程式中，選取此範本：**服務匯流排佇列觸發程序-C#**

     ![選取現有的函式應用程式範本](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. 上**Azure 服務匯流排佇列觸發程序**窗格中，提供您觸發程序的名稱，並設定**服務匯流排連接**佇列，請使用 Azure 服務匯流排 SDK`OnMessageReceive()`接聽程式，然後選擇**建立**。

1. 撰寫基本函式呼叫先前建立的邏輯應用程式端點所使用的觸發程序的佇列訊息。 此範例會使用 `application/json` 訊息內容類型，但是您可以視需要變更此類型。 可能的話，請重複使用 HTTP 用戶端的執行的個體。 如需詳細資訊，請參閱 <<c0> [ 在 Azure Functions 中管理連線](../azure-functions/manage-connections.md)。

   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Callback URL for previously created Request trigger
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible
   private static HttpClient httpClient = new HttpClient();

   public static void Run(string myQueueItem, ILogger log)
   {
       log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

1. 若要測試此函式，請使用 [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) 等工具來新增佇列訊息。

   在函式收到訊息之後，邏輯應用程式會立即觸發。

## <a name="next-steps"></a>後續步驟

[呼叫、 觸發或巢狀工作流程，藉由使用 HTTP 端點](../logic-apps/logic-apps-http-endpoint.md)