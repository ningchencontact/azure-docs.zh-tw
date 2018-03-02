---
title: "使用 Azure 服務匯流排為 Azure Logic Apps 設定傳訊作業 | Microsoft Docs"
description: "使用 Azure 服務匯流排透過邏輯應用程式傳送和接收訊息"
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 02/06/2018
ms.author: ladocs
ms.openlocfilehash: e81580db17610adc6be534c9801881f9b68b14fd
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="send-and-receive-messages-with-the-azure-service-bus-connector"></a>使用 Azure 服務匯流排連接器來傳送及接收訊息

若要使用邏輯應用程式來傳送和接收訊息，請連線至 [Azure 服務匯流排](https://azure.microsoft.com/services/service-bus/)。 您可以執行動作，例如傳送至佇列、傳送至主題、從佇列接收和從訂用帳戶接收。 深入了解 [Azure 服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)和 [Logic Apps 觸發程序的定價方式](../logic-apps/logic-apps-pricing.md)。

## <a name="prerequisites"></a>先決條件

在能使用服務匯流排連接器之前，您必須擁有以下項目，而且這些項目必須存在相同的 Azure 訂用帳戶中，讓其能找到彼此：

* [服務匯流排命名空間和傳訊實體，例如佇列](../service-bus-messaging/service-bus-create-namespace-portal.md)
* [邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-service-bus"></a>連線到 Azure 服務匯流排

若要讓邏輯應用程式可存取任何服務，您必須先建立邏輯應用程式與服務之間的[連線](./connectors-overview.md) (如果您還尚未這麼做)。 此連線會授權邏輯應用程式存取資料。 為了讓邏輯應用程式可存取您的服務匯流排帳戶，請檢查您的權限。

1. 登入 [Azure 入口網站](https://portal.azure.com "Azure 入口網站")。 

2. 移至您的服務匯流排「命名空間」(不是特定的「訊息實體」)。 在命名空間頁面的 [設定] 之下，選擇 [共用存取原則]。 在 [宣告] 之下，確認您有該命名空間的 [管理] 權限。

   ![管理服務匯流排命名空間的權限](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. 如果您想要在稍後手動輸入您的連線資訊，請取得服務匯流排命名空間的連接字串。 選擇 [RootManageSharedAccessKey]。 選擇您的主索引鍵連接字串旁邊的 [複製] 按鈕。 儲存連接字串以供稍後使用。

   ![複製服務匯流排命名空間連接字串](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > 若要確認您的連接字串是否與您的服務匯流排命名空間或特定實體相關聯，請檢查連接字串中的 `EntityPath` 參數。 如果您發現這個參數，此連接字串適用於特定實體，但不是可使用您邏輯應用程式的正確字串。

## <a name="trigger-workflow-when-your-service-bus-gets-new-messages"></a>當您的服務匯流排取得新訊息時觸發工作流程

[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)是在邏輯應用程式中啟動工作流程的事件。 若要在新訊息傳送至服務匯流排時啟動工作流程，請遵循下列步驟來新增可偵測這些訊息的觸發程序。

1. 在 [Azure 入口網站](https://portal.azure.com "Azure 入口網站")中，移至現有的邏輯應用程式，或建立空白的邏輯應用程式。

2. 在 Logic Apps 設計工具中，於 [搜尋] 方塊中輸入「服務匯流排」作為您的篩選條件。 選取**服務匯流排**連接器。 

   ![選取服務匯流排連接器](./media/connectors-create-api-azure-service-bus/select-service-bus-connector.png) 

3. 選取您想要使用的觸發程序。 例如，若要在新項目傳送至「服務匯流排」佇列時，執行邏輯應用程式，則選取此觸發程序：**服務匯流排 - 在佇列中收到訊息時 (自動完成)**

   ![選取服務匯流排觸發程序](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   1. 如果您還沒有服務匯流排命名空間的連線，系統會提示您立即建立此連線。 為您的連線指定名稱，然後選取您想要使用的服務匯流排命名空間。

      ![建立服務匯流排連線](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      或者，若要手動輸入連接字串，請選擇 [手動輸入連線資訊]。 
      了解[如何尋找您的連接字串](#permissions-connection-string)。

   2. 現在請選取要使用的服務匯流排原則，並選擇 [建立]。

      ![建立服務匯流排連線 (第二部分)](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

4. 選取要使用的服務匯流排，並設定檢查佇列的時間間隔和頻率。

   ![選取服務匯流排佇列，並設定輪詢間隔](./media/connectors-create-api-azure-service-bus/select-service-bus-queue.png)

5. 儲存您的邏輯應用程式。 在設計工具的工具列上，選擇 [儲存]。

現在，當邏輯應用程式檢查選取的佇列並發現新的訊息後，觸發程序就會在邏輯應用程式中，針對找到的訊息執行動作。

## <a name="send-messages-from-your-logic-app-to-your-service-bus"></a>從邏輯應用程式傳送訊息至服務匯流排

[動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是邏輯應用程式工作流程所執行的工作。 將觸發程序新增至邏輯應用程式之後，您可以新增一個動作，以對該觸發程序所產生的資料執行作業。 若要將訊息從邏輯應用程式傳送至您的服務匯流排傳訊實體，請遵循下列步驟。

1. 在 Logic Apps 設計工具中，於您的觸發程序之下，選擇 [+ 新增步驟] > [新增動作]。

2. 在搜尋方塊中，輸入「服務匯流排」作為篩選條件。 選取此連接器：**服務匯流排**

   ![選取服務匯流排連接器](./media/connectors-create-api-azure-service-bus/select-service-bus-connector-for-action.png) 

3. 選取此動作：**服務匯流排 - 傳送訊息**

   ![選取「服務匯流排 - 傳送訊息」](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png)

4. 選取要傳送訊息的傳訊實體 (佇列或主題名稱)。 然後，輸入訊息內容和任何其他詳細資料。

   ![選取傳訊實體，並提供訊息詳細資料](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)    

5. 儲存您的邏輯應用程式。 

您現在已設定可從邏輯應用程式傳送訊息的動作。 

## <a name="connector-specific-details"></a>連接器特定的詳細資料

若要深入了解由 Swagger 檔案定義的觸發程序和動作及任何限制，請檢閱[連接器詳細資料](/connectors/servicebus/)。

## <a name="get-support"></a>取得支援

* 如有問題，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交或票選功能構想，請造訪 [Logic Apps 使用者意見反應網站](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>後續步驟

* 了解 [Azure Logic Apps 的其他連接器](../connectors/apis-list.md)