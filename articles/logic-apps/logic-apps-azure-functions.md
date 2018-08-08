---
title: 使用 Azure Functions 在 Azure Logic Apps 中新增和執行自訂程式碼 | Microsoft Docs
description: 了解如何使用 Azure Functions 在 Azure Logic Apps 中新增和執行自訂程式碼片段
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/25/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 20ad738541554279ff9fd6dd6babe90a38676c00
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263185"
---
# <a name="add-and-run-custom-code-snippets-in-azure-logic-apps-with-azure-functions"></a>使用 Azure Functions 在 Azure Logic Apps 中新增和執行自訂程式碼片段

當您想要建立和執行剛剛好能夠解決邏輯應用程式中特定問題的程式碼時，您可以使用 [Azure Functions](../azure-functions/functions-overview.md) 建立自己的函式。 此服務可讓您在邏輯應用程式中建立和執行以 Node.js 或 C# 所撰寫的自訂程式碼片段，而不必煩惱要如何建立整個應用程式或基礎結構以便執行程式碼。 Azure Functions 可提供在雲端進行的無伺服器運算，適合用來執行以下舉例的各種工作：

* 使用 Node.js 或 C# 所支援的函式來擴充邏輯應用程式的行為。
* 在邏輯應用程式工作流程中執行計算。
* 在邏輯應用程式中套用進階格式設定或計算欄位。

