---
title: 中繼資料與 GenerateAnswer API - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 可讓您將索引鍵/值組形式的中繼資料新增至問答集。 您可以篩選使用者查詢的結果，並將可用的其他資訊儲存在待處理的交談。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/27/2019
ms.author: diberry
ms.openlocfilehash: 6bfcb531d0e4e8073a5553f7bc84a25e4f8a92a9
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785688"
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

您使用[GenerateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) bot 或應用程式中來查詢您的使用者問題的知識庫，若要發揮的問題和解答的最佳相符項目設定。

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>若要取得 GenerateAnswer 端點發行 

發行您的知識庫，從之後[QnA Maker 入口網站](https://www.qnamaker.ai)，或使用[API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)，您可以取得 GenerateAnswer 端點的詳細資料。

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

會使用 POST 要求：

* 所需[URI 參數](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* 所需[標頭屬性](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer)， `Authorization`，安全性
* 所需[主體屬性](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto)。 

GenerateAnswer URL 具有下列格式： 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

設定的 HTTP 標頭屬性，請記得`Authorization`字串的值`EndpointKey`使用尾端空格再上找到的端點金鑰**設定**頁面。

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

[回應](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query)是 JSON 物件，包括所有交談，開啟您要顯示答案和下一個所需資訊，如果有的話。

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

## <a name="use-qna-maker-with-a-bot-in-c"></a>在 bot 與搭配使用 QnA MakerC#

Bot framework 提供存取 QnA Maker 的屬性：

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

具有支援 bot[範例](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418)以下列程式碼。

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>在 Node.js 中 bot 與搭配使用 QnA Maker

Bot framework 提供存取 QnA Maker 的屬性：

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.03,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

具有支援 bot[範例](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36)以下列程式碼。

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
