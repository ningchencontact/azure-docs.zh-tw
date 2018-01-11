---
title: "錯誤和例外狀況處理在 Azure 中的 Logic apps |Microsoft 文件"
description: "錯誤和例外狀況處理邏輯應用程式中的模式。"
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
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2017
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>處理錯誤和例外狀況中的 Logic Apps

在 Azure 中的應用程式邏輯提供豐富的工具和可協助您確保您的整合是強固且彈性地防止失敗的模式。 任何整合架構其中適當地處理停機時間的挑戰，或從相依系統問題。 邏輯應用程式可處理錯誤的第一級的體驗。 它可讓您加以處理的例外狀況和您的工作流程中的錯誤所需的工具。

## <a name="retry-policies"></a>重試原則

重試原則是最基本的例外狀況和錯誤處理類型。 如果初始的要求逾時或失敗 (429 會導致任何要求或 5xx 回應)，重試原則定義是否應該重試此動作的方式。 

有四種類型的重試原則： default、 none、 固定間隔和指數的間隔。 如果工作流程定義中未提供重試原則，則會使用預設的原則所定義的服務。 

您可以在特定動作或觸發程序 (如果可重試) 的*輸入*中設定重試原則。 同樣地，您可以設定的重試原則 （如果適用） 中邏輯應用程式的設計工具。 若要設定的重試原則，請在邏輯應用程式的設計工具，請移至**設定**特定的動作。

限制的重試原則的相關資訊，請參閱[Logic Apps 限制以及設定](../logic-apps/logic-apps-limits-and-config.md)。 如需有關支援語法的詳細資訊，請參閱[重試一次在工作流程動作和觸發程序中的 [原則] 區段][retryPolicyMSDN]。

### <a name="default"></a>預設值

如果您未定義的重試原則 (**retryPolicy**是未定義)，會使用預設原則。 預設原則是指數間隔原則傳送最多四個重試次數，以指數方式增加的縮放 7.5 秒的間隔。 間隔的上限為介於 5 到 45 秒。 這項預設原則相當於在此範例中 HTTP 工作流程定義的原則：

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

如果**retryPolicy**設**無**，不會重試失敗的要求。

| 元素名稱 | 必要 | 類型 | 說明 |
| ------------ | -------- | ---- | ----------- |
| type | 是 | 字串 | 無 |

### <a name="fixed-interval"></a>固定間隔

如果**retryPolicy**設**固定**，原則會重試失敗的要求正在等候指定的傳送下一個要求之前的時間間隔。

| 元素名稱 | 必要 | 類型 | 說明 |
| ------------ | -------- | ---- | ----------- |
| type | 是 | 字串 | **修正** |
| count | 是 | 整數  | 重試嘗試的次數。 必須是介於 1 到 90 之間。 |
| interval | 是 | 字串 | 重試間隔中的[ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)。 必須在 PT1D PT5S 之間。 |

### <a name="exponential-interval"></a>指數間隔

如果**retryPolicy**設**指數**，原則以指數方式成長的範圍內隨機的時間間隔之後會重試失敗的要求。 每次重試都保證在大於 **minimumInterval** 和小於 **maximumInterval** 的隨機間隔中傳送。 針對每次重試產生統一的隨機變數，指定下表中的範圍中最多並包括**計數**:

**隨機變數範圍**

| 重試數字 | 最小間隔 | 最大間隔 |
| ------------ |  ------------ |  ------------ |
| 1 | 最大值 (0，**minimumInterval**) | 最小值 (時間間隔，**maximumInterval**) |
| 2 | 最大值 (時間間隔，**minimumInterval**) | 最小值 (2 * 間隔，**maximumInterval**) |
| 3 | 最大值 (2 * 間隔， **minimumInterval**) | 最小值 (4 * 間隔，**maximumInterval**) |
| 4 | 最大 (4 * 間隔，**minimumInterval**) | Min (8 * 間隔，**maximumInterval**) |
| ... |

指數類型原則**計數**和**間隔**所需。 值**minimumInterval**和**maximumInterval**是選擇性的。 您可以將它們分別覆寫 PT5S 和 PT1D 的預設值。

| 元素名稱 | 必要 | 類型 | 說明 |
| ------------ | -------- | ---- | ----------- |
| type | 是 | 字串 | **指數** |
| count | 是 | 整數  | 重試嘗試的次數。 必須是介於 1 到 90 之間。  |
| interval | 是 | 字串 | 重試間隔中的[ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)。 必須在 PT1D PT5S 之間。 |
| minimumInterval | 否 | 字串 | 重試中的最小間隔[ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)。 必須介於 PT5S 和**間隔**。 |
| maximumInterval | 否 | 字串 | 重試中的最小間隔[ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)。 必須介於**間隔**和 PT1D。 |

## <a name="catch-failures-with-the-runafter-property"></a>攔截與 runAfter 屬性失敗

邏輯應用程式的每個動作宣告的動作必須在動作啟動之前完成。 與工作流程中排序步驟相似。 在動作定義中，這種排序稱為**runAfter**屬性。 

**RunAfter**屬性是描述哪些動作和動作狀態執行此動作的物件。 根據預設，使用邏輯應用程式的設計工具加入的所有動作，都設定為執行上述步驟之後，如果上述步驟的結果為**Succeeded**。 

不過，您可以自訂**runAfter**值時要引發執行上述動作的結果都是**失敗**，**已略過**，或一組可能的這些值。 如果您想要特定動作之後將項目加入指定的 Azure 服務匯流排主題**Insert_Row**失敗，您可以使用下列**runAfter**組態：

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

