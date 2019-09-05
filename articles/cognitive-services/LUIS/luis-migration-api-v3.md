---
title: V2 至 V3 API 遷移
titleSuffix: Azure Cognitive Services
description: 第3版端點 Api 已變更。 使用本指南來瞭解如何遷移至第3版端點 Api。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: diberry
ms.openlocfilehash: 5b0516f3d610c0a518d6afc461dddebfb68a7c5d
ms.sourcegitcommit: ac29357a47cc05afdf0f84834de5277598f4d87c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70213526"
---
# <a name="preview-migrate-to-api-version-3x-for-luis-apps"></a>預覽：遷移至 LUIS apps 的 API 3.x 版

查詢預測端點 Api 已變更。 使用本指南來瞭解如何遷移至第3版端點 Api。 

此 V3 API 提供下列新功能，其中包括重要的 JSON 要求和/或回應變更： 

* [外部實體](#external-entities-passed-in-at-prediction-time)
* [動態清單](#dynamic-lists-passed-in-at-prediction-time)
* [預先建立的實體 JSON 變更](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

查詢預測端點[要求](#request-changes)和[回應](#response-changes)有重大變更，可支援上述的新功能，包括下列各項：

* [回應物件變更](#top-level-json-changes)
* [實體角色名稱參考，而不是機構名稱](#entity-role-name-instead-of-entity-name)
* [要在語句中標記實體的屬性](#marking-placement-of-entities-in-utterances)

V3 API 中**不支援**下列 LUIS 功能：

* Bing 拼寫檢查 V7

[參考檔](https://aka.ms/luis-api-v3)適用于 V3。

## <a name="endpoint-url-changes-by-slot-name"></a>依位置名稱的端點 URL 變更

V3 端點 HTTP 呼叫的格式已變更。

|方法|URL|
|--|--|
|GET|HTTPs://<b>{REGION}</b>。 api.cognitive.microsoft.com/luis/<b>v3.0-Preview</b>/APPS/<b>{應用程式識別碼}</b>/slots/<b>{位置名稱}</b>/predict？ query =<b>{query}</b>|
|POST|HTTPs://<b>{REGION}</b>。 api.cognitive.microsoft.com/luis/<b>v3.0-Preview</b>/APPS/<b>{應用程式識別碼}</b>/slots/<b>{位置名稱}</b>/predict|
|||

插槽的有效值：

* `production`
* `staging`

## <a name="endpoint-url-changes-by-version-id"></a>端點 URL 變更（依版本識別碼）

如果您想要依版本進行查詢，您必須先透過[API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b)與`"directVersionPublish":true`發行。 查詢參考版本識別碼的端點，而不是位置名稱。


|方法|URL|
|--|--|
|GET|HTTPs://<b>{REGION}</b>。 api.cognitive.microsoft.com/luis/<b>v3.0-Preview</b>/APPS/<b>{應用程式識別碼}</b>/versions/<b>{版本識別碼}</b>/predict？ query =<b>{query}</b>|
|POST|HTTPs://<b>{REGION}</b>。 api.cognitive.microsoft.com/luis/<b>v3.0-Preview</b>/APPS/<b>{應用程式識別碼}</b>/versions/<b>{版本識別碼}</b>/predict|
|||

## <a name="prebuilt-entities-with-new-json"></a>使用新的 JSON 預建實體

V3 回應物件變更包括預先建立的[實體](luis-reference-prebuilt-entities.md)。 

## <a name="request-changes"></a>要求變更 

### <a name="query-string-parameters"></a>查詢字串參數

V3 API 有不同的查詢字串參數。

|參數名稱|Type|Version|預設|用途|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|將查詢儲存在記錄檔中。| 
|`query`|string|僅限第 3 版|無預設值-GET 要求中需要它|**在 V2 中**，要預測的語句是在`q`參數中。 <br><br>**在 V3**中，此功能會在`query`參數中傳遞。|
|`show-all-intents`|boolean|僅限第 3 版|false|傳回在**預測. 意圖**物件中具有對應分數的所有意圖。 意圖會以物件的形式傳回父`intents`物件。 這可讓您以程式設計方式存取，而不需要在`prediction.intents.give`陣列中尋找意圖：。 在 V2 中，這些會在陣列中傳回。 |
|`verbose`|boolean|V2 & V3|false|**在第2版中**，當設定為 true 時，會傳回所有預測意圖。 如果您需要所有預測的`show-all-intents`意圖，請使用的 V3 參數。<br><br>**在 V3 中**，這個參數只提供實體預測的實體中繼資料詳細資料。  |



<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>`POST`要求的查詢預測 JSON 主體

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

|屬性|Type|Version|預設|用途|
|--|--|--|--|--|
|`dynamicLists`|陣列|僅限第 3 版|不需要。|[動態清單](#dynamic-lists-passed-in-at-prediction-time)可讓您擴充現有的已定型和已發佈清單實體（已在 LUIS 應用程式中）。|
|`externalEntities`|陣列|僅限第 3 版|不需要。|[外部實體](#external-entities-passed-in-at-prediction-time)可讓您的 LUIS 應用程式在執行時間中識別實體並為其加上標籤，以做為現有實體的功能。 |
|`options.datetimeReference`|string|僅限第 3 版|無預設值|用來判斷[datetimeV2 位移](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)。 DatetimeReference 的格式為[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)。|
|`options.overridePredictions`|boolean|僅限第 3 版|false|指定是否使用使用者的[外部實體（具有與現有實體相同的名稱）](#override-existing-model-predictions) ，或模型中的現有實體用於預測。 |
|`query`|string|僅限第 3 版|必要。|**在 V2 中**，要預測的語句是在`q`參數中。 <br><br>**在 V3**中，此功能會在`query`參數中傳遞。|



## <a name="response-changes"></a>回應變更

查詢回應 JSON 已變更為允許以程式設計方式存取最常使用的資料。 

### <a name="top-level-json-changes"></a>最上層 JSON 變更

當設定為 true 時`verbose` ，V2 的最上層 JSON 屬性是，它會傳回`intents`屬性中的所有意圖和其分數：

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

V3 的最上層 JSON 屬性包括：

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

<!--
The `alteredQuery` contains spelling corrections. This corresponds to the V2 API property `alteredQuery`.  
-->

`intents`物件是未排序的清單。 請勿假設中`intents`的第一個子系對應`topIntent`至。 相反地，請`topIntent`使用值來尋找分數：

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

回應 JSON 架構變更允許：

* 清除原始語句、 `query`和傳回的預測之間的`prediction`區別。
* 以程式設計方式輕鬆存取預測的資料。 您可以透過**名稱**來存取意圖和實體的值，而不是列舉 V2 中的陣列。 對於預測實體角色，會傳回角色名稱，因為它在整個應用程式中都是唯一的。
* 會遵守資料類型（如果已決定）。 數值不會再以字串傳回。
* 在物件中傳回的第一個優先順序預測資訊和其他元`$instance`資料之間的區別。 

### <a name="access-instance-for-entity-metadata"></a>實體`$instance`中繼資料的存取

如果您需要實體中繼資料，則查詢字串必須使用`verbose=true`旗標，而回應會包含`$instance`物件中的中繼資料。 下列各節的 JSON 回應中會顯示範例。

### <a name="each-predicted-entity-is-represented-as-an-array"></a>每個預測實體都會以陣列表示

`prediction.entities.<entity-name>`物件包含陣列，因為每個實體可以在語句中預測一次以上。 

### <a name="list-entity-prediction-changes"></a>列出實體預測變更

清單實體預測的 JSON 已變更為陣列陣列：

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
每個內部陣列都會對應至語句內的文字。 內建物件是一個陣列，因為相同的文字可能會出現在清單實體的一個以上子清單中。 

在`entities` 物件`$instance`與物件之間進行對應時，會保留物件的順序供清單實體預測之用。

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>實體角色名稱，而不是機構名稱 

在 V2 中， `entities`陣列傳回所有具有機構名稱的預測實體為唯一識別碼。 在 V3 中，如果實體使用角色，而預測是針對實體角色，則主要識別碼是角色名稱。 這是可行的，因為在整個應用程式中，實體角色名稱必須是唯一的，包括其他模型（意圖、實體）名稱。

在下列範例中：請考慮包含文字`Yellow Bird Lane`的語句。 此文字會預測為的自`Location`定義實體`Destination`角色。

|語句文字|實體名稱|角色名稱|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

在 V2 中，實體是以_機構名稱_識別，並以角色作為物件的屬性：

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

在 V3 中，實體是由_實體角色_參考，如果是針對角色的預測：

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

在 V3 中，具有`verbose`傳回實體中繼資料之旗標的相同結果：

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

## <a name="external-entities-passed-in-at-prediction-time"></a>在預測時間傳入的外部實體

外部實體可讓您的 LUIS 應用程式在執行時間中識別實體並為其加上標籤，以做為現有實體的功能。 這可讓您在將查詢傳送至預測端點之前，先使用自己的個別和自訂實體擷取器。 因為這是在查詢預測端點上完成，所以您不需要重新定型和發行您的模型。

用戶端應用程式會藉由管理實體比對，以及判斷該相符實體的語句中的位置，然後透過要求傳送該資訊，來提供自己的實體解壓縮程式。 

外部實體是擴充任何實體類型的機制，同時仍會用來做為其他模型的信號，例如角色、複合等。

這適用于只有查詢預測執行時間可以使用資料的實體。 這類資料的範例包括經常變更的資料，或每位使用者的特定。 您可以使用使用者連絡人清單中的外部資訊來擴充 LUIS contact 實體。 

### <a name="entity-already-exists-in-app"></a>實體已存在於應用程式中

外部實體（ `entityName`在端點要求張貼本文中傳遞）的值必須已存在於提出要求時，已定型和已發佈的應用程式中。 實體的類型並不重要，所有類型都受到支援。

### <a name="first-turn-in-conversation"></a>第一次開啟對話

請考慮在聊天機器人交談中的第一個語句，其中使用者會輸入下列未完成的資訊：

`Send Hazem a new message`

從聊天機器人到 LUIS 的要求可以傳入張貼本文中有關`Hazem`的資訊，因此它會直接與其中一個使用者連絡人進行比對。

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

預測回應會包含該外部實體，以及所有其他的預測實體，因為它是在要求中定義。  

### <a name="second-turn-in-conversation"></a>第二回合交談

下一個在聊天機器人中語句的使用者會使用更不清楚的詞彙：

`Send him a calendar reminder for the party.`

在先前的語句中，語句會`him`使用做為的`Hazem`參考。 POST 主體中的交談式聊天機器人可以對應`him`到從第一個`Hazem`語句中解壓縮的實體值。

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

預測回應會包含該外部實體，以及所有其他的預測實體，因為它是在要求中定義。  

### <a name="override-existing-model-predictions"></a>覆寫現有的模型預測

`overridePredictions` Options 屬性指定如果使用者傳送的外部實體與具有相同名稱的預測實體重迭，LUIS 會選擇傳入的實體或模型中現有的實體。 

例如，請考量 `today I'm free` 查詢。 LUIS 會`today`偵測為具有下列回應的 datetimeV2：

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

如果使用者傳送外部實體：

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

如果設定為`false`，則 LUIS會傳迴響應，就像未傳送外部實體一樣。`overridePredictions` 

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

如果設定為`true`，則 LUIS會傳迴響應，包括：`overridePredictions`

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>解析度

選擇性`resolution`屬性會在預測回應中傳回，讓您傳入與外部實體相關聯的中繼資料，然後在回應中接收它。 

主要的目的是要擴充預先建立的實體，但不限於該實體類型。 

`resolution`屬性可以是數位、字串、物件或陣列：

* 舉行
* {"text": "value"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>在預測時間傳入的動態清單

動態清單可讓您擴充現有的已定型和已發佈清單實體（已在 LUIS 應用程式中）。 

當您的清單實體值需要定期變更時，請使用這項功能。 這項功能可讓您擴充已定型和已發佈的清單實體：

* 在查詢預測端點要求時。
* 適用于單一要求。

清單實體在 LUIS 應用程式中可以是空的，但它必須存在。 LUIS 應用程式中的清單實體不會變更，但在端點上的預測功能會擴充為包含最多2個具有大約1000專案的清單。

### <a name="dynamic-list-json-request-body"></a>動態清單 JSON 要求主體

傳送下列 JSON 主體，將含有同義字的新子清單新增至清單，並`LUIS` `POST`使用查詢預測要求來預測文字的清單實體：

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
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

預測回應會包含該清單實體，以及所有其他預測的實體，因為它是在要求中定義。 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>TimezoneOffset 已重新命名為 datetimeReference

**在 V2 中**， `timezoneOffset` [參數](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)會以查詢字串參數的形式在預測要求中傳送，不論要求是以 GET 或 POST 要求傳送。 

**在 V3 中**，相同的功能會與 POST 主體參數`datetimeReference`一起提供。 

## <a name="marking-placement-of-entities-in-utterances"></a>標記實體在語句中的位置

**在 V2 中**，實體已在具有和`startIndex` `endIndex`的語句中標記。 

**在 V3 中**，實體會以`startIndex`和`entityLength`標記。

## <a name="deprecation"></a>淘汰 

在 V3 preview 之後，V2 API 將不會被淘汰至少9個月。 

## <a name="next-steps"></a>後續步驟

使用 V3 API 檔，將現有的 REST 呼叫更新為 LUIS[端點](https://aka.ms/luis-api-v3)api。 
