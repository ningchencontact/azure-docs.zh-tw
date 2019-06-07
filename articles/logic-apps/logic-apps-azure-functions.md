---
title: 新增並從 Azure Logic Apps 呼叫 Azure 函式
description: 新增 Azure 函數並執行從邏輯應用程式
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.topic: article
ms.date: 06/04/2019
ms.reviewer: klam, LADocs
ms.openlocfilehash: 524b927ec0966199c51cdee93e920d7b847139ae
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495150"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>從 Azure Logic Apps 呼叫 Azure 函式

當您想要執行邏輯應用程式中執行特定工作的程式碼時，您可以使用來建立您自己的函式[Azure Functions](../azure-functions/functions-overview.md)。 這項服務可協助您建立 Node.js C#，和F#函式，因此您不必建置完整的應用程式或基礎結構，以執行程式碼。 您也可以[從 Azure 函式內呼叫邏輯應用程式](#call-logic-app)。 Azure Functions 可提供在雲端進行的無伺服器運算，適合用來執行以下舉例的各種工作：

* 使用 Node.js 或 C# 中的函式來擴充邏輯應用程式的行為。
* 在邏輯應用程式工作流程中執行計算。
* 在邏輯應用程式中套用進階格式設定或計算欄位。

若要執行程式碼片段，而不需建立 Azure 函式，了解如何[加上執行的內嵌程式碼](../logic-apps/logic-apps-add-run-inline-code.md)。

