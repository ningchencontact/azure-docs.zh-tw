---
title: 疑難排解-個人化工具
titleSuffix: Azure Cognitive Services
description: 本文包含有關個人化工具常見問題的解答。
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: diberry
ms.openlocfilehash: 5aeda9abcebda50cf97e1473b458d8f1f9d15970
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832167"
---
# <a name="personalizer-troubleshooting"></a>個人化工具疑難排解

本文包含有關個人化工具常見問題的解答。

## <a name="transaction-errors"></a>交易錯誤

### <a name="i-get-an-http-429-too-many-requests-response-from-the-service-what-can-i-do"></a>我收到來自服務的 HTTP 429 （太多要求）回應。 我該怎麼辦？

如果您在建立個人化工具實例時選擇了免費的定價層，則允許的排名要求數目會有配額限制。 檢查您的排名 API 的 API 呼叫率（在個人化工具資源 Azure 入口網站的 [計量] 窗格中），並調整定價層（在 [定價層] 窗格中），如果您的呼叫量預期會增加到所選定價層的閾值以外。

### <a name="im-getting-a-5xx-error-on-rank-or-reward-apis-what-should-i-do"></a>我在排名或獎勵 Api 上遇到5xx 錯誤。 我該怎麼做？

這些問題應該是透明的。 如果他們繼續，請在個人化工具資源的 Azure 入口網站中，選取 [**支援 + 疑難排解**] 區段中的 [**新增支援要求**]，以連線支援。


## <a name="learning-loop"></a>學習迴圈

<!--

### How do I import a learning policy?


-->

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>學習迴圈似乎沒有學習。 如何修正這個問題？

學習迴圈需要數千個報酬電話，順位呼叫才會有效率地設定優先順序。

如果您不確定學習迴圈目前的行為，請執行[離線評估](concepts-offline-evaluation.md)，並套用已更正的學習原則。

### <a name="i-keep-getting-rank-results-with-all-the-same-probabilities-for-all-items-how-do-i-know-personalizer-is-learning"></a>我持續取得所有專案的排名結果與所有相同的機率。 如何? 知道個人化工具是學習嗎？

當個人化工具剛啟動且具有_空白_模型，或當您重設個人化工具迴圈，而且您的模型仍在**模型更新頻率**期間內時，會在排名 API 結果中傳回相同的機率。

當新的更新期間開始時，會使用更新的模型，而您會看到機率變更。

### <a name="the-learning-loop-was-learning-but-seems-to-not-learn-anymore-and-the-quality-of-the-rank-results-isnt-that-good-what-should-i-do"></a>學習迴圈已經學習，但似乎無法再學習，而且排名結果的品質並不好。 我該怎麼做？

* 請確定您已完成，並在該個人化工具資源（學習迴圈）的 Azure 入口網站中套用一個評估。
* 請確定所有報酬都已透過獎勵 API 傳送，並已處理。

### <a name="how-do-i-know-that-the-learning-loop-is-getting-updated-regularly-and-is-used-to-score-my-data"></a>如何? 知道學習迴圈會定期更新，並用來對我的資料進行評分嗎？

您可以在 Azure 入口網站的 [**模型與學習設定**] 頁面中，找到模型上次更新的時間。 如果您看到舊的時間戳記，很可能是因為您未傳送排名和報酬電話。 如果服務沒有傳入的資料，就不會更新學習。 如果您看到學習迴圈的更新頻率不足，您可以編輯迴圈的**模型更新頻率**。


## <a name="offline-evaluations"></a>離線評估

### <a name="an-offline-evaluations-feature-importance-returns-a-long-list-with-hundreds-or-thousands-of-items-what-happened"></a>離線評估的功能重要性會傳回包含數百或數千個專案的長清單。 發生什麼情形？

這通常是因為時間戳記、使用者識別碼或一些其他細微的功能在中傳送。

### <a name="i-created-an-offline-evaluation-and-it-succeeded-almost-instantly-why-is-that-i-dont-see-any-results"></a>我建立了離線評估，而且幾乎會立即成功。 這是為什麼？ 我看不到任何結果嗎？

離線評估會在該時段內使用來自事件的定型模型資料。 如果您未在評估開始和結束時間的期間內傳送任何資料，則會在沒有任何結果的情況下完成。 藉由選取您知道已傳送至個人化工具之事件的時間範圍，以提交新的離線評估。

## <a name="learning-policy"></a>學習原則

### <a name="how-do-i-import-a-learning-policy"></a>如何? 匯入學習原則嗎？

深入瞭解[學習原則概念](concept-active-learning.md#understand-learning-policy-settings)，以及[如何](how-to-learning-policy.md)套用新的學習原則。 如果您不想要選取學習原則，可以使用[離線評估](how-to-offline-evaluation.md)，根據您目前的事件來建議學習原則。


## <a name="security"></a>安全性

### <a name="the-api-key-for-my-loop-has-been-compromised-what-can-i-do"></a>我的迴圈的 API 金鑰已遭入侵。 我該怎麼辦？

您可以在交換用戶端之後重新產生一個金鑰，以使用其他金鑰。 有兩個金鑰可讓您以延遲方式傳播金鑰，而不需要停機。 我們建議您在定期執行此動作，做為安全性措施。


## <a name="next-steps"></a>後續步驟

[設定模型更新頻率](how-to-settings.md#model-update-frequency)