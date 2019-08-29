---
title: 知識庫 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 知識庫是由一組問題/回答 (QnA) 配對以及與每個 QnA 配對相關聯的選用中繼資料所組成。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 2b3e74f337cf8f57321c3a8d94f8191fc3ebb530
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093906"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>什麼是 QnA Maker 知識庫？

QnA Maker 知識庫是由一組問題/回答 (QnA) 配對以及與每個 QnA 配對相關聯的選用中繼資料所組成。

## <a name="key-knowledge-base-concepts"></a>重要知識庫概念

* **問題** - 問題包含最能代表使用者查詢的文字。 
* **回答** - 回答是當使用者查詢符合相關聯問題時傳回的回應。  
* **中繼資料** - 中繼資料是與 QnA 配對相關聯的標記，而且以索引鍵/值配對表示。 中繼資料標記用來篩選 QnA 配對，並限制執行查詢比對的集合。

單一 QnA (以數值 QnA 識別碼表示) 具有某個問題的多個變體 (替代問題)，這些變體全都對應至單一回答。 此外, 每個這類配對都可以有多個相關聯的元資料欄位: 一個索引鍵和一個值。

![QnA Maker 知識庫](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>知識庫內容格式

當您將豐富的內容內嵌到知識庫時，QnA Maker 會嘗試將內容轉換為 Markdown。 閱讀[這個](https://aka.ms/qnamaker-docs-markdown-support)部落格，以了解大部分聊天用戶端可理解的 Markdown 格式。

中繼資料欄位是由以冒號分隔的索引鍵/值配對 **(Product:Shredder)** 所組成。 索引鍵和值都必須只包含文字。 中繼資料索引鍵不能包含任何空格。 中繼資料每個索引鍵僅支援一個值。

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker 如何處理使用者查詢以選取最佳答案

定型和[發佈](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base)的 QnA Maker 知識庫會在[GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)接收使用者查詢, 從 bot 或其他用戶端應用程式。 下圖說明接收使用者查詢的過程。

![使用者查詢的排名進程](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

下表將說明此程式:

|步驟|用途|
|--|--|
|1|用戶端應用程式會將使用者查詢傳送至[GENERATEANSWER API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)。|
|2|Qna Maker 會使用語言偵測、spellers 和斷詞工具, 對使用者查詢進行前置處理。|
|3|這會採取此前置處理來改變使用者查詢, 以取得最佳搜尋結果。|
|4|這項改變的查詢會傳送至 Azure 搜尋服務索引, `top`並接收結果數目。 如果這些結果中沒有正確的答案, 請以`top`稍微增加的值。 在 90% 的查詢中`top` , 通常會有10的值可運作。|
|5|QnA Maker 會套用 advanced 特徵化, 以判斷使用者查詢的提取 Azure 搜尋服務結果是否正確。 |
|6|定型的 ranker 模型會使用步驟5中的功能分數來排名 Azure 搜尋服務的結果。|
|7|新的結果會依排名順序傳回用戶端應用程式。|
|||

使用的功能包括但不限於單字層級的語義、主體中的詞彙層級重要性, 以及深度學習的語義模型, 以判斷兩個文字字串之間的相似性與相關性。

## <a name="http-request-and-response-with-endpoint"></a>使用端點的 HTTP 要求和回應
當您發佈知識庫時, 服務會建立以 REST 為基礎的 HTTP**端點**, 可以整合到您的應用程式中, 通常是聊天機器人。 

### <a name="the-user-query-request-to-generate-an-answer"></a>產生解答的使用者查詢要求

**使用者查詢**是終端使用者要求知識庫的問題, 例如`How do I add a collaborator to my app?`。 查詢通常採用自然語言格式, 或幾個代表問題的關鍵字, 例如`help with collaborators`。 查詢會從用戶端應用程式中的 HTTP**要求**傳送至您的知識。

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

您可以藉由設定屬性 (例如[scoreThreshold](./confidence-score.md#choose-a-score-threshold)、 [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)和[strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags)) 來控制回應。

使用具有[多重回合功能](../how-to/multiturn-conversation.md)的[對話內容](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context), 讓交談能夠縮小問題和答案, 以尋找正確和最終的答案。

### <a name="the-response-from-a-call-to-generate-answer"></a>呼叫產生答案的回應

HTTP**回應**是根據指定使用者查詢的最相符項, 從知識庫中抓取的答案。 回應包含答案和預測分數。 如果您想要使用`top`屬性來要求多個頂尖答案, 您可以取得一個以上的頂尖答案, 每個都有一個分數。 

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

### <a name="test-and-production-knowledge-base"></a>測試和生產知識庫
知識庫是儲存以 QnA Maker 建立、維護和使用的問答的存放庫。 每個 QnA Maker 層均可用於多個知識庫。

知識庫有兩種狀態：測試和發佈。 

**測試知識庫**是針對回應的正確性和完整性進行編輯、儲存和測試的版本。 對於測試知識庫所做的變更，不會影響到應用程式/聊天機器人的終端使用者。 測試知識庫在 HTTP 要求`test`中稱為。 

**已發佈的知識庫**是聊天機器人/應用程式中所使用的版本。 發佈知識庫的動作會將測試知識庫的內容放入發佈的知識庫版本。 因為發佈的知識庫是應用程式透過端點使用的版本，所以應該小心確保內容正確且經過完整測試。 已發佈的知識庫`prod`在 HTTP 要求中稱為。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [知識庫的開發生命週期](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>另請參閱

[QnA Maker 概觀](../Overview/overview.md)

使用下列方式建立和編輯知識庫: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.Net SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

產生答案: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.Net SDK](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
