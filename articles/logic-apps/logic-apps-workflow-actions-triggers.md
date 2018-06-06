---
title: 工作流程觸發程序和動作 - Azure Logic Apps | Microsoft Docs
description: 深入了解 Azure Logic Apps 工作流程動作中的觸發程序和動作
services: logic-apps
author: kevinlam1
manager: SyntaxC4
editor: ''
documentationcenter: ''
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 5/8/2018
ms.author: klam; LADocs
ms.openlocfilehash: 88ee3d810a80bed418e8dbafa4f3e35ccf5e85b1
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "33886777"
---
# <a name="triggers-and-actions-for-workflow-definitions-in-azure-logic-apps"></a>Azure Logic Apps 工作流程動作中的觸發程序和動作

在 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 中，所有邏輯應用程式工作流程的開頭都是觸發程序，後面接著動作。 本文說明您可用來建置邏輯應用程式的觸發程序和動作，以便在整合解決方案中自動執行業務工作流程或程序。 您可以利用 Logic Apps 設計工具以視覺化方式建置邏輯應用程式，或利用[工作流程定義語言](../logic-apps/logic-apps-workflow-definition-language.md)直接撰寫基礎工作流程定義，以建置邏輯應用程式。 您可以使用 Azure 入口網站或 Visual Studio。 深入了解[觸發程序和動作的定價方式](../logic-apps/logic-apps-pricing.md)。

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>觸發程序概觀

所有邏輯應用程式都是以觸發程序為開頭，此觸發程序會定義可以具現化和啟動邏輯應用程式工作流程的呼叫。 以下是您可以使用的觸發程序類型：

