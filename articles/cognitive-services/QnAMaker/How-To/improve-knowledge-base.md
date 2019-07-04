---
title: 改善知識庫 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 主動式學習可用來根據使用者提交的內容提出建議的問答組替代問題，以提高知識庫的品質。 您可以檢閱這些建議，並將其新增至現有的問題或加以拒絕。 您的知識庫不會自動變更。 您必須接受任何變更才能生效的建議。 這些建議會新增問題，但不會變更或移除現有的問題。
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/19/2019
ms.author: diberry
ms.openlocfilehash: b73884e544ea1b8ee76c8a891048e6a8e17d6ab3
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204085"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>若要改善您的知識庫中使用 作用中學習

主動式學習可用來根據使用者提交的內容提出建議的問答組替代問題，以提高知識庫的品質。 您可以檢閱這些建議，並將其新增至現有的問題或加以拒絕。 

您的知識庫不會自動變更。 為了讓任何要讓變更生效，您必須接受建議。 這些建議會新增問題，但不會變更或移除現有的問題。

## <a name="what-is-active-learning"></a>什麼是主動式學習？

QnA Maker 可透過隱含和明確的意見反應學習新演變出來的問題。
 
* [隱含的意見反應](#how-qna-makers-implicit-feedback-works)– ranker 的了解當使用者問題有多個答案非常接近的分數，並且會認為這與意見反應。 您不需要採取任何動作的這種情形。
* [明確的意見反應](#how-you-give-explicit-feedback-with-the-train-api)– 多個答案一點變化分數會傳回從知識庫中，當用戶端應用程式會要求使用者的問題是正確的問題。 使用者的明確的意見反應會傳送至使用 QnA Maker[定型 API](#train-api)。 

這兩種方法提供 ranker 已叢集化的類似查詢。

## <a name="how-active-learning-works"></a>主動式學習的運作方式

主動式學習，就會觸發根據的 QnA Maker 所傳回的前幾個答案分數。 如果分數差異落在較小的範圍內，查詢會針對每個可能的 QnA 配對視為可能的建議 （做為替代的問題）。 一旦您接受了特定的 QnA 一組建議的問題時，它將會拒絕其他配對。 您需要儲存和訓練，請接受建議之後，請記得。

當端點取得合理數量和足夠類型的使用查詢時，主動式學習可提供最佳的可能建議。 5 個或更多類似的查詢已叢集化時，每隔 30 分鐘，QnA Maker 會建議使用者為基礎的問題，以接受或拒絕的知識庫設計工具。 所有建議會依相似度叢集化在一起，並根據使用者發出特定查詢的頻率顯示替代問題的最高排名建議。

一旦 QnA Maker 入口網站中，所建議的問題，您需要檢閱並接受或拒絕這些建議。 沒有 API 管理的建議。

## <a name="how-qna-makers-implicit-feedback-works"></a>QnA Maker 隱含的意見反應的運作方式

QnA Maker 隱含的意見反應會使用演算法，以判斷分數鄰近性，然後提出主動式學習建議。 判定相近程度的演算法並非簡單的計算。 在下列範例中的範圍並非予以修正，但應該用做為指南，來了解演算法只影響。

當問題的分數具有高信賴度時 (例如 80%)，可考慮進行主動式學習的分數範圍較廣，大約在 10% 以內。 信賴分數下降時 (例如 40%)，分數的範圍也會隨之降低，大約在 4% 以內。 

## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>如何提供明確的意見反應與訓練 API

請務必 QnA Maker 取得明確的意見反應相關的答案正是最佳途徑。 如何判斷最佳的回答是由您決定，而且可以包括：

* 使用者意見反應，選取其中一個的答案。
* 商務邏輯，例如判斷可接受範圍進行評分。  
* 這兩個使用者的意見反應和商務邏輯的組合。

## <a name="upgrade-your-runtime-version-to-use-active-learning"></a>升級您的執行階段版本，使用主動學習

執行階段 4.4.0 版和更新版本可支援主動式學習。 如果您的知識庫是在舊版中建立的，請[升級執行階段](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates)以使用這項功能。 

## <a name="turn-on-active-learning-to-see-suggestions"></a>開啟作用中的學習，以檢視建議

依預設會關閉主動式學習。 開啟它以查看建議的問題。 開啟主動式學習之後，您需要從用戶端應用程式將資訊傳送至 QnA Maker。 如需詳細資訊，請參閱 < [GenerateAnswer 和訓練 Api 利用 bot 架構流程](#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)。

1. 選取 **發佈**來發行知識庫。 主動學習查詢會從 GenerateAnswer API 預測端點只收集。 QnA Maker 入口網站中測試 窗格的查詢不會影響主動式學習。

1. 若要開啟 作用中的學習上 QnA Maker 入口網站中，移到右上角，選取您**名稱**，請前往[**服務設定**](https://www.qnamaker.ai/UserSettings)。  

    ![開啟主動式學習建議的問題從服務的 [設定] 頁面的替代項目。 在右上方功能表中，選取您的使用者名稱，然後選取 服務設定。](../media/improve-knowledge-base/Endpoint-Keys.png)


1. 尋找 QnA Maker 服務，然後切換**主動式學習**。 

    [![在 [服務設定] 頁面上切換主動學習功能。如果您不可以切換的功能，您可能需要升級您的服務。](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    一次**主動式學習**已啟用，知識庫會建議新的問題定期根據使用者提交的問題。 您可以再次切換設定以停用**主動式學習**。

## <a name="accept-an-active-learning-suggestion-in-the-knowledge-base"></a>接受知識庫中的主動式學習建議

1. 若要查看建議的問題，在**編輯**知識庫 頁面上，選取**檢視選項**，然後選取**顯示作用中的學習建議**。 

    [![在入口網站的 [編輯] 區段中，選取 [顯示的建議，若要查看作用中的學習新問題的替代方案。](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. 篩選問題和答案組，以顯示所選取的只有建議知識庫**篩選-依建議**。

    [![若要檢視僅作用中的學習建議的問題的替代方案，建議切換使用篩選。](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1. 每個 QnA 組建議的核取記號，新的問題替代項目`✔`，以接受問題或`x`拒絕建議。 選取核取記號可新增問題。 

    [![選取或拒絕選取綠色的核取記號或紅色刪除標記的作用中的學習建議的問題的替代方案。](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    您可以新增或刪除_的所有建議_藉由選取**加入所有**或**全部拒絕**內容的工具列中。

1. 選取 [儲存並訓練]  ，以儲存對知識庫所做的變更。

1. 選取 **發佈**，讓變更可從[GenerateAnswer API](metadata-generateanswer-usage.md#generateanswer-request-configuration)。

    5 個或更多類似的查詢已叢集化時，每隔 30 分鐘，QnA Maker 建議替代的問題，以接受或拒絕。


<a name="#score-proximity-between-knowledge-base-questions"></a>

### <a name="architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot"></a>使用 bot GenerateAnswer 和訓練 Api 的架構流程

Bot 或其他用戶端應用程式應該使用下列架構流程，以使用主動學習：

* Bot[從知識庫中取得答案](#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)使用 GenerateAnswer API 時，使用`top`屬性取得的解答數目。
* Bot 決定明確的意見反應：
    * 使用您自己[自訂商務邏輯](#use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user)，篩選出低的分數。
    * 在 bot 或用戶端應用程式中，顯示可能的答案對使用者的清單，並取得使用者選取的回應。
* Bot[傳送選取的回應傳回給 QnA Maker](#bot-framework-sample-code)具有[訓練 API](#train-api)。


### <a name="use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers"></a>使用 GenerateAnswer 要求中的最上層的屬性，來取得數個相符的解答

QnA Maker 的問題的答案，請在提交時`top`的 JSON 主體的屬性會設定傳回的解答數目。 

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

### <a name="use-the-score-property-along-with-business-logic-to-get-list-of-answers-to-show-user"></a>使用商務邏輯以及分數屬性來取得答案對使用者顯示的清單

當用戶端應用程式 （例如聊天機器人） 收到回應時，會傳回前 3 個問題。 使用`score`分析之間的分數相近的屬性。 此鄰近範圍取決於您自己的商務邏輯。 

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

## <a name="client-application-follow-up-when-questions-have-similar-scores"></a>用戶端應用程式在問題具有類似分數時的後續動作

用戶端應用程式會顯示使用者選取的選項就問_單一問題_大部分代表其意圖。 

一旦使用者可以選取現有的問題之一，用戶端應用程式會以使用 QnA Maker 訓練 API 的意見反應傳送使用者的選擇。 此意見反應完成主動學習回饋迴圈。 

## <a name="train-api"></a>將 API 定型

作用中的 learning 意見反應傳送給 QnA Maker 訓練 API 的 POST 要求。 API 簽章是：

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP 要求的屬性|名稱|類型|目的|
|--|--|--|--|
|URL 路由參數|知識庫識別碼|string|測試您知識庫的 GUID。|
|主機的子網域|QnAMaker 資源名稱|string|您在您的 Azure 訂用帳戶中的 QnA Maker 的主機名稱。 在發行知識庫之後，這是可在 [設定] 頁面上取得。 |
|頁首|Content-Type|string|傳送至 API 的本文媒體類型。 預設值為： `application/json`|
|頁首|Authorization|string|您的端點金鑰 (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)。|
|張貼本文|JSON 物件|JSON|訓練意見反應|

JSON 主體具有數個設定：

|JSON 本文屬性|類型|目的|
|--|--|--|--|
|`feedbackRecords`|array|意見反應的清單。|
|`userId`|string|接受建議的問題之人員的使用者識別碼。 使用者識別碼的格式是由您決定。 例如，電子郵件地址可以是在架構中的有效使用者識別碼。 選用。|
|`userQuestion`|string|使用者的查詢的確切文字。 必要。|
|`qnaID`|number|識別碼中找到的問題[GenerateAnswer 回應](metadata-generateanswer-usage.md#generateanswer-response-properties)。 |

範例 JSON 主體看起來像：

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

成功的回應會傳回 204 並沒有 JSON 回應主體的狀態。 

### <a name="batch-many-feedback-records-into-a-single-call"></a>批次處理成單一呼叫的 意見反應的多筆記錄

在用戶端應用程式，例如 bot，您可以儲存的資料，然後在單一的 JSON 主體中傳送的多筆記錄`feedbackRecords`陣列。 

範例 JSON 主體看起來像：

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "How do I ...",
            "qnaId": 1
        },
        {
            "userId": "2",
            "userQuestion": "Where is ...",
            "qnaId": 40
        },
        {
            "userId": "3",
            "userQuestion": "When do I ...",
            "qnaId": 33
        }
    ]
}
```



<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="bot-framework-sample-code"></a>Bot framework 範例程式碼

您的 bot framework 程式碼需要呼叫定型 API 中，如果使用者的查詢應該用於主動式學習。 有兩個撰寫的程式碼片段：

* 判斷查詢是否應該使用作用中的學習
* 將查詢傳送回主動式學習的 QnA Maker 訓練 API

在  [Azure Bot 範例](https://aka.ms/activelearningsamplebot)，這兩種活動曾經。 

### <a name="example-c-code-for-train-api-with-bot-framework-4x"></a>範例C#使用 Bot Framework 訓練 api 的程式碼 4.x

下列程式碼說明如何將資訊傳送回 QnA Maker 與訓練 API。 這[完整程式碼範例](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-activelearning/csharp_dotnetcore)可在 GitHub 上取得。

```csharp
public class FeedbackRecords
{
    // <summary>
    /// List of feedback records
    /// </summary>
    [JsonProperty("feedbackRecords")]
    public FeedbackRecord[] Records { get; set; }
}

/// <summary>
/// Active learning feedback record
/// </summary>
public class FeedbackRecord
{
    /// <summary>
    /// User id
    /// </summary>
    public string UserId { get; set; }

    /// <summary>
    /// User question
    /// </summary>
    public string UserQuestion { get; set; }

    /// <summary>
    /// QnA Id
    /// </summary>
    public int QnaId { get; set; }
}

/// <summary>
/// Method to call REST-based QnAMaker Train API for Active Learning
/// </summary>
/// <param name="host">Endpoint host of the runtime</param>
/// <param name="FeedbackRecords">Feedback records train API</param>
/// <param name="kbId">Knowledgebase Id</param>
/// <param name="key">Endpoint key</param>
/// <param name="cancellationToken"> Cancellation token</param>
public async static void CallTrain(string host, FeedbackRecords feedbackRecords, string kbId, string key, CancellationToken cancellationToken)
{
    var uri = host + "/knowledgebases/" + kbId + "/train/";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(uri);
            request.Content = new StringContent(JsonConvert.SerializeObject(feedbackRecords), Encoding.UTF8, "application/json");
            request.Headers.Add("Authorization", "EndpointKey " + key);

            var response = await client.SendAsync(request, cancellationToken);
            await response.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="example-nodejs-code-for-train-api-with-bot-framework-4x"></a>Node.js 程式碼範例適用於定型 API 使用 Bot Framework 4.x 

下列程式碼說明如何將資訊傳送回 QnA Maker 與訓練 API。 這[完整程式碼範例](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs)可在 GitHub 上取得。

```javascript
async callTrain(stepContext){

    var trainResponses = stepContext.values[this.qnaData];
    var currentQuery = stepContext.values[this.currentQuery];

    if(trainResponses.length > 1){
        var reply = stepContext.context.activity.text;
        var qnaResults = trainResponses.filter(r => r.questions[0] == reply);

        if(qnaResults.length > 0){

            stepContext.values[this.qnaData] = qnaResults;

            var feedbackRecords = {
                FeedbackRecords:[
                    {
                        UserId:stepContext.context.activity.id,
                        UserQuestion: currentQuery,
                        QnaId: qnaResults[0].id
                    }
                ]
            };

            // Call Active Learning Train API
            this.activeLearningHelper.callTrain(this.qnaMaker.endpoint.host, feedbackRecords, this.qnaMaker.endpoint.knowledgeBaseId, this.qnaMaker.endpoint.endpointKey);
            
            return await stepContext.next(qnaResults);
        }
        else{

            return await stepContext.endDialog();
        }
    }

    return await stepContext.next(stepContext.result);
}
```

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>主動學習會儲存在 匯出知識庫

當您的應用程式已啟用，主動式學習，並匯出應用程式， `SuggestedQuestions` tsv 檔案中的資料行，保留使用中的學習資料。 

`SuggestedQuestions`資料行是 JSON 物件的資訊的隱含`autosuggested`，和明確`usersuggested`意見反應。 此 JSON 物件的單一使用者提交問題範例`help`是：

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

當您重新匯入此應用程式時，主動式學習會繼續收集資訊並建議您的知識庫的建議。 

## <a name="best-practices"></a>最佳作法

如需使用主動式學習時的最佳做法，請參閱[最佳做法](../Concepts/best-practices.md#active-learning)。

## <a name="next-steps"></a>後續步驟
 
> [!div class="nextstepaction"]
> [GenerateAnswer API 中使用的中繼資料](metadata-generateanswer-usage.md)
