---
title: "Azure 中 Logic Apps 的錯誤和例外狀況處理 | Microsoft Docs"
description: "Logic Apps 中的錯誤和例外狀況處理模式。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: derek1ee
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; deli
ms.openlocfilehash: a74c7d18306359c9152f139299de1208b5932fe5
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2017
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>處理 Logic Apps 中的錯誤和例外狀況

Azure 中的 Logic Apps 提供豐富的工具和模式，可協助確保您的整合健全且具有復原能力來應付故障情況。 任何整合架構都會帶來適當處理來自相依系統之停機情況或問題的挑戰。 Logic Apps 可讓處理錯誤變成頂級的體驗。 它提供您所需的工具，以根據工作流程中的例外狀況和錯誤採取動作。

## <a name="retry-policies"></a>重試原則

重試原則是最基本的例外狀況和錯誤處理類型。 如果初始要求逾時或失敗 (任何造成 429 或 5xx 回應的要求)，重試原則會定義是否及如何重試動作。 

重試原則有四種：預設、無、固定間隔及指數間隔。 如果工作流程定義中未提供重試原則，就會使用服務所定義的預設原則。 

您可以在特定動作或觸發程序 (如果可重試) 的*輸入*中設定重試原則。 同樣地，您也可以在「邏輯應用程式設計工具」中設定重試原則 (如果適用)。 若要設定重試原則，請在「邏輯應用程式設計工具」中，移至特定動作的 [設定]。

如需有關重試原則限制的資訊，請參閱 [Logic Apps 限制和設定](../logic-apps/logic-apps-limits-and-config.md)。 如需有關支援之語法的詳細資訊，請參閱[工作流程動作和觸發程序中的重試原則區段][retryPolicyMSDN]。

### <a name="default"></a>預設值

如果您沒有定義重試原則 (未定義 **retryPolicy**)，則會使用預設原則。 預設原則是一個指數間隔原則，會傳送最多 4 次重試，以指數方式依 7.5 秒調整增加間隔。 此間隔上限為 5 到 45 秒之間。 此預設原則等同於此範例 HTTP 工作流程定義中的原則：