* 「輪詢」觸發程序，用來定期檢查服務的 HTTP 端點
* 「推送」觸發程序，用來呼叫[工作流程服務 REST API](https://docs.microsoft.com/rest/api/logic/workflows)
 
所有觸發程序都具有下列最上層元素 (儘管有些是選擇性元素)：  
  
```json
"<triggerName>": {
   "type": "<triggerType>",
   "inputs": { "<trigger-behavior-settings>" },
   "recurrence": { 
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": "<recurrence-interval-based-on-frequency>"
   },
   "conditions": [ <array-with-required-conditions> ],
   "splitOn": "<property-used-for-creating-runs>",
   "operationOptions": "<optional-trigger-operations>"
}
```

*必要*

| 元素名稱 | 類型 | 說明 | 
| ------------ | ---- | ----------- | 
| <*triggerName*> | JSON 物件 | 觸發程序的名稱，這是以 Javascript 物件標記法 (JSON) 格式描述的物件  | 
| type | 字串 | 觸發程序類型，例如："Http" 或 "ApiConnection" | 
| 輸入 | JSON 物件 | 定義觸發程序行為的觸發程序輸入 | 
| 週期 | JSON 物件 | 描述觸發程序多久引發一次的頻率和間隔 |  
| frequency | 字串 | 描述觸發程序多久引發一次的時間單位：[秒]、[分鐘]、[小時]、[天]、[週] 或 [月] | 
| interval | 整數  | 描述觸發程序根據頻率多久引發一次的正整數。 <p>以下是最小和最大間隔： <p>- 月：1-16 個月 </br>- 天：1-500 天 </br>- 小時：1-12,000 個小時 </br>- 分鐘：1-72,000 分鐘 </br>- 秒：1-9,999,999 秒<p>例如，如果 interval 是 6，而 frequency 是 "month"，則週期為每隔 6 個月。 | 
|||| 

*選擇性*

| 元素名稱 | 類型 | 說明 | 
| ------------ | ---- | ----------- | 
| [條件](#trigger-conditions) | 陣列 | 決定是否執行工作流程的一個或多個條件 | 
| [splitOn](#split-on-debatch) | 字串 | 運算式會將陣列項目分割 (或「解除批次」) 為多個工作流程執行個體，進行處理。 此選項時適用於可傳回陣列的觸發程序，而且只可用於直接在程式碼檢視中作業時。 | 
| [operationOptions](#trigger-operation-options) | 字串 | 某些觸發程序提供可讓您變更預設觸發程序行為的其他選項 | 
||||| 

## <a name="trigger-types-and-details"></a>觸發程序類型和詳細資料  

每一種觸發程序類型都有不同的介面，以及可定義觸發程序行為的輸入。 

| 觸發程序類型 | 說明 | 
| ------------ | ----------- | 
| [**Recurrence**](#recurrence-trigger) | 根據已定義的排程來引發。 您可以設定一個未來的日期和時間來引發此觸發程序。 您也可以根據頻率來指定工作流程的執行時間和日子。 | 
| [**Request**](#request-trigger)  | 讓邏輯應用程式成為可呼叫的端點，也稱為「手動」觸發程序。 例如，請參閱[透過 HTTP 端點呼叫、觸發或巢狀處理工作流程](../logic-apps/logic-apps-http-endpoint.md)。 | 
| [**HTTP**](#http-trigger) | 檢查或「輪詢」HTTP Web 端點。 HTTP 端點必須藉由使用 "202" 非同步模式或藉由傳回陣列，來遵守特定觸發合約。 | 
| [**ApiConnection**](#apiconnection-trigger) | 運作方式與 HTTP 觸發程序類似，但使用 [Microsoft 受控 API](../connectors/apis-list.md)。 | 
| [**HTTPWebhook**](#httpwebhook-trigger) | 運作方式與 Request 觸發程序類似，但會呼叫指定的 URL 來進行註冊及取消註冊。 |
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | 運作方式與 HTTPWebhook 觸發程序類似，但使用 [Microsoft 受控 API](../connectors/apis-list.md)。 | 
||| 

<a name="recurrence-trigger"></a>

## <a name="recurrence-trigger"></a>循環觸發程序  

此觸發程序會根據您指定的週期和排程來執行，提供一個定期執行工作流程的簡單方式。 

以下是觸發程序定義：

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month",
      "interval": <recurrence-interval-based-on-frequency>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```
*必要*

| 元素名稱 | 類型 | 說明 | 
| ------------ | ---- | ----------- | 
| 週期性 | JSON 物件 | 觸發程序的名稱，這是以 Javascript 物件標記法 (JSON) 格式描述的物件  | 
| type | 字串 | 觸發程序類型，也就是 "Recurrence" | 
| 輸入 | JSON 物件 | 定義觸發程序行為的觸發程序輸入 | 
| 週期 | JSON 物件 | 描述觸發程序多久引發一次的頻率和間隔 |  
| frequency | 字串 | 描述觸發程序多久引發一次的時間單位：[秒]、[分鐘]、[小時]、[天]、[週] 或 [月] | 
| interval | 整數  | 描述觸發程序根據頻率多久引發一次的正整數。 <p>以下是最小和最大間隔： <p>- 月：1-16 個月 </br>- 天：1-500 天 </br>- 小時：1-12,000 個小時 </br>- 分鐘：1-72,000 分鐘 </br>- 秒：1-9,999,999 秒<p>例如，如果 interval 是 6，而 frequency 是 "month"，則週期為每隔 6 個月。 | 
|||| 

*選擇性*

| 元素名稱 | 類型 | 說明 | 
| ------------ | ---- | ----------- | 
| startTime | 字串 | 使用以下格式的開始日期和時間： <p>YYYY-MM-DDThh:mm:ss (如果您指定時區) <p>-或- <p>YYYY-MM-DDThh:mm:ssZ (如果您未指定時區) <p>因此，舉例來說，如果您想要的是 2017 年 9 月 18 日下午 2:00，則請指定 "2017-09-18T14:00:00"，然後指定一個時區，例如 "Pacific Standard Time"，或指定不含時區的 "2017-09-18T14:00:00Z"。 <p>**注意：** 這個開始時間必須依照 [UTC 日期時間格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)中的 [ISO 8601 日期時間規格](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，但不含 [UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 如果您不指定時區，就必須在結尾加上字母 "Z"，其中不含任何空格。 這個 "Z" 係指對等的[航海時間](https://en.wikipedia.org/wiki/Nautical_time)。 <p>就簡單排程來說，開始時間係指第一次發生的時間，而就複雜排程來說，觸發程序會在開始時間一到就立即引發。 如需有關開始日期和時間的詳細資訊，請參閱[建立及排定定期執行的工作](../connectors/connectors-native-recurrence.md)。 | 
| timeZone | 字串 | 只有當您有指定開始時間時才適用，因為此觸發程序並不接受 [UTC 時差](https://en.wikipedia.org/wiki/UTC_offset)。 指定您要套用的時區。 | 
| hours | 整數或整數陣列 | 當您想要執行工作流程時，如果針對 `frequency` 指定 "Day" 或 "Week"，便可指定從 0 到 23 的一或多個整數 (以逗號分隔) 來表示一天中的哪幾個整點。 <p>例如，如果您指定 "10"、"12" 及 "14"，就會得出上午 10 點、下午 12 點及下午 2 點作為整點標記。 | 
| minutes | 整數或整數陣列 | 當您想要執行工作流程時，如果針對 `frequency` 指定 "Day" 或 "Week"，便可指定從 0 到 59 的一或多個整數 (以逗號分隔) 來表示小時中的哪幾個分鐘。 <p>例如，您可以指定 "30" 作為分鐘標記，然後使用上個範例代表一天中的整點，這樣就會得出上午 10:30、下午 12:30 及下午 2:30。 | 
| weekDays | 字串或字串陣列 | 當您想要執行工作流程時，如果針對 `frequency` 指定 "Week"，便可指定一或多天 (以逗號分隔)："Monday"、"Tuesday"、"Wednesday"、"Thursday"、"Friday"、"Saturday" 及 "Sunday" | 
| 並行 | JSON 物件 | 對於週期性與輪詢觸發程序，此物件會指定可以同時執行的工作流程執行個體數目上限。 使用此值來限制後端系統接收的要求。 <p>例如，此值會將並行限制設定為 10 個執行個體：`"concurrency": { "runs": 10 }` | 
| operationOptions | 字串 | `singleInstance` 選項指定觸發程序只會在所有作用中的執行完成後引發。 請參閱[觸發程序：只在作用中的執行完成後引發](#single-instance)。 | 
|||| 

*範例 1*

這個基本週期觸發程序會每天執行：

```json
"recurrenceTriggerName": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*範例 2*

您可以指定開始日期和時間來引發觸發程序。 此週期觸發程序會於指定的日期開始，然後每天引發：

```json
"recurrenceTriggerName": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*範例 3*

此週期觸發程序會於 2017 年 9 月 9 日下午 2:00 開始，在每週星期一早上 10:30、下午 12:30 及下午 2:30 (太平洋標準時間) 引發：

``` json
"myRecurrenceTrigger": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

如需此觸發程序外加範例的詳細資訊，請參閱[建立及排定定期執行的工作](../connectors/connectors-native-recurrence.md)。

<a name="request-trigger"></a>

## <a name="request-trigger"></a>要求觸發程序

此觸發程序會藉由建立可接受連入 HTTP 要求的端點，讓您的邏輯應用程式可呼叫。 若要呼叫此觸發程序，您必須在 [工作流程服務 REST API](https://docs.microsoft.com/rest/api/logic/workflows) 中使用 `listCallbackUrl` API。 若要了解如何使用這個觸發程序作為 HTTP 端點，請參閱[透過 HTTP 端點呼叫、觸發或巢狀處理工作流程](../logic-apps/logic-apps-http-endpoint.md)。

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "GET | POST | PUT | PATCH | DELETE | HEAD",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<propertyName>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   }
}
```

*必要*

| 元素名稱 | 類型 | 說明 | 
| ------------ | ---- | ----------- | 
| manual | JSON 物件 | 觸發程序的名稱，這是以 Javascript 物件標記法 (JSON) 格式描述的物件  | 
| type | 字串 | 觸發程序類型，也就是 "Request" | 
| kind | 字串 | 要求類型，也就是 "Http" | 
| 輸入 | JSON 物件 | 定義觸發程序行為的觸發程序輸入 | 
|||| 

*選擇性*

| 元素名稱 | 類型 | 說明 | 
| ------------ | ---- | ----------- | 
| method | 字串 | 要求必須用來呼叫觸發程序的方法："GET"、"PUT"、"POST"、"PATCH"、"DELETE" 或 "HEAD" |
| relativePath | 字串 | HTTP 端點的 URL 接受的參數相對路徑 | 
| 結構描述 | JSON 物件 | JSON 結構描述，可描述及驗證承載，或觸發程序從連入要求接收的輸入。 此結構描述可協助後續工作流程動作了解要參考的屬性。 | 
| properties | JSON 物件 | JSON 結構描述中可描述承載的一或多個屬性 | 
| 必要 | 陣列 | 需要值的一或多個屬性 | 
|||| 

*範例*

此要求觸發程序會指定連入要求使用 HTTP POST 方法來呼叫觸發程序，以及可驗證來自連入要求之輸入的結構描述： 

```json
"myRequestTrigger": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "Object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "String"
                  },
                  "city": {
                     "type": "String"
                  }
               }
            }
         }
      }
   }
} 
```

<a name="http-trigger"></a>

## <a name="http-trigger"></a>HTTP 觸發程序  

此觸發程序會輪詢指定的端點並檢查回應。 此回應決定是否應該執行工作流程。 `inputs` JSON 物件包括且需要建構 HTTP 呼叫所需的 `method` 和 `uri` 參數：

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET | PUT | POST | PATCH | DELETE | HEAD",
      "uri": "<HTTP-or-HTTPS-endpoint-to-poll>",
      "queries": "<query-parameters>",
      "headers": { "<headers-for-request>" },
      "body": { "<payload-to-send>" },
      "authentication": { "<authentication-method>" },
      "retryPolicy": {
          "type": "<retry-policy-type>",
          "interval": "<retry-interval>",
          "count": <number-retry-attempts>
      }
   },
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": <recurrence-interval-based-on-frequency>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```

*必要*

| 元素名稱 | 類型 | 說明 | 
| ------------ | ---- | ----------- | 
| HTTP | JSON 物件 | 觸發程序的名稱，這是以 Javascript 物件標記法 (JSON) 格式描述的物件  | 
| type | 字串 | 觸發程序類型，也就是 "Http" | 
| 輸入 | JSON 物件 | 定義觸發程序行為的觸發程序輸入 | 
| method | yes | 字串 | 用於輪詢所指定端點的 HTTP 方法："GET"、"PUT"、"POST"、"PATCH"、"DELETE" 或 "HEAD" | 
| uri | yes| 字串 | 觸發程序檢查或輪詢的 HTTP 或 HTTPS 端點 URL。 <p>字串大小上限：2 KB | 
| 週期 | JSON 物件 | 描述觸發程序多久引發一次的頻率和間隔 |  
| frequency | 字串 | 描述觸發程序多久引發一次的時間單位：[秒]、[分鐘]、[小時]、[天]、[週] 或 [月] | 
| interval | 整數  | 描述觸發程序根據頻率多久引發一次的正整數。 <p>以下是最小和最大間隔： <p>- 月：1-16 個月 </br>- 天：1-500 天 </br>- 小時：1-12,000 個小時 </br>- 分鐘：1-72,000 分鐘 </br>- 秒：1-9,999,999 秒<p>例如，如果 interval 是 6，而 frequency 是 "month"，則週期為每隔 6 個月。 | 
|||| 

*選擇性*

| 元素名稱 | 類型 | 說明 | 
| ------------ | ---- | ----------- | 
| 查詢 | JSON 物件 | 您想要包含在 URL 中的任何查詢參數 <p>例如，此元素會將 `?api-version=2015-02-01` 查詢字串新增至 URL： <p>`"queries": { "api-version": "2015-02-01" }` <p>結果︰`https://contoso.com?api-version=2015-02-01` | 
| headers | JSON 物件 | 要與要求一起傳送的一個或多個標頭 <p>例如，若要設定要求的語言和類型︰ <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | JSON 物件 | 傳送至端點的承載 (資料) | 
| 驗證 | JSON 物件 | 連入要求應用來進行驗證的方法。 如需詳細資訊，請參閱[排程器輸出驗證](../scheduler/scheduler-outbound-authentication.md)。 除了排程器之外，還可支援 `authority` 屬性。 若未指定，預設值為 `https://login.windows.net`，但您可以使用不同的值，例如 `https://login.windows\-ppe.net`。 | 
| RetryPolicy | JSON 物件 | 此物件可針對具有 4xx 或 5xx 狀態碼的間歇性錯誤，自訂重試行為。 如需詳細資訊，請參閱[重試原則](../logic-apps/logic-apps-exception-handling.md)。 | 
| 並行 | JSON 物件 | 對於週期性與輪詢觸發程序，此物件會指定可以同時執行的工作流程執行個體數目上限。 使用此值來限制後端系統接收的要求。 <p>例如，此值會將並行限制設定為 10 個執行個體： <p>`"concurrency": { "runs": 10 }` | 
| operationOptions | 字串 | `singleInstance` 選項指定觸發程序只會在所有作用中的執行完成後引發。 請參閱[觸發程序：只在作用中的執行完成後引發](#single-instance)。 | 
|||| 

為了與邏輯應用程式良好搭配運作，HTTP 觸發程序會要求 HTTP API 符合特定模式。 HTTP 觸發程序可辨識下列屬性：  
  
| Response | 必要 | 說明 | 
| -------- | -------- | ----------- |  
| 狀態碼 | yes | 「200 正常」狀態碼會啟動執行。 其他任何的狀態碼則不會啟動執行。 | 
| Retry-after 標頭 | 否 | 邏輯應用程式再次輪詢端點前所需經過的秒數 | 
| 位置標頭 | 否 | 在下一個輪詢間隔時所要呼叫的 URL。 如果未指定，則會使用原本的 URL。 | 
|||| 

不同要求的範例行為

| 狀態碼 | 多久之後重試 | 行為 | 
| ----------- | ----------- | -------- | 
| 200 | {無} | 執行工作流程，然後在所定義的週期之後再次檢查是否有其他資料。 | 
| 200 | 10 秒 | 執行工作流程，然後在 10 秒之後再次檢查是否有其他資料。 |  
| 202 | 60 秒 | 不觸發工作流程。 下一次嘗試會依據所定義的週期，在一分鐘內開始。 如果所定義的週期不超過一分鐘，則 retry-after 標頭的優先順序較高。 否則會採用所定義的週期。 | 
| 400 | {無} | 不正確的要求，不執行工作流程。 如果未定義任何 `retryPolicy`，則會使用預設原則。 達到重試次數之後，觸發程序會在所定義的週期之後再次檢查是否還有資料。 | 
| 500 | {無}| 伺服器錯誤，不執行工作流程。 如果未定義任何 `retryPolicy`，則會使用預設原則。 達到重試次數之後，觸發程序會在所定義的週期之後再次檢查是否還有資料。 | 
|||| 

### <a name="http-trigger-outputs"></a>HTTP 觸發程序輸出

| 元素名稱 | 類型 | 說明 |
| ------------ | ---- | ----------- |
| headers | JSON 物件 | HTTP 回應中的標頭 | 
| body | JSON 物件 | HTTP 回應中的主體 | 
|||| 

<a name="apiconnection-trigger"></a>

## <a name="apiconnection-trigger"></a>APIConnection 觸發程序  

此觸發程序的運作方式與 [HTTP 觸發程序](#http-trigger)類似，但會使用 [Microsoft 受控 API](../connectors/apis-list.md)，因此這個觸發程序的參數不同。 

以下是觸發程序定義 (雖然許多區段都是選擇性區段)，而觸發程序的行為取決於是否包含區段：

```json
"<APIConnectionTriggerName>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "api": {
            "runtimeUrl": "<managed-API-endpoint-URL>"
         },
         "connection": {
            "name": "@parameters('$connections')['<connection-name>'].name"
         },
      },
      "method": "GET | PUT | POST | PATCH | DELETE | HEAD",
      "queries": "<query-parameters>",
      "headers": { "<headers-for-request>" },
      "body": { "<payload-to-send>" },
      "authentication": { "<authentication-method>" },
      "retryPolicy": {
          "type": "<retry-policy-type>",
          "interval": "<retry-interval>",
          "count": <number-retry-attempts>
      }
   },
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": "<recurrence-interval-based-on-frequency>"
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```

*必要*

| 元素名稱 | 類型 | 說明 | 
| ------------ | ---- | ----------- | 
| *APIConnectionTriggerName* | JSON 物件 | 觸發程序的名稱，這是以 Javascript 物件標記法 (JSON) 格式描述的物件  | 
| type | 字串 | 觸發程序類型，也就是 "ApiConnection" | 
| 輸入 | JSON 物件 | 定義觸發程序行為的觸發程序輸入 | 
| host | JSON 物件 | 描述受控 API 的主機閘道和識別碼的 JSON 物件 <p>`host` JSON 物件具有以下元素：`api` 和 `connection` | 
| api | JSON 物件 | 受控 API 的端點 URL： <p>`"runtimeUrl": "<managed-API-endpoint-URL>"` | 
| connection | JSON 物件 | 工作流程所使用的受控 API 連線名稱，其必須包含名為 `$connection` 之參數的參考： <p>`"name": "@parameters('$connections')['<connection-name>'].name"` | 
| method | 字串 | 用於與受控 API 通訊的 HTTP 方法："GET"、"PUT"、"POST"、"PATCH"、"DELETE" 或 "HEAD" | 
| 週期 | JSON 物件 | 描述觸發程序多久引發一次的頻率和間隔 |  
| frequency | 字串 | 描述觸發程序多久引發一次的時間單位：[秒]、[分鐘]、[小時]、[天]、[週] 或 [月] | 
| interval | 整數  | 描述觸發程序根據頻率多久引發一次的正整數。 <p>以下是最小和最大間隔： <p>- 月：1-16 個月 </br>- 天：1-500 天 </br>- 小時：1-12,000 個小時 </br>- 分鐘：1-72,000 分鐘 </br>- 秒：1-9,999,999 秒<p>例如，如果 interval 是 6，而 frequency 是 "month"，則週期為每隔 6 個月。 | 
|||| 

*選擇性*

| 元素名稱 | 類型 | 說明 | 
| ------------ | ---- | ----------- | 
| 查詢 | JSON 物件 | 您想要包含在 URL 中的任何查詢參數 <p>例如，此元素會將 `?api-version=2015-02-01` 查詢字串新增至 URL： <p>`"queries": { "api-version": "2015-02-01" }` <p>結果︰`https://contoso.com?api-version=2015-02-01` | 
| headers | JSON 物件 | 要與要求一起傳送的一個或多個標頭 <p>例如，若要設定要求的語言和類型︰ <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | JSON 物件 | 描述要傳送到受控 API 的承載 (資料) 的 JSON 物件 | 
| 驗證 | JSON 物件 | 連入要求應用來進行驗證的方法。 如需詳細資訊，請參閱[排程器輸出驗證](../scheduler/scheduler-outbound-authentication.md)。 |
| RetryPolicy | JSON 物件 | 此物件可針對具有 4xx 或 5xx 狀態碼的間歇性錯誤，自訂重試行為： <p>`"retryPolicy": { "type": "<retry-policy-type>", "interval": "<retry-interval>", "count": <number-retry-attempts> }` <p>如需詳細資訊，請參閱[重試原則](../logic-apps/logic-apps-exception-handling.md)。 | 
| 並行 | JSON 物件 | 對於週期性與輪詢觸發程序，此物件會指定可以同時執行的工作流程執行個體數目上限。 使用此值來限制後端系統接收的要求。 <p>例如，此值會將並行限制設定為 10 個執行個體：`"concurrency": { "runs": 10 }` | 
| operationOptions | 字串 | `singleInstance` 選項指定觸發程序只會在所有作用中的執行完成後引發。 請參閱[觸發程序：只在作用中的執行完成後引發](#single-instance)。 | 
||||

*範例*

```json
"Create_daily_report": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "api": {
            "runtimeUrl": "https://myReportsRepo.example.com/"
         },
         "connection": {
            "name": "@parameters('$connections')['<connection-name>'].name"
         }     
      },
      "method": "POST",
      "body": {
         "category": "statusReports"
      }  
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

### <a name="apiconnection-trigger-outputs"></a>APIConnection 觸發程序輸出
 
| 元素名稱 | 類型 | 說明 |
| ------------ | ---- | ----------- |
| headers | JSON 物件 | HTTP 回應中的標頭 | 
| body | JSON 物件 | HTTP 回應中的主體 | 
|||| 

<a name="httpwebhook-trigger"></a>

## <a name="httpwebhook-trigger"></a>HTTPWebhook 觸發程序  

此觸發程序的運作方式與 [Request 觸發程序](#request-trigger)類似，可為邏輯應用程式建立可呼叫的端點。 不過，此觸發程序也會呼叫指定的端點 URL，以便註冊或取消註冊訂用帳戶。 您可以採用與 [HTTP 非同步限制](#asynchronous-limits)相同的方式，來對 Webhook 觸發程序指定限制。 

以下是觸發程序定義 (雖然許多區段都是選擇性區段)，而觸發程序的行為取決於您使用或省略的區段：

```json
"HTTP_Webhook": {
    "type": "HttpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "<subscribe-to-endpoint-URL>",
            "headers": { "<headers-for-request>" },
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "<subscription-topic>"
            },
            "authentication": {},
            "retryPolicy": {}
        },
        "unsubscribe": {
            "method": "POST",
            "url": "<unsubscribe-from-endpoint-URL>",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "<subscription-topic>"
            },
            "authentication": {}
        }
    },
}
```

*必要*

| 元素名稱 | 類型 | 說明 | 
| ------------ | ---- | ----------- | 
| HTTP_Webhook | JSON 物件 | 觸發程序的名稱，這是以 Javascript 物件標記法 (JSON) 格式描述的物件  | 
| type | 字串 | 觸發程序類型，也就是 "HttpWebhook" | 
| 輸入 | JSON 物件 | 定義觸發程序行為的觸發程序輸入 | 
| 訂閱 | JSON 物件| 建立觸發程序時，要呼叫並執行初始註冊的連出要求。 系統會進行此呼叫，以便觸發程序開始在端點上接聽事件。 如需詳細資訊，請參閱[訂閱和取消訂閱](#subscribe-unsubscribe)。 | 
| method | 字串 | 用於訂用帳戶要求的 HTTP 方法："GET"、"PUT"、"POST"、"PATCH"、"DELETE" 或 "HEAD" | 
| uri | 字串 | 要傳送訂用帳戶要求的端點 URL | 
|||| 

*選擇性*

| 元素名稱 | 類型 | 說明 | 
| ------------ | ---- | ----------- | 
| 取消訂閱 | JSON 物件 | 連出要求，可在作業使觸發程序無效時自動呼叫並取消訂用帳戶。 如需詳細資訊，請參閱[訂閱和取消訂閱](#subscribe-unsubscribe)。 | 
| method | 字串 | 用於取消要求的 HTTP 方法："GET"、"PUT"、"POST"、"PATCH"、"DELETE" 或 "HEAD" | 
| uri | 字串 | 要傳送取消要求的端點 URL | 
| body | JSON 物件 | 描述訂用帳戶或取消要求的承載 (資料) 的 JSON 物件 | 
| 驗證 | JSON 物件 | 連入要求應用來進行驗證的方法。 如需詳細資訊，請參閱[排程器輸出驗證](../scheduler/scheduler-outbound-authentication.md)。 |
| RetryPolicy | JSON 物件 | 此物件可針對具有 4xx 或 5xx 狀態碼的間歇性錯誤，自訂重試行為： <p>`"retryPolicy": { "type": "<retry-policy-type>", "interval": "<retry-interval>", "count": <number-retry-attempts> }` <p>如需詳細資訊，請參閱[重試原則](../logic-apps/logic-apps-exception-handling.md)。 | 
|||| 

*範例*

```json
"myAppSpotTrigger": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
         "headers": {},
         "body": {
            "hub.callback": "@{listCallbackUrl()}",
            "hub.mode": "subscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      }
   },
}
```

<a name="subscribe-unsubscribe"></a>

### <a name="subscribe-and-unsubscribe"></a>`subscribe`和`unsubscribe`

當工作流程以任何方式發生變更時 (例如認證更新，或觸發程序的輸入參數變更時)，就會進行 `subscribe` 呼叫。 此呼叫使用與標準 HTTP 動作相同的參數。 
 
當作業使 HTTPWebhook 觸發程序無效時，就會自動進行 `unsubscribe` 呼叫，例如：

* 刪除或停用觸發程序。 
* 刪除或停用工作流程。 
* 刪除或停用訂用帳戶。 

為了支援這些呼叫，`@listCallbackUrl()` 函式會針對這個觸發程序傳回唯一的「回呼 URL」。 此 URL 代表使用服務 REST API 之端點的唯一識別碼。 此函式的參數與 HTTP 觸發程序的參數相同。

### <a name="httpwebhook-trigger-outputs"></a>HTTPWebhook 觸發程序輸出

| 元素名稱 | 類型 | 說明 |
| ------------ | ---- | ----------- |
| headers | JSON 物件 | HTTP 回應中的標頭 | 
| body | JSON 物件 | HTTP 回應中的主體 | 
|||| 

<a name="apiconnectionwebhook-trigger"></a>

## <a name="apiconnectionwebhook-trigger"></a>ApiConnectionWebhook 觸發程序

此觸發程序的運作方式與 [HTTPWebhook 觸發程序](#httpwebhook-trigger)類似，但會使用 [Microsoft 受控 API](../connectors/apis-list.md)。 

以下是觸發程序定義：

```json
"<ApiConnectionWebhookTriggerName>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },        
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "queries": "<query-parameters>"
   }
}
```

*必要*

| 元素名稱 | 類型 | 說明 | 
| ------------ | ---- | ----------- | 
| <*ApiConnectionWebhookTriggerName*> | JSON 物件 | 觸發程序的名稱，這是以 Javascript 物件標記法 (JSON) 格式描述的物件  | 
| type | 字串 | 觸發程序類型，也就是 "ApiConnectionWebhook" | 
| 輸入 | JSON 物件 | 定義觸發程序行為的觸發程序輸入 | 
| host | JSON 物件 | 描述受控 API 的主機閘道和識別碼的 JSON 物件 <p>`host` JSON 物件具有以下元素：`api` 和 `connection` | 
| connection | JSON 物件 | 工作流程所使用的受控 API 連線名稱，其必須包含名為 `$connection` 之參數的參考： <p>`"name": "@parameters('$connections')['<connection-name>']['connectionId']"` | 
| body | JSON 物件 | 描述要傳送到受控 API 的承載 (資料) 的 JSON 物件 | 
| NotificationUrl | 字串 | 針對這個觸發程序傳回受控 API 可以使用的唯一「回呼 URL」 | 
|||| 

*選擇性*

| 元素名稱 | 類型 | 說明 | 
| ------------ | ---- | ----------- | 
| 查詢 | JSON 物件 | 您想要包含在 URL 中的任何查詢參數 <p>例如，此元素會將 `?folderPath=Inbox` 查詢字串新增至 URL： <p>`"queries": { "folderPath": "Inbox" }` <p>結果︰`https://<managed-API-URL>?folderPath=Inbox` | 
|||| 

