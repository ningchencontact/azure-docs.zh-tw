---
title: 預測分數-LUIS
titleSuffix: Azure Cognitive Services
description: 預測分數會根據使用者語句, 指出 LUIS API 服務針對預測結果所擁有的信心程度。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 34ec5588a510574f4ea9f01bd23c6f6487e288da
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638367"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>預測分數表示意圖和實體的預測準確性

預測分數會根據使用者語句指出預測結果的信賴程度 LUIS。

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

每個語句預測都會傳回一個最高分的意圖。 這項預測是預測分數的數值比較。 前2個分數的差異可能非常小。 LUIS 不會指出這個鄰近性, 而是傳回最高分。  

## <a name="return-prediction-score-for-all-intents"></a>傳回所有意圖的預測分數

測試或端點結果可以包含所有意圖。 若要進行此設定，請在[端點](https://aka.ms/v1-endpoint-api-docs)上以 `verbose=true` 查詢字串名稱/值組來設定。

## <a name="review-intents-with-similar-scores"></a>檢閱具有相似分數的意圖

檢閱所有意圖的分數是一個很好的方式，不僅可確認所識別的意圖正確，也可確認下一個所識別意圖的分數對語句而言明顯一致較低。

如果有多個意圖的預測分數相近，則 LUIS 可能會根據語句的內容，在意圖之間做切換。 若要修正這種情況, 請繼續將語句新增至每個意圖, 並具有更廣泛的內容差異, 或讓用戶端應用程式 (例如聊天機器人) 進行以程式設計方式選擇如何處理2個前意圖。

因為不具決定性的定型, 所以這兩個過度計分的意圖可能會反轉。 第一高分可能會變成第二高分，而第二高分可能會變成第一高分。 為了避免這種情況, 請將範例語句新增至該語句的前兩個意圖, 並使用文字選擇和內容來區分2個意圖。 兩個意圖應具有相同數目的範例語句。 若要避免因為訓練而發生反轉，區隔語句的經驗法則是分數上要有 15% 的差異。

您可以透過[訓練所有資料](luis-how-to-train.md#train-with-all-data)來關閉不具決定性的定型。

## <a name="differences-with-predictions-between-different-training-sessions"></a>不同訓練課程之間的預測差異

當您在不同的應用程式中定型相同的模型, 而且分數不相同時, 這項差異是因為有不具決定性的定型 (隨機性的元素)。 其次，當語句與多個意圖有任何重疊時，即意謂著相同語句的最高分意圖會根據定型變更。

如果您的聊天機器人需要特定的 LUIS 分數來表示意圖的信心, 您應該使用前兩個意圖之間的分數差異。 這種情況會提供定型變化的彈性。

## <a name="e-exponent-notation"></a>E (指數) 標記法

預測分數可以使用指數標記法，「顯示成」  高於 0-1 的範圍，例如 `9.910309E-07`。 此分數表示一個非常**小**的數字。

|E 標記法分數 |實際分數|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>標點符號

[深入瞭解](luis-concept-utterance.md#punctuation-marks)如何使用或忽略標點符號。 

## <a name="next-steps"></a>後續步驟

請參閱[新增實體](luis-how-to-add-entities.md)，以深入了解如何將實體新增至 LUIS 應用程式。
