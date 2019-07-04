---
title: 預測分數
titleSuffix: Language Understanding - Azure Cognitive Services
description: 預測分數指出 LUIS API 服務的預測結果的信心程度根據使用者的 [utterance]。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 383ce4c4248f7e21f745f503c74a29cb613983e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814006"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>預測分數表示意圖和實體的預測準確性

預測分數指出的預測結果，具有 LUIS 的信賴程度根據使用者的 [utterance]。

預測分數介於零 (0) 到一 (1) 之間。 高信賴度 LUIS 分數的範例是 0.99。 低信賴度的範例是 0.01。 

|分數值|Confidence|
|--|--|
|1|明確相符|
|0.99|高信賴度|
|0.01|低信賴度|
|0|明確不相符|

當語句產生低信賴度分數時，LUIS 會在 [LUIS](luis-reference-regions.md) 網站的 [意圖]  頁面上，將所識別的**已標示的意圖**以紅色框線標示來醒目提示。

![分數差異](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>最高分的意圖

每個語句預測都會傳回一個最高分的意圖。 這項預測是預測分數的數值比較。 最上層的 2 個分數可能具有非常小差異。 LUIS 並未指出此鄰近程度以外傳回最高的分數。  

## <a name="return-prediction-score-for-all-intents"></a>傳回所有意圖的預測分數

測試或端點結果可以包含所有意圖。 若要進行此設定，請在[端點](https://aka.ms/v1-endpoint-api-docs)上以 `verbose=true` 查詢字串名稱/值組來設定。

## <a name="review-intents-with-similar-scores"></a>檢閱具有相似分數的意圖

檢閱所有意圖的分數是一個很好的方式，不僅可確認所識別的意圖正確，也可確認下一個所識別意圖的分數對語句而言明顯一致較低。

如果有多個意圖的預測分數相近，則 LUIS 可能會根據語句的內容，在意圖之間做切換。 若要修正這種情況下，繼續新增表達方式更廣泛的內容相關的差異與每個意圖，或者您可以讓用戶端應用程式，例如聊天機器人，以程式設計方式選擇如何處理 2 個最上層的意圖。

2 個太-接近評分的對應方式，可能會因為不具決定性的訓練反轉。 第一高分可能會變成第二高分，而第二高分可能會變成第一高分。 為了防止這種情況下，將新增至每個最上層的兩個 「 意圖 」 針對該 [utterance]，word 選項與區分 2 意圖的內容的範例談話。 兩個意圖應具有相同數目的範例語句。 若要避免因為訓練而發生反轉，區隔語句的經驗法則是分數上要有 15% 的差異。

您可以關閉不具決定性的訓練，由[訓練的所有資料](luis-how-to-train.md#train-with-all-data)。

## <a name="differences-with-predictions-between-different-training-sessions"></a>使用預測不同的訓練工作階段之間的差異

定型不同的應用程式中，在相同的模型和分數並不相同，這項差異時，因為沒有不具決定性的訓練 （隨機性的項目）。 其次，當語句與多個意圖有任何重疊時，即意謂著相同語句的最高分意圖會根據定型變更。

如果您的聊天機器人需要特定的 LUIS 分數，以指出意圖的信心，您應該使用頂端的兩個 「 意圖 」 的分數差異。 這種情況下提供不同的訓練的彈性。

## <a name="e-exponent-notation"></a>E (指數) 標記法

預測分數可以使用指數標記法，「顯示成」  高於 0-1 的範圍，例如 `9.910309E-07`。 此分數表示一個非常**小**的數字。

|E 標記法分數 |實際分數|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>標點符號

標點符號在 LUIS 中是個別的語彙基元。 包含與不包含尾端句號 utterance 結尾的句點 utterance 兩個不同的談話，而且可能會收到兩個不同的預測。 請確定模型會在[範例語句](luis-concept-utterance.md) (含標點符號和不含標點符號) 或在更容易使用特殊語法來忽略標點符號的[模式](luis-concept-patterns.md)中處理標點符號：`I am applying for the {Job} position[.]`

## <a name="next-steps"></a>後續步驟

請參閱[新增實體](luis-how-to-add-entities.md)，以深入了解如何將實體新增至 LUIS 應用程式。