<a name="trigger-conditions"></a>

## <a name="triggers-conditions"></a>觸發程序：條件

對於任何觸發程序，您都可以包含具有一或多個條件的陣列，以判斷是否應該執行工作流程。 在此範例中，報告只會在工作流程的 `sendReports` 參數設定為 true 時引發。 

```json
"myDailyReportTrigger": {
   "type": "Recurrence",
   "conditions": [ {
      "expression": "@parameters('sendReports')"
   } ],
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

此外，條件可以參考觸發程序的狀態碼。 例如，假設您想要只在網站傳回 "500" 狀態碼時啟動工作流程︰

``` json
"conditions": [ {
   "expression": "@equals(triggers().code, 'InternalServerError')"  
} ]  
```  

> [!NOTE]
> 根據預設，觸發程序只會在收到 "200 OK" 回應時引發。 當任何運算式以任何方式參考觸發程序的狀態碼時，就會取代觸發程序的預設行為。 因此，如果想要針對多個狀態碼 (例如狀態碼 200 和 201) 來引發觸發程序，您必須加入這個陳述式作為您的條件： 
>
> `@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="triggers-split-an-array-into-multiple-runs"></a>觸發程序：將陣列分割為多個執行

如果觸發程序傳回要讓邏輯應用程式處理的陣列，有時候 "for each" 迴圈會花太多時間來處理每個陣列項目。 您可以改用觸發程序中的 **SplitOn** 屬性來對該陣列進行「解除批次」。 

