---
title: V2 至 V3 API 移轉
titleSuffix: Azure Cognitive Services
description: 第 3 版端點已變更的 Api。 您可以使用本指南來了解如何移轉至第 3 版端點的 Api。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 4c08c95a05d4f22e2338a7264409aec0f64a4755
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442511"
---
# <a name="preview-migrate-to-api-version-3x-for-luis-apps"></a>預覽：移轉至的 API 版本 3.x 的 LUIS 應用程式

查詢預測端點已變更的 Api。 您可以使用本指南來了解如何移轉至第 3 版端點的 Api。 

此 V3 API 提供下列新功能，包括重大的 JSON 要求和/或回應變更： 

* [外部實體](#external-entities-passed-in-at-prediction-time)
* [動態清單](#dynamic-lists-passed-in-at-prediction-time)
* [預先建置的實體 JSON 變更](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

查詢預測 endpoint[要求](#request-changes)並[回應](#response-changes)有重大的變更，以支援包括下列新功能，以上所列：

* [回應物件的變更](#top-level-json-changes)
* [實體的角色名稱參考而不是實體名稱](#entity-role-name-instead-of-entity-name)
* [將標示談話中的實體的屬性](#marking-placement-of-entities-in-utterances)

LUIS 功能如下**不支援**V3 API 中：

* Bing 拼字檢查 V7

[參考文件](https://aka.ms/luis-api-v3)供 V3。

## <a name="endpoint-url-changes-by-slot-name"></a>端點 URL 所做的變更位置名稱

V3 端點 HTTP 呼叫的格式已變更。

|方法|URL|
|--|--|
|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0 預覽</b>/apps/&lt<b>{應用程式-識別碼}</b>/slots/<b>{位置-名稱}</b>/ 預測？查詢 =<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|||

## <a name="endpoint-url-changes-by-version-id"></a>端點 URL 所做的變更版本識別碼

如果您想要查詢的版本，您必須先[經由 API 發佈](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b)使用`"directVersionPublish":true`。 查詢參考的版本識別碼，而不是位置名稱的端點。


|方法|URL|
|--|--|
|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0 預覽</b>/apps/&lt<b>{應用程式-識別碼}</b>/versions/<b>{版本-識別碼}</b>/預測？ 查詢 =<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0 預覽</b>/apps/&lt<b>{應用程式-識別碼}</b>/versions/<b>{版本-識別碼}</b>/預測|
|||

## <a name="prebuilt-entities-with-new-json"></a>預先建置的實體，使用新的 JSON

V3 回應物件變更包括[預先建置的實體](luis-reference-prebuilt-entities.md)。 

## <a name="request-changes"></a>要求變更 

### <a name="query-string-parameters"></a>查詢字串參數

V3 API 有不同的查詢字串參數。

|參數名稱|類型|Version|預設值|目的|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|記錄檔中的存放區查詢。| 
|`query`|string|僅限第 3 版|沒有預設值-須在 GET 要求|**在 V2**，以預測 utterance 處於`q`參數。 <br><br>**V3**，功能會傳入`query`參數。|
|`show-all-intents`|boolean|僅限第 3 版|false|傳回所有使用中的對應分數的意圖**prediction.intents**物件。 意圖會傳回父系中的物件為`intents`物件。 如此不必尋找目的陣列中以程式設計方式存取： `prediction.intents.give`。 在 V2 中，這些已傳回陣列中。 |
|`verbose`|boolean|V2 & V3|false|**在 V2**時傳回設為 true，所有預測的意圖。 如果您需要所有預測的對應方式，使用的 V3 param `show-all-intents`。<br><br>**V3**，這個參數只提供實體的實體預測的中繼資料詳細資料。  |



<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>查詢預測 JSON 主體`POST`要求

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "overridePredictions": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|屬性|類型|Version|預設值|目的|
|--|--|--|--|--|
|`dynamicLists`|array|僅限第 3 版|不需要。|[動態清單](#dynamic-lists-passed-in-at-prediction-time)可讓您擴充現有的定型和發佈清單實體，已在 LUIS 應用程式。|
|`externalEntities`|array|僅限第 3 版|不需要。|[外部實體](#external-entities-passed-in-at-prediction-time)可讓您的 LUIS 應用程式識別，並在執行階段，可用來做為現有實體的特徵標記實體。 |
|`options.datetimeReference`|string|僅限第 3 版|沒有預設值|用來判斷[datetimeV2 位移](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)。|
|`options.overridePredictions`|boolean|僅限第 3 版|false|指定如果使用者的[（與現有的實體名稱相同） 的外部實體](#override-existing-model-predictions)使用或用於預測模型中的現有實體。 |
|`query`|string|僅限第 3 版|必要。|**在 V2**，以預測 utterance 處於`q`參數。 <br><br>**V3**，功能會傳入`query`參數。|



## <a name="response-changes"></a>回應變更

在查詢回應，JSON 會變更為允許更高以程式設計方式存取最常使用的資料。 

### <a name="top-level-json-changes"></a>上方層級的 JSON 變更

V2 的最上層 JSON 屬性，當`verbose`設定為 true，傳回所有的對應方式以及其分數中的`intents`屬性：

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

V3 的最上層 JSON 屬性︰

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "normalizedQuery": "this is your utterance you want predicted - after normalization",
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

`intents`物件是未排序的清單。 請勿假設在第一個子系`intents`對應至`topIntent`。 請改用`topIntent`尋找分數的值：

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

允許回應的 JSON 結構描述變更：

* 清除原始 utterance，區別`query`，並傳回預測， `prediction`。
* 更輕鬆地以程式設計方式存取預測的資料。 而不是列舉 V2 中的陣列，您可以存取值所**名為**的意圖和實體。 預測的實體的角色，因為它是唯一的整個應用程式，就會傳回一個角色名稱。
* 資料型別，如果判斷，會遵守。 數值不再是以字串傳回。
* 中的第一優先權預測資訊與其他中繼資料，傳回`$instance`物件。 

### <a name="access-instance-for-entity-metadata"></a>存取`$instance`實體中繼資料

如果您需要實體中繼資料時，需要使用查詢字串`verbose=true`旗標和回應會包含在中繼資料`$instance`物件。 範例會顯示下列各節中的 JSON 回應中。

### <a name="each-predicted-entity-is-represented-as-an-array"></a>預測的每個實體被以陣列

`prediction.entities.<entity-name>`物件包含的陣列，因為每個實體可以在 [utterance] 的一次進行預測。 

### <a name="list-entity-prediction-changes"></a>實體預測變更的清單

JSON 清單實體預測已變更為陣列的陣列：

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
每個內部陣列會對應至在 [utterance] 內的文字。 內部物件是一個陣列，因為相同的文字會出現在清單中實體的一個以上的子清單。 

當之間的對應`entities`物件至`$instance`物件，物件的順序時，清單實體預測會保留。

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>實體的角色名稱，而不是實體名稱 

在 V2 中，`entities`傳回預測的所有實體與實體的名稱的唯一識別碼陣列。 V3，如果實體使用角色，並預測是針對實體角色的主要識別碼。 角色名稱 這可能是因為實體的角色名稱必須是唯一的整個應用程式包括其他的模型 (意圖，entity) 名稱。

在下列範例： 考慮包括文字，utterance `Yellow Bird Lane`。 此文字會預測為自訂`Location`實體的角色`Destination`。

|[Utterance] 文字方塊|實體名稱|角色名稱|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

在 V2 中，實體由_實體名稱_以角色為物件的屬性：

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

在 V3 中所參考的實體_實體的角色_，如果預測是針對角色：

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

V3，在相同產生與`verbose`旗標，以傳回實體的中繼資料：

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

## <a name="external-entities-passed-in-at-prediction-time"></a>在預測時傳入的外部實體

外部實體可讓您的 LUIS 應用程式識別，並在執行階段，可用來做為現有實體的特徵標記實體。 這可讓您使用您自己的個別和自訂實體擷取器，再將查詢傳送給您預測的端點。 因為這是在查詢預測端點，您不需要重新定型和發佈您的模型。

用戶端應用程式提供自己的實體擷取器可藉由管理實體比對和判斷該相符實體的 [utterance] 內的位置，然後再傳送該要求的資訊。 

外部實體受限於擴充任何實體類型，同時仍然使用作為其他的模型，例如角色、 複合及其他訊號的機制。

這是適用於只能在查詢預測執行階段具有可用資料的實體。 資料或特定的每位使用者，經常會變更此類型之資料的範例。 您可以擴充使用者的連絡人清單中的外部資訊與 LUIS 連絡實體。 

### <a name="entity-already-exists-in-app"></a>實體已經存在於應用程式

值`entityName`的端點要求 POST 主體中傳遞的外部實體必須存在於定型和發佈應用程式在提出要求的時間。 實體的型別並不重要，支援所有類型。

### <a name="first-turn-in-conversation"></a>在交談中的第一次開啟

請考量其中使用者會輸入下列資訊不完整的聊天機器人交談中的第一個 [utterance]:

`Send Hazem a new message`

聊天機器人 LUIS 的要求也可以傳入 POST 主體中的資訊關於`Hazem`因此它會直接與做為其中一個使用者的連絡人。

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

預測回應會包含該外部的實體，與所有其他預測實體，因為它定義在要求中。  

### <a name="second-turn-in-conversation"></a>在交談中的第二個回合

聊天機器人到下一步 的使用者 utterance 會使用更含糊不清的詞彙：

`Send him a calendar reminder for the party.`

先前的 [utterance]，在 [utterance] 會使用`him`做為參考， `Hazem`。 交談式的聊天機器人，在 POST 主體中，可以將對應`him`第一個 [utterance] 中，從擷取的實體值`Hazem`。

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

預測回應會包含該外部的實體，與所有其他預測實體，因為它定義在要求中。  

### <a name="override-existing-model-predictions"></a>覆寫現有的模型預測

`overridePredictions` Options 屬性指定，若使用者傳送外部實體重疊與預測實體同名，LUIS 會選擇傳入的實體或現有的模型中的實體。 

例如，請考量 `today I'm free` 查詢。 LUIS 會偵測`today`為 datetimeV2 下列回應：

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

若使用者傳送外部的實體：

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

如果`overridePredictions`設為`false`，LUIS 會傳回回應，如同未傳送外部的實體。 

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

如果`overridePredictions`設為`true`，LUIS 會傳回回應，包括：

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>解決方案

_選擇性_`resolution`預測回應，可讓您傳入與外部的實體，相關聯的中繼資料，則會收到它的屬性會傳回在回應中傳回。 

主要用途是將預先建置的實體，但不限於該實體類型。 

`resolution`屬性可以是數字、 字串、 物件或陣列：

* 「 Dallas 」
* {"text": "value"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>在預測時傳入的動態清單

動態清單可讓您擴充現有的定型和發佈清單實體，已在 LUIS 應用程式。 

當您清單的實體值需要定期變更，請使用此功能。 這項功能可讓您擴充已經定型和發佈清單實體：

* 在查詢預測端點要求的時間。
* 針對單一要求。

清單實體可以是空的 LUIS 應用程式中，但它必須存在。 清單中的實體 LUIS 應用程式不會變更，但在端點上的預測能力已擴充而納入大約 1,000 個項目具有的最多 2 個清單。

### <a name="dynamic-list-json-request-body"></a>動態清單 JSON 要求主體

將下列 JSON 主體，以將新的子清單，使用同義字新增至清單中，並預測文字、 清單實體`LUIS`，使用`POST`查詢預測要求：

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntityName":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

預測回應會包含該清單之實體的所有其他預測實體，因為它定義在要求中。 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>重新命名為 datetimeReference TimezoneOffset

**在 V2**，則`timezoneOffset`[參數](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)是要求中傳送預測做為查詢字串參數，而不論如果要求傳送以 GET 或 POST 要求。 

**V3**，使用 POST 的主體參數，提供相同的功能`datetimeReference`。 

## <a name="marking-placement-of-entities-in-utterances"></a>標示談話中的實體的位置

**在 V2**，實體已在 [utterance] 與標示`startIndex`和`endIndex`。 

**V3**，以將此實體標記`startIndex`和`entityLength`。

## <a name="deprecation"></a>淘汰 

V2 API 不會針對至少 V3 預覽結束後 9 個月被取代。 

## <a name="next-steps"></a>後續步驟

使用 V3 API 文件來更新現有的 REST 呼叫 LUIS[端點](https://aka.ms/luis-api-v3)Api。 