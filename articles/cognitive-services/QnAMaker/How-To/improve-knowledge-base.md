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
ms.date: 03/21/2019
ms.author: diberry
ms.openlocfilehash: f59f281b1bf7fa2851ab7759a0167b5d39ef44c1
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678984"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>若要改善您的知識庫中使用 作用中學習

主動式學習可用來根據使用者提交的內容提出建議的問答組替代問題，以提高知識庫的品質。 您可以檢閱這些建議，並將其新增至現有的問題或加以拒絕。 

您的知識庫不會自動變更。 您必須接受建議，變更才能生效。 這些建議會新增問題，但不會變更或移除現有的問題。

## <a name="what-is-active-learning"></a>什麼是主動式學習？

QnA Maker 可透過隱含和明確的意見反應學習新演變出來的問題。
 
* 隱含的意見反應 – 順位排定程式了解使用者問題有多個答案且分數非常接近，並將其視為意見反應。 
* 明確的意見反應 – 從知識庫傳回分數變化很小的多個答案時，用戶端應用程式會詢問使用者哪個問題是正確的問題。 使用者的明確意見反應會透過訓練 API 傳送至 QnA Maker。 

這兩種方法提供的順位排定程式都具有叢集化的類似查詢。

## <a name="how-active-learning-works"></a>主動式學習的運作方式

系統會根據 QnA Maker 針對任何給定查詢而傳回的幾個最高排名答案的分數，來觸發主動式學習。 如果分數差異落在很小的範圍內，則會將查詢視為每個可能的答案適用的可能_建議_。 

所有建議會依相似度叢集化在一起，並根據使用者發出特定查詢的頻率顯示替代問題的最高排名建議。 當端點取得合理數量和足夠類型的使用查詢時，主動式學習可提供最佳的可能建議。

5 個或更多類似的查詢已叢集化時，每隔 30 分鐘，QnA Maker 會建議使用者為基礎的問題，以接受或拒絕的知識庫設計工具。

一旦 QnA Maker 入口網站中，所建議的問題，您需要檢閱並接受或拒絕這些建議。 

## <a name="upgrade-your-version-to-use-active-learning"></a>升級您的版本使用主動學習

執行階段 4.4.0 版和更新版本可支援主動式學習。 如果您的知識庫是在舊版中建立的，請[升級執行階段](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates)以使用這項功能。 

## <a name="best-practices"></a>最佳作法