解除批次會分割陣列項目，並啟動新邏輯應用程式執行個體，讓其針對每個陣列項目執行。 例如，當您想要輪詢的端點可能在輪詢間隔之間傳回多個新項目時，就適用此方法。
如需 **SplitOn** 在單一邏輯應用程式中可處理的陣列項目數上限，請參閱[限制和設定](../logic-apps/logic-apps-limits-and-config.md)。 

> [!NOTE]
> 在邏輯應用程式的 JSON 定義程式碼檢視中，您可以透過手動定義或覆寫的方式，只將 **SplitOn** 新增至觸發程序。 您無法在實作同步回應模式時使用 **SplitOn**。 任何使用 **SplitOn** 並包含回應動作的工作流程會以非同步的方式執行，且會立即傳送 `202 ACCEPTED` 回應。

如果觸發程序的 Swagger 說明承載屬於陣列，則 **SplitOn** 屬性會自動新增至您的觸發程序。 否則，就要在其中有陣列需要解除批次的回應承載內，新增此屬性。 

例如，假設您有一個會傳回以下回應的 API： 
  
```json
{
    "Status": "Succeeded",
    "Rows": [ 
        { 
            "id": 938109380,
            "name": "customer-name-one"
        },
        {
            "id": 938109381,
            "name": "customer-name-two"
        }
    ]
}
```
  
