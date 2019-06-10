---
title: 中繼資料與 GenerateAnswer API - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 可讓您將索引鍵/值組形式的中繼資料新增至問答集。 您可以篩選使用者查詢的結果，並將可用的其他資訊儲存在待處理的交談。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/30/2019
ms.author: tulasim
ms.openlocfilehash: b18d47b4b09c6fa9c4d5f0ef87d7ebe73f151c60
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693237"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>取得與 GenerateAnswer API 和中繼資料的答案

若要取得使用者的問題的預測的答案，請使用 GenerateAnswer API。 當您發行知識庫時，您可以看到如何在使用此 API 的相關資訊**發佈**頁面。 您也可以設定 API 來篩選根據中繼資料標記的答案，並測試 「 知識庫 」 從測試查詢字串參數的端點。

QnA Maker 可讓您將中繼資料，索引鍵和值組的形式新增至您的問題和答案組。 然後，用來篩選使用者查詢的結果，並將可用的其他資訊儲存在待處理的交談，您可以使用這項資訊。 如需詳細資訊，請參閱[知識庫](../Concepts/knowledge-base.md)。

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>問與答與 QnA 實體存放區

請務必了解 QnA Maker 的問題和答案資料的儲存方式。 下圖說明某個 QnA 實體：

![QnA 實體的圖例](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

每個 QnA 實體都有唯一且持續性的識別碼。 您可以使用識別碼對特定的 QnA 實體進行更新。

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>取得使用 GenerateAnswer API 的回應預測

您使用 GenerateAnswer API，在您的 bot 或應用程式來查詢您的使用者問題的知識庫，若要發揮的問題和解答的最佳相符項目設定。

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>若要取得 GenerateAnswer 端點發行 

發行您的知識庫，從之後[QnA Maker 入口網站](https://www.qnamaker.ai)，或使用[API](https://go.microsoft.com/fwlink/?linkid=2092179)，您可以取得 GenerateAnswer 端點的詳細資料。

若要取得端點詳細資料：
1. 登入 [https://www.qnamaker.ai](https://www.qnamaker.ai)。
1. 在 **我的知識庫**，選取**檢視程式碼**針對您的知識庫。
    ![我的螢幕擷取畫面的知識庫](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. 取得 GenerateAnswer 端點詳細資料。

    ![端點詳細資料的螢幕擷取畫面](../media/qnamaker-how-to-metadata-usage/view-code.png)

您也可以從知識庫的 [設定]  索引標籤取得端點詳細資料。

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>GenerateAnswer 要求設定

您可以透過 HTTP POST 要求來呼叫 GenerateAnswer。 如需示範如何呼叫 GenerateAnswer 的範例程式碼，請參閱[快速入門](../quickstarts/csharp.md)。

**要求 URL**具有下列格式： 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

|HTTP 要求的屬性|名稱|類型|目的|
|--|--|--|--|
|URL 路由參數|知識庫識別碼|字串|測試您知識庫的 GUID。|
|URL 路由參數|QnAMaker 端點主機|字串|您的 Azure 訂用帳戶中，部署端點的主機名稱。 這是隨**設定**頁面之後發行知識庫。 |
|頁首|Content-Type|string|傳送至 API 的本文媒體類型。 預設值是: '|
|頁首|授權|字串|您的端點金鑰 (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)。|
|張貼本文|JSON 物件|JSON|與設定問題。|


JSON 主體具有數個設定：

|JSON 本文屬性|必要項|類型|目的|
|--|--|--|--|
|`question`|必要|字串|使用者問題傳送到您的知識庫。|
|`top`|選用|integer|要包含在輸出中的排名結果數目。 預設值為 1。|
|`userId`|選用|字串|用來識別使用者的唯一識別碼。 此識別碼會記錄在交談記錄中。|
|`scoreThreshold`|選用|integer|會傳回信心分數高於此臨界值的解答。 預設值為 0。|
|`isTest`|選用|Boolean|如果為 true，傳回結果集`testkb`搜尋服務索引，而不是已發行的索引。|
|`strictFilters`|選用|字串|如果指定，將會指示 QnA Maker 僅傳回含有指定中繼資料的解答。 使用`none`以指出回應應該會有任何中繼資料篩選。 |
|`RankerType`|選用|字串|如果指定為`QuestionOnly`，告訴 QnA Maker，以搜尋只問題。 如果未指定，QnA Maker 會搜尋問答集。

範例 JSON 主體看起來像：

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
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
|Id|指派給解答的唯一識別碼。|
|問題|使用者所提供的問題。|
|接聽|問題的答案。|
|source|從中擷取解答或將其儲存在知識庫中的來源名稱。|
|中繼資料|與解答相關聯的中繼資料。|
|metadata.name|中繼資料名稱。 (字串，最大長度：100，必要)|
|metadata.value|中繼資料值。 (字串，最大長度：100，必要)|


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

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>使用中繼資料來篩選所自訂的中繼資料標記的解答

新增中繼資料，可讓您依這些中繼資料標記篩選的解答。 加入中繼資料資料行從**檢視選項**功能表。 選取中繼資料，將中繼資料新增至您的知識庫 **+** 圖示以新增中繼資料組。 此組是由一個索引鍵和一個值所組成。

![新增中繼資料的螢幕擷取畫面](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>使用中繼資料標記的 strictFilters 篩選結果

請考慮使用者問題 < 當？ 關閉這個旅館 」，目的隱含餐廳 」 天堂。 」

由於結果是餐廳 」 天堂 」 中才需要，您可以設定的篩選 GenerateAnswer 呼叫中 「 餐廳名稱 」 的中繼資料。 下列範例會示範這個：

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

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>使用問題和答案的結果來保存交談內容

GenerateAnswer 的回應會包含相符的問題和答案組的對應中繼資料資訊。 您可以使用用戶端應用程式中的這項資訊來更新版本的交談中儲存先前用於交談的內容。 

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

## <a name="match-questions-only-by-text"></a>符合文字的問題，

根據預設，QnA Maker 搜尋問答集。 如果您想要搜尋只問題時，若要產生答案，請使用`RankerType=QuestionOnly`GenerateAnswer 要求 POST 主體中。

您可以搜尋發行之知識庫中，使用`isTest=false`，或使用您建立測試 kb `isTest=true`。

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="next-steps"></a>後續步驟

**發佈**網頁也會提供資訊來產生的答案[Postman](../Quickstarts/get-answer-from-kb-using-postman.md)並[cURL](../Quickstarts/get-answer-from-kb-using-curl.md)。 

> [!div class="nextstepaction"]
> [建立知識庫](./create-knowledge-base.md)
