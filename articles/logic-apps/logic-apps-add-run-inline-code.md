---
title: 新增並執行程式碼片段-Azure Logic Apps
description: 新增並使用 Azure Logic Apps 中的內嵌程式碼執行程式碼片段
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: derek1ee, LADocs
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: 0bfa98396ee3afb80b486a5a17959664dfbe603c
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602112"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>新增並執行 Azure Logic Apps 中使用內嵌程式碼的程式碼片段

當您想要執行的邏輯應用程式內的程式碼片段時，您可以加入內建**內嵌程式碼**邏輯應用程式的工作流程中步驟的動作。 當您想要執行程式碼適合這種情況下，此動作的效果最佳：

* 在 JavaScript 中執行。 即將推出更多的語言。
* 在五秒或更少執行的完成。
* 處理資料最多 50 MB 的大小。
* 使用 Node.js 版本 8.11.1。 如需詳細資訊，請參閱 <<c0> [ 標準的內建物件](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)。 

  > [!NOTE]
  > Require （） 函式不支援**內嵌程式碼**執行 JavaScript 的動作。

此動作會執行程式碼片段，並從該程式碼片段會傳回輸出，作為名為權杖**結果**，讓您可以在邏輯應用程式中用於後續的動作。 如需其他案例中，您要建立您的程式碼的函式，請嘗試[建立，然後呼叫 Azure 函式](../logic-apps/logic-apps-azure-functions.md)邏輯應用程式中。

在本文中，範例邏輯應用程式時，會觸發新的電子郵件送達 Office 365 Outlook 帳戶。 程式碼片段會擷取，並在電子郵件內文中會傳回任何顯示的電子郵件地址。