您的邏輯應用程式只需要來自 `Rows` 的內容，因此您可以建立如下列範例所示的觸發程序。

``` json
"myDebatchTrigger": {
    "type": "Http",
    "recurrence": {
        "frequency": "Second",
        "interval": 1
    },
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> 如果您使用 `SplitOn` 命令，就無法取得陣列外的屬性。 因此針對此範例，您就無法在 API 傳回的回應中取得 `status`屬性。
> 
> 為避免在 `?` 屬性不存在時發生失敗，此範例使用了 `Rows` 運算子。

您的工作流程定義現在可使用 `@triggerBody().name` 來取得第一個執行中的 `customer-name-one`，以及第二個執行中的 `customer-name-two`。 因此，觸發程序的輸出看起來就像下面這些範例︰

```json
{
    "body": {
        "id": 938109380,
        "name": "customer-name-one"
    }
}
```

```json
{
    "body": {
        "id": 938109381,
        "name": "customer-name-two"
    }
}
```

<a name="trigger-operation-options"></a>

## <a name="triggers-operation-options"></a>觸發程序：作業選項

以下觸發程序提供可讓您變更預設行為的其他選項。

| 觸發程序 | 作業選項 | 說明 |
|---------|------------------|-------------|
| [Recurrence](#recurrence-trigger)、 <br>[HTTP](#http-trigger)、 <br>[ApiConnection](#apiconnection-trigger) | singleInstance | 只在所有作用中的執行完成時引發觸發程序。 |
||||

<a name="single-instance"></a>

### <a name="triggers-fire-only-after-active-runs-finish"></a>觸發程序：只在作用中的執行完成後引發

對於您可設定週期的觸發程序，您可以指定觸發程序只在所有使用中執行完成後引發。 如果排定的週期在工作流程執行個體正在執行時發生，觸發程序就會略過，等到下一個排定的週期時才再次檢查。 例如︰

```json
"myRecurringTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Hour",
        "interval": 1,
    },
    "operationOptions": "singleInstance"
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>動作概觀

動作有許多類型，且各有各的獨特行為。 每種動作類型都有定義動作行為的不同輸入。 集合動作可在自身當中包含許多其他動作。 

### <a name="standard-actions"></a>標準動作  

| 動作類型 | 說明 | 
| ----------- | ----------- | 
| **HTTP** | 呼叫 HTTP Web 端點。 | 
| **ApiConnection**  | 運作方式與 HTTP 動作類似，但使用 [Microsoft 受控 API](https://docs.microsoft.com/azure/connectors/apis-list)。 | 
| **ApiConnectionWebhook** | 運作方式與 HTTPWebhook 類似，但使用 Microsoft 受控 API。 | 
| **回應** | 定義連入呼叫的回應。 | 
| **撰寫** | 從動作的輸入建構任意的物件。 | 
| **Function** | 代表 Azure 函式。 | 
| **Wait** | 等候一段固定的時間，或直到某個特定時間為止。 | 
| **Workflow** | 代表巢狀工作流程。 | 
| **撰寫** | 從動作的輸入建構任意的物件。 | 
| **查詢** | 根據條件來篩選陣列。 | 
| **選取** | 將陣列的每個元素投射成一個新的值。 例如，您可以將數字陣列轉換成物件陣列。 | 
| **資料表** | 將項目的陣列轉換為 CSV 或 HTML 資料表。 | 
| **Terminate** | 停止執行工作流程。 | 
| **Wait** | 等候一段固定的時間，或直到某個特定時間為止。 | 
| **Workflow** | 代表巢狀工作流程。 | 
||| 

### <a name="collection-actions"></a>集合動作

| 動作類型 | 說明 | 
| ----------- | ----------- | 
| **If** | 評估運算式，然後根據結果來執行對應的分支。 | 
| **Switch** | 根據物件的特定值來執行不同動作。 | 
| **ForEach** | 這個迴圈動作會逐一查看陣列，並對每個陣列項目執行內部動作。 | 
| **Until** | 這個迴圈動作會執行內部動作，直到條件的結果為 true 為止。 | 
| **範圍** | 用於將其他動作以邏輯方式分組。 | 
|||  

## <a name="http-action"></a>HTTP 動作  

HTTP 動作會呼叫指定的端點並檢查回應，以決定是否應該執行工作流程。 例如︰
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest"
    }
}
```

這裡的 `inputs` 物件會採用下列建構 HTTP 呼叫時所需的參數︰ 

| 元素名稱 | 必要 | 類型 | 說明 | 
| ------------ | -------- | ---- | ----------- | 
| method | yes | 字串 | 使用下列其中一種 HTTP 方法︰"GET"、"POST"、"PUT"、"DELETE"、"PATCH" 或 "HEAD" | 
| uri | yes| 字串 | 觸發程序所檢查的 HTTP 或 HTTPS 端點。 字串大小上限：2 KB | 
| 查詢 | 否 | JSON 物件 | 代表您想要包含在 URL 中的任何查詢參數。 <p>例如，`"queries": { "api-version": "2015-02-01" }` 會將 `?api-version=2015-02-01` 新增至 URL。 | 
| headers | 否 | JSON 物件 | 代表要求中所傳送的每個標頭。 <p>例如，若要對要求設定語言和類型︰ <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | 否 | JSON 物件 | 代表傳送至端點的承載。 | 
| RetryPolicy | 否 | JSON 物件 | 請使用此物件來自訂 4xx 或 5xx 錯誤的重試行為。 如需詳細資訊，請參閱[重試原則](../logic-apps/logic-apps-exception-handling.md)。 | 
| operationsOptions | 否 | 字串 | 定義一組要覆寫的特殊行為。 | 
| 驗證 | 否 | JSON 物件 | 代表要求應用來進行驗證的方法。 如需詳細資訊，請參閱[排程器輸出驗證](../scheduler/scheduler-outbound-authentication.md)。 <p>除了排程器之外，還有一個支援的屬性︰`authority`。 根據預設，若未指定，此值會是 `https://login.windows.net`，但您可以使用不同的值，例如 `https://login.windows\-ppe.net`。 | 
||||| 

HTTP 動作和 APIConnection 動作支援「重試原則」。 重試原則適用於間歇性失敗，其典型是 HTTP 狀態碼 408、429 與 5xx，以及任何連線例外狀況。 您可以使用 `retryPolicy` 物件來定義此原則，如以下所示：
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