```json
"HTTP":
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "exponential",
            "count": 4,
            "interval": "PT7.5S",
            "minimumInterval": "PT5S",
            "maximumInterval": "PT45S"
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

### <a name="none"></a>None

如果將 **retryPolicy** 設定為 **none**，將不會重試失敗的要求。

| 元素名稱 | 必要 | 類型 | 說明 |
| ------------ | -------- | ---- | ----------- |
| type | yes | 字串 | 無 |

### <a name="fixed-interval"></a>固定間隔

如果將 **retryPolicy** 設定為 **fixed**，原則就會先依據指定的時間間隔等候，然後才傳送下一個要求，來重試失敗的要求。

| 元素名稱 | 必要 | 類型 | 說明 |
| ------------ | -------- | ---- | ----------- |
| type | yes | 字串 | **fixed** |
| count | yes | 整數  | 重試嘗試的次數。 必須介於 1 到 90 之間。 |
| interval | yes | 字串 | 以 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)表示的重試間隔。 必須介於 PT5S 到 PT1D 之間。 |

### <a name="exponential-interval"></a>指數間隔

如果將 **retryPolicy** 設定為 **exponential**，原則就會在以指數方式成長範圍內的隨機時間間隔之後，重試失敗的要求。 每次重試都保證在大於 **minimumInterval** 和小於 **maximumInterval** 的隨機間隔中傳送。 針對每次重試 (最高可達 **count** (含))，都會產生下表中所指出範圍內的統一隨機變數：

**隨機變數範圍**

| 重試數目 | 最小間隔 | 最大間隔 |
| ------------ |  ------------ |  ------------ |
| 1 | 最大值 (0，**minimumInterval**) | 最小值 (時間間隔，**maximumInterval**) |
| 2 | 最大值 (時間間隔，**minimumInterval**) | 最小值 (2 * 間隔，**maximumInterval**) |
| 3 | 最大值 (2 * 間隔，**minimumInterval**) | 最小值 (4 * 間隔，**maximumInterval**) |
| 4 | 最大 (4 * 間隔，**minimumInterval**) | Min (8 * 間隔，**maximumInterval**) |
| ... |

針對指數型原則，必須指定 **count** 和 **interval**。 **minimumInterval** 和 **maximumInterval** 的值則為選擇性。 您可以新增它們來分別覆寫 PT5S 和 PT1D 的預設值。

| 元素名稱 | 必要 | 類型 | 說明 |
| ------------ | -------- | ---- | ----------- |
| type | yes | 字串 | **exponential** |
| count | yes | 整數  | 重試嘗試的次數。 必須介於 1 到 90 之間。  |
| interval | yes | 字串 | 以 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)表示的重試間隔。 必須介於 PT5S 到 PT1D 之間。 |
| minimumInterval | 否 | 字串 | 以 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)表示的最小重試間隔。 必須介於 PT5S 到 **interval** 之間。 |
| maximumInterval | 否 | 字串 | 以 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)表示的最小重試間隔。 必須介於 **interval** 到 PT1D 之間。 |

## <a name="catch-failures-with-the-runafter-property"></a>使用 runAfter 屬性來擷取失敗

每個邏輯應用程式動作都會宣告必須先完成哪些動作才能讓動作啟動。 這就像是將工作流程中的步驟排序。 在動作定義中，此排序稱為 **runAfter** 屬性。 

**runAfter**屬性是描述哪些動作和動作狀態會執行動作的物件。 根據預設，所有您使用「邏輯應用程式設計工具」新增的動作都會設定為在前一個步驟執行後才執行，但前提是前一個步驟結果為 **Succeeded**。 

不過，您可以將 **runAfter** 值自訂成在先前動作的結果為 **Failed****Skipped** 或這些值的可能集合時引發動作。 如果您想要在特定動作 **Insert_Row** 失敗後，於指定的「Azure 服務匯流排」主題中新增項目，則可以使用下列 **runAfter** 設定︰

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

請注意，**runAfter** 會設定為在 **Insert_Row** 動作結果為 **Failed** 時引發。 若要在動作狀態為 **Succeeded**、**Failed** 或 **Skipped** 時執行動作，請使用此語法︰

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> 在前一個動作失敗後所執行並順利完成的動作會標示為 **Succeeded**。 這意謂著如果您成功擷取到工作流程中的所有失敗，該次執行本身就會標示為 **Succeeded**。

## <a name="scopes-and-results-to-evaluate-actions"></a>用以評估動作的範圍和結果

您可以將動作一起聚集在某個[範圍](../logic-apps/logic-apps-loops-and-scopes.md)內，類似於在個別動作之後執行的方式。 範圍可作為動作的邏輯群組。 

不論是要組織「邏輯應用程式」動作，還是要對某個範圍的狀態執行彙總評估，範圍都相當有用。 範圍本身會在範圍中的所有動作都已完成之後收到狀態。 範圍狀態會由執行時的相同條件來決定。 如果執行分支中的最後一個動作是 **Failed** 或 **Aborted**，狀態就會是 **Failed**。

若要針對範圍內發生的任何失敗引發特定動作，您可以使用 **runAfter** 搭配標示為 **Failed** 的範圍。 如果範圍內的「任何」動作失敗，且如果您將 **runAfter**用於某個範圍，便可建立單一動作來擷取失敗。

### <a name="get-the-context-of-failures-with-results"></a>透過結果取得失敗的內容

雖然從範圍擷取失敗很實用，但您也可以取得內容來協助您了解實際上有哪些動作失敗，以及了解所傳回的任何錯誤或狀態碼。 **@result()** 工作流程函式會提供範圍內所有動作結果的相關內容。

**@result()** 函式會採用單一參數 (範圍名稱)，並傳回該範圍內所有動作結果的陣列。 這些動作物件包含和 **@actions()** 物件相同的屬性，包括動作開始時間、動作結束時間、動作狀態、動作輸入、動作相互關聯識別碼和動作輸出。 

若要傳送在某個範圍內失敗之任何動作的內容，您可以輕鬆地將 **@result()** 函式與 **runAfter** 屬性配對。

若要針對範圍中結果為 **Failed** 的「每個」 動作執行動作，以及篩選結果陣列以顯示失敗的動作，您可以將 **@result()** 與 [Filter_array](../connectors/connectors-native-query.md) 動作及 [foreach](../logic-apps/logic-apps-loops-and-scopes.md) 迴圈配對。 有了篩選過的結果陣列，您便可以使用 **foreach** 迴圈針對每個失敗執行動作。 

以下是會傳送 HTTP POST 要求的範例，其中含有在範圍 My_Scope 中失敗之任何動作的回應本文：

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

以下是說明前述範例中所發生情況的詳細逐步解說︰

1. 為了取得 My_Scope 內所有動作的結果，**Filter_array** 動作會篩選 **@result('My_Scope')**。

2. **Filter_array** 的條件是任何狀態等於 **Failed**.的 **@result()** 項目。 此條件會將 My_Scope 所有動作結果的陣列篩選成只剩失敗動作結果的陣列。

3. 針對「篩選後陣列」的輸出執行 **foreach** 動作。 此步驟會對之前篩選的每個失敗動作結果執行動作。

    如果範圍中的單一動作失敗，**foreach** 中的動作只會執行一次。 如果有多個失敗動作，則會導致對每個失敗執行一個動作。

4. 在 **foreach** 項目回應本文或 **@item()['outputs']['body']** 上傳送 HTTP POST。 **@result()** 項目圖形與 **@actions()** 圖形相同。 您可以用相同的方式來剖析它。

5. 包含兩個具有失敗動作名稱 **@item()['name']** 和失敗執行用戶端追蹤識別碼 **@item()['clientTrackingId']** 的自訂標頭。

以下提供單一 **@result()** 項目的範例來供您參考。 其中顯示前述範例中所剖析的 **name****body** 及 **clientTrackingId** 屬性。 在 **foreach** 動作之外，**@result()** 會傳回這些物件的陣列。

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

針對不同的例外狀況處理模式，您可以使用本文先前所述的運算式。 您可以選擇在範圍外執行單一例外狀況處理動作，以接受整個篩選後的失敗陣列，並移除 **foreach**。 您也可以包含 **@result()** 回應中的其他有用屬性，如先前所述。

## <a name="azure-diagnostics-and-telemetry"></a>Azure 診斷和遙測

本文所述的模式提供絕佳的方法來處理執行內的錯誤和例外狀況，但您也可以識別和回應與執行本身無關的錯誤。 [Azure 診斷](../logic-apps/logic-apps-monitor-your-logic-apps.md) 提供一個簡單的方法，可讓您將所有工作流程事件 (包括所有執行和動作狀態) 傳送至 Azure 儲存體帳戶或「Azure 事件中樞」中的某個事件中樞。 

若要評估執行狀態，您可以監視記錄和計量，或將它們發佈至您慣用的任何監視工具。 其中一個可能的選項是透過「事件中樞」將所有事件串流到 [Azure 串流分析](https://azure.microsoft.com/services/stream-analytics/)。 在「串流分析」中，您可以根據診斷記錄檔中的任何異常、平均或失敗，撰寫即時查詢。 您可以使用「串流分析」將資訊傳送到其他資料來源，例如傳送到佇列、主題、SQL、Azure Cosmos DB 或 Power BI。

## <a name="next-steps"></a>後續步驟

* 了解客戶如何[使用 Azure 中的 Logic Apps 來建置錯誤處理](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)。
* 尋找更多 [Logic Apps 範例和案例](../logic-apps/logic-apps-examples-and-scenarios.md)。
* 了解如何建立[邏輯應用程式的自動化部署](../logic-apps/logic-apps-create-deploy-template.md)。
* 了解如何[使用 Visual Studio 來建置和部署邏輯應用程式](logic-apps-deploy-from-vs.md)。

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