如需使用主動式學習時的最佳做法，請參閱[最佳做法](../Concepts/best-practices.md#active-learning)。

## <a name="score-proximity-between-knowledge-base-questions"></a>知識庫問題分數的相近程度

當問題的分數具有高信賴度時 (例如 80%)，可考慮進行主動式學習的分數範圍較廣，大約在 10% 以內。 信賴分數下降時 (例如 40%)，分數的範圍也會隨之降低，大約在 4% 以內。 

判定相近程度的演算法並非簡單的計算。 上述範例中的範圍並不是固定的，而僅應作為了解演算法影響的參考依據。

## <a name="turn-on-active-learning"></a>開啟主動式學習

依預設會關閉主動式學習。 開啟它以查看建議的問題。 

1. 選取 **發佈**來發行知識庫。 主動學習查詢會從 GenerateAnswer API 預測端點只收集。 Qna Maker 入口網站中測試 窗格的查詢不會影響主動式學習。

1. 若要開啟主動式學習，請在 QnA Maker 入口網站中，按一下您的 [名稱]，然後移至右上角的 [服務設定][](https://www.qnamaker.ai/UserSettings)。  

    ![開啟主動式學習建議的問題從服務的 [設定] 頁面的替代項目。 在右上方功能表中，選取您的使用者名稱，然後選取 服務設定。](../media/improve-knowledge-base/Endpoint-Keys.png)


1. 尋找 QnA Maker 服務，然後切換**主動式學習**。 

    [![在 [服務設定] 頁面上切換主動學習功能。如果您不可以切換的功能，您可能需要升級您的服務。](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    在**主動式學習**啟用後，知識庫將根據使用者提交的問題定期建議新的問題。 您可以再次切換設定以停用**主動式學習**。

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>將主動式學習的建議新增至知識庫

1. 若要查看建議的問題，請在 **編輯知識庫** 頁面上選取 **顯示建議**。 

    [![在入口網站的 [編輯] 區段中，選取 [顯示的建議，若要查看作用中的學習新問題的替代方案。](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. 篩選問題和答案組，以顯示所選取的只有建議知識庫**篩選-依建議**。

    [![若要檢視僅作用中的學習建議的問題的替代方案，建議切換使用篩選。](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  每個有建議的問題區段都會顯示新的問題，並且附有核取記號 `✔` (用以接受問題) 和 `x` (用以拒絕建議)。 選取核取記號可新增問題。 

    [![選取或拒絕選取綠色的核取記號或紅色刪除標記的作用中的學習建議的問題的替代方案。](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    您可以藉由選取 [全部新增] 或 [全部拒絕] 來新增或刪除所有建議。

1. 選取 [儲存並訓練]，以儲存對知識庫所做的變更。

1. 選取 **發佈**以便可從 GenerateAnswer API 的變更。

    5 個或更多類似的查詢已叢集化時，每隔 30 分鐘，QnA Maker 會建議使用者為基礎的問題，以接受或拒絕的知識庫設計工具。

## <a name="determine-best-choice-when-several-questions-have-similar-scores"></a>在數個問題的分數類似時判斷最佳選擇

當某個問題的分數與其他問題太過接近時，用戶端應用程式開發人員可以選擇要求釐清。

### <a name="use-the-top-property-in-the-generateanswer-request"></a>在 GenerateAnswer 要求中使用 top 屬性

將問題提交至 QnA Maker 以尋求答案時，JSON 主體的某部分可用來傳回多個最高排名答案：

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

當用戶端應用程式 (例如聊天機器人) 收到回應時，會傳回排名最高的 3 個問題：

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

### <a name="client-application-follow-up-when-questions-have-similar-scores"></a>用戶端應用程式在問題具有類似分數時的後續動作

用戶端應用程式會顯示所有問題，並且讓使用者能夠選取最能代表其意圖的問題。 

一旦使用者可以選取現有的問題之一，用戶端應用程式會以使用 QnA Maker 訓練 API 的意見反應傳送使用者的選擇。 此意見反應完成主動學習回饋迴圈。 

使用[Azure Bot 範例](https://aka.ms/activelearningsamplebot)以查看在端對端案例中的主動式學習。

## <a name="train-api"></a>訓練 API

作用中的 learning 意見反應傳送給 QnA Maker 訓練 API 的 POST 要求。 API 簽章是：

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|HTTP 要求的屬性|Name|類型|目的|
|--|--|--|--|
|URL 路由參數|知識庫識別碼|字串|測試您知識庫的 GUID。|
|主機的子網域|QnAMaker 資源名稱|字串|您在您的 Azure 訂用帳戶中的 QnA Maker 的主機名稱。 在發行知識庫之後，這是可在 [設定] 頁面上取得。 |
|頁首|Content-Type|字串|傳送至 API 的本文媒體類型。 預設值為： `application/json`|
|頁首|授權|字串|您的端點金鑰 (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)。|
|張貼本文|JSON 物件|JSON|訓練意見反應|

JSON 主體具有數個設定：

|JSON 本文屬性|類型|目的|
|--|--|--|--|
|`feedbackRecords`|array|意見反應的清單。|
|`userId`|字串|接受建議的問題之人員的使用者識別碼。 使用者識別碼的格式是由您決定。 例如，電子郵件地址可以是在架構中的有效使用者識別碼。 選用。|
|`userQuestion`|字串|問題的確切文字。 必要。|
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

<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>主動學習會儲存在 匯出知識庫

當您的應用程式已啟用，主動式學習，並匯出應用程式， `SuggestedQuestions` tsv 檔案中的資料行，保留使用中的學習資料。 

`SuggestedQuestions`資料行是隱含的資訊的 JSON 物件 (`autosuggested`) 和明確 (`usersuggested`) 的意見反應。 此 JSON 物件的單一使用者提交問題範例`help`是：

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

## <a name="next-steps"></a>後續步驟
 
> [!div class="nextstepaction"]
> [GenerateAnswer API 中使用的中繼資料](metadata-generateanswer-usage.md)