這個範例 HTTP 動作會在有間歇性失敗時重新嘗試擷取最新的消息兩次，總共的執行次數為三次，每次嘗試之間會延遲 30 秒︰
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest",
        "retryPolicy": {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```

重試間隔會以 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601)來指定。 此間隔的最小值 (也是預設值) 為 20 秒，最大值為 1 小時。 最大重試計數 (也是預設值) 為 4 小時。 如果未指定重試原則定義，則會使用 `fixed` 策略搭配預設的重試計數和間隔值。 若要停用重試原則，請將其類型設定為 `None`。

### <a name="asynchronous-patterns"></a>非同步模式

根據預設，所有 HTTP 型動作皆支援標準的非同步作業模式。 因此，如果遠端伺服器藉由「202 已接受」回應指出已接受要求來進行處理，Logic Apps 引擎就會持續輪詢回應之 location 標頭中所指定的 URL，直到達到中止狀態 (亦即非 202 回應) 為止。
  
若要停用先前所述的非同步行為，請在動作輸入中將 `operationOptions` 設定為 `DisableAsyncPattern`。 在此案例中，動作的輸出是根據伺服器所傳來的初始 202 回應。 例如︰
  
```json
"invokeLongRunningOperationAction": {
    "type": "Http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```

<a name="asynchronous-limits"></a>

#### <a name="asynchronous-limits"></a>非同步限制

您可以將非同步模式的持續時間限制在特定的時間間隔內。 如果在經過時間間隔後仍未達到終止狀態，動作的狀態就會標示為 `Cancelled`，而代碼會是 `ActionTimedOut`。 限制逾時是以 ISO 8601 格式來指定。 此範例會示範指定限制的方式：


``` json
"<action-name>": {
    "type": "Workflow|Webhook|Http|ApiConnectionWebhook|ApiConnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="apiconnection-action"></a>APIConnection 動作

這個動作會參考由 Microsoft 控管的連接器，因此需要有效連線的參考，以及 API 與參數的相關資訊。 以下是一個範例 APIConnection 動作：

```json
"Send_Email": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "POST",
        "body": {
            "Subject": "New tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
}
```

| 元素名稱 | 必要 | 類型 | 說明 | 
| ------------ | -------- | ---- | ----------- | 
| host | yes | JSON 物件 | 代表連接器資訊，例如 `runtimeUrl` 和對連線物件的參考。 | 
| method | yes | 字串 | 使用下列其中一種 HTTP 方法︰"GET"、"POST"、"PUT"、"DELETE"、"PATCH" 或 "HEAD" | 
| path | yes | 字串 | API 作業的路徑 | 
| 查詢 | 否 | JSON 物件 | 代表您想要包含在 URL 中的任何查詢參數。 <p>例如，`"queries": { "api-version": "2015-02-01" }` 會將 `?api-version=2015-02-01` 新增至 URL。 | 
| headers | 否 | JSON 物件 | 代表要求中所傳送的每個標頭。 <p>例如，若要對要求設定語言和類型︰ <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | 否 | JSON 物件 | 代表傳送至端點的承載。 | 
| RetryPolicy | 否 | JSON 物件 | 請使用此物件來自訂 4xx 或 5xx 錯誤的重試行為。 如需詳細資訊，請參閱[重試原則](../logic-apps/logic-apps-exception-handling.md)。 | 
| operationsOptions | 否 | 字串 | 定義一組要覆寫的特殊行為。 | 
| 驗證 | 否 | JSON 物件 | 代表要求應用來進行驗證的方法。 如需詳細資訊，請參閱[排程器輸出驗證](../scheduler/scheduler-outbound-authentication.md)。 |
||||| 

重試原則適用於間歇性失敗，其典型是 HTTP 狀態碼 408、429 與 5xx，以及任何連線例外狀況。 您可以使用 `retryPolicy` 物件來定義此原則，如以下所示：

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

## <a name="apiconnection-webhook-action"></a>APIConnectionWebhook 動作

APIConnectionWebhook 動作會參考 Microsoft 受控連接器。 這個動作需要對有效連線的參考，以及 API 與參數的相關資訊。 您可以採用與 [HTTP 非同步限制](#asynchronous-limits)相同的方式，來對 Webhook 動作指定限制。

```json
"Send_approval_email": {
    "type": "ApiConnectionWebhook",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "body": {
            "Message": {
                "Subject": "Approval Request",
                "Options": "Approve, Reject",
                "Importance": "Normal",
                "To": "me@email.com"
            }
        },
        "path": "/approvalmail",
        "authentication": "@parameters('$authentication')"
    },
    "runAfter": {}
}
```

| 元素名稱 | 必要 | 類型 | 說明 | 
| ------------ | -------- | ---- | ----------- | 
| host | yes | JSON 物件 | 代表連接器資訊，例如 `runtimeUrl` 和對連線物件的參考。 | 
| path | yes | 字串 | API 作業的路徑 | 
| 查詢 | 否 | JSON 物件 | 代表您想要包含在 URL 中的任何查詢參數。 <p>例如，`"queries": { "api-version": "2015-02-01" }` 會將 `?api-version=2015-02-01` 新增至 URL。 | 
| headers | 否 | JSON 物件 | 代表要求中所傳送的每個標頭。 <p>例如，若要對要求設定語言和類型︰ <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | 否 | JSON 物件 | 代表傳送至端點的承載。 | 
| RetryPolicy | 否 | JSON 物件 | 請使用此物件來自訂 4xx 或 5xx 錯誤的重試行為。 如需詳細資訊，請參閱[重試原則](../logic-apps/logic-apps-exception-handling.md)。 | 
| operationsOptions | 否 | 字串 | 定義一組要覆寫的特殊行為。 | 
| 驗證 | 否 | JSON 物件 | 代表要求應用來進行驗證的方法。 如需詳細資訊，請參閱[排程器輸出驗證](../scheduler/scheduler-outbound-authentication.md)。 |
||||| 

## <a name="response-action"></a>回應動作  

這個動作包含來自 HTTP 要求的整個回應承載，也包括 `statusCode`、`body` 及 `headers`：
  
```json
"myResponseAction": {
    "type": "Response",
    "inputs": {
        "statusCode": 200,
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        },
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        }
    },
    "runAfter": {}
}
```

回應動作有其他動作所不適用的特殊限制，具體而言就是：  
  
* 邏輯應用程式定義內的平行分支中不能有回應動作，因為連入要求必須有確定性的回應。
  
* 如果工作流程在連入要求已收到回應之後才達到回應動作，系統就會將該回應動作視為失敗或發生衝突。 如此一來，邏輯應用程式回合就會標示為 `Failed`。
  
* 含有回應動作的工作流程無法使用觸發程式定義中的 `splitOn` 命令，因為該呼叫會建立多個回合。 因此，當工作流程作業為 PUT 時，請檢查是否有此情況，並傳回「不正確的要求」回應。

## <a name="compose-action"></a>撰寫動作

此動作可讓您建構任意的物件，而輸出則為評估動作之輸入的結果。 

> [!NOTE]
> 您可以使用 `Compose`動作來建構任何輸出，包括物件、陣列以及邏輯應用程式原生支援的任何其他類型，例如 XML 和二進位檔。

例如，您可以使用 `Compose` 動作來合併多個動作的輸出︰

```json
"composeUserRecordAction": {
    "type": "Compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
    }
}
```

## <a name="function-action"></a>函式動作

此動作可讓您表示及呼叫 [Azure 函式](../azure-functions/functions-overview.md)，例如：

```json
"<my-Azure-Function-name>": {
   "type": "Function",
    "inputs": {
        "function": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/sites/<your-Azure-function-app-name>/functions/<your-Azure-function-name>"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()"
        },
        "method": "POST",
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| 元素名稱 | 必要 | 類型 | 說明 | 
| ------------ | -------- | ---- | ----------- |  
| 函式識別碼 | yes | 字串 | 您想要呼叫之 Azure 函式的資源識別碼。 | 
| method | 否 | 字串 | 用來呼叫函式的 HTTP 方法。 若未指定，則預設方法為 "POST"。 | 
| 查詢 | 否 | JSON 物件 | 代表您想要包含在 URL 中的任何查詢參數。 <p>例如，`"queries": { "api-version": "2015-02-01" }` 會將 `?api-version=2015-02-01` 新增至 URL。 | 
| headers | 否 | JSON 物件 | 代表要求中所傳送的每個標頭。 <p>例如，若要對要求設定語言和類型︰ <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | 否 | JSON 物件 | 代表傳送至端點的承載。 | 
|||||

當您儲存邏輯應用程式時，Logic Apps 引擎會對所參考的函式執行一些檢查︰

* 您必須要能夠存取該函式。
* 您只能使用標準 HTTP 觸發程序或一般 JSON Webhook 觸發程序。
* 函式不應該有任何已定義的路由。
* 只允許使用「函式」和「匿名」授權層級。

> [!NOTE]
> Logic Apps 引擎會擷取及快取在執行階段上使用的觸發程序 URL。 因此，若有任何作業讓快取的 URL 失效，動作就會在執行階段失敗。 若要解決這個問題，請再次儲存邏輯應用程式，這會讓邏輯應用程式再次擷取及快取觸發程序 URL。

## <a name="select-action"></a>選取動作

此動作可讓您將陣列的每個元素投射成一個新的值。 此範例會將數字陣列轉換成物件陣列：

```json
"selectNumbersAction": {
    "type": "Select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

| Name | 必要 | 類型 | 說明 | 
| ---- | -------- | ---- | ----------- | 
| from | yes | 陣列 | 來源陣列 |
| 選取 | yes | 任意 | 套用至來源陣列中每個元素的投射 |
||||| 

`select` 動作的輸出是一個基數與輸入陣列相同的陣列。 每個元素會依據 `select` 屬性的定義進行轉換。 如果輸入是空的陣列，則輸出也是空的陣列。

## <a name="terminate-action"></a>終止動作

此動作會停止工作流程執行、取消進行中的任何動作，以及略過任何其餘的動作。 終止動作不會影響已完成的動作。

例如，停止具有 `Failed` 狀態的執行：

```json
"HandleUnexpectedResponse": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response",
        }
    }
}
```

| Name | 必要 | 類型 | 說明 | 
| ---- | -------- | ---- | ----------- | 
| runStatus | yes | 字串 | 目標執行的狀態 (`Failed` 或 `Cancelled`) |
| runError | 否 | JSON 物件 | 錯誤詳細資料。 只有當 `runStatus` 已設定為 `Failed`時才支援。 |
| runError 代碼 | 否 | 字串 | 執行的錯誤碼 |
| runError 訊息 | 否 | 字串 | 執行的錯誤訊息 | 
||||| 

## <a name="query-action"></a>查詢動作

此動作可讓您根據條件來篩選陣列。 

> [!NOTE]
> 您不可使用撰寫動作來建構任何輸出，包括物件、陣列以及邏輯應用程式原生支援的其他任何類型，例如 XML 和二進位檔。

例如，若要選取大於 2 的數字︰

```json
"filterNumbersAction": {
    "type": "Query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

| Name | 必要 | 類型 | 說明 | 
| ---- | -------- | ---- | ----------- | 
| from | yes | 陣列 | 來源陣列 |
| 其中 | yes | 字串 | 套用至來源陣列中各個元素的條件。 如果沒有任何值符合 `where` 條件，則結果為空白陣列。 |
||||| 

`query` 動作的輸出是一個陣列，其中具有輸入陣列中符合條件的元素。

## <a name="table-action"></a>資料表動作

此動作可讓您將陣列轉換成 CSV 或 HTML 資料表。 

```json
"ConvertToTable": {
    "type": "Table",
    "inputs": {
        "from": "<source-array>",
        "format": "CSV | HTML"
    }
}
```

| Name | 必要 | 類型 | 說明 | 
| ---- | -------- | ---- | ----------- | 
| from | yes | 陣列 | 來源陣列。 如果 `from` 屬性值為空的陣列，則輸出為空的資料表。 | 
| format | yes | 字串 | 您想要的資料表格式 ("CSV" 或 "HTML") | 
| columns | 否 | 陣列 | 您想要的資料表資料行。 用來覆寫預設資料表圖形。 | 
| 資料行標頭 | 否 | 字串 | 資料行標頭 | 
| 資料行值 | yes | 字串 | 資料行值 | 
||||| 

假設您定義一個如以下範例的資料表動作：

```json
"convertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "HTML"
    }
}
```

並針對 `@triggerBody()` ：

```json
[ {"ID": 0, "Name": "apples"},{"ID": 1, "Name": "oranges"} ]
```

以下是此範例的輸出：

<table><thead><tr><th>ID</th><th>Name</th></tr></thead><tbody><tr><td>0</td><td>apples</td></tr><tr><td>1</td><td>oranges</td></tr></tbody></table>

為了自訂此資料表，您可以明確指定資料行，例如：

```json
"ConvertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html",
        "columns": [ 
            {
                "header": "Produce ID",
                "value": "@item().id"
            },
            {
              "header": "Description",
              "value": "@concat('fresh ', item().name)"
            }
        ]
    }
}
```

以下是此範例的輸出：

<table><thead><tr><th>產生識別碼</th><th>說明</th></tr></thead><tbody><tr><td>0</td><td>fresh apples</td></tr><tr><td>1</td><td>fresh oranges</td></tr></tbody></table>

## <a name="wait-action"></a>等候動作  

此動作會在一段指定間隔內暫停工作流程的執行。 以下範例會讓工作流程等候 15 分鐘：
  
```json
"waitForFifteenMinutesAction": {
    "type": "Wait",
    "inputs": {
        "interval": {
            "unit": "minute",
            "count": 15
        }
    }
}
```
  
或者，若要等候直到特定時間點，您可以使用此範例︰
  
```json
"waitUntilOctoberAction": {
    "type": "Wait",
    "inputs": {
        "until": {
            "timestamp": "2017-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> 您可以使用 `interval` 物件或 `until` 物件 (但不能同時使用這兩者)，來指定等候時間。

| 元素名稱 | 必要 | 類型 | 說明 | 
| ------------ | -------- | ---- | ----------- | 
| 直到 | 否 | JSON 物件 | 以時間點為基礎的等候持續時間 | 
| 直到時間戳記 | yes | 字串 | 等候時間到期時的時間點 (以 [UTC 日期時間格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)指定) | 
| interval | 否 | JSON 物件 | 以間隔單位和計數為基礎的等候持續時間 | 
| 間隔單位 | yes | 字串 | 時間單位。 只能使用下列其中一個值︰"second"、"minute"、"hour"、"day"、"week" 或 "month" | 
| 間隔計數 | yes | 整數  | 一個正整數，此正整數代表用於表示等候持續時間的間隔單位數 | 
||||| 

## <a name="workflow-action"></a>工作流程動作

此動作可讓您巢狀化工作流程。 Logic Apps 引擎會對子工作流程 (更具體地說是觸發程序) 執行存取權檢查，因此您必須具備子工作流程的存取權。 例如︰

```json
"<my-nested-workflow-action-name>": {
    "type": "Workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/<my-subscription-ID>/resourceGroups/<my-resource-group-name>/providers/Microsoft.Logic/<my-nested-workflow-action-name>",
            "triggerName": "mytrigger001"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        },
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| 元素名稱 | 必要 | 類型 | 說明 | 
| ------------ | -------- | ---- | ----------- |  
| 主機識別碼 | yes | 字串| 您想要呼叫之工作流程的資源識別碼 | 
| 主機 triggerName | yes | 字串 | 您想要叫用之觸發程序的名稱 | 
| 查詢 | 否 | JSON 物件 | 代表您想要包含在 URL 中的任何查詢參數。 <p>例如，`"queries": { "api-version": "2015-02-01" }` 會將 `?api-version=2015-02-01` 新增至 URL。 | 
| headers | 否 | JSON 物件 | 代表要求中所傳送的每個標頭。 <p>例如，若要對要求設定語言和類型︰ <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | 否 | JSON 物件 | 代表傳送至端點的承載。 | 
||||| 

此動作的輸出會根據您在子工作流程之 `Response` 動作中所做的定義。 如果子工作流程未定義 `Response` 動作，則輸出會是空的。

## <a name="collection-actions-overview"></a>集合動作概觀

為協助您控制工作流程執行，集合動作可以包含其他動作。 您可以直接在集合外部參考集合內的參考動作。 例如，如果您在某個範圍中定義 `Http` 動作，則 `@body('http')` 在工作流程中的任何位置仍然保持有效。 此外，集合內的動作都只可在相同集合內其他動作「完成後執行」。

## <a name="if-action"></a>If 動作

此動作是條件陳述式，可讓您評估條件，並根據運算式是否評估為 true 來執行分支。 如果條件成功評估為 true，此條件就會標示為「成功」狀態。 `actions` 或 `else` 物件中動作會評估為下列值：

* 「Succeeded (成功)」：當動作執行且成功時
* 「Failed (失敗)」：當動作執行但失敗時
* 「Skipped (略過)」：當個別的分支並未實行時

深入了解[邏輯應用程式中的條件陳述式](../logic-apps/logic-apps-control-flow-conditional-statement.md)。

``` json
"<my-condition-name>": {
  "type": "If",
  "expression": "<condition>",
  "actions": {
    "if-true-run-this-action": {
      "type": <action-type>,
      "inputs": {},
      "runAfter": {}
    }
  },
  "else": {
    "actions": {
        "if-false-run-this-action": {
            "type": <action-type>,
            "inputs": {},
            "runAfter": {}
        }
    }
  },
  "runAfter": {}
}
```

| Name | 必要 | 類型 | 說明 | 
| ---- | -------- | ---- | ----------- | 
| 動作 | yes | JSON 物件 | `expression` 評估為 `true` 時要執行的內部動作 | 
| expression | yes | 字串 | 要評估的運算式 |
| else | 否 | JSON 物件 | `expression` 評估為 `false` 時要執行的內部動作 |
||||| 

例如︰

```json
"myCondition": {
    "type": "If",
    "actions": {
        "if-true-check-this-website": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://this-url"
            },
            "runAfter": {}
        }
    },
    "else": {
        "actions": {
            "if-false-check-this-other-website": {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://this-other-url"
                },
                "runAfter": {}
            }
        }
    }
}
```  

### <a name="how-conditions-can-use-expressions-in-actions"></a>條件如何在動作中使用運算式

以下是說明如何在條件中使用運算式的範例︰
  
| JSON 運算式 | 結果 | 
| --------------- | ------ | 
| `"expression": "@parameters('hasSpecialAction')"` | 任何會評估為 true 的值都會促使此條件成立。 僅支援布林運算式。 若要將其他類型轉換成布林值，請使用函式：`empty` 或 `equals` | 
| `"expression": "@greater(actions('action1').output.value, parameters('threshold'))"` | 支援比較函式。 就此範例而言，只有當 action1 的輸出大於閾值時，才會執行動作。 | 
| `"expression": "@or(greater(actions('action1').output.value, parameters('threshold')), less(actions('action1').output.value, 100))"` | 支援使用邏輯函式來建立巢狀布林運算式。 在此範例中，只有當 action1 的輸出超出閾值或低於 100 時，才會執行動作。 | 
| `"expression": "@equals(length(actions('action1').outputs.errors), 0))"` | 若要檢查陣列是否有任何項目，您可以使用陣列函式。 在此範例中，當錯誤陣列空白時，就會執行動作。 | 
| `"expression": "parameters('hasSpecialAction')"` | 此運算式會造成錯誤，且不是有效的條件。 條件必須使用 "@" 符號。 | 
||| 

## <a name="switch-action"></a>Switch 動作

此動作是 Switch 陳述式，會根據物件、運算式或權杖的特定值執行不同動作。 此動作會評估物件、運算式或權杖，然後選擇符合結果的案例，並只針對該案例執行動作。 沒有案例符合結果時，會執行預設動作。 Switch 陳述式執行時，應該只會有一個案例符合結果。 深入了解[邏輯應用程式中的 Switch 陳述式](../logic-apps/logic-apps-control-flow-switch-statement.md)。

``` json
"<my-switch-statement-name>": {
   "type": "Switch",
   "expression": "<evaluate-this-object-expression-token>",
   "cases": {
      "myCase1": {
         "actions": {
           "myAction1": {}
         },
         "case": "<result1>"
      },
      "myCase2": {
         "actions": {
           "myAction2": {}
         },
         "case": "<result2>"
      }
   },
   "default": {
      "actions": {
          "myDefaultAction": {}
      }
   },
   "runAfter": {}
}
```

| Name | 必要 | 類型 | 說明 | 
| ---- | -------- | ---- | ----------- | 
| expression | yes | 字串 | 要評估的物件、運算式或權杖 | 
| 案例 | yes | JSON 物件 | 包含依循運算式結果執行的內部動作集。 | 
| 案例 | yes | 字串 | 要與結果比對的值 | 
| 動作 | yes | JSON 物件 | 案例符合運算式結果時執行的內部動作 | 
| 預設值 | 否 | JSON 物件 | 沒有案例符合結果時執行的內部動作 | 
||||| 

例如︰

``` json
"myApprovalEmailAction": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {...}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
           "Send_an_email_2": {...}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

## <a name="foreach-action"></a>Foreach 動作

這個迴圈動作會逐一查看陣列，並對每個陣列項目執行內部動作。 根據預設，Foreach 迴圈會平行執行。 如需 "for each" 迴圈可平行執行的循環數上限，請參閱[限制和設定](../logic-apps/logic-apps-limits-and-config.md)。若要以循序方式執行每個循環，請將 `operationOptions` 參數設定為 `Sequential`。 深入了解[邏輯應用程式中的 Foreach 迴圈](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)。

```json
"<my-forEach-loop-name>": {
    "type": "Foreach",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    },
    "foreach": "<array>",
    "runAfter": {}
}
```

| Name | 必要 | 類型 | 說明 | 
| ---- | -------- | ---- | ----------- | 
| 動作 | yes | JSON 物件 | 要在迴圈內執行的內部動作 | 
| foreach | yes | 字串 | 要逐一查看的陣列 | 
| operationOptions | 否 | 字串 | 指定任何作業選項來自訂行為。 目前僅支援 `Sequential` 來循序執行反覆運算，而預設行為是平行。 |
||||| 

例如︰

```json
"forEach_EmailAction": {
    "type": "Foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "Send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "foreach": "@body('email_filter')",
    "runAfter": {
        "email_filter": [ "Succeeded" ]
    }
}
```

## <a name="until-action"></a>直到動作

這個迴圈動作會執行內部動作，直到條件評估為 true 為止。 深入了解[邏輯應用程式中的 "until" 迴圈](../logic-apps/logic-apps-control-flow-loops.md#until-loop)。

```json
 "<my-Until-loop-name>": {
    "type": "Until",
    "actions": {
        "myActionName": {
            "type": "<action-type>",
            "inputs": {},
            "runAfter": {}
        }
    },
    "expression": "<myCondition>",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

| Name | 必要 | 類型 | 說明 | 
| ---- | -------- | ---- | ----------- | 
| 動作 | yes | JSON 物件 | 要在迴圈內執行的內部動作 | 
| expression | yes | 字串 | 要在每次反覆運算之後評估的運算式 | 
| limit | yes | JSON 物件 | 迴圈的限制。 必須至少定義一個限制。 | 
| count | 否 | 整數  | 要執行之反覆運算次數的限制 | 
| timeout | 否 | 字串 | 指定迴圈應該執行多久的逾時限制 (以 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601) 指定) |
||||| 

例如︰

```json
 "runUntilSucceededAction": {
    "type": "Until",
    "actions": {
        "Http": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 100,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

## <a name="scope-action"></a>範圍動作

此動作可讓您以邏輯方式將工作流程中的動作分組。 當範圍中的所有動作都執行完成之後，範圍也會取得自己的狀態。 深入了解[範圍](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)。

```json
"<my-scope-action-name>": {
    "type": "Scope",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    }
}
```

| Name | 必要 | 類型 | 說明 | 
| ---- | -------- | ---- | ----------- |  
| 動作 | yes | JSON 物件 | 要在範圍內執行的內部動作 |
||||| 

## <a name="next-steps"></a>後續步驟

* 深入了解[工作流程定義語言](../logic-apps/logic-apps-workflow-definition-language.md)
* 深入了解[工作流程 REST API](https://docs.microsoft.com/rest/api/logic/workflows)