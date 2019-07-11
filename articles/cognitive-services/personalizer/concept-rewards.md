---
title: 獎勵分數 - 個人化工具
titleSuffix: Azure Cognitive Services
description: 獎勵分數會指出個人化選擇 (RewardActionID) 對使用者產生的成效。 獎勵分數的值取決於商務邏輯，並以觀察到的使用者行為作為基礎。 個人化工具會藉由評估獎勵來訓練其機器學習模型。
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 06/07/2019
ms.author: edjez
ms.openlocfilehash: c64d43301fd173203bd1625b8d37120b71c22805
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077405"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>獎勵分數表示個人化的成就

獎勵分數會指出個人化選擇 [RewardActionID](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/rank/rank#response) 對使用者產生的成效。 獎勵分數的值取決於商務邏輯，並以觀察到的使用者行為作為基礎。

個人化工具會藉由評估獎勵來訓練其機器學習模型。 

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>使用獎勵 API 將獎勵分數傳送給個人化工具

獎勵會藉由[獎勵 API](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward) 來傳送給個人化工具。 獎勵是介於 -1 和 1 之間的數字。 個人化工具會訓練模型，以盡可能在一段時間後達到最高的獎勵總和。

獎勵會在使用者行為發生後傳送，而這有可能是幾天後。 個人化工具最多會等到事件已被認定沒有獎勵為止，或是 Azure 入口網站中的[獎勵等候時間](#reward-wait-time)已設定預設獎勵。

如果未在**獎勵等候時間**內收到事件的獎勵分數，則會套用**預設獎勵**。 通常， **[預設獎勵](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** 會設定為零。


## <a name="behaviors-and-data-to-consider-for-rewards"></a>考慮獎勵的行為和資料

針對獎勵分數的內容，考慮下列訊號和行為：

* 涉及選項時，使用者直接輸入的建議 (「您的意思是 X？」)。
* 工作階段長度。
* 工作階段之間的時間。
* 使用者互動的情感分析。
* 直接問題或迷你問卷，Bot 會在其中要求使用者提供關於實用性、精確度的意見反應。
* 回應警示，或延遲回應警示。

## <a name="composing-reward-scores"></a>撰寫獎勵分數

獎勵分數必須在您的商務邏輯內計算。 分數可以表示成：

* 傳送一次的單一數字 
* 立即傳送的分數 (例如 0.8) 和稍後傳送的其他分數 (通常為 0.2)。

## <a name="default-rewards"></a>預設獎勵

發出排名呼叫的期間，如果未在[獎勵等候時間](#reward-wait-time)內收到任何獎勵，個人化工具就會以隱含方式將**預設獎勵**套用到排名事件。

## <a name="building-up-rewards-with-multiple-factors"></a>建置具有多個因素的獎勵  

為了達到有效的個人化，您可以根據多個因素來建置獎勵分數 (-1 和 1 之間的任何數字)。 

例如，您可以套用這些規則來個人化影片內容清單：

|使用者行為|部分評分值|
|--|--|
|按下熱門項目的使用者。|+0.5 獎勵|
|開啟該項目實際內容的使用者。|+0.3 獎勵|
|觀看內容達 5 分鐘或 30% (以時間較長者為主) 的使用者。|+0.2 獎勵|
|||

接著，您可以將獎勵總計傳送給 API。

## <a name="calling-the-reward-api-multiple-times"></a>多次呼叫獎勵 API

您也可以使用相同事件識別碼來呼叫獎勵 API，以傳送不同的獎勵分數。 個人化工具會在取得這些獎勵時，藉由將獎勵彙總為個人化工具設定中指定的形式，來決定最終獎勵。

應用程式設定：

*  **第一個**：採用第一個收到的事件獎勵分數，並捨棄其餘部分。
* **總和**︰採用向 eventId 收集的所有獎勵分數，並將這些獎勵加總。

所有在**獎勵等候時間**後收到的事件獎勵都會遭到捨棄，但不會影響模型訓練。

藉由加總獎勵分數，您最終的獎勵可能會高於 1 或小於 -1。 這並不會造成服務失敗。

<!--
@edjez - is the number ignored if it is outside the acceptable range?
-->

## <a name="best-practices-for-calculating-reward-score"></a>計算獎勵分數的最佳做法

* **考量成功個人化的真實指標**：從點擊數來思考很容易，但良好的獎勵應建立在您希望使用者「達成」  什麼，而不是您希望人們「做」  什麼。  例如，點擊數的奬勵可能會導致人們選取偏向點擊誘餌的內容。

* **針對個人化的成效使用獎勵分數**：個人化電影建議很可能會讓使用者觀看電影，並給予高評等。 由於電影評等可能取決於許多因素 (演出品質和使用者的心情等)，所以對於「個人化」  成效來說，這不是很好的獎勵指標。 使用者觀賞電影的前幾分鐘片段反而是更好的個人化成效指標，而且在 5 分鐘後傳送獎勵分數 1 會是更好的指標。

* **只將獎勵套用到 RewardActionID**：個人化工具會套用獎勵來了解 RewardActionID 中指定動作的效用。 如果您選擇顯示其他動作，而且使用者按了這些動作，則獎勵應為零。

* **考量非預期的結果**：建立的獎勵功能應導向負責任的結果，並符合[道德和負責任的使用](ethics-responsible-use.md)。

* **使用累加獎勵**：針對小型使用者行為增加部分獎勵，有助於讓個人化工具達到更好的獎勵。 此累加獎勵可讓演算法知道使用者愈來愈受到最終預期行為的吸引。
    * 如果您顯示一份電影清單，當使用者將滑鼠停留在第一部電影上一段時間時，您就可以判斷某些使用者參與情形已發生。 此行為可計為 0.1 的獎勵分數。 
    * 如果使用者開啟網頁然後離開，獎勵分數可以是 0.2。 

## <a name="reward-wait-time"></a>報酬等候時間

個人化工具會使排名呼叫資訊和獎勵呼叫中傳送的獎勵相互關聯，藉此來訓練模型。 這些檔案可能會在不同時間傳入。 個人化工具會在限定時間內等候，等候時間會從發出排名呼叫時開始算起，即使發出排名呼叫的是之後才啟動的非作用中事件也是一樣。

如果**獎勵等候時間**過了，而且沒有獎勵資訊，則預設獎勵會套用至該事件來進行訓練。 等候期間上限是 6 天。

## <a name="best-practices-for-setting-reward-wait-time"></a>設定獎勵等候時間的最佳做法

請遵循這些建議來取得更好的結果。

* 請盡可能縮短獎勵等候時間，但要保留足夠的時間來取得使用者意見反應。 

<!--@Edjez - storage quota? -->

* 不要選擇比取得意見反應所需時間還短的期間。 例如，如果您的有些獎勵會在使用者觀看影片 1 分鐘後才傳入，則期限長度就應該至少是此時間的兩倍。

## <a name="next-steps"></a>後續步驟

* [增強式學習](concepts-reinforcement-learning.md) 
* [嘗試使用排名 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [嘗試使用獎勵 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
