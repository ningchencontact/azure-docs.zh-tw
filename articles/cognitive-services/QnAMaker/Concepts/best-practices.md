---
title: 最佳做法 - QnA Maker
titlesuffix: Azure Cognitive Services
description: 使用下列最佳做法可改善您的知識庫，並為您的應用程式/聊天機器人使用者提供更好的結果。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/10/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 2677c993b759988b0a9906b357bcd352b243b5a7
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792684"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>QnA Maker 知識庫的最佳做法
[知識庫開發生命週期](../Concepts/development-lifecycle-knowledge-base.md)可引導您了解如何從頭到尾管理知識庫。 使用下列最佳做法可改善您的知識庫，並為您的應用程式/聊天機器人使用者提供更好的結果。

## <a name="extraction"></a>擷取
QnA Maker 服務會持續改進從內容中擷取 QnA 的演算法，並擴充支援的檔案和 HTML 格式清單。 請遵循[指導方針](../Concepts/data-sources-supported.md)，以根據您的文件類型擷取資料。 

一般情況下，常見問題集頁面應獨立存在，而不會與其他資訊結合。 產品手冊應有清楚的標題，且最好有索引頁面。 

## <a name="creating-good-questions-and-answers"></a>建立良好的問題與解答

### <a name="good-questions"></a>良好的問題

最好的問題非常簡單。 考慮每個問題的關鍵字或片語，然後建立針對該關鍵字或片語的簡單問題。 

您可視需要新增多個替代問題，但要讓替代問題維持一樣的簡單性。 新增更多的單字或片語並非問題的主要目的，也無助於 QnA Maker 尋找答案。 

### <a name="good-answers"></a>良好的解答

最佳解答是簡單的解答，但不會太過簡單 (例如是和否)。 如果您的解答應該連結到其他資源，或提供含有媒體與連結的豐富體驗，請使用[標記](../how-to/metadata-generateanswer-usage.md)來區分您預期的解答類型，然後將該標記與查詢一起提交，以取得正確的解答版本。

## <a name="chit-chat"></a>閒聊
將閒聊新增至您的 Bot，輕而易舉地讓 Bot 變得更健談且吸引人。 您可以輕鬆地建立知識庫時，從預先定義的自身個性遭到新增 chit 聊天資料集，並隨時變更這些。 了解如何[將閒聊新增至您的 KB](../How-To/chit-chat-knowledge-base.md)。 

### <a name="choosing-a-personality"></a>選擇特質
支援數個預先定義的自身個性遭到 chit 對談： 

|人格 |QnA Maker 資料集檔案 |
|---------|-----|
|專業 |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|易記 |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|詼諧 |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|在意 |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|很有興趣 |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

回應的範圍從正式到非正式和不敬。 您應該選取最符合您希望 Bot 所用語調的特質。 您可以檢視[資料集](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)，並選擇一個作為您的機器人的基底，然後以自訂回應。 

### <a name="edit-bot-specific-questions"></a>編輯 Bot 特有問題
有一些屬於閒聊資料集的 Bot 特有問題，而且已填入泛型回答。 變更下列回答，充分反映您的 Bot 詳細資料。 

建議讓下列閒聊 QnA 更加明確：

* 您是誰?
* 您該怎麼辦？
* 您的年齡為何?
* 誰建立了你？
* 您好
   

## <a name="rankingscoring"></a>排名/評分
請務必充分利用 QnA Maker 所支援的排名功能。 這樣將可提高指定的使用者查詢獲得適當回應的可能性。

### <a name="choosing-a-threshold"></a>選擇閾值
預設信賴分數的閾值為 50，不過您可以根據需求為您的知識庫變更該閾值。 每個知識庫各有不同，因此您應該測試並選擇最適合您知識庫的閾值。 深入了解[信賴分數](../Concepts/confidence-score.md)。 

### <a name="add-alternate-questions"></a>新增替代問題
[替代問題](../How-To/edit-knowledge-base.md)可提高使用者查詢產生相符項目的可能性。 在同樣的問題可用多種方式提問時，替代問題就有其效用。 這可以包括文句結構和字組樣式的變更。

|原始查詢|替代查詢|變更| 
|--|--|--|
|Is parking available?|Do you have car park?|文句結構|
 |Hi|Yo<br>Hey there!|字組樣式或俚語|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>使用中繼資料標記來篩選問題和答案

[中繼資料](../How-To/edit-knowledge-base.md)可增加根中繼資料標記縮小使用者查詢結果範圍的能力。 即使查詢相同，知識庫解答也可能根據中繼資料標記而不同。 例如，如果餐廳分店的地點不同 (亦即，中繼資料為「地點：西雅圖」和「地點：雷德蒙」的不同)，「停車場在哪裡」就可能有不同的回答。

### <a name="use-synonyms"></a>使用同義字
儘管同義字在英文中可獲得某種程度的支援，但請使用不區分大小寫的[文字變異形式](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) \(英文\)，來為關鍵字新增採用不同格式的同義字。 同義字應新增於 QnA Maker 服務層級上，而由服務中所有的知識庫所共用。

|原始字組|同義資料表|
|--|--|
|buy|purchase<br>netbanking<br>net banking|

### <a name="use-distinct-words-to-differentiate-questions"></a>使用相異字詞區隔問題
QnA Maker 排名演算法會比對使用者查詢與知識庫中的所含問題，在每個問題分別處理不同的需求時，可獲得最佳效果。 若在不同問題間有相同的字組重複出現，就比較難以用這些字組為指定的使用者查詢選擇正確的解答。 

例如，您對於下列問題可能會有兩個不同的 QnA：

|QnA|
|--|
|where is the parking *location*|
|where is the ATM *location*|

因為這兩個 QnA 是以非常類似的字組表達，此相似性可能會對以 *"where is the `<x>` location"* 這類方式表達的許多使用者查詢，造成非常類似的分數。 相反地，試著避免使用在知識庫中出現於許多問題的字組 (像是 "location")，以 *"where is the parking lot"* 和 *"where is the ATM"* 之類的查詢來清楚區分。 

## <a name="collaborate"></a>共同作業
QnA Maker 可讓使用者對知識庫進行[共同作業](../How-to/collaborate-knowledge-base.md)。 使用者需要有 Azure QnA Maker 資源群組的存取權，才能存取知識庫。 有些組織可能想要將知識庫的編輯和維護委外處理，但同時仍能夠保護其 Azure 資源的存取權。 若要完成此「編輯者-核准者」模型，可以在不同的訂用帳戶中設定兩個相同的 [QnA Maker 服務](../How-to/set-up-qnamaker-service-azure.md)，並選取其中一個用於編輯測試週期。 測試完成之後，就會使用[匯入-匯出](../Tutorials/migrate-knowledge-base.md)程序將知識庫內容轉移至核准者的 QnA Maker 服務，由他來執行最終的知識庫發佈和端點更新。

## <a name="active-learning"></a>主動式學習

[主動式學習](../How-to/improve-knowledge-base.md)具有各種品質和數量的使用者查詢時，其在建議替代問題方面的表現最好。 務必讓用戶端應用程式的使用者查詢參與主動式回饋迴圈，而不需要審查。 一旦 QnA Maker 入口網站中，所建議的問題，您可以**[篩選-依建議](../How-To/improve-knowledge-base.md#add-active-learning-suggestion-to-knowledge-base)** 然後檢閱並接受或拒絕這些建議。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [編輯知識庫](../How-to/edit-knowledge-base.md)