請注意， **runAfter**設如果引發**Insert_Row**動作結果會是**失敗**。 動作狀態時執行動作**Succeeded**，**失敗**，或**已略過**，使用下列語法：

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> 動作執行並成功完成上述動作已失敗之後會標示為**Succeeded**。 這表示，如果您已成功攔截所有失敗的工作流程本身的執行中標示為**Succeeded**。

## <a name="scopes-and-results-to-evaluate-actions"></a>用以評估動作的範圍和結果

您可以在組成群組動作[範圍](../logic-apps/logic-apps-loops-and-scopes.md)，類似於您在個別的動作之後執行的方式。 範圍是動作的邏輯群組。 

領域都是很有用，組織您邏輯應用程式的動作，並執行彙總的評估狀態範圍的項目。 範圍本身會在範圍中的所有動作都已完成之後收到狀態。 範圍狀態會由執行時的相同條件來決定。 如果執行分支中的最後一個動作是**失敗**或**Aborted**，狀態是 **失敗**。

若要引發的範圍內發生任何失敗的特定動作，您可以使用**runAfter**範圍標示為**失敗**。 如果*任何*範圍中的動作失敗，如果您使用**runAfter**範圍中，您可以建立一項動作來攔截錯誤。

### <a name="get-the-context-of-failures-with-results"></a>取得與結果的失敗的內容

雖然攔截失敗從範圍非常有用，您也可以協助您了解哪些動作完全失敗，並了解的任何錯誤或已傳回狀態碼的內容。  **@result（)**工作流程函式提供的範圍中的所有動作結果相關的內容。

 **@result（)**函式會採用單一參數 （範圍名稱），並傳回陣列的所有動作結果從該範圍內。 這些動作物件包含相同的屬性 **@actions（)**輸出物件，包括動作的開始時間、 動作的結束時間、 動作狀態、 輸入動作、 動作相互關聯識別碼和動作。 

若要在範圍內傳送失敗的任何動作的內容，您可以輕易地配對 **@result（)**函式與**runAfter**屬性。

要執行動作*每個*動作的範圍中**失敗**結果，若要篩選的結果失敗的動作陣列，您所能配對和 **@result（)**與[Filter_array](../connectors/connectors-native-query.md)動作和[foreach](../logic-apps/logic-apps-loops-and-scopes.md)迴圈。 以篩選的結果陣列中，您可以執行的動作每個失敗使用**foreach**迴圈。 

此處的範例會將傳送 HTTP POST 要求回應本文的範圍 My_Scope 中失敗的任何動作：

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

以下是詳細的逐步解說來描述在上述範例中發生的事：

1. 若要取得 My_Scope 中的所有動作結果**Filter_array**動作篩選條件 **@result('My_Scope')**。

2. 條件**Filter_array**可以是任何 **@result（)**項目有狀態等於**失敗**。 此條件來篩選從 My_Scope，所有的動作結果，使用只有失敗的動作結果陣列的陣列。

3. 執行**foreach**動作*篩選的陣列*輸出。 此步驟會對之前篩選的每個失敗動作結果執行動作。

    如果範圍中的單一動作失敗中的動作**foreach**只執行一次。 多個失敗的動作會導致失敗的每一個動作。

4. 傳送 HTTP POST **foreach**回應主體的項目或 **@item（['輸出'] ['主體'])**。  **@result（)**項目 圖形是相同 **@actions（)**圖形。 它可以剖析相同的方式。

5. 包含兩個失敗的動作名稱的自訂標頭 **@item（['name'])**失敗執行用戶端追蹤 ID  **@item（['clientTrackingId'])**。

如需參考，以下是在單一範例 **@result（)**項目。 它會顯示**名稱**，**主體**，和**clientTrackingId**剖析的上述範例中的屬性。 外部**foreach**動作，  **@result（)**傳回這些物件的陣列。

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

針對不同的例外狀況處理模式，您可以使用本文稍早所述的運算式。 您可能會選擇執行單一的例外狀況處理可接受的失敗，整個篩選的陣列範圍外的動作，並移除**foreach**。 您也可以包含其他有用的屬性，從 **@result（)**回應，如先前所述。

## <a name="azure-diagnostics-and-telemetry"></a>Azure 診斷和遙測

本文中所述的模式提供絕佳的方式處理錯誤和例外狀況，在執行中，但您也可以識別並獨立執行本身的錯誤回應。 [Azure 診斷](../logic-apps/logic-apps-monitor-your-logic-apps.md)提供簡單的方式，將傳送至 Azure 儲存體帳戶或 Azure 事件中心事件中樞的 （包括所有執行和動作的狀態） 的所有工作流程事件。 

若要評估的執行的狀態，您可以監視記錄檔和度量，或將它們發行至任何您偏好的監視工具。 可能的選項是透過至事件中心的所有事件資料都流[Azure 都 Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)。 在 Stream Analytics 中，您可以撰寫即時查詢的任何異常、 平均值或失敗的診斷記錄檔。 您可以將資訊傳送至其他資料來源，例如佇列、 主題、 SQL、 Azure Cosmos DB，或 Power BI 中使用資料流分析。

## <a name="next-steps"></a>後續步驟

* 請參閱如何客戶[建置錯誤處理與在 Azure 中的 Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)。
* 找到更多[Logic Apps 範例和案例](../logic-apps/logic-apps-examples-and-scenarios.md)。
* 了解如何建立[自動化部署中的 logic apps](../logic-apps/logic-apps-create-deploy-template.md)。
* 深入了解如何[建置和部署使用 Visual Studio 的 logic apps](logic-apps-deploy-from-vs.md)。

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
