---
title: 建立 Azure Logic Apps 中的事件為基礎的工作和工作流程
description: 觸發、 暫停和繼續自動化的工作、 處理序和使用 Azure Logic Apps 在端點上發生的事件為基礎的工作流程
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: c2658df185d4836210c496d2c46a00a3541257a2
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541392"
---
# <a name="automate-event-based-tasks-and-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Azure Logic Apps 中使用 HTTP webhook 自動化事件為基礎的工作和工作流程

具有[Azure Logic Apps](../logic-apps/logic-apps-overview.md)和內建的 HTTP Webhook 連接器，您可以自動化，等候，並根據特定事件發生在 HTTP 或 HTTPS 端點，藉由建置邏輯應用程式上執行的工作流程。 比方說，您可以在其中建立邏輯應用程式監視的服務端點，藉由在觸發工作流程和執行指定的動作，而不是定期檢查之前等待特定事件或*輪詢*該端點。

以下是一些範例事件為基礎的工作流程：

* 等候送過來的項目[Azure 事件中樞](https://github.com/logicappsio/EventHubAPI)觸發邏輯應用程式執行之前。
* 等待核准再繼續工作流程。

## <a name="how-do-webhooks-work"></a>Webhook 的工作？

HTTP webhook 觸發程序是以事件為基礎，這不會相依於檢查或定期輪詢新的項目。 當您儲存邏輯應用程式開頭的 webhook 觸發程序，或當您從停用變成啟用，webhook 觸發程序，會在變更您的邏輯應用程式時*訂閱*特定服務或端點註冊*回呼 URL*與該服務或端點。 觸發程序然後等候該服務或端點以呼叫的 URL，開始執行邏輯應用程式。 類似於[要求觸發程序](connectors-native-reqres.md)，立即指定的事件發生時，就會引發邏輯應用程式。 觸發程序*取消訂閱*從服務或端點或如果您移除觸發程序，並儲存邏輯應用程式，當您變更您的邏輯應用程式，從啟用變成停用。

HTTP webhook 動作也是事件為基礎並*訂閱*特定服務或端點註冊*回呼 URL*與該服務或端點。 Webhook 動作會暫停邏輯應用程式的工作流程，並等待，直到服務或端點的 URL，邏輯應用程式繼續執行之前呼叫。 動作的邏輯應用程式*取消訂閱*從服務或端點，在這些情況下：

* 已成功完成 webhook 動作
* 如果等候回應時取消執行邏輯應用程式
* 之前的邏輯應用程式逾時

例如，Office 365 Outlook 連接器[**傳送核准電子郵件**](connectors-create-api-office365-outlook.md)動作會遵循此模式的 webhook 動作的範例。 您可以使用 webhook 動作到任何服務的檔案，以擴充此模式。

如需詳細資訊，請參閱下列主題：

* [HTTP Webhook 觸發程序參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhook 和訂用帳戶](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [建立支援 webhook 的自訂 Api](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 已部署的端點的 URL 或 API，支援 webhook 訂閱和取消訂閱模式[logic apps 中的 webhook 觸發程序](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)或是[logic apps 中的 webhook 動作](../logic-apps/logic-apps-create-api-app.md#webhook-actions)視需要

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)

* 您想要用來等待特定事件的目標端點的邏輯應用程式。 HTTP Webhook 觸發程序中，啟動[建立空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用的 HTTP Webhook 動作，請以您想要的任何觸發程序啟動邏輯應用程式。 此範例會使用 HTTP 觸發程序的第一個步驟。

## <a name="add-an-http-webhook-trigger"></a>新增 HTTP Webhook 觸發程序

此內建的觸發程序使用指定的服務註冊的回呼 URL，並等候該服務將 HTTP POST 要求傳送至該 URL。 這個事件發生時，觸發程序，就會引發，並立即執行邏輯應用程式。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟空白的邏輯應用程式。

1. 在設計工具中，在 [搜尋] 方塊中，輸入 「 http webhook"作為篩選條件。 從**觸發程序**清單中，選取**HTTP Webhook**觸發程序。

   ![選取 HTTP Webhook 觸發程序](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   此範例中重新命名 「 HTTP Webhook 觸發程序 「 觸發程序，以便在此步驟中有更具描述性的名稱。 此外，範例稍後會新增 HTTP Webhook 動作，而且這兩個名稱必須是唯一。

1. 提供值給[HTTP Webhook 觸發程序參數](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)您想要用於訂閱和取消訂閱呼叫，例如：

   ![輸入 HTTP Webhook 觸發程序參數](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. 若要新增其他可用的參數，請開啟**加入新參數**清單，然後選取您想要的參數。

   HTTP webhook 可用的驗證類型的相關資訊，請參閱[驗證的 HTTP 觸發程序和動作](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)。

1. 請使用當引發觸發程序時執行的動作，繼續建置邏輯應用程式的工作流程。

1. 當您完成時，完成之後，請務必儲存您的邏輯應用程式。 在設計工具的工具列中，選取**儲存**。

   儲存邏輯應用程式會呼叫訂閱端點，並註冊觸發此邏輯應用程式的回呼 URL。

1. 現在，每當目標服務傳送`HTTP POST`要求的回呼 URL，邏輯應用程式會引發，並包含要求會傳遞任何資料。

## <a name="add-an-http-webhook-action"></a>新增 HTTP Webhook 動作

此內建動作以指定的服務註冊的回呼 URL，暫停邏輯應用程式的工作流程，並等候該服務將 HTTP POST 要求傳送至該 URL。 這個事件發生時，此動作會繼續執行邏輯應用程式。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 在邏輯應用程式設計工具中開啟邏輯應用程式。

   此範例會使用 HTTP Webhook 觸發程序的第一個步驟。

1. 在您要新增 HTTP Webhook 動作的步驟中，選取**新增步驟**。

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 選取加號 ( **+** )，隨即出現，然後按**新增動作**。

1. 在設計工具中，在 [搜尋] 方塊中，輸入 「 http webhook"作為篩選條件。 從**動作**清單中，選取**HTTP Webhook**動作。

   ![選取 HTTP Webhook 動作](./media/connectors-native-webhook/select-http-webhook-action.png)

   此範例中重新命名 「 HTTP Webhook 動作 」 的動作，以便在此步驟中有更具描述性的名稱。

1. 提供值給 HTTP Webhook 動作的參數，也就是類似[HTTP Webhook 觸發程序參數](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger)您想要用於訂閱和取消訂閱呼叫，例如：

   ![輸入 HTTP Webhook 動作參數](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   在執行階段，邏輯應用程式會呼叫訂閱端點，執行此動作時。 邏輯應用程式然後暫停工作流程，並等候目標服務以傳送`HTTP POST`的回呼 URL 的要求。 如果動作成功完成，動作會取消訂閱端點，從邏輯應用程式會繼續執行工作流程。

1. 若要新增其他可用的參數，請開啟**加入新參數**清單，然後選取您想要的參數。

   HTTP webhook 可用的驗證類型的相關資訊，請參閱[驗證的 HTTP 觸發程序和動作](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)。

1. 當您完成時，請務必儲存您的邏輯應用程式。 在設計工具的工具列中，選取**儲存**。

## <a name="connector-reference"></a>連接器參考

如需有關觸發程序和動作參數，也就是彼此類似，請參閱[HTTP Webhook 參數](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger)。

### <a name="output-details"></a>输出详细信息

以下是從 HTTP Webhook 觸發程序或動作，它會傳回這項資訊輸出的詳細資訊：

| 屬性名稱 | 類型 | 描述 |
|---------------|------|-------------|
| headers | object | 來自要求的標頭 |
| body | object | JSON 物件 | 要求的主體內容物件 |
| status code | int | 要求中的狀態碼 |
|||

| status code | 描述 |
|-------------|-------------|
| 200 | [確定] |
| 202 | 已接受 |
| 400 | 不正確的要求 |
| 401 | 未經授權 |
| 403 | 禁止 |
| 404 | 找不到 |
| 500 | 內部伺服器錯誤。 發生未知錯誤。 |
|||

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
