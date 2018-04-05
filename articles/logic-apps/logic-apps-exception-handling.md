---
title: Azure 中 Logic Apps 的錯誤和例外狀況處理 | Microsoft Docs
description: Logic Apps 中的錯誤和例外狀況處理模式。
services: logic-apps
documentationcenter: ''
author: dereklee
manager: anneta
editor: ''
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 01/31/2018
ms.author: deli; LADocs
ms.openlocfilehash: 70dd4e98dbffd9dac27752f0b4c2f5ce4ca70bdc
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>處理 Logic Apps 中的錯誤和例外狀況

適當地處理相依系統的停機情況或問題，對任何整合架構來說都是種挑戰。 為了建立穩固且有能力對抗問題與失敗的整合方案，Logic Apps 提供您處理錯誤和例外狀況的一流體驗。 

## <a name="retry-policies"></a>重試原則

對於大部分基本例外狀況和錯誤處理，您可以使用重試原則。 如果有逾時或失敗的初始要求，也就是任何造成 429 或 5xx 回應的要求，此原則會定義動作是否應該重試要求，以及如何重試。 

重試原則有四種：預設、無、固定間隔及指數間隔。 如果工作流程定義沒有重試原則，就會改用服務所定義的預設原則。

若要設定重試原則 (如果適用)，請開啟邏輯應用程式的邏輯應用程式設計工具，並移至邏輯應用程式中特定動作的 [設定]。 或者，針對工作流程定義中的特定動作或觸發程序，在 **inputs** 區段中定義重試原則 (如果可以重試)。 以下是一般語法：

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

如需有關語法和 **inputs** 區段的詳細資訊，請參閱[工作流程動作和觸發程序中的重試原則區段][retryPolicyMSDN]。 如需有關重試原則限制的資訊，請參閱 [Logic Apps 限制和設定](../logic-apps/logic-apps-limits-and-config.md)。 

### <a name="default"></a>預設值