> [!NOTE]
> Logic Apps 與 Azure Functions 之間整合目前不適用於已啟用的位置。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* Azure 函數應用程式，是 Azure functions，以及您的 Azure 函式的容器。 如果您沒有函式應用程式，[請先建立該函式應用程式](../azure-functions/functions-create-first-azure-function.md)。 然後您可以建立您的函式外部邏輯應用程式在 Azure 入口網站中，或是[從您的邏輯應用程式內](#create-function-designer)邏輯應用程式設計工具中。

* 當使用邏輯應用程式，不論是現有或新函式應用程式和函式適用於相同的需求：

  * 您的函式應用程式和邏輯應用程式必須使用相同的 Azure 訂用帳戶。

  * 新的函式應用程式必須使用.NET 或 JavaScript 執行階段堆疊。 當您將新的函式加入現有的函式應用程式時，您可以選取C#或 JavaScript。

  * 您的函式會使用**HTTP 觸發程序**範本。

    HTTP 觸發程序範本可以接受內容中有來自邏輯應用程式的 `application/json` 類型。 當您新增 Azure 函式在邏輯應用程式時，邏輯應用程式設計工具會顯示您的 Azure 訂用帳戶內的這個範本建立的自訂函式。

  * 您的函式不使用自訂的路由，除非您已定義[OpenAPI 定義](../azure-functions/functions-openapi-definition.md)(先前稱為[Swagger 檔案](https://swagger.io/))。

  * 如果您沒有 OpenAPI 定義您的函式時，Logic Apps 設計工具可以讓您更豐富體驗您的工作函式參數。 邏輯應用程式若要尋找和存取具有 OpenAPI 定義的函式，必須先[遵循下列步驟來設定函式應用程式](#function-swagger)。

* 您要在其中新增函式的邏輯應用程式，並包括作為邏輯應用程式中第一個步驟的[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)

  您可以加入執行函式的動作之前，您的邏輯應用程式必須啟動以觸發程序。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>尋找具有 OpenAPI 描述函式

您使用 Logic Apps 設計工具中的函式參數時，更豐富的體驗[產生 OpenAPI 定義](../azure-functions/functions-openapi-definition.md)，先前稱為[Swagger 檔案](https://swagger.io/)，您的函式。 若要設定函式應用程式，讓邏輯應用程式可以尋找和使用具有 Swagger 描述的函式，請遵循下列步驟：

1. 請確定您的函式應用程式正在執行。

1. 在您的函式應用程式，設定[跨原始資源共用 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)以便允許所有來源遵循下列步驟：

   1. 從**函式應用程式**清單中，選取您的函式應用程式。 在右窗格中，選取**平台功能** > **CORS**。

      ![選取您的函式應用程式 > [平台功能] > [CORS]](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. 底下**CORS**，加入星號 ( **`*`** ) 萬用字元字元，但在清單中，移除所有其他來源，然後選擇**儲存**。

      ![將 "CORS* 設為萬用字元 "*"](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>存取 HTTP 要求內的屬性值

Webhook 函式可以透過輸入接受 HTTP 要求，並將這些要求傳遞給其他函式。 例如，雖然 Logic Apps 有[會轉換日期時間值的函式](../logic-apps/workflow-definition-language-functions-reference.md)，但這個基本範例 JavaScript 函式會說明如何存取傳遞給函式的要求物件內所含的屬性，並對該屬性值執行作業。 為了存取物件內的屬性，這個範例使用[點 (.) 運算子](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)：

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

以下是此函式內部的運作情形：

1. 函式會建立 `data` 變數，並將 `request` 物件內的 `body` 物件指派給該變數。 此函式會使用點 (.) 運算子來參考 `request` 物件內的 `body` 物件：

   ```javascript
   var data = request.body;
   ```

1. 此函式現在可以透過 `data` 變數存取 `date` 屬性，並藉由呼叫 `ToDateString()` 函式將該屬性值從 DateTime 類型轉換為 DateString 類型。 此函式也會透過函式回應中的 `body` 屬性傳回結果：

   ```javascript
   body: data.date.ToDateString();
   ```

您已建立 Azure 函式，接下來請遵循如何[將函式新增至邏輯應用程式](#add-function-logic-app)的步驟。

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>在邏輯應用程式內部建立函式

您可以建立從邏輯應用程式內開始使用邏輯應用程式設計工具的 Azure 函式之前，您必須先為您的函式容器的 Azure 函式應用程式。 如果您沒有函式應用程式，請先建立該函式應用程式。 請參閱[在 Azure 入口網站中建立您的第一個函式](../azure-functions/functions-create-first-azure-function.md)。

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 若要建立並新增函式，請遵循適用於您案例的步驟：

   * 在邏輯應用程式工作流程的最後一個步驟中，選擇 [新增步驟]  。

   * 在邏輯應用程式工作流程的現有步驟之間，將滑鼠移到箭號，並選擇加號 (+)，然後選取 [新增動作]  。

1. 在搜尋方塊中，輸入「azure functions」作為篩選條件。 從 [動作] 清單中，選取此動作：**選擇 Azure 函式 – Azure Functions**

   ![尋找 "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. 從函式應用程式清單中，選取您的函式應用程式。 [動作] 清單開啟後，請選取此動作：**Azure 函式-建立新的函式**

   ![選取函式應用程式](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. 在函式定義編輯器中，定義您的函式：

   1. 在 [函式名稱]  方塊中，為您的函式提供名稱。

   1. 在 **程式碼**方塊中，加入您的函式執行完成之後，邏輯應用程式返回函式樣板，包括回應和您想要的裝載程式碼。

      ![定義函式](./media/logic-apps-azure-functions/function-definition.png)

      在範本的程式碼中，`context` 物件  代表邏輯應用程式透過**要求本文** (位於後面的步驟中) 欄位傳送的訊息。 若要從您的函式內存取 `context` 物件的屬性，請使用此語法：

      `context.body.<property-name>`

      例如，若要參考 `context` 物件內的 `content` 屬性，請使用此語法： 

      `context.body.content`

      範本程式碼也包含 `input` 變數，其中儲存著來自 `data` 參數的值，因此函式可以根據該值執行作業。 在 JavaScript 函數中，`data` 變數也是 `context.body` 的捷徑。

      > [!NOTE]
      > 這裡的 `body` 屬性適用於 `context` 物件，與動作輸出中可能也會傳遞給函式的**本文**權杖不同。

   1. 完成之後，請選擇 [建立]  。

1. 在 [要求本文]  方塊中，提供函式的輸入 (必須以 JavaScript 物件標記法 (JSON) 物件進行格式化)。

   此輸入是邏輯應用程式傳送給函式的「內容物件」  或訊息。 當您在 [要求本文]  欄位內按一下時，動態內容清單會隨即出現，讓您可以選取前面步驟中的輸出權杖。 此範例會指明內容承載包含名為 `content` 的屬性，而此屬性具有電子郵件觸發程序中的**寄件者**權杖：

   ![「要求本文」範例 - 內容物件承載](./media/logic-apps-azure-functions/function-request-body-example.png)

   在此處，內容物件不會轉換為字串，所以物件內容會直接新增至 JSON 承載。 不過，如果內容物件不是傳遞字串、JSON 物件或 JSON 陣列的 JSON 權杖，您就會收到錯誤。 因此，如果此範例改為使用**接收時間**權仗，則您可以在內容物件上加上雙引號，將內容物件轉換為字串：  

   ![將物件轉換為字串](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. 若要指定其他詳細資料，例如要使用、 要求標頭或查詢參數的方法，請開啟**加入新參數**清單，然後選取您想要的選項。

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>在邏輯應用程式中新增現有函式

若要從邏輯應用程式呼叫現有 Azure 函式，您可以在邏輯應用程式設計工具中新增 Azure 函式，例如任何其他動作。

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 在您要新增函式的步驟中，選擇**新的步驟**，然後選取**新增動作**。

1. 在搜尋方塊中，輸入「azure functions」作為篩選條件。 從 [動作] 清單中，選取此動作：**選擇 Azure 函式 – Azure Functions**

   ![尋找 "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. 從函式應用程式清單中，選取您的函式應用程式。 函式清單出現後，選取您的函式。

   ![選取函式應用程式和 Azure 函式](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   函式若具有 API 定義 (Swagger 描述)，並已[進行設定以讓邏輯應用程式可以尋找和存取這些函式](#function-swagger)，您就可以選取 [Swagger 動作]  ：

   ![選取函式應用程式、「Swagger 動作」和 Azure 函式](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. 在 [要求本文]  方塊中，提供函式的輸入 (必須以 JavaScript 物件標記法 (JSON) 物件進行格式化)。

   此輸入是邏輯應用程式傳送給函式的「內容物件」  或訊息。 當您按一下在**要求本文** 欄位中，動態內容清單隨即出現，讓您可以選取先前步驟中的語彙基元的輸出。 此範例會指明內容承載包含名為 `content` 的屬性，而此屬性具有電子郵件觸發程序中的**寄件者**權杖：

   ![「要求本文」範例 - 內容物件承載](./media/logic-apps-azure-functions/function-request-body-example.png)

   在此處，內容物件不會轉換為字串，所以物件內容會直接新增至 JSON 承載。 不過，如果內容物件不是傳遞字串、JSON 物件或 JSON 陣列的 JSON 權杖，您就會收到錯誤。 因此，如果此範例改為使用**接收時間**權仗，則您可以在內容物件上加上雙引號，將內容物件轉換為字串：

   ![將物件轉換為字串](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. 若要指定其他詳細資料，例如要使用、 要求標頭或查詢參數的方法，請開啟**加入新參數**清單，然後選取您想要的選項。

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>從 Azure functions 呼叫邏輯應用程式

若要從 Azure 函式內觸發邏輯應用程式，該邏輯應用程式必須以能提供可呼叫端點的觸發程序作為開頭。 例如，您可以使用 **HTTP**、**要求**、**Azure 佇列**或**事件方格**觸發程序來起始邏輯應用程式。 在函式內部，將 HTTP POST 要求傳送給該觸發程序的 URL，並包含您想要讓該邏輯應用程式處理的承載。 如需詳細資訊，請參閱[呼叫、觸發或巢狀邏輯應用程式](../logic-apps/logic-apps-http-endpoint.md)。

## <a name="next-steps"></a>後續步驟

* 了解[Logic Apps 連接器](../connectors/apis-list.md)
