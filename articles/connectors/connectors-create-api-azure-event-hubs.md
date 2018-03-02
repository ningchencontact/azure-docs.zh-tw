---
title: "使用 Azure 事件中樞為 Azure Logic Apps 設定事件監視 | Microsoft Docs"
description: "使用 Azure 事件中樞和邏輯應用程式，來監視接收事件和傳送事件的資料流"
services: logic-apps
keywords: "資料流、事件監視器、事件中樞"
author: ecfan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 076f7dd11ca8c153046727861ecb755e88f32b01
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="monitor-receive-and-send-events-with-the-event-hubs-connector"></a>使用事件中樞連接器來監視、接收及傳送事件

若要設定事件監視器，以便邏輯應用程式偵測事件、接收事件和傳送事件，請從您的邏輯應用程式連線到 [Azure 事件中樞](https://azure.microsoft.com/services/event-hubs)。 深入了解 [Azure 事件中樞](../event-hubs/event-hubs-what-is-event-hubs.md)和 [Logic Apps 連接器的定價方式](../logic-apps/logic-apps-pricing.md)。

## <a name="prerequisites"></a>先決條件

您必須擁有以下這些項目，才能使用事件中樞連接器：

* [Azure 事件中樞命名空間和事件中樞](../event-hubs/event-hubs-create.md)
* [邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-event-hubs"></a>連線到 Azure 事件中樞

若要讓邏輯應用程式可存取任何服務，您必須先建立邏輯應用程式與服務之間的[連線](./connectors-overview.md) (如果您還尚未這麼做)。 此連線會授權邏輯應用程式存取資料。 若要讓邏輯應用程式存取事件中樞，請確認您有權取得事件中樞命名空間的連接字串。

1.  登入 [Azure 入口網站](https://portal.azure.com "Azure 入口網站")。 

2.  移至您的事件中樞「命名空間」，而不是特定事件中樞。 在命名空間頁面的 [設定] 之下，選擇 [共用存取原則]。 在 [宣告] 之下，確認您有該命名空間的 [管理] 權限。

    ![管理事件中樞命名空間的權限](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. 如果您想要在稍後手動輸入您的連線資訊，請取得事件中樞命名空間的連接字串。 選擇 [RootManageSharedAccessKey]。 選擇您的主索引鍵連接字串旁邊的 [複製] 按鈕。 儲存連接字串以供稍後使用。

    ![複製事件中樞命名空間連接字串](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

    > [!TIP]
    > 若要確認您的連接字串是否與您的事件中樞命名空間或特定事件中樞相關聯，請檢查連接字串中的 `EntityPath` 參數。 如果您發現這個參數，此連接字串適用於特定事件中樞「實體」，而不是使用您的邏輯應用程式的正確字串。

## <a name="trigger-workflow-when-your-event-hub-gets-new-events"></a>在事件中樞收到新事件時觸發工作流程

[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)是在邏輯應用程式中啟動工作流程的事件。 若要在新事件傳送至事件中樞時啟動工作流程，請遵循下列步驟來新增可偵測此事件的觸發程序。

1. 在 [Azure 入口網站](https://portal.azure.com "Azure 入口網站")中，移至現有的邏輯應用程式，或建立空白的邏輯應用程式。

2. 在 Logic Apps 設計工具中，於 [搜尋] 方塊中輸入「事件中樞」作為您的篩選條件。 選取此觸發程序︰**當事件可用於事件中樞時**

   ![選取當事件中樞收到新事件時的觸發程序](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

   1. 如果您還沒有事件中樞命名空間的連線，系統會提示您立即建立此連線。 為您的連線指定名稱，然後選取您想要使用的事件中樞命名空間。

      ![建立事件中樞連線](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

      或者，若要手動輸入連接字串，請選擇 [手動輸入連線資訊]。 
      了解[如何尋找您的連接字串](#permissions-connection-string)。

   2. 現在請選取要使用的事件中樞原則，並選擇 [建立]。

      ![建立事件中樞連線 (第二部分)](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. 選取要監視的事件中樞，並設定檢查事件中樞的時間間隔和頻率。

    ![指定事件中樞或取用者群組](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

    > [!TIP]
    > 若要選擇性地選取取用者群組以便讀取事件，請選擇 [顯示進階選項]。

4. 儲存您的邏輯應用程式。 在設計工具的工具列上，選擇 [儲存]。

現在，當邏輯應用程式檢查選取的事件中樞並發現新的事件後，觸發程序就會在邏輯應用程式中，針對找到的事件執行動作。

## <a name="send-events-to-your-event-hub-from-your-logic-app"></a>將事件從邏輯應用程式傳送至事件中樞

[動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是邏輯應用程式工作流程所執行的工作。 將觸發程序新增至邏輯應用程式之後，您可以新增一個動作，以對該觸發程序所產生的資料執行作業。 若要將事件從邏輯應用程式傳送至事件中樞，請遵循下列步驟。

1. 在 Logic Apps 設計工具中，於您的觸發程序之下，選擇 [新增步驟] > [新增動作]。

2. 在搜尋方塊中，輸入「事件中樞」作為篩選條件。
選取此動作：**事件中樞 - 傳送事件**

   ![選取「事件中樞 - 傳送事件」](./media/connectors-create-api-azure-event-hubs/select-event-hubs-send-event-action.png)

3. 選取要對其傳送事件的事件中樞。 然後，輸入事件內容和任何其他詳細資料。

   ![選取事件中樞名稱，並提供事件內容](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

4. 儲存您的邏輯應用程式。

您現在已設定可從邏輯應用程式傳送事件的動作。 

## <a name="connector-specific-details"></a>連接器特定的詳細資料

若要深入了解由 Swagger 檔案定義的觸發程序和動作及任何限制，請檢閱[連接器詳細資料](/connectors/eventhubs/)。

## <a name="get-support"></a>取得支援

* 如有問題，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交或票選功能構想，請造訪 [Logic Apps 使用者意見反應網站](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>後續步驟

* 了解 [Azure Logic Apps 的其他連接器](../connectors/apis-list.md)