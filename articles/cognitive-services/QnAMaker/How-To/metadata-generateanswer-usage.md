---
title: 中繼資料與 GenerateAnswer API - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 可讓您將索引鍵/值組形式的中繼資料新增至問答集。 這項資訊可用來篩選使用者查詢的結果，並儲存待處理的交談中可用的其他資訊。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/16/2019
ms.author: tulasim
ms.openlocfilehash: c18ededc428b215720f8a6a6857a2eabd93bff8b
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683573"
---
# <a name="get-a-knowledge-answer-with-the-generateanswer-api-and-metadata"></a>取得與 GenerateAnswer API 和中繼資料的知識答案

若要取得使用者的問題的預測的答案，請使用 GenerateAnswer API。 當您發行知識庫時，這項資訊來使用此 API 會顯示在 [發行] 頁面上。 您也可以設定 API 來篩選根據中繼資料標記的答案，並測試 「 知識庫 」 從測試查詢字串參數的端點。

QnA Maker 可讓您將索引鍵和值組形式的中繼資料新增至問答集。 這項資訊可用來篩選使用者查詢的結果，並儲存待處理的交談中可用的其他資訊。 如需詳細資訊，請參閱[知識庫](../Concepts/knowledge-base.md)。

<a name="qna-entity"></a>

## <a name="storing-questions-and-answers-with-a-qna-entity"></a>儲存與 QnA 實體的問題和答案

了解 QnA Maker 如何儲存問題/解答資料，是第一要務。 下圖說明某個 QnA 實體：

![QnA 實體](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

每個 QnA 實體都有唯一且持續性的識別碼。 此識別碼可以用來對特定 QnA 實體進行更新。

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>取得使用 GenerateAnswer API 的回應預測

您可以在聊天機器人或應用程式中使用 GenerateAnswer API 來查詢使用者問題的知識庫，而從問答集中取得最佳相符項目。

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>若要取得 GenerateAnswer 端點發行 

在您從 [QnA Maker 入口網站](https://www.qnamaker.ai)或使用 [API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) 發佈知識庫之後，您可以取得 GenerateAnswer 端點的詳細資料。

若要取得端點詳細資料：
1. 登入 [https://www.qnamaker.ai](https://www.qnamaker.ai)。
1. 在 [我的知識庫] 中，對您的知識庫按一下 [檢視程式碼]。
    ![我的知識庫](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. 取得 GenerateAnswer 端點詳細資料。

    ![端點詳細資料](../media/qnamaker-how-to-metadata-usage/view-code.png)

您也可以從知識庫的 [設定] 索引標籤取得端點詳細資料。

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer 要求設定

您可以透過 HTTP POST 要求來呼叫 GenerateAnswer。 如需示範如何呼叫 GenerateAnswer 的範例程式碼，請參閱[快速入門](../quickstarts/csharp.md)。

**要求 URL**具有下列格式： 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

|HTTP 要求的屬性|Name|類型|目的|
|--|--|--|--|
|URL 路由參數|知識庫識別碼|字串|測試您知識庫的 GUID。|
|URL 路由參數|QnAMaker 端點主機|字串|您的 Azure 訂用帳戶中，部署端點的主機名稱。 在發行知識庫之後，這是可在 [設定] 頁面上取得。 |
|頁首|Content-Type|字串|傳送至 API 的本文媒體類型。 預設值是: '|
|頁首|授權|字串|您的端點金鑰 (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)。|
|張貼本文|JSON 物件|JSON|與設定問題|


JSON 主體具有數個設定：

|JSON 本文屬性|必要項|類型|目的|
|--|--|--|--|
|`question`|必要|字串|使用者問題傳送到您的知識庫。|
|`top`|選用|integer|要包含在輸出中的排名結果數目。 預設值為 1。|
|`userId`|選用|字串|用來識別使用者的唯一識別碼。 此識別碼會記錄在交談記錄中。|
|`isTest`|選用|布林值|如果為 true，傳回結果集`testkb`搜尋服務索引，而不是已發行的索引。|
|`strictFilters`|選用|字串|如果指定，將會指示 QnA Maker 僅傳回含有指定中繼資料的解答。 使用`none`以指出回應應該會有任何中繼資料篩選。 |

範例 JSON 主體看起來像：

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer 回應屬性

成功的回應會傳回狀態 200 和 JSON 回應。 

|答案屬性 （依分數排序）|目的|
|--|--|
|分數|0 和 100 之間的排名分數。|
|id|指派給解答的唯一識別碼。|
|問題|使用者所提供的問題。|
|接聽|問題的答案。|
|來源|從中擷取解答或將其儲存在知識庫中的來源名稱。|
|中繼資料|與解答相關聯的中繼資料。|
|metadata.name|中繼資料名稱。 (字串，最大長度：100，必要)|
|metadata.value:中繼資料值。 (字串，最大長度：100，必要)|


```json
{
    "answers": [
        {
            "score": 28.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

<a name="metadata-example"></a>

## <a name="using-metadata-allows-you-to-filter-answers-by-custom-metadata-tags"></a>使用中繼資料可讓您自訂的中繼資料標記來篩選的解答

新增中繼資料，可讓您依這些中繼資料標記篩選的解答。 請考慮下面的常見問題集資料。 請按一下中繼資料圖示，將中繼資料新增至至您的知識庫。

![新增中繼資料](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>使用中繼資料標記的 strictFilters 篩選結果

請考量使用者針對 "Paradise" 餐廳而提出的問題 「飯店何時會打烊」。

由於只需要 "Paradise" 餐廳的結果，因此您可以在 GenerateAnswer 呼叫中設定「餐廳名稱」中繼資料的篩選條件，如下所示。

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>使用問題和答案的結果來保存交談內容

GenerateAnswer 的回應會包含相符的問題/答案組的對應中繼資料資訊。 這項資訊可以用於用戶端應用程式，以更新版本的交談中儲存先前用於交談的內容。 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>後續步驟

發佈頁面也會提供資訊，搭配 [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) 和 [cURL](../Quickstarts/get-answer-from-kb-using-curl.md) 來產生答案。 

> [!div class="nextstepaction"]
> [建立知識庫](./create-knowledge-base.md)