如果您未在 **retryPolicy** 區段中定義重試原則，邏輯應用程式會使用預設原則，也就是[指數間隔原則](#exponential-interval)，此原則會以成指數增加的間隔 (以 7.5 秒作調整) 傳送最多四次重試。 間隔的最小值與最大值為 5 秒和 45 秒。 此原則等同於此 HTTP 工作流程定義範例中的原則：

```json
"HTTP": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "exponential",
            "count": 4,
            "interval": "PT7S",
            "minimumInterval": "PT5S",
            "maximumInterval": "PT1H"
        }
    },
    "runAfter": {}
}
```

### <a name="none"></a>None

如果您將 **retryPolicy** 參數設定為 **none**，則此原則不會重試失敗的要求。

| 元素名稱 | 必要 | 類型 | 說明 | 
| ------------ | -------- | ---- | ----------- | 
| type | yes | 字串 | 無 | 
||||| 

### <a name="fixed-interval"></a>固定間隔

如果將 **retryPolicy** 設定為 **fixed**，此原則就會先依據指定的時間間隔等候，然後才傳送下一個要求，來重試失敗的要求。

| 元素名稱 | 必要 | 類型 | 說明 |
| ------------ | -------- | ---- | ----------- |
| type | yes | 字串 | **fixed** |
| count | yes | 整數  | 重試次數必須介於 1 到 90 之間 | 
| interval | yes | 字串 | 重試間隔為 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，必須在 PT5S 與 PT1D 之間 | 
||||| 

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>指數間隔

如果將 **retryPolicy** 設定為 **exponential**，此原則就會在以指數方式成長範圍內，不定時重試失敗的要求。 原則也保證在大於 **minimumInterval** 和小於 **maximumInterval** 的隨機間隔中傳送每次重試嘗試。 指數原則需要 **count** 和 **interval**，而 **minimumInterval** 值和 **maximumInterval** 值是選擇性的。 如果您想要分別覆寫 PT5S 和 PT1D 預設值，您可以新增這些值。

| 元素名稱 | 必要 | 類型 | 說明 |
| ------------ | -------- | ---- | ----------- |
| type | yes | 字串 | **exponential** |
| count | yes | 整數  | 重試次數必須介於 1 到 90 之間  |
| interval | yes | 字串 | 重試間隔為 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，必須在 PT5S 與 PT1D 之間。 |
| minimumInterval | 否 | 字串 | 重試最小間隔為 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，必須介於 PT5S 與 **interval** 之間 |
| maximumInterval | 否 | 字串 | 重試最小間隔為 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，必須介於 **interval**與 PT1D 之間 | 
||||| 

此資料表說明如何針對每次重試 (最高可達 **count** (含))，產生所指範圍內的統一隨機變數：

**隨機變數範圍**

| 重試數目 | 最小間隔 | 最大間隔 |
| ------------ | ---------------- | ---------------- |
| 1 | 最大值 (0，**minimumInterval**) | 最小值 (時間間隔，**maximumInterval**) |
| 2 | 最大值 (時間間隔，**minimumInterval**) | 最小值 (2 * 間隔，**maximumInterval**) |
| 3 | 最大值 (2 * 間隔，**minimumInterval**) | 最小值 (4 * 間隔，**maximumInterval**) |
| 4 | 最大 (4 * 間隔，**minimumInterval**) | Min (8 * 間隔，**maximumInterval**) |
| .... | | | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>使用 RunAfter 屬性來擷取和處理失敗

每個邏輯應用程式動作都會在動作開始之前，宣告必須先完成的一些動作，就類似於在工作流程中指定步驟順序的方式。 在動作定義中，**runAfter** 屬性會定義此順序，並且是描述哪些動作和動作狀態會執行動作的物件。

根據預設，所有您在「邏輯應用程式設計工具」中新增的動作都會設定為在前一個步驟執行後才執行，但前提是前一個步驟結果為 **Succeeded (成功)**。 不過，您可以自訂 **runAfter** 值，讓動作可以在上一個動作結果為 **Failed (失敗)**、**Skipped (略過)**或這些值的一些組合時引發。 例如，若要在特定的 **Insert_Row** 動作失敗後，將項目新增至特定的服務匯流排主題，您可以使用此 **runAfter** 定義範例：

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

當 **Insert_Row** 動作狀態為 **Failed (失敗)** 時，**RunAfter** 屬性會設為執行。 若要在動作狀態為 **Succeeded**、**Failed** 或 **Skipped** 時執行動作，請使用此語法︰

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> 在前一個動作失敗後所執行並順利完成的動作會標示為 **Succeeded (成功)**。 此行為表示如果您成功擷取到工作流程中的所有失敗，該次執行本身會標示為 **Succeeded (成功)**。

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>以範圍和其結果評估動作

類似於使用 **runAfter** 屬性在個別動作之後執行步驟，您可以在 [scope (範圍)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) 內將動作群組在一起。 當您想要以邏輯方式將動作群組在一起、評估範圍的彙總狀態，並根據這些狀態執行動作時，即可使用範圍。 當範圍中的所有動作都執行完成之後，範圍本身會取得自己的狀態。 

若要檢查範圍的狀態，您可以使用檢查邏輯應用程式執行狀態時所用的相同準則，例如 **Succeeded (成功)**、**Failed (失敗)** 等。 

根據預設，當範圍的所有動作都成功時，範圍的狀態會標示為 **Succeeded (成功)**。 如果範圍內的最後動作結果為 **Failed (失敗)** 或 **Aborted (中止)**，範圍的狀態會標示 **Failed (失敗)**。 

若要攔截 **Failed (失敗)** 範圍的例外狀況，以及執行處理這些錯誤的動作，您可以針對該 **Failed (失敗)** 範圍使用 **runAfter** 屬性。 這樣一來，如果範圍內的「任何」動作失敗，而您對該範圍使用 **runAfter** 屬性，您便可建立單一動作來擷取失敗。

如需範圍的限制，請參閱[限制和設定](../logic-apps/logic-apps-limits-and-config.md)。

### <a name="get-context-and-results-for-failures"></a>取得失敗的內容和結果

雖然從範圍擷取失敗很實用，但還是建議您取得內容以協助解實際上有哪些動作失敗，以及所傳回的任何錯誤或狀態碼。 **@result()** 工作流程函式會提供範圍內所有動作結果的相關內容。

**@result()** 函式會接受單一參數 (範圍名稱)，並傳回該範圍內所有動作結果的陣列。 這些動作物件包含與 **@actions()** 物件相同的屬性，例如動作的開始時間、結束時間、狀態、輸入、相互關聯識別碼和輸出。 若要傳送在某個範圍內失敗之任何動作的內容，您可以輕鬆地將 **@result()** 函式與 **runAfter** 屬性配對。

若要針對範圍中結果為 **Failed** 的「每個」 動作執行動作，以及篩選結果陣列以顯示失敗的動作，您可以將 **@result()** 與**[篩選陣列](../connectors/connectors-native-query.md)** 動作及 **[ForEach](../logic-apps/logic-apps-control-flow-loops.md)** 迴圈配對。 您可以取得篩選後的結果陣列，並使用 **ForEach** 迴圈對每個失敗執行動作。 

以下範例 (隨後並有詳細說明) 會傳送 HTTP POST 要求，其中含有任何在 "My_Scope" 範圍內失敗之動作的回應本文。

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

以下是說明此範例中所發生情況的詳細逐步解說︰

1. 為了取得 "My_Scope" 內所有動作的結果，**篩選陣列**動作會篩選 **@result('My_Scope')**。

2. **篩選陣列**的條件是任何狀態等於 **Failed (失敗)**.的 **@result()** 項目。 此條件會將 "My_Scope" 所有動作結果的陣列篩選成只剩失敗動作結果的陣列。

3. 對篩選後陣列的輸出執行 **For Each** 迴圈動作。 此步驟會對之前篩選的每個失敗動作結果執行動作。

   如果範圍中的單一動作失敗，**foreach** 中的動作只會執行一次。 
   如果有多個失敗動作，則會導致對每個失敗執行一個動作。

4. 在 **foreach** 項目回應本文 \(**@item() ['outputs']['body']**\) 上傳送 HTTP POST。 **@result()** 項目圖形和 **@actions()** 圖形相同，並可透過相同方式剖析。

5. 包含兩個具有失敗動作名稱 **@item()['name']** 和失敗執行用戶端追蹤識別碼 **@item()['clientTrackingId']** 的自訂標頭。

供您參考，以下範例是單一 **@result()** 項目，會顯示前一個範例中剖析的 **name**、**body**和  **clientTrackingId** 屬性。 在 **foreach** 動作之外，**@result()** 會傳回這些物件的陣列。

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/folder-name/resource-name does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

若要執行不同的例外狀況處理模式，您可以使用本文前面描述的運算式。 您可以選擇在範圍外執行單一例外狀況處理動作，以接受整個篩選後的失敗陣列，並移除 **foreach** 動作。 您也可以包含 **@result()** 回應中的其他有用屬性，如先前所述。

## <a name="azure-diagnostics-and-telemetry"></a>Azure 診斷和遙測

上述模式非常適合處理執行內的錯誤和例外狀況，但您也可以識別和回應與執行本身無關的錯誤。 
[Azure 診斷](../logic-apps/logic-apps-monitor-your-logic-apps.md) 提供一個簡單的方法，可讓您將所有工作流程事件 (包括所有執行和動作狀態) 傳送至 Azure 儲存體帳戶或使用「Azure 事件中樞」建立的某個事件中樞。 

若要評估執行狀態，您可以監視記錄和度量，或將它們發佈至您偏好使用的任何監視工具。 其中一個可能的選項是透過「事件中樞」將所有事件串流到 [Azure 串流分析](https://azure.microsoft.com/services/stream-analytics/)。 在「串流分析」中，您可以根據診斷記錄檔中的任何異常、平均或失敗，撰寫即時查詢。 您可以使用「串流分析」將資訊傳送到其他資料來源，例如傳送到佇列、主題、SQL、Azure Cosmos DB 或 Power BI。

## <a name="next-steps"></a>後續步驟

* [看看客戶如何使用 Azure Logic Apps 建置錯誤處理](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [尋找其他 Logic Apps 範例和案例](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9