![範例概觀](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您要加入您的程式碼片段，包括觸發程序的邏輯應用程式。 如果您沒有在邏輯應用程式，請參閱[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

   本主題中的範例邏輯應用程式會使用此 Office 365 Outlook 觸發程序：**新的電子郵件送達時**

* [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)連結到邏輯應用程式

## <a name="add-inline-code"></a>加入內嵌程式碼

1. 如果您還沒有這麼做，在[Azure 入口網站](https://portal.azure.com)，在邏輯應用程式設計工具開啟邏輯應用程式。

1. 在設計工具中，加入**內嵌程式碼**動作位於您想要在邏輯應用程式的工作流程中的位置。

   * 若要在您的工作流程結尾處新增動作，請選擇**新增步驟**。

   * 若要新增的現有步驟之間的動作，將滑鼠指標移到連接這些步驟的箭號上。 選擇加號 (**+**)，然後選取**新增動作**。

   這個範例會新增**內嵌程式碼**下的 Office 365 Outlook 觸發程序的動作。

   ![新增新的步驟](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. 底下**選擇動作**，在 [搜尋] 方塊中，輸入 「 內嵌程式碼 」 作為篩選條件。 從 [動作] 清單中，選取此動作：**執行 JavaScript 程式碼**

   ![選取 「 執行 JavaScript 程式碼 」](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   動作會出現在設計工具，並包含一些預設範例程式碼，包括 return 陳述式。

   ![使用預設範例程式碼的內嵌程式碼動作](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. 在 **程式碼**，刪除的範例程式碼，並輸入您想要執行的程式碼。 撰寫程式碼，您會放置在方法中，但未定義的方法簽章。 

   當您輸入可辨識的關鍵字時，自動完成清單隨即出現，讓您可以從選取可用的關鍵字，例如：

   ![關鍵字自動完成清單](./media/logic-apps-add-run-inline-code/auto-complete.png)

   此範例程式碼片段會先建立變數以存放*規則運算式*，指定要在輸入文字中比對模式。 程式碼，然後建立變數以存放從觸發程序的電子郵件內文資料。

   ![建立變數](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   若要讓觸發程序和先前動作的結果更輕鬆地參考，動態內容清單會顯示當游標位於**程式碼** 方塊中。 此範例中，此清單會顯示可用的結果，從觸發程序，包括**主體**權杖，您現在可以選取。

   選取後**主體**會解析的語彙基元，內嵌程式碼動作以語彙基元`workflowContext`物件，參考的電子郵件`Body`屬性值：

   ![選取結果](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   在 [**程式碼**] 方塊中，程式碼片段可以使用唯讀`workflowContext`物件做為輸入。 此物件具有子屬性，讓您的程式碼存取權的結果可以從觸發程序和工作流程中的上一個動作。
   如需詳細資訊，請參閱本節稍後在本主題中：[參考程式碼中的觸發程序和動作結果](#workflowcontext)。

   > [!NOTE]
   >
   > 如果您的程式碼片段參考使用點 （.） 運算子的動作名稱，您必須加入這些動作名稱[**動作**參數](#add-parameters)。 這些參考必須也將動作名稱使用方括號 ([]) 和引號，例如：
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   不需要的內嵌程式碼動作`return`陳述式，但是所得`return`陳述式可供透過後續動作中參考**結果**語彙基元。 
   比方說，程式碼片段會傳回結果藉由呼叫`match()`函式，電子郵件內文，比對規則運算式中的哪一個尋找比對。 **Compose**動作會使用**結果**權杖來參考內嵌的結果動作的程式碼，並建立單一結果。

   ![完成的邏輯應用程式](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. 完成後，儲存邏輯應用程式。

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>在您的程式碼中的參考觸發程序和動作結果

`workflowContext`物件具有此結構中，其中包括`actions`， `trigger`，和`workflow`個子屬性：

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

下表包含這些子屬性的詳細資訊：

| 屬性 | 類型 | 說明 |
|----------|------|-------|
| `actions` | 物件集合 | 從您的程式碼片段執行前執行的動作結果物件。 每個物件都*鍵值*配對，其中索引鍵是動作的名稱，而此值相當於呼叫[actions() 函式](../logic-apps/workflow-definition-language-functions-reference.md#actions)使用`@actions('<action-name>')`。 動作的名稱會使用相同的動作名稱，用於基礎的工作流程定義，這會取代空格 ("") 中動作名稱以底線 (_)。 從目前執行的工作流程執行個體，這個物件會提供存取至動作的屬性值。 |
| `trigger` | Object | 從觸發程序和對等項目呼叫的結果物件[trigger() 函式](../logic-apps/workflow-definition-language-functions-reference.md#trigger)。 從目前執行的工作流程執行個體，這個物件會提供存取至觸發程序的屬性值。 |
| `workflow` | Object | 工作流程物件，相當於呼叫[workflow （） 函式](../logic-apps/workflow-definition-language-functions-reference.md#workflow)。 這個物件會提供存取至工作流程的屬性值，例如工作流程名稱、 執行的識別碼，並依此類推，從目前執行的工作流程執行個體。 |
|||

在本主題的範例中，`workflowContext`物件具有您的程式碼可以存取這些屬性：

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>新增參數

在某些情況下，您可能需要明確地**內嵌程式碼**動作包括從您的程式碼參考做為相依性，藉由新增觸發程序或特定動作的結果**觸發程序**或**動作**參數。 此選項可用於不在執行階段找到參考的結果的案例。

> [!TIP]
> 如果您計劃重複使用程式碼，將參考加入至屬性使用**程式碼**方塊，讓您的程式碼包含解析的語彙基元參考，而不是將觸發程序或動作新增為明確的相依性。

例如，假設您有參考的程式碼**SelectedOption**因**傳送核准電子郵件**Office 365 Outlook 連接器的動作。 在建立時，Logic Apps 引擎會分析您的程式碼，來決定您所提及的任何觸發程序或動作結果，並會自動包含這些結果。 在執行階段，應該就會發生錯誤，參考的觸發程序或動作結果不提供指定`workflowContext`物件時，您可以將該觸發程序或動作新增為明確的相依性。 在此範例中，您將新增**動作**參數並指定**內嵌程式碼**動作明確包含的結果**傳送核准電子郵件**動作。

若要加入這些參數，請開啟**加入新參數**清單，然後選取您想要的參數：

   ![新增參數](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | 參數 | 說明 |
   |-----------|-------------|
   | **動作** | 包含來自先前動作的結果。 請參閱[包含動作結果](#action-results)。 |
   | **觸發程序** | 包含來自觸發程序的結果。 請參閱[包含觸發程序結果](#trigger-results)。 |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>包含觸發程序的結果

如果您選取**觸發程序**，系統會提示您是否包含觸發程序結果。

* 從**觸發程序**清單中，選取**是**。

<a name="action-results"></a>

### <a name="include-action-results"></a>包含動作的結果

如果您選取**動作**，系統會提示您針對您想要新增的動作。 不過，啟動 新增動作之前，您會需要的版本會出現在邏輯應用程式的基礎工作流程定義的動作名稱。

* 這項功能不支援變數、 迴圈及反覆項目索引。

* 在邏輯應用程式的工作流程定義中的名稱使用底線 (_)，不空格。

* 如需使用點運算子 （.） 的動作名稱，包含這些運算子，例如：

  `My.Action.Name`

1. 在設計工具的工具列中，選擇**程式碼檢視**，並搜尋內`actions`動作名稱的屬性。

   例如，`Send_approval_email_`的 JSON 名稱**傳送核准電子郵件**動作。

   ![在 JSON 中尋找動作名稱](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. 若要返回設計工具檢視，在程式碼檢視 工具列中，選擇**設計工具**。

1. 若要新增的第一個動作，在**動作項目-1**方塊中，輸入動作的 JSON 名稱。

   ![輸入第一個動作](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. 若要新增另一個動作，請選擇**加入新項目**。

## <a name="reference"></a>參考

如需詳細資訊**執行的 JavaScript 程式碼**動作的結構和語法，在邏輯應用程式的基礎工作流程定義中使用工作流程定義語言中，請參閱此動作的[參考區段](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code).

## <a name="next-steps"></a>後續步驟

深入了解[適用於 Azure Logic Apps 的連接器](../connectors/apis-list.md)
