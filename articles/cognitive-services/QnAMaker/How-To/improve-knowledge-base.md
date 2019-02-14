---
title: 改善知識庫 - QnA Maker
titleSuffix: Azure Cognitive Services
description: ''
author: diberry
manager: cgronlun
displayName: active learning, suggestion, dialog prompt, train api, feedback loop, autolearn, auto-learn, user setting, service setting, services setting
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/29/2019
ms.author: diberry
ms.openlocfilehash: 639b665926f54387dfdc6e837c15c8d6d28df925
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755759"
---
# <a name="use-active-learning-to-improve-knowledge-base"></a>使用主動學習來改善知識庫

主動式學習可用來根據使用者提交的內容提出建議的問答組替代問題，以提高知識庫的品質。 您可以檢閱這些建議，並將其新增至現有的問題或加以拒絕。 

您的知識庫不會自動變更。 您必須接受建議，變更才能生效。 這些建議會新增問題，但不會變更或移除現有的問題。

## <a name="active-learning"></a>主動學習

QnA Maker 可透過隱含和明確的意見反應學習新演變出來的問題。
 
* 隱含的意見反應 – 順位排定程式了解使用者問題有多個答案且分數非常接近，並將其視為意見反應。 
* 明確的意見反應 – 從知識庫傳回分數變化很小的多個答案時，用戶端應用程式會詢問使用者哪個問題是正確的問題。 使用者的明確意見反應會透過訓練 API 傳送至 QnA Maker。 

這兩種方法提供的順位排定程式都具有叢集化的類似查詢。

類似的查詢叢集化時，QnA Maker 會建議知識庫設計者接受或拒絕使用者的問題。

## <a name="how-active-learning-works"></a>主動式學習的運作方式

系統會根據 QnA Maker 針對任何給定查詢而傳回的幾個最高排名答案的分數，來觸發主動式學習。 如果分數差異落在很小的範圍內，則會將查詢視為每個可能的答案適用的可能_建議_。 

所有建議會依相似度叢集化在一起，並根據使用者發出特定查詢的頻率顯示替代問題的最高排名建議。 當端點取得合理數量和足夠類型的使用查詢時，主動式學習可提供最佳的可能建議。

## <a name="upgrade-version-to-use-active-learning"></a>升級版本以使用主動式學習

執行階段 4.4.0 版和更新版本可支援主動式學習。 如果您的知識庫是在舊版中建立的，請[升級執行階段](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates)以使用這項功能。 

## <a name="best-practices"></a>最佳作法

如需使用主動式學習時的最佳做法，請參閱[最佳做法](../Concepts/best-practices.md#active-learning)。

## <a name="score-proximity-between-knowledge-base-questions"></a>知識庫問題分數的相近程度

當問題的分數具有高信賴度時 (例如 80%)，可考慮進行主動式學習的分數範圍較廣，大約在 10% 以內。 信賴分數下降時 (例如 40%)，分數的範圍也會隨之降低，大約在 4% 以內。 

判定相近程度的演算法並非簡單的計算。 上述範例中的範圍並不是固定的，而僅應作為了解演算法影響的參考依據。

## <a name="turn-on-active-learning"></a>開啟主動式學習

依預設會關閉主動式學習。 加以開啟可查看建議的問題。 

1. 若要開啟主動式學習，請在 QnA Maker 入口網站中，按一下您的 [名稱]，然後移至右上角的 [服務設定][](https://www.qnamaker.ai/UserSettings)。  

    ![在 [服務設定] 頁面上，將主動式學習切換為開啟](../media/improve-knowledge-base/Endpoint-Keys.png)


1. 尋找 QnA Maker 服務，然後切換**主動式學習**。 

    [![在 [服務設定] 頁面上，將主動式學習切換為開啟](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    在**主動式學習**啟用後，知識庫將根據使用者提交的問題定期建議新的問題。 您可以再次切換設定以停用**主動式學習**。

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>將主動式學習的建議新增至知識庫

1. 若要查看建議的問題，請在 [編輯知識庫] 頁面上選取 [顯示建議]。 

    [![在 [服務設定] 頁面上，切換 [顯示建議] 按鈕](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. 選取 [依建議篩選]，將包含問答組的知識庫篩選成僅顯示建議。

    [![在 [服務設定] 頁面上依建議篩選，而僅查看這些問答組](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  每個有建議的問題區段都會顯示新的問題，並且附有核取記號 `✔` (用以接受問題) 和 `x` (用以拒絕建議)。 選取核取記號可新增問題。 

    [![在 [服務設定] 頁面上，將主動式學習切換為開啟](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    您可以藉由選取 [全部新增] 或 [全部拒絕] 來新增或刪除所有建議。

1. 選取 [儲存並訓練]，以儲存對知識庫所做的變更。


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

使用者選取其中一個現有的問題後， 使用者意見反應將會傳送至 QnA Maker 的[訓練](http://www.aka.ms/activelearningsamplebot) API，以繼續進行主動式學習的意見反應循環。 

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

透過 [Azure Bot C# 範例](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-activelearning-bot)深入了解如何使用主動式學習

## <a name="next-steps"></a>後續步驟
 
> [!div class="nextstepaction"]
> [使用 QnA Maker API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) (英文)
