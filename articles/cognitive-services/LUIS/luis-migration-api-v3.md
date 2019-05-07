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
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 80bc1e6558a9c68d92ce13f8f4c991699d61b9d3
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074520"
---
# <a name="preview-migrate-to-api-version-3x--for-luis-apps"></a>預覽：移轉至的 API 版本 3.x 的 LUIS 應用程式

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

[參考文件](https://aka.ms/luis-preview-api-v3)供 V3。

## <a name="prebuilt-domains-with-new-models-and-language-coverage"></a>預先建置的網域與新的模型和語言涵蓋範圍

檢閱[V3 API 預先建置的網域清單](luis-reference-prebuilt-domains.md)。 這些網域會更完整，在模型和語言涵蓋範圍。 

## <a name="prebuilt-entities-with-new-json"></a>預先建置的實體，使用新的 JSON

下列預先建置的實體會將 JSON 結構描述變更的 V3 API:

* [年齡](luis-reference-prebuilt-age.md#preview-api-version-3x)
* [Currency （貨幣）](luis-reference-prebuilt-currency.md#preview-api-version-3x)
* [DateTimeV2](luis-reference-prebuilt-datetimev2.md#preview-api-version-3x)
* [維度](luis-reference-prebuilt-dimension.md#preview-api-version-3x)
* [電子郵件](luis-reference-prebuilt-email.md#preview-api-version-3x)
* [GeographyV2](luis-reference-prebuilt-geographyv2.md#preview-api-version-3x)
* [Number](luis-reference-prebuilt-number.md#preview-api-version-3x)
* [序數](luis-reference-prebuilt-ordinal.md#preview-api-version-3x)
* [百分比](luis-reference-prebuilt-percentage.md#preview-api-version-3x)
* [PersonName](luis-reference-prebuilt-person.md#preview-api-version-3x)
* [Phonenumber](luis-reference-prebuilt-phonenumber.md#preview-api-version-3x)
* [温度](luis-reference-prebuilt-temperature.md#preview-api-version-3x)
* [URL](luis-reference-prebuilt-url.md#preview-api-version-3x)

## <a name="request-changes"></a>要求變更 

### <a name="query-string-parameters"></a>查詢字串參數

V3 API 有不同的查詢字串參數。

|參數名稱|Type|版本|目的|
|--|--|--|--|
|`query`|string|僅限第 3 版|**在 V2**，以預測 utterance 處於`q`參數。 <br><br>**V3**，功能會傳入`query`參數。|
|`show-all-intents`|boolean|僅限第 3 版|傳回所有使用中的對應分數的意圖**prediction.intents**物件。 意圖會傳回父系中的物件為`intents`物件。 如此不必尋找目的陣列中以程式設計方式存取： `prediction.intents.give`。 在 V2 中，這些已傳回陣列中。 |
|`verbose`|boolean|V2 & V3|**在 V2**時傳回設為 true，所有預測的意圖。 如果您需要所有預測的對應方式，使用的 V3 param `show-all-intents`。<br><br>**V3**，這個參數只提供實體的實體預測的中繼資料詳細資料。  |

<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>查詢預測 JSON 主體`POST`要求

```JSON
{
    "query":"your utterance here",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

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

`Send Hazem a new message`其中`Hazem`做為其中一個使用者的連絡人會直接比對。

<!--

In a [multi-intent](#detect-multiple-intents-within-single-utterance) utterance, you can use the external entity data to help with secondary references. For example, in the utterance `Send Hazem a new message, and let him know about the party.`, two segments of the utterance are predicted:

* `Send Hazem a new message, and`
* `let him know about the party.`

The first segment can correctly predict Hazem when the external entity is sent with the prediction request. The second segment won't know that `him` is a secondary reference to the same data unless you send it with the request and mark it as the same entity.

-->

### <a name="external-entities-json-request-body"></a>外部實體的 JSON 要求主體 

將下列 JSON 主體中傳送至標記的人員名稱， `Hazem`，為外部實體與`POST`查詢預測要求：

```JSON
{
    "query": "Send Hazem a new message.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "externalEntities": [
        {
            "entityName":"my-entity-name-already-in-LUIS-app",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employee": "program manager",
                "type": "individual contributor"
            }
        }
    ]
}
```

預測回應會包含該外部的實體，與所有其他預測實體，因為它定義在要求中。  

#### <a name="entity-already-exists-in-app"></a>實體已經存在於應用程式

實體名稱， `my-entity-name-already-in-LUIS-app`，提出要求時存在於定型和發佈應用程式。 實體的型別並不重要，支援所有類型。

#### <a name="resolution"></a>解決方案

_選擇性_`resolution`預測回應，可讓您傳入與外部的實體，相關聯的中繼資料，則會收到它的屬性會傳回在回應中傳回。 

主要用途是將預先建置的實體，但不限於該實體類型。 

`resolution`屬性可以是數字、 字串、 物件或陣列：

* 「 Dallas 」
* {"text": "value"}
* 12345 
* ["a", "b", "c"]

<!--
Returned JSON response is:

```JSON
not sure what to do here
```
-->

## <a name="dynamic-lists-passed-in-at-prediction-time"></a>在預測時傳入的動態清單

動態清單可讓您更新及延伸現有的定型和發佈清單實體，已在 LUIS 應用程式。 

當您清單的實體值需要定期變更，請使用此功能。 這項功能可讓您更新已訓練，並且已發行之清單的實體：

* 在查詢預測端點要求的時間。
* 針對單一要求。

清單實體可以是空的 LUIS 應用程式中，但它必須存在。 

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

<!--


## Detect multiple intents within single utterance

This feature identifies multiple intents from an utterance, enabling better understanding of complex and compound utterances that include more than one action. There is not prerequisite, or change needed to support this, in the LUIS app for this feature to work. It happens at the query prediction runtime if the associated query string parameter is passed in. 

The V3 query prediction endpoint supports multi-intent query predictions if `multiple-segments=true` is passed in the query string. This means each sentence can have its own intent prediction.

You can use `multiple-segments=true` with `verbose=true` to get the entity metadata for each individual segment.

If multiple segments are not identified, value of the `MultipleSegments` property in the response is `none`.

In **Review endpoint utterances**, the segments are displayed and not the whole query.

In the endpoint logs, an additional boolean column indicates if the query is a multi-intent prediction.

In the V2 endpoint success response, the entire utterance is predicted to a single intent.

### Multiple intents JSON response

In the V3 endpoint success response, each segment is predicted including entities:

```json
{
    "query": "Carol goes to Cairo and Mohamed attends the meeting",
    "prediction": {
        "normalizedQuery": "carol goes to cairo and mohamed attends the meeting",
        "topIntent": "Meetings",
        "intents": {
            "Travel": {
                "score": 0.6123635
            },
            "MultipleSegments": {
                "segments": [
                    {
                        "normalizedQuery": "Carol goes to Cairo and",
                        "topIntent": "Travel",
                        "intents": {
                            "Travel": {
                                "score": 0.6826963
                            }
                        },
                        "entities": {
                            "geographyV2": [
                                "Cairo"
                            ],
                            "personName": [
                                "Carol"
                            ]
                        }
                    },
                    {
                        "normalizedQuery": "and Mohamed attends the meeting",
                        "topIntent": "Meetings",
                        "intents": {
                            "Meetings": {
                                "score": 0.7100854
                            }
                        },
                        "entities": {
                            "personName": [
                                "Mohamed"
                            ]
                        }
                    }
                ]
            }
        },
        "entities": {
            "geographyV2": [
                "Cairo"
            ],
            "personName": [
                "Carol",
                "Mohamed"
            ]
        }
    }
}
```

### Segment splitting tokens

Segments are split based on tokens such as:

Verbs are the primary splitting tokens. For example, where N represents a noun and V represents a verb, and you have an utterance schema that can be defined as `N V N N V N N`, the two segments will be `N V N N`, and `V N N`. 

LUIS doesn't split into segments when:

* Utterance has consecutive verbs. 
* Entity is at the end of the utterance.

--->

## <a name="marking-placement-of-entities-in-utterances"></a>標示談話中的實體的位置

**在 V2**，實體已在 [utterance] 與標示`startIndex`和`endIndex`。 

**V3**，以將此實體標記`startIndex`和`entityLength`。


## <a name="next-steps"></a>後續步驟

使用 V3 API 文件來更新現有的 REST 呼叫 LUIS[端點](https://aka.ms/luis-api-v3)Api。 