您也可以[從 Azure 函式內呼叫邏輯應用程式](#call-logic-app)。

## <a name="prerequisites"></a>必要條件

若要依照本文進行操作，以下是您需要的項目：

* 如果您還沒有 Azure 訂用帳戶，請先<a href="https://azure.microsoft.com/free/" target="_blank">註冊免費的 Azure 帳戶</a>。 

* 要在其中新增函式的邏輯應用程式

  如果您還不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 作為邏輯應用程式中第一個步驟的[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts) 

  邏輯應用程式必須以觸發程序作為開頭，才能新增用於執行函式的動作。

* Azure 函式應用程式 (也就是，Azure 函式的容器) 和 Azure 函式。 如果您沒有函式應用程式，則必須[先建立函式應用程式](../azure-functions/functions-create-first-azure-function.md)。 然後，您可以在邏輯應用程式設計工具中建立函式，不論是要[在邏輯應用程式外另外](#create-function-external)建立，還是要[從邏輯應用程式內部](#create-function-designer)建立都行。

  新的和現有的 Azure 函式應用程式與函式對於使用邏輯應用程式有相同的需求：

  * 函式應用程式必須屬於和邏輯應用程式相同的 Azure 訂用帳戶。

  * 函式必須使用適用於 **JavaScript** 或 **C#** 的**一般 Webhook** 函式範本。 此範本可以接受內容中有來自邏輯應用程式的 `application/json` 類型。 當您將使用這些範本所建立的自訂函式新增至邏輯應用程式時，這些範本也有助於邏輯應用程式設計工具尋找和顯示這些函式。

  * 確認函式範本的 [模式] 屬性已設定為 [Webhook]，[Webhook 類型] 屬性已設定為 [一般 JSON]。

    1. 登入 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>。
    2. 在主要 Azure 功能表上，選取 [函式應用程式]。 
    3. 在 [函式應用程式] 清單中，選取您的函式應用程式，展開該函式，然後選取 [整合]。 
    4. 確認範本的 [模式] 屬性已設定為 [Webhook]，[Webhook 類型] 屬性已設定為 [一般 JSON]。 

  * 如果函式有 [API 定義](../azure-functions/functions-openapi-definition.md) (先前稱為 [Swagger 檔案](http://swagger.io/))，邏輯應用程式設計工具會提供更豐富的函式參數使用體驗。 
  邏輯應用程式若要尋找和存取具有 Swagger 描述的函式，必須先[遵循下列步驟來設定函式應用程式](#function-swagger)。

<a name="create-function-external"></a>

## <a name="create-functions-outside-logic-apps"></a>在邏輯應用程式外部建立函式

在 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>中建立 Azure 函式應用程式 (必須有和邏輯應用程式相同的 Azure 訂用帳戶)，然後建立 Azure 函式。 如果您還不熟悉 Azure Functions，請了解如何[在 Azure 入口網站中建立您的第一個函式](../azure-functions/functions-create-first-azure-function.md)，但請注意下列需求，滿足後才能建立 Azure 函式以供新增和從邏輯應用程式呼叫。

* 確定您有選取適用於 **JavaScript** 或 **C#** 的**一般 Webhook** 函式範本。

  ![一般 Webhook - JavaScript 或 C#](./media/logic-apps-azure-functions/generic-webhook.png)

* 建立 Azure 函式之後，確認範本的 [模式] 和 [Webhook 類型] 屬性已正確設定。

  1. 在 [函式應用程式] 清單中展開該函式，然後選取 [整合]。 

  2. 確認範本的 [模式] 屬性已設定為 [Webhook]，[Webhook 類型] 屬性已設定為 [一般 JSON]。 

     ![函式範本的 [整合] 屬性](./media/logic-apps-azure-functions/function-integrate-properties.png)

<a name="function-swagger"></a>

* (選擇性) 如果您為函式[產生 API 定義](../azure-functions/functions-openapi-definition.md) (先前稱為 [Swagger 檔案](http://swagger.io/))，當您在邏輯應用程式設計工具中使用函式參數時將可獲得更豐富的體驗。 若要設定函式應用程式，讓邏輯應用程式可以尋找和存取具有 Swagger 描述的函式：

  * 確定函式應用程式正在執行。

  * 在函式應用程式中設定[跨原始資源共用 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)，以允許所有來源：

    1. 從 [函式應用程式] 清單開始，選取您的函式應用程式 > [平台功能] > [CORS]。

       ![選取您的函式應用程式 > [平台功能] > [CORS]](./media/logic-apps-azure-functions/function-platform-features-cors.png)

    2. 在 [CORS] 底下新增 `*` 萬用字元，但在清單中移除所有其他來源，然後選擇 [儲存]。

       ![選取您的函式應用程式 > [平台功能] > [CORS]](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

### <a name="access-property-values-inside-http-requests"></a>存取 HTTP 要求內的屬性值

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

2. 此函式現在可以透過 `data` 變數存取 `date` 屬性，並藉由呼叫 `ToDateString()` 函式將該屬性值從 DateTime 類型轉換為 DateString 類型。 此函式也會透過函式回應中的 `body` 屬性傳回結果： 

   ```javascript
   body: data.date.ToDateString();
   ```

您已建立 Azure 函式，接下來請遵循如何[將函式新增至邏輯應用程式](#add-function-logic-app)的步驟。

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>在邏輯應用程式內部建立函式

若要在邏輯應用程式設計工具中從邏輯應用程式內部開始建立 Azure 函式，您必須先擁有 Azure 函式應用程式，以作為函式的容器。 如果您沒有函式應用程式，請先建立該函式應用程式。 請參閱[在 Azure 入口網站中建立您的第一個函式](../azure-functions/functions-create-first-azure-function.md)。 

1. 在 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>的邏輯應用程式設計工具中，開啟邏輯應用程式。 

2. 在想要建立和新增函式的步驟底下，選擇 [新增步驟] > [新增動作]。 

3. 在搜尋方塊中，輸入「azure functions」作為篩選條件。
從動作清單中選取此動作：**選擇 Azure 函式 – Azure Functions** 

   ![尋找 "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. 從函式應用程式清單中，選取您的函式應用程式。 動作清單開啟後，請選取此動作：**Azure Functions - 建立新函式**

   ![選取函式應用程式](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. 在函式定義編輯器中，定義您的函式：

   1. 在 [函式名稱] 方塊中，為您的函式提供名稱。 

   2. 在 [程式碼] 方塊中，於範本內新增函式程式碼，包括要在函式執行完成後傳回給邏輯應用程式的回應和承載。 
   範本程式碼中的內容物件會描述邏輯應用程式傳遞給函式的訊息和內容，例如：

      ![定義函式](./media/logic-apps-azure-functions/function-definition.png)

      在函式內部，您可以使用下列語法來參考內容物件中的屬性：

      ```text
      context.<token-name>.<property-name>
      ```
      針對此範例，以下是您會使用的語法：

      ```text
      context.body.content
      ```

   3. 完成之後，請選擇 [建立]。

6. 在 [要求本文] 方塊中，指定要傳遞作為函式輸入的內容物件 (必須以 JavaScript 物件標記法 (JSON) 進行格式化)。 當您在 [要求本文] 方塊內按一下時，會開啟動態內容清單，以供您選取前面步驟所提供屬性的權杖。 

   這個範例會在來自電子郵件觸發程序的 [本文] 權杖中傳遞物件：  

   ![「要求本文」範例 - 內容物件承載](./media/logic-apps-azure-functions/function-request-body-example.png)

   根據內容物件中的內容，邏輯應用程式設計工具會產生函式範本供您之後進行內嵌編輯。 
   Logic Apps 也會根據輸入內容物件建立變數。

   在此範例中，內容物件不會轉換為字串，所以內容會直接新增至 JSON 承載。 
   不過，如果物件不是 JSON 權杖 (必須是字串、JSON 物件或 JSON 陣列)，您就會收到錯誤。 
   若要將內容物件轉換為字串，請新增雙引號，例如：

   ![將物件轉換為字串](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

7. 若要指定其他詳細資料 (例如，要使用的方法、要求標頭或查詢參數)，請選擇 [顯示進階選項]。

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>在邏輯應用程式中新增現有函式

若要從邏輯應用程式呼叫現有 Azure 函式，您可以在邏輯應用程式設計工具中新增 Azure 函式，例如任何其他動作。 

1. 在 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>的邏輯應用程式設計工具中，開啟邏輯應用程式。 

2. 在想要新增函式的步驟底下，選擇 [新增步驟] > [新增動作]。 

3. 在搜尋方塊中，輸入「azure functions」作為篩選條件。
從動作清單中選取此動作：**選擇 Azure 函式 – Azure Functions** 

   ![尋找 "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. 從函式應用程式清單中，選取您的函式應用程式。 函式清單出現後，選取您的函式。 

   ![選取函式應用程式和 Azure 函式](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   函式若具有 API 定義 (Swagger 描述)，並已[進行設定以讓邏輯應用程式可以尋找和存取這些函式](#function-swagger)，您就可以選取 [Swagger 動作]：

   ![選取函式應用程式、「Swagger 動作」和 Azure 函式](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

5. 在 [要求本文] 方塊中，指定要傳遞作為函式輸入的內容物件 (必須以 JavaScript 物件標記法 (JSON) 進行格式化)。 此內容物件會描述邏輯應用程式傳送給函式的訊息和內容。 

   當您在 [要求本文] 方塊內按一下時，會開啟動態內容清單，以供您選取前面步驟所提供屬性的權杖。 
   這個範例會在來自電子郵件觸發程序的 [本文] 權杖中傳遞物件：

   ![「要求本文」範例 - 內容物件承載](./media/logic-apps-azure-functions/function-request-body-example.png)

   在此範例中，內容物件不會轉換為字串，所以內容會直接新增至 JSON 承載。 
   不過，如果物件不是 JSON 權杖 (必須是字串、JSON 物件或 JSON 陣列)，您就會收到錯誤。 
   若要將內容物件轉換為字串，請新增雙引號，例如：

   ![將物件轉換為字串](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. 若要指定其他詳細資料 (例如，要使用的方法、要求標頭或查詢參數)，請選擇 [顯示進階選項]。

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>從函式呼叫邏輯應用程式

若要從 Azure 函式內觸發邏輯應用程式，該邏輯應用程式必須具有可呼叫的端點，更具體地說，要具有**要求**觸發程序。 然後，從函式內部將 HTTP POST 要求傳送給該**要求**觸發程序的 URL，並包含您想要讓該邏輯應用程式處理的承載。 如需詳細資訊，請參閱[呼叫、觸發或巢狀邏輯應用程式](../logic-apps/logic-apps-http-endpoint.md)。 

## <a name="get-support"></a>取得支援

* 如有問題，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交或票選功能構想，請造訪 [Logic Apps 使用者意見反應網站](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>後續步驟

* 了解[Logic Apps 連接器](../connectors/apis-list.md)
