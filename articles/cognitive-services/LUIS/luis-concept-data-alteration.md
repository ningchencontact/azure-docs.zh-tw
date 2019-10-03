---
title: 資料改變-LUIS
titleSuffix: Azure Cognitive Services
description: 了解如何在於 Language Understanding (LUIS) 中進行預測之前變更資料
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: diberry
ms.openlocfilehash: 734389c92ede88d336df60a1a79a738d2abcfa92
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703167"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>預測之前或預測期間變更語句資料
LUIS 提供可在預測之前或預測期間操作語句的方法。 其中包括[修正拼寫](luis-tutorial-bing-spellcheck.md)，以及修正預先建立之[datetimeV2](luis-reference-prebuilt-datetimev2.md)的時區問題。 

## <a name="correct-spelling-errors-in-utterance"></a>校正語句中的拼字錯誤
LUIS 使用 [Bing 拼字檢查 API V7](../Bing-Spell-Check/overview.md) 來校正語句中的拼字錯誤。 LUIS 需要與該服務相關的金鑰。 請建立金鑰，然後在[端點](https://go.microsoft.com/fwlink/?linkid=2092356)新增該金鑰作為查詢字串參數。 

您也可以在 [Test] \(測試\) 面板中[輸入金鑰](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel)來更正拼字錯誤。 金鑰會以工作階段變數的形式保留在 [Test] \(測試\) 面板的瀏覽器中。 請在您想要校正拼字的每個瀏覽器工作階段中，將金鑰新增至 [Test] \(測試\) 面板。 

在測試面板及端點的金鑰使用會計入[金鑰使用量](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/)配額中。 LUIS 實作「Bing 拼字檢查」的文字長度限制。 

端點必須有兩個參數，才能讓拼字校正運作：

|參數|值|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|[Bing 拼字檢查 API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) 端點金鑰|

當 [Bing 拼字檢查 API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) 偵測到錯誤時，系統會將原始語句和校正後語句及預測一起從端點傳回。

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 預測端點回應](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 預測端點回應](#tab/V3)
 
```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * * 

### <a name="list-of-allowed-words"></a>允許的單字清單
LUIS 中使用的 Bing 拼寫檢查 API 不支援在拼寫檢查改變期間忽略的字詞清單。 如果您需要允許單字或縮略字清單，請先處理用戶端應用程式中的語句，再將語句傳送至 LUIS 進行意圖預測。

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>變更預先建置 datetimeV2 實體的時區
當 LUIS 應用程式使用預先建立的[datetimeV2](luis-reference-prebuilt-datetimev2.md)實體時，可以在預測回應中傳回 datetime 值。 要求的時區會用來判斷要傳回的正確日期時間。 如果要求來自 Bot 或另一個集中式應用程式，請在其抵達 LUIS 之前，先更正 LUIS 使用的時區。 

### <a name="endpoint-querystring-parameter"></a>端點查詢字串參數
更正時區的方式是使用 `timezoneOffset` 參數將使用者時區新增至[端點](https://go.microsoft.com/fwlink/?linkid=2092356)。 `timezoneOffset` 的值應該是用以變更時間的正數或負數 (以分鐘為單位)。  

|參數|值|
|--|--|
|`timezoneOffset`|正數或負數 (以分鐘為單位)|

### <a name="daylight-savings-example"></a>日光節約範例
如果您需要讓傳回的預先建置 datetimeV2 針對日光節約時間進行調整，則應該針對[端點](https://go.microsoft.com/fwlink/?linkid=2092356)查詢，使用 `timezoneOffset` 查詢字串參數搭配以分鐘為單位的 +/- 值。

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2 預測端點要求](#tab/V2)

增加 60 分鐘： 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

減去 60 分鐘： 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 預測端點要求](#tab/V3)

增加 60 分鐘：

HTTPs：//{region}. luis/v 3.0-預覽/應用程式/{appId}/插槽/生產/預測？ query = 開啟燈？**timezoneOffset = 60**& 拼字檢查 = {boolean} & bing-拼寫檢查-訂用帳戶-機碼 = {string} & log = {boolean}

減去 60 分鐘： 

HTTPs：//{region}. luis/v 3.0-預覽/應用程式/{appId}/插槽/生產/預測？ query = 開啟燈？**timezoneOffset =-60**& 拼字檢查 = {boolean} & bing-拼寫檢查-訂用帳戶-機碼 = {string} & log = {boolean}

深入瞭解[V3 預測端點](luis-migration-api-v3.md)。

* * * 

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>C# 程式碼可判斷 timezoneOffset 的正確值
下列 C# 程式碼使用 [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) 類別的 [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) 方法，根據系統時間判斷正確的 `timezoneOffset`：

```CSharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用此教學課程來校正拼字錯誤](luis-tutorial-bing-spellcheck.md)
