---
title: 建立以事件為基礎的工作流程或動作 - Azure Logic Apps | Microsoft Docs
description: 使用 Webhook 和 Azure Logic Apps 自動執行以事件為基礎的工作流程或動作
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.topic: article
tags: connectors
ms.date: 07/21/2016
ms.openlocfilehash: c3047000843e054e71ec1a80313118a25e7c4905
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60447183"
---
# <a name="create-event-based-workflows-or-actions-by-using-webhooks-and-azure-logic-apps"></a>使用 Webhook 和 Azure Logic Apps 建立以事件為基礎的工作流程或動作

透過使用 webhook 動作和觸發程序，您可以啟動、暫停和繼續流程來執行這些工作︰

* 收到項目時，即從 [Azure 事件中樞](https://github.com/logicappsio/EventHubAPI) 觸發
* 等待核准再繼續工作流程

深入了解關於[如何建立支援 webhook 的自訂 API](../logic-apps/logic-apps-create-api-app.md)。

## <a name="use-the-webhook-trigger"></a>使用 webhook 觸發程序

[觸發程序](../connectors/apis-list.md)是啟動邏輯應用程式工作流程的事件。 Webhook 觸發程序是以事件為基礎，這不需要依賴輪詢新的項目。 當您儲存邏輯應用程式 webhook 觸發程序，或當您從停用，以變更您的邏輯應用程式時啟用 webhook 觸發程序*訂閱*至指定的服務或端點註冊*回呼 URL*與該服務或端點。 觸發程序接著會使用該 URL 來執行必要的邏輯應用程式。 像是[要求觸發程序](connectors-native-reqres.md)，立即預期的事件發生時，就會引發邏輯應用程式。 觸發程序*取消訂閱*如果您移除觸發程序，並儲存邏輯應用程式，或當您變更您的邏輯應用程式，從啟用變成停用。

以下是如何在邏輯應用程式設計工具中設定 HTTP 觸發程序的範例。 這個步驟假設您已部署或正在存取的 API 會遵循[邏輯應用程式中的 webhook 訂閱和取消訂閱模式](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)。 

**新增 webhook 觸發程序**

1. 將 **HTTP Webhook** 觸發程序新增為邏輯應用程式中的第一個步驟。
2. 填入 webhook 訂閱和取消訂閱呼叫的參數。

   此步驟所遵循的模式與 [HTTP 動作](connectors-native-http.md)格式相同。

     ![HTTP 觸發程序](./media/connectors-native-webhook/using-trigger.png)

3. 加入至少一個動作。
4. 按一下 [儲存] 來發佈邏輯應用程式。 此步驟會呼叫含有觸發此邏輯應用程式所需的回呼 URL 訂閱端點。
5. 每當服務對回呼 URL 發出 `HTTP POST` 時，就會觸發邏輯應用程式，並包含要求中所傳遞的任何資料。

## <a name="use-the-webhook-action"></a>使用 webhook 動作

[*動作*](../connectors/apis-list.md)是定義作業和執行邏輯應用程式的工作流程。 當邏輯應用程式執行 webhook 動作，該動作*訂閱*至指定的服務或端點註冊*回呼 URL*與該服務或端點。 Webhook 動作接著會等到該服務的 URL，邏輯應用程式繼續執行之前呼叫。 從服務或端點，在這些情況下，取消訂閱的邏輯應用程式： 

* 已成功完成 webhook 動作
* 如果等候回應時取消執行邏輯應用程式
* 之前的邏輯應用程式逾時

例如， [**傳送核准電子郵件**](connectors-create-api-office365-outlook.md)動作會遵循此模式的 webhook 動作的範例。 您可以透過 webhook 動作將此模式擴充到任何服務。 

以下是如何在邏輯應用程式設計工具中設定 webhook 動作的範例。 這些步驟假設您已部署或正在存取的 API 會遵循[邏輯應用程式中的 webhook 訂閱和取消訂閱模式](../logic-apps/logic-apps-create-api-app.md#webhook-actions)。 

**若要新增 webhook 動作**

1. 選擇 [新增步驟] > [新增動作]。

2. 在搜尋方塊中，輸入 "webhook" 以找出 **HTTP Webhook** 動作。

    ![選取查詢動作](./media/connectors-native-webhook/using-action-1.png)

3. 填入 webhook 訂閱和取消訂閱呼叫的參數

   此步驟所遵循的模式與 [HTTP 動作](connectors-native-http.md)格式相同。

     ![完整查詢動作](./media/connectors-native-webhook/using-action-2.png)
   
   在執行階段中，邏輯應用程式會進行該步驟之後呼叫訂閱端點。

4. 按一下 [儲存] 來發佈邏輯應用程式。

## <a name="technical-details"></a>技術詳細資訊

以下是更多關於 webhook 支援的觸發程序和動作詳細資料。

## <a name="webhook-triggers"></a>WebHook 觸發程序

|  動作 | 描述 |
| --- | --- |
| HTTP Webhook |訂閱服務的回呼 URL，就能視需要呼叫該 URL 以觸發邏輯應用程式。 |

### <a name="trigger-details"></a>觸發程序詳細資料

#### <a name="http-webhook"></a>HTTP Webhook

訂閱服務的回呼 URL，就能視需要呼叫該 URL 以觸發邏輯應用程式。
代表必要欄位 * 。

| 顯示名稱 | 屬性名稱 | 描述 |
| --- | --- | --- |
| 訂閱方法* |method |用於訂閱要求的 HTTP 方法 |
| 訂閱 URI* |uri |用於訂閱要求的 HTTP URI |
| 取消訂閱方法* |method |用於取消訂閱要求的 HTTP 方法 |
| 取消訂閱 URI* |uri |用於取消訂閱要求的 HTTP URI |
| 訂閱本文 |body |訂閱的 HTTP 要求本文 |
| 訂閱標頭 |headers |訂閱的 HTTP 要求標頭 |
| 訂閱驗證 |驗證 |訂閱要使用的 HTTP 驗證。 如需詳細資訊，[請參閱 HTTP 連接器](connectors-native-http.md#authentication) |
| 取消訂閱頁面 |body |取消訂閱的 HTTP 要求本文 |
| 取消訂閱標頭 |headers |取消訂閱的 HTTP 要求標頭 |
| 取消訂閱驗證 |驗證 |取消訂閱要使用的 HTTP 驗證。 如需詳細資訊，[請參閱 HTTP 連接器](connectors-native-http.md#authentication) |

**輸出詳細資料**

Webhook 要求

| 屬性名稱 | 数据类型 | 描述 |
| --- | --- | --- |
| headers |物件 |Webhook 要求標頭 |
| body |物件 |Webhook 要求物件 |
| 狀態碼 |int |Webhook 要求狀態碼 |

## <a name="webhook-actions"></a>Webhook 動作

|  動作 | 描述 |
| --- | --- |
| HTTP Webhook |訂閱服務的回呼 URL，就能視需要呼叫繼續工作流程步驟的 URL。 |

### <a name="action-details"></a>動作詳細資料

#### <a name="http-webhook"></a>HTTP Webhook

訂閱服務的回呼 URL，就能視需要呼叫繼續工作流程步驟的 URL。
代表必要欄位 * 。

| 顯示名稱 | 屬性名稱 | 描述 |
| --- | --- | --- |
| 訂閱方法* |method |用於訂閱要求的 HTTP 方法 |
| 訂閱 URI* |uri |用於訂閱要求的 HTTP URI |
| 取消訂閱方法* |method |用於取消訂閱要求的 HTTP 方法 |
| 取消訂閱 URI* |uri |用於取消訂閱要求的 HTTP URI |
| 訂閱本文 |body |訂閱的 HTTP 要求本文 |
| 訂閱標頭 |headers |訂閱的 HTTP 要求標頭 |
| 訂閱驗證 |驗證 |訂閱要使用的 HTTP 驗證。 如需詳細資訊，[請參閱 HTTP 連接器](connectors-native-http.md#authentication) |
| 取消訂閱頁面 |body |取消訂閱的 HTTP 要求本文 |
| 取消訂閱標頭 |headers |取消訂閱的 HTTP 要求標頭 |
| 取消訂閱驗證 |驗證 |取消訂閱要使用的 HTTP 驗證。 如需詳細資訊，[請參閱 HTTP 連接器](connectors-native-http.md#authentication) |

**輸出詳細資料**

Webhook 要求

| 屬性名稱 | 数据类型 | 描述 |
| --- | --- | --- |
| headers |物件 |Webhook 要求標頭 |
| body |物件 |Webhook 要求物件 |
| 狀態碼 |int |Webhook 要求狀態碼 |

## <a name="next-steps"></a>後續步驟

* [建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [尋找其他連接器](apis-list.md)
